# Atividade de Inteligência Artificial

## Equipe
- Alberth Viana de Lima
- Allan Carvalho
- Ana Júlia Pereira Corrêa
- Bianka Vasconcelos
- Daniel Silveira Gonzalez
- Guilherme Sahdo Maciel
- Júlio Melo Campos
- Luã Henrique
- Micael Viana
- Stepheson Custódio
- Vinícius Chagas

# Lógicas não clássicas para Representação do Conhecimento e Raciocínio Automático

## 1. O que é Model Checking como método de Raciocínio Automatizado (Dedução Automática)

Model Checking é um método formal de verificação automática que confirma se um modelo computacional (como um sistema de hardware, software ou protocolo) satisfaz uma especificação formal (geralmente expressa em lógica temporal). Ele funciona explorando exaustivamente todos os estados possíveis do sistema para verificar se a propriedade desejada (ex.: "nunca ocorre deadlock") é válida em todos eles.

### Abordagem:
- O sistema é modelado como um autômato finito ou uma máquina de estados
- A especificação é escrita em lógica temporal (ex.: LTL - Linear Temporal Logic ou CTL - Computational Tree Logic)
- O verificador (como NuSMV ou SPIN) executa uma busca sistemática para confirmar ou refutar a propriedade

### Vantagens:
- Automatizado e exaustivo (não depende de intervenção humana)
- Capaz de fornecer contraexemplos se a propriedade falhar

## 2. Diferença para Resolução do Prolog

### Model Checking:
- Focado em verificação de modelos de sistemas infinitos ou finitos (via abstração)
- Usa lógica temporal para expressar propriedades dinâmicas (ex.: "eventualmente X ocorrerá")
- Baseado em exploração de estados e algoritmos de grafos (ex.: algoritmos de ciclo para detectar livelocks)

### Resolução do Prolog:
- Baseado em unificação e backtracking em lógica de primeira ordem
- Usado para inferência lógica (ex.: responder a consultas como "? pai(X, ana)")
- Não lida diretamente com propriedades temporais ou concorrência

### Principais diferenças:
- Prolog é declarativo e voltado para inferência simbólica, enquanto Model Checking é orientado a estados e verificação de comportamentos temporais
- Prolog não verifica exaustivamente todos os estados possíveis de um sistema

### Tabela Comparativa:

| Critério         | Model Checking                     | Prolog                          |
|------------------|------------------------------------|---------------------------------|
| Paradigma        | Verificação de estados             | Inferência lógica              |
| Lógica Usada     | Lógica temporal (CTL, LTL)         | Lógica de primeira ordem       |
| Abordagem        | Exploração exaustiva de estados    | Backtracking + unificação      |
| Saída            | Contraexemplos / Witness (planos)  | Respostas a consultas (termos) |
| Aplicação Típica | Protocolos de rede, hardware       | Bancos de dados, IA simbólica  |

## 3. Aplicações além de Planejamento

Além de planejamento (ex.: verificação de sequências de ações em robótica), Model Checking e outros métodos de verificação são usados em:

- **Hardware**: Verificação de circuitos digitais (ex.: ausência de race conditions em CPUs)
- **Protocolos de Rede**: Garantir que protocolos como TCP/IP não entrem em estados inválidos
- **Sistemas Críticos**: Verificação de controladores de usinas nucleares ou sistemas de aviação
- **Blockchain**: Validação de contratos inteligentes para evitar vulnerabilidades
- **Biologia Computacional**: Verificação de modelos de interação entre proteínas

Outros métodos de verificação (ex.: Theorem Proving) são aplicados em:
- **Compiladores**: Provar corretude de otimizações
- **Criptografia**: Verificar propriedades de segurança em algoritmos

## 4. Como a saída do NuSMV gera um plano de ações

O NuSMV (um verificador de modelos) pode ser usado para gerar planos de ações quando a especificação inclui propriedades de reachability (ex.: "é possível alcançar o estado X?"). O processo é:

1. **Modelagem**:
   - O sistema é descrito como um autômato com variáveis de estado e transições
   - Exemplo: Um robô com estados como "inicial", "coletar_obj", "entregar_obj"

2. **Especificação**:
   - Define-se uma meta em CTL/LTL, como `EF (entregar_obj)` ("existe um caminho onde entregar_obj é alcançado")

3. **Verificação**:
   - Se a propriedade for válida, o NuSMV gera um counterexample (quando falha) ou um witness (quando válida), que é uma sequência de estados levando ao objetivo

4. **Extraindo o Plano**:
   - O witness é uma trajetória de estados (ex.: inicial → coletar_obj → entregar_obj)
   - Cada transição entre estados corresponde a uma ação do plano (ex.: "mover_para_obj", "agarrar_obj")

