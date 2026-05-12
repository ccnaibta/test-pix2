# 💰 PIX Payment API - Node.js 18

Aplicação simples de recebimento de valores via PIX com arquitetura limpa e separação clara de responsabilidades.

## 🎯 Características

✅ **Arquitetura em Camadas** - Routes → Controllers → Services → Database  
✅ **Sem Duplicação de Código** - Injeção de dependências com Singleton  
✅ **Validações Centralizadas** - No Service Layer  
✅ **ES Modules Nativo** - Node.js 18+ com `import/export`  
✅ **Mock Database** - Armazenamento em memória (Map)  
✅ **Clean Code** - Métodos com responsabilidade única  

## 📁 Estrutura do Projeto

```
src/
├── index.js                    # Entry point - Express server
├── database/
│   └── pixDatabase.js          # Mock database (Singleton)
├── services/
│   └── pixService.js           # Lógica de negócio e validações
├── controllers/
│   └── pixController.js        # Gerenciamento de requisições HTTP
└── routes/
    └── pixRoutes.js            # Definição de endpoints
```

## 🚀 Como Instalar e Rodar

### Pré-requisitos
- Node.js 18+

### Instalação
```bash
npm install
```

### Executar em Desenvolvimento
```bash
npm run dev
```

### Executar em Produção
```bash
npm start
```

A aplicação iniciará em `http://localhost:3000`

## 💰 Endpoints

### 1️⃣ POST `/pix/receive` - Receber PIX
Recebe um pagamento PIX e atualiza o saldo.

**Request:**
```bash
curl -X POST http://localhost:3000/pix/receive \
  -H "Content-Type: application/json" \
  -d '{
    "amount": 150.50,
    "originKey": "chave@pix.com",
    "description": "Pagamento cliente A"
  }'
```

**Response (201 Created):**
```json
{
  "success": true,
  "message": "PIX recebido com sucesso",
  "data": {
    "id": 1,
    "amount": 150.50,
    "originKey": "chave@pix.com",
    "description": "Pagamento cliente A",
    "timestamp": "2026-05-12T14:30:00.000Z",
    "type": "RECEIVE"
  },
  "timestamp": "2026-05-12T14:30:00.000Z"
}
```

### 2️⃣ GET `/pix/balance` - Consultar Saldo
Retorna o saldo atual.

**Request:**
```bash
curl http://localhost:3000/pix/balance
```

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "balance": 150.50,
    "currency": "BRL",
    "lastUpdate": "2026-05-12T14:30:00.000Z"
  },
  "timestamp": "2026-05-12T14:30:00.000Z"
}
```

### 3️⃣ GET `/pix/transactions` - Listar Transações
Retorna todas as transações realizadas.

**Request:**
```bash
curl http://localhost:3000/pix/transactions
```

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "transactions": [
      {
        "id": 1,
        "amount": 150.50,
        "originKey": "chave@pix.com",
        "description": "Pagamento cliente A",
        "timestamp": "2026-05-12T14:30:00.000Z",
        "type": "RECEIVE"
      }
    ],
    "totalCount": 1,
    "totalReceived": 150.50,
    "lastUpdate": "2026-05-12T14:30:00.000Z"
  },
  "timestamp": "2026-05-12T14:30:00.000Z"
}
```

### 4️⃣ GET `/pix/transactions/:id` - Detalhe da Transação
Retorna os detalhes de uma transação específica.

