---
title: Scheduling scrobbler (properly this time)
author: Conor Neilson
date: '2021-08-14'
slug: scheduling-scrobbler-properly-this-time
categories:
  - R
tags:
  - R
  - tutorial
featureImage: images/nelson_lakes_photo.jpg
---
I finally learn how Github Secrets work!

<!-- more -->

## My previous mistake

In a previous post, I showed an example of how to use Github Actions to schedule `scrobbler` to run automatically and save the downloaded data into your github repo. This was a bit janky because you had to hardcode your API details into the script, and therefore it had to be a private repo in order to avoid leaking your details.

I'm pleased to announce I'm now much wiser in the ways of Github Secrets, and in this post I'll show how to adapt my previous one to not have to hardcode your secrets.

Before getting started I recommend reading the original post to understand the basics - this post will just show how to update the previous one. 

## A smarter way

Note: The easiest way to get started is to [clone the repository that I made](https://github.com/condwanaland/myscrobble). Then you should just be able to update the secrets with your own details and go on as before. If you'd prefer to start from scratch, keep reading.


### Create a package

As before, you need to create a package skeleton. I use RStudio and go through the package setup wizard to do so. 


### The .R file
Once you have your skeleton, we now need to create a `.R` file that holds our script. This will go in the `R` directory of your package, and I like to name the file `run.R`. Copy the following code into it.
```
library(scrobbler)

mydf <- download_scrobbles()

saveRDS(mydf,
        file = paste0(
          "scrobbles/scrobbles-",
          make.names(Sys.Date()),
          ".Rda")
)
```

First this code loads the `scrobbler` library.
Then we call the `download_scrobbles` function. We don't need to pass any arguments here because by default the function looks for environment variables - we'll supply these in the Github Action file. 
Finally, we save the file into the `scrobbles/` directory. 


### The data directory

Now we need to create a directory for the data to land in. Navigate to the package root directory, then create a folder called `scrobbles`. Place an empty text file in that directory (I name mine `touch.txt`). We need this empty file so that git will commit the directory. 


### The workflow file

Now we need a place for the Github Actions file. Create a directory `.github/workflows` (notice the full stop at the beginning). Then create a yaml file called `run_scrobbles.yaml` and copy over all the text from my example [here](https://github.com/condwanaland/myscrobble/blob/main/.github/workflows/run-scrobbles.yaml). 
I explained most the of the salient bits of this file in my previous post, so I'll just go over the changes here.

Lines 22 & 23 are:
```
      SCROB_USER_ID: ${{ secrets.user_id}}
      SCROB_API_KEY: ${{ secrets.api_key }}
```
This creates two variables in our Actions environment, `SCROB_USER_ID` and `SCROB_API_KEY`. We populate them with variables from `secrets` - don't worry about this for now, we'll come to secrets later. 

Then in lines 38-43 we have this:
```
      - name: Create and populate .Renviron file
        run: |
          echo LASTFM_API_USERNAME="$SCROB_USER_ID" >> ~/.Renviron
          echo LASTFM_API_KEY="$SCROB_API_KEY" >> ~/.Renviron
        shell: bash
```

If you haven't heard of `.Renviron` files before, they are just a way to store environment variables. R will then automatically source it at startup, and load any variables into your session. We can use this to store the environment variables for `scrobbler`, so that `download_scrobbles` can then pick them up.


### Secrets

That is all we need to do on the package front. Now go to Github and create a new repository (name it anything you like). Then follow the prompts to commit your files to your new repo. 

Now you need to add your secrets. On your repo home page, navigate to the 'Settings' pane, and then select 'Secrets', and then click 'New repository secret'. From here, make the name of your secret 'USER_ID' and set the value to be your Last.fm username. Repeat the process with a secret name 'API_KEY', and paste in your api key as the value.


### Testing

THis should now be everything you need! We can test that it works by manually running the workflow file. On your repo homepage, navigate to the 'Actions' pane. On the left hand side select your workflow file, then on the right hand side select 'run workflow'. This will start the file running, and you should see it appear after refreshing your page. Monitor this to check that it works (should take a few minutes), and on success you should have a file waiting in your `scrobbles/` directory.


### Scheduling

I've set my script to run on Sundays at midday, but if you want to change this you should edit line 3 in the workflow file. This uses a system called 'cron scheduling' - there are plenty of tutorials available to learn how to use this.


I hope this has been informative, and you now feel more secure running something like this online. Please reach out with any questions you have, and let me know what you're using `scrobbler` for!
