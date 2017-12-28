## Folder structure

* There is no folder structure of project that you must follow in php.
* After referring to some articles on the Internet, I think a possible structure would like this:

        v app
            > model
            > service
            > controller
            > view
            > util
        v public
            > css
            > js
            > image
            .htaccess
            index.php
        > vendor
        .gitignore
        ...
        
* **[app]** keeps the core of project, like model, service, controller......
* **[public]** keeps resources that are public to the Internet.
    * **.htaccess** is a setting file for Apache server, You can set visibility of resources and some other rules.
      See [here](https://httpd.apache.org/docs/current/howto/htaccess.html) for more detail.
    * **index.php** can be used for router. The code will be executed when there is no matched resource to the URI.
* **[vendor]** keeps the libraries downloaded by Composer.

## Run the server for development

* Use php built-in server, just for **TEST**
* Run following command under the public folder

        php -S 0.0.0.0:[port] index.php
        
* **index.php** can be replaced with any file as router.
        
## How the project works

* The role of **index.php** is like the main function in C language.
* According to [PSR-1-2.3.(Side Effects)](http://www.php-fig.org/psr/psr-1/#23-side-effects), 
  we **SHOULD NOT** **include** or **require** any files in the files that include any declaration.
    * What if we need to **extends** a class?
        * A.php

                <?php
                namespace util1;
                
                class A
                {
                    //some codes here
                }
                
        * B.php
        
                <?php
                namespace util1\bpart;
                
                require_once __DIR__."/../A.php";  //ruin PSR-1-2.3.
                
                use util1\A as A;
                
                class B extends A
                {
                    //some codes here
                }

    * Use **Autoloader**!!

## Autoloader

* See [PSR-4](http://www.php-fig.org/psr/psr-4/) for autoloader standards
* See an example for quick start.
* Suppose that we have a util called **util1**. Following is the folder structure. (See [previous part](https://github.com/HarkuLi/PHP-Note/blob/master/project_structure.md#how-the-project-works) for contents of **A.php** and **B.php**)

        v app
            v util
                v util1
                    autoload.php
                    A.php
                    v bpart
                        B.php
            ...
        v public
            index.php
            ...
        > vendor
        .gitignore
        ...

* autoload.php

        <?php
        namespace util1;
        
        spl_autoload_register(function (string $className) {
            $className = ltrim($className, __NAMESPACE__);
            $className = str_replace("\\", DIRECTORY_SEPARATOR, $className);
            require_once __DIR__ . $className . ".php";
        });
        
* We can use **autoload.php** to require any resource in **util1** as long as we abide by PSR-4 for namespace.
  Therefore, we can write every php files in the **util1** without using **require** or **include** explicitly
  by putting every dependencies in the autoloader and **require** the autoloader while using the **util1**.
* e.g.
  
        <?php
        //index.php
        require_once __DIR__."/../app/util/util1/autoload.php";
        use util1\A as A
        
        $a = new A();
