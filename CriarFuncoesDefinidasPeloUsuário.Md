# Mecanismo de Banco de Dados

## Criar fun��es definidas pelo usu�rio

## Aplica-se a: 

:heavy_check_mark: SQL Server (todas as vers�es compat�veis) 
:heavy_check_mark: Banco de Dados SQL do Azure

Este t�pico descreve como criar uma **UDF** *(fun��o definida pelo usu�rio)* no **SQL Server** usando *Transact-SQL*.

### Antes de come�ar

#### Limita��es e restri��es

- Fun��es definidas pelo usu�rio n�o podem ser usadas para executar a��es que modificam o estado do banco de dados.
- As fun��es definidas pelo usu�rio n�o podem conter uma cl�usula OUTPUT INTO que tenha uma tabela como seu destino.
- As fun��es definidas pelo usu�rio n�o podem retornar v�rios conjuntos de resultados. Use um procedimento armazenado se precisar retornar v�rios conjuntos de resultados.
- O tratamento de erros � restringido em uma fun��o definida pelo usu�rio. Uma UDF n�o � compat�vel com TRY...CATCH, @ERROR ou RAISERROR.
- As fun��es definidas pelo usu�rio n�o podem chamar um procedimento armazenado, mas podem chamar um procedimento armazenado estendido.
- As fun��es definidas pelo usu�rio n�o podem fazer uso de SQL din�mico ou tabelas tempor�rias. Vari�veis de tabela s�o permitidas.
- As instru��es SET n�o s�o permitidas em uma fun��o definida pelo usu�rio.
- A cl�usula FOR XML vazia n�o � permitida.
- Fun��es definidas pelo usu�rio podem ser aninhadas, isto �, uma fun��o definida pelo usu�rio pode chamar outra. O n�vel de aninhamento � incrementado quando a execu��o da fun��o � iniciada, e reduzido quando a execu��o da fun��o chamada � conclu�da. At� 32 n�veis de fun��es definidas pelo usu�rio podem ser aninhados. Se o m�ximo de n�veis de aninhamento for excedido haver� falha em toda a cadeia de fun��es da chamada de aninhamento. Qualquer refer�ncia a um c�digo gerenciado de uma fun��o definida pelo usu�rio do Transact-SQL � contada como um n�vel em rela��o ao limite de 32 n�veis de aninhamento. Os m�todos invocados a partir do c�digo gerenciado n�o s�o contados em rela��o a esse limite.
- As seguintes instru��es do Service Broker n�o podem ser inclu�das na defini��o de uma fun��o definida pelo usu�rio Transact-SQL:

	- BEGIN DIALOG CONVERSATION
	- END CONVERSATION
	- GET CONVERSATION GROUP
	- MOVE CONVERSATION
	- RECEIVE
	- SEND

## Permiss�es

Requer a permiss�o CREATE FUNCTION no banco de dados e a permiss�o ALTER no esquema no qual a fun��o est� sendo criada. Se a fun��o especificar um tipo definido pelo usu�rio, a permiss�o EXECUTE ser� exigida no tipo.

## Fun��es escalares

O exemplo a seguir cria uma fun��o escalar (UDF escalar) de v�rias instru��es no banco de dados AdventureWorks2012. A fun��o pega um valor de entrada, um ProductID, e retorna um �nico valor de dados, a quantidade agregada do produto especificado no estoque.

O exemplo a seguir usa a fun��o ufnGetInventoryStock , para retornar a quantidade atual do estoque dos produtos que t�m um ProductModelID entre 75 e 80.

...
 Observa��o

Para obter mais informa��es e exemplos de fun��es escalares, confira CREATE FUNCTION (Transact-SQL).
...

## Fun��es com valor de tabela

O exemplo a seguir cria uma TVF (fun��o com valor de tabela) embutida no banco de dados AdventureWorks2012. A fun��o pega um par�metro de entrada, um ID cliente (loja), e retorna as colunas ProductID, Namee a agrega��o das vendas do ano, at� a data atual, como YTD Total para cada produto vendido para a loja.

