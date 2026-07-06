
# Source code installation of PostgreSQL(community version of PostgreSQL)


In this article, we will explain how to install PostgreSQL 14 using source code installation in Linux systems.

Major advantage of using source code installation is it can be highly customized during installation.

When we  build from source, we  have complete control over the optional features, compiler options, and installation directories for the package. When you install from a precompiled package, you're stuck with the choices made by the person who constructed the package. Of course, using a precompiled package is much simpler. If you want to get up and running as quickly as possible, install from a binary package. If you want more control (as well as a better understanding of the options), build your own copy from source code.

Note-: If there are some customization done in  PostgreSQL source code before compiling it then customization in source code is not supported to receive support from EDB support team until it is recommended or approved by the EDB support.


# Below is the test case for RHEL.


Step 1) First install required prerequisites such as gcc, readline-devel and zlib-devel using package manager as shown below.

# yum install gcc zlib-devel readline-devel     [On RHEL/CentOS]
# apt install gcc zlib1g-dev libreadline6-dev   [On Debian/Ubuntu]

[Requirement/prerequisites](https://www.postgresql.org/docs/14/install-requirements.html)

```
[root@rijavan-node-01-test ~]# yum install gcc zlib-devel readline-devel
```

The output will be below it's going to install above packages including dependancy pakages.

```
Installed:
  gcc.x86_64 0:4.8.5-44.el7                                                 readline-devel.x86_64 0:6.2-11.el7                                                 zlib-devel.x86_64 0:1.2.7-21.el7_9                                                

Dependency Installed:
  cpp.x86_64 0:4.8.5-44.el7                        glibc-devel.x86_64 0:2.17-326.el7_9     glibc-headers.x86_64 0:2.17-326.el7_9     kernel-headers.x86_64 0:3.10.0-1160.88.1.el7     libmpc.x86_64 0:1.0.1-3.el7     mpfr.x86_64 0:3.1.1-4.el7    
  ncurses-devel.x86_64 0:5.9-14.20130511.el7_4    

Dependency Updated:
  glibc.x86_64 0:2.17-326.el7_9          glibc-common.x86_64 0:2.17-326.el7_9          libgcc.x86_64 0:4.8.5-44.el7          libgomp.x86_64 0:4.8.5-44.el7          readline.x86_64 0:6.2-11.el7          zlib.x86_64 0:1.2.7-21.el7_9         

Complete!
[root@rijavan-node-01-test ~]# 
```

Step 2) Download the Source code PostgreSQL Software tar file from the official postgres website using the following wget command directly on system.

Command wget stands for web get. The wget is a free non-interactive file downloader command. Type wget followed by the file URL you wish to download to your command prompt app, and the download should begin after you press enter.

