# 🔐 SafeCrack Pro - Controlador de Cofre Digital

O **SafeCrack Pro** é um módulo de hardware desenvolvido em **SystemVerilog** que simula o comportamento de um cofre eletrônico. O sistema exige uma sequência específica de entradas para desbloquear, fornecendo feedback visual através de LEDs e penalizando tentativas incorretas com um temporizador de bloqueio.

## 📋 Visão Geral

O sistema opera como uma **Máquina de Estados Finitos (FSM)** que monitora 3 botões de entrada. O usuário deve inserir a senha correta (sequência de botões) para abrir o cofre.

* **Feedback de Progresso:** LEDs verdes acendem progressivamente conforme a senha é inserida corretamente.
* **Segurança:** Qualquer entrada incorreta leva ao estado de **ERRO**, bloqueando o sistema temporariamente.
* **Temporização:** Baseado em um clock de 50 MHz.
    * Tempo de Cofre Aberto: **5 segundos**.
    * Penalidade de Erro: **3 segundos**.

## ⚙️ Especificações de Hardware

### Entradas e Saídas

| Porta | Tipo | Largura | Descrição |
| :--- | :--- | :---: | :--- |
| `clk` | Input | 1-bit | Clock do sistema (Frequência esperada: **50 MHz**). |
| `rstn` | Input | 1-bit | Reset Assíncrono (Active Low). |
| `btn` | Input | 3-bits | Botões de entrada (Fisicamente Active Low, invertidos internamente). |
| `leds_verde` | Output | 3-bits | Indicam o estágio do progresso da senha. |
| `led_vermelho`| Output | 1-bit | Indica estado de erro/bloqueio. |

### Senha Padrão
A lógica de transição está codificada para a seguinte sequência (baseada nos índices dos botões):
1.  **Botão 0** (`3'b001`)
2.  **Botão 1** (`3'b010`)
3.  **Botão 2** (`3'b100`)

## 🧠 Diagrama de Estados (FSM)

O sistema utiliza codificação **One-Hot** para maior confiabilidade. Abaixo está o fluxo de operação:

```mermaid
stateDiagram-v2
    [*] --> S0: Reset
    
    S0 --> S1: Botão 0 (Correto)
    S0 --> ERRO: Qualquer outro botão
    
    S1 --> S2: Botão 1 (Correto)
    S1 --> ERRO: Qualquer outro botão
    
    S2 --> SUCESSO: Botão 2 (Correto)
    S2 --> ERRO: Qualquer outro botão

    SUCESSO --> S0: Após 5 segundos
    ERRO --> S0: Após 3 segundos
    
    note right of S0: 1 LED Verde
    note right of S1: 2 LEDs Verdes
    note right of S2: 3 LEDs Verdes
    note right of SUCESSO: Cofre Aberto
    note right of ERRO: LED Vermelho (Bloqueado)


![alt text](<Captura de Tela 2025-12-16 às 12.23.52.png>)