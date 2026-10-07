# **Guia de leitura dos códigos**

## **Visão geral**

Este repositório reúne os códigos utilizados na análise de perfis morfológicos de Cell Painting descrita no trabalho **“Aprendizado de máquina interpretável na análise de assinaturas mitocondriais em Cell Painting”**.

O objetivo desta documentação é explicar a lógica dos cadernos computacionais para leitores das áreas biológicas e da saúde. Assim, o foco não está na sintaxe do Python, mas em responder três perguntas para cada etapa:

**O que entra na análise? O que o código calcula? Como o resultado deve ser interpretado biologicamente?**

O ponto de partida não são imagens brutas de microscopia, mas **perfis morfológicos agregados por poço**, previamente calculados a partir das imagens do conjunto EU-OPENSCREEN bioactives. Cada perfil contém centenas ou milhares de medidas que descrevem propriedades como intensidade de fluorescência, textura, forma, granularidade e distribuição espacial.

O código reorganiza essas medidas para responder a três questões centrais:

1. existem condições com alterações predominantemente associadas às características mitocondriais?  
2. essas alterações são reproduzidas entre réplicas e locais experimentais?  
3. as características de uma réplica permitem predizer a magnitude da resposta mitocondrial observada em outra?

---

# **1\. Como interpretar os dados**

## **1.1 O que representa uma linha dos dados?**

A unidade básica analisada é um **poço da microplaca**.

Embora muitas células sejam fotografadas e analisadas em cada poço, as características utilizadas neste trabalho já foram agregadas pelos autores do conjunto de dados. Portanto, cada linha do arquivo representa uma síntese quantitativa da população de objetos celulares daquele poço.

Isso é importante porque:

* células individuais do mesmo poço não são tratadas como réplicas experimentais;  
* campos de imagem do mesmo poço também não constituem réplicas independentes;  
* as réplicas experimentais são identificadas como R1, R2, R3 e R4.

Uma condição é definida pela combinação entre composto, concentração e placa.

---

# **2\. Organização do repositório**

## **Caderno 00 — preparação**

`00_LEIA_PRIMEIRO_e_preparacao.ipynb`

Este caderno verifica se os arquivos necessários estão disponíveis e organiza os caminhos utilizados pelos demais notebooks.

Ele não realiza a análise biológica propriamente dita.

**Em termos práticos:** corresponde à preparação da bancada antes de iniciar um experimento, verificando se todos os materiais necessários estão presentes e identificados corretamente.

---

# **3\. Análise independente dos quatro locais**

Os cadernos:

* `01_IMTM_perfis_ate_SHAP.ipynb`  
* `02_FMP_perfis_ate_SHAP.ipynb`  
* `03_MEDINA_perfis_ate_SHAP.ipynb`  
* `04_USC_perfis_ate_SHAP.ipynb`

aplicam essencialmente o mesmo fluxo aos quatro locais experimentais.

A análise é feita separadamente porque cada local possui sua própria distribuição de controles e suas próprias características experimentais.

## **3.1 Auditoria inicial**

Antes de calcular qualquer assinatura, o código verifica:

* presença dos arquivos esperados;  
* duplicações;  
* valores ausentes ou não finitos;  
* identificação de placas e réplicas;  
* número de objetos utilizados na formação de cada perfil.

Essa etapa corresponde a um **controle de qualidade dos dados**.

Um perfil existente no arquivo não é automaticamente considerado biologicamente confiável. Por exemplo, poços formados a partir de poucos objetos celulares são sinalizados para inspeção.

---

# **4\. Normalização em relação aos controles DMSO**

As características originais possuem escalas muito diferentes.

Uma medida de intensidade pode assumir centenas ou milhares de unidades, enquanto uma medida de textura pode variar em uma escala muito menor. Esses valores não podem ser comparados diretamente.

Para resolver esse problema, cada característica é expressa em relação aos **controles DMSO da mesma placa e réplica**.

De maneira simplificada, o código pergunta:

> Quanto esta medida do tratamento se afasta do comportamento típico dos controles da mesma placa?

A normalização utiliza a mediana e o desvio absoluto mediano (MAD), que são medidas robustas e menos sensíveis a valores extremos.

Após essa transformação:

