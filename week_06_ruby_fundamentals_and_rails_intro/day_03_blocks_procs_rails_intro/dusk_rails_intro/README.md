
#Intro to Rails

###Goals for today:

- Apply knowledge of web frameworks to Rails
- Apply the concept of Model View Controller to structure applications
- Build a fullstack app in Rails

| Note |
| :--- |
| This is a first introduction and high level overview of Rails. No one should expect to fully understand all the topics covered. We will discuss each topic more in specialized lessons.|

### Past Feedback

| Graham Wong (WDI SF Jan-March) via Hipchat|
| :-- |
| *I think Rails is one of those things that just makes sense once you start doing it*  |

### Moving Away from Node & Express

What do we want you to walk away with from Express?

* Handling params in a request
* Routing a request
* Presenting a view with ejs
* CRUD and RESTful resources
* Object / Relational mapping and migrations

If you are familiar with those then you are prepared and will practice them again in Rails. Try not to worry about data or databases when reviewing these notes.

### Why The Rails Workflow and Organization?

In a bullet list, the why is as follows:
  
* to separate certain aspects of a larger applications
* to familiarize ourselves with the most common pattern used to design web applications, **Model-View-Controller**
* to facilitate workflow using conventions

**MVC**

| Component | Type of Concern |
| :--- | :--- |
| <b>M</b>odel | Data Concerns |
| <b>V</b>iew | Presentation Concerns |
|<b>C</b>ontrollers  | Request and Response (or Action) Concerns |

Having a **Model** component helps avoid issues of mixing the logic of gathering or editing data with displaying it (*Views*) or determining what data is related to requests from app users (*Controllers*).

Having a **View** dedicates one component just to specializing presentation of html to users using data without worrying about how data was retrieved (*Model*) or if it was relevant to a request made by a user (*Controller*).

Having a **Controller** component helps manage which data resources are relevant to a request from a user, what to do with the data, and how to respond to a user without worrying about how to present an html response (*View*) or manage logic concerning data(*Model*).

