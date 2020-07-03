---
layout: post
title:  "Abstract Data Type og Graph strukturer"
date:   2020-07-03 08:13:00 +0200
categories: php
tags: [PHP, Data Structures, ADT, Graph]
excerpt_separator: <!--more-->
---
Ideen om at udvikle et Asset Management system stammer fra en situation jeg sidder i på mit arbejde, hvor jeg er ved at indkøbe en ny bærbar til en kollega. Det kombineret med en anden situation, hvor jeg skulle klargøre en bærbar til en ny medarbejder.
<!--more-->
Hvis ikke du ved hvad jeg mener med et Asset Management system, så det jeg har i tankerne et system hvor jeg kan se hvem der har hvilke bærbare og stationære computere, hvilke af disse har vi stadig garantier på, og hvilke maskiner har jeg problemer med.

Det der begejstrer de små grå, er kompleksiteten i den underliggende data struktur bag sådan en løsning. Selvfølgelig kan jeg bare lave en relationel database hvor jeg har medarbejdere i én tabel, hardware i en anden osv. Men det er sgu for kedeligt, næh, at udvikle det ved hjælp af en graph database, dét er interessant.

## Hierarkisk træ struktur
I første omgang vil jeg prøve at repræsentere data ved hjælp af en træ struktur og se, hvor langt jeg kan komme inden jeg løber ind i problemer. Jeg ved allerede nu, at idet to noder med samme forældre, ikke kan linke til samme "barn" (terminologien er fucked) kommer til at give problemer. Hvorfor? Forestil dig en data struktur, hvor f.eks 2 bærbare maskiner skal have en forbindelse til samme problem. Hvordan vil du præsentere det i en struktur som denne?

![hierarchical tree](/assets/img/tree.png)

"A" kan være IT-udstyr, bare for at holde det hele indenfor samme terminologi. "C" er hardware og "H" kan være min egen Mac Air, eller min arbejdstelefon er måske bedre. Min telefon bliver stjålet, hvilket oprettes som et incident "M" i denne struktur.

Lad os antage at en kollega er ude for det samme, hans/hendes/dens telefon bliver stjålet, ikke af den samme tyv, men jeg vil gerne linke til samme incident, måske fordi begge telefoner er iPhones, hvad ved jeg. Kollegaen's telefon kan være "G".

### Begrænsninger i et hierarkisk træ
Tænk på et rigtigt træ, 2 grene kan ikke vokse samme og blive til én gren, ikke i teorien, hvad der sker i naturen er noget andet. Det betyder at "H" og "G" ikke begge kan linke til "M", hvilket vil give mig dubletter.

Jeg kan heller ikke flytte alle incidents over i "D" og link "H" og "G" over i "D", der mås man ikke "the computer says no!".

Det er her en graph database kommer ind i billedet idet en graph database primært fokuserer på forbindelsen mellem objekter.

![hierarchical tree](/assets/img/graph.png)

Som det kan ses på dette fine billede kan alle "noder" i en graph struktur være forbundet med hinanden. I eksemplet med min telefon er det muligt at linke flere "noder", eller telefoner, til samme incident. På den måde vil jeg kunne se alle stjålne telefoner på tværs af træet ved at se på "M".

