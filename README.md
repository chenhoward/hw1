Rails Decal Homework 1
---------------------

For homework 1 we are going to make a dynamic website that contains a static page about yourself and dynamic pages that work with the User model.  The main page will be an index of all the users in your database.  Clicking on one of the users will give you a detailed view of the user.
From the Add User button on the top left of the user index page you can go to a page that will allow you to add users to your database.  For a link to a basic version of the completed website go here:

http://powerful-forest-5694.herokuapp.com/

Anything that is meant to be run in your shell has a \$ infront of it while anything with a \> is meant to be run in your rails console.

**WARNING: DO NOT EDIT MIGRATION FILES, OR BAD THINGS MIGHT HAPPEN.  IF YOU DID AND YOUR DATABASE IS SCREWED UP, TYPE THE COMMAND**

    $ rake db:drop

**Resources to Help You Develop:**

Gems:

In the Gemfile, we've included 2 gems:

1. Better Errors: https://github.com/charliesome/better_errors
2. Binding of Caller: https://github.com/banister/binding_of_caller
This will make debugging your Rails applications much easier; include them in your future rails projects for easier development!

Migrations:

Last class, we found that many students had trouble with migrations, so we are including this resource for more information about migrations. This resource is relatively short and will give you a full understanding on what migrations are and how migrations work in rails. Refer to this resource for help on writing migrations!

http://guides.rubyonrails.org/migrations.html

**What To Do:**

Let's say one day we had a genius idea of making a website that would be a database of users that people can use for data mining, cloud, big data or whatever buzzword is in nowadays.  We would first make a user index page. That is, a page that lists all the users.

**Your User Index Page:**

  The user index method in the controller will be the root of this app.  We already created the user controller and the view so don't worry about that yet. In your routes.rb file add:

```ruby
  root 'users#index'
```

  Now if we start up our application we get a 'NameError in UsersController#index'.  If we look at the code snippet that is in the Error we realize that when we are calling User.all we are calling something that doesn't exist (our User model).
  The whole point of the app is to have users so having a user model is important.  We also want to have fields for our user that people would find valuable, like name and age. To generate a User model in our rails app and
  a migration to add a users table in our database we run:

    $ rails generate model User name:string years_old:integer

  This rails generate command creates a user model so that our rails application can interact with the user data and a migration file.  A migration file is a script that gives the database commands such as creating tables, adding columns, and removing
  columns.  Important: we should NEVER edit an old migration file directly.  We should ONLY add migrations.  If we don't, we'll end up with inconsistencies between the database and our app. The rails generate model tells rails that we are adding a model called User and that it has two fields, a name which is a string, and years_old which is an integer.  To run our migration script we type in the command:

    rake db:migrate

  This will tell rails the read the migration files and run their commands.  Now refresh your root page and it should now be working.  The page is pretty empty because there are no users in the database, which is pretty boring.  To add users into our database
  we can open our rails console (think about it as running our app without views) by typing:

    $ rails console

  After the console is open type in the command in the console:

    > u = User.new

  This creates a new empty user object and stores it in the variable u.  To set its name and years old type in the commands:

    > u.name = "John Doe"
    > u.years_old = "15"

  Now our user exists with the name "John Doe" and the years_old 15 but isn't saved into our database, it only exists in our ruby environment.  To save an object into the database we can call:

    > u.save

  This will store the user stored in the variable u into the database.  To query for all the users in the database enter:

    > User.all

  After saving the user refresh the page and now your index should be populated with the user John Doe!  To get a basic understanding of how this is working you can observe the index function in the UsersController (app/controller/users_controller.rb)
  and look at the index view (app/views/users/index.html.erb)

**Flexing your routing and front-end skillz:**
  If you look at the index page, at the bottom there is an about link.  Can you figure out how to make that link route to an about page?  We defined a static pages controller, but it is empty.  Add a function to the static pages controller, route the GET request
  for '/about', and create the view for the about page.  If you get stuck reference lab1 and lecture 2 about Static Pages and routing.

