# Mecanismo de Banco de Dados

## Criar funções definidas pelo usuário

## Aplica-se a: 

:heavy_check_mark: SQL Server (todas as versões compatíveis) 
:heavy_check_mark: Banco de Dados SQL do Azure

Este tópico descreve como criar uma **UDF** *(função definida pelo usuário)* no **SQL Server** usando *Transact-SQL*.

### Antes de começar

#### Limitações e restrições

- Funções definidas pelo usuário não podem ser usadas para executar ações que modificam o estado do banco de dados.
- As funções definidas pelo usuário não podem conter uma cláusula OUTPUT INTO que tenha uma tabela como seu destino.
- As funções definidas pelo usuário não podem retornar vários conjuntos de resultados. Use um procedimento armazenado se precisar retornar vários conjuntos de resultados.
- O tratamento de erros é restringido em uma função definida pelo usuário. Uma UDF não é compatível com TRY...CATCH, @ERROR ou RAISERROR.
- As funções definidas pelo usuário não podem chamar um procedimento armazenado, mas podem chamar um procedimento armazenado estendido.
- As funções definidas pelo usuário não podem fazer uso de SQL dinâmico ou tabelas temporárias. Variáveis de tabela são permitidas.
- As instruções SET não são permitidas em uma função definida pelo usuário.
- A cláusula FOR XML vazia não é permitida.
- Funções definidas pelo usuário podem ser aninhadas, isto é, uma função definida pelo usuário pode chamar outra. O nível de aninhamento é incrementado quando a execução da função é iniciada, e reduzido quando a execução da função chamada é concluída. Até 32 níveis de funções definidas pelo usuário podem ser aninhados. Se o máximo de níveis de aninhamento for excedido haverá falha em toda a cadeia de funções da chamada de aninhamento. Qualquer referência a um código gerenciado de uma função definida pelo usuário do Transact-SQL é contada como um nível em relação ao limite de 32 níveis de aninhamento. Os métodos invocados a partir do código gerenciado não são contados em relação a esse limite.
- As seguintes instruções do Service Broker não podem ser incluídas na definição de uma função definida pelo usuário Transact-SQL:

	- BEGIN DIALOG CONVERSATION
	- END CONVERSATION
	- GET CONVERSATION GROUP
	- MOVE CONVERSATION
	- RECEIVE
	- SEND

## Permissões

Requer a permissão CREATE FUNCTION no banco de dados e a permissão ALTER no esquema no qual a função está sendo criada. Se a função especificar um tipo definido pelo usuário, a permissão EXECUTE será exigida no tipo.

## Funções escalares

O exemplo a seguir cria uma função escalar (UDF escalar) de várias instruções no banco de dados AdventureWorks2012. A função pega um valor de entrada, um ProductID, e retorna um único valor de dados, a quantidade agregada do produto especificado no estoque.

O exemplo a seguir usa a função ufnGetInventoryStock , para retornar a quantidade atual do estoque dos produtos que têm um ProductModelID entre 75 e 80.

...
 Observação

Para obter mais informações e exemplos de funções escalares, confira CREATE FUNCTION (Transact-SQL).
...

## Funções com valor de tabela

O exemplo a seguir cria uma TVF (função com valor de tabela) embutida no banco de dados AdventureWorks2012. A função pega um parâmetro de entrada, um ID cliente (loja), e retorna as colunas ProductID, Namee a agregação das vendas do ano, até a data atual, como YTD Total para cada produto vendido para a loja.

O exemplo a seguir invoca a função e especifica a ID do cliente 602.

O exemplo a seguir cria uma MSTVF (função com valor de tabela de várias instruções) no banco de dados AdventureWorks2012. A função usa um único parâmetro de entrada, um EmployeeID , e retorna uma lista de todos os funcionários que reportam direta ou indiretamente ao funcionário especificado. A função que especifica a ID do funcionário 109 é invocada em seguida.

O exemplo a seguir invoca a função e especifica a ID do funcionário 1.

...
Observação

Para obter mais informações e exemplos TVFs embutidas (funções com valor de tabela embutidas) e MSTVFs (funções com valor de tabela de várias instruções), confira CREATE FUNCTION (Transact-SQL).
...

### Práticas Recomendadas

Se uma UDF (função definida pelo usuário) não for criada com a cláusula SCHEMABINDING, as alterações feitas nos objetos subjacentes poderão afetar a definição da função e produzir resultados inesperados quando ela for chamada. É recomendável que você implemente um dos seguintes métodos para garantir que a função não se torne desatualizada devido a alterações em seus objetos subjacentes:

- Especifique a cláusula WITH SCHEMABINDING quando você estiver criando a UDF. Isso garante que os objetos referenciados na definição da função não possam ser modificados, a menos que a função também seja modificada.
- Execute o procedimento armazenado sp_refreshsqlmodule depois de modificar um objeto especificado na definição da UDF.

Se estiver criando uma UDF que não acessa os dados, especifique a opção SCHEMABINDING. Isso impedirá que o otimizador de consulta gere operadores de spool desnecessários para planos de consulta que envolvem essas UDFs. Para obter mais informações sobre spools, confira Referência de operadores lógicos e físicos de plano de execução. Para obter mais informações sobre como criar uma função associada a esquema, confira Funções associadas a esquema.

O ingresso em uma MSTVF em uma cláusula FROM é possível, mas pode resultar em baixo desempenho. SQL Server não pode usar todas as técnicas otimizadas em algumas instruções que podem ser incluídas em uma MSTVF, resultando em um plano de consulta de qualidade inferior. Para obter o melhor desempenho possível, sempre que possível use junções entre tabelas base em vez de funções.

...
Importante

As MSTVFs têm uma estimativa de cardinalidade fixa de 100 começando com SQL Server 2014 (12.x) e de 1 para versões anteriores SQL Server.
Começando com SQL Server 2017 (14.x), otimizar um plano de execução que usa as MSTVFs pode aproveitar a execução intercalada, que resulta no uso de cardinalidade real em vez da heurística acima.
Para obter mais informações, consulte Interleaved execution for multi-statement table valued functions (Execução intercalada para funções com valor de tabela de várias instruções).
...

...
Observação

ANSI_WARNINGS não é cumprido quando você passa parâmetros em um procedimento armazenado, em uma função definida pelo usuário ou quando declara ou define variáveis em uma instrução de lote. Por exemplo, se a variável for definida como char(3) e, em seguida, configurada com um valor maior que três caracteres, os dados serão truncados até o tamanho definido e a instrução INSERT ou UPDATE terá êxito.
...






















[Artigo original](https://docs.microsoft.com/pt-br/sql/relational-databases/user-defined-functions/create-user-defined-functions-database-engine?view=sql-server-ver15)