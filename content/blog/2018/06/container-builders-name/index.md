---
title: "On Google Container Builder's Name"
date: 2018-06-22T07:21:03-07:00
draft: false
---
Google Container Builder is a very capable CI platform that I think is absent from discussions
far too often. It's incredibly flexible and simple and this combination makes it very powerful.

Google Container Builder has a very unfortunate name. I have a habit of calling it Google Cloud
Builder instead of it's given name. Most of the time I'm not sure people people notice at all, it
might just be the cast that no one is listening.

I — along with a few coworkers & friends — assumed that the Container builder was only _good_
for building and *publishing* docker images. However Container build is also very good at running
arbitrary things *in*  docker containers.

It doesn't help that the product exists as a sub-resource of the Google Container Registry which
implies — at least to me, others are probably more thoughtful in their assumptions — that it
would only be capable of producing artifacts which are published in the container registry.

{{< figure container-registry-interface Resize "400x" >}}

Why a product that can stand on its own is hidden as a sub-page of another product is something
I don't understand. For now I'll continue to call it "Cloud Builder" or abbreviate it to just
GCB. Since the product is marketed as a sub-tool of gcr.io it's omitted from consideration when
it could otherwise be a top contender.
