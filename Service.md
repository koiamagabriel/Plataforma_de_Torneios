# FCHub — Construção de Serviços

## Visão Geral

Este projeto implementa a evolução do sistema **FCHub**, uma aplicação web para gerenciamento de torneios esportivos desenvolvida com **FastAPI**, **Jinja2**, **CSS** e **persistência local em arquivos JSON**.

Nesta etapa da disciplina, foram implementados **dois serviços relacionados ao cenário de negócio de registro e validação de resultado de partida**:

- `ValidacaoResultadoService`
- `RegistrarResultadoService`

O objetivo desta construção foi permitir que um jogador participante de uma partida informe o resultado, que esse resultado seja validado pelo sistema e, em seguida, registrado com o status **aguardando confirmação**.

---

## Serviços Implementados

### 1. ValidacaoResultadoService

Serviço responsável por validar se um resultado informado pode ser aceito para uma partida específica.

### Responsabilidades
- verificar se o usuário está autenticado;
- verificar se a partida existe;
- verificar se o usuário participa da partida;
- verificar se a partida ainda aceita registro de resultado;
- verificar se ainda não existe resultado já informado;
- validar se os placares são números inteiros válidos;
- impedir empate.

### Operação principal
- `validar_resultado_partida(id_partida, current_user, placar_jogador_a, placar_jogador_b)`

### Saída esperada
- indicação de sucesso ou falha na validação;
- mensagem de retorno;
- dados validados prontos para o próximo serviço.

---

### 2. RegistrarResultadoService

Serviço responsável por registrar o resultado informado na partida e alterar o estado do resultado para **aguardando confirmação**.

### Responsabilidades
- localizar a partida;
- salvar o placar informado;
- registrar qual jogador informou o resultado;
- alterar o campo `status_resultado` para `aguardando_confirmacao`.

### Operação principal
- `registrar_resultado_pendente(id_partida, current_user, placar_jogador_a, placar_jogador_b)`

### Saída esperada
- indicação de sucesso ou falha no registro;
- mensagem de retorno;
- estado atualizado da partida.

---

## Arquitetura Adotada

A arquitetura do projeto foi mantida simples e organizada, separando responsabilidades entre camada de entrada, lógica de negócio e persistência.

### Estrutura principal

- `app/main.py`  
  Camada de entrada da aplicação. Define as rotas HTTP e coordena o fluxo entre os serviços.

- `app/auth_service.py`  
  Responsável por cadastro e login de usuários.

- `app/torneios_service.py`  
  Responsável pela criação de torneios, inscrições e geração automática das partidas.

- `app/validacao_resultado_service.py`  
  Serviço de validação do resultado da partida.

- `app/registrar_resultado_service.py`  
  Serviço de registro do resultado pendente.

- `app/storage.py`  
  Responsável por leitura e escrita dos arquivos JSON.

- `app/template/`  
  Páginas HTML da aplicação.

- `app/static/`  
  Arquivos de estilo da aplicação.

- `app/data/users.json`  
  Persistência de usuários.

- `app/data/tournaments.json`  
  Persistência de torneios.

- `app/data/matches.json`  
  Persistência de partidas.

---

## Estilo de Coordenação

O estilo de coordenação adotado foi **orquestração**.

### Justificativa

A escolha por orquestração foi feita porque o fluxo entre os serviços exige uma **sequência fixa de execução**:

1. o usuário informa o resultado da partida;
2. a aplicação chama o `ValidacaoResultadoService`;
3. se o resultado for válido, a aplicação chama o `RegistrarResultadoService`;
4. o resultado é registrado como `aguardando_confirmacao`.

Nesse modelo, a **camada de entrada da aplicação**, implementada no `main.py`, atua como ponto central do fluxo, garantindo a ordem correta da execução.

---

## Endpoint Implementado

### Registrar resultado da partida

**POST** `/partidas/{match_id}/resultado`

#### Parâmetros recebidos
- `match_id` — identificador da partida
- `placar_jogador_a` — placar do jogador A
- `placar_jogador_b` — placar do jogador B

#### Fluxo do endpoint
1. recebe a requisição do usuário;
2. chama o `ValidacaoResultadoService`;
3. se a validação for bem-sucedida, chama o `RegistrarResultadoService`;
4. salva os dados da partida;
5. retorna mensagem de sucesso ou erro.

---

## Fluxo de Interação entre os Serviços

### Serviços envolvidos
- Camada de entrada da aplicação
- `ValidacaoResultadoService`
- `RegistrarResultadoService`
- Persistência de partidas

### Mensagens trocadas
- `ValidarResultadoPartidaRequest`
- `ValidarResultadoPartidaResponse`
- `RegistrarResultadoPendenteRequest`
- `RegistrarResultadoPendenteResponse`

### Sequência de execução
1. O usuário informa o resultado da partida.
2. A aplicação recebe os dados.
3. O sistema chama o `ValidacaoResultadoService`.
4. O serviço valida a partida, o usuário e o placar informado.
5. Se o resultado for inválido, o fluxo é interrompido e o usuário recebe uma mensagem de erro.
6. Se o resultado for válido, o sistema chama o `RegistrarResultadoService`.
7. O serviço registra o placar e altera o status do resultado para `aguardando_confirmacao`.
8. A aplicação retorna a resposta final ao usuário.

---

## Regras de Negócio Implementadas

As seguintes regras foram implementadas na validação do resultado:

- apenas usuários participantes da partida podem registrar resultado;
- a partida precisa existir;
- a partida precisa estar com status `pendente`;
- o resultado só pode ser informado se `status_resultado` estiver como `nao_informado`;
- os placares devem ser números inteiros;
- valores negativos não são permitidos;
- empate não é permitido.

---

## Como Executar o Projeto

## Pré-requisitos
- Python 3.10 ou superior
- `pip`

## Instalação das dependências

Na raiz do projeto, execute:

```bash
python -m pip install fastapi uvicorn jinja2 python-multipart itsdangerous pychallonge
