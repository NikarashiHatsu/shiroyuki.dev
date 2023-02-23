---
title: "Cara Mengatasi Could Not Resolve Hostname github.com Pada Linux"
date: 2023-02-23T12:00:20+07:00
tags: ["linux", "tips"]
author: "Aghits Nidallah"
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
description: ""
canonicalURL: ""
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
    image: "/images/article/cara-mengatasi-could-not-resolve-hostname-github-com-pada-linux/thumbnail.jpg" # image path/url
    alt: "Thumbnail" # alt text
    caption: "Foto oleh <a href='https://unsplash.com/@introspectivedsgn?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText'>Erik Mclean</a> dari <a href='https://unsplash.com/photos/sxiSod0tyYQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText'>Unsplash</a>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/NikarashiHatsu/shiroyuki.dev/tree/main/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---

This situation began when I planned to do a git pull on a project in CentOS 7
server. However, unlike my previous experience, the response that appeared was
as follows:

```
ssh: Could not resolve hostname github.com: Temporary failure in name resolution fatal: Could not read from remote repository.
Please make sure you have the correct access rights
and the repository exists.
```

It was very surprising, because previously I could always do the pull without
any problems. After consulting with someone who is an expert in handling
servers, the solution was quite simple, which is by editing the `/etc/resolv.conf`
file and adding the following two lines:

```
nameserver 8.8.8.8
nameserver 8.8.4.4
```

If you're new to using Linux, you can use the `nano /etc/resolv.conf` or
`vi /etc/resolv.conf` command to edit the file. However, it's important to
remember that the usage of these two text editors is different.

As additional information, if you don't have access to edit the file, you can
add the `pkexec` command before the `nano` or `vi` command. In this case, you
will be prompted to enter the root password to be able to edit the `resolv.conf`
file.

That's the end of my short tutorial and development notes. Hopefully, it's
useful and helps you all."

Thumbnail by <a href='https://unsplash.com/@introspectivedsgn?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText'>Erik Mclean</a> on <a href='https://unsplash.com/photos/sxiSod0tyYQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText'>Unsplash</a>