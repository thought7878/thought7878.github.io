MVC（Model - View - Controller）是一种广泛应用于软件开发的架构模式，它将应用程序划分为三个主要部分：模型（Model）、视图（View）和控制器（Controller），通过分离不同的功能模块，提高了代码的可维护性、可扩展性和可测试性。以下是对 MVC 架构模式的详细介绍：

### 各部分的职责

#### 1. 模型（Model）

- **定义**：模型是应用程序中负责处理数据和业务逻辑的部分。它代表了应用程序的核心数据结构和操作这些数据的方法。
- **功能**：模型负责数据的存储、检索、更新和验证等操作。例如，在一个电商应用中，模型可能包含商品信息、用户信息等数据结构，以及添加商品、修改用户信息等业务逻辑。
- **示例（Python 代码）**：

```python
class ProductModel:
    def __init__(self):
        self.products = []

    def add_product(self, name, price):
        product = {'name': name, 'price': price}
        self.products.append(product)
        return product

    def get_all_products(self):
        return self.products
```

#### 2. 视图（View）

- **定义**：视图是应用程序中负责呈现数据给用户的部分。它将模型中的数据以可视化的方式展示给用户，如网页、桌面应用的界面等。
- **功能**：视图不处理业务逻辑，只负责数据的展示。它监听模型的变化，并根据模型的数据更新界面。例如，在电商应用中，视图可能是商品列表页面，将模型中的商品信息以列表的形式展示给用户。
- **示例（HTML 代码）**：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF - 8" />
    <title>Product List</title>
  </head>

  <body>
    <h1>Product List</h1>
    <ul id="productList">
      <!-- 这里会动态插入商品信息 -->
    </ul>
  </body>
</html>
```

#### 3. 控制器（Controller）

- **定义**：控制器是模型和视图之间的桥梁，负责接收用户的输入，并根据输入调用模型的相应方法进行业务处理，然后更新视图以反映模型的变化。
- **功能**：控制器处理用户的请求，如点击按钮、提交表单等，根据请求的类型调用模型的方法进行数据处理，再将处理结果传递给视图进行展示。例如，在电商应用中，当用户点击“添加商品”按钮时，控制器会接收这个请求，调用模型的 `add_product` 方法添加商品，然后更新视图显示新的商品列表。
- **示例（Python Flask 代码）**：

```python
from flask import Flask, render_template_string, request
app = Flask(__name__)
model = ProductModel()

@app.route('/')
def index():
    products = model.get_all_products()
    html = """
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF - 8">
        <title>Product List</title>
    </head>
    <body>
        <h1>Product List</h1>
        <ul>
            {% for product in products %}
            <li>{{ product.name }} - ${{ product.price }}</li>
            {% endfor %}
        </ul>
        <form action="/add_product" method="post">
            <input type="text" name="name" placeholder="Product Name">
            <input type="number" name="price" placeholder="Price">
            <input type="submit" value="Add Product">
        </form>
    </body>
    </html>
    """
    return render_template_string(html, products=products)

@app.route('/add_product', methods=['POST'])
def add_product():
    name = request.form.get('name')
    price = float(request.form.get('price'))
    model.add_product(name, price)
    return index()

if __name__ == '__main__':
    app.run(debug=True)
```

### MVC 架构的工作流程

1. **用户操作**：用户通过视图界面进行操作，如点击按钮、输入数据等。
2. **请求传递**：视图将用户的操作请求传递给控制器。
3. **业务处理**：控制器接收到请求后，根据请求的类型调用模型的相应方法进行业务处理。
4. **数据更新**：模型处理完业务逻辑后，更新自身的数据状态。
5. **视图更新**：控制器通知视图，视图根据模型的新数据更新界面，将最新的数据展示给用户。

### MVC 架构的优点

- **可维护性高**：由于模型、视图和控制器的职责分离，当需要修改某一部分的功能时，只需要关注对应的模块，不会影响其他模块。
- **可扩展性强**：可以方便地对模型、视图或控制器进行扩展，添加新的功能。
- **可测试性好**：每个模块的功能相对独立，可以单独对模型、视图和控制器进行单元测试。

### MVC 架构的缺点

- **增加代码复杂度**：对于简单的应用程序，采用 MVC 架构可能会增加代码的复杂度，因为需要额外的代码来实现模块之间的交互。
- **模块间通信复杂**：模型、视图和控制器之间的通信需要进行精心设计和管理，否则可能会导致代码混乱。
