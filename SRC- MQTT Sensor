#include <SPI.h>
#include <UIPEthernet.h>
#include <PubSubClient.h>

// Function prototypes
void subscribeReceive(char* topic, byte* payload, unsigned int length);
 
// Set your MAC address and IP address here
byte mac[] = { 0xDE, 0xAD, 0xBE, 0xEF, 0xFE, 0xED };
IPAddress ip(192, 168, 1, 149);
 
// Make sure to leave out the http and slashes!
const char* server = "test.mosquitto.org";
 
// Ethernet and MQTT related objects
EthernetClient ethClient;
PubSubClient mqttClient(ethClient);

/* Acionamento Bomba de Ar  */
  const int pwm = 3;
  const int pot = A0;
  int ValorLidoPot_Real = 0;
  int ValorLidoPot_Mapeado = 0;

/* Lendo valores do Sensor de Pressão  */
  const int sensorPressure = A2;  

/* Variaveis do Rele */
  const int pino_rele = 7; // pino onde o rele está conectado
  int estado_rele = 0; // variável para armazenar o estado do rele

void setup()
{
  // Useful for debugging purposes
  Serial.begin(9600);

   /* Aciona Bomba de Ar */
  pinMode(pwm, OUTPUT);
  pinMode(pot, INPUT);

  /* Lendo Sensor de Pressão */
  pinMode(sensorPressure, INPUT);
  pinMode(pino_rele, OUTPUT); // configura o pino com o rele como saída

  
  // Start the ethernet connection
  Ethernet.begin(mac, ip);              
  
  // Ethernet takes some time to boot!
  delay(3000);                          
 
  // Set the MQTT server to the server stated above ^
  mqttClient.setServer(server, 1883);   
 
  // Attempt to connect to the server with the ID "myClientID"
  if (mqttClient.connect("myClientJoni")) 
  {
    Serial.println("Connection has been established, well done");
 
    // Establish the subscribe event
    mqttClient.setCallback(subscribeReceive);
  } 
  else 
  {
    Serial.println("Looks like the server connection failed...");
  }
}

void loop()
{
  // This is needed at the top of the loop!
  mqttClient.loop();
 
  // Ensure that we are subscribed to the topic "MakerIOTopic"
  mqttClient.subscribe("Joni_ED_Topic");
 
  // Attempt to publish a value to the topic "MakerIOTopic"
  if(mqttClient.publish("Joni_ED_Topic", "Hello 1234"))
  {
    Serial.println("Publish message success");
  }
  else
  {
    Serial.println("Could not send message :(");
  }

  
  // Dont overload the server!
  delay(4000);

  
  /* Acionando a Bomba de Ar */
  ValorLidoPot_Real = analogRead(pot);
  ValorLidoPot_Mapeado = map(ValorLidoPot_Real, 0, 1023, 0, 255);
  analogWrite(pwm, ValorLidoPot_Mapeado);  
  digitalWrite(pino_rele, estado_rele); // atualiza o estado do rele
  digitalWrite(pino_rele, 1);
 
}

void subscribeReceive(char* topic, byte* payload, unsigned int length)
{
  // Print the topic
  Serial.print("Topic: ");
  Serial.println(topic);
 
  // Print the message
  Serial.print("Message: ");
  for(int i = 0; i < length; i ++)
  {
    Serial.print(char(payload[i]));
  }
 
  // Print a newline
  Serial.println("");
}
