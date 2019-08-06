## Case Sensitivity

* All **keywords**, **classes**, **functions**, and **user-defined functions** are ***NOT*** case-sensitive.
* All **variable** names are, however, case-sensitive.

## Scope

### Local:

* A variable declared within a function can only be accessed within that function.
* e.g.

    ```php
    <?php
    function foo() {
        $x = 5; // local scope
        echo $x;
    }
    foo();
	
    // x can't be accessed outside the function
    echo $x;
    ```

### Global:

* A variable declared **outside** a function has a **global scope** and can ***ONLY*** be accessed **outside** a function.
* e.g.

    ```php
    <?php
    $x = 5; // global scope

    function foo() {
        // x can't be accessed inside the function
        echo $x;
    }
	
    echo $x;
    ```

### Static:

* The value of a local variable will be retained if using the **static** keyword.
* e.g.
	
    ```php
    <?php
    function foo() {
        static $x = 0;
        ++$x;
    }
	
    foo(); // x = 1
    foo(); // x = 2
    ```

### $this, self::, and static::

* $this
    * **$this-> will try to call private methods from the same scope.**
    * otherwise, $this indicates the current **Object**.
    * e.g.
        * code:

            ```php
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
            ```

        * result:

            ```
            in class A
            in class A
            in class B
            ```
        
        * if change fromWhere() in class A to public, the result would be:

            ```
            in class B
            in class A
            in class B
            ```
        
        * if remove fromWhere() in class A, the result would be:

            ```
            in class B
            Call to undefined method A::fromWhere()
            in class B
            ```

* self::
    * Indicate the current **Class**.
* static::
    * Indicate the **late override one**. (i.e. **Late Static Binding**)
* e.g.
    * code:

        ```php
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
        ```

    * result:

        ```
        in class B
        in class A
        in class B
        ```

### self::class in traits

In general, self::class will return the name of current class. In trait, however, it will return **the name of the class which uses the trait** as of PHP 5.5.18, and return **the name of the trait** in PHP 5.5.5 and before. I am not sure about certain version for this change, but it's definitely changed between PHP 5.5.5 and PHP 5.5.18.

## Data Types

* The PHP `var_dump()` function returns the data type and value.

### Type Casting

* **int** casting from **float** performs like **floor()**.
    * e.g.

        ```php
        <?php
        $test = (int)1.4;
        echo $test."\n";
        $test = (int)1.5;
        echo $test."\n";
        ```

      It will print:
      
        ```
        1
        1
        ```

## Constants

* Constants are **automatically global** and can be used ***across the entire script***.

## Operators

### Comparison Operators

