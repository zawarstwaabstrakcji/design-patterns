---
title: Repository
description: >
    Repozytorium stanowi separację pomiędzy domeną a warstwą persystencji. Repozytorium dostarcza interfejs kolekcji
    w celu dostępu do danych przechowywanych w bazie danych, systemie plików czy zewnętrznej usłudze. Dane zwracane
    są w postaci obiektów.
enabled: true
type: null 
usage_info: >
    Repozytorium należy traktować jak kolekcję encji. Kolekcja pozwala jedynie na dodanie, usunięcie, sprawdzenie czy element 
    istnieje lub wyszukanie go na podstawie pewnych kryteriów. W związku z tym nie należy umieszczać w repozytorium 
    metod związanych np. z generowaniem danych, tworzeniem zapytań czy nawet tworzeniem encji.
posts: []

---
{% block description %}
Repozytorium stanowi separację pomiędzy domeną a warstwą persystencji. Repozytorium dostarcza interfejs kolekcji
w celu dostępu do danych przechowywanych w bazie danych, systemie plików czy zewnętrznej usłudze. Dane zwracane
są w postaci obiektów.
{% endblock %}

{% block dummy_implementation_php %}
```language-php
interface ObjectRepository
{
    public function add(Object $object);
    
    public function getById(Id $id);
    
    public function remove(Object $object);
    
    public function contains(Object $object);
}

final class InMemoryRepository implements ObjectRepository
{
    private $objects;
    
    public function __construct()
    {
        $this->objects = [];    
    }
    
    public function add(Object $object)
    {
        $this->objects[(string) $object->getId()] = $object;
    }
    
    public function getById(Id $id)
    {
        if (!array_key_exists((string) $id, $this->objects)) {
            throw new UserNotFoundException();
        }
        
        return $this->objects[(string) $id];
    }
    
    public function remove(Object $object)
    {
        unset($this->objects[(string) $object->getId()]);
    }
    
    public function contains(Object $object)
    {
        return array_key_exists((string) $object->getId(), $this->objects);
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
Repozytorium reprezentuje kolekcję encji tego samego typu. Przykładem może być kolekcja użytkowników. 
Ponieważ większość encji występuje w postaci kolekcji, repozytorium praktycznie należy stosować za każdym razem
kiedy potrzebujemy przeszukać kolekcję encji.  
W przypadkach gdzie kolekcja ma być przeszukana przy użyciu większej ilości kryteriów, można wprowadzić dodatkowy obiekt
``Criteria`` który przyjmie wszystkie parametry wyszukiwania i na ich podstawie zwróci pasujące encje. 
{% endblock %}

{% block example_specification %}
- należy utworzyć usługę, która zarejestruje nowego użytkownika
- podczas rejestracji należy upewnić się czy użytkownik o podanym adresie email nie został już wcześniej stworzony
- wykorzystując repozytorium należy znaleźć użytkownika o konkretnym adresie email
- repozytorium powinno bazować na DAO (Data Access Object), który bezpośrednio wykorzystuje *data storage*
{% endblock %}

{% block example_code_php %}
```language-php

class User
{
    private $email;
    
    public function __construct(Email $email)
    {
        $this->email = $email;    
    }
    
    public function getEmail()
    {
        return $this->email;   
    }
}

interface UserRepository
{
    public function add(User $user);
    
    public function getByEmail(Email $email);
    
    public function hasUserWithEmail(Email $email);
}

final class DAORepository implements UserRepository
{
    private $dao;
    
    public function __construct(UserDAO $userDAO)
    {
        $this->dao = $userDAO;
    }
    
    public function add(User $user)
    {
        $this->dao->saveNew([
            'email' => (string) $user->getEmail()
        ]);
    }
    
    public function getByEmail(Email $email)
    {
        $userData = $this->dao->getUserDataByEmail((string) $email);
        
        if (empty($userData)) { 
            throw new UserNotFoundException();
        }
        
        return new User(new Email($userData['email']));
    }
    
    public function hasUserWithEmail(Email $email)
    {
        return $this->dao->hasUserDataWithEmail((string) $email);
    }
}

final class UserRegistrationService
{
    private $users;
    
    public function __construct(UserRepository $repository)
    {
        $this->users = $repository;
    }
    
    public function register(array $data)
    {
        $email = new Email($data['email']);
        
        if ($this->users->hasUserWithEmail($email)) {
            throw new EmailAlreadyUsedException;
        }
        
        $this->users->add(new User($email));
    }
}

class UserController
{
    private $userRepository;
     
    private $templateEngine;
    
    public function __construct(UserRepository $userRepository, TemplateEngine $templateEngine)
    {
        $this->userRepository = $userRepository;
        $this->templateEngine = $templateEngine;
    }
    
    /**
     * @Route('/user/{email}')
     */
    public function displayUserAction($email)
    {
        try {
            $user = $this->userRepository->getByEmail(new Email($email));
        } catch (UserNotFoundException $e) {
            throw new HttpNotFoundException;
        }
        
        return new Response($this->templateEngine->render('user_template.html', ['user' => $user]));
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
W przykładzie pokazano najbardziej istotną cechę repozytorium a jest nią dostarczenie interfejsu kolekcji 
reprezentującej zbiór encji. Poza wyżej wymienionymi przykładami, kolekcja pozwala również na **usunięcie** elementu.
W bardziej złożonych przypadkach, metody ``getByXXX`` lub ``findByXXX`` można zastąpić metodą ``getBy(Criteria $criteria)`` gdzie
obiekt ``Criteria`` zawiera wszystkie dane na podstawie których należy przeszukać kolekcję.
{% endblock %}