[PostgreSQL download site](https://www.postgresql.org/download/)
[PostgreSQL Source](https://www.postgresql.org/ftp/source/)

```
[root@rijavan-node-01-test ~]# wget https://ftp.postgresql.org/pub/source/v14.7/postgresql-14.7.tar.gz --no-check-certificate
--2023-04-08 14:28:03--  https://ftp.postgresql.org/pub/source/v14.7/postgresql-14.7.tar.gz
Resolving ftp.postgresql.org (ftp.postgresql.org)... 147.75.85.69, 87.238.57.227, 72.32.157.246, ...
Connecting to ftp.postgresql.org (ftp.postgresql.org)|147.75.85.69|:443... connected.
WARNING: cannot verify ftp.postgresql.org's certificate, issued by ‘/C=US/O=Let's Encrypt/CN=R3’:
  Issued certificate has expired.
HTTP request sent, awaiting response... 200 OK
Length: 29070900 (28M) [application/octet-stream]
Saving to: ‘postgresql-14.7.tar.gz’

100%[=========================================================================================================================================================================================================>] 29,070,900  13.9MB/s   in 2.0s   

2023-04-08 14:28:06 (13.9 MB/s) - ‘postgresql-14.7.tar.gz’ saved [29070900/29070900]

[root@rijavan-node-01-test ~]#
```
sample output-: 

```
[root@rijavan-node-01-test ~]# ls -lrth 
total 28M
-rw-------. 1 root root 6.5K Jun  5  2018 original-ks.cfg
-rw-------. 1 root root 6.8K Jun  5  2018 anaconda-ks.cfg
-rw-r--r--. 1 root root  28M Feb  6 21:55 postgresql-14.7.tar.gz
```

Step 3) Use tar command to extract the downloaded tarball file. New directory named postgresql-14.7 will be created.

```
[root@rijavan-node-01-test ~]# tar -xvf postgresql-14.7.tar.gz 
````
tar -->> tape archive
x -->> extract
v -->> verbose
f -->> file

sample output-: 

```
[root@rijavan-node-01-test ~]# ls -lrth 
drwxrwxrwx. 6 1107 1107 4.0K Feb  6 21:55 postgresql-14.7
-rw-r--r--. 1 root root  28M Feb  6 21:55 postgresql-14.7.tar.gz
[root@rijavan-node-01-test ~]#
```
Step 4) Next step for installation procedure is to configure the downloaded source code by choosing the options according to our needs.

```
[root@rijavan-node-01-test ~]# cd postgresql-14.7
[root@rijavan-node-01-test postgresql-14.7]# 
```
use ./configure --help to get help about various options.

```
[root@rijavan-node-01-test postgresql-14.7]# ./configure --help 
`configure' configures PostgreSQL 14.7 to adapt to many kinds of systems.

Usage: ./configure [OPTION]... [VAR=VALUE]...

To assign environment variables (e.g., CC, CFLAGS...), specify them as
VAR=VALUE.  See below for descriptions of some of the useful variables.

Defaults for the options are specified in brackets.

Configuration:
  -h, --help              display this help and exit
      --help=short        display options specific to this package
      --help=recursive    display the short help of all the included packages
  -V, --version           display version information and exit
  -q, --quiet, --silent   do not print `checking ...' messages
      --cache-file=FILE   cache test results in FILE [disabled]
  -C, --config-cache      alias for `--cache-file=config.cache'
  -n, --no-create         do not create output files
      --srcdir=DIR        find the sources in DIR [configure dir or `..']

Installation directories:
  --prefix=PREFIX         install architecture-independent files in PREFIX
                          [/usr/local/pgsql]
  --exec-prefix=EPREFIX   install architecture-dependent files in EPREFIX
                          [PREFIX]

For better control, use the options below.

Fine tuning of the installation directories:
  --bindir=DIR            user executables [EPREFIX/bin]
  --sbindir=DIR           system admin executables [EPREFIX/sbin]
  --libexecdir=DIR        program executables [EPREFIX/libexec]
  --sysconfdir=DIR        read-only single-machine data [PREFIX/etc]
  --sharedstatedir=DIR    modifiable architecture-independent data [PREFIX/com]
  --localstatedir=DIR     modifiable single-machine data [PREFIX/var]
  --libdir=DIR            object code libraries [EPREFIX/lib]
  --includedir=DIR        C header files [PREFIX/include]
  --oldincludedir=DIR     C header files for non-gcc [/usr/include]
  --datarootdir=DIR       read-only arch.-independent data root [PREFIX/share]
  --datadir=DIR           read-only architecture-independent data [DATAROOTDIR]
  --infodir=DIR           info documentation [DATAROOTDIR/info]
  --localedir=DIR         locale-dependent data [DATAROOTDIR/locale]
  --mandir=DIR            man documentation [DATAROOTDIR/man]
  --docdir=DIR            documentation root [DATAROOTDIR/doc/postgresql]
  --htmldir=DIR           html documentation [DOCDIR]
  --dvidir=DIR            dvi documentation [DOCDIR]
  --pdfdir=DIR            pdf documentation [DOCDIR]
  --psdir=DIR             ps documentation [DOCDIR]

System types:
  --build=BUILD     configure for building on BUILD [guessed]
  --host=HOST       cross-compile to build programs to run on HOST [BUILD]

Use these variables to override the choices made by `configure' or to help
it to find libraries and programs with nonstandard names/locations.

Report bugs to <pgsql-bugs@lists.postgresql.org>.
PostgreSQL home page: <https://www.postgresql.org/>.
[root@rijavan-node-01-test postgresql-14.7]# 
```

Step 5) Now create a directory where you want to install postgres files and use prefix option with configure.

By default, `make install' will install all the files in `/usr/local/pgsql/bin', `/usr/local/pgsql/lib' etc.  
You can specify an installation prefix other than `/usr/local/pgsql' using `--prefix', for instance `--prefix=$HOME'.

```
[root@rijavan-node-01-test postgresql-14.7]# mkdir /PostgreSQL-14/
[root@rijavan-node-01-test postgresql-14.7]# 
[root@rijavan-node-01-test postgresql-14.7]# ./configure --prefix=/PostgreSQL-14/
```
Note-: We may get missing OS packages error here at the time of configure if we have not installed following dependancy packages gcc, readline-devel and zlib-devel

Sample output-: 

