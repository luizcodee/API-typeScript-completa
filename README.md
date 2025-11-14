# API TypeScript Completa

API REST desenvolvida em TypeScript com Node.js, Express e MySQL.

## 🚀 Tecnologias

- **Node.js** - Runtime JavaScript
- **TypeScript** - Superset do JavaScript
- **Express** - Framework web
- **MySQL2** - Banco de dados
- **bcrypt** - Criptografia de senhas
- **CORS** - Cross-Origin Resource Sharing

## 📋 Funcionalidades

### Produtos
- ✅ Listar todos os produtos
- ✅ Buscar produto por ID
- ✅ Criar novo produto
- ✅ Editar produto completo
- ✅ Editar produto parcial
- ✅ Deletar produto

### Usuários
- ✅ Listar todos os usuários
- ✅ Buscar usuário por ID
- ✅ Criar novo usuário (senha criptografada)
- ✅ Editar usuário
- ✅ Deletar usuário

## 🛠️ Instalação

1. Clone o repositório
```bash
git clone https://github.com/luizcodee/API-typeScript-completa.git
```

2. Instale as dependências
```bash
npm install
```

3. Configure o banco de dados no arquivo `.env`
```env
PORT=3000
HOST=localhost
USER=root
PASSWORD=sua_senha
DATABASE=db_loja
```

4. Execute o servidor
```bash
npm run dev
```

## 📊 Banco de Dados

### Tabela Product
```sql
CREATE TABLE Product (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    price DECIMAL(10,2) NOT NULL,
    stock INT NOT NULL,
    createAt DATETIME DEFAULT CURRENT_TIMESTAMP,
    updateAt DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

### Tabela User
```sql
CREATE TABLE User (
    id INT AUTO_INCREMENT PRIMARY KEY,
    email VARCHAR(255) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    nome VARCHAR(255) NOT NULL,
    role VARCHAR(50) NOT NULL,
    createAt DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

## 🔗 Endpoints

### Produtos
- `GET /api/` - Listar produtos
- `GET /api/produto/:id` - Buscar produto
- `POST /api/produto` - Criar produto
- `PUT /api/:produto/:id` - Editar produto
- `PATCH /api/:produto/:id` - Editar parcial
- `DELETE /api/:produto/:id` - Deletar produto

### Usuários
- `GET /api/usuario` - Listar usuários
- `GET /api/usuario/:id` - Buscar usuário
- `POST /api/usuario` - Criar usuário
- `PUT /api/usuario/:id` - Editar usuário
- `DELETE /api/usuario/:id` - Deletar usuário

## 📝 Exemplo de Uso

### Criar Usuário
```json
POST /api/usuario
{
  "email": "usuario@exemplo.com",
  "password": "senha123",
  "nome": "João Silva",
  "role": "user"
}
```

### Criar Produto
```json
POST /api/produto
{
  "name": "Produto Exemplo",
  "description": "Descrição do produto",
  "price": 29.99,
  "stock": 100
}
```

## 🔒 Segurança

- Senhas são criptografadas com bcrypt
- Prepared statements para prevenir SQL Injection
- CORS configurado para requisições cross-origin