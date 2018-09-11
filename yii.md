## Session

### Logs in the function that overload CHttpSession.writeSession() not work.

Assuming that we have a customized log route.

```php
<?php
class MyLogRoute extends CFileLogRoute
{
    ...
    protected function processLogs($logs)
    {
        echo "Process logs.<br>";
        ...
    }
    ...
}
```

A customized session.

```php
<?php
class MySession extends CHttpSession
{
    ...
    public function writeSession($id, $data)
    {
        Yii::log("Write session.");
        echo "Write session.<br>";
    }
}
```

And a controller that will write session.

```php
<?php
class TestController extends CController
{
    private $session;

    public function __construct($id)
    {
        parent::__construct($id);
        $this->session = Yii::app()->getSession();
    }

    public function actionWrite()
    {
        $this->session->add("cake", "oishii");
        echo "ActionWrite end.<br>"
    }
}
```

Then, visit the page test/write, it will print:

```
ActionWrite end.
Process logs.
Write session.
```

It shows us somethings.

1. Yii will close the session for us if we don't do that.
2. Yii will process logs before write session.

Because 2., all logs written in the writeSession() won't work.

Now, modify TestController.actionWrite()

```php
<?php
public function actionWrite()
{
    $this->session->add("cake", "oishii");
    $this->session->close();
    echo "ActionWrite end.<br>"
}
```

And then, visit the page test/write again, it will print:

```
Write session.
ActionWrite end.
Process logs.
```

The logs in the writeSession() will be processed because it is written before processLogs() is called.

Therefore, to prevent the logs in the writeSession() from losing,

```
ALWAYS remember to close the session if you don't need it anymore.
```
