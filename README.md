# Ruby Drill: Exploring Scope 
 
## Summary
When we write our applications, we create objects: strings, arrays, hashes, custom objects, etc.  After creating an object, we often we want to ues it later in our program.  So, we define a variable and assign our object to the variable.

We have different options when defining variables: global variables, constants, local variables, instance variables, and class variables.  Our choice of variable type will affect when and how we're able to access a variable.

The question we'll explore in this challenge is "Where are our variables visible?".  In more technical language, we might ask, "What is the *scope* of a variable?".  We'll explore and refine our understanding of scope in Ruby.


### Changing Scope
![scope change visualization](readme-assets/scope_sequence.gif)

*Figure 1*. Visual model of changing scope as code executes.

Our programs begin executing in a top-level scope.  Some code triggers a change in scope.  For example, using the keyword `class` when defining a class changes scope, and the code defining the class is executed within this new scope.  Then, when the class definition is complete, our program returns to the previous scope.  Or, when we call a method, the method is executed in a new scope.  When the method is finished executing, our program again returns to the previous scope and continues.  This process is modeled in Figure 1.

When scope changes, we lose access to some variables, as we'll see.


*Note:*   The section [*Scope of Constants and Variables*][pickaxe guide scope] from *Programming Ruby: The Pragmatic Programmer's Guide* can serve as an additional reference on the scope of variables.


## Releases
### Release 0: Global Variables
Global variables are the easiest to understand when it comes to scope.  That is because they have visibility everywhere.  In other words, scope changes have no effect on whether or not a global variable is accessible.  We can access them and reassign their values anywhere in our code.

We're going to demonstrate working with global variables with the provided `TipCalculator` module.  Look at the code for the module (see `tip_calculator.rb`). We can see that the module has a method that returns the value of a global variable `$global_tip_percentage`.  It also has a method that reassigns the value of that global variable.  Before we begin working with the code, note that before a global variable's value has been assigned, its value is `nil`.

Open IRB and ... 

1. Check the value of `$global_tip_percentage`.  It should return `nil`, since we haven't assigned it a value.

2. Assign a value to our global variable:  `$global_tip_percentage = 0.15`.

3. `load 'tip_calculator.rb'`.  When the module definition is executed, the value of `$global_tip_percentage` is reassigned to be `0.2`.

4. Check the value of `$global_tip_percentage`.  It should have been changed to `0.2`.

5. Ask the `TipCalculator` for the customary percentage.  It should return `0.2`, the value of `$global_tip_percentage`.

6. Assign a new value to our global variable:  `$global_tip_percentage = 0.25`.

7. Ask the `TipCalculator` for the customary percentage again.  It should return the value we just assigned to the global variable.

8. Ask the `TipCalculator` to set the customary percentage:  `TipCalculator.customary_percentage = 0.3`, which will again reassign the value of our global variable.

6. Check the value of `$global_tip_percentage`.  It should have changed to the value we just supplied.

7. Exit IRB.


To reiterate, global variables are always accessible.  We've assigned and accessed the value of a global variable in the top-level scope, while a module was being defined, and from within methods.  No scope change had an effect on our ability to access the global variable.

As we might imagine, relying on global variables whose values are subject to being changed at any time by any object can cause unexpected results.  Therefore, it's best practice not to utilize global variables, if it can be avoided.


### Release 1: Constants
Constants are variables whose values are meant to be ... constant.  In other words, we don't reassign their values.  Conventionally, constants are written in all caps with words separated by underscores (e.g., `MINIMUM_HEIGHT`).  However, any variable that just begins with a capital letter is considered by Ruby to be a constant.  For example, classes and modules are considered constants as well, though we use different conventions for their names (e.g., `Person` or `HeightValidator`).

As with global variables, constants are always accessible in our programs.  However, depending on the scope in which the constant has been defined, it might not be as straight forward to access as a global variable.

We're going to demonstrate working with global variables with the provided `RideHeightCalculator` module.  Look at the code for the module (see `ride_height_calculator.rb`).  In the code, we begin by defining a constant, `PARK_NAME`.  We then define a module and within the module define a series of constants (e.g., `ROLLER_COASTER_MINIMUM_HEIGHT`).

