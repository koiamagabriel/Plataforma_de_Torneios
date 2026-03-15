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

# FCHub

Sistema web simples para gerenciamento de torneios, com foco em dois componentes principais:

- **Login de Usuário**
- **Gestão de Torneios**

O projeto foi desenvolvido com **FastAPI**, páginas HTML com **Jinja2**, estilização em **CSS** e persistência local em **arquivos JSON**.  
Nesta versão, não é necessário banco de dados.

---

## 1. Descrição dos dois componentes implementados

### 1.1 Componente de Login de Usuário

O componente de **Login de Usuário** é responsável por controlar o acesso ao sistema.

Suas funções principais são:

- cadastrar novos usuários com **e-mail** e **senha**
- permitir login
- identificar automaticamente o tipo do usuário com base no domínio do e-mail
- manter o usuário autenticado durante a navegação

### Regras de perfil
- e-mails terminados em `@admin.com` → usuário **administrador**
- e-mails terminados em `@gmail.com` → usuário **padrão**

### Usuários iniciais do sistema
O sistema já inicia com dois usuários salvos:

- `user@admin.com` → administrador
- `user@gmail.com` → usuário padrão

Senha inicial dos dois:
- `123456`

---

### 1.2 Componente de Gestão de Torneios

O componente de **Gestão de Torneios** é responsável por controlar os torneios e as inscrições.

Suas funções principais são:

- criar torneios com formato de **8 ou 16 jogadores**
- listar torneios cadastrados
- permitir que usuários se inscrevam em torneios
- controlar o número de inscritos
- mudar o status do torneio quando ele lota
- gerar automaticamente as partidas da primeira rodada quando o torneio atinge o limite de jogadores

Além disso, o sistema já está preparado para integração com a biblioteca **Challonge**, embora o funcionamento local continue normal mesmo sem credenciais configuradas.

---

## 2. Interfaces fornecidas

Neste projeto, as interfaces fornecidas são os serviços e rotas que cada componente disponibiliza.

### 2.1 Interfaces fornecidas pelo componente de Login de Usuário

Arquivo:
- `app/auth_service.py`

Métodos fornecidos:
- `register_user(email, password)`
- `login_user(email, password)`
- `get_user_by_email(email)`

Esses métodos permitem:
- cadastrar usuário
- validar login
- consultar usuário salvo

---

### 2.2 Interfaces fornecidas pelo componente de Gestão de Torneios

Arquivo:
- `app/torneios_service.py`

Métodos fornecidos:
- `create_tournament(nome, formato, current_user)`
- `list_tournaments()`
- `get_tournament_by_id(tournament_id)`
- `subscribe_user(tournament_id, current_user)`
- `get_matches_by_tournament(tournament_id)`

Esses métodos permitem:
- criar torneio
- listar torneios
- buscar torneio por id
- inscrever usuário
- visualizar partidas geradas automaticamente

---

## 3. Interfaces requeridas

As interfaces requeridas são os recursos de que cada componente precisa para funcionar.

### 3.1 Interfaces requeridas pelo componente de Login de Usuário

O componente de login depende do arquivo:

- `app/storage.py`

Funções utilizadas:
- `load_users()`
- `save_users(users)`
- `get_next_user_id()`

Essas funções são responsáveis por salvar e carregar os usuários em JSON.

---

### 3.2 Interfaces requeridas pelo componente de Gestão de Torneios

O componente de torneios depende dos arquivos:

- `app/storage.py`
- biblioteca `challonge` 

Funções utilizadas em `storage.py`:
- `load_tournaments()`
- `save_tournaments(tournaments)`
- `load_matches()`
- `save_matches(matches)`
- `get_next_tournament_id()`
- `get_next_match_id()`

---

## 4. Explicação de como ocorre a comunicação entre eles

A comunicação entre os componentes acontece por meio do arquivo principal:

- `app/main.py`

O fluxo ocorre da seguinte forma:

1. O usuário acessa a tela de **cadastro/login**
2. O `main.py` chama o **AuthService**
3. Depois do login, o usuário é salvo na sessão
4. Ao acessar torneios, o `main.py` recupera o usuário logado
5. O `main.py` chama o **TorneiosService**
6. O `TorneiosService` usa os dados do usuário para:
   - validar se ele pode criar torneio
   - permitir inscrição
   - gerar partidas quando o torneio enche

