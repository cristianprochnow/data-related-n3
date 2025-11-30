# Avaliação N3

Avaliação N3 das disciplinas de Ciência de Dados e Análise Preditiva.

# Estudantes

- [x] Cristian Prochnow
- [x] Marlon de Souza

#  A Fundação do Projeto - O Problema de Negócio 

O dataset que está sendo usado como base é relacionado a uma pesquisa feita com diversos colaboradores ao redor do mundo questionando-lhes acerca de suas posições atuais em cargos relacionados a IA.

O arquivo foi pego a partir do [Kaggle](https://www.kaggle.com/datasets/cedricaubin/ai-ml-salaries) e também pode ser encontrado para download direto a partir deste link.

Com isso, a pergunta de negócio elencada é:

> "Quais são os principais fatores que determinam a variação salarial para profissionais de Inteligência Artificial e Machine Learning globalmente? Dentre fatores como nível de experiência, localização da empresa, porte da empresa e a modalidade de trabalho (remoto vs. presencial), qual deles exerce a maior influência na remuneração final?"

É por meio disso que essa análise desbrava o contexto do grande aumento dos cargos relacionados a Machine Learning e Inteligência Artificial que aparecem no mercado, juntamente com essas oportunidades de trabalho remoto para fora do país que ainda existe. Em um mercado tão aquecido atualmente, esse estudo é a oportunidade de verificar por meio de uma ótica como está parte do mercado global.

A partir dessa premissa, o objetivo então é responder a dúvida apresentada acima, verificando então, por meio das características do profissional presente no *dataset*, a faixa salarial que aquele profissional se encaixaria ou também a qual intervalo de mercado ele pertence.

É importante notar que essa análise inicial será feita sobre uma pequena parcela de profissionais que fazem isso, então é um ponto muito interessante verificar como o modelo desenvolvido vai se comportar ao longo de outros contextos apresentados, sejam mais amplos ou mais específicos.

# A Jornada dos Dados - Pipeline e Arquitetura

O arquivo foi pego a partir do [Kaggle](https://www.kaggle.com/datasets/cedricaubin/ai-ml-salaries). Esse *dataset* possui uma característica interessante que é justamente já estar pronto para análises. Isto é, não é necessária anonimicação dos dados, controle grosseiro de duplicatas, tratamento de incoerências nas informações ou qualquer outra medida mais brusca. O que será feito, principalmente, é refinar esses dados e aprimorar a análise por meio de qualquer outra informação sintetizada a partir desses dados, caso haja necessidade.

## Arquitetura

Quanto à arquitetura escolhida, o foco é criar um Data Lakehouse, justamente pela oportunidade de juntar informações em vários formatos, provenientes de várias fontes distintas, da melhor forma possível. Para abordar essa forma de guardar as informações, foi escolhida a arquitetura em medalhão, para tratar separar os dados em três categorias distintas.

Na categoria `bronze` (pasta `data/bronze`) vão as informações brutas, convertida para o formato de arquivo mais conveniente para se encaixar na categoria de Lakehouse (`.parquet`). Por meio dessa conversão, o tratamento dos dados ocorre de forma mais eficiente do que usando apenas `CSV`.

O próximo passo é a categoria `silver` (pasta `data/silver`), na qual ocorre a primeira camada de tratamento, que justamente fica responsável por limpar a base e também realizar qualquer formatação que seja necessária. É aqui que as informações podem surgir e os dados serem modificados em seu valor, não apenas formatação.

E então, no passo `gold` (pasta `data/gold`) é onde os dados estão prontos para serem usados no modelo. Aqui é onde as últimas formatações das informações com mudanças focadas na análise da base pelo modelo.

Essa arquitetura foi escolhida principalmente pela dinâmica de ao mesmo tempo que separa os dados por "estado", ainda acaba mantendo uma espécie de *log* sobre os estágios que a base passou. Caso seja desejado refazer os dados para a etapa `gold`, por exemplo, basta pegar os dados da etapa `silver` e formatá-los novamente.

## Injeção dos dados

O arquivo base disponível - em `data/salaries.csv` - é o *dataset* baixado diretamente do Kaggle, e nessa listagem há a informação contabilizada através da pesquisa com diversos desenvolvedores ao redor do mundo, que trabalham exclusivamente em cargos de IA/ML.

As colunas abrangem dados diversos sobre a pesquisa, como ano ao qual foi feita a pesquisa com aquele indivíduo específico (`work_year`), nível de experiência junto com o tipo (`experience_level` e `employment_type`), salário na moeda local de onde aquele desenvolvedor more, juntamente com a conversão direta para o dólar (`salary` e `salary_in_usd`) e até o país que ele mora em contraste com o da empresa (`employee_residance` e `company_location`).

Com isso, o primeiro passo para essa transformação é tornar os dados mais acessíveis parea o restante do processo, colocando em formato `.parquet`, para que se encaixe nas categoria `bronze` (`data/bronze`), como é feito no notebook `01_injecao_dados.ipynb`.