# LUCAS CARVALHO DE ARAÚJO - 2685975

# 🐾 CasaPet — Compilador para Casa Automatizada de Pets

Projeto acadêmico de **Compiladores** que implementa uma linguagem de programação simples para automatizar uma casa destinada ao cuidado de pets.

O projeto utiliza **Python** e a biblioteca **Lark** para transformar programas escritos em uma linguagem específica de domínio, chamada **CasaScript**, em **bytecode** executado por uma máquina virtual baseada em pilha.

---

## 📌 Problema de mercado

No contexto de cuidados com animais domésticos, os tutores podem precisar monitorar e controlar diferentes aspectos da rotina dos pets, como alimentação, hidratação, temperatura do ambiente, presença do animal e condições da caixa de areia, especialmente quando não estão em casa. O problema abordado pelo projeto é a necessidade de transformar essas situações em **regras automáticas, claras e programáveis**, permitindo que sensores desencadeiem ações em dispositivos da residência. A proposta da CasaPet é oferecer uma linguagem simples para representar essas regras de automação, facilitando a criação de comportamentos como ligar o bebedouro quando o pet está presente, acionar o pote de ração em determinado horário, ajustar o ar-condicionado quando a temperatura estiver elevada e emitir notificações em situações específicas.

---

## 🎯 Objetivo

Desenvolver um pequeno compilador capaz de interpretar regras de automação de uma casa para pets, passando por diferentes fases do processo de compilação:

```text
Código-fonte CasaScript
        ↓
Análise Léxica
        ↓
Análise Sintática
        ↓
Construção da AST
        ↓
Análise Semântica
        ↓
Otimização
        ↓
Geração de Bytecode
        ↓
Máquina Virtual
        ↓
Execução das ações
```

---

## 🧩 Funcionalidades

O projeto implementa:

- análise léxica com tokens e posições;
- análise sintática com **LALR(1)**;
- construção de **Árvore Sintática Abstrata (AST)**;
- análise semântica com verificação de tipos, sensores, dispositivos e ações;
- identificação de valores inválidos e regras de negócio;
- otimização da AST;
- **constant folding**;
- eliminação de dupla negação;
- simplificação de identidades aritméticas;
- simplificação lógica;
- eliminação de código morto;
- geração de bytecode;
- execução do bytecode em uma máquina virtual;
- curto-circuito das expressões lógicas;
- simulação de eventos dos sensores;
- execução das ações quando ocorre uma mudança de estado;
- bateria de testes automáticos.

---

# 📒 Tabela de Símbolos

A tabela de símbolos representa os elementos disponíveis na CasaPet: **sensores, dispositivos e ações**.

## 📡 Sensores

| Nome | Categoria | Tipo |
|---|---|---|
| `hora` | Sensor | `hora` |
| `vasilha` | Sensor | `texto` |
| `caixa_areia` | Sensor | `texto` |
| `presenca` | Sensor | `logico` |
| `temperatura` | Sensor | `numero` |

### Valores definidos no projeto

| Sensor | Valores/faixa |
|---|---|
| `hora` | `0` a `23` |
| `temperatura` | `-10` a `50` |
| `vasilha` | `cheia`, `vazia` |
| `caixa_areia` | `suja`, `limpa` |

---

## ⚙️ Dispositivos

| Nome | Categoria | Tipo |
|---|---|---|
| `ar` | Dispositivo | `ar_condicionado` |
| `pote` | Dispositivo | `pote` |
| `bebedouro` | Dispositivo | `bebedouro` |
| `alarme` | Dispositivo | `alarme` |

---

## ⚡ Ações

| Ação | Parâmetros | Dispositivos/valores aceitos |
|---|---|---|
| `ligar` | `dispositivo` | pote, bebedouro, alarme e ar |
| `desligar` | `dispositivo` | pote, bebedouro, alarme e ar |
| `ajustar` | `dispositivo, numero` | ar-condicionado |
| `notificar` | `texto` | mensagem de texto |

O `ajustar` possui uma faixa específica para o ar-condicionado:

```text
16 a 30
```

---

# 📝 Exemplo de programa em CasaScript

O programa utilizado como exemplo no projeto contém cinco regras:

```text
# Regras da casa inteligente 🏠

QUANDO vasilha == "vazia" E hora >= 12:00 ENTAO
    ligar(pote);
    ligar(alarme);
    notificar("Pote enchido às 12 horas")
FIM

QUANDO vasilha == "cheia" ENTAO
    desligar(pote)
FIM

QUANDO temperatura > 26 + 2 E presenca ENTAO
    ajustar(ar, 22)
FIM

QUANDO presenca ENTAO
    ligar(bebedouro)
SENAO
    desligar(bebedouro)
FIM

QUANDO caixa_areia == "suja" ENTAO
    ligar(alarme);
    notificar("Caixa suja!")
FIM
```

