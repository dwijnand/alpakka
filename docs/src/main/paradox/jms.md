# JMS Connector

The JMS connector provides Akka Stream sources and sinks to connect to JMS providers.

## Artifacts

sbt
:   @@@vars
    ```scala
    libraryDependencies += "com.lightbend.akka" %% "akka-stream-alpakka-jms" % "$version$"
    libraryDependencies += "javax.jms" % "jms" % "1.1"
    ```
    @@@

Maven
:   @@@vars
    ```xml
    <dependency>
      <groupId>com.lightbend.akka</groupId>
      <artifactId>akka-stream-alpakka-jms_$scalaBinaryVersion$</artifactId>
      <version>$version$</version>
    </dependency>
    <dependency>
      <groupId>javax.jms</groupId>
      <artifactId>jms</artifactId>
      <version>1.1</version>
    </dependency>
    ```
    @@@

Gradle
:   @@@vars
    ```gradle
    dependencies {
      compile group: "com.lightbend.akka", name: "akka-stream-alpakka-jms_$scalaBinaryVersion$", version: "$version$"
      compile group: 'javax.jms', name: 'jms', version: '1.1'
    }
    ```
    @@@

## Usage

The JMS message model supports several types of message body (see @extref(javaee-api:javax.jms.Message)) and Alpakka currently supports messages with a body containing a @extref[String](java-api:java.lang.String) object.

Use the case class @scaladoc[JmsTextMessage](akka.stream.alpakka.jms.JmsTextMessage$) to wrap the messages you want to send and optionally set their properties (see below for an example).

### Sending messages to a JMS provider

First define a jms `ConnectionFactory` depending on the implementation you're using. Here we're using Active MQ.

