# API TypeScript Completa

API REST DESENVOLVIDA em TypeScript com Node.js, Express e MySQL.

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

## 🛠️ Passo a Passo - Criação do Projeto

### 1. Configuração Inicial
```bash
# Criar pasta do projeto
mkdir api-typescript
cd api-typescript

# Inicializar projeto Node.js
npm init -y

# Instalar dependências de produção
npm install express mysql2 cors dotenv bcrypt

# Instalar dependências de desenvolvimento
npm install -D @types/express @types/cors @types/node @types/bcrypt typescript ts-node nodemon

# Inicializar TypeScript
npx tsc --init
```

### 2. Estrutura de Pastas
```bash
# Criar estrutura de pastas
mkdir src
mkdir src/controller
mkdir src/model
mkdir src/interfaces
```

### 3. Configurar package.json
Adicionar script no package.json:
```json
{
  "scripts": {
    "dev": "nodemon --exec ts-node src/server.ts"
  }
}
```

### 4. Criar arquivo .env
```env
PORT=3000
HOST=localhost
USER=root
PASSWORD=sua_senha
DATABASE=db_loja
```

### 5. Arquivos Criados

#### src/interfaces/types.ts
```typescript
export interface IUser{
    email:string;
    password:string;
    nome:string;
    role:string;
    createAt:Date;
}

export interface IProduct{
    id:number;
    name:string;
    description:string;
    price:number;
    stock:number;
    createAt:Date;
    updateAt:Date;
}
```

#### src/model/connectionModule.ts
```typescript
import mysql from 'mysql2/promise';
import { config } from 'dotenv';
config();

export const connectionModule = mysql.createPool({
    host: process.env.HOST,
    user: process.env.USER,
    password: process.env.PASSWORD,
    database: process.env.DATABASE
});
```

#### src/model/userModel.ts
```typescript
import { IUser } from "../interfaces/types";
import { connectionModule } from "./connectionModule";

const getAll = async () => {
    const [listaUser] = await connectionModule.execute('SELECT * FROM User');
    return listaUser;
}

const getById = async (id: number) => {
    const [user] = await connectionModule.execute(`SELECT * FROM User WHERE id = ${id}`);
    return user;
}

const newUser = async (body: IUser) => {
    const { email, password, nome, role, createAt } = body;
    const query = 'INSERT INTO User (email, password, nome, role, createAt) VALUES (?, ?, ?, ?, ?)';
    const newU = await connectionModule.execute(query, [email, password, nome, role, createAt ?? new Date()]);
    return newU;
}

const editUser = async (id: number, body: IUser) => {
    const { email, password, nome, role } = body;
    const query = 'UPDATE User SET email = ?, password = ?, nome = ?, role = ? WHERE id = ?';
    const [edit] = await connectionModule.execute(query, [email, password, nome, role, id]);
    return edit;
}

const removeUser = async (id: number) => {
    const [user] = await connectionModule.execute(`DELETE FROM User WHERE id=${id}`);
    return user;
}

export default {
    getAll,
    getById,
    newUser,
    editUser,
    removeUser
}
```

#### src/controller/userController.ts
```typescript
import { Request, Response } from "express";
import userModel from "../model/userModel";
import bcrypt from "bcrypt";

const getAll = async (req: Request, res: Response) => {
    const listaUser = await userModel.getAll();
    return res.status(200).json(listaUser);
}

const getById = async (req: Request, res: Response) => {
    const id = req.params.id;
    const user = await userModel.getById(Number(id));
    return res.status(200).json(user);
}

const newUser = async (req: Request, res: Response) => {
    const { password, ...userData } = req.body;
    const hashedPassword = await bcrypt.hash(password, 10);
    const newU = await userModel.newUser({ ...userData, password: hashedPassword });
    return res.status(201).json(newU);
}

const editUser = async (req: Request, res: Response) => {
    const id = Number(req.params.id);
    const edit = await userModel.editUser(id, req.body);
    return res.status(200).json(edit);
}

const removeUser = async (req: Request, res: Response) => {
    const id = Number(req.params.id);
    const user = await userModel.removeUser(id);
    return res.status(200).json({ message: 'Usuário removido com sucesso' });
}

export default {
    getAll,
    getById,
    newUser,
    editUser,
    removeUser
}
```

#### src/router.ts
```typescript
import productController from './controller/productController';
import userController from './controller/userController';
import express from "express";

export const router = express.Router()

// Rotas de produtos
router.get('/', productController.getAll)
router.get('/produto/:id', productController.getById)
router.post('/produto', productController.NewProduct)
router.put('/:produto/:id', productController.editProduct)
router.patch('/:produto/:id', productController.editPartial)
router.delete('/:produto/:id', productController.removeProduct)

// Rotas de usuários
router.get('/usuario', userController.getAll)
router.get('/usuario/:id', userController.getById)
router.post('/usuario', userController.newUser)
router.put('/usuario/:id', userController.editUser)
router.delete('/usuario/:id', userController.removeUser)
```

#### src/app.ts
```typescript
import { router } from "./router"
import express from "express";
import cors from "cors";

export const app = express()

app.use(express.json())
app.use(cors())
app.use('/api', router)
```

#### src/server.ts
```typescript
import {app}  from "./app";
import {config} from 'dotenv'
config()

app.listen(process.env.PORT, () => {
    console.log(`O servidor esta rodando na porta : ${process.env.PORT}`)
})
```

## 📊 Banco de Dados

### Criar Banco e Tabelas
```sql
-- Criar banco
CREATE DATABASE db_loja;
USE db_loja;

-- Tabela Product
CREATE TABLE Product (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    price DECIMAL(10,2) NOT NULL,
    stock INT NOT NULL,
    createAt DATETIME DEFAULT CURRENT_TIMESTAMP,
    updateAt DATETIME DEFAULT CURRENT_TIMESTAMP
);

-- Tabela User
CREATE TABLE User (
    id INT AUTO_INCREMENT PRIMARY KEY,
    email VARCHAR(255) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    nome VARCHAR(255) NOT NULL,
    role VARCHAR(50) NOT NULL,
    createAt DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

## 🚀 Executar o Projeto

```bash
# Instalar dependências
npm install

# Executar em modo desenvolvimento
npm run dev
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