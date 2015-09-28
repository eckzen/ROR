1. If we want to generate static pages follow the controller name with the static pages to generate

	a. TERMINAL : rails generate controller Pages home about
	
	b. routes.rb is updated
	
		1. get 'pages/home'
		   get 'pages/about'
		   
	c. class PagesController < ApplicationController
  		def home
  		end

  		def help
  		end
		end
		
		1. When either page is called, code is executed in these methods and then the view is displayed. 
		
	d. app/views/pages/about.html.erb view
	
		1. <h1>About Us</h1>
		<p>We love what we do and do what we love.</p>
		<p>
		<a href="mailto:derekbanas@example.com?
		Subject=I%20need%20help">
		Contact Derek for Help</a>
		</p>
		
	e. app/views/pages/home.html.erb view
	
		1. <h1>Home</h1>
		<p>Welcome to our home page</p>

2. Testing

	1. Tests allow us to simulate a user interacting with our application
	
	2. Create tests that will secure your app, protect from known errors and protect code that is likely to change.

	3. test/controllers/pages_controller_test.rb was generated
	
		a. 
		require 'test_helper'

		class PagesControllerTest < ActionController::TestCase
  		test "should get home" do
    		get :home
    		assert_response :success
  		end

  		test "should get about" do
    		get :about
    		assert_response :success
  		end
		end
		
			1. Issue a GET request for the home action and check that we receive a success status code response
			
	4. TERMINAL : rake db:create --trace
	
		a. Create databases using settings in database.yml
		
	5. TERMINAL : rake test
	
		a. Checks that all assertions are positive
		
	6. Add this to pages_controller_test.rb
	
		a. test "should get directions" do
    		get :directions
    		assert_response :success
  		end
  		
  		b. After TERMINAL : rake test
  		
  			1. ActionController::UrlGenerationError: No route matches {:action=>"directions", :controller=>"pages"}
  			
  	7. Add this to fix the route error
  	
  		a. get 'pages/directions'
  		
  			1. TERMINAL : rake test
  			
  				a. AbstractController::ActionNotFound: The action 'directions' could not be found for PagesController
  				
  	8. Add this to the PagesController
  	
  		a. def directions
  			end
  			
  			1. TERMINAL : rake test
  			
  				a. ActionView::MissingTemplate: Missing template pages/directions
  				
  	9. Create directions.html.erb in /app/views/pages
  	
  		a. <h1>Directions</h1>
		<p>Directions to our place</p>

			1. TERMINAL : rake test
			
				a. 0 errors SUCCESS
				
	10. assert_select checks for the presence of an HTML tag on the page
	
		a. class PagesControllerTest < ActionController::TestCase
  			test "should get home" do
    			get :home
    			assert_response :success
    			assert_select "title", "Welcome"
  			end

  			test "should get about" do
    			get :about
    			assert_response :success
    			assert_select "title", "About Us"
  			end

  			test "should get directions" do
    			get :directions
    			assert_response :success
    			assert_select "title", "Directions"
  			end
			end
			
			1. TERMINAL : rake test

				a. 3 Failures
					<Directions> expected but was <Sampblog>
					<About Us> expected but was <Sampblog>
					<Welcome> expected but was <Sampblog>
					
			2. Fix by adding the titles using provide at the top of each view
			
				a. <% provide(:title, "Welcome") %>
					<% provide(:title, "Directions") %>
					<% provide(:title, "About Us") %>

			3. application.html.erb is the default layout used for every page. It puts what ever is in each page view between the body tags. We can use the value stored in the title symbol for each page with yield.
			
				a. <title><%= yield(:title) %></title>
				
			4. TERMINAL : rake test
			
				a. 0 failures SUCCESS
				