**Exemplo Prático**:
Em um sistema de transporte, o NuSMV pode gerar uma sequência de movimentos para um drone evitar obstáculos e chegar ao destino, baseado em regras de transição modeladas.

## Explicação do Código da Torre de Hanói (NuSMV)

```smv
MODULE main
-- Hanoi problem with three poles (left, middle, right)
-- and four ordered disks d1, d2, d3, d4,
-- disk d1 is the biggest one

MODULE main: Define o módulo principal do modelo.
Comentários: Descrevem o problema: 3 pinos (esquerda, meio, direita) e 4 discos ordenados por tamanho (d1 é o maior, d4 o menor).
VAR
d1 : {left,middle,right};
d2 : {left,middle,right};
d3 : {left,middle,right};
d4 : {left,middle,right};
move : 1..4; -- possible moves

VAR: Declara variáveis do sistema:
d1, d2, d3, d4: Posições dos discos (esquerda, meio ou direita).
move: Variável que representa a ação de mover um disco (1 para d1, 2 para d2, etc.).
DEFINE
move_d1 := move=1;
move_d2 := move=2;
move_d3 := move=3;
move_d4 := move=4;

DEFINE: Define macros para simplificar expressões:
move_d1 é verdadeiro se move=1 (movendo d1), e assim por diante.
Essas macros facilitam a leitura das condições de transição.
-- A block is clear iff there is no disk on it
-- di is clear iff di!=dj for every j>i
DEFINE
clear_d1 :=
d1!=d2 &
d1!=d3 &
d1!=d4;
clear_d2 :=
d2!=d3 &
d2!=d4;
clear_d3 :=
d3!=d4;
clear_d4 := TRUE;

clear_dX: Define se um disco está "limpo" (sem discos menores em cima):
clear_d1 é verdadeiro se d1 não está sob nenhum outro disco (d2, d3, d4 não estão em cima dele).
clear_d2 verifica se d2 não está sob d3 ou d4, e assim por diante.
clear_d4 é sempre verdadeiro, pois é o menor disco (não pode ter discos menores em cima).
-- initially all items are on the left pole
INIT
d1 = left &
d2 = left &
d3 = left &
d4 = left;

INIT: Define o estado inicial:
Todos os discos começam no pino esquerdo (left).
TRANS
move_d1 ->
-- only d1 changes
next(d1) != d1 &
next(d2) = d2 &
next(d3) = d3 &
next(d4) = d4 &
-- no other disks on d1
clear_d1 &
-- no smaller disks on the next pole
next(d1) != d2 &
next(d1) != d3 &
next(d1) != d4

TRANS: Define as regras de transição para mover d1:
Condição: Se move_d1 for verdadeiro (movendo d1):
next(d1) != d1: O disco d1 deve mudar de pino.
next(d2) = d2 etc.: Os outros discos permanecem no mesmo pino.
clear_d1: O disco d1 deve estar "limpo" (não sob outros discos).
next(d1) != d2 & next(d1) != d3 & next(d1) != d4:
O novo pino de d1 não pode ter discos menores (d2, d3, d4) no mesmo local.
Isso garante que não se coloca um disco maior em cima de um menor.
-- spec to find a solution to the problem
CTLSPEC
! EF (d1=right & d2=right & d3=right & d4=right)

CTLSPEC: Define a especificação a ser verificada:
EF (d1=right & ... ) significa "eventualmente todos os discos estarão no pino direito".
! (negação): A propriedade afirma que não existe um caminho para alcançar o objetivo.
Se o NuSMV refutar essa propriedade (ou seja, encontrar um caminho válido), o contraexemplo será o plano de movimentos para resolver o problema.
Funcionamento do Código

Modelagem do Estado:

Cada variável d1, d2, etc., representa a posição de um disco.
move escolhe qual disco mover a cada passo.

Regras de Movimento:

Apenas um disco pode ser movido por vez.
Um disco só pode ser movido se estiver "limpo" (clear_dX).
Não é permitido colocar um disco maior em cima de um menor (verificando next(d1) != d2, etc.).

Verificação:

A especificação !EF(...) força o NuSMV a buscar um caminho que violate a negação, ou seja, um caminho que atinga o objetivo (todos os discos no pino direito).
O contraexemplo gerado pelo NuSMV será a sequência de movimentos válida para resolver o problema.
Observações Importantes

Limitação do Código:

O trecho do TRANS para move_d1 parece incompleto (falta fechar o & e o ;).
Em um código completo, haveriam condições similares para move_d2, move_d3 e move_d4, garantindo que cada disco só possa ser movido se estiver "limpo" e respeitar as regras de tamanho.

Exemplo de Execução:

Se o NuSMV encontrar um caminho válido, ele exibirá uma sequência como:
d1=left, d2=left, d3=left, d4=left (inicial)  
→ mover d4 para o meio  
→ mover d3 para a direita  
→ ...  
→ todos os discos no pino direito.

```

