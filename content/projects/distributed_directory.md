---
title: "Distributed directory"
date: 2020-12-14T20:21:00+01:00
draft: false
description: "Tool for data synchronization between computers in P2P manner"
tags: [
    "p2p", "csharp"
]
---

<p align="center">
    <img src="/multiple_sources.png" width="400">
</p>

Carried out in the form of an engineering thesis.
Program that can exchange data between computers without central server in a way similar to BitTorrent.

The project has been divided into three parts:
* Backbone - part responsible for establishing P2P network connections - fully published on Github and as NuGet package  - [more details here](/projects/p2p_network_builder)
* Distributed directory - part described in this post - not  published online yet
* Mobile client - based on Xamarin - [more details here](/projects/distributed_directory_mobile)

Project described in details here (polish version only): [praca_inzynierska.pdf](/praca_inzynierska.pdf)

Although the project has been brought to a working state (synchronization of files between devices is possible), the amount of work required to polish it and fix all bugs led me to use another open-source solution for daily basis: [Syncthing](https://syncthing.net/).