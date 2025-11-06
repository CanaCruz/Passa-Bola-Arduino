⚽ Passa a Bola — Monitoramento de Saúde (ESP32 + MQTT + Node-RED)

Sistema IoT desenvolvido como prova de conceito (PoC) para monitorar em tempo real sinais vitais de atletas durante uma partida de futebol.
O projeto conecta um ESP32 a sensores simulados de temperatura (DHT22), frequência cardíaca (BPM) e oxigenação sanguínea (SpO₂).
Os dados são enviados via MQTT para o Node-RED, que exibe um dashboard web com gauges, gráfico temporal e status automático (OK / ALERTA / CRÍTICO).
O sistema também permite o envio de comandos do dashboard para o hardware, como acionar o LED verde via MQTT.

👥 Integrantes

Arthur Canaverde da Cruz — RM: 563029
Lucas Costa Zago — RM: 562028

⚙️ Detalhes da Implementação
Microcontrolador: ESP32 DevKit V1
Sensores: DHT22 (temperatura/umidade) + simulação de BPM e SpO₂ em pinos analógicos
Comunicação: MQTT (test.mosquitto.org:1883)
Dashboard: Node-RED + node-red-dashboard
Tópicos MQTT
sensor/dht/Arthur → envio de dados JSON {Temperatura, BPM, SpO2, status, ts}
sensor/dht/status/Arthur → status online/offline (LWT)
led/control/Arthur → controle remoto de LED (1 = liga / 0 = desliga)

🧪 Resultados da PoC
Durante os testes:
O dashboard exibiu atualização em tempo real dos sinais vitais enviados pelo ESP32.
O status alterava automaticamente conforme limites fisiológicos:
OK → dentro da faixa normal
ALERTA → leve desvio
CRÍTICO → valor extremo ou perda de sinal
O comando LED funcionou corretamente via Node-RED, acionando o pino digital correspondente.
O sistema manteve conexão estável com o broker público Mosquitto.

🌐 Integração IoT com o Site
A PoC também simulou a integração do dashboard com o site Passa a Bola, onde os dados poderiam ser exibidos no painel dos atletas em tempo real (via MQTT WebSocket ou API).
Prints de integração demonstram a comunicação entre o ESP32 → Broker MQTT → Node-RED → Interface Web.
Fluxo	Print
Dashboard Node-RED com gauges e gráfico	
Conexão MQTT ativa	
LED controlado via dashboard	
▶️ Como Executar
No Wokwi
Adicione as libs: Adafruit GFX, Adafruit SSD1306, DHT sensor library, PubSubClient
Clique em Run e permita Internet
Alterar MQTT_HOST no sketch, se usar outro broker

🔗 Projeto Wokwi:
https://wokwi.com/projects/442277443782837249
No Node-RED
Instale o dashboard:
npm i node-red-dashboard
Importe o arquivo node-red/flow.json
Configure o nó MQTT com o broker e porta
Acesse http://localhost:1880/ui

🎥 Demonstração em Vídeo
🔗 YouTube: https://youtu.be/Doma4EKaZ-8

🧩 Estrutura de Pastas e Replicabilidade
Passa-Bola-Arduino/
├── esp32_passabola.ino          # Código do microcontrolador (Arduino IDE)
├── node-red/
│   └── flow.json                # Fluxo completo do Node-RED
├── prints/                      # Evidências e capturas de tela
│   ├── dashboard.png
│   ├── mqtt.png
│   └── led.png
├── README.md                    # Documentação do projeto
└── docs/
    └── guia_execucao.md         # Passo a passo detalhado de instalação


🔁 Replicabilidade:
Basta clonar o repositório, importar o fluxo Node-RED, e rodar o sketch do ESP32 no Wokwi ou Arduino IDE. O sistema reconecta automaticamente ao broker MQTT e reproduz o mesmo comportamento da PoC.

⚠️ Aviso
Projeto educacional, com sensores simulados.
Não se destina ao uso médico.
