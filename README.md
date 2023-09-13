# SAMonitor
[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2Fmarkski1%2FSAMonitor.svg?type=shield)](https://app.fossa.com/projects/git%2Bgithub.com%2Fmarkski1%2FSAMonitor?ref=badge_shield)


This is the monorepo for SAMonitor, a free and open source server monitor, tracking over a thousand SA-MP and open.mp servers.

Providing: A server browser, a public API and a Masterlist alternative.

## Contents

- [Server browser](#server-browser)
- [Masterlist](#masterlist)
- [API Endpoints](#api-endpoints)
  - [GetAllServers](#getallservers)
  - [GetFilteredServers](#getfilteredservers)
  - [GetServerByIP](#getserverbyip)
  - [GetServerPlayers](#getserverplayers)
  - [GetTotalPlayers](#gettotalplayers)
  - [GetGlobalMetrics](#getglobalmetrics)
  - [GetServerMetrics](#getservermetrics)
- [Data schemas](#data-schemas)
- [Add your server](#add-your-server)

## Server browser

The server browser is available at https://sam.markski.ar

It consumes the public API endpoints listed below.

It is written in HTMX and PHP, which provides a very lightweight website where logic largely runs on the server side. Ideal for the types of computers and phones to be expected in regions where San Andreas Multiplayer remains popular.

## Masterlist

We offer a masterlist alternative, at `http://gateway.markski.ar/api/GetMasterlist`.

You may specify a `version` parameter. Example: `http://gateway.markski.ar/api/GetMasterlist?version=0.3.7`

It is a relatively high-quality masterlist, since rather than functioning as a hastily-updated text file or database, it only provides servers which are actively running.
Servers which failed to be online in the last ~6 hours are not included in this list (but are re-added when they return).

If you wish to use SAMonitor's masterlist in SA-MP, check out [SA-MP Masterlist Fix](https://github.com/spmn/sa-mp_masterlist_fix)!

## API Endpoints

All of these endpoints are located at `http://sam.markski.ar/api/`.

Note that this endpoint ONLY replies to standard GET http queries. If that doesn't work, try `http://gateway.markski.ar/api/`. Both return the same data, but `gateway` does not go benefit from Cloudflare's network, which may make it slower.

### GetAllServers

Return a JSON collection with the latest information of every server SAMonitor tracks.

Try it: http://sam.markski.ar/api/GetAllServers

### GetFilteredServers

The most complex endpoint of all, but certainly worth it: Returns the list of servers, but with specified filtering.

By default, it'll omit severs with 0 players.

Parameters, all of which are optional: 
```
  show_empty:
   - Possible values: 0 or 1. If unspecified, 0.

  order:
   - Possible values: "none", "players", "ratio". If unspecified, "none".

  name, gamemode:
   - Possible values: Any specified text. This is basically a search.
  
  paging_size:
   - Provide any number greater than 0 to do paging.
     If specified, only this amount of entries will be returned.

  page:
   - Provide any number greater or equal than 0.
     Specifies the page, to be used along with paging_size.
```

Try it: http://sam.markski.ar/api/GetFilteredServers?name=Roleplay&order=player

### GetServerByIP

Provided an IP address (optionaly with a specified port), returns information about it.

If no port is provided and several servers are under that IP, the one at 7777 will be chosen.

Try it: http://sam.markski.ar/api/GetServerByIP?ip_addr=151.80.19.151:7777

### GetServerPlayers

Provided an IP address (optionaly with a specified port), return a list of players.

Try it: http://sam.markski.ar/api/GetServerPlayers?ip_addr=51.68.204.178:7777

### GetTotalPlayers

Get a simple integer with the sum of players in the tracked servers.

Try it: http://sam.markski.ar/api/GetTotalPlayers

### GetAmountServers

A little pointless for most, but returns the amount of servers currently online.

Optionally, use `include_dead=1` to retrieve the total amount of servers being tracked, including offline.

Try it: http://sam.markski.ar/api/GetAmountServers

### GetGlobalMetrics

Providing an amount of hours, get metrics for global count of players and servers in the last given hours.

If no hour is provided, defaults to 6.

Try it: http://sam.markski.ar/api/GetGlobalMetrics?hours=6

### GetServerMetrics

Providing an IP address and an amount of hours, get metrics for the count of players in the last given hours.

If no hour is provided, defaults to 6.

Try it: http://sam.markski.ar/api/GetServerMetrics?hours=6&ip_addr=51.68.204.178:7777

NOTE: By default, returns no entries for times a server failed to respond to a query. To have these, use `include_misses=1`. These are marked with a player count of -1, and can be used to (somewhat loosely) track downtime.

## Data schemas

There are two schemas: Player and Server.

The player schema is used for GetServerPlayers. It is the simplest schema, and will likely never change.

```
  id:    Integer
  ping:  Integer
  name:  String
  score: Integer
```

The server schema is likely to change as SAMonitor is fairly in-development. However breaking changes are unlikely and it might just come in the form of adding more fields.

```
  success:       Boolean
  lastUpdated:   DateTime
  worldTime:     DateTime (only the Time portion has the relevant data)
  playersOnline: Integer
  maxPlayers:    Integer
  allowsDL:      Boolean
  lagComp:       Boolean
  name:          String
  gameMode:      String
  ipAddr:        String
  mapName:       String
  website:       String
  version:       String
  language:      String
  sampCac:       String (Version of SAMPCAC required. "Not required" otherwise.)
```

The API ***should*** never return any null values. Either '0' or "Unknown" would come in their placee where appropiate, or at worst, an empty [] response. Still, because bugs can exist, you may wish to account for the possibility of 'null' returns.

## Add your server

You may add your server through the option at the SAMonitor website.

## License
[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2Fmarkski1%2FSAMonitor.svg?type=large)](https://app.fossa.com/projects/git%2Bgithub.com%2Fmarkski1%2FSAMonitor?ref=badge_large)