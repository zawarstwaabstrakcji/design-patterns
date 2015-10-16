# Za Warstwą Abstrakcji - Wzorce Projektowe

Repozytorium zawiera wszystkie wzorce projektowe dostępne na stronie http://zawarstwaabstrakcji.pl/wzorce-projektowe/

W celu dodania nowego wzorca należy skorzystać z szablonu dostępnego w pliku ``template.md``

Konfiguracja wzorca:

- **title** - nazwa wyświetlana na stronie
- **description** - opis SEO
- **enabled** - po ustawieniu na ``true`` wzorzec nie będzie widoczny na liście wzorców
- **type** - **Kreacyjny**, **Strukturalny**, **Czynnościowy** lub ``null``
- **usage_info** - ważne informacje związane z wykorzystaniem wzorca, przykład: http://zawarstwaabstrakcji.pl/wzorce-projektowe/null-object/
- **posts** - tablica zawierająca url'e wpisów, w których wzorzec został wymieniony

Treść poszczególnych bloków formatowana jest przy użyciu składni [Markdown](https://daringfireball.net/projects/markdown/syntax)

W celu dodania fragmentu kodu źródłowego należy zastosować następującą składnie:

<pre>
```language-php
class CodeExample
{
    // tutaj kod
}
```
</pre>