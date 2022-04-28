API Design Guidelines

Conteúdos

Introdução
Convenções usadas no guia
Escopo do documento
Definições
RESTful API
Recursos
Idempotência
Métodos, requisições e respostas HTTP
Métodos HTTP
Regras para Métodos HTTP
Método GET
Método POST
Método PUT
Método DELETE
Requisições e respostas HTTP
Corpo da requisição e resposta
Headers da requisição e resposta
Headers comuns
Headers customizados
Códigos de Retorno HTTP
Os códigos de retorno mais comuns
Regras para códigos de retorno HTTP
URL
Schemes
Paths
Regras para recursos e subrecursos
Regras para nomenclatura dos recursos e subrecursos
Regras para identificação dos recursos e subrecursos
Query Parameters
Regras de nomenclatura
JSON
Regras para a nomenclatura geral
Tipos primitivos
Strings
Booleans
Enums
Numbers
Null
Tipos de valores comuns no JSON
Endereço
Valor monetário
Datas
Porcentagem
Erros
400 (Bad Request)
401 (Unauthorized)
403 (Forbidden)
404 (Not Found)
422 (Unprocessable)
5XX (Erros no servidor)
Versionamento
Ciclo de vida das APIs
Scopes OAuth 2.0
Regras para Scopes
Apêndice A - Palavras-chave


Introdução

Este guia foi criado com o intuito de apoiar os desenvolvedores no design de APIs fornecidas pela Evoluservices. As nossas APIs são construídas seguindo os princípios descritos aqui. Porém, este documento não foi criado com o propósito de detalhar todas as decisões possíveis durante a criação de uma API. Portanto, irá descrever algumas regras de alto-nível que devem ser seguidas, mas que também não representam uma verdade absoluta que não pode ser revista, dependendo do caso específico. 
Como a arquitetura adotada é a arquitetura REST, boa parte deste documento trata-se das boas práticas HTTP e REST.
Além disso, o guia sempre está aberto às modificações de tal forma que haja maior concordância possível com as opiniões dos desenvolvedores envolvidos.

Convenções usadas no guia

