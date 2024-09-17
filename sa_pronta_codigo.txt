// FAZER TÓPICO academia/contador para o gráfico
// FAZER O JSON PARA ENVIAR academia/sensir
// FAZER COM QUE O TAGO ENVIE DADOS PARA O RELE



//Envio de Dados para tagoIO via MQTT

#include <ArduinoJson.h>
#include "EspMQTTClient.h"

// Sensor RFID
#include <SPI.h>
#include <MFRC522.h>
#define SS_PIN  5  
#define RST_PIN 17 
MFRC522 rfid(SS_PIN, RST_PIN);
char hex_string[3];

// Relé
int rele = 4;

// LED'S
int pino_led_vermelho = 26;
int pino_led_verde = 27;
const int pino_sensorIr = 25;

//variáveis para Json
char json_sensrfid[100];
char json_atdrele[100];
char json_sensir[100];
char json_contador[100];

// Variáveis internas RFID
String id = "";
String textoArmazenarId = "";

// Variáveis internas Relé
String textoStatusRele = "";

// Variáveis internas IR
String textoStatusIr = "";

// Variáveis Internas Contador
int cont = 0;

//configurações da conexão MQTT
EspMQTTClient client
(
  "FIESC_IOT", //nome da sua rede Wi-Fi
  "C6qnM4ag81", //senha da sua rede Wi-Fi
  "mqtt-dashboard.com",
  "",
  "", 
  "equipe_sa"            
);

//EspMQTTClient client("FIESC_IOT", "C6qnM4ag81", "mqtt.tago.io", "Token", "a69fcc1e-31f3-4d0c-9fbe-cf23c920ec6b", "TestClient", 1883 );

//configuração dos pinos
void setup()
{
  Serial.begin(115200);
  //configurações dos pinos
  // pinMode(pino_led, OUTPUT); 

  // pinMode(pino_botao, INPUT); //configura o pino como entrada
  // digitalWrite(pino_botao, HIGH); //ativa pull-up no pino de entrada

  // Sensor RFID
  SPI.begin(); // init SPI bus
  rfid.PCD_Init(); // init MFRC522
  Serial.println("Sensor RFID Inicializado Com Sucesso!!!");

  // Relé
  pinMode(rele, OUTPUT);

  // LED'S
  pinMode(pino_led_verde, OUTPUT);
  pinMode(pino_led_vermelho, OUTPUT);

  // Sensor IR
  pinMode(pino_sensorIr, INPUT);


      digitalWrite(pino_led_verde, LOW);
      digitalWrite(pino_led_vermelho, HIGH);
      digitalWrite(rele, HIGH);

}



void leitura_sinais()
{  
  



 if (rfid.PICC_IsNewCardPresent()) { // new tag is available
    if (rfid.PICC_ReadCardSerial()) { // NUID has been readed
      MFRC522::PICC_Type piccType = rfid.PICC_GetType(rfid.uid.sak);
      Serial.print("RFID Tag Type: ");
      Serial.println(rfid.PICC_GetTypeName(piccType));
      // print UID in Serial Monitor in the hex format
      Serial.print("UID:");

      id = "";
      for (int i = 0; i < rfid.uid.size; i++) {
        if(rfid.uid.uidByte[i] < 0x10)
        {
          sprintf(hex_string, " 0%X", rfid.uid.uidByte[i]); //convert number to hex
        }else{
          sprintf(hex_string, " %X", rfid.uid.uidByte[i]); //convert number to hex
        }
        id += hex_string;
        Serial.print(rfid.uid.uidByte[i] < 0x10 ? " 0" : " ");
        Serial.print(rfid.uid.uidByte[i], HEX);
      }
      Serial.println();
      // Serial.println(id);
      if(id == " 09 3D AD 98"){
        
        textoArmazenarId = "Acesso liberado!";
        textoStatusRele = "Porta Aberta!";
        Serial.println("==================");
        Serial.println(textoArmazenarId);
        Serial.println("==================");
        Serial.println(textoStatusRele);
        Serial.println("==================");
        digitalWrite(pino_led_verde, HIGH);
        digitalWrite(pino_led_vermelho, LOW);
        digitalWrite(rele, LOW);
        cont += 1;
        
      }else if(id == " 99 D1 18 98"){

        textoArmazenarId = "Acesso liberado!";
        textoStatusRele = "Porta Aberta!";
        Serial.println("==================");
        Serial.println(textoArmazenarId);
        Serial.println("==================");
        Serial.println(textoStatusRele);
        Serial.println("==================");
        digitalWrite(pino_led_verde, HIGH);
        digitalWrite(pino_led_vermelho, LOW);
        digitalWrite(rele, LOW);
        cont += 1;
      }
      else{

        textoArmazenarId = "Acesso negado!";
        textoStatusRele = "Porta Fechada!";
        
        Serial.println("==================");
        Serial.println(textoArmazenarId);
        Serial.println("==================");
        Serial.println(textoStatusRele);
        Serial.println("==================");
        digitalWrite(pino_led_verde, LOW);
        digitalWrite(pino_led_vermelho, HIGH);
        digitalWrite(rele, HIGH);
        // Serial.println("RELÉ DESLIGADO");
        // Código da Inválida = 95 2F 34 5F

      }

              

        // Serial.println("passou diretooooooooooooooo");
      rfid.PICC_HaltA(); // halt PICC
      rfid.PCD_StopCrypto1(); // stop encryption on PCD



    }
  }
  verificarSensorIr();

}

