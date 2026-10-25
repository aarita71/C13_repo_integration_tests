# Casos de Teste de Integração — Gerados pelo Prompt 1

> **Prompt utilizado**: Geração de Cenários de Integração (ver `prompts/prompt_cenarios.md`)
> **Cenário**: Order Service → Product Catalog Service

## Caminho Feliz (Happy Path)

| ID | Descrição | Pré-condições | Passos | Resultado Esperado | Categoria |
|----|-----------|---------------|--------|--------------------|-----------|
| IT-001 | Pedido criado com produto disponível | Produto "Notebook" (id=1) com stock=50, price=2999.90 | 1. POST /api/orders {productId:"1", quantity:2} | HTTP 201, orderId gerado, estoque reduzido para 48 | Happy Path |
| IT-002 | Pedido múltiplo com validação de preço | Produto "Mouse Gamer" (id=5) com stock=100, price=79.90 | 1. POST /api/orders {productId:"5", quantity:10} | HTTP 201, totalPrice=799.00, estoque=90 | Happy Path |
| IT-003 | Pedido de último item em estoque | Produto "Teclado Mecânico" (id=8) com stock=1 | 1. POST /api/orders {productId:"8", quantity:1} | HTTP 201, estoque=0 | Happy Path |
| IT-004 | Pedido consulta preço atualizado | Produto atualizado de R$100 para R$120 | 1. POST /api/orders {productId:"3", quantity:1} | HTTP 201, totalPrice=120.00 (preço vigente) | Happy Path |

## Erros e Falhas

| ID | Descrição | Pré-condições | Passos | Resultado Esperado | Categoria |
|----|-----------|---------------|--------|--------------------|-----------|
| IT-005 | Produto inexistente no catálogo | Nenhum produto com id=999 | 1. POST /api/orders {productId:"999", quantity:1} | HTTP 404, mensagem "Produto não encontrado" | Erro |
| IT-006 | Estoque insuficiente | Produto com stock=3, pedido de quantity=10 | 1. POST /api/orders {productId:"2", quantity:10} | HTTP 409, mensagem "Estoque insuficiente" | Erro |
| IT-007 | Catálogo indisponível (timeout) | Product Catalog Service fora do ar | 1. POST /api/orders {productId:"1", quantity:1} 2. Timeout após 5s | HTTP 503, mensagem "Serviço de catálogo indisponível" | Erro |
| IT-008 | Catálogo retorna resposta inválida | Catalog retorna status 200 com body malformado | 1. POST /api/orders {productId:"1", quantity:1} | HTTP 500 no Order Service, log de erro registrado | Erro |

## Edge Cases (Casos de Borda)

| ID | Descrição | Pré-condições | Passos | Resultado Esperado | Categoria |
|----|-----------|---------------|--------|--------------------|-----------|
| IT-009 | Quantidade zero | Produto válido em estoque | 1. POST /api/orders {productId:"1", quantity:0} | HTTP 400, mensagem "Quantidade deve ser maior que zero" | Edge Case |
| IT-010 | Quantidade negativa | Produto válido em estoque | 1. POST /api/orders {productId:"1", quantity:-5} | HTTP 400, validação de entrada | Edge Case |
| IT-011 | Concorrência: dois pedidos simultâneos | Produto com stock=1 | 1. POST /api/orders {productId:"1", quantity:1} (concorrente A) 2. POST /api/orders {productId:"1", quantity:1} (concorrente B) | Um retorna 201, outro retorna 409 (estoque esgotado) | Edge Case |
| IT-012 | Pedido com quantity exatamente igual ao stock | Produto com stock=50 | 1. POST /api/orders {productId:"1", quantity:50} | HTTP 201, estoque=0 | Edge Case |

## Resumo da Cobertura

- **Happy Path**: 4 casos (IT-001 a IT-004)
- **Erros/Falhas**: 4 casos (IT-005 a IT-008)
- **Edge Cases**: 4 casos (IT-009 a IT-012)
- **Total**: 12 casos de teste de integração
