---
title: Herramientas y entornos para desarrollar complementos para SharePoint
ms.prod: SHAREPOINT
ms.assetid: 6906eb86-8270-4098-8106-1e8d0d3c212e
---


# Herramientas y entornos para desarrollar complementos para SharePoint
Obtenga información sobre las opciones para crear un entorno de desarrollo para Complementos de SharePoint.
Existen dos patrones básicos para entornos de desarrollo de Complementos de SharePoint:





- **El sitio web de SharePoint para prueba y depuración se encuentra en un sitio web de SharePoint Online en una suscripción a Office 365.** Generalmente, Visual Studio se instala en un equipo local, pero Visual Studio basada en la nube también es una opción.


- **El sitio web de SharePoint para prueba y depuración se encuentra en una granja de SharePoint local y de un servidor.** Visual Studio se instala en el mismo equipo.



Tenga en cuenta lo siguiente:





- Prácticamente cualquier complemento que cree se puede implementar en SharePoint Online o en granjas de SharePoint locales, independientemente del tipo de entorno utilizado. Como regla general, los complementos que no se pueden implementar en SharePoint Online tampoco se pueden desarrollar con eso. Por ejemplo: complementos que requieren  [Permisos de control total](add-in-permissions-in-sharepoint-2013.md) y complementos que usan el [sistema de autorización de confianza elevada](creating-sharepoint-add-ins-that-use-high-trust-authorization.md).


- Puede desarrollar Complementos de SharePoint hospedados en SharePoint y hospedados en proveedor, independientemente del tipo de entorno utilizado.


- Puede tener sitios para prueba locales y SharePoint Online.


- Teniendo en cuenta todo, las dos opciones son igualmente fáciles de configurar.


 **Para crear el entorno de SharePoint Online** mediante una suscripción de SharePoint Online que puede usar para desarrollo, consulte [Crear un sitio para desarrolladores en una suscripción existente de Office 365](create-a-developer-site-on-an-existing-office-365-subscription.md). **Para crear el entorno local**, consulte [Configurar un entorno de desarrollo en el nivel local para complementos para SharePoint](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md).
> **NOTA**
> Este tema solo se ocupa de entornos de desarrollo de Complementos de SharePoint. Si va a desarrollar soluciones de granja de servidores, consulte  [Configurar un entorno de desarrollo general para SharePoint 2013](http://msdn.microsoft.com/library/08e4e4e1-d960-43fa-85df-f3c279ed6927%28Office.15%29.aspx). Si planea efectuar ambos tipos de desarrollo, comience con el último artículo y consulte  [Configurar un entorno de desarrollo en el nivel local para complementos para SharePoint](set-up-an-on-premises-development-environment-for-sharepoint-add-ins.md) para conocer los pasos adicionales que necesita para desarrollar Complementos de SharePoint.





## Otra información de herramientas


-  [Crear un complemento básico hospedado en SharePoint para SharePoint con las herramientas de desarrollo Napa Office 365](create-a-basic-sharepoint-hosted-add-in-by-using-napa-office-365-development-too.md)


-  [Crear complementos de SharePoint en Visual Studio](create-sharepoint-add-ins-in-visual-studio.md)



## Recursos adicionales
<a name="bk_addresources"> </a>


-  [Complementos de SharePoint](sharepoint-add-ins.md)



