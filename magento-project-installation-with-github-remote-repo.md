1. Command for dumping databse into a database file <db1-name_write-date-here-for-organization.mysql>

Command:
mysqldump -u <msql-username> -p <db-name-to-copy-from>

Example:
mysqldump -u magento2 -p digiplus

2. Login into mysql in a new terminal instance.

Inside mysql > Create New Database for your Project > Then run the following command to copy the database
contents from the existing '<db1-name_write-date-here-for-organization.mysql>' database to the newly created database :

Command:
source <filename.sql>

3. Git clone the remote repository on your instance. (Typically it would be /var/www/html/<your-project-name>)

Command:
git clone <github ssh repo link> <your-project-directory-name>

Example (assuming we are in /var/www/html/ ):
git clone git@github.com:vidyasagarmukkavili-hbwsl/BlueMA.git blueMa

# Known issue: Make sure your directory has the owner set as the actual owner with whom the SSH keys are

registered with on GitHub;
Because while trying to clone using as root/sudo/su, the SSH keys may not match as they may have
been generated without sudo permissions by a different user such as a user.

4. After cloning the files successfully, configure the hosts, nginx and database to point to the correct domain:

   4.1 /etc/hosts should point to the correct location
   4.2 /etc/ngin-x/sites-available should have a configuration for the port.
   Configure/Comment out the FastCGI part with #
   4.3 Make symlinks for the same in sites-enabled using the following command:
   ln -s /etc/nginx/sites-available/<your-site-configuration-file-name> /etc/nginx/sites-enabled

   nginx -t
   ^ to check if it is configured properly

   systemctl restart nginx
   ^ to restart the nginx service

   4.4 Update the core_config_data in the database with the new baseurl for the website using sql commands
   with the update set quierries :

   Command Example:

   update core_config_data
   set value='http://bluema.magento.com/'
   where path='web/unsecure/base_url';

   Change the set value to the correct domain and subdomains.

5. Connect the database to the git repository:

Open the project directory in code and copy the '<magento-project-instance>/etc/config.php' and
'<magento-project-instance>/etc/env.php' from another working instance of magento project which was
manually installed with the setup commands.(preferrably from the original installation working directory)

or get it from here ->

6. Almost there! Dont forget to give all the relevant permissions to make it work:

sudo find var generated vendor pub/static pub/media app/etc -type f -exec chmod g+w {} +
sudo find var generated vendor pub/static pub/media app/etc -type d -exec chmod g+ws {} +
sudo chown -R vidya_sagar_mukkavili:www-data .
sudo chmod u+x bin/magento

sudo chmod -R 777 var pub/static generated generated/

---

If everything is done correctly, setup is now done.

---

Deploy to test the setup:

- Production Mode Deploy:

sudo php bin/magento deploy:mode:set production

sudo php bin/magento cache:flush

[
Make sure to remove maintainance.flag file in var folder in case of any issue with loading, like

Magento The server is temporarily unable to service your request due to maintenance downtime or capacity problems. Please try again later
]

sudo php bin/magento indexer:reindex
sudo php bin/magento setup:upgrade
sudo php bin/magento setup:static-content:deploy -f
sudo php bin/magento cache:flush

---

- Developer Mode Deploy:

sudo php bin/magento deploy:mode:set developer
