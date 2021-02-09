---
layout: post
title:  "Spinning Up a Static Website in S3"
date:   2021-02-09 18:39:26 +1100
category: devops
tags: resume cloudflare bootstrap aws s3
---
To get the Cloud Resume Challenge started in earnest, today I spun up a really quick static website in [S3](https://s3.console.aws.amazon.com/) using some pre-made HTML and CSS from [Bootstrap](https://getbootstrap.com/). I opted for using a Bootstrap example for my Resume site simply to save time, I figured it's better to get something online now than to worry about writing my own HTML and CSS.

I'll work on the actual 'resume content' another time too. First things first, I want to get the infrastructure built then make it flashy later.

Using [Bootstap](https://getbootstrap.com/) [CloudFlare](https://www.cloudflare.com/) and [S3](https://s3.console.aws.amazon.com/) I was able to configure a very basic static site in about 45 minutes. Most of that time was actually spent working out why the template I downloaded wasn't working properly. More on that below.

In terms of what's next, I'll work on getting a JavaScript visitor counter up and running. The challenge requires this counter to store and retrieve the count in a database somewhere. The challenge recommends not making calls into the DB directly, but rather, through a custom API.

I've got another project that makes calls directly into its database, so I'm excited to learn how to create an API and perhaps take what I learn there and improve my other project.

Feel free to go ahead and check out [trev.cloud](https://trev.cloud). At the time of writing, it's literally just the Bootstrap Cover template, but if you're reading this in the future, I hope you like my resume!

The rest of this post looks at steps 1-6 of the Cloud Resume Challenge. Lets go.

# 1. Certification

>Your resume needs to have the AWS Cloud Practitioner certification on it. This is an introductory certification that orients you on the industry-leading AWS cloud – if you have a more advanced AWS cert, that’s fine but not expected. No cheating: include the validation code on the resume. You can sit this exam online for $100 USD. If that cost is a dealbreaker for you, let me know and I’ll see if I can help. A Cloud Guru offers exam prep resources.

During and February and March of 2019 I wanted to validate some of my self paced AWS learning with an actual certification, it's not as though I felt I needed it at the time but I definitely wanted to get back into the swing of learning and testing - something I've not done for a long time.

When you pass an AWS exam they give you a spiffy online badge, [here's mine](https://www.youracclaim.com/badges/c00bd598-4227-49e9-8b5e-e3cb53a4ede7/public_url)

# 2. HTML and 3. CSS

> Your resume needs to be written in HTML. Not a Word doc, not a PDF.

>Your resume needs to be styled with CSS. No worries if you’re not a designer – neither am I. It doesn’t have to be fancy. But we need to see something other than raw HTML when we open the webpage.

To speed things up I used a pre-compiled [example Bootstrap template](https://getbootstrap.com/docs/5.0/getting-started/download/#examples). For this project I chose Cover, a simple single page site. This should suit me fine. I can customise the HTML and CSS to suit my needs later on but for now it's a placeholder.

As mentioned above, most of my time on this part of the project was spent trying to work out why the Cover template wasn't working straight out of the box. I used the Inspector in Chrome and saw there was a console error, unable to find `bootstrap.min.css`.

Out of the box, `index.html` comes like this:
{% highlight html %}
<link href="../assets/dist/css/bootstrap.min.css" rel="stylesheet">
{% endhighlight %}

But actually, to get it working I to be more like:
{% highlight html %}
<link href="./assets/dist/css/bootstrap.min.css" rel="stylesheet">
{% endhighlight %}

Spot the difference? There's one less `.` right before `/assets/`.

# 4. Static S3 Website

>Your HTML resume should be deployed online as an Amazon S3 static website. Services like Netlify and GitHub Pages are great and I would normally recommend them for personal static site deployments, but they make things a little too abstract for our purposes here. Use S3.

To set this blog up I've already used GitHub Pages so I definitely wanted to go the S3 route following the requirements of the challenge. I've used S3 buckets as static sites for other projects, so it was good to revisit the setup steps and following the [AWS doco](https://docs.amazonaws.cn/en_us/AmazonS3/latest/userguide) makes setting up easy.

- Create a new bucket named `trev.cloud`. Side note: I'm pretty sure the bucket name has to be the same as my URL. As of writing this, I'm not actually certain but I erred on the side of caution. Plus, it's good to see bucket names based on their URLs / projects - it makes it easy to quickly identify what a bucket is for if you haven't set tags up and such.
- Open the new bucket and go to properties. Under Static website hosting, choose Edit then Under Static website hosting, choose Enable. Save the changes.
- In Index document, enter the file name of the index document, in this case `index.html` then choose Save changes.
- Under Static website hosting, note the Endpoint, in this case - `http://trev.cloud.s3-website-ap-southeast-2.amazonaws.com`

Once the bucket is enabled for static website hosting, you still can't hit it until you modify the permissions.

- Choose Permissions. Under Block public access (bucket settings), choose Edit.
- Clear Block all public access, and choose Save changes.

The final configuration step before adding files is to add a `Bucket Policy` to grant public read access for your website.
- Under Bucket Policy, choose Edit.

I used an existing policy I had previously set up for another site, making sure I edited the resource field to reflect the name of this site.

{% highlight json %}
{
    "Version": "2008-10-17",
    "Statement": [
        {
            "Sid": "AllowPublicRead",
            "Effect": "Allow",
            "Principal": {
                "AWS": "*"
            },
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::trev.cloud/*"
        }
    ]
}
{% endhighlight %}

Now configured to be accessed publicly, it was time to upload some files.

- Go into the bucket, over to the Objects tab then chooe Upload.
- Drag and drop works on the GUI in Chrome so I selected all the files in the project folder and dragged across the into the broswer. Once there I hit Upload.

As soon as the upload was complete, that was almost it... the site was now up and running and I confirmed as much by navigating to the website endpoint [http://trev.cloud.s3-website-ap-southeast-2.amazonaws.com](http://trev.cloud.s3-website-ap-southeast-2.amazonaws.com) and seeing the [Bootstrap Cover template](https://getbootstrap.com/docs/5.0/examples/cover/). Then, the last thing left to do was configure the HTTPS and DNS.

# 5. HTTPS
>The S3 website URL should use HTTPS for security. You will need to use Amazon CloudFront to help with this.

Well this one was a gimme since I had already configured my domain in [Cloudflare](https://cloudflare.com) to get [this blog up and running]({% post_url 2021-02-03-making-this-blog %}).

6. DNS
>Point a custom DNS domain name to the CloudFront distribution, so your resume can be accessed at something like my-c00l-resume-website.com. You can use Amazon Route 53 or any other DNS provider for this. A domain name usually costs about ten bucks to register.

The final step to make this site easier for the world to find was to configure DNS.

- In Cloudflare I created a new `CNAME` record `trev.cloud` that targets the S3 bucket endpoint `trev.cloud.s3-website-ap-southeast-2.amazonaws.com`.

I confirmed the site was up by navigating to [https://trev.cloud](https://trev.cloud) and seeing the template.

Thanks for reading.

Trev
