---
title: Virtual proxy
description: >
    Wzorzec stworzony w celu kontrolowania tworzenia lub dostępu do kosztownych obiektów. Oryginalny obiekt zastępowany
    jest przez proxy, które dziedziczy z orignału dzięki czemu dostarcza identyczne API i może być użyty zamiast oryginału.
    Oryginaly obiekt może być utworzony na żądanie.
enabled: false
type: Strukturalny 
usage_info: null
posts: []

---
{% block description %}
Wzorzec stworzony w celu kontrolowania tworzenia lub dostępu do kosztownych obiektów. Oryginalny obiekt zastępowany
jest przez proxy, które dziedziczy z orignału dzięki czemu dostarcza identyczne API i może być użyty zamiast oryginału.
Oryginaly obiekt może być utworzony na żądanie.  
Wykorzystywany w sytuacjach, w których należy utworzyć obiekt w celu dostępu do części tego obiektu podczas
gdy pozostała jego część jest nieużywana. 
{% endblock %}

{% block dummy_implementation_php %}
```language-php
class Object
{
    private $id;
    
    private $collection;
    
    public function __construct(Id $id)
    {
        $this->id = $id;
    }
    
    public function getId()
    {
        return $this->id;
    }
    
    public function addToCollection(Element $element)
    {
        $this->collection[] = $element;
    }
    
    public function getCollection()
    {
        return $this->collection();
    }
}

class ObjectProxy extends Object
{
    private $storage;
    
    private $object;
    
    private $isInitialized;
    
    public function __construct(Storage $storage, Id $id)
    {
        $this->storage = $storage;
        $this->object = new Object($id);
        $this->isInitialized = false;
    }
    
    public function getId()
    {
        return $this->object->getId();
    }
    
    public function addToCollection(Element $element)
    {
        $this->object->addToCollection($element);
    }
    
    public function getCollection()
    {
        $this->initialize();
        return $this->object->getCollection();
    }
    
    private function initialize()
    {
        if ($this->isInitialized) {
            return ;
        }
        
        $elements = $this->storage->findElementsFor($this->object->getId());
        
        foreach ($elements as $element) {
            $this->object->addElement($element);
        }
    }
}
```
{% endblock %}

{% block when_to_use %}
Virtual Proxy świetnie sprawdza się w sytuacjach, w których operacje biznesowe polegają na obiektach, których tworzenie 
jest kosztowne. Jeżeli chcemy wykonać jedną operację przy użyciu obiektu, który jest powiązany z ogromną ilością innych
obiketów nie ma sensu ładować ich wszystkich do pamięci. Proxy załaduje jedynie niezbędne do wykonania czynności dane a 
resztę pozostawi bez zmian nawet ich nie inicjalizując.
{% endblock %}

{% block example_specification %}
- W systemie istnieje faktura o numerze "Faktura 2015-01-01"
- Faktura o numerze "Faktura 2015-01-01" posiada 250 pozycji. 
- Faktura o numerze "Faktura 2015-01-01" została wystawiona z terminem płatności 7 dni
- Zmień termin płatności faktury o numerze "Faktura 2015-01-01" z 7 dni na 14 dni bez ładowania do pamięci wszystkich pozycji na fakturze.
{% endblock %}

{% block example_code_php %}
```language-php

class Invoice
{
    private $number;
    
    private $items;
    
    private $paymentDeadline;
    
    public function __construct(Number $number)
    {
        $this->number = $number;
        $this->paymentDeadline = new Days(7);
    }
    
    public function changePaymentDeadline(Days $newDeadline)
    {
        $this->paymentDeadline = $newDeadline;
    }
    
    public function addItem(Item $item)
    {
        $this->items[] = $item;
    }
    
    public function getItems()
    {
        return $this->items;
    }
}
```
{% endblock %}

{% block example_explanation %}
// TODO
{% endblock %}
