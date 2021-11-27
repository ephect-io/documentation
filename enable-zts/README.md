## Enable ZTS for Ephect framework

If you want to enable the parallel feature, you must have a ZTS version of PHP and setup the Parallel extension.

### PHP thread-safe

The easiest way to install PHP-ZTS is by using PhpBrew.

Here is a sample of PHP compilation statement for getting a thread-safe version of php-fpm : 

    phpbrew install 8.0.9 +bcmath +bz2 +calendar +cli +ctype +dom +fileinfo +filter +fpm +ipc +json +mbregex +mbstring +mhash +pcntl +pcre +pdo +pear +phar +posix +readline +sockets +tokenizer +xml +curl +openssl +zip +sqlite -- --enable-zts

To install PhpBrew, please refer to the documentation at [https://github.com/phpbrew/phpbrew](https://github.com/phpbrew/phpbrew).

### Parallel extension

Ephect uses Parallel extension as multi-thread mechanism. At the time of writing, Parallel extension ***cannot*** be installed in PhpBrew just by typing the usual statement:
   
    phpbrew ext install parallel

Instead, you need to download the [develop zip archive](https://github.com/krakjoe/parallel/archive/refs/heads/develop.zip) and add the extension to PhpBrew on your own:

    wget https://github.com/krakjoe/parallel/archive/refs/heads/develop.zip
    unzip develop.zip
    cd parallel-develop
    phpize
    ./configure --enable-parallel
    make
    make test
    make install
    
The library will be installed in the right place like: 

    ~/.phpbrew/php/php-8.0.9/lib/php/extensions/no-debug-zts-20200930/

However you still need to declare the extension:

    echo extension=parallel.so > ~/.phpbrew/php/php-8.0.9/var/db/parallel.ini
