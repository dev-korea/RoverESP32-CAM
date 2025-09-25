ESP32-CAM Robot Car (Unipampa)

Projeto de carrinho robô controlado diretamente pelo ESP32-CAM via página web servida pelo próprio módulo (SoftAP). A interface web exibe o streaming de vídeo (MJPEG) da câmera e um D-pad para direção do carrinho. Também há um botão “Tirar foto” que baixa um JPEG capturado no momento.

Hardware validado com ESP32-CAM AI-Thinker + ponte H L9110S + 2 motores DC.

✨ Funcionalidades

Wi-Fi SoftAP: o ESP32-CAM cria sua própria rede (SSID configurável).

UI Web responsiva (porta 80): vídeo centralizado em cima e controles embaixo.

Streaming MJPEG (porta 81): /stream, incorporado na página via <img>.

Captura de foto (porta 80): /photo baixa foto.jpg.

Direção imediata sem PWM (robusto): frente, ré, esquerda, direita e parar.

Compatível com desktop e celular (toque, mouse e teclado WASD/setas).

🧰 Hardware

ESP32-CAM (AI-Thinker) — PSRAM habilitada; partição Huge APP.

Ponte H L9110S (não possui ENA/ENB; cada motor usa IA/IB).

Alimentação

ESP32-CAM: 5 V ≥ 1 A (recomendado buck 5 V/2 A) + capacitores de desacoplamento (≥470 µF + 100 nF).

Motores: fonte/bateria dedicada (ex.: 2S Li-ion 6–8 V).

GND comum entre ESP e ponte H.

Fiação (padrão do firmware):

Motor A (esquerdo): A-IA = GPIO13, A-IB = GPIO15

Motor B (direito): B-IA = GPIO14, B-IB = GPIO2

⚠️ GPIO2 é pino de boot: evite mantê-lo baixo no reset. Se necessário, use pull-up 10 k ou remapeie para outro GPIO “seguro”.

Dica: inverter sentido = trocar IA↔IB no software ou inverter os fios do motor.

🧪 Endpoints

Porta 80 (WebServer)

/ – página HTML (vídeo + D-pad + botão Tirar foto).

/cmd?c=F|B|L|R|S – comandos de direção (Frente, Ré, Esquerda, Direita, Stop).

/photo – captura e download de um JPEG.

Porta 81 (esp_http_server)

/stream – MJPEG (multipart/x-mixed-replace).

🛠️ Build & Upload

Placa: ESP32 Wrover Module

Partition Scheme: Huge APP

PSRAM: Enabled

IDE: Arduino IDE 2.x (ou PlatformIO)

Bibliotecas: já inclusas com o core ESP32 (esp_camera, esp_http_server, WiFi, WebServer).

Após fazer upload, conecte ao SSID (padrão): TESTE-L9110S (senha 12345678)
Abra o navegador em http://192.168.4.1/.

🚗 Controles

Clique/Toque: mantenha pressionado para movimento contínuo; solte para parar.

Teclado (desktop): W/A/S/D ou setas.

Botão “Tirar foto”: baixa foto.jpg sem sair da página.

🔧 Configuração rápida

Edite no código:

const char* AP_SSID = "TESTE-L9110S";
const char* AP_PASS = "12345678";

// Pinos L9110S
const uint8_t A_IA = 13, A_IB = 15;
const uint8_t B_IA = 14, B_IB = 2;
const bool INVERT_A = false, INVERT_B = false;


Para reduzir uso de energia ou aumentar FPS, ajuste em initCamera():

frame_size (ex.: FRAMESIZE_QQVGA para menor resolução)

jpeg_quality (número maior = mais compressão, menos banda/CPU).

🧯 Solução de Problemas

Câmera não inicia / stream cai → quase sempre queda de tensão.

Use fonte 5 V ≥ 1 A; adicione capacitores; evite LED flash (GPIO4) durante stream.

Boot travando → GPIO2 baixo no reset.

Adicione pull-up 10 k em GPIO2 ou mude o pino no firmware.

Motores não giram

Verifique GND comum; meça 3,3 V nos pinos IA/IB ao acionar; confira Vcc da ponte H; inverta fios se direção estiver trocada.

📦 Estrutura básica do firmware

SoftAP na porta 80 entrega a UI responsiva e recebe comandos /cmd.

esp_http_server na porta 81 envia MJPEG contínuo (/stream).

Captura de foto disponível na porta 80 em /photo (evita CORS).

Motores controlados por digitalWrite (sem PWM) para máxima simplicidade e robustez.

🗺️ Roadmap (ideias futuras)

Slider de velocidade com PWM (LEDC).

Botão lanterna (GPIO4) on/off na UI.

Seleção dinâmica de resolução/qualidade do stream.

Failsafe: parar motores se não houver comando por X ms.

Logs/telemetria (tensão, RSSI) no rodapé da UI.

📄 Licença

Defina a licença do repositório (ex.: MIT, BSD-3-Clause, GPLv3). Exemplo:

MIT License — Copyright (c) 2025

👥 Créditos

Projeto acadêmico Unipampa — integração e adaptação de controle via ESP32-CAM com streaming MJPEG e ponte H L9110S. Contribuições são bem-vindas!
