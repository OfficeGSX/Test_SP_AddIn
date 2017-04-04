---
title: Fazem solicitações de lote com as APIs REST
ms.prod: SHAREPOINT
ms.assetid: d6aab58f-77d2-4f0d-a007-6d55ba865d07
---


# Fazem solicitações de lote com as APIs REST
Saiba como usar a opção de consulta  `$batch` com as APIs REST/OData.
Este artigo descreve como você pode lote consultas e operações contra a API REST/OData do Microsoft SharePoint Online (e no local SharePoint 2016 e posterior) e o  [subconjunto de arquivos e pastas](http://msdn.microsoft.com/en-us/office/office365/api/files-rest-operations) de Office 365 APIs REST. Com essa técnica, você pode melhorar o desempenho do seu suplemento combinando muitas operações em uma única solicitação para o servidor e back uma única resposta.
  
    
    


## Resumo executivo da opção $batch

SharePoint Online (e no local SharePoint 2016 e posterior) e o Office 365 APIs implementar a opção de consulta OData  `$batch` , portanto, você pode contar com [a documentação oficial](http://www.odata.org/documentation/odata-version-3-0/batch-processing) para obter detalhes sobre como usá-lo. (É outra opção para ver as postagens de blog de Andrew Connell sobre o assunto, começando à meia [parte 1 - SharePoint REST API processamento em lotes](http://www.andrewconnell.com/blog/part-1-sharepoint-rest-api-batching-understanding-batching-requests).) Este é apenas um lembrete dos pontos principais:
  
    
    

- A URL da solicitação consiste a URL do serviço de raiz e a opção  `$batch` ; Por exemplo, `https://fabrikam.sharepoint.com/_api/$batch` ou `https://fabrikam.office365.com/api/v1.0/me/$batch`.
    
  
- O corpo da solicitação HTTP é de tipo MIME  *com diversas partes/resumida*  .
    
  
- O corpo da solicitação é dividido em partes que são separados uns dos outros por uma cadeia de caracteres de limite especificado no cabeçalho da solicitação.
    
  
- Cada parte do corpo tem seu próprio verbo HTTP e URL do REST e seu próprio corpo interno quando aplicável.
    
  
- Uma parte pode ser uma operação de leitura (ou chamada de função), ou um conjunto de alterações de uma ou mais escrever operações (ou invocações de função). Um conjunto de alterações em si é um MIME tipo  *com diversas partes/resumida*  com subpartes que contêm inserir, atualizar ou excluir operações.
    
    > **IMPORTANTE**
      > Neste momento, SharePoint e Office 365 APIs não suportam a funcionalidade "tudo ou nada" para conjuntos de alterações que tenham mais de uma operação dentro deles. Se qualquer uma das operações filho falhar, os outros ainda concluir e não são revertidos.

## Code samples

Amostras de código que usa a opção de consulta  `$batch` contra SharePoint APIs REST/OData:
  
    
    

- **C#:** [OfficeDev/Core.ODataBatch](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.ODataBatch)
    
  
- **JavaScript:** [andrewconnell/sp-o365-rest](https://github.com/andrewconnell/sp-o365-rest/blob/master/SpRestBatchSample/Scripts/App.js)
    
  

## Exemplo de solicitações e respostas

O exemplo a seguir é um exemplo de uma solicitação HTTP bruto que duas operações GET que recuperar os títulos de todos os itens de duas listas diferentes de lotes.
  
    
    

```

POST https://fabrikam.sharepoint.com/_api/$batch HTTP/1.1
Authorization: Bearer <access token omitted>
Content-Type: multipart/mixed; boundary=batch_e3b6819b-13c3-43bb-85b2-24b14122fed1
Host: fabrikam.sharepoint.com
Content-Length: 527
Expect: 100-continue

--batch_e3b6819b-13c3-43bb-85b2-24b14122fed1
Content-Type: application/http
Content-Transfer-Encoding: binary

GET https://fabrikam.sharepoint.com/_api/Web/lists/getbytitle('Composed%20Looks')/items?$select=Title HTTP/1.1

--batch_e3b6819b-13c3-43bb-85b2-24b14122fed1
Content-Type: application/http
Content-Transfer-Encoding: binary

GET https://fabrikam.sharepoint.com/_api/Web/lists/getbytitle('User%20Information%20List')/items?$select=Title HTTP/1.1

--batch_e3b6819b-13c3-43bb-85b2-24b14122fed1--

```

O exemplo a seguir é um exemplo do corpo de uma solicitação HTTP bruto que obter das lista-de-listas do SharePoint e uma exclusão de uma lista de lotes.
  
    
    



```

POST https://fabrikam.sharepoint.com/_api/$batch HTTP/1.1
Authorization: Bearer <access token omitted>
Content-Type: multipart/mixed; boundary=batch_7ba8d60b-efce-4a2f-b719-60c27cc0e70e
Host: fabrikam.sharepoint.com
Content-Length: 647
Expect: 100-continue

--batch_7ba8d60b-efce-4a2f-b719-60c27cc0e70e
Content-Type: multipart/mixed; boundary=changeset_efb6b37c-a5cd-45cb-8f5f-4d648006e65d

--changeset_efb6b37c-a5cd-45cb-8f5f-4d648006e65d
Content-Type: application/http
Content-Transfer-Encoding: binary

DELETE https://fabrikam.sharepoint.com/_api/Web/lists/getbytitle('OldList') HTTP/1.1
If-Match: "1"

--changeset_efb6b37c-a5cd-45cb-8f5f-4d648006e65d--
--batch_7ba8d60b-efce-4a2f-b719-60c27cc0e70e
Content-Type: application/http
Content-Transfer-Encoding: binary

GET https://fabrikam.sharepoint.com/_api/Web/lists HTTP/1.1

--batch_7ba8d60b-efce-4a2f-b719-60c27cc0e70e--
```


## Links para bibliotecas úteis

Há bibliotecas do OData que oferecem suporte a OData lotes para vários idiomas. A seguir estão dois exemplos. Para obter uma lista mais completa, consulte  [OData bibliotecas](http://www.odata.org/libraries/).
  
    
    

-  [Biblioteca de OData do .NET](http://msdn.microsoft.com/en-us/office/microsoft.data.odata%28v=vs.90%29). Consulte especialmente as classes **ODataBatch***.
    
  
-  [Biblioteca de datajs](http://datajs.codeplex.com/documentation). Consulte especialmente  [operações em lotes](http://datajs.codeplex.com/wikipage?title=datajs%20OData%20API&amp;referringTitle=Documentation#Batch).
    
  

