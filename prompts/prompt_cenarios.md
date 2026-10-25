# Prompt Tipo 1: Geração de Cenários de Integração

## Prompt Utilizado

```
Você é um QA Engineer especialista em testes de integração de microsserviços.

CONTEXTO:
Temos dois microsserviços que se comunicam via API REST:
- Product Catalog Service: GET /api/products/{id} → retorna {id, name, price, stock}
- Order Service: POST /api/orders → recebe {productId, quantity} e consulta o catálogo

FLUXO DE INTEGRAÇÃO:
1. Cliente envia POST /api/orders com {productId, quantity}
2. Order Service faz GET /api/products/{productId}
3. Se produto existe e há estoque suficiente → pedido criado (HTTP 201)
4. Se produto não existe → HTTP 404
5. Se estoque insuficiente → HTTP 409

SOLICITAÇÃO:
Gere pelo menos 12 casos de teste de integração cobrindo:
- 4 cenários de caminho feliz (happy path)
- 4 cenários de erro/falha (erros de API, timeout, serviço indisponível)
- 4 cenários de edge case (limites, dados inconsistentes, concorrência)

FORMATO DE SAÍDA (tabela Markdown):
| ID | Descrição | Pré-condições | Passos | Resultado Esperado | Categoria |
```

## Observações sobre o Prompt

- **Persona definida**: "QA Engineer especialista" para direcionar a expertise
- **Contexto técnico**: endpoints, métodos e fluxos especificados
- **Cobertura explícita**: número mínimo por categoria (happy path, erro, edge)
- **Formato estruturado**: tabela Markdown para facilitar conversão em automação
