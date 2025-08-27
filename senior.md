# Lexxen  
## Desafio Prático - Desenvolvedor Laravel - Sênior  

Seja bem-vindo(a)! Este desafio foi criado para avaliar não apenas sua capacidade de programar em Laravel, mas também sua **maturidade técnica**, **visão de arquitetura** e **boas práticas de engenharia de software**. Considere que esta aplicação deve ser pensada como se fosse utilizada em **produção real**.  

---

## 1. Usuários & Contas  
- Implementar **CRUD de usuários**.  
- Cada usuário deve possuir **uma conta bancária vinculada**.  
- Usuário inicia em `waiting_approval`.
  - Status: `waiting_approval`, `approved`, `reproved`. 
- Criar **comando e fluxo de aprovação/reprovação**:  
  - Usuário reprovado não pode logar.  
  - Usuário aprovado gera automaticamente uma conta bancária com **número único** e saldo inicial = 0.  
- A conta deve ter atributos: `account`, `balance`, `status` (`ATIVA`, `BLOQUEADA`).  
- Apenas usuários aprovados podem movimentar.  

---

## 2. Transferências  
- Usuários podem transferir entre suas próprias contas ou para contas de outros usuários.  
- Regras:  
  - Não pode transferir se a conta estiver bloqueada.  
  - Não pode transferir com saldo insuficiente.  
  - Operação deve ser **transacional** e **idempotente** (não permitir duplicidade em caso de reprocessamento).  
- Implementar camada de **Orquestrador de Transações** (não direto no Controller)(etapa 3).  
- As transferências devem ter **logs detalhados** de auditoria (quem iniciou, valores antes/depois, status).  

---

## 3. Extrato (Observer & Eventos)  
- Criar um **Observer** ou **Domain Event** para `Transfer`, que registre automaticamente no extrato:  
  - Conta origem, conta destino, valor, data/hora, saldo após operação.  
- O extrato deve poder ser consultado **com paginação e filtros**:  
  - Por período.  
  - Por tipo (`entrada`/`saída`).  
- O modelo de extrato deve estar otimizado para **grandes volumes de dados**.  

---

## 4. Segurança & Boas Práticas  
- Autenticação com **Laravel Sanctum**.  
- Todas as rotas devem exigir autenticação e autorização adequada (policy).  .  
- Respostas padronizadas em JSON (`success`, `data`, `errors`).  

---

## 5. Testes Automatizados  
Cobertura mínima:  
- Fluxo completo de transferência bem-sucedida.  
- Transferência com saldo insuficiente.  
- Conta bloqueada.  
- Reprocessamento (idempotência).  
- Consulta de extrato com filtros.  

Diferenciais:  
- Testes de **integração** validando transações no banco.  
- Testes de **concorrência** simulando múltiplas transferências simultâneas.  

---

## Requisitos Não Funcionais  
- **Laravel 11 ou 12**, banco **Postgres**.  
- Uso de **Migrations, Seeders, Factories**.  
- **Docker** ou instruções de setup para rodar facilmente.  
- Código organizado com **PSR-12** e boas práticas de DDD e SOLID.  
- README explicando:  
  - Setup do projeto.  
  - Fluxos principais.  
  - Decisões arquiteturais.  

---

## Entrega  
- Enviar em **repositório privado no GitHub**.  
- Conceder acesso à avaliação.  
- README com instruções claras de execução.  