* valores próximos de zero representam medidas semelhantes aos controles;  
* valores positivos indicam aumento em relação à referência;  
* valores negativos indicam redução;  
* a magnitude do valor representa o tamanho relativo do desvio.

A normalização não transforma uma característica em teste estatístico.

Um valor normalizado elevado não significa, por si só, significância estatística ou alteração funcional.

---

# **5\. Seleção das características**

Os perfis de Cell Painting possuem grande número de variáveis, e várias delas descrevem aspectos muito semelhantes da célula.

O código remove:

* medidas tecnicamente inadequadas;  
* características sem variação útil;  
* características com valores inválidos;  
* variáveis altamente redundantes.

A redundância é avaliada por correlação.

Quando duas características apresentam comportamento muito semelhante ao longo dos poços, uma delas pode ser removida para evitar que a mesma informação tenha peso excessivo nas análises seguintes.

Essa etapa reduz a dimensionalidade, mas **não escolhe características com base no nome ou mecanismo conhecido dos compostos**.

---

# **6\. Organização das medidas em blocos**

As características são agrupadas segundo a origem de sua informação.

Os principais blocos utilizados são:

* **Mito** — características associadas ao canal mitocondrial;  
* **AGP** — actina, Golgi e membrana plasmática;  
* **DNA** — características relacionadas à marcação nuclear;  
* **ER** — retículo endoplasmático/RNA;  
* **Cells, Cyto e Nuc** — grupos morfológicos derivados dos objetos celulares, citoplasmáticos e nucleares.

Esses blocos são agrupamentos analíticos de características.

Eles não devem ser interpretados como compartimentos biológicos totalmente independentes, porque alterações celulares podem afetar simultaneamente várias estruturas.

---

# **7\. Construção do escore de magnitude**

Para cada bloco, o código resume as várias características em uma medida de **magnitude multivariada**.

Primeiro é calculada a raiz da média dos quadrados das características normalizadas, denominada RMS.

Biologicamente, o RMS responde aproximadamente:

> Considerando todas as características deste bloco ao mesmo tempo, qual é a magnitude global da diferença em relação aos controles?

Em seguida, esse valor é comparado à distribuição dos controles DMSO.

O percentil 95 dos controles é utilizado como referência, produzindo o escore:

**A \= RMS / P95 dos controles**

Assim:

* `A = 1` corresponde ao limite operacional adotado;  
* `A > 1` significa que a magnitude está acima da referência definida pelos controles;  
* `A < 1` significa que permanece dentro dessa referência.

É fundamental não interpretar `A > 1` como:

* valor de p;  
* probabilidade;  
* demonstração de toxicidade;  
* demonstração de disfunção mitocondrial;  
* prova de seletividade farmacológica.

Trata-se apenas de um **limiar operacional de magnitude morfológica**.

---

# **8\. Definição da assinatura predominantemente mitocondrial**

A presença de `A_Mito > 1` não é suficiente para classificar uma condição como predominantemente mitocondrial.

Isso ocorre porque uma perturbação celular ampla pode alterar simultaneamente mitocôndrias, núcleo, retículo endoplasmático e morfologia celular.

Por isso, a regra utilizada no trabalho exige conjuntamente:

* três réplicas avaliáveis;  
* `A_Mito > 1` em pelo menos duas réplicas;  
* ausência de excedência dos demais blocos;  
* concordância direcional dos perfis mitocondriais entre réplicas.

O objetivo é encontrar condições em que a alteração mitocondrial se destaque em relação ao restante do perfil.

Ainda assim, a classificação significa:

**“perfil morfológico predominantemente mitocondrial segundo a regra definida”**

e não:

**“composto biologicamente seletivo para mitocôndrias”.**

Essa distinção é central para a interpretação dos resultados.

---

# **9\. Reamostragem dos controles**

O código também testa quanto a classificação depende dos controles utilizados para definir o P95.

Para isso, os controles DMSO são reamostrados diversas vezes e o limite é recalculado.

Se uma condição permanece classificada em grande parte dessas reamostragens, sua classificação é relativamente pouco sensível à composição específica dos controles.

Esse procedimento avalia **robustez da referência**.

Ele não estima a probabilidade de a condição se repetir em um novo experimento.

---

# **10\. Avaliação em R4**

R1, R2 e R3 são utilizadas no conjunto de desenvolvimento.

