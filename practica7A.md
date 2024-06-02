# Pràctica 7

## Practica 7A: Busos de Comunicació I2S

### Objectiu

L'objectiu d'aquesta pràctica és comprendre el funcionament del bus I2S i realitzar una pràctica per reproduir àudio des de la memòria interna de l'ESP32 utilitzant el protocol I2S per generar dades de so digital sense pèrdua de qualitat. 
Utilitzarem la placa de connexió d'àudio MAX98357 I2S per a la conversió de la senyal digital en una senyal analògica.

### Codi

```cpp
#include "AudioGeneratorAAC.h"  //llibreria per generar àudio des de fitxers AAC
#include "AudioOutputI2S.h"     //llibreria per gestionar la sortida d'àudio via I2S
#include "AudioFileSourcePROGMEM.h" //llibreria per obtenir fitxers d'àudio des de la memòria interna (PROGMEM)
#include "sampleaac.h"     //dades d'àudio en format AAC emmagatzemades com una matriu a la memòria del programa

AudioFileSourcePROGMEM *in;   //punter per a la font d'àudio des de PROGMEM
AudioGeneratorAAC *aac;      //per al generador d'àudio AAC
AudioOutputI2S *out;        //i per a la sortida d'àudio I2S

void setup(){ 
    Serial.begin(115200); 
    in = new AudioFileSourcePROGMEM(sampleaac, sizeof(sampleaac)); //Inicialitza la font d'àudio amb les dades AAC des de PROGMEM
    aac = new AudioGeneratorAAC(); 
    out = new AudioOutputI2S();    
    out->SetGain(0.125);           //Configura el guany de la sortida d'àudio
    out->SetPinout(26, 25, 22);    //Configura els pins I2S
    aac->begin(in, out);           //Comença la generació d'àudio
} 

void loop(){ 
    if (aac->isRunning()) { //Comprova si el generador d'àudio està en funcionament
        aac->loop();        //Si ho està, continua la reproducció d'àudio
    } else { 
        aac->stop();        // Si ha acabat la reproducció, atura el generador d'àudio
        Serial.printf("Sound Generator\n");  //missatge indicant que ha finalitzat
        delay(1000);        //Espera 1 segon abans de tornar a intentar reproduir
    } 
}
```

### Sortida pel port sèrie

Durant la reproducció de l'àudio, no hi haurà cap sortida específica al port sèrie relacionada amb la reproducció contínua.

Quan la reproducció de l'àudio acaba, apareixerà el missatge: Sound Generator
Aquest missatge es mostrarà cada segon després que l'àudio hagi acabat de reproduir-se, indicant que el generador de so ha finalitzat la reproducció.

### Funcionament del programa
Aquest codi permet reproduir un fitxer d'àudio AAC emmagatzemat a la memòria interna de l'ESP32 utilitzant el protocol I2S per enviar el senyal d'àudio a la placa de connexió d'àudio MAX98357, que el converteix en una senyal analògica per a la reproducció d'àudio.