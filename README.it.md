# Sinatra

[![Gem Version](https://badge.fury.io/rb/sinatra.svg)](http://badge.fury.io/rb/sinatra)
[![Build Status](https://secure.travis-ci.org/sinatra/sinatra.svg)](https://travis-ci.org/sinatra/sinatra)
[![SemVer](https://api.dependabot.com/badges/compatibility_score?dependency-name=sinatra&package-manager=bundler&version-scheme=semver)](https://dependabot.com/compatibility-score.html?dependency-name=sinatra&package-manager=bundler&version-scheme=semver)

Sinatra è un [DSL](https://it.wikipedia.org/wiki/Domain-specific_language) per
la rapida creazione di applicazioni Web in Ruby con minimo sforzo:


```ruby
# myapp.rb
require 'sinatra'

get '/' do
  'Hello world!'
end
```

Installa la gem:

```shell
gem install sinatra
```

Avvia con:

```shell
ruby myapp.rb
```

Visita: [http://localhost:4567](http://localhost:4567)

Modifiche al codice non avranno effetto finché non riavvierai il server.
Riavvia il server ogni volta che mofichi il codice o usa
[sinatra/reloader](http://www.sinatrarb.com/contrib/reloader).

Si consiglia di eseguire anche `gem install thin`, che Sinatra 
utilizzerà se disponibile.

## Sommario

* [Sinatra](#sinatra)
    * [Sommario](#sommario)
    * [Percorsi](#percorsi)
***********************************************************************************
    * [Conditions](#conditions)
    * [Return Values](#return-values)
    * [Custom Route Matchers](#custom-route-matchers)
    * [Static Files](#static-files)
    * [Views / Templates](#views--templates)
        * [Literal Templates](#literal-templates)
        * [Available Template Languages](#available-template-languages)
            * [Haml Templates](#haml-templates)
            * [Erb Templates](#erb-templates)
            * [Builder Templates](#builder-templates)
            * [Nokogiri Templates](#nokogiri-templates)
            * [Sass Templates](#sass-templates)
            * [SCSS Templates](#scss-templates)
            * [Less Templates](#less-templates)
            * [Liquid Templates](#liquid-templates)
            * [Markdown Templates](#markdown-templates)
            * [Textile Templates](#textile-templates)
            * [RDoc Templates](#rdoc-templates)
            * [AsciiDoc Templates](#asciidoc-templates)
            * [Radius Templates](#radius-templates)
            * [Markaby Templates](#markaby-templates)
            * [RABL Templates](#rabl-templates)
            * [Slim Templates](#slim-templates)
            * [Creole Templates](#creole-templates)
            * [MediaWiki Templates](#mediawiki-templates)
            * [CoffeeScript Templates](#coffeescript-templates)
            * [Stylus Templates](#stylus-templates)
            * [Yajl Templates](#yajl-templates)
            * [WLang Templates](#wlang-templates)
        * [Accessing Variables in Templates](#accessing-variables-in-templates)
        * [Templates with `yield` and nested layouts](#templates-with-yield-and-nested-layouts)
        * [Inline Templates](#inline-templates)
        * [Named Templates](#named-templates)
        * [Associating File Extensions](#associating-file-extensions)
        * [Adding Your Own Template Engine](#adding-your-own-template-engine)
        * [Using Custom Logic for Template Lookup](#using-custom-logic-for-template-lookup)
    * [Filters](#filters)
    * [Helpers](#helpers)
        * [Using Sessions](#using-sessions)
            * [Session Secret Security](#session-secret-security)
            * [Session Config](#session-config)
            * [Choosing Your Own Session Middleware](#choosing-your-own-session-middleware)
        * [Halting](#halting)
        * [Passing](#passing)
        * [Triggering Another Route](#triggering-another-route)
        * [Setting Body, Status Code and Headers](#setting-body-status-code-and-headers)
        * [Streaming Responses](#streaming-responses)
        * [Logging](#logging)
        * [Mime Types](#mime-types)
        * [Generating URLs](#generating-urls)
        * [Browser Redirect](#browser-redirect)
        * [Cache Control](#cache-control)
        * [Sending Files](#sending-files)
        * [Accessing the Request Object](#accessing-the-request-object)
        * [Attachments](#attachments)
        * [Dealing with Date and Time](#dealing-with-date-and-time)
        * [Looking Up Template Files](#looking-up-template-files)
    * [Configuration](#configuration)
        * [Configuring attack protection](#configuring-attack-protection)
        * [Available Settings](#available-settings)
    * [Environments](#environments)
    * [Error Handling](#error-handling)
        * [Not Found](#not-found)
        * [Error](#error)
    * [Rack Middleware](#rack-middleware)
    * [Testing](#testing)
    * [Sinatra::Base - Middleware, Libraries, and Modular Apps](#sinatrabase---middleware-libraries-and-modular-apps)
        * [Modular vs. Classic Style](#modular-vs-classic-style)
        * [Serving a Modular Application](#serving-a-modular-application)
        * [Using a Classic Style Application with a config.ru](#using-a-classic-style-application-with-a-configru)
        * [When to use a config.ru?](#when-to-use-a-configru)
        * [Using Sinatra as Middleware](#using-sinatra-as-middleware)
        * [Dynamic Application Creation](#dynamic-application-creation)
    * [Scopes and Binding](#scopes-and-binding)
        * [Application/Class Scope](#applicationclass-scope)
        * [Request/Instance Scope](#requestinstance-scope)
        * [Delegation Scope](#delegation-scope)
    * [Command Line](#command-line)
        * [Multi-threading](#multi-threading)
    * [Requirement](#requirement)
    * [The Bleeding Edge](#the-bleeding-edge)
        * [With Bundler](#with-bundler)
    * [Versioning](#versioning)
    * [Further Reading](#further-reading)
***********************************************************************************
## Percorsi

In Sinatra, un percorso è un metodo HTTP abbinato a un pattern per URL-matching.
Ogni percorso è associata a un blocco:

```ruby
get '/' do
  .. mostra qualcosa ..
end

post '/' do
  .. crea qualcosa ..
end

put '/' do
  .. rimpiazza qualcosa ..
end

patch '/' do
  .. modifica qualcosa ..
end

delete '/' do
  .. cancella qualcosa ..
end

options '/' do
  .. elencare qualcosa ..
end

link '/' do
  .. collega qualcosa ..
end

unlink '/' do
  .. separa qualcosa ..
end
```

I percorsi sono invocati nell'ordine in cui sono definiti. Il primo percorso
che corrisponde alla richiesta viene invocato.

I percorsi con barre finali sono diversi da quelli senza:

```ruby
get '/foo' do
  # Non corrisponde a "GET /foo/"
end
```

Percorsi possono contenere parametri, accessibili via l'Hash `params`:

```ruby
get '/hello/:name' do
  # Corrisponde a "GET /hello/foo" e "GET /hello/bar"
  # params['name'] contiene 'foo' o 'bar'
  "Hello #{params['name']}!"
end
```

Puoi utilizzare paramteri anche atraverso un blocco:

```ruby
get '/hello/:name' do |n|
  # Corrisponde a "GET /hello/foo" e "GET /hello/bar"
  # params['name'] contiene 'foo' o 'bar'
  # n continene params['name']
  "Hello #{n}!"
end
```

Percorsi possono contenere parametri splat (o caratteri jolly), accessibili
con l'Array `params['splat']`:

```ruby
get '/say/*/to/*' do
  # Corrisponde a "GET /say/hello/to/world"
  params['splat'] # => ["hello", "world"]
end

get '/download/*.*' do
  # Corrisponde a "GET /download/path/to/file.xml"
  params['splat'] # => ["path/to/file", "xml"]
end
```

O con blocchi:

```ruby
get '/download/*.*' do |path, ext|
  # Corrisponde a "GET /download/path/to/file.xml"
  [path, ext] # => ["path/to/file", "xml"]
end
```

Percorsi con Regular Expressions:

```ruby
get /\/hello\/([\w]+)/ do
  # Corrisponde a "GET /download/path/to/file.xml"
  "Hello, #{params['captures'].first}!"
end
```

Or with a block parameter:

```ruby
get %r{/hello/([\w]+)} do |c|
  # Matches "GET /meta/hello/world", "GET /hello/world/1234" etc.
  "Hello, #{c}!"
end
```

Route patterns may have optional parameters:

```ruby
get '/posts/:format?' do
  # matches "GET /posts/" and any extension "GET /posts/json", "GET /posts/xml" etc
end
```

Routes may also utilize query parameters:

```ruby
get '/posts' do
  # matches "GET /posts?title=foo&author=bar"
  title = params['title']
  author = params['author']
  # uses title and author variables; query is optional to the /posts route
end
```

By the way, unless you disable the path traversal attack protection (see
[below](#configuring-attack-protection)), the request path might be modified before
matching against your routes.

You may customize the [Mustermann](https://github.com/sinatra/mustermann#readme)
options used for a given route by passing in a `:mustermann_opts` hash:

```ruby
get '\A/posts\z', :mustermann_opts => { :type => :regexp, :check_anchors => false } do
  # matches /posts exactly, with explicit anchoring
  "If you match an anchored pattern clap your hands!"
end
```

It looks like a [condition](#conditions), but it isn't one! These options will
be merged into the global `:mustermann_opts` hash described
[below](#available-settings).