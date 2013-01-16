---
layout: post
title: "Merging I18n Translations for SimpleForm and Formtastic"
date: 2013-01-16 19:11
comments: true
categories:
---
In a current Rails project we are using [SimpleForm](https://github.com/plataformatec/simple_form) to create forms in view templates. The backend is based on [ActiveAdmin](http://activeadmin.info) that uses [Formtastic](https://github.com/justinfrench/formtastic) for creating forms. As the application should support different languages we defined translations for labels, placeholders, hints and so on for the front-end ([SimpleForm](https://github.com/plataformatec/simple_form)):

``` yaml config/locales/forms.de.yml
de:
  simple_form:
    labels:
      user:
        first_name: 'Vorname'
        last_name: 'Nachname'
        # ...
    placeholders:
      user:
        first_name: '...'
        # ...
    hints:
      user:
        first_name: '...'
        # ...
```

Of course these translation should be used for the backend ([Formtastic](https://github.com/justinfrench/formtastic)), too. Instead of defining these items again within the `formtastic` namespace, the merge operator of [YAML](http://www.yaml.org) (`<<`) can be used to keep the locale file [DRY](http://en.wikipedia.org/wiki/Don't_repeat_yourself):

``` yaml config/locales/forms.de.yml
de:
  simple_form: &form_definitions
    labels:
      user:
        first_name: 'Vorname'
    # ...
  formtastic:
    << *from_definitions
```

The `simple_form` namespace is simply aliased with `form_definitions` and merged into the `formtastic` namespace.