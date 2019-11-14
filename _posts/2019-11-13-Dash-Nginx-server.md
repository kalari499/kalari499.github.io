---
layout: post
title: Building&#58; Dash - Nginx webserver
description: Project Incredibles series (Dash)&#58; A detailed instruction on how to set up a Nginx webserver with PHP and MySQL, with some security notes.
category: Incredibles
---

### Context

This is the first post in the series [Project Incredibles](/incredibles).
It will be a long post so hang tight.

Today we are going to setup Dash machine - a dynamic webserver using Nginx, or as the buzzword goes "LEMP stack".
To be more precise, we are going to look at how to set up a basic Nginx server, with PHP and HTTPS support, as well as MySQL for the database.
I'm also going to make some security notes at the bottom to see some common pitfalls/misconfigurations of server.

Our starting point is a fresh installation of Ubuntu Server 18.04 on VirtualBox.
I choose it as the operating system for the server here because:
- Ubuntu: The most common Linux distro out there, which means more packages, more testing and more community support
- Ubuntu Server: we don't need any desktop for a webserver, just SSH in to do everything
- 18.04 LTS: LTS is the most stable, and 18.04 is the latest version

### Step 1: Install Nginx server

First things first, let's synchronize our package database with upstream
```bash
sudo apt-get update
```

*Optional*: For upgrading the whole system after synchronizing database.
```bash
sudo apt-get upgrade
```
Technically this is not needed but it is generally recommended, because it's usually security or stability update.

To install Nginx from nginx package
```bash
sudo apt-get install nginx
```
Just that simple we have installed Nginx server.
To confirm that the server is running, run
```bash
systemctl status nginx
```
![Screenshot](/assets/07-1.png)

If nothing goes wrong, open our favourite browser and navigate to localhost (port 80).
We should see this default webpage.
![Screenshot](/assets/07-2.png)

The default root path for webserver is at */var/www/html/*.
The server now can serve static HTML/CSS contents.
But we still can't do anything dynamic yet.


### Step 2: Add PHP support

A static webserver is cool, but some people need dynamic contents.
Let's use PHP for this.

To install PHP-FPM (PHP FastCGI Process Manager)
```bash
sudo apt-get install php-fpm
```

Unfortunately the systemd service for PHP-FPM is not *php-fpm.service* but instead *php${version}-fpm*
In order to find it run
```bash
systemctl list-unit-files | grep php # Mine shows php7.2-fpm.server
```
Now we can check if PHP-FPM is running with
```bash
systemctl status php7.2-fpm
```

Next thing to do is changing *sites-available/default* to make Nginx use PHP-FPM, something like this
```bash
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;

    index index.php index.html;
    
    server_name _;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
    }
```
and restart Nginx for it to use new config
```bash
sudo systemctl restart nginx
```

To confirm that PHP is running, modify */var/www/html/index.php*
```bash
cat > /var/www/html/index.php << EOF
<?php
phpinfo();
?>
EOF
```
and open localhost.
We should see the info page.
![Screenshot](/assets/07-3.png)

That's it, now we have ourselves a fast webserver that can serve both static webpages as well as dynamic webpages using PHP.
But we still don't have anything in the server yet.


### Step 3: Add contents to webserver (part 1)

Let's build something simple to demonstrate the usage of our server.
I'm going to set up a page with login/logout functionality, with the help from PHP session and its API.

This is our main page `index.php`, it would redirect us to login page if we are not logged in.
```php
<?php
session_start();
if (!isset($_SESSION['user'])) {
    header("Location: login.php");
    echo "Redirecting you to login portal.";
    exit();
}
?>

<title>Dashboard</title>
<?php
    $user = $_SESSION['user'];
    echo "You are logged in as user $user. Click <a href=/logout.php>here</a> to logout.";
?>
```

The main logic of the program `login.php`, it handles the login stuff obviously.
```php
<?php
session_start();
$err = "";
if (isset($_POST['submit'])) {
    if (empty($_POST['username']) || empty($_POST['password'])) {
        $err = "Username or password empty.";
    }
    else {
        $username=$_POST['username'];
        $password=$_POST['password'];
        if ($username == 'dash' && $password == 'hunter2') {
            $_SESSION['user'] = $username;
            header("Location: index.php");
            exit();
        }
        else {
            $err = "Wrong credential.";
        }
    }
}
?>

<title>Login</title>
<form action=<?php echo $_SERVER['PHP_SELF']; ?> method="post">
    <label>Username</label>
    <input name="username" type="text">
    <br/>
    <label>Password</label>
    <input name="password" type="password">
    <br/>
    <input name="submit" type="submit" value="login">
    <br/>
    <span><?php echo $err; ?></span>
</form>
```