**Request:**
```bash
curl http://localhost:3000/pix/transactions/1
```

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "id": 1,
    "amount": 150.50,
    "originKey": "chave@pix.com",
    "description": "Pagamento cliente A",
    "timestamp": "2026-05-12T14:30:00.000Z",
    "type": "RECEIVE"
  },
  "timestamp": "2026-05-12T14:30:00.000Z"
}
```

### 5️⃣ GET `/health` - Status da API
Verifica se a API está online.

**Request:**
```bash
curl http://localhost:3000/health
```

**Response (200 OK):**
```json
{
  "status": "ok",
  "timestamp": "2026-05-12T14:30:00.000Z"
}
```

## ⚠️ Tratamento de Erros

A API retorna erros com status HTTP apropriados:

**Erro de Validação (400 Bad Request):**
```json
{
  "error": "Campo \"amount\" é obrigatório e deve ser um número",
  "timestamp": "2026-05-12T14:30:00.000Z"
}
```

**Transação Não Encontrada (404 Not Found):**
```json
{
  "error": "Transação 999 não encontrada",
  "timestamp": "2026-05-12T14:30:00.000Z"
}
```

**Erro Interno do Servidor (500):**
```json
{
  "error": "Erro ao processar requisição",
  "timestamp": "2026-05-12T14:30:00.000Z"
}
```

## 🏗️ Arquitetura e Separação de Responsabilidades

### 📍 Database Layer (`pixDatabase.js`)
- Gerencia armazenamento em memória
- Métodos: `addTransaction()`, `getBalance()`, `getTransactions()`, `getTransactionById()`
- **Padrão**: Singleton para garantir única instância

### 📍 Service Layer (`pixService.js`)
- Contém lógica de negócio e validações
- Valida dados de entrada
- Processa transações
- Retorna erros com status HTTP apropriados
- **Padrão**: Singleton para reutilização

### 📍 Controller Layer (`pixController.js`)
- Gerencia requisições HTTP
- Chama métodos do service
- Formata respostas JSON
- Passa erros para middleware de tratamento
- Todos os métodos são estáticos (stateless)

### 📍 Routes Layer (`pixRoutes.js`)
- Define os endpoints REST
- Mapeia rotas para controllers
- Usa Express Router para modularização

### 📍 Main Application (`index.js`)
- Configura Express
- Define middlewares
- Monta rotas
- Inicia servidor
- Middleware centralizado de tratamento de erros

## 🔒 Princípios Aplicados

### ✅ Injeção de Dependências
- Database e Service são importados como singletons
- Evita duplicação e garante coesão

### ✅ Responsabilidade Única
- Cada módulo tem um propósito bem definido
- Sem mistura de responsabilidades

### ✅ DRY (Don't Repeat Yourself)
- Validações centralizadas no service
- Tratamento de erros centralizado

### ✅ Clean Code
- Nomes descritivos
- Documentação JSDoc
- Métodos concizos

## 📊 Fluxo de uma Requisição

```
POST /pix/receive
    ↓
pixRoutes.js (define rota)
    ↓
pixController.receivePix() (recebe requisição)
    ↓
pixService.receivePixPayment() (valida e processa)
    ↓
pixDatabase.addTransaction() (persiste em mock)
    ↓
Response 201 Created (retorna transação)
```

## 🧪 Exemplos de Uso

### Receber 3 PIX
```bash
curl -X POST http://localhost:3000/pix/receive \
  -H "Content-Type: application/json" \
  -d '{"amount": 100, "originKey": "joao@pix.com", "description": "Aluguel"}'

curl -X POST http://localhost:3000/pix/receive \
  -H "Content-Type: application/json" \
  -d '{"amount": 250.75, "originKey": "maria@pix.com", "description": "Freelance"}'

curl -X POST http://localhost:3000/pix/receive \
  -H "Content-Type: application/json" \
  -d '{"amount": 50, "originKey": "pedro@pix.com"}'
```

### Ver Saldo
```bash
curl http://localhost:3000/pix/balance
```

### Ver Transações
```bash
curl http://localhost:3000/pix/transactions
```

### Ver Transação Específica
```bash
curl http://localhost:3000/pix/transactions/2
```

## 📝 Notas

- Os dados são armazenados em memória e serão perdidos quando a aplicação reiniciar
- A aplicação usa ES Modules nativo do Node.js 18+
- Todas as respostas incluem `timestamp` para rastreamento
- Validações robustas previnem dados inválidos

## 📄 Licença

MIT

---

**Desenvolvido com ❤️ em Node.js 18**
