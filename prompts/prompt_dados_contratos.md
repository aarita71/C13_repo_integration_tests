# Prompt Tipo 2: Geração de Dados de Teste e Contratos

## Prompt Utilizado

```
Você é um QA Engineer focado em dados de teste e contratos de API.

CONTEXTO:
Mesma integração: Order Service → Product Catalog Service
- POST /api/orders recebe {productId: string, quantity: integer}
- GET /api/products/{id} retorna {id, name, price, stock, category}

SOLICITAÇÃO:

1. Gere 5 conjuntos de dados de teste (request + response esperado) cobrindo:
   - Pedido válido com produto em estoque
   - Pedido com quantidade no limite do estoque
   - Pedido com produto sem estoque
   - Pedido com produto inexistente
   - Pedido com quantidade zero ou negativa

2. Para cada conjunto, forneça:
   - Request payload (JSON)
   - Response esperado (JSON) com status code
   - Descrição do cenário

3. Gere o contrato simplificado (formato OpenAPI-like) para os dois endpoints.

FORMATO DE SAÍDA:
- Dados de teste em blocos JSON com comentários
- Contratos em formato JSON estruturado
```

## Observações sobre o Prompt

- **Foco em dados concretos**: payloads JSON prontos para uso
- **Diversidade de cenários**: inclui casos de borda (zero, negativo, limite)
- **Contratos formais**: solicita definição de formato esperado
- **Realismo**: dados em português, valores monetários realistas
