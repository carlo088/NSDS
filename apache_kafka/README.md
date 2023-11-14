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
2. navigate to /docker and
```
docker compose up
```

Then, move to root folder and build the maven project with
```
mvn clean install
```
To compile java
```
mvn exec:java -Dexec.mainClass="it.polimi.middleware.kafka.basic.BasicProducer"
mvn exec:java -Dexec.mainClass="it.polimi.middleware.kafka.basic.BasicConsumer"
```