### Data struktur
I [gundb](https://gun.eco/) er det lykkedes Mark Nadal at lave en graph database i JSON, det er et skide spændende projekt og den knægt er vild, han har nogle vilde ideer. Men jeg vil helst lave en løsning som er nem at hoste, som ikke kræfer Node.js men kan køre hvor PHP kan køre og samtidig vil jeg helst have data i en database og ikke som en json fil på en server. Jeg er gammeldags når det kommer til den slags, jeg ved det.

Et første eksperiment på en JSON baseret struktur

{% highlight json %}
{
  "hardware": {
    "phone": {
      "uuid": "e2616458-a6c1-40e0-86f9-2a2354fa2a3a",
      "employees": {
        "a49d4eec-a99b-4835-87d5-69499c18b55b": "kristiannissesn"
      }
    },
    "laptop": {
      "uuid": "916bf2c6-061a-4fbf-a972-f3971247b799"
    }
  },
  "employees": {
    "kristiannissen": {
      "uuid": "a49d4eec-a99b-4835-87d5-69499c18b55b",
      "hardware": {
        "e2616458-a6c1-40e0-86f9-2a2354fa2a3a": "phone",
        "916bf2c6-061a-4fbf-a972-f3971247b799": "laptop"
      }
    },
    "wonderwoman": {
      ...
    },
    "batwoman": {
      ...
    }
  },
  "incidents": {
    "stolenshit": {
      "uuid": "4b693f1f-f8d1-4269-80aa-83ea12e58d8a",
      "hardware": {
        "e2616458-a6c1-40e0-86f9-2a2354fa2a3a": "phone",
      }
    }
  }
}
{% endhighlight %}

Bemærk, der er ingen Arrays i denne struktur, det er kun key/value værdier, hvorfor? Firebase! I firebase som er en JSON database er det ikke muligt at gennem et Array fordi det ikke har et index, ikke et synligt index.

Men det gør bare strukturen meget kompleks at vedligeholde og forstå når man læser den. Men fordelen er, at du kan søge i den ved hjælp af keys.

{% highlight json %}
"hardware/iphone/employees"
{% endhighlight %}

Eller hvis du vil slå en medarbejder op

{% highlight json %}
"employees/kristiannissen/hardware"
{% endhighlight %}

Og på samme måde kan incidents findes frem og derfra hvilket hardware der er tale om og videre til, hvem der ejede det hardware der blev stjålet.

Men udfordringen er fortsat at finde ud af, hvordan jeg gemmer ovenstående og hvordan jeg indexerer data så jeg hurtigt kan søge i det. Der kunne være et index ved siden af, hvor alle Uuid'er er gemt samt hvor i strukturen de hører til, men den mangler jeg at knække. Gun har en interessant løsning på det som jeg skal have set mere på.

### PHP Pseudo Code
For at forsøge at repræsentere ovenstående struktur i PHP forestiller jeg mig noget i stil med følgende

{% highlight json %}
<?php

namespace WeirdStructures;

class Node implements NodeInterface {
  public $uuid;
  public $type;
  public $name;

  private $childNodes;

  public __construct(string $type, string $name) {
    $this->type = $type;
    $this->name = $name;
    $this->childNodes = [];
  }
  ...
  public addChildNode(Node $node) {
    $this->childNodes[$node->uuid] = $node;
  }
  ...
}
{% endhighlight %}

Jeg valgte at min klasse her skal implementere et interface, jeg er ikke helt sikker på hvorfor, men hvis jeg på et tidspunkt for brug for at repræsentere en node som noget andet, kan et interface være en god idé da koblingen mellem typerne ikke er så stærk så længe de implementerer samme interface.

Men jeg mangler fortsat at finde ud af hvor dan jeg nemt kan søge ned igennem strukturen og også hvordan jeg gemmer flere informationer om f.eks en telefon? Jeg kan ikke nøjed med at sige at medarbejder X ejer telefon Y. Telefonen er nød til også at have et serie nummer og lign informationer i strukturen for at det kan bruges til noget.

{% highlight json %}
"phone": {
  "uuid": "e2616458-a6c1-40e0-86f9-2a2354fa2a3a",
  "employees": {
    "a49d4eec-a99b-4835-87d5-69499c18b55b": "kristiannissesn"
  },
  "attributes": {
    "serialnumber": "666",
    "manufacturer": "wauwey"
  }
}
{% endhighlight %}

Selve pseudo koden kunne se ud som følgende, lidt inspireret at Eloquent fra Laraval og Doctrine som jeg arbejdede med for 100 år siden.

{% highlight php %}
<?php

namespace WeirdStructures\QueryLang;

class Query {
  protected $path;

  public function __construct() {
    $this->path = "";
    return $this;
  }
  ...
  public function find(string $query) {
    $this->path = explode('/', $query);
    ...
  }
}
{% endhighlight %}

Ovenstående skulle så anvendes på følgende måde lidt inspireret af en Xpath lignende [syntaks](https://devhints.io/xpath)

{% highlight php %}
<?php

use WeirdStructures\QueryLang\Query;

$stolen_hardware = new Query()
  ->find("//incidents/stolenshit/hardware");

$stolen_wauwey_hardware = new Query()
  ->find("//incidents/stolenshit/hardware/[@manufacturer='wauwey']");

$all_waywey_shit = new Query()
  ->find("//hardware/[@manufacturer='wauwey']")
{% endhighlight %}

Der er fortsat en del der skal på plads inden jeg kan gå i gang med implementeringen og nogle gange er det arbejdet med hvordan, der er det interessante ved ideen, mere end selve implementeringen.