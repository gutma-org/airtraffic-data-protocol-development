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
	- [Traffic Source and Mandatory Fields](#traffic-source-and-mandatory-fields)
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
- UAS / Aircraft itself: In the future all drones will detect neighboring aircraft and share it (Traffic information service - broadcast (TIS-B) collects information and broadcasts it to any aircraft in the region)

## Stakeholders
At this stage the document is primarily aimed at the following stakeholders who process or utilize this data.

- An entity that aggregates that information to provide a big picture. This could be a UAS Service Supplier, Air Navigation Service Provider or other aggregators  
- 2nd level of aggregators that in turn send this data to the stakeholders listed above.


## Sample Traffic Object
Below is a sample traffic object a 1st level sensor records. This object details information about two aircraft in the airspace:

		{
		   "observations":[
			  {
				 "icao_address":"39C812",
				 "traffic_source":0,
				 "lat_dd":47.538528,
				 "lon_dd":-115.133696,
				 "altitude_mm":13106400,
				 "heading_de2":203,
				 "hor_velocity_cms":23149,
				 "ver_velocity_cms":0,
				 "squawk":1362,
				 "altitude_type":0,
				 "call_sign":"LEA022H ",
				 "emitter_type":2,
				 "source_guid":"7541622b4f4c2e59",
				 "utc_sync":1,
				 "time_stamp":"2017-02-13T14:42:00.111Z",
				 "processing_delay":"13106400",
				 "estimated_error_latitude":"0.000008",
				 "estimated_error_longitude":"0.000002",
				 "estimated_error_altitude":"2",
				 "estimated_error_heading":"0.000002",
				 "estimated_error_horizontal_velocity":"3",
				 "estimated_error_vertical_velocity":"1",
				 "detail":{
					"navigation_integrity":8,
					"navigation_accuracy":2,
					"vertical_velocity_source":1,
					"emergency_status":0,
					"surveillance_status":0,
					"barometric_altitude_difference_mm":0,
					"system_integrity_level":3,
					"air_ground_state":0,
					"sv_heading_type":0,
					"vertical_velocity_type":1,
					"navigation_position_accuracy":10,
					"nav_velocity_accuracy":2,
					"navigation_integrity_barometric":1,
					"tcas_acas_operating":1,
					"tcas_acas_advisory":0,
					"ident_switch_active":0,
					"magnetic_heading":0,
					"utc_coupled_condition":0
				 }
			  },
			  {
				 "icao_address":"780A70",
				 "traffic_source":0,
				 "heading_de2":289,
				 "hor_velocity_cms":24127,
				 "ver_velocity_cms":-32,
				 "altitude_type":0,
				 "emitter_type":0,
				 "source_guid":"7541622b4f4c2e59",
				 "utc_sync":1,
				 "time_stamp":"2017-02-13T14:41:57.189Z",
				 "detail":{
					"navigation_integrity":0,
					"navigation_accuracy":2,
					"vertical_velocity_source":0,
					"emergency_status":0,
					"surveillance_status":0,
					"barometric_altitude_difference_mm":0,
					"system_integrity_level":0,
					"air_ground_state":0,
					"sv_heading_type":0,
					"vertical_velocity_type":0,
					"navigation_position_accuracy":0,
					"nav_velocity_accuracy":2,
					"navigation_integrity_barometric":0,
					"tcas_acas_operating":0,
					"tcas_acas_advisory":0,
					"ident_switch_active":0,
					"magnetic_heading":0,
					"utc_coupled_condition":0
				 }
			  }
		   ]
		}


## Sample Source status Object
All sensors should provide a status on its own health and basic information about itself such as its location. The following JSON extract details an output on the health or service. The sensor can be online and made available via a API endpoint.

		{
		"status": {	
		      "source_guid":"7541622b4f4c2e59",
		      "source_version_major":0,
		      "source_version_minor":9,
		      "source_version_build":4,
		      "time_stamp":"2017-02-13T14:42:00.18872Z",                
		      "source_latitude_dd":48.091530,
		      "source_longitude_dd":-114.105026,
		      "source_latitude_dd":48.091530,
		      "source_altitude_mm":13106400,
			  "source_altitude_type":0,
		      "gps_status":3,
		      "receiver_status":0,
		      "hardware_version":"0.0.1",
		      "software_version":"0.0.1",
		      "software_checksum":"73f48840b60ab6da68b03acd322445ee",
			  "software_checksum_type":"0",
			  "data_reporting_version":"1"

		      }
		}

## Data Specifications

### Traffic Object Details
For mandatory fields, please refer to the [Traffic Source and Mandatory Fields](#traffic-source-and-mandatory-fields) section.

| Field Name   | Data Type | Acceptable Value Range |Description |
| :--- | :---: | :---| :--- |
| icao_address | %02X%02X%02X | e.g. AC82EC  |ICAO 24-bit address |
| traffic_source | %d | 0-10 |0 = 1090ES <br> 1 = UAT <br> 2 = Multi-radar (MRT)<br> 3 = MLAT<br> 4 = SSR<br> 5 = PSR<br> 6 = Mode-S<br> 7 = MRT<br> 8 = SSR + PSR Fused<br> 9 = ADS-B<br> 10 = FLARM|
| source_type | %d | 0-1 |0 = True <br> 1 = Fused <br>|
| lat_dd | %f | -180 to 180 degrees |Latitude expressed as decimal degrees |
| lon_dd | %f |  -180 to 180 degrees |Longitude expressed as decimal degrees |
| altitude_mm | %f |  0 to 10058400 |Geometric altitude or barometric pressure altitude in millimeters |
| heading_de2 | %d | 0 to 36000 |Course over ground in centi-degrees |
| hor_velocity_cms | %f | 0 to 10000000 | Horizontal velocity in centimeters/sec |
| ver_velocity_cms | %f | 0 to 10000000 | Vertical velocity in centimeters/sec with positive being up |
| squawk | %d | x |Transponder code |
| altitude_type | %d | 0-1 |Altitude Source <br>0 = Barometric <br> 1 = Geometric |
| call_sign | %c%c%c%c %c%c%c%c | e.g. N905NA |call_sign |
| emitter_type | %d | 0-18 |Category type of the emitter <br> 0 = No aircraft type information <br> 1 = Light (ICAO) &lt; 15,500 lbs<br> 2 = Small - 15,500 to 75,000 lbs<br> 3 = Large - 75,000 to 300,000 lbs <br> 4 = High Vortex Large (e.g., B757)<br> 5 = Heavy (ICAO) - &gt; 300,000 lbs<br> 6 = Highly Maneuverable &gt; 5G acceleration and high speed<br> 7 = Rotocraft<br> 8 = Glider/sailplane<br> 9 = Lighter than air<br> 10 = Parachutist/sky diver<br> 11 = Ultra light/hang glider/paraglider<br> 12 = Unmanned aerial vehicle<br> 13 = Space/trans-atmospheric vehicle<br> 14 = Surface vehicle-emergency vehicle<br> 15 = Surface vehicle-service vehicle<br> 16 = Point Obstacle (includes tethered balloons)<br> 17 = Cluster Obstacle<br> 18 = Line Obstacle|
| sequenceNumber   | %d | 0 to 10000000 |Auto incrementing packet sequence number |
| source_guid | %02x%02x%02x%02x %02x%02x%02x%02x | x |Unique source/equipment Identifier |
| utc_sync | %d | x |UTC time flag |
| time_stamp | %s | YYYY-MM-DDThh:mm:ss.sss |Time packet was received at the sourceStation ISO 8601 format: YYYY-MM-DDThh:mm:ss.sss |
| processing_delay | %d | 0-10000 |Delay in processing:  the difference when the data was received and published. In milli-seconds. |
| estimated_error_latitude | %f | 0-0.1 |Estimated error in latitude in decimal degrees |
| estimated_error_longitude | %f | 0-0.1 |Estimated error in longitude in decimal degrees |
| estimated_error_altitude | %f | 0-1000 |Estimated error in Altitude in meters |
| estimated_error_heading | %f | 0-0.1 |Estimated Error in Heading in decimal degrees |
| estimated_error_velocity | %d | 0-1000 |Estimated error in Velocity |
| estimated_error_vertical_velocity | %d | 0-1000 |Estimated error in Vertical Velocity |



A field called Detail can be added for extra information for each of the aircraft object if avaialble.

| Field Name | Data Type | Acceptable Value Range |Description |
| :--- | :---: |:--- |:--- |
| navigation_integrity | %d | 0-16 |Navigation integrity category (NIC) <br> 0  = RC &gt;=37.04 km (20 NM) Unknown Integrity <br> 1  = RC &lt; 37.04 km (20 NM) RNP-10 containment radius <br> 2  = RC &lt; 14.816 km (8 NM) RNP-4 containment radius <br> 3  = RC &lt; 7.408 km (4 NM) RNP-2 containment radius <br> 4  = RC &lt; 3.704 km (2 NM) RNP-1 containment radius <br> 5  = RC &lt; 1852 m (1 NM) RNP-0.5 containment radius <br> 6  = RC &lt; 1111.2 m (0.6 NM) RNP-0.3 containment radius <br> 7  = RC &lt; 370.4 m (0.2 NM) RNP-0.1 containment radius <br> 8  = RC &lt; 185.2 m (0.1 NM) RNP-0.05 containment radius <br> 9  = RC &lt; 75 m and VPL &lt; 112 m e.g., SBAS, HPL, VPL <br> 10 = RC &lt; 25 m and VPL &lt; 37.5 m e.g., SBAS, HPL, VPL <br> 11 = RC &lt; 7.5 m and VPL &lt; 11 m e.g., GBAS, HPL, VPL <br> 12 = (Reserved) (Reserved) <br> 13 = (Reserved) (Reserved) <br> 14 = (Reserved) (Reserved) <br> 15 = (Reserved) (Reserved) <br> 16 = (Reserved) (Reserved) |
| navigation_accuracy | %d | 0-7 |Navigation accuracy category (NACv)  <br> 0 = Unknown or &gt;= 10 m/s Unknown &gt;= 50 feet (15.24 m) per second <br> 1 = &lt; 10 m/s  &lt; 50 feet (15.24 m) per second <br> 2 = &lt; 3 m/s < 15 feet (4.57 m) per second <br> 3 = &lt; 1 m/s &lt; 5 feet (1.52 m) per second <br> 4 = &lt; 0.3 m/s &lt; 1.5 feet (0.46 m) per second <br> 5 = (Reserved) (Reserved) <br> 6 = (Reserved) (Reserved) <br> 7 = (Reserved) (Reserved) |
| vertical_velocity_source | %d | 0-1 |Vertical velocity source<br> 0 = Pressure <br> 1 = Geometric  |
| emergency_status | %d | 0-6 |Emergency status <br> 0 = No-Emergency <br> 1 = General Emergency <br> 2 = Lifeguard/Medical <br> 3 = Min Fuel <br> 4 = No Comm <br> 5 = Unlawful Interference <br> 6 = Downed Aircraft |
| system_integrity_level | %d | x |System Integrity Level (SIL) |
| air_ground_state | %d | 0-2 |Airborne or ground <br> 0 = Airborne subsonic condition <br> 1 = Airborne supersonic condition <br> 2 = On ground condition |
| sv_heading_type | %d | 0-3 |Track angle from heading <br> 0 = Data Not Available <br> 2 = Magnetic Heading <br> 3 = True Heading |
| vertical_velocity_type | %d | 0-1 |Vertical rate information <br> 0 = Pressure <br> 1 = Geometric |
| navigation_position_accuracy | %d | 0-14 |The reported State Vector has sufficient position accuracy for the intended use (NACp) <br> 0  = EPU &gt;= 18.52 km (10 NM) <br> 1  = EPU &lt; 18.52 km (10 NM) <br> 2  = EPU &lt; 7.408 km (4 NM) <br> 3  = EPU &lt; 3.704 km (2 NM) <br> 4  = EPU &lt; 1852 m (1NM) <br> 5  = EPU &lt; 926 m (0.5 NM) <br> 6  = EPU &lt; 555.6 m (0.3 NM) <br> 7  = EPU &lt; 185.2 m (0.1 NM) <br> 8  = EPU &lt; 92.6 m (0.05 NM) <br> 9  = EPU &lt; 30 m and VEPU &lt; 45 m <br> 10 = EPU &lt; 10 m and VEPU &lt; 15 m <br> 11 = EPU &lt; 3 m and VEPU &lt; 4 m <br> 12 = (Reserved) <br> 13 = (Reserved) <br> 14 = (Reserved) <br> 15 = (Reserved) |
| nav_velocity_accuracy | %d | 0-7 |The least accurate velocity component being transmitted (NACv) <br> 0 = Unknown or &gt;= 10 m/s Unknown or &gt;= 50 feet (15.24 m) per second <br> 1 = &lt; 10 m/s &lt; 50 feet (15.24 m) per second <br> 2 = &lt; 3 m/s &lt; 15 feet (4.57 m) per second <br> 3 = &lt; 1 m/s &lt; 5 feet (1.52 m) per second <br> 4 = &lt; 0.3 m/s &lt; 1.5 feet (0.46 m) per second <br> 5 = (Reserved) (Reserved) <br> 6 = (Reserved) (Reserved) <br> 7 = (Reserved) (Reserved)|
| navigation_integrity_barometric | %d | 0-1 |Barometer checked (NICbaro) <br>0 = Barometric Pressure Altitude has NOT been cross checked <br>1 = Barometric Pressure Altitude has been cross checked |
| tcas_acas_operating | %d | 0-1 |Aircraft is fitted with a TCAS (ACAS) computer and that computer is turned on and operating in a mode that can generate Resolution Advisory (RA) alerts <br> 0 - is not Fitted <br> 1 - is fitted with TCAS |
| tcas_acas_advisory | %d | 0-1 |TCAS II or ACAS computer is currently issuing a Resolution Advisory <br> 0 - Is not issuing advisory <br> 1  - is issuing advisort |
| ident_switch_active | %d | 0-1 |Ident switch is activated <br> 0 - is not activated <br> 1- is activated |
| atc_services_received | %d | 0-1 |ATC pilot message mode setting <br> 0 = Not receiving ATC messages <br> 1 = Receiving ATC messages |
| magnetic_heading | %d | 0-1 |True north or magnetic north <br> 0 = True north <br> 1 = Magnetic north |
| utc_coupled_condition | %d | 0-1 |Represents if the Ground Station is UTC-Coupled <br> 0 = Ground Station is not UTC coupled <br> 1 = Ground Station is UTC coupled |
| surveillance_status | %d | 0-3 |Surveillance status <br> 0 = No Condition <br> 1 = permanent alert <br> 2 = temp alert <br> 3 = SPI |
| secondary_altitude_type | %d | x |Altitude source <br> 0 = Pressure <br> 1 = Geometric |
| secondary_altitude_mm | %f | 0-1000000 |Geometric altitude or barometric pressure altitude in millimeters |
| tisb_site_id | %d | 0-1 | 0 - The tisb_site_id is unit-less and is from the a transmitted TISb UAT message signifies which uplink tower transmitted the TISb frame 1 - The tisb_site_id is not unit-less |
| transmit_mso | %d | 0-63 |the transmit_mso is the 6bit field from the transmitted UAT message which should signify which MSO the message was transmitted in. MSO&#39;s can range from 0 to 3951 but only transmit the 6 LSB&#39;s of the actual MSO if transmitted. Received range is from 0 - 63. |
|address_qualifier | %d | 0-7 | Defines the type of target that delivered the data <br> 0 = ADS-B target with ICAO 24-bit <br> 1 = Reserved for National use <br> 2 = TIS-B target with ICAO 24-bit address <br> 3 = TIS-B target with track file identifier <br> 4 = Surface Vehicle <br> 5 = Fixed ADS-B Beacon <br> 6 = (Reserved) <br> 7 = (Reserved) |
| uat_mops_version | %d | 1-2 |1 = DO-282A<br> 2 = DO-282B |
| call_sign_id | %d | 0-1 |0 = Fightplan <br> 1 = call_sign |


### Status Object Details

| Field Name | Data Type | Acceptable Values |Description |
| :--- | :---: | :---: |:--- |
| source_guid | %02x%02x%02x%02x %02x%02x%02x%02x | x | Unique Station identifier |
| source_version_major | %d | x | SOURCE\_MAJOR\_VERSION |
| source_version_minor | %d | x |SOURCE\_MINOR\_VERSION |
| source_version_build | %d | x | SOURCE\_BUILD\_VERSION |
| time_stamp | %s | ISO | Time packet was received at the sourceStation ISO 8601 format |
| source_latitude_dd | %f | -180.00 to 180.00 | Fixed station latitude expressed as decimal degrees |
| source_longitude_dd | %f | -180.00 to 180.00 |Fixed station longitude expressed as decimal degrees |
| source_altitude_type | %d | 0-1 | 0 = Barometric Altitude 1 = GNSS Altitude |
| source_altitude_mm | %d | 0-1000000 |  Altitude in mm |
| gps_status | %d | 0-4 |The communication and health status of the source GPS <br> 0 = GPS not present or functioning <br> 1 = Not locked <br> 2 = 2D fix <br> 3 = 3D fix <br> 4 = DGPS fix |
| receiver_status | %d | 0-2 | The communication and health status of the sourceStation receiver <br> 0 = functioning normally <br> 1 = excessive communication errors <br> 2 = device not transmitting |
| hardware_version | %s | x |  A version number for the hardware |
| receiver_status | %d | 0-2 | The communication and health status of the sourceStation receiver <br> 0 = functioning normally <br> 1 = excessive communication errors <br> 2 = device not transmitting |
| hardware_version | %s | x |  A version number for the hardware |
| software_checksum | %s | x |  A string that has the checksum for the software |
| software_checksum_type | %d | 0-2 |  0 = MD5 <br> 1 = SHA-1 |
| data_reporting_version | %d | x |  Version of the traffic data protocol that is being reported by this sensor |


## Traffic Source and Mandatory Fields

There following table details the mandatory fields required per traffic source, this takes in to account the fact that not all traffic sources can provide all the data:

| Traffic Source | Mandatory Fields |
| :--- | :--- |
|1090ES | 1. icao_address <br> 2. traffic_source <br> 3. source_type <br> 4. lat_dd <br> 5. lon_dd <br> 6. altitude_mm <br> 7. time_stamp |
| UAT | 1. icao_address <br> 2. traffic_source <br> 3. source_type<br> 4. lat_dd <br> 5. lon_dd <br> 6. altitude_mm<br> 7. time_stamp  |
| Multi-radar (MRT) | 1. traffic_source <br> 2. source_type<br> 3. lat_dd <br> 4. lon_dd <br> 5. altitude_mm <br> 6. time_stamp |
| MLAT | 1. traffic_source <br> 2. source_type<br> 3. lat_dd <br> 4. lon_dd <br> 5. altitude_mm<br> 6. time_stamp  |
| SSR | 1. traffic_source<br> 2. source_type <br> 3. lat_dd <br> 4. lon_dd <br> 5. altitude_mm<br> 6. time_stamp  |
| PSR | 1. traffic_source <br> 2. source_type<br> 3. lat_dd <br> 4. lon_dd <br> 5. altitude_mm |
| Mode-S | 1. icao_address <br> 2. traffic_source<br> 3. source_type <br> 4. lat_dd <br> 5. lon_dd <br> 6. altitude_mm<br> 7. time_stamp  |
| MRT | 1. icao_address <br> 2. traffic_source <br> 3. source_type<br> 4. lat_dd <br> 5. lon_dd <br> 6. altitude_mm<br> 7. time_stamp  |
| SSR + PSR Fused | 1. icao_address <br> 2. traffic_source <br> 3. source_type<br> 4. lat_dd <br> 5. lon_dd <br> 6. altitude_mm <br> 7. time_stamp |
| ADS-B | 1. icao_address <br> 2. traffic_source <br> 3. source_type<br> 4. lat_dd <br> 5. lon_dd <br> 6. altitude_mm<br> 7. time_stamp  |


## Appendix
The objective of this section is to highlight some considerations for different sensor manufacturers and others when implementing this system especially as the UTM ecosystem evolves.

### Data Verbosity
It can be argued that the structure stated above produces a very verbose JSON. There are many tricks and techniques to compress JSON e.g. [protobuf](https://developers.google.com/protocol-buffers/) and ways to exchange protobuf e.g. [GPRC](https://grpc.io/docs/guides/) and others that already exist or will be developed in the future. However, verbosity is a consideration that must be taken into account given the frequency with which we expect this data to be updated (1 object per second). At this time (mid-2018) most of the sensors are ethernet-based with a MAC address associated with them. Since the sensor is ethernet based, bandwidth etc. is not a issue. So this section deals with a trade-off that we anticipate will have to be made as sensors evolve in the UTM ecosystem. We anticipate different type of sensors (ground, air bourne etc.) and different network and operating environments (e.g. ethernet, LTE, IoT, Bluetooth and others). These environments come with their own constraints and operating conditions and appropriate technical decisions need to be made.

A large object can be sent without having to worry about data limits, bandwidth etc. However, if the same data is sent over LTE or other IOT / low bandwidth environments the size of the object becomes a major problem. We anticipate in some cases the sensors will be airborne or the sensor will have a `data plan` associated with it. In such cases it maybe useful to limit the amount of data transmitted so as not to use up bandwidth and also for other reasons like clogging the network and conserving battery. We recommend that at the very least, in such environments, the fields mentioned in [Traffic Source and Mandatory Fields](#traffic-source-and-mandatory-fields) section should be followed.


### Push vs Pull
Similar to the considerations above, there are primarily two modes that data can be transmitted, a user requests data from a sensor (pull) and a sensor emits it out over a public interface (push) regardless if someone is asking for it. We anticipate technical challenges when using either of these models in the future. For e.g. in a pull model a interested party has to send a request through a HTTPS POST or others to get a response from the sensor. But how does one know how many sensors are around in a geography to make such queries (discovery)?

In a push model data is being emitted continuously over publicly documented interfaces so the interested parties have to tune their receivers on that interface to get data. At the moment apart from some protocols like ADS-B, FLARM etc. there is no standard way to sense and receive transmissions. In a `push` environment, there is a high chance of clogging the airspace with data if there are a number of sensors emitting it. We anticipate that sensor manufacturers will build interfaces unique to their device and there will not be any standard port (e.g. 22 for SSH) that all of them would agree on.

Currently, a number of these sensors are connected over Ethernet so a PC, server or laptop's network discovery tools help in identifying and connecting to the sensor but in the future the network environment will play a important part in the transmission and delivery model.

### Full Object vs Deltas
This protocol assumes that every data transmitted is a complete object that has all the flights in the airspace at that time. There is another chain of thought that is has some benefits that need to considered: the transmission of deltas, i.e. only the changes to things. This type of data transmission is commonly used to efficiently transmit temporal data and comes with a number of benefits. For the purposes of this protocol, we will not consider this in scope, instead we rely on the sensor manufacturers to develop APIs and technologies to enable `delta` transmissions and queries. Full verbose data transmission capability is a must in a sensor so delta but as detailed earlier a verbose data feed may not be most suitable. A way forward in this situation especially with a scenario with limited bandwidth is to have a API available in the sensor that sends information as deltas via a API call and reduces the frequency at which the full data object is sent over the network.

## Open Issues
Currently there are a few open issues and they are tracked using the issues section in this repository, please open new issues if you want to comment or have concerns about the protocol, the key ones that address the appendix section above are below and your suggestions / contributions are welcome. Please add additional comments on the issue itself or suggest merge requests.
