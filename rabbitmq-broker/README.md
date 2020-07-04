# Publishing Results using RabbitMQ Message Broker

Taken from sample-apps `/opt/nvidia/deepstream/deepstream-5.0/sources/apps/sample_apps/deepstream-test5`

install rabbitmq client
```bash
git clone -b v0.8.0  --recursive https://github.com/alanxz/rabbitmq-c.git
cd rabbitmq-c && mkdir build && cd build
cmake ..
cmake --build .
cp librabbitmq/librabbitmq.so.4 /opt/nvidia/deepstream/deepstream-5.0/lib/
ldconfig
```

install rabbitmq server
```bash
apt install rabbitmq-server
service rabbitmq-server start
```

setup rabbitmq management
```bash
rabbitmq-plugins enable rabbitmq_management
```

> we'll be using the default exchange amq.topic

create a queue
```bash
rabbitmqadmin -u guest -p guest -V / declare queue name=myqueue durable=false auto_delete=true
```

bind queue
```bash
rabbitmqadmin -u guest -p guest -V / declare binding source=amq.topic destination=myqueue routing_key=topicname
```

check if queue was created
```bash
rabbitmqctl list_queues
```

setup monitoring
```bash
apt install amqp-tools
```

create `cfg_ampq.txt`
```txt
###############################################################################

[message-broker]
password = guest
#optional
hostname = localhost
username = guest
port = 5672
exchange = amq.topic
topic = topicname
```

Add the RabbitMQ Config in `configs/test5_config_file_src_infer.txt`
```txt
[sink1]
enable=1
#Type - 1=FakeSink 2=EglSink 3=File 4=UDPSink 5=nvoverlaysink 6=MsgConvBroker
type=6
msg-conv-config=dstest5_msgconv_sample_config.txt
#(0): PAYLOAD_DEEPSTREAM - Deepstream schema payload
#(1): PAYLOAD_DEEPSTREAM_MINIMAL - Deepstream schema payload minimal
#(256): PAYLOAD_RESERVED - Reserved type
#(257): PAYLOAD_CUSTOM   - Custom schema payload
msg-conv-payload-type=0
msg-broker-proto-lib=/opt/nvidia/deepstream/deepstream-5.0/lib/libnvds_amqp_proto.so
#Provide your msg-broker-conn-str here
msg-broker-conn-str=localhost;5672;guest
topic=topicname
#Optional:
msg-broker-config=../cfg_ampq.txt
```

`test_amqp_recv.sh`
```bash
while read -r line; do
    echo "$line" >> log.txt
done
```

Store the message in a log
```bash
amqp-consume  -q "myqueue" -r "topicname" -e "amq.topic" ./test_amqp_recv.sh
```

Run the app
```bash
./deepstream-test5-app -c configs/test5_config_file_src_infer.txt
```