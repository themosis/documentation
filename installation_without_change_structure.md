Installation without change WordPress structure
============

1. Install clean WordPress
2. Intsall themosis using composer
3. Move themosis files
4. Initialize themosis plugin
5. Set folder premissions
6. WordPress setup


1. Install clean WordPress:
----------------

It is not necessary but for example and exclude conflicts with other plugins i want to install a clean WordPres. 

```bash
cd "your web site folder"
curl -O https://wordpress.org/latest.zip
unzip latest.zip
mv wordpress/* .
rm latest.zip
rm -rf wordpress
```

2. Intsall themosis in temporary folder using composer
-------------------

Create and go to your temporary folder:

```bash
mkdir tmp
cd tmp
```

Open your `Terminal` or `Console` and execute the following command:

```bash
composer create-project themosis/themosis my-project-name
```

This will create a directory called `my-project-name` on your system and automatically download the latest WordPress version along with the latest Themosis framework version and its dependencies.

3. Move themosis files
-----------------------------

Move themosis themosis-framework int to  wordpress/wp-content/plugins and themosis-theme in to  wordpress/wp-content/themes.

4. Initialize themosis plugin
--------------------

Initialize themosis-framework using composer. Go to themosis-framework folder and open your `Terminal` or `Console` and execute the following command:

```bash
composer install
```

5. Set folder premissions
--------------------

Go to the wp-content/themes/themosis-theme/app folder and set 777 premissions to storage folder.
```bash
cd "your web site folder"
cd wp-content/themes/themosis-theme/app
chmod -R 777 storage
```

6. WordPress setup
--------------------

Install the WordPress. Active themosis plugin and theme.



<a href="http://www.youtube.com/watch?feature=player_embedded&v=7wqpr6S8b5I
" target="_blank"><img src="http://img.youtube.com/vi/7wqpr6S8b5I/0.jpg" 
alt="Installation without change WordPress structure" width="240" height="180" border="10" /></a>

THE END.