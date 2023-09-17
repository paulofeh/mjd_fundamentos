# Trabalho final
## Integrantes do grupo: Gabriela Bertolo, Melissa Duarte, Michelly Neris, Nicole Lacerda e Paulo Fehlauer
### 1. Escolha ao menos uma base de dados pública para trabalhar. Justifique em um parágrafo sua escolha, incluindo também informações básicas sobre a base (fonte, metodologia de coleta, data de atualização e limitações mais evidentes).
  - Escolhemos a base de dados do SINASC - Sistema de Informações sobre Nascidos Vivos, na versão tratada pela Plataforma de Ciência de Dados aplicada à Saúde (PCDaS), da Fiocruz.
  - A escolha se deu pelo interesse do grupo no tema da natalidade e pelo fato de se tratar de uma base já tratada, padronizada e enriquecida pela equipe da Fiocruz.
  - Os dados originais provêm do DATASUS e são obtidos a partir da coleta da “Declaração de Nascidos Vivos” (DN), documento padrão e de preenchimento obrigatório em todo o território nacional.
  - O tratamento feito pelo PCDaS resulta em um dataset anual com todos os registros das declarações de nascidos vivos contidas no SINASC.
  - Os dados disponíveis hoje no sistema compreendem os anos de 1996 a 2021 e as bases são entregues em formato .csv separadas por estado e ano.
  - Neste trabalho, por conta do tamanho das bases, optamos por restringir a análise ao estado de São Paulo e aos anos de 2019 a 2021.
  - Link para a base: https://pcdas.icict.fiocruz.br/conjunto-de-dados/sistema-de-informacao-sobre-nascidos-vivos/
### 2. Crie um roteiro de entrevista para aplicar a essa base de dados.
  - Como se distribuem os partos natural e cesáreo ao longo da semana?
  - Há alguma correlação entre a proporção de cesáreas e os finais de semana?
  - E entre a proporção de cesáreas e vésperas de feriados?
  - Como a pandemia de Covid-19 afetou esse cenário?
### 3. Documente no github as funções e cálculos aplicados para obter as respostas.
  - 
### 4. Indique um caminho inicial de pauta que poderia ser explorado a partir dos insights obtidos.
  - Observamos uma correlação evidente entre a proporção de cesáreas em relação ao dia da semana. Ao longo dos anos, é consistente a queda na proporção de cesáreas nos sábados e domingos. Em 2021, por exemplo, apenas 2 domingos registraram número de cesáreas maior do que 50%, sendo que a média diária para o mesmo ano é de aproximadamente 57% de cesáreas.
  - Não foi possível identificar correlação específica entre a proporção de cesáreas e vésperas de feriados. Um possível refinamento dessa pergunta poderia partir da diferenciação entre dados da rede pública e da rede privada.
  - Tampouco foi possível identificar possíveis efeitos da pandemia sobre essas proporções, visto que as linhas dos 3 anos analisados (2019, 2020 e 2021) são bastante similares. 


