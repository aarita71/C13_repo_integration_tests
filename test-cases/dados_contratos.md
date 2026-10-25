# Dados de Teste e Contratos — Gerados pelo Prompt 2

> **Prompt utilizado**: Geração de Dados de Teste e Contratos (ver `prompts/prompt_dados_contratos.md`)
> **Cenário**: Order Service → Product Catalog Service

---

## 1. Conjuntos de Dados de Teste

### Conjunto DT-001: Pedido Válido com Produto em Estoque

**Request:**
```json
POST /api/orders
{
  "productId": "PROD-1001",
  "quantity": 3
}
```

**Response esperado do Catalog (GET /api/products/PROD-1001):**
```json
{
  "id": "PROD-1001",
  "name": "Notebook Dell Inspiron 15",
  "price": 3499.90,
  "stock": 25,
  "category": "Eletrônicos"
}
```

**Response esperado do Order Service:**
```json
Status: 201 Created
{
  "orderId": "ORD-20240001",
  "productId": "PROD-1001",
  "productName": "Notebook Dell Inspiron 15",
  "quantity": 3,
  "unitPrice": 3499.90,
  "totalPrice": 10499.70,
  "status": "CONFIRMED",
  "createdAt": "2026-10-24T10:30:00Z"
}
```

---

### Conjunto DT-002: Pedido com Quantidade no Limite do Estoque

**Request:**
```json
POST /api/orders
{
  "productId": "PROD-2005",
  "quantity": 8
}
```

**Response esperado do Catalog:**
```json
{
  "id": "PROD-2005",
  "name": "Mouse Logitech MX Master 3",
  "price": 499.90,
  "stock": 8,
  "category": "Periféricos"
}
```

**Response esperado do Order Service:**
```json
Status: 201 Created
{
  "orderId": "ORD-20240002",
  "productId": "PROD-2005",
  "productName": "Mouse Logitech MX Master 3",
  "quantity": 8,
  "unitPrice": 499.90,
  "totalPrice": 3999.20,
  "status": "CONFIRMED",
  "createdAt": "2026-10-24T10:35:00Z"
}
```
**Nota**: Estoque do produto vai para 0 após este pedido.

---

### Conjunto DT-003: Pedido com Produto Sem Estoque

**Request:**
```json
POST /api/orders
{
  "productId": "PROD-3010",
  "quantity": 1
}
```

**Response esperado do Catalog:**
```json
{
  "id": "PROD-3010",
  "name": "Teclado Mecânico Redragon Kumara",
  "price": 199.90,
  "stock": 0,
  "category": "Periféricos"
}
```

**Response esperado do Order Service:**
```json
Status: 409 Conflict
{
  "error": "INSUFFICIENT_STOCK",
  "message": "Estoque insuficiente para o produto PROD-3010. Disponível: 0, Solicitado: 1",
  "productId": "PROD-3010",
  "availableStock": 0,
  "requestedQuantity": 1
}
```

---

### Conjunto DT-004: Pedido com Produto Inexistente

**Request:**
```json
POST /api/orders
{
  "productId": "PROD-9999",
  "quantity": 2
}
```

**Response esperado do Catalog:**
```json
Status: 404 Not Found
{
  "error": "PRODUCT_NOT_FOUND",
  "message": "Produto PROD-9999 não encontrado no catálogo"
}
```

**Response esperado do Order Service (propagação):**
```json
Status: 404 Not Found
{
  "error": "PRODUCT_NOT_FOUND",
  "message": "Produto PROD-9999 não encontrado. Verifique o catálogo."
}
```

---

### Conjunto DT-005: Pedido com Quantidade Zero (Validação)

**Request:**
```json
POST /api/orders
{
  "productId": "PROD-1001",
  "quantity": 0
}
```

**Response esperado do Order Service:**
```json
Status: 400 Bad Request
{
  "error": "INVALID_QUANTITY",
  "message": "A quantidade deve ser maior que zero. Valor recebido: 0",
  "field": "quantity",
  "value": 0
}
```
**Nota**: O Order Service NÃO deve consultar o Catalog Service neste caso, pois a validação é local.

---

## 2. Análise da Diversidade dos Dados

| Conjunto | Valor Limite | Cenário | Tipo |
|----------|-------------|---------|------|
| DT-001 | Stock=25, Qty=3 | Normal, folga de estoque | Happy Path |
| DT-002 | Stock=8, Qty=8 | Limite exato | Edge Case |
| DT-003 | Stock=0 | Estoque esgotado | Erro |
| DT-004 | Produto inexistente | ID inválido | Erro |
| DT-005 | Quantity=0 | Valor zero | Validação |

---

## 3. Observações sobre os Dados Gerados

- Os valores monetários são **realistas em Reais (BRL)**
- Os nomes de produtos são **realistas** (marcas conhecidas)
- Os IDs seguem padrão `PROD-XXXX` e `ORD-YYYYMMDDNNNN`
- Cada conjunto inclui **request, response intermediário e response final**
- Os cenários cobrem: happy path, limite, estoque zero, produto inexistente e validação
