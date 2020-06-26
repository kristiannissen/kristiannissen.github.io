---
layout: post
title:  "PHP Kode Udfordring men droppede afhængigheden til regex"
date:   2020-06-23 09:52:00 +0200
categories: php
tags: [PHP]
excerpt_separator: <!--more-->
---
Jeg droppede sent mandag aften at droppe den store afhængighed til regulære udtryk i min [template parser](https://github.com/kristiannissen/morningcoffee/blob/master/src/MorningCoffee/BashParser.php), problemet med at normalisere whitespace gjorde løsingen skrøbelig.
<!--more-->
## Læs 1 linje af gangen
I stedet for at være afhængig af regulære udtryk, valgte jeg at indlæse HTML en linje af gangen og håndtere den. På den måde kunne jeg droppe problemet med whitespace i min regulære udtryk. Det blev til en ekstra hjælpe klasse som gør det muligt at genbruge de samme streng funktioner igen og igen.

I min [Str klassen](https://github.com/kristiannissen/morningcoffee/blob/master/src/MorningCoffee/Str.php) implementerede jeg de klassiske funktioner som starts_with ends_with og lignende og kunne så nemt håndtere hver linje i HTML koden
```
if (Str::contains($line, '/([^el]if)\s?\[{2}/')) {
    // if [[ 1 + 1 = 2]] then
    $code = str_replace(
        ["[[", "]]", "=", " then"],
        ["(", ")", "==", ":"],
        trim($line)
    );
    $match = Str::matches($line, '/([^el]if)\s?\[{2}/');
    $content = substr_replace(
        $content,
        "<?php " . $code . " ?>",
        stripos($content, $match),
        strlen(trim($line))
    );
}
```
Hele [BashParseren](https://github.com/kristiannissen/morningcoffee/blob/master/src/MorningCoffee/BashParser.php) blev på denne måde meget mere stabil, Str klassen kunne godt trænge til lidt Unit tests for at sikre den ikke rammer forbi, men det bliver en anden dag.

Men denne løsning gør det også langt lettere at udvide BashParseren med flere muligheder end hvad der lige nu er muligt.