# geonames v2.x


[![Latest Stable Version](https://poser.pugx.org/xlvis7/geonames/version)](https://packagist.org/packages/xlvis7/geonames)  [![Total Downloads](https://poser.pugx.org/xlvis7/geonames/downloads)](https://packagist.org/packages/xlvis7/geonames)  [![License](https://poser.pugx.org/xlvis7/geonames/license)](https://packagist.org/packages/xlvis7/geonames) [![GitHub issues](https://img.shields.io/github/issues/xlvis7/Geonames)](https://github.com/xlvis7/Geonames/issues) [![GitHub forks](https://img.shields.io/github/forks/xlvis7/Geonames)](https://github.com/xlvis7/Geonames/network) [![GitHub stars](https://img.shields.io/github/stars/xlvis7/Geonames)](https://github.com/xlvis7/Geonames/stargazers) ![Travis (.org)](https://img.shields.io/travis/xlvis7/Geonames)  

A Laravel (php) package to interface with the geo-location services at geonames.org.

## Notes
Forked by xlvis7 to support Laravel7

There is still a lot that needs to be done to make this package "complete". I've gotten it to a point where I can use it for my next project. As time allows, I will improve the documentation and testing that comes with this package. Thanks for understanding.

## Installation
```
composer require xlvis7/geonames
```
And then add `geonames` provider to `providers` array in `app.php` config file:

```php
MichaelDrennen\Geonames\GeonamesServiceProvider::class,
```

After that, Run migrate command:

```
php artisan migrate
```

Want to install all of the geonames records for the US, Canada, and Mexico as well as pull in the feature codes 
definitions file in English? 
```php
php artisan geonames:install --country=US --country=CA --country=MX --language=en
```

Want to just install everything in the geonames database?
```php
php artisan geonames:install
```

## Maintenance
Now that you have the geonames database up and running on your system, you need to keep it up-to-date.

I have an update script that you need to schedule in Laravel to run every day.

Some info on how to schedule Laravel artisan commands:

https://laravel.com/docs/5.6/scheduling#scheduling-artisan-commands

You can read this notice at: http://download.geonames.org/export/dump/

<code>The "last modified" timestamp is in Central European Time. </code>

It looks like geonames updates their data around 3AM CET.

So if you schedule your system to run the geonames:update artisan command after 4AM CET, you should be good to go.

I like to keep my servers running on GMT. Keeps things consistent.

(Central European Time is 1 hour ahead of Greenwich Mean Time)

Assuming your servers are running on GMT, your update command would look like: 
```php
$schedule->command('geonames:update')->dailyAt('3:00');
```

The update artisan command will handle the updates and deletes to the geonames table.

## Gotchas
Are you getting something like: 1071 Specified key was too long

@see https://laravel-news.com/laravel-5-4-key-too-long-error

Add this to your AppServiceProvider.php file:
```php
Schema::defaultStringLength(191);
```

###A quick word on indexes

This library contains a bunch of migrations that contain a bunch of indexes. Now not everyone will need all of the indexes.

So when you install this library, run the migrations and delete the indexes that you don't need.

Also, Laravel doesn't let you specify a key length for indexes on varchar columns. There are two indexes suffering from this limit. Instead of creating indexes on those columns the "Laravel way", I send a raw/manual query to create the indexes with the proper lengths.