### Resumo da comunicação
- `main.py` faz a ponte entre as telas e os serviços
- `auth_service.py` cuida do acesso
- `torneios_service.py` cuida das regras dos torneios
- `storage.py` cuida da persistência dos dados

---

## 5. Justificativa de como foi evitado o acoplamento direto

O acoplamento direto foi evitado separando claramente as responsabilidades de cada arquivo.

### Estratégias adotadas

#### Separação por responsabilidade
- `auth_service.py` cuida apenas do login e cadastro
- `torneios_service.py` cuida apenas dos torneios
- `storage.py` cuida apenas da persistência
- `main.py` organiza as rotas e a navegação
- `template/` contém apenas as páginas HTML
- `static/` contém apenas o CSS

#### Comunicação controlada
O componente de torneios não manipula diretamente a tela, e o componente de login não controla a lógica dos torneios.  
Tudo passa pelo `main.py`, que faz a integração entre os componentes.

#### Persistência isolada
Os dados não ficam espalhados pelo sistema.  
Toda leitura e escrita de usuários, torneios e partidas fica centralizada em `storage.py`.

### Benefícios
Com isso, o projeto fica:
- mais fácil de entender
- mais fácil de testar
- mais fácil de manter
- mais fácil de evoluir no futuro

---

## 6. Instruções para execução do projeto

## 6.1 Pré-requisitos

Antes de executar, verifique se você possui instalado:

- Python 3.10 ou superior
- pip

---

## 6.2 Estrutura esperada do projeto

A estrutura deve estar assim:

```text
fchub/
├── app/
   ├── static/
   │   └── style.css
   ├── template/
   │   ├── auth.html
   │   ├── menu.html
   │   ├── torneios.html
   │   └── criar_torneio.html
   ├── auth_service.py
   ├── main.py
   ├── storage.py
   └── torneios_service.py
```

## 6.3 Instalar dependências

Abra o terminal na pasta raiz do projeto e execute:

```bash
python -m pip install fastapi uvicorn jinja2 python-multipart itsdangerous pychallonge
```
## 6.4 Executar o projeto

Ainda na pasta raiz do projeto, execute:
```
python -m uvicorn app.main:app --reload
```
Se tudo estiver correto, o terminal exibirá algo semelhante a:
```
Uvicorn running on http://127.0.0.1:8000
```

## 6.5 Acessar o sistema

Abra o navegador e Acesse:
```
http://127.0.0.1:8000
```
## 6.6 Testar o login

### Usuário administrador

- E-mail: `user@admin.com`
- Senha: `123456`

### Usuário padrão

- E-mail: `user@gmail.com`
- Senha: `123456`

---

## 6.7 Fluxo de teste recomendado

### Teste com administrador

1. Fazer login com `user@admin.com`
2. Entrar em **Criar Torneio**
3. Criar um torneio com formato **8** ou **16**
4. Ir para **Listar Torneios**
5. Confirmar que o torneio aparece na listagem

### Teste com usuário padrão

1. Fazer login com `user@gmail.com`
2. Ir para **Listar Torneios**
3. Selecionar um torneio
4. Clicar em **Participar do torneio**
5. Verificar se a inscrição foi registrada

### Teste de lotação

1. Cadastrar ou usar mais usuários
2. Inscrever até atingir o limite do torneio
3. Verificar se o status muda
4. Verificar se as partidas da primeira rodada aparecem automaticamente

---

## 6.8 Persistência dos dados

Os dados são salvos localmente em arquivos JSON criados automaticamente durante a execução.

Isso permite:

- manter os usuários cadastrados
- manter os torneios criados
- manter as partidas geradas

Sem necessidade de banco de dados nesta versão.

---

## Considerações finais

Esta versão do projeto foi construída para ser simples, funcional e fácil de entender.

Os dois componentes implementados — **Login de Usuário** e **Gestão de Torneios** — já permitem demonstrar o fluxo principal do sistema:

- cadastro e login
- separação entre administrador e usuário padrão
- criação de torneios
- inscrição em torneios
- geração automática de partidas quando o torneio lota

A arquitetura também permite futuras expansões, como:

- ranking
- controle de resultados
- gerenciamento de partidas
- autenticação mais segura
- banco de dados real
- integração completa com o Challonge




