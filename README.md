# Running Flyway migration as Cloud Foundry "task"

```
./mvnw clean package -DskipTests=true
cf push flyway-migration -p target/flyway-migration-0.0.1-SNAPSHOT.jar --no-route --no-start
cf create-service mysql free demo-db
cf bind-service flyway-migration demo-db
cf start flyway-migration
cf run-task flyway-migration ".java-buildpack/open_jdk_jre/bin/java org.springframework.boot.loader.JarLauncher" --name migrate
cf logs flyway-migration --recent
cf run-task flyway-migration ".java-buildpack/open_jdk_jre/bin/java org.springframework.boot.loader.JarLauncher" --name migrate
cf logs flyway-migration --recent
```

Tested with

* CF API Version: 2.75.0 / 3.10.0
* cf CLI 6.26.0+9c9a261fd

## Running Task (1st time)

```console
$ cf run-task flyway-migration ".java-buildpack/open_jdk_jre/bin/java org.springframework.boot.loader.JarLauncher" --name migrate
Creating task for app flyway-migration in org *** / space *** as ***...
OK

Task has been submitted successfully for execution.
task name:   migrate
task id:     1
```

```console
$ cf logs flyway-migration --recent
..
2017-11-22T23:44:41.46+0900 [APP/TASK/migrate/0] OUT 2017-11-22 14:44:41.462  INFO 6 --- [           main] nfigurationApplicationContextInitializer : Adding cloud service auto-reconfiguration to ApplicationContext
2017-11-22T23:44:41.49+0900 [APP/TASK/migrate/0] OUT 2017-11-22 14:44:41.490  INFO 6 --- [           main] com.example.FlywayMigrationApplication   : The following profiles are active: cloud
2017-11-22T23:44:41.49+0900 [APP/TASK/migrate/0] OUT 2017-11-22 14:44:41.490  INFO 6 --- [           main] com.example.FlywayMigrationApplication   : Starting FlywayMigrationApplication on 9f01f6af-a074-409e-a67b-cc14181a1c86 with PID 6 (/home/vcap/app/BOOT-INF/classes started by vcap in /home/vcap/app)
2017-11-22T23:44:41.57+0900 [APP/TASK/migrate/0] OUT 2017-11-22 14:44:41.575  INFO 6 --- [           main] s.c.a.AnnotationConfigApplicationContext : Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@46fbb2c1: startup date [Wed Nov 22 14:44:41 UTC 2017]; root of context hierarchy
2017-11-22T23:44:42.66+0900 [APP/TASK/migrate/0] OUT 2017-11-22 14:44:42.667  INFO 6 --- [           main] urceCloudServiceBeanFactoryPostProcessor : Auto-reconfiguring beans of type javax.sql.DataSource
2017-11-22T23:44:42.69+0900 [APP/TASK/migrate/0] OUT 2017-11-22 14:44:42.690  INFO 6 --- [           main] o.c.r.o.s.c.s.r.PooledDataSourceCreator  : Found Tomcat JDBC connection pool on the classpath. Using it for DataSource connection pooling.
2017-11-22T23:44:43.29+0900 [APP/TASK/migrate/0] OUT 2017-11-22 14:44:43.291  INFO 6 --- [           main] o.f.core.internal.util.VersionPrinter    : Flyway 3.2.1 by Boxfuse
2017-11-22T23:44:43.30+0900 [APP/TASK/migrate/0] OUT 2017-11-22 14:44:43.302  WARN 6 --- [           main] o.a.tomcat.jdbc.pool.ConnectionPool      : maxIdle is larger than maxActive, setting maxIdle to: 4
2017-11-22T23:44:43.91+0900 [APP/TASK/migrate/0] OUT 2017-11-22 14:44:43.919  INFO 6 --- [           main] o.f.c.i.dbsupport.DbSupportFactory       : Database: jdbc:mysql://10.0.16.48:3306/cf_d98d8e61_9559_452c_a950_8e50b1a3adcb?user=oXbdI6zCXpRXvorC&password=HSNAjfEO4bgBlyvc (MySQL 5.5)
2017-11-22T23:44:43.99+0900 [APP/TASK/migrate/0] OUT 2017-11-22 14:44:43.991  INFO 6 --- [           main] o.f.core.internal.command.DbValidate     : Validated 2 migrations (execution time 00:00.031s)
2017-11-22T23:44:44.03+0900 [APP/TASK/migrate/0] OUT 2017-11-22 14:44:44.034  INFO 6 --- [           main] o.f.c.i.metadatatable.MetaDataTableImpl  : Creating Metadata table: `cf_d98d8e61_9559_452c_a950_8e50b1a3adcb`.`schema_version`
2017-11-22T23:44:44.12+0900 [APP/TASK/migrate/0] OUT 2017-11-22 14:44:44.126  INFO 6 --- [           main] o.f.core.internal.command.DbMigrate      : Migrating schema `cf_d98d8e61_9559_452c_a950_8e50b1a3adcb` to version 1 - create-schema
2017-11-22T23:44:44.12+0900 [APP/TASK/migrate/0] OUT 2017-11-22 14:44:44.126  INFO 6 --- [           main] o.f.core.internal.command.DbMigrate      : Current version of schema `cf_d98d8e61_9559_452c_a950_8e50b1a3adcb`: << Empty Schema >>
2017-11-22T23:44:44.17+0900 [APP/TASK/migrate/0] OUT 2017-11-22 14:44:44.170  INFO 6 --- [           main] o.f.core.internal.command.DbMigrate      : Migrating schema `cf_d98d8e61_9559_452c_a950_8e50b1a3adcb` to version 2 - import-initial-data
2017-11-22T23:44:44.20+0900 [APP/TASK/migrate/0] OUT 2017-11-22 14:44:44.200  INFO 6 --- [           main] o.f.core.internal.command.DbMigrate      : Successfully applied 2 migrations to schema `cf_d98d8e61_9559_452c_a950_8e50b1a3adcb` (execution time 00:00.169s).
2017-11-22T23:44:44.34+0900 [APP/TASK/migrate/0] OUT 2017-11-22 14:44:44.345  INFO 6 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2017-11-22T23:44:44.38+0900 [APP/TASK/migrate/0] OUT {id=2, message=hello2}
2017-11-22T23:44:44.38+0900 [APP/TASK/migrate/0] OUT {id=3, message=hello3}
2017-11-22T23:44:44.38+0900 [APP/TASK/migrate/0] OUT {id=1, message=hello1}
2017-11-22T23:44:44.39+0900 [APP/TASK/migrate/0] OUT 2017-11-22 14:44:44.391  INFO 6 --- [       Thread-2] s.c.a.AnnotationConfigApplicationContext : Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@46fbb2c1: startup date [Wed Nov 22 14:44:41 UTC 2017]; root of context hierarchy
2017-11-22T23:44:44.39+0900 [APP/TASK/migrate/0] OUT 2017-11-22 14:44:44.393  INFO 6 --- [       Thread-2] o.s.j.e.a.AnnotationMBeanExporter        : Unregistering JMX-exposed beans on shutdown
2017-11-22T23:44:44.39+0900 [APP/TASK/migrate/0] OUT 2017-11-22 14:44:44.389  INFO 6 --- [           main] com.example.FlywayMigrationApplication   : Started FlywayMigrationApplication in 4.12 seconds (JVM running for 4.856)
2017-11-22T23:44:44.41+0900 [APP/TASK/migrate/0] OUT Exit status 0
2017-11-22T23:44:44.44+0900 [APP/TASK/migrate/0] OUT Destroying container
2017-11-22T23:44:45.18+0900 [APP/TASK/migrate/0] OUT Successfully destroyed container~
```