```
checking build system type... x86_64-pc-linux-gnu
checking host system type... x86_64-pc-linux-gnu
checking which template to use... linux
checking whether NLS is wanted... no
checking for default port number... 5432
checking for block size... 8kB
checking for segment size... 1GB
checking for WAL block size... 8kB
checking for gcc... gcc
checking whether the C compiler works... yes
checking for C compiler default output file name... a.out
checking for suffix of executables... 
checking whether we are cross compiling... no
checking for suffix of object files... o
checking whether we are using the GNU C compiler... yes
checking whether gcc accepts -g... yes
checking for gcc option to accept ISO C89... none needed
checking for gcc option to accept ISO C99... -std=gnu99
checking for g++... no
checking for c++... no
checking whether we are using the GNU C++ compiler... no
checking whether g++ accepts -g... no
checking for gawk... gawk
......
......
......
......

config.status: creating GNUmakefile
config.status: creating src/Makefile.global
config.status: creating src/include/pg_config.h
config.status: creating src/include/pg_config_ext.h
config.status: creating src/interfaces/ecpg/include/ecpg_config.h
config.status: linking src/backend/port/tas/dummy.s to src/backend/port/tas.s
config.status: linking src/backend/port/posix_sema.c to src/backend/port/pg_sema.c
config.status: linking src/backend/port/sysv_shmem.c to src/backend/port/pg_shmem.c
config.status: linking src/include/port/linux.h to src/include/pg_config_os.h
config.status: linking src/makefiles/Makefile.linux to src/Makefile.port
[root@rijavan-node-01-test postgresql-14.7]# 
```
Step 6) After configuring, next we will start to build PostgreSQL using following make command.

```
[root@rijavan-node-01-test postgresql-14.7]# make
```
Sample output-: 

```
[root@rijavan-node-01-test postgresql-14.7]# make
make -C ./src/backend generated-headers
make[1]: Entering directory `/root/postgresql-14.7/src/backend'
make -C catalog distprep generated-header-symlinks
make[2]: Entering directory `/root/postgresql-14.7/src/backend/catalog'
make[2]: Nothing to be done for `distprep'.
.......
.......
.......
.......

make[2]: Leaving directory `/root/postgresql-14.7/src/test/isolation'
make -C test/perl all
make[2]: Entering directory `/root/postgresql-14.7/src/test/perl'
make[2]: Nothing to be done for `all'.
make[2]: Leaving directory `/root/postgresql-14.7/src/test/perl'
make[1]: Leaving directory `/root/postgresql-14.7/src'
make -C config all
make[1]: Entering directory `/root/postgresql-14.7/config'
make[1]: Nothing to be done for `all'.
make[1]: Leaving directory `/root/postgresql-14.7/config'
```

Step 7)After build process finishes, now install PostgreSQL using following commands. 


# make install

This will install files into the directories that were specified in above step Make sure that you have appropriate permissions to write into that area. Normally you need to do this step as root.

To install the documentation (HTML and man pages) enter:

# make install-docs

If you built the world above, type instead:

# make install-world

 This also installs the documentation.

```
[root@rijavan-node-01-test postgresql-14.7]# make install-world
```

sample output-: 

```
make -C ./src/backend generated-headers
make[1]: Entering directory `/root/postgresql-14.7/src/backend'
make -C catalog distprep generated-header-symlinks
make[2]: Entering directory `/root/postgresql-14.7/src/backend/catalog'
make[2]: Nothing to be done for `distprep'.
make[2]: Nothing to be done for `generated-header-symlinks'.
make[2]: Leaving directory `/root/postgresql-14.7/src/backend/catalog'
make -C utils distprep generated-header-symlinks
make[2]: Entering directory `/root/postgresql-14.7/src/backend/utils'
make[2]: Nothing to be done for `distprep'.
make[2]: Nothing to be done for `generated-header-symlinks'.
make[2]: Leaving directory `/root/postgresql-14.7/src/backend/utils'
make[1]: Leaving directory `/root/postgresql-14.7/src/backend'
make -C doc install
make[1]: Entering directory `/root/postgresql-14.7/doc'
make -C src install
make[2]: Entering directory `/root/postgresql-14.7/doc/src'
make -C sgml install
make[3]: Entering directory `/root/postgresql-14.7/doc/src/sgml'
.....

.....

.....

......

/bin/mkdir -p '/PostgreSQL-14/bin'
/bin/install -c  vacuumlo '/PostgreSQL-14/bin'
make[2]: Leaving directory `/root/postgresql-14.7/contrib/vacuumlo'
make[1]: Leaving directory `/root/postgresql-14.7/contrib'
[root@rijavan-node-01-test postgresql-14.7]# 
```
Postgresql 14 has been installed in /PostgreSQL-14/ directory.

