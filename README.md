# Desaparecidos na Cidade de São Paulo (2015–2025)

Análise de dados produzida para a reportagem especial **"Vidas em suspenso: Periferias registram maior número de desaparecidos em SP"**, publicada pela **Agência Mural** (@agenciamural).

🔗 **Leia a reportagem completa:** [agenciamural.org.br/especiais/desaparecidos-em-sp](https://agenciamural.org.br/especiais/desaparecidos-em-sp/)

---

## Como fizemos?

### 1. FONTE DOS DADOS
Os dados foram obtidos via portal **FalaSP** sobre pedido de **Lei de Acesso à Informação (LAI)**, Lei nº 12.527/2011 (Protocolo `2025072815313670`), referentes a Boletins de Ocorrência (BOs) de desaparecimento de pessoas no Estado de São Paulo, extraídos do sistema de Registro Digital de Ocorrências (RDO) e São Paulo Polícia Judiciária (SPJ).

* **Período analisado:** Janeiro de 2015 a julho de 2025
* **Município:** São Paulo (capital)
* **Total de registros analisados:** 74.026 boletins de ocorrência

---

### 2. LIMITAÇÕES E CONSIDERAÇÕES IMPORTANTES

#### 2.1. Sobre a natureza dos dados
> **ATENÇÃO:** Os dados representam **BOLETINS DE OCORRÊNCIA** registrados, não o número real de desaparecimentos.

Isso significa que:
* Pessoas que desapareceram mas não tiveram BO registrado **não** estão nos dados;
* Uma mesma pessoa pode gerar múltiplos BOs (ao longo do tempo ou em diferentes delegacias);
* Pessoas encontradas continuam na base (o sistema registra o BO, não necessariamente a resolução).

#### 2.2. Implementação gradual do sistema
Conforme a metodologia oficial da SSP-SP:
> *“A implantação do sistema RDO (Registro Digital de Ocorrências) foi concretizada de modo gradual nas diversas unidades policiais do Estado, tendo sua abrangência alcançado todos os municípios apenas a partir do ano de 2010.”*

**Implicações:**
* Dados de 2015–2016 podem ter subnotificação por conta da forma de registro dos dados;
* Comparações temporais devem considerar essa limitação.

#### 2.3. Registros fora da circunscrição
Segundo a metodologia oficial da SSP:
> *“Cerca de 60% das ocorrências são registradas fora de sua circunscrição, ou seja, local de ocorrência.”*

**Para esta análise:**
* Utilizado o campo `NOME_DELEGACIA_CIRC` (delegacia de circunscrição = local real da ocorrência);
* Não utilizado `NOME_DELEGACIA` (delegacia de registro).

#### 2.4. Inconsistências nos dados
As bases apresentam inconsistências inerentes ao processo de registro, incluindo:

* **a) Variações na grafia de bairros:**
  * Exemplo: `“GRAJAU”`, `“GRAJAÚ”`, `“PQ GRAJAU”`, `“PARQUE GRAJAU”`.
  * **Decisão metodológica:** Não unificamos variações para evitar distorções (ex.: “Parque Grajaú” pode significar que o registro foi feito não necessariamente no bairro do Grajaú).
* **b) Variações na classificação de cor/raça:**
  * Duplicatas nos valores: `“Branca”` e `“Branca ”` (registrado com espaço).
  * **Solução aplicada:** Padronização com remoção de espaços e conversão para maiúsculas.
* **c) Dados faltantes (Capital):**
  * **Bairro:** 2,6% campos sem informação
  * **Cor/raça:** 19,6% campos sem informação
  * **Idade:** 1,2% campos sem informação (1,8% no recorte estadual)
* **d) Mudanças na estrutura do sistema:**
  * *“As tabelas e campos de preenchimento deste sistema são objetos de constante aperfeiçoamento”*
  * Base 2015–2019: 57 colunas
  * Base 2020–2022: 57 colunas
  * Base 2023–2025: 60 colunas (campos adicionais)

#### 2.5. Tratamento de duplicatas
Conforme orientação oficial:
> *“Cada linha constante na tabela registra os dados de uma pessoa, natureza ou objeto relacionado no boletim. (…) Para conclusões quanto as quantidades de ocorrências é necessária exclusão das duplicidades por meio dos campos: NOME_DELEGACIA, ANO_BO, NUM_BO”*

#### 2.6. Ano de 2025 incompleto

> Os dados de 2025 cobrem apenas de janeiro a julho. Comparações entre 2025 e anos fechados (2015 a 2024) não são equivalentes e devem considerar essa diferença de período.

