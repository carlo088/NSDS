To launch Zookeeper and Kafka one has two options:

1.
```
sudo systemctl enable zookeeper
sudo systemctl start zookeeper
sudo systemctl status zookeeper
```
```
sudo systemctl enable kafka
sudo systemctl start kafka
sudo systemctl status kafka
```
2. [On my local macOS] Navigate to /docker and
```
docker-compose up
```
(port 9092 may be occupied by Java, in this case ```kill $PID$```)
Then open VScode, that has the Java extensions to run the project.

To compile Java from Terminal: move to root folder and build the maven project with
```
mvn clean install
```
To compile java
```
mvn exec:java -Dexec.mainClass="it.polimi.middleware.kafka.basic.BasicProducer"
mvn exec:java -Dexec.mainClass="it.polimi.middleware.kafka.basic.BasicConsumer"
```

### BasicConsumer
Keep in mind the following:
```
public class ConsoleConsumer {
    # some hyperparameters
    private static final String defaultGroupId = "groupA";
    private static final String defaultTopic = "topicA";
    private static final String serverAddr = "localhost:9092";

    public static void main(String[] args) {
        # ready to define properties
        final Properties props = new Properties();
        props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, serverAddr);
        props.put(ConsumerConfig.GROUP_ID_CONFIG, groupId);
        # ...

        # define the Consumer
        KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);

        # define what the Consumer should do:
        while (true) {
            # the POLL method pulls the messages that are being sent
            final ConsumerRecords<String, String> records = consumer.poll(Duration.of(5, ChronoUnit.MINUTES));
            # now iterate through for every record within records
            for (final ConsumerRecord<String, String> record : records) {
                # in this case, just print the message to the terminal
                System.out.print("Consumer group: " + groupId + "\t");
                System.out.println("Partition: " + record.partition() +
                        "\tOffset: " + record.offset() +
                        "\tKey: " + record.key() +
                        "\tValue: " + record.value()
                );
            }
        }
    }
}
```
### BasicProducer
Keep in mind the following:
```
public class BasicProducer {
    ... 
    public static void main(String[] args) {
        ...
        final KafkaProducer<String, String> producer = new KafkaProducer<>(props);
        final Random r = new Random();
        
        # generate message to send (in the key-value format)
        for (int i = 0; i < numMessages; i++) {
            final String topic = topics.get(r.nextInt(topics.size()));
            final String key = "Key" + r.nextInt(1000);
            final String value = "Val" + i;
            System.out.println(
                    "Topic: " + topic +
                    "\tKey: " + key +
                    "\tValue: " + value
            );
            
            # produce the record (or message)
            final ProducerRecord<String, String> record = new ProducerRecord<>(topic, key, value);

            # send the record
            final Future<RecordMetadata> future = producer.send(record);

        producer.close();
    }
}
```

### TopicManager
Class provides a simple command-line interface to manage Kafka topics. It can list existing topics, delete a specified topic if it exists, and create a new topic with the specified parameters. 


## Exercise 1:
Consumer C1 prints records on the standard output:
```
...
while (true) {
    final ConsumerRecords<String, String> records = consumer.poll(Duration.of(5, ChronoUnit.MINUTES));
    for (final ConsumerRecord<String, String> record : records) {
        System.out.print("Consumer group: " + groupId + "\t");
        System.out.println("Partition: " + record.partition() +
                 "\tOffset: " + record.offset() +
                "\tKey: " + record.key() +
                "\tValue: " + record.value()
...
```
Consumer C2 processes each value (e.g., removes upper case letters) and stores the result to a new topic, without delivery guarantees
```
...
while (true) {
    final ConsumerRecords<String, String> records = consumer.poll(Duration.of(5, ChronoUnit.MINUTES));
    for (final ConsumerRecord<String, String> record : records) {
        final String key = record.key();
        final String newValue = record.key().toLowerCase();
        producer.send(new ProducerRecord<>(outputTopic, key, newValue));
            }
...
```

## Exercise 2
Now assume that you realize that consumer C2 is too slow, tt cannot sustain the rate of messages added to the input topic. How can you improve the performance of the system?

## Exercise 3
Now you want C2 to guarantee **exactly-once semantics**. Each input message should be delivered to the output topic once and only once.

Exactly-once semantics in Kafka is a combination of idempotence on the producer side and proper configuration on the consumer side.

Enabling idempotence in the Kafka producer ensures that each message is sent to the Kafka broker exactly once. Idempotence means that even if a producer sends a message multiple times due to failures or retries, the broker will ensure that only one copy of the message is stored.

"Exactly-once semantics" refers to the guarantee that a message will be either processed and delivered exactly once to the consumer or not processed at all.

props.put(ProducerConfig.ENABLE_IDEMPOTENCE_CONFIG, String.valueOf(true));
