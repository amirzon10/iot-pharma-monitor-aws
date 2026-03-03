Deployment Region: eu-north-1 (Stockholm)

1. System Objectives & Scenario
Cold Chain is an important aspect in drug logistics. The project addresses the problem of monitoring the environment at Dublin Lab A using the IoT monitoring system to guarantee that the sensitive medicines (insulin or vaccines) are kept in the safe environment conditions.
Monitoring Objectives:
* Temperature: helps to prevent the denaturation of proteins (Threshold: $>25 o C).
* Humidity: Ensures packaging integrity and prevents chemical degradation.
* Atmospheric Pressure: Detects vacuum-seal breaches or altitude changes during transit.
* Geospatial Tracking: Real-time location metadata for logistics auditing.

2. High-Level Architecture

![System Architecture](images/IOT system flow.png)

The system architecture is based on the use of decoupled, cloud-native design that guarantees high availability and data persistence.
There are software components that the computer recognizes.<|human|>Software Component Identification:

* IoT Level (The Edge):
    * Sense HAT Simulator: Generates raw environmental telemetry.
    * Node-RED: The Event-based logic engine that is the Edge Gateway.
* Cloud Level (AWS):
    * AWS IoT Core: Manages secure MQTT message brokering.
    * IoT Rules Engine: real-time SQL packet filter.
    * Amazon DynamoDB: A NoSQL database that is used to achieve long-term persistence of the telemetry history.
    * AWS SNS: This is an asynchronous notification service of threshold-breach alerting.
* Application Level:
  * Web Dashboard: A Web-based (JavaScript) interface.
  * aws-sdk-js: This package helps in connecting the browser to the database.
  * Chart.js: Chart library- A high-performance time series visualizations library.


3. IoT Data Gathering and Cloud Intersection.
Data Schema
The system makes use of structured JSON payload. The application uses the unmarshalling layer to take care of the DynamoDB marshalled format in which numbers are represented as strings and have type descriptors.
Data Mapping, Mathematically represented:
$$ \text {Sensor Value } (v) = M.sensors.key.N. N.sensors.parseFloat( M.sensors.key.N)
Published Payload to sensehat/data:

Notification Logic (SNS)
The system utilizes a Reactive Pattern. The SQL logic of a stream that is monitored by an IoT Rule is as follows:
Select FROM sensehat/data WHERE sensors.temperature is larger than 25.
In case of the condition, a trigger is relayed to the SNS Topic, and the latter sends an instant email notification to the logistics manager.

5. Web Visualisation & Analytics
The dashboard will be used to operate with three vital data perspectives:
1. Real-Time Status: The latest packet is retrieved and displayed as "Live Data."
2. Historical Trends: Separate line charts for each sensor type using Chart.js.
3. Hourly Aggregates: The system will query the previous 3,600000ms of data to compute:
$$\mu = \frac{\sum_{i=1}^{n} x_i}{n}$$
Range Analysis:
$$\text{Boundaries} = [\min(X), \max(X)]$$

🚀 5. Setup & Execution
Prerequisites
* Node-RED installed locally or on a Pi.
* The AWS Account having an active IAM user (AmazonDynamoDBFullAccess)..
Installation Steps
1. AWS Setup: * Create a DynamoDB table named SensorData (Partition Key: device_id, Sort Key: timestamp).
    * Create an SNS Topic and subscribe your email.
    * Create an IoT Rule with the SQL statement provided in Section 3.
2. Node-RED: * Import PharmaGuard_Flow.json from the /node-red folder.
    * Update the AWS MQTT node with your specific IoT Endpoint.
3. Dashboard: * Open index.html.
    * Ensure the AWS Credentials (Access Key/Secret Key) are correctly configured in the <script> section.
