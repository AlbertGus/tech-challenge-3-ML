# 📚 Tech Challenge - Fase 3: Predição de Metas de Alfabetização Escolar

## 🎯 Contexto do Problema
A alfabetização na idade certa é o pilar fundamental para o desenvolvimento educacional e socioeconômico de longo prazo de qualquer município brasileiro. No entanto, o sucesso escolar não é um evento isolado; ele reflete um ecossistema complexo influenciado por infraestrutura escolar, condições de saneamento, acesso à tecnologia e o nível de vulnerabilidade social das famílias. Gestores públicos e tomadores de decisão enfrentam o desafio contínuo de antecipar quais redes de ensino correm o risco de não atingir suas metas educacionais, permitindo uma atuação preventiva antes que a defasagem se consolide.

## 🎯 Objetivo Analítico
Desenvolver um modelo de Machine Learning supervisionado, altamente interpretável e blindado contra vazamento de dados (*data leakage*), capaz de prever se um município/rede de ensino atingirá ou superará as metas de alfabetização estabelecidas, priorizando a mitigação de erros críticos de falso positivo para otimizar a alocação de recursos públicos.

## 🗂️ Descrição das Bases Utilizadas
O pipeline analítico integra três fontes de dados oficiais distintas, unificadas por meio de engenharia de dados automatizada utilizando o código oficial de 7 dígitos do IBGE:

* **Camada Gold (Fase 2):** Indicadores agregados de desempenho educacional, taxas de alfabetização e séries históricas de metas por município e rede de ensino.
* **Censo Escolar 2025 (INEP):** Microdados detalhados de infraestrutura escolar, abarcando indicadores de saneamento básico (água potável e esgoto), conectividade (acesso à internet), computadores e espaços de leitura/bibliotecas.
* **Atlas Brasil (IDHM e Vulnerabilidade):** Dados demográficos e socioeconômicos de referência, focados em proporções de domicílios vulneráveis à pobreza e jovens fora da escola ou do mercado de trabalho.

## ⚙️ Etapas de Modelagem
O projeto foi estruturado em um fluxo reprodutível dividido em quatro notebooks sequenciais localizados na pasta `notebooks/`:

* **`01_exploracao_base_gold.ipynb`:** Análise exploratória inicial dos indicadores educacionais da camada Gold.
* **`02_limpeza_dados_externos.ipynb`:** Extração automatizada da API de localidades do IBGE (garantindo o "De/Para" de municípios) e limpeza estruturada das bases do Atlas e Censo Escolar, com perfis técnicos documentados.
* **`03_cruzamento_e_storytelling.ipynb`:** Construção da Tabela Analítica Final (ABT) e validação visual de hipóteses de negócio através de gráficos integrados.
* **`04_modelagem_machine_learning.ipynb`:** Pré-processamento avançado com imputação de nulos, padronização e codificação, seguido de treinamento supervisionado, *tuning* de hiperparâmetros e explicabilidade.

## 🧠 Escolha do Algoritmo
O algoritmo escolhido foi o **Random Forest Classifier**. A decisão fundamenta-se na capacidade natural do modelo baseado em árvores de decisão de lidar com interações não-lineares complexas. Nossa análise exploratória demonstrou que fatores isolados possuem baixa correlação linear com a alfabetização, o que nos provou que o sucesso educacional é multifatorial e exigia um modelo capaz de ler o conjunto dessas variáveis — capturando o efeito combinado de infraestrutura deficiente e vulnerabilidade social que modelos lineares tradicionais ignoram.

## 📊 Métricas de Avaliação e Regra de Negócio
No contexto de políticas públicas educacionais, o impacto dos erros de previsão é assimétrico. Por isso, a modelagem incorporou uma forte diretriz de negócios:

* **O Falso Positivo (O Risco Crítico):** Ocorre quando o modelo prevê que o município atingirá a meta, mas ele falha. O impacto é severo, pois gestores podem reter suporte pedagógico ou recursos essenciais, deixando crianças sem o apoio necessário.
* **O Falso Negativo (O Custo Preventivo):** Ocorre quando o modelo prevê que a cidade falhará, mas ela atinge a meta. O impacto resulta no direcionamento preventivo de recursos e atenção extra, gerando um custo administrativo marginal, porém salvaguardando o objetivo educacional.

Para mitigar o falso positivo, o pipeline implementou otimização focada em **Precisão** via `GridSearchCV` com `StratifiedKFold`, além de ajuste de peso de classes (`class_weight='balanced'`) e sintonia fina do limiar de decisão (*Threshold Tuning*).

## 🔍 Interpretabilidade dos Resultados
Para erradicar a opacidade de "caixa preta" do algoritmo e garantir transparência técnica aos gestores, utilizamos a biblioteca **SHAP** (*SHapley Additive exPlanations*). O gráfico de impacto global revelou que:

* A variável de dependência administrativa (`rede`) exerce forte influência inicial na separação dos padrões de desempenho.
* Indicadores estruturais, como a presença de bibliotecas/salas de leitura (`IN_BIBLIOTECA_SALA_LEITURA`), aparecem no topo do ranking de importância preditiva.
* Os índices de vulnerabilidade social do Atlas atuam como forças direcionais que aumentam ou reduzem substancialmente a probabilidade de sucesso da rede de ensino.

## ⚠️ Limitações do Projeto
* **Desalinhamento Temporal:** As bases socioeconômicas do Atlas utilizam o censo demográfico de referência, enquanto o Censo Escolar e as metas educacionais pertencem a ciclos recentes, exigindo cautela na projeção direta de causalidade de longo prazo.
* **Agregação Municipal:** O agrupamento de indicadores por média municipal pode mascarar disparidades extremas entre escolas individuais situadas dentro da mesma rede de ensino.

## 💡 Aplicação Prática para Políticas Públicas
A ferramenta desenvolvida serve como um sistema de **alerta precoce** (*Early Warning System*) para secretarias de educação. Em vez de reagir a índices consolidados tardiamente, os gestores públicos podem utilizar o modelo para identificar antecipadamente quais redes municipais apresentam risco iminente de desvio de meta, direcionando insumos de infraestrutura, pacotes de conectividade e programas de reforço de forma cirúrgica.

## 🚀 Possíveis Evoluções Futuras
* Incorporação de séries temporais mais robustas para avaliar a evolução longitudinal das redes de ensino.
* Desagregação da análise do nível municipal para o nível de unidade escolar individual, ampliando a granularidade das intervenções.
* Implementação de um painel interativo (*Dashboard* em Streamlit ou Power BI) integrado ao pipeline para consumo em tempo real pelos tomadores de decisão.
