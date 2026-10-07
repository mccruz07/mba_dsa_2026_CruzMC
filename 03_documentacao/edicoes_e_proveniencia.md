# Edições da distribuição

A distribuição reúne códigos recuperados do Drive e apresentações documentadas de scripts já utilizados. Não substitui os originais.

1. Saídas, contadores de execução e metadados transitórios foram removidos das cópias de publicação. Isso reduz os rastros de sessão e evita apresentar uma execução antiga como saída de uma nova sessão.
2. `PASTA_RETOMAR` foi redefinida para `None` nas quatro cópias locais. `ABRIR_RESERVADA` e a autorização do complemento foram deixadas desligadas. São seletores de execução, não mudanças de limiares.
3. Somente os caminhos dos recursos da comparação, integração e figuras foram ajustados para a nova pasta. As diferenças exatas estão em `proveniencia_cadernos.json`.
4. No caderno gráfico, rótulos foram mapeados para a numeração aprovada. Os valores das tabelas e funções de cálculo não foram trocados.
5. Os cadernos 00 e 10 são novos auxiliares de distribuição/exportação, explicitamente posteriores às análises. Não são apresentados como cadernos que treinaram os modelos históricos.
6. Os registros originais de R4 conservam seu contexto temporal. O relatório da comparação v2 é anterior ao complemento MEDINA; o caderno 10 reúne a situação final sem modificar a fonte antiga.

O arquivo separado `TCC_cadernos_originais_rastreabilidade.zip`, na pasta de fontes do Drive, contém as cópias recebidas antes da limpeza. Ele não faz parte do ZIP leve de código. Sua função é permitir comparação byte a byte; as execuções e saídas antigas não substituem o teste de uma nova instalação.

Não foi escolhido automaticamente um conjunto mais favorável de sementes, modelos ou controles. Não foram recalculados SHAP ou modelos. A bibliografia e a redação do TCC permanecem fora do escopo desta organização.
