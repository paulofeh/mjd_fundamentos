# Trabalho final
## Correlação entre partos por cesárea e jornada de trabalho na área médica
Para este trabalho, escolhemos entrevistar a base de dados do [SINASC - Sistema de Informações sobre Nascidos Vivos](https://pcdas.icict.fiocruz.br/conjunto-de-dados/sistema-de-informacao-sobre-nascidos-vivos/), na versão tratada pela Plataforma de Ciência de Dados aplicada à Saúde (PCDaS), da Fiocruz. A escolha se deu por conta do interesse do grupo pelo tema da natalidade, como uma pauta de suporte a uma pesquisa mais ampla sobre os temas da violência obstétrica e da chamada "epidemia de cesáreas", bem como pelo fato de se tratar de uma base já tratada, padronizada e enriquecida pela equipe da Fiocruz, dispondo de um dicionário de dados completo e didático.

Os dados originais da base do PCDaS provêm do DATASUS e são obtidos a partir da coleta da “Declaração de Nascidos Vivos” (DN), um documento padrão e de preenchimento obrigatório em todo o território nacional. Cada registro dessa tabela corresponde a uma criança nascida viva no país. O tratamento feito pelo PCDaS resulta em um *dataset* anual com todos os registros das declarações de nascidos vivos contidas no SINASC. Chamou a nossa atenção o trabalho cuidadoso de [documentação](https://pcdas.icict.fiocruz.br/conjunto-de-dados/sistema-de-informacao-sobre-nascidos-vivos/documentacao/) das etapas de tratamento. 

Os dados disponíveis hoje no sistema do PCDaS compreendem os anos de 1996 a 2021 e as bases são entregues em formato .csv separadas por estado e ano. Neste trabalho, por conta do volume de dados, optamos por restringir a análise ao estado de São Paulo e aos anos de 2019 a 2021 - o que, por si só, compreende já um total de 1.660.740 registros. Assim, as conclusões apontadas ao final ficam condicionadas a esse recorte.

### Entrevistando os dados
As perguntas que pautaram a nossa entrevista foram, inicialmente:
  - Como se distribuem os partos natural e cesáreo ao longo da semana?
  - Há alguma correlação entre a proporção de cesáreas e os finais de semana?
  - E entre a proporção de cesáreas e vésperas de feriados?
  - Como a pandemia de Covid-19 afetou esse cenário?

Estas perguntas servem de suporte a uma pesquisa mais ampla que busca investigar possíveis relações entre a predominância de cesáreas no Brasil e a quantidade de denúncias de violência obstétrica. Alguns especialistas propõem essa associação devido ao fato de que muitas cesarianas são impostas (ou fortemente sugeridas) às parturientes sem uma orientação clara dos motivos; uma das razões para isso seria uma maior facilidade de adequação das cesáreas às agendas dos médicos. O Brasil tem a segunda maior taxa de cesáreas do mundo, com 57,7% dos partos sendo realizados por esse meio anualmente, quando a orientação da OMS é a de que esse número não passe de 15%.  

### Procedimentos utilizados
1. Inicialmente, selecionamos a geografia e o recorte histórico a partir da plataforma do PCDaS, baixando na sequência as bases de dados selecionadas em formato .csv.
2. Criamos um banco de dados SQLite no DB Browser importando as 3 tabelas selecionadas:
	- ETLSINASC.DNRES_SP_2019_t.csv
	- ETLSINASC.DNRES_SP_2020_t.csv
	- ETLSINASC.DNRES_SP_2021_t.csv
3. A partir do dicionário de dados disponível na plataforma do PCDaS, selecionamos as colunas mais importantes para a nossa entrevista:
	-  DTNASC: Data do nascimento, no formato ddmmaaaa
	-  PARTO: Tipo de parto, conforme a tabela: 9: Ignorado; 1: Vaginal; 2: Cesáreo
 	-  dia_semana_nasc: Dia da semana em que ocorreu o nascimento (dom; seg; ter; qua; qui; sex; sáb)
4. Passamos a explorar a base a partir do nosso roteiro e seguindo as etapas demonstradas a seguir (sempre repetindo o procedimento para cada ano):

**- Analisar a estrutura da tabela:**
```
-- EXECUTANDO TUDO EM 'estrutura da tabela'
--
-- Na linha 1:
SELECT * 
	FROM SP2021
	LIMIT 10
-- Resulto: 10 linhas retornadas em 16 ms
```

**- Calcular o total de registros em cada tabela:**
```
-- EXECUTANDO TUDO EM 'total_registros'
--
-- Na linha 1:
SELECT COUNT(*) AS total_registros
FROM SP2021;
-- Resulto: 1 linhas retornadas em 750 ms
```

**- Calcular o total de registros classificado por dia do ano e tipo de parto:**
```
-- EXECUTANDO TUDO EM 'nascidos/dia/parto melhor'
--
-- Na linha 1:
SELECT 
    DTNASC,
    SUM(CASE WHEN PARTO = 1 THEN 1 ELSE 0 END) AS Vaginal,
    SUM(CASE WHEN PARTO = 2 THEN 1 ELSE 0 END) AS Cesarea,
    SUM(CASE WHEN PARTO = 9 THEN 1 ELSE 0 END) AS Outros,
	-- Adicione mais SUM(CASE ...) para outros valores de PARTO conforme necessário
    COUNT(*) AS Total_Ocorrencias
FROM 
    SP2021
GROUP BY 
    DTNASC;
-- Resulto: 365 linhas retornadas em 1687 ms
```

Os resultados dessa consulta foram então exportados para arquivos .csv e analisados em uma [planilha eletrônica](https://docs.google.com/spreadsheets/d/1ub0e5jqcROehEkE-APDfoZ3aIwgQhjdL_u8GTZRh6_U/edit?usp=sharing). 

Nesta etapa, foram necessárias algumas transformações na coluna *DTNASC*, já que os dados estão em formato *ddmmaaaa* mas os dias menores que 10 não continham um zero à esquerda, impedindo a ordenação cronológica na planilha. Para isso, utilizamos algumas funções de transformação de texto, como *MID*, *RIGHT* e *JOIN*, para primeiro padronizar as células e depois ordená-las cronologicamente. Também incluímos uma coluna com o cálculo da proporção de cesáreas em relação ao total de nascidos em cada dia. Por fim, plotamos os dados em um gráfico de linha do tempo (disponível na planilha acima). 

Uma primeira leitura dos gráficos mostrou não haver uma correlação evidente entre a proporção de cesáreas e eventuais feriados e datas comemorativas, mas mostrou que há um padrão constante de redução das cesáreas aos sábados e domingos. Inspirados por eessa leitura, realizamos mais uma consulta em SQL.

**- Filtrar e agrupar os dados por *dia_semana_nasc* e *PARTO*, repetindo o processo para cada ano:**
```
-- EXECUTANDO TUDO EM 'nascidos/dsemana/parto melhor'
--
-- Na linha 1:
SELECT 
    dia_semana_nasc,
    SUM(CASE WHEN PARTO = 1 THEN 1 ELSE 0 END) AS Vaginal,
    SUM(CASE WHEN PARTO = 2 THEN 1 ELSE 0 END) AS Cesarea,
    SUM(CASE WHEN PARTO = 9 THEN 1 ELSE 0 END) AS Outros,
	-- Adicione mais SUM(CASE ...) para outros valores de PARTO conforme necessário
    COUNT(*) AS Total_Ocorrencias,
	ROUND(CAST(SUM(CASE WHEN PARTO = 2 THEN 1 ELSE 0 END) AS FLOAT) / COUNT(*) * 100, 2) || '%' AS Proporcao_Cesareas
FROM 
    SP2021
GROUP BY 
    dia_semana_nasc;
-- Resulto: 7 linhas retornadas em 3058 ms
```

Novamente, exportamos os dados obtidos para o formato .csv e adicionamos à planilha, calculando agora a proporção para cada tipo de parto em relação ao dia da semana em que foi realizado. Então, plotamos as proporções em um gráfico de barras verticais agrupando-as por ano / dia da semana.

### Algumas respostas
  - Observamos uma correlação evidente entre a proporção de cesáreas em relação ao dia da semana. Ao longo de todo os períodos analisados, é consistente a queda na proporção de cesáreas aos sábados e domingos. Em 2021, por exemplo, apenas 2 domingos registraram número de cesáreas maior do que 50%, sendo que a média diária para o mesmo ano é de aproximadamente 57% de cesáreas.
  - Não foi possível identificar uma correlação específica entre a proporção de cesáreas e vésperas de feriados ou datas comemorativas. Um possível refinamento dessa pergunta poderia partir da diferenciação entre dados da rede pública e da rede privada, já que a taxa de cesáreas em estabelecimentos privados supera os 80%.
  - Não foi possível identificar possíveis efeitos da pandemia da Covid-19 sobre essas proporções, visto que os gráficos dos 3 períodos analisados (2019, 2020 e 2021) são bastante similares.

### Um caminho para investigações futuras
A nossa investigação surgiu da conversa com especialistas que alegam que alguns obstetras, em especial na saúde suplementar, chegam a fazer 4 ou 5 cesáreas no mesmo dia, tirando proveito da possibilidade de agendamento deste tipo de procedimento, o que resulta em benefício para a equipe médica. A análise feita a partir dos dados de São Paulo revelou uma correlação que parece corroborar os apontamentos feitos pelos especialistas com quem conversamos, em especial a evidente redução das cesáreas nos finais de semana. 

Especialistas também afirmam não ser admitido realizar uma cesariana antes das 39 semanas de gestação, a não ser que haja recomendação explícita. Ao mesmo tempo, o Brasil tem um elevado índice de cesarianas sem indicação e também uma elevada taxa de prematuridade. Uma pauta a ser investigada poderia buscar possíveis relações entre a taxa de cesarianas e a de prematuridade, cruzamento que pode apontar para práticas pouco recomendadas - e até mesmo abusivas, como nos casos de violência obstétrica. A proposta da pauta é a de verificar se a "comodidade" das cesarianas, ao não respeitar o tempo natural do trabalho de parto, pode estar entre as razões para a elevada taxa de prematuridade, que afeta a saúde do bebê e da mãe.

*Integrantes do grupo: Gabriela Bertolo, Melissa Duarte, Michelly Neris, Nicole Lacerda e Paulo Fehlauer*

