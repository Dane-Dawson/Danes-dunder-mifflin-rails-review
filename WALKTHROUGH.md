I have created a (more or less) step by step walkthrough of each part of the Dunder Mifflin lab. This is not all inclusive for every single thing I typed, but I tried to be more thorough on describing all the steps needed. If you can finish *this* lab, you have all the tools you need for your code challenge! 

From the beginning!

vVvVvV Inital setup, checking routes and relationships VvVvVv

Let's get rails installed by running
```
bundle install
```
After reading the README (at least twice) we should know what the lab is asking of us. We'll start by setting up the relationships.

Check relationships in the model => employee belongs to dog, dog has_many employees

Look at models, add associations accordingly
Now lets make sure our tables represent the data relationships we want.
Check schema, modify tables (who belongs to who? Where should any foreign key be stored?)

Read through seeds, verify data and associations work out (a person belongs to a dog, need to make dogs first). If an employee belongs to a dog, it requires a dog_id attribute...check if the schema has one! If the data is all set up with attributes and relationships, we can move on to the next step!

Let's get our DB initialized and seeded by running
```
rails db:migrate
rails db:seed
```
:::Quick DB check:::
rails c => check out models and associations
Once we check all the associations are connection, we know the DB (models, associations, and attributes connecting them) is set up correctly. We can do this here by checking if any instance of Dog has employees, and if any any of Employee has a dog. For example
```
Dog.first.employees
#should return an array of employee objects
Employee.first.dog
#should return a single instance of a Dog object.
```
Last thing we'll need to do is check routes. Look at the routes folder to see what you have to work with, and feel free to type rails routes in the terminal to get your custom paths listed out.

Let's boot the server up and keep it going for the rest of the lab!
```
rails s
```


vVvVvV Let's get to the MVC magic! VvVvVv

First place we will start is on controllers

Pick one controller to start with, we'll do DogsController first for no particular reason.

My pattern to write code out for Rails is -> create action, create view, test hardcode view for routing, test data availability in the view, fill in data/functionality, then make it pretty.

!! Create Action !!
For this demo in the DogsController we will start with index, it'll be nice to be able to see all our dogs on one page. Create an index method.

After the index action is defined, let's create the view that is connected to it!

!! Create View !!
DogsController#Index->view/dogs/index
Remember that for Rails you have to create a views folder for the controller, named the same thing the controller is (in this case, dogs). Make sure it is directly under the views folder, not nested inside layout by accident!

create a file inside the dogs folder matching the name of the action you are calling with your controller plus the appropriate file tags. In this case, index.html.erb

!! Test View with hardcode !!
Add a simple header to the index page and visit it in the browser to make sure the connection is solid. I usually just do a simple "This is the {name_of_viewpage} page!" to test.

Then add in the data passed through the action.

!! Test data availability in view !!
As a quick test I just try to grab a single attribute from the object. Our dog has a name, so I just throw a 
```
<%= @dogs% >
```
and refresh to make sure I have some objects showing up on the screen! 

!! Fill in data/functionality !!
Our index page is going to have a list of dogs that, according to the README, when clicked should take us to a show page for that dog. To start with let's just make it a list of dogs.

```
<ul>
<% @dogs.each do |dog| %>
    <li> <%= dog.name %> </li>
<% end %>
</ul>
```

Now that we know we have the collection of objects present on our index, we need to create a show action to see a single model. Before we can do any links, we need to make sure the page and path exists in the first place!


My process is still:-> Action, view, test base page connection, check data in view, add all data/functionality, clean up/make pretty. I'm going to skip the detailed steps from above for the show action.


Return to DogsController, add in a show action, then create a show view page in the views/dogs folder. Hard code and test the view, then attempt to use data passed in. Once you have a show page, check your Dog model and get all the attributes visible on the page. Feel free to add text/minor styling as you'd like.

Now let's connect the index page to the show page with a link_to on each dog in the index list. 

views/dogs/index
```
<ul>
<% @dogs.each do |dog| %>
    <li> <%= link_to dog.name dog_path(dog)%> </li>
<% end %>
</ul>
```
I like to add a "return to main page" on each show page as well. Given text is purely an example.

views/dogs/show
```
<%= link_to "Click here to see all our dogs!" dogs_path%>
```

Go ahead and do all these steps for the EmployeesController as well, but pay attention to what information is wanted in the README.

vVvVvV Using forms to create and update models VvVvVv

The README tells us that the goal is to "be able to create AND edit an Employee, and only be able to select 1 dog from a list of already existing dogs."

We need to make sure we have a routes for these paths, I used resources so it's covered!

We will start with creating a new Employee. Let's follow the same flow as before and create the new action in our Employee controller and it's view. As always, I test the controller with a hardcoded header before I attempt any data interaction. My view/employees/new.html.erb just says "New Employee form" to start.

Now we get to make our form! I prefer to use the form_for tag for no real reason other than I am used to it and like it. I referenced the schema to see what attributes were needed to create an employee model and created the following form:

