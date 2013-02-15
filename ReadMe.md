# Example of Camel-SSH using Keys

This examples shows how to use the Camel-SSH component to call Apache Karaf
using Public Key versus password.

## Requirements

* [Apache Maven](http://maven.apache.org) version 3.x
* [Apache Karaf](http://karaf.apache.org) version 2.3.0

## Setup Karaf

We will need to make 2 changes to Karaf for this example to work.

1. Register the Bouncy Castle Java Cryptography Extension (JCE) with Karaf.
2. Add a user and public key to Karaf's identity store.

### Register Bouncy Castle

We need the Bouncy Castle JCE as Karaf's included Mina SSHD server uses this to handle Public Key files.

Install the following jars into the `<karaf_home>/lib/ext` directory.

    curl -O http://downloads.bouncycastle.org/java/bcprov-jdk15on-147.jar
    curl -O http://downloads.bouncycastle.org/java/bcprov-ext-jdk15on-147.jar
    curl -O http://downloads.bouncycastle.org/java/bcpkix-jdk15on-147.jar

Edit the `<karaf_home>/etc/config.properties` file, making 2 changes

1. add the following line

    org.apache.karaf.security.providers = org.bouncycastle.jce.provider.BouncyCastleProvider

2. update the `org.osgi.framework.bootdelegation` property, appending `,org.bouncycastle.*` to the end. It should look
something like the following when done

    org.osgi.framework.bootdelegation=org.apache.karaf.jaas.boot,sun.*,com.sun.*,javax.transaction,javax.transaction.*,org.bouncycastle.*

### Add a user and public key

Edit the `<karaf_home>/etc/keys.properties` file, appending a new user `scott`. The format of the keys.properties
file is as follows

    <user name>=<public key>,<role>

For this example you will need to add a new user `scott` with the key value from the included
`src/main/resources/test_rsa.pub` file. The included Camel route uses the associated private key
`src/main/resources/test_rsa` when connecting to the updated Karaf server.

Append the following to the `<karaf_home>/etc/keys.properties` file

    scott=AAAAB3NzaC1yc2EAAAADAQABAAABAQC4MSqXneGVM9n3Rnqfya0D31crTx+J8X8dKZ5/+2Rq96/klnmt6uGyl+DI8G0HXVK66KqU2Z0qUE9ytOMR+zSPA+zU4Bhd3G82qdJsys7Nz8t9wSWQs8/ItWTjwg+b3vVLcI1Q8sSgfxqZvD/XQk4srbDs9ba55yjEPn5DOhA7UFbFAv8menSgbpNFMrUQQ9DJcMVlu3MOcMylI5UFFf1uvDD3vUoFXZa6GQOK9j1AGvfagGgBEqV4/AiKzDmQDFqMzmTfTfBpi1crY0SGXdT26PE4Knx7KP7bBJupBm6lJ9Pk39iw8dnDWcq45EEqrHsI0dps7/iQgYxYqBmFLIkP,admin

### Start Karaf

Karaf should be all configured at this point so start up an instance by running the `bin/karaf` from
the `<karaf_home>` directory.

## Building this example

To build, run the following command

    mvn clean install

To test locally, assuming you have Setup Karaf and have it running, run the following command

    mvn camel:run

## Deploy to Karaf

From the running Karaf console, run the following commands to install this example into your
running Karaf server.

    features:addurl mvn:org.example/example-security-camel-ssh/1.0-SNAPSHOT/xml/features
    features:install example-security-camel-ssh

This example outputs into the Karaf log file so you can either `tail -f <karaf_home>data/log/karaf.log`
or from the Karaf console run the following command

    log:tail


