# Docker Compose WordPress Plugin Testing with PHPUnit

This Docker Compose configuration sets up an environment for testing WordPress plugins using PHPUnit.

It is based on these two repositories:
* [Brad Traversy's Docker Compose File for WordPress](https://gist.github.com/bradtraversy/faa8de544c62eef3f31de406982f1d42)
* [Chris Zarate's WordPress Plugin/ Theme Development Docker Compose Configuration](https://github.com/chriszarate/docker-compose-wordpress)

I've also created a YouTube video to walk through the setup process. You can find that video here: https://youtu.be/CqVV5xXydBQ 

## Getting Started

### Installing WordPress and MySQL

1. Clone or fork this repository.

2. Run `docker-compose up -d` to compose WordPress and MySQL. The WordPress filesystem will be added to the project as a volume within the `wordpress` directory. This may take some time, but it will usually be completed within a minute.

3. Open `localhost:80` in the browser and perform the famous five-second install.

4. To check for errors, run `docker-compose logs`.

### Installing and Configuring PHPUnit

1. Run the following command to compose the PHPUnit container and its associated database:
```sh
docker-compose -f docker-compose.yml -f docker-compose.phpunit.yml up -d
```

2. If you need to check for errors, run `docker-compose -f docker-compose.phpunit.yml logs`.

3. Enter the PHPUnit container with bash by running the following command:
```sh
docker-compose -f docker-compose.phpunit.yml run --rm wordpress_phpunit bash
```

4. Once youve entered the container, change to the `/app/bin/` directory. This directory should contain a shell script called `install-wp-tests.sh`.

5. Within `/app/bin/` in the `wordpress_phpunit` container, run the following command to install the tests:
```sh
./install-wp-tests.sh wordpress_test root '' mysql_phpunit latest true
```

6. Exit the container by running `exit`.

### Adding Plugin and Configuring the PHPUnit Bootstrap File

1. Running `docker-compose up -d` should have created a `wordpress` directory in the project root. This directory should contain the complete WordPress file system. Add your plugin's directory to `wordpress/wp-content/plugins`.

2. The `tests` directory at the root of the project should contain a file called `bootstrap.php`. Within that file, you'll see this code on line 20:
```php
require dirname( dirname( __FILE__ ) ) . '/wordpress/wp-content/plugins/my-plugin/class-my-plugin.php';
```

3. Change `my-plugin` to match the directory of the new plugin that you created.

4. If you want the example test to pass, create a file called `class-my-plugin.php` within your new plugin directory and add the following PHP within it:
```php
<?php
/**
 * Plugin Name:     My Plugin
 * Plugin URI:      PLUGIN SITE HERE
 * Description:     PLUGIN DESCRIPTION HERE
 * Author:          YOUR NAME HERE
 * Author URI:      YOUR SITE HERE
 * Text Domain:     my-plugin
 * Domain Path:     /languages
 * Version:         0.1.0
 *
 * @package         My_Plugin
 */

/**
 * My Plugin class
 */
class My_Plugin {}
```

### Running the Test Suite

Run the PHPUnit tests from the project root directory using the following command:
```sh
docker-compose -f docker-compose.phpunit.yml run --rm wordpress_phpunit phpunit
```

### Writing Tests

This video course on [writing PHPUnit tests for WordPress](https://www.youtube.com/watch?v=Azb9yGp8jdM) is a useful resource for understanding the basics. After that, the [PHPUnit documentation](https://phpunit.readthedocs.io/en/9.2/) is very helpful.

## Other Notes

### Tearing Down Containers and Cleaning Up

To tear down the containers created by both compose files, fun the following two commands from the project's root directory:
```sh
docker-compose down --volumes --remove-orphans
```
```sh
docker-compose -f docker-compose.phpunit.yml down --volumes --remove-orphans
```

### Serving Site to a Different Port

To serve the site to a port other than `localhost:80`, change the first value of `services/wordpress/ports` within the `docker-compose.yml` file. For example, in order to serve to port 5000, this entry could be changed to `- '5000:80'`.

### Adding Feartures/ Other Repositories

This repository is based on the two repositories linked below. These repositories each contain other features, which are indicated below. It is likely that these other features could be added in future development.

* [Brad Traversy's Docker Compose File for WordPress](https://gist.github.com/bradtraversy/faa8de544c62eef3f31de406982f1d42)
  * phpMyAdmin
* [Chris Zarate's WordPress Plugin/ Theme Development Docker Compose Configuration](https://github.com/chriszarate/docker-compose-wordpress)
  * WP-CLI
  * MariaDB rather than MySQL
  * Development Proxy Server

### Additional Documentation/ Resources

* [Docker Compose Documentation](https://docs.docker.com/compose/compose-file/)
* [Brad Traversy's Video on Using His Docker Compose File for WordPress](https://www.youtube.com/watch?v=pYhLEV-sRpY)
* [Video Course on WordPress Plugin Development for Beginners](https://www.youtube.com/watch?v=0l7JTie_6jM&list=PLriKzYyLb28kR_CPMz8uierDWC2y3znI2)
* [Video Course on Writing PHPUnit Tests for WordPress Plugins](https://www.youtube.com/watch?v=Azb9yGp8jdM)
