# Ruby on Rails
1. Convention over Configuration: Don't worry how to configure as long as you follow convention.
2. `inflections`: are responsible for creating lowercase plural controller and/or routes etc when we
3. Diff between `new` and `create` action ? New instantiate the object and create communicates with the model.
4. Naming convention between `views` and `action` ? Both have to have the same name so it can communicate by default.
5. When `scaffold` and `controller` are used with generator command i.e `rails g XXX` it creates everything needed for the
application ( i.e view, model, controller methods etc ) but when we do `model` with generator we limit just to ActiveRecord.
6. `resource` generator is most commonly used in daily basis.
7. `application.rb` is the main file and entry point of the app, Like in react we have `App.js`.
8. partials can be created to share the common things to be shared, which by convention always starts with underscore.
So we can have `_shared.html.erb` to be used with `create.html.erb` and `edit.html.erb`
9. if you make changes / updates to `routes.rb` always make sure to restart the server by doing `rails s`.


### How it works ?
Rails follows MVC pattern, so that being said whenever we use `generator` to create `scaffold` or `controllers` it ties everything in Model View Controller way. For simplicity, lets image we hit url `localhost:3000/pages/info` in rails app.
1. Step 1 (route):
`pages/info` is route defined in the `routes.rb` file i.e `config/routes.rb`. Which may look as mentioned below and rails will
tie this to `Page` controller with action `info`.
```ruby
# config/routes.rb
Rails.application.routes.draw do
  get 'pages/info'
end
```
In short, it accepted the request for route `pages/info`.

2. Step 2 (action / controller):
As mentioned in routes, all the route definitions are mapped to `actions` in the controller. i.e (controller/pages_controller.rb)
So corresponding `pages/info` route action would look like,
```ruby
class PagesController < ApplicationController
  def info
    @pageData = Company.all
    #instanceVariable = ModelName.getAll
  end
end
```
In short, if the accepted routes has action defined then it will be called. So we can process the data from model or do some
calculations. In our case we called model `Company` and fetch everything the model has.

3. Step 3 (model):
Now the model can query data from the db etc, so Company model would look like
```ruby
class Company < ApplicationRecord
end
```
In short, model gives connection to database.

4. Step 4 (view):
Important things to remember instance variable declared in controllers action methods as made available to `view` by ruby, so we don't have worry about connecting it. So in Step 2 `@pageData` was an instance variable for action `info` so view page `info.html.erb` has this ready to be used.
```
<h1> Info Page </h1>
<%= @pageData.inspect %>
```
can see everything pageData instance has on the html page.


### Ruby Version Manager.
- `rvm list` ( lists all of the ruby )
- `rvm install 2.5.0` ( rvm installs the ruby of version 2.5.0 and gives option to switch back to older version )
- `rvm use 2.4.0` ( points the current version of ruby to 2.4.0 )
- `rvm gemset list` ( points where all the gems are )
- `rvm gemset use global` ( points to use global gems installed rather than default location )

### Gem Configuration
- `gem -v` (version of gem)
- `gem update --system` ( updates right set of gem with respect to ruby version pointed by the rvm )
- `gem update` ( updates the gem )
- `gem install bundler` ( packaging tool )
- `gem install nokogiri` ( gem for parsing, communicating etc make sure to install this before rails )
- `gem install rails` ( rails is a gem as well )


### Run Rails App
```ruby
rails new Blog -T --database=postgresql
```
`T`: don't include test frame work
`database`: defining our database instead of default sql light  
```
rails db:create
rails db:migrate
rails s
```
#### Helpful Rails Command
- `rails -h` : helper list
- `--api` : If we decide to skip rails frontend and only have rails backend then use this.
- `rails c` : direction connection to database to execute query

### Rake
- `rake routes` ( finds all the routes in the system )

### GENERATORS:
We will dive through different type of generators which we can use to build boilerplate automatically for us by rails.

#### Scaffold
Ability to create multiple items at the same time using rails generator. Scaffold is one of the rails generator we could use.
```ruby
rails g scaffold Blog title:string body:text
# rails generator typeOfGenerator nameOfFeature attributes:dataType
```
This generates all the necessary things (i.e views, controllers, stylesheet, database etc) for us. We can verify this by checking `db/migrate/timestamp_create_nameOfFeature.rb`. Until this point rails has established the ActiveRecord which will be used to store data however we need to migrate this to our chosen db (`db/schema.rb` will not have these changes).
```ruby
rails db:migrate
```
1. Before
```ruby
ActiveRecord::Schema.define(version: 0) do
  enable_extension "plpgsql"
end
```
2. After
```ruby
ActiveRecord::Schema.define(version: 2018_09_28_164347) do
  enable_extension "plpgsql"

  create_table "blogs", force: :cascade do |t|
    t.string "title"
    t.text "body"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end
end
```
3. Verify
Until this point the scaffolding has created all the boilerplate we needed to have the application running, we migrated the database so we can store it in postgresql. How can we verify ?
```ruby
rails s #starting the server
```
Navigate to `localhost:3000/blogs`, wala we see the new page. This has been achieved by routes defined in `config/routes.rb` and respective app/controllers, app/views etc.

