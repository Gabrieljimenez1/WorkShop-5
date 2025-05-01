# WorkShop-5

## Diseño del controlador e imlmentación

Para el diseño de este prototipo se utilizaron principalmente 2 programas, los cuales fueron Tinkercad y Wokwi.

En Tinkercad, se implemento un primer acercamiento que consistia de 2 arduinos uno, donde hay un arduino master y un esclado, este ultimo es el que recibe los datos de la temperatura que son enviados por el TMP36 cada segundo, despues de eso, y mediante conexión serial se le envian los datos al arduino esclado, donde se verifica la temperatura, y en caso de ser mas de 30 grados, se debera activar un led. Esta es la implementación que se planteo:

Imagen 1

Tambien, a continucacion se documenta el codigo que es usado en ambos Arduinos

Arduino Maestro:

Imagen 3

Arduino Esclavo:

Imagen 2

De forma siguiente, se implemento el mismo circuito en el programa Wokwi, en donde si se puede utilizar un ESP32, esto es importante ya que al requerir guardar los datos en una representacion de tabla en ThingSpeak es necesario tener un modulo wifi, para poder conectarse mediante IOT a el programa ya mencionado. Dentro de este programa se tienen diferencias en el codigo, ya que el ESP32 necesita cambios en las importaciones, como se mostrara adelante, ademas de eso, se realizo una configuración con los modulos de wifi.h y thingspeak.h, esto para realizar la accion ya mencionada, de poner conectarse y enviar los datos a ThingSpeak. a continuacion se presenta el codigo actualizado del ESP32:

