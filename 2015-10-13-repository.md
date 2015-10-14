---
title: Repository
description: >
    Repozytorium stanowi separację pomiędzy domeną a warstwą persystencji. Repozytorium dostarcza interfejs kolekcji
    w celu dostępu do danych przechowywanych w bazie danych, systemie plików czy zewnętrznej usłudze. Dane zwracane
    są w postaci obiektów.
enabled: true
type: null 
usage_info: null
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
    
    public function findBy(Id $id);
    
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
    
    public function findBy(Id $id)
    {
        return array_key_exists((string) $id, $object) ? $this->objects[(string) $id] : null;
    }
    
    public function remove(Object $object)
    {
        unset($this->objects[(string) $object->getId()]);
    }
    
    public function contains(Object $object)
    {
        return array_key_exists((string) $object->getId(), $object);
    }
}
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
    
    public function findBy(Email $email);
    
    public function hasUserWith(Email $email);
}

interface DAORepository
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
    
    public function findBy(Email $email)
    {
        $userData = $this->dao->findBy((string) $email);
        
        if (!is_null($userData)) { 
            return new User(new Email($userData['email']));
        }
        
        return ;
    }
    
    public function hasUserWith(Email $email)
    {
        return $this->dao->hasUserWith((string) $email);
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
        
        if ($this->users->hasUserWith() {
            throw new EmailAlreadyUsedException;
        }
        
        $this->users->add(new User($email));
    }
}

class UserController
{
    private $container; 
    
    public function __construct(ServiceLocator $container)
    {
        $this->container = $container;    
    }
    
    /**
     * @Route('/user/{email}')
     */
    public function displayUserAction($email)
    {
        $user = $this->get('user.repository')->findBy(new Email($email));
        
        if (is_null($user)) {
            throw new NotFoundException;
        }
        
        return new Response($this->templateEngine->render('user_template.html', ['user' => $user]));
    }
}
```
{% endblock %}

{% block example_explanation %}
W przykładzie pokazano najbardziej istotną cechę repozytorium a jest nią dostarczenie interfejsu kolekcji 
reprezentującej zbiór encji. Poza wyżej wymienionymi przykładami, kolekcja pozwala również na **usunięcie** elementu.
W bardziej złożonych przypadkach, metody ``findByXXX`` można zastąpić metodą ``findBy(Criteria $criteria)`` gdzie
obiekt ``Criteria`` zawiera wszystkie dane na podstawie których należy przeszukać kolekcję.
{% endblock %}
