## Ember Notes

- Model Template Component
- Models persist data
- Templates persist the data and userface
- Components how the app behaves between user interaction, templates and models.


## URL, Router, and Route Handler
- URL changes with user interaction w/ app.
- Ember routers map the URL to a route handler first.
- Ember seeks an entry on the Router that matches the current URL, and loads the route handler that corresponds to that route.

# Once loaded, the route handler has two primary responsibilities:
1. It renders a template.
2. Loads more information that is made available to that template.

URL-> ROUTER -> ROUTE HANDLER (with Model - contains data source) -> Template -> after user interaction -> dynamic content based on interaction.

## Templates
- Code client actually sees.
- Organizes layout of HTML in an application.  They look like HTML fragments.
<div>Hi, this is a valid Ember template!</div>
- Includes 'Handlebars' language.

# Handlebars http://handlebarsjs.com/
- Looks like twig in PHP. <p>Hello {{name}} !</p>
- They can display properties, which is either a component or a route.
- {{name}} is a property provided by the templates context.
- They may also contain helpers and components.

## Route Generators:
- Found in app/router.js
- Each time we create a new route, a route handler and a template are generated.
- Route handler is the .js file that corresponds to the .hbs file of the same name.  
- Route handler is responsible for loading model data.
- The data is then accessible to the corresponding template.
- It can be passed to any child components rendered within the template.
- The route handler is the ONLY part of an Ember application that can access model data.  
- Model information can be displayed within a template, but that template's route handler must retrieve it for the template.
- ember g route about
- ember g route contact
- ember g route index - no route is generated with index within Route.js.  Becomes homepage.
...
Router.map(function() {
  this.route('contact');
  this.route('about');
  });

## Models = Firebase
- Represent persistent state.  
- Saves information to a web server or to a bowser's local storage.

## Components
- Control how the UI behaves.
- There are two sides of the SAME component!
- Components consist of TWO files:
- A Handlebars template that defines the display (app/templates/components/rental-tile.hbs)
- A JavaScript source file that defines how it will behave (app/components/rental-tile.js).
- The files will have the same name, but their extensions will be .hbs and .js, respectively.


# This is a method in the Ember class called a 'hook'.
- In the practice lesson, it returns our hard-coded array of rentals.
- This data is now available to the route's corresponding index.hbs templates as the 'model' property.
- When we're in index.hbs, the keyword 'model' can be used to access the rental array in index.js route handler.
- THIS IS ONLY FOR 'RIGHT NOW'.
- THIS INFO WILL LATER BE FOUND IN 'COMPONENTS'.
  model() {
    return rentals;
  },


## Hyper links within Ember.js:
- Called 'helpers'
- Never add spaces between handlebars and content.
- {{#link-to 'contact'}}Click here to contact us.{{/link-to}}

## For each loop:
<ul>
  {{#each model as |rental|}}
    <li>{{rental.owner}}'s {{rental.type}} in {{rental.city}}</li>
  {{/each}}
</ul>

## USING FIREBASE
- From top level of project folder:
$ ember install emberfire
- Adds emberfire to package.json, firebase to bower.json, and creates a new application adapter.

# Adapters
- app/adapters/application.js
- Adapters connect applications to their data stores, ie Firebase.

## Ember Routing Map: https://www.learnhowtoprogram.com/javascript/ember-js/ember-data-and-firebase

## Debugging: https://guides.emberjs.com/v2.0.0/templates/development-helpers/

For variables:
{{log 'Name is:' name}}

Adding a Breakpoint:
{{debugger}}

> get('foo')

get is also aware of keywords. So in this situation:

{{#each items as |item|}}
  {{debugger}}
{{/each}}

> get('item.name')

You can also access the context of the view to make sure it is the object that you expect:

> context
