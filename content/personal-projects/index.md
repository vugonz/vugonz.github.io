---
draft: false 
date: 2024-10-21T00:00:00Z
title: Personal Projects 
toc: true
---

## BitTorrent
I toyed with the BitTorrent 1.0 protocol for sometime. It was a very rewarding journey, as I learned a lot about it and many adjacent topics. I ended up developing a Leecher Client, a very rustic HTTP Tracker, a script for creating single file torrents and a Bencode library in Go, which is my current starting point to rewritting all these tools in Go.


Here is a video of the leecher downloading the Debian ISO.
{{<youtube MUlLtGutd-4>}}

**Tools**: Python, asyncio, Go

- [PyBT (leecher)](https://github.com/vugonz/pybt)
- [tracker](https://github.com/vugonz/tracker)
- [mktorrent-py](https://github.com/vugonz/mktorrent-py)
- [bencode-py](https://github.com/vugonz/bencode-py)
- [bencode-go](https://github.com/vugonz/bencode)

## Crawler
A little program I use to backup file servers over HTTP. Once the first backup is made the program keeps track of resources' last modified state and attempts to save as much bandwidth as possible on each run, much like git that only downloads the difference on pull. One downside of the current approach is that the metadata, which is stored in the filesystem, tends to eat up some space.

**Tools**: Go

- [cp-http](https://github.com/vugonz/cp-http)

## URL Shortener
My first attempt at writting boilerplate code for a layered architecture of a Golang API, focused on the standard library `net/http` package.

**Tools**: Go, Redis
- [url-shortener](https://github.com/vugonz/url-shortener)

## MC Proxy
A Java Minecraft Server reverse proxy with the intent of saving server resorces by idling the server on no activity. Still a work in progress.

**Tools**: Go

- [mc-proxy](https://github.com/vugonz/mc-proxy)

## Shell

A little shell I developed for the CodeCrafters [Build Your Own X](https://codecrafters.io/) challenge. It is super simple and straightforward. In the end I decided to do add some extra spice like pipe support. In the future I am also looking forward to enable input and output redirection, text navigation and command history.

**Tools**: Go

- [lil-shell](https://github.com/vugonz/lil-shell)

## RSS Subscriber

One of my first projects. Later I tried rewriting a more polished version with a configuration service but it never saw the light of day. This one is a simple script that is ran periodically as a cronjob. It subscribes to RSS feeds and posts updates to webhooks.

**Tools**: Python, asyncio, SQLite

- [cm-py](https://github.com/domingos-de-missa-dc/domingos-de-cm)