A R4 é utilizada para perguntar:

> Uma condição identificada anteriormente volta a apresentar o mesmo padrão em outra réplica?

Nesta etapa:

* as características selecionadas não são redefinidas;  
* o P95 de desenvolvimento não é recalculado;  
* não são escolhidos novos candidatos com base em R4.

Portanto, R4 avalia a repetição de uma regra previamente definida.

Há diferenças históricas entre os locais quanto ao momento em que R4 foi consultada; essas diferenças são registradas no repositório e devem ser consideradas ao interpretar a independência dessa avaliação.

O caderno:

`05_MEDINA_R4_sem_retreino.ipynb`

realiza a aplicação complementar de R4 no MEDINA sem novo treinamento ou busca de candidatos.

---

# **11\. Comparação entre diferentes componentes do perfil**

Além da regra de prioridade, os códigos verificam quantas condições apresentam excedência em cada bloco.

Essa análise responde:

> Quando Mito está alterado, os outros componentes celulares também costumam estar alterados?

Esse resultado ajuda a distinguir uma alteração concentrada no bloco mitocondrial de uma resposta celular mais ampla.

---

# **12\. Agrupamento não supervisionado por K-means**

O K-means é utilizado de forma exploratória para verificar se condições com respostas semelhantes se organizam espontaneamente em grupos.

Foram examinadas duas representações.

### **Magnitude**

Considera o tamanho global das alterações nos canais.

Duas condições podem ficar próximas porque ambas apresentam respostas fortes, mesmo que o equilíbrio entre os canais não seja idêntico.

### **Contraste**

Reduz a influência da magnitude global e enfatiza quais canais estão relativamente mais ou menos alterados.

Assim, duas condições podem ser agrupadas por apresentarem um padrão proporcional semelhante.

Os grupos formados pelo K-means são **grupos matemáticos de perfis**.

Eles não representam automaticamente mecanismos moleculares, doenças ou classes farmacológicas.

A estabilidade entre réplicas é avaliada pelo índice de Rand ajustado (ARI).

---

# **13\. Predição entre réplicas**

A modelagem supervisionada responde a outra pergunta.

Ela não pergunta se uma condição é ou não candidata.

A pergunta é:

> As características morfológicas observadas em uma réplica permitem estimar a magnitude mitocondrial observada em outra?

O alvo utilizado é `log(1 + A_Mito)`.

São avaliadas todas as seis direções possíveis entre R1, R2 e R3.

Exemplos:

* R1 → R2;  
* R1 → R3;  
* R2 → R1;  
* etc.

---

# **14\. Separação entre treinamento e teste**

Um ponto importante é evitar que o mesmo composto apareça simultaneamente no treinamento e no teste em doses diferentes.

Por isso, as partições são agrupadas pelo código EOS.

Dessa forma, quando um composto é utilizado no teste, as condições associadas ao mesmo código não são utilizadas para treinar aquele modelo.

Essa estratégia reduz o risco de desempenho artificialmente elevado causado pela presença de observações muito relacionadas nos dois conjuntos.

---

# **15\. Modelos comparados**

O XGBoost é comparado a alternativas mais simples.

Entre elas estão:

* uso de um valor central do conjunto de treinamento;  
* cópia direta do A\_Mito da réplica de origem;  
* calibração linear;  
* regressões Ridge;  
* XGBoost.

Essa comparação é importante porque permite perguntar se um modelo mais complexo realmente acrescenta informação em relação a estratégias mais simples.

---

# **16\. Como interpretar MAE, RMSE, R² e Spearman**

### **MAE**

Representa o erro absoluto médio.

Quanto menor, melhor.

### **RMSE**

Também mede erro, mas penaliza mais fortemente grandes erros.

### **R²**

Avalia quanto da variação do alvo é acompanhada pelas predições.

Não representa “porcentagem de acerto”.

### **Correlação de Spearman**

Avalia se o modelo preserva a ordenação relativa das condições.

Uma boa correlação pode existir mesmo quando os valores absolutos das predições não são perfeitos.

---

# **17\. Interpretação com SHAP**

O XGBoost combina muitas características simultaneamente, dificultando saber quais informações sustentam cada predição.

O SHAP é utilizado para decompor as predições e indicar quanto cada característica contribuiu para deslocar o valor previsto.

