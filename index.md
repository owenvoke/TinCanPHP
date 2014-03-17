---
layout: index
---

### Supported API Versions

1.0.1, **1.0.0**

### Basic Usage

The following samples show the basic usage of TinCanPHP.

All samples assume that a `$lrs` object has already been created with the proper endpoint and credentials to connect to the LRS.

```php
$lrs = new TinCan\RemoteLRS(
    'https://cloud.scorm.com/tc/public/',
    '1.0.1',
    '<Test Username>',
    '<Test Password>'
);
```

Sample sending of a statement and checking for successful response:

```
$actor = new TinCan\Agent(
    [ 'mbox' => 'mailto:info@tincanapi.com' ]
);
$verb = new TinCan\Verb(
    [ 'id' => 'http://adlnet.gov/expapi/verbs/experienced' ]
);
$activity = new TinCan\Activity(
    [ 'id' => 'http://rusticisoftware.github.com/TinCanPHP' ]
);
$statement = new TinCan\Statement(
    [
        'actor' => $actor,
        'verb'  => $verb,
        'object' => $activity,
    ]
);

$response = $lrs->sendStatement($statement);
if ($response->success) {
    print "Statement sent successfully!\n";
}
else {
    print "Error statement not sent: " . $response->content . "\n";
}
```

Alternatively most interfaces that take a specific type of object can also be passed an array that will be materialized into the particular type of object. This allows for easier creation of simplified calls, such as sending a statemnt without pre-composing the objects. Such as:

```php
$response = $lrs->sendStatement(
    [
        'actor' => [
            'mbox' => 'info@tincanapi.com',
        ],
        'verb' => [
            'id' => 'http://adlnet.gov/expapi/verbs/experienced',
        ],
        'object' => [
            'id' => 'http://rusticisoftware.github.com/TinCanPHP',
        ],
    ]
);
```

The various parts of the Statement structure can also be instantiated using the `fromJSON` method as a factory. For instance given a string of JSON representing an Agent we can get an instance of the `TinCan\Agent` class by doing:

```php
$stringOfJSON = '{"mbox":"mailto:info@tincanapi.com"}';

$agent = TinCan\Agent::fromJSON($stringOfJSON);
```

All objects provide a get/set interface for their properties, and in most cases the setter can be chained. So we can construct an Activity like:

```php
$activity = new TinCan\Activity();
$activity
    ->setId('http://rusticisoftware.github.com/TinCanPHP')
    ->setDefinition([]);

$activity->getDefinition()
    ->setType('http://activitystrea.ms/schema/1.0/page')
    ->getName()
    ->set('en-US', 'TinCanPHP Homepage');
```

Sample of working with the document APIs, in particular the State API.

```php
$saveResponse = $lrs->saveState(
    $activity,
    $agent,
    'testDocument',
    'someValue'
);
if ($saveResponse->success) {
    $retrieveResponse = $lrs->retrieveState(
        $activity,
        $agent,
        'testDocument'
    );
    if ($retrieveResponse->success) {
        print $retrieveResponse->content; // 'someValue'

        $deleteResponse = $lrs->deleteState($activity, $agent, 'testDocument');
    }
}
```