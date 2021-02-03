---
layout: post
title:  "Cloud Resume Challenge Sidequest: Making a Blog Site"
date:   2021-02-03 19:35:26 +1100
category: devops
tags: blog aws jekyll github cloudflare
---
As I mentioned in my [last post]({% post_url 2021-02-03-hello-world %}), the final step of the Cloud Resume Challenge is to write a short blog post about what you've learned along the way. This has actually inspired me to keep an ongoing blog. So before I begin the challenge I'm building my own blog, from the ground up. A sidequest!

# Why create a blog from scratch and not use [dev.to](https://dev.to) or some other already existing platform?

In the spirit of the Cloud Resume Challenge I thought it would be cool to build my own blog site right alongside my resume site.

My initial thoughts were if I'm provisioning a database in AWS for the Resume site, I could probably leverage that for a blog site too.

I started searching for tutorials on the internet `how to code a blog from scratch` and `nosql blog tutorial` and such. I didn't find much that would suit what I was after, but I did keep seeing the name [Jekyll](https://jekyllrb.com/) pop up so I thought I'd take a look.

# Blog setup
Jekyll was so quick to set up that I haven't really worked out how to use it properly yet but I'm looking forward to tweaking as I go.

 - I purchased a domain in [Route 53](https://console.aws.amazon.com/route53), but immediately deleted the hosted zone it creates and pointed the domain's nameservers to [Cloudflare](https://cloudflare.com/)'s; `sam.cloudflare.com` and `uma.cloudflare.com`. I do this because AWS costs $0.50 per hosted zone a month where as CloudFlare has a free option which gets the job done you can turn SSL on with a single setting.
 - With a domain name, the next step was to install Jekyll locally following the [instructions here](https://jekyllrb.com/docs/installation/windows/).
 - After a quick online search [I found this blog post by Malathi T. on setting up Jekyll](https://www.loginradius.com/blog/async/setup-blog-in-minutes-with-jekyll/) and it has pretty much everything I needed to know about getting set up and published online.

- To get a new blog running locally:

  {% highlight console %}
  gem install jekyll bundler
  cd blog
  jekyll new blog
  bundle exec jekyll serve
  {% endhighlight %}
  This serves the blog locally on `http://127.0.0.1:4000` allowing you to see your site locally. While the server's running any edits made will automatically be built by the task and you can see right away by refreshing your browser.

- With the blog running locally, I created a new repo for it in GitHub and following the instructions in the post, I pushed contents of the fresh install to the [new repo](https://github.com/fixie360/blog/).

- In the `Settings` of the repo, I scrolled down to the `GitHub Pages` section and chose the `main` branch from the dropdown as the Source and hit save. Once GitHub pages is enabled on a branch, GitHub automatically rebuilds the blog site when there's a new to the branch.

- Next I set `Custom Domain` to [`blog.trev.cloud`](https://blog.trev.cloud). Because I'm using a subdomain this automatically created a `CNAME` file in the repo with `blog.trev.cloud` in it. It took me a while to work out where I was supposed to create the `CNAME` file because it wasn't super clear in the doco, but then as it turns out, the file was created automatically anyway.

- With the blog pushed to GitHub and configured for GitHub pages it was now time to go over to CloudFlare to get the DNS set up. I created a new site in cloudflare for my domain `trev.cloud` and chose the Free plan. Once you add a domain CloudFlare scans for existing DNS records. Since this was a new site, there were none and so I added a new record Type `CNAME` with Name `blog` targeted at my GitHub pages URL `fixie360-.github.io`. I hit next, CloudFlare advised to update name servers, but I'd already done that step so I hit `Done, check nameservers`

# Publishing my first post
After getting the config finished I wrote my first post - [Hello, World!]({% post_url 2021-02-03-hello-world %}) and updated the `_config.yaml`. My site was now looking like it was ready to publish. I saved everything and pushed it to the repo and that was pretty much it. All done. It actually took me much longer to write about setting up my blog than it took to set it up.

Thanks for reading.

Trev
