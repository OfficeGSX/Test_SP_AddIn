---
title: Configurar un entorno de desarrollo en el nivel local para complementos para SharePoint
ms.prod: SHAREPOINT
ms.assetid: b0878c12-27c9-4eea-ae3b-7e79e5a8838d
---


# Configurar un entorno de desarrollo en el nivel local para complementos para SharePoint
Obtenga información sobre cómo configurar un entorno de desarrollo especialmente adaptado para desarrollar Complementos de SharePoint con una instalación local de SharePoint.
## Instalar el sistema operativo para el entorno de desarrollo de Complementos de SharePoint
<a name="bk_installOS"> </a>

Los requisitos para un entorno de desarrollo son menos estrictos y costosos que los requisitos para un entorno de producción y las directrices que aquí se describen no son compatibles con la instalación de un entorno de producción. Consulte  [Información general sobre la instalación y configuración de SharePoint 2013](http://technet.microsoft.com/es-es/library/ee667264%28v=office.15%29),  [Requisitos de hardware y software para SharePoint 2013](http://technet.microsoft.com/es-es/library/cc262485%28v=office.15%29) y [Configuración de un entorno para complementos de SharePoint](http://technet.microsoft.com/es-es/library/fp161236%28office.15%29.aspx) para obtener instrucciones sobre la configuración de la instalación de un entorno de producción de SharePoint.



En todos los entornos de desarrollo, se debe usar un equipo con una CPU de x64 y al menos 6 GB de RAM para instalar y ejecutar SharePoint. 24 GB de RAM es preferible.



En función de sus requisitos y presupuesto específicos, podrá elegir alguna de las siguientes opciones:




- Instale SharePoint en Windows Server 2008 R2 Service Pack 1 x64 o en Windows Server 2012.


- Use Microsoft Hyper-V e instale SharePoint en una máquina virtual que tenga el sistema operativo invitado Windows Server 2008 R2 Service Pack 1 x64 o Windows Server 2012. En  [Procedimientos recomendados para la configuración de las máquinas virtuales de SharePoint 2013 y del entorno de Hyper-V](http://technet.microsoft.com/es-es/library/ff621103%28office.15%29.aspx) encontrará los pasos a seguir para configurar una máquina virtual de Microsoft Hyper-V virtual para SharePoint.



> **NOTA**
> Las instalación de SharePoint es compatible únicamente en Windows Server 2008 R2 Service Pack 1 x64 o en Windows Server 2012. Si quiere desarrollar Complementos de SharePoint para SharePoint en Windows 7 o en Windows 8, puede registrarse en un Sitio para desarrolladores de Office 365 y desarrollar complementos de forma remota. 





## Instalar los requisitos previos para el sistema operativo y SharePoint
<a name="bk_prereqsOS"> </a>


1. Ejecute la herramienta PrerequisiteInstaller.exe que se incluye con los archivos de instalación.


2. Ejecute la herramienta Setup.exe que se incluye con los archivos de instalación.


3. Acepte los Términos de licencia del software de Microsoft.


4. En la página **Elija la instalación que desea**, elija **Independiente**.

   **Figura 1. Opción de tipo de instalación**



!\[Tipo de servidor de instalación de SharePoint 2013](images/SP15_app_ServerType.gif)





5. Si se producen errores en la instalación, revise el archivo de registro. Para buscar el archivo de registro, abra una ventana con el símbolo del sistema y luego escriba los siguientes comandos en este símbolo del sistema. Aparecerá asimismo un vínculo al archivo de registro cuando se complete la instalación.

 ```

cd %temp%
dir /od *.log
 ```

6. Una vez se haya completado la instalación, se le solicitará que inicie el Asistente para configuración de Productos y Tecnologías de SharePoint.

    > **NOTA**
      > Es posible que se produzca un error en el Asistente para configuración de Productos y Tecnologías de SharePoint si usa un equipo que esté conectado a un dominio, pero no a un controlador de dominio. Si se produce este error, establezca una conexión con un controlador de dominio directamente o mediante una conexión de red privada virtual (VPN) o bien inicie sesión con una cuenta local que disponga de privilegios administrativos en el equipo. 
7. Una vez que haya finalizado el asistente de configuración, verá la página **Selección de plantilla** del nuevo sitio de SharePoint. En esta página, elija la plantilla **Sitio para desarrolladores**. Solamente podrá implementar Complementos de SharePoint desde Visual Studio en un Sitio para desarrolladores.

   **Figura 2. Elegir la página de la plantilla del sitio**



!\[Página de plantilla de sitio](images/SP15_appChooseSiteTemplate.gif)






## Configurar servicios en SharePoint para el uso de complementos de servidor a servidor
<a name="Servertoserver"> </a>

En este paso, configurará servicios en SharePoint para el uso de complementos de servidor a servidor. Si sigue estos pasos, podrá crear complementos de gran confianza hospedados por el proveedor con su instalación. Vea  [Crear complementos de SharePoint de elevada confianza](create-high-trust-sharepoint-add-ins.md) para más información sobre la creación de este tipo de complementos.




1. Asegúrese de configurar la aplicación de perfil de usuario y el servicio de administración de aplicaciones. (Se denomina "Servicio de administración de aplicaciones" porque originalmente los Complementos de SharePoint se llamaban "aplicaciones para SharePoint"). Los pasos son estos:

1. En **Administración central**, en **Administración de aplicaciones**, seleccione **Administrar aplicaciones de servicio**.


2. En la página **Aplicaciones de servicio**, asegúrese de que se han iniciado los siguientes servicios:

  - Aplicación de servicio de perfiles de usuario


  - Servicio de administración de aplicaciones


3. En **Administración de aplicaciones**, seleccione **Administrar servicios en el servidor**. 


4. En la página **Servicios del servidor**, asegúrese de que se han iniciado los siguientes servicios:

  - Servicio de perfiles de usuario 


2. Asegúrese de crear al menos un perfil en la **Aplicación de servicio de perfiles de usuario**. Los pasos son estos:

1. En **Administración central**, en **Administración de aplicaciones**, seleccione **Administrar aplicaciones de servicio**.


2. Después seleccione **Aplicación de servicio de perfiles de usuario**.


3. En la página **Servicio Administrar perfil: Aplicación de servicio de perfiles de usuario**, en **Personas**, seleccione **Administrar perfiles de usuario**.


4. En la página **Administrar perfiles de usuario**, seleccione **Nuevos perfiles**.


5. En la página **Agregar perfil de usuario**, escriba el nombre de la cuenta y la dirección de correo.


6. Seleccione **Guardar y cerrar**.

    > **NOTA**
      > Si recibe un mensaje que indica que ya existe el perfil que trata de crear, seleccione **Cancelar y volver atrás**. 
7. Al volver a la página **Administrar perfiles de usuario**, debería ver **Número total de perfiles: 1**.



## Instalar Visual Studio y Office Developer Tools para Visual Studio
<a name="SP15Appdevonprem_bk_installVS"> </a>


- Si todavía no tiene instalado **Visual Studio** 2013 o una versión posterior, instálelo con las instrucciones de [Install Visual Studio](http://msdn.microsoft.com/library/da049020-cfda-40d7-8ff4-7492772b620f.aspx). Se recomienda utilizar la  [versión más reciente del Centro de descargas de Microsoft](https://www.visualstudio.com/downloads/download-visual-studio-vs).


- Visual Studio incluye **Microsoft Office Developer Tools para Visual Studio**, pero a veces se publica una versión de las herramientas entre actualizaciones de Visual Studio. Para asegurarse de que tiene la versión más reciente de las herramientas, ejecute el [programa de instalación de Office Developer Tools para Visual Studio 2013](http://aka.ms/OfficeDevToolsForVS2013), o el  [programa de instalación de Office Developer Tools para Visual Studio 2015](http://aka.ms/OfficeDevToolsForVS2015).



### Registro detallado en Visual Studio

Siga estos pasos si desea activar el registro detallado:




1. Abra el registro y vaya a **HKEY_CURRENT_USER\\Software\\Microsoft\\VisualStudio\\ _nn.n_\\SharePointTools**, donde _nn.n_ es la versión de Visual Studio; por ejemplo, 12.0 o 14.0.


2. Agregue una clave DWORD llamada **EnableDiagnostics**.


3. Asigne el valor **1** a la clave.


La ruta de acceso del Registro cambiará en futuras versiones de Visual Studio.




## Configurar un dominio de complemento aislado en SharePoint
<a name="SP15appdevonprem_bk_configure"> </a>

Lea  [Sitios web host, sitios web de complemento y el dominio aislado](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md#IsolatedDomain) antes de llevar a cabo los procedimientos de esta sección.



Debe crear un dominio aislado en su granja de servidores de SharePoint. Además, su instalación de SharePoint necesita un dominio de encabezado de host de caracteres comodín general donde pueda aprovisionar los complementos hospedados en SharePoint.



Con fines de desarrollo, podrá modificar el archivo de hosts según vaya necesitando enrutar el equipo que use para el desarrollo hacia una instancia de prueba de una Complemento de SharePoint. Visual Studio modifica el archivo de hosts de forma automática cuando compila e implementa el complemento. 




> **NOTA**
> Para las granjas de servidores de producción, debería crear una estrategia de enrutamiento de DNS dentro de la intranet y opcionalmente configurar su firewall. Consulte  [Instalar y administrar complementos de SharePoint](http://technet.microsoft.com/es-es/library/fp161232%28v=office.15%29) para obtener más información sobre cómo crear y configurar un entorno de producción para Complementos de SharePoint.




Siga los pasos del procedimiento a continuación para crear un dominio aislado de complemento.




> **NOTA**
> Es preciso seguir todos los pasos del procedimiento de más abajo habiendo iniciado sesión como administrador de granja y debe ejecutar el símbolo del sistema y la Consola de administración de SharePoint como administrador. 





### Crear un dominio aislado de complemento en el equipo destinado al desarrollo


1. Abra el símbolo del sistema y escriba los comandos siguientes para asegurarse de que los servicios spadmin y sptimer se están ejecutando.

 ```

net start spadminv4
net start sptimerv4
 ```

2. Ejecute la Consola de administración de SharePoint como administrador y escriba el siguiente comando para crear el dominio aislado de complemento. Reemplace  _contosoaddins.com_ con el dominio de complemento. *No*  debería ser un subdominio del dominio de host de SharePoint. Si lo es, invalidaría enormemente las ventajas de seguridad de tener dominios de complemento aislados. Por ejemplo, si el dominio de host es contoso.com, no use addins.contoso.com como dominio de complemento.

 ```

Set-SPAppDomain "contosoaddins.com"
 ```

3. Escriba el siguiente comando en la Consola de administración de SharePoint para asegurarse de que los servicios SPSubscriptionSettingsService y AppManagementServiceInstance se están ejecutando.

 ```
  Get-SPServiceInstance | where{$_.GetType().Name -eq "AppManagementServiceInstance" -or $_.GetType().Name -eq "SPSubscriptionSettingsServiceInstance"} | Start-SPServiceInstance
 ```

4. Escriba el comando siguiente en la Consola de administración de SharePoint para comprobar que los servicios SPSubscriptionSettingsService y AppManagementServiceInstance se están ejecutando. El resultado indicará si cada uno de los servicios está en línea.

 ```
  Get-SPServiceInstance | where{$_.GetType().Name -eq "AppManagementServiceInstance" -or $_.GetType().Name -eq "SPSubscriptionSettingsServiceInstance"}
 ```

5. Debe especificar una cuenta donde se vayan a ejecutar las instancias de los servicios SPSubscriptionService y AppManagementServiceInstance. Esta cuenta debe ser una cuenta SPManagedAccount. Puede crear una cuenta SPManagedAccount si escribe el siguiente comando en la Consola de administración de SharePoint. (Se le pedirá el dominio o usuario y contraseña de la cuenta).

 ```
  $account = New-SPManagedAccount
 ```

6. Especifique la configuración de una cuenta, de un grupo de aplicaciones y de una base de datos para los servicios SPSubscriptionService y AppManagementServiceInstance; para ello, escriba el código siguiente en la Consola de administración de SharePoint. Si ha creado una cuenta SPManagedAccount en el paso anterior, use el nombre de la cuenta aquí.

 ```
  $account = Get-SPManagedAccount "domain\\user"
$appPoolSubSvc = New-SPServiceApplicationPool -Name SettingsServiceAppPool -Account $account
$appPoolAppSvc = New-SPServiceApplicationPool -Name AppServiceAppPool -Account $account
$appSubSvc = New-SPSubscriptionSettingsServiceApplication -ApplicationPool $appPoolSubSvc -Name SettingsServiceApp -DatabaseName SettingsServiceDB 
$proxySubSvc = New-SPSubscriptionSettingsServiceApplicationProxy -ServiceApplication $appSubSvc
$appAppSvc = New-SPAppManagementServiceApplication -ApplicationPool $appPoolAppSvc -Name AppServiceApp -DatabaseName AppServiceDB
$proxyAppSvc = New-SPAppManagementServiceApplicationProxy -ServiceApplication $appAppSvc

 ```

7. Especifique el prefijo de su complemento (consulte  [Sitios web host, sitios web de complemento y el dominio aislado](host-webs-add-in-webs-and-sharepoint-components-in-sharepoint-2013.md#IsolatedDomain)) escribiendo el código siguiente en el Shell de administración de SharePoint.

 ```

Set-SPAppSiteSubscriptionName -Name "add-in" -Confirm:$false
 ```

 **Lleve a cabo el procedimiento siguiente únicamente si su entorno usa un servidor proxy.** Una vez que haya creado el dominio de complemento aislado, siga los pasos del procedimiento a continuación para agregar un dominio a la lista de omisión de Internet Explorer. De esta forma, tendrá la certeza de que podrá visitar este dominio tras implementar un complemento hospedado en SharePoint o un complemento hospedado por el proveedor que incluya una web de complemento.




### Agregar un dominio aislado de complemento a la lista de omisión de Internet Explorer


1. En Internet Explorer, vaya a **Herramientas**.


2. Elija **Opciones de Internet**.


3. En la pestaña **Conexiones**, presione el botón **Configuración de LAN**.


4. Desactive la casilla **Detectar la configuración automáticamente**.


5. Seleccione la casilla **Usar un servidor proxy para la LAN**.


6. Presione el botón **Avanzadas** y luego agregue*.dominiodelcomplemento.com a la lista **Excepciones**.


7. Presione el botón **Aceptar**.


8. Presione el botón **Aceptar** para cerrar el cuadro de diálogo **Configuración de la red de área local (LAN)**.


9. Presione el botón **Aceptar** para cerrar el cuadro de diálogo **Opciones de Internet**.


Consulte  [Implementación e instalación de aplicaciones para SharePoint: métodos y opciones](deploying-and-installing-sharepoint-add-ins-methods-and-options.md) para obtener información sobre las opciones que están a su disposición para desarrollar complementos.




> **SUGERENCIA**
> Después de implementar en la instalación un complemento hospedado en SharePoint, puede que se le pida iniciar sesión con sus credenciales al tratar de iniciarla. Tendrá que deshabilitar la comprobación de bucle invertido para evitar estos mensajes. Vea  [Aparece el error 401.1 cuando explora un sitio web que usa Autenticación integrada y que está hospedado en IIS 5.1 o una versión posterior](http://support.microsoft.com/kb/896861/es-es) para consultar cómo deshabilitar la comprobación de bucle invertido.





## Recursos adicionales
<a name="SP15SetupSPO365_bk_addlresources"> </a>


-  [Complementos de SharePoint](sharepoint-add-ins.md)


-  [Empezar a crear complementos hospedados en proveedor para SharePoint](get-started-creating-provider-hosted-sharepoint-add-ins.md)


-  [Empezar a crear complementos hospedados en SharePoint para SharePoint](get-started-creating-sharepoint-hosted-sharepoint-add-ins.md)







