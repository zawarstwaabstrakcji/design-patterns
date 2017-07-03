---
title: Strategia
description: >
    Wzorzec strategia bywa użyteczny, gdy klasa może zachowywać się różnie w zależności od potrzeb. Gdy wykonanie metody
    wymaga użycia różnych algorytmów, możemy zapisać je w postaci klas i używać dokładnie jednego z nich w danym
    czasie.
enabled: true
type: Czynnościowy
usage_info: null
posts: []

---
{% block description %}
W sytuacji kiedy przebieg programu powinien zachowywać się różnie w zależności od zastanej sytuacji, możemy oczywiście
zrobić tzw. "drabinkę IFów" i cieszyć się z nieczytelnego kodu o dużej złożoności cyklomatycznej. Pomijając
oczywisty brak profesjonalizmu, łamiemy zasadę Open/Closed. Zatem zastanówmy się, jak stworzyć kod, który
będzie czysty i w przyszłości rozszerzalny.
{% endblock %}

{% block dummy_implementation_php %}
```language-php
interface Strategy
{
    public function doSomething();
}

class FirstStrategy implements Strategy
{
    public function doSomething() {
        return "I'm working under primary strategy!";
    }
}

class SecondStrategy implements Strategy
{
    public function doSomething() {
        return "I'm working under secondary strategy!";
    }
}

class Context
{
    private $strategy;

    public function __construct(Strategy $condition)
    {
        $this->strategy = $condition;
    }

    public function doSomethingUnderStrategy()
    {
        return $this->strategy->doSomething();
    }
}
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
Jak widać, klasa bazowa (Context) zależnie od zastanego warunku, inicjalizuje jedną z klas strategii. Klasy strategii
z kolei implementują wspólny interfejs Strategy z zadeklarowaną jedną metodą. W ten sposób, kiedy mamy sytuację
jak w przypadku na przykład wyboru odpowiedniego algorytmu wyszukiwania optymalnego do zastanych danych, możemy
wybrać odpowiednią strategię, która go implementuje. Strategie możemy dowolnie rozszerzać dodając kolejne klasy,
a ich znikoma złożoność bardzo ułatwia zrozumienie działania programu. Przećwiczmy wzorzec na przykładzie zniżek
w sklepie internetowym (zniżki nie mogą się sumować, gdyby mogły, lepiej użyć wzorca dekorator).
{% endblock %}

{% block example_specification %}
- Jako klient sklepu internetowego, mamy do wyboru różne zniżki
- Po wyborze rodzaju zniżki, informacja przechodzi do oprogramowania sklepu, gdzie używana jest odpowiednia strategia
zależna od wybranej zniżki
- Aplikacja oblicza finalną cenę przy użyciu udpowiedniej strategii
{% endblock %}

{% block example_code_php %}
```language-php
interface Discount
{
    public function calculateDiscount($price);
}

// when there is no reason for discount
class NoDiscount implements Discount
{
    public function calculateDiscount($price)
    {
        return 0;
    }
}

// receive $10 discount if total price is greater than $19
class DiscountCoupon implements Discount
{
    public function calculateDiscount($price)
    {
        if ($price > 19) {
            return 10;
        } else {
            return 0;
        }
    }
}

// whoa! lucky you, black friday today, all prices 30% off
class BlackFriday implements Discount
{
    public function calculateDiscount($price)
    {
        return $price * 0.3;
    }
}

// buy more, gain higher discount
class ProgressDiscount implements Discount
{
    public function calculateDiscount($price)
    {
        if ($price > 50) {
            return $price * 0.20;
        } elseif ($price > 10 && $price <= 50) {
            return $price * 0.1;
        } else {
            return $price * 0.05;
        }
    }
}

class Checkout
{
    private $price;
    private $discountStrategy;

    public function __construct($price, Discount $discount)
    {
        $this->price = $price;
        $this->discountStrategy = $discount;
    }

    public function getTotalPrice()
    {
        return $this->price - $this->discountStrategy->calculateDiscount($this->price);
    }
}

class CheckoutFactory
{
    public static function create($price, $discountType)
    {
        switch ($discountType)
        {
            case 'coupon':
                $discount = new DiscountCoupon();
                break;
            case 'black_friday':
                $discount = new BlackFriday();
                break;
            case 'progress':
                $discount = new ProgressDiscount();
                break;
            default:
                throw new RuntimeException('Unknown discount type');
        }

        return new Checkout($price, $discount);
    }
}
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
Udało nam się obliczyć ostateczną cenę przy użyciu wzorca strategia. Klasa Checkout pobiera wartość zamówienia oraz
rodzaj wybranego rabatu. Dzięki temu może wybrać odpowiedni algorytm obliczania zniżki i metoda getTotalPrice()
jest w stanie odpowiedzieć nam na pytanie ile użytkownik musi zapłacić. Oczywiście powyższy przykład jest bardzo
trywialny i w rzeczywistoście nie zdarza się aby w e-commerce występowały tak proste procesy, ale dzięki wzorcom
projektowym możemy nasz sklep internetowy zaprojektować w profesjonalny sposób.
{% endblock %}
