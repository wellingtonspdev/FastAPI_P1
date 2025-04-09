# 🚀 Sistema de Gestão de Produtos e Usuários - Relatório Técnico Completo

![Licença MIT](https://img.shields.io/badge/license-MIT-blue)
![Versão 2.0](https://img.shields.io/badge/version-2.0-green)
![Status](https://img.shields.io/badge/status-stable-brightgreen)

## 🏗️ Arquitetura MVC

## 📝 Relatório Técnico

### 1. Arquitetura MVC Implementada

O sistema foi desenvolvido seguindo rigorosamente o padrão **Model-View-Controller (MVC)**, com as seguintes características:

**a) Camada Model (Models)**
```python
# produto_model.py
class Produto(Base):
    __tablename__ = 'produtos'
    
    id = Column(Integer, primary_key=True)
    nome = Column(String(100), nullable=False)
    preco = Column(Float, nullable=False)
    quantidade = Column(Integer, default=0)
    
    @classmethod
    def buscar_por_nome(cls, session, nome):
        return session.query(cls).filter(Produto.nome.ilike(f'%{nome}%')).all()
```
- Responsável pela interação com o banco MySQL via SQLAlchemy ORM
- Contém toda a estrutura de dados e relações
- Implementa métodos de busca e filtros complexos

**b) Camada Controller (Controllers)**
```python
# produto_controller.py
class ProdutoController:
    @staticmethod
    def criar_produto_com_validacao(session, dados):
        try:
            produto_validado = ProdutoSchema(**dados)
            produto = Produto(**produto_validado.dict())
            session.add(produto)
            session.commit()
            return produto
        except ValidationError as e:
            raise ValueError(str(e))
```
- Gerencia a lógica de negócios
- Coordena a comunicação entre Models e Views
- Implementa validações complexas

**c) Camada View (Templates)**
```html
<!-- produtos/editar.html -->
{% extends "base.html" %}

{% block content %}
<form method="POST" action="/produtos/{{produto.id}}/editar">
    <input type="text" name="nome" value="{{produto.nome}}" 
           required minlength="3" maxlength="100">
    <span class="error">{{erros.nome if erros and erros.nome}}</span>
</form>
{% endblock %}
```
- Responsável pela apresentação dos dados
- Implementa validações no cliente
- Exibe feedbacks de erro

### 2. Sistema de Validação Multicamadas

Implementamos um sistema robusto de validação em três níveis:

**a) Frontend (Templates Jinja2)**
```html
<input type="number" name="preco" step="0.01" min="0.01" required
       oninvalid="this.setCustomValidity('Preço deve ser positivo')">
```

**b) Backend (Pydantic)**
```python
class ProdutoSchema(BaseModel):
    nome: constr(min_length=3, max_length=100)
    preco: confloat(gt=0)
    quantidade: conint(ge=0) = 0
    
    @validator('nome')
    def nome_deve_ter_espaco(cls, v):
        if ' ' not in v:
            raise ValueError('Deve conter espaço')
        return v.title()
```

### 3. Sistema de Rotas Avançado

As rotas foram implementadas com:

**a) Organização Modular**
```python
# produtos_routes.py
router = APIRouter(
    prefix="/produtos",
    tags=["Produtos"],
    responses={404: {"description": "Não encontrado"}}
)

@router.get("/", response_class=HTMLResponse)
async def listar_produtos(request: Request):
    # Implementação
```

**b) Tratamento de Erros**
```python
@router.put("/{id}")
async def atualizar_produto(id: int, request: Request):
    try:
        produto = ProdutoController.atualizar_produto(
            request.state.db, id, await request.form()
        )
        return RedirectResponse(f"/produtos/{id}", status_code=303)
    except ValueError as e:
        return mostrar_erro_edicao(request, id, str(e))
```

### 4. Desafios e Soluções

| Desafio | Solução Implementada | Código Exemplo |
|---------|----------------------|----------------|
| Validação complexa de preços | Implementação de validadores customizados no Pydantic | `@validator('preco') def validar_preco(cls, v): ...` |
| Sincronização estado do banco | Uso de sessions atômicas e rollback automático | `with session.begin(): ...` |
| Formulários complexos | Divisão em componentes reutilizáveis | `{% include 'componentes/campo.html' %}` |
| Performance em listagens | Implementação de paginação lazy | `session.query(Produto).limit(10).offset((pagina-1)*10)` |

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

Referências Técnicas

1. **Padrão MVC**
   - [Documentação oficial do FastAPI sobre MVC](https://fastapi.tiangolo.com/advanced/mvc/)
   - Fowler, M. (2002). *Patterns of Enterprise Application Architecture*. Addison-Wesley.

2. **Validação de Dados**
   - [Documentação Pydantic](https://pydantic-docs.helpmanual.io/)
   - [HTML5 Form Validation](https://developer.mozilla.org/en-US/docs/Learn/Forms/Form_validation)

3. **Boas Práticas REST**
   - Fielding, R. (2000). *Architectural Styles and the Design of Network-based Software Architectures* (Dissertation)
   - [Microsoft REST API Guidelines](https://github.com/microsoft/api-guidelines)

4. **Segurança**
   - [OWASP Top 10](https://owasp.org/www-project-top-ten/)
   - [SQLAlchemy Security](https://docs.sqlalchemy.org/en/14/security.html)

5. **MySQL e Python**
   - [MySQL Connector/Python](https://dev.mysql.com/doc/connector-python/en/)
   - [SQLAlchemy ORM](https://docs.sqlalchemy.org/en/14/orm/)

## 👥 Autores e Contribuições

- [Wellington Siqueira Porto](https://github.com/wellingtonspdev)
- [Kauã Hiro](https://github.com/kaua-hiro)

---

<div align="center">
  Desenvolvido com Python 🐍 e FastAPI ⚡ 
</div>
