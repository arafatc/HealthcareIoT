# HealthcareIoT-Basic
The device simulator will publish Heart Rate, SpO2 (Oxygen Saturation) and Body Temperature values periodically. This is channeled to the cloud (Iot Core → DynamoDB), and then we aggregate data and detect anomalies. The device simulator code can push data to an IoT core endpoint.

![image](https://user-images.githubusercontent.com/45310865/135245132-d8c24e4f-885e-43d7-a907-18d4f75e6bc2.png)

Problem Statement.  
  There are three top-level tasks in this project as listed below:
1. Created Things, Certificates, Policy, DynamoDB table
(bsm_data) and IoT Core Rule to push the data created to the DynamoDB table. 

2. Aggregate the data in another table
  a. Created a new table (bsm_agg_data). Created model and database classes to access DynamoDB.
  b. Added functionality to aggregate the data from the three sensors individually, at the minute level. Then calculated and stored the average,
  minimum and maximum values taking all values within a minute in account. Added aggregator method call for a specific time range and aggregate
  data per minute within that range. This is stored programmatically in the aggregator table.
  For example, when called with a time range of ‘2021-02-17 13:00:00 to 2021-02-17 14:00:00’, it creates 60 rows per sensor type in the aggregated table, 
  each being the  aggregation of all values within a minute for that sensor data type. Entries can be skipped if there is no relevant data.

3. Detect anomalies based on rules and store them
  a. Created a rules config file in json to define combination alert rules. In this we define an OR rule with min and max average values for any of the
  three data types. In addition, we can also define a trigger count so that if the values should be breached continuously that many times, an
  alert would  be raised. For example, a rule can be {‘type’: ‘HeartRate’, ‘avg_min’: 55, ‘avg_max’: 105, ‘trigger_count’: 5} 
  This should trigger an alert if five continuous aggregated (at the minute level) average values are outside the min/max range.
  Also wrote a parser for these rules in the code. 
  b. Created a new table (bsm_alerts) using code. Created the appropriate model to access it.
  Added functionality to run the rule check for a specified time range. It checks all rules on all devices individually within that time period and
  raise alerts as appropriate. These rules are printed on the console, and saved to the bsm_alerts
