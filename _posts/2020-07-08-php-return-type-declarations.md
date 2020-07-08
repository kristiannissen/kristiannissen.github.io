---
layout: post
title:  "PHP Return type declarations"
date:   2020-07-08 23:02:00 +0200
categories: php
tags: [PHP]
excerpt_separator: <!--more-->
---
Der er virkelig kommet nogle fede features in PHP 7, jeg har ikke programmeret, sådan rigtigt, i PHP i nogle år, det har mere været Python og lidt Java som jeg opgav pga Googles elendige Gcloud dokumentation.
<!--more-->
En af de features jeg opdagede ved et tilfælde er **return type declarations**, noget jeg synes er virkelig fedt og som er med til gøre koden langt mere læsbar.

## Return type declarations
Lad mig vise et eksempel af, jeg taler om med lidt OOP kode fra den kode udfordring jeg gennemførte for nogle dage siden.

{% highlight php %}
...
public function parseContent()
{
  $contents = $this->parser->parse($this->file_contents);

  return $contents;
}
{% endhighlight %}

I eksempel herover har jeg endnu ikke tilføjet return type declaration men som navnet antyder, skal metoden fortælle retur værdien, string, boolean osv.

{% highlight php %}
...
public function parseContent() : string
{
  $contents = $this->parser->parse($this->file_contents);

  return $contents;
}
{% endhighlight %}

I eksemplet herover fortæller jeg, at metoden parseContent() returnerer en streng. Hvorfor er det smart? Det hjælper med at læse koden idet du nu ved, hvad du skal forvente denne metode returnerer.

Der hvor jeg synes filmen knækker for gutter bag PHP 7 return type declaration, er der, hvor de gør retur typen nullable... Hvorfor dog det?

{% highlight php %}
...
public function parseContent() : ?string
{
  $contents = $this->parser->parse($this->file_contents);

  return $contents;
}
{% endhighlight %}

Ved at ændre string til **?string**, kan metoden enten returnere en streng, eller null. Men hvorfor dog blande tingene sammen?! Måske det bare er fordi jeg er gammel og tænker på sprog som Java, hvor du ved præcist, hvad en metode returnerer.

I stedet for ?string som gør retur typen nullable, mener jeg man skal holde sig til samme retur type. Hvis du forventer en metode skal returnere en streg eller et array, giver det i mit hoved ingen mening at samme metode også kan retunere null. Hvorfor kan metoden ikke bare returnere et array som enten indeholder data og hvis ikke returnerer metoden et tomt array, men typen er den samme.

### Hvorfor er det en fordel?
Fordi du kan stole på, at metoden altid returnerer samme type, så du kan håndtere retur værdien som den samme type og ikke 2 forskellige typer. Det mener jeg gør din kode langt mere anvendelig og det betyder samtidig at du altid kan "stole på" hvad metoden returnerer.

### Pseudo kode eksempel forude
Her er et eksempel på, hvad jeg mener med ovenstående argument

{% highlight php %}
if (count($api->get_array()))
{
  foreach ($api->get_array() as $key => $val)
  {
    echo "Hello Pussy, have a $key with a $val";
  }
}
{% endhighlight %}

Ovenstående vil fungere lige meget om get_array() metoden (beklager navngivningen) returnerer et tomt array eller et fyldt array. Hvor imod, hvis get_array() returnerer null i stedet for et tomt array, skal jeg tage højde for 2 retur typer.