As características são posteriormente reunidas por:

* medida individual;  
* família;  
* bloco.

Dessa maneira, é possível perguntar, por exemplo:

> O modelo depende principalmente de características de Mito ou utiliza também informações de outras dimensões do perfil?

Valores elevados de SHAP indicam importância para a **predição do modelo**.

Eles não demonstram que a característica seja causalmente responsável pelo fenômeno biológico.

---

# **18\. Comparação entre os quatro locais**

O caderno:

`06_comparacao_quatro_locais.ipynb`

compara IMTM, FMP, MEDINA e USC.

A análise verifica diferentes níveis de concordância:

* condições prioritárias compartilhadas;  
* magnitude mitocondrial;  
* ordenação dos compostos;  
* proximidade ao limiar;  
* distribuição por placa;  
* comportamento dos controles.

As características e referências são calculadas separadamente em cada local.

Portanto, os valores não representam uma unidade absoluta comum aos quatro experimentos.

A comparação pergunta se **o mesmo procedimento aplicado independentemente** recupera padrões semelhantes.

---

# **19\. Alda-1 como estudo de caso**

Os cadernos:

* `07_Alda1_imagens_R1_R3.ipynb`  
* `08_Alda1_integracao_visual_quantitativa.ipynb`

ligam os perfis quantitativos às imagens de microscopia.

Alda-1 foi utilizada para comparar três níveis diferentes de descrição:

1. aparência visual;  
2. intensidade agregada;  
3. perfil multivariado.

Essas medidas não são equivalentes.

Uma condição pode apresentar A\_Mito elevado sem aumento visual evidente da fluorescência, porque A\_Mito combina várias propriedades além da intensidade média.

Da mesma forma, uma imagem visualmente mais brilhante não necessariamente produz uma assinatura multivariada concordante entre réplicas.

A inspeção visual serve como contextualização e não como validação funcional.

---

# **20\. Figuras e tabelas**

O caderno:

`09_figuras_quantitativas.ipynb`

reproduz figuras quantitativas a partir de tabelas e resultados preservados.

O caderno:

`10_figuras_tabelas_aprovadas_e_MEDINA_R4.ipynb`

organiza os elementos apresentados na versão final do TCC e confere resultados associados ao complemento de MEDINA/R4.

Esses cadernos não substituem os notebooks nos quais os modelos foram originalmente ajustados.

---

# **21\. Cadernos suplementares**

A pasta `02_cadernos_suplementares` contém análises realizadas durante o desenvolvimento do projeto.

Entre elas estão:

* ZCA/Ledoit-Wolf;  
* análise multibloco;  
* agrupamentos históricos;  
* auditoria dos DMSO;  
* sensibilidade ao P95;  
* calibração residual;  
* análises preliminares das imagens.

Esses cadernos documentam decisões analíticas e testes de sensibilidade.

Eles não devem ser interpretados como novas campanhas independentes ou como substitutos das análises principais.

---

# **22\. O que este código permite concluir**

Os códigos permitem avaliar:

* magnitude de alterações morfológicas;  
* predominância relativa do bloco mitocondrial;  
* repetibilidade entre réplicas;  
* estabilidade em relação aos controles;  
* organização exploratória dos perfis;  
* previsibilidade entre réplicas;  
* características utilizadas pelos modelos.

Os códigos **não demonstram diretamente**:

* função mitocondrial;  
* toxicidade;  
* mecanismo molecular;  
* seletividade farmacológica;  
* eficácia terapêutica;  
* causalidade biológica.

Essas perguntas exigem validação experimental específica.

---

# **23\. Ordem recomendada para leitura**

Para um leitor que não trabalha rotineiramente com programação, recomenda-se:

**1\.** ler este guia;  
**2\.** abrir `00_LEIA_PRIMEIRO_e_preparacao.ipynb`;  
**3\.** escolher um dos cadernos 01–04 e ler primeiro as células de texto;  
**4\.** acompanhar a sequência normalização → seleção → A\_Mito → prioridade → ML → SHAP;  
**5\.** consultar o caderno 06 para entender a comparação entre locais;  
**6\.** finalizar com os cadernos 07 e 08, que conectam os números às imagens.

Não é necessário compreender cada linha de Python para entender a lógica científica da análise.

&nbsp;

&nbsp;