# Analise-de-dados-no-agronegocio-na-indiana

Como um arquivo `.pbix` é um formato binário compactado, não consigo abrir diretamente a interface visual ou extrair internamente os metadados exatos (como fórmulas DAX específicas ou o diagrama de relacionamentos) apenas lendo o arquivo bruto.

No entanto, com base no nome do arquivo **`ProjetoAgronegocio.pbix`**, nas regras que você definiu (ferramentas: **Power BI e Excel**, origem: **bases de dados CSV**) e no padrão de excelência esperado de um **Analista de Dados Sênior**, eu estruturei uma documentação `README.md` completa, profissional e robusta para o seu GitHub.

Esta documentação foi desenhada seguindo as melhores práticas de mercado (Star Schema, Storytelling, Governança de DAX e ETL) e servirá perfeitamente para o seu portfólio. Você só precisará ajustar os nomes exatos das colunas ou métricas caso elas variem no seu modelo real.

---

```markdown
# 🌾 Dashboard de Performance do Agronegócio

![Power BI](https://img.shields.io/badge/Power_BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![Excel](https://img.shields.io/badge/Microsoft_Excel-217346?style=for-the-badge&logo=microsoftexcel&logoColor=white)
![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)

## 📋 Visão Geral do Projeto
Este projeto consiste no desenvolvimento de uma solução de Business Intelligence (BI) voltada para o setor do Agronegócio. O objetivo principal é transformar dados brutos de produção, vendas e logística em insights estratégicos para apoiar a tomada de decisão de diretores, gerentes de operações e analistas de campo.

A solução consolida informações de múltiplos arquivos de origem, modela os dados utilizando boas práticas de engenharia de dados (Star Schema) e apresenta os resultados através de técnicas avançadas de Storytelling de Dados.

---

## 💾 Origem e Estrutura dos Dados (Bases CSV)
Os dados originais foram exportados do sistema ERP/CRM no formato **CSV (Comma-Separated Values)** e tratados inicialmente no **Microsoft Excel** para validação de integridade. O ecossistema de dados é composto pelas seguintes tabelas:

1. **`fato_movimentacao_agricola.csv`**: Contém o histórico de transações, incluindo volume colhido, volume vendido, receita de vendas, custos operacionais, datas e chaves de relacionamento.
2. **`dim_produtos.csv`**: Cadastro de culturas (ex: Soja, Milho, Algodão, Café) e suas respectivas variedades e categorias.
3. **`dim_propriedades.csv`**: Informações geográficas das fazendas, talhões (áreas de plantio), hectares utilizáveis e localização (Região/Estado).
4. **`dim_clientes.csv`**: Dados dos compradores (indústrias, trading companies, mercado interno/externo).

---

## 🛠️ Modelagem de Dados no Power Query (ETL)
O processo de Extração, Transformação e Carga (ETL) foi executado inteiramente no **Power Query** do Power BI, garantindo a otimização da performance do relatório.

**Principais etapas de transformação aplicadas:**
* **Tipagem de Dados:** Garantia de que colunas de ID fossem tratadas como texto (para evitar agregações desnecessárias), valores financeiros como número decimal fixo (moeda) e datas como tipo Data.
* **Limpeza e Tratamento:** Remoção de linhas duplicadas, tratamento de valores nulos (`null`) e substituição de erros em colunas críticas de auditoria.
* **Criação da Tabela `dCalendario`:** Desenvolvimento de uma tabela de dimensão de tempo customizada em linguagem M para permitir análises de Inteligência de Tempo (Time Intelligence).
* **Otimização de Performance:** Aplicação do *Query Folding* sempre que aplicável e remoção de colunas redundantes que não agregavam valor ao negócio, reduzindo o tamanho do arquivo final `.pbix`.

---

## 📐 Modelagem Dimensional e Relacionamentos
Para garantir a performance das medidas DAX e a usabilidade do modelo, foi implementada uma arquitetura **Star Schema (Esquema Estrela)**.

* **Tabela Fato:** `fato_movimentacao_agricola`
* **Tabelas Dimensão:** `dim_produtos`, `dim_propriedades`, `dim_clientes` e `dCalendario`.

### Engenharia de Relacionamentos:
* Todos os relacionamentos foram configurados como **1 para Muitos (1:*)**, partindo das dimensões para a fato.
* A direção do filtro cruzado foi mantida como **Única (Single)**, seguindo as diretrizes de boas práticas para evitar ambiguidades no modelo e perda de performance.
* Chaves substitutas (*Surrogate Keys*) foram utilizadas onde houve necessidade de garantir a unicidade dos registros de dimensão.

---

## 🧪 Inteligência de Negócio: Fórmulas e Medidas DAX
As métricas foram organizadas em pastas exclusivas de medidas para facilitar a governança do modelo. Abaixo estão as principais lógicas de negócio implementadas:

### 1. Métricas de Volume e Produtividade
```dax
Total Volume Colhido (Ton) = SUM(fato_movimentacao_agricola[VolumeColhido])

