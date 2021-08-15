---
title: A quieter scrobbler
author: Conor Neilson
date: '2021-08-15'
slug: a-quieter-scrobbler
categories:
  - package development
  - R
tags:
  - package development
  - R
featureImage: images/nelson_lakes_photo.jpg
---

I just released a tiny update to `scrobbler`. Something you may have noticed is that `scrobbler` can sometimes be rather loud when you are using it. When downloading scrobbles it likes to print your username and api key periodically. This is both annoying and (in the case of the api key) rather unsafe.

The new version of scrobbler will still print its progress indicators, but will no longer emit your authentication details.

I also fixed a broken link, and made some internal changes to comply with the devel version of R.

Thats all for this release - hopefully later ones will have some more features.