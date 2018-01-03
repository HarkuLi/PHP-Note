## Folder structure

* There is no folder structure of project that you must follow in php.
* After referring to some articles on the Internet, I think a possible structure would like this:

        v app
            > Model
            > Service
            > Controller
            > View
            > Util
        v public
            > css
            > js
            > image
            index.php
        > vendor
        .gitignore
        ...
        
* **[app]** keeps the core of project, like model, service, controller......
* **[public]** keeps resources that are public to the Internet.
    * **index.php** can be used for router. The code will be executed when there is no matched resource to the URI.
* **[vendor]** keeps the libraries downloaded by Composer.

## Run the server for development

* Use php built-in server, just for **TEST**
* Run following command under the public folder

        php -S 0.0.0.0:[port] index.php
        
* **index.php** can be replaced with any file as router.

## Run on Apache server

1. Put the project folder in the Apache root directory.
    * On Linux, for example, is `/var/www/html`.
2. Put .htaccess in the Apache root directory.
    * **.htaccess** is a setting file for Apache server, You can set some rules in it.
      See [here](https://httpd.apache.org/docs/current/howto/htaccess.html) for more detail.
    * You need to enable mod_rewrite.
    * For Windows, see [here](https://webdevdoor.com/php/mod_rewrite-windows-apache-url-rewriting).
    * For Linux
        1. Edit /etc/apache2/apache2.conf, and modify the setting of Directory /var/www/ as:

                <Directory /var/www/>
                    Options Indexes FollowSymLinks
                    #enable AllowOverride
                    AllowOverride All
                    Require all granted
                </Directory>

        2. Enable mod_rewrite
        
                sudo a2enmod rewrite

3. The folder `/var/www/html` would look like:

        v your_project
            > app
            v public
                index.php
                ...
            > vendor
            .gitignore
            ...
        .htaccess

4. In order to use index.php as router, you need to write some rules in **.htaccess** file.

        # disable the function for showing content list in folders on the page
        Options -Indexes
        # enable the rewrite rules
        RewriteEngine On
        # mapping all paths to index.php
        RewriteRule ^.*$ your_project/public/index.php [L]

## How the project works

* The role of **index.php** is like the main function in C language.
* According to [PSR-1-2.3.(Side Effects)](http://www.php-fig.org/psr/psr-1/#23-side-effects), 
  we **SHOULD NOT** **include** or **require** any files in the files that include any declaration.
    * What if we need to **extends** a class?
        * A.php

                <?php
                namespace Util1;
                
                class A
                {
                    //some codes here
                }
                
        * B.php
        
                <?php
                namespace Util1\Bpart;
                
                require_once __DIR__."/../A.php";  //ruin PSR-1-2.3.
                
                use Util1\A as A;
                
                class B extends A
                {
                    //some codes here
                }

    * Use **Autoloader**!!

## Autoloader

* See [PSR-4](http://www.php-fig.org/psr/psr-4/) for autoloader standards

### Write by ourselves

* Look at an example for quick start.
* Suppose that we have a util called **Util1**. Following is the folder structure. (See [previous part](https://github.com/HarkuLi/PHP-Note/blob/master/project_structure.md#how-the-project-works) for contents of **A.php** and **B.php**)

        v app
            v Util
                v Util1
                    autoload.php
                    A.php
                    v Bpart
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
        namespace Util1;
        
        spl_autoload_register(function (string $className) {
            $className = ltrim($className, __NAMESPACE__);
            $className = str_replace("\\", DIRECTORY_SEPARATOR, $className);
            require_once __DIR__ . $className . ".php";
        });
        
* We can use **autoload.php** to require any resource in **Util1** as long as we abide by PSR-4 for namespace.
  Therefore, we can write every php files in the **Util1** without using **require** or **include** explicitly
  by putting every dependencies in the autoloader and **require** the autoloader while using the **Util1**.
* e.g.
  
        <?php
        //index.php
        require_once __DIR__."/../app/Util/Util1/autoload.php";
        use Util1\A as A
        
        $a = new A();
        
### Use the autoloader offered by Composer(Recommended)

* Use the same project structure above for example.
* **composer.json** (Comments are not permitted in the Json file, and here is just for explanation.)

        {
            "autoload": {
                "psr-4": {
                    //[namespace]: [practical location]
                    "Util1\\": "app/Util/Util1"
                }
            }
        }

* After editting **composer.json** run

        composer dump
        
  to generate autoload files.
* **index.php**

        <?php
        require_once __DIR__."/../vendor/autoload.php";
        use Util1\A as A
        
        $a = new A();
