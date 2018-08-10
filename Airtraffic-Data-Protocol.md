# Air Traffic Data Protocol
- [Air Traffic Data Protocol](#air-traffic-data-protocol)
	- [Objective](#objective)
	- [Scope](#scope)
	- [Data Sources](#data-sources)
	- [Stakeholders](#stakeholders)
	- [Sample Traffic Object](#sample-traffic-object)
	- [Sample Source status Object](#sample-source-status-object)
	- [Data Specifications](#data-specifications)
		- [Traffic Object Details](#traffic-object-details)
		- [Status Object Details](#status-object-details)
	- [Appendix](#appendix)
		- [Data Verbosity](#data-verbosity)
		- [Push vs Pull](#push-vs-pull)
		- [Full Object vs Deltas](#full-object-vs-deltas)
	- [Open Issues](#open-issues)

## Objective 
As UTM moves into it's next phase of evolution, the challenges around integrating unmanned aviation with manned and other entities sharing the sky becomes critical. Air traffic data is sensed using different sensors and have different capabilities, technologies and data formats that need to be reconciled. Therefore the core objective of this reporting standard is the following: 

- Identify aircraft with high certainty
- Minimize Latency, reduce bandwidth
- Ensure quality and integrity of the data
- Ability to merge different data sources into a single feed. 

## Scope

At the moment the scope of the document is limited to air traffic data. Following items are currently out of scope for this document and data feed: 

- Dynamic geofencing 
- No-fly zones
- NOTAMs 
- Weather reports 

## Data Sources
The following data sources are considered in scope for the purposes of this data feed: 

- Aircraft equipped with sensors that detect and produce data (e.g. ADS-B  / Mode S / Primary Radar / Mode AC / FLARM / UAT)
- Sensors from private companies / 1st level sensors detecting emitted data (e.g. OEM / Radar manufacturers / Sensor manufacturers)
- UAS / Aircraft itself: In the future all drones will detect neighboring aircraft and share it (Traffic information service � broadcast (TIS-B) collects information and broadcasts it to any aircraft in the region)

## Stakeholders
At this stage the document is primarily aimed at the following stakeholders who process or utilize this data. 

- An entity that aggregates that information to provide a big picture. This could be a UAS Service Supplier, Air Navigation Service Provider or other aggregators  
- 2nd level of aggregators that in turn send this data to the stakeholders listed above. 


## Sample Traffic Object
Below is a sample traffic object a 1st level sensor records. This object details information about two aircraft in the airspace: 

		{
		   "observations":[
			  {
				 "icaoAddress":"39C812",
				 "trafficSource":0,
				 "latDD":47.538528,
				 "lonDD":-115.133696,
				 "altitudeMM":13106400,
				 "headingDE2":203,
				 "horVelocityCMS":23149,
				 "verVelocityCMS":0,
				 "squawk":1362,
				 "altitudeType":0,
				 "callsign":"LEA022H ",
				 "emitterType":2,
				 "sourceGuid":"7541622b4f4c2e59",
				 "utcSync":1,
				 "timeStamp":"2017-02-13T14:42:00.111Z",
				 "processingDelay":"13106400",
				 "EstimErrLat":"0.000008",
				 "EstimErrLon":"0.000002",
				 "EstimErrAlt":"2",
				 "EstimErrHdg":"0.000002",
				 "EstimErrHorVel":"3",
				 "EstimErrVerVel":"1",
				 "detail":{
					"navIntegrity":8,
					"navAccuracy":2,
					"verVelocitySrc":1,
					"emergencyStatus":0,
					"surveilStatus":0,
					"baroaltDiffMM":0,
					"sysIntegrityLevel":3,
					"airGroundState":0,
					"svHeadingType":0,
					"verticalVelType":1,
					"navPostionAccuracy":10,
					"navVelocityAccuracy":2,
					"navIntegrityBaro":1,
					"tcasAcasOperating":1,
					"tcasAcasAdvisory":0,
					"identSwActive":0,
					"magHeading":0,
					"utcCoupledCondition":0
				 }
			  },
			  {
				 "icaoAddress":"780A70",
				 "trafficSource":0,
				 "headingDE2":289,
				 "horVelocityCMS":24127,
				 "verVelocityCMS":-32,
				 "altitudeType":0,
				 "emitterType":0,
				 "sourceGuid":"7541622b4f4c2e59",
				 "utcSync":1,
				 "timeStamp":"2017-02-13T14:41:57.189Z",
				 "detail":{
					"navIntegrity":0,
					"navAccuracy":2,
					"verVelocitySrc":0,
					"emergencyStatus":0,
					"surveilStatus":0,
					"baroaltDiffMM":0,
					"sysIntegrityLevel":0,
					"airGroundState":0,
					"svHeadingType":0,
					"verticalVelType":0,
					"navPostionAccuracy":0,
					"navVelocityAccuracy":2,
					"navIntegrityBaro":0,
					"tcasAcasOperating":0,
					"tcasAcasAdvisory":0,
					"identSwActive":0,
					"magHeading":0,
					"utcCoupledCondition":0
				 }
			  }
		   ]
		}


## Sample Source status Object
All sensors should provide a status on its own health and basic information about itself such as its location. The following JSON extract details an output on the health or service. The sensor can be online and made available via a API endpoint. 

		{
		"status": {
		      "sourceGuid":"7541622b4f4c2e59", 
		      "sourceVersionMajor":0, 
		      "sourceVersionMinor":9, 
		      "sourceVersionBuild":4, 
		      "timeStamp":"2017-02-13T14:42:00.18872Z",                
		      "sourceLatDD":48.091530, 
		      "sourceLonDD":-114.105026, 
		      "gpsStatus":3,
		      "receiverStatus":0 
		      }
		}

## Data Specifications 

### Traffic Object Details

| Field Name   | Data Type | Description |
| :--- | :---: | :--- |
| icaoAddress | %02X%02X%02X | ICAO 24-bit address |
| trafficSource | %d | 0 = 1090ES <br> 1 = UAT <br> 2 = Multi-radar (MRT)<br> 3 = MLAT<br> 4 = SSR<br> 5 = PSR<br> 6 = Mode-S<br> 7 = MRT<br> 8 = SSR + PSR Fused<br> 9 = ADS-B|
| latDD | %f | Latitude expressed as decimal degrees |
| lonDD | %f | Longitude expressed as decimal degrees |
| altitudeMM | %ld | Geometric altitude or barometric pressure altitude in millimeters |
| headingDE2 | %d | Course over ground in centi-degrees |
| horVelocityCMS | %lu | Horizontal velocity in centimeters/sec |
| verVelocityCMS | %ld | Vertical velocity in centimeters/sec with positive being up |
| squawk | %d | Transponder code |
| altitudeType | %d | Altitude Source | 0 = Pressure <br> 1 = Geometric |
| callSign | %c%c%c%c %c%c%c%c | Callsign |
| emitterType | %d | Category type of the emitter <br> 0 = No aircraft type information <br> 1 = Light (ICAO) &lt; 15,500 lbs<br> 2 = Small - 15,500 to 75,000 lbs<br> 3 = Large - 75,000 to 300,000 lbs <br> 4 = High Vortex Large (e.g., B757)<br> 5 = Heavy (ICAO) - &gt; 300,000 lbs<br> 6 = Highly Maneuverable &gt; 5G acceleration and high speed<br> 7 = Rotocraft<br> 8 = Glider/sailplane<br> 9 = Lighter than air<br> 10 = Parachutist/sky diver<br> 11 = Ultra light/hang glider/paraglider<br> 12 = Unmanned aerial vehicle<br> 13 = Space/trans-atmospheric vehicle<br> 14 = Surface vehicle-emergency vehicle<br> 15 = Surface vehicle-service vehicle<br> 16 = Point Obstacle (includes tethered balloons)<br> 17 = Cluster Obstacle<br> 18 = Line Obstacle|
| sequenceNumber   | %d | Auto incrementing packet sequence number |
| sourceGuid | %02x%02x%02x%02x %02x%02x%02x%02x | Unique source/equipment Identifier |
| utcSync | %d | UTC time flag |
| timeStamp | %s | Time packet was received at the sourceStation ISO 8601 format: YYYY-MM-DDTHH:mm:ss:ffffffffZ |
| processingDelay | %d | Delay in processing:  the difference when the data was received and published. In milli-seconds. |
| estimatedErrorLat | %f | Estimated error in latitude in decimal degrees |
| estimatedErrorLon | %f | Estimated error in longitude in decimal degrees |
| estimatedErrorAlt | %f | Estimated error in Altitude in meters |
| estimatedErrorHdg | %f | Estimated Error in Heading in decimal degrees |
| estimatedErrorVel | %d | Estimated error in Velocity |
| estimatedErrorVerVel | %d | Estimated error in Vertical Velocity |



A field called Detail can be added for extra information for each of the aircraft object if avaialble. 

| Field Name | Data Type | Description |
| :--- | :---: | :--- |
| navIntegrity | %d | Navigation integrity category (NIC) <br> 0  = RC &gt;=37.04 km (20 NM) Unknown Integrity <br> 1  = RC &lt; 37.04 km (20 NM) RNP-10 containment radius <br> 2  = RC &lt; 14.816 km (8 NM) RNP-4 containment radius <br> 3  = RC &lt; 7.408 km (4 NM) RNP-2 containment radius <br> 4  = RC &lt; 3.704 km (2 NM) RNP-1 containment radius <br> 5  = RC &lt; 1852 m (1 NM) RNP-0.5 containment radius <br> 6  = RC &lt; 1111.2 m (0.6 NM) RNP-0.3 containment radius <br> 7  = RC &lt; 370.4 m (0.2 NM) RNP-0.1 containment radius <br> 8  = RC &lt; 185.2 m (0.1 NM) RNP-0.05 containment radius <br> 9  = RC &lt; 75 m and VPL &lt; 112 m e.g., SBAS, HPL, VPL <br> 10 = RC &lt; 25 m and VPL &lt; 37.5 m e.g., SBAS, HPL, VPL <br> 11 = RC &lt; 7.5 m and VPL &lt; 11 m e.g., GBAS, HPL, VPL <br> 12 = (Reserved) (Reserved) <br> 13 = (Reserved) (Reserved) <br> 14 = (Reserved) (Reserved) <br> 15 = (Reserved) (Reserved) <br> 16 = (Reserved) (Reserved) |
| navAccuracy | %d | Navigation accuracy category (NACv)  <br> 0 = Unknown or &gt;= 10 m/s Unknown &gt;= 50 feet (15.24 m) per second <br> 1 = &lt; 10 m/s  &lt; 50 feet (15.24 m) per second <br> 2 = &lt; 3 m/s < 15 feet (4.57 m) per second <br> 3 = &lt; 1 m/s &lt; 5 feet (1.52 m) per second <br> 4 = &lt; 0.3 m/s &lt; 1.5 feet (0.46 m) per second <br> 5 = (Reserved) (Reserved) <br> 6 = (Reserved) (Reserved) <br> 7 = (Reserved) (Reserved) |
| verVelocitySrc | %d | Vertical velocity source<br> 0 = Pressure <br> 1 = Geometric  |
| emergencyStatus | %d | Emergency status <br> 0 = No-Emergency <br> 1 = General Emergency <br> 2 = Lifeguard/Medical <br> 3 = Min Fuel <br> 4 = No Comm <br> 5 = Unlawful Interference <br> 6 = Downed Aircraft |
| sysIntegrityLevel | %d | System Integrity Level (SIL) |
| airGroundState | %d | Airborne or ground <br> 0 = Airborne subsonic condition <br> 1 = Airborne supersonic condition <br> 2 = On ground condition |
| svHeadingType | %d | Track angle from heading <br> 0 = Data Not Available <br> 2 = Magnetic Heading <br> 3 = True Heading |
| verticalVelType | %d | Vertical rate information <br> 0 = Pressure <br> 1 = Geometric |
| navPostionAccuracy | %d | The reported State Vector has sufficient position accuracy for the intended use (NACp) <br> 0  = EPU &gt;= 18.52 km (10 NM) <br> 1  = EPU &lt; 18.52 km (10 NM) <br> 2  = EPU &lt; 7.408 km (4 NM) <br> 3  = EPU &lt; 3.704 km (2 NM) <br> 4  = EPU &lt; 1852 m (1NM) <br> 5  = EPU &lt; 926 m (0.5 NM) <br> 6  = EPU &lt; 555.6 m (0.3 NM) <br> 7  = EPU &lt; 185.2 m (0.1 NM) <br> 8  = EPU &lt; 92.6 m (0.05 NM) <br> 9  = EPU &lt; 30 m and VEPU &lt; 45 m <br> 10 = EPU &lt; 10 m and VEPU &lt; 15 m <br> 11 = EPU &lt; 3 m and VEPU &lt; 4 m <br> 12 = (Reserved) <br> 13 = (Reserved) <br> 14 = (Reserved) <br> 15 = (Reserved) |
| navVelocityAccuracy | %d | The least accurate velocity component being transmitted (NACv) <br> 0 = Unknown or &gt;= 10 m/s Unknown or &gt;= 50 feet (15.24 m) per second |<br> 1 = &lt; 10 m/s &lt; 50 feet (15.24 m) per second <br> 2 = &lt; 3 m/s &lt; 15 feet (4.57 m) per second <br> 3 = &lt; 1 m/s &lt; 5 feet (1.52 m) per second <br> 4 = &lt; 0.3 m/s &lt; 1.5 feet (0.46 m) per second <br> 5 = (Reserved) (Reserved) <br> 6 = (Reserved) (Reserved) <br> 7 = (Reserved) (Reserved)|
| navIntegrityBaro | %d | Barometer checked (NICbaro) <br>0 = Barometric Pressure Altitude has NOT been cross checked <br>1 = Barometric Pressure Altitude has been cross checked |
| tcasAcasOperating | %d | Aircraft is fitted with a TCAS (ACAS) computer and that computer is turned on and operating in a mode that can generate Resolution Advisory (RA) alerts |
| tcasAcasAdvisory | %d | TCAS II or ACAS computer is currently issuing a Resolution Advisory |
| identSwActive | %d | Ident switch is activated |
| atcServicesRecvd | %d | ATC pilot message mode setting <br> 0 = Not receiving ATC messages <br> 1 = Receiving ATC messages |
| magHeading | %d | True north or magnetic north <br> 0 = True north <br> 1 = Magnetic north |
| utcCoupledCondition | %d | Represents if the Ground Station is UTC-Coupled <br> 0 = Ground Station is not UTC coupled <br> 1 = Ground Station is UTC coupled |
| surveilStatus | %d | Surveillance status <br> 0 = No Condition <br> 1 = permanent alert <br> 2 = temp alert <br> 3 = SPI |
| secondaryAltType | %d | Altitude source <br> 0 = Pressure <br> 1 = Geometric |
| secondaryAltitudeMM | %ld | Geometric altitude or barometric pressure altitude in millimeters |
| tisBSiteId | %d | The tisBSiteId is unit-less and is from the a transmitted TISb UAT message signifies which uplink tower transmitted the TISb frame |
| transmitMSO | %d | the transmitMSO is the 6bit field from the transmitted UAT message which should signify which MSO the message was transmitted in. MSO&#39;s can range from 0 to 3951 but only transmit the 6 LSB&#39;s of the actual MSO if transmitted. Received range is from 0 - 63. |
|addressQualifier | %d | Defines the type of target that delivered the data <br> 0 = ADS-B target with ICAO 24-bit <br> 1 = Reserved for National use <br> 2 = TIS-B target with ICAO 24-bit address <br> 3 = TIS-B target with track file identifier <br> 4 = Surface Vehicle <br> 5 = Fixed ADS-B Beacon <br> 6 = (Reserved) <br> 7 = (Reserved) |
| uatMopsVersion | %d |1 = DO-282A<br> 2 = DO-282B |
| callSignID | %d | 0 = Fightplan <br> 1 = CallSign |


### Status Object Details

| Field Name | Data Type | Description |
| :--- | :---: | :--- |
| sourceGuid | %02x%02x%02x%02x %02x%02x%02x%02x | Unique Station identifier |
| sourceVersionMajor | %d | SOURCE\_MAJOR\_VERSION |
| sourceVersionMinor | %d | SOURCE\_MINOR\_VERSION |
| sourceVersionBuild | %d | SOURCE\_BUILD\_VERSION |
| timeStamp | %s | Time packet was received at the sourceStation ISO 8601 format |
| sourceLatDD | %f | Fixed station latitude expressed as decimal degrees |
| sourceLonDD | %f | Fixed station longitude expressed as decimal degrees |
| sourceAltType | %d | 0 = Barometric Altitude 1 = GNSS Altitude |
| sourceAltMM | %d |  Altitude in mm |
| gpsStatus | %d | The communication and health status of the source GPS <br> 0 = GPS not present or functioning <br> 1 = Not locked <br> 2 = 2D fix <br> 3 = 3D fix <br> 4 = DGPS fix |
| receiverStatus | %d | The communication and health status of the sourceStation receiver <br> 0 = functioning normally <br> 1 = excessive communication errors <br> 2 = device not transmitting |



## Appendix
The objective of this section is to highlight some considerations for different sensor manufacturers and others when implementing this system especially as the UTM ecosystem evolves.

### Data Verbosity
It can be argued that the structure stated above produces a very verbose JSON. There are many tricks and techniques to compress JSON e.g. [protobuf](https://developers.google.com/protocol-buffers/) and ways to exchange protobuf e.g. [GPRC](https://grpc.io/docs/guides/) and others that already exist or will be developed in the future. However, verbosity is a consideration that must be taken into account given the frequency with which we expect this data to be updated (1 object per second). At this time (mid-2018) most of the sensors are ethernet-based with a MAC address associated with them. Since the sensor is ethernet based, bandwidth etc. is not a issue. So this section deals with a trade-off that we anticipate will have to be made as sensors evolve in the UTM ecosystem. We anticipate different type of sensors (ground, air bourne etc.) and different network and operating environments (e.g. ethernet, LTE, IoT, Bluetooth and others). These environments come with their own constraints and operating conditions and appropriate technical decisions need to be made. 

A large object can be sent without having to worry about data limits, bandwidth etc. However, if the same data is sent over LTE or other IOT / low bandwidth environments the size of the object becomes a major problem. We anticipate in some cases the sensors will be airborne or the sensor will have a `data plan` associated with it. In such cases it maybe useful to limit the amount of data transmitted so as not to use up bandwidth and also for other reasons like clogging the network and conserving battery. We recommend that at the very least the following fields be transmitted by sensors in a low bandwidth environment. It must be noted that this is not a comprehensive list or a official recommendation.

- icaoAddress
- trafficSource
- latDD
- lonDD
- altitudeMM

### Push vs Pull
Similar to the considerations above, there are primarily two models that data can be transmitted, a user requests data from a sensor (pull) and a sensor emits it out over a public interface (push) regardless if someone is asking for it. We anticipate technical challenges when using either of these models in a sensor in the future. For e.g. in a pull model a interested party has to send a request through a HTTP POST or others to get a response with the air-traffic data. But how does one know how many sensors are around to make such queries (discovery). In a push model data is being emitted continuously over publicly documented interfaces so the interested parties have to tune their receivers on that interface to get data. At the moment apart from some protocols like ADS-B, FLARM etc. there is no standard way to sense and receive transmissions. We anticipate that sensor manufacturers will build interfaces unique to their device and there will not be any standard port (e.g. 22 for SSH) that all of them would agree on.

Currently, a number of these sensors are connected over Ethernet so a PC, server or laptop's network discovery tools help in identifying and connecting to the sensor but in the future the network environment will play a important part in the transmission and delivery model.

### Full Object vs Deltas
This protocol assumes that every data transmitted is a complete object that has all the flights in the airspace at that time. There is another chain of thought that is has some benefits, the transmission of deltas, i.e. only the changes to things. This type of data transmission is commonly used to efficiently transmit temporal data and comes with a number of benefits. For the purposes of this protocol, we will not consider this in scope, instead we rely on the sensor manufacturers to develop APIs and technologies to enable `delta` transmissions and queries. Most people prefer full verbose data transmission but as detailed earlier a verbose data feed may not be most suitable. A way forward in this situation especially with a scenario with limited bandwidth is to have a API available in the sensor that sends information as deltas via a API call and reduces the frequency at which the full data object is sent over the network. 

## Open Issues
Currently there are a few open issues and they are tracked using the issues section in this repository, please open new issues if you want to comment or have concerns about the protocol, the key ones that address the appendix section above are below and your suggestions / contributions are welcome. Please add additional comments on the issue itself or suggest merge requests.

