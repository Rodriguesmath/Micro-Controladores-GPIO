
# Resumo Detalhado: GPIO no STM32F407

## 1. Introdução às Portas GPIO
Os GPIOs (**General Purpose Input/Output**) são os pinos que permitem a interação do microcontrolador STM32F407 com o mundo externo, conectando dispositivos como sensores, botões, LEDs, motores e displays.

Cada pino pode ser configurado para operar em diferentes modos, dependendo da aplicação desejada:
- **Entrada digital**: Lê estados lógicos (0 ou 1) de dispositivos externos.
- **Saída digital**: Controla dispositivos externos, fornecendo um nível lógico alto (1) ou baixo (0).
- **Função analógica**: Converte sinais analógicos para digitais ou vice-versa.
- **Função alternativa (AF)**: Permite que o pino atue como interface de comunicação (SPI, I2C, UART, PWM, etc.).

**Organização das Portas:**
- O STM32F407 possui **80 pinos** de I/O agrupados em 5 portas:
  - **GPIOA, GPIOB, GPIOC, GPIOD e GPIOE**.
  - Cada porta possui **16 pinos**, numerados de 0 a 15 (PA0 a PA15, por exemplo).

## 2. Configuração das Portas GPIO
Para configurar uma porta GPIO, o microcontrolador utiliza diversos **registradores** mapeados na memória, que permitem definir o modo de operação, tipo de saída, velocidade e resistores pull-up/pull-down.

### 2.1 Registrador MODER (Mode Register)
Define o **modo de operação** de cada pino.

| Valor Binário | Modo de Operação        |
|---------------|-------------------------|
| 0b00          | Entrada Digital          |
| 0b01          | Saída Digital            |
| 0b10          | Função Alternativa (AF)  |
| 0b11          | Função Analógica          |

**Exemplo:**  
Configurar o pino PA5 como saída digital:
```c
GPIOA->MODER |= 0b01 << 10;  // Configura o pino PA5 como saída digital
```

### 2.2 Registrador OTYPER (Output Type Register)
Define o **tipo de saída**.

| Tipo        | Descrição                                                                 |
|-------------|---------------------------------------------------------------------------|
| Push-Pull   | O pino fornece corrente para o dispositivo externo quando está em nível alto. |
| Open-Drain  | O pino apenas puxa corrente para o terra (0). Necessita resistor pull-up.    |

**Exemplo:**  
Configurar o pino PA5 como saída push-pull:
```c
GPIOA->OTYPER &= ~(1 << 5);  // Configura o pino PA5 como push-pull
```

### 2.3 Registrador OSPEEDR (Output Speed Register)
Define a **velocidade** com que o sinal de saída do pino varia.

| Valor Binário | Velocidade        |
|---------------|-------------------|
| 0b00          | Low Speed          |
| 0b01          | Medium Speed       |
| 0b10          | High Speed         |
| 0b11          | Very High Speed    |

**Exemplo:**  
Configurar o pino PA5 com alta velocidade:
```c
GPIOA->OSPEEDR |= 0b10 << 10;  // Configura alta velocidade no pino PA5
```

### 2.4 Registrador PUPDR (Pull-Up/Pull-Down Register)
Define o uso de resistores internos.

| Valor Binário | Resistor Interno          |
|---------------|---------------------------|
| 0b00          | Sem resistor               |
| 0b01          | Pull-Up (força nível alto) |
| 0b10          | Pull-Down (força nível baixo) |

**Exemplo:**  
Configurar o pino PA5 com resistor pull-up:
```c
GPIOA->PUPDR |= 0b01 << 10;  // Ativa o pull-up no pino PA5
```

## 3. Modos de Operação dos GPIO
### 3.1 Modo de Entrada Digital
Lê o estado lógico do pino usando o **registrador de dados de entrada** (IDR).

**Exemplo de Leitura:**  
```c
int estado = GPIOA->IDR & (1 << 5);  // Lê o estado do pino PA5
```

### 3.2 Modo de Saída Digital
Define o estado lógico do pino usando o **registrador de dados de saída** (ODR).

**Exemplo de Escrita:**
```c
GPIOA->ODR |= (1 << 5);  // Define o pino PA5 como nível alto
GPIOA->ODR &= ~(1 << 5); // Define o pino PA5 como nível baixo
```

### 3.3 Modo de Função Alternativa (AF)
Permite que o pino atue como interface de comunicação ou periférico.

## 4. Função Analógica
O pino opera como entrada/saída para conversão ADC (Analógico-Digital) ou DAC (Digital-Analógico).

## 5. Mecanismo de Travamento de Configuração
O **registrador LCKR** permite travar as configurações de pinos GPIO.

**Exemplo:**  
```c
GPIOA->LCKR |= (1 << 16) | (1 << 5);  // Trava a configuração do pino PA5
```

## 6. Acesso Direto vs. CMSIS
- **Acesso Direto**: Mais rápido, mas menos portátil.
- **CMSIS**: Facilita a portabilidade e manutenção do código.

**Exemplo com CMSIS:**
```c
GPIO_InitTypeDef GPIO_InitDef;
GPIO_InitDef.GPIO_Pin = GPIO_Pin_5;
GPIO_InitDef.GPIO_Mode = GPIO_Mode_OUT;
GPIO_Init(GPIOA, &GPIO_InitDef);
```

## 7. Componentes Externos Comuns
- **LEDs**: Sinalização.
- **Botões/Chaves**: Conectados às entradas.
- **Displays de 7 segmentos**: Usados para exibir dígitos alfanuméricos.