![MVC Diagram](http://elibildner.files.wordpress.com/2012/06/screen-shot-2012-06-05-at-2-12-18-am.png)

**Excercise (5 min): Talk amongst yourself how the Node/Express express framework supports MVC, or not.**
	
### Related Materials/Information

* Conventions over Configurations 
* Models, Views, and Controllers 
* HTTP (**GET, POST, PUT, DELETE**) verbs
* The `params` of a request, (Covered in **Express**)
* Forms (**action**, **method**, and **form fields**)
* render & redirects
* ORM
* `http://guides.rubyonrails.org`

### Lesson Road Map

We demonstrate MVC by making an application to handle managing our favorite cook books.

* Part I 
	* Create a new **app folder** for our app
	* Create **new rails app** and explore directory structure
	* Design a **simple object model**
	* Use rails generators to **create MVC stubs** for a `Book` 
	* Inspect and discuss generated files:
		* Book model
		* Books controller
		* Book views
		* Migration files
	* Run a **database migration**
    * Write a **route** for the home page 
    * Add a **controller** for static pages
    * Add **routes** and **links** to static pages
* Part II    
    * Add a **second resource** `Recepie`
    * Create **association** between `Book` and `Recepie`
    * Explore models in **rails console**
    * Add **validations**
    * Customize **forms** to create Book/Recepie association
    * Add a **third resource** and demonstrate **many to many** relationships 
        

## PART I - Let's get started

Verify:

	rails --version

We’ll develop a simple demonstration application to show off some of the power of Rails. The app manages cookbooks and recipes, it's a cookbook_app.


Creating a new app:

	rails new cookbook_app
	cd cookbook_app
	git init; git add .; git commit -m'initial commit'

Let's take a tour ...

###Gemfile

Contains **gems** (packages) that you include in the app. Check out the [Rubygems](https://rubygems.org) and the [Ruby Tool Box](https://www.ruby-toolbox.com/)

	`...` 		use specified version
	`>= ...` 	use specified version, or greater 
	`~> ... ` 	pessimistic version constraint 

##### Installing gems

	bundle install

Check what's installed:

	gem list
	
**Let's fire up the server and see what we've got.** 

In the project root dir, run `rails server`, or short:

	rails s
	
Go to [Cookbook App](http://localhost:3000)	
	
###app folder

`/app` is the most important folder. It's the home for MVC components

- No models (yet)
- No views, but a `layout` (`erb` stands for embedded ruby)
- A single controller: `ApplicationController`.  The **superclass** of all controllers in the system.

**Convention:**
 
- In general:
	- Rails class names are **CamelCase**
	- File names are **snake_case**
	
#####ApplicationController
	
Important: Controller names are **plural**.

What's `protect_from_forgery`? Rails magic! A great example of out-of-the-box functionality.

It's a *CSRF countermeasure*, let's take a look at rails guides:

*"To protect against forged requests, we introduce a required security token that our site knows but other sites don't know. We include the security token in requests and verify it on the server"* 

### assets folder

`app/assets` - A place for:

- images
- javascripts
- stylesheets

Automatically included in your app via the `Asset Pipeline`

### config folder

Pre-configured set of system and application settings - change only as neccessary.

Some highlights:

- environments/*
- database.yml
- **routes.rb**

	
##Moving on with cookbook_app

We are taking the *bottom up* approach, i.e. we start by thinking about the objects in our system: 

Let's start with two objects and a simple association:

	Book and Recipe

The Rails models, as well as corresponding controllers and views, will be created by a scaffold generator program, which comes standard with each Rails project. 

**Note:** In a RESTful world, **objects** that are part of the MVC pattern, such as `Book` or `Recepie`, are referred to as **resources** 

###Books

Let's start with cook books. In rails, we can use generators to create code stubs. Here we use the **scaffold** generator.

	rails g scaffold book title:string cuisine:string chef:string image:text

Generators take care of proper naming, i.e. controller names will be **plural** and model names will be **singular**

**Scaffolding** generates a complete set of MVC objects, fully *CRUDed*. If you want a little bit more control over what's being generated, you have the option to generate models and controllers individually.

	rails g model MODEL_NAME attr1:type attr2:type ... 
	rails g controller CONTROLLER_NAME method1 method2
	
Use `rails g` to view a list of all generators.

**Database Migration**

To proceed with the cookbook application, we first need to migrate the database using `rake`.

**Rake** is a tool you can use with Ruby projects. It allows you to use ruby code to define "tasks" that can be run in the command line. Use `rake -T` to see a list of build-in tasks.	

Run the migration:

	bundle exec rake db:migrate

This simply updates the database with the new `Book` data model.

Go to [Cookbook App](http://localhost:3000) and take a look at the the new model.

#####Explore generated files (diagram)

- A **model** - subclass of `ActiveRecord`
- A **controller** - subclass of `ApplicationController`
	- CRUD methods
	- build-in json api
	- instance variables
	- render & redirect
	- Filters: methods that are run before, after or "around" a controller action.
	- params.require ...
- A set of **views**
	- erb
	- partials
	- forms
	- link_helpers
- Routes! `rake routes`
- Migration file
- Tests!

**Example: new route/controller/view mapping**

**Excercise 1 :**

1) Change the home page to display list off books instead of 'Rails welcome' page.

2) Create a controller `Corporate` that serves *about* and *careers* pages:

- Generate controller with 'about' and 'careers' methods
- Add two routes to 'about' and 'careers'
- Add dummy text to 'about' and 'careers' views
- Create links to 'about' and 'careers' in the nav bar


### Adding 2nd resource: Recipe

**Excercise 2 :** 

Let's add a second resource `Recipe` using scaffold generator. We set up the one-to-many relationsship to `books` by including the foreign key `book_id`. Here's the data model:

```
# Table name: recipes
#  id - auto-generated by rails
#  name         :string(255)
#  course       :string(255)
#  cooktime     :integer
#  servingsize  :integer
#  instructions :text
#  image        :text
#  book_id      :integer
```

Note that Rails automatically adds an ID column 
 
###Book/Recipe Model association

Modles are subclasses of `Active Record`, the rails ORM. Active Record is very powerful. Here's how you create a basic association. 

**app/models/book.rb**

```
class Book < ActiveRecord::Base
  has_many :recipes
end
```

**app/models/recipe.rb**

```
class Recipe < ActiveRecord::Base
  belongs_to :book
end
```

Verify relationship in **rails console**, fire up `rails console`, or short:	

	rails c

**Playing CRUD in rails console**

```
b = Book.new
b.title = "Great German Cooking"
b.chef = 'markus'

b
b.save

b.all
b.first

Book.find_by_chef("markus")
Book.where(chef: "markus")
Book.find_by_chef_and_cuisine("markus", "german")
Book.where(chef: "markus", cuisine: "german")

Book.where(chef: "markus").delete_all

!!Excercise create recepies and associate with a book

```
 
### End of Part 1 - summary

What we've learned:

- High-level overview of Rails
- Introduction to MVC
- First taste of the REST architecture
- Basic data modeling with Active Record


##Part II - Extending cookbook app

Fork and clone this [repo](https://github.com/wdi-sf-fall/cookbook_app). Then run:

	bundle install
	rake db:migrate
	rake db:seed

Up next: 

- Model association
- Model validations
- Forms: Making the Book / Recepie association in Views
- many-to-many relationships: adding ingredients


###Active Record	- validations

**From rails guides:**
*Validations are used to ensure that only valid data is saved into your database. For example, it may be important to your application to ensure that every user provides a valid email address and mailing address. Model-level validations are the best way to ensure that only valid data is saved into your database. They are database agnostic, cannot be bypassed by end users, and are convenient to test and maintain. Rails makes them easy to use, provides built-in helpers for common needs, and allows you to create your own validation methods as well.*

Add validation to Book model

		validates :title, presence: true
		validates :title, length: { minimum: 3 }
 
Add multiple rules to single `validates`:
 
```
 validates :title, presence: true, length: { 
		minimum: 3,
		maximum: 100,
		too_short: "must have least %{count} letters, dude",
		too_long: "is way too long"
  }
```  
 
The `:on` option lets you specify when the validation should happen. The default behavior for all the built-in validation helpers is to be run on save (both when you're creating a new record and when you're updating it).	 

	validates :title, uniqueness: true, on: :create

### Updating Book & Recepie Forms

books/show.html.erb:

```
<% @book.recipes.each do |recipe| %>

  <div class="row">
    <div class="col-md-5 col-sm-5">
      <p class="lead"><%= recipe.name %></p>
      <%= image_tag(recipe.image) %>
    </div>
   </div>

<% end %>
```

books/_form.html.erb:


	<div class="input-group">
    <% Recipe.order(:name).each do |recipe| %>
       <span class="input-group-addon">
        <%= recipe.name %>
        
        <%= check_box_tag 'book[recipe_ids][]', recipe.id, recipe.in?(@book.recipes), {class: "checkbox inline"} %>
        </span>
    <% end %>
	</div>

### Weekend lab - Continue work on cook book app

Assignment:

- Introduce a new resource `ingredient`. Here is the table schema:

```
# Table name: ingredients
#
#  id          :integer          not null, primary key
#  name        :string(255)
#  measurement :string(255)
#  image       :text
```

* Add many-to-many relationship between `ingredient` and `recipe`
* Create and change views to support new model:
	* Create views to add, delete, show and list ingredients
	* Change the recipe new/edit form to support assigning ingredients	
	* Show ingredients on recipe page

Hints:
- Research [rails guides](http://guides.rubyonrails.org/association_basics.html) find out how many-to-many realtionships work.
- many-to-many relationships require a join table. A join table is not a model, just a table. You will need to create a *stand alone migration* that creates the join table, see [Migrations](http://guides.rubyonrails.org/migrations.html#creating-a-standalone-migrationl)
- This [Stackflow article](http://stackoverflow.com/questions/19473044/rails-4-many-to-many-association-not-working)


### Lab Solution


####1) Create ingredient resource

Looks like a typical MVC resource, let's scaffold:

	rails g scaffold ingredient name:string measurement:string image:text

Verify that resource was created. How?

####2) Create join table for recipe and ingredients

Create *stand alone* migration and run it. There is a migration generator which will produce join tables if JoinTable is part of the name, see rails guides. Let's try this:

	rails g migration CreateJoinTableRecipeIngredient recipe ingredient

Inspect generated migration file:

	class CreateJoinTableRecipeIngredient < ActiveRecord::Migration
  		def change
    		create_join_table :recipes, :ingredients do |t|
      			# t.index [:recipe_id, :ingredient_id]
      			# t.index [:ingredient_id, :recipe_id]
    		end
  		end
	end

[API docs](http://apidock.com/rails/ActiveRecord/ConnectionAdapters/SchemaStatements/create_join_table) for **create_join_table** method

**Alternatively**: You can also create the table explicitly. To generate an empty migration file:

	rails generate migration AddIngredientsRecipesTable


Generates: 

```
class AddIngredientsRecipesTable < ActiveRecord::Migration
  def change
  end
end
```

Now you add **create_table** statement directly:

	create_table :ingredients_recipes, :id => false do |t|
		t.integer :ingredient_id
		t.integer :recipe_id
	end
 

Either way, when done, run migration:

	rake db:migrate
	
Verify that table was created in DB. In this project we use sqlite:

	sqlite3 db/development.sqlite3 
	> .schema
	
	
####3) Add many-to-many association in models

In [rails guides](http://guides.rubyonrails.org/association_basics.html): Choosing Between has_many :through and has_and_belongs_to_many

Our join table is not an independent entity, i.e. a first class model, it's only a join table, hence we chose **has_and_belongs_to_many**

In `/app/models`:

```
class Recipe < ActiveRecord::Base
  belongs_to(:book)
  has_and_belongs_to_many :ingredients
end
```

```
class Ingredient < ActiveRecord::Base
	  has_and_belongs_to_many :recipes
end
```

####4) Test in rails console

Fire up rails console:

	Recipe.first.ingredients

	Ingredient.first.recipes
	
We don't get any errors, we're good to go. Inspect SQL, looks good?

```
SELECT  "recipes".* FROM "recipes"   ORDER BY "recipes"."id" ASC LIMIT 1
SELECT "ingredients".* FROM "ingredients" INNER JOIN "ingredients_recipes" ON "ingredients"."id" = "ingredients_recipes"."ingredient_id" WHERE "ingredients_recipes"."recipe_id" = ?  [["recipe_id", 22]]

```	

Yes, it does.

####5) Add seed data

In /db/seed.rb, add seed data to test new relationship:

	r1.ingredients = [i1, i2, i4]

Run

	rake db:seed
	
Go to localhost:3000 and verify the UI, did the seed data stick?
 		
####6) Make ingredients show up on recipe page

	<div class="col-md-7 col-sm-7">
    <% @recipe.ingredients.each do |ingredient| %>
      <%= ingredient.name %>
      <%= image_tag(ingredient.image,  size: "40x40") %>
    <% end %>
	</div>

####7) Add ingredients to recipe add/edit form


	<div class="input-group">
    <% Ingredient.order(:name).each do |ingredient| %>
      	<span class="input-group-addon"><%= ingredient.name %>
      	<%= check_box_tag 'recipe[ingredient_ids][]', ingredient.id, ingredient.in?(@recipe.ingredients.to_a), {class: "checkbox inline"} %>
      </span>
    	<% end %>
	</div>


####8) Add link to ingredients to nav bar

Use **url_helper**!

	<li><%= link_to('Ingredients', ingredients_path) %></li>

####9) Style ingredient views

... that's not done :-( 








