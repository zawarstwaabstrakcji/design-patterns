---
title: Virtual proxy
description: >
    Wzorzec stworzony w celu kontrolowania tworzenia lub dostępu do kosztownych obiektów. Oryginalny obiekt zastępowany
    jest przez proxy, które dziedziczy z oryginału dzięki czemu dostarcza identyczne API i może być użyty zamiast oryginału.
    Oryginaly obiekt może być utworzony na żądanie.
enabled: true
type: Strukturalny 
usage_info: null
posts: []

---
{% block description %}
Wzorzec stworzony w celu kontrolowania tworzenia lub dostępu do kosztownych obiektów. Oryginalny obiekt zastępowany
jest przez proxy, które dziedziczy z oryginału dzięki czemu dostarcza identyczne API i może być użyty zamiast oryginału.
Oryginalny obiekt może być utworzony na żądanie.  
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
        
        $this->isInitialized = true;
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
- Pobierz całkowitą kwotę na jaką została wystawiona faktura bez ładowania wszystkich pozycji.
- Kwota całkowita to suma cen wszystkich pozycji na fakturze.
{% endblock %}

{% block example_code_php %}
```language-php

class Invoice
{
    private $number;
    
    private $items;

    public function __construct(Number $number, $items = [])
    {
        $this->number = $number;
        foreach ($items as $item) {
            $this->addItem($item);
        }
    }
    
    public function getNumber()
    {
        return $this->number;
    }
    
    public function addItem(Item $item)
    {
        $this->items[] = $item;
    }
    
    public function getItems()
    {
        return $this->items;
    }
    
    public function getTotalPrice()
    {
        $total = new Money(0, 'PLN');
        
        foreach ($this->items as $item) {
            $total->add($item->getPrice());
        }
        
        return $total;
    }
}

class IvnoiceProxy extends Invoice
{
    private $invoice; 
    
    private $storage;
    
    private $isInitialized;
    
    public function __construct(Storage $storage, Number $number, $items = [])
    {
        $this->storage = $storage;
        $this->invoice = new Invoice($number, []);
        $this->isInitialized = false;
    }
    
    public function getNumber()
    {
        return $this->invoice->getNumber();
    }
    
    public function addItem(Item $item)
    {
        $this->items[] = $item;
    }
    
    public function getItems()
    {
        $this->initialize();
        return $this->items;
    }
    
    public function getTotalPrice()
    {
        $amount = $this->storage->calculateTotalPriceForInvoice($this->invoice->getNumber());
        
        return new Money($amount, 'PLN');
    }
   
    private function initialize()
    {
        if ($this->isInitialized) {
            return ;
        }
        
        $items = $this->storage->getItemsForInvoice($this->invoice->getNumber());
        
        foreach ($items as $item) {
            $this->invoice->addItem($item);
        }
        
        $this->isInitialized = true;
    }
}
```
{% endblock %}

{% block example_explanation %}
W przykładzie pokazana została sytuacja, w której tworzenie obiektu z wszystkimi jego zależnościami może okazać się 
niewydajne i niepotrzebne. Virtual Proxy rozwiązuje ten problem poprzez załadowanie najcięższych zależności obiektu
dopiero kiedy są one faktycznie wykorzystywane, tzw. *lazy loading*. W tym przypadku ładowanie wszystkich przedmiotów na fakturze nie miało
większego sensu kiedy celem było jedynie policzenie całkowitej kwoty na jaką została wystawiona faktura. 
Tą operację lepiej przenieść na silnik bazodanowy lub jakąś zewnętrzną usługę, która z takimi obliczeniami poradzi 
sobie o wiele lepiej niż aplikacja.
{% endblock %}
