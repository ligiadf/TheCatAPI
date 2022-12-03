# The Cat API - Postar imagens

[The Cat API](https://thecatapi.com/) é uma API que pode ser usada gratuitamente e possui diferentes funcionalidades como adicionar imagens, filtrar por raça, votar e favoritar.

Esta documentação é um exercício do curso [Decolando em TW: da API à documentação](https://marimoreiratw.com.br/decolando-em-tw/) e vai focar na funcionalidade de adicionar imagens, pesquisar e excluir imagens.

## Visão geral

O recurso de postar imagens possibilita **adicionar fotos de gatos** e possui uma verificação de inteligência artificial para validar se é uma imagem de gato.

Estão disponíveis quatro endpoints para utilização do recurso de postar imagens: [adicionar](#adicionar-seu-gatinho-cat2), [recuperar uma](pesquisar-pelo-gatinho-adicionado-smile_cat) ou [todas as imagens](pesquisar-pelo-gatinho-adicionado-smile_cat) e [excluir imagens](excluir-um-gatinho-crying_cat_face).

:x: Exemplo de imagens não aceitas:

![cat-doctor-who](https://user-images.githubusercontent.com/39387852/205444849-12c2e4f6-f4c7-4662-aa1c-2c3072aa3be3.jpg "Foto de uma mulher fantasiada de gato, uma personagem do seriado Doctor Who")

## Autenticação

Esta API pública utiliza autenticação via ``token``, enviada como parâmetro ``header``.

1. Faça registro em https://thecatapi.com/signup para obter sua chave API por e-mail
2. Adicione a chave API no ``header`` da requisição, no campo ``x-api-key`` em todas as requisições

Exemplo no Postman:

![image](https://user-images.githubusercontent.com/39387852/205442726-0f62efe9-7543-4aa2-b301-6b94865a7eaf.png)

## Descrição do objeto - images

O objeto `images` representa as fotos de gatos enviadas. Caso a imagem não seja de gato, ou seja de alguma forma inapropriada, ela será rejeitada.

| Nome | Descrição | Tipo | Obrigatório |
|------|-----------|------|-------------|
| `id` | ID da imagem. | `UUID` | Sim :heavy_check_mark: |
| `url` | URL para acessar a imagem. | `string` | Sim :heavy_check_mark: |
| `width` | Largura da imagem em pixels. Valor gerado automaticamente. | `integer` | Sim :heavy_check_mark: |
| `height` | Altura da imagem em pixels. Valor gerado automaticamente. | `integer` | Sim :heavy_check_mark: |
| `created_at` | Data de upload da imagem no formato ISO. Valor gerado automaticamente. <br>*2022-11-24T17:41:35.000Z* | `datetime` | Sim :heavy_check_mark: | 
| `original_filename` | Nome do arquivo original. | `string` | Sim :heavy_check_mark: | 
| `sub_id` | ID para identificação interna. | `string` | Não |
| `pending` | Status interno para indicar se está pendente. Valores:<br>*0=falso<br>1=verdadeiro* | `integer` | Não | 
| `approved` | Status interno para indicar se está aprovada. Valores:<br>*0=falso<br>1=verdadeiro* | `integer` | Não | 
| `breed_ids` | Identificação de raça. **Não implementado.** | N/A | Não | 

## Casos de uso

### Adicionar seu gatinho :cat2:

**Endpoint**: ``POST /images/upload``

Adicione uma nova imagem no sistema carregando um arquivo válido contendo um gato.

Formatos aceitos: .gif, .jpg, ou .png

#### Requisição

**Requisição - body**

| Nome | Descrição | Tipo | Obrigatório |
|------|-----------|------|-------------|
| `file` | Arquivo em .gif, .png, ou .jpg | `file` | Sim :heavy_check_mark: |
| `sub_id` | ID para identificação interna. | `string` | Não |

#### Resposta

**400 - Bad request**

Caso a imagem não seja válida, ou seja, não é uma imagem identificada como de gato, o retorno será o erro **400 - Bad Request** com mensagem ``Classifcation failed: correct animal not found.``

![cat-doctor-who](https://user-images.githubusercontent.com/39387852/205444849-12c2e4f6-f4c7-4662-aa1c-2c3072aa3be3.jpg "Foto de uma mulher fantasiada de gato, uma personagem do seriado Doctor Who"

![image](https://user-images.githubusercontent.com/39387852/205445104-ca2484e1-7de1-4121-9b13-ddabf68d8564.png "Tela do Postman mostrando a requisição e o retorno com erro 400 Bad Request")


### Pesquisar pelo gatinho adicionado :smile_cat:

### Pesquisar por todos os gatinhos :smile_cat::smile_cat:

### Excluir um gatinho :crying_cat_face:

Exclua uma imagem do sistema.

Nota: este endpoint executa um *soft delete*, ou seja, não exclui de fato do banco de dados, apenas indica que não deve mais ser recuperada.