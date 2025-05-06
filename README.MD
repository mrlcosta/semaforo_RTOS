# Documentação Técnica - Sistema de Semáforo Inteligente com FreeRTOS

## Descrição Geral

Este projeto implementa um sistema de semáforo inteligente utilizando o microcontrolador RP2040 (Raspberry Pi Pico) na placa BitDog Lab. O sistema utiliza o sistema operacional de tempo real FreeRTOS para gerenciar múltiplas tarefas concorrentes que controlam os diferentes componentes de hardware.

O semáforo possui dois modos de operação:
- **Modo Diurno (Normal)**: Ciclo completo de cores (verde → amarelo → vermelho)
- **Modo Noturno**: Amarelo piscante

Cada estado do semáforo é representado visualmente através de LEDs RGB, matriz de LED e display OLED, além de fornecer feedback sonoro específico através de um buzzer para auxiliar pessoas com deficiência visual.

## Componentes de Hardware

- Placa BitDog Lab com RP2040
- Display OLED (I2C, endereço 0x3C)
- Matriz de LED (5x5)
- LEDs RGB (verde e vermelho)
- Buzzer
- Botões de controle

## Requisitos de Software

- SDK do Raspberry Pi Pico
- FreeRTOS
- Biblioteca SSD1306 para controle do display OLED
- PIO (Programmable I/O) para controle da matriz de LED

## Configuração de Pinos

| Componente | Pino | Definição |
|------------|------|-----------|
| I2C SDA    | 14   | I2C_SDA   |
| I2C SCL    | 15   | I2C_SCL   |
| Botão A    | 5    | BOTAO_A   |
| Botão B    | 6    | BOTAO_B   |
| LED Verde  | 11   | LED_VERDE |
| LED Vermelho | 13 | LED_VERMELHO |
| Buzzer     | 21   | BUZZER_PIN |
| Matriz LED | 7    | OUT_PIN   |

## Estados do Semáforo

- **VERDE (0)**: Sinaliza "Prossiga"
- **AMARELO (1)**: Sinaliza "Atenção"
- **VERMELHO (2)**: Sinaliza "Pare"
- **NOTURNO (3)**: Modo especial com amarelo piscante

## Tarefas FreeRTOS

O sistema utiliza 5 tarefas independentes para controlar os diferentes componentes:

1. **button_task**: Monitora os botões e alterna entre os modos diurno e noturno
2. **buzzer_task**: Controla os sinais sonoros específicos para cada estado do semáforo
3. **display_task**: Atualiza o display OLED com informações sobre o estado atual
4. **led_task**: Controla os LEDs RGB e gerencia a transição de estados
5. **matriz_led_task**: Controla a matriz de LEDs para mostrar padrões visuais

## Padrões Sonoros do Buzzer

- **Verde**: 1 beep curto por segundo (duração: 1000ms)
- **Amarelo**: Beeps rápidos intermitentes (duração: 100ms)
- **Vermelho**: Tom curto contínuo (duração: 500ms ligado, 1.5s desligado)
- **Modo Noturno**: Beep lento a cada 2 segundos (duração: 300ms)

## Instruções de Uso

- **Botão A**: Alterna entre os modos diurno e noturno
- **Botão B**: Aciona o modo BOOTSEL (reinicia o dispositivo em modo de programação)

## Funcionamento

1. No **modo diurno**, o semáforo segue o ciclo tradicional:
   - Verde (4 segundos) → Amarelo (4 segundos) → Vermelho (4 segundos)
   - O display, os LEDs e a matriz LED mostram informações correspondentes a cada estado
   - O buzzer emite sons específicos para cada estado

2. No **modo noturno**:
   - Apenas o amarelo pisca lentamente
   - O display mostra "Modo noturno" e "Preste atenção"
   - O buzzer emite beeps lentos a cada 2 segundos


## Observações Importantes

- O sistema utiliza apenas tarefas do FreeRTOS sem uso de filas, semáforos ou mutexes
- A comunicação entre tarefas é feita através de variáveis globais compartilhadas
- O modo atual do sistema é controlado pela flag global `modo_diurno`
- O estado atual do semáforo é gerenciado pela variável global `estado_semaforo`