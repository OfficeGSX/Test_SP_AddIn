---
title: Effectuer des requêtes de lot avec les API REST
ms.prod: SHAREPOINT
ms.assetid: d6aab58f-77d2-4f0d-a007-6d55ba865d07
---


# Effectuer des requêtes de lot avec les API REST
Apprendre à utiliser l'option de requête  `$batch` avec les API REST/OData.
Cet article décrit le traitement par lots des requêtes et des opérations avec les API REST/ODATA de Microsoft SharePoint Online (et SharePoint sur site 2016 et ultérieur) et le  [sous-ensemble de fichiers et de dossiers](http://msdn.microsoft.com/fr-fr/office/office365/api/files-rest-operations) des API REST d'Office 365. Cette technique améliore les performances de votre complément en combinant de nombreuses opérations en une seule requête au serveur et en renvoyant une seule réponse.





## Synthèse de l'option $batch

SharePoint Online (et SharePoint sur site 2016 ou ultérieur) et les API Office 365 mettent en œuvre l'option de requête OData  `$batch`. Vous pouvez donc vous reporter à la  [documentation officielle](http://www.odata.org/documentation/odata-version-3-0/batch-processing) pour obtenir des instructions d'utilisation détaillées. (Vous pouvez également consulter le blog de Connell sur le sujet en commençant par la [Partie 1, relative au traitement par lot des API REST SharePoint](http://www.andrewconnell.com/blog/part-1-sharepoint-rest-api-batching-understanding-batching-requests).) Ce document contient uniquement un rappel des principaux points :




- L'URL de la requête est constituée de l'URL du service racine et de l'option  `$batch` (par exemple, `https://fabrikam.sharepoint.com/_api/$batch` ou `https://fabrikam.office365.com/api/v1.0/me/$batch`).


- Le corps de la requête HTTP est de type MIME  *multipart/mixed*  .


- Le corps de la requête est divisé en plusieurs parties, séparées par une chaîne de limitation spécifiée dans l'en-tête de la requête.


- Chaque partie du corps comporte son propre verbe HTTP, sa propre URL REST et son propre corps interne, le cas échéant.


- Un composant peut être une opération de lecture (ou un appel de fonction), ou un ensemble de modifications d'une ou plusieurs opérations d'écriture (ou appels de fonctions). Un ensemble de modifications est lui-même un type MIME  *multipart/mixed*  avec des sous-parties contenant des opérations d'insertion, de mise à jour ou de suppression.

    > **IMPORTANTE**
      > À l'heure actuelle, SharePoint et les API Office 365 ne prennent pas en charge la fonctionnalité « tout ou rien » pour les ensembles de modification contenant plusieurs opérations. Si l'une des opérations enfants échoue, les autres s'exécutent quand même et ne sont pas annulées. 

## Exemples de code

Exemples de code utilisant l'option de requête  `$batch` dans les API REST/OData SharePoint :




- **C#:** [OfficeDev/Core.ODataBatch](https://github.com/OfficeDev/PnP/tree/master/Samples/Core.ODataBatch)


- **JavaScript :** [andrewconnell/sp-o365-rest](https://github.com/andrewconnell/sp-o365-rest/blob/master/SpRestBatchSample/Scripts/App.js)



## Exemples de requêtes et de réponses

Voici un exemple de requête HTTP brute effectuant un traitement par lots de deux opérations GET qui extraient les titres de tous les éléments de deux listes différentes.



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

Voici un exemple de corps d'une demande HTTP brute qui effectue le traitement par lots d'une opération DELETE sur une liste et d'une opération GET sur la liste des listes SharePoint.





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

--batch_7ba8d60b-efce-4a2f-b719-60c27cc0e70e--```


## Liens vers des bibliothèques utiles

Certaines bibliothèques OData prennent en charge le traitement par lots OData dans de nombreuses langues. Deux exemples sont fournis ci-dessous. Pour obtenir une liste plus complète, consultez la rubrique  [OData Libraries](http://www.odata.org/libraries/).




-  [Bibliothèque OData .NET](http://msdn.microsoft.com/fr-fr/office/microsoft.data.odata%28v=vs.90%29). Voir particulièrement les classes **ODataBatch***.


-  [Bibliothèque Datajs](http://datajs.codeplex.com/documentation). Voir particulièrement  [Batch operations](http://datajs.codeplex.com/wikipage?title=datajs%20OData%20API&amp;referringTitle=Documentation#Batch).