**Procedimento aplicado:**
* **Duplicatas removidas:** 9.735 registros
* **Critério:** Combinação única de `NOME_DELEGACIA` + `ANO_BO` + `NUM_BO`
* Manteve-se o primeiro registro em caso de duplicidade.

---

### 3. PROCESSAMENTO DOS DADOS

#### 3.1. Ferramentas utilizadas
* **Plataforma:** Google Colaboratory (Colab)
* **Linguagem:** Python (bibliotecas: `pandas`, `numpy`, `matplotlib`, `seaborn`)
* **Assistência:** IA (Claude / Gemini) para desenvolvimento e revisão de código

#### 3.2. Etapas do processamento
1. **Carregamento das bases:** 3 arquivos XLSB carregados (2015–2019, 2020–2022, 2023–2025) — total bruto de **234.513 registros**.
2. **Limpeza e padronização:** Remoção de colunas duplicadas, padronização de nomes de colunas entre períodos diferentes e identificação de **51 colunas comuns** às três bases.
3. **Unificação:** Concatenação das três bases em um único dataframe utilizando apenas as colunas comuns.
4. **Remoção de duplicatas:** Aplicação do critério oficial (`NOME_DELEGACIA` + `ANO_BO` + `NUM_BO`) — **9.735 duplicatas removidas**, resultando em **224.778 registros únicos** no Estado.
5. **Filtro geográfico:** Filtro aplicado (`CIDADE == "S.PAULO"`), chegando à base final de **74.026 registros**.
6. **Tratamento de datas:** Como o campo `DATA_OCORRENCIA_BO` veio em formato serial na conversão XLSB, utilizou-se o campo `ANO_BO` como referência temporal.
7. **Análise exploratória:** Distribuição temporal (2015–2025), perfil demográfico (sexo, idade, cor/raça), análise geográfica (bairros e delegacias) e cruzamentos (sexo × faixa etária).

---

### 4. NÃO SE TRATAM DE ESTATÍSTICAS OFICIAIS
> **IMPORTANTE:** Conforme a própria SSP-SP:
> *“A inclusão ou alteração de um campo e respectivos períodos de implementação podem influenciar diretamente nos critérios de pesquisa executados, não havendo na base fornecida o tratamento metodológico necessário para qualificá-las como dados estatísticos oficiais.”*
>
> *“O número total de boletins de ocorrências registrados sob uma natureza criminal não representa a estatística criminal do Estado ou de determinada área ou região.”*

Esta análise trabalha com dados primários brutos, não com estatísticas oficialmente tratadas.

---

### 5. RECORTE ANALÍTICO
**Objetivo:** Analisar o perfil dos desaparecimentos registrados na cidade de São Paulo, com atenção especial a regiões periféricas.

**Justificativa da abordagem:**
* Não foi aplicada uma definição rígida de “periferia” para evitar viés classificatório;
* O recorte sobre "periferias" é total usado pela Agência Mural;
* A análise apresenta os dados por bairros e delegacias, permitindo interpretações contextualizadas;
* A classificação de regiões como “periféricas” fica a cargo da análise qualitativa posterior;
* **Total de bairros identificados:** 6.016 valores únicos registrados.

---

### 6. CONSIDERAÇÕES FINAIS
Esta análise reconhece suas limitações e busca transparência metodológica ao invés de conclusões definitivas. Os números aqui apresentados refletem BOs registrados (não desaparecimentos reais totais), dados com inconsistências inerentes ao sistema de registro e um recorte temporal específico com variações na cobertura do sistema.

Qualquer interpretação deve considerar:
* Subnotificação (pessoas que desaparecem mas não geram BO);
* Possibilidade de múltiplos registros da mesma pessoa;
* Heterogeneidade na qualidade dos dados ao longo do tempo;
* Diferenças entre local de registro e local de ocorrência;
* Variações na grafia e classificação de campos descritivos.

---

### RESUMO DO PROCESSO
1. Obtenção de 3 bases de dados via FalaSP (formato XLSB)
2. Carregamento no Google Colab usando Python/`pandas`
3. Limpeza: remoção de colunas duplicadas e padronização
4. Unificação das três bases (234.513 registros)
5. Remoção de duplicatas conforme metodologia oficial (9.735 removidas)
6. Filtro para município de São Paulo (74.026 registros finais)
7. Análise exploratória e revisão de código
8. Geração de rankings e estatísticas descritivas
9. Exportação de bases tratadas para análise posterior