void verificarSensorIr()
{
        if(digitalRead(pino_sensorIr) == LOW){
    
          digitalWrite(pino_led_verde, LOW);
          digitalWrite(pino_led_vermelho, HIGH);
          digitalWrite(rele, HIGH);
          textoStatusIr = "Passou!!!";
          Serial.println("Passou!!");
        textoStatusRele = "Porta Fechando!";
        textoArmazenarId = "";
        }else{
          Serial.println("Não Passou!!");
          textoStatusIr = "Não Passou!!!";
          
        }
}

void converte_json()
{

  StaticJsonDocument<100>  sjson_sensrfid;

    sjson_sensrfid["variable"] = "sensrfid";
    sjson_sensrfid["value"] = textoArmazenarId;
    serializeJson(sjson_sensrfid, json_sensrfid);

  StaticJsonDocument<100>  sjson_atdrele;

    sjson_atdrele["variable"] = "atdrele";
    sjson_atdrele["value"] = textoStatusRele;
    serializeJson(sjson_atdrele, json_atdrele);  

  StaticJsonDocument<100>  sjson_sensir;

    sjson_sensir["variable"] = "sensir";
    sjson_sensir["value"] = textoStatusIr;
    serializeJson(sjson_sensir, json_sensir); 

  StaticJsonDocument<100>  sjson_contador;

    sjson_contador["variable"] = "contador";
    sjson_contador["value"] = cont;
    serializeJson(sjson_contador, json_contador); 

}

void envia_msg()
{
  client.publish("academia/rfid", json_sensrfid); 
  client.publish("academia/rele", json_atdrele);
  client.publish("academia/ir", json_sensir);
  client.publish("academia/cont", json_contador);
}

//loop do programa
void loop()
{
  leitura_sinais();
  converte_json();
  envia_msg();

  delay(1000);

  client.loop();
}

void processa_msg(const String payload)
{
  StaticJsonDocument<300> msg;

  DeserializationError err = deserializeJson(msg, payload);
  if (err) {
    Serial.print(F("deserializeJson() failed with code "));
    Serial.println(err.f_str());
  }

  Serial.print("var:");  
  String var = msg["variable"];
  Serial.println(var);
  if(var == "status")
  {
    Serial.print("value:");
    String val = msg["value"];
    Serial.println(val);
    if(val == "false"){
      digitalWrite(rele, HIGH);
      Serial.println("RELÉ DESLIGADO!!");
    }else{
      digitalWrite(rele, LOW);
      Serial.println("RELÉ ACIONADO");
    }
    
  }
  if(var == "statusled")
  {
    Serial.print("value:");
    String val = msg["value"]; 
    Serial.println(val);
    if(val == "false"){
      digitalWrite(pino_led_vermelho, LOW);
      digitalWrite(pino_led_verde, HIGH);
      Serial.println("Led DESLIGADO");
      delay(3000);
    }else{

      digitalWrite(pino_led_vermelho, HIGH);
      digitalWrite(pino_led_verde, LOW);
      Serial.println("Led LIGADO");
      delay(3000);
      
    }
    
  }
 
}

// This function is called once everything is connected (Wifi and MQTT)
// WARNING : YOU MUST IMPLEMENT IT IF YOU USE EspMQTTClient
void onConnectionEstablished()
{
   client.subscribe("academia/atuadormanual", [] (const String &payload)  {
   Serial.println(payload);
   processa_msg(payload);
  });
}