And lastly a simple logout page `logout.php` to round up.
```php
<?php
session_start();
session_destroy();
?>

<title>Logout</title>
<p>You are logged out.</p>
<p>Click <a href="/">here</a> to return to main page.</p>
```

![Screenshot](/assets/07-4.png)

And voila, here we have our very simple functional page.
However, there is still a few things missing: the username/password value is hardcoded and no signup functionality.


### Step 4: Install MySQL database

Of course we need a database.
Here I choose MySQL as the database because it's the classic common one.

To install MySQL, open our terminal again and run
```bash
sudo apt-get install mysql-server mysql-client php-mysql
```

To confirm that MySQL server is running, run
```bash
systemctl status mysql
```
![Screenshot](/assets/07-5.png)

At the default installation, the only user that can access running MySQL server is root.
This is not very desirable because our webserver is running as www-data, instead we want be able to login using class username/password.

To connect to MySQL server, run
```bash
sudo mysql
```

To list available users as well as authentication method, run
```bash
SELECT user, host, authentication_string, plugin FROM mysql.user;
```
You should see something like: root - localhost - (empty) - auth\_socket.
This means the user root@localhost can connect using socket.
To change the authentication method to using password (with *hunter2* as password), run
```bash
UPDATE mysql.user SET plugin='mysql_native_password' WHERE user='root';
UPDATE mysql.user SET authentication_string=password('hunter2') WHERE user='root';
```

Apply changes and quit
```bash
FLUSH PRIVILEGES;
QUIT;
```

We have successfully installed and configured MySQL server.
Now any users can connect and interact with the server with username/password: root/hunter2.
![Screenshot](/assets/07-6.png)


### Step 5: Add content to webserver (part 2)

Let's upgrade our webserver with new SQL technology.
Instead of letting only dash login, we will create a database of users, as well add a signup functionality.

First let's create a database named *superheroes* with a table *information* in MySQL shell
```bash
CREATE DATABASE superheroes;

USE superheroes;

CREATE TABLE information (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    password VARCHAR(50) NOT NULL,
    real_name VARCHAR(50),
    age INT UNSIGNED
);
```

To add an user into *information* table
```bash
INSERT INTO information (username, password, real_name, age)
VALUES ('dash', SHA2('dash', 512), 'Dash Parr', 10);
```
![Screenshot](/assets/07-7.png)

#### IMPORTANT NOTES:
- It's important to use strong cryptographic function, don't use MD5 or SHA1. Instead use SHA2 family, which contains SHA-224, SHA-256, SHA-384, SHA-512. Here I use SHA-512 for demonstration, it's stronger than the others but also takes more spaces to store (128 characters)
- You should use salt as well to avoid rainbow table attack.
- Becareful with logging. Statements contain passwords in plain-text can be found in the logfile, which effectively rendered the hashing part useless.

Anyway let's continue creating some more fake accounts for our information databases.
![Screenshot](/assets/07-8.png)

Now we need to change `login.php` to interact with database
```php
<?php
session_start();
$err = "";
if (isset($_POST['submit'])) {
    if (empty($_POST['username']) || empty($_POST['password'])) {
        $err = "Username or password empty.";
    }
    else {
        $sql = new mysqli("localhost", "root", "hunter2", "superheroes");
        if ($sql->connect_errno) {
            echo "Connection error: " . $sql->connect_error;
            exit();
        }

        # Please don't get SQL injection
        # I really mean it, it's so easy to avoid

        $username=$sql->real_escape_string($_POST['username']);
        $password=hash("sha512", $sql->real_escape_string($_POST['password']));

        $query = $sql->query("SELECT * FROM information WHERE username='$username' AND password='$password';);
        if ($query->num_rows > 0) {
            $_SESSION['user'] = $username;
            header("Location: index.php");
            $query->close();
            exit();
        }
        else {
            $err = "Wrong credential.";
            $query->close();
        }
    }
}
?>

<title>Login</title>
<form action=<?php echo $_SERVER['PHP_SELF']; ?> method="post">
    <label>Username</label>
    <input name="username" type="text">
    <br/>
    <label>Password</label>
    <input name="password" type="password">
    <br/>
    <input name="submit" type="submit" value="login">
    <br/>
    <span><?php echo $err; ?></span>
</form>
```

