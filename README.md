# Testes de Integração com GenAI — Catálogo + Pedidos

## 📋 Contexto

Este repositório documenta a aplicação de **prompts estruturados com GenAI** para
geração de casos de teste de integração entre dois microsserviços:

| Serviço | Responsabilidade | Endpoint |
|---------|-----------------|----------|
| **Product Catalog Service** | Gerenciar produtos, estoque e preços | `GET /api/products/{id}` |
| **Order Service** | Criar e processar pedidos | `POST /api/orders` |

### Fluxo de Integração

```
[Order Service] --HTTP POST--> [Product Catalog Service]
                <--JSON Response--
```

Quando um pedido é criado, o Order Service consulta o Product Catalog Service
para validar disponibilidade e preço do produto.

---

## 🎯 Tipos de Prompts Utilizados

### Prompt 1 — Geração de Cenários de Integração
> Foco: mapear fluxos de integração (caminho feliz, erros, edge cases)

### Prompt 2 — Geração de Dados de Teste e Contratos
> Foco: definir payloads de request/response e dados de teste

---

## 📁 Estrutura do Repositório

```
repo-integration-tests/
├── README.md                         # Este arquivo
├── prompts/
│   ├── prompt_cenarios.md            # Prompt tipo 1 (cenários)
│   └── prompt_dados_contratos.md     # Prompt tipo 2 (dados/contratos)
├── test-cases/
│   ├── cenarios_integracao.md        # Casos gerados pelo Prompt 1
│   └── dados_contratos.md            # Casos gerados pelo Prompt 2
└── contracts/
    └── api_contracts.json            # Contratos de API gerados
```

---

## 🔗 Como os Prompts Influenciaram a Qualidade

### Prompt 1 — Cenários de Integração

**Influência na qualidade e diversidade:**
- O prompt direcionado a cenários forçou a LLM a pensar em **fluxos ponta a ponta**,
  não apenas em unidades isoladas. Isso trouxe diversidade nos casos: caminho feliz,
  timeout de comunicação, produto indisponível, preço alterado entre consulta e criação.
- Sem o prompt estruturado, a LLM tenderia a gerar apenas casos óbvios (produto existe →
  pedido criado). Com papéis definidos (persona de QA) e exemplos (few-shot), os cenários
  ficaram mais realistas e cobriram falhas de infraestrutura.

**Ajustes necessários:**
- Adicionei a instrução "inclua pelo menos 3 cenários de falha de comunicação" — sem isso,
  a LLM gerava apenas 1 erro genérico.
- Especifiquei o formato de saída (tabela com ID, descrição, pré-condições, passos,
  resultado esperado) para evitar texto corrido difícil de converter em testes.

### Prompt 2 — Dados de Teste e Contratos

**Influência na qualidade e diversidade:**
- Este prompt gerou **dados concretos e reprodutíveis**. Os payloads JSON permitiram
  implementação direta em ferramentas como Postman, RestAssured ou pytest.
- A diversidade veio da solicitação explícita de "dados boundary" (limite de estoque,
  preço zero, produto com nome especial). Sem isso, a LLM usaria apenas valores genéricos.

**Ajustes necessários:**
- Refinei pedindo "valores realistas em português" para evitar nomes como "Lorem ipsum".
- Solicitei explicitamente os contratos OpenAPI simplificados, pois a LLM tendia a
  gerar apenas dados soltos sem definir o formato esperado.

---

## 💡 Boas Práticas na Redação de Prompts

1. **Defina persona e contexto** — "Você é um QA Engineer especialista em testes de integração"
2. **Forneça o contrato de API** — A LLM precisa saber os endpoints, métodos e formatos
3. **Use few-shot examples** — Mostre 1-2 exemplos do formato esperado
4. **Especifique cobertura** — Liste categorias: happy path, erro, edge case, performance
5. **Defina formato de saída** — Tabela, JSON ou formato estruturado evita texto corrido
6. **Itere** — Primeira geração raramente é ideal; refine com feedback

---

## 🚀 GenAI como Copiloto

A GenAI atua como copiloto ao:
- **Acelerar** a definição de cenários que o tester talvez não consideraria
- **Gerar dados** realistas e variados rapidamente
- **Documentar** contratos e casos em formato estruturado
- **Complementar** (não substituir) o julgamento humano sobre cobertura e prioridade

> ⚠️ A LLM não conhece o contexto completo do sistema. O tester deve validar se os
> cenários gerados fazem sentido para a arquitetura real.

---

## 📌 Como Executar

Os casos de teste estão documentados em Markdown para leitura humana.
Os contratos em JSON podem ser importados em ferramentas de teste de API.

Para executar os testes em automação, recomenda-se:
- **Postman/Newman** para testes manuais e de coleção
- **pytest + requests** para automação em Python
- **RestAssured** para automação em Java
