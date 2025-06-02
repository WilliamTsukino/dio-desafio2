# 📊 Insights de Vendas Meganium (AliExpress)

## 📌 Objetivo
Este projeto tem como objetivo analisar os dados de vendas da Meganium no AliExpress, extraindo insights estratégicos para otimização do negócio.

## 📂 Estrutura do Repositório
- `data/` → Contém os arquivos de dados brutos utilizados na análise.
- `insights/` → Relatórios gerados a partir dos dados processados.
- `prompts/` → Arquivos com os prompts utilizados para gerar insights.
- `scripts/` → Código Python para pré-processamento e análise dos dados.

## 🚀 Pré-processamento dos Dados
```python
from datetime import datetime

df['date'] = pd.to_datetime(df['date'])
df['buyer_birth_date'] = pd.to_datetime(df['buyer_birth_date'], errors='coerce')
df['year_month'] = df['date'].dt.to_period('M')
df['buyer_age'] = datetime.now().year - df['buyer_birth_date'].dt.year

# Insight 1: Produto mais vendido (quantidade e valor total)
produto_mais_vendido_qtd = df.groupby('product_sold')['quantity'].sum().sort_values(ascending=False).head(1)
produto_mais_vendido_valor = df.groupby('product_sold')['total_price'].sum().sort_values(ascending=False).head(1)

# Insight 2: Evolução de vendas por mês
vendas_por_mes = df.groupby('year_month')['total_price'].sum()

# Insight 3: Ticket médio
ticket_medio = df['total_price'].sum() / df['invoice_id'].nunique()

# Insight 4: Impacto dos cupons
desconto_total = df['discount_value'].sum()
percentual_desconto = (desconto_total / df['total_price'].sum()) * 100

# Insight 5: Top países por quantidade e valor
pais_qtd = df.groupby('delivery_country')['quantity'].sum().sort_values(ascending=False).head(1)
pais_valor = df.groupby('delivery_country')['total_price'].sum().sort_values(ascending=False).head(1)

# Insight 6: Comparativo por país (ticket médio)
ticket_pais = df.groupby('delivery_country').apply(lambda x: x['total_price'].sum() / x['invoice_id'].nunique())

# Insight 7: Distribuição de idade dos compradores
faixas_etarias = pd.cut(df['buyer_age'], bins=[0, 20, 30, 40, 50, 60, 100], right=False).value_counts().sort_index()

# Insight 8: Compradores únicos vs recorrentes
comprador_freq = df['buyer_name'].value_counts()
compradores_unicos = (comprador_freq == 1).sum()
compradores_recorrentes = (comprador_freq > 1).sum()

# Insight 9: Cupons mais usados
cupons_uso = df['discount_coupon'].value_counts().head(3)

# Insight 10: Proporção de pedidos com desconto
total_com_desconto = df[df['discount_value'] > 0]['invoice_id'].nunique()
total_pedidos = df['invoice_id'].nunique()
percentual_com_desconto = (total_com_desconto / total_pedidos) * 100

# Insight 11: Dias da semana com mais vendas
df['weekday'] = df['date'].dt.day_name()
dias_mais_vendas = df.groupby('weekday')['total_price'].sum().sort_values(ascending=False)

# Insight 12: Sazonalidade de produtos específicos
sazonalidade_produto = df.groupby(['year_month', 'product_sold'])['quantity'].sum().unstack().fillna(0)
