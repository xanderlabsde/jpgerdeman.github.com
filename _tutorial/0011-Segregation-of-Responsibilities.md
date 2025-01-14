Segregation of Responsibilities
===============================

Looking at our `user\add.php` we see that it is very difficult to comprehend at a glance.

    <?php
    $state = getRequestParameter( 'state', 'displayForm' );

    switch($state)
    {
        case 'submit':
            Database::addUser($_POST);
            break;
        default:
    ?>
    <form method="post" action="index.php?action=useradd&state=submit">
        username: <input type="text" name="name"><br>
        name: <input type="text" name="name"><br>
        email: <input type="text" name="name"><br>
        <input type="submit" name="Button" value="Submit">
    </form>
    <?php
        break;
    }

It's not complicated, but there is definetly alot going on.

Let's see what this action does.

* decides what action to perform depending on state
* writes user to Database
* displays form

So this action is actually two actions to begin. It sends out a form and saves it. This should be split as they are two concerns.

This action also takes care of displaying HTML. We'll separate this in two parts. The first takes care of the logic, i.e. setting up variables to display. The second part is a pure display part.

Add a `templates` directory to the `user` directory and put the HTML for the form in a file `actions/user/templates/add.php`. Change the action to look like

    <?php
    $state = getRequestParameter( 'state', 'displayForm' );
    
    switch($state)
    {
        case 'submit':
            Database::addUser($_POST);
            break;
        default:
            include __DIR__.'/templates/add.php';
        break;
    }

So if we step back and look at our application now we see that the frontcontroller
determines which action to call which then takes over control. It calls the correct template
and hands that back to the database.

What we have here is a typical architectural design pattern. We sepearted
controlling logic (actions) from the part that is viewed (templates) and from
our model logic (Database). This is called the Model-View-Controller pattern,
or MVC for short. MVC is one of the most used patterns in frameworks.

Using well known design patterns makes it easier for other developers to
understand your code. It also makes it easier to talk about code.

Looking at our code we see that we have the following issues still:

- Database is going to get crowded and unwieldy
- ugly URLS
- user/add is ugly and unsafe
- We have no Layout

We'll work through these point for point

## Software Design Patterns

Patterns are abstract descriptions of reusable templates or solutions to recurring problems.
Since they are formalized it simplifies talking about design, as each pattern adds to the
developers vocabulary. Patterns are usually backed up by several implementations and experience
making them tested and proven solutions a developer can rely upon.

Some argue, that design patterns often add unnecessary abstraction to their code. Patterns do
more often than not add more abstraction, but usually because it makes the code easier to extend.
Sometimes a pattern already solves problems a developer hasn't even foreseen yet. Remember
patterns represent the experience of several implementations and of one or more developers.

However patterns are no magic bullet. If misused a pattern can have the opposite of the
expected effect. Use patterns where they fit a problem; don't fit the problem to a pattern.