## Running Task (2nd time)

```console
$ cf run-task flyway-migration ".java-buildpack/open_jdk_jre/bin/java org.springframework.boot.loader.JarLauncher" --name migrate
Creating task for app flyway-migration in org *** / space *** as ***...
OK

Task has been submitted successfully for execution.
task name:   migrate
task id:     2
```

```console
$ cf logs flyway-migration --recent
(..omit..)
2017-11-23T01:56:47.54+0900 [APP/TASK/migrate/0] OUT 2017-11-22 16:56:47.545  INFO 7 --- [           main] o.f.core.internal.command.DbMigrate      : Schema `cf_d98d8e61_9559_452c_a950_8e50b1a3adcb` is up to date. No migration necessary.
(..omit..)
```

## Task results

```console
$ cf tasks flyway-migration
Getting tasks for app flyway-migration in org *** / space *** as ***...
OK

id   name      state       start time                      command
2    migrate   SUCCEEDED   Wed, 22 Nov 2017 17:16:43 UTC   .java-buildpack/open_jdk_jre/bin/java org.springframework.boot.loader.JarLauncher
1    migrate   SUCCEEDED   Wed, 22 Nov 2017 17:15:19 UTC   .java-buildpack/open_jdk_jre/bin/java org.springframework.boot.loader.JarLauncher
```