Esse programa demonstra condições, operadores lógicos, comparações, expressões aritméticas, múltiplas ações e o uso de `SENAO`.

---

# 🔤 Exemplo da análise léxica

Um trecho como:

```text
QUANDO vasilha == "vazia" E hora >= 12:00 ENTAO
```

é transformado em tokens, por exemplo:

| Lexema | Token |
|---|---|
| `QUANDO` | `QUANDO` |
| `vasilha` | `NOME` |
| `==` | `OP_COMP` |
| `"vazia"` | `TEXTO` |
| `E` | `E` |
| `hora` | `NOME` |
| `>=` | `OP_COMP` |
| `12:00` | `HORA` |
| `ENTAO` | `ENTAO` |

Espaços em branco e comentários são descartados pelo lexer.

---

# 🌳 AST

Após a análise sintática, o projeto transforma a árvore produzida pelo Lark em uma **AST**, eliminando a estrutura desnecessária da gramática e mantendo os elementos importantes do programa.

Exemplo conceitual:

```text
📦 Programa
├── 📏 Regra #1
│   ├── SE: ⚖️ Comparacao (>)
│   │   ├── 📡 Variavel temperatura
│   │   └── 🔢 Numero 30
│   └── FAÇA: ⚡ Acao ligar()
│       └── 📡 Variavel ar
```

---

# 🧠 Análise Semântica

Na análise semântica, o compilador verifica se o programa faz sentido dentro das regras da CasaPet.

Entre as verificações realizadas estão:

- existência do sensor;
- distinção entre sensores e dispositivos;
- compatibilidade entre tipos;
- quantidade correta de argumentos das ações;
- dispositivos compatíveis com cada ação;
- valores permitidos pelos sensores;
- faixa de ajuste do ar-condicionado;
- divisão por zero;
- comparações incompatíveis;
- conflitos entre ações.

Exemplo de erro semântico:

```text
QUANDO hora == "noite" ENTAO ligar(alarme) FIM
```

A comparação é inválida porque `hora` possui tipo `hora`, enquanto `"noite"` é um `texto`.

---

# 🚀 Otimização

Antes da geração do bytecode, a AST pode ser otimizada.

O projeto implementa:

### Constant Folding

```text
26 + 2
```

é transformado em:

```text
28
```

### Dupla negação

```text
NAO NAO presenca
```

é transformado em:

```text
presenca
```

### Identidades aritméticas

Exemplos:

```text
temperatura + 0  → temperatura
temperatura * 1  → temperatura
temperatura / 1  → temperatura
temperatura * 0  → 0
```

### Simplificação lógica

Exemplos:

```text
VERDADEIRO E x → x
FALSO OU x     → x
```

### Código morto

Uma regra cuja condição foi determinada como permanentemente falsa pode ser removida antes da geração do bytecode.

---

# 💻 Exemplo de Bytecode Gerado

Para a **Regra #1** do programa:

```text
QUANDO vasilha == "vazia" E hora >= 12:00 ENTAO
    ligar(pote);
    ligar(alarme);
    notificar("Pote enchido às 12 horas")
FIM
```

o compilador gera:

```text
══════ Regra #1 ══════

[condição]
  0  LOAD_SENSOR           'vasilha'
  1  PUSH_CONST            'vazia'
  2  COMPARE               ==
  3  JUMP_IF_FALSE_OR_POP  -> 7
  4  LOAD_SENSOR           'hora'
  5  PUSH_CONST            720
  6  COMPARE               >=
  7  RETURN

[ações]
  0  LOAD_DEVICE           'pote'
  1  CALL_ACTION           ligar  (args=1)
  2  LOAD_DEVICE           'alarme'
  3  CALL_ACTION           ligar  (args=1)
  4  PUSH_CONST            'Pote enchido às 12 horas'
  5  CALL_ACTION           notificar  (args=1)
  6  HALT
```

A instrução:

```text
JUMP_IF_FALSE_OR_POP
```

implementa o **curto-circuito lógico** do operador `E`. Assim, se `vasilha == "vazia"` for falso, a expressão seguinte não precisa ser avaliada.

A hora `12:00` é convertida internamente para **720 minutos**:

```text
12 × 60 = 720
```

---

# 🖥️ Máquina Virtual

O bytecode é executado por uma **máquina virtual baseada em pilha**.

Algumas das principais instruções são:

