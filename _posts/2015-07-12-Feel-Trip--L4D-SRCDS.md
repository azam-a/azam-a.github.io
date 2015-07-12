---
layout: post
title: Feel Trip - l4d srcds
---

![_config.yml]({{ site.baseurl }}/images/l4d/overall.png)

Something had triggered my emotion this month. It reminds me of 6 years ago. 

I've recently resigned from my previous post this year, and was experiencing the same phase around the same period in 2009.

During that time, I was heavily absorbed by Left4Dead (L4D).
When I was studying in the UK (2005-2008), I actively played Counter-Strike:Source (CSS) on publicly-accessible dedicated servers, and there were some cool server-side plugins (Zombie mod, RPG mod, deathmatch-only mode, etc). Having a dedicated zombie-based FPS was a godsend.

Back in 2009, [Steam](http://store.steampowered.com/) was not so accessible to Malaysians, due to the high prices and lack of access to debit/credit cards. Most of us were still students, so the budgets were quite tight. 
Most of them didn't have legit L4D clients, so we had to find workarounds.

I set up a few instances of [SRCDS](https://developer.valvesoftware.com/wiki/Source_Dedicated_Server) on my home broadband (ADSL 4Mbit), hosting L4D games to my online friends.

To improve things further, I started looking for plugins and found [SourceMod](https://www.sourcemod.net/) and [Metamod](http://metamod.org/).

Before being bought by LogMeIn, [Hamachi](https://en.wikipedia.org/wiki/Hamachi_(software)) offered free VPN services. My SRCDS was configured to also accept non-steam clients to connect (I had to patch something IIRC) and I left the server running 24/7.

Using Hamachi, I assigned a static virtual IP for each user in the group. That way, their IP will remain constant and tracking plugins could track their in-game stats.

I installed a few plugins and tweaked some of them to cater for non-legit users. Since non-legit clients do not have [AuthId](https://sm.alliedmods.net/api/index.php?fastload=show&id=1100), I had to patch the plugins to use [ClientIP](https://sm.alliedmods.net/api/index.php?fastload=show&id=400).

![_config.yml]({{ site.baseurl }}/images/l4d/plugins.png)

Some plugins were not shown in the list. My HDD crashed in 2010, along with in-game screenshots :'(.

I would like to highlight a few memorable plugins used that greatly improved our in-game experience:

- [Map Decals](https://forums.alliedmods.net/showthread.php?t=69502)

This plugin allows the server host to put decals on map and they will be automatically loaded to all connected players. I saw this first whilst playing on some CSS servers, where the server owners put advertisements on in-game items (walls, cars, advert boards, etc).

Actually I pitched the in-game advertising/monetising business model in my enterpreneurial subject presentation during my undergrad years, but the assessors were too out of touch to understand.

- [Stripper:Source](https://forums.alliedmods.net/showthread.php?t=39439)

Stripper allows the admin to spawn items in predetermined coordinates throughout the maps. Seeing the news about [L4D censorship in Germany](http://left4dead.wikia.com/wiki/International_Weapons), I used to plugin to spawn the region-restricted weapons in the maps, allowing all players in the server to get access to the extra weapons.

Screenshot related to ChatLogEx, I will describe later: 
![_config.yml]({{ site.baseurl }}/images/l4d/stripper1.png)
![_config.yml]({{ site.baseurl }}/images/l4d/stripper2.png)

- [ChatLogEx](https://forums.alliedmods.net/showthread.php?p=817310)

I would say that this is one of the best plugins ever. This plugin allows servers to log game events to database (connections, kills, completion, etc). There was a companion web-app written in PHP to output the logged events from the database to web browser.

To make things more interactive, I incorporated [Ruckman RCON Source PHP](http://www.ruckman.net/downloads-1), which is a web-based utility for [RCON](https://developer.valvesoftware.com/wiki/Source_RCON_Protocol).
Combined with ChatLogEx, external users could interact with the players currently in-game, using web chats. Messages from the web interface will be displayed in-game, and vice-versa in real-time.

Luckily at that time, most players did not have microphones for in-game communications. In L4D, communication is essential, so they tend to use text-based in-game chats a lot. This also allowed me to monitor the game whilst I was away from the machine (office, hometown, etc).

Some screenies for feel trip:
![_config.yml]({{ site.baseurl }}/images/l4d/chatlogex-lyn1.png)
![_config.yml]({{ site.baseurl }}/images/l4d/chatlogex-lyn2.png)
![_config.yml]({{ site.baseurl }}/images/l4d/chatlogex-lyn3.png)
![_config.yml]({{ site.baseurl }}/images/l4d/chatlogex-lyn4.png)
![_config.yml]({{ site.baseurl }}/images/l4d/chatlogex-lyn5.png)
![_config.yml]({{ site.baseurl }}/images/l4d/chatlogex-lyn6.png)
![_config.yml]({{ site.baseurl }}/images/l4d/chatlogex-lyn7.png)
![_config.yml]({{ site.baseurl }}/images/l4d/chatlogex-lyn8.png)

Even players from other countries were also having fun:
![_config.yml]({{ site.baseurl }}/images/l4d/chatlogex-ext1.png)
![_config.yml]({{ site.baseurl }}/images/l4d/chatlogex-ext2.png)

I also created dynamic SRCDS server status images:

![_config.yml]({{ site.baseurl }}/images/l4d/status1.png)
![_config.yml]({{ site.baseurl }}/images/l4d/status2.png)

These dynamic images were embedded in the [main website]({{ site.baseurl }}/images/l4d/overall.png) as well as forum signature.

The main website provides a one-stop centre for spectators to check in-game status, chat with the players, set up game sessions, and view server availability.

I managed to salvage some of my scripts used in the setup ([website](https://github.com/azam-a/feeltrain/tree/master/stats), [dynamic signature](https://github.com/azam-a/feeltrain/tree/master/query)).
Bear in mind that it was back in 2009, where I didn't really know how to program. The scripts and plugins were hacked together via trial-and-error, with no clear understanding of basic programming and file versioning concepts.

Looking back, it was a fun year. People grew up and have their own commitments now. I do hope we could re-group again for L4D sessions, provided I have enough time to do the whole setup again.

-End-
