# Sistema de Torneios EA FC (8/16) — FastAPI

Projeto da disciplina para construir um sistema de **gerenciamento de torneios de EA FC (FIFA)** com:
- inscrições automáticas em torneios de **8 ou 16 jogadores**
- **fechamento automático** quando atingir o limite
- **geração de chaveamento**
- **registro e validação de resultados** (confirmação/recusa + decisão do admin em disputa)
- **rankings** por **pontuação** e por **campeões (títulos)**

---

# Alunos:

João Pedro Lopes Santana Villas Bôas - RA: 22.125.065-7 <br>
Gabriel Koiama - RA: 22.125.067-3<br>
Rafael Takahagi Mendes - RA: 22.126.084-7<br>
Laura de Souza Parente - RA: 22.123.033-7

## 🎯 Objetivo do Projeto
Centralizar e automatizar a organização de torneios, evitando o uso de planilhas/grupos e reduzindo problemas como:
- falta de controle de vagas
- chaveamento manual
- resultados contestados sem rastreabilidade
- ranking sem histórico confiável

# FCHub – Gestão de Torneios e Gestão de Partidas

## Visão Geral

Este repositório contém a implementação inicial do **FCHub**, uma plataforma voltada ao gerenciamento de torneios de **EA FC**.  
Nesta etapa do projeto, o foco está em dois componentes centrais da aplicação:

- **Gestão de Torneios**
- **Gestão de Partidas**

Ambos os componentes foram projetados de forma modular, com comunicação baseada em interfaces, buscando separar responsabilidades e reduzir acoplamento direto entre as partes do sistema.

Além disso, a solução considera o uso da **API/biblioteca Challonge** como apoio para operações relacionadas à organização do torneio e estruturação das partidas.

---

# 1. Descrição dos dois componentes implementados

## 1.1 Componente de Gestão de Torneios

O componente de **Gestão de Torneios** é responsável por administrar o ciclo de vida dos torneios dentro da plataforma.  
Seu papel é controlar a disponibilidade de torneios, permitir a inscrição de jogadores e identificar o momento em que um torneio atinge sua capacidade máxima.

### Principais responsabilidades
- Listar torneios disponíveis para inscrição
- Realizar a inscrição de jogadores em torneios
- Verificar disponibilidade de vagas
- Marcar torneios como lotados quando atingirem o limite
- Acionar integrações externas relacionadas à estrutura do torneio, quando necessário

Esse componente representa a porta de entrada para o jogador participar de uma competição e concentra as regras ligadas ao estado do torneio.

---

## 1.2 Componente de Gestão de Partidas

O componente de **Gestão de Partidas** é responsável por controlar as partidas pertencentes aos torneios já formados.  
Seu foco está no acompanhamento do andamento das partidas e na manipulação dos resultados informados pelos jogadores.

### Principais responsabilidades
- Consultar partidas de um jogador em determinado torneio
- Registrar o resultado de uma partida
- Consultar o status atual de uma partida
- Oficializar resultados após validação
- Consumir informações do torneio para garantir consistência entre bracket, jogadores e partidas

Esse componente entra em ação após a formação do torneio, sendo responsável pela operação do fluxo competitivo.

---

# 2. Interfaces fornecidas

As interfaces fornecidas representam os serviços que cada componente disponibiliza ao restante do sistema.

## 2.1 Interface fornecida pelo componente Gestão de Torneios

### `ITorneios`

Responsável por expor as operações ligadas ao gerenciamento dos torneios.

#### Operações
- `listarTorneiosDisponiveis()`
- `inscreverEmTorneio(idJogador, idTorneio)`
- `marcarTorneioComoLotado(idTorneio)`

### Objetivo
Permitir que outros componentes e camadas da aplicação interajam com as regras de negócio de torneios sem depender da implementação concreta.

---

## 2.2 Interface fornecida pelo componente Gestão de Partidas

### `IResultadosPartida`

Responsável por expor as operações ligadas à consulta, atualização e oficialização de partidas.

#### Operações
- `consultarPartidasDoJogador(idJogador, idTorneio)`
- `registrarResultadoDaPartida(idPartida, resultado)`
- `consultarStatusDaPartida(idPartida)`
- `oficializarResultadoDaPartida(idPartida)`

### Objetivo
Oferecer um contrato estável para que as funcionalidades relacionadas às partidas possam ser utilizadas sem dependência direta da lógica interna do componente.

---

# 3. Interfaces requeridas

As interfaces requeridas representam os contratos externos dos quais um componente depende para funcionar corretamente.

## 3.1 Interfaces requeridas pela Gestão de Torneios

