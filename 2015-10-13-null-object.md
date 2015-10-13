---
title: Null Object
description: >
    Wzorzec polega na utworzeniu klasy implementującej konkretny interfejs, jednak nie posiadającej żadnego zachowania. 
    Wszystkie metody wymagane przez interfejs zwracają pusty wynik lub niezmodyfikowane argumenty i nie wykonują żadnych operacji. 
enabled: true
type: Czynnościowy
usage_info: >
    Wykorzysatnie tego wzorca może utrudnić debugowanie kodu. Ma to miejsce w przypadku kiedy spodziewamy się 
    jakiegoś zachowania patrząc na wykorzystany interfejs, jednak jego implementacja opiera się o wzorzec <strong>Null Object</strong>.
posts: []
    
---
{% block description %}
Wzorzec polega na utworzeniu obiektu implementującego konkretny interfejs, jednak nie posiadającego żadnego zachowania. 
Wszystkie metody wymagane przez interfejs zwracają pusty wynik lub niezmodyfikowane 
argumenty i nie wykonują żadnych operacji.
{% endblock %}

{% block dummy_implementation_php %}
```language-php
interface CalculationStrategy
{
    public function calculate($input);
}

final class NullStrategy implements CalculationStrategy
{
    public function calculate($input) 
    {
        return 0;
    }
}
```
{% endblock %}

{% block when_to_use %}
Wzorzec znajduje zastosowanie w przypadkach kiedy implementacja danego interfejsu jest wymagana, jednak samo zachowanie 
nie jest istotne, lub kiedy implementacja interfejsu reprezentuje konkretną strategię. 
Wzorzec z powodzeniem można zastosować w celu wyeliminowania konieczności tworzenia argumentów opcjonalnych.
Bardzo dobrze sprawdza się w środowiskach testowych.  
Może również posłużyć do rozpoznawania stanu, przykładowo jeżeli encja wymaga identyfikatora nadawanego przez zewnętrzną usługę (np. bazę danych). 
Podczas inicjalizacji można zastosować ``NullID`` tak aby w czasie mapowania encji do bazy danych dało się odróżnić, które
encje mają już odpowiadający im rekord w bazie oraz dla których taki rekord należałoby utworzyć.
{% endblock %}

{% block example_specification %}
- należy utworzyć usługę odpowiadającą za wysyłkę wiadomości.
- usługa powinna zapisywać w logach inforamcję o tym, że wiadomość jest przygotowana do wysyłki oraz o tym, została poprawnie wysłana.
- w środowisku testowym usługa nie powinna zapisywać niczego do logów.
{% endblock %}

{% block example_code_php %}
```language-php
interface Logger
{
    public function log($message);
}

final class NullLogger implements Logger
{
    public function log($message)
    {
    }
}

class MessageSenderService
{
    private $logger;
    
    public function __construct(Logger $logger)
    {
        $this->logger = $logger;
    }
    
    public function send(Message $message)
    {
        $this->logger("Pre send message: %d", $message->getId());
        // send message 
        $this->logger("Post send message: %d", $message->getId());
    }
}
```
{% endblock %}

{% block example_explanation %}
Powyższy przypadek można było również zrealizować za pomocą warunków oczekując opcjonalnego argumentu w konstruktorze ``MessageSenderService``.
Takie podejście spowodowałoby jednak konieczność utworzenia warunku sprawdzającego czy ``Logger`` został przekazany
w każdym miejscu, w którym powinien być użyty lub utworzenie prywatnej metody.  
Dzięki implementacji ``NullLogger`` nie było to konieczne, co przełożyło się na uproszczenie kodu usługi.
W prostym przypadku przekazanie jednego opcjonalnego argumentu nie jest jeszcze problemem, jednak kiedy tego typu argumentów
przybywa, kod klasy zwiększa się niepotrzebnie.
{% endblock %}
