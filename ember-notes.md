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


### Monday - Components - Update
- Update rental component is 'called' within the rental-tile component.
- "Data down, actions up" data must be passed downwards from the route, through the template, and into a component.
- Actions must be passed upwards from the component, to the template, and into the route handler.
$ ember g component update-rental
- Component is rendered in the rental-tile.hbs component template.
 {{update-rental rental=rental update="update"}}
- New file is given html structure here:
app/templates/components/update-rental.hbs
- This added property will toggle the form's display.  When the property is false we will see and "update" button.  Clicking the button will set the property to true, which will display the form.
<button {{action 'updateRentalForm'}}>Update</button>
- We've bound the action 'update' to the "save" button.  When the user fills out the form and hits "Save" the update action in the JS portion of this component will be triggered.  
<button {{action 'update' rental}}>Save</button>
app/components/update-rental.js
# Nested Components - Update within Rental-tile.js
- Because update-rental is nested within another component, rental-tile, this.sendAction('update', rental, params); only sends the update() action up one level.  It is sent from the update-rental component to it's parent component, rental-tile.  The rental-tile component will also have to pass the action upward in order for this action to eventually reach the appropriate route. Rental-tile will need its own update action to do this.
- Within our index template, we include a line to map the update() action passed upwards  from rental-tile to the corresponding update() action defined in our index route.
{{rental-tile rental=rental update="update" destroyRental="destroyRental"}}
- Now we need to add the update() action to the route handler. The route handler is the only porttion of our application that should directly access model data.  The route handler needs to know what specific rental to update and the new properties we'd like to save to this rental.  This action will take two arguments (rental, params).  These two objects we've been passing through each level of our app.
app/routes/index.js
...
update(rental, params) {
     debugger;
     rental.save();
     this.transitionTo('index');
   },

# Update action in index.js
app/routes/index.js

For each key in params,
if it is NOT undefined,
take the rental and set the property that matches the currnet key, to the value of the current key,
after looping through all of the keys, save the rental,
transition to the index route.
...
update(rental, params) {
  Object.keys(params).forEach(function(key) {
    if(params[key]!==undefined) {
      rental.set(key,params[key]);
    }
  });
  rental.save();
  this.transitionTo('index');
},
...

## Dynamic Routing: Find Record
- As we add more rentals to the app, we want them to be displayed as linked list items so they may view more details on a seperate page.
$ ember g route rental
- We'll need to create a Dynamic Segment.
- A dynamic segment is a placeholder that may be dynamically updated depending on the circumstances. In this case the ds we add to the rental route will represent the id of a given rental in Firebase.  this will allow us to return only a single rental in the route's model hook.
- When the router recieves a request matching /rental/:rental_id, it will map the URL to the rental route handler, rental.js, and send a params hash that includes the value of the :rental_id segment in the URL.
...
Router.map(function() {
  ...
  this.route('rental', {path: '/rental/:rental_id'});
});
- The rental route handler will then use the params.rental_id in the model hook to locate the single rental record we've requested.
app/routes/rental.js
import Ember from 'ember';
export default Ember.Route.extend({
  model(params) {
    return this.store.findRecord('rental', params.rental_id);
    },
  });
- findRecord() finds a specific record.
- This will take us to:
app/templates/rental.hbs
<h2>More information about {{model.owner}}'s {{model.type}}</h2>
- We are accessing a specific record by calling model.owner, model.type etc.

# Rental-detail Component
- This will display the details of a given record.
$ ember g component Rental-detail
- Details rendered here:
app/templates/rental.hbs
<h2>More information about {{model.owner}}'s {{model.type}}</h2>
{{rental-detail rental=model}}

## Refactoring -
app/templates/components/rental-detail.hbs

Located in {{rental.city}}, this {{rental.bedrooms}} bedroom {{rental.type}} is
available by arrangement through {{rental.owner}}.
<br>
<p><img src={{rental.image}} alt={{rental.type}}></p>

<button {{action 'delete' rental}}>Delete</button>

app/components/rental-detail.js

import Ember from 'ember';

export default Ember.Component.extend({
  actions: {
    delete(rental) {
      if (confirm('Are you sure you want to delete this rental?')) {
        this.sendAction('destroyRental', rental);
      }
    }
  }
});

app/templates/rental.hbs

<h2>More information about {{model.owner}}'s {{model.type}}:</h2>

{{update-rental rental=model update="update"}}

{{rental-detail rental=model destroyRental="destroyRental"}}

app/routes/rental.js

import Ember from 'ember';

export default Ember.Route.extend({
  model(params) {
    return this.store.findRecord('rental', params.rental_id);
  },
  actions: {
    update(rental, params) {
      Object.keys(params).forEach(function(key) {
        if(params[key]!==undefined) {
          rental.set(key,params[key]);
        }
      });
      rental.save();
      this.transitionTo('index');
    },
    destroyRental(rental) {
      rental.destroyRecord();
      this.transitionTo('index');
    }
  }
});

app/components/rental-tile.js

import Ember from 'ember';

export default Ember.Component.extend({
  isImageShowing: false,
  actions: {
    imageShow: function() {
      this.set('isImageShowing', true);
    },
    imageHide: function() {
     this.set('isImageShowing', false);
   }
  }
});

app/templates/components/rental-tile.hbs

<li>
  {{#if isImageShowing }}
    <p><img src={{rental.image}} alt={{rental.type}} {{action 'imageHide'}} ></p>
  {{else}}
    <button {{action 'imageShow'}} >Show image</button>
  {{/if}}
</li>

app/routes/index.js

import Ember from 'ember';

export default Ember.Route.extend({
  model() {
     return this.store.findAll('rental');
   },
   actions: {
    saveRental3(params) {
      var newRental = this.store.createRecord('rental', params);
      newRental.save();
      this.transitionTo('index');
    }
  }
});

app/templates/index.hbs

<h1> Welcome to Super Rentals </h1>

We hope you find exactly what you're looking for in a place to stay.

<ul>
  {{#each model as |rental|}}
     {{rental-tile rental=rental}}
  {{/each}}
</ul>

{{new-rental saveRental2="saveRental3"}}

{{#link-to 'about'}}About{{/link-to}}
{{#link-to 'contact'}}Click here to contact us.{{/link-to}}

app/templates/components/rental-tile.hbs

<li>
  {{#link-to 'rental' rental.id}}{{rental.owner}}'s {{rental.type}} in {{rental.city}} {{/link-to}}
  {{#if isImageShowing}}
    <p><img src={{rental.image}} alt={{rental.type}} {{action 'imageHide'}}></p>
  {{else}}
    <button {{action 'imageShow'}}>Image</button>
  {{/if}}
</li>

## Installing bootstrap
$ ember install ember-bootstrap
