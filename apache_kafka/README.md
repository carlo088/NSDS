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
(port 9092 may be occupied by Java, in this case ```kill $PID$```)

Then, move to root folder and build the maven project with
```
mvn clean install
```
To compile java
```
mvn exec:java -Dexec.mainClass="it.polimi.middleware.kafka.basic.BasicProducer"
mvn exec:java -Dexec.mainClass="it.polimi.middleware.kafka.basic.BasicConsumer"
```
