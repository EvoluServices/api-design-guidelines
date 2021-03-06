# Especificação OpenAPI

* Recomendado: O arquivo OpenAPI deve ser capaz de gerar código cliente de maneira eficaz. Ou seja, usando um gerador de código automático, o código gerado deve compilar e ser legível aos desenvolvidores.
* Todos campos `description` devem terminar com ponto final;

## Endpoint

* Os endpoints devem ser os mesmos (padrão REST)... // TODO

### Métodos GET

* Não devem possuir request body. Os parâmetros devem ser enviados na URL;

Cada método do endpoint deve possuir:
* `summary`: explicar em poucas palavras o que o endpoint faz;
* `description`: explicar em detalhes o que o endpoint faz. Incluindo o comportamento de casos limítrofes (se houver).;
* Recomendado: `operationId` (para geração automática do código) // TODO obrigatório?;
* `security`:  que indica o escopo necessário para a realização da operação;

## Especificação de responses

### Response de sucesso

* Respostas de sucesso devem retornar código HTTP `2XX`;
* A descrição deve indicar (não necessarimente nestas palavras):
  - "Sucesso" para código `200`;
  - "Recurso criado" para código `201`;
  - "Requisição aceita" para código `202`;
  - "Recurso atualizado" para código `204`;
  - Incluir detalhes sobre os dados retornados, caso houver. Se não houver dados no corpo da resposta, mencionar que a resposta tem corpo vazio;

### Response de erro

* Recomendado: Iniciar as frases com "Ocorre quando...";
* `400`: deve incluir as maneiras pelas quais os parâmetros da requisição podem estar inválidos;
* `401`: Recomendado: 'Ocorre quando o token de autenticação utilizado é desconhecido ou está expirado.';
* `403`: Recomendado: 'Ocorre quando o token de autenticação utilizado é válido, mas suas permissões não incluem: "Permissão 1", "Permissão 2", etc.'. Podendo omitir permissões sensíveis;
* `404`: Mencionar quais recursos podem não ser encontrados. Mencionando apenas os recursos referenciados na requisição (o id é enviado na requisição);
* `5XX`: Recomendado: 'Erro interno nos servidores da EvoluServices (contate o suporte).';

### Media type

* Retornar preferencialmente `application/json`;

## Schemas

* Recomendado: Todos os schemas devem possuir pelo menos um exemplo (//TODO obrigatório?);
* Cada atributo dos schemas deve possuir um exemplo;
    - Todos os exemplos do tipo string devem ter aspas duplas:
      - Errado:
        - `example: VISA`
        - `example: 'VISA'`
      - Correto:
        - `example: "VISA"`

# Convenção de nomenclatura

## Nomes dos schemas OpenAPI

* Não deve possuir o termo "DTO" (Data Transfer Object);
* Os schemas raíz, usados em requisições, devem possuir sufixo "Request";
* Os schemas raíz, usados na respostas, devem possuir o sufixo "Response";

* Recomendado: Prefixos para schemas de requests e response:
  - "Get" para GET; // TODO diferenciar recurso de lista de recursos?
  - "Create" para POST;
  - "Update" para PUT;
  - "Remove" para DELETE;

- Exemplo:
  - Errado:
    - OrderDto (schema de requisição POST);
    - CreateOrderDto (schema de requisição POST);
    - CreateOrderRequestDto (schema de requisição POST);
    - CreateOrderDTORequest (schema de requisição POST);
    - UpdateOrderRequest (schema de requisição POST);
  - Correto:
    - CreateOrderRequest
