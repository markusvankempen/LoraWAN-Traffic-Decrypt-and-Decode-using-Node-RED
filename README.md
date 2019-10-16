# LoraWAN-Traffic-Decrypt-and-Decode using Node-RED
This shows the Node-RED flows which can decrypt (lorae node/sensor) payload via the APP &amp; Network key.
The flow used the node-red package 
https://github.com/scne/node-red-contrib-lora-packet-converter/tree/master/lora-packet-converter
the base of the code is this node.js package 
https://github.com/anthonykirby/lora-packet
#
## Motivation
I want to send the Lora Message directly via mqtt to node-red and process
them there with out the need of a LoraWAN server ot go the cloud like TheThingNetwor

### Other references for Test 
I used a LoPy form PYCOM as a single channel gateway aka nanogateway
https://pycom.io/product/lopy4/
and a PI LoRa bonnett
https://www.adafruit.com/product/4074
https://learn.adafruit.com/using-lorawan-and-the-things-network-with-circuitpython?view=all

here a good links for testing the decryption/
https://lorawan-packet-decoder-0ta6puiniaut.runkit.sh
https://codebeautify.org/string-hex-converter
here more infos
https://forum.pycom.io/topic/3958/how-decrypt-lora-data/3


### Here the Node-RED test flow with example message

