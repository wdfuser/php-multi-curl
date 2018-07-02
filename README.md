PHP Multi-Curl
======

A simple and efficient library wrapping curl_multi_* is used to handle parallel http requests.

## Requirements

* PHP 5.4 or later
* PHP cURL extension

## Installation via Composer([packagist](https://packagist.org/packages/hhxsv5/php-multi-curl))

```BASH
composer require "hhxsv5/php-multi-curl:~1.0" -vvv
```

## Usage

```PHP
//require '../vendor/autoload.php';
use Hhxsv5\PhpMultiCurl\Curl;
use Hhxsv5\PhpMultiCurl\MultiCurl;

//Single http request
$options = [//The custom the curl options
    CURLOPT_TIMEOUT        => 10,
    CURLOPT_CONNECTTIMEOUT => 5,
    CURLOPT_USERAGENT      => 'Multi-Curl Client V1.0',
];

$c1 = new Curl($options);
$c1->makeGet($getUrl);
$response = $c1->exec();
if ($response->hasError()) {
    //Fail
    var_dump($response->getError());
} else {
    //Success
    var_dump($response->getHttpCode(), $response->getBody());
}
```
 
```PHP
//Multi http request
$c2 = new Curl();
$c2->makeGet($getUrl);
$c3 = new Curl();
$c3->makePost($postUrl);

$mc = new MultiCurl();
$mc->addCurls([$c2, $c3]);
$allSuccess = $mc->exec();
if ($allSuccess) {
    //All success
    var_dump($c2->getResponse()->getBody(), $c3->getResponse()->getBody());
} else {
    //Some curls failed
    var_dump($c2->getResponse()->getError(), $c3->getResponse()->getError());
}

//Reuse $mc
$c4 = new Curl();
$c4->makeGet($getUrl);
$c5 = new Curl();
$c5->makePost($postUrl);
$mc->addCurls([$c4, $c5]);
$allSuccess = $mc->exec();
if ($allSuccess) {
    //All success
    var_dump($c4->getResponse()->getBody(), $c5->getResponse()->getBody());
} else {
    //Some curls failed
    var_dump($c4->getResponse()->getError(), $c5->getResponse()->getError());
}
```

```PHP
//Upload file
$postUrl = 'http://localhost/upload.php';//<?php var_dump($_FILES);

$options = [//The custom the curl options
    CURLOPT_TIMEOUT        => 10,
    CURLOPT_CONNECTTIMEOUT => 5,
    CURLOPT_USERAGENT      => 'Multi-Curl Client V1.0',
];
$c = new Curl($options);
$file1 = new CURLFile('./olddriver.gif', 'image/gif', 'name1');
$params = ['file1' => $file1];
$c->makePost($postUrl, $params);
$response = $c->exec();
if ($response->hasError()) {
    //Fail
    var_dump($response->getError());
} else {
    //Success
    var_dump($response->getBody());
}
```

```PHP
//Download file
$fileUrl = 'http://localhost/test.gif';//<?php var_dump($_FILES);
$options = [//The custom the curl options
    CURLOPT_TIMEOUT        => 3600,//1 hour
    CURLOPT_CONNECTTIMEOUT => 10,
];
$c = new Curl($options);
$c->makeGet($fileUrl);
$response = $c->exec();
if ($response->hasError()) {
    //Fail
    var_dump($response->getError());
} else {
    //Success
    $targetFile = './a/b/c/test.gif';
    var_dump($c->responseToFile($targetFile));
}
```

## TODO

* Anything what you want

## License

[MIT](https://github.com/hhxsv5/php-multi-curl/blob/master/LICENSE)
