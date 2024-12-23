# Meshtastic-MQTT-Discovery
Home Automation Script and Automation to setup a Meshtastic Node as a Real Device in Home Assistant

<h3>An Integration already exist</h2>
Before you continue I recently found out there is already an integration available under the name "Meshtastic MQTT Client" in HACS (or nr 761792106) zie also <a href="https://github.com/kvj/hass_Mtastic_MQTT">github</a>.
The benefit of this integration is that it does not require JSON. It is likely you wish to follow this integration.

<h3>My starting Point</h2>
When I started my objective was to see a Meshtastic Node as 1 device. After a lot of wrong intents and 'kommaneuken' I decided to abandon YAML (but I still prefer packages to keep everything of a project in 1 file) and do this directly in Home Assistant where mistake are noticed directly. This is the endresult:

<img src="Meshtastic.png" width="700" />
 
<h2>Requirement(s)</h2>

There is only 1 requirement and that is that you have your <b>own</b> MQTT-broker, a semi-requirement is that inside your own broker you setup a <b>MQTT Bridge</b> to a Meshtastic MQTT broker. How to do this is beyond the scope of this documentation and is much better explained elsewhere like <a href="https://www.meshnet.nl/setup-mqtt-bridge.html">here</a> however in Dutch. <b>WARNING: publish inside your own broker and not in other mans broker !!!!!</b>. When your broker part is setup correctly you may create a new automation and copy the content of file "AUTOMATION - Meshtastic Node - retain topics.yaml" (if you prefer other textual methods you might need to change the intent etc). Before you activate you need to update the <b>TOPiC</b> lines:

topic: msh/pa8f/<zip_code_range>/2/json/LongFast/<your_mqtt_node>

With your details. This automation will publish the incoming JSON topic into seperate and retained topics. The topics are extended with the FROM and TYPE fields, if the TYPE=telemetry this is even more differentiated by type of metric: device, environment and power. The result (2-retained) looks in MQTT-explorer like this: 

<img src="MQTT-explorer.png" width="600" />

Note: If your Mesh is big you might want to limit this by selecting only the nodes of your scope.

<h2>MQTT Discovery</h2
                    
To discover each Meshtastic Node. You need to run the script "SCRIPT - Meshtastic Node mac4 MQTT Discovery.yaml". Before you run this script you need to change: 
    <ul>
      <li>mac4 in an unique identifier (I use the mac4 extention of the device)</li>
      <li>nodid into the value of FROM field of the new device</li>
      <li>set each "discover_" variable to true/false if it needs to be included or not</li>
      <li>update the details of the device, "identifiers" and "name" are already updated in previous steps, you may remove the rest but watch out for the last missing komma</li>
    </ul>
Some changes needs to repeated so a "replace all" is better. After the updates you only need to run this script once. this will create the dicovery settings under the "homeassistant" (see MQTT-discovery entity" in previous picture).If this device or one of its sensors become obsolete you can delete this directly in MQTT-explorer.

NOTE: Not every possible sensor is already included but hopefully this setup is easy enough to extend this.

<h2>Template Sensor</h2

You can create also an additional template sensor in Settings - Device - Helper, this sensor will calulate the distance of a node to Home with this template:
{{ distance('device_tracker.meshtastic_node_mac4_tracker') | round(2) }}
See for further required settings the picture below:

<img src="template_sensor.png" width="300" />

As you can see you also be able to link this sensor with the device itself.
This action is only relevant if the node has a position and in the script "discover_GPS: true" when the script was run for this node.

<h2>Change Log</h2>

<ul>
<li>20241201 - Publish first version</li>
<li>20241203 - The Device_Tracker is now included in the Device, seems order is pretty important to be successful</li>
<li>20241208 - Discovery of an existing integration</li>  
</ul>

<h2>Inspired by</h2>

Found mostly my inspiration in the Home Assistant Forum to new a few (just a few):
<ul>
  <li>https://meshtastic.org/docs/software/integrations/mqtt/home-assistant/</li>
  <li>https://community.home-assistant.io/t/creating-a-device-with-multiple-sensor-entities-via-mqtt-discovery/584372/5</li>
  <li>https://community.home-assistant.io/t/mqtt-sensor-how-to-load-attributes/212523/6</li>
  <li>https://community.home-assistant.io/t/split-mqtt-payload-and-send-values-to-entities/257445/4</li>
  <li>https://community.home-assistant.io/t/convert-integer-to-hours-and-minutes/333945/2</li>
  <li>https://community.home-assistant.io/t/passing-trigger-payload-json-to-script/278951/6</li>
  <li>https://community.home-assistant.io/t/2023-7-responding-services/588733/258</li>
  <li>https://community.home-assistant.io/t/example-using-response-from-a-script/599180/13</li>
  <li>https://community.home-assistant.io/t/convert-received-hex-codes-to-usable-decimal-values/465514</li>
  <li>https://community.home-assistant.io/t/changing-a-decimal-value-into-a-hex-value-jinja/11925/4</li>
  <li>https://community.home-assistant.io/t/extract-substring-from-string/94592/2</li>
  <li>https://www.home-assistant.io/integrations/input_text/</li>
  <li>https://www.home-assistant.io/integrations/device_tracker.mqtt/</li>
  <li>https://github.com/ESPresense/ESPresense/discussions/144</li>
  <li>https://github.com/iantrich/config-template-card/issues/10</li>
</ul>
