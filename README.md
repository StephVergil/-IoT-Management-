# IoT Management: A Comprehensive Guide

This guide provides a detailed walkthrough of setting up IoT device management using **ThingsBoard**, an open-source IoT platform. It explains foundational concepts, practical steps, and the role of MQTT messaging for telemetry and alarm handling.

---

## Project Links

- [IoT Management Part 1](https://github.com/StephVergil/-IoT-Management-/blob/main/IoT%20Management%20%20Part%201.docx)
- [IoT Management Part 2](https://github.com/StephVergil/-IoT-Management-/blob/main/loT%20Management%20part%202.docx)

---

## Overview

### What is IoT Management?

The **Internet of Things (IoT)** is a network of interconnected devices that communicate with each other to collect and exchange data. Effective IoT management involves:
- Configuring and monitoring devices.
- Establishing rules for automated decision-making.
- Managing device data securely and efficiently.

### Objectives of the Lab

1. **ThingsBoard Service**: Set up an IoT management platform for handling devices and data.
2. **Accounts**: Create and configure users, tenants, and customers.
3. **Rule Chains**: Automate the processing of IoT device telemetry and alarms.
4. **IoT Devices**: Add, configure, and test devices using MQTT protocols.

---

## Key Concepts

### **1. ThingsBoard**
ThingsBoard is a scalable and open-source IoT platform that provides:
- **Device Management**: Adding, grouping, and monitoring devices.
- **Telemetry Processing**: Handling real-time data (temperature, humidity, etc.).
- **Rule Chains**: Automated workflows for data processing and alerts.

### **2. MQTT (Message Queuing Telemetry Transport)**
MQTT is a lightweight messaging protocol designed for IoT devices:
- **Publisher-Subscriber Model**: Devices publish data to topics, and subscribers receive updates.
- **Efficient Communication**: Ideal for low-bandwidth, high-latency environments.

### **3. Rule Chains**
Rule chains in ThingsBoard allow automation of IoT device behavior:
- Validate incoming data.
- Trigger alerts based on conditions.
- Save telemetry for analysis.

---

## Step-by-Step Guide

### 1. Starting the Service and Creating Accounts

#### **1.1 Start ThingsBoard Service**
1. **Launch the Server**:
   - Open the **UbuntuSRV** terminal.
   - Run the following command to start the ThingsBoard service:
     ```bash
     sudo docker run -it -p 9090:9090 -p 1883:1883 -p 7070:7070 -p 5683-5688:5683-5688/udp \
     -v ~/.mytb-data:/data -v ~/.mytb-logs:/var/log/thingsboard --name mytb --restart always thingsboard/tb-postgres
     ```

2. **Verify Service**:
   - Access `http://localhost:9090` in a browser.
   - Log in with default credentials and confirm that ThingsBoard is running.

**Why?**  
- ThingsBoard acts as the control center for IoT devices. Running the service initializes its ability to manage devices, users, and data.

---

#### **1.2 Create and Secure Accounts**
1. **Update Admin Credentials**:
   - Change the admin email to `sysadmin@xyzsecurity.com`.
   - Set a strong password (`NDGlabpass123!`).

2. **Create a New Tenant**:
   - Delete the default tenant for security purposes.
   - Add a tenant titled `netlab`.

3. **Add a Tenant Admin**:
   - Email: `netlab_tenantadmin@xyzsecurity.com`.
   - Activate the admin account via the provided link.

4. **Add a Customer**:
   - Title: `test_customer`.

**Why?**  
- Tenants represent organizations, while customers represent device owners or groups. These accounts ensure secure and hierarchical access to IoT resources.

---

### 2. Configuring Rule Chains and Devices

#### **2.1 Automate with Rule Chains**
1. **Create a Rule Chain**:
   - Navigate to **Rule Chains** → Create `temperatureRule`.

2. **Add Rule Components**:
   - **Message Type Switch**: Routes POST telemetry messages.
   - **Check Existence Fields**: Validates that `temperature` and `humidity` fields exist.
   - **Script**: Processes data with this logic:
     ```javascript
     return msg.temperature > 68 && msg.temperature < 71 && msg.humidity > 40 && msg.humidity < 60;
     ```
   - **Save Timeseries**: Stores valid data for analysis.
   - **Create Alarm**: Triggers warnings for out-of-range values.

3. **Link Components**:
   - Connect components to define the data flow:
     - Input → Message Type Switch → Check Existence Fields → Script.
     - Script → Save Timeseries (True) and Create Alarm (False).

4. **Save Rule Chain**:
   - Finalize the chain to automate telemetry validation and alarm creation.

**Why?**  
- Rule chains enable automated responses to device data, reducing manual intervention and ensuring efficient monitoring.

---

#### **2.2 Add IoT Devices**
1. **Create a Device**:
   - Name: `temperatureSensor`.
   - Profile: `mqtt`.
   - Rule Chain: `temperatureRule`.

2. **Configure MQTT**:
   - Telemetry Topic: `v1/devices/temperatureSensor/telemetry`.
   - Attributes Topic: `v1/devices/temperatureSensor/attributes`.

3. **Assign to Customer**:
   - Assign the device to `test_customer`.

**Why?**  
- Devices must be configured to communicate data (telemetry) and alarms to ThingsBoard for analysis.

---

### 3. Testing IoT Devices

#### **3.1 Send MQTT Messages**
1. **Copy Access Token**:
   - Navigate to `temperatureSensor` → Copy the access token.

2. **Send Telemetry**:
   - Open a terminal and send a message:
     ```bash
     mosquitto_pub -d -q 1 -h "127.0.0.1" -t "v1/devices/temperatureSensor/telemetry" -u "$ACCESS_TOKEN" \
     -m '{"temperature":69,"humidity":56}'
     ```

3. **Trigger Alarms**:
   - Send an out-of-range temperature:
     ```bash
     mosquitto_pub -d -q 1 -h "127.0.0.1" -t "v1/devices/temperatureSensor/telemetry" -u "$ACCESS_TOKEN" \
     -m '{"temperature":85,"humidity":50}'
     ```
   - Check the **Alarms** tab for warnings.

**Why?**  
- Sending test messages ensures the device and rule chains function as expected. The alarms confirm proper telemetry validation.

---

## Resources

- **ThingsBoard Documentation**: [Official Site](https://thingsboard.io/docs/)
- **MQTT Overview**: [Learn MQTT](https://mqtt.org/)
- **Docker Commands**: [Docker CLI Guide](https://docs.docker.com/engine/reference/commandline/cli/)

---

## Key Takeaways

1. **IoT Device Management**:
   - Securely manage devices, tenants, and customers through ThingsBoard.
2. **Automated Monitoring**:
   - Use rule chains for real-time telemetry validation and alerts.
3. **Scalable Testing**:
   - Leverage MQTT for lightweight, efficient device communication.

---

### Disclaimer
This project was conducted in a controlled environment. Unauthorized use of these techniques or tools outside such an environment may violate ethical guidelines and legal regulations.

---

> **Author**: Stephanie Vergil
