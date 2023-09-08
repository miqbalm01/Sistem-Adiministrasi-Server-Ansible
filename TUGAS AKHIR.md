## TUGAS AKHIR

#### Grahito Ardani B	(1202199007) || M. Iqbal Maulana	(1202190023)

#### IT 02-02

------

##### Membuat LXC

LXC yang kita perlukan adalah :

* 6 LXC ubuntu 20.04 PHP 7.4
* 2 LXC debian 10 PHP 5.6
* 1 LXC debian 10 mariadb server

kita buat dulu LXC php5 dengan nama lxc_php5.2_1 dan lxc_php5.2_2 dan set IP masing -  masing lxc.

![1](https://user-images.githubusercontent.com/93030868/151694474-87271093-f9a0-4b44-86c8-3cde519a00e3.png)

lalu kita set codeigniter untuk lxc_php5_1 dan lxc_php5_2 dengan ip yang telah kita atur

![2](https://user-images.githubusercontent.com/93030868/151694475-2eab748b-623a-47e8-864c-d6fd178ba2ae.png)

lalu kita buat Ansible codeiginiter dengan nama deploy-app.yml yang berisi domain container yang kita gunakan
```markdown
- hosts: ci
  vars:
    git_url: 'https://github.com/aldonesia/sas-ci'
    destdir: '/var/www/html/ci'
    domain: 'lxc_php5_1.dev'
    domain: 'lxc_php5_2.dev'
  roles:
    - app
```

![3](https://user-images.githubusercontent.com/93030868/151694476-f6a99c8e-25ea-4d8f-ba72-58da3ebfc4e4.png)

jalankan Ansible nya

![4](https://user-images.githubusercontent.com/93030868/151694478-d84201d4-75ec-4c21-9c55-c110577d8ae3.png)

Buat direktori Handlers, tasks dan templates
Setting app/hendlers yang berfungsi untuk restart

![5](https://user-images.githubusercontent.com/93030868/151694479-f67f565d-3662-4f2c-b139-459481e1a842.png)

app/tasks
```markdown
---
- name: delete apt chache
  become: yes
  become_user: root
  become_method: su
  command: rm -vf /var/lib/apt/lists/*

- name: install requirement dpkg to install php5
  become: yes
  become_user: root
  become_method: su
  apt: name={{ item }} state=latest update_cache=true
  with_items:
    - ca-certificates
    - apt-transport-https
    - wget
    - curl
    - python-apt
    - software-properties-common
    - git

- name: Add key
  apt_key:
    url: https://packages.sury.org/php/apt.gpg
    state: present

- name: Add Php Repository
  apt_repository:
      repo: "deb https://packages.sury.org/php/ stretch main"
      state: present
      filename: php.list
      update_cache: no

- name: install nginx php5
  become: yes
  become_user: root
  become_method: su
  apt: name={{ item }} state=latest update_cache=true
  with_items:
    - nginx
    - nginx-extras
    - php5.6
    - php5.6-fpm
    - php5.6-common
    - php5.6-cli
    - php5.6-curl
    - php5.6-mbstring
    - php5.6-mysqlnd
    - php5.6-xml

- name: Git clone repo sas-ci
  become: yes
  git:
    repo: '{{ git_url }}'
    dest: "{{ destdir }}"

- name: Copy app.conf
  template:
    src=templates/app.conf
    dest=/etc/nginx/sites-available/{{ domain }}
  vars:
    servername: '{{ domain }}'

- name: Delete another nginx config
  become: yes
  become_user: root
  become_method: su
  command: rm -f /etc/nginx/sites-enabled/*

- name: Symlink app.conf
  command: ln -sfn /etc/nginx/sites-available/{{ domain }} /etc/nginx/sites-enabled/{{ domain }}
  notify:
    - restart nginx

- name: Write {{ domain }} to /etc/hosts
  lineinfile:
    dest: /etc/hosts
    regexp: '.*{{ domain }}$'
    line: "127.0.0.1 {{ domain }}"
    state: present
```

![6](https://user-images.githubusercontent.com/93030868/151694480-2fe2ff58-5fda-496d-9690-97ff31a1db65.png)

app/templates
```markdown
server {
  listen 80;
  server_name {{servername}};
  root {{ destdir }};
  index index.php;
  location / {
     try_files $uri $uri/ /index.php?$query_string;
  }
  location ~ \.php$ {
     fastcgi_pass unix:/run/php/php5.6-fpm.sock;  #Sesuaikan dengan versi PHP
     fastcgi_index index.php;
     fastcgi_param SCRIPT_FILENAME {{ destdir }}$fastcgi_script_name;
     include fastcgi_params;
  }
}
```

![7](https://user-images.githubusercontent.com/93030868/151694481-c132947b-b206-4e1e-a974-0c3b77667b31.png)

lalu kita jalankan http://kelompok11.fpsas/app

![8](https://user-images.githubusercontent.com/93030868/151694482-a4bba7ca-4916-4440-a9bf-e858d864fbbc.png)

kita buat 6 LXC php7 yang berisi Laravel,YII dan Wordpress lalu set ip masing-masing LXC

![9](https://user-images.githubusercontent.com/93030868/151694483-56d273a2-6061-4669-b2e1-fe871c9264a7.png)

sebelum melanjutkan cek dulu versi ubuntu dan debian yang kita gunakan apakah sesuai yang diminta yaitu menggunakan ubuntu 20 dan debian 10.

```markdown
lsb_release -a
```

![10](https://user-images.githubusercontent.com/93030868/151694485-ca9b812e-63d7-449c-a286-1aba20c62abe.png))

###### Laravel

Set domain pada Laravel dengan membuat install-laravel.yml
```markdown
- hosts: laravel
  vars:
    username: 'iqbal'
    password: 'iqbal'
    domain: "lxc_php7_2.dev"
    domain: "lxc_php7_3.dev"
    domain: "lxc_php7_4.dev"
    domain: "lxc_php7_5.dev"
  roles:
    - php
    - lv
```

![11](https://user-images.githubusercontent.com/93030868/151694486-f45ea359-dc5f-4c0d-81a4-7b4c6298f5ae.png)

kita lakukan set pada php tasks 
```markdown
---
- name: delete apt chache
  become: yes
  become_user: root
  become_method: su
  command: rm -vf /var/lib/apt/lists/*

- name: install php
  become: yes
  become_user: root
  become_method: su
  apt: name={{ item }} state=latest update_cache=true
  with_items:
    - gtkhash
    - crack-md5
    - git
    - curl
    - nginx
    - nginx-extras
    - php7.4
    - php7.4-fpm
    - php7.4-curl
    - php7.4-xml
    - php7.4-gd
    - php7.4-opcache
    - php7.4-mbstring
    - php7.4-zip
    - php7.4-json
    - php7.4-cli

- name: enable module php mbstring
  command: phpenmod mbstring
  notify:
    - restart php
```

![12](https://user-images.githubusercontent.com/93030868/151694487-c81c3cdf-7419-4cd3-9b60-e57fe92baca3.png)

php handlers
```markdown
---
- name: restart php
  become: yes
  become_user: root
  become_method: su
  action: service name=php7.4-fpm state=restarted

- name: restart nginx
  become: yes
  become_user: root
  become_method: su
  action: service name=nginx state=restarted
```

![13](https://user-images.githubusercontent.com/93030868/151694489-c7c72a90-2303-4f92-aa72-601ad7e4452f.png)

lakukan juga pada Laravel

laravel handlers

![14](https://user-images.githubusercontent.com/93030868/151694490-93d227ad-0984-4cf4-b9b2-fbd5dfbe9578.png)

laravel Tasks
```markdown
---
- name: delete apt chache
  become: yes
  become_user: root
  become_method: su
  command: rm -vf /var/lib/apt/lists/*

- name: Download and install Composer
  shell: curl -sS https://getcomposer.org/installer | php
  args:
    chdir: /usr/src/
    creates: /usr/local/bin/composer
    warn: false
  become: yes

- name: Add Composer to global path
  copy:
    dest: /usr/local/bin/composer
    group: root
    mode: '0755'
    owner: root
    src: /usr/src/composer.phar
    remote_src: yes
  become: yes

- name: Ansible delete file create-project
  file:
    path: /var/www/html/lara
    state: absent

- name: composer create-project
  shell: /usr/local/bin/composer create-project laravel/laravel /var/www/html/lara --prefer-dist --no-interaction

- name: Copy .env.template
  template:
    src=templates/env.template
    dest=/var/www/html/lara/.env

- name: composer
  shell: cd /var/www/html/lara; /usr/local/bin/composer install  --no-interaction

- name: key
  shell: /usr/bin/php7.4 /var/www/html/lara/artisan key:generate

- name: chmod
  become: yes
  become_user: root
  become_method: su
  command: chmod 777 -R /var/www/html/lara/storage

- name: Copy lv.conf
  template:
    src=templates/lv.conf
    dest=/etc/nginx/sites-available/{{ domain }}
  vars:
    servername: '{{ domain }}'

- name: copy php7.conf
  template:
    src=templates/php7.conf
    dest=/etc/php/7.4/fpm/pool.d/www.conf

- name: Symlink lv.conf
  command: ln -sfn /etc/nginx/sites-available/{{ domain }} /etc/nginx/sites-enabled/{{ domain }}
  notify:
    - restart nginx

- name: Write {{ domain }} to /etc/hosts
  lineinfile:
    dest: /etc/hosts
    regexp: '.*{{ domain }}$'
    line: "127.0.0.1 {{ domain }}"
    state: present


```

![15](https://user-images.githubusercontent.com/93030868/151694491-fc350a6c-d5a2-4c4d-81de-642d3ba88852.png)

laravel env.templates
```markdown
APP_NAME=Laravel
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://kelompok11.fpsas

LOG_CHANNEL=stack
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=10.0.3.50
DB_PORT=3306
DB_DATABASE=lara_db
DB_USERNAME=iqbal
DB_PASSWORD=iqbal

BROADCAST_DRIVER=log
CACHE_DRIVER=file
FILESYSTEM_DRIVER=local
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120

MEMCACHED_HOST=127.0.0.1

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=mailhog
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS=null
MAIL_FROM_NAME="${APP_NAME}"

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=
AWS_USE_PATH_STYLE_ENDPOINT=false

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
PUSHER_APP_CLUSTER=mt1

MIX_PUSHER_APP_KEY="${PUSHER_APP_KEY}"
MIX_PUSHER_APP_CLUSTER="${PUSHER_APP_CLUSTER}"
```

![16](https://user-images.githubusercontent.com/93030868/151694492-bf585ef3-2dec-4793-9f42-c32342d41a3f.png)

laravel roles/lv/templates/php7.conf
```markdown
; Start a new pool named 'www'.
; the variable $pool can we used in any directive and will be replaced by the
; pool name ('www' here)
[www]

; Per pool prefix
; It only applies on the following directives:
; - 'slowlog'
; - 'listen' (unixsocket)
; - 'chroot'
; - 'chdir'
; - 'php_values'
; - 'php_admin_values'
; When not set, the global prefix (or /usr) applies instead.
; Note: This directive can also be relative to the global prefix.
; Default Value: none
;prefix = /path/to/pools/$pool

; Unix user/group of processes
; Note: The user is mandatory. If the group is not set, the default user's group
;       will be used.
user = www-data
group = www-data

; The address on which to accept FastCGI requests.
; Valid syntaxes are:
;   'ip.add.re.ss:port'    - to listen on a TCP socket to a specific address on
;                            a specific port;
;   'port'                 - to listen on a TCP socket to all addresses on a
;                            specific port;
;   '/path/to/unix/socket' - to listen on a unix socket.
; Note: This value is mandatory.
listen = 127.0.0.1:9001

; Set listen(2) backlog. A value of '-1' means unlimited.
; Default Value: 128 (-1 on FreeBSD and OpenBSD)
;listen.backlog = -1

; Set permissions for unix socket, if one is used. In Linux, read/write
; permissions must be set in order to allow connections from a web server. Many
; BSD-derived systems allow connections regardless of permissions.
; Default Values: user and group are set as the running user
;                 mode is set to 0666
listen.owner = www-data
listen.group = www-data
listen.mode = 0666

; List of ipv4 addresses of FastCGI clients which are allowed to connect.
; Equivalent to the FCGI_WEB_SERVER_ADDRS environment variable in the original
; PHP FCGI (5.2.2+). Makes sense only with a tcp listening socket. Each address
; must be separated by a comma. If this value is left blank, connections will be
; accepted from any ip address.
; Default Value: any
;listen.allowed_clients = 127.0.0.1

; Choose how the process manager will control the number of child processes.
; Possible Values:
;   static  - a fixed number (pm.max_children) of child processes;
;   dynamic - the number of child processes are set dynamically based on the
;             following directives. With this process management, there will be
;             always at least 1 children.
;             pm.max_children      - the maximum number of children that can
;                                    be alive at the same time.
;             pm.start_servers     - the number of children created on startup.
;             pm.min_spare_servers - the minimum number of children in 'idle'
;                                    state (waiting to process). If the number
;                                    of 'idle' processes is less than this
;                                    number then some children will be created.
;             pm.max_spare_servers - the maximum number of children in 'idle'
;                                    state (waiting to process). If the number
;                                    of 'idle' processes is greater than this
;                                    number then some children will be killed.
;  ondemand - no children are created at startup. Children will be forked when
;             new requests will connect. The following parameter are used:
;             pm.max_children           - the maximum number of children that
;                                         can be alive at the same time.
;             pm.process_idle_timeout   - The number of seconds after which
;                                         an idle process will be killed.
; Note: This value is mandatory.
pm = dynamic

; The number of child processes to be created when pm is set to 'static' and the
; maximum number of child processes when pm is set to 'dynamic' or 'ondemand'.
; This value sets the limit on the number of simultaneous requests that will be
; served. Equivalent to the ApacheMaxClients directive with mpm_prefork.
; Equivalent to the PHP_FCGI_CHILDREN environment variable in the original PHP
; CGI. The below defaults are based on a server without much resources. Don't
; forget to tweak pm.* to fit your needs.
; Note: Used when pm is set to 'static', 'dynamic' or 'ondemand'
; Note: This value is mandatory.
pm.max_children = 5

; The number of child processes created on startup.
; Note: Used only when pm is set to 'dynamic'
; Default Value: min_spare_servers + (max_spare_servers - min_spare_servers) / 2
pm.start_servers = 2

; The desired minimum number of idle server processes.
; Note: Used only when pm is set to 'dynamic'
; Note: Mandatory when pm is set to 'dynamic'
pm.min_spare_servers = 1

; The desired maximum number of idle server processes.
; Note: Used only when pm is set to 'dynamic'
; Note: Mandatory when pm is set to 'dynamic'
pm.max_spare_servers = 3

; The number of seconds after which an idle process will be killed.
; Note: Used only when pm is set to 'ondemand'
; Default Value: 10s
;pm.process_idle_timeout = 10s;

; The number of requests each child process should execute before respawning.
; This can be useful to work around memory leaks in 3rd party libraries. For
; endless request processing specify '0'. Equivalent to PHP_FCGI_MAX_REQUESTS.
; Default Value: 0
pm.max_requests = 100

; The URI to view the FPM status page. If this value is not set, no URI will be
; recognized as a status page. It shows the following informations:
;   pool                 - the name of the pool;
;   process manager      - static, dynamic or ondemand;
;   start time           - the date and time FPM has started;
;   start since          - number of seconds since FPM has started;
;   accepted conn        - the number of request accepted by the pool;
;   listen queue         - the number of request in the queue of pending
;                          connections (see backlog in listen(2));
;   max listen queue     - the maximum number of requests in the queue
;                          of pending connections since FPM has started;
;   listen queue len     - the size of the socket queue of pending connections;
;   idle processes       - the number of idle processes;
;   active processes     - the number of active processes;
;   total processes      - the number of idle + active processes;
;   max active processes - the maximum number of active processes since FPM
;                          has started;
;   max children reached - number of times, the process limit has been reached,
;                          when pm tries to start more children (works only for
;                          pm 'dynamic' and 'ondemand');
; Value are updated in real time.
; Example output:
;   pool:                 www
;   process manager:      static
;   start time:           01/Jul/2011:17:53:49 +0200
;   start since:          62636
;   accepted conn:        190460
;   listen queue:         0
;   max listen queue:     1
;   listen queue len:     42
;   idle processes:       4
;   active processes:     11
;   total processes:      15
;   max active processes: 12
;   max children reached: 0
;
; By default the status page output is formatted as text/plain. Passing either
; 'html', 'xml' or 'json' in the query string will return the corresponding
; output syntax. Example:
;   http://www.foo.bar/status
;   http://www.foo.bar/status?json
;   http://www.foo.bar/status?html
;   http://www.foo.bar/status?xml
;
; By default the status page only outputs short status. Passing 'full' in the
; query string will also return status for each pool process.
; Example:
;   http://www.foo.bar/status?full
;   http://www.foo.bar/status?json&full
;   http://www.foo.bar/status?html&full
;   http://www.foo.bar/status?xml&full
; The Full status returns for each process:
;   pid                  - the PID of the process;
;   state                - the state of the process (Idle, Running, ...);
;   start time           - the date and time the process has started;
;   start since          - the number of seconds since the process has started;
;   requests             - the number of requests the process has served;
;   request duration     - the duration in µs of the requests;
;   request method       - the request method (GET, POST, ...);
;   request URI          - the request URI with the query string;
;   content length       - the content length of the request (only with POST);
;   user                 - the user (PHP_AUTH_USER) (or '-' if not set);
;   script               - the main script called (or '-' if not set);
;   last request cpu     - the %cpu the last request consumed
;                          it's always 0 if the process is not in Idle state
;                          because CPU calculation is done when the request
;                          processing has terminated;
;   last request memory  - the max amount of memory the last request consumed
;                          it's always 0 if the process is not in Idle state
;                          because memory calculation is done when the request
;                          processing has terminated;
; If the process is in Idle state, then informations are related to the
; last request the process has served. Otherwise informations are related to
; the current request being served.
; Example output:
;   ************************
;   pid:                  31330
;   state:                Running
;   start time:           01/Jul/2011:17:53:49 +0200
;   start since:          63087
;   requests:             12808
;   request duration:     1250261
;   request method:       GET
;   request URI:          /test_mem.php?N=10000
;   content length:       0
;   user:                 -
;   script:               /home/fat/web/docs/php/test_mem.php
;   last request cpu:     0.00
;   last request memory:  0
;
; Note: There is a real-time FPM status monitoring sample web page available
;       It's available in: ${prefix}/share/fpm/status.html
;
; Note: The value must start with a leading slash (/). The value can be
;       anything, but it may not be a good idea to use the .php extension or it
;       may conflict with a real PHP file.
; Default Value: not set
pm.status_path = /php-status

; The ping URI to call the monitoring page of FPM. If this value is not set, no
; URI will be recognized as a ping page. This could be used to test from outside
; that FPM is alive and responding, or to
; - create a graph of FPM availability (rrd or such);
; - remove a server from a group if it is not responding (load balancing);
; - trigger alerts for the operating team (24/7).
; Note: The value must start with a leading slash (/). The value can be
;       anything, but it may not be a good idea to use the .php extension or it
;       may conflict with a real PHP file.
; Default Value: not set
;ping.path = /ping

; This directive may be used to customize the response of a ping request. The
; response is formatted as text/plain with a 200 response code.
; Default Value: pong
;ping.response = pong

; The access log file
; Default: not set
;access.log = log/$pool.access.log

; The access log format.
; The following syntax is allowed
;  %%: the '%' character
;  %C: %CPU used by the request
;      it can accept the following format:
;      - %{user}C for user CPU only
;      - %{system}C for system CPU only
;      - %{total}C  for user + system CPU (default)
;  %d: time taken to serve the request
;      it can accept the following format:
;      - %{seconds}d (default)
;      - %{miliseconds}d
;      - %{mili}d
;      - %{microseconds}d
;      - %{micro}d
;  %e: an environment variable (same as $_ENV or $_SERVER)
;      it must be associated with embraces to specify the name of the env
;      variable. Some exemples:
;      - server specifics like: %{REQUEST_METHOD}e or %{SERVER_PROTOCOL}e
;      - HTTP headers like: %{HTTP_HOST}e or %{HTTP_USER_AGENT}e
;  %f: script filename
;  %l: content-length of the request (for POST request only)
;  %m: request method
;  %M: peak of memory allocated by PHP
;      it can accept the following format:
;      - %{bytes}M (default)
;      - %{kilobytes}M
;      - %{kilo}M
;      - %{megabytes}M
;      - %{mega}M
;  %n: pool name
;  %o: ouput header
;      it must be associated with embraces to specify the name of the header:
;      - %{Content-Type}o
;      - %{X-Powered-By}o
;      - %{Transfert-Encoding}o
;      - ....
;  %p: PID of the child that serviced the request
;  %P: PID of the parent of the child that serviced the request
;  %q: the query string
;  %Q: the '?' character if query string exists
;  %r: the request URI (without the query string, see %q and %Q)
;  %R: remote IP address
;  %s: status (response code)
;  %t: server time the request was received
;      it can accept a strftime(3) format:
;      %d/%b/%Y:%H:%M:%S %z (default)
;  %T: time the log has been written (the request has finished)
;      it can accept a strftime(3) format:
;      %d/%b/%Y:%H:%M:%S %z (default)
;  %u: remote user
;
; Default: "%R - %u %t \"%m %r\" %s"
;access.format = %R - %u %t "%m %r%Q%q" %s %f %{mili}d %{kilo}M %C%%

; The log file for slow requests
; Default Value: not set
; Note: slowlog is mandatory if request_slowlog_timeout is set
;slowlog = log/$pool.log.slow

; The timeout for serving a single request after which a PHP backtrace will be
; dumped to the 'slowlog' file. A value of '0s' means 'off'.
; Available units: s(econds)(default), m(inutes), h(ours), or d(ays)
; Default Value: 0
;request_slowlog_timeout = 0

; The timeout for serving a single request after which the worker process will
; be killed. This option should be used when the 'max_execution_time' ini option
; does not stop script execution for some reason. A value of '0' means 'off'.
; Available units: s(econds)(default), m(inutes), h(ours), or d(ays)
; Default Value: 0
;request_terminate_timeout = 0

; Set open file descriptor rlimit.
; Default Value: system defined value
;rlimit_files = 1024

; Set max core size rlimit.
; Possible Values: 'unlimited' or an integer greater or equal to 0
; Default Value: system defined value
;rlimit_core = 0

; Chroot to this directory at the start. This value must be defined as an
; absolute path. When this value is not set, chroot is not used.
; Note: you can prefix with '$prefix' to chroot to the pool prefix or one
; of its subdirectories. If the pool prefix is not set, the global prefix
; will be used instead.
; Note: chrooting is a great security feature and should be used whenever
;       possible. However, all PHP paths will be relative to the chroot
;       (error_log, sessions.save_path, ...).
; Default Value: not set
;chroot =

; Chdir to this directory at the start.
; Note: relative path can be used.
; Default Value: current directory or / when chroot
chdir = /

; Redirect worker stdout and stderr into main error log. If not set, stdout and
; stderr will be redirected to /dev/null according to FastCGI specs.
; Note: on highloaded environement, this can cause some delay in the page
; process time (several ms).
; Default Value: no
catch_workers_output = yes

; Limits the extensions of the main script FPM will allow to parse. This can
; prevent configuration mistakes on the web server side. You should only limit
; FPM to .php extensions to prevent malicious users to use other extensions to
; exectute php code.
; Note: set an empty value to allow all extensions.
; Default Value: .php
;security.limit_extensions = .php .php3 .php4 .php5 .php7

; Pass environment variables like LD_LIBRARY_PATH. All $VARIABLEs are taken from
; the current environment.
; Default Value: clean env
;env[HOSTNAME] = $HOSTNAME
env[PATH] = /srv/www/phpcs/scripts/:/usr/local/bin:/usr/bin:/bin
;env[TMP] = /tmp
;env[TMPDIR] = /tmp
;env[TEMP] = /tmp

; Additional php.ini defines, specific to this pool of workers. These settings
; overwrite the values previously defined in the php.ini. The directives are the
; same as the PHP SAPI:
;   php_value/php_flag             - you can set classic ini defines which can
;                                    be overwritten from PHP call 'ini_set'.
;   php_admin_value/php_admin_flag - these directives won't be overwritten by
;                                     PHP call 'ini_set'
; For php_*flag, valid values are on, off, 1, 0, true, false, yes or no.

; Defining 'extension' will load the corresponding shared extension from
; extension_dir. Defining 'disable_functions' or 'disable_classes' will not
; overwrite previously defined php.ini values, but will append the new value
; instead.

; Note: path INI options can be relative and will be expanded with the prefix
; (pool, global or /usr)

; Default Value: nothing is defined by default except the values in php.ini and
;                specified at startup with the -d argument
;php_admin_value[sendmail_path] = /usr/sbin/sendmail -t -i -f www@my.domain.com
;php_flag[display_errors] = off
;php_admin_value[error_log] = /var/log/fpm-php.www.log
;php_admin_flag[log_errors] = on
;php_admin_value[memory_limit] = 32M
```

![17](https://user-images.githubusercontent.com/93030868/151694493-15eba12c-267c-41f6-91e6-af9b8c5f469b.png)

lalu kita jalankan laravel nya http://kelompok11.fpas/ dan laravel dapat digunakan

![18](https://user-images.githubusercontent.com/93030868/151694494-ee15b6e2-4c3a-4fa1-8287-8fa39e8a78c6.png)

###### YII

selanjut nya kita setting YII untuk kelompok11.fpsas/product

Ansibel YII
```markdown
install-yii.yml
- hosts: yii
  vars:
    username: 'iqbal'
    password: 'iqbal'
    domain: 'lxc_php7_1.dev'
    domain: 'lxc_php7_2.dev'
    domain: 'lxc_php7_4.dev'
    domain: 'lxc_php7_5.dev'
    domain: 'lxc_php7_6.dev'
  roles:
    - php
    - yii
```

![19](https://user-images.githubusercontent.com/93030868/151694495-7b397b6b-3934-4ae6-837a-05e11501822a.png)

YII Tasks
```markdown
---
- name: delete apt chache
  become: yes
  become_user: root
  become_method: su
  command: rm -vf /var/lib/apt/lists/*

- name: install php
  become: yes
  become_user: root
  become_method: su
  apt: name={{ item }} state=latest update_cache=true
  with_items:
    - gtkhash
    - crack-md5
    - git
    - curl
    - nginx
    - nginx-extras
    - php7.4
    - php7.4-fpm
    - php7.4-curl
    - php7.4-xml
    - php7.4-gd
    - php7.4-opcache
    - php7.4-mbstring
    - php7.4-zip
    - php7.4-json
    - php7.4-cli

- name: enable module php mbstring
  command: phpenmod mbstring
  notify:
    - restart php

```
![20](https://user-images.githubusercontent.com/93030868/151694496-6ff9f3ea-f313-49ff-ae69-e70df3c47c06.png)

YII handlers
```markdown
---
- name: restart php
  become: yes
  become_user: root
  become_method: su
  action: service name=php7.4-fpm state=restarted

- name: restart nginx
  become: yes
  become_user: root
  become_method: su
  action: service name=nginx state=restarted
```

![21](https://user-images.githubusercontent.com/93030868/151694497-43acfc80-6bf6-42a6-a820-2a3d9b471803.png)

YII templates lv.conf
```markdown
server {
    set $host_path "/var/www/html/basic";
    #access_log  /www/testproject/log/access.log  main;

    server_name lxc_php7_1.dev lxc_php7_2.dev lxc_php7_4.dev lxc_php7_5.dev lxc_php7_6.dev;
    root   $host_path/web;
    set $yii_bootstrap "index.php";

    charset utf-8;

    location / {
        index  index.html $yii_bootstrap;
        try_files $uri $uri/ /$yii_bootstrap?$args;
    }

    location ~ ^/(protected|framework|themes/\w+/views) {
        deny  all;
    }

    #avoid processing of calls to unexisting static files by yii
    location ~ \.(js|css|png|jpg|gif|swf|ico|pdf|mov|fla|zip|rar)$ {
        try_files $uri =404;
    }

    # pass the PHP scripts to FastCGI server listening on UNIX socket
    location ~ \.php {
        fastcgi_split_path_info  ^(.+\.php)(.*)$;

        #let yii catch the calls to unexising PHP files
        set $fsn /$yii_bootstrap;
        if (-f $document_root$fastcgi_script_name){
            set $fsn $fastcgi_script_name;
        }
       fastcgi_pass   unix:/run/php/php7.4-fpm.sock;
        include fastcgi_params;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fsn;

       #PATH_INFO and PATH_TRANSLATED can be omitted, but RFC 3875 specifies them for CGI
        fastcgi_param  PATH_INFO        $fastcgi_path_info;
        fastcgi_param  PATH_TRANSLATED  $document_root$fsn;
    }

    # prevent nginx from serving dotfiles (.htaccess, .svn, .git, etc.)
    location ~ /\. {
        deny all;
        access_log off;
        log_not_found off;
    }
}
```

![22](https://user-images.githubusercontent.com/93030868/151694498-a3f9bb6b-8a8d-4950-850c-7d7ce5d229fd.png)

lalu kita set YII agar terkoneksi ke database

![23](https://user-images.githubusercontent.com/93030868/151694499-3da42120-ab74-4c8c-b094-dd4e5be3ebfb.png)

lalu kita jalankan YII

![24](https://user-images.githubusercontent.com/93030868/151694500-0b6726a3-bd8b-4843-b06f-6f7c444b0ed0.png)

###### WordPress

Set Wordpress

Ansible Wordpres
```markdown
---
- hosts: lxc_php7_1
  vars:
    username: 'iqbal'
    password: 'iqbal'
    domain: 'lxc_php7_1.dev'
  roles:
    - wp
```

![25](https://user-images.githubusercontent.com/93030868/151694501-da4c88ac-e3d2-4221-bdff-cdf1c7ef2375.png)

Task Wordpress
```markdown
---
- name: delete apt chache
  become: yes
  become_user: root
  become_method: su
  command: rm -vf /var/lib/apt/lists/*

- name: install requirement
  become: yes
  become_user: root
  become_method: su
  apt: name={{ item }} state=latest update_cache=true
  with_items:
    - nginx
    - nginx-extras
    - curl
    - wget
    - php7.4
    - php7.4-fpm
    - php7.4-curl
    - php7.4-xml
    - php7.4-gd
    - php7.4-opcache
    - php7.4-mbstring
    - php7.4-zip
    - php7.4-json
    - php7.4-cli
    - php7.4-mysqlnd
    - php7.4-xmlrpc
    - php7.4-curl

- name: wget wordpress
  shell: wget -c http://wordpress.org/latest.tar.gz

- name: tar latest.tar.gz
  shell: tar -xvzf latest.tar.gz

- name: copy folder wordpress
  shell: cp -R wordpress /var/www/html/wp

- name: chmod
  become: yes
  become_user: root
  become_method: su
  command: chmod 775 -R /var/www/html/wp/

- name: copy .wp-config.conf
  template:
    src=templates/wp.conf
    dest=/var/www/html/wp/wp-config.php

- name: copy wordpress.conf
  template:
    src=templates/wordpress.conf
    dest=/etc/nginx/sites-available/{{ domain }}
  vars:
    servername: '{{ domain }}'

- name: copy php7.conf
  template:
    src=templates/php7.conf
    dest=/etc/php/7.4/fpm/pool.d/www.conf

- name: Symlink wordpress.conf
  command: ln -sfn /etc/nginx/sites-available/{{ domain }} /etc/nginx/sites-enabled/{{ domain }}
  notify:
    - restart nginx

- name: Write {{ domain }} to /etc/hosts
  lineinfile:
    dest: /etc/hosts
    regexp: '.*{{ domain }}$'
    line: "127.0.0.1 {{ domain }}"
    state: present

- name: enable module php mbstring
  command: phpenmod mbstring
  notify:
    - restart php
```

![26](https://user-images.githubusercontent.com/93030868/151694502-dd52c522-b6d2-4156-b962-b713e4e11b0f.png)

Handlers Wordpress
```markdown
---
- name: restart nginx
  become: yes
  become_user: root
  become_method: su
  action: service name=nginx state=restarted

- name: restart php
  become: yes
  become_user: root
  become_method: su
  action: service name=php7.4-fpm state=restarted
```

![27](https://user-images.githubusercontent.com/93030868/151694504-a6e5fd63-ecc7-414d-9f67-a34c73a1b3b2.png)

Templates Wordpress(wp.conf)
```markdown
<?php
/**
 * The base configuration for WordPress
 *
 * The wp-config.php creation script uses this file during the installation.
 * You don't have to use the web site, you can copy this file to "wp-config.php"
 * and fill in the values.
 *
 * This file contains the following configurations:
 *
 * * MySQL settings
 * * Secret keys
 * * Database table prefix
 * * ABSPATH
 *
 * @link https://wordpress.org/support/article/editing-wp-config-php/
 *
 * @package WordPress
 */

define( 'WP_HOME', 'http://news.kelompok11.fpsas' );
define( 'WP_SITEURL', 'http://news.kelompok11.fpsas' );

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define( 'DB_NAME', 'wp_db' );

/** MySQL database username */
define( 'DB_USER', 'iqbal' );

/** MySQL database password */
define( 'DB_PASSWORD', 'iqbal' );

/** MySQL hostname */
define( 'DB_HOST', '10.0.3.50:3306' );

/** Database charset to use in creating database tables. */
define( 'DB_CHARSET', 'utf8' );

/** The database collate type. Don't change this if in doubt. */
define( 'DB_COLLATE', '' );
/**#@+
 * Authentication unique keys and salts.
 *
 * Change these to different unique phrases! You can generate these using
 * the {@link https://api.wordpress.org/secret-key/1.1/salt/ WordPress.org secret-key service}.
 *
 * You can change these at any point in time to invalidate all existing cookies.
 * This will force all users to have to log in again.
 *
 * @since 2.6.0
 */
define( 'AUTH_KEY',         'put your unique phrase here' );
define( 'SECURE_AUTH_KEY',  'put your unique phrase here' );
define( 'LOGGED_IN_KEY',    'put your unique phrase here' );
define( 'NONCE_KEY',        'put your unique phrase here' );
define( 'AUTH_SALT',        'put your unique phrase here' );
define( 'SECURE_AUTH_SALT', 'put your unique phrase here' );
define( 'LOGGED_IN_SALT',   'put your unique phrase here' );
define( 'NONCE_SALT',       'put your unique phrase here' );

/**#@-*/

/**
 * WordPress database table prefix.
 *
 * You can have multiple installations in one database if you give each
 * a unique prefix. Only numbers, letters, and underscores please!
 */
$table_prefix = 'wp_';

/**
 * For developers: WordPress debugging mode.
 *
 * Change this to true to enable the display of notices during development.
 * It is strongly recommended that plugin and theme developers use WP_DEBUG
 * in their development environments.
 *
 * For information on other constants that can be used for debugging,
 * visit the documentation.
 *
 * @link https://wordpress.org/support/article/debugging-in-wordpress/
 */
define( 'WP_DEBUG', false );

/* Add any custom values between this line and the "stop editing" line. */



/* That's all, stop editing! Happy publishing. */

/** Absolute path to the WordPress directory. */
if ( ! defined( 'ABSPATH' ) ) {
        define( 'ABSPATH', __DIR__ . '/' );
}

/** Sets up WordPress vars and included files. */
require_once ABSPATH . 'wp-settings.php';
```

![28](https://user-images.githubusercontent.com/93030868/151694505-fd226289-c581-4bff-a3e6-38cbfe9b386d.png)

Templates Wordpress(wordpress.conf)
```markdown
server {
     listen 80;
     listen [::]:80;

     # Log files for Debugging
     access_log /var/log/nginx/wordpress-access.log;
     error_log /var/log/nginx/wordpress-error.log;

     # Webroot Directory for Laravel project
     root /var/www/html/wp;
     index index.php index.html index.htm;

     # Your Domain Name
     server_name lxc_php7_1.dev lxc_php7_2.dev lxc_php7_4.dev lxc_php7_6.dev;

     location / {
             try_files $uri $uri/ /index.php?$query_string;
     }

     # PHP-FPM Configuration Nginx
     location ~ \.php$ {
             try_files $uri =404;
             fastcgi_split_path_info ^(.+\.php)(/.+)$;
             fastcgi_pass 127.0.0.1:9001;
             fastcgi_index index.php;
             fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
             include fastcgi_params;
     }
}
```

![29](https://user-images.githubusercontent.com/93030868/151694506-e77e6d6a-25fc-4a1a-8119-3f52a838de8e.png)

kita juga set pada templates php7.conf
```markdown
; Start a new pool named 'www'.
; the variable $pool can we used in any directive and will be replaced by the
; pool name ('www' here)
[www]

; Per pool prefix
; It only applies on the following directives:
; - 'slowlog'
; - 'listen' (unixsocket)
; - 'chroot'
; - 'chdir'
; - 'php_values'
; - 'php_admin_values'
; When not set, the global prefix (or /usr) applies instead.
; Note: This directive can also be relative to the global prefix.
; Default Value: none
;prefix = /path/to/pools/$pool

; Unix user/group of processes
; Note: The user is mandatory. If the group is not set, the default user's group
;       will be used.
user = www-data
group = www-data

; The address on which to accept FastCGI requests.
; Valid syntaxes are:
;   'ip.add.re.ss:port'    - to listen on a TCP socket to a specific address on
;                            a specific port;
;   'port'                 - to listen on a TCP socket to all addresses on a
;                            specific port;
;   '/path/to/unix/socket' - to listen on a unix socket.
; Note: This value is mandatory.
listen = 127.0.0.1:9001

; Set listen(2) backlog. A value of '-1' means unlimited.
; Default Value: 128 (-1 on FreeBSD and OpenBSD)
;listen.backlog = -1

; Set permissions for unix socket, if one is used. In Linux, read/write
; permissions must be set in order to allow connections from a web server. Many
; BSD-derived systems allow connections regardless of permissions.
; Default Values: user and group are set as the running user
;                 mode is set to 0666
listen.owner = www-data
listen.group = www-data
listen.mode = 0666

; List of ipv4 addresses of FastCGI clients which are allowed to connect.
; Equivalent to the FCGI_WEB_SERVER_ADDRS environment variable in the original
; PHP FCGI (5.2.2+). Makes sense only with a tcp listening socket. Each address
; must be separated by a comma. If this value is left blank, connections will be
; accepted from any ip address.
; Default Value: any
;listen.allowed_clients = 127.0.0.1

; Choose how the process manager will control the number of child processes.
; Possible Values:
;   static  - a fixed number (pm.max_children) of child processes;
;   dynamic - the number of child processes are set dynamically based on the
;             following directives. With this process management, there will be
;             always at least 1 children.
;             pm.max_children      - the maximum number of children that can
;                                    be alive at the same time.
;             pm.start_servers     - the number of children created on startup.
;             pm.min_spare_servers - the minimum number of children in 'idle'
;                                    state (waiting to process). If the number
;                                    of 'idle' processes is less than this
;                                    number then some children will be created.
;             pm.max_spare_servers - the maximum number of children in 'idle'
;                                    state (waiting to process). If the number
;                                    of 'idle' processes is greater than this
;                                    number then some children will be killed.
;  ondemand - no children are created at startup. Children will be forked when
;             new requests will connect. The following parameter are used:
;             pm.max_children           - the maximum number of children that
;                                         can be alive at the same time.
;             pm.process_idle_timeout   - The number of seconds after which
;                                         an idle process will be killed.
; Note: This value is mandatory.
pm = dynamic

; The number of child processes to be created when pm is set to 'static' and the
; maximum number of child processes when pm is set to 'dynamic' or 'ondemand'.
; This value sets the limit on the number of simultaneous requests that will be
; served. Equivalent to the ApacheMaxClients directive with mpm_prefork.
; Equivalent to the PHP_FCGI_CHILDREN environment variable in the original PHP
; CGI. The below defaults are based on a server without much resources. Don't
; forget to tweak pm.* to fit your needs.
; Note: Used when pm is set to 'static', 'dynamic' or 'ondemand'
; Note: This value is mandatory.
pm.max_children = 5

; The number of child processes created on startup.
; Note: Used only when pm is set to 'dynamic'
; Default Value: min_spare_servers + (max_spare_servers - min_spare_servers) / 2
pm.start_servers = 2

; The desired minimum number of idle server processes.
; Note: Used only when pm is set to 'dynamic'
; Note: Mandatory when pm is set to 'dynamic'
pm.min_spare_servers = 1

; The desired maximum number of idle server processes.
; Note: Used only when pm is set to 'dynamic'
; Note: Mandatory when pm is set to 'dynamic'
pm.max_spare_servers = 3

; The number of seconds after which an idle process will be killed.
; Note: Used only when pm is set to 'ondemand'
; Default Value: 10s
;pm.process_idle_timeout = 10s;

; The number of requests each child process should execute before respawning.
; This can be useful to work around memory leaks in 3rd party libraries. For
; endless request processing specify '0'. Equivalent to PHP_FCGI_MAX_REQUESTS.
; Default Value: 0
pm.max_requests = 100

; The URI to view the FPM status page. If this value is not set, no URI will be
; recognized as a status page. It shows the following informations:
;   pool                 - the name of the pool;
;   process manager      - static, dynamic or ondemand;
;   start time           - the date and time FPM has started;
;   start since          - number of seconds since FPM has started;
;   accepted conn        - the number of request accepted by the pool;
;   listen queue         - the number of request in the queue of pending
;                          connections (see backlog in listen(2));
;   max listen queue     - the maximum number of requests in the queue
;                          of pending connections since FPM has started;
;   listen queue len     - the size of the socket queue of pending connections;
;   idle processes       - the number of idle processes;
;   active processes     - the number of active processes;
;   total processes      - the number of idle + active processes;
;   max active processes - the maximum number of active processes since FPM
;                          has started;
;   max children reached - number of times, the process limit has been reached,
;                          when pm tries to start more children (works only for
;                          pm 'dynamic' and 'ondemand');
; Value are updated in real time.
; Example output:
;   pool:                 www
;   process manager:      static
;   start time:           01/Jul/2011:17:53:49 +0200
;   start since:          62636
;   accepted conn:        190460
;   listen queue:         0
;   max listen queue:     1
;   listen queue len:     42
;   idle processes:       4
;   active processes:     11
;   total processes:      15
;   max active processes: 12
;   max children reached: 0
;
; By default the status page output is formatted as text/plain. Passing either
; 'html', 'xml' or 'json' in the query string will return the corresponding
; output syntax. Example:
;   http://www.foo.bar/status
;   http://www.foo.bar/status?json
;   http://www.foo.bar/status?html
;   http://www.foo.bar/status?xml
;
; By default the status page only outputs short status. Passing 'full' in the
; query string will also return status for each pool process.
; Example:
;   http://www.foo.bar/status?full
;   http://www.foo.bar/status?json&full
;   http://www.foo.bar/status?html&full
;   http://www.foo.bar/status?xml&full
; The Full status returns for each process:
;   pid                  - the PID of the process;
;   state                - the state of the process (Idle, Running, ...);
;   start time           - the date and time the process has started;
;   start since          - the number of seconds since the process has started;
;   requests             - the number of requests the process has served;
;   request duration     - the duration in µs of the requests;
;   request method       - the request method (GET, POST, ...);
;   request URI          - the request URI with the query string;
;   content length       - the content length of the request (only with POST);
;   user                 - the user (PHP_AUTH_USER) (or '-' if not set);
;   script               - the main script called (or '-' if not set);
;   last request cpu     - the %cpu the last request consumed
;                          it's always 0 if the process is not in Idle state
;                          because CPU calculation is done when the request
;                          processing has terminated;
;   last request memory  - the max amount of memory the last request consumed
;                          it's always 0 if the process is not in Idle state
;                          because memory calculation is done when the request
;                          processing has terminated;
; If the process is in Idle state, then informations are related to the
; last request the process has served. Otherwise informations are related to
; the current request being served.
; Example output:
;   ************************
;   pid:                  31330
;   state:                Running
;   start time:           01/Jul/2011:17:53:49 +0200
;   start since:          63087
;   requests:             12808
;   request duration:     1250261
;   request method:       GET
;   request URI:          /test_mem.php?N=10000
;   content length:       0
;   user:                 -
;   script:               /home/fat/web/docs/php/test_mem.php
;   last request cpu:     0.00
;   last request memory:  0
;
; Note: There is a real-time FPM status monitoring sample web page available
;       It's available in: ${prefix}/share/fpm/status.html
;
; Note: The value must start with a leading slash (/). The value can be
;       anything, but it may not be a good idea to use the .php extension or it
;       may conflict with a real PHP file.
; Default Value: not set
pm.status_path = /php-status

; The ping URI to call the monitoring page of FPM. If this value is not set, no
; URI will be recognized as a ping page. This could be used to test from outside
; that FPM is alive and responding, or to
; - create a graph of FPM availability (rrd or such);
; - remove a server from a group if it is not responding (load balancing);
; - trigger alerts for the operating team (24/7).
; Note: The value must start with a leading slash (/). The value can be
;       anything, but it may not be a good idea to use the .php extension or it
;       may conflict with a real PHP file.
; Default Value: not set
;ping.path = /ping

; This directive may be used to customize the response of a ping request. The
; response is formatted as text/plain with a 200 response code.
; Default Value: pong
;ping.response = pong

; The access log file
; Default: not set
;access.log = log/$pool.access.log

; The access log format.
; The following syntax is allowed
;  %%: the '%' character
;  %C: %CPU used by the request
;      it can accept the following format:
;      - %{user}C for user CPU only
;      - %{system}C for system CPU only
;      - %{total}C  for user + system CPU (default)
;  %d: time taken to serve the request
;      it can accept the following format:
;      - %{seconds}d (default)
;      - %{miliseconds}d
;      - %{mili}d
;      - %{microseconds}d
;      - %{micro}d
;  %e: an environment variable (same as $_ENV or $_SERVER)
;      it must be associated with embraces to specify the name of the env
;      variable. Some exemples:
;      - server specifics like: %{REQUEST_METHOD}e or %{SERVER_PROTOCOL}e
;      - HTTP headers like: %{HTTP_HOST}e or %{HTTP_USER_AGENT}e
;  %f: script filename
;  %l: content-length of the request (for POST request only)
;  %m: request method
;  %M: peak of memory allocated by PHP
;      it can accept the following format:
;      - %{bytes}M (default)
;      - %{kilobytes}M
;      - %{kilo}M
;      - %{megabytes}M
;      - %{mega}M
;  %n: pool name
;  %o: ouput header
;      it must be associated with embraces to specify the name of the header:
;      - %{Content-Type}o
;      - %{X-Powered-By}o
;      - %{Transfert-Encoding}o
;      - ....
;  %p: PID of the child that serviced the request
;  %P: PID of the parent of the child that serviced the request
;  %q: the query string
;  %Q: the '?' character if query string exists
;  %r: the request URI (without the query string, see %q and %Q)
;  %R: remote IP address
;  %s: status (response code)
;  %t: server time the request was received
;      it can accept a strftime(3) format:
;      %d/%b/%Y:%H:%M:%S %z (default)
;  %T: time the log has been written (the request has finished)
;      it can accept a strftime(3) format:
;      %d/%b/%Y:%H:%M:%S %z (default)
;  %u: remote user
;
; Default: "%R - %u %t \"%m %r\" %s"
;access.format = %R - %u %t "%m %r%Q%q" %s %f %{mili}d %{kilo}M %C%%

; The log file for slow requests
; Default Value: not set
; Note: slowlog is mandatory if request_slowlog_timeout is set
;slowlog = log/$pool.log.slow

; The timeout for serving a single request after which a PHP backtrace will be
; dumped to the 'slowlog' file. A value of '0s' means 'off'.
; Available units: s(econds)(default), m(inutes), h(ours), or d(ays)
; Default Value: 0
;request_slowlog_timeout = 0

; The timeout for serving a single request after which the worker process will
; be killed. This option should be used when the 'max_execution_time' ini option
; does not stop script execution for some reason. A value of '0' means 'off'.
; Available units: s(econds)(default), m(inutes), h(ours), or d(ays)
; Default Value: 0
;request_terminate_timeout = 0

; Set open file descriptor rlimit.
; Default Value: system defined value
;rlimit_files = 1024

; Set max core size rlimit.
; Possible Values: 'unlimited' or an integer greater or equal to 0
; Default Value: system defined value
;rlimit_core = 0

; Chroot to this directory at the start. This value must be defined as an
; absolute path. When this value is not set, chroot is not used.
; Note: you can prefix with '$prefix' to chroot to the pool prefix or one
; of its subdirectories. If the pool prefix is not set, the global prefix
; will be used instead.
; Note: chrooting is a great security feature and should be used whenever
;       possible. However, all PHP paths will be relative to the chroot
;       (error_log, sessions.save_path, ...).
; Default Value: not set
;chroot =

; Chdir to this directory at the start.
; Note: relative path can be used.
; Default Value: current directory or / when chroot
chdir = /

; Redirect worker stdout and stderr into main error log. If not set, stdout and
; stderr will be redirected to /dev/null according to FastCGI specs.
; Note: on highloaded environement, this can cause some delay in the page
; process time (several ms).
; Default Value: no
catch_workers_output = yes

; Limits the extensions of the main script FPM will allow to parse. This can
; prevent configuration mistakes on the web server side. You should only limit
; FPM to .php extensions to prevent malicious users to use other extensions to
; exectute php code.
; Note: set an empty value to allow all extensions.
; Default Value: .php
;security.limit_extensions = .php .php3 .php4 .php5 .php7

; Pass environment variables like LD_LIBRARY_PATH. All $VARIABLEs are taken from
; the current environment.
; Default Value: clean env
;env[HOSTNAME] = $HOSTNAME
env[PATH] = /srv/www/phpcs/scripts/:/usr/local/bin:/usr/bin:/bin
;env[TMP] = /tmp
;env[TMPDIR] = /tmp
;env[TEMP] = /tmp

; Additional php.ini defines, specific to this pool of workers. These settings
; overwrite the values previously defined in the php.ini. The directives are the
; same as the PHP SAPI:
;   php_value/php_flag             - you can set classic ini defines which can
;                                    be overwritten from PHP call 'ini_set'.
;   php_admin_value/php_admin_flag - these directives won't be overwritten by
;                                     PHP call 'ini_set'
; For php_*flag, valid values are on, off, 1, 0, true, false, yes or no.

; Defining 'extension' will load the corresponding shared extension from
; extension_dir. Defining 'disable_functions' or 'disable_classes' will not
; overwrite previously defined php.ini values, but will append the new value
; instead.

; Note: path INI options can be relative and will be expanded with the prefix
; (pool, global or /usr)

; Default Value: nothing is defined by default except the values in php.ini and
;                specified at startup with the -d argument
;php_admin_value[sendmail_path] = /usr/sbin/sendmail -t -i -f www@my.domain.com
;php_flag[display_errors] = off
;php_admin_value[error_log] = /var/log/fpm-php.www.log
;php_admin_flag[log_errors] = on
;php_admin_value[memory_limit] = 32M
```

![30](https://user-images.githubusercontent.com/93030868/151694507-f940b707-9f2e-47d4-8b55-93960f227c13.png))

kita jalankan Wordpress

![31](https://user-images.githubusercontent.com/93030868/151694508-3797759f-0085-49be-a39b-45724ecf3dce.png)

###### LXC Database
```markdown
- hosts: LXC_DB_SERVER
  vars:
    username: 'iqbal'
    password: 'iqbal'
    domain: 'lxc_db_server.dev'
  roles:
    - db
    - pma
```

![32](https://user-images.githubusercontent.com/93030868/151694509-4615f4c0-2e49-4ae8-a786-928cc167fc35.png)

lalu kita set ansible pada database

Task DB
```markdown
---
- name: delete apt chache
  become: yes
  become_user: root
  become_method: su
  command: rm -vf /var/lib/apt/lists/*

- name: install nginx phpmyadmin
  become: yes
  become_user: root
  become_method: su
  apt: name={{ item }} state=latest update_cache=true
  with_items:
    - curl
    - nginx
    - nginx-extras
    - apache2
    - php
    - wget
    - apache2-utils
    - libapache2-mod-php
    - php7.3
    - php7.3-pdo
    - php7.3-fpm
    - php7.3-common
    - php7.3-curl
    - php7.3-xml
    - php7.3-gd
    - php7.3-opcache
    - php7.3-mbstring
    - php7.3-zip
    - php7.3-json
    - php7.3-cli
    - php7.3-mysql
    - lsb-release
    - apt-transport-https
    - ca-certificates

- name: wget pma
  shell: wget https://files.phpmyadmin.net/phpMyAdmin/5.1.1/phpMyAdmin-5.1.1-all-languages.tar.gz

- name: stack boss
  shell: tar -zxvf phpMyAdmin-5.1.1-all-languages.tar.gz

- name: copy folder pma
  shell: mv phpMyAdmin-5.1.1-all-languages /usr/share/phpmyadmin

- name: enable module php mbstring
  command: phpenmod mbstring
  notify:
    - restart php

- name: Copy pma.local
  template:
    src=templates/pma.local
    dest=/etc/nginx/sites-available/{{ domain }}
  vars:
    servername: '{{ domain }}'

- name: Symlink pma.local
  command: ln -sfn /etc/nginx/sites-available/{{ domain }} /etc/nginx/sites-enabled/{{ domain }}
  notify:
    - restart nginx

- name: Write {{ domain }} to /etc/hosts
  lineinfile:
    dest: /etc/hosts
    regexp: '.*{{ domain }}$'
    line: "127.0.0.1 {{ domain }}"
    state: present
```

![33](https://user-images.githubusercontent.com/93030868/151694510-cc47a6f1-2f94-4be2-8160-07192359bc67.png)

Handlres DB
```markdown
---
- name: stop apache2
  become: yes
  become_user: root
  become_method: su
  action: service name=apache2 state=stopped

- name: restart nginx
  become: yes
  become_user: root
  become_method: su
  action: service name=nginx state=restarted

- name: restart php
  become: yes
  become_user: root
  become_method: su
  action: service name=php7.3-fpm state=restarted
```

![34](https://user-images.githubusercontent.com/93030868/151694511-4183e32f-6bf0-4ef1-be6c-2f7d59fe9fd0.png)

Templates DB
```markdown
server {
    listen 80;

      server_name {{servername}};

      root /usr/share/phpmyadmin;

      index index.php;

      location / {

           try_files $uri $uri/ @phpmyadmin;

      }
      location @phpmyadmin {
              fastcgi_pass unix:/run/php/php7.3-fpm.sock;   #Sesuaikan dengan versi PHP

              fastcgi_param SCRIPT_FILENAME /usr/share/phpmyadmin/index.php;

              include /etc/nginx/fastcgi_params;

              fastcgi_param SCRIPT_NAME /index.php;
      }
      location ~ \.php$ {

              fastcgi_pass unix:/run/php/php7.3-fpm.sock;  #Sesuaikan dengan versi PHP

              fastcgi_index index.php;

              fastcgi_param SCRIPT_FILENAME /usr/share/phpmyadmin$fastcgi_script_name;

              include fastcgi_params;

      }
}
```

![35](https://user-images.githubusercontent.com/93030868/151694512-49fa2526-c6b4-4eb9-bd95-941f9ba1b78b.png)

lalu kita jalankan DB dan database dapat dijalankan dan tersambung denagan YII

![36](https://user-images.githubusercontent.com/93030868/151694513-1c26f633-8de4-4751-baf2-c23d544eb418.png)

###### Set Host Utama

kita tambahkan domain pada masing-masing container dan sesuai ip yang telah kita setting pada awal instalasi

```markdown
nano /etc/hosts
```

![37](https://user-images.githubusercontent.com/93030868/151694452-11a27576-e963-43a5-8d39-7111fb2adf52.png)

lalu kita buat semua container auto start

![38](https://user-images.githubusercontent.com/93030868/151694455-a77e7122-d3ee-4f41-980b-0b39ef2ecf91.png)

###### Host Grouping

lakukan grouping pada host untuk memfokuskan lxc sesuai yang diperlukan
```markdown
Ansible Hosts Groupping
[ci]
lxc_php5_1 ansible_host=lxc_php5_1.dev ansible_ssh_user=root ansible_become_pass=iqbal
lxc_php5_2 ansible_host=lxc_php5_2.dev ansible_ssh_user=root ansible_become_pass=iqbal

[laravel]
lxc_php7_2 ansible_host=lxc_php7_2.dev ansible_ssh_user=root ansible_become_pass=iqbal
lxc_php7_3 ansible_host=lxc_php7_3.dev ansible_ssh_user=root ansible_become_pass=iqbal
lxc_php7_4 ansible_host=lxc_php7_4.dev ansible_ssh_user=root ansible_become_pass=iqbal
lxc_php7_5 ansible_host=lxc_php7_5.dev ansible_ssh_user=root ansible_become_pass=iqbal

[wordpress]
lxc_php7_1 ansible_host=lxc_php7_1.dev ansible_ssh_user=root ansible_become_pass=iqbal
lxc_php7_2 ansible_host=lxc_php7_2.dev ansible_ssh_user=root ansible_become_pass=iqbal
lxc_php7_4 ansible_host=lxc_php7_4.dev ansible_ssh_user=root ansible_become_pass=iqbal
lxc_php7_6 ansible_host=lxc_php7_6.dev ansible_ssh_user=root ansible_become_pass=iqbal

[yii]
lxc_php7_1 ansible_host=lxc_php7_1.dev ansible_ssh_user=root ansible_become_pass=iqbal
lxc_php7_2 ansible_host=lxc_php7_2.dev ansible_ssh_user=root ansible_become_pass=iqbal
lxc_php7_4 ansible_host=lxc_php7_4.dev ansible_ssh_user=root ansible_become_pass=iqbal
lxc_php7_5 ansible_host=lxc_php7_5.dev ansible_ssh_user=root ansible_become_pass=iqbal
lxc_php7_6 ansible_host=lxc_php7_6.dev ansible_ssh_user=root ansible_become_pass=iqbal

[database]
LXC_DB_SERVER ansible_host=lxc_db_server.dev ansible_ssh_user=root ansible_become_pass=iqbal
```

![39](https://user-images.githubusercontent.com/93030868/151694456-8768c5c6-641e-48c4-a94c-1c77aa7b25a8.png)

###### Load Balancer

lakukan load balancer pada YII, Laravel, dan Ci

Least Connection

* LXC_PHP7_1
* LXC_PHP7_2
* LXC_PHP7_4
* LXC_PHP7_6

Ip Hash

* LXC_PHP7_2
* LXC_PHP7_3
* LXC_PHP7_4
* LXC_PHP7_5

Weighted Load Balancing

* LXC_PHP7_1 (Weight=3)
* LXC_PHP7_2 (Weight=2)
* LXC_PHP7_4 (Weight=4)
* LXC_PHP7_5 (Weight=1)
* LXC_PHP7_6 (Weight=6)

Round Robin

* LXC_PHP5_1
* LXC_PHP5_2

```markdown
/etc/nginx/sites-available/kelompok11.fpsas
```

![40](https://user-images.githubusercontent.com/93030868/151694457-f8aaff84-7d0d-4251-8391-c73fd15798e4.png)

![41](https://user-images.githubusercontent.com/93030868/151694458-55e3dd5e-a0ca-45b8-916d-1c73c0af8537.png)

pada Wordpress

```markdown
/etc/nginx/sites-available/news.kelompok11.fpsas
```

![42](https://user-images.githubusercontent.com/93030868/151694460-f407b5ba-8b8d-4bf5-a423-ba88bc13f682.png)

###### Analisa JMeter

lalu kita gunakan app jmeter untuk load testing

* **50 Users**

  Summary report

  ![43](https://user-images.githubusercontent.com/93030868/151694461-4a2f13d5-238b-44d5-8558-49a4b7bae87e.png)

  Details User Akses

  ![44](https://user-images.githubusercontent.com/93030868/151694462-9f481612-e0f0-4534-9a22-7ed74222be8b.png)

  Grafik

  ![45](https://user-images.githubusercontent.com/93030868/151694463-c0816ae6-12e3-4d71-a009-c13cf0464283.png)

* **150 Users**

  Summary report

  ![46](https://user-images.githubusercontent.com/93030868/151694464-a3da57ba-a9cc-47ae-a169-9762b2998804.png)

  Details User Akses

  ![47](https://user-images.githubusercontent.com/93030868/151694465-8b9de199-1295-4ada-9624-c454d81849c1.png)

  Grafik

  ![48](https://user-images.githubusercontent.com/93030868/151694466-ebf5e453-25ce-4da1-ac3a-cb84a33f837f.png)

* **300 Users**

  Summary Report

  ![49](https://user-images.githubusercontent.com/93030868/151694467-c0888acb-1637-473e-acac-aa2a84b9793b.png)

  Detail Users Akses

  ![50](https://user-images.githubusercontent.com/93030868/151694469-796aedbb-4d4e-4427-bd96-a7d9a3c453c3.png)

  Grafik

  ![51](https://user-images.githubusercontent.com/93030868/151694470-eab4362f-5969-45c8-941f-2bc451cc4dcb.png)

* **500 Users**

  Summary report

  ![52](https://user-images.githubusercontent.com/93030868/151694471-3d0ddcbc-bfc6-4f62-bd01-086ac7346184.png)

  Detail Users Akses

  ![53](https://user-images.githubusercontent.com/93030868/151694472-fc79348d-80d3-48fb-8abd-19c23cb54fc6.png)

  Grafik

  ![54](https://user-images.githubusercontent.com/93030868/151694473-1f87dd55-65cb-4555-8cce-80ef94e09b54.png)

  ------

  #### Analisa

  ##### 1. Berapa nilai rata - rata througput untuk setiap website yang dihasilkan dari load testing?

  50 Users

  * Laravel			: 237,0/sec
  * Codeigneter   : 190,8/sec
  * YII                     : 198,4/sec
  * Wordpress      : 212,8/sec

  150 Users

  * Laravel			: 33,2/min
  * Codeigneter   : 33,2/min
  * YII                     : 33,2/min
  * Wordpress      : 33,2/min

  300 Users

  * Laravel			: 48,4/min
  * Codeigneter   : 48,4/min
  * YII                     : 48,4/min
  * Wordpress      : 48,4/min

  500 Users

  * Laravel			: 1,0/sec
  * Codeigneter   : 1,0/sec
  * YII                     : 1,0/sec
  * Wordpress      : 1,0/sec

  ##### 2. Berapa nilai rata - rata jumlah user yang dapat dilayani setiap detik untuk setiap website yang dihasilkan dari load testing?

  50 Users

  * Laravel			: 65 Users/sec
  * Codeigneter   : 37 Users/sec
  * YII                     : 33 Users/sec
  * Wordpress      : 86 Users/sec

  150 Users

  * Laravel			: 134 users/sec
  * Codeigneter   : 24 User/sec
  * YII                     : 22 Users/sec
  * Wordpress      : 211 Users/sec

  300 Users

  * Laravel			: 108 Users/sec
  * Codeigneter   : 18 Users/sec
  * YII                     : 17 Users/sec
  * Wordpress      : 158 Users/sec

  500 Users

  * Laravel			: 87 Users/sec
  * Codeigneter   : 25 Users/sec
  * YII                     : 23 Users/sec
  * Wordpress      : 150 Users/sec

  ##### 3. Bagaimana cara mengurangi nilai througput dan meningkatkan nilai jumlah user yang dapat dilayani setiap detik untuk skema yang telah dibuat ? Sebutkan faktor faktor yang mempengaruhi !

  * Topologi Jaringan
  * Penggunaan Load balance
  * Banyak nya Pengguna
  * Spesifikasi Device User Dan Server
  * Tipe Data
  * Piranti Jaringan

  
