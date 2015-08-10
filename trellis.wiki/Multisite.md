# Multisite

(If you are encountering trouble with multisite, you may want to monitor [#266](https://github.com/roots/trellis/issues/266).)

Trellis assumes your WordPress configuration already has multisite set up. If not, ensure the following values are placed somewhere in `wp-config.php` (or `config/application.php` if you're using [Bedrock](https://roots.io/bedrock/)) *before running the `wordpress-sites` role*:

```php
/* Multisite */
define('WP_ALLOW_MULTISITE', true);
define('MULTISITE', true);
define('SUBDOMAIN_INSTALL', true); // Set to false if using subdirectories
define('DOMAIN_CURRENT_SITE', getenv('DOMAIN_CURRENT_SITE'));
define('PATH_CURRENT_SITE', '/');
define('SITE_ID_CURRENT_SITE', 1);
define('BLOG_ID_CURRENT_SITE', 1);
```

You'll also need to open the environment files in the `group_vars/` directory and update the multisite settings:

```yaml
multisite:
  enabled: true
  subdomain: false   # Set to true if you're using a subdomain multisite install
```

And set the `domain_current_site` variable under `env`:

```yaml
env:
  domain_current_site: example.com
```

## Subdomain installs and hosts

Install the [Landrush](https://github.com/phinze/landrush) Vagrant plugin:

```
vagrant plugin install landrush
```

Landrush spins up a small DNS server that allows us to use wildcard subdomains, a requirement for subdomain multisite installs.

Make the following changes to your `Vagrantfile`:

```diff
+ PRIVATE_IP = '192.168.50.5'
```

```diff
-  config.vm.network :private_network, ip: '192.168.50.5'
+  config.vm.network :private_network, ip: PRIVATE_IP
```

```diff
-  if Vagrant.has_plugin? 'vagrant-hostsupdater'
-    config.hostsupdater.aliases = aliases
+  if Vagrant.has_plugin? 'landrush'
+    config.landrush.enabled = true
+    config.landrush.tld = config.vm.hostname
+
+    aliases.each do |host|
+      config.landrush.host host, PRIVATE_IP
+    end
   else
-    puts 'vagrant-hostsupdater missing, please install the plugin:'
-    puts 'vagrant plugin install vagrant-hostsupdater'
+    puts 'landrush missing, please install the plugin:'
+    puts 'vagrant plugin install landrush'
   end
```

### Debugging Landrush

If something goes wrong with Landrush such as not being able to resolve a
website from the guest:

```
vagrant landrush list
```

And remove any extraneous entries and try again.
