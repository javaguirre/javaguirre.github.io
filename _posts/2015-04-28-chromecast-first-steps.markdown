---
title: 'Chromecast, first steps'
date: 2015-04-28 06:15:52 +0000
tags: chromecast development
layout: post
subclass: 'post tag-test tag-content'
---
A friend of mine gave me a Chromecast for my birthday, and I gotta say I found it very nice and full of possibilities. Plenty of Android apps are prepared for streaming to chromecast and there are a few games that proves the potential of the platform too.

![](/content/images/2015/11/Chromecast.jpg)

I've used the apps from *rtve* and *atresplayer* with success (spanish tv), and I am able to stream my own videos with a great quality with [videostream][videostream] (I have the premium account), a flawless piece of software.

I wanted to know how to create apps for Chromecast, and It seems to be pretty straight forward.You need to develop a *sender* and a *receiver*, using Javascript, html and css. The sender is in the client (Chrome browser, Android/iOS device) and the receiver a web application that gets loaded in your chromecast when you launch the application in your device and connnect to It.

I started playing with it last Sunday, I used [this repository][sender_repo] as a sender and [this one as a receiver][receiver_repo]. It's important to know you need to register your device in the [Google Chromecast console][chromecast_console] (**5$ fee**) and then you will be able to test your apps in your chromecast before publishing. The receiver has to be in an url available for your chromecast (in your *LAN* is fine for testing, once you want to publish It will need to be in a public url obviously).

After registering the chromecast in the console and once It's ready, you need to plug/unplug your chromecast (reboot It from Android didn't work for me), otherwise your chromecast sender won't be able to detect the receiver.

[videostream]: https://chrome.google.com/webstore/detail/videostream-for-google-ch/cnciopoikihiagdjbjpnocolokfelagl?hl=en
[sender_repo]: https://github.com/googlecast/cast-sender-tool-chrome
[receiver_repo]: https://github.com/googlecast/cast-custom-receiver
[chromecast_console]: https://developers.google.com/cast/docs/registration

