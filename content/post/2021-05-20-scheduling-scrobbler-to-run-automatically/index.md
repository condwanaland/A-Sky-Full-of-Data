---
title: Scheduling scrobbler to run automatically
author: R package build
date: '2021-05-20'
slug: scheduling-scrobbler-to-run-automatically
categories:
  - R
tags:
  - R
  - tutorial
featureImage: images/nelson_lakes_photo.jpg
---

Using github to automatically schedule, run, and store dataframes of your scrobbles in a repository. 

<!-- more -->

One of the biggest issues you can run into when trying to analyse your scrobble data is waiting for it all to download when running `download_scrobbles()`. While the addition of `update_scrobbles()` partially helps this, it still relies on you having a relatively recent source of scrobbles available in a dataset. 

The idea behind this post is to setup a github repo where you can store scrobbled datasets. You then put a small .R script in there that runs the actual download, and then use Github Actions to set this script running on a schedule. While it might sound complicated, it's actually relatively straightforward. 

***
*Please note, this tutorial involves saving your Last.fm API key into a (allbeit private) github repository. This is potentially a security risk, I am not a security expert, and I only recommend doing this if you are comfortable putting your api key in a repo. I cannot be held responsible for any exposure of your api key.*
*** 
`-`

To get started, you need to learn a little about running Github Actions with R. The absolute best place to learn is , [this tutorial](https://blog.simonpcouch.com/blog/r-github-actions-commit/) by Simon Couch.
The tutorial I've written here will focus on the **deviations** you need to make from Simons tutorial. It's broken up into sections below, based on the sections of Simon's tutorial. As it describes deviations, I highly highly advise reading and understanding his post first, then come here to adapt it.


### The setup

The first deviation from Simons tutorial is you should create a **private** github repository, so your lastfm creds are not immediately public.


### The R Script

First thing is to create the `.R` script that is placed inside the `/R` directory of your package. For scrobbler, this is relatively straightforward. Copy the text below, making sure to substitute your credentials in. 

```
library(scrobbler)

mydf <- download_scrobbles(
  "your_lastfm_username",
  "your_lastfm_api_key"
)

saveRDS(mydf,
        file = paste0(
          "scrobbles/scrobbles-",
          make.names(Sys.Date()),
          ".Rda")
)
```

***
  
*Note: If following the syntax above, make sure the 'scrobbles/' directory exists and is committed to Github. Git will normally not commit an empty directory. To get around this, create the 'scrobbles/' directory and add a blank text file inside it. You should then be able to commit the text file, and when you push to Github the 'scrobbles/' directory will be created.*

***
`-`
  
### The workflow file
  
Ive hosted a complete version of the workflow file as a Gist [here](https://gist.github.com/condwanaland/869346ecf9b0cd59cec853e5526953e1). The easiest thing to go is copypaste that into your github workflow file. For those interested,  I'll highlight the important differences between this one and Simons below. 

#### Lines 1-4
```
on:
  schedule:
   - cron: "0 0 * * 0"
  workflow_dispatch:
```

The - cron: command here is how you schedule the script to run at certain times. It uses a a special 'cron syntax'. The easiest way to get the schedule you want is to use a website like '[Crontab](https://crontab.guru/#0_0_*_*_0)'. This particular syntax is scheduled to run once a week on Sundays.

Here, Ive also added the 'workflow_dispatch:' command. This allows you to navigate to the workflow file on github and run it by clicking a button. This is perfect for debugging, or if you want a current version of your data. 

***
*Note: If you run this more than once a day, make sure to update your code to save the filenames to catch the time (with Sys.time()) instead of just the date.*
***
`-`

#### Lines 33-34
```
- name: Install curl
run: sudo apt-get install libcurl4-openssl-dev
```

As `scrobbler` talks to an API, it requires the R package `curl` which in turn is built upon the C library `libcurl`. This command ensures that `libcurl` gets installed on the server. 


#### Line 58
```
source("R/run.R")
```

Make sure to change the name of the R file to whatever you have named your script file. 


### Wrap up
With all of this together, you should have a repo that commits a `.Rda` file of all your scrobbles on whatever schedule you wish. 

Remember to be considerate with this. `scrobbler` is not designed for a continuous stream of data, so dont set it to run every minute or something. Think about how often you need it, and base your schedule around that. I only look at mine every so often, so weekly works for me. If I need a later version, I can always run `update_scrobbles` on the dataframe, and it'll update quickly.

If you run into any issues with this, please reach out - Im happy to assist in anyway. 