Open IRB and ...

1. Check the value of `PARK_NAME`.  Unlike constants which, when unassigned, default to `nil`, trying to access an uninitialized constant will raise an error.

2. `load 'ride_height_checker.rb'`

3. Check the value of `PARK_NAME`.  Now that it's been declared and assigned, we can get its value.

4. Check the value of `ROLLER_COASTER_MINIMUM_HEIGHT`.  Again we get an error.  Because this constant was declared within a module, we can't access it directly.

5. Check the value of `RideHeightChecker::ROLLER_COASTER_MINIMUM_HEIGHT`.  Because we defined this constant within the `RideHeightChecker`, we can go through that module to access it, making use of the namespace operator, `::`.

6. Exit IRB.

7. Look at the code for the private method `.minimum_height_for_type`.  The constants are being accessed there without referencing the name of the module.  That's possible from within the module definition itself—the same would be true for a class definition.  But, as we saw, from outside the module definition, the constants are only accessible by using the module as a namespace (e.g., RideHeightChecker::ROLLER_COASTER_MINIMUM_HEIGHT).


*Note:*  Technically, the value of a constant can be changed, but doing so would violate convention, and Ruby will warn us.


### Release 2: Local Variables
In contrast to global variables and constants which are accessible throughout our programs, local variables are accessible only within the scope in which they are defined.  Any time scope changes, we're working with a new set of local variables; because of this, it's helpful to understand when changes in scope are triggered.

We'll explore the visibility of local variables by writing some code in IRB.  So, open IRB and ...

1.  Check the value of `self`.  It should be `main`, indicating that we're working in the top-level scope.

2.  Define a local variable in the top-level scope:  `status = :curious`.

3.  Access the value of the local variable:  `status`.  We haven't changed scope, so we have access to our top-level local variable.

4.  Write a method to return the value of our variable:

  ```ruby
  def current_status
    return status
  end
  ```

5.  Run the method:  `current_status`.  What does the error message tell us?  There is no local variable `status`.  Why not?  Because we've changed scope and lost access to the local variables in the top-level scope.

6.  Overwrite the `current_status` method so that it defines its own `status` local variable:

  ```ruby
  def current_status
    status = :intrigued
    return status
  end
  ```

7.  Run the method:  `current_status`.  We see that it returns `:intrigued`, the value of the local variable `status` within the scope of the method.

8.  Has this affected the value of the top-level local variable `status`?  Access the value of `status`.  It's value has not been changed.  As we know it's out of scope when our method runs.

9. Overwrite the method again so that it has one parameter:

  ```ruby
  def current_status(status)
    return status
  end
  ```

10.  Run the method:  `current_status(:inquisitive)`.  It returns the argument, `:inquisitive`.  If we define a method with parameters—in this case one parameter named `status`—the parameters become local variables when the method runs. When we then call the method, the arguments we pass to it are assigned to local variables—in this case, `status`.

11.  Given that method parameters act as local variables when the method runs, would having a parameter named `status` interfere with our top-level variable `status`?  Access the value of `status`.  Was it affected?


This is by no means an exhaustive exploration of local variables, but it should provide a foundation for understanding how changes in scope affect the visibility of local variables.  The key take away is that anytime scope changes, there is also a change in the set of local variables which are visible.


### Release 3: Instance Variable
We've worked with instance variables in previous challenges.  Instance variables are named beginning with `@`.  For example, `@name`, `@date_of_birth`, etc.  We use them to hold state for individual objects.  If we have a class `Employee`, we would use instance variables to hold the name of each unique instance of `Employee`.

The visibility of instance variables is very limited.  They are only visible to the object to which they belong.  As we remember from the [accessor methods challenge], if we want an object to share the values of its instance variables, we create accessor methods to expose them.

We're going to explore a little more about instance variables.  We'll be working with the provided `Dog` class.  Take a moment to review the file `dog.rb`.  Notice that when a new instance of the class `Dog` is created, three arguments are expect and the each is assigned to an instance variable.  Then, each of the instance variables is exposed through a long-hand accessor method.