```
[root@rijavan-node-01-test postgresql-14.7]# du -sch /PostgreSQL-14/
52M	/PostgreSQL-14/
52M	total
[root@rijavan-node-01-test postgresql-14.7]# cd /PostgreSQL-14/
[root@rijavan-node-01-test PostgreSQL-14]# ls -lrth 
total 12K
drwxr-xr-x. 5 root root   46 Apr  8 15:47 share
drwxr-xr-x. 4 root root 4.0K Apr  8 15:47 include
drwxr-xr-x. 4 root root 4.0K Apr  8 15:47 lib
drwxr-xr-x. 2 root root 4.0K Apr  8 15:47 bin
[root@rijavan-node-01-test PostgreSQL-14]#
```

Step 8) Now create a "postgres" user and directory to be used as data directory for initializing database cluster. Owner of this data directory should be "postgres" user and permissions should be 700 and also set bash_profile/ environment variables for PostgreSQL binaries for our ease.

User creation-: 

```
[root@rijavan-node-01-test PostgreSQL-14]# id postgres
id: postgres: no such user
[root@rijavan-node-01-test PostgreSQL-14]#
[root@rijavan-node-01-test PostgreSQL-14]# useradd postgres
[root@rijavan-node-01-test PostgreSQL-14]# passwd postgres
Changing password for user postgres.
New password: 
BAD PASSWORD: The password contains the user name in some form
Retype new password: 
passwd: all authentication tokens updated successfully.
[root@rijavan-node-01-test PostgreSQL-14]# 
```

Creating data directory-: 

```
[root@rijavan-node-01-test PostgreSQL-14]# mkdir /PostgreSQL-14/data/
[root@rijavan-node-01-test PostgreSQL-14]# chown postgres:postgres -R /PostgreSQL-14/data/
[root@rijavan-node-01-test PostgreSQL-14]# chmod 700 -R /PostgreSQL-14/data/
[root@rijavan-node-01-test PostgreSQL-14]# 
```
Setting up bash_profile/environment variables-:Setting environment variables are useful when performing tasks within PostgreSQL including starting and shutting down the postmaster processes.

```
[root@rijavan-node-01-test PostgreSQL-14]# sudo su - postgres 
[postgres@rijavan-node-01-test ~]$ echo "PATH=/PostgreSQL-14/bin" >> ~/.bash_profile
[postgres@rijavan-node-01-test ~]$ echo "PGDATA=/PostgreSQL-14/data" >> .bash_profile
[postgres@rijavan-node-01-test ~]$ echo "PGDATABASE=postgres" >> ~/.bash_profile
[postgres@rijavan-node-01-test ~]$ echo "PGUSER=postgres" >> ~/.bash_profile
[postgres@rijavan-node-01-test ~]$ echo "PGPORT=5432" >> ~/.bash_profile
[postgres@rijavan-node-01-test ~]$ cat .bash_profile 
# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
	. ~/.bashrc
fi

# User specific environment and startup programs

PATH=$PATH:$HOME/.local/bin:$HOME/bin

export PATH
PATH=/PostgreSQL-14/bin
PGDATA=/PostgreSQL-14/data
PGDATABASE=postgres
PGUSER=postgres
PGPORT=5432
[postgres@rijavan-node-01-test ~]$
```
Run the source command-: 

```
[postgres@rijavan-node-01-test ~]$ source ~/.bash_profile
```
Check environment variables set or not

```
[postgres@rijavan-node-01-test ~]$ psql --version
psql (PostgreSQL) 14.7
[postgres@rijavan-node-01-test ~]$ 
```

[environment variables](https://www.postgresql.org/docs/14/libpq-envars.html)


Step 9) Now initialize database using the following "initdb" command as "postgres" user.

```
[postgres@rijavan-node-01-test ~]$ initdb -D /PostgreSQL-14/data/ -U postgres -W 
The files belonging to this database system will be owned by user "postgres".
This user must also own the server process.

The database cluster will be initialized with locale "en_US.UTF-8".
The default database encoding has accordingly been set to "UTF8".
The default text search configuration will be set to "english".

Data page checksums are disabled.

Enter new superuser password: 
Enter it again: 

fixing permissions on existing directory /PostgreSQL-14/data ... ok
creating subdirectories ... ok
selecting dynamic shared memory implementation ... posix
selecting default max_connections ... 100
selecting default shared_buffers ... 128MB
selecting default time zone ... UTC
creating configuration files ... ok
running bootstrap script ... ok
performing post-bootstrap initialization ... sh: locale: command not found
2023-04-08 16:55:09.640 UTC [20014] WARNING:  no usable system locales were found
ok
syncing data to disk ... ok

initdb: warning: enabling "trust" authentication for local connections
You can change this by editing pg_hba.conf or using the option -A, or
--auth-local and --auth-host, the next time you run initdb.

Success. You can now start the database server using:

    pg_ctl -D /PostgreSQL-14/data/ -l logfile start

[postgres@rijavan-node-01-test ~]$ 
```
Where -D is location for this database cluster or we can say it is data directory where we want to initialize database cluster, -U for database superuser name and -W for password prompt for database superuser.

