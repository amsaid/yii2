Configuration
=============

In Yii application and majority of components have sensible defaults so it's unlikely you spend lots of time configuring
it. Still there are some mandatory options such as database connection you should set up.

How application is configured depends on application template but there are some general principles applying in any case.

Configuring options in bootstrap file
-------------------------------------

For each application in Yii there is at least one bootstrap file. For web applications it's typically `index.php`, for
console applications it's `yii`. Both are doing nearly the same job:

1. Setting common constants.
2. Including Yii itself.
3. Including Composer autoloader.
4. Reading config file into `$config`.
5. Creating new application instance using `$config` and running it.

Bootstrap file is not the part of framework but your application so it's OK to adjust it to fit your application. Typical
adjustments are the value of `YII_DEBUG` that should never be `true` on production and the way config is read.

Configuring application instance
--------------------------------

It was mentioned above that application is configured in bootstrap file when its instance is created. Config is typically
stored in a PHP file in `/config` directory of the application and looks like the following:

```php
<?php
return array(
	'id' => 'applicationId',
	'basePath' => dirname(__DIR__),
	'components' => array(
		// ...
	),
	'params' => require(__DIR__ . '/params.php'),
);
```

In the above array keys are names of application properties. Depending on application type you can check properties of
either `\yii\web\Application` or `\yii\console\Application`. Both are extended from `\yii\base\Application`.

> Note that you can configure not only public class properties but anything accessible via setter. For example, to
  configure runtime path you can use key named `runtimePath`. There's no such property in the application class but
  since there's a corresponding setter named `setRuntimePath` it will be properly configured.

Configuring application components
----------------------------------

Majority of Yii functionality are application components. These are attached to application via its `components` property:

```php
<?php
return array(
	'id' => 'applicationId',
	'basePath' => dirname(__DIR__),
	'components' => array(
		'cache' => array(
			'class' => 'yii\caching\FileCache',
		),
		'user' => array(
			'identityClass' => 'app\models\User',
		),
		'errorHandler' => array(
			'errorAction' => 'site/error',
		),
		'log' => array(
			'traceLevel' => YII_DEBUG ? 3 : 0,
			'targets' => array(
				array(
					'class' => 'yii\log\FileTarget',
					'levels' => array('error', 'warning'),
				),
			),
		),
	),
	// ...
);
```

In the above four components are configured: `cache`, `user`, `errorHandler`, `log`. Each entry key is a component ID
and the value is the configuration array. ID is used to access the component like `\Yii::$app->myComponent`.
Configuration array has one special key named `class` that sets component class. The rest of the keys and values are used
to configure component properties in the same way as top-level keys are used to configure application properties.

Each application has predefined set of the components. In case of configuring one of these `class` key is omitted and
application default class is used instead. You can check `registerCoreComponents` method of the application you are using
to get a list of component IDs and corresponding classes.

Note that Yii is smart enough to configure the component when it's actually used i.e. if `cache` is never used it will
not be instantiated and configured at all.

Setting component defaults classwide
------------------------------------

TBD