## Saída

Foi adicionado o que faltava, ou seja, foram cobertas as limitações:
* O trecho do TRANS para move_d1 parece incompleto (falta fechar o & e o ;).
*	No código completo, foran adicionadas as condições similares para move_d2, move_d3 e move_d4, garantindo que cada disco só possa ser movido se estiver "limpo" e respeitar as regras de tamanho.
 
```smv 
*** This is NuSMV 2.6.0 (compiled on Wed Oct 14 15:36:56 2015)
*** Enabled addons are: compass
*** For more information on NuSMV see <http://nusmv.fbk.eu>
*** or email to <nusmv-users@list.fbk.eu>.
*** Please report bugs to <Please report bugs to <nusmv-users@fbk.eu>>

*** Copyright (c) 2010-2014, Fondazione Bruno Kessler

*** This version of NuSMV is linked to the CUDD library version 2.4.1
*** Copyright (c) 1995-2004, Regents of the University of Colorado

*** This version of NuSMV is linked to the MiniSat SAT solver. 
*** See http://minisat.se/MiniSat.html
*** Copyright (c) 2003-2006, Niklas Een, Niklas Sorensson
*** Copyright (c) 2007-2010, Niklas Sorensson

-- specification !(EF (((d1 = right & d2 = right) & d3 = right) & d4 = right))  is false
-- as demonstrated by the following execution sequence
Trace Description: CTL Counterexample 
Trace Type: Counterexample 
  -> State: 1.1 <-
    d1 = left
    d2 = left
    d3 = left
    d4 = left
    move = 4
    move_d4 = TRUE
    move_d3 = FALSE
    move_d2 = FALSE
    move_d1 = FALSE
    clear_d4 = TRUE
    clear_d3 = FALSE
    clear_d2 = FALSE
    clear_d1 = FALSE
  -> State: 1.2 <-
    d4 = middle
    move = 3
    move_d4 = FALSE
    move_d3 = TRUE
    clear_d3 = TRUE
  -> State: 1.3 <-
    d3 = right
    move = 4
    move_d4 = TRUE
    move_d3 = FALSE
    clear_d2 = TRUE
  -> State: 1.4 <-
    d4 = right
    move = 2
    move_d4 = FALSE
    move_d2 = TRUE
    clear_d3 = FALSE
  -> State: 1.5 <-
    d2 = middle
    move = 4
    move_d4 = TRUE
    move_d2 = FALSE
    clear_d1 = TRUE
  -> State: 1.6 <-
    d4 = left
    move = 3
    move_d4 = FALSE
    move_d3 = TRUE
    clear_d3 = TRUE
    clear_d1 = FALSE
  -> State: 1.7 <-
    d3 = middle
    move = 4
    move_d4 = TRUE
    move_d3 = FALSE
    clear_d2 = FALSE
  -> State: 1.8 <-
    d4 = middle
    move = 1
    move_d4 = FALSE
    move_d1 = TRUE
    clear_d3 = FALSE
    clear_d1 = TRUE
  -> State: 1.9 <-
    d1 = right
    move = 4
    move_d4 = TRUE
    move_d1 = FALSE
  -> State: 1.10 <-
    d4 = right
    move = 3
    move_d4 = FALSE
    move_d3 = TRUE
    clear_d3 = TRUE
    clear_d1 = FALSE
  -> State: 1.11 <-
    d3 = left
    move = 4
    move_d4 = TRUE
    move_d3 = FALSE
    clear_d2 = TRUE
  -> State: 1.12 <-
    d4 = left
    move = 2
    move_d4 = FALSE
    move_d2 = TRUE
    clear_d3 = FALSE
    clear_d1 = TRUE
  -> State: 1.13 <-
    d2 = right
    move = 4
    move_d4 = TRUE
    move_d2 = FALSE
    clear_d1 = FALSE
  -> State: 1.14 <-
    d4 = middle
    move = 3
    move_d4 = FALSE
    move_d3 = TRUE
    clear_d3 = TRUE
  -> State: 1.15 <-
    d3 = right
    move = 4
    move_d4 = TRUE
    move_d3 = FALSE
    clear_d2 = FALSE
  -> State: 1.16 <-
    d4 = right
    clear_d3 = FALSE
```