Scala
: @@snip (../../../../jms/src/test/scala/akka/stream/alpakka/jms/scaladsl/JmsConnectorsSpec.scala) { #connection-factory }

Java
: @@snip (../../../../jms/src/test/java/akka/stream/alpakka/jms/javadsl/JmsConnectorsTest.java) { #connection-factory }


Create a sink, that accepts and forwards @extref[String](java-api:java.lang.String)s to the JMS provider.

Scala
: @@snip (../../../../jms/src/test/scala/akka/stream/alpakka/jms/scaladsl/JmsConnectorsSpec.scala) { #create-text-sink }

Java
: @@snip (../../../../jms/src/test/java/akka/stream/alpakka/jms/javadsl/JmsConnectorsTest.java) { #create-text-sink }

@scaladoc[JmsSink](akka.stream.alpakka.jms.JmsSink$) contains factory methods to facilitate the creation of sinks.

Last step is to @extref[materialize](akka-docs:scala/stream/stream-flows-and-basics) and run the sink(s) we have created.

Scala
: @@snip (../../../../jms/src/test/scala/akka/stream/alpakka/jms/scaladsl/JmsConnectorsSpec.scala) { #run-text-sink }

Java
: @@snip (../../../../jms/src/test/java/akka/stream/alpakka/jms/javadsl/JmsConnectorsTest.java) { #run-text-sink }

### Sending messages with properties to a JMS provider

Create a sink, that accepts and forwards @scaladoc[JmsTextMessage](akka.stream.alpakka.jms.JmsTextMessage$)s to the JMS provider:

Scala
: @@snip (../../../../jms/src/test/scala/akka/stream/alpakka/jms/scaladsl/JmsConnectorsSpec.scala) { #create-messages-with-properties }

Java
: @@snip (../../../../jms/src/test/java/akka/stream/alpakka/jms/javadsl/JmsConnectorsTest.java) { #create-test-message-list #create-messages-with-properties }

### Receiving @extref[String](java-api:java.lang.String) messages from a JMS provider

Create a source:

Scala
: @@snip (../../../../jms/src/test/scala/akka/stream/alpakka/jms/scaladsl/JmsConnectorsSpec.scala) { #create-text-source }

Java
: @@snip (../../../../jms/src/test/java/akka/stream/alpakka/jms/javadsl/JmsConnectorsTest.java) { #create-text-source }

The `bufferSize` parameter controls the maximum number of messages to prefetch before applying backpressure.

Run the source and take the same amount of messages as we previously sent to it.

Scala
: @@snip (../../../../jms/src/test/scala/akka/stream/alpakka/jms/scaladsl/JmsConnectorsSpec.scala) { #run-text-source }

Java
: @@snip (../../../../jms/src/test/java/akka/stream/alpakka/jms/javadsl/JmsConnectorsTest.java) { #run-text-source }

### Receiving @extref[javax.jms.Message](javaee-api:javax.jms.Message)s from a JMS provider

Create a @extref[javax.jms.Message](javaee-api:javax.jms.Message) source:

Scala
: @@snip (../../../../jms/src/test/scala/akka/stream/alpakka/jms/scaladsl/JmsConnectorsSpec.scala) { #create-jms-source }

Java
: @@snip (../../../../jms/src/test/java/akka/stream/alpakka/jms/javadsl/JmsConnectorsTest.java) { #create-jms-source }

The `bufferSize` parameter controls the maximum number of messages to prefetch before applying backpressure.

Run the source and specify the amount of messages to take:

Scala
: @@snip (../../../../jms/src/test/scala/akka/stream/alpakka/jms/scaladsl/JmsConnectorsSpec.scala) { #run-jms-source }

Java
: @@snip (../../../../jms/src/test/java/akka/stream/alpakka/jms/javadsl/JmsConnectorsTest.java) { #run-jms-source }

### Receiving @extref[javax.jms.Message](javaee-api:javax.jms.Message)s from a JMS provider with a selector

Create a @extref[javax.jms.Message](javaee-api:javax.jms.Message) source specifying a [JMS selector expression](https://docs.oracle.com/cd/E19798-01/821-1841/bncer/index.html):

Scala
: @@snip (../../../../jms/src/test/scala/akka/stream/alpakka/jms/scaladsl/JmsConnectorsSpec.scala) {             #create-jms-source-with-selector }

Java
: @@snip (../../../../jms/src/test/java/akka/stream/alpakka/jms/javadsl/JmsConnectorsTest.java) { #create-jms-source-with-selector }

Verify that we are only receiving messages according to the selector:

Scala
: @@snip (../../../../jms/src/test/scala/akka/stream/alpakka/jms/scaladsl/JmsConnectorsSpec.scala) { #assert-only-odd-messages-received }

Java
: @@snip (../../../../jms/src/test/java/akka/stream/alpakka/jms/javadsl/JmsConnectorsTest.java) { #assert-only-odd-messages-received }

### Using Topic with an JMS provider

You can use JMS topic in a very similar way.

For the Sink :

Scala
: @@snip (../../../../jms/src/test/scala/akka/stream/alpakka/jms/scaladsl/JmsConnectorsSpec.scala) { #create-topic-sink }

Java
: @@snip (../../../../jms/src/test/java/akka/stream/alpakka/jms/javadsl/JmsConnectorsTest.java) { #create-topic-sink }

For the source :

Scala
: @@snip (../../../../jms/src/test/scala/akka/stream/alpakka/jms/scaladsl/JmsConnectorsSpec.scala) { #create-topic-source }

Java
: @@snip (../../../../jms/src/test/java/akka/stream/alpakka/jms/javadsl/JmsConnectorsTest.java) { #create-topic-source }

Such sink and source can be started the same way as in the previous example.

### Running the example code

The code in this guide is part of runnable tests of this project. You are welcome to edit the code and run it in sbt.

Scala
:   ```
    sbt
    > jms/testOnly *.JmsConnectorsSpec
    ```

Java
:   ```
    sbt
    > jms/testOnly *.JmsConnectorsTest
    ```

### Using IBM MQ

You can use IBM MQ like any other JMS Provider by creating a `QueueConnectionFactory` or a `TopicConnectionFactory`
and creating a `JmsSourceSettings` or `JmsSinkSettings` from it.
The below snippets have been tested with a default IBM MQ docker image which contains queues and topics for testing.
The following command starts MQ 9 using docker:

    docker run --env LICENSE=accept --env MQ_QMGR_NAME=QM1 --publish 1414:1414 --publish 9443:9443 ibmcom/mq:9

MQ settings for this image are shown here: https://github.com/ibm-messaging/mq-docker#mq-developer-defaults

#### Create a JmsSource to an IBM MQ Queue

The `MQQueueConnectionFactory` needs a queue manager name and a channel name, the docker command used in the previous section sets up a `QM1` queue manager and a `DEV.APP.SVRCONN` channel. The IBM MQ client makes it possible to 
connect to the MQ server over TCP/IP or natively through JNI (when the client and server run on the same machine). In the examples below we have chosen to use TCP/IP, which is done by setting the transport type to `CommonConstants.WMQ_CM_CLIENT`.

Scala
:   ```
    import com.ibm.mq.jms.MQQueueConnectionFactory
    import com.ibm.msg.client.wmq.common.CommonConstants
    val QueueManagerName = "QM1"
    val TestChannelName = "DEV.APP.SVRCONN"
    // Create the IBM MQ QueueConnectionFactory
    val queueConnectionFactory = new MQQueueConnectionFactory()
    queueConnectionFactory.setQueueManager(QueueManagerName)
    queueConnectionFactory.setChannel(TestChannelName)
    // Connect to IBM MQ over TCP/IP
    queueConnectionFactory.setTransportType(CommonConstants.WMQ_CM_CLIENT)
    val TestQueueName = "DEV.QUEUE.1"
    val jmsSource: Source[String, NotUsed] = JmsSource.textSource(
      JmsSourceSettings(queueConnectionFactory).withQueue(TestQueueName)
    )
    ```

Java
:   ```
    import com.ibm.mq.jms.MQQueueConnectionFactory;
    import com.ibm.msg.client.wmq.common.CommonConstants;
    String queueManagerName = "QM1";
    String testChannelName = "DEV.APP.SVRCONN";
    // Create the IBM MQ QueueConnectionFactory
    MQQueueConnectionFactory queueConnectionFactory = new MQQueueConnectionFactory();
    queueConnectionFactory.setQueueManager(queueManagerName);
    queueConnectionFactory.setChannel(testChannelName);
    // Connect to IBM MQ over TCP/IP
    queueConnectionFactory.setTransportType(CommonConstants.WMQ_CM_CLIENT);
    String testQueueName = "DEV.QUEUE.1";
    Source<String, NotUsed> jmsSource = JmsSource.textSource(
      JmsSourceSettings
        .create(queueConnectionFactory)
        .withQueue(testQueueName)
    );
    ```

#### Create a JmsSink to an IBM MQ Topic
The IBM MQ docker container sets up a `dev/` topic, which is used in the example below.

Scala
:   ```
    import com.ibm.mq.jms.MQTopicConnectionFactory
    import com.ibm.msg.client.wmq.common.CommonConstants
    val QueueManagerName = "QM1"
    val TestChannelName = "DEV.APP.SVRCONN"
    // Create the IBM MQ TopicConnectionFactory
    val topicConnectionFactory = new MQTopicConnectionFactory()
    topicConnectionFactory.setQueueManager(QueueManagerName)
    topicConnectionFactory.setChannel(TestChannelName)
    // Connect to IBM MQ over TCP/IP
    topicConnectionFactory.setTransportType(CommonConstants.WMQ_CM_CLIENT)
    val TestTopicName = "dev/"
    val jmsTopicSink: Sink[String, NotUsed] = JmsSink(
      JmsSinkSettings(topicConnectionFactory).withTopic(TestTopicName)
    )
    ```

Java
:   ```
    import com.ibm.mq.jms.MQTopicConnectionFactory;
    import com.ibm.msg.client.wmq.common.CommonConstants;
    String queueManagerName = "QM1";
    String testChannelName = "DEV.APP.SVRCONN";
    // Create the IBM MQ TopicConnectionFactory
    val topicConnectionFactory = new MQTopicConnectionFactory();
    topicConnectionFactory.setQueueManager(queueManagerName);
    topicConnectionFactory.setChannel(testChannelName);
    // Connect to IBM MQ over TCP/IP
    topicConnectionFactory.setTransportType(CommonConstants.WMQ_CM_CLIENT);
    String testTopicName = "dev/";
    Sink<String, NotUsed> jmsTopicSink = JmsSink.textSink(
      JmsSinkSettings
        .create(topicConnectionFactory)
        .withTopic(testTopicName)
    );
    ```
