# Symfony sample

<p align="center">

  [![Tested with TestCafe](https://img.shields.io/badge/tested%20with-TestCafe-2fa4cf.svg)](https://github.com/DevExpress/testcafe)
  [![Build Status](https://travis-ci.org/Rebolon/php-sf-flex-webpack-encore-vuejs.png?branch=master)](https://travis-ci.org/Rebolon/php-sf-flex-webpack-encore-vuejs)
  [![Known Vulnerabilities](https://snyk.io/test/github/rebolon/php-sf-flex-webpack-encore-vuejs/badge.svg?targetFile=package.json)](https://snyk.io/test/github/rebolon/php-sf-flex-webpack-encore-vuejs?targetFile=package.json)

</p>

## requirements

You need PHP (7.x), composer, and npm
You also need to configure your php with curl and openssl
You have to setup the certificates [download pem file](https://curl.haxx.se/docs/caextract.html), put it somewhere on your system and set your php.ini with those values:
 
 * curl.cainfo = PATH_TO_YOUR_CERTIFICATE/cacert.pe
 * openssl.cafile = PATH_TO_YOUR_CERTIFICATE/cacert.pem
 
You can run the app with a PHP Built_in server (all npm command relys on it), but some features may not work finely. In fact, 
Api-platform package rely on some HTTP SERVER features that are not implemented by the PHP Built-in server. So the api indexes
like /api/index.json, /api/index.jsonld or /api/index.html will just return a 404 Not Found. There is also the react-admin 
app generated by the Api-platform that will not work because it relies on api json index.

## explanation

This application has been realized to get a sample front app with sf3+ & vuejs but it also show basic controllers with what most developpers do :
basic controller, controller with twig, http call to external API, logging, API... We will try to not use front manipulation outside of VueJS (the sample with twig are really basic and won't use form per example)
Here is how it has been created:

* composer create-project symfony/skeleton sf-flex-encore-vuejs
* cd sf-flex-encore-vuejs
* composer req encore annotations twig api http profiler log doctrine-migrations admin webonyx/graphql-php
* composer require --dev doctrine/doctrine-fixtures-bundle phpunit/phpunit symfony/dom-crawler symfony/browser-kit symfony/css-selector security-checker roave/security-advisories:dev-master
* yarn add vue vue-router quasar-framework quasar-extras vuelidate vue-apollo@next graphql apollo-client apollo-link apollo-link-http apollo-link-error apollo-cache-inmemory graphql-tag react react-dom prop-types axios rxjs @devexpress/dx-react-core @devexpress/dx-react-grid
* yarn add --dev vue-loader vue-template-compiler vue-router react-loader babel-preset-es2017 babel-preset-react sass-loader node-sass bootstrap@4.0.0 testcafe testcafe-vue-selectors jasmine karma karma-jasmine karma-spec-reporter karma-junit-reporter karma-webpack karma-chrome-launcher offline-plugin
* yarn install 

Then some php controllers has been created on following routes :
 
 * / : DefaultController with the menu to navigate throught different controllers
 * /demo/simple : SimpleController with route config in routes.yaml and logger injection with autowiring
 * /demo/hello/:name : HelloController with route config in annotations and twig template
 * /demo/vuejs : VuejsController with route config in annotations and VueJS app with specific js/css import
 * /demo/quasar : QuasarController like VuejsController but with the Quasar framework for UX components
 * /demo/http-plug : HttpPlugController to show how to call external API from your controller
 * /demo/login : LoginController for standard login by Symfony
 * /demo/vuejs/login : LoginJsonController for json login with JS applications
 * /demo/form : [Work in progress] authentification with javascript, and a full web application with vuejs and api-platform(rest/graphql)
 * /api : access ApiPlatform api doc (you need to be authentified from /demo/form if you want to play with it: all entities are configured to be accessed with ROLE_USER)
 * /api/graphql : access ApiPlatform GraphQL implementation (beta release)
 * /api-platform-admin-react : the react admin provided by api platform package (more info here [https://api-platform.com/docs/admin/getting-started](https://api-platform.com/docs/admin/getting-started))
 * /admin : use the easy admin bundle to allow a comparison between fullstack PHP and PHP/VueJS

And then we followed the [documentation here from api platform](https://api-platform.com/docs/admin/getting-started) to create the Admin React tool. We just proceed to some adjustments:

 * we kept only src folder and moved its content at the root of /assets/js/api-platform-admin-react
 * we added the authClient like they did on the [documentation](https://api-platform.com/docs/admin/authentication-support)
 * on main component componentWillMount() we just do a first fecth on /token to retreive a valid csrf_token for next api calls
 
But, Vuejs, ReactJS and Angular together ? with Symfony4, WTF ???
Yes it can seems completely stupid to use all this technologies together, but don't forget one thing : this is a POC !
The aim is not to help you to mix all those techs, but just to help you to use some of them finely.
The biggest problem in my case is the dependancy management : all those JS libraries may need the same deps but in different
version... For instance it seems to be ok, but i think that in future it could be a real brain-teaser.

## configuration

You can change the php executable (if you want to use a php version with xdebug, or just another version of php) using
package.json in the config section. Default php uses the one in path if exists.
You can also change the web server port and the asset server port in the same config section.

```
    "config": {
        "php": "php",
        "server_port_web": "80",
        "server_port_asset": "8080",
        "test_browser": "chrome:headless,firefox"
    },
```

The test_browser section represent all the browsers you want to use with the testcafe testing tool.

## components

* flex: new symfony system to make web dev life easier ; it works with [recipes](https://symfony.sh/)
* [vuejs](https://vuejs.org/): top js framework to build SPA, or just widget on classic page
* [quasar](http://quasar-framework.org/): UX component library based on VueJS
* encore: symfony solution to wrap webpack config and, once again, make your life simpler
* offline-plugin: webpack plugin to manage offline assets
* annotations: use annotations everywhere in your PHP code
* [twig](https://twig.symfony.com/): symfony template solution, useless if you don't want to render template with symfony, but usefull to be able to use assets twig helper with webpack encore
* [api](api-platform.com): api-platform to build REST api(instead of fosrestbundle)
* [react](https://reactjs.org/): js framework used here by api-platform for their react admin component, it's built on top of [https://marmelab.com/admin-on-rest](https://marmelab.com/admin-on-rest)
* [@devexpress/dx-react-core @devexpress/dx-react-grid](https://devexpress.github.io/devextreme-reactive/react/grid/docs/guides/getting-started/): UX components library based on React
* http: a cool library to do http call from http (you could switch it with Guzzle)
* doctrine-migrations: based on Doctrine ORM, it make it easy to change your db during a project life
* doctrine-fixture: also based on Doctrine to help you to add fixtures in your DB (for your tests or for project init)
* admin: easy admin component to build quick backend with auto form
* profiler: for debugging purpose
* log: a logger for symfony
* security-checker: a tool to check known securities vulnerabilities, to use it, run `php bin/console security:check`
* roave/security-advisories: a tool that prevent the install of PHP package from composer with known vulnerabilities
* [phpunit](https://phpunit.de/), crawler, browserkit, css-selector: php/symfony task for testing (@todo ll last 3 should be a recipe)
* babel-preset-es2017: do you really need explanation ?
* testcafe: an e2e test framework (might be changed with chimp or anything else, gimme better idea)
* [jasmine](https://jasmine.github.io/) & [karma](https://karma-runner.github.io/2.0/index.html): a stack for unit & e2e tests (a more standard stack to replace testcafé)
* sass: hey, we are not in nineties, we don't write css now
* [bootstrap](https://getbootstrap.com/): the 4th version of the first class css framework (not used with quasar)
* axios: the library to perform http calls
* [rxjs](http://reactivex.io/rxjs/): THE library to replace the usage of Promise !

Here are some uninstalled components that may help you:

* rekit: an IDE for React devlopment, you should install it glabally

For Angular5, i decided to do quite different way:
* i initialized a project with angular-cli (first install it globally `yarn add -g @angular/cli`) like this: cd assets/js && ng new devxpress-angular && cd devxpress-angular && yarn install
* then i customize the .angular-cli.json to set output path to my root public/dist-ng folder
* i set this dist-ng folder because `encore` will remove dist folder when we run some command so with different dist folders i don't have any problem
* i customize my npm watch scripts and set a new watch:windows which allows to run the 2 watcher (one for encore, and one for angularCli) at the same time with only one npm scripts
* i decided to not use `npm run serve` but `npm run build` with watch options because this last one is the only way to generate files in dist-ng folder. ng serve does everything in memory only

## run

* install the project with `npm run init-project` (or init-project:windows for windows system) which will launch :
  1. copy the env file (or set them on your system) : `cp .env.dist .env`
  2. php dependancies installation: `composer install`
  3. nodejs tooling installation (and angular deps): `npm install && cd assets/js/devxpress-angular && npm install`
  4. assets generation: `npm run dev`
  5. db init: `php bin/console doctrine:database:create` & `doctrine:schema:create` & `doctrine:fixtures:load`
     * it creates the physical database from the `config/packages/doctrine.yaml` file
     * it builds the schema based on `src/Entity/*` files
     * it load fixtures based on a db sample built with `calibre` software plus a plugin that export data to sqlite format. An alternative to this would have been to use https://github.com/hautelook/AliceBundle and build yaml fixtures, but i already had an sqlite db so i didn't need this :-)  
* Run your application with php built-in server:
  1. Change to the project directory
  2. Execute the `npm run dev-server-hot` (or dev-server-hot:windows for windows system) command to start the asset server that will build your assets and your manifest.json and serve the assets with hot module replacment when you do a modification on a vuejs file 
  3. Execute the `npm run sf-dev` (or sf-dev:windows for windows system) command;
  4. Browse to the http://localhost:80/ URL.

     * Run composer require symfony/web-server-bundle for a better web server.
     * Quit the server with CTRL-C.
     * And launch `php bin/console server:start 127.0.0.1:80`
    
  5. Run frontend tests with `npm run test`

Don't forget to prefer an nginx/apache server to be able to use full features of api-platform.

* Read the documentation at https://symfony.com/doc

If you want to change default ports, you can use package.json > config : server_port_web for the web server (php built in server), and server_port_asset for the asset server.
Default ports are 80 and 8080.

If you update the project:

 * Don't forget to run `doctrine:migrations:migrate` to take care of DB modifications.
 * Do the following command `npm run dump-js-config` (or dump-js-config:windows for windows system) to create the js config file

## webpack

Everything is managed by 'encore' symfony package, so have a look at the webpack.config.js and then read their [docs](http://symfony.com/doc/current/frontend.html)
 * npm run dev : will build your assets (in this project it's /public/build/)
 * npm run watch : does the same thing than npm run dev, but it watches files modification to re-generate the assets
 * npm run dev-server :  build the manifest.json that map your assets qith their url from the asset server and start a web server that will serve those assets
 * npm run dev-server-hot : does the same thing as previously, but with vuejs framework it also does Hot Module Replacement 
 * npm run build : build your assets for **production**
 
Take care, the asset server listen to port 8080 so don't start your main server on that port, or specify another port for the dev-server using ` --port 9999` for example

Also, if you want to use the asset server finely, you have to add the assets configuration in the config/packages/framework.yaml file :
`json_manifest_path: '%kernel.project_dir%/public/build/manifest.json'`. In fact the npm command will build asset in memory only, and modify the manifest file to map asset to a new url served by the asset server instead of the main web server.

In the main layout, we load 3 common files: manifest.js, vendor.js and sw.js. Vendor is where you wan put all common libraries used on almost all pages.
The source file for this bundle is assets/js/app.js. SW is for the service workers. It's default behavior is to manage the Cache file. You can have a look 
at [offline-plugin for webpack](https://github.com/NekR/offline-plugin).

## code quality
### PHP
The project uses 2 packages to lint and fix the code style of PHP code :You can install phpcs to check your code
 * squizlabs/PHP_CodeSniffer to lint and follow PSR1/PSR2 rules. 
 * friendsofphp/php-cs-fixer to fix the code
 
Lint with this command `vendor/bin/phpcs src -n --standard=PSR1,PSR2 --report=summary` to get a summary of errors.
You can fix it with `vendor/in/phpcbf src -n --standard=PSR1,PSR2` which is delivered with phpcs, but it doesn't fix everything
so i suggest to use php-cs-fixer with this command `vendor/bin/php-cs-fixer fix src --rules=@PSR1,@PSR2`

### Javascript
For Javascript the following packages has been used: 
 `npm install prettier`
 
To lint the code: `node bin-prettier.js assets/js/**`
To fix it: `node bin-prettier.js assets/js/** --write`

### IDE

For PHP you should configure your IDE to follow PSR1/PSR2 code style (or anything else if you prefer). For JS you will
have to install [prettier tool](https://prettier.io/docs/en/editors.html).

## Tests

On PHP we use PHPUnit and Symfony web testcase. For Javascript, we decided to work with testcafé.
We have basic tests on PHP that will try to test that we have HTTP 200 OK on each routes. We should also tests Commands and
other classes, but we should also test more finely the API content.
Take care with Symfony4 to configure the config/packages/test/framework.yaml file to overload the session.storage_id with
a mock !

On Javascript we have unit and e2 tests. Units tests are managed by jasmine and karma. It allows to test function, class, component. 
For e2e tests we use testcafe from devExpress. It allows to launch browsers and drive them by code to reproduce a human behavior. 
Here the tests runs on a chrome headless, and firefox but you can configure it in the package.json file in the config.test_browser node.

`npm run test-cafe && npm run test-karma` will run js test and will generate a testcafe.xunit.xml and karma_report.xml files in the following folder `/var/report/`.

If you wonder how to tests your VueJS components, you can hae a look at [this website which describe a lot of tests](https://blog.octo.com/vue-js-en-tdd/). Sadly it's in french !

## Security

On PHP i use those 2 packages to prevent the use of deprecated packages or with vulnerabilities:
 * security-checker: when you run `php bin/console security:check` it will checks your dependancies vs known vulnerabilites. You should use it on existing project.
 * roave/security-advisories: it will prevent the composer require and update command on package with known vulnerabilities. But it won't prevent the composer install with an existing composer.lock so you are safe to deliver existing project.

On JS i use snyk services.

@TODO finish on PHP and JS checks + tools to audit the code + software that analyse sql/xss/file injection, csrf, ...

## API

Take care at the custom listeners that you could write based on [Api-Platform documentation](https://api-platform.com/docs/core/events/). They are used by all controllers, not only those
from ApiPlatform.

### Sort

If you want to allow sorting based on columns, you will have to add Filter annotations on Entity. Look at the [Book entity and its ApiFilter](https://github.com/Rebolon/php-sf-flex-webpack-encore-vuejs/blob/38d98658b5e9c2848fe6ad0187c24650225be508/src/Entity/Library/Book.php#L27) which allow to sort on id and title.
Then you will be able to call the api loke this: http://localhost/api/books?order[id]=DESC

### Filter

Sometime you will want to filter on some fields: i want Reviews published after xxx.
For things like this you have to add new ApiFilter (like we did with sort). There is a sample in the [Review entity](https://github.com/Rebolon/php-sf-flex-webpack-encore-vuejs/blob/38d98658b5e9c2848fe6ad0187c24650225be508/src/Entity/Library/Review.php#L19)

You can also filter on relation, but in that case you will have to use the id (or the iris) of the relation as the value of the filter. More information in [documentation of ApiPlatform](https://api-platform.com/docs/core/filters#search-filter). 

### Custom Route

There is a sample of custom route based on Action Demand Responder pattern that will allow to create new Books and it's dependancies in one HTTP call. 
You won't need to create sub-entity before creating the main one, said the book.
The endpoint is /api/booksiu/special_3 [POST].
It takes the following JSON string as Body:

```
// The most complete sample, with de-duplication of editor (only once will be created)
{
    "book": {
        "title": "Zombies in western culture",
        "editors": [{
            "publication_date": "1519664915", 
            "collection": "printed version", 
            "isbn": "9781783743230", 
            "editor": {
                "name": "Open Book Publishers"
            }
        }, {
            "publication_date": "1519747464", 
            "collection": "ebooks", 
            "isbn": "9791036500824", 
            "editor": {
                "name": "Open Book Publishers"
            }
        }],
        "authors": [{
            "role": {
                "translation_key": "WRITER"
            }, 
            "author": {
                "firstname": "Marc", 
                "lastname": "O'Brien"
            }
        }, {
            "role": {
                "translation_key": "WRITER"
            }, 
            "author": {
                "firstname": "Paul", 
                "lastname": "Kyprianou"
            }
        }],
        "serie": {
            "name": "Open Reports Series"
        }
    }
}

// This one re-use database information for editor / author / job / serie
{
    "book": {
        "title": "Oh my god, how simple it is !",
        "editors": [{
            "publication_date": "1519664915", 
            "collection": "from my head", 
            "isbn": "9781783742530", 
            "editor": 1
        }, {
            "publication_date": "1519747464", 
            "collection": "ebooks", 
            "isbn": "9782821883963", 
            "editor": {
                "name": "Open Book Publishers"
            }
        }],
        "authors": [{
            "role": 2, 
            "author": 3
        }, {
            "role": {
                "translation_key": "WRITER"
            }, 
            "author": {
                "firstname": "Paul", 
                "lastname": "Kyprianou"
            }
        }],
        "serie": 4
    }
}
```

## todo

- [x] setup Sf4
- [x] setup symfony/webpack-encore
- [x] add a model with doctrine entities
- [x] configure ApiPlatform
- [x] setup one custom route for ApiPlatform
- [x] setup custom route with nested objects on create/update/read (read should be solved ith serializer, create/update might be solved with custom route or DTOS so try 2 ways)
- [ ] those custom route must referenced by the API documentation on /api route with correct description (for instance it's just book: string ...)
- [x] improve JSON error from custom route: for instance when editor is misfilled it just return this message (without property path): jsonOrArray can be string or array 
- [ ] graphQL: multiple queries in one call ?
- [ ] graphQL: multiple mutations in one call ?
- [ ] graphQL: how to mutate nested objects in a minimal call ?
- [ ] check security and csrf system with ApiPlatform (really required ?)
- [ ] check security on ApiPlatform (do we need JWT or just cookies, in that case we are stateful which is not cool for deployment and replication)
- [x] setup VueJS
- [x] use Quasar with VueJS
- [x] move on Quasar 0.15.x
- [x] setup CSRF protection with VueJS app
- [x] setup unit tests for JS (karma/jasmine)
- [x] setup e2e tests for JS (testcafé)
- [x] setup phpunit tests for PHP (unit test and webtestcase)
- [x] write some JS units tests
- [x] write some JS e2e tests
- [x] write some PHP tests
- [ ] fix testcafe role where sometimes they are not played: https://testcafe-discuss.devexpress.com/t/role-sometime-it-doesnt-seem-to-be-played/875 
- [x] setup tests reports
- [ ] setup security with Symfony (ticket open coz i get 500 instead of 403: https://github.com/symfony/symfony/issues/25806) and choose between cookie (stateful), JWT (with Lexik bundle) or ApiKey (https://symfony.com/doc/current/security/guard_authentication.html)
- [x] setup EasyAdminBundle
- [ ] improve EasyAdminBundle with custom screen
- [x] setup React Admin from ApiPlatform (this issue is mandatory https://github.com/api-platform/api-platform/issues/584)
- [ ] fix React Admin Book creation: it allows multiple authors (projectBookCreation) whereas it should not, and try to display the name of the author/editor... instead of their iris which is unundertandable
- [ ] customize React Admin to display more information on datagrid, and customize form in book edition per example
- [ ] keep EasyAdminBundle or React Admin: make a choice coz both are doing the same, i need to measure differences, and also the ease to do custom screen (change forms, manage rights...)
- [ ] create another route with VueJS that use GQL instead of REST
- [ ] code style: use phpcscbf instead of php_cs_fixer coz it's embeded with phpcs and it uses the phpcs config file
- [ ] transform this project into a meta package that will install all requirements for JS app within Symfony (like does laravel)
- [x] check if i need the JMSSerilizerBundle or if the serilizer component is enough (if autowiring runs well, why not): I prefer to use Symfony serializer, it's enough
- [ ] have a lookAt the HauteLookAliceBundle to help in the creation of real fixtures during tests (instead of generating a new test.db which could be long)

* improve this tutorial with ~~an API Route built with Api platform (without DB)~~ and install the vue-generator from api-platform for a crud sample :
    * The question for instance is `How to override ApiPlatform routing: i want some route to be overloaded: POST/PUT Book should be able to add also Auhtors and/or Editors`
* ~~add db fixtures at init ! almost 40 books and some reviews (at least 3 for 5 1st books)~~ all sqlite fixtures is converted into the final db model
* customize easyAdminBundle to add author/editor from Book and display those related infos on Book admin page (same for other author/editor entities and serie/reviews)
* manage Entity orphanRemoval / CASCADE onDelete
* find a good way to add a `pre-commit` hook that lint PHP and JS code, and run the PHP / JS tests
* Here is a sample of GraphQL query with 2 queries in one call but i don't know how to manage this with Vue-apollo (he can't accept this coz it maps the result to the data[nameOfapolloQuery] whereas i have 2 names...) ? IN FACT the solution is the result hook (see https://github.com/Akryum/vue-apollo/issues/15, and the documentation)
```
query getBooksAndSerieQry($firstBook: Int, $afterBook: String, $firstSerie: Int, $afterSerie: String) {
    getBooksAndSerie: books(first: $firstBook, after: $afterBook) {
        edges {
            node {
                id
                title
            }
            cursor
        }
        pageInfo {
            endCursor
            hasNextPage
        }
    }
    getBooksAndSerie: series(first:$firstSerie, after: $afterSerie) {
        edges {
            node {
                name
            }
            cursor
        }
        pageInfo {
            endCursor
            hasNextPage
        }
    }
}
```

## third party issues that helped me to go on 

 * https://github.com/api-platform/api-platform/issues/488
 * https://github.com/api-platform/api-platform/issues/501 
 * https://github.com/api-platform/api-platform/issues/502
 * https://github.com/api-platform/api-platform/issues/519
 * https://github.com/api-platform/api-platform/issues/530
 * https://github.com/api-platform/api-platform/issues/535
 * https://github.com/api-platform/api-platform/issues/537
 * https://github.com/api-platform/api-platform/issues/581
 * https://github.com/api-platform/api-platform/issues/584
 * https://github.com/javiereguiluz/easy-admin-demo/issues/61
 * https://github.com/javiereguiluz/easy-admin-demo/issues/60
 * https://github.com/symfony/symfony/issues/25806
 * https://github.com/symfony/symfony/issues/8467
 * https://github.com/symfony/webpack-encore/issues/256
 * https://testcafe-discuss.devexpress.com/t/role-sometime-it-doesnt-seem-to-be-played/875
