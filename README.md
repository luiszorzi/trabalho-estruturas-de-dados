# Sistema de Simulação de Fila de Atendimento Hospitalar

Este projeto foi desenvolvido para a disciplina de **Estruturas de Dados** e simula, em linguagem C, um sistema de gerenciamento de filas de atendimento para um ambiente hospitalar ou clínico. O sistema utiliza duas aplicações que rodam em paralelo: um **menu de interação** para o usuário e um **simulador de atendimento** que processa as fichas em tempo real.

A comunicação entre os dois programas é feita de forma assíncrona através de arquivos binários, permitindo que eles operem de forma independente.

## 💡 Características

* **Dois Programas Independentes:** Uma interface de menu para o usuário (`menu`) e um processo de fundo para a simulação (`simulacao`).
* **Fila com Prioridade:** Utiliza uma Árvore Binária de Busca (BST) onde cada nó contém uma fila, permitindo gerenciar múltiplos níveis de prioridade de forma eficiente. Pacientes com maior prioridade (menor número) são sempre atendidos primeiro.
* **Fila Comum:** Pacientes sem prioridade (`prioridade = 6`) são gerenciados em uma fila padrão (FIFO - First-In, First-Out).
* **Persistência de Dados:** O estado completo das filas e relatórios é salvo em arquivos binários (`.dat`). Isso permite que a simulação seja interrompida e retomada a qualquer momento sem perda de dados.
* **Simulação em Tempo Real:** O simulador pode operar em dois modos:
    * **AGUARDAR:** Pausa o atendimento, mas continua exibindo quem são os próximos da fila.
    * **SIMULAR:** Processa ativamente as fichas, respeitando o tempo de atendimento de cada uma.
* **Relatórios de Especialidades:** Gera e exibe um relatório que contabiliza a quantidade de atendimentos requisitados para cada especialidade médica, ajudando na análise de demanda.

## ⚙️ Como Funciona

O projeto é baseado no padrão **Produtor-Consumidor**, onde os dois programas principais se comunicam de forma indireta:

1.  **`menu` (O Produtor):**
    * É a interface com o usuário. Permite criar novas fichas de pacientes, alterar o estado da simulação e visualizar relatórios.
    * Quando uma nova ficha é criada, ela é salva no arquivo `inbox.dat`.
    * Quando o estado da simulação é alterado (ex: para `SIMULAR`), essa informação é salva no arquivo `config.dat`.

2.  **`simulacao` (O Consumidor):**
    * É um processo que roda em um loop contínuo.
    * A cada ciclo, ele lê o arquivo `config.dat` para saber o que fazer (Aguardar, Simular ou Terminar).
    * Ele verifica se o arquivo `inbox.dat` existe. Se sim, processa todas as fichas, inserindo-as nas filas corretas (BST de prioridade ou fila comum), e depois apaga o `inbox.dat`.
    * Se estiver no modo `SIMULAR`, ele atende o próximo paciente da fila (respeitando a prioridade), aguarda o tempo de atendimento simulado, e salva o estado atual das filas.

Essa arquitetura permite que o menu seja fechado e reaberto várias vezes sem interromper o processo de simulação, que continua rodando em segundo plano.

## 🛠️ Estruturas de Dados

* **Fila (`fila.h`, `fila.c`):** Uma implementação clássica de fila com lista encadeada, usada para os pacientes da fila comum.
* **BST de Filas (`bst_fila.h`, `bst_fila.c`):** A estrutura principal para gerenciamento de prioridades. É uma Árvore Binária de Busca onde a chave de cada nó é a prioridade, e o dado armazenado é uma Fila (`Fila*`) contendo os pacientes daquela prioridade.
* **Relatório (`relatorio.h`, `relatorio.c`):** Um array dinâmico que armazena o nome das especialidades e a contagem de atendimentos para cada uma.
* **Configurações (`tad_configs.h`, `tad_configs.c`):** Um TAD simples para gerenciar o estado da simulação através de um `enum` (`AGUARDAR`, `SIMULAR`, `TERMINAR`).

## 🚀 Compilação e Execução

Para compilar e executar o projeto, você precisará de um compilador C (como o GCC).

### Compilando os Programas

Você precisa compilar os dois executáveis separadamente. Abra seu terminal na pasta do projeto e execute os seguintes comandos:

1.  **Compilar o Menu:**
    ```bash
    gcc -o menu menu.c fila.c bst_fila.c tad_configs.c relatorio.c -Wextra -Wall
    ```
2.  **Compilar a Simulação:**
    ```bash
    gcc -o simulacao simulacao.c fila.c bst_fila.c tad_configs.c -Wextra -Wall
    ```

### Executando o Sistema

A simulação requer que os dois programas rodem ao mesmo tempo. Para isso, **você precisará de dois terminais abertos**.

1.  **Terminal 1:** Inicie o processo de simulação. Ele começará no modo `AGUARDAR`.
    ```bash
    ./simulacao
    ```
2.  **Terminal 2:** Inicie o menu para interagir com a simulação.
    ```bash
    ./menu
    ```

Agora você pode usar as opções do menu no **Terminal 2** para criar fichas, iniciar a simulação e ver os resultados sendo processados no **Terminal 1**.

## 📁 Estrutura dos Arquivos

### Código-Fonte

* `menu.c`: Lógica da interface do usuário (criação de fichas, etc.).
* `simulacao.c`: Lógica do processo de atendimento e gerenciamento de estado.
* `fila.h` / `fila.c`: Implementação da estrutura de Fila.
* `bst_fila.h` / `bst_fila.c`: Implementação da Árvore Binária de Busca de Filas de Prioridade.
* `relatorio.h` / `relatorio.c`: Implementação do sistema de relatórios de especialidades.
* `tad_configs.h` / `tad_configs.c`: Implementação do TAD para controle de estado.

### Arquivos de Dados (Gerados durante a execução)

* `fila.dat`: Armazena o estado da fila comum.
* `bst.dat`: Armazena o estado da fila de prioridades.
* `relatorio.dat`: Armazena os dados do relatório de especialidades.
* `inbox.dat`: Arquivo temporário que funciona como "caixa de entrada" para novas fichas.
* `config.dat`: Arquivo de controle que armazena o estado atual da simulação.
