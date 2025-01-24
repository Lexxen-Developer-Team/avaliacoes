# ![Logo da Empresa](https://lexxen.com/Group%201000001771.svg)

# Desafio Prático - Desenvolvedor Laravel - Sênior

Seja bem-vindo(a)! Esta prova foi criada para avaliar suas habilidades técnicas e seu conhecimento em boas práticas de desenvolvimento. Leia atentamente os requisitos abaixo e siga as instruções para entrega. Boa sorte!

---

### Cenário: Sistema de Gerenciamento de Projetos

Você foi contratado para desenvolver uma API RestFull para gerenciar projetos e suas respectivas tarefas. Os requisitos são os seguintes:

## Requisitos Funcionais

### 1. Gerenciamento de Usuários
- Somente usuários com perfil de administrador podem criar novos usuários.  
- Todos os usuários devem ser autenticados para acessar qualquer funcionalidade da API.  
- A autenticação deve ser realizada utilizando **Laravel Sanctum**.

### 2. Gerenciamento de Projetos
- Cada projeto deve conter os seguintes atributos:
  - **Título** (obrigatório).
  - **Descrição** (opcional).
  - **Status**: ativo ou inativo (obrigatório).
  - **Data de Início** (obrigatória).
  - **Data de Término** (opcional).
- Um projeto pode ser atribuído a um ou mais usuários cadastrados.
- Apenas usuários autenticados podem criar, editar ou excluir projetos.
- Quando finalizado, dispare um e-mail para os membros do projeto informando.

### 3. Gerenciamento de Tarefas
- Cada tarefa deve ser vinculada a um único projeto.
- As tarefas devem conter os seguintes atributos:
  - **Título** (obrigatório).
  - **Descrição** (opcional).
  - **Status**: pendente, em progresso ou concluída (obrigatório).
  - **Data Limite** (opcional).
- Usuários podem alterar o status de uma tarefa para "concluída".
- Apenas usuários atribuídos ao projeto correspondente podem gerenciar as tarefas associadas.

### 4. Filtros e Busca
- **Projetos:**
  - Permitir listar projetos aplicando filtros por:
    - Status (ativo/inativo).
    - Data de Início.
    - Usuários atribuídos.
- **Tarefas:**
  - Permitir listar tarefas aplicando filtros por:
    - Status (pendente, em progresso, concluída).
    - Data Limite.

### 5. Logs de Atividade
- Registrar logs para eventos importantes, incluindo:
  - Criação de novos projetos.
  - Atribuição ou remoção de usuários de projetos.
  - Alterações no status de tarefas (incluindo conclusão).
- Os logs devem incluir informações sobre:
  - Usuário responsável pela ação.
  - Data e hora do evento.

---

## Requisitos Não Funcionais

### 1. Tecnologia
- Utilize **Laravel 11**, API mode.
- O banco de dados deve ser **MySQL**, **Postgres** ou **SQLite**.
- Inclua uma documentação da API (pode ser no formato **Swagger**, **Postman**, **Insomnia** ou **Arquivos HTTP**).
- O código deve estar no **GitHub**.

---

## Entrega

### 1. Repositório
- O projeto deve ser enviado em um **repositório privado**.
- Adicionar um readme explicando o funcionamento do sistema.

#### 2. Data de entrega
- Deve ser publicado até 23:59 de segunda-feira ( 27/01/2024 )

### 3. Email para entrega
- Deve ser adicionados os seguintes emails ao repositório:
  - paulo.neto@lexxen.com
  - vitor.federle@lexxen.com
---

## Critérios de Avaliação

### 1. Qualidade do Código
- Estrutura, organização e legibilidade.
- Implementação de **testes automatizados** (unitários ou de integração).
- Uso de boas práticas do Laravel, como:
  - **Resources**.
  - **Policies**.
  - **Query Builders**.

### 2. Funcionalidades
- Aderência aos requisitos.
- Implementação de validações e tratamento de erros.

### 3. Performance e Escalabilidade
- Uso adequado de relacionamentos.
- Implementação de **cache** onde for relevante (e.g., listas frequentemente acessadas).

---

## Extra (Não Obrigatório)

- Crie um **Job** para arquivar projetos concluídos há mais de 30 dias:
  - Necessário marcação no banco para removê-los das listas de uso corriqueiro.
- Chame esse **Job** nas **Schedules** para executar automaticamente todos os dias.

