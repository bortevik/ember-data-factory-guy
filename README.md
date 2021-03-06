# Using as Gem

To Use with in Rails project or project with sprockets:

In Gemfile:

```ruby
gem 'ember-data-factory-guy', group: test
```

then:

```
$ bundle install
```

# How this works

Add fixtures to the store using the:

  * DS.FixtureAdapter
  * DS.RestAdapter
  * DS.ActiveModelAdapter

```javascript

  ////////////////////////////////////////////
  // Model definitions

  User = DS.Model.extend({
    name:     DS.attr 'string',
    projects: DS.hasMany 'project'
  })

  Project = DS.Model.extend({
    title: DS.attr 'string'
  })

  ////////////////////////////////////////////
  // Fixture definitions for models

  FactoryGuy.define('user', {
   // default values for 'user' attributes
    default: {
      name: 'User1'
    },
    // named 'user' type with custom attributes
    admin: {
      name: 'Admin'
    }
  });

  FactoryGuy.define('project', {
    default: {title: 'Project'}
  });

  //////////////////////////////////////////////////////////////////
  //
  // building json with FactoryGuy.build
  //
  var userJson = FactoryGuy.build('user') // {id: 1, name: 'User1'}
  var customUserJson = FactoryGuy.build('user', name: 'bob') // {id: 2, name: 'bob'}
  var namedUserJson = FactoryGuy.build('admin') // {id: 3, name: 'Admin'}

  //////////////////////////////////////////////////////////////////
  // store.makeFixture method creates model in the store
  //
  // with DS.Fixture adapter
  //  makeFixture returns json
  //
  store.makeFixture('user'); //  user.FIXTURES = {id: 1, name: 'User1'}
  store.makeFixture('user', {name: 'bob'}); //  user.FIXTURES = {id: 2, name: 'bob'}
  store.makeFixture('admin'); //  user.FIXTURES = {id: 3, name: 'Admin'}
  store.makeFixture('admin', name: 'My name'); //  user.FIXTURES = {id: 4, name: 'My name'}

  // Use store.find to get the model instance
  store.makeFixture('user');
  store.find('user', 1).then(function(user) {
    user.get('name') == 'My name';
  });

  // and to setup associations ...
  var project = store.makeFixture('project');
  var user = store.makeFixture('user', projects: [project.id]);

  // with fixture adapter all associations are treated as async, so it's
  // a bit clunky to get this associated data. When using DS.FixtureAdapter
  // in view specs though, this clunk is dealt with for you.
  store.find('user', 1).then(function(user) {
    user.get('name') == 'My name';
    user.get('projects').then(function(projects) {
      projects.length == 1;
    });
  });

  //////////////////////////////////////////////////////////////////
  // store.makeFixture method creates model and adds it to store
  //
  // with DS.ActiveModelAdapter/DS.RestAdapter
  //
  // returns a model instances so you can synchronously
  // start asking for data, as soon as you get the model
  //
  var user = store.makeFixture('user');
  var user = store.makeFixture('user', {name: 'bob'}); //  user.toJSON() = {id: 2, name: 'bob'}
  var user = store.makeFixture('admin'); //  user.toJSON() = {id: 3, name: 'Admin'}
  var user = store.makeFixture('admin', name: 'My name'); //  user.toJSON() = {id: 4, name: 'My name'}

  // and to setup associations ...

  var project = store.makeFixture('project');
  var user = store.makeFixture('user', projects: [project.id]);

  user.get('projects.length') == 1;
  user.get('projects.get('user') == user;


```