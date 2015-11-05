---
title: Dekorator
description: >
    Wzorzec dekorator stosowany jest tam, gdzie dziedziczenie nie jest optymalnym sposobem rozszerzania funkcjonalności
    klasy. Polega na dołączeniu nowych atrybutów poprzez “opakowanie” obiektu bazowego obiektem zwanym dekoratorem.
enabled: true
type: Strukturalny 
usage_info: null
posts: []

---
{% block description %}
Wyobraźmy sobie sytuację, gdzie kombinacja wielu dodatków do bazowej klasy spowodowałaby rozlew klas dziedziczących,
które programista musiałby zaimplementować w aplikacji, na przykład:
PizzaWithPepperoniAndTabasco extends Pizza, PizzaWithPepperoniAndMushrooms extends Pizza i tak dalej. Można sobie łatwo
wyobrazić jak wiele typowa pizzeria może stworzyć kombinacji tego smacznego placka. Z pomocą przychodzi wzorzec
dekorator, który umożliwia nam rozszerzanie funkcjonalności obiektu w czasie pracy aplikacji. 
{% endblock %}

{% block dummy_implementation_php %}
```language-php
interface SomeFancyInterface
{
    public function getInfo();
}

class BasicObject implements SomeFancyInterface
{
    public function getInfo()
    {
        return "I'm basic object";
    }
}

class Decorator implements SomeFancyInterface
{
    private $object;

    public function __construct(SomeFancyInterface $object)
    {
        $this->object = $object;
    }

    public function getInfo()
    {
        return $this->object->getInfo() . " decorated!";
    }
}

$object = new BasicObject();
$decoratedObject = new Decorator($object);

echo $object->getInfo() . PHP_EOL; // I'm basic object decorated!
```
{% endblock %}

{% block dummy_implementation_ruby %}
```language-ruby

```
{% endblock %}

{% block dummy_implementation_java %}
```language-java

```
{% endblock %}

{% block dummy_implementation_c_sharp %}
```language-csharp

```
{% endblock %}

{% block when_to_use %}
Dekorator jest niezwykle użyteczny w momencie kiedy naturalnym zachowaniem pewnego obiektu jest jego podatność na 
rozszerzenia. Można wyobrazić sobie wiele przypadków użycia, jak na przykład proces zakupu samochodu (dodwanie nowych
opcji, jak nawigacja, przyciemniane szyby), wspomniana wyżej pizzeria, polisa ubezpieczeniowa itp. Zaprojektujmy zatem
banalny model dla sprzedaży telewizji kablowej, skupmy się na ofercie.
{% endblock %}

{% block example_specification %}
- Mamy podstawową ofertę
- Klient może do oferty dodać opcje: pakiet sportowy, edukacyjny i filmowy
- Zbudujmy finalną ofertę za pomocą dekoratorów
{% endblock %}

{% block example_code_php %}
```language-php
interface Offer
{
    public function getPrice();
}

final class BaseOffer implements Offer
{
    public function getPrice()
    {
        return 40;
    }
}

final class SportDecorator implements Offer
{
    private $offer;

    public function __construct(Offer $offer)
    {
        $this->offer = $offer;
    }

    public function getPrice()
    {
        return $this->offer->getPrice() + 20;
    }
}

final class EducationDecorator implements Offer
{
    private $offer;

    public function __construct(Offer $offer)
    {
        $this->offer = $offer;
    }

    public function getPrice()
    {
        return $this->offer->getPrice() + 10;
    }
}

final class CinemaDecorator implements Offer
{
    private $offer;

    public function __construct(Offer $offer)
    {
        $this->offer = $offer;
    }

    public function getPrice()
    {
        return $this->offer->getPrice() + 30;
    }
}

$offer = new BaseOffer();
$offerWithSport = new SportDecorator($offer); 
$offerWithSportAndCinema = new CinemaDecorator(new SportDecorator($offer));
$fullOffer = new CinemaDecorator(new SportDecorator(new EducationDecorator($offer))); 
```
{% endblock %}

{% block example_code_ruby %}
```language-ruby

```
{% endblock %}

{% block example_code_java %}
```language-java

```
{% endblock %}

{% block example_code_c_sharp %}
```language-csharp

```
{% endblock %}

{% block example_explanation %}
Wprowadziliśmy wspólny interfejs dla podstawowej klasy jak i klas dekoratorów. Z racji tego, że każdy obiekt dekorowany
będzie spójny interfejsem z obiektem bazowym, możemy swobodnie dekorować obiekty zmieniając ich właściwości. Powyższy
przykład został zaimplementowany przy użyciu interfejsów, ale równie dobrze można go zaimplementować przy użyciu klas
abstrakcyjnych.
{% endblock %}
