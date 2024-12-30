# Documentação do Código

Este documento descreve o funcionamento de um sistema de e-commerce implementado em Flask, incluindo autenticação de usuários, gerenciamento de produtos e carrinho de compras.

---

## Estrutura do Código

### 1. Importação de Módulos

O código utiliza os seguintes módulos e bibliotecas:

- **Flask**: Framework para criação de aplicações web.
- **Flask-SQLAlchemy**: ORM para interação com banco de dados.
- **Flask-CORS**: Habilita CORS para acesso entre domínios.
- **Flask-Login**: Gerenciamento de autenticação de usuários.

```
from flask import Flask, request, jsonify
from flask_sqlalchemy import SQLAlchemy
from flask_cors import CORS
from flask_login import UserMixin, login_user, LoginManager, login_required, logout_user, current_user
```

### 2. Configuração da Aplicação

A aplicação é configurada com uma chave secreta para sessões, um banco de dados SQLite e CORS.

```
app = Flask(__name__)
app.config['SECRET_KEY'] = "minha_chave_123"
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///ecommerce.db'
```

O gerenciador de login e o ORM são inicializados:

```
login_manager = LoginManager()
db = SQLAlchemy(app)
login_manager.init_app(app)
login_manager.login_view = 'login'
CORS(app)
```

---

## Modelos

### 1. Usuário (`User`)

Representa os usuários do sistema.

```
class User(db.Model, UserMixin):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), nullable=False, unique=True)
    password = db.Column(db.String(80), nullable=True)
    cart = db.relationship('CartItem', backref='user', lazy=True)
```

### 2. Produto (`Product`)

Representa os produtos disponíveis para compra.

```
class Product(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(120), nullable=False)
    price = db.Column(db.Float, nullable=False)
    description = db.Column(db.Text, nullable=True)
```

### 3. Item do Carrinho (`CartItem`)

Relaciona usuários a produtos no carrinho.

```
class CartItem(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=False)
    product_id = db.Column(db.Integer, db.ForeignKey('product.id'), nullable=False)
```

---

## Rotas

### 1. Autenticação

### Login

Realiza login do usuário com validação de credenciais.

```
@app.route('/login', methods=["POST"])
def login():
    ...
```

### Logout

Realiza logout do usuário autenticado.

```
@app.route('/logout', methods=["POST"])
@login_required
def logout():
    ...
```

### 2. Gerenciamento de Produtos

### Adicionar Produto

Adiciona um novo produto ao banco de dados.

```
@app.route('/api/products/add', methods=["POST"])
@login_required
def add_products():
    ...
```

### Listar Produtos

Retorna uma lista de todos os produtos disponíveis.

```
@app.route('/api/products', methods=['GET'])
def get_products():
    ...
```

### Detalhes do Produto

Obtém detalhes de um produto específico.

```
@app.route('/api/products/<int:product_id>', methods=['GET'])
def get_product_details(product_id):
    ...
```

### Atualizar Produto

Atualiza informações de um produto existente.

```
@app.route('/api/products/update/<int:product_id>', methods=['PUT'])
@login_required
def update_product(product_id):
    ...
```

### Excluir Produto

Remove um produto do banco de dados.

```
@app.route('/api/products/delete/<int:product_id>', methods=['DELETE'])
@login_required
def delete_product(product_id):
    ...
```

### 3. Gerenciamento do Carrinho

### Adicionar ao Carrinho

Adiciona um produto ao carrinho do usuário.

```
@app.route('/api/cart/add/<int:product_id>', methods=['POST'])
@login_required
def add_to_cart(product_id):
    ...
```

### Visualizar Carrinho

Retorna os itens no carrinho do usuário autenticado.

```
@app.route('/api/cart', methods=['GET'])
@login_required
def view_cart():
    ...
```

### Remover do Carrinho

Remove um item do carrinho do usuário.

```
@app.route('/api/cart/remove/<int:product_id>', methods=['DELETE'])
@login_required
def remove_from_cart(product_id):
    ...
```

### Finalizar Compra

Esvazia o carrinho do usuário após o checkout.

```
@app.route('/api/cart/checkout', methods=["POST"])
@login_required
def checkout():
    ...
```

---

## Execução

A aplicação é executada no modo de depuração:

```
if __name__ == '__main__':
    app.run(debug=True)
```

---

## Observações

- **Segurança**: Em produção, o uso de `SECRET_KEY` e senhas deve ser tratado de forma mais segura.
- **Validações**: Implementar validações adicionais para evitar inconsistências.
- **Melhorias**: Considerar o uso de hashing para armazenar senhas e um banco de dados robusto em produção.
