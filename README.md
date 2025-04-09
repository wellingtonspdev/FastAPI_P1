# 📊 Sistema de Gerenciamento de Produtos e Usuários

![Licença](https://img.shields.io/badge/Licença-MIT-green)
![Versão](https://img.shields.io/badge/Versão-1.0-blue)
![Status](https://img.shields.io/badge/Status-Em%20Desenvolvimento-yellow)

## 📝 Relatório Técnico

Este documento apresenta o relatório técnico do desenvolvimento do Sistema de Gerenciamento de Produtos e Usuários, uma aplicação web construída seguindo o padrão de arquitetura MVC (Model-View-Controller).

---

## 📋 Sumário

1. [📌 Visão Geral](#-visão-geral)
2. [🏗️ Arquitetura MVC](#️-arquitetura-mvc)
3. [🔧 Tecnologias Utilizadas](#-tecnologias-utilizadas)
4. [⚙️ Implementação](#️-implementação)
5. [✅ Validação de Campos](#-validação-de-campos)
6. [🚧 Desafios e Soluções](#-desafios-e-soluções)
7. [🚀 Como Executar](#-como-executar)
8. [🔌 Endpoints da API](#-endpoints-da-api)
9. [📚 Referências](#-referências)

---

## 📌 Visão Geral

O Sistema de Gerenciamento de Produtos e Usuários é uma aplicação web desenvolvida para gerenciar o cadastro, visualização, edição e remoção de produtos e usuários. A aplicação oferece uma API RESTful com endpoints bem definidos, além de uma interface de usuário para interação com o sistema.

---

## 🏗️ Arquitetura MVC

A aplicação foi desenvolvida seguindo o padrão de arquitetura MVC (Model-View-Controller), que separa a aplicação em três componentes principais:

### 📦 Model

Os modelos representam a estrutura de dados da aplicação e a lógica de negócios. Eles são responsáveis por:
- Definir a estrutura das entidades (produtos e usuários)
- Interagir com o banco de dados
- Implementar regras de negócio específicas

<details>
<summary>Exemplo conceitual de implementação de um modelo</summary>

```python
class Produto:
    def __init__(self, id, nome, descricao, preco, quantidade):
        self.id = id
        self.nome = nome
        self.descricao = descricao
        self.preco = preco
        self.quantidade = quantidade
    
    @staticmethod
    def buscar_todos(db):
        # Lógica para buscar todos os produtos no banco de dados
        pass
        
    @staticmethod
    def buscar_por_id(db, id):
        # Lógica para buscar um produto específico
        pass
        
    def salvar(self, db):
        # Lógica para salvar o produto no banco de dados
        pass
```
</details>

### 🖼️ View

As views são responsáveis pela apresentação dos dados ao usuário. No nosso sistema, utilizamos:
- Templates HTML para renderizar páginas
- CSS para estilização de elementos
- Formulários para entrada de dados

### 🎮 Controller

Os controllers gerenciam o fluxo da aplicação, processando requisições, interagindo com os modelos e retornando respostas:

<details>
<summary>Exemplo conceitual de um controller</summary>

```python
# Exemplo conceitual de um controller
def listar_produtos():
    produtos = Produto.buscar_todos(db)
    return render_template('produtos/lista.html', produtos=produtos)

def cadastrar_produto():
    if request.method == 'POST':
        # Processar dados do formulário
        produto = Produto(None, nome, descricao, preco, quantidade)
        produto.salvar(db)
        return redirect('/produtos')
    return render_template('produtos/cadastrar.html')
```
</details>

---

## 🔧 Tecnologias Utilizadas

| Categoria | Tecnologia |
|-----------|------------|
| **Backend** | Python com FastAPI |
| **ORM** | SQLAlchemy - Mapeamento objeto-relacional para interação com o banco de dados |
| **Banco de Dados** | SQL - Sistema de gerenciamento de banco de dados relacional |
| **Frontend** | HTML e CSS para interface do usuário |
| **Validação** | Pydantic - Biblioteca Python para validação de dados |

---

## ⚙️ Implementação

### 📂 Estrutura do Projeto

A estrutura do projeto segue a arquitetura MVC, com separação clara de responsabilidades entre os diferentes componentes:

```
FASTAPI_P1-MAIN/
├── controllers/                # Controladores da aplicação
│   ├── produto_controller.py   # Controlador de produtos
│   └── usuario_controller.py   # Controlador de usuários
├── database/                   # Configuração do banco de dados
│   └── config.py               # Configurações de conexão
├── models/                     # Modelos da aplicação
│   ├── database.py             # Configuração do ORM
│   ├── produto_model.py        # Modelo de produtos
│   └── usuario_model.py        # Modelo de usuários
├── routes/                     # Rotas da API
│   ├── produtos_routes.py      # Rotas de produtos
│   └── usuario_routes.py       # Rotas de usuários
├── templates/                  # Templates HTML
│   ├── produtos/               # Templates relacionados a produtos
│   │   ├── cadastro.html       # Formulário de cadastro de produtos
│   │   ├── detalhes.html       # Página de detalhes do produto
│   │   ├── editar.html         # Formulário de edição de produtos
│   │   └── lista.html          # Lista de produtos
│   ├── usuarios/               # Templates relacionados a usuários
│   │   ├── cadastro.html       # Formulário de cadastro de usuários
│   │   ├── detalhes.html       # Página de detalhes do usuário
│   │   ├── editar.html         # Formulário de edição de usuários
│   │   └── lista.html          # Lista de usuários
│   ├── base.html               # Template base para herança
│   └── index.html              # Página inicial
├── validators/                 # Validadores de dados
│   ├── produto_validator.py    # Validação de produtos
│   └── usuario_validator.py    # Validação de usuários
├── testes_endpoint/            # Testes de API
│   ├── DELETE -produtos-{id}-deletar-Excluir o produto com o ID especificado.png
│   ├── DELETE -usuarios{id}-Excluir o usuario com o ID especificado.png
│   ├── DELETE -usuarios{id}-Excluir o usuario com o ID especificado.2.png
│   ├── GET -produtos-{id}-Retornar o produto com o ID especificado.png
│   ├── POST -produtos-Criar um novo produto (com validação de campos).png
│   ├── POST-produtos-Criar um novo produto (com validação de campos)2.png
│   ├── PUT -produtos-{id}-editar-Atualizar os dados de um produto existente (com validação de campos).png
│   └── PUT -usuarios-{id}-Atualizar os dados de um usuário existente (com validação de campos).2.png
├── .env.example                # Exemplo de variáveis de ambiente
├── main.py                     # Ponto de entrada da aplicação
├── README.md                   # Documentação do projeto
└── requirements.txt            # Dependências do projeto
```

### 🔄 Fluxo da Aplicação

1. O usuário acessa uma URL
2. O router direciona a requisição para o controller apropriado
3. O controller processa a requisição e interage com os modelos necessários
4. Os dados são validados antes de serem processados
5. O resultado é renderizado em um template ou retornado como resposta

---

## ✅ Validação de Campos

A validação de campos é um aspecto crucial da aplicação, garantindo que apenas dados válidos sejam processados:

- **🔤 Validação de Tipos**: Garantir que os dados estejam no formato correto
- **📏 Validação de Restrições**: Verificar comprimentos mínimos/máximos, valores permitidos
- **🧩 Validação de Negócio**: Aplicar regras específicas do domínio

<details>
<summary>Exemplo de validação</summary>

```python
def validar_produto(nome, preco, quantidade):
    erros = []
    
    if not nome or len(nome) < 3:
        erros.append("Nome deve ter pelo menos 3 caracteres")
    
    if not preco or preco <= 0:
        erros.append("Preço deve ser maior que zero")
    
    if quantidade is None or quantidade < 0:
        erros.append("Quantidade não pode ser negativa")
    
    return erros
```
</details>

---

## 🚧 Desafios e Soluções

### 💾 Persistência de Dados

**Desafio**: Implementar um sistema eficiente de acesso ao banco de dados.

**Solução**: Utilização de um ORM (Object-Relational Mapping) para abstrair a complexidade do acesso ao banco de dados, permitindo manipular registros como objetos Python.

### 🔒 Segurança

**Desafio**: Proteger a aplicação contra vulnerabilidades comuns.

**Solução**: Implementação de validação rigorosa de entrada, sanitização de dados e proteção contra ataques como SQL Injection e Cross-Site Scripting (XSS).

### 👥 Experiência do Usuário

**Desafio**: Criar uma interface intuitiva e responsiva.

**Solução**: Desenvolvimento de uma interface limpa com CSS, fornecendo feedback claro para ações do usuário, validação de formulários e mensagens de erro informativas.

### 📈 Escalabilidade

**Desafio**: Projetar o sistema para crescer com o aumento de usuários e dados.

**Solução**: Adoção de práticas como paginação de resultados, otimização de consultas ao banco de dados e uso eficiente de recursos do servidor.

---

## 🚀 Como Executar

1. Clone o repositório
2. Copie o arquivo `.env.example` para `.env` e configure as variáveis de ambiente
3. Instale as dependências: `pip install -r requirements.txt`
4. Execute a aplicação: `python main.py`
5. Acesse a aplicação em `http://localhost:8000`

---

## 🔌 Endpoints da API

### 📦 Produtos

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| GET | `/produtos` | Lista todos os produtos |
| POST | `/produtos/cadastrar` | Cria novo produto |
| GET | `/produtos/{id}` | Mostra um produto |
| PUT | `/produtos/{id}/editar` | Atualiza produto |
| DELETE | `/produtos/{id}` | Deleta produto |

### 👤 Usuários

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| GET | `/usuarios` | Lista todos os usuários |
| POST | `/usuarios/cadastrar` | Cria novo usuário |
| GET | `/usuarios/{id}` | Mostra um usuário |
| PUT | `/usuarios/{id}/editar` | Atualiza usuário |
| DELETE | `/usuarios/{id}` | Deleta usuário |

## Testes de Endpoint

Para garantir a qualidade e o correto funcionamento da API, foram implementados testes automatizados para cada endpoint. Os testes verificam tanto o fluxo normal de operação quanto situações de erro, garantindo que a aplicação responda adequadamente em todos os cenários.

### Metodologia de Testes

Os testes foram realizados utilizando ferramentas de automação e testes manuais com o Postman para verificar:
- Códigos de status HTTP corretos
- Formato e conteúdo das respostas
- Validação de entrada
- Tratamento de erros

### Testes dos Endpoints de Produtos

#### GET /produtos/{id} - Obter um produto específico

Este endpoint retorna os detalhes de um produto específico com base no ID fornecido.

![GET /produtos/{id} - Retornar o produto com o ID especificado](testes_endpoint/GET%20-produtos-%7Bid%7D-Retornar%20o%20produto%20com%20o%20ID%20especificado.png)

#### POST /produtos/cadastrar - Criar um novo produto

Este endpoint permite a criação de um novo produto com validação de todos os campos obrigatórios.

![POST /produtos - Criar um novo produto (com validação de campos)](testes_endpoint/POST%20-produtos-Criar%20um%20novo%20produto%20%28com%20validação%20de%20campos%29.png)

![POST /produtos - Criar um novo produto (validação adicional)](testes_endpoint/POST-produtos-Criar%20um%20novo%20produto%20%28com%20validação%20de%20campos%292.png)

#### PUT /produtos/{id}/editar - Atualizar um produto

Este endpoint permite a atualização dos dados de um produto existente.

![PUT /produtos/{id}/editar - Atualizar os dados de um produto existente](testes_endpoint/PUT%20-produtos-%7Bid%7D-editar-Atualizar%20os%20dados%20de%20um%20produto%20existente%20%28com%20validação%20de%20campos%29.png)

#### PUT /usuarios/{id}/editar - Atualizar um usuário

Este endpoint permite a atualização dos dados de um usuário existente.

![PUT /usuarios/{id}/editar - Atualizar os dados de um usuário existente](testes_endpoint/PUT%20-usuarios-%7Bid%7D-Atualizar%20os%20dados%20de%20um%20usuário%20existente%20%28com%20validação%20de%20campos%29.2.png)

#### DELETE /produtos/{id} - Excluir um produto

Este endpoint permite a exclusão de um produto do sistema.

![DELETE /produtos/{id} - Excluir o produto com o ID especificado](testes_endpoint/DELETE%20-produtos-%7Bid%7D-deletar-Excluir%20o%20produto%20com%20o%20ID%20especificado.png)

### Testes dos Endpoints de Usuários

#### GET /usuarios - Listar todos os usuários

Este endpoint retorna a lista completa de usuários cadastrados no sistema.

![GET /usuarios - Listar todos os usuários](testes_endpoint/GET%20-produtos-%7Bid%7D-Retornar%20o%20produto%20com%20o%20ID%20especificado.png)

#### GET /usuarios/{id} - Obter um usuário específico

Este endpoint retorna os detalhes de um usuário específico com base no ID fornecido.

![GET /usuarios/{id} - Retornar o usuário com o ID especificado](testes_endpoint/GET%20-produtos-%7Bid%7D-Retornar%20o%20produto%20com%20o%20ID%20especificado.png)

#### DELETE /usuarios/{id} - Excluir um usuário

Este endpoint permite a exclusão de um usuário do sistema.

![DELETE /usuarios/{id} - Excluir o usuário com o ID especificado] (testes_endpoint/DELETE-usuarios{id}-Excluir-o-usuario-com-o-ID-especificado.2.png)

#### DELETE /usuarios/{id} - Excluir um usuário (alternativo)

Este endpoint permite a exclusão de um usuário do sistema (teste alternativo).

![DELETE /usuarios/{id} - Excluir o usuário com o ID especificado (alternativo)](testes_endpoint/DELETE%20-usuarios%7Bid%7D-Excluir%20o%20usuario%20com%20o%20ID%20especificado.2.png)

### Resultados dos Testes

Todos os endpoints foram testados com sucesso, demonstrando que a API está funcionando conforme o esperado. Os testes verificaram:

1. **Funcionalidade básica**: Cada endpoint realiza corretamente sua função principal
2. **Validação de dados**: Campos obrigatórios e formatos são validados adequadamente
3. **Tratamento de erros**: A API responde apropriadamente a entradas inválidas
4. **Consistência**: As respostas seguem um formato padronizado

### Ferramentas Utilizadas

- **Postman**: Para testes manuais e documentação da API
- **Pytest**: Para testes automatizados
- **SQLite**: Banco de dados para ambiente de teste

---

## 📚 Referências

- [Padrão MVC (Model-View-Controller)](https://developer.mozilla.org/en-US/docs/Glossary/MVC)
- [Princípios de Design RESTful](https://restfulapi.net/)
- [Boas Práticas de Segurança Web](https://owasp.org/www-project-top-ten/)
- [Otimização de Desempenho Web](https://web.dev/performance-optimizing-content-efficiency/)
- [Princípios de Usabilidade](https://www.nngroup.com/articles/ten-usability-heuristics/)

---

<div align="center">
  <p>Desenvolvido com ❤️ pela Equipe de Desenvolvimento</p>
</div>