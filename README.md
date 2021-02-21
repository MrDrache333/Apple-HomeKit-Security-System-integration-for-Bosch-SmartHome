# How to integrate the Bosch SmartHome Security System into HomeKit using the ioBroker Plugins Node-Red, YaHKa and bshb
This repository describes how to integrate the Bosch SmartHome Security System into Apple HomeKit

![homekit](https://github.com/MrDrache333/Apple-HomeKit-Security-System-integration-for-Bosch-SmartHome/blob/main/homekit.jpg?raw=true)
![bshb](https://github.com/holomekc/ioBroker.bshb/raw/master/admin/bshb-logo.jpg)

## Requirements
- Installed [Node-RED Adapter](https://github.com/ioBroker/ioBroker.node-red)
- Installed [YaHKa-Adapter](https://github.com/jensweigele/ioBroker.yahka)
- Installed and configured [bshb-Adapter](https://github.com/holomekc/ioBroker.bshb)

## Create objects in ioBroker
Please create the following datapoints in ioBroker

```
0_userdata.0
  |- Yahka_Devices
     |- SecuritySystem
        |- targetState (Numeric 0-3)
        |- currentState (Numeric 0-4)
```

## Import example into Node-Red
Import the following nodes into Node-Red and change the objects to your needs
```json
[{"id":"5a47e3bd.4bd22c","type":"ioBroker in","z":"a82bdceb.4f65d","name":"Bosch Security System State","topic":"bshb.0.intrusionDetectionSystem.IntrusionDetectionControl.value","payloadType":"value","onlyack":"","func":"all","gap":"","fireOnStart":"false","x":160,"y":1020,"wires":[["a76a6f68.f61a2"]]},{"id":"56b29dc0.6e63dc","type":"ioBroker in","z":"a82bdceb.4f65d","name":"Bosch Security System Alarmstate","topic":"bshb.0.intrusionDetectionSystem.SurveillanceAlarm.value","payloadType":"value","onlyack":"","func":"all","gap":"","fireOnStart":"false","x":180,"y":1080,"wires":[["a76a6f68.f61a2"]]},{"id":"b8fa720e.76e278","type":"ioBroker out","z":"a82bdceb.4f65d","name":"Bosch Security System Currentstate","topic":"0_userdata.0.Yahka_Devices.SecuritySystem.currentState","ack":"true","autoCreate":"false","stateName":"","role":"","payloadType":"","readonly":"","stateUnit":"","stateMin":"","stateMax":"","x":1030,"y":980,"wires":[]},{"id":"dfc422ad.2eec2","type":"ioBroker in","z":"a82bdceb.4f65d","name":"Bosch Security System Targetstate","topic":"0_userdata.0.Yahka_Devices.SecuritySystem.targetState","payloadType":"value","onlyack":"","func":"rbe","gap":"","fireOnStart":"false","x":180,"y":1140,"wires":[["84850a14.181988"]]},{"id":"8863ec95.6479a8","type":"function","z":"a82bdceb.4f65d","name":"","func":"var armed = msg.payload == \"SYSTEM_ARMED\"\nvar alarm = msg.payload == \"ALARM_ON\"\nvar profile = Number(msg.activeProfile)\nif (armed){\n    if (profile === 0){\n        msg.payload = 1\n    }else if (profile === 2){\n        msg.payload = 2\n    }\n}else {\n    msg.payload = 3\n}\nif (alarm){\n    msg.payload = 4\n}\nreturn msg;","outputs":1,"noerr":0,"initialize":"","finalize":"","x":600,"y":1020,"wires":[["768425c0.a25564","70cae884.4fa448"]]},{"id":"190d0f1b.963e39","type":"function","z":"a82bdceb.4f65d","name":"","func":"msg.newState = Number(msg.payload);\nmsg.payload = true\nif (msg.newState != Number(msg.currentState))\n    return msg;","outputs":1,"noerr":0,"initialize":"","finalize":"","x":580,"y":1140,"wires":[["5fb6e50a.1f846c"]]},{"id":"a76a6f68.f61a2","type":"ioBroker get","z":"a82bdceb.4f65d","name":"Active Profile","topic":"bshb.0.intrusionDetectionSystem.IntrusionDetectionControl.activeProfile","attrname":"activeProfile","payloadType":"value","x":430,"y":1020,"wires":[["8863ec95.6479a8","3cd51038.d5dd5"]]},{"id":"2756b4ea.126be4","type":"ioBroker out","z":"a82bdceb.4f65d","name":"Disarm","topic":"bshb.0.intrusionDetectionControl.disarmProtection","ack":"false","autoCreate":"false","stateName":"","role":"","payloadType":"","readonly":"","stateUnit":"","stateMin":"","stateMax":"","x":960,"y":1100,"wires":[]},{"id":"5fb6e50a.1f846c","type":"switch","z":"a82bdceb.4f65d","name":"Switch TargetState","property":"newState","propertyType":"msg","rules":[{"t":"eq","v":"0","vt":"num"},{"t":"eq","v":"1","vt":"num"},{"t":"eq","v":"2","vt":"num"},{"t":"eq","v":"3","vt":"num"}],"checkall":"true","repair":false,"outputs":4,"x":750,"y":1140,"wires":[["2756b4ea.126be4"],["dfb57347.1c5078"],["5325863d.d11b4"],["2756b4ea.126be4"]]},{"id":"dfb57347.1c5078","type":"ioBroker out","z":"a82bdceb.4f65d","name":"FullProtection","topic":"bshb.0.intrusionDetectionControl.fullProtection","ack":"false","autoCreate":"false","stateName":"","role":"","payloadType":"","readonly":"","stateUnit":"","stateMin":"","stateMax":"","x":980,"y":1140,"wires":[]},{"id":"5325863d.d11b4","type":"ioBroker out","z":"a82bdceb.4f65d","name":"Individual Protection","topic":"bshb.0.intrusionDetectionControl.individualProtection","ack":"false","autoCreate":"false","stateName":"","role":"","payloadType":"","readonly":"","stateUnit":"","stateMin":"","stateMax":"","x":1000,"y":1180,"wires":[]},{"id":"84850a14.181988","type":"ioBroker get","z":"a82bdceb.4f65d","name":"Current State","topic":"0_userdata.0.Yahka_Devices.SecuritySystem.currentState","attrname":"currentState","payloadType":"value","x":420,"y":1140,"wires":[["190d0f1b.963e39"]]},{"id":"768425c0.a25564","type":"switch","z":"a82bdceb.4f65d","name":"","property":"payload","propertyType":"msg","rules":[{"t":"neq","v":"4","vt":"num"}],"checkall":"true","repair":false,"outputs":1,"x":790,"y":1020,"wires":[["76e25e85.c71e48"]]},{"id":"70cae884.4fa448","type":"delay","z":"a82bdceb.4f65d","name":"delay","pauseType":"delay","timeout":"50","timeoutUnits":"milliseconds","rate":"1","nbRateUnits":"1","rateUnits":"second","randomFirst":"1","randomLast":"5","randomUnits":"seconds","drop":false,"x":790,"y":980,"wires":[["b8fa720e.76e278"]]},{"id":"76e25e85.c71e48","type":"ioBroker out","z":"a82bdceb.4f65d","name":"Bosch Security System Targetstate","topic":"0_userdata.0.Yahka_Devices.SecuritySystem.targetState","ack":"true","autoCreate":"false","stateName":"","role":"","payloadType":"","readonly":"","stateUnit":"","stateMin":"","stateMax":"","x":1020,"y":1020,"wires":[]}]
```
Now it should look like this:

![Bildschirmfoto 2021-01-31 um 17 41 18](https://user-images.githubusercontent.com/22854641/106391022-8d0f8100-63eb-11eb-8764-c2e968511592.jpg)


## Configure the Security System in YaHKa
Create a new device and add the Security System service with the following settings:

![Bildschirmfoto 2021-01-29 um 16 34 04](https://user-images.githubusercontent.com/22854641/106294893-096d5d00-6250-11eb-879d-da70e25c1a32.jpg)

## (If not already done) Add the YaHKa-Bridge in the Home-App to your HomeKit devices
For this, klick in the YaHKa-Adapter configuration on the ioBroker-HUB (as shown in the image above) and use the configured pin to add the bridge to HomeKit.

The created Security System should then be automatically added to HomeKit.

In the Home-App you should get a new device with an Alarm-Light-Icon like this.
![Bildschirmfoto 2021-01-29 um 16 42 28](https://user-images.githubusercontent.com/22854641/106295636-fc9d3900-6250-11eb-826b-037505caace5.jpg)

You can now control your Bosch SmartHome Security System via HomeKit.
Right now the following actions in HomeKit perform the following actions in the Bosch Security System

* ZUHAUSE/HOME <-> SYSTEM_DISARMED
* ABWESEND/AWAY <-> ARM fullProtection
* NACHT/NIGHT <-> ARM individualProtection
* AUS/OFF <-> SYSTEM_DISARMED

If the System was disarmed by the Bosch SmartHome System the HomeKit App will show the State AUS/OFF. Therefore automations created by the Home-App will work (TRIGGERED, ACTIVATED and DEACTIVATED). 
![Bildschirmfoto 2021-01-29 um 16 50 03](https://user-images.githubusercontent.com/22854641/106296506-0d01e380-6252-11eb-8aa6-94920d0f947b.jpg)

If you also want to play sounds on your HomePods whenever the Security System State changes, take a look at [THIS](https://github.com/MrDrache333/Apple-HomeKit-Alarmanlage-Sounds)
