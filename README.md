# dRehmFlight-ESP32

Port para **ESP32** do [dRehmFlight](https://github.com/nickrehm/dRehmFlight), o
flight controller aberto de **Nicholas Rehm**, adaptado para o estudo de comparação de
um Trabalho de Conclusão de Curso (medição de **jitter de loop** e **vibração estrutural**
em bancada).

> ⚠️ Firmware para **bancada/estudo**, não é um firmware de voo validado. Réplica por
> conta e risco de quem montar — inclusive na parte física (solda, pinagem, hélices).

## Relação com o projeto original

- **Upstream:** [nickrehm/dRehmFlight](https://github.com/nickrehm/dRehmFlight) — Beta 1.3 (Teensy)
- **Licença:** **GPLv3**, a mesma do projeto original (ver [`dRehmFlight_ESP32/COPYING.txt`](dRehmFlight_ESP32/COPYING.txt)).
  Copyright (C) Nicholas Rehm — este repositório é uma **derivação** e mantém a GPLv3.
- Se você publicar isto como *fork* pelo GitHub, o crédito ao autor original aparece
  automaticamente na página do repositório.

## O que mudou em relação ao upstream (Teensy)

Resumo (a descrição completa está em [`LEIA-PRIMEIRO_ESP32.txt`](LEIA-PRIMEIRO_ESP32.txt)):

- Base: `dRehmFlight_Teensy_BETA_1.3`
- `PWMServo` (Teensy) → **ESP32Servo** (chamadas `.write()` mantidas)
- Receptor PWM **desativado** (os pinos padrão do RX conflitam com o I2C do MPU6050
  no ESP32) — os canais são forçados por **comando serial**
- Pinos remapeados para o ESP32 DevKit (GPIOs 6–11 são da flash; GPIO 12 é strapping)
- I2C a **400 kHz** (upstream usava 1 MHz; aqui a fiação é em protoboard)
- Instrumentação de TCC: linha **STATS** por segundo (jitter de loop + vibração) e
  medição de vibração pela IMU (`|a| − 1g`, EMA α = 0,01) — mesma definição usada
  no firmware de comparação

## Arquivos

```
dRehmFlight-ESP32/
├── LEIA-PRIMEIRO_ESP32.txt      ← documentação completa do port (pinos, comandos, STATS)
└── dRehmFlight_ESP32/
    ├── dRehmFlight_ESP32.ino    ← firmware principal
    ├── radioComm.ino            ← funções de rádio (receptor desativado)
    ├── COPYING.txt              ← GPLv3
    └── src/                     ← drivers embutidos (MPU6050/I2Cdev, MPU9250, SBUS, DSMRX, TFMPlus)
```

## Como montar (lado do software)

1. **Arduino IDE 2.x** + suporte da placa: *Boards Manager* → **"esp32 by Espressif Systems"**.
2. Biblioteca **ESP32Servo** (as demais — `Wire`, `SPI` — já vêm no core do ESP32;
   os drivers em `src/` são internos ao sketch).
3. Abra `dRehmFlight_ESP32/dRehmFlight_ESP32.ino`.
4. Placa: **ESP32 Dev Module**, escolha a porta COM → **Verificar** → **Enviar**.
5. Detalhes de pinagem, comandos seriais e saída STATS: **leia o `LEIA-PRIMEIRO_ESP32.txt`**.

## Tabela de pinos (resumo)

| Função | GPIO |
|---|---|
| ESC A (m1, dianteiro esq.) | 19 |
| ESC B (m2, dianteiro dir.) | 18 |
| ESC C (m4, trás esq.) | 33 |
| ESC D (m3, trás dir.) | 32 |
| I2C MPU6050 (SDA / SCL) | 21 / 22 |
| LED | 2 |

PWM dos ESCs: 400 Hz, 1000–2000 µs.

## Por que existe este port

Para comparar, **na mesma bancada e com as mesmas hélices**, a temporização de loop e
a vibração de duas arquiteturas de controle: o firmware próprio (Cascata) e este port
do dRehmFlight. A comparação é o capítulo de resultados do TCC.

## Créditos

- **dRehmFlight** — Nicholas Rehm ([nickrehm/dRehmFlight](https://github.com/nickrehm/dRehmFlight)), **GPLv3**
- Elementos inspirados em [Broking YMFC-32](http://www.brokking.net/ymfc-32_main.html),
  filtro Madgwick ([MadgwickAHRS](https://github.com/arduino-libraries/MadgwickAHRS)) e
  biblioteca MPU9250 de Bolder Flight — detalhes no cabeçalho de `dRehmFlight_ESP32.ino`
- Port, instrumentação e documentação: **André Prieto França Filho** (2026)