#### Controllers
```ruby
rails g controller Pages home about contact
# If we need to delete or revert them
rails destroy controller Pages home about contact
```

#### Model
Model is most light weight generator rails has, which doesn't create anything in controller or views but just the data records. So if we weigh then `scaffold` > `controller` > `model`.

1. Step (generator command):
```ruby
rails g model Skill title:string value:integer
#rails generator typeOfGenerator nameOfFeature tableColumnName:tableType tableColumnName:tableType
```
This would create connection to our database using ActiveRecord
```ruby
# models/skill.rb
class Skill < ActiveRecord
end
```
and added database migration script like
```ruby
#db/migrate/CURRENTDATE_create_skills.rb
class CreateSkills < ActiveRecord::Migration[5.2]
  def change
    create_table :skills do |t|  #table name skills
      t.string :title #first column we defined
      t.integer :value #second column we defined

      t.timestamps #this is added by rails by default
    end
  end
end
```
2. Step (migrate database):
Up to this point we have create the database but this would not have updated the schema needed to apply for our postgresql database. We can verify the file `db/schema.rb` before and after running the migrate command.
```ruby
rails db:migrate
```
3. Step ( optional: add record )
Adding record directly to database from rails command line `rails c`.
```ruby
Skill.create(title:'Rails',value:35)
#modelName.create(columnName: stringValue, columnName: intValue)
```
this would result in adding data record and we can verify by typing `Skill.all` in the `rails c` session. This is similar to what we would do in controller action method if we need to retrieve data from the model. Example, if we happen to have action method `rating` in Controller `Book` then it would look like
```ruby
class BooksController < ApplicationController
  def info
    @bookData = Skill.all
    #instanceVariable = ModelName.getAllData i.e like ( select * from Skill )
  end
end
```

#### Resources
Resources are miniature version of `scaffold`, we would get pretty much everything what we would from `scaffold` minus the views.
```ruby
rails g resource Portfolio title:string description:string link:text
```
i.e it would create model, controller, and empty directory in views but nothing in it. So this would be similar to above what we did in `Model` so if we do `rails db:migrate` we are pretty much done with establishing the database connection and make it ready to use.

#### Custom Generator
Until this point we have been using predefined generators such as `model`,`controller`,`scaffold`, and `model`.
Here we can see how we can leverage custom generator.

#### Gem
In this section we would explore creating Gem, when we execute below bundle ( make sure bundler is installed ) we create boilerplate needed to
create gem and it also initiate the git repo for us.

1. Step (create gem):
```ruby
bundle gem cittalab_rights_gem
#bundle gem gem_name
```

2. Step (Add methods):
we can add modules, actions we need to define as part of the gem. Example we are adding copyrights module to be used in the gem by writing separate `Renderer` class. So inside lib/gem_name.rb add new require for renderer i.e `require "cittalab_rights_gem/renderer"`. Create `renderer.rb` inside cittalab_rights_gem and define the class
```ruby
#module gemName (should match cittalab_rights_gem)
module CittalabRightsGem
  class Renderer
		def self.copyright name, msg
			"&copy; #{Time.now.year} | <b>#{name}</b> #{msg}".html_safe
		end
	end
end
```
3. Step (update gitignore)
```
*.gem
```

4. Step (Git)
Create new repot for gem file with the same name as gem name i.e cittalab_rights_gem. Copy below from the git instructions
```
git remote add origin https://github.com/citta-lab/cittalab_rights_gem.git
git push origin master
```

5. Step (build gem)
```
gem build cittalab_rights_gem.gemspec
```
this changes doesn't need to be pushed to github and hence we added `*.gem` to gitignore.

6. Step ( use gem )
If we are not pushing the gem to the gem-repo we can leverage the gem in app by adding below line in `Gemfile`
```
gem 'cittalab_rights_gem', git:'https://github.com/citta-lab/cittalab_rights_gem'
```

#### Local Gem
If we are in a scenario to work off of local gem in the app and is added in the gemspec then we need to do below two steps.

1. update gemspec
```ruby
spec.add_dependency 'example-gem'
```

2. update gemfile
```ruby
gem 'example-gem', :path => '../example-gem'
```
if we don't need to add in gemspec then we can just get away with step 2. Refer this post from [stackoverflow](https://stackoverflow.com/questions/13678957/how-to-add-dependency-of-a-local-gem-to-a-rails-plugin-engine-in-gemspec-file).



### Building Blocks
1. Before Action :
```ruby
before_action :set_blog, only: [:show, :edit, :update, :destroy]
#beforeRunningAnything: callSetBlogMethod onlyFor methodNameInArray
```
before_action is used to run the identical code which can be part of multiple methods. So before calling `show` or `edit` etc it will make sure to call `set_blog` method and execute the code.

