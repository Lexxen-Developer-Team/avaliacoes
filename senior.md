# Lexxen

**Desafio Prático - Desenvolvedor Laravel Senior**

✨ Seja muito bem-vindo(a)!
Este desafio foi criado para conhecer suas habilidades com Laravel, boas práticas de desenvolvimento, pensamento assíncrono e otimização de banco de dados.

Leia os requisitos com atenção e sinta-se à vontade para caprichar na sua solução. Estamos ansiosos para ver o melhor do seu código. Boa sorte e divirta-se no processo! 🚀

---

## **1. Usuários**

- CRUD de usuários, pessoa física e pessoa jurídica.
- Separar pessoas físicas de jurídicas no banco.
- Cada usuário possui uma ou mais contas bancárias.
- O usuário deve iniciar em **“aguardando aprovação”**.
- Criar um **Job/Command** para aprovar ou reprovar usuários em lote.
- Usuários reprovados **não podem efetuar login**.
- Implementar **validação de duplicidade de e-mail** com regra no banco (único) e tratamento adequado no código.
- O usuário nunca pode ser excluída permanentemente.

---

## **2. Contas**

- Atributos: `número`, `status (ATIVA, BLOQUEADA)`.
- O saldo da conta é a soma dos saldos das carteiras.
- Criar conta com saldo inicial = **0**.
- Apenas usuários **aprovados e ativos** podem movimentar.
- Implementar **otimização para evitar N+1 queries**:
  - Exemplo: ao listar contas de um usuário, evitar múltiplos SELECTs para buscar extratos ou transferências relacionadas.

---

## **3. Carteiras**
- Atributos: `name`, `balance`, `type (DEFAULT, WALLET)`, `status (ATIVA, DESATIVA)`.
- Deve conter *polimorfismo (morth)* para usuários PJ e PF.
- Uma carteira desativada não pode receber dinheiro.
- Para desabilidar uma carteira, deve-se remover todo saldo antes.
- A carteira nunca pode ser excluída permanentemente.
- A carteira do tipo `DEFAULT` deve ser criado na aprovação do usuário.
- O usuário não pode criar carteiras do tipo `DEFAULT`.

---

## **4. Transferências**

- Usuários podem transferir dinheiro entre suas próprias carteiras e para contas de outros usuários.
- **Regras**:
  - Ao enviar para uma conta de terceiros, deve-se por o saldo na carteira `DEFAULT` do recebedor.
  - Conta bloqueada não pode transferir.
  - Carteiras desabilitadas não podem transferir ou receber.
  - Saldo insuficiente → deve lançar exceção de negócio.
  - Operação deve ser **transacional** (não permitir saldo negativo em concorrência).
  - Implementar **Job assíncrono** para processar transferências (simular fila de processamento).
  - Evitar **duplicidade de processamento** em transferências (usar `idempotency key` ou outro mecanismo para garantir que a mesma operação não seja executada duas vezes).

---

## **5. Extrato**

- Criar um **Observer** para `Transfer`.
- Após cada transferência concluída, registrar no **Extrato**:
  - Conta origem, conta destino, valor, data/hora, saldo após operação.
- Extrato deve ser paginado e filtrável por período (`start_date`, `end_date`).
- Deve suportar busca de extratos por usuário **sem gerar consultas duplicadas** (exemplo: eager loading).
- O extrato é vinculado a uma carteira.

---

## **6. Requisitos Não Funcionais**

- API RestFull.
- Laravel 11 ou 12 (modo API).
- Banco de dados: PostgreSQL ou MySQL.
- Autenticação com Sanctum.
- Estrutura limpa com `Migrations`, `Models`, `Seeders`, `Factories`.
- Usar **cache** em pelo menos uma consulta crítica (exemplo: saldo ou extrato agregado).
- Evitar SQL redundante em consultas de alto volume (demonstre uso de `withCount`, `join`, ou cache).
- Deve-se utilizar DTOs no recebimento da requests.
- Deve-se utilizar Resources nas devoluções da API.
- O controller não pode resolver RN, apenas manipulações no Eloquent (query/paginacões).
- Deve-se utilizar Gate para poder garantir segurança.

---

## **7. Arquitetura**

Deve-se utilizar **Docker** para resolver o modelo de arquitetura:

#### Componentes

