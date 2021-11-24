---
title: Implementación de la supervisión de amenazas continua de SAP | Microsoft Docs
description: Aprenda a implementar la solución Microsoft Sentinel para entornos de SAP.
author: batamig
ms.author: bagol
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: dfafd0ccdb37e2f716bf50195ce19ad06486eb50
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712069"
---
#  <a name="deploy-sap-continuous-threat-monitoring-preview"></a>Implementación de la supervisión de amenazas continua de SAP (versión preliminar)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

En este artículo se describe paso a paso el proceso de implementación de la supervisión de amenazas continua de Microsoft Sentinel para SAP.

> [!IMPORTANT]
> La solución de SAP de Microsoft Sentinel está actualmente en VERSIÓN PRELIMINAR. En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>

## <a name="overview"></a>Información general

Las [soluciones de Microsoft Sentinel](sentinel-solutions.md) incluyen contenido de seguridad agrupado, tal como detecciones de amenazas, libros y listas de reproducción. Con estas soluciones, puede incorporar contenido de seguridad de Microsoft Sentinel para un conector de datos específico mediante un único proceso.

Utilizando el conector de datos de Microsoft Sentinel para SAP, puede supervisar los sistemas SAP en busca de amenazas sofisticadas en el nivel empresarial y de aplicación.

El conector de datos de SAP transmite 14 registros de aplicación desde todo el entorno del sistema SAP. El conector de datos recopila los registros de Advanced Business Application Programming (ABAP) a través de llamadas RFC de NetWeaver y de datos de almacenamiento de archivos a través de la interfaz de control de OSSAP. El conector de datos de SAP se añade a la capacidad de Microsoft Sentinel para supervisar la infraestructura subyacente de SAP.

Para ingerir registros de SAP en Microsoft Sentinel, debe tener instalado el conector de datos de Microsoft Sentinel para SAP en el entorno de SAP. Se recomienda usar un contenedor de Docker en una máquina virtual de Azure para la implementación, tal como se indica en este tutorial.

Una vez implementado el conector de datos de SAP, implemente el contenido de seguridad de la solución de SAP para obtener información sobre el entorno de SAP de su organización y mejorar la funcionalidad de todas las operaciones de seguridad relacionadas.

En este artículo, aprenderá a:

> [!div class="checklist"]
> * Preparar el sistema SAP para la implementación del conector de datos correspondiente.
> * Usar un contenedor de Docker y una máquina virtual (VM) de Azure para implementar el conector de datos de SAP.
> * Implementar el contenido de seguridad de la solución SAP en Microsoft Sentinel.

> [!NOTE]
> Se requieren pasos adicionales para implementar el conector de datos de SAP a través de una conexión de red segura (SNC). Para obtener información adicional, consulte [Implementación del conector de datos SAP de Microsoft Sentinel con SNC](sap-solution-deploy-snc.md).
>
## <a name="prerequisites"></a>Requisitos previos

Para implementar el contenido de seguridad de Microsoft Sentinel y el conector de datos de SAP, tal y como se describe en este tutorial, debe cumplir los siguientes requisitos previos:

