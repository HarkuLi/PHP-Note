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

### $this, self::, and static::

* $this
    * **$this-> will try to call private methods from the same scope.**
    * otherwise, $this indicates the current **Object**.
    * e.g.
        * code:
        
                <?php
                class A
                {
                    private function fromWhere()  //scope A
                    {
                        echo "in class " . __CLASS__ . "<br>";
                    }
                    
                    public function test()  //scope A
                    {
                        $this->fromWhere();
                        self::fromWhere();
                        static::fromWhere();
                    }
                }
                
                class B extends A
                {
                    public function fromWhere()  //scope B
                    {
                        echo "in class " . __CLASS__ . "<br>";
                    }
                }
                
                $b = new B();
                $b->test();  //call test() in scope A, and then private fromWhere() in scope A is called
                
        * result:
        
                in class A
                in class A
                in class B
        
        * if change fromWhere() in class A to public, the result would be:
        
                in class B
                in class A
                in class B
        
        * if remove fromWhere() in class A, the result would be:
        
                in class B
                Call to undefined method A::fromWhere()
                in class B
        
* self::
    * Indicate the current **Class**.
* static::
    * Indicate the **late override one**. (i.e. **Late Static Binding**)
* e.g.
    * code:
    
            <?php
            class A
            {
                public function fromWhere()
                {
                    echo "in class " . __CLASS__ . "<br>";
                }
            
                public function test()
                {
                    $this->fromWhere();
                    self::fromWhere();
                    static::fromWhere();
                }
            }
        
            class B extends A
            {
                public function fromWhere()
                {
                    echo "in class " . __CLASS__ . "<br>";
                }
            }
            
            $b = new B();
            $b->test();

    * result:

            in class B
            in class A
            in class B

## Data Types

* The PHP `var_dump()` function returns the data type and value.

### Type Casting

* **int** casting from **float** performs like **floor()**.
    * e.g.

            <?php
            $test = (int)1.4;
            echo $test."\n";
            $test = (int)1.5;
            echo $test."\n";

      It will print:
      
            1
            1

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
	    
## Include

* The include and require statements are **identical**, except upon **failure**.
    * require: produce a fatal error (E_COMPILE_ERROR) and stop the script
    * include: only produce a warning (E_WARNING) and the script will continue
* include_once / require_once can avoid repeating include.
    
## Quotation marks

* Single mark ''
    * The content inside it is common text.
    * \ only works for \' and \\
* Double mark ""
    * Variables inside it are converted to their values.
* e.g.

        <?php
        $w = "world!!";
        echo 'Hello $w'."\n";   //print: Hello $w
        echo "Hello $w\n";    //print: Hello world!!

## mime_content_type

* This function will return "text/plain" for css and js files instead of "test/css" or "text/javascript".
* e.g.

        <?php
        echo mime_content_type("test.js")."\n";
        echo mime_content_type("test.css")."\n";
        
  result:
  
        text/plain
        text/plain

## Null coalescing operator

* The null coalescing operator `??` has been added as syntactic sugar in **PHP 7** for null/exist judgement.
  It returns its first operand if it exists and is not NULL.
* commmon case:

        <?php
        if (isset($_GET['user'])) {
            $user = $_GET['user'];
        } else {
            $user = 'default';
        }

  after PHP 7, we can do this with

        $user = $_GET['user'] ?? 'default';
        
  Coalescing can be chained.
  
        $user = $_GET['user'] ?? $_POST['user'] ?? 'default';

## Regular Expression

### `.*` and `.*?`

* `*` is greedy; `*?` is non-greedy.
* e.g. Consider a string "10100000000000100"
    * `*` will match to the end and then backtrack until it can match 1. Therefore, you get "101000000000001" finally.
    * `?` will match nothing and then try to match extra characters until it matches 1. Therefore, you get "101" finally.

## Useful functions

### nl2br()

* Return string with `<br />` or `<br>` inserted **before** all newlines (\r\n, \n\r, \n and \r).
* e.g.

        <?php
        echo nl2br("foo\n bar");

  result:

        foo<br />
         bar