For more info and options we can refer initdb –help.

```
[postgres@rijavan-node-01-test ~]$ initdb --help
initdb initializes a PostgreSQL database cluster.

Usage:
  initdb [OPTION]... [DATADIR]

Options:
  -A, --auth=METHOD         default authentication method for local connections
      --auth-host=METHOD    default authentication method for local TCP/IP connections
      --auth-local=METHOD   default authentication method for local-socket connections
 [-D, --pgdata=]DATADIR     location for this database cluster
  -E, --encoding=ENCODING   set default encoding for new databases
  -g, --allow-group-access  allow group read/execute on data directory
  -k, --data-checksums      use data page checksums
      --locale=LOCALE       set default locale for new databases
      --lc-collate=, --lc-ctype=, --lc-messages=LOCALE
      --lc-monetary=, --lc-numeric=, --lc-time=LOCALE
                            set default locale in the respective category for
                            new databases (default taken from environment)
      --no-locale           equivalent to --locale=C
      --pwfile=FILE         read password for the new superuser from file
  -T, --text-search-config=CFG
                            default text search configuration
  -U, --username=NAME       database superuser name
  -W, --pwprompt            prompt for a password for the new superuser
  -X, --waldir=WALDIR       location for the write-ahead log directory
      --wal-segsize=SIZE    size of WAL segments, in megabytes

Less commonly used options:
  -d, --debug               generate lots of debugging output
      --discard-caches      set debug_discard_caches=1
  -L DIRECTORY              where to find the input files
  -n, --no-clean            do not clean up after errors
  -N, --no-sync             do not wait for changes to be written safely to disk
      --no-instructions     do not print instructions for next steps
  -s, --show                show internal settings
  -S, --sync-only           only sync data directory

Other options:
  -V, --version             output version information, then exit
  -?, --help                show this help, then exit

If the data directory is not specified, the environment variable PGDATA
is used.

Report bugs to <pgsql-bugs@lists.postgresql.org>.
PostgreSQL home page: <https://www.postgresql.org/>
[postgres@rijavan-node-01-test ~]$
```

Step 10) After initializing database, start the database cluster or if we need to change port or listen address for server, edit the postgresql.conf file in data directory of database server.

```
[postgres@rijavan-node-01-test ~]$ vi /PostgreSQL-14/data/postgresql.conf 
## change port and listen_addresses
# - Connection Settings -

listen_addresses = '*'          # what IP address(es) to listen on;
                                        # comma-separated list of addresses;
                                        # defaults to 'localhost'; use '*' for all
                                        # (change requires restart)
port = 5432                             # (change requires restart)

:wq!
```

```
[postgres@rijavan-node-01-test ~]$ pg_ctl -D /PostgreSQL-14/data/ -l logfile start
waiting for server to start.... done
server started
[postgres@rijavan-node-01-test ~]$
[postgres@rijavan-node-01-test ~]$ pwd
/home/postgres
[postgres@rijavan-node-01-test ~]$ cat logfile 
2023-04-08 17:06:41.811 UTC [20084] LOG:  starting PostgreSQL 14.7 on x86_64-pc-linux-gnu, compiled by gcc (GCC) 4.8.5 20150623 (Red Hat 4.8.5-44), 64-bit
2023-04-08 17:06:41.812 UTC [20084] LOG:  listening on IPv4 address "0.0.0.0", port 5432
2023-04-08 17:06:41.812 UTC [20084] LOG:  listening on IPv6 address "::", port 5432
2023-04-08 17:06:41.815 UTC [20084] LOG:  listening on Unix socket "/tmp/.s.PGSQL.5432"
2023-04-08 17:06:41.820 UTC [20085] LOG:  database system was shut down at 2023-04-08 16:55:09 UTC
2023-04-08 17:06:41.823 UTC [20084] LOG:  database system is ready to accept connections
[postgres@rijavan-node-01-test ~]$
```
Step 11) After starting database, verify the status of PostgreSQL server process by using following commands.

