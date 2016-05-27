Generate large number of unique coupons
=======================================

This library create promo codes which should be short in length (example ~ 6 characters) just storing the data in a table, setting an auto-increment and using that ID to hash out a 6-digit random code
To use library you'll need to have Hashids. Hashids[hashids] is a small open-source library that generates short, unique, non-sequential ids from numbers.

 * [Installation](#installation)
 * [Usage](#usage)
 * [Examples](#examples)

Installation
------------
with Composer[composer], in the root directory of your project:
    composer install 

Usage
-----

Make sure the autoloader is included in your project's bootstrap file.

    require_once "vendor/autoload.php";

    use Hashids\Hashids;

    $minHashLength = 6;
    $maxIntValue   = 999999;
    $hashIds       = new Hashids('this is my salt', $minHashLength, 'ABCDEFGHIJKLMNPQRSTUVWXYZ1234567890'); //A-Z and 0-9, omitting "O"

    $mysqli = new mysqli('localhost', 'root', 'root', 'hashids');

    if ($mysqli->connect_error) {
        die('Connect Error (' . $mysqli->connect_errno . ') ' . $mysqli->connect_error);
    }

    echo 'Success... ' . $mysqli->host_info . "\n";

    $sql = "DROP TABLE IF EXISTS `coupons`";
    if ($mysqli->query( $sql ) !== TRUE) {
        die( 'Error: ' . $sql . "\n" . $mysqli->error );
    }

    $sql = "CREATE TABLE `coupons` (
            `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
              `encode` varchar(6) NOT NULL,
              `coupons` varchar(6) NOT NULL DEFAULT '',
              PRIMARY KEY (`id`),
              UNIQUE KEY `coupons` (`coupons`)
            ) ENGINE=InnoDB DEFAULT CHARSET=utf8;";
    if ($mysqli->query( $sql ) !== TRUE) {
        die( 'Error: ' . $sql . "\n" . $mysqli->error );
    }

    for ($i = 1; $i <= $maxIntValue; $i++) {
        $id = $hashIds->encode($i);
        $numbers = $hashIds->decode($id);
        //var_dump($id, $numbers);

        $sql = "INSERT INTO coupons (encode, coupons) VALUES ( '$i', '$id' )";

        if ($mysqli->query($sql) === true) {
            echo 'New record created successfully' . "\n";
        } else {
            echo 'Error: ' . $sql . "\n" . $mysqli->error;
        }
    }

    $mysqli->close();
    
[hashids]: https://github.com/ivanakimov/hashids.php
[composer]: https://getcomposer.org/