| Instrução | Função |
|---|---|
| `PUSH_CONST` | Coloca uma constante na pilha |
| `LOAD_SENSOR` | Carrega o valor de um sensor |
| `LOAD_DEVICE` | Carrega um dispositivo |
| `BIN_OP` | Executa uma operação aritmética |
| `COMPARE` | Executa uma comparação |
| `NOT` | Inverte um valor lógico |
| `JUMP_IF_FALSE_OR_POP` | Implementa curto-circuito de `E` |
| `JUMP_IF_TRUE_OR_POP` | Implementa curto-circuito de `OU` |
| `CALL_ACTION` | Executa uma ação |
| `RETURN` | Retorna o resultado de uma condição |
| `HALT` | Encerra a execução |

---

# 🏠 Simulação da Casa

A classe `Casa` mantém o estado dos sensores e dispositivos.

### Sensores iniciais

```python
{
    "hora": 12,
    "temperatura": 25,
    "umidade": 60,
    "vasilha": "cheia",
    "caixa_areia": "limpa",
    "presenca": True
}
```

### Dispositivos

```python
{
    "pote": ...,
    "bebedouro": ...,
    "alarme": ...,
    "ar": ...
}
```

O projeto também possui uma `CentralDeAutomacao`, que recebe eventos dos sensores, executa as condições compiladas e dispara as ações correspondentes.

As regras são disparadas por **transição de estado**:

```text
FALSO → VERDADEIRO
```

e o bloco `SENAO` é executado quando ocorre:

```text
VERDADEIRO → FALSO
```

---

# 📅 Exemplo de simulação

Durante a simulação de um dia, o sistema altera os valores dos sensores:

```text
hora = 12
vasilha = vazia
vasilha = cheia
temperatura = 31
hora = 20
temperatura = 24
caixa_areia = limpa
caixa_areia = suja
hora = 23
presenca = False
```

A central avalia as regras compiladas a cada evento e registra as ações executadas.

Exemplos de ações registradas:

```text
[12h] ⚡ desligar(pote)
[12h] ⚡ ligar(bebedouro)
[12h] ⚡ ajustar(ar) -> 22
[20h] ⚡ ligar(alarme)
[20h] 📱 NOTIFICAÇÃO: Caixa suja!
```

<img width="771" height="372" alt="Screenshot 2026-10-07 151224_edited" src="https://github.com/user-attachments/assets/12246cd5-424d-4532-8ee6-07c175eaa84e" />


---

# 🧪 Testes

O projeto possui uma bateria de testes automáticos cobrindo diferentes fases do compilador.

Entre os casos testados estão:

- regra simples;
- acionamento do pote;
- condição sobre a caixa de areia;
- regra com `SENAO`;
- ar-condicionado e notificação;
- condições compostas;
- símbolo inválido;
- horário inválido;
- ação com argumentos incorretos;
- caractere inválido;
- ausência do `FIM`;
- sensor inexistente.

Resultado atual da bateria de testes:

```text
🧪 15/15 testes passaram
```

---

# 🛠️ Tecnologias

- **Python**
- **Lark**
- **Jupyter Notebook / Google Colab**
- **ipywidgets**
- **IPython**

Principais recursos utilizados:

```python
Lark
Transformer
AST
dataclasses
Máquina Virtual
Bytecode
Expressões Regulares
```

---

# ▶️ Como executar

## Google Colab

Abra o notebook:

```text
Pratica2.ipynb
```

Execute as células em ordem.

## VS Code

Instale as dependências:

```bash
python -m pip install lark ipywidgets ipykernel
```

Depois abra o notebook no VS Code utilizando a extensão **Jupyter**.

---

# 📁 Estrutura conceitual

```text
CasaPet
│
├── Análise Léxica
│   └── Tokens
│
├── Análise Sintática
│   └── Parser LALR(1)
│
├── AST
│   └── Estruturas da linguagem
│
├── Análise Semântica
│   └── Tabela de símbolos
│
├── Otimização
│   ├── Constant Folding
│   ├── Simplificação lógica
│   ├── Identidades aritméticas
│   ├── Dupla negação
│   └── Código morto
│
├── Geração de Código
│   └── Bytecode
│
└── Máquina Virtual
    └── Execução das automações
```

---

# 📚 Conceitos de compiladores aplicados

O projeto demonstra, na prática, conceitos fundamentais de construção de compiladores:

- análise léxica;
- análise sintática;
- gramática livre de contexto;
- parser LALR(1);
- AST;
- tabela de símbolos;
- análise semântica;
- otimização de código;
- geração de código intermediário;
- bytecode;
- máquina virtual;
- execução baseada em pilha;
- backpatching;
- curto-circuito de operadores lógicos;
- tratamento de erros;
- testes automatizados.

---

## 👨‍💻 Projeto acadêmico

Projeto desenvolvido para a disciplina de **Compiladores**, utilizando o cenário de uma **casa automatizada para pets**.

**Autor:** Lucas Carvalho

GitHub:  
https://github.com/LucasCAraujo21