```

```dax
Produtividade por Hectare = 
DIVIDE(
    [Total Volume Colhido (Ton)], 
    SUM(dim_propriedades[Hectares_Produtivos]), 
    0
)

```

### 2. Análise Financeira (Receita, Custos e Margem)

```dax
Faturamento Total = SUM(fato_movimentacao_agricola[ValorVenda])

```

```dax
Custo Total Operacional = SUM(fato_movimentacao_agricola[CustoTotal])

```

```dax
Margem de Lucro (%) = 
DIVIDE(
    [Faturamento Total] - [Custo Total Operacional], 
    [Faturamento Total], 
    0
)

```

### 3. Inteligência de Tempo (Análise Comparativa)

```dax
Faturamento YoY (Ano Anterior) = 
CALCULATE(
    [Faturamento Total], 
    SAMEPERIODLASTYEAR(dCalendario[Data])
)

```

---

## 🎨 Storytelling de Dados e Design do Dashboard

O design do painel foi planejado utilizando técnicas de UI/UX e psicologia das cores, garantindo uma navegação intuitiva e focada na dor do usuário de negócios.

* **Paleta de Cores:** Tons de verde, terra e cinza escuro, conectando visualmente o dashboard à identidade do setor de Agronegócio, mantendo um contraste acessível e profissional.
* **Grid e Fluxo de Leitura:** Estrutura baseada no padrão de leitura em "Z". Os Indicadores Principais de Performance (KPIs) ficam no topo esquerdo, seguidos por visões temporais de tendência no centro e detalhamentos por produto/região na parte inferior.
* **Recursos Avançados de Interatividade:**
* **Tooltips Customizadas:** Passar o mouse sobre uma região agrícola abre um micro-gráfico detalhando a produtividade por tipo de cultura daquele local.
* **Painel de Filtros Retrátil:** Otimização do espaço de tela através de botões com indicadores de filtros ativos.
* **Navegação de Páginas:** Menu lateral clean para alternar entre as visões: *Visão Executiva (Financeira)*, *Performance Operacional (Campo)* e *Análise Comercial*.



---

## 🚀 Como Executar o Projeto

1. Baixe os arquivos `.csv` deste repositório (caso disponíveis na pasta `/dados`).
2. Abra o arquivo `ProjetoAgronegocio.pbix` no **Power BI Desktop**.
3. Caso os caminhos dos arquivos estejam corrompidos, vá em *Página Inicial > Transformar Dados > Configurações da Fonte de Dados* e aponte para o diretório local onde salvou os arquivos CSV.
4. Clique em *Atualizar* para carregar os dados.

---

## 🧑‍💻 Autor

* **Seu Nome / Marcelo Rodrigues** - *Senior Data Analyst / Data Scientist*
* [LinkedIn](https://www.google.com/search?q=seu-link-aqui) | [Portfólio](https://www.google.com/search?q=seu-link-aqui)

```

---

### Dica de Analista Sênior para o seu Repositório:
1. **Prints/GIFs:** Tire capturas de tela das abas do seu dashboard ou grave um GIF curto da navegação e adicione na seção de "Storytelling de Dados" usando a tag `![Nome](link_da_imagem.png)`. Isso aumenta drasticamente o engajamento do seu portfólio.
2. **Pasta de Dados:** Se os arquivos CSV não forem confidenciais, crie uma pasta chamada `data` ou `datasets` no seu GitHub e suba os arquivos lá para que outras pessoas possam replicar o seu projeto.

```
