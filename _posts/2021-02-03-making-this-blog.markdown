---
layout: post
title:  "Cloud Resume Challenge Sidequest: Making a Blog Site"
date:   2021-02-03 19:35:26 +1100
category: devops
tags: blog aws jekyll github cloudflare
---
As I mentioned in my [last post]({% post_url 2021-02-03-hello-world %}), the final step of the Cloud Resume Challenge was to write a short blog post about what was learned along the way. This has actually inspired me to keep an ongoing blog. So before I begin the challenge I'm building my own blog, from the ground up. A sidequest!

# Why create a blog from scratch and not use [dev.to](https://dev.to) or some other already existing platform?

In the spirit of the Cloud Resume Challenge I thought it would be cool to build my own blog site right alongside my resume site.

My initial thoughts were if I'm provisioning a database in AWS for the Resume site anyway, I could probably leverage that for a blog site too.

I started searching for tutorials on the internet `how to code a blog from scratch` and `nosql blog tutorial` and such. I didn't find much that would suit what I was after, but I did keep seeing the name [Jekyll](https://jekyllrb.com/) pop up so I thought I'd take a look.

Jekyll is an open-sourced lightweight blogging software written in Ruby that is compatible with [GitHub Pages](https://pages.github.com/). Getting a personal site up is as simple as installing the software and pushing it to a repo configured as a GitHub Pages site, then, any time updates are made to the repo, GitHub Pages automatically rebuilds the site for you and your changes are live.

Getting it up was so quick that I haven't really worked out how to use it properly yet but I'm looking forward to tweaking as I go. Essentially, CI/CD without any effort I guess.

# Blog setup

 - For starters, I needed a domain. I purchased a `trev.cloud` in [Route 53](https://console.aws.amazon.com/route53). Route 53 automatically creates hosted zones (for your DNS entries) but I removed this and instead pointed the domain's nameservers to [Cloudflare](https://cloudflare.com/)'s (`sam.cloudflare.com` and `uma.cloudflare.com`). I do this because AWS costs $0.50 per hosted zone a month where as CloudFlare has a free option which gets the job done you can turn SSL on with a single setting.
 - With a domain name, the next step was to install Jekyll locally following the [instructions here](https://jekyllrb.com/docs/installation/windows/).
 - After a quick online search [I found this blog post by Malathi T. on setting up Jekyll](https://www.loginradius.com/blog/async/setup-blog-in-minutes-with-jekyll/) and it has pretty much everything I needed to know about getting set up and published online.

- To get a new blog running locally:

  {% highlight console %}
  gem install jekyll bundler
  cd blog
  jekyll new blog
  bundle exec jekyll serve
  {% endhighlight %}

  This serves the blog locally on `http://127.0.0.1:4000` allowing you to see your site right away in your browser. While the server's running any edits made will automatically be bundled and served by the running Jekyll command and you can see right them right away by refreshing your browser. This is particularly handy for seeing how the markdown formatting looks.

- With the blog running, I created a new repo for it in GitHub and following the instructions in the post, I pushed contents of the fresh install to the [new repo](https://github.com/fixie360/blog/).

- In the `Settings` of the repo, I scrolled down to the `GitHub Pages` section and chose the `main` branch from the dropdown as the Source and hit save. Once GitHub pages is enabled on a branch, GitHub automatically rebuilds the blog site when there's a change to the branch.

- Next I set `Custom Domain` to [`blog.trev.cloud`](https://blog.trev.cloud). Because I'm using a subdomain this automatically created a `CNAME` file in the repo with `blog.trev.cloud` in it. It took me a while to work out where I was supposed to create the `CNAME` file because it wasn't super clear in the doco, but then as it turns out, the file was created automatically anyway.

- With the blog pushed to GitHub and configured for GitHub Pages it was now time to go over to CloudFlare to get the DNS set up. I created a new site for my domain `trev.cloud` and chose the Free plan. Once you add a domain, CloudFlare scans for existing DNS records. Since this was a new site, there were none. I added a new record type `CNAME` with name `blog` targeted at my GitHub pages URL `fixie360-.github.io` then hit next, CloudFlare advised to update name servers, but I'd already done that step so I hit `Done, check nameservers`

# Publishing my first post
After getting the config finished I wrote my first post - [Hello, World!]({% post_url 2021-02-03-hello-world %}) and updated the `_config.yaml`. My site was now looking like it was ready to publish.

I saved everything and pushed it to the repo and that was pretty much it. All done. It actually took me much longer to write about setting up my blog than it took to set it up.

Thanks for reading.

Trev