3. Helper Functions

	a. We can define helper functions in app/helpers/application_helper.rb
	
		1. module ApplicationHelper
			def get_season()
    			# Object that retrieves date information
    			# Also retrieve year, day, wday (0 - 6), yday (0 - 365), 
    			hour (24), min (59), sec (59), zone (Time Zone)
    			time = Time.new

    			if(time.month >= 3) && (time.month <= 5)
      				"Yeah it is Spring"
    			elsif (time.month > 5) && (time.month <= 8)
      				"Everyone Loves Summer"
    			elsif (time.month > 8) && (time.month <= 10)
      				"Put on Your Coat because Fall is here"
    			else
      				"Yuck Winter"
    			end
  			end
  			
  			# Gives max_value a default value of 10 if no attribute is passed
  			def get_random_number(max_value = 10)
    			rand(max_value)
  			end
  			end
			
		2. home.html.erb
		
			a. <% provide(:title, "Welcome") %>
				<h1>Home</h1>
				<p>Welcome to our home page</p>
				<p><%= get_season() %></p>
				<p><%= get_random_number(100) %></p>

		3. # Using Arrays to generate random messages
		def get_random_welcome()
    		opening = ["What a beautiful day. ",
                "Welcome to our site. ",
                "Thank you for stopping by. "]

    		middle = ["We hope you find what you need. ",
                "We have a wide selection.  ",
                "Check out our sale items. "]

    		ending = ["Contact us if you need help. ",
                "We are here to server you. ",
                "Call us if you need to 412-555-1212. "]

    		"#{opening[rand(3)]} #{middle[rand(3)]}
    		#{ending[rand(3)]}"

  			end
  			
  			a. Add to home.html.erb <p><%= get_random_welcome() %></p>

		4. def count_to_10
			x = 2

    		number_list = "1"

    		loop do
      			number_list = number_list + ", " + x.to_s
				x += 1
      			break if x > 10
    		end
			"#{number_list}"
			end
			
			a. Add to home.html.erb <p><%= count_to_10() %></p>
			
4. We can layout all of our pages in /app/views/layouts/application.html.erb

	a. <!DOCTYPE html> means we use HTML5 by default
	
	b. application.html.erb
	
<!DOCTYPE html>
<html>
<head>
  <title><%= yield(:title) %></title>
  <%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track' => true %>
  <%= javascript_include_tag 'application', 'data-turbolinks-track' => true %>
  <%= csrf_meta_tags %>
</head>
<body>

<div id="wrapper">

<header>

<%= link_to image_tag("nttlogo.png",  alt: "New Think Tank Logo"),
            {:controller => 'pages', :action => 'home'},
            {:class => 'websiteLogo'} %>

<%= link_to 'READIT' , {:controller => 'pages', :action => 'home'},
{:class => 'websiteTitle'} %>

</header>

<nav id="horzNav">

    <p>
      <%= link_to 'Home', {:controller => 'pages', :action => 'home'} %>
      <%= link_to 'About', {:controller => 'pages', :action => 'about'} %>
      <%= link_to 'Directions', {:controller => 'pages', :action => 'directions'} %>
      <%= link_to 'Articles', {:controller => 'articles', :action => 'index'} %>
      <%= link_to 'Users', {:controller => 'users', :action => 'index'} %>
    </p>

</nav>

<main id="content">

<%= yield %>

</main>

</div> <!-- END OF WRAPPER -->

</body>
</html>

	c. /app/assets/images add nttlogo.png
	
	d. /app/assets/stylesheets/application.css
	
a:hover {
  color: #007ebd;
  background-color: #FFF;
}

body {
  background: #5D8AA8;
}

div#wrapper{
  margin:auto;
  background:white;
  width:800px;
  height:2000px;
}

header {
  display:block;
  /* Padding Top, Right, Bottom, Left */
  padding: 10px 10px 30px 10px;
}

.websiteLogo{
  float:left;
  text-decoration: none;
}

.websiteTitle {
  font-size: 50px;
  text-decoration: none;
  font-weight: bold;
  display: inline;
  line-height: 90px;
  padding-left: 20px;
}

#horzNav {
  display:block;
  /* Padding Top, Right, Bottom, Left */
  padding: 10px 20px 10px 10px;
  background: black;
}

#horzNav a {
  text-decoration: none;
  padding: 0px 0px 0px 20px;
  font-weight: bold;
  color: white;
}

#horzNav a:hover {
  background-color: black;
  color: #007ebd;
}

main {
  /* Padding Top, Right, Bottom, Left */
  padding: 10px 20px 20px 20px;
}