* `<>` and `!=` both represent weak-typed inequality in PHP. However, `<>` doesn't have the same visual harmony with the strong-typed inequality operator, `!==`, so **always** use `!=` for weak-typed inequality.
* <font color=red>Don't Trust PHP Floating Point Numbers When Equating.</font>
    * Because floating point numbers are actually being stored in the binary base 2, and it causes the loss of precision.
    * e.g.

        ```php
        $sum = 0.1 + 0.2;
        echo $sum; // 0.3
        echo ($sum === 0.3) ? "true" : "false"; // false
        ```

    * Solutions:
        1. Test against the smallest acceptable difference.

            ```php
            $sum = 0.1 + 0.2;
            echo (abs($sum - 0.3) < 0.1) ? "true" : "false"; // true
            ```
			
        2. Use PHP’s BC Math functions for handling the addition.
		
            ```php
            // the third parameter specifies the precision after the floating point
            $sum = bcadd(0.1, 0.2, 1);
            echo $sum; // 0.3
            echo ($sum === 0.3) ? "true" : "false"; // true
            ```
		
    * [Reference](https://andy-carter.com/blog/don-t-trust-php-floating-point-numbers-when-equating)

### Logical Operators

* The difference between operators `and` and `&&` (`or` and `||`) is their **precedence**. See [here](http://php.net/manual/en/language.operators.precedence.php).

## Functions

* By default, function arguments are <font color=red>passed by value</font>.
* Use an ampersand (**&**) for passed by reference.
    * e.g.

        ```php
        <?php
        function cat_some_str(&$input_str) {
            $input_str .= "cat.";
        }

        $str = "This is a ";
        cat_come_str($str);
        echo $str; // outputs "This is a cat."
        ```

## Type declarations

* Type declarations were also known as **type hints in PHP 5**.
* ***Aliases for the scalar types are not supported***.
    * alias table

        |  type  |    alias   |
        | :----: | :--------: |
        | bool   | boolean    |
        | int    | integer    |
	  
    * e.g.

        ```php
        <?php
        function test(boolean $param) {}
        test(true); // fatal error
        ```
	    
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

    ```php
    <?php
    $w = "world!!";
    echo 'Hello $w'."\n";   //print: Hello $w
    echo "Hello $w\n";    //print: Hello world!!
    ```

## mime_content_type

* This function will return "text/plain" for css and js files instead of "test/css" or "text/javascript".
* e.g.

    ```php
    <?php
    echo mime_content_type("test.js")."\n";
    echo mime_content_type("test.css")."\n";
    ```
        
  result:

    ```php
    text/plain
    text/plain
    ```

## Null coalescing operator

* The null coalescing operator `??` has been added as syntactic sugar in **PHP 7** for null/exist judgement.
  It returns its first operand if it exists and is not NULL.
* commmon case:

    ```php
    <?php
    if (isset($_GET['user'])) {
        $user = $_GET['user'];
    } else {
        $user = 'default';
    }
    ```

  after PHP 7, we can do this with

    ```php
    $user = $_GET['user'] ?? 'default';
    ```
        
  Coalescing can be chained.

    ```php
    $user = $_GET['user'] ?? $_POST['user'] ?? 'default';
    ```

## Splat operator

Arrays and Traversable objects can be unpacked into argument lists by using the ... operator.

* Version: PHP 5.6+

* Function declaration

    ```php
    <?php
    function add(...$input)
    {
        return array_sum($input);
    }
    var_dump(add(1, 2, 3) === 6);
    ```

* Calling function

    ```php
    <?php
    function add($a, $b, $c)
    {
        return $a + $b + $c;
    }
    var_dump(add(1, ...[2, 3]) === 6);
    ```

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

    ```php
    <?php
    echo nl2br("foo\n bar");
    ```

  result:

    ```
    foo<br />
     bar
    ```

### array_merge() and array_replace()

The behavior of array_merge and array_replace are almost the same except numeric keys (including numeric string) handling.

* array_merge() will renumber all numeric indices with incrementing keys starting from zero and retain all values in numeric indices(no replace).

  e.g.

    ```php
    <?php
    $a = ["55" => 1, "56" => 2, "57" => 3];
    $b = ["55" => 4, "100" => 5, "101" => 6];
    print_r(array_merge($a, $b));
    ```

  result:

    ```
    Array
    (
        [0] => 1
        [1] => 2
        [2] => 3
        [3] => 4
        [4] => 5
        [5] => 6
    )
    ```

* array_replace() will not renumber numeric indices and replace all values in the first array if following arrays have the same key.

  e.g.

    ```php
    <?php
    $a = ["55" => 1, "56" => 2, "57" => 3];
    $b = ["55" => 4, "100" => 5, "101" => 6];
    print_r(array_replace($a, $b));
    ```

  result:

    ```
    Array
    (
        [55] => 4
        [56] => 2
        [57] => 3
        [100] => 5
        [101] => 6
    )
    ```

### array_unique() and array_flip(array_flip())

Both way makes elements in the array unique, but they have some differences.

1. `array_unique()` will retain keys, but double `array_flip()` won't.
2. Double `array_flip()` is faster. It is probably because of `array_unique()` retaining keys.

#### speed comparison

code:

```php
<?php
$target = [];
for ($i=0; $i<50; ++$i) {
    $target[] = $i;
    $target[] = $i;
}

$testCount = 100000;

$start = microtime(true);
for ($i=0; $i<$testCount; ++$i) {
    array_unique($target);
}
$end = microtime(true);
$cost = $end - $start;
echo "array_unique cost: $cost\n";

$start = microtime(true);
for ($i=0; $i<$testCount; ++$i) {
    array_flip(array_flip($target));
}
$end = microtime(true);
$cost = $end - $start;
echo "array_flip cost: $cost\n";
```

result:

```
array_unique cost: 0.43791103363037
array_flip cost: 0.20182299613953
```
