# ESP8266-WI-FI-Module-Control-LED-On-OFF-Using-a-Mobile-switch
This project demonstrates how to contol an LED wirelessly using the ESP8266 WI-FI module and a mobile device.by connecting the ESP8266 to a wifi network a simple web interface or mobile switch is created to turn the LED on/off Remotely
code used for project
#include <ESP8266WiFi.h>

// Replace with your network credentials
const char* ssid = "mani";
const char* password = "1234567890";

// Set the GPIO pin where the LED is connected
const int ledPin = 2; // Built-in LED on NodeMCU is D4 (GPIO2)

// Create a Wi-Fi server on port 80
WiFiServer server(80);

void setup() {
  Serial.begin(115200);
  pinMode(ledPin, OUTPUT);
  digitalWrite(ledPin, LOW); // LED initially OFF

  // Connect to Wi-Fi network
  Serial.print("Connecting to ");
  Serial.println(ssid);
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.print(".");
  }

  Serial.println("");
  Serial.println("WiFi connected!");
  Serial.print("IP address: ");
  Serial.println(WiFi.localIP()); // Display IP to connect from mobile

  server.begin();
}

void loop() {
  WiFiClient client = server.available(); // Listen for incoming clients

  if (!client) return;

  Serial.println("New Client Connected.");
  String request = client.readStringUntil('\r');
  Serial.println(request);
  client.flush();

  // Check the request for LED ON or OFF
  if (request.indexOf("/LED=ON") != -1) {
    digitalWrite(ledPin, HIGH);
  } 
  if (request.indexOf("/LED=OFF") != -1) {
    digitalWrite(ledPin, LOW);
  }

  // Send web page to client
  client.println("HTTP/1.1 200 OK");
  client.println("Content-Type: text/html");
  client.println("");
  client.println("<!DOCTYPE HTML>");
  client.println("<html>");
  client.println("<h1>ESP8266 LED Control</h1>");
  client.println("<p><a href=\"/LED=ON\"><button>LED ON</button></a></p>");
  client.println("<p><a href=\"/LED=OFF\"><button>LED OFF</button></a></p>");
  client.println("</html>");
  delay(1);
  Serial.println("Client Disconnected.");
}
