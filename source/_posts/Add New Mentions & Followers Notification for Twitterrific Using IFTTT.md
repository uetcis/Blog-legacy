---
title: Add New Mentions & Followers Notification for Twitterrific Using IFTTT
date: 2019-04-27T15:45:48+08:00
tags: 
- Tech
categories: 
- Tech
- Programming
---
# Add New Mentions & Followers Notification for Twitterrific Using IFTTT
## The Problem
As 3rd-party Twitter client users, we have a problem with receiving push notifications.

So what should we do if we would like to get informed when someone follows us or mentions us? Go back to the official client?

Of course not. As Twitterrific has recently added a new URL Scheme which enables other apps to open twitter pages using it, we get to solve this problem elegantly using automation (through IFTTT).

## Solution
We're going to create an Applet in IFTTT, which sends a notification opening Twitterrific every time when there is a new mention or follower.

First, choose a trigger. Here we're choosing "New mention of you" or "New follower".

![Choose a Trigger](https://i.imgur.com/rk9Z9Ms.jpg)

Then, for action, use Notification and choose "Send a rich notification from the IFTTT app" so that we can send a notification with a URL.

![Choose an Action](https://i.imgur.com/NsjNCxc.jpg)

After that, customize the title, message and image URL depending on your own choice. For the link URL, paste `twitterrific://?url={{LinkToProfile}}` for new followers, or `twitterrific://?url={{LinkToTweet}}` for new mentions into that field.

![Set Link URL](https://i.imgur.com/xYyAcei.jpg)

There you go! Next time when there's a new mention/follower of you on Twitter, you can receive a notification that can open the corresponding page in Twitterrific.