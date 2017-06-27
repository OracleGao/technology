# Using soft linking file or directory in webapps when deploy the web app in tomcat

You need to modify the ${TOMCAT_HOME}/conf/context.xml

- when the version is less equal than 7.x (7.x-)
```xml
<context allowLinking="true">
...
</context>
```
- when the version is great equal than 8.x (8.x+)
```xml
<context>
...
<Resources allowLinking="true" />
...
</context>
```
