<!-- TITLE: Htaccess -->
<!-- SUBTITLE: Collection of htaccess snippets -->

# .htaccess
These snippets are useful only on sites running an apache server. More information can be found at https://github.com/h5bp/html5-boilerplate/blob/master/.htaccess

## Proper MIME type for all files
The Multipurpose Internet Mail Extensions (MIME) type is a standardized way to indicate the nature and format of a document.
Further Reading: https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types

```apache_conf
# ----------------------------------------------------------------------
# Proper MIME type for all files
# ----------------------------------------------------------------------

# JAVASCRIPT
# Normalize to standard type (it's sniffed in IE anyways)
# tools.ietf.org/html/rfc4329#section-7.2
AddType application/javascript         js jsonp
AddType application/json               json

# Audio
AddType audio/ogg                      oga ogg
AddType audio/mp4                      m4a f4a f4b

# Video
AddType video/ogg                      ogv
AddType video/mp4                      mp4 m4v f4v f4p
AddType video/webm                     webm
AddType video/x-flv                    flv

# SVG
# Required for svg webfonts on iPad
# twitter.com/FontSquirrel/status/14855840545
AddType     image/svg+xml              svg svgz
AddEncoding gzip                       svgz

# Webfonts
AddType application/vnd.ms-fontobject  eot
AddType application/x-font-ttf         ttf ttc
AddType font/opentype                  otf
AddType application/x-font-woff        woff

# Assorted types
AddType image/x-icon                        ico
AddType image/webp                          webp
AddType text/cache-manifest                 appcache manifest
AddType text/x-component                    htc
AddType application/xml                     rss atom xml rdf
AddType application/x-chrome-extension      crx
AddType application/x-opera-extension       oex
AddType application/x-xpinstall             xpi
AddType application/octet-stream            safariextz
AddType application/x-web-app-manifest+json webapp
AddType text/x-vcard                        vcf
AddType application/x-shockwave-flash       swf

```

## Gzip Compression
A file format for delivereding compress files to the browser. Improves load time.

```apache_conf
# ----------------------------------------------------------------------
# Gzip compression
# ----------------------------------------------------------------------
AddOutputFilterByType DEFLATE text/plain
AddOutputFilterByType DEFLATE text/html
AddOutputFilterByType DEFLATE text/xml
AddOutputFilterByType DEFLATE text/css
AddOutputFilterByType DEFLATE application/xml
AddOutputFilterByType DEFLATE application/xhtml+xml
AddOutputFilterByType DEFLATE application/rss+xml
AddOutputFilterByType DEFLATE application/javascript
AddOutputFilterByType DEFLATE application/x-javascript

<IfModule mod_deflate.c>

  # Force deflate for mangled headers
  # developer.yahoo.com/blogs/ydn/posts/2010/12/pushing-beyond-gzipping/
  <IfModule mod_setenvif.c>
    <IfModule mod_headers.c>
      SetEnvIfNoCase ^(Accept-EncodXng|X-cept-Encoding|X{15}|~{15}|-{15})$ ^((gzip|deflate)\s*,?\s*)+|[X~-]{4,13}$ HAVE_Accept-Encoding
      RequestHeader append Accept-Encoding "gzip,deflate" env=HAVE_Accept-Encoding
    </IfModule>
  </IfModule>

  <IfModule !mod_filter.c>
    # Legacy versions of Apache
    AddOutputFilterByType DEFLATE text/html text/plain text/css application/json
    AddOutputFilterByType DEFLATE application/javascript
    AddOutputFilterByType DEFLATE text/xml application/xml text/x-component
    AddOutputFilterByType DEFLATE application/xhtml+xml application/rss+xml application/atom+xml
    AddOutputFilterByType DEFLATE image/x-icon image/svg+xml application/vnd.ms-fontobject application/x-font-ttf font/opentype
  </IfModule>

</IfModule>
```

## Expiring Headers
These allow for better cache control. They assume a static file name, and no versioning in the url.

```apache_conf
# ----------------------------------------------------------------------
# Expires headers (for better cache control)
# ----------------------------------------------------------------------

<IfModule mod_expires.c>
	ExpiresActive on

	# Perhaps better to whitelist expires rules? Perhaps.
	ExpiresDefault                          "access plus 1 month"

	# cache.appcache needs re-requests in FF 3.6 (thanks Remy ~Introducing HTML5)
	ExpiresByType text/cache-manifest       "access plus 0 seconds"

	# Your document html
	ExpiresByType text/html                 "access plus 0 seconds"

	# Data
	ExpiresByType text/xml                  "access plus 0 seconds"
	ExpiresByType application/xml           "access plus 0 seconds"
	ExpiresByType application/json          "access plus 0 seconds"

	# Feed
	ExpiresByType application/rss+xml       "access plus 1 hour"
	ExpiresByType application/atom+xml      "access plus 1 hour"

	# Favicon (cannot be renamed)
	ExpiresByType image/x-icon              "access plus 1 week"

	# Media: images, video, audio
	ExpiresByType image/gif                 "access plus 1 month"
	ExpiresByType image/png                 "access plus 1 month"
	ExpiresByType image/jpeg                "access plus 1 month"
	ExpiresByType video/ogg                 "access plus 1 month"
	ExpiresByType audio/ogg                 "access plus 1 month"
	ExpiresByType video/mp4                 "access plus 1 month"
	ExpiresByType video/webm                "access plus 1 month"

	# HTC files  (css3pie)
	ExpiresByType text/x-component          "access plus 1 month"

	# Webfonts
	ExpiresByType application/x-font-ttf    "access plus 1 month"
	ExpiresByType font/opentype             "access plus 1 month"
	ExpiresByType application/x-font-woff   "access plus 1 month"
	ExpiresByType image/svg+xml             "access plus 1 month"
	ExpiresByType application/vnd.ms-fontobject "access plus 1 month"

	# CSS and JavaScript
	ExpiresByType text/css                  "access plus 1 week"
	ExpiresByType application/javascript    "access plus 1 week"

</IfModule>

```