| Área | Descripción |
| --- | --- |
|**Requisitos previos de Azure** | **Acceso a Microsoft Azure Sentinel**. Anote el identificador y la clave del área de trabajo de Microsoft Sentinel que se usará en este tutorial cuando se [implemente el conector de datos de SAP](#deploy-your-sap-data-connector). <br><br>Para ver estos detalles en Microsoft Sentinel, vaya a **Configuración** > **Configuración del área de trabajo** > **Agents management** (Administración de agentes). <br><br>**Capacidad para crear recursos de Azure**. Para más información, consulte la [documentación de Azure Resource Manager](../azure-resource-manager/management/manage-resources-portal.md). <br><br>**Acceso al almacén de claves de Azure**. En este tutorial se describen los pasos recomendados para usar el almacén de claves de Azure para almacenar las credenciales. Para más información, consulte la [documentación de Azure Key Vault](../key-vault/index.yml). |
|**Requisitos previos del sistema** | **Software**. El script de implementación del conector de datos de SAP instala automáticamente los requisitos previos de software. Para más información, consulte [Software instalado automáticamente](#automatically-installed-software). <br><br> **Conectividad del sistema**. Asegúrese de que la máquina virtual que actúa como host del conector de datos de SAP tiene acceso a: <br>- Microsoft Sentinel <br>- Su almacén de claves de Azure <br>- El host del entorno de SAP, a través de los siguientes puertos TCP: *32xx*, *5xx13* y *33xx*, donde *xx* es el número de instancia de SAP. <br><br>Asegúrese de que también tiene una cuenta de usuario de SAP para acceder a la página de descarga del software de SAP.<br><br>**Arquitectura del sistema**. La solución SAP se implementa en una máquina virtual como un contenedor de Docker y cada cliente de SAP requiere su propia instancia de contenedor. Para obtener recomendaciones de dimensionamiento, vea [Dimensionamiento recomendado de máquinas virtuales recomendado](sap-solution-detailed-requirements.md#recommended-virtual-machine-sizing). <br>La máquina virtual y el área de trabajo de Microsoft Sentinel pueden estar en diferentes suscripciones de Azure e incluso en inquilinos de Azure AD diferentes.|
|**Requisitos previos de SAP** | **Versiones de SAP admitidas**. Se recomienda usar [SAP_BASIS, versión 750 SP13](https://support.sap.com/en/my-support/software-downloads/support-package-stacks/product-versions.html#:~:text=SAP%20NetWeaver%20%20%20%20SAP%20Product%20Version,%20%20SAPKB710%3Cxx%3E%20%207%20more%20rows) o versiones posteriores. <br><br>Seleccione los pasos de este tutorial para proporcionar instrucciones alternativas si trabaja con una versión de SAP anterior a [SAP_BASIS 740](https://support.sap.com/en/my-support/software-downloads/support-package-stacks/product-versions.html#:~:text=SAP%20NetWeaver%20%20%20%20SAP%20Product%20Version,%20%20SAPKB710%3Cxx%3E%20%207%20more%20rows).<br><br> **Detalles del sistema SAP**. Tome nota de los siguientes detalles del sistema SAP para usarlos en este tutorial:<br>- Dirección IP del sistema SAP<br>- Número del sistema SAP como, por ejemplo, `00`<br>- Identificador del sistema SAP, del sistema SAP NetWeaver (por ejemplo, `NPL`) <br>- Identificador de cliente de SAP como, por ejemplo, `001`<br><br>**Acceso a la instancia de SAP NetWeaver**. El acceso a las instancias de SAP debe usar una de las siguientes opciones: <br>- [Usuario y contraseña de SAP ABAP](#configure-your-sap-system). <br>- Un usuario con un certificado X509, mediante SAP CRYPTOLIB PSE. Esta opción puede requerir pasos manuales expertos.<br><br>**Soporte técnico del equipo de SAP**.  Necesitará el soporte técnico del equipo de SAP para que le ayuden a asegurarse de que el sistema SAP esté [configurado correctamente](#configure-your-sap-system) para la implementación de la solución. |
| | |

### <a name="automatically-installed-software"></a>Software instalado automáticamente

El [script de implementación del conector de datos de SAP](#deploy-your-sap-data-connector) instala el siguiente software en la máquina virtual con privilegios *sudo* (raíz):

- [Unzip](https://www.microsoft.com/en-us/p/unzip/9mt44rnlpxxt?activetab=pivot:overviewtab)
- [NetCat](https://sectools.org/tool/netcat/)
- [Python 3.6 o versiones posteriores](https://www.python.org/downloads/)
- [Python 3-pip](https://pypi.org/project/pip/)
- [Docker](https://www.docker.com/)

## <a name="configure-your-sap-system"></a>Configuración del sistema SAP

En este procedimiento se describe cómo asegurarse de que el sistema SAP tenga instalados los requisitos previos correctos y esté configurado para la implementación del conector de datos de Microsoft Sentinel para SAP.

> [!IMPORTANT]
> Realice este procedimiento junto con el equipo de SAP para garantizar las configuraciones correctas.
>

**Para configurar el sistema SAP para el conector de datos:**

1. Asegúrese de que las siguientes notas de SAP están implementadas en el sistema, en función de la versión:

    | Versiones &nbsp;BASIS&nbsp; de SAP | Nota requerida |
    | --- | --- |
    | - 750 SP01 a SP12<br>- 751 SP01 a SP06<br>- 752 SP01 a SP03 | 2641084: Acceso de lectura estandarizado para los datos de registro de auditoría de seguridad |
    | - 700 a 702<br>- 710 a 711, 730, 731, 740 y 750 | 2173545: CD: CHANGEDOCUMENT_READ_ALL |
    | - 700 a 702<br>- 710 a 711, 730, 731 y 740<br>- 750 a 752 | 2502336: CD (documento de cambios): RSSCD100: solo lectura desde el archivo, no desde la base de datos |
    | | |

   Las versiones posteriores no requieren las notas adicionales. Para obtener información adicional, consulte el [sitio de Launchpad de soporte técnico de SAP](https://support.sap.com/en/index.html). Inicie sesión con una cuenta de usuario de SAP.

1. Descargue e instale una de las siguientes solicitudes de cambio de SAP desde el [repositorio de GitHub de Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/CR):

    - **Versión SAP 750 o posterior**: instale la solicitud de cambio de SAP *144 (NPLK900144)*
    - **Versión SAP 740**: instale la solicitud de cambio de SAP *146 (NPLK900146)*

    Al realizar este paso, asegúrese de usar el modo binario para transferir los archivos al sistema SAP y de usar el código de transacción de SAP **STMS_IMPORT**.

    > [!NOTE]
    > En el área **Import Options** (Opciones de importación) de SAP, puede ver la opción **Ignore Invalid Component Version** (Ignorar versión del componente no válida). Si se muestra, seleccione esta opción antes de continuar.
    >

1. Para importar la solicitud de cambio *14 (NPLK900163)* de SAP, cree un rol de SAP llamado **/MSFTSEN/SENTINEL_CONNECTOR**. Use el código de transacción de SAP **STMS_IMPORT**.

    Compruebe que el rol se crea con los permisos necesarios, como:

    :::image type="content" source="media/sap/required-sap-role-authorizations.png" alt-text="Permisos de rol necesarios para el conector de datos de Microsoft Sentinel para SAP.":::

    Para más información, consulte [Autorizaciones para el usuario de ABAP](sap-solution-detailed-requirements.md#required-abap-authorizations).

1. Cree un usuario de RFC/NetWeaver sin cuadro de diálogo para el conector de datos de SAP y asóciele el rol */MSFTSEN/SENTINEL_CONNECTOR* recién creado.

    - Después de asociar el rol, compruebe que los permisos de rol se distribuyen al usuario.
    - Este proceso requiere que use un nombre de usuario y una contraseña para el usuario de ABAP. Después de crear el nuevo usuario y de que este tenga los permisos necesarios, asegúrese de cambiar la contraseña del usuario de ABAP.

1. Descargue y coloque el SDK de SAP NetWeaver RFC 7.50 para la versión Linux ON x86_64 de 64 BITS en la máquina virtual, ya que lo necesitará durante el proceso de instalación.

    Por ejemplo, busque el SDK en el sitio de [descarga de software de SAP](https://launchpad.support.sap.com/#/softwarecenter/template/products/_APP=00200682500000001943&_EVENT=DISPHIER&HEADER=Y&FUNCTIONBAR=N&EVENT=TREE&NE=NAVIGATE&ENR=01200314690100002214&V=MAINT) > **SAP NW RFC SDK** > **SAP NW RFC SDK 7.50** > **nwrfc750X_X-xxxxxxx.zip**. Asegúrese de descargar la opción **LINUX ON X86_64 de 65 BITS**. Copie el archivo, por ejemplo, mediante SCP, en la máquina virtual.

    Necesitará una cuenta de usuario de SAP para acceder a la página de descarga de software de SAP.

1. (Opcional) El archivo *Auditlog* de SAP se usa en todo el sistema y admite varios clientes SAP. Sin embargo, cada instancia de la solución Microsoft Sentinel para SAP solo admite un único cliente SAP.

    Por lo tanto, si tiene un sistema SAP de varios clientes, se recomienda que habilite el archivo *Auditlog* solo para el cliente donde implemente la solución SAP para evitar la duplicación de datos.


## <a name="deploy-a-linux-vm-for-your-sap-data-connector"></a>Implementación de una máquina virtual Linux para el conector de datos de SAP

En este procedimiento se describe cómo usar la CLI de Azure para implementar una máquina virtual Ubuntu Server 18.04 LTS y asignarla con una [identidad administrada por el sistema](../active-directory/managed-identities-azure-resources/index.yml).

> [!TIP]
> También puede implementar el conector de datos en RHEL, versión 7.7 o posteriores, o en suSE, versión 15 o posteriores. Tenga en cuenta que el sistema operativo y los niveles de revisión deben estar completamente actualizados.
>

Para implementar y preparar la máquina virtual Ubuntu, haga lo siguiente:

1. Use el siguiente comando como ejemplo. Asegúrese de insertar los valores del grupo de recursos y el nombre de la máquina virtual.

    ```azurecli
    az vm create  --resource-group [resource group name]   --name [VM Name] --image UbuntuLTS  --admin-username azureuser --data-disk-sizes-gb 10 – --size Standard_DS2 --generate-ssh-keys  --assign-identity
    ```

1. En la nueva máquina virtual, instale:

    - [Venv](https://docs.python.org/3.8/library/venv.html), con Python, versión 3.8 o superior.
    - La versión 2.8.0 de la [CLI de Azure](/cli/azure/), o cualquier versión posterior.

> [!IMPORTANT]
> Asegúrese de aplicar los procedimientos recomendados de seguridad para su organización, al igual que haría con cualquier otra máquina virtual.
>

Para más información, consulte [Inicio rápido: Creación rápida de una máquina virtual con la CLI de Azure](../virtual-machines/linux/quick-create-cli.md).

## <a name="create-a-key-vault-for-your-sap-credentials"></a>Creación de un almacén de claves para sus credenciales de SAP

En este tutorial se usa una instancia de [Almacén de claves de Azure](../key-vault/index.yml) recién creada o dedicada para almacenar las credenciales del conector de datos de SAP.

Para crear o dedicar una instancia de Almacén de claves de Azure:

1. Cree una nueva instancia de Almacén de claves de Azure o elija una existente para dedicarla a la implementación del conector de datos de SAP.

    Por ejemplo, para crear un nuevo almacén de claves, ejecute los siguientes comandos. Asegúrese de usar el nombre del grupo de recursos del almacén de claves y escriba el nombre del almacén de claves.

    ```azurecli
    kvgp=<KVResourceGroup>

    kvname=<keyvaultname>

    #Create a key vault
    az keyvault create \
      --name $kvname \
      --resource-group $kvgp
    ```

1. Asigne una directiva de acceso que incluya los permisos GET, LIST y SET para la identidad administrada de la máquina virtual, usando uno de los siguientes métodos:

    - **Azure Portal**:

        1. En el Almacén de claves de Azure, seleccione **Directivas de acceso** > **Agregar directiva de acceso - Permisos de los secretos: Get, List y Set** > **Seleccionar la entidad de seguridad**.  
        1. Escriba el [nombre de la máquina virtual](#deploy-a-linux-vm-for-your-sap-data-connector) y, a continuación, seleccione **Agregar** > **Guardar**.

        Para más información, consulte la [documentación de Key Vault](../key-vault/general/assign-access-policy-portal.md).

    - **La CLI de Azure**:

        1. Use el siguiente comando para obtener el [identificador de seguridad de la máquina virtual](#deploy-a-linux-vm-for-your-sap-data-connector). Asegúrese de escribir el nombre del grupo de recursos de Azure.  

            ```azurecli
            VMPrincipalID=$(az vm show -g [resource group] -n [Virtual Machine] --query identity.principalId -o tsv)
            ```  

            El identificador de la entidad de seguridad se muestra para que lo use en el paso siguiente.

        1. Ejecute el siguiente comando para asignar los permisos de acceso de la máquina virtual al almacén de claves. Asegúrese de escribir el nombre del grupo de recursos y el valor del identificador de la entidad de seguridad que se devolvió en el paso anterior.

            ```azurecli
            az keyvault set-policy -n [key vault] -g [resource group] --object-id $VMPrincipalID --secret-permissions get list set
            ```

## <a name="deploy-your-sap-data-connector"></a>Implementación del conector de datos de SAP

El script de implementación del conector de datos de Microsoft Sentinel para SAP instala el [software necesario](#automatically-installed-software) y, después, instala el conector en la [máquina virtual recién creada](#deploy-a-linux-vm-for-your-sap-data-connector). También almacena las credenciales en el [almacén de claves específico.](#create-a-key-vault-for-your-sap-credentials)

El script de implementación del conector de datos de SAP se almacena en el [Repositorio de GitHub de Microsoft Sentinel > DataConnectors > SAP](https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-sentinel-kickstart.sh).

Para ejecutar el script de implementación del conector de datos de SAP, necesitará los siguientes detalles:

- Los detalles del área de trabajo de Microsoft Sentinel, tal y como aparecen en la sección [Requisitos previos](#prerequisites).
- Los detalles del sistema SAP que aparecen en la sección [Requisitos previos](#prerequisites).
- Acceso a un usuario de máquina virtual con privilegios de sudo.
- El usuario de SAP que creó en [Configuración del sistema SAP](#configure-your-sap-system), con el rol **/MSFTSEN/SENTINEL_CONNECTOR** aplicado.
- La ayuda del equipo de SAP.

Para ejecutar el script de implementación de la solución de SAP, haga lo siguiente:

1. Ejecute el siguiente comando para implementar la solución de SAP en la máquina virtual.

    ```azurecli
    wget -O sapcon-sentinel-kickstart.sh https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-sentinel-kickstart.sh && bash ./sapcon-sentinel-kickstart.sh
    ```

1. Siga las instrucciones en pantalla para escribir los detalles de SAP y del almacén de claves, y completar la implementación. Aparece un mensaje de confirmación cuando se completa la implementación:

    ```azurecli
    The process has been successfully completed, thank you!
    ```

    Microsoft Sentinel empieza a recuperar los registros de SAP para el intervalo de tiempo configurado, hasta 24 horas antes de la hora de inicialización.

1. Se recomienda revisar los registros del sistema para asegurarse de que el conector de datos transmite datos. Ejecute:

    ```bash
    docker logs -f sapcon-[SID]
    ```

## <a name="deploy-sap-security-content"></a>Implementación del contenido de seguridad de SAP

Implemente el [contenido de seguridad de SAP](sap-solution-security-content.md) desde las áreas **Soluciones** y **Listas de seguimiento** de Microsoft Sentinel.

La **solución Supervisión continua de amenazas para SAP de Microsoft Sentinel**  permite que el conector de datos de SAP se muestre en el área Conectores de datos **de** Microsoft Sentinel. La solución también implementa el libro **SAP: aplicaciones y productos del sistema** y las reglas de análisis relacionadas con SAP.

Agregue manualmente listas de seguimiento relacionadas con SAP al área de trabajo de Microsoft Sentinel.

Para implementar el contenido de seguridad de la solución de SAP, haga lo siguiente:

1. En Microsoft Sentinel, en el panel izquierdo, seleccione **Soluciones (versión preliminar)** .

    La página **Soluciones** muestra una lista filtrada en la que se pueden realizar búsquedas de soluciones.

1. Para abrir la página de la solución de SAP, seleccione **Microsoft Sentinel: supervisión continua de amenazas para SAP (versión preliminar)** .

    :::image type="content" source="media/sap/sap-solution.png" alt-text="Captura de pantalla del panel de la solución &quot;Microsoft Sentinel - Continuous Threat Monitoring for SAP (Microsoft Sentinel: supervisión continua de amenazas para SAP)(versión preliminar)&quot;.":::

1. Seleccione **Crear** para iniciar el asistente para la implementación de soluciones y escriba los detalles de la suscripción de Azure, el grupo de recursos y el área de trabajo de Log Analytics donde desea implementar la solución.

1. Seleccione **Siguiente** para recorrer las pestañas **Conectores de datos**, **Análisis** y **Libros**, en las que podrá obtener más información sobre los componentes que se implementarán con esta solución.

    El nombre predeterminado del libro es **SAP: Aplicaciones y productos del sistema (versión preliminar)** . Cámbielo en la pestaña Libros según sea necesario.

    Para obtener información adicional, consulte [Solución Microsoft Sentinel para SAP: Referencia de contenido de seguridad (versión preliminar pública)](sap-solution-security-content.md).

1. En la pestaña **Revisar y crear**, espere el mensaje **Validación superada** y, a continuación, seleccione **Crear** para implementar la solución.

    > [!TIP]
    > También puede seleccionar **Descargar una plantilla** para obtener un vínculo para implementar la solución como código.

1. Una vez completada la implementación, aparece un mensaje de confirmación en la parte superior derecha de la página.

    Para mostrar el contenido recién implementado, vaya a:

    - **Administración de amenazas** > **Libros** > **Mis libros**, para buscar los [libros de SAP integrados](sap-solution-security-content.md#built-in-workbooks).
    - **Configuración** > **Análisis** para ver una serie de [reglas de análisis relacionadas con SAP](sap-solution-security-content.md#built-in-analytics-rules).

1. Agregue listas de seguimiento relacionadas con SAP para usarlas en búsquedas, reglas de detección, búsqueda de amenazas y cuadernos de estrategias de respuesta. Estas listas de seguimiento proporcionan la configuración de la solución de supervisión continua de amenazas de Microsoft Sentinel para SAP. Haga lo siguiente:

    a. Descargue las listas de seguimiento de SAP desde el repositorio de GitHub de Microsoft Sentinel en https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/Analytics/Watchlists.  
    b. En el área **Listas de seguimiento** de Microsoft Sentinel, agregue las listas de seguimiento al área de trabajo de Microsoft Sentinel. Use los archivos CSV descargados como orígenes y, a continuación, personalícelos según sea necesario para su entorno.  

    [ ![Listas de seguimiento relacionadas con SAP agregadas a Microsoft Sentinel.](media/sap/sap-watchlists.png) ](media/sap/sap-watchlists.png#lightbox)

    Para más información, consulte [Uso de listas de seguimiento de Microsoft Sentinel](watchlists.md) y [Listas de seguimiento de SAP disponibles](sap-solution-security-content.md#available-watchlists).

1. En Microsoft Sentinel, vaya al conector de datos de **Azure Sentinel Continuous Threat Monitoring para SAP** (supervisión continua de amenazas para SAP) para confirmar la conexión:

    [![Captura de pantalla de la página del conector de datos de Microsoft Sentinel - Continuous Threat Monitoring for SAP (Microsoft Sentinel: supervisión continua de amenazas para SAP).](media/sap/sap-data-connector.png)](media/sap/sap-data-connector.png#lightbox)

    Los registros de SAP ABAP aparecen en la página **Registros** de Microsoft Sentinel, en **Registros personalizados**:

    [![Captura de pantalla de los registros ABAP SAP en el área "Registros personalizados" de Microsoft Sentinel.](media/sap/sap-logs-in-sentinel.png) ](media/sap/sap-logs-in-sentinel.png#lightbox)

    Para obtener más información, consulte la [referencia sobre registros de la solución Microsoft Sentinel SAP (versión preliminar pública)](sap-solution-log-reference.md).


## <a name="update-your-sap-data-connector"></a>Actualización del conector de datos de SAP

Si ya tiene un contenedor de Docker en ejecución con una versión anterior del conector de datos de SAP, ejecute el script de actualización del conector de datos de SAP para obtener las características más recientes disponibles.

Asegúrese de que tiene las versiones más recientes de los scripts de implementación correspondientes del repositorio de Github de Microsoft Sentinel. 

Ejecute:

```azurecli
wget -O sapcon-instance-update.sh https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-instance-update.sh && bash ./sapcon-instance-update.sh
```

El contenedor de Docker del conector de datos de SAP en la máquina está actualizado. 

Asegúrese de comprobar si hay otras actualizaciones disponibles, como:

- Solicitudes de cambio de SAP pertinentes, en el [repositorio de GitHub de Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/CR).
- Contenido de seguridad de SAP de Microsoft Sentinel, en la solución **Microsoft Sentinel Continuous Threat Monitoring for SAP**.
- Listas de reproducción pertinentes, en el [repositorio de GitHub de Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/Analytics/Watchlists).

## <a name="collect-sap-hana-audit-logs"></a>Recopilación de registros de auditoría de SAP HANA

Si ha configurado registros de auditoría de base de datos de SAP HANA con Syslog, también tendrá que configurar el agente de Log Analytics para recopilar los archivos de Syslog.

1. Asegúrese de que el registro de auditoría de SAP HANA está configurado para usar Syslog como se describe en la *nota 0002624117 de SAP*, a la que se accede desde el [sitio de soporte técnico de SAP Launchpad](https://launchpad.support.sap.com/#/notes/0002624117). Para más información, consulte:

    - [Registro de auditoría de SAP HANA: procedimiento recomendado](https://archive.sap.com/documents/docs/DOC-51098)
    - [Recomendaciones de auditoría](https://help.sap.com/viewer/742945a940f240f4a2a0e39f93d3e2d4/2.0.05/en-US/5c34ecd355e44aa9af3b3e6de4bbf5c1.html)

1. Compruebe los archivos de Syslog del sistema operativo para ver los eventos de base de datos de HANA pertinentes.

1. Instale y configure un agente de Log Analytics en la máquina:

    a. Inicie sesión en el sistema operativo de la base de datos HANA como un usuario con privilegios sudo.  
    b. En Azure Portal, vaya al área de trabajo de Log Analytics. En la parte izquierda, en **Configuración**, seleccione **Agents management > Linux servers** >  **(Administración de agentes > Servidores Linux)** .  
    c. Copie el código que se muestra en el cuadro **Descargar e incorporar Agent para Linux** en el terminal y ejecute el script.

    El agente de Log Analytics se instala en la máquina y se conecta al área de trabajo. Para obtener más información, consulte [Instalación del agente de Log Analytics en equipos Linux](../azure-monitor/agents/agent-linux.md) y [Agente de OMS para Linux](https://github.com/microsoft/OMS-Agent-for-Linux) en el repositorio GitHub de Microsoft.

1. Actualice la pestaña **Agents Management > Linux servers** (Administración de agentes > Servidores Linux) para confirmar que tiene **1 equipo Linux conectado**.

1. En **Configuración** en el panel izquierdo, seleccione **Configuración de agentes** y después la pestaña **Syslog**.

1. Seleccione **Add facility** (Agregar instalación) para agregar las instalaciones que quiera recopilar. 

    > [!TIP]
    > Como las instalaciones donde se guardan los eventos de base de datos de HANA pueden cambiar entre cada distribución, se recomienda agregar todas las instalaciones, comprobarlas en los registros de Syslog y, después, quitar las que no sean pertinentes.
    >

1. En Microsoft Sentinel, compruebe que los eventos de base de datos de HANA se muestran ahora en los registros ingeridos.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las soluciones de Microsoft Sentinel para SAP:

- [Implementación del conector de datos de SAP de Microsoft Sentinel con SNC](sap-solution-deploy-snc.md)
- [Opciones de configuración de expertos, implementación local y orígenes de registro de SAPControl](sap-solution-deploy-alternate.md)
- [Requisitos de SAP detallados para la solución de SAP de Microsoft Sentinel](sap-solution-detailed-requirements.md)
- [Referencia a los registros de la solución de SAP de Microsoft Sentinel](sap-solution-log-reference.md)
- [Microsoft Sentinel para SAP: contenido de seguridad integrado](sap-solution-security-content.md)
- [Solución de problemas de implementación de la solución SAP de Microsoft Sentinel](sap-deploy-troubleshoot.md)

Para obtener más información, consulte [soluciones de Microsoft Sentinel](sentinel-solutions.md).
