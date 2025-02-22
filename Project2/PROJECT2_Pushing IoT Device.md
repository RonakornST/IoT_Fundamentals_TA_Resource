---

# Pushing IoT Device  
This is a guideline for students enrolled in the **2102541 IoT Fundamental Course** (academic year 2024).  

**Class Instructors:**  
- [Assoc. Prof. Chaodit Aswakul](https://ee.eng.chula.ac.th/chaodit-aswakul/)  
- [Assoc. Prof. Wanchalerm Pora](https://ee.eng.chula.ac.th/wanchalerm-pora/)  

Department of Electrical Engineering, Faculty of Engineering, Chulalongkorn University, Thailand  

This repository was created by **Ronakorn Saetang**  
Department of Electrical Engineering, Faculty of Engineering, Chulalongkorn University, Bangkok, Thailand  

---

## Overview  
This project builds on **Project #1** and the **NETPIE2020 experiment**, where we used **Postman** to simulate a real IoT device communicating with a **NETPIE broker** via **MQTT**.  

In this experiment, we will focus on connecting a real IoT device (**Cucumber Board**) to **NETPIE** for **local and remote monitoring & control**.  

### **Key Objectives:**  
- Setting up an account on **NETPIE.io**  
- Creating and configuring IoT devices  
- Using **MQTT Explorer** for data publishing and subscription  
- Developing an **Arduino** program to read sensor data and push it to NETPIE  

---

## Getting Started  

### **Step 1: Set Up a NETPIE Account**  
1. **Create an account** on [NETPIE.io](https://auth.netpie.io/signup).  
2. **Log in** to [NETPIE 2020](https://netpie.io/guide) and follow the [official documentation](https://docs.netpie.io/en/).  
3. **Create a project** inside NETPIE.  
4. **Create two devices** within the project (both devices must be in the **same group**):  
   - `Cucumber` (for local monitoring & control)  
   - `App` (for remote monitoring & control)  
5. **Retrieve credentials**: Each device will have a **Client ID**, **Token** (username), and **Secret** (password). These will be used in Step 2.  
6. **Modify the schema** for `Cucumber` to store incoming data via MQTT:  
   - Click **Schema**, then select **Edit Code**.  
   - Modify the schema to match the following format:  
     **Note:** Use this format `{ "data" : {"sensor1": value, "sensor2": value, ...} }` so that messages can be published to **@shadow/data/update**.  
     ```json
     {
       "additionalProperties": false,
       "properties": {
         "temp": {
           "operation": {
             "store": {
               "ttl": "30d"
             }
           },
           "type": "number"
         },
         "humid": {
           "operation": {
             "store": {
               "ttl": "30d"
             }
           },
           "type": "number"
         }
       }
     }
     ```  
   - **Save** the schema.  

---

### **Step 2: Use MQTT Explorer**  
1. **Install MQTT Explorer**  
   - Download and install [MQTT Explorer](http://mqtt-explorer.com/).  
2. **Create a new connection** in MQTT Explorer:  
   - **Username**: Copy the **Token** from the `Cucumber` device.  
   - **Password**: Copy the **Secret** from the `Cucumber` device.  
   - **Client ID**: Copy from the `Cucumber` device.  
   - **Subscribe to the topic**: `@shadow/data/update`.  
   - Click **SAVE** and **CONNECT**.  
3. **Verify connection**:  
   - Go to the NETPIE web interface, find the `Cucumber` device, and check for an **active connection**.  
4. **Publish test data**:  
   - In MQTT Explorer, go to the **Publish Pane**.  
   - Set **Topic**: `@shadow/data/update`.  
   - Select **JSON** format and enter:  
     ```json
     {
       "data": {
         "temp": 25,
         "humid": 60
       }
     }
     ```  
   - Click **PUBLISH**.  
   - Verify the values appear in NETPIE’s **Shadow Values**.  
5. **Test with multiple values**:  
   - Publish different `temp` and `humid` values.  
   - Click the **FEED** button in NETPIE to visualize data trends.  

---

### **Step 3: Arduino IDE - Cucumber Board Integration**  
1. **Sensors on the Cucumber Board**:  
   - **HTS221** (or **SHT4x**) for temperature & humidity  
   - **BMP280** for air pressure & temperature  
   - **MPU6050** for acceleration & angular speed  
2. **Configure the I2C pins on the Cucumber Board**  
   - Ensure the board can interact with onboard sensors.  
   - **Hint:** Use `Wire.h` to assign the I2C pin number.  
3. **Check which temperature sensor is present (HTS221 or SHT4X)**  
   - Use an **I2C scanner script** (available online or in Arduino IDE examples) to detect I2C addresses.  
   - **I2C addresses:**  
     - **HTS221** → `0x5F`  
     - **SHT4x** → `0x44`  
   - You can also find the I2C addresses of **BMP280** and **MPU6050** online.  
4. **Create Thread 1: Read Sensor Values**  
   - Use `Adafruit_HTS221` and `Adafruit_BMP280` libraries.  
   - Read sensor values every **1 minute** and store them in a **FIFO Queue**.  
5. **Create Thread 2: Publish Data to NETPIE**  
   - Read from **Thread 1’s FIFO queue**.  
   - Publish the data to NETPIE’s `Cucumber` device.  
   - Use the **same Client ID, Username, and Password** from the `Cucumber` device.  
   - Modify the schema in NETPIE to accommodate all sensor data.  
6. **Create Thread 3: Subscribe to Commands and Execute Actions**  
   - **Subscribe to** `@shadow/data/update`.  
   - When receiving a command (e.g., turn on an LED, change the sampling rate), parse the JSON payload.  
   - Execute the corresponding action on the **Cucumber Board**.  
   - Send a confirmation message back to NETPIE.  

---

## **Submission Guidelines**  
- **Demo Video**: Showcase your **Cucumber Board** successfully publishing data to **NETPIE**.  

---

## **References**  
- [NETPIE Official Guide](https://netpie.io/guide)  
- [NETPIE Documentation](https://docs.netpie.io/en/)  
- [MQTT Explorer](http://mqtt-explorer.com/)  
- [Arduino PubSubClient](https://pubsubclient.knolleary.net/)  

---

**Happy Coding! 🚀**  
