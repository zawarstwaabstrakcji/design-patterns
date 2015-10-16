---
title: Factory Method
description: >
    Wzorzec projektowy, którego celem jest dostarczenie interfejsu dla klas odpowiedzialnych za tworzenie konkretnego
    typu obiektów.
enabled: true
type: Kreacyjny
posts: 
    - /20150924-wzorce-projektowe-w-praktyce
    
---
{% block description %}
Wzorzec projektowy, którego celem jest dostarczenie interfejsu dla klas odpowiedzialnych za 
tworzenie konkretnego typu obiektów.
{% endblock %}

{% block dummy_implementation_php %}
```language-php
interface Factory
{
    public function createFrom(array $arguments);
}

class ObjectFactory implements Factory
{
    public function createFrom(array $arguments)
    {
        return new Object($arguments['foo'], $arguments['bar']);
    }
}
```
{% endblock %}

{% block dummy_implementation_ruby %}
```language-ruby
// TODO
```
{% endblock %}

{% block dummy_implementation_java %}
```language-java
// TODO
```
{% endblock %}

{% block dummy_implementation_c_sharp %}
```language-csharp
// TODO
```
{% endblock %}

{% block when_to_use %}
Wzorzec świetnie sprawdza się w sytuacjach, w których należy utworzyć obiekt posiadający pewien predefiniowany
zestaw zachowań (implementuje interfejs), których oczekujemy, jednak sam typ obiektu nie jest istotny.
{% endblock %}

{% block example_specification %}
- należy utworzyć usługę, która na podstawie przekazanych parametrów stworzy obiekt użytkownika.
- w systemie mogą istnieć dwa typy użytkowników, osoba prywatna oraz firma.
- firma musi posiadać NIP, osoba prywatna musi posiadać PESEL.
- firma nie może być osobną prywatną, ani odwrotnie.
{% endblock %}

{% block example_code_php %}
```language-php
interface User
{
    // user behavior
}

final class PrivatePerson implements User
{
    private $peselNumber;
    
    public function __construct(PESEL $peselNumber)
    {
        $this->pesel = $peselNumber;
    }
}

final class Company implements User
{
    private $nip;
    
    public function __construct(NIP $peselNumber)
    {
        $this->nip = $nip;
    }
}

interface UserFactory
{
    public function createFrom(array $data);
}

final class MultipleUserTypeFactory implements UserFactory
{
    public function createFrom(array $data)
    {
        if (array_key_exists('nip', $data) {
            return new Company(new Nip[$data['nip']);
        }
        
        if (array_key_exists('pesel', $data]) {
            return new Company(new PESEL[$data['pesel']);
        }
        
        throw new \InvalidArgumentException("Data required to create User needs to have PESEL or NIP.");
    }
}

final class UserRegistrationService
{
    private $userFactory;
    
    public function __construct(UserFactory $factory)
    {
        $this->userFactory = $factory;
    }
    
    public function register(array $data)
    {
        $user = $this->userFactory->createFrom($data);
        // do something with user
    }
}
```
{% endblock %}

{% block example_code_ruby %}
```language-ruby
// TODO
```
{% endblock %}

{% block example_code_java %}
```language-java
// TODO
```
{% endblock %}

{% block example_code_c_sharp %}
```language-csharp
// TODO
```
{% endblock %}

{% block example_explanation %}
W powyższym przykładzie usługa ```UserRegistrationService``` nie dba o to jaki typ użytkownika utworzy, nie takie 
jest jej przeznaczenie. Dzięki temu usługa może być rozszerzana poprzez przekazanie jej innej implementacji ``UserFactory``
bez konieczności modyfikowania kodu samej usługi.  
Wzorzec jest również pomocny w przypadku uruchamiania aplikacji w środowisku testowym nie polegającym na żadnej 
bazie danych. Rodzaj fabryki może być zależny od środowiska, dzięki czemu raz utworzona zostanie encja która w jakiś sposób
mapowana jest na bazę danych, a raz encja "czysta", nie będąca powiązana z niczym zewnętrznym. 
{% endblock %}
