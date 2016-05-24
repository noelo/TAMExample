# TAMExample
OSE demo with Tomcat, A-MQ persisting to mysql

## MYSQL instance
Set up using the exisiting OSE templates

## A-MQ Broker
Build on top of existing image using the command below.
The custom build adds the driver & connection pooling jars as well as configuring the datasource in the config file

```
oc new-app openshift/jboss-amq-62~https://github.com/noelo/TAMExample.git --context-dir=amq
```

### Add relevant hostname, username & password to the DC
```
oc env dc tamexample amqusername=admin
oc env dc tamexample mysqlhostname=mysql.tamexample.svc.cluster.local
oc env dc tamexample amqpwd=admin123  
```

### Enable SSL

#### Create keystores
```
keytool -genkey -alias broker -keyalg RSA -keystore keystore.jks
keytool -export -alias broker -keystore keystore.jks -file broker_cert
keytool -genkey -alias client -keyalg RSA -keystore client.jks
keytool -import -alias broker -keystore client.jks -file broker_cert
cp client.jks truststore.jks
```

#### Create secrets and add mounts and environment variables
```
oc secret new amqsecret keystore.jks truststore.jks
oc volumes dc/tamexample --add -t secret -m /etc/amq-secret-volume --secret-name amqsecret
oc env dc tamexample AMQ_TRUSTSTORE=truststore.jks
oc env dc tamexample AMQ_KEYSTORE=keystore.jks
oc env dc tamexample AMQ_KEYSTORE_PASSWORD=password
oc env dc tamexample AMQ_TRUSTSTORE_PASSWORD=password
oc env dc tamexample AMQ_KEYSTORE_TRUSTSTORE_DIR=/etc/amq-secret-volume
```
