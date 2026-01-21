# 🔐 SafeCrack Pro - Controlador de Cofre Digital

O **SafeCrack Pro** é um módulo de hardware desenvolvido em **SystemVerilog** que simula o comportamento de um cofre eletrônico. O sistema exige uma sequência específica de entradas para desbloquear, fornecendo feedback visual através de LEDs e penalizando tentativas incorretas com um temporizador de bloqueio.

## 👥 Membros do Projeto

* **Luiz Miguel de Oliveira Siqueira**
* **Samuel Renan Mendes Umbelino do Monte**
* **Vicente Ancelmo de Oliveira Morais**
* **Lucas Mateus dos Santos Arruda**
* **Cleyton Junior da Silca Cardoso**

## 📋 Visão Geral

O sistema opera como uma **Máquina de Estados Finitos (FSM)** que monitora 3 botões de entrada. O usuário deve inserir a senha correta (sequência de botões) para abrir o cofre.

* **Feedback de Progresso:** LEDs verdes acendem progressivamente conforme a senha é inserida corretamente.
* **Segurança:** Qualquer entrada incorreta leva ao estado de **ERRO**, bloqueando o sistema temporariamente.
* **Temporização:** Baseado em um clock de 50 MHz.
    * Tempo de Cofre Aberto: **5 segundos**.
    * Penalidade de Erro: **3 segundos**.

## ⚙️ Especificações de Hardware

### Entradas e Saídas

| Porta | Tipo | Descrição |
| :--- | :--- | :--- |
| `clk` | Input | Clock do sistema (Frequência esperada: **50 MHz**). |
| `rstn` | Input | Reset Assíncrono (Active Low). |
| `btn` | Input | Botões de entrada (Fisicamente Active Low, invertidos internamente). |
| `leds_verde` | Output | Indicam o estágio do progresso da senha. |
| `led_vermelho`| Output | Indica estado de erro/bloqueio. |

### Senha Padrão
A lógica de transição está codificada para a seguinte sequência (baseada nos índices dos botões):
1.  **Botão 0** (`3'b001`)
2.  **Botão 1** (`3'b010`)
3.  **Botão 2** (`3'b100`)

## 🧠 Lógica dos Estados (FSM)

O sistema utiliza codificação **One-Hot** para maior confiabilidade. O fluxo de operação segue a lógica abaixo:

1.  **Estado Inicial (S0):** Aguarda o primeiro dígito. (1 LED Verde aceso).
2.  **Estado S1:** Se o botão 0 for pressionado, avança. (2 LEDs Verdes acesos).
3.  **Estado S2:** Se o botão 1 for pressionado, avança. (3 LEDs Verdes acesos).
4.  **Sucesso:** Se o botão 2 for pressionado, o cofre abre por 5 segundos.
5.  **Erro:** Se qualquer botão incorreto for pressionado em qualquer etapa, o sistema bloqueia por 3 segundos (LED Vermelho aceso) e depois reinicia.

## Diagrama de estados

![alt text](<Captura de Tela 2025-12-16 às 12.23.52-1.png>)

## Detalhamento do código

Foi utilizado como base o código do projeto SafeCrack no link: https://gitlab.com/-/snippets/4909655

Alterações para adequação dos requisitos foram em:
1. **Definição dos estados:** Inserimos comentários e inserimos os nomes das variáveis que indicam erro ou sucesso.
2. **Clock:** Implementamos dois contadores de tempo para acerto ou erro, com 3 segundos no erro e 5no acerto. O código original só tinha um de 1 segundo.
3. **transition logic:** Alteramos a lógica do anterior que resetava no erro. Nesse código, esse vai para o estado erro, onde acenderá o led por 3 segundos e retornará para o estado inicial.
4. **Delay:** Para o momento que o sistema fica no clock até o tempo acabar, tanto o da parte de erro quanto o da parte de acerto.
5. **Output logic:** As saídas que são os leds acesos em cada estado.
