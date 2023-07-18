# 📝💡 Projeto LabEddit

O LabEddit é um projeto Full Stack que visa fornecer uma API e um website para gerenciar posts e comentários. 

## No Back-end 

Link repositório: https://github.com/MelMorette/projeto-labeddit-back-end

Foi desenvolvido utilizando Node.js e TypeScript, juntamente com o framework Express. A API utiliza um banco de dados SQL, especificamente SQLite, e o acesso a ele é feito através da biblioteca Knex.

O projeto segue uma arquitetura em camadas, o que proporciona uma separação clara de responsabilidades e facilita a manutenção e escalabilidade do sistema. Além disso, são utilizados conceitos de Programação Orientada a Objetos (POO) para organizar o código.

A autenticação e autorização são parte fundamental da API. A identificação dos usuários é feita utilizando UUID (Universally Unique Identifier), garantindo que cada usuário tenha um identificador único. As senhas dos usuários são armazenadas de forma segura com a técnica de hash utilizando Bcrypt. Para a autenticação dos usuários, é utilizado um sistema de tokens JWT (JSON Web Token), que são emitidos no momento do login e devem ser incluídos nas requisições protegidas para garantir o acesso adequado aos endpoints.

O projeto inclui também a definição de roles de usuário, sendo possível atribuir aos usuários a role de "NORMAL" ou "ADMIN". O payload do token JWT contém informações como o id do usuário, o nome e a role.

#### Conteúdos abordados no Back-end
- NodeJS
- Typescript
- Express
- SQL e SQLite
- Knex
- POO
- Arquitetura em camadas
- Geração de UUID
- Geração de hashes
- Autenticação e autorização
- Roteamento
- Postman

## No Front-end

O projeto de Front-End foi desenvolvido utilizando ReactJS como biblioteca principal para a construção da interface do usuário. O layout visual da aplicação foi criado no Figma, essa referência visual do Figma serviu como guia para estilização e organização dos elementos gráficos na aplicação.

Além disso, para facilitar a estilização e tornar a aplicação visualmente agradável, o projeto fez uso do Bootstrap, uma biblioteca de estilos e componentes CSS pré-definidos que aceleram o desenvolvimento e proporcionam uma aparência consistente.

A interação com o Back-End foi possível graças à utilização do Knex, uma biblioteca SQL query builder que facilita a comunicação com o banco de dados SQLite. O Knex permitiu o acesso e manipulação dos dados armazenados no banco de forma simplificada e segura.

Com essa combinação de tecnologias e utilitários, o Front-End conseguiu entregar uma experiência de usuário responsiva e esteticamente agradável, respeitando o layout definido no Figma e interagindo de forma eficiente com o Back-End através do Knex. O ReactJS, por sua vez, proporcionou uma estrutura modular e componentizada, permitindo a criação de uma aplicação escalável e de fácil manutenção. O Bootstrap agilizou o processo de estilização, garantindo uma aparência consistente e profissional em toda a aplicação.

#### Conteúdos abordados no Front-end
- Layout do Figma: https://www.figma.com/file/Byakv89sjTqI6NG2NRAAKJ/Projeto-Integrador-Labeddit?node-id=0%3A1&t=haX9j5M0lHbjWnAr-0
- ReactJS
- Knex
- Bootstrap

