I use WHMCS to manage billing for hosting accounts on my Webmin/Virtualmin server.  Occasionally, after updating the server, a few things go awry, including the ionCube PHP Loader.

Intermittent problems that don't happen often are often hard to fix because I forget how to do it.  So this is an account of how to fix this problem.

First, the problem.  When I access the root of WHMCS on my web server, I get the following:

!!! error
    Site error: the ionCube PHP Loader needs to be installed. This is a widely used PHP extension for running ionCube protected PHP code, website security and malware blocking. Please visit get-loader.ioncube.com for install assistance.

## How to Solve

Download the most recent ioncube loader that matches the PHP version on the server `php -v`.
Extract the download to the `/usr/local` folder.
Find the pertinent PHP file for the PHP version running on the server, most likely /etc/php/version/apache2/php.ini

Ensure that the following line is in the php.ini file:

`zend_extension = /usr/local/ioncube/ioncube_loader_lin_x.so`

Save and restart apache.