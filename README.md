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

---

## 👥 Perfis de Usuário
- **Jogador**
  - cadastra-se / autentica-se
  - escolhe um torneio (8 ou 16) para se inscrever
  - registra resultados e confirma/recusa o resultado do adversário
  - consulta chaveamento, partidas e rankings

- **Admin (Organizador/Moderador)**
  - acompanha torneios e disputas
  - decide o vencedor quando houver recusa do resultado
  - garante consistência e integridade do sistema

---

## 🧩 Regras Principais do Sistema
### Torneios (8 ou 16)
- O jogador escolhe se quer entrar em um torneio de **8** ou **16**.
- Existe sempre um torneio **ABERTO** para cada formato (quando necessário).
- Quando o torneio atinge **8/8** ou **16/16**:
  - o torneio é **fechado**
  - o sistema **gera o chaveamento**
  - o sistema **abre automaticamente outro torneio** do mesmo formato

### Resultados (registro + validação)
- Após a partida, **um dos jogadores** registra o placar.
- O adversário deve **confirmar** ou **recusar**:
  - **Confirmar** → resultado confirmado e o chaveamento avança
  - **Recusar** → abre disputa e o **Admin decide** e confirma o vencedor

### Rankings
- **Ranking por pontuação:** baseado em vitórias/derrotas (mesma pontuação definida no projeto).
- **Ranking de campeões:** contabiliza quantos torneios cada jogador venceu (títulos).

---

## ✅ Principais Funcionalidades (alto nível)
- Cadastro e autenticação de usuários (Jogador/Admin)
- Inscrição em torneios (8 ou 16)
- Fechamento automático e geração de chaveamento
- Criação e gestão de partidas
- Registro e validação de resultados (com disputa)
- Rankings por pontuação e por campeões
- Histórico de torneios e partidas

---

## 🛠️ Tecnologias
### Linguagem
- **Python**

### Framework
- **FastAPI**
  - API REST rápida e moderna
  - documentação automática via Swagger (OpenAPI)
  - validação de dados com Pydantic

---

## 📦 Estrutura (sugestão inicial)
> A estrutura pode evoluir durante a disciplina.

