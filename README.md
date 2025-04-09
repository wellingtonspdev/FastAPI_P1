# 🚀 Sistema de Gestão de Produtos e Usuários

![Licença MIT](https://img.shields.io/badge/license-MIT-blue)
![Versão 1.0](https://img.shields.io/badge/version-1.0-green)
![Status](https://img.shields.io/badge/status-stable-brightgreen)

## 🏗️ Arquitetura MVC

O sistema foi estruturado seguindo rigorosamente o padrão MVC (Model-View-Controller), proporcionando separação clara de responsabilidades:

### 📦 Camada Model (Models)
```python
# Exemplo: produto_model.py
class Produto(Base):
    __tablename__ = 'produtos'
    
    id = Column(Integer, primary_key=True)
    nome = Column(String(100), nullable=False)
    preco = Column(Float, nullable=False)
    quantidade = Column(Integer, default=0)
    
    @classmethod
    def buscar_por_id(cls, session, id):
        return session.query(cls).filter_by(id=id).first()
```
Responsável por:
- Interação com o banco de dados MySQL via SQLAlchemy ORM
- Definição da estrutura das tabelas
- Métodos de consulta e persistência

### 🎮 Camada Controller (Controllers)
```python
# Exemplo: produto_controller.py
class ProdutoController:
    @staticmethod
    def criar_produto(session, dados):
        produto = Produto(
            nome=dados['nome'],
            preco=dados['preco'],
            quantidade=dados.get('quantidade', 0)
        )
        session.add(produto)
        session.commit()
        return produto
```
Responsável por:
- Lógica de negócios
- Validações básicas
- Intermediação entre Models e Views

### 🖼️ Camada View (Templates)
```html
<!-- Exemplo: templates/produtos/cadastro.html -->
{% extends "base.html" %}

{% block content %}
<form method="POST" action="/produtos/criar">
    <input type="text" name="nome" required minlength="3">
    <input type="number" name="preco" step="0.01" min="0" required>
    <button type="submit">Cadastrar</button>
</form>
{% endblock %}
```
Responsável por:
- Apresentação dos dados
- Formulários de interação
- Validações no front-end

## 🔍 Validações em Templates

As validações nos templates Jinja2 ocorrem em três níveis:

1. **Validação HTML5**:
```html
<input type="text" name="nome" required minlength="3" maxlength="100">
```

2. **Validação no Backend** (via Pydantic):
```python
class ProdutoSchema(BaseModel):
    nome: str = Field(..., min_length=3, max_length=100)
    preco: float = Field(..., gt=0)
    quantidade: int = Field(0, ge=0)
```

3. **Feedback de Erros**:
```html
{% if erro %}
<div class="alert alert-error">
    {{ erro }}
</div>
{% endif %}
```

## 🛣️ Sistema de Rotas

O roteamento foi implementado com o FastAPI seguindo boas práticas RESTful:

### Rotas de Produtos (`produtos_routes.py`)
```python
router = APIRouter(prefix="/produtos")

@router.get("/", response_class=HTMLResponse)
async def listar_produtos(request: Request):
    produtos = ProdutoController.listar_produtos(request.state.db)
    return templates.TemplateResponse("produtos/lista.html", {"request": request, "produtos": produtos})

@router.post("/criar")
async def criar_produto(request: Request):
    form_data = await request.form()
    try:
        ProdutoController.criar_produto(request.state.db, dict(form_data))
        return RedirectResponse("/produtos", status_code=303)
    except ValueError as e:
        return templates.TemplateResponse("produtos/cadastro.html", {"request": request, "erro": str(e)})
```

### Rotas de Usuários (`usuario_routes.py`)
```python
router = APIRouter(prefix="/usuarios")

@router.get("/{id}")
async def obter_usuario(id: int):
    usuario = UsuarioController.obter_por_id(id)
    if not usuario:
        raise HTTPException(status_code=404)
    return usuario
```

## 🏗️ Estrutura do Projeto

```
projeto_gestao/
├── controllers/
│   ├── produto_controller.py    # Lógica para produtos
│   └── usuario_controller.py    # Lógica para usuários
│
├── database/
│   ├── config.py                # Configurações do banco
│   └── models/
│       ├── database.py          # Configuração do ORM
│       ├── produto_model.py     # Modelo de produtos
│       └── usuario_model.py     # Modelo de usuários
│
├── routes/
│   ├── produtos_routes.py       # Rotas de produtos
│   └── usuario_routes.py        # Rotas de usuários
│
├── templates/
│   ├── produtos/
│   │   ├── cadastro.html        # Formulário de cadastro
│   │   ├── detalhes.html        # Visualização de produto
│   │   ├── editar.html          # Formulário de edição
│   │   └── lista.html           # Listagem de produtos
│   │
│   ├── usuarios/
│   │   ├── cadastro.html        # Formulário de usuário
│   │   ├── detalhes.html        # Perfil de usuário
│   │   ├── editar.html          # Edição de usuário
│   │   └── lista.html           # Lista de usuários
│   │
│   ├── base.html                # Template base
│   └── index.html               # Página inicial
│
├── validators/                  # Validações customizadas
├── testes_endpoint/             # Evidências de testes
├── .env.example                 # Variáveis de ambiente
├── main.py                      # Aplicação principal
├── README.md                    # Documentação
└── requirements.txt             # Dependências
```

---

## 🔄 Fluxo de Dados

1. **Requisição** chega à rota correspondente
2. **Controller** processa a requisição
3. **Model** interage com o banco via SQLAlchemy
4. **Resposta** é renderizada (HTML) ou retornada (HTML)

---

## ✅ Validações Implementadas

| Entidade  | Campo          | Validações                              |
|-----------|----------------|-----------------------------------------|
| Produto   | nome           | Obrigatório, 3-100 caracteres           |
|           | preço          | Decimal positivo                        |
|           | quantidade     | Inteiro não negativo                    |
| Usuário   | email          | Formato válido, único no sistema        |
|           | senha          | Mínimo 8 caracteres                     |

---

## 🧪 Testes de Endpoint

### 🛍️ Módulo de Produtos

| Método | Endpoint               | Casos Testados | Evidência |
|--------|------------------------|----------------|-----------|
| GET    | `/produtos`            | Listagem completa |   ![GET-produtos-Retornar-todos-os-produto](https://github.com/user-attachments/assets/71b45a3e-6f7b-47fd-8b9b-5a68fe115bdb) |

| GET    | `/produtos/[id]`       | Produto específico | ![GET-produtos{id}-Retornar-o-produto-com-o-ID-especificado](https://github.com/user-attachments/assets/d200e0b9-1e02-4438-9db9-c8c7bd52cfc8) |

| POST   | `/produtos`            | Criação com validação | ![POST-usuarios-Criar-um-novo-usuario-(com-validação-de-campos)](https://github.com/user-attachments/assets/5f43d9be-0c0b-4240-951a-a14862b962e9)

| PUT    | `/produtos/[id]`       | Atualização parcial | ![PUT-produtos-{id}-Atualizar-os-dados-de-um-produto-existente-(com validação de campos)](https://github.com/user-attachments/assets/d8459042-ef72-4f58-813c-125c53428aa1) |

| DELETE | `/produtos/[id]`       | Exclusão segura | ![DELETE-produtos{id}-Excluir-o-produto-com-o-ID-especificado](https://github.com/user-attachments/assets/48ff4241-6c9e-416d-9578-38a662ca464d) |


### 👥 Módulo de Usuários

| Método | Endpoint               | Casos Testados | Evidência |
|--------|------------------------|----------------|-----------|
| GET    | `/usuarios`            | Listagem completa |   ![GET-usuarios{id}Retornar-o-usuario-com-o-ID-especificado](https://github.com/user-attachments/assets/a47220a8-4b7a-488c-a374-49d5528032be) |

| GET    | `/usuarios/[id]`       | Usuário específico |![GET-usuarios{id}Retornar-o-usuario-com-o-ID-especificado](https://github.com/user-attachments/assets/d85ff2d6-6aa6-47b2-bd5d-1b7399a4c05a) |

| POST   | `/usuarios`            | Registro com validação | ![POST-usuarios-Criar-um-novo-usuario-(com-validação-de-campos)](https://github.com/user-attachments/assets/9a3d1ace-8607-4076-9ece-a9bdb6a94b9e) |

| PUT    | `/usuarios/[id]`       | Atualização de perfil |![PUT-usuarios{id}-Atualizar-os-dados-de-um-usuario-existente-(com-validação-de-campos)](https://github.com/user-attachments/assets/f3eee6f7-454a-4764-ae30-eb50c5bea968) |

| DELETE | `/usuarios/[id]`       | Remoção de conta | ![DELETE usuario](teste_endpoint/DELETE-usuarios{id}-Excluir-o-usuario-com-o-ID-especificado.png) |

---

## 🚀 Como Executar

1. **Pré-requisitos**:
   - Python 3
   - MySQL Server
   - MySQL Workbench

2. **Configuração**:
   ```bash
   # Clonar repositório
   git clone [URL_DO_REPOSITORIO]
   
   # Configurar ambiente
   cp .env.example .env
   # Editar .env com suas credenciais MySQL
   
   # Instalar dependências
   pip install -r requirements.txt
   ```

3. **Execução**:
   ```bash
   uvicorn main:app --reload
   ```

Acesse a API em `http://localhost:8000` e a documentação interativa em `http://localhost:8000/docs`

---

## 👥 Autores

- [Wellington Siqueira Porto](https://github.com/wellingtonspdev)
- [Kauã Hiro](https://github.com/kaua-hiro)

---

<div align="center">
  Desenvolvido com Python 🐍 e FastAPI ⚡ 
</div>
