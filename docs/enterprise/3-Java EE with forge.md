If you’ve spent any time developing Java EE-based projects (or any nontrivial application, for that matter!), you’ve likely invested a good amount of energy in creating the project layout, defining dependencies, and informing the build system of the relevant class paths to be used in compilation and execution. Although Maven enables us to reduce that load as compared with undertaking project setup manually, there’s typically quite a bit of boilerplate involved in the *pom.xml* defining your requirements.

JBoss Forge offers an incremental project enhancement for Java EE. Implemented as a command shell and integration with some IDE, Forge gives us the ability to alter project files and folders. Some concrete tasks we might use Forge to handle are:

- Adding Java Persistence API (JPA) entities and describing their model
- Configuring Maven dependencies
- Setting up project scaffolding
- Generating a view layer, reverse-engineered from a domain model
- Deploying to an application server

# Installing Forge

1. Ensure that you have already JDK8 installed  and all setup as default . JDK 9 does not work with Forge
2. [Download](https://forge.jboss.org/download) and Un-zip Forge into a folder on your hard-disk, this folder will be your `FORGE_HOME`


3. Add `$FORGE_HOME/bin` to your path

On Unix based operating systems, this typically means editing your `~/.bashrc` or `~/.profile`; you will need to the following entries:

```bash
export FORGE_HOME=~/forge/
export PATH=$PATH:$FORGE_HOME/bin
```

# Running Forge

While on Windows, you will need to right-click on open the “Control Panel”, then click “System Properties”, open the “Advanced” tab, then click “Environment Variables” and add these two entries visually. It is recommended to set User variables for Forge, unless you have placed the unzipped distribution in a folder where all users can access it.

You may also simply create a shortcut to `bin/forge` (Unix,) or `bin\forge.bat` (Windows,) and place it on your Desktop.

Open a command prompt and run `forge` – located in $FORGE_HOME/bin/ (if you are on Windows, you will need to run `forge.bat` unless using a Unix-style terminal)

```bash
localhost:~ $ forge
[~] $
```

 

# Sample scripts

### Create a project with custom package

```bash
$ project-new --named example --top-level-package org.mycomp.myapp --type war --final-name myExample --stack JAVA_EE_7
$ ls
```

### Create a new JPA @Entity class

```bash
$ project-new --named example --stack JAVA_EE_7
$ jpa-new-entity --named SampleEntity
$ jpa-new-field --named sampleField
$ jpa-new-field --named createdOn --type java.util.Date --temporal-type TIMESTAMP
$ ls
```

### Add an @N-To-N relationship between two @Entity classes

```bash
$ project-new --named example --stack JAVA_EE_7
$ jpa-new-entity --named Hurricane
$ jpa-new-entity --named Continent
$ jpa-new-field --named hurricanes --type org.example.model.Hurricane --relationship-type Many-to-Many --inverse-field-name continents
```

### Add Bean Validation constraints on your @Entity

```bash
$ jpa-new-entity --named Speaker
$ jpa-new-field --named firstname
$ jpa-new-field --named surname
$ jpa-new-field --named bio --length 2000
$ jpa-new-field --named twitter
$ constraint-add --on-property firstname --constraint NotNull
$ constraint-add --on-property surname --constraint NotNull
$ constraint-add --on-property bio --constraint Size --max 2000
```

### Create a REST endpoint from @Entity

```bash
$ project-new --named example --stack JAVA_EE_7
$ jpa-new-entity --named SampleEntity
$ jpa-new-field --named sampleField
$ rest-generate-endpoints-from-entities --targets org.example.model.SampleEntity
```

### Generate entites from an existing database

```bash
$ project-new --named example --stack JAVA_EE_7
$ jpa-setup
$ jpa-generate-entities-from-tables --jdbc-url jdbc:postgresql://localhost:5432/mydb --user-name user --user-password pwd --driver-location /Users/bob/.m2/repository/org/postgresql/postgresql/9.3-1100-jdbc3/postgresql-9.3-1100-jdbc3.jar --hibernate-dialect org.hibernate.dialect.PostgreSQLDialect
```

### Create a Java enum

```bash
java-new-enum --named Language
java-new-enum-const --named ENGLISH
java-new-enum-const --named FRENCH
java-new-enum-const --named PORTUGUESE
```

 

# Running in an IDE

- Install jboss developer studio
- Configure Maven
- Configure JDK 1.8
- Install and configure wildfly 10

Choose from the upper Menu: File | New | Server and expand the JBoss Community option. A “WildFly 10” Server is included:

[![img](https://community.bbd.co.za/wp-content/uploads/2016/11/wildfly_deploy1-.png)](https://community.bbd.co.za/wp-content/uploads/2016/11/wildfly_deploy1-.png)

In the next screen you have to choose if the Server is locally installed (Local) or it’s a remote server (Remote) and how the Server Lifecycle is managed:

[![img](https://community.bbd.co.za/wp-content/uploads/2016/11/wildfly10_eclipse_2-.png)](https://community.bbd.co.za/wp-content/uploads/2016/11/wildfly10_eclipse_2-.png)

Finally, point to the location where the application server is installed, the JRE execution environment and the configuration file to be used (default is standalone.xml) :

[![img](https://community.bbd.co.za/wp-content/uploads/2016/11/wildfly10_eclipse_3-.png)](https://community.bbd.co.za/wp-content/uploads/2016/11/wildfly10_eclipse_3-.png)

Remember that WildFly 10 application server requires a JDK 1.8, therefore you will not be able to start it with an older JDK version. Click Finish. WildFly is now configured on your Eclipse environment!

[![img](https://community.bbd.co.za/wp-content/uploads/2016/11/wildfly10_eclipse_4-.png)](https://community.bbd.co.za/wp-content/uploads/2016/11/wildfly10_eclipse_4-.png)

Click on the Icons in the toolbar to Debug / Start / Profile / Stop your Server. Right click on it and choose “Add or Remove ” to Deploy applications on it.

# 

# Create a Java Enterprise Application

Start the Forge Console in JEE perspective

copy the below script into your console

```bash
project-new--named shoppinglist --top-level-package com.bbd;
jpa-new-entity--namedshopping;
jpa-new-field --nameditem;
scaffold-setup--providerFaces;
scaffold-generate--providerFaces--targets com.bbd.model.*;
build;
```

Copy to server or Right click “run as server”

go to the URL <http://localhost:8080/shoppinglist>  and confirm your application is running

# 

# Exercise

create a hot-or-not application

- must store cars and celebrities
- must have a rating to indicate hot/not
- should be Angular front end
- must store url of the pictures for cars/celebrities and display on front-end (html hint below)

```html
<img src=”{{votableEnt.imageURL}}” style=”width:300px;” />
```



## Part 2

locate h2 jar
java -cp h2-1.3.173.jar org.h2.tools.Server (starts browser)
Change default datasource in standalone to be h2 server based
(Use url from browser)

## Part 3

Use JDBC to pull report and create CSV file
Create a sql statement on the average vote per category

 

## Solution – Part 1 forge script

```bash
project-new --named h2n --top-level-package org.example --type war --final-name h2n;
jpa-setup --provider Hibernate --container WILDFLY;
jpa-new-entity --named Car --target-package org.example.model;
jpa-new-field --named make; jpa-new-field --named model;
jpa-new-field --named vote --type java.lang.Integer;
constraint-add --on-property make --constraint NotNull;
constraint-add --on-property vote --constraint Min --value 1;
constraint-add --on-property vote --constraint Max --value 10;
jpa-new-entity --named Celebrity --target-package org.example.model;
jpa-new-field --named name; jpa-new-field --named surname;
jpa-new-field --named vote --type java.lang.Integer;
constraint-add --on-property name --constraint NotNull;
constraint-add --on-property surname --constraint NotNull;
constraint-add --on-property vote --constraint Min --value 1;
constraint-add --on-property vote --constraint Max --value 10;
cd ..;
rest-setup;
rest-generate-endpoints-from-entities --targets org.example.model.*;
scaffold-setup --provider AngularJS;
scaffold-generate --provider AngularJS --targets org.example.model.*;
cd ~~;
build;
```

## Solution – Complete application

<https://github.com/roryp/HotOrNot>