# mba_dsa_2026_CruzMC
# Aprendizado de máquina interpretável na análise de assinaturas mitocondriais em Cell Painting

Este repositório reúne os cadernos computacionais e a documentação associados ao Trabalho de Conclusão de Curso de **Mario Costa Cruz**, desenvolvido no **MBA em Data Science e Analytics da USP/Esalq**.

O projeto utiliza perfis morfológicos públicos de **Cell Painting** de células HepG2 do conjunto **EU-OPENSCREEN bioactives** para investigar padrões predominantemente associados às características mitocondriais. O fluxo de análise inclui controle de qualidade, normalização baseada em controles DMSO, seleção de características, construção de escores multivariados, avaliação de repetibilidade entre réplicas e locais, agrupamento exploratório, modelagem por **XGBoost** e interpretação das predições por **SHAP**.

O ponto de partida das análises são **perfis agregados por poço** disponibilizados pelos autores do conjunto de dados. As imagens brutas não foram reprocessadas para recalcular segmentações ou características celulares.

## Organização

### `01_cadernos_principais`

Contém o fluxo principal da análise.

- `00_LEIA_PRIMEIRO_e_preparacao.ipynb` — preparação do ambiente e verificação das entradas.
- `01_IMTM_perfis_ate_SHAP.ipynb` — análise do conjunto IMTM.
- `02_FMP_perfis_ate_SHAP.ipynb` — análise do conjunto FMP.
- `03_MEDINA_perfis_ate_SHAP.ipynb` — análise do conjunto MEDINA.
- `04_USC_perfis_ate_SHAP.ipynb` — análise do conjunto USC.
- `05_MEDINA_R4_sem_retreino.ipynb` — aplicação complementar da análise à R4 do MEDINA.
- `06_comparacao_quatro_locais.ipynb` — comparação dos resultados entre IMTM, FMP, MEDINA e USC.
- `07_Alda1_imagens_R1_R3.ipynb` — inspeção das imagens de Alda-1 e controles DMSO.
- `08_Alda1_integracao_visual_quantitativa.ipynb` — integração entre avaliação visual, intensidade e perfil multivariado.
- `09_figuras_quantitativas.ipynb` — reprodução das principais figuras quantitativas.
- `10_figuras_tabelas_aprovadas_e_MEDINA_R4.ipynb` — organização dos elementos utilizados na versão final do TCC.

### `02_cadernos_suplementares`

Contém análises históricas e de sensibilidade realizadas durante o desenvolvimento do projeto, incluindo avaliação de controles, sensibilidade ao P95, transformações adicionais, agrupamentos e análises preliminares das imagens.

Esses cadernos documentam o desenvolvimento metodológico, mas não substituem as campanhas padronizadas apresentadas nos cadernos principais.

### `03_documentacao`

Reúne informações para interpretação e reprodução das análises.

Recomenda-se iniciar por:

- `guia_de_leitura_dos_codigos.md` — explicação do pipeline em linguagem voltada a leitores das áreas biológicas e da saúde;
- `mapa_do_TCC.md` — correspondência entre análises, cadernos, figuras e tabelas do TCC;
- `dados_necessarios.md` — arquivos necessários para reexecução;
- `edicoes_e_proveniencia.md` — informações de rastreabilidade dos cadernos.

## Fluxo analítico

Em termos simplificados, o procedimento segue a sequência:

**perfis por poço → controle de qualidade → normalização pelos DMSO → seleção de características → escores por blocos → priorização de padrões mitocondriais → avaliação entre réplicas → XGBoost → SHAP → comparação entre locais → inspeção das imagens**

O escore `A_Mito` resume a magnitude multivariada das características mitocondriais em relação à distribuição dos controles. Valores superiores a 1 representam excedência da referência operacional, e não significância estatística ou demonstração de disfunção mitocondrial.

Da mesma forma, a classificação de uma condição como predominantemente mitocondrial corresponde a uma **definição morfológica operacional** e não estabelece seletividade farmacológica ou mecanismo de ação.

Os modelos **XGBoost** foram utilizados para avaliar se as características morfológicas de uma réplica continham informação capaz de predizer a magnitude mitocondrial observada em outra réplica. O **SHAP** foi utilizado para identificar quais características contribuíram para essas predições. Essas contribuições descrevem o comportamento do modelo e não devem ser interpretadas como relações causais.

## Dados originais

Os dados utilizados são provenientes do conjunto **EU-OPENSCREEN bioactives**:

> Wolff et al. (2025). *Morphological profiling data resource enables prediction of chemical compound properties*. iScience 28(5): 112445.

Dados e documentação dos autores:  
https://github.com/schmiedc/EU-OS_bioactives

As imagens estão disponíveis no projeto `cpg0036-EU-OS-bioactives` da **Cell Painting Gallery**.

## Reprodução das análises

Os notebooks foram organizados para execução no **Google Colab**. Alguns cadernos dependem dos perfis originais e de arquivos auxiliares que não estão necessariamente incluídos no repositório devido ao tamanho ou à origem externa dos dados.

Antes de reexecutar as análises, consulte:

`03_documentacao/dados_necessarios.md`

A reprodução de figuras a partir de tabelas preservadas não equivale à repetição completa do treinamento dos modelos a partir dos perfis originais.

## Escopo

Este repositório documenta uma estratégia de análise de perfis morfológicos. Os resultados permitem investigar padrões quantitativos e sua repetibilidade, mas não demonstram isoladamente função mitocondrial, mecanismo molecular, toxicidade ou atividade farmacológica seletiva.
