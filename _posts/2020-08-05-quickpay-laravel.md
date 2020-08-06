---
layout: post
title:  "QuickPay Laravel pakke"
date:   2020-08-05 16:00:00 +0200
categories: quickpay laravel
tags: [Laravel, QuickPay]
excerpt_separator: <!--more-->
---
Jeg valgte så at bruge en stor del af min sommerferie op at implementere en Laravel specifik [QuickPay pakke](https://github.com/kristiannissen/laravel-quickpay). De små grå trængte til lidt motion. Det var ikke helt nemt, selve opsætningen, som jeg egentlig forventede ville være temmelig out of the box var ret besværlig.
<!--more-->
Det, at få PHPUnit tests til at fungere med denne løsning, var ret krævende. Underligt nok. Det jeg ville opnår var, at jeg i min løsning kunne gøre brug af Laravels helpers, som __event()__ og __config()__, så jeg ikke skulle implementere mine egne funktioner for at gøre, hvad Laravel allerede har indbygget.

Specielt denne del af min løsning synes jeg er ret spiffy

```
public function validateParams(array $rules = [], array $data_types = [])
{
    $rule_keys = array_keys($rules);
    $data_keys = array_keys($data_types);
    foreach ($rule_keys as $rule_key) {
        list($name, $type) = explode(':', $rule_key);
        if (in_array($name, $data_keys) == false) {
            throw new \InvalidArgumentException(
                sprintf('The parameter %s is missing!', $rules[$rule_key])
            );
        } elseif (gettype($data_types[$name]) !== $type) {
            throw new \UnexpectedValueException(
                sprintf(
                    'The parameter %s should be of type %s but %s received!',
                    strtoupper($name),
                    strtoupper($type),
                    strtoupper(gettype($data_types[$name]))
                )
            );
        }
    }
}
```

Lækker, ikke? Det giver ikke så megen mening at sidde og se på uden for kontekst, men lad mig forklare ideen.

### QuickPay dokumentation
QuickPay dokumentationen beskriver hvordan du sender en HTTP request til en given service, og i dokumentationen fortæller de også, hvilke parametre der er påkrævet og hvilket er frivillige.

Tag deres [subscription service](https://learn.quickpay.net/tech-talk/api/services/#POST-subscriptions---format-) som eksempel. For at kunne oprette en ny subscription, skal følgende parametre sendes til deres endpoint som en del af det HTTP request du laver

* order_id
* currency
* description

Hvis jeg sender en request til deres endpoint uden en af ovenstående parametre, eller hvis jeg sender en request hvor jeg f.eks sender currency som et tal, kaster de en eksemption retur i hovedet på mig.

Jeg lavede derfor ovenstående parameter validering, så der ikke laves et kald til deres endpoint som ikke returnerer de data man forventer.

Her er et eksempel på hvad der sker når en obligatorisk parameter mangler:

```
$service = new SubscriptionService();

$subscription = $service->create([
  'currency' => 'DKK',
  'description' => 'Your Hello Kitty Subscription',
]);
```

Vil returnere en InvalidArgumentException exception, det burde måske være en MissingArgumentException i stedet, fordi order_id er en obligatorisk parameter. Dette request vil ikke ramme QuickPays endpoint.

Her er et eksempel på hvad der sker når en obligatorisk parameter har en forkert type:
```
$service = new SubscriptionService();

$subscription = $service->create([
  'order_id' => 666,
  'currency' => 'DKK',
  'description' => 'Your Hello Kitty Subscription',
]);
```

Vil returnere en UnexpectedValueException exception, fordi order_id i førlge dokumentationen skal være en tekst streng og ikke en integer. Dette request vil heller ikke ramme QuickPays endpoint.

På den måde kan dokumentationen for obligatoriske parametre og deres typer, bliver til regler som er implementeret lidt i stil med Laravels Validator, jeg smider bare en exception i stedet for en boolsk værdi.

Og du får en, synes jeg selv, ret pædagogisk fejlbesked som fortæller dig hvad problemet er. Du kan se her, hvordan reglerne for [obligatoriske parametre](https://github.com/kristiannissen/laravel-quickpay/blob/b035f541724579b93efa657adabe854d7e0b9045/src/Subscription/SubscriptionService.php#L18) og deres typer angives.