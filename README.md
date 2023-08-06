# data_sensor_to_SQL


Certainly! Here's a description of a setup involving an HDT (High Definition Temperature) sensor connected to an ESP32 microcontroller, which is then linked to a database using PHP:

1. Hardware Setup:
   - Connect the HDT sensor to the ESP32 microcontroller. The sensor measures temperature and provides analog output.
   - Wire the sensor's analog output to one of the ADC pins of the ESP32. For example, connect it to ADC0 pin.
   - Connect the ESP32 to the internet using Wi-Fi.

2. ESP32 Programming:
   - Set up the ESP32 board on the Arduino IDE or your preferred development environment.
   - Install the necessary libraries for ESP32 and Wi-Fi connectivity.
   - Write code to read the analog value from the ADC pin connected to the HDT sensor.
   - Convert the analog value to temperature using the sensor's calibration data.
   - Establish a Wi-Fi connection to your local network.
   - Set up an HTTP client on the ESP32 to send temperature data to the PHP server.

3. PHP Server Setup:
   - Set up a web server with PHP support. You can use Apache or Nginx as the web server software.
   - Install PHP and configure it to work with the web server.
   - Create a PHP script that will handle incoming requests from the ESP32 and interact with the database.
   - Establish a connection to the database from the PHP script. You can use MySQL or any other supported database system.
   - Implement API endpoints in the PHP script to receive temperature data from the ESP32 and store it in the database.

4. Database Setup:
   - Set up a database system such as MySQL or MariaDB.
   - Create a table in the database to store the temperature data. The table can have columns like timestamp and temperature value.

5. Integration:
   - Modify the ESP32 code to send an HTTP POST request to the PHP server, including the temperature data in the request body.
   - The PHP script should receive the temperature data, validate it, and then insert it into the database using appropriate SQL queries.
   - You can use prepared statements or other techniques to ensure the security and integrity of the data being inserted.
   - The PHP script can send a response back to the ESP32 indicating the success or failure of the database insertion.

By following these steps, you can create a system where an HDT sensor connected to an ESP32 microcontroller sends temperature data to a PHP server, which then stores the data in a database.

this link for yoy if you want to code php and see my data "https://github.com/Memo0302/code_php_is_Receives_data"

```
#include <WiFi.h>
#include <HTTPClient.h>

#include <DHT.h> 
#define DHTPIN 27  
#define DHTTYPE DHT11 
DHT dht11(DHTPIN, DHTTYPE); 

String URL = "http://xxxxxx/se/dht.php";// xxxx mean my ip adress

const char* ssid = "xxxxxx"; // mean my name the network
const char* password = "xxxxx"; // mean my password

int temperature = 0;
int humidity = 0;

void setup() {
  Serial.begin(115200);

  dht11.begin(); 
  
  
}

void loop() {
  if(WiFi.status() != WL_CONNECTED) {
     WiFi.mode(WIFI_OFF);
  delay(1000);
  //This line hides the viewing of ESP as wifi hotspot
  WiFi.mode(WIFI_STA);
  
  WiFi.begin(ssid, password);
  Serial.println("Connecting to WiFi");
  
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
    
  }
  Serial.println("");
  }

  temperature = dht11.readTemperature(); //Celsius
  humidity = dht11.readHumidity();
  
  // Check if any reads failed.
  if (isnan(temperature) || isnan(humidity)) {
    Serial.println("Failed to read from DHT sensor!");
    temperature = 0;
    humidity = 0;
  }
  String postData = "temperature=" + String(temperature) + "&humidity=" + String(humidity);
  
  HTTPClient http;
  http.begin(URL);
  http.addHeader("Content-Type", "application/x-www-form-urlencoded");
  
  int httpCode = http.POST(postData);
  String payload = "";

  if(httpCode > 0) {
    // file found at server
    if(httpCode == HTTP_CODE_OK) {
      String payload = http.getString();
      Serial.println(payload);
    } else {
      // HTTP header has been send and Server response header has been handled
      Serial.printf("[HTTP] GET... code: %d\n", httpCode);
    }
  } else {
    Serial.printf("[HTTP] GET... failed, error: %s\n", http.errorToString(httpCode).c_str());
  }
  
  http.end();  //Close connection

  Serial.print("Data: "); Serial.println(postData);
  Serial.print("httpCode: "); Serial.println(httpCode);
  Serial.print("payload : "); Serial.println(payload);
  Serial.println("--------------------------------------------------");
  delay(5000);
}
```

![esp_dht](https://github.com/Memo0302/data_sensor_to_SQL/assets/92684739/a1154611-8686-4764-a336-6b394ea260c2)