```
[postgres@rijavan-node-01-test ~]$ ps -ef |grep -i postgres
postgres 20084     1  0 17:06 ?        00:00:00 /PostgreSQL-14/bin/postgres -D /PostgreSQL-14/data
postgres 20086 20084  0 17:06 ?        00:00:00 postgres: checkpointer 
postgres 20087 20084  0 17:06 ?        00:00:00 postgres: background writer 
postgres 20088 20084  0 17:06 ?        00:00:00 postgres: walwriter 
postgres 20089 20084  0 17:06 ?        00:00:00 postgres: autovacuum launcher 
postgres 20090 20084  0 17:06 ?        00:00:00 postgres: stats collector 
postgres 20091 20084  0 17:06 ?        00:00:00 postgres: logical replication launcher 
root     20124  1226  0 17:13 pts/0    00:00:00 grep --color=auto -i postgres
[postgres@rijavan-node-01-test ~]$
[postgres@rijavan-node-01-test ~]$netstat -apn |grep -i 5432
tcp        0      0 0.0.0.0:5432            0.0.0.0:*               LISTEN      20084/postgres      
tcp6       0      0 :::5432                 :::*                    LISTEN      20084/postgres      
unix  2      [ ACC ]     STREAM     LISTENING     30336    20084/postgres       /tmp/.s.PGSQL.5432
[postgres@rijavan-node-01-test ~]$
```
We can see that database cluster is running fine, and startup logs can be found at location specified with -l option (-l logfile) while starting database cluster.

Step 12) Now connect to database cluster and cross verify below details.

```
[postgres@rijavan-node-01-test ~]$ psql
psql (14.7)
Type "help" for help.

postgres=# select version();
                                                 version                                                 
---------------------------------------------------------------------------------------------------------
 PostgreSQL 14.7 on x86_64-pc-linux-gnu, compiled by gcc (GCC) 4.8.5 20150623 (Red Hat 4.8.5-44), 64-bit
(1 row)

postgres=# show data_directory;
   data_directory    
---------------------
 /PostgreSQL-14/data
(1 row)

postgres=# show port;
 port 
------
 5432
(1 row)

postgres=# show listen_addresses;
 listen_addresses 
------------------
 *
(1 row)

postgres=# 
postgres=# \q --->> to quit form postgres console.
[postgres@rijavan-node-01-test ~]$ 
```
# Post Installation steps

We can see PostgreSQL binaries in below location as we have mentioned --prefix after step 7 it was generated check below sample outout for the validation.

```
[root@rijavan-node-01-test bin]# pwd
/PostgreSQL-14/bin
[root@rijavan-node-01-test bin]# ls -lrth 
total 14M
-rwxr-xr-x. 1 root root 8.4M Apr  8 15:47 postgres
lrwxrwxrwx. 1 root root    8 Apr  8 15:47 postmaster -> postgres
-rwxr-xr-x. 1 root root 970K Apr  8 15:47 ecpg
-rwxr-xr-x. 1 root root 145K Apr  8 15:47 initdb
-rwxr-xr-x. 1 root root 109K Apr  8 15:47 pg_amcheck
-rwxr-xr-x. 1 root root  48K Apr  8 15:47 pg_archivecleanup
-rwxr-xr-x. 1 root root 138K Apr  8 15:47 pg_basebackup
-rwxr-xr-x. 1 root root  97K Apr  8 15:47 pg_receivewal
-rwxr-xr-x. 1 root root  97K Apr  8 15:47 pg_recvlogical
-rwxr-xr-x. 1 root root  66K Apr  8 15:47 pg_checksums
-rwxr-xr-x. 1 root root  43K Apr  8 15:47 pg_config
-rwxr-xr-x. 1 root root  61K Apr  8 15:47 pg_controldata
-rwxr-xr-x. 1 root root  76K Apr  8 15:47 pg_ctl
-rwxr-xr-x. 1 root root 423K Apr  8 15:47 pg_dump
-rwxr-xr-x. 1 root root 193K Apr  8 15:47 pg_restore
-rwxr-xr-x. 1 root root 117K Apr  8 15:47 pg_dumpall
-rwxr-xr-x. 1 root root  71K Apr  8 15:47 pg_resetwal
-rwxr-xr-x. 1 root root 145K Apr  8 15:47 pg_rewind
-rwxr-xr-x. 1 root root  49K Apr  8 15:47 pg_test_fsync
-rwxr-xr-x. 1 root root  43K Apr  8 15:47 pg_test_timing
-rwxr-xr-x. 1 root root 159K Apr  8 15:47 pg_upgrade
-rwxr-xr-x. 1 root root 117K Apr  8 15:47 pg_verifybackup
-rwxr-xr-x. 1 root root 107K Apr  8 15:47 pg_waldump
-rwxr-xr-x. 1 root root 194K Apr  8 15:47 pgbench
-rwxr-xr-x. 1 root root 642K Apr  8 15:47 psql
-rwxr-xr-x. 1 root root  86K Apr  8 15:47 createdb
-rwxr-xr-x. 1 root root  78K Apr  8 15:47 dropdb
-rwxr-xr-x. 1 root root  83K Apr  8 15:47 createuser
-rwxr-xr-x. 1 root root  77K Apr  8 15:47 dropuser
-rwxr-xr-x. 1 root root  82K Apr  8 15:47 clusterdb
-rwxr-xr-x. 1 root root  91K Apr  8 15:47 vacuumdb
-rwxr-xr-x. 1 root root  91K Apr  8 15:47 reindexdb
-rwxr-xr-x. 1 root root  77K Apr  8 15:47 pg_isready
-rwxr-xr-x. 1 root root  53K Apr  8 15:47 oid2name
-rwxr-xr-x. 1 root root  53K Apr  8 15:47 vacuumlo
[root@rijavan-node-01-test bin]#
```

