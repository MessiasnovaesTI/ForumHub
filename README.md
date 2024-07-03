# ForumHub
Praticando Spring Framework: Challenge Fórum Hub

Para desenvolver o Fórum Hub, vamos criar uma aplicação que gerencie usuários, tópicos e cursos. A seguir, um esboço das principais funcionalidades e endpoints necessários, incluindo autenticação e autorização.

### Tecnologias
- Backend: Node.js com Express
- Banco de dados: MongoDB
- Autenticação: JWT (JSON Web Token)

### Endpoints

1. **Autenticação**
   - **POST /auth/register**: Registro de novo usuário
   - **POST /auth/login**: Login do usuário e geração de token JWT

2. **Usuários**
   - **GET /users**: Listar todos os usuários (restrito a administradores)
   - **GET /users/:id**: Obter informações de um único usuário (autenticado)

3. **Tópicos**
   - **GET /topics**: Listar todos os tópicos
   - **GET /topics/:id**: Obter um tópico específico
   - **POST /topics**: Criar um novo tópico (autenticado)
   - **PUT /topics/:id**: Atualizar um tópico (autenticado, autor do tópico)
   - **DELETE /topics/:id**: Deletar um tópico (autenticado, autor do tópico)

4. **Cursos**
   - **GET /courses**: Listar todos os cursos
   - **GET /courses/:id**: Obter um curso específico

### Regras de Negócio
- Apenas usuários cadastrados podem criar, atualizar e deletar tópicos.
- Um tópico pertence a um curso.
- A data de criação do tópico deve ser gerada automaticamente no backend.
- Apenas o autor do tópico pode atualizar ou deletar o tópico.

### Modelo de Dados (MongoDB)

#### Usuário
```json
{
  "username": "string",
  "email": "string",
  "password": "string" // armazenado de forma segura (hash)
}
```

#### Tópico
```json
{
  "title": "string",
  "message": "string",
  "course": "string", // ID do curso
  "author": "string", // ID do usuário autor
  "createdAt": "date"
}
```

#### Curso
```json
{
  "name": "string",
  "description": "string"
}
```

### Implementação

#### 1. Autenticação
- Registro de novo usuário
- Login do usuário e geração de token JWT

#### 2. Tópicos
- Criar, listar, atualizar e deletar tópicos

#### 3. Cursos
- Listar e obter cursos

### Exemplos de Endpoints

#### Registro de Usuário
```http
POST /auth/register
Content-Type: application/json

{
  "username": "john_doe",
  "email": "john@example.com",
  "password": "securePassword123"
}
```

#### Login de Usuário
```http
POST /auth/login
Content-Type: application/json

{
  "email": "john@example.com",
  "password": "securePassword123"
}
```

#### Criar Tópico
```http
POST /topics
Content-Type: application/json
Authorization: Bearer <token>

{
  "title": "Me ajuda nessa dúvida",
  "message": "Estou com dúvida",
  "course": "Java"
}
```

#### Atualizar Tópico
```http
PUT /topics/:id
Content-Type: application/json
Authorization: Bearer <token>

{
  "title": "Dúvida solucionada",
  "message": "Não tenho mais dúvida"
}
```

### Segurança
- Utilizar middleware para verificar tokens JWT em endpoints protegidos.
- Verificar se o usuário é o autor do tópico antes de permitir atualização ou deleção.

### Organização do Projeto
- Utilizar Trello para gerenciar tarefas e progresso.
- Criar um README detalhado explicando como configurar e utilizar a aplicação.

### Exemplos de Código

#### Middleware de Autenticação
```javascript
const jwt = require('jsonwebtoken');

const authMiddleware = (req, res, next) => {
  const token = req.headers['authorization'].split(' ')[1];
  if (!token) return res.status(403).send('Token é necessário.');

  jwt.verify(token, process.env.JWT_SECRET, (err, user) => {
    if (err) return res.status(401).send('Token inválido.');
    req.user = user;
    next();
  });
};

module.exports = authMiddleware;
```

#### Rota para Criar Tópico
```javascript
app.post('/topics', authMiddleware, async (req, res) => {
  const { title, message, course } = req.body;
  const newTopic = new Topic({
    title,
    message,
    course,
    author: req.user.id,
    createdAt: new Date()
  });
  await newTopic.save();
  res.status(201).send(newTopic);
});
```

Com essas orientações e exemplos, é possível começar a desenvolver o Fórum Hub, garantindo que as funcionalidades estejam protegidas por autenticação e que somente usuários autorizados possam criar, atualizar e deletar tópicos.