# 🏦 Banco de dados
![Diagrama do Banco de Dados](https://i.imgur.com/vNX4V1e.png)

# Token payload e User roles
O enum de roles e o payload do token JWT devem estar no seguinte formato:
```typescript
export enum USER_ROLES {
    NORMAL = "NORMAL",
    ADMIN = "ADMIN"
}

export interface TokenPayload {
    id: string,
    name: string,
    role: USER_ROLES
}
```

# Exemplos de requisição

## Signup
Endpoint público utilizado para cadastro. Devolve um token jwt.
```typescript
// request POST /users/signup
// body JSON
{
  "name": "Elon Musk",
  "email": "elonmusk@email.com",
  "password": "Tesla1234"
}

// response
// status 201 CREATED
{
  token: "um token jwt"
}
```

## Signin
Endpoint público utilizado para login. Devolve um token jwt.
```typescript
// request POST /users/login
// body JSON
{
  "email": "elonmusk@email.com",
  "password": "Tesla1234"
}

// response
// status 200 OK
{
  token: "um token jwt"
}
```

## Create post
Endpoint protegido, requer um token jwt para acessá-lo.
```typescript
// request POST /posts
// headers.authorization = "token jwt"
// body JSON
{
    "textarea": "Xiaomi > iPhone. Change my mind!"
}

// response
// status 201 CREATED
```

## Get posts
Endpoint protegido, requer um token jwt para acessá-lo.
```typescript
// request GET /posts
// headers.authorization = "token jwt"

// response
// status 200 OK
[
    {
        "id": "uma uuid v4",
        "textarea": "Xiaomi > iPhone. Change my mind!",
        "likes": 10,
        "dislikes": 1,
        "createdAt": "2023-01-20T12:11:47:000Z"
        "updatedAt": "2023-01-20T12:11:47:000Z"
        "creator": {
            "id": "uma uuid v4",
            "name": "Fulano"
        }
    },
    {
        "id": "uma uuid v4",
        "textarea": "Me formei na Labenu!",
        "likes": 50,
        "dislikes": 0,
        "createdAt": "2023-01-20T15:41:12:000Z"
        "updatedAt": "2023-01-20T15:49:55:000Z"
        "creator": {
            "id": "uma uuid v4",
            "name": "Ciclana"
        }
    }
]
```

## Edit post
Endpoint protegido, requer um token jwt para acessá-lo.<br>
Só quem criou a post pode editá-lo e somente o textarea pode ser editado.
```typescript
// request PUT /posts/:id
// headers.authorization = "token jwt"
// body JSON
{
    "textarea": "Bloqueado pela Apple."
}

// response
// status 200 OK
```

## Delete post
Endpoint protegido, requer um token jwt para acessá-lo.<br>
Só quem criou a post pode deletá-lo. Admins podem deletar a post de qualquer pessoa.

```typescript
// request DELETE /posts/:id
// headers.authorization = "token jwt"

// response
// status 200 OK
```

## Like or dislike post/comments - Ambos endpoints fazem as duas coisas (Like/Dislike)

Endpoints protegidos, requer um token jwt para acessá-los.<br>
Quem criou a post/comentário não pode dar like ou dislike no mesmo.<br><br>
Caso dê um like em um post/comentário que já tenha dado like, o like é desfeito.<br>
Caso dê um dislike em um post/comentário que já tenha dado dislike, o dislike é desfeito.<br><br>
Caso dê um like em um post/comentário que tenha dado dislike, o like sobrescreve o dislike.<br>
Caso dê um dislike em um post/comentário que tenha dado like, o dislike sobrescreve o like.<br>

### Like (funcionalidade 1)
```typescript
// request PUT /posts/:id/like
// headers.authorization = "token jwt"
// body JSON
{
    "like": true
}

// response
// status 200 OK
```

### Dislike (funcionalidade 2)
```typescript
// request PUT /posts/:id/like
// headers.authorization = "token jwt"
// body JSON
{
    "like": false
}

// response
// status 200 OK
```

### Para entender a tabela likes_dislikes
- no SQLite, lógicas booleanas devem ser controladas via 0 e 1 (INTEGER)
- quando like valer 1 na tabela é porque a pessoa deu like na post
    - na requisição like é true
    
- quando like valer 0 na tabela é porque a pessoa deu dislike na post
    - na requisição like é false
    
- caso não exista um registro na tabela de relação, é porque a pessoa não deu like nem dislike.
- caso dê like em um post/comentário que já tenha dado like, o like é removido (deleta o item da tabela).
- caso dê dislike em um post/comentário que já tenha dado dislike, o dislike é removido (deleta o item da tabela).

# Documentação Postman
https://documenter.getpostman.com/view/27061449/2s946fftkT
