# WorkShop-5

## Diseño del controlador e imlmentación

Para el diseño de este prototipo se utilizaron principalmente 2 programas, los cuales fueron Tinkercad y Wokwi.

En Tinkercad, se implemento un primer acercamiento que consistia de 2 arduinos uno, donde hay un arduino master y un esclado, este ultimo es el que recibe los datos de la temperatura que son enviados por el TMP36 cada segundo, despues de eso, y mediante conexión serial se le envian los datos al arduino esclado, donde se verifica la temperatura, y en caso de ser mas de 30 grados, se debera activar un led. Esta es la implementación que se planteo:

![Imagen 11](Imagenes/imagen1.png)

Tambien, a continucacion se documenta el codigo que es usado en ambos Arduinos

Arduino Maestro:

![Imagen 2](Imagenes/imagen3_maestro.png)

Arduino Esclavo:

![Imagen 3](Imagenes/imagen2_esclavo.png)

De forma siguiente, se implemento el mismo circuito en el programa Wokwi, en donde si se puede utilizar un ESP32, esto es importante ya que al requerir guardar los datos en una representacion de tabla en ThingSpeak es necesario tener un modulo wifi, para poder conectarse mediante IOT a el programa ya mencionado. Dentro de este programa se tienen diferencias en el codigo, ya que el ESP32 necesita cambios en las importaciones, como se mostrara adelante, ademas de eso, se realizo una configuración con los modulos de wifi.h y thingspeak.h, esto para realizar la accion ya mencionada, de poner conectarse y enviar los datos a ThingSpeak. a continuacion se presenta el codigo actualizado del ESP32:

```
#include <WiFi.h>
#include <HTTPClient.h>

// Configuración WiFi
const char* ssid = "SSID";
const char* password = "CONTRASEÑA";

// Configuración ThingSpeak
unsigned long* channelID = "2943302"; 
const char* writeAPIKey = "39SYLBJLOTYOW7MU"; 

// Pines
#define LED_PIN 18
#define RXD2 21
#define TXD2 22

// Variables
float temperatura = 0.0;

void setup() {
  Serial.begin(115200);
  Serial2.begin(9600, SERIAL_8N1, RXD2, TXD2);

  pinMode(LED_PIN, OUTPUT);
  digitalWrite(LED_PIN, LOW);

  // Conexión WiFi
  WiFi.begin(ssid, password);
  Serial.print("Conectando a WiFi...");
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nConectado a WiFi");
}

void loop() {
  // Lectura desde Arduino esclavo
  if (Serial2.available()) {
    String data = Serial2.readStringUntil('\n');
    temperatura = data.toFloat();
    Serial.print("Temperatura recibida: ");
    Serial.println(temperatura);

    // Enviar a ThingSpeak
    if (WiFi.status() == WL_CONNECTED) {
      HTTPClient http;
      String url = server + String("?api_key=") + apiKey + "&field1=" + String(temperatura);
      
      http.begin(url);
      int httpCode = http.GET();
      if (httpCode > 0) {
        Serial.println("Datos enviados a ThingSpeak");
      } else {
        Serial.print("Error al enviar: ");
        Serial.println(httpCode);
      }
      http.end();
    } else {
      Serial.println("WiFi desconectado, reconectando...");
      WiFi.begin(ssid, password);
    }

    // Control del LED
    if (temperatura > 30.0) {
      digitalWrite(LED_PIN, HIGH);
    } else {
      digitalWrite(LED_PIN, LOW);
    }
  }

  delay(15000);  // Delay para cumplir con el límite de 15s de ThingSpeak
}
```

Se puede ver que este codigo tiene diferencias en el tema de utilizar las conexiones Wifi para poder tener contacto con la tabla que montamos en ThingSoeak, ademas de eso, se hace el mismo control que el arduino maestro del punto anterior.

##IoT Deployment

PAra el desarrollo de la parte de Iot del proyecto, primeramente se creo una cuenta en la pagina ThiingSpeak, donde se permite realizar monitoreos de tipos de conexiones, para esto se tiene que crear un nuevo canal, como lo llaman ellos, este canal tiene unos datos importantes que se pudieron ver en el codigo presentado arriba, los cuales son:

```
unsigned long* channelID = "2943302"; 
const char* writeAPIKey = "39SYLBJLOTYOW7MU";

```

Esto es la forma que se tiene para conectarse al canal, el cual monitera las variables de la temperatura, demostrada en una tabla con relacion al tiempo y un "led" donde al momento de sobre pasar la temperatura se enciende, igual que el led fisico que se tiene. Ademas de esto, en el codigo se tuvo que utilizar la libreria de Wifi, ya que es la que permite utilizar el modulo de conexion y poder conectarse, para esto, ambos dispositivos, tanto el ESP 32 y el computador donde se esta corriendo el monitoreo en ThingSpeak tienen que estar en la misma red Wifi, que para este proyecto en especifico, fue un Hostpot de uno de los celulared del grupo.

## Explicación del codigo

