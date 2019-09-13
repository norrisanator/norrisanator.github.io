---
layout: post
title: SourceBans++ Bug
---

SourceBans++ is a global admin, ban, and communication management for the Source engine.


Version: SourceBans++ 1.5.4.7 and 1.7.0.931 thanks to github.com/rumblefrog

## Discovery

Normally with sourcebans you ban a player just using a steam id, and when you browse to that player from the dashboard
it takes you to the ban page.

**`index.php?p=banlist&advSearch=STEAM_X:X:XXXXXXXXX&advType=steamid&Submit`**

However if you do a IP ban instead then the url will display the ip that is banned.

**`index.php?p=banlist&advSearch=XXX.XXX.XXX.XXX&advType=ip&Submit`**

The issue with this is that if you are not logged in then you can view the ip of the player that is banned.

This isn't a very serious bug as this only accessable via the dashboard and the dashboard is often cleared very quickly,
due to the high number of bans coming through.


https://github.com/sbpp/sourcebans-pp/issues/599