## Rewrite Engine
Turning the rewrite engine on is necessary for any rules and redirects to work. Every snippet listed below this point requires the rewrite engine to be turned on.

```apache_conf
# ----------------------------------------------------------------------
# Start rewrite engine
# ----------------------------------------------------------------------

<IfModule mod_rewrite.c>
	 Options +FollowSymlinks
     RewriteEngine On
	 RewriteBase /
</IfModule>
```

## No WWW
Removes the "www." from the beginning of a URL

```apache_conf
# ----------------------------------------------------------------------
# Remove "www." from beginning of URLs
# http://stackoverflow.com/questions/234723/generic-htaccess-redirect-www-to-non-www
# ----------------------------------------------------------------------

<IfModule mod_rewrite.c>
	RewriteCond %{HTTP_HOST} ^www\.(.*)$ [NC]
	RewriteRule ^(.*)$ http://%1/$1 [R=301,L]
</IfModule>
```

## Force WWW
Forces the "www." to the beginning of a URL

```apache_conf
# ----------------------------------------------------------------------
# Force "www." to beginning of URLs
# http://stackoverflow.com/questions/4916222/htaccess-how-to-force-www-in-a-generic-way
# ----------------------------------------------------------------------

# If using this, make sure the "Remove www. from beginning of URLS" block is commented out

<IfModule mod_rewrite.c>
    RewriteCond %{HTTP_HOST} !^$
    RewriteCond %{HTTP_HOST} !^www\. [NC]
    RewriteCond %{HTTPS}s ^on(s)|
    RewriteRule ^ http%1://www.%{HTTP_HOST}%{REQUEST_URI} [R=301,L]
</IfModule>
```

## Force HTTPS
Forces HTTPS, excludes local and dev environments by subdomain.

```apache_conf
# ----------------------------------------------------------------------
# Force https:// for the Control Panel
# Exclude our local and  development environment if we don't have ssl setup there
# ----------------------------------------------------------------------

RewriteCond %{HTTPS} !=on
RewriteCond %{HTTP_HOST} !^local\.(.*)$ [NC]
RewriteCond %{HTTP_HOST} !^dev\.(.*)$ [NC]
RewriteRule ^(admin/.*)$ https://%{SERVER_NAME}/$1 [R=301,L]
```

## Remove Trailing Slash
Always remove the trailing slash. Looks cleaner, and makes a query string easier

```apache_conf
# ------------------------------------------------------------
# Remove trailing slashes from the end of the URL
# This rule must come before removal of index.php from the URL
# ------------------------------------------------------------

RewriteCond %{REQUEST_METHOD} !=POST
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.+)/$ /$1 [R=301,L]
```

## Security
These provide different security enhancements for the site. Each block can be added independently of the others.

```apache_conf
# ----------------------------------------------------------------------
# A little more security
# ----------------------------------------------------------------------

# Do we want to advertise the exact version number of Apache we're running?
# Probably not.
# This can only be enabled if used in httpd.conf - It will not work in .htaccess
# ServerTokens Prod

# "-Indexes" will have Apache block users from browsing folders without a default document
# Usually you should leave this activated, because you shouldn't allow everybody to surf through
# every folder on your server (which includes rather private places like CMS system folders).
<IfModule mod_autoindex.c>
  Options -Indexes
</IfModule>

# Block access to "hidden" directories or files whose names begin with a period. This
# includes directories used by version control systems such as Subversion or Git.
<IfModule mod_rewrite.c>
  RewriteCond %{SCRIPT_FILENAME} -d [OR]
  RewriteCond %{SCRIPT_FILENAME} -f
  RewriteRule "(^|/)\." - [F]
</IfModule>

# Block access to backup and source files
# These files may be left by some text/html editors and
# pose a great security danger, when anyone can access them
<FilesMatch "(\.(bak|config|sql|fla|psd|ini|log|sh|inc|swp|dist)|~)$">
  Order allow,deny
  Deny from all
  Satisfy All
</FilesMatch>

# Increase cookie security
<IfModule php5_module>
  php_value session.cookie_httponly true
</IfModule>

# Hide .git and _svn folders so they cannot be accessed
# But you shouldn't upload them in the first place
<IfModule mod_rewrite.c>
	RewriteRule ^(.*/)*_svn/ / [F,L]
	RewriteRule ^(.*/)*\.git/ / [F,L]
</IfModule>
```