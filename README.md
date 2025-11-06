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


