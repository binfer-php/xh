To begin with, you need to install the XHProf extension. Refer to the [PHP documentation](http://www.php.net/manual/en/xhprof.setup.php) if you need assistance.

You will need to manually create the database and populate it with the provided scheme. The database scheme is located at `/setup/database.sql`.

Rename the `/xhprof/includes/config.inc.sample.php` to `/xhprof/includes/config.inc.php`. There are only two supported parameters.

* `xhprof_url` is the URL to the XHProf.io library.
* `pdo` is the PDO instance. This library uses [PDO](http://uk3.php.net/pdo) to handle all of the database operations.
* `enable` is a closure to control when enable data collection, return true means always enable

Some cases for reference:

Alway enable
``` php
'enable' => function() {
     return true;
}
```

Enable if url contents `debug` parameter:
``` php
'enable' => function() {
    if (!empty($_GET['debug'])) {
        return true;
    }
}
```

Enable for 1/100 probability
``` php
'enable' => function() {
    return rand(0, 100) === 42;
}
```

Enable for url path is `/`:
``` php
'enable' => function() {
    if (parse_url($_SERVER['REQUEST_URI'], PHP_URL_PATH) === '/') {
        return true;
    }
}
```


For XHProf.io to start collecting data, you need `/inc/inject.php` files included to every file of interest. The recommended approach is to update your `php.ini` configuration to automatically prepend and append these files.

    auto_prepend_file = /[absolute path to xhprof.io]/inc/inject.php

If you use nginx, you could configuration `auto_prepend_file` as a `fastcgi_param`

    fastcgi_param  PHP_VALUE "auto_prepend_file=/[absolute path to xhprof.io]/inc/inject.php";

If you are using PHP-FPM, then XHProf.io will utilise `fastcgi_finish_request` to hide any overhead related to data collection. There is nothing to worry about if you are not using PHP-FPM either, as the overhead is less than a few milliseconds.
