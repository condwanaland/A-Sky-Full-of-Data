---
title: Auto-authentication with scrobbler
author: R package build
date: '2021-01-31'
slug: auto-authentication-with-scrobbler
categories:
  - R
  - package development
tags:
  - R
  - tutorial
  - package development
featureImage: images/nelson_lakes_photo.jpg
---

Authenticate automatically using environment variables

<!--more-->

I'm pleased to announce a new version of `scrobbler` which introduces a slightly new way to authenticate `download_scrobbles` and `update_scrobbles` calls. This change is released on CRAN, so you can get the update by running

```
install.packages("scrobbler")
```

<br/><br/>

### New authentication

In previous versions of `scrobbler`, whenever you wanted to download scrobbles you had to pass in your username and API key.

```
library(scrobbler)
my_data <- download_scrobbles(username = "your_username", api_key = "your_api_key")
```

This quickly gets tiring (particularly with the API key), so I'm pleased to announce there is now a better way. 

By default, the `download_scrobbles` and `update_scrobbles` functions will now search for environment variables named `LASTFM_API_USERNAME` and `LASTFM_API_KEY`, and automatically use their values. To set these values, simply run

```
Sys.setenv(LASTFM_API_USERNAME = "your_username")
Sys.setenv(LASTFM_API_KEY = "your_api_key")
```

Then all you need to do is run

```
my_data <- download_scrobbles()
```

As you can see, you no longer to authenticate directly in the function call, as it is handled automatically through the environment variables you set. 

One important thing of note here, is that environment variables are not persisted across R sessions. So if you quit and start R again, you'd need to rerun the `Sys.setenv` code everytime. This doesn't seem like we've gained much now right? Enter the `.r_profile` file!



### .r_profile file

The `.r_profile` file is a hidden file that is run each time you start R. Since it is run by default, its a great way to store environment variables you always want available. We can get easy access to the `.r_profile` file through the `usethis` package.

```
install.packages("usethis")
library(usethis)
edit_r_profile()
```

This will open up your `.r_profile` file. Now all you need to do is add the code below into the file, and save it (it's the exact same code we used above).

```
Sys.setenv(LASTFM_API_USERNAME = "your_username")
Sys.setenv(LASTFM_API_KEY = "your_api_key")
```

Once you've saved and closed your `.r_profile` file, and restarted R, you never need to worry about passing in authentication options again. Just run either `download_scrobbles()` or `update_scrobbles()` with no `username` or `api_key` arguments, and it should work automatically.

As always, if anything doesn't work, or something isn't clear, reach out on github.