### Interface externa de integração com Challonge
O componente de torneios depende de uma interface de integração com a **API/biblioteca Challonge**, responsável por operações como criação, consulta e sincronização de informações do torneio.

Exemplo conceitual:
- `IChallongeTorneios`

#### Possíveis responsabilidades dessa interface
- Criar torneio na plataforma externa
- Consultar dados estruturais do torneio
- Sincronizar participantes
- Atualizar informações do bracket quando aplicável

---

## 3.2 Interfaces requeridas pela Gestão de Partidas

### `ITorneios`
O componente de partidas depende da interface fornecida pela Gestão de Torneios para garantir que as partidas sejam tratadas dentro do contexto correto do torneio.

Essa dependência permite, por exemplo:
- validar se a partida pertence ao torneio correto
- recuperar contexto do torneio associado
- consultar informações estruturais necessárias ao fluxo de partidas

### Interface externa de integração com Challonge
O componente de partidas também depende de uma interface de integração com a **API/biblioteca Challonge**, utilizada para consultar ou atualizar informações relacionadas às partidas.

Exemplo conceitual:
- `IChallongePartidas`

#### Possíveis responsabilidades dessa interface
- Consultar partidas/bracket gerados externamente
- Obter identificadores externos de confrontos
- Atualizar resultados no serviço externo
- Sincronizar status das partidas

---

# 4. Explicação de como ocorre a comunicação entre eles

A comunicação entre os componentes ocorre de maneira **indireta**, por meio de **interfaces bem definidas**.

## Fluxo geral de comunicação

1. O componente **Gestão de Torneios** administra o torneio e controla as inscrições.
2. Quando o torneio atinge sua capacidade, ele pode ser marcado como lotado e preparado para a fase de partidas.
3. O componente **Gestão de Partidas** consulta o contexto do torneio por meio da interface `ITorneios`.
4. Com base nessas informações, o componente de partidas gerencia os confrontos e os resultados.
5. Sempre que necessário, ambos os componentes podem utilizar seus respectivos adaptadores de integração com o Challonge.

## Exemplo prático de interação

- Um jogador se inscreve em um torneio por meio da **Gestão de Torneios**
- O sistema valida a inscrição e atualiza o estado do torneio
- Após a formação do torneio, a **Gestão de Partidas** consulta o torneio vinculado
- O jogador registra o resultado de uma partida
- O componente de partidas valida e persiste o resultado
- Caso exista sincronização externa, o adaptador do Challonge é acionado

### Resumo
A **Gestão de Partidas não acessa diretamente a implementação interna da Gestão de Torneios**.  
Ela utiliza apenas o contrato exposto por `ITorneios`, mantendo a comunicação controlada e previsível.

---

# 5. Justificativa de como foi evitado o acoplamento direto

O acoplamento direto foi evitado por meio de uma arquitetura baseada em **interfaces e separação de responsabilidades**.

## Estratégias adotadas

### 5.1 Uso de contratos
Cada componente expõe apenas aquilo que precisa ser consumido pelos demais, por meio de interfaces.  
Isso significa que nenhum componente depende diretamente da classe concreta do outro.

### 5.2 Dependência de abstrações
O componente de **Gestão de Partidas** depende de `ITorneios`, e não da implementação concreta da Gestão de Torneios.  
Da mesma forma, as integrações com o Challonge também podem ser tratadas por interfaces específicas, permitindo troca de implementação sem impacto nas regras de negócio.

### 5.3 Separação entre regra de negócio e integração externa
As regras centrais do domínio ficam nos componentes internos, enquanto o acesso ao Challonge fica encapsulado em adaptadores ou serviços de infraestrutura.  
Isso impede que a lógica principal do sistema fique presa a detalhes de API, biblioteca ou fornecedor externo.

### 5.4 Facilidade de manutenção e testes
Essa abordagem facilita:
- substituição de implementações
- criação de mocks para testes
- evolução isolada dos componentes
- reaproveitamento de código
- menor impacto de mudanças futuras

## Benefício arquitetural
Com essa estratégia, o sistema se torna mais:
- modular
- testável
- extensível
- reutilizável
- fácil de manter

---

# 6. Instruções para execução do projeto

> **Observação:** esta seção considera uma execução padrão de um projeto Python com FastAPI.  
> Os nomes de arquivos e diretórios podem ser ajustados conforme a implementação final do repositório.

## Pré-requisitos
- Python 3.10 ou superior
- `pip`
- Ambiente virtual recomendado
- Acesso à API/biblioteca Challonge, se a integração externa estiver habilitada

## Passo a passo

### 1. Clonar o repositório
```bash
git clone <URL_DO_REPOSITORIO>
cd <NOME_DO_REPOSITORIO>
