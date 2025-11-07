# Sensor de velocidade de bola para treinos de futebol - foco no projeto do Passa a Bola

> O documento a seguir trata-se de um guia completo para replicar e entender o projeto IoT que mede velocidade de uma bola de treino de futebol, utilizando um ESP32, sensores físicos e trasmissão de dados via MQTT para o Fiware

## Problema e proposta
  Em cenários atuais, os treinos de futebol, tanto técnicos quanto preparatórios, precisam medir a intensidade de chutes e passes. Os sistemas profissionais, em sua maioria, são caros, fechados e não integráveis.
  
  A proposta consiste em construir um sensor de velocidade acessível, com hardware de fácil acesso, capaz de funcionar em campos e integrar dados em tempo real a uma plataforma Fiware.

---

## Medindo velocidade da bola
Dentro da solução, há 2 sensores (IR) posicionados lado a lado, cada um separado por um metro.

Quando a bola passa pelo 1º sensor, ocorre o registro t1. O mesmo acontece com o segundo sensor, este que registra o t2

Velocidade (m/s) = 1 / (t2 - t1)
Conversão para km/h: km/h = m/s * 3,6

---
## Arquitetura do sistema 
> Observação: o ESP32 não envia HTTP, ele, na verdade, publica MQTT

<img width="580" height="507" alt="image" src="https://github.com/user-attachments/assets/41b7adeb-be5d-44d5-8842-3dc7954f99a5" />

## O que foi necessário
- ESP32 DevKit V1 (principal microcontrolador)
- Sensores IR ou quebra-feixe
- Power bank (alimentação do ESP32 em campo)
- Caixa de suporte (proteção do circuito)
- Dispositivo para rodar Mosquitto e Fiware via Docker
- Postman (consultar Fiware)

## Softwares necessários
- Mosquitto (broker MQTT)
- Docker
- Fiware IoT Agent (MQTT)
- Orion Context Broker
- Postman

## Configuração do Broker
> Arquivo já disponível neste repositório.

Para subir o broker: 
```
docker compose up -d
```

## Código do ESP32
> Sketch disponível nesse repositório /fiware/esp32_sensor.ino

O que o código faz:
- Conexão wifi
- Conexão ao broker MQTT
- Calculo de velocidade
- Publicação JSON em MQTT

## Integração com Fiware
Foi utilizado o IoT Agent para transformar MQTT em entidade do Orion 
`Postman request: http://localhost:4041/iot/devices`

## O que é esperado 

Se tudo funcionar, teremos o seguinte resultado no Orion:
```
 {
"id": "BallSensor:ball_sensor_01",
"type": "BallSensor",
"speed": {
"value": 31.2,
"type": "Number"
}
}
```
---
## Teste wokwi
Caso não tenha os materiais necessário, é possível verificar a funcionalidade desse projeto utilizando o wokwi, esse que oferece IR receiver, o qual trata-se de um dispositivo diferente daquele apresentado neste projeto, mas que traz resoltados semelhantes e é ótimo para verificar se a implementação funciona. Segue o diagrama:

<img width="478" height="447" alt="image" src="https://github.com/user-attachments/assets/aadacfce-f9c7-4feb-80bb-f4c28c14ff5e" />

Para realizar o teste sem o uso dos softwares aqui apresentados, mude as configurações do código esp32 (já mencionado e disponibilizado nesse repositório) para:
```
const char* ssid = "Wokwi-GUEST";
const char* password = "";
const char* mqtt_server = "test.mosquitto.org"; // (container Docker)
const int mqtt_port = 1883;
const char* deviceId = "ball_sensor_01";
const float distance_m = 1.0; // distância entre sensores em metros
```
Resultado esperado:

<img width="638" height="536" alt="image" src="https://github.com/user-attachments/assets/afe71e1a-cd3c-4f35-bab1-4ce7173db6bd" />


Segue o vídeo do hands-on dessa solução:
https://youtu.be/6bc-2mrQBro?si=5WRSFUv1csO4RLw1

### 6. Equipe
Raphael Aaron - 564067;
Felipe Catto - 562106;
Kimberly Kristina - 564080;
Laura Dantas - 564064.

