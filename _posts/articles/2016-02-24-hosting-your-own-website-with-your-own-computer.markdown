---
layout: post
title: "Hosting your Own Website with your Own Computer"
categories: articles
excerpt: "Can I host My Own Website with My Own Computer?"
date: 2016-02-24T10:51:49+08:00
search_omit: true_
---


This is an interesting topic. I collected this method while reading someone's idea from Quora.
These are the steps:

1. Linux is not the only OS you can use to host a website (although it is the most popular). Any computer (Windows, Mac, or Linux) can host a website. All you need is code compatible with that operating system, and to expose the port your website is on
2. You'll need an always-on computer. This means your laptop (and probably desktop) won't be good enough. You can find an old, cheap computer, put it in the closet, and keep it on at all times to host your site
3. For users outside of your network to reach it, you need to forward port 80 on your router to the web server. This usually isn't recommended for home networks, so make sure you know what you're doing
4. This may violate your ISP's terms of service. If you start to get notable traffic to the site, your ISP may drop you or take legal action. Non-business internet service almost always disallows this
5. You'll need to use a dynamic IP service. The IP address assigned to your home changes periodically, which is done by the ISP to protect you (for the most part). Dynamic IP addressing is less of a security risk (harder to track you, harder to attack a user, etc). To assign a URL to your IP, there are services out there that track your IP and dynamically update DNS, like DynDNS
6. You'll get attacked constantly. Now, these "attacks" aren't usually very difficult to defend against since they're just scripts looking for weak servers. If you enable SSH on port 22 you'll see hundreds of attempts to log in from servers in China. So make sure you have a basic understanding of firewalls and security.

It sounds like you really don't want to spend the money to host a site, but you'll take far more time setting up the server, networking, dynamic DNS, etc. Or you could just pay a couple dollars a month for someone else to do that for you.
