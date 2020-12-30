---
layout: post
title:  "Go lang interface"
date:   2020-12-30 13:35:00 +0200
categories: golang
tags: [golang]
excerpt_separator: <!--more-->
---
I det projekt jeg pt arbejder på for at lære Golang ville jeg implementere en REST service som kunne returnere nogle aggregerede data til et dashboard.<!--more-->
Men idet Go lang er type stærkt er det ikke muligt at lave et JSON response som indeholder andet end 1 bestemt type, her er et eksempel
```
package main

import (
	"fmt"
)

func main() {
	data := make(map[string]string)
	data["hello"] = "Kitty"

	fmt.Println(data)
}
```
Output af ovenstående vil være *map[hello:Kitty]*, men min service skal både kunne returnere string, int og nested maps.

## Interface
For at kunne returnere et map som indeholder flere forskellige værdier end kun string, kan jeg bruge et Interface som i eksemplet her under
```
package main

import (
	"fmt"
)

type Data interface{}

func main() {
	data := make(map[string]Data)
	data["hello"] = "Kitty"
	data["kitty"] = 42

	fmt.Println(data)
}
```
Output af ovenstående er *map[hello:Kitty kitty:42]*
## REST service
På denne måde kan min REST service returnere flere forskellige typer af data som vist her under (beklager formateringen)
```
{
FileModTime: "2020-12-30T11:18:00Z",
HopsData: {
Argentina: 1,
Australia: 15,
Austria and Slovenia: 1,
Belgium: 5,
Canada: 2,
Czech Republic: 2,
Czechia: 8,
Czechoslovakia: 1,
Former Yugoslavia: 1,
France: 6,
Germany: 27,
Japan: 9,
New Zealand: 23,
Poland: 3,
Russia: 1,
Serbia: 2,
Slovenia: 16,
South Africa: 3,
Switzerland: 1,
UK: 45,
UK / France: 1,
US: 72,
Uncertain (Belgium or Denmark): 1,
Unknown: 21
},
NumberOfHops: 267
}
```
Go lang interface giver dig mulighed for at blande forskellige data typer i et map.
