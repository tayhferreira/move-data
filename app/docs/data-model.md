# Modelo de Dados

A API possui duas entidades: Orders (Pedidos) e Items (Itens), relacionadas por um relacionamento 1:N (um para muitos).

## Tabela: `orders`

| Coluna  | Tipo (SQLAlchemy) | Tipo (PostgreSQL) | Restrições           |
|---------|-------------------|-------------------|----------------------|
| id      | String | VARCHAR  | PRIMARY KEY, UUID gerado automaticamente |
| customer| String | VARCHAR  | NOT NULL                                 |
| status  | String  | VARCHAR | DEFAULT `open`                           |
| created_at | DateTime(timezone=True) | TIMESTAMP WITH TIME ZONE | DEFAULT data/hora atual (UTC) |

---

## Tabela: `items`

| Coluna  | Tipo (SQLAlchemy) | Tipo (PostgreSQL) | Restrições                               |
|---------|-------------------|-------------------|------------------------------------------|
| id      | String            | VARCHAR           | PRIMARY KEY, UUID gerado automaticamente |
| order_id| String            | VARCHAR           | FOREIGN KEY → `orders.id`, NOT NULL      |
| sku     | String            | VARCHAR           | NOT NULL                                 |
| descrip | String            | VARCHAR           | NOT NULL                                 |
| quant.  | Integer           | INTEGER           | NOT NULL                                 |

---

## Relacionamento

A API implementa um relacionamento 1:N (um para muitos) entre as entidades.

- Um Order (Pedido) pode possuir vários Items (Itens).
- Cada Item pertence a exatamente um Order.

A chave estrangeira `order_id` está localizada na tabela `items` e referencia a coluna `id` da tabela `orders`.

```text
orders
-------------------------
id (PK)
customer
status
created_at
      │
      │ 1
      │
      │ N
items
-------------------------
id (PK)
order_id (FK → orders.id)
sku
description
quantity
Cascade

O relacionamento foi configurado com:

cascade="all, delete-orphan"

Essa configuração significa que:

ao excluir um Order, todos os Items associados também são excluídos automaticamente;
um Item que deixar de pertencer a qualquer Order (delete-orphan) também será removido automaticamente do banco de dados.

Essa estratégia garante a integridade referencial, evitando que existam itens sem um pedido associado.