And voila we have ourselves a functional login page that allows multiple people.
As an exericse for you, try creating a `signup.php` where anybody can sign up.
Don't forget to check if an user already exists, check empty fields and such.


### Step 6: Add HTTPS support for server

We have got ourselves a server that we can log in.
But everything is still in HTTP, which means everything is in clear text, anybody can eavesdrop the traffic to see your password.
That's not good.
Let's add HTTPS to the our server.

First, we are going to create a SSL certificate.
Here I'm going to use self-signed certificate for demonstration.

Let's create a generic folder to store our cert
```bash
sudo mkdir /etc/nginx/ssl-certs
cd /etc/nginx/ssl-certs
```

Generating private key (.key) with 1024 bits
```bash
sudo openssl genrsa -out dash.key 1024
```

Generate certificate signing request (.csr)
```bash
sudo openssl req -new -key dash.key -out dash.csr
```

Then final to sign the request, as well as export it in X.509 format.
This cert would be valid for 365 days.
```bash
sudo openssl x509 -req -days 365 -in dash.csr -signkey dash.key -out dash.crt
```

Now let's add a new config file at */etc/nginx/sites-available/https*.
It is pretty much self-explanatory.
```bash
server {
    listen 443 ssl;
    listen [::]:443 ssl;

    root /var/www/html;

    index index.php index.html;

    server_name _;
    ssl_certificate     /etc/nginx/ssl-certs/dash.crt;
    ssl_certificate_key /etc/nginx/ssl-certs/dash.key;
    ssl_protocols       TLSv1 TLSv1.1 TLSv1.2; # Note: TLSv1 might be vulnerable
    ssl_ciphers         HIGH:!aNULL:!MD5;

    location / {
        try_files $uri $uri/ =404;
    }

    localtion ~\.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
    }
}
```

Of course we also need to make a symlink to enable it.
Then we restart to see the changes.
```bash
sudo ln -s /etc/nginx/sites-available/https /etc/nginx/sites-enabled/https
sudo systemctl restart nginx
```

You can even delete symlink *default* to our HTTP for security reason (more on this later).

Voila, we have ourselves a HTTPS site (notice the lock at the corner).
![Screenshot](/assets/07-9.png)


### Security Notes

Before ending, I want to say some remarks about security and common pitfalls when setting up such webserver.

#### Not escaping SQL query properly

The absolute first thing when dealing with anything that involved user's input, is to sanitize it.
Otherwise, if you are lucky you would face this scenario.

![Screenshot](/assets/07-10.png)

Worse, a script kiddie can run sqlmap against your server and dump your whole database out.
Or even worse, he can drop a backdoor to your server with MySQL's I/O operations *LOAD_FILE* and *INTO OUTFILE*.
That's how you are pwned.

#### Exposed SQL Server

With default installation, MySQL server is binded to localhost-127.0.0.1 only (see `/etc/mysql/mysql.conf.d/mysqld.conf`).
This means only you and your running webserver can connect to it.
However a common misconfiguration in the wild is the SQL server is binded to 0.0.0.0 instead, which means all interfaces.
This allows attackers can bruteforce password for the server.

#### HTTP and HTTPS

Never ever communicate login information or any other sensitive things in clear text.
Everything supports HTTPS nowadays.
Even though I'm using self-signed cert here for demonstration, but for serious usage, consider using an actual certificate.
I've heard good things about Let's Encrypt but haven't used it personally.
The reason for not using self-signed cert is you are advocating your users to ignore SSL warning, which could lead to much worse things.

Also if you are going to use HTTPS, you should consider dropping support to HTTP.
What I mean is either redirecting to HTTPS page, or not supported at all.
Your users might be vulnerable to MITM + SSLstrip attack if you keep HTTP page.

#### Cryptographic functions

I just want to state again, that you shouldn't use MD5 or SHA1 for hashing passwords.
Use SHA2.
And you should use random salting for even more security.

Also you should consider dropping TLSv1 for HTTPS site.
I know that Nginx default configuration has TLSv1 for SSL for better compability.
TLSv1 is outdated and vulnerable to [POODLE attack since 2014](https://www.cvedetails.com/cve/CVE-2014-3566/).

For absolute security only use TLSv1.2 because TLSv1.1 might also be vulnerable to TLSv1.1.

### The End

Thanks for reading this far.
That's it for this post.
I hope you've learnt something from this.
See you next time.