```
<%= form_for(@employee) do |f| %>
  <label>Employee first name:</label><br>
  <%= f.text_field :first_name %><br>

  <label>Employee last name:</label><br>
  <%= f.text_field :last_name %><br>

  <label>Employee alias:</label><br>
  <%= f.text_field :alias %><br>

  <label>Employee office:</label><br>
  <%= f.text_field :office %><br>

  <label>Employee title:</label><br>
  <%= f.text_field :title %><br>

  <label>Employee photo url:</label><br>
  <%= f.text_field :img_url %><br>

<label>Which dog does this employee belong to? </label>
<%= f.collection_select :dog_id, Dog.all, :id, :name %><br>
  <%= f.submit %>
<% end %>
```
I used another example of label for this form. This is (for our purposes) interchangable with the other syntax we have seen more commonly.

For a refresher on the collection_select, see below vVv

```
<%# How to use collection_select:
collection_select takes in (up to) 5 arguements.
collection_select(arg1, arg2, arg3, arg4, arg5)
arg1 = The object you are working with. If you are within a form (which we usually will be) this can be omitted
arg2 = What you are naming the saved value as within the params you get with form submission
arg3 = An array of objects that you are wanting to make your selection from 
arg4 = What value within each arg3 object you are saving to params by making your selection
arg5 = What the user sees on the drop down selector as a visible option
in practice
Because we are in a form for @playdate, we can omit the first arguement %>
<%= form_for @playdate do |f| %>
                        <%# arg2       arg3       arg4   arg5 %>
<%= f.collection_select :person_id, Person.all, :id, :name %>
                <%# 2-Value sent||3-collection||4-value captured||5-display value %>
<%= f.collection_select :kitten_id, Kitten.all, :id, :name %>
<% end %>
```

Now that we have an action that gets us connected to a form we can fill out with information, let's add the create action to actually get this data to persist in our DB. 

In the EmployeesController we will start with setting up our strong params.

Quick refresher for the basic syntax of this method
~~~
params.require(arg1).permit(arg2, arg3, etc)
~~~
arg1 = The name of the key/object within the params that your information is packaged. In our case, it matches what we make our form_for target.
<br>
arg2-infin = The name(s) of the allowed keys your params will ultimately hold. This needs to include *any* value you need when calling these params. For us, it's every attribute for an Employee (which we can find again on the schema!)

```
def employee_params
    params.require(:employee).permit(:first_name, :last_name, :alias, :office, :title, :img_url, :dog_id)
end
```

If we test the create employee path now (and everything is named correctly) we should get an error that tells us there is no create method currently, so let's do that! Just write a create action in your Employees controller using our strong params. I also add a redirect so that after making a new employee we can see them on the index page.
 
EmployeesController
```
 def create
        @employee = Employee.create(employee_params)

        redirect_to employees_path
    end
```

Last thing we need is an edit form, so we make our edit view and mimic our edit/update actions similarly to our new/create actions with a few modifications. For Update we will need to find our Employee by ID again, and instead of doing a .create we do a .update!

EmployeesController
```
def edit
    @employee = Employee.find(params[:id])
end

def update
    @employee = Employee.find(params[:id])
    @employee = Employee.update(employee_params)

    redirect_to employee_path(@employee)
end
```

For now we will copy the form for create exactly and place it into an edit view with a new header describing it. I added a link_to the edit path on the show page for each employee.

Just to fully round it out, I'll add in a delete form to remove employees. First the action:
```
def destroy
    @employee = Employee.find(params[:id])
    @employee.destroy
    redirect_to employees_path
end
```

and then I added this onto the show page for each employee.
```
Has this employee been causing trouble? To delete them <br>
<%= form_for @employee, method: :delete do |f| %>
<%= f.submit "Click Here"%>
<%end%>
```
The last thing we will do is add in Dwight's validation concerns.
Add the validation check inside the model that you are checking (ours is the Employee)
Employee model
```
    validates :alias, uniqueness: true
    validates :title, uniqueness: true
```

This makes sure that we can't create a model *unless* it's title and alias are unique, but we need to write something in our code to catch if a model is invalid! We will add the following lines to our EmployeesController create method (which is where we would do the validation check)

EmployeesController
```
def create
    @employee = Employee.create(employee_params)
    if @employee.valid?
    redirect_to employees_path
    else 
    render new_employee_path
    end
end
```
And I added this conditional render for displaying the error messages. Fancy! When it does the redirect to new_employee_path on the else, it carries with it the @employee with the error messages that prevented it from saving.

views/employees/new
```
<% if @employee.errors.any? %>
  <div id="error_explanation">
    <h3><%= pluralize(@employee.errors.count, "error") %> prohibited this employee from being created:</h3>
    <ul>
      <% @employee.errors.full_messages.each do |message| %>
        <li><%= message %></li>
      <% end %>
    </ul>
    <p>Please adjust your new employee file accordingly and try again! </p>
  </div>
<% end %>
```
This checks if the employee being sent in has errors (which should only happen if you try to create a new employee and it fails), and, if there are errors, it gives a count of the number of issues and says what they are.

There we go! That should be all the deliverables!

This is not the fanciest way to do things, but it is one of the easiest ways to start! Included is fully functional code that I took my examples out of, some with minor tweaking for styling/being witty but the core functionality holds true. I also added some links between pages to let it flow a bit more naturally.