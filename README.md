# TAMExample
OSE demo with Tomcat, A-MQ persisting to mysql


### Build on top of existing image using:

```
oc new-app openshift/jboss-amq-62~https://github.com/noelo/TAMExample.git --context-dir=amq
```

### Add relevant hostname, username & password to the DC

```
oc env dc tamexample amqusername=admin
oc env dc tamexample mysqlhostname=172.30.146.172
oc env dc tamexample amqpwd=admin123 --overwrite
```
