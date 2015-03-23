# Sofa/Eloquence

[![Build Status](https://travis-ci.org/jarektkaczyk/eloquence.svg?branch=master)](https://travis-ci.org/jarektkaczyk/eloquence)

Useful extensions for the Eloquent ORM.

:construction: **WIP** currently only `Mappable` (inspired by @RomainLanz) and `Formattable`
If you want to know more about new extensions you can check our [Roadmap](#roadmap)!

# Table of Contents

* [Team Members](#team-members)
* [Requirements](#requirements)
* [Getting Started](#getting-started)
* [Mappable](#mappable)
  * [Explicit vs. Implicit mappings](#explicit-vs-implicit-mappings)
* [Formattable](#formattable)
* [Roadmap](#roadmap)

# <a name="team-members"></a>Team Members

* Jarek Tkaczyk ([SOFTonSOFA](http://softonsofa.com/)) <jarek@softonsofa.com>

# <a name="requirements"></a>Requirements

* This package requires PHP 5.4+

# <a name="getting-started"></a>Getting Started

1. Require the package in your `composer.json`:

```
    "require": {
        ...
        "sofa/eloquence": "~0.1@dev",
        ...
    },

```

2. Add trait you want to use to the model.

# <a name="mappable"></a>Mappable

Define mappings on the protected `$maps` variable like bellow.

```php
<?php namespace App;

use Sofa\Eloquence\Mappable; // trait

class User extends \Eloquent {

    use Mappable;

    protected $maps = [
      // implicit mapping:
      'profile' => ['first_name', 'last_name'],

      // explicit mapping:
      'picture' => 'profile.piture_path'
    ];

    public function profile()
    {
      return $this->belongsTo(Profile::class);
    }
```

You can also add mapped attributes to the array representation of your model, just like any accessor:

```php
<?php namespace App;

use Sofa\Eloquence\Mappable; // trait

class User extends \Eloquent {

    use Mappable;

    protected $maps = [
      'picture' => 'profile.piture_path'
    ];

    protected $appends = ['picture'];
}
```

You can get as well as set mapped attributes:

```php
$user->profile->first_name; // 'Jarek Tkaczyk'
$user->first_name = 'John Doe';

$user->profile->first_name; // 'John Doe'

// remember to save related model in order to save the changes:
$user->profile->save();
// or simply use push:
$user->push();
```


## <a name="explicit-vs-implicit-mappings"></a>Explicit vs. Implicit mappings

`Mappable` offers 2 ways of defining mappings for your convenience.

Let's compare equivalent mappings:
```php
// Assuming User belongsTo Profile
// and Profile hasOne Picture
// profiles table: id, first_name, last_name
// pictures table: id, profile_id, path


// User model
// explicit
protected $maps = [
  'first_name'   => 'profile.first_name',   // $user->first_name
  'last_name'    => 'profile.last_name',    // $user->last_name
  'picture_path' => 'profile.picture.path', // $user->picture_path
];

// implicit
protected $maps = [
  'profile'         => ['first_name', 'last_name'], // $user->first_name / ->last_name
  'profile.picture' => ['path'],                    // $user->path
];
```

As you notice behaviour is just the same. However there is slight difference - explicit mapping offers more flexibility, in that you can define custom key for mapped value (`picture_path`), while with implicit mapping you have to use real attribute name defined in the related model (`path`).

# <a name="formattable"></a>Formattable

Define format on the protected `$formats` variable like bellow.

```php
<?php namespace App;

use Sofa\Eloquence\Formattable; // trait

class User extends \Eloquent {

    use Formattable;

    protected $formats = [
        'first_name' => 'strtolower|ucwords',
        'last_name' => ['strtolower', 'ucwords'],
        'slug' => '\Str@slug',
    ];
}
```

```php
$user->first_name = 'john'; // Will set 'John'
$user->last_name = 'doe'; // Will set 'Doe'
$user->slug = 'Awesome package!'; // Will set 'awesome-package'
```

# <a name="roadmap"></a>Roadmap
- [ ] ~~Set validation rules directly on the model. [Ardent](https://github.com/laravelbook/ardent)~~
- [ ] Set relations on an array. (e.g. `protected $relations = ['profile' => 'has_one'];`)

...and much more to come soon!