If we have installed PostgreSQL by source method then we need to use "pg_ctl" utility to start/stop/restart/reload PostgreSQL server, if we don't want to use "pg_ctl" utility instead of this we want "systemctl" Operating system utility like default/package based method of installation and after a reboot of operating system or after killing PostgreSQL process/postmaster.pid we want to auto start/bootup PostgreSQL server then we need to do following steps to achive it.

```
[root@rijavan-node-01-test ~]# sudo vi /usr/lib/systemd/system/pgcluster14.service
[Unit]
Description=PostgreSQL database server
After=network.target

[Service]
Restart=on-failure
RestartSec=5s
TimeoutSec=120
User=postgres
Group=postgres
Environment=PGDATA=/PostgreSQL-14/data/
PIDFile=/PostgreSQL-14/data/postmaster.pid
ExecStart= /PostgreSQL-14/bin/pg_ctl -s -D ${PGDATA} start -w -t 120
ExecReload=/PostgreSQL-14/bin/pg_ctl -s -D ${PGDATA} reload
ExecStop=  /PostgreSQL-14/bin/pg_ctl -s -D ${PGDATA} stop -m fast
# Due to PostgreSQL's use of shared memory, OOM killer is often overzealous in
# killing Postgres, so adjust it downward
OOMScoreAdjust=-200

[Install]
WantedBy=multi-user.target

:wq!
```
```
[root@rijavan-node-01-test ~]# sudo systemctl daemon-reload
[root@rijavan-node-01-test ~]# sudo systemctl status pgcluster14.service
● pgcluster14.service - PostgreSQL database server
   Loaded: loaded (/usr/lib/systemd/system/pgcluster14.service; disabled; vendor preset: disabled)
   Active: inactive (dead)
[root@rijavan-node-01-test ~]# 
[root@rijavan-node-01-test ~]# sudo systemctl is-enabled pgcluster14.service
disabled
[root@rijavan-node-01-test ~]# sudo systemctl enable pgcluster14.service
Created symlink from /etc/systemd/system/multi-user.target.wants/pgcluster14.service to /usr/lib/systemd/system/pgcluster14.service.
[root@rijavan-node-01-test ~]# 
[root@rijavan-node-01-test ~]# sudo su - postgres
Last login: Sun Apr  9 08:09:02 UTC 2023 on pts/0
[postgres@rijavan-node-01-test ~]$ 
[postgres@rijavan-node-01-test ~]$ pg_ctl -D /PostgreSQL-14/data/ status 
pg_ctl: server is running (PID: 20084)
/PostgreSQL-14/bin/postgres "-D" "/PostgreSQL-14/data"
[postgres@rijavan-node-01-test ~]$ pg_ctl -D /PostgreSQL-14/data/ stop
waiting for server to shut down.... done
server stopped
[root@rijavan-node-01-test bin]# systemctl daemon-reload
[root@rijavan-node-01-test bin]# systemctl start  pgcluster14.service
[root@rijavan-node-01-test bin]# 
[root@rijavan-node-01-test bin]# ps -ef |grep postgres 
postgres 21835     1  0 08:14 ?        00:00:00 /PostgreSQL-14/bin/postgres -D /PostgreSQL-14/data
postgres 21837 21835  0 08:14 ?        00:00:00 postgres: checkpointer 
postgres 21838 21835  0 08:14 ?        00:00:00 postgres: background writer 
postgres 21839 21835  0 08:14 ?        00:00:00 postgres: walwriter 
postgres 21840 21835  0 08:14 ?        00:00:00 postgres: autovacuum launcher 
postgres 21841 21835  0 08:14 ?        00:00:00 postgres: stats collector 
postgres 21842 21835  0 08:14 ?        00:00:00 postgres: logical replication launcher 
root     21850 21533  0 08:14 pts/0    00:00:00 grep --color=auto postgres
[root@rijavan-node-01-test bin]#
```
Now after a reboot of operating system we can see the PostgreSQL service is automatically running.

