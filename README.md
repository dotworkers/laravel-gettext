# Laravel Gettext

[![Stable build Status](https://travis-ci.org/xinax/laravel-gettext.png?branch=1.0.3)](https://travis-ci.org/xinax/laravel-gettext) <a href="https://github.com/xinax/laravel-gettext/tree/1.0.3">Latest Laravel 4 stable release (1.0.3)</a>

[![Stable build Status](https://travis-ci.org/xinax/laravel-gettext.png?branch=2.0.0)](https://travis-ci.org/xinax/laravel-gettext) <a href="https://github.com/xinax/laravel-gettext/tree/2.0.0">Latest Laravel 5 stable release (2.0.0)</a>

[![Dev build Status](https://travis-ci.org/xinax/laravel-gettext.png?branch=master)](https://travis-ci.org/xinax/laravel-gettext) <a href="https://github.com/xinax/laravel-gettext/tree/master">Development master</a> Unstable, only for development (dev-master)

*Laravel Gettext* is a package compatible with the great Laravel PHP Framework. It provides a simple way to add localization support to Laravel applications. It is designed to work with *GNU Gettext* and *PoEdit*.

Note that <b>branch 1.x is for Laravel 4</b>, <b>branch 2.x is for Laravel 5</b>

### 1. Requirements

- Composer - http://www.getcomposer.org
- Laravel 5.* - http://www.laravel.com
- php-gettext - http://www.php.net/manual/en/book.gettext.php
- GNU Gettext on system (and production server!) - http://www.gnu.org/software/gettext/
- PoEdit - http://poedit.net/

### 2. Install

Add the composer repository to your *composer.json* file:

```json
    "xinax/laravel-gettext": "2.x" //L5
```

And run composer update. Once it's installed, you can register the service provider in app/config/app.php in the providers array:

```php
  'providers' => array(
      'Xinax\LaravelGettext\LaravelGettextServiceProvider',
  )
```

Now you need to publish the configuration file in order to set your own application values:

```bash
    php artisan config:publish xinax/laravel-gettext
```

This command set the package configuration file in: *app/config/packages/xinax/laravel-getttext/config.php*.

In Laravel 5 you also need to register the LaravelGettext middleware in the app/Http/Kernel.php file:

```php
    protected $middleware = [
        ...
        'Xinax\LaravelGettext\Middleware\GettextMiddleware',
    ]
```

### 3. Configuration

At this time your application have full gettext support. Now you need to set some configuration values in *config.php*.

```php
    /**
     * Default locale: this will be the default for your application all 
     * localized strings. Is to be supposed that all strings are written 
     * on this language.
     */
    'locale' => 'es_ES', 
```

```php
    /**
     * Default locale: this will be the default for your application. 
     * Is to be supposed that all strings are written in this language.
     */
    'supported-locales' => array(
        'es_ES',
        'en_US',
        'it_IT',
        'es_AR',
    ),  
```

```php
    /**
     * Default charset encoding.
     */
    'encoding' => 'UTF-8',
```

Ok, now is configured. It's time to generate the directory structure and translation files for first time:

```bash
    php artisan gettext:create
```

With this command the needed directories and files are created on **app/lang/i18n**

### 4. Workflow

##### A. Write strings :D

By default *LaravelGettext* looks on app/controllers and app/views recursively searching for translations. Translations are all texts printed with the **_()** function. Let's look a simple view example:

```php
    // an example view file
    echo 'Non translated string';
    echo _('Translated string');
    echo _('Another translated string');
```

```php
    // an example view in blade
    {{ _('Translated string') }}
```

##### B. Translate with PoEdit

Open the PO file for the language that you want to translate with PoEdit. The PO files are located by default in *app/lang/i18n/lang_to_be_translated/LC_MESSAGES/messages.po*. If you have multiple gettext domains, one file is generated by each domain.

<img src="https://raw.github.com/xinax/laravel-gettext/master/doc/poedit.png" />

Once PoEdit is loaded press the Update button to load all localized strings. You can repeat this step anytime you add a new localized string. 

Fill translation fields in PoEdit and save the file. The first time that you do this the MO files will be generated for each locale.

##### C. Runtime methods

To change configuration on runtime you have these methods:

```php
    /**
     * Sets the Current locale.
     * Example param value: 'es_ES'
     *
     * @param mixed $locale the locale
     * @return LaravelGettext
     */
    LaravelGettext::setLocale($locale);
```

```php
    /**
     * Gets the Current locale.
     * Example returned value: 'es_ES'
     *
     * @return String
     */
     LaravelGettext::getLocale();
```

```php
    /**
     * Gets the language portion of the locale.
     * Eg from en_GB, returns en
     *
     * @return mixed
     */
    LaravelGettext::getLocaleLanguage()
```

```php
    /**
     * Sets the Current encoding.
     * Example param value: 'UTF-8'     
     * 
     * @param mixed $encoding the encoding
     * @return LaravelGettext
     */
     LaravelGettext::setEncoding($encoding);
```

```php
    /**
     * Gets the Current encoding.
     * Example returned value: 'UTF-8'       
     * 
     * @return String
     */
     LaravelGettext::getEncoding();
```

```php
    /**
     * Sets the current domain
     * 
     * @param String $domain
     */
    LaravelGettext::setDomain($domain);
```

```php
    /**
     * Returns the current domain
     *
     * @return String
     */
    LaravelGettext::getDomain();
```

```php
    /**
     * Returns the language selector object
     *
     * @param  Array $labels 
     * @return LanguageSelector         
     */
    LaravelGettext::getSelector($labels = []);
```
    

### 5. Features and examples:

#### A. Route and controller implementation example:

app/routes.php

```php
    Route::get('/lang/{locale?}', [
        'as'=>'lang', 
        'uses'=>'HomeController@changeLang'
    ]);
```

app/controllers/HomeController.php

```php
    /**
     * Changes the current language and returns to previous page
     * @return Redirect
     */
    public function changeLang($locale=null){
        
        LaravelGettext::setLocale($locale);
        return Redirect::to(URL::previous());

    }
```

#### B. A basic language selector example:

```php
  <ul>
      @foreach(Config::get('laravel-gettext::config.supported-locales') as $locale)
            <li><a href="/lang/{{$locale}}">{{$locale}}</a></li>
      @endforeach
  </ul>
```

#### C. Built-in language selector:

You can use the built-in language selector in your views:

```php
    LaravelGettext::getSelector()->render();
```

It also supports custom labels:

```php
    LaravelGettext::getSelector([
        'en_US' => 'English',
        'es_ES' => 'Spanish',
        'de_DE' => 'Deutsch',
    ])->render();
```    

#### D. Adding source directories and domains

You can achieve this editing the **source-paths** configuration array. By default app/views and app/controllers are set.

```php
    /**
     * Paths where PoEdit will search recursively for strings to translate. 
     * All paths are relative to app/ (don't use trailing slash).
     *
     * Remember to call artisan gettext:update after change this.
     */
    'source-paths' => array(
        'controllers',
        'views',
        'foo/bar',              // app/foo/bar
    ),
```

You may want your **translations in different files**. Translations in GNUGettext are separated by domains, domains are simply context names.

Laravel-Gettext set always a default domain that contains all paths that doesn't belong to any domain, its name is established by the 'domain' configuration option.

To add a new domain just wrap your paths in the desired domain name, like this example:

```php
    'source-paths' => array(
		'frontend' => array(
			'controllers',
			'views/frontend'
		),
		'backend' => array(
			'views/backend'
		),
		'views/misc'
	),
```

This configuration generates three translation files by each language: **messages.po**, **frontend.po** and **backend.po**

To change the current domain in runtime:

```php
    LaravelGettext::setDomain("backend");
```

**Remember:** *update your gettext files every time you change the 'source-paths'* option, otherwise is not necessary.

```bash
    php artisan gettext:update
```

This command will update your PO files and will keep the current translations intact. After this you can open PoEdit and click on update button to add the new text strings in the new paths.

You can update only the files of a single domain with the same command:

```bash
    php artisan gettext:update --domain backend
```

#### E. About gettext cache

Sometimes when you edit/add translations on PO files the changes does not appear instantly. This is because the gettext cache system holds content. The most quick fix is restart your web server.

### 6. Contributing

If you want to help with the development of this package, you can:

- Warn about errors that you find, in issues section 
- Send me a pull request with your patch
- Fix my disastrous English in the documentation/comments ;-)
- Make a fork and create your own version of laravel-gettext
- Give a star!
