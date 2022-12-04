
# Sobre

Esta documentação é um exercício do curso [Decolando em TW: da API à documentação](https://marimoreiratw.com.br/decolando-em-tw/) e tem como objetivo documentar as funcionalidades de adicionar, pesquisar e excluir imagens na API de gatinhos.

A documentação é composta por uma visão geral da API, endereço e forma de autenticação, descrição do objeto _image_, além da especificação de quatro endpoints com respectivos parâmetros e exemplos de requisição e resposta.

# The Cat API

[The Cat API](https://thecatapi.com/ "Site oficial The Cat API") é uma API pública que permite a manipulação de imagens de gatos, com funcionalidades como adicionar imagens, filtrar por raça, votar e favoritar. Pode ser utilizada de forma gratuita, com limite de 10 mil requisições por mês.

## Visão geral - Imagens

O recurso de imagens possibilita **adicionar e recuperar fotos de gatos** através de **quatro endpoints**, explicados na seção de casos de uso:
1. POST /images/upload :arrow_right: [Adicionar imagens](#adicionar-seu-gatinho-cat2)
2. GET /images/{image_id} :arrow_right: [Recuperar uma imagem por ID](pesquisar-pelo-gatinho-adicionado-smile_cat) 
3. GET /images :arrow_right: [Recuperar todas as imagens](pesquisar-pelo-gatinho-adicionado-smile_cat)
4. DELETE /images/{image_id} :arrow_right: [Excluir imagens](excluir-um-gatinho-crying_cat_face)

Possui uma verificação de inteligência artificial que **valida se a imagem é de um gato realmente**.

:heavy_check_mark: Exemplo de imagem aceita:

![manja-vitolic-gKXKBY-C-Dk-unsplash](https://user-images.githubusercontent.com/39387852/205504865-fe67faf6-28b9-492c-b84f-5fc40268f0f8.jpg "Foto de um gato branco e preto com as patas dianteiras à mostra em um fundo verde")

Imagem: [Manja Vitolic on Unsplash](https://unsplash.com/photos/gKXKBY-C-Dk)

:x: Exemplo de imagem não aceita:

![cat-doctor-who](https://user-images.githubusercontent.com/39387852/205444849-12c2e4f6-f4c7-4662-aa1c-2c3072aa3be3.jpg "Foto de uma mulher fantasiada de gato, uma personagem do seriado Doctor Who")

Imagem: Reprodução

## Caminho nas chamadas

O endereço utilizado para as requisições é: **https://api.thecatapi.com/v1**

## Autenticação

Esta API utiliza autenticação via `token`, enviada como um parâmetro tipo `header`. A chave API pode ser gerada gratuitamente por meio de um rápido registro no site oficial.

**Como gerar sua chave API para utilizar como token de acesso:**

1. Faça registro em [thecatapi.com/signup](https://thecatapi.com/signup) para obter sua chave API por e-mail;
2. Adicione a chave API no campo `x-api-key` do `header` em todas as requisições.

**Exemplo no Postman:**

![image](https://user-images.githubusercontent.com/39387852/205442726-0f62efe9-7543-4aa2-b301-6b94865a7eaf.png)

## Descrição do objeto - images

O objeto `images` representa as fotos de gatos enviadas. Caso a imagem não seja de gato, ou seja de alguma forma inapropriada, ela não será adicionada.

| Nome | Descrição | Tipo | Obrigatório |
|------|-----------|------|-------------|
| `id` | ID da imagem. Valor gerado automaticamente. | `UUID` | Sim :heavy_check_mark: |
| `url` | URL para acessar a imagem. | `string` | Sim :heavy_check_mark: |
| `width` | Largura da imagem em pixels. Valor gerado automaticamente. | `integer` | Sim :heavy_check_mark: |
| `height` | Altura da imagem em pixels. Valor gerado automaticamente. | `integer` | Sim :heavy_check_mark: |
| `created_at` | Data de upload da imagem no formato ISO. Valor gerado automaticamente. <br>*2022-11-24T17:41:35.000Z* | `datetime` | Sim :heavy_check_mark: | 
| `original_filename` | Nome do arquivo original. | `string` | Sim :heavy_check_mark: | 
| `sub_id` | ID para identificação interna. | `string` | Não |
| `pending` | Status interno para indicar se está pendente. Valores:<br>*0=falso<br>1=verdadeiro* | `integer` | Não | 
| `approved` | Status interno para indicar se está aprovada. Valores:<br>*0=falso<br>1=verdadeiro* | `integer` | Não | 
| `breed_ids` | Identificação de raça. **Não implementado.** | N/A | Não | 

# Casos de uso

A seguir são apresentados os quatro endpoints que permitem adicionar, recuperar e excluir imagens de gatos, apresentando os parâmetros necessários e exemplo de requisição e resposta.

## Adicionar seu gatinho :cat2:

**Endpoint**: `POST /images/upload`

Adicione uma nova imagem no sistema carregando um arquivo válido contendo um gato.
Os tipos de arquivos aceitos são: .gif, .jpg, e .png.

**Parâmetros - form**

| Nome | Descrição | Tipo | Obrigatório |
|------|-----------|------|-------------|
| `file` | Arquivo em .gif, .png, ou .jpg | `file` | Sim :heavy_check_mark: |
| `sub_id` | ID para identificação interna, como um identificador no seu sistema de origem. | `string` | Não |

### Requisição

Exemplo de requisição em  **cURL**:

```c
curl --location --request POST 'https://api.thecatapi.com/v1/images/upload' \
--header 'x-api-key: live_xxxxxxxxxx' \
--form 'file=@"_3QaRAObB/cat-201.jpg"' \
--form 'sub_id="132456"'
```

### Resposta

Se a imagem enviada for válida, o retorno será de sucesso **201 - Created** e informará um JSON com os campos criados:

```json
{
    "id": "JTC8TKTkG",
    "url": "https://cdn2.thecatapi.com/images/JTC8TKTkG.jpg",
    "sub_id": "132456",
    "width": 750,
    "height": 600,
    "original_filename": "cat-201.jpg",
    "pending": 0,
    "approved": 1
}
```

Caso a imagem não seja válida, ou seja, não é uma imagem identificada como de gato, o retorno será o erro **400 - Bad Request** com mensagem não formatada em JSON: `Classifcation failed: correct animal not found.`

Exemplo no Postman:
![image](https://user-images.githubusercontent.com/39387852/205445104-ca2484e1-7de1-4121-9b13-ddabf68d8564.png "Tela do Postman mostrando a requisição e o retorno com erro 400 Bad Request")

## Pesquisar pelo gatinho adicionado :smile_cat:

**Endpoint**: `GET /images/{image_id}`

Pesquise por um ID para recuperar uma imagem enviada anteriormente.

O ID pode ser obtido na resposta do endpoint de [adicionar imagens](#adicionar-seu-gatinho-cat2).

**Parâmetros - path**

| Nome | Descrição | Tipo | Obrigatório |
|------|-----------|------|-------------|
| `id` | ID da imagem. | `UUID` | Sim :heavy_check_mark: |

### Requisição

Exemplo de requisição em  **cURL**:

```c
curl --location --request GET 'https://api.thecatapi.com/v1/images/JTC8TKTkG' \
--header 'x-api-key: live_xxxxxxxxxx'
```

### Resposta

Se a imagem existir, o retorno será de sucesso **200 - OK** e informará um JSON com a descrição da imagem, contendo os campos de identificação, endereço, largura e altura:

```json
{
    "id": "JTC8TKTkG",
    "url": "https://cdn2.thecatapi.com/images/JTC8TKTkG.jpg",
    "width": 750,
    "height": 600
}
```

Caso o ID enviado não exista, o retorno será o código **400 - Bad Request** com mensagem não formatada em JSON: `Couldn't find an image matching the passed 'id' of xxxxx`.


## Pesquisar por todos os gatinhos :smile_cat::smile_cat:

**Endpoint**: `GET /images`

Pesquise por todas as imagens existentes no sistema, enviando como parâmetros alguns filtros para limitar a busca.

Nota: em sua versão gratuita, a API define o valor máximo do parâmetro `limit`para 25.

## Excluir um gatinho :crying_cat_face:

**Endpoint**: `DELETE /images/{image_id}`

Exclua uma imagem do sistema. 

**Importante**: este endpoint executa um *soft delete*, ou seja, não exclui de fato do banco de dados, apenas indica que não deve mais ser recuperada.

**Parâmetros - path**

| Nome | Descrição | Tipo | Obrigatório |
|------|-----------|------|-------------|
| `id` | ID da imagem.| `UUID` | Sim :heavy_check_mark: |

### Requisição

Exemplo de requisição em  **cURL**:

```c
curl --location --request DELETE 'https://api.thecatapi.com/v1/images/42eTgMuoL' \
--header 'x-api-key: live_xxxxxxxxxx'
```

### Resposta

Se a imagem existir, o retorno será de sucesso **204 - No Content** sem um valor de retorno, o que indicar que a imagem foi excluída.

Exemplo no postman:

![delete](https://user-images.githubusercontent.com/39387852/205504913-816ad58a-c7d8-41ac-a389-3bd922878ea8.PNG)

Caso o ID enviado não exista, o retorno será o código **400 - Bad Request** com mensagem não formatada em JSON: `INVALID_DATA`.

**Para confirmar a exclusão**, execute novamente a [pesquisa da imagem por ID](pesquisar-pelo-gatinho-adicionado-smile_cat), sendo esperado o retorno **400 - Bad Request** com mensagem não formatada em JSON: `Couldn't find an image matching the passed 'id' of xxxxx`.