**Displaying values on the Show User Page:**

  If we look at routes file we see a get request for 'users/:id' that will link to the UsersController's show function (app/controllers/users_controller).  The :id just means the value that comes after the / will be put into a dictionary (hashMap if you have
  taken 61B) called ```params``` with the key being :id and the value being whatever comes after the /.  We won't worry about how we are passing the id's when we click on users in our index page for now.  Now let's take a look at the UsersController.  In the show
  function we call User.find(params[:id]).  What User.find(num) does is it queries the User table for a user with id num.  You can open up the rails console and try this yourself.  If we look at the show view
  (app/views/users/show.html.erb) we are trying to access a variable called @user.  We should go in the controller and tie the variable @user to the output of User.find so our view has access to the user's content.  Our show method should now look like:

```ruby
def show
  @user = User.find(params[:id])
end
```


  After doing that click on a user in the index to see the show view.

**Adding the new function to the controller:**
  On the top left of the index page the AddUser button links to a '/user/new', however there is no route for that (our rails application does not know how to process that GET request) and there is no new command in our controller, but there is a new view.  If
  we look at our new view we can see that it needs a variable called @user which is a object of model User.  That means in our UsersController (app/controllers/users_controller.rb) we should define a function called new and create an empty user called @user
  for our view to use:

```ruby
  def new
    @user = User.new
  end
```

  After we do that and save we should route our new user page as:

    get '/user/new', to: 'CONTROLLER#FUNCTION'

  Try to figure out what to replace CONTROLLER and FUNCTION with (Look at how routed the show and the about page).  After we finish the routing we should click the Add User button on the top left to check to see if we get to the new page.  If that works
  try adding users by inputting names, clicking create, and clicking on home to check.

**Adding years_old to form input:**

  So right now the only field you can add to your user when you create one is the user's name.  Our users have a column called years_old that we want to also keep.  To do this we want to add a form input to the new users view.  To do this simply copy the
  form input for the name and paste it next to it.  Instead of ':name' though we want the form input to match up to ':years_old'.  This value tells the form input what it should match up to.  After we copy the form input try and add a new user with a years_old
  value.  Go to the show view of the user and check if it has an age.  If you look, the age doesn't appear there. Why? Rails uses strong parameters, which means we enforce which attributes can be changed by a form.
  This provides security in case someone wants to add or edit information they don't have access to.  If you open up the UsersController (app/controllers/users_controller.rb) and look at the create function you will notice that it runs the create
  function (User.create(...) is equivalent to calling User.new(...).save) with user_params as an argument.  If we go to the bottom of the file user_params is a method that returns the params with only the :name attribute to be passed on. If we add a the years_old hash so
  it reads:

```ruby

def user_params
  params.require(:user).permit(:name, :years_old)
end

```

  We tell rails we are going to allow years_old to be passed along.  Save and try now to add a user through the new view and the years_old value should now save.

**Removing years_old and adding Age:**

Currently in our user table to keep the age of our user we have a column years old.  Let's say we determine that we are lazy and years_old is a terrible name for a column and we want to name the column age instead.  We have not put our rails app online and
since we only have test data it is cool to delete the years_old column and add the age column.  TO REMOVE A COLUMN WE DO NOT WANT TO EDIT OUR MIGRATION FILES.  We should rather run another migration that will tell our database to delete the column.  Since
rails is nice and has all these magical powers we can easily generate a migration by typing the command:

    $ rails generate migration RemoveCOLUMNFromTABLE COLUMNNAME:DATATYPE

Where we enter the correct values for the words that are all caps.  This means for our purpose we want to do this command:

    $ rails generate migration RemoveYearsOldFromUsers years_old:integer

Now that the column is gone we want to add our new age column.  To create a migration to add a column to a table we use the command:

    $ rails generate migration AddCOLUMNToTABLE COLUMNNAME:DATATYPE

where we replace all the uppercase words to the correct value.  This means we should type the command:

    $ rails generate migration AddAgeToUsers age:integer

After creating both migrations we should run them to update our database with:

    $ rake db:migrate

Now that our Users now have these fields we have to update our show view, the new view, and the user_params to use the age column rather than the years old column.  After we do that we should test our app to see if any future users we add saves the age and
correctly displays it.

**Adding a major column to the user:**

First we want to add a column to the user by doing a migration.  Create a migration that will add the column major to the user table.  After that we want to display it in the show view of the user using embedded ruby.  Now try opening up the rails console
and update the users column field manually.  Use the show view to verify that the major is showing.  Finally we want to add the field major to the form field in the new users page and make sure to add the major field as a permitted field in the user_params
in the UsersController.

**AND CONGRATULATIONS YOU ARE DONE!**
