# Using soft linking file in webapps

you need to modify the ${TOMCAT_HOME}/conf/context.xml

- when the version is less equal than 7.x (7.x-)
```xml
<context allowLinking="true">
...
</context>
```
```xml
- when the version is great equal than 8.x (8.x+)
<context>
...
<Resources allowLinking="true" />
...
</context>
