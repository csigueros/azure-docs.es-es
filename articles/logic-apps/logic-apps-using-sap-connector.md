---
title: Conexión a sistemas SAP
description: Cómo acceder a recursos de SAP y administrarlos mediante la automatización de flujos de trabajo con Azure Logic Apps.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, azla
ms.topic: how-to
ms.date: 09/13/2021
tags: connectors
ms.openlocfilehash: d973276781a1664680a3dec08eb894fc362911a5
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211183"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Conexión a sistemas SAP desde Azure Logic Apps

En este artículo, se explica cómo acceder a los recursos de SAP desde Azure Logic Apps mediante el [conector de SAP](/connectors/sap/).

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta y una suscripción de Azure. Si aún no tiene ninguna suscripción de Azure, [regístrese para obtener una cuenta gratuita de Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un flujo de trabajo de la aplicación lógica desde el que quiere acceder a los recursos de SAP. Si no está familiarizado con Azure Logic Apps, consulte la [información general de Azure de Logic Apps](logic-apps-overview.md) y la [guía de inicio rápido para crear su primer flujo de trabajo de la aplicación lógica en Azure Portal](quickstart-create-first-logic-app-workflow.md).

  * Si ha usado una versión anterior del conector de SAP que ha quedado en desuso, debe [migrar al conector actual](#migrate-to-current-connector) para poder conectarse al servidor de SAP.

  * Si está ejecutando el flujo de trabajo de la aplicación lógica en instancias multiinquilino de Azure, consulte los [requisitos previos de instancias multiinquilino](#multi-tenant-azure-prerequisites).

  * Si está ejecutando el flujo de trabajo de la aplicación lógica en un [entorno del servicio de integración (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) de nivel prémium, consulte los [requisitos previos de ISE](#ise-prerequisites).

* Un [servidor de aplicaciones de SAP](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) o [servidor de mensajes de SAP](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm) para el que quiere obtener acceso desde Azure Logic Apps. Para obtener información sobre los servidores SAP que admiten este conector, consulte [Compatibilidad de SAP](#sap-compatibility).

  > [!IMPORTANT]
  > Asegúrese de configurar el servidor SAP y la cuenta de usuario para permitir el uso de RFC. Para obtener más información, que incluye los tipos de cuenta de usuario admitidos y la autorización mínima necesaria para cada tipo de acción (RFC, BAPI, IDOC), revise la siguiente nota de SAP: [460089: perfiles de autorización mínimos para programas RFC externos](https://launchpad.support.sap.com/#/notes/460089). 
  >
  > * Para las acciones de RFC, la cuenta de usuario necesita además acceso a los módulos de función `RFC_GROUP_SEARCH` y `DD_LANGU_TO_ISOLA`.
  > * Para las acciones de BAPI, la cuenta de usuario también necesita acceso a los siguientes módulos de función: `BAPI_TRANSACTION_COMMIT`, `BAPI_TRANSACTION_ROLLBACK`, `RPY_BOR_TREE_INIT`, `SWO_QUERY_METHODS` y `SWO_QUERY_API_METHODS`.
  > * Para las acciones de IDOC, la cuenta de usuario también necesita acceso a los siguientes módulos de función: `IDOCTYPES_LIST_WITH_MESSAGES`, `IDOCTYPES_FOR_MESTYPE_READ`, `INBOUND_IDOCS_FOR_TID`, `OUTBOUND_IDOCS_FOR_TID`, `GET_STATUS_FROM_IDOCNR` y `IDOC_RECORD_READ`.
  > * Para la acción **Leer tabla**, la cuenta de usuario también necesita acceso a *uno* de los siguientes módulos de función: `RFC BBP_RFC_READ_TABLE` o `RFC_READ_TABLE`.

* Contenido del mensaje que puede enviar al servidor de SAP, como un archivo de IDoc de ejemplo. Este contenido debe estar en formato XML e incluir el espacio de nombres para la [acción de SAP](#actions) que se va a usar. Puede [enviar documentos IDoc con un esquema de archivo plano ajustándolos en un sobre XML](#send-flat-file-idocs).

* Para usar el desencadenador **Cuando se recibe un mensaje de SAP**, también debe realizar estas tareas:

  * Configure los permisos de seguridad de la puerta de enlace de SAP o la lista de control de acceso (ACL). En los archivos **secinfo** y **reginfo**, que están visibles en el cuadro de diálogo Monitor de puerta de enlace (código T SMGW), siga **Goto > Expert Functions > External Security > Maintenance of ACL Files** (Ir a > Funciones expertas > Seguridad externa > Mantenimiento de archivos ACL). Se necesita la siguiente configuración de permisos:

    `P TP=LOGICAPP HOST=<on-premises-gateway-server-IP-address> ACCESS=*`

    Esta línea tiene el siguiente formato:

    `P TP=<trading-partner-identifier-(program-name)-or-*-for-all-partners> HOST=<comma-separated-list-with-external-host-IP-or-network-names-that-can-register-the-program> ACCESS=<*-for-all-permissions-or-a-comma-separated-list-of-permissions>`

    Si no configura los permisos de seguridad de la puerta de enlace de SAP, es posible que reciba este error:

    `Registration of tp Microsoft.PowerBI.EnterpriseGateway from host <host-name> not allowed`

    Para obtener más información, revise [SAP Note 1850230 - GW: "Registration of tp &lt;program ID&gt; not allowed"](https://userapps.support.sap.com/sap/support/knowledge/en/1850230) (Nota de SAP 1850230 - GW: No se permite el registro del identificador de programa de tp).

  * Configure el registro de seguridad de la puerta de enlace de SAP para buscar los problemas de la lista de control de acceso (ACL). Para obtener más información, vea el [tema de ayuda de SAP para configurar el registro de la puerta de enlace](https://help.sap.com/erp_hcm_ias2_2015_02/helpdata/en/48/b2a710ca1c3079e10000000a42189b/frameset.htm).

  * En el cuadro de diálogo **Configuración de conexiones RFC** (código T SM59), cree una conexión RFC con el tipo **TCP/IP**. El **Tipo de activación** debe ser **Programa de servidor registrado**. Establezca el valor de **Tipo de comunicación de la conexión RFC con el sistema de destino** en **Unicode**.

  * Si usa este desencadenador de SAP con el parámetro **Formato de IDOC** establecido en **FlatFile** junto con la [acción Descodificación de archivos planos](logic-apps-enterprise-integration-flatfile.md), tendrá que usar la propiedad `early_terminate_optional_fields` en el esquema de archivo plano estableciendo el valor en `true`.

    Este requisito es necesario porque el registro de datos IDoc de archivo plano que envía SAP en la llamada a tRFC `IDOC_INBOUND_ASYNCHRONOUS` no se agrega a la longitud completa del campo SDATA. Azure Logic Apps proporciona los datos originales de IDoc de archivo plano sin relleno tal y como se reciben de SAP. Además, al combinar este desencadenador de SAP con la acción Descodificación de archivos planos, el esquema que se proporciona a la acción debe coincidir.

  > [!NOTE]
  > Este desencadenador de SAP utiliza la misma ubicación de URI para renovar y cancelar una suscripción de webhook. La operación de renovación usa el método `PATCH` de HTTP, mientras que la operación de cancelación usa el método `DELETE` de HTTP. Este comportamiento puede provocar que una operación de renovación aparezca como una operación de cancelación en el historial del desencadenador, pero la operación sigue siendo una renovación porque el desencadenador usa `PATCH` como método HTTP, no `DELETE`.

### <a name="sap-compatibility"></a>Compatibilidad de SAP

El conector de SAP es compatible con los siguientes tipos de sistemas SAP:

* Sistemas SAP basados en HANA locales y basados en la nube, como S/4 HANA.

* Sistemas SAP clásicos locales, como R/3 y ECC.

El conector de SAP admite los siguientes tipos de integración de datos y mensajes de los sistemas basados en SAP NetWeaver:

* Documento intermedio (IDoc)

* Interfaz de programación de aplicaciones empresariales (BAPI)

* Llamada a función remota (RFC) y RFC transaccional (tRFC)

El conector de SAP emplea la [biblioteca de SAP .NET Connector (NCo)](https://support.sap.com/en/product/connectors/msnet.html).

Para usar el [desencadenador de SAP](#triggers) y las [acciones de SAP](#actions) disponibles, primero debe autenticar la conexión. Puede autenticar la conexión con un nombre de usuario y una contraseña. El conector de SAP también admite [comunicaciones de red seguras de SAP (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true) para la autenticación. SNC puede usarse con el inicio de sesión único (SSO) de SAP NetWeaver o con funcionalidades de seguridad adicionales de productos externos. Si usa SNC, consulte los [requisitos previos de SNC](#snc-prerequisites) y los [requisitos previos de SNC para el conector del ISE](#snc-prerequisites-ise).

### <a name="migrate-to-current-connector"></a>Migración al conector actual

Los conectores anteriores del servidor de aplicaciones de SAP y el servidor de mensajes de SAP están en desuso desde el 29 de febrero de 2020. Para migrar al conector de SAP actual, siga estos pasos:

1. Actualice la [puerta de enlace de datos local](https://www.microsoft.com/download/details.aspx?id=53127) a la versión actual. Para obtener más información, consulte [Instalación de una puerta de enlace de datos local para Azure Logic Apps](logic-apps-gateway-install.md).

1. En el flujo de trabajo de la aplicación lógica que usa el conector de SAP anterior, elimine la acción **Enviar a SAP**.

1. Agregue la acción **Enviar mensaje a SAP** desde el conector de SAP más reciente.

1. Vuelva a conectarse al sistema SAP en la nueva acción.

1. Guarde el flujo de trabajo de la aplicación lógica. En la barra de herramientas del diseñador, seleccione **Save** (Guardar).

## <a name="multi-tenant-azure-prerequisites"></a>Requisitos previos de instancias multiinquilino de Azure

Estos requisitos previos se aplican si el flujo de trabajo de la aplicación lógica se ejecuta en instancias multiinquilino de Azure. El conector de SAP administrado no se ejecuta de forma nativa en un [ISE](connect-virtual-network-vnet-isolated-environment-overview.md).

> [!TIP]
> Si usa un ISE de nivel prémium, puede usar el conector del ISE de SAP en lugar del conector de SAP administrado. Para obtener más información, consulte los [requisitos previos de ISE](#ise-prerequisites).

El conector de SAP administrado se integra con los sistemas SAP locales a través de la [puerta de enlace de datos local](logic-apps-gateway-connection.md). Por ejemplo, en escenarios de envío de mensajes, al enviar un mensaje desde un flujo de trabajo de la aplicación lógica hasta un sistema SAP, la puerta de enlace de datos actúa como cliente RFC y reenvía las solicitudes recibidas del flujo de trabajo de la aplicación lógica a SAP. Del mismo modo, en escenarios de recepción de mensajes, la puerta de enlace de datos actúa como servidor RFC que recibe solicitudes de SAP y las reenvía al flujo de trabajo de la aplicación lógica.

1. [Descargue e instale la puerta de enlace de datos local](logic-apps-gateway-install.md) en un equipo host o máquina virtual que exista en la misma red virtual que el sistema SAP al que se va a conectar.

1. [Cree un recurso de puerta de enlace de Azure](logic-apps-gateway-connection.md#create-azure-gateway-resource) para la puerta de enlace en Azure Portal. Esta puerta de enlace le ayuda a acceder de forma segura a los datos y recursos que se encuentran en el entorno local. Asegúrese de usar una versión compatible de la puerta de enlace.

  > [!TIP]
  > Si tiene problemas con la puerta de enlace, intente [actualizar a la versión más reciente](https://aka.ms/on-premises-data-gateway-installer), que puede incluir actualizaciones para resolver el problema.

1. [Descargue e instale la biblioteca cliente de SAP más actualizada](#sap-client-library-prerequisites) en el mismo equipo local que la puerta de enlace de datos local.

1. Configure los nombres de host de red y la resolución de nombres de servicio para la máquina host donde instaló la puerta de enlace de datos local.

  Si tiene previsto usar nombres de host o nombres de servicio para la conexión desde Azure Logic Apps, debe configurar cada aplicación, mensaje y servidor de puerta de enlace de SAP junto con sus servicios para la resolución de nombres. La resolución de nombres de host de red se configura en el archivo `%windir%\System32\drivers\etc\hosts` o en el servidor DNS que está disponible para el equipo host de puerta de enlace de datos local. La resolución de nombres de servicio se configura en `%windir%\System32\drivers\etc\services`. Si no piensa usar nombres de host de red o nombres de servicio para la conexión, puede usar direcciones IP de host y números de puerto de servicio en su lugar.

   Si no tiene una entrada DNS para el sistema SAP, en el ejemplo siguiente se muestra una entrada de ejemplo para el archivo de hosts:

   ```text
   10.0.1.9           sapserver                   # SAP single-instance system host IP by simple computer name
   10.0.1.9           sapserver.contoso.com       # SAP single-instance system host IP by fully qualified DNS name
   ```

   Un conjunto de entradas de ejemplo para los archivos de servicios es:

   ```text
   sapdp00            3200/tcp              # SAP system instance 00 dialog (application) service port
   sapgw00            3300/tcp              # SAP system instance 00 gateway service port
   sapmsDV6           3601/tcp              # SAP system ID DV6 message service port
   ```

## <a name="ise-prerequisites"></a>Requisitos previos del Entorno del servicio de integración (ISE)

Un ISE proporciona acceso a los recursos que están protegidos por una red virtual de Azure y ofrece otros conectores nativos del ISE que permiten a los flujos de trabajo de aplicaciones lógicas acceder directamente a los recursos locales sin usar la puerta de enlace de datos local.

1. Si aún no tiene una cuenta de Azure Storage y un contenedor de blobs, cree ese contenedor mediante [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) o el [Explorador de Azure Storage](../storage/blobs/quickstart-storage-explorer.md).

1. [Descargue e instale la biblioteca más actualizada de clientes de SAP](#sap-client-library-prerequisites) en el equipo local. Debe tener los siguientes archivos de ensamblado:

   * libicudecnumber.dll

   * rscp4n.dll

   * sapnco.dll

   * sapnco_utils.dll

1. Cree un archivo .zip que incluya estos archivos de ensamblado. Cargue el paquete en el contenedor de blobs en Azure Storage.

1. En Azure Portal o el Explorador de Azure Storage, vaya a la ubicación del contenedor donde cargó el archivo. zip.

1. Copie la dirección URL de la ubicación del contenedor. Asegúrese de incluir el token de firma de acceso compartido (SAS) para autorizarlo. De lo contrario, se producirá un error en la implementación del conector del ISE de SAP.

1. Instale e implemente el conector de SAP en el ISE. Para obtener más información, consulte [Adición de conectores del ISE](add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment).

   1. En [Azure Portal](https://portal.azure.com), busque y abra el ISE.

   1. En el menú del ISE, seleccione **Conectores administrados** &gt; **Agregar**. En la lista de conectores, busque y seleccione **SAP**.

   1. En el panel **Add a new managed connector** (Agregar un nuevo conector administrado), en el cuadro **SAP package** (Paquete de SAP), pegue la dirección URL del archivo .zip que contiene los ensamblados de SAP. Vuelva a asegurarse de incluir el token de SAS.

   1. Haga clic en **Crear** para terminar de crear el conector del ISE.

1. Si la instancia de SAP y el ISE se encuentran en distintas redes virtuales, también debe [poner en el mismo nivel esas redes](../virtual-network/tutorial-connect-virtual-networks-portal.md) para que estén conectadas. Consulte también los [requisitos previos de SNC para el conector del ISE](#snc-prerequisites-ise).

1. Obtenga las direcciones IP de la aplicación, los mensajes y los servidores de puerta de enlace de SAP que planea usar para conectarse desde el flujo de trabajo de la aplicación lógica. La resolución de nombres de red no está disponible para las conexiones SAP en un ISE.

1. Obtenga los números de puerto para la aplicación, los mensajes y los servicios de puerta de enlace de SAP que planea usar para la conexión con la aplicación lógica. La resolución de nombres de servicio no está disponible para el conector de SAP en ISE.

### <a name="sap-client-library-prerequisites"></a>Requisitos previos de la biblioteca de cliente de SAP

En la siguiente lista se describen los requisitos previos para la biblioteca cliente de SAP que se usa con el conector:

* Instale la versión más reciente: [SAP Connector (NCo 3.0) para Microsoft .NET 3.0.24.0 compilado con .NET Framework 4.0  - Windows de 64 bits (x64)](https://support.sap.com/en/product/connectors/msnet.html). Las versiones anteriores de SAP NCo podrían experimentar los siguientes problemas:

  * Cuando se envía más de un mensaje de IDoc al mismo tiempo, esta condición bloquea todos los mensajes posteriores que se envían al destino de SAP, lo que hace que se agote el tiempo de espera de los mensajes.

  * La activación de la sesión podría producir un error debido a una sesión filtrada. Esta condición podría bloquear las llamadas enviadas por SAP al desencadenador de flujo de trabajo de la aplicación lógica.

  * La puerta de enlace de datos local (versión de junio de 2021) depende del método `SAP.Middleware.Connector.RfcConfigParameters.Dispose()` de SAP NCo para liberar recursos.

* Debe instalar la versión de 64 bits para la biblioteca cliente de SAP, ya que la puerta de enlace de datos solo se ejecuta en sistemas de 64 bits. Al instalar la versión de 32 bits no admitida, se produce un error de "imagen incorrecta".

* Copie los archivos de ensamblado de la carpeta de instalación predeterminada a otra ubicación, según el escenario que se indica a continuación.

  * En el caso del flujo de trabajo de la aplicación lógica que se ejecuta en un ISE, siga los [Requisitos previos del Entorno del servicio de integración (ISE)](#ise-prerequisites) en su lugar.

  * En el caso del flujo de trabajo de la aplicación lógica que se ejecuta en instancias multiinquilino de Azure y usa la puerta de enlace de datos local, copie los archivos de ensamblado a la carpeta de instalación de la puerta de enlace de datos. 

    > [!NOTE]
    > Si se produce un error en la conexión de SAP con un mensaje que le pide que **compruebe la información de la cuenta o los permisos y pruebe otra vez**, asegúrese de que se han copiado los archivos de ensamblado en la carpeta de instalación de la puerta de enlace de datos.
    > 
    > Para solucionar otros problemas, use el [visor de registro de enlaces de ensamblados de .NET](/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer). 
    > Esta herramienta le permite comprobar que los archivos de ensamblado se encuentran en la ubicación correcta. 
  
  * Si lo prefiere, seleccione la opción **Global Assembly Cache registration** (Registro de la caché global de ensamblados) al instalar la biblioteca cliente de SAP.

Tenga en cuenta las siguientes relaciones entre la biblioteca cliente de SAP, .NET Framework, el entorno de ejecución de .NET y la puerta de enlace:

* Tanto Microsoft SAP Adapter como el servicio de host de la puerta de enlace usan .NET Framework 4.7.2.

* SAP NCo para .NET Framework 4.0 funciona con procesos que usan las versiones 4.0 a 4.8 del entorno de ejecución de .NET.

* NCo de SAP para .NET Framework 2.0 funciona con procesos que usan las versiones 2.0 a 3.5 del entorno de ejecución de .NET y ya no funciona con la puerta de enlace más reciente.

### <a name="snc-prerequisites"></a>Requisitos previos de SNC

Si usa una puerta de enlace de datos local con SNC opcional, que solo se admite en instancias multiinquilino de Azure, debe configurar estas opciones adicionales. Si usa un ISE, consulte los [requisitos previos de SNC para el conector del ISE](#snc-prerequisites-ise).

Si usa SNC con el inicio de sesión único (SSO), asegúrese de que el servicio de la puerta de enlace de datos se ejecuta con un usuario asignado con el usuario de SAP. Para cambiar la cuenta predeterminada, seleccione **Cambiar cuenta** y escriba las credenciales de usuario.

![Captura de pantalla que muestra Azure Portal con la configuración de puerta de enlace de datos local y la página de configuración del servicio con el botón para cambiar la cuenta de servicio de puerta de enlace seleccionada.](./media/logic-apps-using-sap-connector/gateway-account.png)

Si habilita SNC a través de un producto de seguridad externo, copie la biblioteca o los archivos de SNC en el mismo equipo donde está instalada la puerta de enlace de datos. Algunos ejemplos de productos SNC incluyen [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos y NTLM. Para obtener más información sobre la habilitación de SNC para la puerta de enlace de datos, consulte [Habilitación de las comunicaciones de red seguras (SNC)](#enable-secure-network-communications).

> [!TIP]
> La versión de la biblioteca SNC y sus dependencias deben ser compatibles con el entorno de SAP.
>
> * Debe usar `sapgenpse.exe` específicamente como utilidad SAPGENPSE.
> * Si usa una puerta de enlace de datos local, copie también estos mismos archivos binarios en la carpeta de instalación.
> * Si se proporciona PSE en la conexión, no es necesario copiar ni configurar PSE y SECUDIR para la puerta de enlace de datos local.
> * También puede usar la puerta de enlace de datos local para solucionar los problemas de compatibilidad de la biblioteca.

#### <a name="snc-prerequisites-ise"></a>Requisitos previos de SNC (ISE)

La versión ISE del conector de SAP admite SNC X.509. Puede habilitar SNC para las conexiones de SAP ISE con los pasos siguientes:

> [!IMPORTANT]
> Antes de volver a implementar un conector de SAP existente para usar SNC, debe eliminar todas las conexiones al conector anterior. Varios flujos de trabajo de aplicaciones lógicas pueden usar la misma conexión a SAP. Por lo tanto, debe eliminar todas las conexiones SAP de todos los flujos de trabajo de aplicaciones lógicas en el ISE. A continuación, debe eliminar el conector anterior.
>
> Al eliminar un conector antiguo, todavía puede mantener los flujos de trabajo de aplicaciones lógicas que usan este conector. Después de volver a implementar el conector, puede autenticar la nueva conexión en los desencadenadores y acciones de SAP en estos flujos de trabajo de aplicaciones lógicas.

En primer lugar, si ya ha implementado el conector de SAP sin las bibliotecas SNC o SAPGENPSE, elimine todas las conexiones y el conector.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Elimine todas las conexiones al conector de SAP de los flujos de trabajo de aplicaciones lógicas.

   1. Abra su recurso de aplicación lógica en Azure Portal.

   1. En el menú de la aplicación lógica, en **Herramientas de desarrollo**, seleccione **Conexiones de API**.

   1. En la página **Conexiones de API**, seleccione la conexión de SAP.

   1. En el menú de la página de la conexión, seleccione **Eliminar**.

   1. Acepte el mensaje de confirmación para eliminar la conexión.

   1. Espere la notificación del portal de que se ha eliminado la conexión.

1. O bien, elimine las conexiones al conector de SAP desde las conexiones de API del ISE.

   1. Abra el recurso del ISE en Azure Portal.

   1. En el menú de ISE, en **Configuración**, seleccione **Conexiones de API**.

   1. En la página **Conexiones de API**, seleccione la conexión de SAP.

   1. En el menú de la página de la conexión, seleccione **Eliminar**.

   1. Acepte el mensaje de confirmación para eliminar la conexión.

   1. Espere la notificación del portal de que se ha eliminado la conexión.

Después, elimine el conector de SAP del ISE. Debe eliminar todas las conexiones a este conector en todas las aplicaciones lógicas para poder eliminar el conector. Si aún no ha eliminado todas las conexiones, revise el conjunto de pasos anterior.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Abra de nuevo el recurso del ISE en Azure Portal.

1. En el menú de ISE, en **Configuración**, seleccione **Conectores administrados**.

1. En la página **Conectores administrados**, active la casilla del conector de SAP.

1. En la barra de herramientas, seleccione **Eliminar**.

1. Acepte el mensaje de confirmación para eliminar el conector.

1. Espere la notificación del portal de que se ha eliminado el conector.

Después, implemente o vuelva a implementar el conector de SAP en el ISE:

1. Prepare un nuevo archivo zip para usarlo en la implementación del conector de SAP. Debe incluir la biblioteca SNC y la utilidad SAPGENPSE.

   1. Copie todas las bibliotecas SNC, SAPGENPSE y NCo en la carpeta raíz del archivo ZIP. No coloque estos archivos binarios en subcarpetas.

   1. Debe usar la biblioteca de SNC de 64 bits. No hay compatibilidad de 32 bits.

   1. La biblioteca de SNC y sus dependencias deben ser compatibles con el entorno de SAP. Para comprobar la compatibilidad, consulte los [requisitos previos del ISE](#ise-prerequisites).

1. Siga los pasos de implementación de los [requisitos previos del ISE](#ise-prerequisites) con el nuevo archivo ZIP.

Por último, cree nuevas conexiones que usen SNC en todas las aplicaciones lógicas que utilizan el conector de SAP. Para cada conexión, siga estos pasos:

1. Vuelva a abrir el flujo de trabajo en el diseñador de flujo de trabajo.

1. Cree o edite un paso que use el conector de SAP.

1. Escriba la información necesaria sobre la conexión de SAP.

   :::image type="content" source=".\media\logic-apps-using-sap-connector\ise-connector-settings.png" alt-text="Captura de pantalla del diseñador de flujo de trabajo, en la que se muestra la configuración de conexión de SAP." lightbox=".\media\logic-apps-using-sap-connector\ise-connector-settings.png":::

   > [!NOTE]
   > Los campos **Nombre de usuario de SAP Username** y **Contraseña de SAP** son opcionales. Si no proporciona un nombre de usuario y una contraseña, el conector usa el certificado de cliente proporcionado en un paso posterior para la autenticación.

1. Habilite SNC.

   1. En **Usar SNC**, active la casilla.

   1. En **Biblioteca de SNC**, escriba el nombre de la biblioteca de SNC. Por ejemplo, `sapcrypto.dll`.

   1. En **Nombre de asociado de SNC**, escriba el nombre de SNC del back-end. Por ejemplo, `p:CN=DV3, OU=LA, O=MS, C=US`.

   1. En **Certificado SNC** escriba el certificado público del cliente SNC en formato codificado en base64. No incluya el encabezado o pie de página PEM.

   1. Opcionalmente, escriba la configuración de SNC para **Mi nombre de SNC** y **Calidad de protección de SNC** según sea necesario.

   :::image type="content" source=".\media\logic-apps-using-sap-connector\ise-connector-settings-snc.png" alt-text="Captura de pantalla que muestra el diseñador de flujo de trabajo y las opciones de configuración de SNC para una nueva conexión SAP." lightbox=".\media\logic-apps-using-sap-connector\ise-connector-settings-snc.png":::

1. Configure las opciones de PSE. En **PSE**, escriba el PSE de SNC como binario codificado en base64.

   * El PSE debe contener el certificado de cliente privado, cuya huella digital coincide con el certificado de cliente público que proporcionó en el paso anterior.

   * El PSE puede contener certificados de cliente adicionales.

   * El PSE no debe tener ningún PIN. Si es necesario, establezca el PIN en vacío mediante la utilidad SAPGENPSE.

   Para la rotación de certificados, siga estos pasos:

   1. Actualice el PSE binario codificado en base64 para todas las conexiones que usan SAP ISE X.509 en el ISE.

   1. Importe los nuevos certificados en la copia del PSE.

   1. Codificación del archivo PSE como binario codificado en base64.

   1. Edite la conexión de API para el conector de SAP y guarde el nuevo archivo PSE allí.

   El conector detecta el cambio de PSE y actualiza su propia copia durante la siguiente solicitud de conexión.

   > [!NOTE]
   > Si usa más de un certificado de cliente SNC para el ISE, debe proporcionar el mismo PSE para todas las conexiones. Puede establecer el parámetro de certificado público de cliente para especificar el certificado para cada conexión usada en el ISE.

1. Seleccione **Crear** para crear su conexión. Si los parámetros son correctos, se crea la conexión. Si hay un problema con los parámetros, el cuadro de diálogo de creación de la conexión muestra un mensaje de error.

   > [!TIP]
   > Para solucionar problemas de parámetros de conexión, puede usar una puerta de enlace de datos local y los registros locales de la puerta de enlace.

1. En la barra de herramientas del diseñador del flujo de trabajo, seleccione **Guardar** para guardar los cambios.

## <a name="send-idoc-messages-to-sap-server"></a>Envío de mensajes de IDoc al servidor de SAP

Siga estos ejemplos para crear un flujo de trabajo de la aplicación lógica que envíe un mensaje de IDoc a un servidor de SAP y devuelva una respuesta:

1. [Cree un flujo de trabajo de aplicación lógica desencadenado por una solicitud HTTP.](#add-http-request-trigger)

1. [Cree una acción en el flujo de trabajo para enviar un mensaje a SAP.](#create-sap-action-to-send-message)

1. [Cree una acción de respuesta HTTP en el flujo de trabajo.](#create-http-response-action)

1. [Cree un patrón de solicitud-respuesta de llamada de función remota (RFC) si usa un RFC para recibir respuestas de SAP ABAP.](#create-rfc-request-response)

1. [Pruebe la aplicación lógica.](#test-your-logic-app-workflow)

<a name="add-http-request-trigger"></a>

### <a name="add-an-http-request-trigger"></a>Adición de un desencadenador de solicitud HTTP

Para que el flujo de trabajo de la aplicación lógica reciba IDoc de SAP a través de HTTP XML, puede usar el [desencadenador de solicitud](../connectors/connectors-native-reqres.md).

> [!TIP]
> Para recibir IDoc a través de Common Programming Interface Communication (CPIC) como XML sin formato o como un archivo plano, consulte la sección [Recibir un mensaje de SAP](#receive-message-sap).

Esta sección continúa con el desencadenador de solicitud, por lo que primero debe crear un flujo de trabajo de aplicación lógica con un punto de conexión en Azure para enviar solicitudes *HTTP POST* al flujo de trabajo. Cuando el flujo de trabajo de la aplicación lógica reciba estas solicitudes HTTP, el desencadenador se activará y ejecutará el paso siguiente en el flujo de trabajo.

1. En [Azure Portal](https://portal.azure.com), cree una aplicación lógica en blanco que abra el diseñador de flujo de trabajo.

1. En el cuadro de búsqueda, escriba `http request` como filtro. En la lista **Desencadenadores**, seleccione **Cuando se recibe una solicitud HTTP**.

   ![Captura de pantalla que muestra el diseñador de flujo de trabajo con un nuevo desencadenador de solicitud que se agrega al flujo de trabajo de la aplicación lógica.](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Guarde el flujo de trabajo de la aplicación lógica, que genera una dirección URL del punto de conexión que puede recibir solicitudes. En la barra de herramientas del diseñador, seleccione **Save** (Guardar). La dirección URL del punto de conexión aparece ahora en el desencadenador.

   ![Captura de pantalla que muestra el diseñador de flujo de trabajo con el desencadenador de solicitud con la dirección URL POST generada que se va a copiar.](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="create-sap-action-to-send-message"></a>Creación de una acción de SAP para enviar el mensaje

A continuación, cree una acción para enviar el mensaje de IDoc a SAP cuando se active el [desencadenador de solicitud](#add-http-request-trigger). De forma predeterminada, se usa el establecimiento inflexible de tipos para comprobar si hay valores no válidos, para lo cual se realiza la validación de XML con respecto al esquema. Este comportamiento puede ayudarlo a detectar problemas con antelación. La opción **Escritura segura** está disponible para la compatibilidad con versiones anteriores y solo comprueba la longitud de cadena. Más información sobre la opción [Escritura segura](#safe-typing).

1. En el diseñador de flujo de trabajo, en el desencadenador, seleccione **Nuevo paso**.

   ![Captura de pantalla que muestra el diseñador de flujo de trabajo con el flujo de trabajo que se está editando para agregar un nuevo paso.](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. En el cuadro de búsqueda, escriba `send message sap` como filtro. En la lista **Actions** (Acciones), seleccione **Send message to SAP** (Enviar mensajes a SAP).

   ![Captura de pantalla que muestra el diseñador de flujo de trabajo con la acción "Enviar mensaje a SAP" seleccionada.](./media/logic-apps-using-sap-connector/select-sap-send-action.png)

   O bien, puede elegir la pestaña **Enterprise** y seleccionar la acción de SAP.

   ![Captura de pantalla que muestra el diseñador de flujo de trabajo con la acción "Enviar mensaje a SAP" seleccionada en la pestaña Enterprise (Empresa).](./media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Si la conexión ya existe, continúe con el siguiente paso. Si se le pide que cree una nueva conexión, proporcione la siguiente información para conectarse al servidor de SAP local.

   1. Proporcione un nombre para la conexión.

   1. Si usa la puerta de enlace de datos, siga estos pasos:

      1. En la sección **Puerta de enlace de datos**, en **Suscripción**, primero seleccione la suscripción de Azure para el recurso de puerta de enlace de datos que creó en el Azure Portal para la instalación de su puerta de enlace de datos.

      1. En **Puerta de enlace de conexión**, seleccione su recurso de puerta de enlace de datos en Azure.

   1. Para la propiedad **Tipo de inicio de sesión**, siga el paso en función de si la propiedad está establecida en **Servidor de aplicaciones** o **Grupo**.

      * Para **Servidos de aplicaciones**, estas propiedades, que generalmente parecen opcionales, son necesarias:

        ![Captura de pantalla que muestra cómo crear una conexión de servidor de aplicaciones de SAP.](./media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Para **Grupo**, estas propiedades, que generalmente parecen opcionales, son necesarias:

        ![Captura de pantalla que muestra cómo crear una conexión de servidor de mensajes de SAP.](./media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

        En SAP, el grupo de inicio de sesión se mantiene abriendo el cuadro de diálogo **CCMS: Mantener grupos de inicio de sesión** (código T SMLG). Para obtener más información, consulte [SAP Note 26317 - Set up for LOGON group for automatic load balancing](https://service.sap.com/sap/support/notes/26317) (Nota de SAP 26317: configuración del grupo LOGON para el equilibrio de carga automático).

      De forma predeterminada, se usa el establecimiento inflexible de tipos para comprobar si hay valores no válidos, para lo cual se realiza la validación de XML con respecto al esquema. Este comportamiento puede ayudarlo a detectar problemas con antelación. La opción **Escritura segura** está disponible para la compatibilidad con versiones anteriores y solo comprueba la longitud de cadena. Más información sobre la opción [Escritura segura](#safe-typing).

   1. Cuando haya finalizado, seleccione **Crear**.

      Azure Logic Apps configura y comprueba la conexión para asegurarse de que funciona correctamente.

      > [!NOTE]
      > Si recibe el siguiente error, hay un problema con la instalación de la biblioteca cliente de SAP NCo: 
      >
      > **Error en la prueba de conexión. Error: "Error al procesar la solicitud". Detalles del error: "No se puede cargar el archivo o ensamblado "sapnco, Version=3.0.0.42, Culture=neutral, PublicKeyToken 50436dca5c7f7d23" o una de sus dependencias. El sistema no encuentra el archivo especificado".**
      >
      > Asegúrese de [instalar la versión necesaria de la biblioteca cliente de SAP NCo y cumplir todos los demás requisitos previos](#sap-client-library-prerequisites).

1. Ahora, busque y seleccione una acción en el servidor SAP.

   1. En el cuadro **SAP Action** (Acción de SAP), elija el icono de carpeta. Desde la lista de archivos, busque y seleccione el mensaje de SAP que quiere utilizar. Para navegar por la lista, utilice las flechas.

      En este ejemplo se selecciona un IDOC con el tipo **Orders** (Pedidos).

      ![Captura de pantalla que muestra cómo buscar y seleccionar una acción de IDoc.](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Si no encuentra la acción que quiere, puede escribir manualmente una ruta de acceso, por ejemplo:

      ![Captura de pantalla que muestra cómo proporcionar manualmente una ruta de acceso a una acción de IDoc.](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Proporcione el valor de la **acción de SAP** mediante el editor de expresiones. De este modo, puede usar la misma acción para diferentes tipos de mensajes.

      Para obtener más información sobre las operaciones de IDoc, consulte [Esquemas de mensaje para operaciones de IDoc](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Haga clic en el cuadro **Mensaje de entrada** para que aparezca la lista de contenido dinámico. En dicha lista, en **When a HTTP request is received** (Cuando se recibe una solicitud HTTP), seleccione el campo **Cuerpo**.

      En este paso, se incluye el contenido del cuerpo del desencadenador de la solicitud y se envía la salida al servidor SAP.

      ![Captura de pantalla que muestra cómo seleccionar la propiedad "Cuerpo" en el desencadenador de solicitud.](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Cuando haya terminado, la acción SAP debe parecerse a este ejemplo:

      ![Captura de pantalla que muestra cómo completar la acción de SAP.](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Guarde el flujo de trabajo de la aplicación lógica. En la barra de herramientas del diseñador, seleccione **Save** (Guardar).

### <a name="send-flat-file-idocs"></a>Envío de IDoc de archivos planos

Puede usar documentos IDoc con un esquema de archivo plano ajustándolos en un sobre XML. Para enviar un IDoc de archivo plano, use las instrucciones genéricas para [crear una acción de SAP para enviar el mensaje de IDoc](#create-sap-action-to-send-message) con los siguientes cambios.

1. Para la acción **Enviar mensaje a SAP**, use el URI de acción de SAP `http://Microsoft.LobServices.Sap/2007/03/Idoc/SendIdoc`.

1. Dé formato a un mensaje de entrada con un sobre XML.

Por ejemplo, consulte la siguiente carga XML de ejemplo:

```xml
<SendIdoc xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/">
<idocData>EDI_DC 3000000001017945375750 30INVOIC011BTSVLINV30KUABCABCFPPC LDCA X004010810 4 SAPMSX LSEDI ABCABCFPPC 000d3ae4-723e-1edb-9ca4-cc017365c9fd 20210217054521INVOICINVOIC01ZINVOIC2RE 20210217054520
E2EDK010013000000001017945375000001E2EDK01001000000010 ABCABC1.00000 0060 INVO9988298128 298.000 298.000 LB Z4LR EN 0005065828 L
E2EDKA1 3000000001017945375000002E2EDKA1 000000020 RS ABCABCFPPC 0005065828 ABCABCABC ABCABC Inc. Limited Risk Distributor ABCABC 1950 ABCABCABCA Blvd ABCABAABCAB L5N8L9 CA ABCABC E ON V-ABCABC LDCA
E2EDKA1 3000000001017945375000003E2EDKA1 000000020 AG 0005065828 ABCABCFPPC ABCABC ABCABC ABCABC - FPP ONLY 88 ABCABC Crescent ABCABAABCAB L5R 4A2 CA ABCABC 111 111 1111 E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000004E2EDKA1 000000020 RE 0005065828 ABCABCFPPC ABCABC ABCABC ABCABC - FPP ONLY 88 ABCABC Crescent ABCABAABCAB L5R 4A2 CA ABCABC 111 111 1111 E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000005E2EDKA1 000000020 RG 0005065828 ABCABCFPPC ABCABC ABCABC ABCABC - FPP ONLY 88 ABCABC Crescent ABCABAABCAB L5R 4A2 CA ABCABC 111 111 1111 E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000006E2EDKA1 000000020 WE 0005001847 41 ABCABC ABCABC INC (ABCABC) DC A. ABCABCAB 88 ABCABC CRESCENT ABCABAABCAB L5R 4A2 CA ABCABC 111-111-1111 E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000007E2EDKA1 000000020 Z3 0005533050 ABCABCABC ABCABC Inc. ABCA Bank Swift Code -ABCABCABCAB Sort Code - 1950 ABCABCABCA Blvd. Acc No -1111111111 ABCABAABCAB L5N8L9 CA ABCABC E ON ABCABCFPPC EN
E2EDKA1 3000000001017945375000008E2EDKA1 000000020 BK 1075 ABCABCABC ABCABC Inc 1950 ABCABCABCA Blvd ABCABAABCAB ON L5N 8L9 CA ABCABC (111) 111-1111 (111) 111-1111 ON
E2EDKA1 3000000001017945375000009E2EDKA1 000000020 CR 1075 CONTACT ABCABCABC 1950 ABCABCABCA Blvd ABCABAABCAB ON L5N 8L9 CA ABCABC (111) 111-1111 (111) 111-1111 ON
E2EDK02 3000000001017945375000010E2EDK02 000000020 0099988298128 20210217
E2EDK02 3000000001017945375000011E2EDK02 000000020 00140-N6260-S 20210205
E2EDK02 3000000001017945375000012E2EDK02 000000020 0026336270425 20210217
E2EDK02 3000000001017945375000013E2EDK02 000000020 0128026580537 20210224
E2EDK02 3000000001017945375000014E2EDK02 000000020 01740-N6260-S
E2EDK02 3000000001017945375000015E2EDK02 000000020 900IAC
E2EDK02 3000000001017945375000016E2EDK02 000000020 901ZSH
E2EDK02 3000000001017945375000017E2EDK02 000000020 9078026580537 20210217
E2EDK03 3000000001017945375000018E2EDK03 000000020 02620210217
E2EDK03 3000000001017945375000019E2EDK03 000000020 00120210224
E2EDK03 3000000001017945375000020E2EDK03 000000020 02220210205
E2EDK03 3000000001017945375000021E2EDK03 000000020 01220210217
E2EDK03 3000000001017945375000022E2EDK03 000000020 01120210217
E2EDK03 3000000001017945375000023E2EDK03 000000020 02420210217
E2EDK03 3000000001017945375000024E2EDK03 000000020 02820210418
E2EDK03 3000000001017945375000025E2EDK03 000000020 04820210217
E2EDK17 3000000001017945375000026E2EDK17 000000020 001DDPDelivered Duty Paid
E2EDK17 3000000001017945375000027E2EDK17 000000020 002DDPdestination
E2EDK18 3000000001017945375000028E2EDK18 000000020 00160 0 Up to 04/18/2021 without deduction
E2EDK28 3000000001017945375000029E2EDK28 000000020 CA BOFACATT Bank of ABCABAB ABCABC ABCABAB 50127217 ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000030E2EDK28 000000020 CA 026000082 ABCAbank ABCABC ABCABAB 201456700OLD ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000031E2EDK28 000000020 GB ABCAGB2L ABCAbank N.A ABCABA E14, 5LB GB63ABCA18500803115593 ABCABCABC ABCABC Inc. GB63ABCA18500803115593
E2EDK28 3000000001017945375000032E2EDK28 000000020 CA 020012328 ABCABANK ABCABC ABCABAB ON M5J 2M3 2014567007 ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000033E2EDK28 000000020 CA 03722010 ABCABABC ABCABABC Bank of Commerce ABCABAABCAB 64-04812 ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000034E2EDK28 000000020 IE IHCC In-House Cash Center IHCC1075 ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000035E2EDK28 000000020 CA 000300002 ABCAB Bank of ABCABC ABCABAB 0021520584OLD ABCABCABC ABCABC Inc.
E2EDK28 3000000001017945375000036E2EDK28 000000020 US USCC US Cash Center (IHC) city USCC1075 ABCABCABC ABCABC Inc.
E2EDK29 3000000001017945375000037E2EDK29 000000020 0064848944US A CAD CA ABCABC CA United States US CA A Air Air
E2EDKT1 3000000001017945375000038E2EDKT1 000000020 ZJ32E EN
E2EDKT2 3000000001017945375000039E2EDKT2 000038030 GST/HST877845941RT0001 *
E2EDKT2 3000000001017945375000040E2EDKT2 000038030 QST1021036966TQ0001 *
E2EDKT1 3000000001017945375000041E2EDKT1 000000020 Z4VL
E2EDKT2 3000000001017945375000042E2EDKT2 000041030 0.000 *
E2EDKT1 3000000001017945375000043E2EDKT1 000000020 Z4VH
E2EDKT2 3000000001017945375000044E2EDKT2 000043030 *
E2EDK14 3000000001017945375000045E2EDK14 000000020 008LDCA
E2EDK14 3000000001017945375000046E2EDK14 000000020 00710
E2EDK14 3000000001017945375000047E2EDK14 000000020 00610
E2EDK14 3000000001017945375000048E2EDK14 000000020 015Z4F2
E2EDK14 3000000001017945375000049E2EDK14 000000020 0031075
E2EDK14 3000000001017945375000050E2EDK14 000000020 021M
E2EDK14 3000000001017945375000051E2EDK14 000000020 0161075
E2EDK14 3000000001017945375000052E2EDK14 000000020 962M
E2EDP010013000000001017945375000053E2EDP01001000000020 000011 2980.000 EA 298.000 LB MOUSE 298.000 Z4TN 4260
E2EDP02 3000000001017945375000054E2EDP02 000053030 00140-N6260-S 00000120210205 DFUE
E2EDP02 3000000001017945375000055E2EDP02 000053030 0026336270425 00001120210217
E2EDP02 3000000001017945375000056E2EDP02 000053030 0168026580537 00001020210224
E2EDP02 3000000001017945375000057E2EDP02 000053030 9100000 00000120210205 DFUE
E2EDP02 3000000001017945375000058E2EDP02 000053030 911A 00000120210205 DFUE
E2EDP02 3000000001017945375000059E2EDP02 000053030 912PP 00000120210205 DFUE
E2EDP02 3000000001017945375000060E2EDP02 000053030 91300 00000120210205 DFUE
E2EDP02 3000000001017945375000061E2EDP02 000053030 914CONTACT ABCABCABC 00000120210205 DFUE
E2EDP02 3000000001017945375000062E2EDP02 000053030 963 00000120210205 DFUE
E2EDP02 3000000001017945375000063E2EDP02 000053030 965 00000120210205 DFUE
E2EDP02 3000000001017945375000064E2EDP02 000053030 9666336270425 00000120210205 DFUE
E2EDP02 3000000001017945375000065E2EDP02 000053030 9078026580537 00001020210205 DFUE
E2EDP03 3000000001017945375000066E2EDP03 000053030 02920210217
E2EDP03 3000000001017945375000067E2EDP03 000053030 00120210224
E2EDP03 3000000001017945375000068E2EDP03 000053030 01120210217
E2EDP03 3000000001017945375000069E2EDP03 000053030 02520210217
E2EDP03 3000000001017945375000070E2EDP03 000053030 02720210217
E2EDP03 3000000001017945375000071E2EDP03 000053030 02320210217
E2EDP03 3000000001017945375000072E2EDP03 000053030 02220210205
E2EDP19 3000000001017945375000073E2EDP19 000053030 001418VVZ
E2EDP19 3000000001017945375000074E2EDP19 000053030 002RJR-00001 AB ABCABCABC Mouse FORBUS BLUETOOTH
E2EDP19 3000000001017945375000075E2EDP19 000053030 0078471609000
E2EDP19 3000000001017945375000076E2EDP19 000053030 003889842532685
E2EDP19 3000000001017945375000077E2EDP19 000053030 011CN
E2EDP26 3000000001017945375000078E2EDP26 000053030 00459064.20
E2EDP26 3000000001017945375000079E2EDP26 000053030 00352269.20
E2EDP26 3000000001017945375000080E2EDP26 000053030 01052269.20
E2EDP26 3000000001017945375000081E2EDP26 000053030 01152269.20
E2EDP26 3000000001017945375000082E2EDP26 000053030 0126795.00
E2EDP26 3000000001017945375000083E2EDP26 000053030 01552269.20
E2EDP26 3000000001017945375000084E2EDP26 000053030 00117.54
E2EDP26 3000000001017945375000085E2EDP26 000053030 00252269.20
E2EDP26 3000000001017945375000086E2EDP26 000053030 940 2980.000
E2EDP26 3000000001017945375000087E2EDP26 000053030 939 2980.000
E2EDP05 3000000001017945375000088E2EDP05 000053030 + Z400MS List Price 52269.20 17.54 1 EA CAD 2980
E2EDP05 3000000001017945375000089E2EDP05 000053030 + XR1 Tax Jur Code Level 6795.00 13.000 52269.20
E2EDP05 3000000001017945375000090E2EDP05 000053030 + Tax Subtotal1 6795.00 2.28 1 EA CAD 2980
E2EDP05 3000000001017945375000091E2EDP05 000053030 + Taxable Amount + TaxSubtotal1 59064.20 19.82 1 EA CAD 2980
E2EDP04 3000000001017945375000092E2EDP04 000053030 CX 13.000 6795.00 7000000000
E2EDP04 3000000001017945375000093E2EDP04 000053030 CX 0 0 7001500000
E2EDP04 3000000001017945375000094E2EDP04 000053030 CX 0 0 7001505690
E2EDP28 3000000001017945375000095E2EDP28 000053030 00648489440000108471609000 CN CN ABCAB ZZ 298.000 298.000 LB US 400 United Stat KY
E2EDPT1 3000000001017945375000096E2EDPT1 000053030 0001E EN
E2EDPT2 3000000001017945375000097E2EDPT2 000096040 AB ABCABCABC Mouse forBus Bluetooth EN/XC/XD/XX Hdwr Black For Bsnss *
E2EDS01 3000000001017945375000098E2EDS01 000000020 0011
E2EDS01 3000000001017945375000099E2EDS01 000000020 01259064.20 CAD
E2EDS01 3000000001017945375000100E2EDS01 000000020 0056795.00 CAD
E2EDS01 3000000001017945375000101E2EDS01 000000020 01159064.20 CAD
E2EDS01 3000000001017945375000102E2EDS01 000000020 01052269.20 CAD
E2EDS01 3000000001017945375000103E2EDS01 000000020 94200000 CAD
E2EDS01 3000000001017945375000104E2EDS01 000000020 9440.00 CAD
E2EDS01 3000000001017945375000105E2EDS01 000000020 9450.00 CAD
E2EDS01 3000000001017945375000106E2EDS01 000000020 94659064.20 CAD
E2EDS01 3000000001017945375000107E2EDS01 000000020 94752269.20 CAD
E2EDS01 3000000001017945375000108E2EDS01 000000020 EXT
Z2XSK010003000000001017945375000109Z2XSK01000000108030 Z400 52269.20
Z2XSK010003000000001017945375000110Z2XSK01000000108030 XR1 13.000 6795.00 CX
</idocData>
</SendIdoc>
```

### <a name="create-http-response-action"></a>Creación de una acción de respuesta HTTP

Ahora, agregue una acción de respuesta al flujo de trabajo de la aplicación lógica e incluya el resultado de la acción SAP. De este modo, el flujo de trabajo de la aplicación lógica devuelve los resultados del servidor SAP al solicitante original.

1. En el diseñador de flujo de trabajo, en la acción de SAP, seleccione **Nuevo paso**.

1. En el cuadro de búsqueda, escriba `response` como filtro. En la lista **Actions** (Acciones), seleccione **Response** (Respuesta).

1. Haga clic en el cuadro **Cuerpo** para que aparezca la lista de contenido dinámico. En dicha lista, en **Send message to SAP** (Enviar mensaje a SAP), seleccione el campo **Body** (Cuerpo).

   ![Captura de pantalla que muestra cómo finalizar la acción de SAP.](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Guarde el flujo de trabajo de la aplicación lógica.

### <a name="create-rfc-request-response"></a>Creación de una solicitud-respuesta de RFC

Debe crear un patrón de solicitud y respuesta si necesita recibir respuestas mediante una llamada de función remota (RFC) a Azure Logic Apps de ABAP para SAP. Para recibir IDoc en el flujo de trabajo de la aplicación lógica, debe hacer que la primera acción del flujo de trabajo sea una [acción de respuesta](../connectors/connectors-native-reqres.md#add-a-response-action) con un código de estado de `200 OK` y ningún contenido. Este paso recomendado completa la transferencia asincrónica de la Unidad lógica de trabajo (LUW) para SAP a través de tRFC inmediatamente, lo que deja la conversación de CPIC para SAP de nuevo disponible. Después, puede agregar otras acciones en el flujo de trabajo de la aplicación lógica para procesar el IDoc recibido sin bloquear más transferencias adicionales.

> [!NOTE]
> El desencadenador de SAP recibe IDoc a través de tRFC, que no tiene un parámetro de respuesta por diseño.

Para implementar un patrón de solicitud y respuesta, primero debe detectar el esquema RFC mediante el [comando `generate schema`](#generate-schemas-for-artifacts-in-sap). El esquema generado tiene dos posibles nodos raíz: 

* El nodo de solicitud, que es la llamada que recibe de SAP.
* El nodo de respuesta, que es la respuesta a SAP.

En el ejemplo siguiente, se genera un patrón de solicitud y respuesta desde el módulo RFC `STFC_CONNECTION`. Se analiza el código XML de la solicitud para extraer un valor de nodo en el que SAP solicita `<ECHOTEXT>`. La respuesta inserta la marca de tiempo actual como un valor dinámico. Se recibirá una respuesta similar cuando envíe un RFC `STFC_CONNECTION` desde un flujo de trabajo de la aplicación lógica a SAP.

```xml
<STFC_CONNECTIONResponse xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <ECHOTEXT>@{first(xpath(xml(triggerBody()?['Content']), '/*[local-name()="STFC_CONNECTION"]/*[local-name()="REQUTEXT"]/text()'))}</ECHOTEXT>
  <RESPTEXT>Azure Logic Apps @{utcNow()}</RESPTEXT>
```

### <a name="test-your-logic-app-workflow"></a>Comprobación del flujo de trabajo de la aplicación lógica

1. Si la aplicación lógica no está aún habilitada, en el menú de la aplicación lógica, elija **Información general**. En la barra de herramientas, seleccione **Enable** (Habilitar).

1. En la barra de herramientas del diseñador, seleccione **Run** (Ejecutar). Con este paso, se inicia manualmente el flujo de trabajo de la aplicación lógica.

1. Desencadene el flujo de trabajo de la aplicación lógica mediante el envío de una solicitud HTTP POST a la dirección URL del desencadenador de la solicitud. Incluya el contenido del mensaje con la solicitud. Para enviar la solicitud, puede usar una herramienta como [Postman](https://www.getpostman.com/apps).

   En el caso de este artículo, la solicitud envía un archivo de IDoc, que debe estar en formato XML e incluir el espacio de nombres de la acción de SAP que está utilizando, por ejemplo:

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
   <idocData>
   <...>
   </idocData>
   </Send>
   ```

1. Después de enviar la solicitud HTTP, espere la respuesta del flujo de trabajo de la aplicación lógica.

   > [!NOTE]
   > El flujo de trabajo de la aplicación lógica puede agotar el tiempo de espera si todos los pasos necesarios para la respuesta no finalizan dentro del [límite de tiempo de espera de la solicitud](logic-apps-limits-and-config.md). Si se produce esta situación, es posible que se bloqueen las solicitudes. A fin de poder diagnosticar problemas, obtenga información sobre cómo puede [comprobar y supervisar las aplicaciones lógicas](monitor-logic-apps.md).

Ahora ha creado un flujo de trabajo de la aplicación lógica que puede comunicarse con el servidor SAP. Ahora que ha configurado una conexión de SAP para el flujo de trabajo de la aplicación lógica, puede explorar otras acciones de SAP disponibles, como RFC y BAPI.

<a name="receive-message-sap"></a>

## <a name="receive-message-from-sap"></a>Recibir un mensaje de SAP

En este ejemplo se usa un flujo de trabajo de la aplicación lógica que se desencadena cuando la aplicación recibe un mensaje de un sistema SAP.

### <a name="add-an-sap-trigger"></a>Adición de un desencadenador de SAP

1. En Azure Portal, cree una aplicación lógica en blanco que abra el diseñador de flujo de trabajo.

1. En el cuadro de búsqueda, escriba `when message received sap` como filtro. En la lista **Desencadenadores**, seleccione **When a message is received from SAP** (Cuando se recibe un mensaje de SAP).

   ![Captura de pantalla que muestra cómo agregar un desencadenador de SAP.](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   O bien, puede elegir la pestaña **Enterprise** y después seleccionar la acción de desencadenar:

   ![Captura de pantalla que muestra cómo agregar un desencadenador de SAP desde la pestaña Enterprise (Empresa).](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Si la conexión ya existe, continúe con el paso siguiente para configurar el desencadenador de SAP. Sin embargo, si se le solicitan detalles de conexión, proporcione la información para que pueda crear una conexión a su servidor SAP local ahora.

   1. Proporcione un nombre para la conexión.

   1. Si usa la puerta de enlace de datos, siga estos pasos:

      1. En la sección **Puerta de enlace de datos**, en **Suscripción**, primero seleccione la suscripción de Azure para el recurso de puerta de enlace de datos que creó en el Azure Portal para la instalación de su puerta de enlace de datos.

      1. En **Puerta de enlace de conexión**, seleccione su recurso de puerta de enlace de datos en Azure.

   1. Siga proporcionando información acerca de la conexión. Para la propiedad **Tipo de inicio de sesión**, siga el paso en función de si la propiedad está establecida en **Servidor de aplicaciones** o **Grupo**:

      * Para **Servidos de aplicaciones**, estas propiedades, que generalmente parecen opcionales, son necesarias:

        ![Captura de pantalla que muestra cómo crear una conexión con el servidor de aplicaciones de SAP.](./media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Para **Grupo**, estas propiedades, que generalmente parecen opcionales, son necesarias:

        ![Captura de pantalla que muestra cómo crear una conexión con el servidor de mensajes de SAP.](./media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      De forma predeterminada, se usa el establecimiento inflexible de tipos para comprobar si hay valores no válidos, para lo cual se realiza la validación de XML con respecto al esquema. Este comportamiento puede ayudarlo a detectar problemas con antelación. La opción **Escritura segura** está disponible para la compatibilidad con versiones anteriores y solo comprueba la longitud de cadena. Más información sobre la opción [Escritura segura](#safe-typing).

   1. Cuando haya finalizado, seleccione **Crear**.

      Azure Logic Apps configura y comprueba la conexión para asegurarse de que funciona correctamente.

1. En función de la configuración del sistema SAP, proporcione los [parámetros necesarios](#parameters) y agregue cualquier otro parámetro disponible para este desencadenador, por ejemplo:

   * Para recibir IDocs como XML sin formato, en el desencadenador **Cuando se recibe un mensaje de SAP**, que admite el formato XML sin formato de SAP, agregue y establezca el parámetro **Formato de IDOC** en **SapPlainXml**.

   * Para recibir IDocs como un archivo plano con el mismo desencadenador de SAP, agregue y establezca el parámetro **Formato de IDOC** en **FlatFile**. Cuando también se usa la [acción Descodificación de archivo plano](logic-apps-enterprise-integration-flatfile.md), en el esquema de archivo plano, debe usar la propiedad `early_terminate_optional_fields` y establecer el valor en `true`.

     Este requisito es necesario porque el registro de datos IDoc de archivo plano que envía SAP en la llamada a tRFC `IDOC_INBOUND_ASYNCHRONOUS` no se agrega a la longitud completa del campo SDATA. Azure Logic Apps proporciona los datos originales de IDoc de archivo plano sin relleno tal y como se reciben de SAP. Además, al combinar este desencadenador de SAP con la acción Descodificación de archivos planos, el esquema que se proporciona a la acción debe coincidir.

   * Para [filtrar los mensajes que recibe desde el servidor SAP, especifique una lista de acciones de SAP](#filter-with-sap-actions).

     Por ejemplo, seleccione una acción de SAP desde el selector de archivos:

     ![Captura de pantalla que muestra cómo agregar una acción de SAP al flujo de trabajo de la aplicación lógica.](./media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

     Si lo prefiere, puede especificar una acción manualmente:

     ![Captura de pantalla que muestra cómo escribir manualmente la acción de SAP que quiere usar.](./media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

     Este es un ejemplo que muestra la apariencia de la acción cuando se configura el desencadenador para recibir varios mensajes.

     ![Captura de pantalla que muestra un ejemplo de desencadenador que recibe varios mensajes.](./media/logic-apps-using-sap-connector/example-trigger.png)

     Para obtener más información sobre la acción de SAP, consulte [Esquemas de mensaje para operaciones de IDoc](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Ahora guarde el flujo de trabajo de la aplicación lógica para poder empezar a recibir mensajes del sistema SAP. En la barra de herramientas del diseñador, seleccione **Save** (Guardar).

   El flujo de trabajo de la aplicación lógica está preparado para recibir mensajes del sistema SAP.

   > [!NOTE]
   > El desencadenador de SAP en estos pasos es un desencadenador basado en webhook, no un desencadenador de sondeo. Si usa la puerta de enlace de datos, el desencadenador se llama desde la puerta de enlace de datos solo cuando existe un mensaje, por lo que no es necesario realizar ningún sondeo.

1. En el historial de desencadenadores del flujo de trabajo de la aplicación lógica, compruebe que el registro del desencadenador se realiza correctamente.

Si recibe un error **500 Puerta de enlace incorrecta** o **400 Solicitud incorrecta** con un mensaje similar a **servicio "sapgw00" desconocido**, se ha producido un error en la resolución del nombre del servicio de red en el número de puerto, por ejemplo:

```json
{
   "body": {
      "error": {
         "code": 500,
         "source": "EXAMPLE-FLOW-NAME.eastus.environments.microsoftazurelogicapps.net",
         "clientRequestId": "00000000-0000-0000-0000-000000000000",
         "message": "BadGateway",
         "innerError": {
            "error": {
               "code": "UnhandledException",
               "message": "\nERROR service 'sapgw00' unknown\nTIME Wed Nov 11 19:37:50 2020\nRELEASE 721\nCOMPONENT NI (network interface)\nVERSION 40\nRC -3\nMODULE ninti.c\nLINE 933\nDETAIL NiPGetServByName: 'sapgw00' not found\nSYSTEM CALL getaddrinfo\nCOUNTER 1\n\nRETURN CODE: 20"
            }
         }
      }
   }
}
```

* **Opción 1:** en la configuración del desencadenador y la conexión de API, reemplace el nombre del servicio de puerta de enlace por su número de puerto. En el error de ejemplo, `sapgw00` debe reemplazarse por un número de puerto real, por ejemplo, `3300`. Esta es la única opción disponible para ISE.

* **Opción 2:** si usa la puerta de enlace de datos local, puede agregar el nombre del servicio de puerta de enlace a la asignación de puertos en `%windir%\System32\drivers\etc\services` y, después, reiniciar el servicio de puerta de enlace de datos local, por ejemplo:

  ```text
  sapgw00  3300/tcp
  ```

Es posible que reciba un error similar cuando el nombre del servidor de aplicaciones o del servidor de mensajes de SAP se resuelve en la dirección IP. En el caso de ISE, debe especificar la dirección IP para el servidor de aplicaciones o el servidor de mensajes de SAP. En el caso de la puerta de enlace de datos local, puede agregar el nombre a la asignación de direcciones IP en `%windir%\System32\drivers\etc\hosts`, por ejemplo:

```text
10.0.1.9 SAPDBSERVER01 # SAP System Server VPN IP by computer name
10.0.1.9 SAPDBSERVER01.someguid.xx.xxxxxxx.cloudapp.net # SAP System Server VPN IP by fully qualified computer name
```

### <a name="parameters"></a>Parámetros

Junto con entradas de cadena y número simples, el conector de SAP acepta estos parámetros de tabla (entradas `Type=ITAB`):

* Parámetros de dirección de tabla, tanto de entrada como de salida, para versiones anteriores de SAP.

* Parámetros de cambio, que reemplazan los parámetros de dirección de la tabla para las versiones más recientes de SAP.

* Parámetros de tabla jerárquica

### <a name="filter-with-sap-actions"></a>Filtrado con acciones de SAP

Opcionalmente, puede filtrar los mensajes que el flujo de trabajo de la aplicación lógica recibe del servidor SAP proporcionando una lista o una matriz, con una o varias acciones de SAP. De forma predeterminada, esta matriz está vacía, lo que significa que la aplicación lógica recibe todos los mensajes del servidor SAP sin filtrar.

Al configurar el filtro de matriz, el desencadenador solo recibe mensajes de los tipos de acción de SAP especificados y rechaza todos los demás mensajes del servidor SAP. Sin embargo, este filtro no afecta al establecimiento de tipos de la carga recibida, que puede ser flexible o inflexible.

Cualquier filtrado de acciones de SAP se produce en el nivel del adaptador de SAP para la puerta de enlace de datos local. Para obtener más información, consulte [Envío de IDoc de prueba a Azure Logic Apps desde SAP](#test-sending-idocs-from-sap).

Si no puede enviar paquetes de IDoc desde SAP al desencadenador del flujo de trabajo de la aplicación lógica, consulte el mensaje de rechazo de llamada de RFC transaccional (tRFC) en el cuadro de diálogo tRFC de SAP (código T SM58). En la interfaz de SAP, puede recibir los siguientes mensajes de error, que se recortan debido a los límites de subcadena del campo **Status Text** (Texto de estado).

#### <a name="the-requestcontext-on-the-ireplychannel-was-closed-without-a-reply-being-sent"></a>Se cerró el elemento RequestContext en la interfaz IReplyChannel sin que se enviara una respuesta

Este mensaje de error indica errores inesperados cuando el controlador comodín del canal finaliza el canal debido a un error y vuelve a generar el canal para procesar otros mensajes.

Para confirmar que el flujo de trabajo de la aplicación lógica recibió el IDoc, [agregue una acción de respuesta](../connectors/connectors-native-reqres.md#add-a-response-action) que devuelva un código de estado `200 OK`. Deje el cuerpo vacío y no realice modificaciones ni realice adiciones a los encabezados. El IDoc se transporta a través de tRFC, que no permite una carga de respuesta.

Para rechazar el IDoc en su lugar, responda con cualquier código de estado HTTP distinto de `200 OK`. A continuación, el adaptador de SAP devuelve una excepción a SAP en su nombre. Solo debe rechazar el IDoc para señalar los errores de transporte a SAP, como un IDoc mal enrutado que la aplicación no puede procesar. No debe rechazar un IDoc para los errores de nivel de aplicación, como los problemas con los datos contenidos en el IDoc. Si retrasa la aceptación del transporte para la validación en el nivel de la aplicación, es posible que experimente un rendimiento negativo debido al bloqueo de la conexión a raíz del transporte de otros IDOC.

Si recibe este mensaje de error y experimenta errores sistémicos al llamar a Azure Logic Apps, compruebe que ha configurado la configuración de red para el servicio de puerta de enlace de datos local de su entorno específico. Por ejemplo, si su entorno de red requiere el uso de un proxy para llamar a los puntos de conexión de Azure, debe configurar el servicio de puerta de enlace de datos local para usar el proxy. Para obtener más información, consulte [Configuración de proxy](/dotnet/framework/network-programming/proxy-configuration).

Si recibe este mensaje de error y experimenta errores intermitentes al llamar a Azure Logic Apps, puede que tenga que aumentar el número de reintentos o el intervalo de reintento.

1. Compruebe la configuración de SAP en el archivo de configuración del servicio de puerta de enlace de datos local, `Microsoft.PowerBI.EnterpriseGateway.exe.config`.

   La configuración del número de reintentos es similar a `WebhookRetryMaximumCount="2"`. La configuración del intervalo de reintento es similar a `WebhookRetryDefaultDelay="00:00:00.10"` y el formato del intervalo de tiempo es `HH:mm:ss.ff`.

1. Guarde los cambios. Reinicie su puerta de enlace de datos local.

#### <a name="the-segment-or-group-definition-e2edk36001-was-not-found-in-the-idoc-meta"></a>No se encontró el segmento o la definición de grupo E2EDK36001 en los metadatos del IDoc

Este mensaje de error indica que se produjeron errores esperados con otros errores. Por ejemplo, el error de generación de una carga XML de IDoc porque SAP no lanza sus segmentos. Como resultado, faltan los metadatos de tipo de segmento necesarios para la conversión.

Para que SAP publique estos segmentos, póngase en contacto con el ingeniero de ABAP para su sistema SAP.

### <a name="asynchronous-request-reply-for-triggers"></a>Solicitud-respuesta asincrónica para los desencadenadores

El conector de SAP es compatible con el [patrón de solicitud-respuesta asincrónico](/azure/architecture/patterns/async-request-reply) de Azure para los desencadenadores de Azure Logic Apps. Puede usar este patrón para crear solicitudes correctas que podrían no realizarse con el patrón de solicitud-respuesta sincrónico predeterminado.

> [!TIP]
> En flujos de trabajo de aplicaciones lógicas con varias acciones de respuesta, todas las acciones de respuesta deben usar el mismo patrón de solicitud-respuesta. Por ejemplo, si el flujo de trabajo de la aplicación lógica usa un control de cambio con varias acciones de respuesta posibles, debe configurar todas las acciones de respuesta para usar el mismo patrón de solicitud-respuesta, ya sea sincrónico o asincrónico.

Si habilita la respuesta asincrónica para la acción de respuesta, el flujo de trabajo de la aplicación lógica puede responder con una respuesta `202 Accepted` después de aceptar una solicitud de procesamiento. La respuesta contiene un encabezado de ubicación que puede usar para recuperar el estado final de la solicitud.

Para configurar un patrón de solicitud-respuesta asincrónico para el flujo de trabajo de la aplicación lógica mediante el conector de SAP, siga estos pasos:

1. Abra la aplicación lógica en el diseñador de flujo de trabajo.

1. Confirme que el conector de SAP es el desencadenador del flujo de trabajo de la aplicación lógica.

1. Abra la acción de **respuesta** del flujo de trabajo de la aplicación lógica. En la barra de título de la acción, seleccione el menú ( **...** ) &gt; **Configuración**.

1. En la **configuración** de la acción de respuesta, active la alternancia en **Respuesta asincrónica**. Seleccione Listo.

1. Guarde los cambios en el flujo de trabajo de la aplicación lógica.

## <a name="find-extended-error-logs"></a>Búsqueda de registros de errores ampliados

Para ver los mensajes de error completos, compruebe los registros ampliados del adaptador de SAP. También puede [habilitar un archivo de registro ampliado para el conector de SAP](#extended-sap-logging-in-on-premises-data-gateway).

* En el caso de las versiones de puerta de enlace de datos local de abril de 2020 y anteriores, los registros están deshabilitados de forma predeterminada.

* En el caso de las versiones de puerta de enlace de datos local de junio de 2020 y posteriores, puede [habilitar los registros de puerta de enlace en la configuración de la aplicación](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app).

  * El nivel de registro predeterminado es **Advertencia**.

  * Si habilita **Registro adicional** en la configuración de **Diagnóstico** de la aplicación de puerta de enlace de datos local, el nivel de registro aumenta a **Informativo**.

  * Para aumentar el nivel de registro a **Detallado**, actualice el siguiente valor en el archivo de configuración. Normalmente el archivo de configuración se encuentra en `C:\Program Files\On-premises data gateway\Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config`.

    ```xml
    <setting name="SapTraceLevel" serializeAs="String">
       <value>Verbose</value>
    </setting>
    ```

### <a name="extended-sap-logging-in-on-premises-data-gateway"></a>Registro de SAP ampliado en la puerta de enlace de datos local

Si usa una [puerta de enlace de datos local para Azure Logic Apps](logic-apps-gateway-install.md), puede configurar un archivo de registro ampliado para el conector de SAP. Puede usar la puerta de enlace de datos local para redirigir los eventos de Seguimiento de eventos para Windows (ETW) en los archivos de registro giratorios que se incluyen en los archivos .zip de registro de la puerta de enlace.

Puede [exportar todos los registros de configuración y servicio de la puerta de enlace](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app) a un archivo .zip en desde la configuración de la aplicación de puerta de enlace.

> [!NOTE]
> El registro ampliado puede afectar al rendimiento del flujo de trabajo de las aplicaciones lógicas cuando está siempre habilitado. Se recomienda desactivar los archivos de registro ampliados una vez que haya terminado de analizar y solucionar un problema.

#### <a name="capture-etw-events"></a>Captura de eventos ETW

Opcionalmente, los usuarios avanzados pueden capturar eventos ETW directamente. Puede [consumir los datos de Azure Diagnostics en Event Hubs](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md) o [recopilar los datos para registros de Azure Monitor](../azure-monitor/agents/diagnostics-extension-logs.md). Para obtener más información, consulte los [procedimientos recomendados para recopilar y almacenar datos](/azure/architecture/best-practices/monitoring#collecting-and-storing-data). Puede usar [PerfView](https://github.com/Microsoft/perfview/blob/master/README.md) para trabajar con los archivos ETL resultantes o puede escribir su propio programa. Este tutorial usa PerfView:

1. En el menú de PerfView, seleccione **Recopilar** &gt; **Recopilar** para capturar los eventos.

1. En el campo **Proveedor adicional**, escriba `*Microsoft-LobAdapter` para especificar el proveedor de SAP para capturar eventos del adaptador de SAP. Si no especifica esta información, el seguimiento solo incluye eventos ETW generales.

1. Deje el resto de los valores predeterminados. Si quiere, puede cambiar el nombre de archivo o la ubicación en el campo **Archivo de datos**.

1. Seleccione **Iniciar colección** para iniciar el seguimiento.

1. Una vez que haya reproducido el problema o recopilado suficientes datos de análisis, seleccione **Detener recopilación**.

1. Para compartir los datos con otra entidad, como los ingenieros de Soporte técnico de Azure, comprima el archivo ETL.

1. Para ver el contenido del seguimiento:

   1. En PerfView, seleccione **Archivo** &gt; **Abrir** y, a continuación, seleccione el archivo ETL que acaba de generar.

   1. En la barra lateral de PerfView, en la sección **Eventos** del archivo ETL.

   1. En **Filtro**, filtre por `Microsoft-LobAdapter` para ver solo los eventos y procesos de puerta de enlace pertinentes.

### <a name="test-your-workflow"></a>Prueba del flujo de trabajo

1. Para desencadenar el flujo de trabajo de la aplicación lógica, envíe un mensaje desde el sistema SAP.

1. En el menú de la aplicación lógica, seleccione **Información general**. Revise el **historial de ejecuciones** para ver las nuevas ejecuciones del flujo de trabajo de la aplicación lógica.

1. Abra la ejecución más reciente, que muestra el mensaje enviado desde su sistema SAP en la sección de salidas del desencadenador.

### <a name="test-sending-idocs-from-sap"></a>Prueba de envío de IDoc desde SAP

Para enviar IDoc desde SAP al flujo de trabajo de la aplicación lógica, necesita la siguiente configuración mínima:

> [!IMPORTANT]
> Siga estos pasos solo al probar la configuración de SAP con el flujo de trabajo de la aplicación lógica. Los entornos de producción requieren configuración adicional.

1. [Configuración de un destino RFC en SAP.](#create-rfc-destination)

1. [Creación de una conexión ABAP con el destino RFC.](#create-abap-connection)

1. [Creación de un puerto receptor.](#create-receiver-port)

1. [Creación de un puerto remitente.](#create-sender-port)

1. [Creación de un asociado del sistema lógico.](#create-logical-system-partner)

1. [Creación de perfil de socio.](#create-partner-profiles)

1. [Prueba de envío de mensajes.](#test-sending-messages)

#### <a name="create-rfc-destination"></a>Creación de destino RFC

1. Para abrir la opción **Configuration of RFC Connections** (Configuración de las conexiones RFC), en la interfaz de SAP, use el código de transacción (código T) **sm59** con el prefijo **/n**.

1. Seleccione **TCP/IP Connections** > **Create** (Conexiones TCP/IP > Crear).

1. Cree un nuevo destino RFC con la siguiente configuración:

    1. En **RFC Destination** (Destino RFC), escriba un nombre.

    1. En la pestaña **Technical Settings** (Configuración técnica), en **Activation Type** (Tipo de activación), seleccione **Registered Server Program** (Programa de servidor registrado).

    1. En **Program ID** (Id. de programa), escriba un valor. En SAP, el desencadenador del flujo de trabajo de la aplicación lógica se registrará con este identificador.

       > [!IMPORTANT]
       > El **identificador de programa** de SAP distingue mayúsculas de minúsculas. Asegúrese de que usa de forma coherente el mismo formato de mayúsculas y minúsculas para el **identificador de programa** al configurar el flujo de trabajo de la aplicación lógica y el servidor SAP. De lo contrario, podría recibir los siguientes errores en el monitor tRFC (T-Code SM58) al intentar enviar un IDoc a SAP:
       >
       > * **No se encontró la función IDOC_INBOUND_ASYNCHRONOUS.**
       > * **No se admite el cliente RFC que no sea ABAP (tipo de socio).**
       >
       > Para más información de SAP, consulte las notas siguientes (inicio de sesión necesario):
       >
       > * [https://launchpad.support.sap.com/#/notes/2399329](https://launchpad.support.sap.com/#/notes/2399329)
       > * [https://launchpad.support.sap.com/#/notes/353597](https://launchpad.support.sap.com/#/notes/353597)

    1. En la pestaña **Unicode**, para **Communication Type with Target System** (Tipo de comunicación con sistema de destino), seleccione **Unicode**.

       > [!NOTE]
       > Las bibliotecas de cliente .NET de SAP solo admiten la codificación de caracteres Unicode. Si recibe el error `Non-ABAP RFC client (partner type ) not supported` al enviar IDoc desde SAP a Azure Logic Apps, compruebe que el valor de **Tipo de comunicación con sistema de destino** esté establecido en **Unicode**.

1. Guarde los cambios.

1. Registre el nuevo **identificador de programa** con Azure Logic Apps mediante la creación de un flujo de trabajo de aplicación lógica que comienza con el desencadenador de SAP denominado **Cuando se recibe un mensaje de SAP**.

   De este modo, al guardar el flujo de trabajo, Azure Logic Apps registra el **identificador de programa** en la puerta de enlace de SAP.

1. En el historial de desencadenadores del flujo de trabajo, los registros del adaptador de SAP de puerta de enlace de datos local y los registros de seguimiento de puerta de enlace de SAP, compruebe el estado de registro. En el cuadro de diálogo Monitor de puerta de enlace de SAP (código T SMGW), en **Logged-On Clients** (Clientes que han iniciado sesión), el nuevo registro debe aparecer como **Registered Server** (Servidor registrado).

1. Para probar la conexión, en la interfaz de SAP, en el nuevo **RFC Destination** (Destino RFC), seleccione **Connection Test** (Prueba de conexión).

#### <a name="create-abap-connection"></a>Creación de conexión ABAP

1. Para abrir la opción **Configuration of RFC Connections** (Configuración de las conexiones RFC), en la interfaz de SAP, use el código de transacción (código T) **sm59** _ con el prefijo _ */n**.

1. Seleccione **ABAP Connections** > **Create** (Conexiones ABAP > Crear).

1. **RFC Destination** (Destino RFC), escriba el identificador de [su sistema SAP de prueba](#create-rfc-destination).

1. Guarde los cambios.

1. Para probar la conexión, seleccione **Connection Test** (Prueba de conexión).

#### <a name="create-receiver-port"></a>Creación de un puerto receptor

1. Para abrir la configuración **Ports In IDOC processing** (Procesamiento de puertos en IDoc), en la interfaz de SAP, use el código de transacción (código T) **we21** con el prefijo **/n**.

1. Seleccione **Ports** > **Transactional RFC** > **Create** (Puertos > RFC transaccional > Crear).

1. En el cuadro de configuración que se abre, seleccione **own port name** (nombre propio del puerto). En el puerto de prueba, escriba un **nombre**. Guarde los cambios.

1. En la configuración del nuevo puerto receptor, para **RFC destination** (Destino RFC), escriba el identificador del [destino RFC de prueba](#create-rfc-destination).

1. Guarde los cambios.

#### <a name="create-sender-port"></a>Creación de un puerto remitente

1. Para abrir la configuración **Ports In IDOC processing** (Procesamiento de puertos en IDoc), en la interfaz de SAP, use el código de transacción (código T) **we21** con el prefijo **/n**.

1. Seleccione **Ports** > **Transactional RFC** > **Create** (Puertos > RFC transaccional > Crear).

1. En el cuadro de configuración que se abre, seleccione **own port name** (nombre propio del puerto). En el puerto de prueba, escriba un **nombre** que empiece por **SAP**. Todos los nombres de puerto remitente deben comenzar con las letras **SAP**, por ejemplo, **SAPTEST**. Guarde los cambios.

1. En la configuración del nuevo puerto remitente, para **RFC destination** (Destino RFC), escriba el identificador de [la conexión de ABAP](#create-abap-connection).

1. Guarde los cambios.

#### <a name="create-logical-system-partner"></a>Creación de un asociado del sistema lógico

1. Para abrir la configuración **Change View "Logical Systems": Overview** (Cambiar la vista de sistemas lógicos: información general), en la interfaz de SAP, use el código de transacción (código T) **bd54**.

1. Acepte el mensaje de advertencia que aparece: **Caution: The table is cross-client** (Precaución, la tabla afecta a varios clientes).

1. Encima de la lista que muestra los sistemas lógicos existentes, seleccione **New Entries** (Nuevas entradas).

1. En el nuevo sistema lógico, escriba un identificador de **Log.System** y una descripción breve del **nombre**. Guarde los cambios.

1. Cuando aparezca **Prompt for Workbench** (Solicitud de banco de trabajo), cree una nueva solicitud. Para ello, proporcione una descripción (si ya ha creado una solicitud, omita este paso).

1. Después de crear la solicitud de banco de trabajo, vincule esa solicitud a la solicitud de actualización de la tabla. Para confirmar que la tabla se ha actualizado, guarde los cambios.

#### <a name="create-partner-profiles"></a>Creación de perfiles de socios

En entornos de producción, debe crear dos perfiles de socio. El primer perfil es para el remitente, que es su organización y el sistema SAP. El segundo perfil es para el receptor, que es la aplicación lógica.

1. Para abrir la configuración **Partner profiles** (Perfiles de asociado), en la interfaz de SAP, use el código de transacción (código T) **we20** con el prefijo **/n**.

1. En **Partner Profiles** (Perfiles de socio), seleccione **Partner Type LS** > **Create** (Tipo de socio LS > Crear).

1. Cree un nuevo perfil de socio con la siguiente configuración:

    * En **Partner No.** (Núm. de socio), escriba el [identificador de socio del sistema lógico](#create-logical-system-partner).

    * En **Partn. Type** (Tipo de socio), escriba **LS**.

    * En **Agent** (Agente), escriba el identificador de la cuenta de usuario de SAP que utilizará al registrar identificadores de programa para Azure Logic Apps u otros sistemas que no sean de SAP.

1. Guarde los cambios. Si no ha [creado el socio del sistema lógico](#create-logical-system-partner), aparecerá el error **Enter a valid partner number** (Escriba un número de socio válido).

1. En la configuración del perfil de socio, en **Outbound parmtrs.** (Parámetros de salida), seleccione **Create outbound parameter** (Crear parámetro de salida).

1. Cree un nuevo parámetro de salida con la siguiente configuración:

    * Escriba su **tipo de mensaje**, por ejemplo, **CREMAS**.

    * Escriba el [identificador del puerto receptor](#create-receiver-port).

    * Escriba un tamaño de IDoc para **Pack. Tamaño**. O bien, para [enviar IDoc de uno en uno desde SAP](#receive-idoc-packets-from-sap), seleccione **Pass IDoc Immediately** (Pasar IDoc de inmediato).

1. Guarde los cambios.

#### <a name="test-sending-messages"></a>Prueba de envío de mensajes

1. Para abrir la configuración **Test Tool for IDoc Processing** (Procesamiento de la herramienta de prueba para IDoc), en la interfaz de SAP, use el código de transacción (código T) **we19** con el prefijo **/n**.

1. En **Template for test** (Plantilla para prueba), seleccione **Via message type** (Mediante tipo de mensaje) y escriba su tipo de mensaje; por ejemplo, **CREMAS**. Seleccione **Crear**.

1. Confirme el mensaje **Which IDoc type?** (¿Qué tipo de IDoc?) seleccionando **Continue** (Continuar).

1. Seleccione el nodo **EDIDC**. Escriba los valores adecuados para los puertos receptor y remitente. Seleccione **Continuar**.

1. Seleccione **Standard Outbound Processing** (Procesamiento de salida estándar).

1. Para iniciar el procesamiento de IDoc de salida, seleccione **Continue** (Continuar). Una vez finalizado el procesamiento, aparece el mensaje **IDoc sent to SAP system or external program** (IDoc enviado a sistema SAP o programa externo).

1. Para comprobar si hay errores de procesamiento, use el código de transacción (código T) **sm58** con el prefijo **/n**.

## <a name="receive-idoc-packets-from-sap"></a>Recepción de paquetes IDoc de SAP

Puede configurar SAP para que [envíe IDoc en paquetes](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/4ab38886549a6d8ce10000000a42189c.html), que son lotes o grupos de IDoc. Para recibir paquetes IDoc, el conector de SAP y, en concreto, el desencadenador, no necesitan una configuración adicional. Pero para procesar cada elemento en un paquete IDoc después de que el desencadenador reciba el paquete, se necesitan algunos pasos adicionales para dividir el paquete en IDoc individuales.

Este es un ejemplo que muestra cómo extraer IDoc individuales de un paquete mediante la [función `xpath()`](./workflow-definition-language-functions-reference.md#xpath):

1. Antes de empezar, necesita un flujo de trabajo de la aplicación lógica con un desencadenador de SAP. Si aún no tiene este desencadenador en el flujo de trabajo de la aplicación lógica, siga los pasos anteriores de este tema para [configurar un flujo de trabajo de la aplicación lógica con un desencadenador de SAP](#receive-message-from-sap).

   > [!IMPORTANT]
   > El **identificador de programa** de SAP distingue mayúsculas de minúsculas. Asegúrese de que usa de forma coherente el mismo formato de mayúsculas y minúsculas para el **identificador de programa** al configurar el flujo de trabajo de la aplicación lógica y el servidor SAP. De lo contrario, podría recibir los siguientes errores en el monitor tRFC (T-Code SM58) al intentar enviar un IDoc a SAP:
   >
   > * **No se encontró la función IDOC_INBOUND_ASYNCHRONOUS.**
   > * **No se admite el cliente RFC que no sea ABAP (tipo de socio).**
   >
   > Para más información de SAP, consulte las notas siguientes (inicio de sesión necesario) [https://launchpad.support.sap.com/#/notes/2399329](https://launchpad.support.sap.com/#/notes/2399329) y [https://launchpad.support.sap.com/#/notes/353597](https://launchpad.support.sap.com/#/notes/353597).

   Por ejemplo:

   ![Captura de pantalla que muestra cómo agregar un desencadenador de SAP al flujo de trabajo de la aplicación lógica.](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. [Agregue una acción de respuesta al flujo de trabajo de la aplicación lógica](../connectors/connectors-native-reqres.md#add-a-response-action) para responder inmediatamente con el estado de la solicitud de SAP. Se recomienda agregar esta acción inmediatamente después del desencadenador para liberar el canal de comunicación con el servidor SAP. Elija uno de los siguientes códigos de estado (`statusCode`) para usarlo en la acción de respuesta:

    * **202 Aceptado**, lo que significa que la solicitud se ha aceptado para el procesamiento, pero el procesamiento aún no se ha completado.

    * **204 Sin contenido**, lo que significa que el servidor ha realizado correctamente la solicitud y no hay contenido adicional para enviar en el cuerpo de la carga de respuesta. 

    * **200 - CORRECTO**. Este código de estado siempre contiene una carga útil, aunque el servidor genere un cuerpo de carga de longitud cero. 

1. Obtenga el espacio de nombres raíz de IDoc XML que el flujo de trabajo de la aplicación lógica recibe de SAP. Para extraer este espacio de nombres del documento XML, agregue un paso que cree una variable de cadena local y almacene dicho espacio de nombres mediante una expresión `xpath()`:

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Captura de pantalla que muestra cómo obtener el espacio de nombres raíz de IDoc.](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Para extraer un IDoc individual, agregue un paso que cree una variable de matriz y almacene la colección de IDoc mediante otra expresión `xpath()`:

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Captura de pantalla que muestra cómo obtener una matriz de elementos.](./media/logic-apps-using-sap-connector/get-array.png)

   La variable de matriz hace que cada IDoc esté disponible para que el flujo de trabajo de la aplicación lógica lo procese individualmente mediante la enumeración de la colección. En este ejemplo, el flujo de trabajo de la aplicación lógica transfiere cada IDoc a un servidor SFTP mediante un bucle:

   ![Captura de pantalla que muestra cómo enviar un IDoc a un servidor SFTP.](./media/logic-apps-using-sap-connector/loop-batch.png)

   Cada IDoc debe incluir el espacio de nombres raíz, que es el motivo por el que el contenido del archivo se ajusta dentro de un elemento `<Receive></Receive>` junto con el espacio de nombres raíz antes de enviar el IDoc a la aplicación de nivel inferior o servidor SFTP en este caso.

Puede usar la plantilla de inicio rápido para este patrón si selecciona esta plantilla en el diseñador de flujo de trabajo al crear un nuevo flujo de trabajo de la aplicación lógica.

![Captura de pantalla que muestra cómo seleccionar una plantilla de aplicación lógica por lotes.](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Generar esquemas para artefactos en SAP

En este ejemplo, se utiliza un flujo de trabajo de la aplicación lógica que se puede desencadenar con una solicitud HTTP. Para generar los esquemas para los IDoc y BAPI especificados, la acción de SAP **Generate schema** (Generar esquema) envía una solicitud a un sistema SAP.

Esta acción de SAP devuelve un [esquema XML](#sample-xml-schemas), no el contenido o los datos del propio documento XML. Los esquemas que se devuelven en la respuesta se cargan en una cuenta de integración mediante el conector de Azure Resource Manager. Los esquemas contienen las siguientes partes:

* La estructura del mensaje de solicitud. Use esta información para formar la lista `get` de BAPI.

* La estructura del mensaje de respuesta. Utilice esta información para analizar la respuesta.

Para enviar el mensaje de solicitud, use la acción genérica de SAP **Enviar mensaje a SAP** o las acciones **\[BAPI] Call method in SAP** ([BAPI] Llamar al método en SAP) de destino.

### <a name="sample-xml-schemas"></a>Esquemas XML de ejemplo

Si está aprendiendo a generar un esquema XML para usarlo en la creación de un documento de ejemplo, consulte los ejemplos siguientes. En estos ejemplos se muestra cómo puede trabajar con muchos tipos de cargas, entre las que se incluyen:

* [Solicitudes RFC](#xml-samples-for-rfc-requests)

* [Solicitudes BAPI](#xml-samples-for-bapi-requests)

* [Solicitudes IDoc](#xml-samples-for-idoc-requests)

* Tipos de datos de esquemas XML simples o complejos

* Parámetros de tabla

* Comportamientos XML opcionales

Puede comenzar el esquema XML con un prólogo XML opcional. El conector de SAP funciona con o sin el prólogo XML.

```xml
<?xml version="1.0" encoding="utf-8">
```

#### <a name="xml-samples-for-rfc-requests"></a>Ejemplos XML para solicitudes de RFC

El ejemplo siguiente es una llamada de RFC simple. El nombre de RFC es `STFC_CONNECTION`. Esta solicitud usa el espacio de nombres predeterminado `xmlns=`, pero puede asignar y utilizar alias de espacio de nombres como `xmmlns:exampleAlias=`. El valor del espacio de nombres es el espacio de nombres para todas las RFC en SAP para servicios de Microsoft. Hay un parámetro de entrada simple en la solicitud: `<REQUTEXT>`.

```xml
<STFC_CONNECTION xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
   <REQUTEXT>exampleInput</REQUTEXT>
</STFC_CONNECTION>
```

El ejemplo siguiente es una llamada de RFC con un parámetro de tabla. Esta llamada de ejemplo y su grupo de RFC de prueba están disponibles como parte de todos los sistemas SAP. El nombre del parámetro de tabla es `TCPICDAT`. El tipo de línea de tabla es `ABAPTEXT` y este elemento se repite para cada fila de la tabla. Este ejemplo contiene una sola línea, denominada `LINE`. Las solicitudes con un parámetro de tabla pueden contener cualquier número de campos, donde el número es un entero positivo (*n*). 

```xml
<STFC_WRITE_TO_TCPIC xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
   <RESTART_QNAME>exampleQName</RESTART_QNAME>
   <TCPICDAT>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
         <LINE>exampleFieldInput1</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
         <LINE>exampleFieldInput2</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
         <LINE>exampleFieldInput3</LINE>
      </ABAPTEXT>
   </TCPICDAT>
</STFC_WRITE_TO_TCPIC>
```

> [!NOTE]
> Observe el resultado de RFC **STFC_WRITE_TO_TCPIC** con el explorador de datos del inicio de sesión de SAP (T-Code SE16). Use el nombre de tabla **TCPIC**.

El ejemplo siguiente es una llamada de RFC con un parámetro de tabla que tiene un campo anónimo. Un campo anónimo es cuando el campo no tiene asignado ningún nombre. Los tipos complejos se declaran en un espacio de nombres independiente, en el que la declaración establece un nuevo valor predeterminado para el nodo actual y todos sus elementos secundarios. En el ejemplo se usa el código hexadecimal `x002F` como carácter de escape para el símbolo */* , porque este símbolo está reservado en el nombre de campo de SAP.

```xml
<RFC_XML_TEST_1 xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
   <IM_XML_TABLE>
      <RFC_XMLCNT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
         <_x002F_AnonymousField>exampleFieldInput</_x002F_AnonymousField>
      </RFC_XMLCNT>
   </IM_XML_TABLE>
</RFC_XML_TEST_1>
```

En el ejemplo siguiente se incluyen los prefijos de los espacios de nombres. Puede declarar todos los prefijos a la vez o puede declarar un número de ellos como atributos de un nodo. El alias del espacio de nombres de RFC `ns0` se utiliza como la raíz y los parámetros para el tipo básico.

> [!NOTE]
> Los tipos complejos se declaran en otro espacio de nombres para tipos de RFC con el alias `ns3` en lugar del espacio de nombres de RFC normal con el alias `ns0`.

```xml
<ns0:BBP_RFC_READ_TABLE xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Rfc/" xmlns:ns3="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc/">
   <ns0:DELIMITER>0</ns0:DELIMITER>
   <ns0:QUERY_TABLE>KNA1</ns0:QUERY_TABLE>
   <ns0:ROWCOUNT>250</ns0:ROWCOUNT>
   <ns0:ROWSKIPS>0</ns0:ROWSKIPS>
   <ns0:FIELDS>
      <ns3:RFC_DB_FLD>
         <ns3:FIELDNAME>KUNNR</ns3:FIELDNAME>
      </ns3:RFC_DB_FLD>
   </ns0:FIELDS>
</ns0:BBP_RFC_READ_TABLE>
```

#### <a name="xml-samples-for-bapi-requests"></a>Ejemplos XML para solicitudes de BAPI

Los siguientes ejemplos XML son solicitudes de ejemplo para [llamar al método BAPI](#actions).

> [!NOTE]
> SAP pone los objetos comerciales a disposición de los sistemas externos mediante su descripción en respuesta a RFC `RPY_BOR_TREE_INIT`, que Azure Logic Apps emite sin ningún filtro de entrada. Logic Apps inspecciona la tabla de salida `BOR_TREE`. El campo `SHORT_TEXT` se utiliza para los nombres de objetos comerciales. Azure Logic Apps no puede acceder a los objetos comerciales no devueltos por SAP en la tabla de salida.
> 
> Si usa objetos comerciales personalizados, debe asegurarse de publicar y liberar estos objetos comerciales en SAP. De lo contrario, SAP no muestra los objetos comerciales personalizados en la tabla de salida `BOR_TREE`. No se puede acceder a los objetos comerciales personalizados en Logic Apps hasta que se exponen los objetos comerciales desde SAP. 

En el ejemplo siguiente se obtiene una lista de bancos mediante el método de BAPI `GETLIST`. Este ejemplo contiene el objeto comercial de un banco: `BUS1011`.

```xml
<GETLIST xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
   <BANK_CTRY>US</BANK_CTRY>
   <MAX_ROWS>10</MAX_ROWS>
</GETLIST>
```

En el ejemplo siguiente se crea un objeto de banco mediante el método `CREATE`. En este ejemplo se usa el mismo objeto comercial que en el ejemplo anterior: `BUS1011`. Cuando use el método `CREATE` para crear un banco, asegúrese de confirmar los cambios porque este método no se confirma de forma predeterminada.

> [!TIP]
> Asegúrese de que el documento XML sigue las reglas de validación configuradas en el sistema SAP. Por ejemplo, en este documento de referencia, la clave del banco (`<BANK_KEY>`) debe ser un número de enrutamiento bancario, que en Estados Unidos también se conoce como ABA.

```xml
<CREATE xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_ADDRESS>
    <BANK_NAME xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleBankName</BANK_NAME>
    <REGION xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleRegionName</REGION>
    <STREET xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleStreetAddress</STREET>
    <CITY xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">Redmond</CITY>
  </BANK_ADDRESS>
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</CREATE>
```

En el ejemplo siguiente se obtienen detalles de un banco mediante el número de enrutamiento bancario, el valor de `<BANK_KEY>`. 

```xml
<GETDETAIL xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</GETDETAIL>
```

#### <a name="xml-samples-for-idoc-requests"></a>Ejemplos de XML para solicitudes de IDoc

Para generar un esquema XML de IDoc para SAP sin formato, use la aplicación **Inicio de sesión de SAP** y el código T `WE-60`. Acceda a la documentación de SAP a través de la GUI y genere esquemas XML en formato XSD para sus tipos y extensiones de IDoc. Para obtener una explicación de los formatos y las cargas de SAP genéricos y sus cuadros de diálogo integrados, consulte la [documentación de SAP](https://help.sap.com/viewer/index).

En este ejemplo se declaran los espacios de nombres y el nodo raíz. El URI del código de ejemplo (`http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send`) declara la siguiente configuración:

* `/IDoc` es la nota raíz para todos los IDoc.

* `/3` es la versión de tipos de registro para definiciones de segmento comunes.

* `/ORDERS05` es el tipo de IDoc.

* `//` es un segmento vacío porque no hay ninguna extensión IDoc.

* `/700` es la versión de SAP.

* `/Send` es la acción para enviar la información a SAP.

```xml
<ns0:Send xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send" xmlns:ns3="http://schemas.microsoft.com/2003/10/Serialization" xmlns:ns1="http://Microsoft.LobServices.Sap/2007/03/Types/Idoc/Common/" xmlns:ns2="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700">
   <ns0:idocData>
```

Puede repetir el nodo `idocData` para enviar un lote de IDoc en una sola llamada. En el ejemplo siguiente, hay un registro de control (`EDI_DC40`) y varios registros de datos.

```xml
<...>
  <ns0:idocData>
    <ns2:EDI_DC40>
      <ns1:TABNAM>EDI_DC40</ns1:TABNAM>
      <...>
      <ns1:ARCKEY>Cor1908207-5</ns1:ARCKEY>
    </ns2:EDI_DC40>
    <ns2:E2EDK01005>
      <ns2:DATAHEADERCOLUMN_SEGNAM>E23DK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:CURCY>USD</ns2:CURCY>
    </ns2:E2EDK01005>
    <ns2:E2EDK03>
    <...>
  </ns0:idocData>
```

El ejemplo siguiente es un registro de control IDoc de ejemplo, que usa el prefijo `EDI_DC`. Debe actualizar los valores para que coincidan con la instalación de SAP y el tipo de IDoc. Por ejemplo, el código de cliente de IDoc puede no ser `800`. Póngase en contacto con su equipo de SAP para asegurarse de que está usando los valores correctos para la instalación de SAP.

```xml
<ns2:EDI_DC40>
  <ns:TABNAM>EDI_DC40</ns1:TABNAM>
  <ns:MANDT>800</ns1:MANDT>
  <ns:DIRECT>2</ns1:DIRECT>
  <ns:IDOCTYP>ORDERS05</ns1:IDOCTYP>
  <ns:CIMTYP></ns1:CIMTYP>
  <ns:MESTYP>ORDERS</ns1:MESTYP>
  <ns:STD>X</ns1:STD>
  <ns:STDVRS>004010</ns1:STDVRS>
  <ns:STDMES></ns1:STDMES>
  <ns:SNDPOR>SAPENI</ns1:SNDPOR>
  <ns:SNDPRT>LS</ns1:SNDPRT>
  <ns:SNDPFC>AG</ns1:SNDPFC>
  <ns:SNDPRN>ABAP1PXP1</ns1:SNDPRN>
  <ns:SNDLAD></ns1:SNDLAD>
  <ns:RCVPOR>BTSFILE</ns1:RCVPOR>
  <ns:RCVPRT>LI</ns1:RCVPRT>
```

El ejemplo siguiente es un registro de datos de ejemplo con segmentos sin formato. En este ejemplo se usa el formato de fecha de SAP. Los documentos fuertemente tipados pueden utilizar formatos de fecha XML nativos, como `2020-12-31 23:59:59`.

```xml
<ns2:E2EDK01005>
  <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
    <ns2:CURCY>USD</ns2:CURCY>
    <ns2:BSART>OR</ns2:BSART>
    <ns2:BELNR>1908207-5</ns2:BELNR>
    <ns2:ABLAD>CC</ns2:ABLAD>
  </ns2>
  <ns2:E2EDK03>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK03</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:IDDAT>002</ns2:IDDAT>
      <ns2:DATUM>20160611</ns2:DATUM>
  </ns2:E2EDK03>
```

El ejemplo siguiente es un registro de datos con segmentos agrupados. El registro incluye un nodo primario de grupo (`E2EDKT1002GRP`) y varios nodos secundarios, como `E2EDKT1002` y `E2EDKT2001`. 

```xml
<ns2:E2EDKT1002GRP>
  <ns2:E2EDKT1002>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT1002</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:TDID>ZONE</ns2:TDID>
  </ns2:E2EDKT1002>
  <ns2:E2EDKT2001>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT2001</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:TDLINE>CRSD</ns2:TDLINE>
  </ns2:E2EDKT2001>
</ns2:E2EDKT1002GRP>
```

El método recomendado es crear un identificador de IDoc para su uso con tRFC. Puede establecer este identificador de transacción (`tid`) mediante la [operación de envío de IDoc](/connectors/sap/#send-idoc) en la API del conector de SAP.

El ejemplo siguiente es un método alternativo para establecer el identificador de la transacción, o `tid`. En este ejemplo, el último nodo de segmento de registro de datos y el nodo de datos de IDoc están cerrados. A continuación, el GUID (`guid`) se usa como el identificador de tRFC para detectar duplicados.

```xml
     </E2STZUM002GRP>
  </idocData>
  <guid>8820ea40-5825-4b2f-ac3c-b83adc34321c</guid>
</Send>
```

### <a name="add-the-request-trigger"></a>Adición del desencadenador Request

1. En Azure Portal, cree una aplicación lógica en blanco que abra el diseñador de flujo de trabajo.

1. En el cuadro de búsqueda, escriba `http request` como filtro. En la lista **Desencadenadores**, seleccione **Cuando se recibe una solicitud HTTP**.

   ![Captura de pantalla que muestra cómo agregar el desencadenador de solicitud.](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Ahora puede guardar la aplicación lógica para poder generar una dirección URL del punto de conexión para el flujo de trabajo de la aplicación lógica. En la barra de herramientas del diseñador, seleccione **Save** (Guardar).

   La dirección URL del punto de conexión aparece ahora en el desencadenador, por ejemplo:

   ![Captura de pantalla que muestra cómo generar la dirección URL del punto de conexión.](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Adición de una acción de SAP para generar esquemas

1. En el diseñador de flujo de trabajo, en el desencadenador, seleccione **Nuevo paso**.

   ![Captura de pantalla que muestra cómo agregar un nuevo paso al flujo de trabajo de la aplicación lógica.](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. En el cuadro de búsqueda, escriba `generate schemas sap` como filtro. En la lista **Actions** (Acciones), seleccione **Generate schemas** (Generar esquemas).
  
   ![Captura de pantalla que muestra cómo agregar la acción "Generar esquemas" al flujo de trabajo.](./media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   O bien, puede elegir la pestaña **Enterprise** y seleccionar la acción de SAP.

   ![Captura de pantalla que muestra cómo seleccionar la acción "Generar esquemas" en la pestaña Enterprise.](./media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Si la conexión ya existe, continúe con el paso siguiente para configurar la acción de SAP. Sin embargo, si se le solicitan detalles de conexión, proporcione la información para que pueda crear una conexión a su servidor SAP local ahora.

   1. Proporcione un nombre para la conexión.

   1. En la sección **Puerta de enlace de datos**, en **Suscripción**, primero seleccione la suscripción de Azure para el recurso de puerta de enlace de datos que creó en el Azure Portal para la instalación de su puerta de enlace de datos. 

   1. En **Puerta de enlace de conexión**, seleccione su recurso de puerta de enlace de datos en Azure.

   1. Siga proporcionando información acerca de la conexión. Para la propiedad **Tipo de inicio de sesión**, siga el paso en función de si la propiedad está establecida en **Servidor de aplicaciones** o **Grupo**:

      * Para **Servidos de aplicaciones**, estas propiedades, que generalmente parecen opcionales, son necesarias:

        ![Captura de pantalla que muestra cómo crear una conexión para el servidor de aplicaciones de SAP.](./media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Para **Grupo**, estas propiedades, que generalmente parecen opcionales, son necesarias:

        ![Captura de pantalla que muestra cómo crear una conexión para el servidor de mensajes de SAP.](./media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

   1. Cuando haya finalizado, seleccione **Crear**.

      Azure Logic Apps configura y comprueba la conexión para asegurarse de que funciona correctamente.

1. Proporcione la ruta de acceso del artefacto para el que quiere generar el esquema.

   Puede seleccionar la acción de SAP desde el selector de archivos:

   ![Captura de pantalla que muestra cómo seleccionar una acción de SAP.](./media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   O bien, puede escribir manualmente la acción:

   ![Captura de pantalla que muestra cómo escribir manualmente una acción de SAP.](./media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Para generar esquemas de más de un artefacto, proporcione los detalles de la acción de SAP de cada artefacto, por ejemplo:

   ![Captura de pantalla que muestra cómo seleccionar "Agregar nuevo elemento".](./media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Captura de pantalla que muestra dos elementos.](./media/logic-apps-using-sap-connector/schema-generator-example.png)

   Para más información sobre la acción de SAP, consulte [Esquemas de mensaje para operaciones de IDoc](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Guarde el flujo de trabajo de la aplicación lógica. En la barra de herramientas del diseñador, seleccione **Save** (Guardar).

De forma predeterminada, se usa el establecimiento inflexible de tipos para comprobar si hay valores no válidos, para lo cual se realiza la validación de XML con respecto al esquema. Este comportamiento puede ayudarlo a detectar problemas con antelación. La opción **Escritura segura** está disponible para la compatibilidad con versiones anteriores y solo comprueba la longitud de cadena. Más información sobre la opción [Escritura segura](#safe-typing).

### <a name="test-your-workflow"></a>Prueba del flujo de trabajo

1. En la barra de herramientas del diseñador, elija **Ejecutar** para desencadenar una ejecución del flujo de trabajo de la aplicación lógica.

1. Abra la ejecución y compruebe las salidas de la acción **Generate schemas** (Generar esquemas).

   Las salidas muestran los esquemas generados para la lista de mensajes especificada.

### <a name="upload-schemas-to-an-integration-account"></a>Carga de esquemas en una cuenta de integración

Si lo desea, puede descargar o almacenar los esquemas generados en repositorios, como un blob, el almacenamiento o la cuenta de integración. Las cuentas de integración proporcionan una excelente experiencia con otras acciones de XML, por lo que en este ejemplo se muestra cómo cargar los esquemas en una cuenta de integración del mismo flujo de trabajo de la aplicación lógica mediante el conector de Azure Resource Manager.

1. En el diseñador de flujo de trabajo, en el desencadenador, seleccione **Nuevo paso**.

1. En el cuadro de búsqueda, escriba `resource manager` como filtro. Seleccione **Create or update a resource** (Crear o actualizar un recurso).

   ![Captura de pantalla que muestra cómo seleccionar la acción de Azure Resource Manager.](./media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Escriba los detalles, incluida su suscripción a Azure, el grupo de recursos de Azure y la cuenta de integración. Para agregar tokens de SAP a los campos, haga clic dentro del cuadro de esos campos y seleccione de la lista de contenido dinámico que aparece.

   1. Abra la lista **Agregar nuevo parámetro** y seleccione los campos **Ubicación** y **Propiedades**.

   1. Proporcione detalles de estos nuevos campos como se muestra en este ejemplo.

      ![Captura de pantalla que muestra cómo escribir los detalles de la acción de Azure Resource Manager.](./media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   La acción **Generate schemas** (Generar esquemas) de SAP genera esquemas como una colección, de modo que el diseñador agrega automáticamente un bucle **For each** a la acción. El siguiente es un ejemplo que muestra cómo aparece esta acción:

   ![Captura de pantalla que muestra la acción de Azure Resource Manager con un bucle "for each".](./media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > Los esquemas utilizan el formato codificado en Base64. Para cargar los esquemas en una cuenta de integración, deben descodificarse mediante la función `base64ToString()`. El siguiente es un ejemplo en el que se muestra el código del elemento `"properties"`:
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. Guarde el flujo de trabajo de la aplicación lógica. En la barra de herramientas del diseñador, seleccione **Save** (Guardar).

### <a name="test-your-workflow"></a>Prueba del flujo de trabajo

1. En la barra de herramientas del diseñador, elija **Ejecutar** para desencadenar manualmente el flujo de trabajo de la aplicación lógica.

1. Tras ejecutarse correctamente, vaya a la cuenta de integración y compruebe que existen los esquemas generados.

<a name="enable-secure-network-communications"></a>

## <a name="enable-secure-network-communications-snc"></a>Habilitación de las comunicaciones de red seguras (SNC)

Antes de empezar, asegúrese de que cumple los [requisitos previos](#prerequisites) enumerados anteriormente, que solo se aplican cuando se usa la puerta de enlace de datos y los flujos de trabajo de aplicaciones lógicas se ejecutan en instancias multiinquilino de Azure:

* Asegúrese de que la puerta de enlace de datos local está instalada en un equipo que se encuentre en la misma red que el sistema SAP.

* Para el inicio de sesión único, la puerta de enlace de datos se ejecuta con un usuario que se asigna a un usuario de SAP.

* La biblioteca de SNC que proporciona las funciones de seguridad adicionales está instalada en la misma máquina que la puerta de enlace de datos. Algunos ejemplos incluyen [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos y NTLM.

   Para habilitar SNC en las solicitudes a o desde el sistema SAP, active la casilla **Usar SNC** en la conexión de SAP y proporcione estas propiedades:

   ![Captura de pantalla que muestra cómo configurar SNC en una conexión SAP.](./media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Propiedad | Descripción |
   |----------| ------------|
   | **SNC Library Path** (Ruta de acceso a la biblioteca de SNC) | El nombre de la biblioteca de SNC o la ruta de acceso relativa a la ubicación de instalación de NCo o la ruta de acceso absoluta. Algunos ejemplos son `sapsnc.dll`, `.\security\sapsnc.dll` o `c:\security\sapsnc.dll`. |
   | **SNC SSO** (SSO de SNC) | Cuando se conecta mediante SNC, la identidad de SNC normalmente se usa para autenticar al autor de llamada. Otra opción consiste en invalidarla para que se pueda usar la información de usuario y contraseña para autenticar al autor de llamada, pero la línea siga cifrada. |
   | **SNC My Name** (Mi nombre de SNC) | En la mayoría de los casos, puede omitir esta propiedad. La solución de SNC instalada conoce normalmente su propio nombre SNC. En el caso de soluciones que admitan varias identidades, puede que deba especificar la identidad que se usará para este destino o servidor en concreto. |
   | **SNC Partner Name** (Nombre del asociado de SNC) | El nombre del SNC back-end. |
   | **SNC Quality of Protection** (Calidad de protección de SNC) | La calidad de servicio que se usará para la comunicación SNC de este destino o servidor en particular. El sistema back-end define el valor predeterminado. El valor máximo lo define el producto de seguridad usado para SNC. |
   |||

   > [!NOTE]
   > No establezca las variables de entorno SNC_LIB y SNC_LIB_64 en la máquina donde tenga la puerta de enlace de datos y la biblioteca de SNC. Si lo hace, tienen prioridad sobre el valor de la biblioteca de SNC pasado a través del conector.

## <a name="safe-typing"></a>Escritura segura

De forma predeterminada, al crear la conexión de SAP se usa el establecimiento inflexible de tipos para comprobar si hay valores no válidos; para ello, se realiza la validación de XML con respecto al esquema. Este comportamiento puede ayudarlo a detectar problemas con antelación. La opción **Escritura segura** está disponible para la compatibilidad con versiones anteriores y solo comprueba la longitud de cadena. Si elige **Escritura segura**, los tipos DATS y TIMS en SAP se tratan como cadenas en lugar de como sus equivalentes XML, `xs:date` y `xs:time`, donde `xmlns:xs="http://www.w3.org/2001/XMLSchema"`. La escritura segura afecta al comportamiento de generación de todos los esquemas, al mensaje de envío de "se ha enviado" la carga y de "se ha recibido" la respuesta y al desencadenador.

Cuando se usa el establecimiento inflexible de tipos (la opción **Escritura segura** no está habilitada), el esquema asigna los tipos DATS y TIMS a tipos XML más sencillos:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Cuando se envían mensajes con establecimiento inflexible de tipos, la respuesta DATS y TIMS se adapta al formato del tipo XML coincidente:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Cuando la opción **Escritura segura** está habilitada, el esquema solo asigna los tipos DATS y TIMS a campos de cadena XML con restricciones de longitud, por ejemplo:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

Cuando se envían mensajes con **Escritura segura** habilitada, la respuesta DATS y TIMS se parece a la de este ejemplo:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="send-sap-telemetry-foron-premises-data-gateway-to-azure-application-insights"></a>Envío de datos de telemetría de SAP sobre la puerta de enlace de datos local a Azure Application Insights

Con la actualización de agosto de 2021 para la puerta de enlace de datos local, las operaciones del conector de SAP pueden enviar datos de telemetría desde la biblioteca cliente de SAP y seguimientos desde el adaptador de SAP de Microsoft a [Application Insights](../azure-monitor/app/app-insights-overview.md), que es una funcionalidad de Azure Monitor. Estos datos de telemetría incluyen principalmente los datos siguientes:

* Métricas y seguimientos basados en métricas y monitores de SAP NCo.

* Seguimientos del adaptador de SAP de Microsoft.

### <a name="metrics-and-traces-from-sap-client-library"></a>Métricas y seguimientos de la biblioteca cliente de SAP

Las *métricas* son valores numéricos que pueden o no variar durante un período de tiempo, en función del uso y la disponibilidad de los recursos en la puerta de enlace de datos local. Puede usar estas métricas para comprender mejor el estado del sistema y crear alertas sobre las actividades siguientes:

* Si el estado del sistema está en declive.

* Eventos inusuales.

* Carga pesada en el sistema.

Esta información se envía a la tabla de Application Insights, `customMetrics`. De manera predeterminada, las métricas se envían a intervalos de 30 segundos.

Las métricas y los seguimientos de SAP NCo se basan en métricas de SAP NCo, específicamente en las siguientes clases de NCo:

* RfcDestinationMonitor.

* RfcConnectionMonitor.

* RfcServerMonitor.

* RfcRepositoryMonitor.

Para más información sobre las métricas que proporciona cada clase, revise la [documentación de SAP NCo (se requiere inicio de sesión)](https://support.sap.com/en/product/connectors/msnet.html#section_512604546).

Los *seguimientos* incluyen información de texto que se usa con las métricas. Esta información se envía a la tabla de Application Insights, llamada `traces`. De manera predeterminada, los seguimientos se envían a intervalos de 10 minutos.

### <a name="set-up-sap-telemetry-for-application-insights"></a>Configuración de la telemetría de SAP para Application Insights​

Para poder enviar datos de telemetría de SAP sobre la instalación de la puerta de enlace a Application Insights, debe haber creado y configurado el recurso de Application Insights. Para más información, revise la siguiente documentación:

* [Creación de un recurso de Application Insights (clásico)](../azure-monitor/app/create-new-resource.md)

* [Recursos de Application Insights basados en áreas de trabajo](../azure-monitor/app/create-workspace-resource.md)

Para habilitar el envío de datos de telemetría de SAP a Application Insights, siga estos pasos:

1. Descargue el paquete NuGet para **Microsoft.ApplicationInsights.EventSourceListener.dll** desde esta ubicación: [https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/2.14.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/2.14.0).

1. Agregue el archivo descargado al directorio de instalación de la puerta de enlace de datos local.

1. En el directorio de instalación de la puerta de enlace de datos local, compruebe que el archivo **Microsoft.ApplicationInsights.dll** tenga el mismo número de versión que el archivo **Microsoft.ApplicationInsights.EventSourceListener.dll** que agregó. Actualmente, la puerta de enlace usa la versión 2.14.0.

1. En el archivo **ApplicationInsights.config**, agregue la [clave de instrumentación de Application Insights](../azure-monitor/app/app-insights-overview.md#how-does-application-insights-work); para ello, quite la marca de comentario de la línea que tiene el elemento `<InstrumentationKey></Instrumentation>`. Reemplace el marcador de posición, *your-Application-Insights-instrumentation-key*, por la clave, por ejemplo:

      ```xml
      <?xml version="1.0" encoding="utf-8"?>
      <ApplicationInsights schemaVersion="2014-05-30" xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
         <!-- Uncomment this element and insert your Application Insights key to receive ETW telemetry about your gateway <InstrumentationKey>*your-instrumentation-key-placeholder*</InstrumentationKey> -->
         <TelemetryModules>
            <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule, Microsoft.ApplicationInsights">
               <IsHeartbeatEnabled>false</IsHeartbeatEnabled>
            </Add>
            <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
               <Sources>
                  <Add Name="Microsoft-LobAdapter" Level="Verbose" />
               </Sources>
            </Add>
         </TelemetryModules>
      </ApplicationInsights>
      ```

1. En el archivo **ApplicationInsights.config**, puede cambiar el valor de `Level` en los seguimientos necesarios para las operaciones del conector de SAP según sus requisitos, por ejemplo:

   ```xml
   <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
         <Add Name="Microsoft-LobAdapter" Level="Verbose" />
      </Sources>
   </Add>
   ```

   Para más información, revise la siguiente documentación:

   * Valores de `Level`: [enumeración EventLevel](/dotnet/api/system.diagnostics.tracing.eventlevel)

   * [Seguimiento de EventSource](../azure-monitor/app/configuration-with-applicationinsights-config.md#eventsource-tracking)

   * [Eventos EventSource](../azure-monitor/app/asp-net-trace-logs.md#use-eventsource-events)

1. Después de aplicar los cambios, reinicie el servicio de la puerta de enlace de datos local.

### <a name="review-metrics-in-application-insights"></a>Revisión de métricas en Application Insights

Después de ejecutar las operaciones de SAP en el flujo de trabajo de la aplicación lógica, puede revisar los datos de telemetría que se enviaron a Application Insights.

1. Abra el recurso de Application Insights en Azure Portal.

1. En el menú de recursos, en **Supervisión**, seleccione **Registros**.

   En la captura de pantalla siguiente se muestra Azure Portal con Application Insights, que está abierto en el panel **Registros**:

   [![Captura de pantalla que muestra Azure Portal con Application Insights abierto en el panel "Registros" para crear consultas.](./media/logic-apps-using-sap-connector/application-insights-query-panel.png)](./media/logic-apps-using-sap-connector/application-insights-query-panel.png#lightbox)

1. En el panel **Registros**, puede crear una [consulta](/azure/data-explorer/kusto/query/) mediante el [lenguaje de consulta de Kusto (KQL)](/azure/data-explorer/kusto/concepts/) que se base en sus requisitos específicos.

   Puede usar un patrón de consulta similar a la consulta de ejemplo siguiente:

   ```Kusto
   customMetrics
   | extend DestinationName = tostring(customDimensions["DestinationName"])
   | extend MetricType = tostring(customDimensions["MetricType"])
   | where customDimensions contains "RfcDestinationMonitor"
   | where name contains "MaxUsedCount"
   ```

1. Después de ejecutar la consulta, revise los resultados.

   En la captura de pantalla siguiente, se muestra la tabla de resultados de métricas de la consulta de ejemplo:

   [![Captura de pantalla que muestra Application Insights con la tabla de resultados de métricas.](./media/logic-apps-using-sap-connector/application-insights-metrics.png)](./media/logic-apps-using-sap-connector/application-insights-metrics.png#lightbox)

   * **MaxUsedCount** es "el número máximo de conexiones de cliente que el destino supervisado usó simultáneamente", como se describe en la [documentación de SAP NCo (se requiere inicio de sesión)](https://support.sap.com/en/product/connectors/msnet.html#section_512604546). Puede usar este valor para conocer el número de conexiones abiertas simultáneamente.

   * La columna **valueCount** muestra **2** para cada lectura, porque las métricas se generan a intervalos de 30 segundos y Application Insights agrega estas métricas por minuto.

   * La columna **DestinationName** contiene una cadena de caracteres que es el nombre interno del adaptador de SAP de Microsoft.

     Para comprender mejor este destino de llamada de función remota (RFC), use este valor con `traces`, por ejemplo:

     ```Kusto
     customMetrics
     | extend DestinationName = tostring(customDimensions["DestinationName"])
     | join kind=inner (traces
        | extend DestinationName = tostring(customDimensions["DestinationName"]),
        AppServerHost = tostring(customDimensions["AppServerHost"]),
        SncMode = tostring(customDimensions["SncMode"]),
        SapClient = tostring(customDimensions["Client"])
        | where customDimensions contains "RfcDestinationMonitor"
        )
        on DestinationName , $left.DestinationName == $right.DestinationName
     | where customDimensions contains "RfcDestinationMonitor"
     | where name contains "MaxUsedCount"
     | project AppServerHost, SncMode, SapClient, name, valueCount, valueSum, valueMin, valueMax
     ```

También puede crear gráficos o alertas de métricas mediante esas funcionalidades en Application Insights, por ejemplo:

[![Captura de pantalla que muestra Application Insights con los resultados en formato de gráfico.](./media/logic-apps-using-sap-connector/application-insights-metrics-chart.png)](./media/logic-apps-using-sap-connector/application-insights-metrics-chart.png#lightbox)

### <a name="traces-from-microsoft-sap-adapter"></a>Seguimientos del adaptador de SAP de Microsoft

Puede usar los seguimientos enviados desde el adaptador de SAP de Microsoft para el análisis posterior al problema y para encontrar los errores internos del sistema existentes que podrían o no aparecer en las operaciones del conector de SAP. Estos seguimientos tienen el elemento `message` establecido en `"n\a"` porque proceden de un marco de origen de eventos que es anterior a Application Insights, por ejemplo:

```Kusto
traces
| where message == "n/a"
| where severityLevel > 0
| extend ActivityId = tostring(customDimensions["ActivityId"])
| extend fullMessage = tostring(customDimensions["fullMessage"])
| extend shortMessage = tostring(customDimensions["shortMessage"])
| where ActivityId contains "8ad5952b-371e-4d80-b355-34e28df9b5d1"
```

En la captura de pantalla siguiente, se muestra la tabla de resultados de seguimientos de la consulta de ejemplo:

[![Captura de pantalla que muestra Application Insights con la tabla de resultados de seguimientos.](./media/logic-apps-using-sap-connector/application-insights-traces.png)](./media/logic-apps-using-sap-connector/application-insights-traces.png#lightbox)

## <a name="advanced-scenarios"></a>Escenarios avanzados

### <a name="change-language-headers"></a>Cambio de los encabezados de idioma

Cuando se conecta a SAP desde Logic Apps, el idioma predeterminado de la conexión es el inglés. Puede establecer el idioma de la conexión mediante el [encabezado HTTP estándar `Accept-Language`](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.4) con las solicitudes entrantes.

> [!TIP]
> La mayoría de los exploradores web agregan un encabezado `Accept-Language` basado en la configuración del usuario. El explorador web aplica este encabezado cuando se crea una nueva conexión de SAP en el diseñador de flujo de trabajo, se actualiza la configuración del explorador web para usar el lenguaje que prefiera o se crea la conexión de SAP mediante Azure Resource Manager en lugar del diseñador de flujo de trabajo.

Por ejemplo, puede enviar una solicitud con el encabezado `Accept-Language` al flujo de trabajo de la aplicación lógica mediante el desencadenador de **solicitud**. Todas las acciones del flujo de trabajo de la aplicación lógica reciben el encabezado. A continuación, SAP usa los idiomas especificados en sus mensajes del sistema, como los mensajes de error de BAPI.

Los parámetros de conexión de SAP para un flujo de trabajo de aplicación lógica no tienen una propiedad de idioma. Por lo tanto, si usa el encabezado `Accept-Language`, es posible que reciba el siguiente error: **Please check your account info and/or permissions and try again** (Compruebe la información de la cuenta o los permisos e inténtelo de nuevo). En este caso, compruebe los registros de errores del componente de SAP en su lugar. En realidad, el error se produce en el componente de SAP que usa el encabezado, por lo que podría obtener uno de estos mensajes de error:

* `"SAP.Middleware.Connector.RfcLogonException: Select one of the installed languages"`

* `"SAP.Middleware.Connector.RfcAbapMessageException: Select one of the installed languages"`

### <a name="confirm-transaction-explicitly"></a>Confirmación de la transacción explícitamente

Cuando se envían transacciones a SAP desde Logic Apps, este intercambio se produce en dos pasos, tal como se describe en el documento de SAP [Transactional RFC Server Programs](https://help.sap.com/doc/saphelp_nwpi71/7.1/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true) (Programas del servidor RFC transaccional). De manera predeterminada, la acción **Enviar a SAP** controla ambos pasos para la transferencia de la función y la confirmación de la transacción en una sola llamada. El conector de SAP ofrece la opción de desacoplar estos pasos. Puede enviar un IDoc y, en lugar de confirmar automáticamente la transacción, puede usar la acción explícita **\[IDOC] Confirmar id. de transacción**.

Esta capacidad de desacoplar la confirmación del id. de transacción resulta útil cuando no quiere duplicar las transacciones en SAP, por ejemplo, en escenarios en los que se pueden producir errores debido a causas como problemas de red. Al confirmar el id. de la transacción por separado, la transacción solo se completa una vez en el sistema SAP.

Este es un ejemplo que muestra este patrón:

1. Cree una aplicación lógica en blanco y agregue el desencadenador de solicitud.

1. En el conector de SAP, agregue la acción **\[IDOC] Send document to SAP**  ([IDOC] Enviar documento a SAP). Proporcione los detalles del IDoc que envía al sistema SAP.

1. Para confirmar explícitamente el ID de la transacción en un paso separado, en el campo **Confirmar TID**, seleccione **No**. Para el campo opcional **GUID del id. de transacción**, puede especificar manualmente el valor o hacer que el conector genere y devuelva automáticamente este GUID en la respuesta de la acción **\[[IDOC] Send document to SAP** ([IDOC] Enviar documento a SAP).

   ![Captura de pantalla que muestra las propiedades de la acción "[IDOC] Send document to SAP" ([IDOC] Enviar documento a SAP).](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. Para confirmar explícitamente el id. de transacción, agregue la acción **\[IDOC] Confirmar id. de transacción**, asegurándose de [evitar el envío de IDoc duplicados a SAP](#avoid-sending-duplicate-idocs). Haga clic en el cuadro **Id. de transacción** para que aparezca la lista de contenido dinámico. En esa lista, seleccione el valor de **Id. de transacción** que se devuelve de la acción **\[IDOC] Send document to SAP**  ([IDOC] Enviar documento a SAP).

   ![Captura de pantalla que muestra la acción "Confirmar id. de transacción".](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   Después de ejecutar este paso, la transacción actual se marca como completa en ambos extremos, en el lado del conector de SAP y en el lado del sistema SAP.

#### <a name="avoid-sending-duplicate-idocs"></a>Evitar el envío de IDoc duplicados

Si experimenta un problema con IDoc duplicados que se envían a SAP desde el flujo de trabajo de la aplicación lógica, siga estos pasos para crear una variable de cadena que sirva como el identificador de la transacción de IDoc. La creación de este identificador de transacción ayuda a evitar transmisiones de red duplicadas cuando hay problemas, como interrupciones temporales, problemas de red o confirmaciones perdidas.

> [!NOTE]
> Los sistemas SAP olvidan un identificador de transacción después de un tiempo especificado, o 24 horas de forma predeterminada. Por consiguiente, SAP nunca no puede confirmar un identificador de transacción si se desconoce el id. o el GUID.
> Si se produce un error en la confirmación de un identificador de transacción, este error indica que hubo un problema en la comunicación con el sistema SAP antes de que SAP pudiera reconocer la confirmación.

1. En el diseñador de flujo de trabajo, agregue la acción **Inicializar la variable** al flujo de trabajo de la aplicación lógica.

1. En el editor de la acción **Inicializar la variable**, defina las siguientes configuraciones. A continuación, guarde los cambios.

   1. Para **Nombre**, escriba un nombre para la variable. Por ejemplo, `IDOCtransferID`.

   1. Para **Tipo**, seleccione **Cadena** como el tipo de variable.

   1. Para **Valor**, seleccione el cuadro de texto **Escriba el valor inicial** para abrir el menú de contenido dinámico.

   1. Seleccione la pestaña **Expresiones**. En la lista de funciones, escriba la función `guid()`.

   1. Seleccione **Aceptar** para guardar los cambios. El campo **Valor** se establece ahora en la función `guid()`, que genera un GUID.

1. Después de la acción **Inicializar variable,** agregue la acción **\[IDOC] Send document to SAP** ([IDOC] Enviar documento a SAP).

1. En el editor de la acción **\[IDOC] Send document to SAP** ([IDOC] Enviar documento a SAP), defina la configuración siguiente. A continuación, guarde los cambios.

   1. Para **Tipo de IDOC** seleccione el tipo de mensaje y, en **Mensaje de entrada de IDOC**, especifique el mensaje.

   1. Para **Versión de SAP**, seleccione los valores de la configuración de SAP.

   1. Para **Versión de tipos de registro**, seleccione los valores de la configuración de SAP.

   1. Para **Confirmar TID**, seleccione **No**.

   1. Seleccione **Add new parameter list** (Agregar nueva lista de parámetros) > **GUID del id. de transacción**.

   1. Seleccione el cuadro de texto para abrir el menú de contenido dinámico. En la pestaña **Variables**, seleccione el nombre de la variable que creó, por ejemplo, `IDOCtransferID`.

1. En la barra de título de la acción **\[IDOC] Send document to SAP** ([IDOC] Enviar documento a SAP), seleccione **...**  > **Configuración**.

   Para **Directiva de reintentos**, se recomienda seleccionar **Valor predeterminado** &gt; **Listo**. Sin embargo, en su lugar, puede configurar una directiva personalizada para sus necesidades específicas. En el caso de las directivas personalizadas, se recomienda configurar al menos un reintento para superar las interrupciones temporales de la red.

1. Después de la acción **\[IDOC] Send document to SAP** ([IDOC] Enviar documento a SAP), agregue la acción **\[IDOC] Confirmar id. de transacción**.

1. En el editor de la acción **\[IDOC] Confirmar id. de transacción**, defina la configuración siguiente. A continuación, guarde los cambios.

1. Para **Id. de transacción**, escriba de nuevo el nombre de la variable. Por ejemplo, `IDOCtransferID`.

1. Opcionalmente, valide la desduplicación en el entorno de prueba. Repita la acción **\[IDOC] Send document to SAP** ([IDOC] Enviar documento a SAP) con el mismo **Id. de transacción** que usó en el paso anterior.

   Cuando se envía el mismo IDoc dos veces, puede validar que SAP pueda identificar la duplicación de la llamada tRFC y resolver las dos llamadas a un solo mensaje entrante de IDoc.

## <a name="known-issues-and-limitations"></a>Limitaciones y problemas conocidos

Estos son los problemas y limitaciones actualmente conocidos para el conector de SAP (que no sea ISE) administrado:

* En general, el desencadenador de SAP no admite los clústeres de puerta de enlace de datos. En algunos casos de conmutación por error, el nodo de puerta de enlace de datos que se comunica con el sistema SAP puede diferir del nodo activo, lo que produce un comportamiento inesperado.

  * En los escenarios de envío, se admiten clústeres de puerta de enlace de datos en modo de conmutación por error.

  * Los clústeres de puerta de enlace de datos en modo de equilibrio de carga no se admiten con las [acciones de SAP](#actions) con estado. Estas acciones incluyen **\[BAPI - RFC] Crear sesión con estado**, **\[BAPI] Confirmar transacción**, **\[BAPI] Revertir transacción**, **\[BAPI - RFC] Cerrar sesión con estado**, y todas las acciones que especifican un valor de **Id. de sesión**. Las comunicaciones con estado deben permanecer en el mismo nodo de clúster de puerta de enlace de datos.

  * En el caso de las acciones de SAP con estado, use la puerta de enlace de datos en modo no clúster o en un clúster configurado solo para la conmutación por error.

* El conector SAP no admite actualmente las cadenas de enrutador SAP. La puerta de enlace de datos local debe existir en la misma LAN que el sistema SAP al que quiere conectarse.

* En la acción **\[BAPI] Call method in SAP** ([BAPI] Llamar al método en SAP), la característica de confirmación automática no confirmará los cambios de BAPI si existe al menos una advertencia en el objeto **CallBapiResponse** que devuelve la acción. Para confirmar los cambios de BAPI a pesar de las advertencias, cree una sesión de forma explícita con la acción **\[BAPI - RFC] Crear sesión con estado**, deshabilite la característica de confirmación automática en la acción **\[BAPI] Call method in SAP** ([BAPI] Llamar al método en SAP) y llame a la acción **\[BAPI] Confirmar transacción** en su lugar.

* En el caso de las [aplicaciones lógicas de un entorno de servicio de integración (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md), la versión con la etiqueta ISE de este conector usa en su lugar los [límites de mensajes de ISE](logic-apps-limits-and-config.md#message-size-limits).

## <a name="connector-reference"></a>Referencia de conectores

Para obtener más información sobre el conector de SAP, consulte la [referencia del conector](/connectors/sap/). Puede encontrar detalles sobre los límites, parámetros y devoluciones del conector, los desencadenadores y las acciones de SAP.

### <a name="triggers"></a>Desencadenadores

:::row:::
    :::column span="1":::
        [**Cuando se recibe un mensaje de SAP**](/connectors/sap/#when-a-message-is-received)
    :::column-end:::
    :::column span="3":::
        Cuando se reciba un mensaje de SAP, hace algo.
    :::column-end:::
:::row-end:::

### <a name="actions"></a>Acciones

:::row:::
    :::column span="1":::
        [ **[BAPI - RFC] Cerrar sesión con estado**](/connectors/sap/#[bapi---rfc]-close-stateful-session-(preview))
    :::column-end:::
    :::column span="3":::
        Permite cerrar una sesión de conexión con estado al sistema SAP.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[BAPI - RFC] Crear una sesión con estado**](/connectors/sap/#[bapi---rfc]-create-stateful-session-(preview))
    :::column-end:::
    :::column span="3":::
        Permite crear una sesión de conexión con estado al sistema SAP.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[BAPI] Llamar al método en SAP**](/connectors/sap/#[bapi]-call-method-in-sap-(preview))
    :::column-end:::
    :::column span="3":::
        Permite llamar al método BAPI en el sistema SAP.
        \
        \
        Debe usar los parámetros siguientes con la llamada:     \
        \
        **Objeto de negocios** (`businessObject`), que es un menú desplegable que permite búsquedas.
        \
        \
        **Método** (`method`), que rellena los métodos disponibles después de seleccionar **Business Object**. Los métodos disponibles varían en función del **objeto de negocios** seleccionado.
        \
        \
        **Parámetros de BAPI de entrada** (`body`), en los que se llama al documento XML que contiene los valores de parámetro de entrada del método BAPI para la llamada, o al URI de Storage Blob que contiene los parámetros de BAPI.
        \
        \
        Para obtener ejemplos detallados de cómo usar la acción **[BAPI] Call method in SAP** (Llamar al método en SAP [BAPI]), consulte los [Ejemplos XML para solicitudes de BAPI](#xml-samples-for-bapi-requests).
        \
        Si usa el diseñador de flujo de trabajo para editar la solicitud de BAPI, puede usar las siguientes funciones de búsqueda:     \
        \
        Seleccione un objeto en el diseñador para ver un menú desplegable de los métodos disponibles.
        \
        \
        Filtre los tipos de objetos comerciales por palabra clave mediante la lista de búsqueda que la llamada API de BAPI proporciona.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[BAPI] Confirmar transacción**](/connectors/sap/#[bapi]-commit-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        Permite confirmar la transacción de BAPI para la sesión.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[BAPI] Revertir transacción**](/connectors/sap/#[bapi]-roll-back-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        Permite revertir la transacción de BAPI para la sesión.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[IDOC - RFC] Confirmar id. de transacción**](/connectors/sap/#[idoc---rfc]-confirm-transaction-id-(preview))
    :::column-end:::
    :::column span="3":::
        Envía la confirmación del identificador de transacción a SAP.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[IDOC] Get IDOC list for transaction**](/connectors/sap/#[idoc]-get-idoc-list-for-transaction-(preview)) (Obtener la lista de IDOC para la transacción)
    :::column-end:::
    :::column span="3":::
        Permite obtener una lista de IDoc para la transacción por identificador de sesión o identificador de transacción.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[IDOC] Get IDOC status**](/connectors/sap/#[idoc]-get-idoc-status-(preview)) (Obtener estado de IDOC)
    :::column-end:::
    :::column span="3":::
        Permite obtener el estado de un IDoc.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[IDOC] Send document to SAP**](/connectors/sap/#[idoc]-send-document-to-sap-(preview)) (Enviar documento a SAP)
    :::column-end:::
    :::column span="3":::
        Envía el mensaje de IDoc al servidor de SAP.
        \
        \
        Debe usar los parámetros siguientes con la llamada:     \
        \
        **Tipo de IDoc con extensión opcional** (`idocType`), que es un menú desplegable que permite búsquedas.
        \
        \
        **Mensaje de IDoc de entrada** (`body`), en el que se llama al documento XML que contiene la carga de IDoc, o al URI de Storage Blob que contiene el documento XML de IDoc. Este documento debe cumplir el esquema XML de IDoc de SAP de acuerdo con la documentación de IDoc WE60 o el esquema generado para el identificador URI de acción de IDoc de SAP coincidente.
        \
        \
        El parámetro opcional de **versión de lanzamiento de SAP** (`releaseVersion`) rellena los valores después de seleccionar el tipo de IDoc y depende del tipo de IDoc seleccionado.
        \
        \
        Para obtener ejemplos detallados de cómo usar la acción Enviar IDoc, consulte el [tutorial para enviar mensajes de IDoc al servidor de SAP](#send-idoc-messages-to-sap-server).
        \
        \
        Para saber cómo usar el parámetro opcional **Confirmar TID** (`confirmTid`), consulte el [tutorial para confirmar la transacción de forma explícita](#confirm-transaction-explicitly).
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[RFC] Add RFC to transaction**](/connectors/sap/#[rfc]-add-rfc-to-transaction-(preview)) (Agregar RFC a la transacción)
    :::column-end:::
    :::column span="3":::
        Permite agregar una llamada de RFC a la transacción.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[RFC] Call function in SAP**](/connectors/sap/#[rfc]-call-function-in-sap-(preview)) (Llamar a función en SAP)
    :::column-end:::
    :::column span="3":::
        Permite llamar a una operación de RFC (sRFC, tRFC o qRFC) en el sistema SAP.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[RFC] Confirmar transacción**](/connectors/sap/#[rfc]-commit-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        Permite confirmar la transacción de RFC para la sesión o cola.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[RFC] Crear transacción**](/connectors/sap/#[rfc]-create-transaction-(preview))
    :::column-end:::
    :::column span="3":::
        Permite crear una nueva transacción por identificador o nombre de cola. Si la transacción existe, obtenga los detalles.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [ **[RFC] Get transaction**](/connectors/sap/#[rfc]-get-transaction-(preview)) (Obtener transacción)
    :::column-end:::
    :::column span="3":::
        Permite obtener los detalles de una transacción por identificador o nombre de cola. Cree una nueva transacción si no existe ninguna.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [**Generar esquemas**](/connectors/sap/#generate-schemas)
    :::column-end:::
    :::column span="3":::
        generación de esquemas de artefactos SAP para IDOC, BAPI o RFC.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [**Read SAP table**](/connectors/sap/#read-sap-table-(preview)) (Leer tabla de SAP)
    :::column-end:::
    :::column span="3":::
        Permite leer una tabla de SAP.
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="1":::
        [**Enviar mensaje a SAP**](/connectors/sap/#send-message-to-sap)
    :::column-end:::
    :::column span="3":::
        Envía todos los tipos de mensajes (RFC, BAPI, IDoc) a SAP.
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Pasos siguientes

* [Conéctese a sistemas locales](logic-apps-gateway-connection.md) desde Azure Logic Apps.
* Aprenda a validar, transformar y usar otras operaciones de mensaje con [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).
* Obtenga más información sobre otros [conectores de Logic Apps](../connectors/apis-list.md)
