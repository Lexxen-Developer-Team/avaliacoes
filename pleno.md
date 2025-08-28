## Lexxen
# Desafio Prático - Desenvolvedor Laravel - Pleno

Seja bem-vindo(a)! Esta prova foi criada para avaliar suas habilidades técnicas e seu conhecimento em boas práticas de desenvolvimento. Leia atentamente os requisitos abaixo e siga as instruções para entrega. Boa sorte!

---

### 1. Usuários
- CRUD de usuários.
- Cada usuário pode ter **uma conta bancária**.
- Usuário deve iniciar em 'aguardando aprovação'.
- Deve haver um comando para 'aprovar ou reprovar o usuário'. 
- Usuário reprovado não pode efetuar login.
  
### 2. Contas
- Atributos: número, saldo, status (`ATIVA`, `BLOQUEADA`).
- Criar conta com saldo inicial = 0.
- Apenas usuários ativos podem movimentar.

### 3. Transferências
- Usuários podem transferir dinheiro entre **suas próprias contas** ou para contas de outros usuários.
- Regras:
  - Não pode transferir se a conta estiver bloqueada.
  - Não pode transferir se saldo insuficiente.
- A operação deve ser **transacional** (não deixar saldo negativo).

### 4. Extrato (Observer)
- Criar um **Observer** para `Transfer` que, após cada transferência concluída, registre automaticamente no **Extrato da conta**:
  - Conta origem, conta destino, valor, data/hora, saldo após operação.
- O extrato deve poder ser listado por usuário.

---

## Requisitos Não Funcionais
- Ser uma **API RestFull**.
- **Laravel 11 ou 12** (api mode), banco **Postgres/MySQL/SQLite**.
- **Sanctum** para autenticação.
- **Migrations, Models, Seeders, Factories** organizados.
- **Testes automatizados** cobrindo:
  - Transferência com sucesso.
  - Saldo insuficiente.
  - Conta bloqueada.

---

## Entrega
- Enviar em um **repositório privado** no GitHub.
- Incluir **README** com instruções para rodar:
