# Descoped Atlassian Fisheye and Crucible

## About

Fisheye provides a read-only window into your Subversion, Perforce, CVS, Git, and Mercurial repositories, all in one place. Keep a pulse on everything about your code: Visualize and report on activity, integrate source with JIRA issues, and search for commits, files, revisions, or people.

Crucible is a collaborative code review application used to find bugs and improve code quality through peer code review.

For all aspects about configuring, using and administering Fisheye please see the official [Fisheye Documentation](https://confluence.atlassian.com/fisheye/fisheye-documentation-home-298976794.html) and [Crucible Documentation](https://confluence.atlassian.com/crucible/crucible-documentation-home-298977323.html). 

## How to use?

The examples shown below assume you will use a MySQL database.

> Please pay attention to the IP addresses used in the examples below. The IP `192.168.1.2` refers to your host OS. The IP `172.17.0.2` refers to the MySQL database and the IP `172.17.0.3` to the newly installed Fisheye guest OS. To figure out the IP in your guest OS you can either connect to a running instance by issuing `docker exec -it [container-name] /bin/bash` and do `ifconfig` or locate the IP from `docker inspect [container-name]`.


### Prerequisites

* MySQL 5.5 or 5.6 (please notice that Fisheye is not compatible with MySQL 5.7)
* PostgreSQL 8.4+

> Important notice: The Postgres driver is shipped with the Fisheye distribution, whereas the MySQL driver will be downloaded when running the image.


#### Database Setup

MySQL setup (assuming that MySQL isn't installed yet):

```
$ docker run -d -p 3306:3306 --name mysql -v /var/mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=[db-password] mysql/mysql-server:5.6
$ mysql -h 172.17.0.2 -u root -p[db-password]
CREATE DATABASE IF NOT EXISTS fisheye CHARACTER SET utf8 COLLATE utf8_bin;
```

If you use a default Docker installation with no images installed, the assigned IP for MySQL will be: `172.17.0.2`.

Optionally you may configure security constraints by:

```
GRANT ALL PRIVILEGES ON fisheye.* TO '[appuser]'@'172.17.0.3' IDENTIFIED BY '[apppassword]' with grant option;
```

> Please notice that the `[appuser]` and `[apppassword]` must be configured to what is appropriate for your system.


### Installation

Run docker using port 8060 on your host (if available):

```
docker run -p 8060:8060 descoped/fisheye-crucible
```


Run with data outside the container using a volume:

```
$ docker run --name fisheye -v /var/fisheye:/var/atlassian-home -e CONTEXT_PATH=ROOT -e DATABASE_URL=mysql://[username]:[password]@172.17.0.2/fisheye -e INSTALL_CRUCIBLE= -p 8060:8060 descoped/fisheye-crucible
```


To stop the running instance:

```
$ docker fisheye stop
```


To start running instance:

```
$ docker fisheye start
```


#### Docker Volume

The mappable VOLUME is: `/var/atlassian-home`

#### Browser URL:

```
http://192.168.1.2:8060/
```


The host IP is assumed to be `192.168.1.2`.


### Configuration

#### Database connection

The connection to the database can be specified with an URL of the format:
```
[database type]://[username]:[password]@[host]:[port]/[database name]
```


Where ```database type``` is either ```mysql``` or ```postgresql``` and the full URL look like this:

**MySQL:**

```
mysql://<username>:<password>@172.17.0.2/fisheye
```


**PostgreSQL:**

```
postgresql://<username>:<password>@172.17.0.2/fisheye
```


### Environement variables

Configuration options are set by setting environment variables when running the image. What follows it a table of the supported variables:

Variable         | Function
-----------------|------------------------------
CONTEXT_PATH     | Context path of the Fisheye webapp. You can set this to add a path prefix to the url used to access the webapp. i.e. setting this to ```fisheye``` will change the url to http://192.168.1.2:8060/fisheye/. The value ```ROOT``` is reserved to mean that you don't want a context path prefix. Defaults to ```ROOT```
DATABASE_URL     | Connection URL specifying where and how to connect to a database dedicated to Fisheye. This variable is optional and if specified will cause the Fisheye setup wizard to skip the database setup set.


## Source code

If you want to contribute to this project or make use of the source code; you'll find it on [GitHub](https://github.com/descoped/docker-fisheye-crucible).

### Building the image

```
docker build -t descoped/fisheye-crucible .
```


### Further reading

* Reference to [base image](https://hub.docker.com/r/descoped/atlassian-base/)