2. resources :
Resources is a special word which encapsulates all RESTFull routes and hence we can have `GET, POST, UPDATE, and DELETE` by just having `resource` pointing it to the instance.
```ruby
Rails.application.routes.draw do
  resources :blogs
end
```
we can verify this by doing `rake routes` which will display routes as mentioned below
```
blogs   GET    /blogs(.:format)    blogs#index
```
3. inflections :
Is responsible for creating and/or generating lowercase plural controllers, views, models when we created an application. Example
```ruby
rails new Blog -T --database=postgresql
```
created controller as `blogs_controller.rb` and `blogs` folder in views etc.

4. link_to :
If we want to link some method call in the view i.e (`example.html.erb`) file then we can go about it in two ways,
- manual way using href.
- rails way using link_to.
Lets imagine we need to have an ability to link new page call upon clicking `create new` in some `exampleFile.html.erb`, then we can do
```
<h1> Manual way </h1>
<a href='pages/new' > Create New </a>
```
The better way is doing rails way,
```
<h1> Rails way </h1>
<% link_to "Create New", new_page_url %>  
```
Wow, where did these `pages/new` and `new_page_url` come from ? If we do `rake routes` we can see list of routes defined in the app. For
example,
```
Prefix    Verb   URI Pattern                     Controller#Action
pages     GET    /pages(.:format)                pages#index
          POST   /pages(.:format)                pages#create
new_page  GET    /pages/new(.:format)            pages#new       <--- URI Pattern is used in Manual way & Prefix is used in Rails way
edit_page GET    /pages/:id/edit(.:format)       pages#edit
```
So as i mentioned prefix is used in rails way, however we had to add `_path` to the prefix instead of using it directly. If we just use the
prefix then it would give us path with domain name like `http://localhost:3000/pages/new` but we just needed to update the URI pattern with
`pages/new`.

5. Routes (routes.rb file):
In rails world we always start with defining routes then adding respective action in controller, view to complete the process. That being established we can customize the routes instead of using from generators such as `scaffold` or `resource`. First step is to run `rake routes``
to understand established routes generated by generators.
```
Prefix         Verb   URI Pattern                     Controller#Action
pages_home     GET    /pages/home(.:format)             pages#home
pages_about    GET    /pages/about(.:format)            pages#about
pages_contact  GET    /pages/contact(.:format)          pages#contact    
```
Now lets look routes defined in routes.rb file by generators,
```ruby
Rails.application.routes.draw do
  get 'pages/home'
  get 'pages/about'
  get 'pages/contact'
  resources :blogs
end
```
Now lets look at the controller action methods corresponding to these ( ignore it's just the skeleton )
```ruby
class PagesController < ApplicationController
  def home
    #called when pages/home is called
  end

  def about
    #called when pages/about is called
  end

  def contact
    #called when pages/contact is called
  end
end
```
So we can customize this by redefining the routes as
```ruby
Rails.application.routes.draw do
  #custom routes
  root to: 'pages#home'  #made this as default page, so this will be loaded on opening the app
  get 'about-me', to: 'pages#about' #gave custom name to about action
  get 'contact', to: 'pages#contact'
  resources :blogs
end
```
Similarly, we can also override the default `resources` added by the generator with custom route name, example `resources :blogs` line routes.rb can we overwritten as
```
resources :blogs, except: [:show]
get 'blog/:id', to: 'blogs#show', as: 'blog_show'
```
In first line, we said use resources for everything but `show` and defined `get` CRUD operation to define path referenced from `rake routes` and pointed to controller action method i.e `controller#actionMethod` and renamed it as `blog_show` so we can use this in any of the link_to inside views. example, updated view will be as mentioned below,
```
<p><%= link_to item.title , blog_path(item.id) %></p>       <-- BEFORE
<p><%= link_to item.title , blog_show_path(item.id) %></p>  <-- AFTER
```

5.1 Namespace
If we need to modify or add default route path to add for all children path then we can make use of namespace to bundle all the child paths as mentioned below and update the controller and views to reflect the changes.
```ruby
# namespace implementation
namespace :admin do
  get 'dashboard/main'
  get 'dashboard/user'
  get 'dashboard/blog'
end
```
This will result in responding to url path such as `admin/dashboard/user` etc. To make this work, we need to update the controller folder to have admin as child folder and then `dashboard/dashboard_controller.rb`. Similarly, in views `admin/dashboard/user.html.erb`. Make sure to update the controller class to have `Admin` in front of it. i.e `class Admin::DashboardController < ApplicationController` in dashboard_controller.rb



### Question
1. How does it redirect from new to create ? You mentioned it will call create whenever we hit create button but it wasn't clear if there is any implicit routes defined by rails ? if so can you explain where ?
2. In lecture #33 you were explaining about index and show action method, Can you explain how and what `Blog` is in `@blogs = Blog.all`. Is it referencing to an class or an instance of the class or how did this land in this page ? file doesn't show it has been imported.
