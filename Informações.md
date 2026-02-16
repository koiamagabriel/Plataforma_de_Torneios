## 👥 Perfis de Usuário
- **Jogador**
  - cadastra-se
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
- **Ranking por pontuação:** baseado em vitórias/derrotas.
- **Ranking de campeões:** contabiliza quantos torneios cada jogador venceu (títulos).

---

## ✅ Principais Funcionalidades (alto nível)
- Cadastro de usuários (Jogador/Admin)
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