As palavras-chave "PRECISA", "NÃO PODE", "OBRIGATÓRIO", "DEVERÁ", "NÃO DEVERÁ", "DEVE", "NÃO DEVE", "RECOMENDADO", "PODE" e "OPCIONAL" neste guia serão destacadas em NEGRITO E COM LETRA MAIÚSCULA. Elas são interpretadas conforme descrito no [RFC2119](https://www.ietf.org/rfc/rfc2119.txt), porém, de maneira traduzida.
Para mais detalhes dessas palavras-chave, você pode consultar a Apêndice A.
(Acho que precisamos passar a tradução para cá, senão vai ficar muito difícil de entender o que cada palavra quer dizer exatamente)


Escopo do documento
	Este documento irá cobrir APIs REST. Se sua API usar outra arquitetura, tenha certeza que não poderia ser substituída por uma API REST. Outras arquiteturas não fazem parte do escopo deste documento.

Definições

RESTful API

As APIs da EvoluServices utilizam o padrão RESTful e, portanto, o guia seguirá os princípios baseados nesta arquitetura, podendo não ser aplicável para outros tipos de padrão de design de APIs.

Para mais informações sobre esse tipo de arquitetura, consulte a [Página da Wikipedia](https://en.wikipedia.org/wiki/Representational_state_transfer) ou [o capítulo da dissertação](https://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm) escrita por Roy Fielding sobre o assunto.

Recursos

Para entender melhor este guia é necessário entender a definição de recursos.

Como dita na [dissertação do Roy Fielding](https://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm), o recurso é definido como abstração chave da informação no padrão REST. Qualquer informação que pode ser nomeada pode ser considerada um recurso. Nas APIs da Evoluservices, por exemplo, podem observar alguns recursos que são frequentemente observados como Estabelecimento, Transação, Pagamento e entre outros. Um recurso também pode conter um outro conjunto de recursos, este conjunto também é denominado como subrecursos. Uma Transação (recurso), por exemplo, pode conter um conjunto de Pagamentos (subrecursos). 

Outra forma de entender o que é um recurso, é algo que você pode “recorrer a” quando você precisar.

Idempotência

A idempotência é a capacidade de permanecer o resultado inalterado mesmo fazendo múltiplas requisições repetidamente.

(A Stripe tem uma camada de idempotência, talvez seja a melhor solução para implementar idempotência nas nossas APIs. Depois da uma olhada: https://stripe.com/docs/api/idempotent_requests) 

Métodos, requisições e respostas HTTP

Métodos HTTP

Os APIs são responsáveis por consumir estes recursos através dos métodos HTTP, que são responsáveis por operações que serão executadas sobre os recursos através de requisições.
As operações simples feitas pelos métodos HTTP são representadas pela sigla CRUD, que consiste em Criar (Create), Ler (Read), Atualizar (Update) e Remover (Delete) recursos.

Cada método HTTP possui um nome padrão. Estes nomes são também conhecidos como HTTP Verbs (ou Verbos HTTP). Os HTTP Verbs mais conhecidos são representados na tabela abaixo:


HTTP Verb
Descrição
GET
Recupera um recurso
POST
Cria ou executa uma operação complexa sobre um recurso
PUT
Atualiza um recurso
DELETE
Remove um recurso
PATCH
Faz uma atualização parcial de um recurso


Existem outros métodos que são menos utilizados. Mais detalhes podem ser consultados, por exemplo, na página do [Mozilla](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Methods)

Regras para os métodos HTTP

Método GET

Ao utilizar o método GET, o recurso envolvido NÃO PODE ser modificado de maneira alguma. Esta operação é [idempotente](link para definição de idempotênia) por definição.

Método POST

Para a criação de recursos e subrecursos, o método POST DEVE ser utilizado. 
Após criar o recurso, se for feita a mesma requisição novamente, DEVE retornar um aviso que o recurso já foi criado, ao invés de criar o mesmo recurso novamente e ocorrer duplicação.
No caso da criação de recursos ou subrecursos, a resposta da requisição deve ter status code 201 (Created), para indicar que um recurso foi criado. Para mais detalhes, consultar a seção de “Códigos de retorno HTTP”.
(se houver um status code HTTP específico, acho uma boa oportunidade colocar aqui)
Para operações complexas, como em algumas operações feitas ao nível da regra de negócio, é RECOMENDADO que seja utilizado o método POST.
Esta operação não é idempotente e, por isso, É RECOMENDADO que faça o procedimento citado [aqui](Link para o local onde diz sobre a idempotência em detalhes]

Método PUT

Para modificar algum valor dentro de um recurso existente ou para reestabelecer um relacionamento entre este recurso com algum subrecurso existente, DEVE-SE utilizar o método PUT. 

Método DELETE

Para remover um recurso ou um subrecurso existente, DEVE-SE utilizar o método DELETE. 
No caso em que é modificado um valor dentro de um recurso ou subrecurso que o considera como removido, ao invés de removê-lo literalmente (soft delete), DEVE-SE, mesmo assim, utilizar este método.

Requisições e Respostas HTTP

Corpo da requisição e respostas

Nas requisições e respostas HTTP, o corpo da requisição DEVE ser no formato JSON, em conformidade com o que está descrito no documento [RFC7159](https://datatracker.ietf.org/doc/html/rfc7159). Consequentemente, os endpoints DEVEM aceitar conteúdos do tipo application/json e também responder application/json por padrão, caso o cliente não especificar.

Headers da requisição e respostas

O propósito do Headers (ou Cabeçalhos) HTTP é enviar dados adicionais sobre a requisição ou resposta de maneira isolada. Os Headers DEVEM ser case sensitive, ou seja, DEVEM diferenciar letras maiúsculas das minúsculas (case sensitive).

Os Headers são geralmente usados para lidar com “cross-cutting concerns”, ou seja, com partes do sistema que envolvem várias outras partes como logging, monitoramento, sincronizações e entre outros. Portanto, ao processar uma requisição, uma API NÃO DEVE depender dos valores contidos nos Headers HTTP. Por isso, qualquer valor que pertença a algum recurso ou ligado à qualquer regra de negócio NÃO DEVE ser inserido dentro dos Headers. 
Entretanto, existem algumas exceções as quais os valores ligados à alguma regra de negócio PODEM ser inseridos, tais como o Location, nas requisições que retornam com status 201 (Created) ou 3XX (status ligados aos redirecionamentos).

Além disso, é RECOMENDADO que seja usado, ao máximo, Header já existentes por padrão no protocolo HTTP e evitar criar um customizado.

Headers Comuns

Os Headers mais conhecidos são mostrados abaixo. Para ver mais detalhes, clique no nome do Header que abrirá uma página com mais informações.

[Accept](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Headers/Accept)

Explica sobre os tipos de dados ([MIME-Type](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types)) que podem ser retonados no corpo da resposta.

Formatos:
Accept: <MIME_type>/<MIME_subtype>
Accept: <MIME_type>/*
Accept: */*

Exemplo: Accept: text/html, application/xhtml+xml, application/xml;q=0.9, image/webp, */*;q=0.8

[Authorization](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Headers/Authorization)

Contém as credenciais necessárias para autenticar o usuário com o servidor. No caso em que a autenticação falha, a response DEVE retornar com o erro 401 e com o Header WWW-Authenticate indicando o tipo de autenticação que deve ser feita.

Formato: Authorization: <tipo> <credenciais>

Para o nosso API, a autenticação é feita em OAuth2, e, portanto, o valor que deve ser utilizado no <tipo> DEVE ser “Bearer” e <credenciais>, o token de autenticação.
O token NÃO DEVE ser um JWT, pois isso torna algumas informações sensíveis no token, explícitas.

[Content-Type](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Headers/Content-Type)

Indica o formato usado para transmitir os dados do recurso. Os APIs DEVEM aceitar o seguinte valor:
Content-Type: application/json


[Location](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Headers/Location)

Indica a URL da página à qual deve ser redirecionada quando a resposta for retornada no caso da resposta com código 3XX (redirecionamento) ou URL que dá acesso ao recurso quando a resposta for 201 (criado). Este valor apenas DEVE ser inserido no caso do retorno com os códigos de status citados anteriormente.

Fomato: Location: <url> 

[WWW-Authenticate](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Headers/WWW-Authenticate)

Este header define o método de autenticação que deve ser usado para obter acesso ao recurso. O WWW-Authenticate DEVE ser retornado quando o status retornar com valor 401 (Unauthorized) de acordo com a [seção 4.1 do RFC7235](https://datatracker.ietf.org/doc/html/rfc7235#section-4.1).

Formato: WWW-Authenticate <tipo> realm=<realm>*

* O valor realm NÃO É MANDATÓRIO.

Como a autenticação dos nossos APIs utilizam o método OAuth 2.0, o valor do <tipo> DEVE ser “Bearer”. O realm PODE ser inserido para referenciar o nome do ambiente. Entretanto, pelo fato de que o formato dos hostnames dos nossos APIs já demonstra uma boa distinção entre os ambientes, o preenchimento deste campo tende a NÃO SER NECESSÁRIO, já que os clientes frequentemente exibirão um hostname formatado, caso não haja o seu preenchimento.

Neste guia foi citado apenas alguns Headers dentre vários que existem. Se deseja consultar algum outro header que não foi descrito aqui, pode consultar, por exemplo, a [documentação do Mozilla](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Headers) sobre este tópico. 

Header Customizado

O Header HTTP possibilita criar também chaves/valores customizados. Antigamente, era recomendado referi-los com o prefixo X-, porém entrou em desuso, pelo fato de que alguns headers padrões passaram a usar este mesmo prefixo de acrodo com [RFC6648](https://datatracker.ietf.org/doc/html/rfc6648) e, por isso, NÃO SE RECOMENDA essa convenção nos dias de hoje.

Além disso, para manter a consistência, RECOMENDA-SE que este header customizado seja incluído também na response, com o mesmo valor enviado na requisição. 

Códigos de Retorno HTTP

Os códigos de Retorno HTTP tem como finalidade verificar se as requisições foram feitas com sucesso ou não e detalhar o que fez tornar uma requisição bem sucedida, ou, caso contrário, que motivo levou para resultar em tal erro. Os códigos são representados por 3 dígitos e são categorizados de acordo com o primeiro dígito. Elas são representadas abaixo:
1XX:  é usada apenas para fins informativos. Esta categoria não costuma ser muito utilizada.
2XX: é usada caso a requisição for bem sucedida.
3XX: é usada no caso em que deve haver redirecionamento após retornar a resposta da requisição.
4XX: é usada no caso em que há algum erro na requisição no lado do cliente, isto é, quando o aplicativo cliente fez algum tipo de requisição que é considerada errada pelo servidor.
5XX: é usada no caso em que há algum erro na requisição no lado do servidor. São categorias de erros as quais os clientes não têm controle e geralmente devem entrar em contato com o responsável do servidor para mitigá-los.

 Os códigos de retorno mais comuns

Cada código de retorno existe uma descrição padronizada que se aplica para todos os desenvolvedores que utilizam o protocolo HTTP. Na tabela abaixo, são mostrados o número do código, a descrição padronizada e a detalhada de cada código, que é comumente utilizado nas requisições.

Código
Descrição
Descrição Detalhada
200
OK
Significa que a requisição foi feita com sucesso.
201
Created
Significa que o recurso foi criado com sucesso.
202
Accepted
Geralmente utilizado nas requisições assíncronas. Significa que a requisição foi recebida, porém, o API ainda não processou completamente a requisição.
204
No-Content
Significa que não existe conteúdo na requisição, mas que os cabeçalhos são úteis. Geralmente esse código de retorno é utilizado quando é feita uma atualização ou remoção de recursos, através dos métodos PUT e DELETE, respectivamente.
301
Moved Permanently
Significa que o recurso deve ser redirecionado permanentemente a um URL inserido no campo Location do Header.
302
Found
Significa que o recurso foi redirecionado temporariamente a um URL inserido no campo Location do Header.
400
Bad Request
Significa que a requisição enviada pelo cliente não está em conformidade (algum valor do campo no formato inválido, erro de sintaxe nos nomes do campo, etc.).
401
Unauthorized
Significa que o cliente não está autenticado no servidor para ser possível realizar as requisições
403
Forbidden
Significa que o cliente está autenticado no servidor, porém a requisição está sendo rejeitada por algum outro motivo como a permissão, por exemplo.
404
Not Found
Significa que algum recurso não foi encontrado durante o consumo da requisição.
422
Unprocessable
Significa que a requisição feita pelo cliente está em conformidade sintaticamente, mas está incorreta semanticamente. 
500
Internal Server Error
Significa que houve algum erro interno no servidor.






Regras para o código de retorno HTTP

A tabela abaixo mostra quais códigos de retorno comuns (exceto 1XX e 3XX) são permitidos para cada método HTTP. É RECOMENDADO que siga as normas da tabela ao fazer a escolha dos códigos de retorno.



Método HTTP
200 
201 
204 
400 
401 
403 
404 
422 
500 
GET
X




X
X
X
X
X
X
POST
X
X


X
X
X
X
X
X
PUT
X


X
X
X
X
X
X
X
DELETE
X


X
X
X
X
X
X
X
PATCH
X


X
X
X
X
X
X
X


Além disso, existem outras regras a serem seguidas:
No método GET, o retorno sempre DEVE ser 200, caso o recurso buscado for encontrado. No caso do erro, mesmo que o recurso buscado esteja removido através de um soft delete, DEVE retornar o código 404.
No método POST, é RECOMENDADO que retorne o 201 caso a requisição seja feita com sucesso, para indicar que o recurso foi criado no API. A URL que dá acesso ao recurso DEVE ser inserido dentro do Header “Location” do response e o recurso criado NÃO DEVE estar no corpo da response.
Para os métodos PUT, PATCH, que consiste na atualização de recursos e DELETE, que consiste na remoção de recursos, caso não seja necessário retornar nenhum conteúdo dentro da response, o método DEVE retornar com o código 204 caso seja feita com sucesso para indicar que a resposta não possui nenhum conteúdo. Caso haja a real necessidade de inserir algum conteúdo na resposta, neste caso, DEVE ser retornado com o código 200, no caso de sucesso.
Para qualquer recurso ou sub-recurso não encontrado durante o consumo da requisição nos APIs, o código de retorno DEVE ser 404, independente do método HTTP que esteja sendo utilizado.




URL

As URIs das APIs da Evoluservices tem a seguinte composição:

https://evoluservices.com/apis/v2/resource/{resource-id}?query1=value1
\______/\_______________/\______________________________/\___________/
   |           |                     |                         |                
 scheme     authority               path                      query

Nas URLs NÃO DEVE haver a presença de letras maiúsculas e deve sempre começar com letra alfanumérica.



Schemes

Para Scheme, por motivo de segurança, é RECOMENDADO que se utilize o HTTPS, principalmente nos ambientes de produção.

Paths

Os paths são compostos de várias partes, sendo estas, mostradas na tabela abaixo, baseando-se no exemplo da URL no início da seção:

Parte
Descrição
apis
Uma convenção específica para APIs da Evoluservices. Significa para identificar que o path se refere à uma chamada de um API externo.
v2
A versão da API utilizada. DEVE ser composta pela versão MAJOR da especificação da API com prefixo “v”.
resource
Nome de um recurso. Os nomes DEVEM ser substantivos no plural e, para casos de nomes compostos, DEVEM estar separados por um hífen (“-”). 
{resource-id}
O identificador do recurso. Este campo é opcional, caso não queira fazer referência a um recurso em específico.




Nos APIs da Evoluservices, o path DEVE sempre começar como prefixo /apis/, seguidos de /v{versão-do-api}. A versão da API DEVE seguir as regras conforme descritas na seção “Versionamento de API”. A exceção à regra se aplica apenas à primeira versão das APIs que se inicia com prefixo /api/, sem a versão, por ser um sistema legado. Ademais, a codificação das URLs DEVEM ser em UTF-8.

Regras para recursos e sub-recursos

Um recurso individual deve ser representado no formato /nome-do-recurso/{id-do-recurso} no path.
Para representar um sub-recurso de um recurso individual, é RECOMENDADO criar um nome que represente a relação do recurso e do sub-recurso, por exemplo, ao invés de /recurso/{id-do-recurso}/subrecursos, utilizar, /recurso-subrecursos/.
No caso em que quer representar um sub-recurso em específico, é RECOMENDADO que, ao invés de /recurso/{id-do-recurso}/subrecursos/{id-do-subrecurso}, utilizar o formato /recurso-subrecursos/{id-do-subrecurso}.

Regras para identificação dos recursos e subrecursos

É RECOMENDADO que para ID do recurso seja utilizado um prefixo que deixe claro o recurso ao qual se refere com um underline no final (_), seguido de UUID.


No path, um identificador de recursos NÃO DEVE ser seguido de um outro identificador de recursos, por exemplo /recurso/{id-do-recurso}/{id-do-outro-recurso}
Um sub-recurso apenas DEVE ser retornado se o recurso o qual faz parte existe e se realmente existe uma relação entre eles, por motivo de segurança e integridade de dados.
Os identificadores DEVEM ser percent-encoded.


Query Parameters

Query parameters DEVEM estar no formato {variável}={valor}.
As query parameters apenas DEVEM ser usados para ordenar ou filtrar uma coleção de recursos e sub-recursos e, portanto, NÃO DEVEM ser utilizados nos retornos de um recurso ou sub-recurso individual.
As query parameters NÃO PODEM ser usadas para buscar algum recurso ou sub-recurso. Para isso, utilizar o identificador de recursos no path.
As query parameters NÃO DEVEM ser campos obrigatórios numa URL. 
Para requisições POST, NÃO DEVE utilizar os query parameters, por conta do uso do corpo da requisição como envio de dados de um recurso.



Regras de nomenclatura para query parameters

Se uma variável de query for um nome composto, as palavras DEVEM estar separadas com underline (_).
Para o uso de mais de um query parameter numa URL, as queries DEVEM ser separados por caractere and (&)
Para o uso de mais de um valor dentro de uma mesma variável do query parameter, os valores DEVEM estar separados por uma vírgula (,).
Nas queries, DEVEM apenas usar caracteres minúsculos, alfanuméricos e underlines. Em termos de codificação, DEVE ser percent-encoded.
 

JSON

Esta seção aborda como os campos e valores do JSON devem ser formatados ou usados durante a implementação dos APIs.

Regras gerais de nomenclatura

Para APIs da Evoluservices, convencionamos que os campos DEVEM estar no formato camelCase.
Os nomes dos campos DEVE ser em inglês e conectivos como “of” ou “the” DEVEM ser omitidos.

Tipos primitivos

Strings

É RECOMENDADO que, para haver a consistência dos valores nos JSONs de input, criar um pattern utilizando expressões regulares. Entretanto, DEVE-SE utilizar o pattern apenas quando realmente o valor deve possuir um formato padrão. Por exemplo, um valor que representa uma descrição de uma transação não possui um padrão definido. Neste caso, o pattern não seria necessário. Porém, para um valor do campo que precisa ser um UUID, é necessário que haja um pattern que atenda ao seu formato.
Para a consistência, é RECOMENDADO que seja estabelecido um tamanho mínimo e máximo para o valor.
O valor do string DEVE sempre estar dentro de aspas duplas (“”)


Booleans
NÃO É RECOMENDADO que sejam utilizados prefixo “is” nos campos com valor booleano, entretanto, PODE-SE usar o prefixo “has”, quando achar necessário.

Enums

Os valores de Enum DEVEM apenas aceitar caracteres alfanuméricos e underline(_) e DEVEM ser representados com letras maiúsculas.
É RECOMENDADO que o valor dos Enums nunca se altere após a implementação nos APIs para garantir a consistência.
É RECOMENDADO que o tamanho mínimo seja 1 e máximo 255. O campo NÃO DEVE ser vazio para Enum.

Numbers

No caso dos campos numéricos, é RECOMENDADO que, em casos em que o seu valor não necessita de números muito grandes, use o formato em int (ou int32) e para números muito grandes usem o formato em long (ou int64). Por exemplo, para parcelas de uma transação, o número não passaria dos 100 e formato int seria o ideal. No caso do valor da transação que possibilita ser um número na casa dos milhões, seria ideal utilizar o campo no formato long.
É RECOMENDADO que, para consistência do valor, determine um limite mínimo e máximo permitido para um campo numérico.

Null
Se o campo é do tipo Null, significa que o campo aceita valores nulos. Para representá-lo uma propriedade na forma campo: null  DEVE estar explícito no JSON. 
O fato do campo não estar explícito no JSON não significa que o campo é do tipo Null. Neste caso, é definido como tipo Undefined.
Valor null também é diferente de outros tipos de valores que podem indicar um campo vazio como array vazio ([]) ou String vazio (“”)

Tipos de valores comuns no JSON

Valor monetário

O valor monetário NÃO DEVE ser representado por apenas um valor decimal. DEVE-SE cria uma classe representando o valor, com menor unidade de contagem possível (por exemplo, para real brasileiro colocar valor em centavos e no iene japonês, colocar valor em ienes). E o código contendo a sigla da moeda que está sendo utilizada, de acordo com o [ISO4217](https://pt.wikipedia.org/wiki/ISO_4217).
O valor monetário NÃO PODE ser um valor negativo.

Datas

As datas que indicam um instante, DEVEM usar o formato “YYYY-MM-DDTHH:mm:ssZ” para datas UTC. A inclusão de frações de segundo para maior precisão É PERMITIDA. O “T” entre o dia e a hora serve como separador.  Ao invés do “T”, PODE-SE também utilizar o caractere espaço (“ “), ficando no formato “YYYY-MM-DD HH:mm:ssZ”.
Para timezones que não são UTC, DEVE-SE usar os caracteres “+” ou “-” e as horas de diferença do UTC. Por exemplo, se a data é 2022-04-27 às 11:48:35 da manhã em UTC, em Brasília, que possui timezone UTC-3, o formato da data ficaria “2022-04-27T13:48:35-3:00”. Este mesmo horário em Paris seria “2022-04-27T1:48:35+2:00”


Erros

Quando a requisição retorna erro com status 4XX ou 5XX, é necessário retornar uma resposta contendo no corpo o seguinte JSON:

{
	code: {CÓDIGO_DO_ERRO}
	description: {Descrição detalhada do erro}
}

O campo code, que representa o código do erro, DEVE ser do tipo Enum e estar em inglês. Já o campo description, que detalha o erro retornado, DEVE ser do tipo String e traduzido de acordo com o idioma configurado.

Para cada status code retornado, é RECOMENDADO que siga o padrão de formato como mostrado abaixo:

400 (Bad Request)

Para o Bad Request, que representa os erros de sintaxe, o código do erro DEVE descrever claramente o tipo de erro que o cliente cometeu. Por exemplo, se o cliente mandou um nome do estabelecimento na requisição com caracteres especiais como ‘#’, o que não é bem aceito para esse tipo de valor, o código do erro deveria ser algo semelhante a “INVALID_MERCHANT_NAME”, deixando claro ao cliente que o nome do estabelecimento usado não é aceito pelo servidor.
É RECOMENDADO que o valor do campo “code” seja iniciado com o prefixo 
“INVALID_” para erro 400 para ter uma distinção melhor entre o erro sintático e erro semântico, sendo este, retornado pelo erro 422.

401 (Unauthorized)

Para esse tipo de resposta, como é um erro retornado quando a autenticação não foi feita, não existem erros muito distintos. Portanto, RECOMENDA-SE que o campo code tenha o valor fixo “UNAUTHORIZED” para deixar claro que é a descrição padrão do status 401 e não precisar necessariamente recorrer ao código do erro.
Na descrição, é RECOMENDADO que uma mensagem como “Token de autenticação é desconhecido ou está expirado.” seja adicionada no campo. NÃO RECOMENDAMOS uma descrição específica para este erro, pois explicitar erros detalhados de autenticação ao cliente não seria uma boa prática.

403 (Forbidden)

Para esse status code, assim como o erro 401, não existem motivos muito distintos para o erro. Esse erro geralmente é retornado quando o cliente não possui alguma permissão interna do cliente. Considerando este fato, RECOMENDA-SE, da mesma forma feita no caso do erro 401, fixar o valor do campo “code” como “FORBIDDEN” para deixar clara a descrição padrão para este status code.
Na descrição, é RECOMENDADO que introduza a descrição com algo parecido com “Token de autenticação é válido, porém, você não tem a permissão de:” e, em seguida, explicitar as permissões que o cliente não possui.
As APIs PODEM omitir permissões que são consideradas sensíveis. Por exemplo, explicitar permissões relacionadas às regras antifraude ao cliente não seria uma boa ideia por conta do risco deste burlá-las ao passar alguma transação.

404 (Not Found)

No caso do erro 404, DEVE-SE deixar claro, no código, o recurso o qual não foi encontrado. Por exemplo, se o recurso “estabelecimento” (merchant) está ausente, poderia ser MERCHANT_NOT_FOUND.
RECOMENDA-SE que no código contenha o prefixo “NOT_FOUND” para deixar claro que o erro se trata da ausência de um recurso.

422 (Unprocessable)

Para response que retorna este status code, não existe um padrão de formato de código, porém DEVE deixar claro qual foi o erro semântico. Por exemplo, se a parcela mínima permitida para uma transação está maior que a parcela máxima permitida, o código poderia ser “MIN_INSTALLMENT_GRETER_THAN_MAX_INSTALLMENT”.

5XX (Erros no servidor)

Esse tipo de erro raramente acontecem, mas se ocorrer, é RECOMENDADO que no campo “code” seja inserido enum com uma descrição simples como “SERVER_ERROR”.
Na descrição, é RECOMENDADO inserir uma mensagem recomendando que contate o suporte do servidor, caso esse tipo de erro ocorra, já que o cliente não tem controle sobre esse tipo de erro.


Versionamento da API

Ao criar uma API, você terá pelo menos dois itens distintos para versionar: a implementação da API; e a especificação da API. 

Na implementação, ou seja, no serviço da API, RECOMENDAMOS que seja usado versionamento [semântico](https://semver.org/lang/pt-BR/).
Na especificação OpenAPI, a API DEVE seguir o  padrão semântico com adição do prefixo “v” e remoção da versão do PATCH.

Os endpoints da API DEVEM refletir a versão da API, mas apenas a versão MAJOR. Todos os endpoints das APIs devem começar com “/apis/v{major_version}/”. Mas você pode configurar um proxy para não deixar este prefixo na sua aplicação.

Exemplo errado:
1.3 (faltou o “v” de versão)
v0.1 (major_version não pode ser 0, mas você pode usar 0 para indicar que a especificação ainda está em desenvolvimento)
endpoint: /apis/2/recurso (faltou o “v”)
endpoint: /apis/v1.3/recurso (incluiu o minor_version no endpoint)

Exemplo correto:
v1.2 (versão da especificação)
…/apis/v2/recurso

Uma atualização da versão MAJOR PODE ser retrocompatível. Já uma atualização da versão MINOR PRECISA ser retrocompatível.

Scopes OAuth 2.0

Com exceção da primeira versão, que utiliza o BasicAuth ou JWT, os APIs da Evoluservices utilizam a autenticação do tipo OAuth 2.0.
Na autenticação em OAuth 2.0, existe um mecanismo chamado Scopes, que possibilita limitar o acesso às operações efetuadas por APIs através do token de autenticação.
Cada scope é representado por um string, no seguinte formato:

scope.operation
\___/ \_______/
  |       |
  1       2
1. Escopo, que descreve onde o acesso está sendo limitado
2. O tipo de operação que está sendo limitado.

O escopo e o tipo de operação que está sendo limitado são divididos pelo ponto. 


Regras para Scopes


O escopo DEVE ser um substantivo e estar com a letra minúscula.
Excluindo alguns escopos padronizados como openid, que é utilizado para permitir o uso do OpenID Connect ou offline que permite atualizar o token depois de um longo período, RECOMENDA-SE que seja o nome do recurso e, em casos de nomes compostos, DEVE-SE separar por um hífen (-).
Os tipos de operação PODEM ser de leitura (read), que é geralmente usado para permissão de busca de recursos, escrita (write), geralmente usado para permissão de criação, atualização e remoção de recursos ou alguma operação mais complexa em relação ao mesmo. Além desses, existe o caso em que aceita todas as operações para um certo escopo, representado por um asterisco (*) e novos tipos de operações PODEM ser criadas e incluídas para cada escopo, caso necessite de adaptações em relação às permissões de acesso. Entretanto, NÃO É RECOMENDADO que existam tipos de operações em excesso. Neste caso, É RECOMENDADO o uso de permissões internas.
Se deseja permitir total acesso à uma certa operação, basta o scope ser um asterisco (*).
O scope NÃO PERMITE aceitar um tipo de operação para qualquer escopo, ou seja, estar no formato *.read, por exemplo. 



Apêndice A - Palavras-chave

Este Apêndice diz, com mais detalhes, os significados de cada palavra chave descrito conforme [RFC2119](https://www.ietf.org/rfc/rfc2119.txt), descritos a seguir:

PRECISA ou os termos OBRIGATÓRIO ou MANDATÓRIO, significa que é um requerimento absoluto na especificação.
NÃO PODE ou o termo PROIBIDO significa que é uma proibição absoluta na especificação.
DEVERÁ, DEVE ou DEVEM, significa que pode existir algumas circunstâncias em particular, que a regra deve ser desconsiderada, mas todas as implicações devem ser compreendidas e cuidadosamente relevadas antes de escolher um curso diferente.
NÃO DEVERÁ, NÃO DEVE ou NÃO DEVEM, significa que pode existir algumas circunstâncias em particular em que a regra pode ser aceita, mas todas as implicações devem ser compreendidas e cuidadosamente relevadas antes de implementar qualquer comportamento descrito com esta palavra-chave.
RECOMENDADO significa que a regra é aceitável, porém, não há convicções que que ela DEVE ser absoluta.
PODE ou o adjetivo OPCIONAL, significa que a regra é verdadeiramente opcional. Um fornecedor pode optar por incluir a regra porque um
determinado mercado exige ou porque um fornecedor sente que a aplicação da regra aprimora o produto, enquanto que outro fornecedor pode omitir-la. Uma implementação que não inclui uma opção específica DEVE ser    preparado para interoperar com outra implementação que não inclui a opção, embora talvez com funcionalidade reduzida.  No mesmo fluxo, uma implementação que inclui uma opção específica DEVE estar preparada para interoperar com outra implementação que não inclui tal opção (exceto para o recurso que a opção fornece).
