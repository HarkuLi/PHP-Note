## Case Sensitivity

* All **keywords**, **classes**, **functions**, and **user-defined functions** are ***NOT*** case-sensitive.
* All **variable** names are, however, case-sensitive.

## Scope

### Local:

* A variable declared within a function can only be accessed within that function.
* e.g.

        <?php
        function foo() {
            $x = 5; // local scope
            echo $x;
        }
        foo();
		
        // x can't be accessed outside the function
        echo $x;
        ?>

### Global:

* A variable declared **outside** a function has a **global scope** and can ***ONLY*** be accessed **outside** a function.
* e.g.

        <?php
        $x = 5; // global scope

        function foo() {
            // x can't be accessed inside the function
            echo $x;
        }
		
        echo $x;
        ?>

### Static:

* The value of a local variable will be retained if using the **static** keyword.
* e.g.
	
        <?php
        function foo() {
            static $x = 0;
            ++$x;
        }
		
        foo(); // x = 1
        foo(); // x = 2
        ?>

## Data Types

* The PHP `var_dump()` function returns the data type and value.

## Constants

* Constants are **automatically global** and can be used ***across the entire script***.

## Operators

### Comparison Operators

* `<>` and `!=` both represent weak-typed inequality in PHP. However, `<>` doesn't have the same visual harmony with the strong-typed inequality operator, `!==`, so **always** use `!=` for weak-typed inequality.
* <font color=red>Don't Trust PHP Floating Point Numbers When Equating.</font>
    * Because floating point numbers are actually being stored in the binary base 2, and it causes the loss of precision.
    * e.g.

            $sum = 0.1 + 0.2;
            echo $sum; // 0.3
            echo ($sum === 0.3) ? "true" : "false"; // false
			
    * Solutions:
        1. Test against the smallest acceptable difference.
		
                $sum = 0.1 + 0.2;
                echo (abs($sum - 0.3) < 0.1) ? "true" : "false"; // true
			
        2. Use PHP’s BC Math functions for handling the addition.
		
                // the third parameter specifies the precision after the floating point
                $sum = bcadd(0.1, 0.2, 1);
                echo $sum; // 0.3
                echo ($sum === 0.3) ? "true" : "false"; // true
    * [Reference](https://andy-carter.com/blog/don-t-trust-php-floating-point-numbers-when-equating)

### Logical Operators

* The difference between operators `and` and `&&` (`or` and `||`) is their **precedence**. See [here](http://php.net/manual/en/language.operators.precedence.php).

## Functions

* By default, function arguments are <font color=red>passed by value</font>.
* Use an ampersand (**&**) for passed by reference.
    * e.g.

            <?php
            function cat_some_str(&$input_str) {
                $input_str .= "cat.";
            }

            $str = "This is a ";
            cat_come_str($str);
            echo $str; // outputs "This is a cat."
            ?>

## Type declarations

* Type declarations were also known as **type hints in PHP 5**.
* ***Aliases for the scalar types are not supported***.
    * alias table

        |  type  |    alias   |
        | :----: | :--------: |
        | bool   | boolean    |
        | int    | integer    |
	  
    * e.g.

            <?php
            function test(boolean $param) {}
                test(true); // fatal error
            ?>
