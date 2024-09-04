---
title: Current dev project - spotty
author: Conor Neilson
date: '2021-08-15'
slug: current-dev-project-spotty
categories:
  - package development
  - R
tags:
  - package development
  - R
featureImage: images/nelson_lakes_photo.jpg
---

I started playing around with a new package today which I've titled `spotty`. `spotty`'s goal is to be a minimal wrapper around the spotify API. It intends to only do a small number of things but do them well. 

There is of course the very well done package [spotifyr](https://github.com/charlie86/spotifyr) by Charlie Thompson. `spotty` is not indended to compete with this package at all, and will not come anywhere near the breadth of features offered by `spotifyr`

### The motivation

I'm interested in maintaining a list of all the tracks I've saved on Spotify. My reasoning here is that if Spotify was to close, or my account gets closed, I should have a way to rebuild my saved tracks, which is made up mostly of one-off or more esoteric songs, and therefore would be harder to rebuild from memory. 

I started by trying to use spotifyr but quickly ran into an [error](https://github.com/charlie86/spotifyr/issues/157) while trying to access my saved tracks. I looked through the source code to try find where the error was, but was unable to find a fix. As an alternative I thought I might try making a small package which had two functions: retrieving saved tracks, and saved albums. By design they would return the complete list of tracks/albums, as opposed to the `spotifyr` approach where you have to use a combination of `limit`, `offset`, and `lapply` to get the complete list, rather than just the last 20.

The package is still very early, and this shouldn't be' interpreted as a release announcement - more just a look into what I'm' currently working on. Therefore, please don't expect any guarantees or documentation (yet). 

You can see the current state of development at [this repo](https://github.com/condwanaland/spotty). Currently working is the function `get_saved_tracks()`. It should work, but will require you to register a Spotify dev account, get a client id and client secret, and save them as environment variables. Check out the source code for details. 

`spotty` development will probably continue shortly, but just wanted to give a glance to my current dev thoughts. Please open an issue if you'd like to use this soon and I can prioritise some things

Hope everyone is well