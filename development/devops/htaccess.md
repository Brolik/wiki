<!-- TITLE: Htaccess -->
<!-- SUBTITLE: Collection of htaccess snippets -->

# .htaccess
These snippets are useful only on sites running an apache server. More information can be found at https://github.com/h5bp/html5-boilerplate/blob/master/.htaccess

### Proper MIME type for all files

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