Open IRB and ...

1.  `load 'dog.rb'`.

2.  Create a new instance of the class `Dog`:  

  ```ruby
  jayda = Dog.new('Jayda', 'English Bull Terrier', 'OH-123456')
  ```

3.  Ask the new dog for its name:  `jayda.name`.  And, the object returns the value of its `@name` instance variable.  

  In the body of the method `Dog#name`, we make no reference to any particular object, we just ask for `@name`.  Presumably, there could be any number of objects with the instance variable `@name`.  How is it determined which object's instance variable to return?
  
4.  Check the value of `self`.  It should be `main`.

5.  Check the value of `@name`.  What is it?  It should be `nil`.  Just like global variables, instance variables which have not been assigned a value return `nil`.

6.  Assign `@name` a value:  `@name = 'Sam'`.

7.  Check the value of `@name`.  It should return `'Sam'`.  We know that instance variables are internal to an object.  To which object does this particular `@name` instance variable belong?

8.  Check our dog's name to see if it's changed:  `jayda.name`.  It should not have been altered.

9.  Define a method directly on our top-level object (i.e., `main`) to return its instance variable `@name`:

  ```ruby
  def self.name
    @name
  end
  ```

10.  Call the method on `self`:  `self.name`.  What is returned?  `'Sam'`.  When were were accessing and assigning instance variables in the top-level scope, we were dealing with the instance variables of `self`.

  And that's always the case.  When we access or assign an instance variable, we do so from or to whichever object `self` refers.  Just as `self` is the default receiver of method calls, it is also the object we deal with when working with instance variables.






#### Classes and instance variables

Add a class `BasicClass` to your program.  Duplicate your `get_local_var` method within the class.

Create a new instance of `BasicClass` and call the `get_local_var` method on it.  What happens?

Now add an instance variable `@instance_var` to your class and define an instance method `get_instance_var` for your class that returns the value of `@instance_var`.

Try creating a new instance of the class and calling `get_instance_var` on it.  What happens?

#### Getters and setters

Your instance method `get_instance_var` is an example of a *getter method*.   It allows us *read access* to an instance variable of an object.

A *setter method* is similar, except that instead of reading an instance variable, it allows us to *write* a new value to the instance variable.

Create a setter method for `BasicClass` named `set_instance_var=` that takes one argument and then assigns the value of the argument to the instance variable `@instance_var`.

Note that a commonly used pattern for setter methods is to name them with a `=` at the end.  This is simply syntactic sugar that allows us to write code like this:

```ruby
foo = BasicClass.new
foo.get_instance_var
# => 'default value'
foo.set_instance_var = 'some other value'
```

The last line is the same as writing `foo.set_instance_var=('some other value')`, it's just easier to read.  Thank you, Ruby.

#### Class methods

A *class method* is a method on a class, as opposed to a method on an instance of a class.  It works like this:

```ruby
class MyCoolClass
  def self.some_method
    "quack"
  end
end

MyCoolClass.some_method # => "quack"
```

What are some class methods you've seen?  `MyCoolClass.new` is one class method that every class in Ruby comes with, which returns a new instance of a class.

#### Class variables

An *instance variable*, as the name implies, is associated with an *instance* of a class.  In other words, its value can be different for different objects created by the same class.

Try creating two different objects from `BasicClass` and then using your setter method to give each of them a different value for the `@instance_var`.  Then use your getter method on each to see if they are *actually* different.

A *class variable*, on the other hand, is the same for all instances of a class.

Within your class definition, create the class variable `@@class_var` and assign it a value.

Then define a getter and setter method for `@@class_var` and play around with assigning new values to it from multiple instances of the class.  

What happens if you change the value of `@@class_var` from one instance, and then create a new instance of the class and use your getter method to read the value of `@@class_var`?



<!-- ##Optimize Your Learning  -->

##Resources

[accessor methods challenge]: ../../../ruby-drill-accessor-methods-challenge
[pickaxe guide scope]: http://ruby-doc.com/docs/ProgrammingRuby/html/language.html#UP
