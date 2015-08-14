# SchemaValidations

SchemaValidations is an ActiveRecord extension that keeps your model class
definitions simpler and more DRY, by automatically defining validations based
on the database schema.

[![Gem Version](https://badge.fury.io/rb/schema_validations.svg)](http://badge.fury.io/rb/schema_validations)
[![Build Status](https://secure.travis-ci.org/SchemaPlus/schema_validations.svg)](http://travis-ci.org/SchemaPlus/schema_validations)
[![Coverage Status](https://img.shields.io/coveralls/SchemaPlus/schema_validations.svg)](https://coveralls.io/r/SchemaPlus/schema_validations)
[![Dependency Status](https://gemnasium.com/lomba/schema_validations.svg)](https://gemnasium.com/lomba/schema_validations)


## Overview

One of the great things about Rails (ActiveRecord, in particular) is that it
inspects the database and automatically defines accessors for all your
columns, keeping your model class definitions simple and DRY.  That's great
for simple data columns, but where it falls down is when your table contains
constraints.

    create_table :users do |t|
      t.string :email, :null => false, :limit => 30
      t.boolean :confirmed, :null => false
    end

In that case :null => false, :limit => 30 and :boolean must be covered on the
model level.

    class User < ActiveRecord::Base
      validates :email, :presence => true, :length => { :maximum => 30 }
      validates :confirmed, :presence => true, :inclusion => { :in => [true, false] }
    end

...which isn't the most DRY approach.

SchemaValidations aims to cover that and does boring work for you. It inspect
the database and automatically creates validations basing on the schema. After
installing it your model is as simple as it can be.

    class User < ActiveRecord::Base
    end

Validations are there but they are created by schema_validations under the
hood.

## Compatibility

As of version 1.2.0, SchemaValidations supports and is tested on:

<!-- SCHEMA_DEV: MATRIX - begin -->
<!-- These lines are auto-generated by schema_dev based on schema_dev.yml -->
* ruby **2.1.5** with activerecord **4.2.3**, using **mysql2**

<!-- SCHEMA_DEV: MATRIX - end -->

Earlier versions of SchemaValidations supported:

*   rails 3.2, 4.1, and 4.2.0
*   MRI ruby 1.9.3 and 2.1.5

## Installation

Simply add schema_validations to your Gemfile.

    gem "schema_validations"

### What if I want something special?

SchemaValidations is highly customizable. You can configure behavior globally
via SchemaValidations.setup or per-model via
SchemaValidations::ActiveRecord::schema_validations, such as:

    class User < ActiveRecord::Base
      schema_validations :except => :email
      validates :email, :presence => true, :length => { :in => 5..30 }
    end

See SchemaValidations::Config for the available options.

### This seems cool, but I'm worried about too much automagic

You can globally turn off automatic creation in
`config/initializers/schema_validations.rb`:

    SchemaValidations.setup do |config|
      config.auto_create = false
    end

Then in any model where you want automatic validations, just do

    class Post < ActiveRecord::Base
      schema_validations
    end

You can also pass options as per above.

## Which validations are covered?

Constraints:

|      Constraint     |                     Validation                           |
|---------------------|----------------------------------------------------------|
| :null => false      | validates ... :presence => true                          |
| :limit => 100       | validates ... :length => { :maximum => 100 }             |
| :unique => true     | validates ... :uniqueness => true                        |

Data types:

|         Type       |                      Validation                           |
|--------------------|-----------------------------------------------------------|
| :boolean           | :validates ... :inclusion => { :in => [true, false] }     |
| :float             | :validates ... :numericality => true                      |
| :integer           | :validates ... :numericality => { :only_integer => true } |

## How do I know what it did?
If you're curious (or dubious) about what validations SchemaValidations
defines, you can check the log file.  For every assocation that
SchemaValidations defines, it generates an info entry such as

    [schema_validations] Article.validates_length_of :title, :allow_nil=>true, :maximum=>50

which shows the exact validation definition call.


SchemaValidations defines the validations lazily for each class, only creating
them when they are needed (to validate a record of the class, or in response
to introspection on the class).  So you may need to search through the log
file for "schema_validations" to find all the validations, and some classes'
validations may not be defined at all if they were never needed for the logged
use case.

## Release Notes

### 1.2.0

* No longer pull in schema_plus's auto-foreign key behavior. Limited to AR >= 4.2.1

### 1.1.0

* Works with Rails 4.2.

### 1.0.1

* Fix enums in Rails 4.1.  Thanks to [@lowjoel](https://github.com/lowjoel)

### 1.0.0

* Works with Rails 4.0.  Thanks to [@davll](https://github.com/davll)
* No longer support Rails < 3.2 or Ruby < 1.9.3

### 0.2.2

* Rails 2.3 compatibility (check for Rails::Railties symbol).  thanks to https://github.com/thehappycoder

### 0.2.0

* New feature: ActiveRecord#validators and ActiveRecord#validators_on now ensure schema_validations are loaded

## History

*   SchemaValidations is derived from the "Red Hill On Rails" plugin
    schema_validations originally created by harukizaemon
    (https://github.com/harukizaemon)

*   SchemaValidations was created in 2011 by Michał Łomnicki and Ronen Barzel


## Testing

Are you interested in contributing to schema_validations?  Thanks!  Please follow
the standard protocol: fork, feature branch, develop, push, and issue pull request.

Some things to know about to help you develop and test:

<!-- SCHEMA_DEV: TEMPLATE USES SCHEMA_DEV - begin -->
<!-- These lines are auto-inserted from a schema_dev template -->
* **schema_dev**:  SchemaValidations uses [schema_dev](https://github.com/SchemaPlus/schema_dev) to
  facilitate running rspec tests on the matrix of ruby, activerecord, and database
  versions that the gem supports, both locally and on
  [travis-ci](http://travis-ci.org/SchemaPlus/schema_validations)

  To to run rspec locally on the full matrix, do:

        $ schema_dev bundle install
        $ schema_dev rspec

  You can also run on just one configuration at a time;  For info, see `schema_dev --help` or the [schema_dev](https://github.com/SchemaPlus/schema_dev) README.

  The matrix of configurations is specified in `schema_dev.yml` in
  the project root.


<!-- SCHEMA_DEV: TEMPLATE USES SCHEMA_DEV - end -->

Code coverage results will be in coverage/index.html -- it should be at 100% coverage.

## License

This gem is released under the MIT license.
