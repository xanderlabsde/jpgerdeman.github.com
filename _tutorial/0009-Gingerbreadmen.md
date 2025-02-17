Prepatory Change
================

Add a new action `adduser`. All places should be in place. All that is left to do is to write the code to inserts the user. That should be easy enough and is left for the reader.

There is one other thing that we'll change. Change your `lib\Database.php` to look the following

    <?php
    class Database
    {
        static public function retrieveUser( $name )
        {
            $link = mysqli_connect($server, $username, $password);
            $stmt = 'SELECT * from user WHERE uname = '.$name;
            $result = mysqli_query($link, $query);
            $user = mysqli_fetch_object($result);
            return $user;
        }
    }

Don't forget to add your `insertUser` function. This change is cosmetical in nature and absolutely unnecassary. You could even rightfully argue, that is not clean code. I'm building up to our firstfundemental change.

Our code thus far was procedural, with this change we'll start to change it to the object oriented paradigm. Actually it is the second shift in paradigms this code has undervent; the first being the introduction of functions. That first shift made us go from pure imperative coding, to using the procedural paradigm. Imperative programming means, that every line explicitly states a necessary step to achieve our goals. 

Procdural programming is still imperative, but it introduces reusable chunks of code, that improve maintenance and makes the code more expressive. Procedural programming also introduces the notion of scopes. A scope is the "living quarters" of a variable. Every variable trying to leave it's scope dies. This makes it possible to reuse variable names without fear of overwriting a previously defined variable.

    function foo($a)
    {
        echo $a; // 3
        $b = 4;
        echo $b; // 4
    }

    $a = 3;
    foo( $a );
    echo $b; // throws an error because $b is not defined

## Gingerbreadmen
In procedural programming we have variables, that define state and we have functions, that execute logic. The separation is strict. In object oriented programming we define structures, that define state and come with their own logic.

Object oriented programming can be done in pure procedural languages, however it is far easier to do in object oriented languages.

To introduce object oriented programming (OOP) we'll start with a story.

My wife loves baking and my niece loves eating gingerbreadmen. My wife has a cookie cutter for gingerbreadmen. All gingerbreadmen look the same, but differ in their decoration. Some of raisin eyes, others have made ties and skirts from frosting and others still have gumdrops as buttons. No matter what the decoration is, the gingerbreadmen all look the same. They have two arms, two legs and a head.

My niece loves eating gingerbread men. She'll laugh because of the decoration then proceed to bite of the right leg then the left arm then right arm, the left leg and finally the head. "Because he wants to watch" she would say. 

When my wife tried switching to hearts or houses my niece would refuse to eat the gingerbread. She would bawl and claim "But their is no one to watch".

OOP is alot like that. You define a class (your cookie cutter) and define the methods on that class (the limbs). When you instantiate an instance (cut the gingerbreadman from the dough) it will look like all other instances from that class, except for it's state (the gingerbreadmans' decoration).

Let's look at a small example

    <?php
    class Pet
    {
        // This function is called when a new instance is created
        public function __construct( $animal )
        {
            $this->animal = $animal;
        }

        public function eat()
        {
            echo $this->animal." is eating";
        }
    }

    $a = new Pet('dog'); //This creates a new instance and calls __construct
    $b = new Pet('cat');
    
    $a->eat(); //dog is eating
    $b->eat(); //cat is eating
    eat(); // undefined function Error

So `$a` and `$b` are two instances from the same class, each has its own state, but have the same set of functions.

## Plato's Gingerbreadmen
In the above code samples we've used keywords, that have not been explained up till now.

`public`, `protected` and `private` are access modifiers. They indicate who may call the function, i.e. from where you are allowed to call this function. A `public` function may be called from anywhere, a `private` one may only be called from within the class or the same instance and `protected` means that derived classes - which we haven't met yet - may also access the function.

The same modifiers apply to the properties of a class. A property is a variable, that belongs to the class or instace. In our Pet example `$this->animal` refers to such a property. If possible, it is always a better idea to define the properties at the top of your class. This makes it easier for readers to know what to expect.


    <?php
    class Pet
    {
        protected $animal;
        
        public function __construct( $animal )
        {
            $this->animal = $animal;
        }

    ...

The `static` keyword used in the first example indicates, that the method does not operate on the state of an instance. No instance property is changed or evaluated in the method. It reminds me of Plato's Allegory of the Cave. A static Method or property is a call to that perfect entity, from which all instances are imperfect copies of.

Since a static method is a call to the class rather than an instance, you don't need an instance to call it at all.

    <?php
    Database::retrieveUser('Leeloo');

See! Without creating an instance we were able to call the static method. 

In our pet example `$this` refers to the instance. Using `$this` in a static method will result in an error. Because a static method refers to the class not an instance. To reference the class we have to use `self`.

    class Pet
    {
        static public $allDead = false;
        
        protected $animal;
        
        public function __construct( $animal )
        {
            $this->animal = $animal;
        }
        
        static public function areAllDead()
        {
            return self::$allDead;
        }
        
        public function eat()
        {
            if( self::areAllDead() )
            {
                echo $this->animal." is dead";
            }
            else
            {
                echo $this->animal." is eating";
            }
        }
    }

    $a = new Pet('dog'); //This creates a new instance and calls __construct
    $b = new Pet('cat');
    
    $a->eat(); //dog is eating
    $b->eat(); //cat is eating
    Pet::$allDead = true;
    $a->eat(); //dog is dead
    $b->eat(); //cat is dead

This seems like a good place to stop for now. This is a very brief introduction to object orientation. We'll talk more about this paradigm later. For now I suggest you reread this text and make sure you have understood things, especially the difference between class and instance, this far.
