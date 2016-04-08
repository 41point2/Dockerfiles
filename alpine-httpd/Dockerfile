FROM alpine:latest

# We are going to add the testing repository to the repository file in /etc/apk/repositories,
# then get the contents of the repos with apk update, then add Apache with mods/deps, OpenSSL,
# php7 from testing, along with support for Perl, Java web apps and Python (Python3 in this case).

RUN 	echo "@testing http://nl.alpinelinux.org/alpine/edge/testing" >> /etc/apk/repositories && \
	apk update && \
	apk add apache2 apache2-ssl php7-apache2@testing apache2-utils apache2-error apache2-lua \
	apache2-proxy apache2-proxy-html apache2-ldap apache2-icons apache2-webdav apache-mod-fcgid libxml2 \
	apache2-mod-perl@testing python3 && \

# This next lines use Sed to edit httpd.conf and files in /etc/apache2/conf.d. The default installation expects 
# some files to be in different places - it's okay if you just install apache2, but when you start adding 
# modules and their dependencies, their config files expect their dependencies to be in different locations.
# We're going to edit the .conf files to reflect the location in the container, and the move the files from 
# the old ServerRoot in /var/www to /etc/apache2. We're going to leave the DocumentRoot as /var/www/localhost.
# I've chosen to enable a number of modules to give a fully featured install of Apache. You can disable them 
# by removing them from the package list above, or, if they're native modules, commenting out or removing them 
# from the seds below.
# Changes ServerRoot
	sed -i '41s;/var/www;/etc/apache2;' /etc/apache2/httpd.conf && \
# Changes location of mod_cgi.so
	sed -i '154s;lib/apache2;/etc/apache2/modules;' /etc/apache2/httpd.conf && \
# Changes locations of WebDAV modules
	sed -i 's;lib/apache2;/etc/apache2/modules;' /etc/apache2/conf.d/dav.conf && \
# Changes locations of SSL modules
	sed -i 's;lib/apache2;/usr/lib/apache2;' /etc/apache2/conf.d/ssl.conf && \

# Enabling some modules
#mod_slotmem_shm
	sed -i '136s;#LoadModule;LoadModule;' /etc/apache2/httpd.conf && \
#mod_session mod_session_cookies mod_session_dbd mod_dbd mod_auth_form mod_authz_dbd mod_authn_dbd mod_request
	sed -i '79s;#LoadModule;LoadModule;' /etc/apache2/httpd.conf && \
	sed -i '87s;#LoadModule;LoadModule;' /etc/apache2/httpd.conf && \
	sed -i '91s;#LoadModule;LoadModule;' /etc/apache2/httpd.conf && \
	sed -i '103s;#LoadModule;LoadModule;' /etc/apache2/httpd.conf && \
	sed -i '111s;#LoadModule;LoadModule;' /etc/apache2/httpd.conf && \
	sed -i '133s;#LoadModule;LoadModule;' /etc/apache2/httpd.conf && \
	sed -i '134s;#LoadModule;LoadModule;' /etc/apache2/httpd.conf && \
	sed -i '135s;#LoadModule;LoadModule;' /etc/apache2/httpd.conf && \
#mod_cgi mod_actions
	sed -i '154s;#LoadModule;LoadModule;' /etc/apache2/httpd.conf && \
	sed -i '159s;#LoadModule;LoadModule;' /etc/apache2/httpd.conf && \
#mod_speling mod_rewrite
	sed -i '160s;#LoadModule;LoadModule;' /etc/apache2/httpd.conf && \
	sed -i '163s;#LoadModule;LoadModule;' /etc/apache2/httpd.conf && \
#mod_unique_id
	sed -i '129s;#LoadModule;LoadModule;' /etc/apache2/httpd.conf && \
#mod_sed
	sed -i '116s;#LoadModule;LoadModule;' /etc/apache2/httpd.conf && \

# We need to create a symbolic link for libxml2

	ln -s /usr/lib/libxml2.so.2.9.3 /usr/lib/libxml2.so && \

# Now we move all the folders from the old ServerRoot /var/www to the new location in /etc/apache2

	mv /var/www/logs /etc/apache2 && \
	mv /var/www/modules /etc/apache2 && \
	mv /var/www/run /etc/apache2 && \

# Creates /run/apache2 for shared memory to work

	mkdir /run/apache2

# Exposes port 80.

EXPOSE 80

# Starts Apache in the foreground, else the container will terminate.

CMD ["apachectl","-k","start","-DFOREGROUND"]
