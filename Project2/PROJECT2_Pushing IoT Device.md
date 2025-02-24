# Pushing IoT Device
This is a guideline for students who enroll in 2102541 IoT Fundamental Course (academic year 2024)

Class Instructors:

- Assoc. Prof. Chaodit Aswakul (https://ee.eng.chula.ac.th/chaodit-aswakul/)
- Assoc. Prof. Wanchalerm Pora (https://ee.eng.chula.ac.th/wanchalerm-pora/)

Department of Electrical Engineering, Faculty of Engineering, Chulalongkorn University, Thailand

This repository was created by Ronakorn Saetang
Department of Electrical
Engineering, Faculty of Engineering, Chulalongkorn University, Bangkok Thailand



## Overview
The key objectives include:
- Setting up an account on **NETPIE.io**
- Creating and configuring IoT devices on NETPIE
- Using **MQTT Explorer** for data publishing and subscription
- Developing an **Arduino** program to read sensor data and push it to NETPIE

## Getting Started

### Step 1: Set Up NETPIE Account
1. **Create an account** on [NETPIE.io](https://auth.netpie.io/signup).
2. **Login** to [NETPIE 2020](https://netpie.io/guide) and follow the [official documentation](https://docs.netpie.io/en/).
3. **Create a project** inside NETPIE.
4. **Create two devices** within the project (Make sure both devices are in the **same group !** ):
   - `Cucumber` (for local monitoring & control)
   - `App` (for remote monitoring & control)
5. **Retrieve credentials**: Each device will have a `Client ID`, `Token` (username), and `Secret` (password). We'll use it later in Step 2.
6. **Modify the schema** for `Cucumber` to store incoming data via MQTT:
   - Press the **Schema** button and select **Edit Code**.
   - Modify the schema to match the following format:
   - NOTE!!!: You have to use this format { "data" : {"sensor1":value, "sensor2":value,...} } so that 
   we could publish our message to the topic **@shadow/data/update**
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
   - **SAVE** the schema.

### Step 2: Use MQTT Explorer
Before writing the actual Arduino code, we will use **MQTT Explorer** or **POSTMAN** as a proof of concept to verify communication with NETPIE. This ensures that our MQTT connection, topic subscriptions, and data publishing work correctly before integrating the real IoT device.

1. **Install MQTT Explorer**
   - Download from [mqtt-explorer.com](http://mqtt-explorer.com/) and install it.
2. **Create a new connection** in MQTT Explorer:
   - **Username**: Copy **Token** from `Cucumber` device.
   - **Password**: Copy **Secret** from `Cucumber` device.
   - **Client ID**: Copy **Client ID** from `Cucumber` device.
   - **Subscribe to topic**: `@shadow/data/update`
   - Click **SAVE** and **CONNECT**.
3. **Verify connection**:
   - Go back to NETPIE web, find the `Cucumber` device, and check for an active connection.
4. **Publish test data**:
   - In MQTT Explorer, go to the **Publish Pane**.
   - Set **Topic**: `@shadow/data/update`
   - Select **JSON** format and enter:
     ```json
     {
      "data": {
        "temp" : 25,
        "humid": 60
        }
     }
     ```
   - Click **PUBLISH**.
   - Verify the values appear in NETPIE’s **Shadow Values**.
5. **Test with multiple values**:
   - Publish different `temp` and `humid` values.
   - Click the **FEED** button in NETPIE to visualize data trends.

### Step 3: Arduino IDE - Cucumber Board Integration
1. **Sensors on Cucumber Board**:
   - **HTS221** (or **SHT4x**) for temperature & humidity
   - **BMP280** for air pressure & temperature
   - **MPU6050** for acceleration & angular speed
2. **Configure your Arduino I2C pin for cucumber board so that cucumber board could interact with on board sensors** `HINT!: Try use Wire.h to assign I2C pin number`
3. **Check that your cucumber board has HTS221 or SHT4X** `HINT!: You can use I2C scanner script from internet or example in Arduino IDE`
   - You can use I2C scanner script from internet or example in Arduino IDE, to scan for on board sensors I2C address to determine, which sensor is on your board.
   - HTS221 I2C address is **0x5F** and SHT4x I2C address is **0x44**
   - You can find BMP280 and MPU6050 I2C address from internet, if you are curious.
4. **Create Thread 1: Read sensor values and put the data at the back of a FIFO Queue**.
   - reads all the physical quantities from HTS221/SHT4x and BMP280 only with the help of **Adafruit_HTS221** and **Adafruit_BMP280** libraries every minute.
   - Put the data at the back of a FIFO Queue.  
5. **Create Thread 2: Publish to NETPIE using `pubsubclient`**:
   - Read from the **FIFO queue in thread 1**.
   - Use the same **Client ID, Username, and Password** of NETPIE’s `Cucumber` device for connection.
   - Publish the sensor data to the MQTT topic **@shadow/data/update** on NETPIE’s `Cucumber` device.
6. **Modify the schema in NETPIE to accommodate all sensor data**:
7. **Verify that sensor data appears correctly in NETPIE’s Shadow values.** 
   

## Submission Guidelines
- **Demo Video**: Showcase your Cucumber Board successfully publishing data to NETPIE.


## References
- [NETPIE Official Guide](https://netpie.io/guide)
- [NETPIE Documentation](https://docs.netpie.io/en/)
- [MQTT Explorer](http://mqtt-explorer.com/)
- [Arduino PubSubClient](https://pubsubclient.knolleary.net/)

**Happy Coding! 🚀**
