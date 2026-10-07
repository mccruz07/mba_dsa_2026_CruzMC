# Dados, dependências e limites de reprodução

## Entradas que não foram duplicadas

Os 112 CSVs agregados de origem (quatro locais, sete placas, quatro réplicas), matrizes completas, modelos treinados e TIFFs permanecem fora desta distribuição. Os identificadores SHA-256 dos arquivos de cada campanha estão em `04_fontes_e_referencia/recursos/campanhas/<LOCAL>/entradas_sha256.json`.

Pastas originais dos perfis:

- IMTM: https://drive.google.com/drive/folders/12fSaPsWXavic_hcmJFFy_hjY1hmQRvEj
- FMP: https://drive.google.com/drive/folders/1igixEqSCcDpVAo6LPc2WA_HgW9k-Q9jQ
- MEDINA: https://drive.google.com/drive/folders/1qsurIq2S4unIoShskP-OrKotLgj9Z5aJ
- USC: https://drive.google.com/drive/folders/1Kzbu-GRK_WWG9uHav1798v6zJIbpvy44

Esses endereços identificam as pastas verificadas no projeto; a disponibilidade para outros usuários depende das permissões das fontes. O repositório dos autores também documenta a distribuição pública. Não invente nomes ou troque CSVs corrigidos por versões antigas. No IMTM, as versões aprovadas de B1001/R4 e B1007/R4 não devem ser trocadas uma segunda vez.

## O que cada etapa exige

**01–04:** arquivos `*_CP_Profiles_Aggregated.csv` em `MyDrive/mba_tcc/<LOCAL>_HepG2/` e ZIP documental correspondente em `pipelines_<LOCAL>_HepG2/`. O caderno 00 prepara somente os ZIPs documentais ausentes. O seletor `PASTA_RETOMAR=None` inicia uma nova pasta com data/hora; indicar uma pasta não restaura as variáveis em memória. Execute as seções anteriores em ordem. Não ignore incompatibilidades de ambiente.

**05:** campanha MEDINA de desenvolvimento, arquivos congelados e sete CSVs R4. Usa o complemento que resolveu a retomada, sem carregar XGBoost. `AUTORIZAR_R4=False` nesta cópia requer decisão explícita de reanalisar.

**06:** `recursos/fontes/fontes_comparacao_padronizada_4locais.zip`, incluído. Reproduz a comparação a partir de saídas, não retreina os modelos nem recompõe os perfis brutos. Alguns textos/diagnósticos do snapshot são anteriores à abertura MEDINA/R4; o estado final está no caderno 10.

**07:** mapas incluídos, acesso ao acervo público de TIFFs e espaço para cache. Pode reutilizar o piloto já presente no Drive. R4 visual permanece desligada porque o TCC descreve a inspeção de R1–R3. Os identificadores dos campos não são réplicas experimentais adicionais.

**08:** ficha humana e fontes de integração incluídas; 12 CSVs agregados B1001/R1–R3 e os quatro ZIPs de leitura finais nas pastas de resultados originais. Os nomes exatos estão em `campanhas_do_TCC.json`. Os arquivos não foram duplicados para evitar transformar o repositório de código em um depósito de todas as observações. Não substituir por outra campanha com nome parecido.

**09:** tabelas derivadas incluídas em `recursos/tabelas`. Os cinco gráficos quantitativos podem ser reproduzidos sem os CSVs completos. A apresentação gráfica pode variar com Matplotlib/fontes, sem mudar a base numérica.

**10:** imagens e tabelas aprovadas e complemento MEDINA/R4 incluídos. A exportação de imagens preserva os bytes; as tabelas são uma transcrição documental das células do Word. Só a parte de auditoria explicitamente indicada reconta valores de escores.

**S00–S05:** dependem das pastas históricas `pipeline_IMTM_HepG2/...` e `resultados_tcc/...` registradas nos próprios cadernos. Não são substituíveis pelas pastas padronizadas. S00 preserva seletores históricos entre fases; não é um fluxo inteiramente portátil de execução única. S01/S02 fornecem a sequência documental utilizada por S03–S05. A reprodução desses suplementos a partir de uma máquina vazia exige os respectivos arquivos históricos, que não foram duplicados.

**S06/S07:** piloto e complemento de visualização; S07 usa os seis TIFFs Mito já baixados pelo piloto. Preservam a origem da escolha de uma janela complementar. Não substituem a avaliação humana final.

## Ambiente

As versões científicas principais registradas são Python 3.13.15, NumPy 2.3.5, pandas 2.2.3, SciPy 1.17.0, scikit-learn 1.8.0, XGBoost 3.1.3, SHAP 0.50.0 e RDKit 2025.9.4. O complemento MEDINA usou Python 3.13.16. São registros das execuções, não uma promessa de instalação automática ou equivalência em qualquer versão futura do Colab. Bibliotecas de imagem e suplementos seguem seus próprios registros. Use sessões separadas e respeite pedidos de reinício após instalação.
