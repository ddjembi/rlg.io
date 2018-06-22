---
title: "Episode IV: a New Host"
date: 2018-06-20T16:38:55-07:00
draft: false
---

Moving platforms!

I've recently switched domains and hosting providers for my personal site.

[rlg.io][rlg] is hosted on Google's [Firebase Hosting][firebase] service. The previous incarnation
of my site was hosted on [GitHub Pages][ghpages] and used [Cloudflare][cloudflare] for free TLS
with a custom domain. This worked really well but it seems to be a pretty common way of hosting
a personal blog and I wanted to learn something new.

I want to focus on writing and communicating with the community so I set out to find an alternative
solution that was:

1. Low Maintenance
2. Fault Tolerant
3. Fast
4. Automated
5. Lost Cost

The site must be low maintenance since I've got a lot of other projects I'm working on and any time
spent messing with the site is time not spent on more interesting things.

The site must also work when the power is out at my place and I don't want to worry about a VM
going down, it needs to be fault-tolerant.

It needs to be quick — both to publish and read — I want my readers to have a simple reading
experience that works on mobile networks.

I need publishing to be automated to the "push-button" level; I want to be able to focus on the
content over anything elase and having to jump through hoops to publish will take away from
the content. Writing is hard enough as it is I don't need to make it any harder.

Lastly, this blog isn't making my any money so it needs to be cheap to run.

---

The solution I landed on was using Google Container Builder to build a Hugo site and publish it to
Firebase Hosting. You can find the source for the site on [github.com/rlguarino/rlg.io][source].

In a future posts I'll go over each bit of that repository in detail, stay tuned.


[rlg]: https://rlg.io
[firebase]: http://firebase.google.com
[ghpages]: https://pages.github.com/
[cloudflare]: https://www.cloudflare.com
[source]: https://github.com/rlguarino/rlg.io
