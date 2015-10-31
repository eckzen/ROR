1. This time I'll use scaffold generators to develop a Rails app.

	a. A scaffold in Rails is a full set of model, database migration for that model, controller to manipulate it, views to view and manipulate the data, and a test suite for each of the above.

2. TERMINAL : rails new sampblog -d mysql

	a. Generates the application skeleton using MySQL

3. TERMINAL : bundle install 

	a. Tells bundler to get everything our app needs 

4. Set up the database and files

	a. Open the files in Sublime TERMINAL : subl . (OSX/Linux) or subl.exe folder_name (Windows)

	b. Edit /config/database.yml

		1. default: &default
  		adapter: mysql2
  		encoding: utf8
  		pool: 5
  		username: sbadmin
  		password: password
  		host: localhost
  		socket: /var/mysql/mysql.sock

		development:
  		<<: *default
  		database: sampblog_development

  	c. Create the MySQL database and user

  		1. mysql5 -u root -p OR mysql -u root -p

  		2. MySQL TERMINAL : CREATE DATABASE sampblog_development;

		3. MySQL TERMINAL : SHOW DATABASES;

		4. Switch to database
			a. MySQL TERMINAL : USE sampblog_development;

		5. Create a user for our database

			a. MySQL TERMINAL : 
			GRANT ALL PRIVILEGES ON sampblog_development.*
    		TO 'sbadmin'@'localhost'
    		IDENTIFIED BY 'password';

    	6. MySQL TERMINAL : exit

    	7. MySQL TERMINAL : mysql5 -u blogadmin -p

    	8. MySQL TERMINAL : use sampblog_development;

5. Users Database

	a. Users Table

		1. id : integer

		2. password : string

		3. email : string

	b. Article Table

		1. id : integer

    	2. title : string

    	3. post : string

    	4. user_id : integer

6. Create User resource with Scaffold Generator

	a. TERMINAL : rails generate scaffold User password:string email:string

		1. State we want password and email of type string in the database

		2. id is automatically generated

		3. The Model, Controller, Views, Migration File and more is 
		automatically created

	b. TERMINAL : rake db:migrate

		1. Migrate the database, or create the table 

		2. MySQL TERMINAL : show tables;

		3. MySQL TERMINAL : describe users;

    c. TERMINAL : rails server

    	1. Starts the server

7. The Users Views

	a. _form.html.erb

		1. Way of separating out duplicate code. Put <%= render 'form' %>
		any place you want this code to show

	b. edit.html.erb

		1. http://localhost:3000/users/1/edit

	c. index.html.erb

		1. http://localhost:3000/users

	d. index.json.jbuilder

	e. new.html.erb

		1. http://localhost:3000/users/new

	f. show.html.erb

	g. show.json.jbuilder

	h. rake routes (Defines the methods execute on a resource)

		1. Prefix Verb   URI Pattern         Controller#Action
    	users GET    /users(.:format)        users#index

    		a. Get all resources

        POST   /users(.:format)          users#create

        	a. Create a new resource

 		new_user GET    /users/new(.:format) users#new
		edit_user GET    /users/:id/edit(.:format) users#edit
     	user GET    /users/:id(.:format)   users#show

     		a. Get a single resource

        PATCH  /users/:id(.:format)      users#update
        PUT    /users/:id(.:format)      users#update

        	a. Used to update a partial resource

        DELETE /users/:id(.:format)      users#destroy

        	a. Delete a resource

8. Process of retrieving User Data (Index)

	a. Browser : http://localhost:3000/users

	b. Route : users -> index action

	c. index Action : Tell model to retrieve user data

	d. Model asks database for all user data

	e. Model returns user data to Controller

	f. Controller stores user data in @users instance variable for view

	g. View displays the users using embedded Ruby

	h. Controller sends HTML to browser  	

9. routes.rb

	a. Rails.application.routes.draw do
  resources :users
  root 'users#index'

  		1. Call controller users and action index at the root URL

10. users_controller.rb

	a. 
	class UsersController < ApplicationController
  	before_action :set_user, only: [:show, :edit, :update, :destroy]

  		1. A before action halts the request for show, edit, update, destroy
  		until a user has been created

  	def index
    	@users = User.all
  	end

  		2. Create an instance variable that contains all user data

11. index.html.erb

	a. 
	<% @users.each do |user| %>
      <tr>
        <td><%= user.password %></td>
        <td><%= user.email %></td>
        <td><%= link_to 'Show', user %></td>
        <td><%= link_to 'Edit', edit_user_path(user) %></td>
        <td><%= link_to 'Destroy', user, method: :delete, data: { confirm: 'Are you sure?' } %></td>

		1. Cycles through and prints all user data

		2. Show calls for user data with matching id

		3. Edit opens edit screen for user with matching id

		4. Destroy deletes user after the deletion is confirmed

