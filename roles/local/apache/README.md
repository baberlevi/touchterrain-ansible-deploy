### Global

# Ansible Role: Apache
An Ansible Role that installs and configures Apache 2.4 on Red Hat and CentOS 7.

## Requirements
None.

## Installation
git clone https://git.linux.iastate.edu/las-it/apache.git

## Role Variables

The following variable array is set as default configuration for vsftpd for default configuration at defaults/main.yml

The config template uses a key value pair for setting configurations. Please add or edit configurations to file default/main.yml.

Your own apache parameters can be used in these configuration files.

```yaml
apache_install_packages:
  - "httpd"
  - "php-ldap"
  - "php-mbstring"
  - "php-gd"
  - "php-ldap"
  - "mod_ssl"

apache_configuration_path: "/etc/httpd/conf"
apache_vhost_sites_enabled_path: "/etc/httpd/sites-enabled"
apache_vhost_sites_available_path: "/etc/httpd/sites-available"
apache_daemon: "httpd"

apache_conf_httpd:
  ServerRoot: "/etc/httpd"
  Listen: "80"
  Include: "conf.modules.d/*.conf"
  User: "apache"
  Group: "apache"
  ServerAdmin: "las-webadmin@iastate.edu"
  - Directory: "/"
    AllowOverride: "none"
    Require: "all denied"
  DocumentRoot: "/var/www/html"
  - Directory: "/var/www"
    AllowOverride: "none"
    Require: "all granted"
  - Directory: "/var/www/html"
    Options: "Indexes FollowSymLinks"
    AllowOverride: "None"
    Require: "all granted"
  - IfModule: "dir_module"
    DirectoryIndex: "index.html"
  - Files: ".ht*"
    Require: "all denied"
  ErrorLog: "logs/error_log"
  LogLevel: "warn"
  - IfModule: "log_config_module"
    LogFormat: '"%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined'
    LogFormat: '"%h %l %u %t \"%r\" %>s %b" common'
    - IfModule: "logio_module"
      LogFormat: '"%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\" %I %O" combinedio'
      CustomLog: '"logs/access_log" combined'
  - IfModule: "alias_module"
    ScriptAlias: '/cgi-bin/ "/var/www/cgi-bin/"'
  - Directory: '"/var/www/cgi-bin"'
    AllowOverride: "None"
    Options: "None"
    Require: "all granted"
  - IfModule: "mime_module"
    TypesConfig: "/etc/mime.types"
    AddType: "application/x-compress .Z"
    AddType: "application/x-gzip .gz .tgz"
    AddType: "text/html .shtml"
    AddOutputFilter: "INCLUDES .shtml"
  AddDefaultcharset: "UTF-8"
  - IfModule: "mime_magic_module"
    MIMEMagicFile: "conf/magic"
  EnableSendfile: "on"
  IncludeOptional: "conf.d/*.conf"
  IncludeOptional: "sites-enabled/*.conf"

apache_conf_vhosts:
  - name: "local.dev.conf"
    ServerName: "local.dev"
    DocumentRoot: "/var/www/html"
    ServerAdmin: "test@example.com"
    port: "80"
    - Directory: "/var/www/html"
      Require: "all granted"
      AllowOverride: "all"

apache_confdir:
  - name: "autoindex.conf"
    IndexOptions: "FancyIndexing HTMLTable VersionSort"
    Alias: '/icons/ "/usr/share/httpd/icons"'
    - Directory: '"/usr/share/httpd/icons"'
      Options: "Indexes MultiViews FollowSymlinks"
      AllowOverride: "None"
      Require: "all granted"
    AddIconByEncoding: "(CMP,/icons/compressed.gif) x-compress x-gzip"
    AddIconByType: "(TXT,/icons/text.gif) text/*"
    AddIconByType: "(IMG,/icons/image2.gif) image/*"
    AddIconByType: "(SND,/icons/sound2.gif) audio/*"
    AddIconByType: "(VID,/icons/movie.gif) video/*"
    AddIcon: "/icons/binary.gif .bin .exe"
    AddIcon: "/icons/binhex.gif .hqx"
    AddIcon: "/icons/tar.gif .tar"
    AddIcon: "/icons/world2.gif .wrl .wrl.gz .vrml .vrm .iv"
    AddIcon: "/icons/compressed.gif .Z .z .tgz .gz .zip"
    AddIcon: "/icons/a.gif .ps .ai .eps"
    AddIcon: "/icons/layout.gif .html .shtml .htm .pdf"
    AddIcon: "/icons/text.gif .txt"
    AddIcon: "/icons/c.gif .c"
    AddIcon: "/icons/p.gif .pl .py"
    AddIcon: "/icons/f.gif .for"
    AddIcon: "/icons/dvi.gif .dvi"
    AddIcon: "/icons/uuencoded.gif .uu"
    AddIcon: "/icons/script.gif .conf .sh .shar .csh .ksh .tcl"
    AddIcon: "/icons/tex.gif .tex"
    AddIcon: "/icons/bomb.gif core."
    AddIcon: "/icons/back.gif .."
    AddIcon: "/icons/hand.right.gif README"
    AddIcon: "/icons/folder.gif ^^DIRECTORY^^"
    AddIcon: "/icons/blank.gif ^^BLANKICON^^"
    DefaultIcon: "/icons/unknown.gif"
    ReadmeName: "README.html"
    HeaderName: "HEADER.html"
    IndexIgnore: ".??* *~ *# HEADER* README* RCS CVS *,v *,t"

  - name: "php.conf"
    AddType: "text/html .php"
    DirectoryIndex: "index.php"
    PHP_Option: "php_value session.save_handler 'files'"
    PHP_Option: "php_value session.save_path '/var/lib/php/session'"
    - FilesMatch: "\.php$"
      SetHandler: "application/x-httpd-php"

  - name: "phpMyAdmin.conf"
    Alias: "/phpMyAdmin /usr/share/phpMyAdmin"
    Alias: "/phpmyadmin /usr/share/phpMyAdmin"
    - Directory: "/usr/share/phpMyAdmin"
      AddDefaultCharset: "UTF-8"
      - IfModule: "mod_authz_core.c"
        - RequireAny:
          Require: "ip 10.24.78.64"
          Require: "ip 127.0.0.1"
          Require: "ip ::1"
      - IfModule: "!mod_authz_core.c"
        Order: "Deny, Allow"
        Deny: "from All"
        Allow: "from 127.0.0.1"
        Allow: "from ::1"
    - Directory: "/usr/share/phpMyAdmin/setup"
      - IfModule: "mod_authz_core.c"
        - RequireAny:
          Require: "ip 10.24.78.64"
          Require: "ip 127.0.0.1"
          Require: "ip ::1"
      - IfModule: "!mod_authz_core.c"
        Order: "Deny, Allow"
        Deny: "from All"
        Allow: "from 127.0.01"
        Allow: "from ::1"
    - Directory: "/usr/share/phpMyAdmin/libraries/"
      Order: "Deny, Allow"
      Deny: "from All"
      Allow: "from None"
    - Directory: "/usr/share/phpMyAdmin/setup/lib/"
      Order: "Deny, Allow"
      Deny: "from All"
      Allow: "from None"
    - Directory: "/usr/share/phpMyAdmin/setup/frames/"
      Order: "Deny, Allow"
      Deny: "from All"
      Allow: "from None"

  - name: "shibd.conf"
    LoadModule: "mod_shib /usr/lib64/shibboleth/mod_shib_24.so"
    ShibCompatValidUser: "Off"
    - Location: "/Shibboleth.sso"
      AuthType: "None"
      Require: "all granted"
    - IfModule: "mod_alias.c"
      - Location: "/shibboleth-sp"
        AuthType: "None"
        Require: "all granted"
      Alias: "/shibboleth-sp/main.css /usr/share/shibboleth/main.css"
    - Location: "/secure"
      AuthType: "shibboleth"
      ShibRequestSetting: "requireSession 1"
      Require: "shib-session"

  - name: "ssl.conf"
    Listen: "443 https"
    SSLPassPhraseDialog: "exec:/usr/libexec/httpd-ssl-pass-dialog"
    SSLSessionCache: "shmcb:/run/httpd/sslcache(512000)"
    SSLSessionCacheTimeout: "300"
    SSLRandomSeed: "startup file:/dev/urandom 256"
    SSLRandomSeed: "connect builtin"
    SSLCryptoDevice: "builtin"

  - name: "userdir.conf"
    - IfModule: "mod_userdir.c"
      UserDir: "disabled"
    - Directory: "/home/*/public_html"
      AllowOverride: "FileInfo AuthConfig Limit Indexes"
      Options: "MultiViews Indexes SymLinksIfOwnerMatch IncludesNoExec"
      Require: "method GET POST OPTIONS"

  - name: "welcome.conf"
    Alias: "/.noindex.html /usr/share/httpd/noindex/index.html"
    - LocationMatch: '"^/+$"'
      Options: "-Indexes"
      ErrorDocument: "403 /.noindex.html"
    - Directory: "/usr/share/httpd/noindex"
      AllowOverride: "None"
      Require: "all granted"

```