```
[{"id":"38ac183c.4b6398","type":"lora-packet-converter","z":"2dec3743.9f7fd8","name":"Network 2 App Key","nsw":"27E87B6710ACC5FB9098F19AE77DC62B","asw":"B887FD00569BE0746E8020746EFC9BA8","x":170,"y":240,"wires":[["bd74e702.470948","741088ec.e35b68"]]},{"id":"bd74e702.470948","type":"debug","z":"2dec3743.9f7fd8","name":"Decrypted","active":false,"tosidebar":true,"console":false,"tostatus":false,"complete":"true","targetType":"full","x":230,"y":300,"wires":[]},{"id":"3adca77e.cd5858","type":"function","z":"2dec3743.9f7fd8","name":"Add DeviceID","func":"devid=\"12345678901 \"\n\n// the lora lora-packet-converter package expect device id (12) as a string before the Lora wan Packet\nmvk=msg.payload.data // from the injection \nnode.warn(mvk)\n//test={\"rxpk\":[{\"data\":\"QIoaASYAYAABTqf1tMolR+Q=\",\"time\":\"2019-10-16T02:43:52.558399Z\",\"chan\":0,\"tmst\":110465012,\"stat\":1,\"modu\":\"LORA\",\"lsnr\":1,\"rssi\":-113,\"rfch\":0,\"codr\":\"4/5\",\"freq\":903.9,\"datr\":\"SF7BW125\",\"size\":17}]}\n//msg={}\nmsg.payload=new Buffer(\"12345678901 \"+JSON.stringify(mvk))\n\nreturn msg;","outputs":1,"noerr":0,"x":140,"y":160,"wires":[["38ac183c.4b6398","2dce78af.a8a6c8"]]},{"id":"38e79b7f.664a14","type":"inject","z":"2dec3743.9f7fd8","name":"LoraWAN message form pi  with CPU load","topic":"test message","payload":"{\"value\":\"hello\",\"type\":\"dev\",\"msgid\":0,\"data\":{\"rxpk\":[{\"data\":\"QIoaASYAYAABTqf1tMolR+Q=\",\"time\":\"2019-10-16T02:43:52.558399Z\",\"chan\":0,\"tmst\":110465012,\"stat\":1,\"modu\":\"LORA\",\"lsnr\":1,\"rssi\":-113,\"rfch\":0,\"codr\":\"4/5\",\"freq\":903.9,\"datr\":\"SF7BW125\",\"size\":17}]}}","payloadType":"json","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":220,"y":100,"wires":[["3adca77e.cd5858"]]},{"id":"741088ec.e35b68","type":"function","z":"2dec3743.9f7fd8","name":"Sensor data to hex/decode","func":"\nbytes=msg.payload.rxpk[0].data // sensor/LoraWAN data playload\n\nnewbytes=Buffer.from(bytes, 'utf-8')\nnode.warn(newbytes) ///yeah\n\n//decode ...payload\n//https://learn.adafruit.com/using-lorawan-and-the-things-network-with-circuitpython?view=all\n\nCPU_Load = (newbytes[0] << 8) | newbytes[1];\noTemperature = (newbytes[2] << 8) | newbytes[1];\n   \nmsg.payload.CPU_Load = CPU_Load / 100;\nmsg.payload.oTemperature = oTemperature / 100;\n  \nreturn msg;\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n","outputs":1,"noerr":0,"x":560,"y":240,"wires":[["b99d50ef.24e7a"]]},{"id":"e286d41.a330828","type":"comment","z":"2dec3743.9f7fd8","name":"Example of Decryptin and decoding LoraWAN message","info":"Note:\nI use the APP & network key form TheThinkNetwork\n\nmy Hardware was a pycom LoPY as Single Channel Gateway\nand a Lora PI Bonnet using python\nhttps://learn.adafruit.com/using-lorawan-and-the-things-network-with-circuitpython?view=all\n\nI want to send the Lora Message directly via mqtt to node-red and process\nthem there with out the need of a loraWAN server\n\n\nhere a good link for testing\nhttps://lorawan-packet-decoder-0ta6puiniaut.runkit.sh\nhere more infos\nhttps://github.com/anthonykirby/lora-packet/blob/master/demo/demo1.js\nmore infos\nhttps://forum.pycom.io/topic/3958/how-decrypt-lora-data/3\nhttps://codebeautify.org/string-hex-converter","x":240,"y":40,"wires":[]},{"id":"b99d50ef.24e7a","type":"debug","z":"2dec3743.9f7fd8","name":"Sensor Data Decoded","active":true,"tosidebar":true,"console":false,"tostatus":false,"complete":"true","targetType":"full","x":640,"y":300,"wires":[]},{"id":"2dce78af.a8a6c8","type":"debug","z":"2dec3743.9f7fd8","name":"InComing LoraWAN","active":false,"tosidebar":true,"console":false,"tostatus":false,"complete":"true","targetType":"full","x":400,"y":160,"wires":[]},{"id":"ca8715d9.5b65b8","type":"comment","z":"2dec3743.9f7fd8","name":"Example payload with CPU and temp","info":"{\n    \"value\": \"hello\",\n    \"type\": \"dev\",\n    \"msgid\": 0,\n    \"data\": {\n        \"rxpk\": [\n            {\n                \"data\": \"QIoaASYAYAABTqf1tMolR+Q=\",\n                \"time\": \"2019-10-16T02:43:52.558399Z\",\n                \"chan\": 0,\n                \"tmst\": 110465012,\n                \"stat\": 1,\n                \"modu\": \"LORA\",\n                \"lsnr\": 1,\n                \"rssi\": -113,\n                \"rfch\": 0,\n                \"codr\": \"4/5\",\n                \"freq\": 903.9,\n                \"datr\": \"SF7BW125\",\n                \"size\": 17\n            }\n        ]\n    }\n}","x":890,"y":60,"wires":[]},{"id":"35ce2337.e09f5c","type":"comment","z":"2dec3743.9f7fd8","name":"Network Key","info":"27E87B6710ACC5FB9098F19AE77DC62B","x":810,"y":120,"wires":[]},{"id":"8c8821b7.f0437","type":"comment","z":"2dec3743.9f7fd8","name":"App Key","info":"B887FD00569BE0746E8020746EFC9BA8","x":800,"y":160,"wires":[]}]
```
## Pitfall/Challange
The current plugin (lora-packet-converter) for Node-RED does not return the decryted payload as a hex string ... just bytes as a string which is a bit confusing. 

The flowing code snippet address this issue - took a while tho to figure it out ;)
```
bytes=msg.payload.rxpk[0].data // sensor/LoraWAN data playload
newbytes=Buffer.from(bytes, 'utf-8')
node.warn(newbytes) 
```
## Authors

* **Markus van Kempen ** - *Initial work* 