12. Create Article resource with Scaffold Generator

	a. TERMINAL : rails generate scaffold Article title:string post:text user_id:integer

    b. TERMINAL : rake db:migrate

13. routes.rb

	a.
	Rails.application.routes.draw do
  	resources :articles

  	resources :users

  	b. articles_controller.rb Controller is created

  	c. _form, edit, index, new, show views are also created

14. Setting requirements with validates

	a. user.rb Model

		1. class User < ActiveRecord::Base
  			validates :password, presence: true,
    				length: { minimum: 8 }
  			validates :email, presence: true
		   end

	b. article.rb Model

		1. class Article < ActiveRecord::Base
  			validates :title, presence: true,
    			length: { maximum: 50 }
  			validates :post, presence: true
			end

15. Create association between user and article with belongs_to and has_many

	a.class User < ActiveRecord::Base
  		has_many :articles
  		validates :password, presence: true,
    		length: { minimum: 8 }
  		validates :email, presence: true
		end

	b. class Article < ActiveRecord::Base
  		belongs_to :user
  		validates :title, presence: true,
    		length: { maximum: 50 }
  		validates :post, presence: true
  		validates :user_id, presence: true
		end

16. Generate comment model

	a. rails generate model Comment commenter:string body:text article:references

    b. rake db:migrate

    	1. Creates Comment table and adds foreign keys

    c. class Comment < ActiveRecord::Base
  		belongs_to :article
		end

	d. routes.db

		1. Rails.application.routes.draw do
  			resources :articles do
    			resources :comments
  			end

  			resources :users

  			a. Make comments a nested resource inside of Article

  	e. Generate Controller for Comment

  		1. TERMINAL : rails generate controller Comments

  	f. Update article/show.html.erb so comments can be made

  		1. <p id="notice"><%= notice %></p>

<p>
  <strong>Title:</strong>
  <%= @article.title %>
</p>

<p>
  <strong>Post:</strong>
  <%= @article.post %>
</p>

<p>
  <strong>User:</strong>
  <%= @article.user_id %>
</p>

<h2>Comments</h2>
<% @article.comments.each do |comment| %>
  <p>
    <strong>Commenter:</strong>
    <%= comment.commenter %>
  </p>

  <p>
    <strong>Comment:</strong>
    <%= comment.body %>
  </p>
<% end %>

<h2>Add a comment:</h2>
<%= form_for([@article, @article.comments.build]) do |f| %>
  <p>
    <%= f.label :commenter %><br>
    <%= f.text_field :commenter %>
  </p>
  <p>
    <%= f.label :body %><br>
    <%= f.text_area :body %>
  </p>
  <p>
    <%= f.submit %>
  </p>
<% end %>

<%= link_to 'Edit', edit_article_path(@article) %> |
<%= link_to 'Back', articles_path %>

	g. Update /controllers/comments_controller.rb

		1. class CommentsController < ApplicationController
			def create

			# Get Article comment is attached to

    		@article = Article.find(params[:article_id])

    		# Create and save comment

    		@comment = @article.comments.create(comment_params)

    		# Go to the article this comment is associated with

    		redirect_to article_path(@article)
  			end

  		private
    		def comment_params
      		params.require(:comment).permit(:commenter, :body)
    	end

17. Add style to Article index with Sass

	a. /assets/stylesheets/articles.scss

		1. table{
  			border-collapse: collapse;
			}

			table tr td{
  				padding: 10px;
			}

			thead {
  				text-align: left;
  				background: #00308F;
  				color: #FFF;
			}

			thead th {
  				padding: 10px;
			}

			.list_line_even {
  				background: #FAEBD7;
			}

			.list_line_odd {
  				background: #FEFEFA;
			}

    b. /views/articles/index.html.erb

    	1. 
    	<p id="notice"><%= notice %></p>

<h1>Listing Articles</h1>

<table>
  <thead>
    <tr>
      <th>Title</th>
      <th>Post</th>
      <th>User</th>
      <th colspan="3"></th>
    </tr>
  </thead>

  <tbody>
    <% @articles.each do |article| %>
      <tr class="<%= cycle('list_line_odd', 'list_line_even') %>">
        <td><%= article.title %></td>
        <td><%= article.post %></td>
        <td><%= article.user_id %></td>
        <td><%= link_to 'Show', article %></td>
        <td><%= link_to 'Edit', edit_article_path(article) %></td>
        <td><%= link_to 'Destroy', article, method: :delete, data: { confirm: 'Are you sure?' } %></td>
      </tr>
    <% end %>
  </tbody>
</table>

<br>

<%= link_to 'New Article', new_article_path %>