Now Rebooting opreating system to cross check-:
```
[root@rijavan-node-01-test bin]# init 6
PolicyKit daemon disconnected from the bus.
We are no longer a registered authentication agent.
Connection to 172.19.15.5 closed by remote host.
Connection to 172.19.15.5 closed.
```
PostgreSQL service status after reboot-: 

```
[centos@rijavan-node-01-test ~]$ uptime 
 08:23:41 up 1 min,  1 user,  load average: 0.08, 0.07, 0.03
[centos@rijavan-node-01-test ~]$ ps -ef |grep postgres
postgres   808     1  0 08:21 ?        00:00:00 /PostgreSQL-14/bin/postgres -D /PostgreSQL-14/data
postgres   857   808  0 08:21 ?        00:00:00 postgres: checkpointer 
postgres   858   808  0 08:21 ?        00:00:00 postgres: background writer 
postgres   859   808  0 08:21 ?        00:00:00 postgres: walwriter 
postgres   860   808  0 08:21 ?        00:00:00 postgres: autovacuum launcher 
postgres   861   808  0 08:21 ?        00:00:00 postgres: stats collector 
postgres   862   808  0 08:21 ?        00:00:00 postgres: logical replication launcher 
centos    1201  1181  0 08:23 pts/0    00:00:00 grep --color=auto postgres
[centos@rijavan-node-01-test ~]$ sudo su - postgres
Last login: Sun Apr  9 08:10:34 UTC 2023 on pts/0
[postgres@rijavan-node-01-test ~]$ psql
psql (14.7)
Type "help" for help.

postgres=# SSELECT current_timestamp - pg_postmaster_start_time() as uptime;
    uptime
-----------------
 00:01:30.306699
(1 row)

postgres=# \q  --->> to quit form postgres console.
```
Now killing the postmster.pid/PostgreSQL process manually to cross check-: 

```
[root@rijavan-node-01-test ~]# ps -ef |grep postgres 
postgres   808     1  0 08:21 ?        00:00:00 /PostgreSQL-14/bin/postgres -D /PostgreSQL-14/data
postgres   857   808  0 08:21 ?        00:00:00 postgres: checkpointer 
postgres   858   808  0 08:21 ?        00:00:00 postgres: background writer 
postgres   859   808  0 08:21 ?        00:00:00 postgres: walwriter 
postgres   860   808  0 08:21 ?        00:00:00 postgres: autovacuum launcher 
postgres   861   808  0 08:21 ?        00:00:00 postgres: stats collector 
postgres   862   808  0 08:21 ?        00:00:00 postgres: logical replication launcher 
root      1295  1279  0 08:44 pts/0    00:00:00 grep --color=auto postgres
[root@rijavan-node-01-test ~]# cd /PostgreSQL-14/data/
[root@rijavan-node-01-test data]# cat postmaster.pid 
808
/PostgreSQL-14/data
1681028511
5432
/tmp
*
   117817         0
ready   
[root@rijavan-node-01-test data]# kill -9 808
[root@rijavan-node-01-test data]# ps -ef |grep postgres
postgres  1301     1  2 08:44 ?        00:00:00 /PostgreSQL-14/bin/postgres -D /PostgreSQL-14/data
postgres  1303  1301  0 08:44 ?        00:00:00 postgres: checkpointer 
postgres  1304  1301  0 08:44 ?        00:00:00 postgres: background writer 
postgres  1305  1301  0 08:44 ?        00:00:00 postgres: walwriter 
postgres  1306  1301  0 08:44 ?        00:00:00 postgres: autovacuum launcher 
postgres  1307  1301  0 08:44 ?        00:00:00 postgres: stats collector 
postgres  1308  1301  0 08:44 ?        00:00:00 postgres: logical replication launcher 
root      1310  1279  0 08:44 pts/0    00:00:00 grep --color=auto postgres
[root@rijavan-node-01-test data]# sudo su - postgres
Last login: Sun Apr  9 08:25:34 UTC 2023 on pts/0
[postgres@rijavan-node-01-test ~]$ 
[postgres@rijavan-node-01-test ~]$ psql
psql (14.7)
Type "help" for help.

postgres=# SELECT current_timestamp - pg_postmaster_start_time() as uptime;
     uptime      
-----------------
 00:00:14.973522
(1 row)

postgres=# \q  --->> to quit form postgres console.
```

we can see above automatically PostgreSQL service has been started with new pid.

That’s It! in next upcoming article, we will cover upgradation of PostgreSQL if PostgreSQL installed with source code till then stay tuned with EDB.

  