- **NGINX (balanceador):** distribui requisições entre os pods do app.
- **Laravel App Pods (APP1…APPN):** instâncias idênticas e stateless.
- **Redis Cluster:** cache, sessões e locks distribuídos.
- **PostgreSQL (Write):** banco transacional principal.
- **Service MSEX:** serviço especializado que grava dados no MongoDB.
- **MongoDB:** armazenamento de documentos/eventos.

#### Fluxos

- **Síncrono (HTTP):**
  - Cliente → NGINX → APP
  - APP → Redis / Postgres

- **Assíncrono (Fila):**
  - APP → Message Broker (do Laravel) → Postgres

#### Diagrama

![](/assets/arquitetura-senior.png)
- Disponível em `/assets/arquitetura-senior.php`

---

## **8. Testes Automatizados**

Cobrir pelo menos:

- Aprovação de usuário (Job funcionando).
- Transferência realizada com sucesso:
  -	Entre carteiras do mesmo usuário.
	-	Da carteira do usuário para uma conta de outro usuário.
- Saldo insuficiente.
- Conta bloqueada.
- Transferência duplicada não deve ser processada duas vezes.
- Extrato registrado corretamente.
- Garantir que queries de extrato não façam N+1.

---

# 🔹 Cenários de Teste

### **1. Aprovação de Usuários**

- Criar 10 usuários em massa via Factory.
- Executar o Job de aprovação:
  - 5 devem ser aprovados.
  - 3 reprovados.
  - 2 continuam aguardando.
- Garantir que apenas aprovados conseguem logar.
- Validar que não existam duplicidades de e-mail.

---

### **2. Transferência Concorrente**

- Criar duas contas A e B, ambas com saldo de `1000`.
- Disparar **5 Jobs assíncronos simultâneos** de transferência de A → B no valor de `300` cada.
- Esperado:
  - Apenas **3 transferências devem ser processadas com sucesso**.
  - As outras 2 devem falhar por saldo insuficiente.
  - Saldo final da conta A = `100`.
  - Saldo final da conta B = `1900`.

---

### **3. Transferência Duplicada (Idempotência)**

- Criar uma transferência A → B de `200` com uma `idempotency_key`.
- Executar **o mesmo Job 2 vezes seguidas**.
- Esperado:
  - O sistema deve processar apenas **uma transferência real**.
  - O extrato não deve conter duplicidade.
  - O saldo final deve refletir apenas **1 débito**.

---

### **4. Conta Bloqueada**

- Conta A com saldo `500`.
- Conta A é bloqueada.
- Tentativa de transferência A → B `100`.
- Esperado:
  - Operação negada com mensagem adequada.
  - Nenhuma alteração no saldo.
  - Nenhum registro de extrato criado.

---

### **5. Extrato Paginado e Filtrado**

- Criar 50 transferências A ↔ B em datas diferentes.
- Consultar o extrato de A filtrando por `start_date` e `end_date` (últimos 7 dias).
- Esperado:
  - Apenas transferências dentro do período retornam.
  - Extrato deve estar **ordenado por data decrescente**.
  - Respeitar paginação (`per_page=10`).

---

### **6. Performance (N+1 Queries)**

- Criar 5 usuários, cada um com 5 contas, cada conta com 50 transferências.
- Consultar o endpoint `/users/{id}/accounts?with=extrato`.
- Esperado:
  - A query não deve executar **250 SELECTs extras** (problema de N+1).
  - Deve usar eager loading (`with`, `withCount` ou `join`).
  - O log de queries deve mostrar **poucas consultas otimizadas**.

---

### **7. Cache de Saldo**

- Criar conta com saldo `1000`.
- Transferir `200`.
- Esperado:
  - O saldo atualizado deve ser retornado **imediatamente** da cache.
  - Se a cache for invalidada, o saldo deve ser recalculado do banco corretamente.

---

### **8. Job de Aprovação em Massa com Erro**

- Criar 20 usuários aguardando aprovação.
- Rodar Job para aprovar todos.
- No meio do processo, simular erro (lançar exceção proposital na 10ª aprovação).
- Esperado:
  - Nenhum usuário deve ficar aprovado parcialmente.
  - A transação deve **rollbackar**.
  - Todos permanecem aguardando aprovação.

---

## **Entrega**

- Incluir README com:
  - Passos para rodar a aplicação.
  - Passos para rodar os testes.
  - Se possível, incluir Docker.
