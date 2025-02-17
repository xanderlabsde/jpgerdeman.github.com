The First Cleanup
==
I'll assume your PHP file looks something like this right now.

	<?php
	$action = isset($_GET['action'])?$_GET['action']:'hello';
	if( $action === 'profile')
	{
		$uname = isset($_GET['uname'])?$_GET['uname']:'anonymous';

		$link = mysqli_connect($server, $username, $password);
		$stmt = 'SELECT * from user WHERE uname = "'.$uname.'"';
		$result = mysqli_query($link, $query);
		$user = mysqli_fetch_object($result);

		echo "Profile";
		printf('Name %s', $user->name);
		printf('UserName %s', $user->uname);
		printf('email %s', $user->email);
	}
	else
	{
		$uname = isset($_GET['uname'])?$_GET['uname']:'anonymous';

		$link = mysqli_connect($server, $username, $password);
		$stmt = 'SELECT * from user WHERE uname = "'.$uname.'"';
		$result = mysqli_query($link, $query);
		$user = mysqli_fetch_object($result);

		printf('Hello %s', $user->name);
	}


The more actions we add the larger the if statement is going to get. Using
switch you could save a few lines, but in the long run it's better to split 
the responsibilities into their own files.

We'll create an actions folder, where we'll put our actions. To keep it simple
we'll name the files according to their action. Copy the content of the `then` block
to a file `profile.php` in the actions folder and replace it by `require 
'actions\profile.php`. Similarly copy the content of `else` to `actions\hello.php` and replace it. 

Our index.php now controls the entry to our site, this is also commonly
called a frontcontroller. 

If you look at the code now, you'll see that the if statement is still there. In
fact if you compare the code of all files. We actually added two more lines, as
well as more files. Was that sensible?

Well by splitting the code, we make it easier to find and edit a certain piece of code.
You can edit a file without fearing to accidentally alter unrelated code. Look at the
if statement in our index.php. Isn't it more expressive now? Doesn't it state it purpose
with more elegance now?

To me opening a file always feels like diving in a pool. The more code in one file,
the more crammed it feels to me. Adding to such a file feels like stuffing to many
critters in too little space with too little oxygen. A small file - that is one with few lines of code - or an empty file feels to me like green grass, fresh air, life and opportunity. At least that is how I feel.

## switch vs if

A switch is a nicer way to write a series of if and else-if statements.

    switch( $action )
    {
        case 'profile':
            require 'action\profile.php';
            break;
        case 'hello':
        default:
            require 'action\hello.php';
            break;
    }

The variable `$action` is compared to the values behind the word case and if it matches it will execute all following lines until a `break` or the end of the switch is encountered. The above switch is identical to

    if( $action == 'profile' )
    {
        require 'action\profile.php';
    }
    elseif( $action == 'hello' )
    {
        require 'action\hello.php';
    }
    else
    {
        require 'action\hello.php';
    }

In previous versions of PHP, that is before PHP 5.2, it seems that if statements where faster than switch statements. In current versions switch has become faster. That is nice to know but ultimately you should not base a decision on this fact. Executing millions of switch and if statements might result in a gain of a hundredth of a second. Just too little to get worried about. 

Your code will be read more often than wrote, so you want to use the statement that expresses your intent the most clearly. I usually turn to a switch if I'm checking the value of a variable against a discrete set of values. Though possible I refrain from putting logic into the case of a switch statement. As we will see later on I will turn to another solution once I condensed the logic to such a simple switch statement.

## Require vs Include
PHP knows four commands to include other files. Each includes the code of the included file in the calling file at the exact point it was included at. So to PHP our Code still looks exactly the same.

    // foo.php
    echo $name;

    // bar.php
    $name = 'Joe';
    require_once 'foo.php';

    // foobar.php 
    $name = 'Joe';
    echo $name;

Calling bar will execute the same as foobar. Also foo can reference `$name` even though it is declared in bar. There is a slight difference in that each inclusion will force PHP to execute a costly disklookup. There are ways to stipulate the cost however.

PHP defines `include`, `include_once`, `require` and `require_once`. They all will include the given file, but with slight differences. `include` will not throw an error, when the referenced file does not exist - it is a soft inclusion. `require` is a hard inclusion, meaning it will throw an error if the to be included does not exist. The `*_once` versions of the commands will include a file only once. Subsequent inclusions will have no effect.

    <?php
        // foo.php
        echo 'foo.php';

        // bar.php
        include_once 'foo.php';
        include_once 'foo.php';

Will only echo `foo.php` once, since it will only include the file once. Whereas the following code will eho it out twice

    <?php
        // foo.php
        echo 'foo.php';

        // bar.php
        include 'foo.php';
        include 'foo.php';