O exemplo a seguir invoca a fun��o e especifica a ID do cliente 602.

O exemplo a seguir cria uma MSTVF (fun��o com valor de tabela de v�rias instru��es) no banco de dados AdventureWorks2012. A fun��o usa um �nico par�metro de entrada, um EmployeeID , e retorna uma lista de todos os funcion�rios que reportam direta ou indiretamente ao funcion�rio especificado. A fun��o que especifica a ID do funcion�rio 109 � invocada em seguida.

O exemplo a seguir invoca a fun��o e especifica a ID do funcion�rio 1.

...
Observa��o

Para obter mais informa��es e exemplos TVFs embutidas (fun��es com valor de tabela embutidas) e MSTVFs (fun��es com valor de tabela de v�rias instru��es), confira CREATE FUNCTION (Transact-SQL).
...

### Pr�ticas Recomendadas

Se uma UDF (fun��o definida pelo usu�rio) n�o for criada com a cl�usula SCHEMABINDING, as altera��es feitas nos objetos subjacentes poder�o afetar a defini��o da fun��o e produzir resultados inesperados quando ela for chamada. � recomend�vel que voc� implemente um dos seguintes m�todos para garantir que a fun��o n�o se torne desatualizada devido a altera��es em seus objetos subjacentes:

- Especifique a cl�usula WITH SCHEMABINDING quando voc� estiver criando a UDF. Isso garante que os objetos referenciados na defini��o da fun��o n�o possam ser modificados, a menos que a fun��o tamb�m seja modificada.
- Execute o procedimento armazenado sp_refreshsqlmodule depois de modificar um objeto especificado na defini��o da UDF.

Se estiver criando uma UDF que n�o acessa os dados, especifique a op��o SCHEMABINDING. Isso impedir� que o otimizador de consulta gere operadores de spool desnecess�rios para planos de consulta que envolvem essas UDFs. Para obter mais informa��es sobre spools, confira Refer�ncia de operadores l�gicos e f�sicos de plano de execu��o. Para obter mais informa��es sobre como criar uma fun��o associada a esquema, confira Fun��es associadas a esquema.

O ingresso em uma MSTVF em uma cl�usula FROM � poss�vel, mas pode resultar em baixo desempenho. SQL Server n�o pode usar todas as t�cnicas otimizadas em algumas instru��es que podem ser inclu�das em uma MSTVF, resultando em um plano de consulta de qualidade inferior. Para obter o melhor desempenho poss�vel, sempre que poss�vel use jun��es entre tabelas base em vez de fun��es.

...
Importante

As MSTVFs t�m uma estimativa de cardinalidade fixa de 100 come�ando com SQL Server 2014 (12.x) e de 1 para vers�es anteriores SQL Server.
Come�ando com SQL Server 2017 (14.x), otimizar um plano de execu��o que usa as MSTVFs pode aproveitar a execu��o intercalada, que resulta no uso de cardinalidade real em vez da heur�stica acima.
Para obter mais informa��es, consulte Interleaved execution for multi-statement table valued functions (Execu��o intercalada para fun��es com valor de tabela de v�rias instru��es).
...

...
Observa��o

ANSI_WARNINGS n�o � cumprido quando voc� passa par�metros em um procedimento armazenado, em uma fun��o definida pelo usu�rio ou quando declara ou define vari�veis em uma instru��o de lote. Por exemplo, se a vari�vel for definida como char(3) e, em seguida, configurada com um valor maior que tr�s caracteres, os dados ser�o truncados at� o tamanho definido e a instru��o INSERT ou UPDATE ter� �xito.
...






















[Artigo original](https://docs.microsoft.com/pt-br/sql/relational-databases/user-defined-functions/create-user-defined-functions-database-engine?view=sql-server-ver15)