---
title: Implementación y supervisión de honeytoken de Azure Key Vault con Microsoft Sentinel
description: Implemente claves y secretos honeytoken de Azure Key Vault y supervíselos con Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.openlocfilehash: 73d3c43e93dce030895291859acb723a94a0cadc
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301331"
---
# <a name="deploy-and-monitor-azure-key-vault-honeytokens-with-microsoft-sentinel-public-preview"></a>Implementación y supervisión de honeytoken de Azure Key Vault con Microsoft Sentinel (versión preliminar pública)

> [!IMPORTANT]
> La solución Microsoft Sentinel Deception (Honey Tokens) está actualmente en VERSIÓN PRELIMINAR. En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>

En este artículo se explica cómo usar la solución **Microsoft Sentinel Deception (Honey Tokens)** para implementar claves y secretos señuelo de [Azure Key Vault](/azure/key-vault/), denominados *honeytoken*, en cargas de trabajo existentes.

Use las [reglas de análisis](detect-threats-built-in.md), las [listas de control](watchlists.md) y los [libros](monitor-your-data.md) proporcionados por la solución para supervisar el acceso a los honeytoken implementados.

Al usar honeytoken en el sistema, los principios de detección siguen siendo los mismos. Como no hay motivos legítimos para acceder a un honeytoken, cualquier actividad indicará la presencia de un usuario que no está familiarizado con el entorno y que podría ser un atacante.

## <a name="before-you-begin"></a>Antes de empezar

Para empezar a usar la solución **Microsoft Sentinel Deception (Honey Tokens)** , asegúrese de que tiene lo siguiente:

- **Roles necesarios**: debe ser administrador de inquilinos para instalar la solución **Microsoft Sentinel Deception (Honey Tokens)** . Una vez que esté instalada la solución, puede compartir el libro con los propietarios del almacén de claves para que puedan implementar honeytoken propios.

- **Conectores de datos necesarios**: asegúrese de que ha implementado los conectores de datos de [Azure Key Vault](data-connectors-reference.md#azure-key-vault) y [Azure Activity](data-connectors-reference.md#azure-activity) en el área de trabajo y que están conectados.

  Compruebe que el enrutamiento de datos se ha realizado correctamente y que los datos de **KeyVault** y **AzureActivity** fluyen a Microsoft Sentinel. Para más información, consulte:

  - [Conexión de Microsoft Sentinel a servicios de Azure, Windows, Microsoft y Amazon](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)
  - [Búsqueda del conector de datos de Microsoft Sentinel](data-connectors-reference.md)

## <a name="install-the-solution"></a>Instalar la solución

Instale la solución **Microsoft Sentinel Deception (Honey Tokens)** como haría con [otras soluciones](sentinel-solutions-deploy.md). En la página de la solución **Azure Sentinel Deception**, seleccione **Start** (Iniciar) para empezar.

:::image type="content" source="media/monitor-key-vault-honeytokens/honeytoken-create-solution.png" alt-text="Captura de pantalla de la página de creación de la solución.":::

**Para instalar la solución Deception**:

En los pasos siguientes se describen las acciones específicas necesarias para la solución **Microsoft Sentinel Deception (Honey Tokens)** .

1. En la pestaña **Aspectos básicos**, seleccione el mismo grupo de recursos en el que se encuentre el área de trabajo de Microsoft Sentinel.

1. En la pestaña **Prerequisites** (Requisitos previos) en el campo **Function app name** (Nombre de la aplicación de funciones), escriba un nombre descriptivo para la aplicación de funciones de Azure que creará los honeytoken en los almacenes de claves.

    El nombre de la aplicación de funciones debe ser único, tener entre 2 y 22 caracteres de longitud, y solo caracteres alfanuméricos.

    A continuación se muestra un comando con el nombre que ha definido. Por ejemplo:

    :::image type="content" source="media/monitor-key-vault-honeytokens/prerequisites.png" alt-text="Captura de pantalla de la pestaña de requisitos previos en la que se muestra el comando curl actualizado.":::

1. Seleccione **Click here to open a cloud shell** (Haga clic aquí para abrir Cloud Shell) a fin de abrir una pestaña de Cloud Shell. Inicie sesión si se le solicita y, después, ejecute el comando que se muestra.

    El script que ejecuta crea una aplicación de funciones de Azure AD (AAD), que implementará los honeytoken.    Por ejemplo:

    ```bash
    Requesting a Cloud Shell.Succeeded
    Connecting terminal...

    Welcome to Azure Cloud Shell

    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell

    maria@Azure:~$curl -sL https://aka.ms/sentinelhoneytokensappcreate | bash -s HoneyTokenFunctionApp
    ```

    La salida del script incluye el id. de la aplicación y el secreto de AAD. Por ejemplo:

    ```bash
    WARNING: The output includes credentials that you must protect. Be sure that you do not include these credentials in your code or check the credentials into your source control. For more information, see https://aka.ms/azadsp-cli
    function app name: HoneyTokenFunctionApp
    AAD App Id: k4js48k3-97gg-3958=sl8d=48620nne59k4
    AAD App secret: v9kUtSoy3u~K8DKa8DlILsCM_K-s9FR3Kj
    maria@Azure:~$
    ```

1. De nuevo en Microsoft Sentinel, en la parte inferior de la pestaña **Requisitos previos**, escriba el id. de la aplicación de AAD y el secreto en los campos correspondientes. Por ejemplo:

    :::image type="content" source="media/monitor-key-vault-honeytokens/client-app-secret-values.png" alt-text="Captura de pantalla de los valores agregados de aplicación cliente y secreto de la aplicación de funciones.":::

1. Seleccione **Click here to continue in your function app settings** (Haga clic aquí para continuar con la configuración de la aplicación de funciones) en el paso 4. Se abre una nueva pestaña del explorador en la configuración de la aplicación de Azure AD.

    Inicie sesión si se le solicita y, después, seleccione **Grant admin consent for `<your directory name>`** (Conceder consentimiento de administrador para) para continuar. Por ejemplo:

    :::image type="content" source="media/monitor-key-vault-honeytokens/grant-admin-access.png" alt-text="Captura de pantalla del botón para conceder consentimiento del administrador para el directorio.":::

    Para más información, vea [Concesión del consentimiento del administrador en Registros de aplicaciones](/azure/active-directory/manage-apps/grant-admin-consent).

1. De nuevo en Microsoft Sentinel, en las pestañas **Libros**, **Análisis**, **Listas de seguimiento** y **Cuadernos de estrategias**, observe el contenido de seguridad que se va a crear y modifique los nombres según sea necesario.

    > [!NOTE]
    > En otras instrucciones de este artículo se hace referencia a los libros **HoneyTokensIncidents** y **SOCHTManagement**. Si cambia los nombres de estos libros, asegúrese de anotar los nuevos como referencia propia y úselos según sea necesario en lugar de los nombres predeterminados.

1. En la pestaña **Azure Functions**, defina los valores siguientes:

    **Configuración de Key Vault**: los siguientes campos definen valores para el almacén de claves donde almacenará el secreto de la aplicación de AAD. Estos campos *no* definen el almacén de claves donde va a implementar los honeytoken.

    |Campo  |Descripción  |
    |---------|---------|
    | **Plan de servicio**     |   Seleccione si quiere usar un plan **Premium** o de **Consumo** para la aplicación de funciones. Para más información, vea [Hospedaje de plan de consumo de Azure Functions](/azure/azure-functions/consumption-plan) y [Plan Premium de Azure Functions](/azure/azure-functions/functions-premium-plan).      |
    | **Si se crea un almacén de claves**     |    Seleccione **new** (nuevo) para crear un almacén de claves para el secreto de la aplicación o **existing** (existente) para usar uno ya existente.   |
    | **Nombre de KeyVault**     | Solo se muestra cuando ha seleccionado crear un almacén de claves. <br><br>Escriba el nombre del almacén de claves que quiera usar para almacenar el secreto de la aplicación. Este nombre debe ser único globalmente.     |
    | **KeyVault resource group** (Grupo de recursos de KeyVault)     |Solo se muestra cuando ha seleccionado crear un almacén de claves. <br><br> Seleccione el nombre del grupo de recursos donde quiera almacenar el almacén de claves para la clave de aplicación.      |
    | **Existing key vaults** (Almacenes de claves existentes) | Solo se muestra cuando ha seleccionado usar un almacén de claves existente. Seleccione el almacén de claves que quiera usar. |
    | **Nombre de secreto de KeyVault**     |   Escriba el nombre del secreto usado para almacenar el secreto de cliente.      |

    **Honeytoken configuration** (Configuración de honeytoken): los campos siguientes definen la configuración que se utiliza para las claves y los secretos usados en los honeytoken. Use convenciones de nomenclatura que se combinarán con los requisitos de nomenclatura de la organización para que los atacantes no los distingan.

    |Campo  |Descripción  |
    |---------|---------|
    |**Palabras clave de claves**     |  Escriba listas separadas por comas de los valores que quiera usar con los nombres de honeytoken de señuelo.  Por ejemplo, `key,prod,dev`.  Los valores solo deben ser alfanuméricos.   |
    |**Secretos**     |   Escriba listas de valores separados por comas que quiera usar con los secretos de honeytoken de señuelo.  Por ejemplo, `secret,secretProd,secretDev`. Los valores solo deben ser alfanuméricos.    |
    |**Additional HoneyToken Probability** (Probabilidad adicional de honeytoken)     |  Escriba un valor entre `0` y `1`, como `0.6`. Este valor define la probabilidad de que se agregue más de un honeytoken al almacén de claves.       |
    |     |         |

1. Seleccione **Siguiente: Revisar y crear** para terminar de instalar la solución.

    Una vez que se instala la solución, se muestran los elementos siguientes:

    - Un vínculo al libro **SOCHTManagement**. Es posible que haya modificado este nombre en la pestaña **Workbooks** (Libros) anteriormente en este procedimiento.

    - La dirección URL de una plantilla de ARM personalizada. Puede usar esta plantilla de ARM para implementar una iniciativa de Azure Policy, conectada a una recomendación personalizada de Microsoft Defender for Cloud, que distribuye el libro **SOCHTManagement** a los propietarios de almacenes de claves de la organización.

1. En la pestaña **Pasos posteriores a la implementación** se indica que puede usar la información que se muestra en la salida de la implementación para distribuir la recomendación personalizada de Microsoft Defender for Cloud a todos los propietarios de almacenes de claves de la organización, que recomienda implementar honeytoken en sus almacenes de claves.

    Use la [dirección URL de la plantilla de ARM](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2faka.ms%2fsentinelhoneytokenspolicy) personalizada que se muestra en la salida de instalación para abrir la página **Implementación personalizada** de la plantilla vinculada.

    Para más información, vea [Distribución del libro SOCHTManagement](#distribute-the-sochtmanagement-workbook).

## <a name="deploy-your-honeytokens"></a>Implementación de los honeytoken

Después de instalar la solución **Microsoft Sentinel Deception (Honey Tokens)** , está listo para empezar a implementar honeytoken en los almacenes de claves mediante los pasos del libro **SOCHTManagement**.

Se recomienda compartir el libro **SOCHTManagement** con los propietarios del almacén de claves de la organización para que puedan crear honeytoken propios en sus almacenes de claves. Es posible que haya cambiado el nombre de este libro al [instalar la solución](#install-the-solution). Al compartirlo, asegúrese de conceder solo permisos de lectura.

**Implemente honeytoken en los almacenes de claves**:

1. En Microsoft Sentinel, vaya a **Libros > Mis libros** y abra el libro **SOCHTManagement**. Es posible que haya modificado este nombre al implementar la solución.

1. Seleccione **Ver libro guardado** > **Agregar como de confianza**. Por ejemplo:

    :::image type="content" source="media/monitor-key-vault-honeytokens/add-as-trusted.png" alt-text="Captura de pantalla del botón &quot;Agregar como de confianza&quot; del libro SOCHTManagement.":::

    La infraestructura se implementa en los almacenes de claves para permitir la implementación de honeytoken.

1. En la pestaña **Key Vault** del libro, expanda la suscripción para ver los almacenes de claves listos para implementar honeytoken y los que ya tienen honeytoken implementados.

    En la columna **Is Monitored by SOC** (Supervisado por SOC), una marca de verificación verde :::image type="icon" source="media/monitor-key-vault-honeytokens/checkmark.png" border="false"::: indica que el almacén de claves ya tiene honeytoken. Una marca X de color rojo :::image type="icon" source="media/monitor-key-vault-honeytokens/xmark.png" border="false"::: indica que el almacén de claves todavía no tiene honeytoken. Por ejemplo:

    :::image type="content" source="media/monitor-key-vault-honeytokens/honeytokens-deployed.png" alt-text="Captura de pantalla de los libros SOCHTManagement en la que se muestran los honeytoken implementados.":::

1. Desplácese hacia abajo en la página del libro y use las instrucciones y vínculos de la sección **Take an action** (Realizar una acción) para implementar honeytoken en todos los almacenes de claves a escala o implementarlos manualmente de uno en uno.

    # <a name="deploy-at-scale"></a>[Implementación a escala](#tab/deploy-at-scale)

    **Para implementar honeytoken a gran escala**:

    1. Seleccione el vínculo **Enable user** (Habilitar usuario) para implementar una plantilla de ARM que implemente una directiva de acceso del almacén de claves, y conceda el identificador de usuario especificado derechos para crear los honeytoken.

        Inicie sesión si se le solicita y escriba valores en las áreas **Detalles del proyecto** y **Detalles de instancia** para la implementación de la plantilla de ARM. Busque el **identificador de inquilino** y el **Id. de objeto de usuario** en la página principal de Azure Active Directory de los usuarios.

        Cuando haya terminado, seleccione **Revisar y crear** para implementar la plantilla de ARM. Por ejemplo:

        :::image type="content" source="media/monitor-key-vault-honeytokens/deploy-arm-template.png" alt-text="Captura de pantalla de la página Implementación personalizada.":::

        Se valida la configuración y, cuando se supera la validación, se muestra una confirmación: **Validación superada**

        En la parte inferior de la página, seleccione **Crear** para implementar la plantilla de ARM y ver una página de confirmación de implementación correcta.

    1. De nuevo en Microsoft Sentinel, en el libro **SOCHTManagement** > en el área **Take an action**  (Realizar una acción)  > **Implementar a escala**, seleccione el vínculo **Click to deploy** (Haga clic para implementar) a fin de agregar honeytoken a todos los almacenes de claves a los que tiene acceso en la suscripción seleccionada.

        Cuando haya terminado, los resultados de la implementación de honeytoken se muestran en una tabla en una nueva pestaña.

    1. Asegúrese de seleccionar el vínculo **Disable your user** (Deshabilitar el usuario) para quitar la directiva de acceso que ha creado antes. Vuelva a iniciar sesión si se le solicita, escriba los valores de la implementación de ARM personalizada y, después, implemente la plantilla de ARM. En este paso se implementa una directiva de acceso del almacén de claves que quita los derechos de usuario para crear claves y secretos.

    # <a name="deploy-a-single-honeytoken"></a>[Implementación de un único honeytoken](#tab/deploy-a-single-honeytoken)

    **Para implementar manualmente un único honeytoken**:

    1. En la tabla de la parte superior de la página, seleccione el almacén de claves donde quiera implementar el honeytoken. En la parte inferior de la página aparece la sección **Deploy on a specific key-vault:** (Implementar en un almacén de claves específico:).

    1. Desplácese hacia abajo y, en la lista desplegable **Honeytoken type** (Tipo de honeytoken), seleccione si quiere crear una clave o un secreto. En el campo **New honeytoken name** (Nuevo nombre de honeytoken), escriba un nombre descriptivo para el honeytoken. Por ejemplo:

        :::image type="content" source="media/monitor-key-vault-honeytokens/deploy-manually.png" alt-text="Captura de pantalla de la implementación en un área específica del almacén de claves.":::

    1. En la tabla **Operation** (Operación), expanda la sección **Deploy a honeytoken** (Implementar un honeytoken) y seleccione cada nombre de tarea para realizar los pasos necesarios. Inicie sesión si se le solicita.

        - Seleccione **Click to validate the key-vault is audited** (Haga clic para validar que el almacén de claves está auditado). En Azure Key Vault, compruebe que la configuración de diagnóstico del almacén de claves está establecida para enviar eventos de auditoría a Log Analytics.
        - Seleccione **Enable your user in the key-vault's policy if missing** (Habilitar el usuario en la directiva del almacén de claves si falta). En Azure Key Vault, asegúrese de que el usuario tiene acceso para implementar honeytoken en las ubicaciones necesarias. Para guardar los cambios, seleccione **Guardar**.
        - Seleccione **Click to add a honey token to the key-vault** (Haga clic para agregar un honeytoken al almacén de claves) para abrir Azure Key Vault. Agregue un nuevo honeytoken, como un secreto nuevo, al almacén de claves configurado.
        - Seleccione **Click to add monitoring in the SOC** (Haga clic para agregar supervisión en el SOC). Si se realiza correctamente, se muestra un mensaje de confirmación en una nueva pestaña: `Honey-token was successfully added to monitored list`.

        Para más información, consulte la [documentación de Azure Key Vault](/azure/key-vault/secrets/about-secrets).

    > [!NOTE]
    > Asegúrese de el vínculo **Disable back your user in the key-vault's policy if needed** (Deshabilitar el usuario en la directiva del almacén de claves si es necesario) para quitar los derechos de concesión creados por la directiva de acceso para crear los honeytoken.
    >

    # <a name="remove-a-honeytoken"></a>[Eliminación de un honeytoken](#tab/remove-a-honeytoken)

    **Para quitar un honeytoken específico**:

    1. En la tabla de la parte superior de la página, seleccione el almacén de claves donde quiera quitar un honeytoken. En la parte inferior de la página aparece la sección **Deploy on a specific key-vault:** (Implementar en un almacén de claves específico:).

    1. En la tabla **Operation** (Operación), expanda la sección **Remove a honeytoken** (Quitar un honeytoken) y seleccione cada nombre de tarea para realizar los pasos necesarios. Inicie sesión si se le solicita.

        - Seleccione **Click to delete the honey token from the key-vault** (Haga clic para eliminar el honeytoken del almacén de claves) para abrir Azure Key Vault en la página donde puede quitar el honeytoken.
        - Seleccione **Send an email to update the SOC** (Enviar un correo electrónico para actualizar el SOC). Se abre un correo electrónico en el cliente de correo electrónico predeterminado para el SOC y se recomienda quitar la supervisión de honeytoken para el almacén de claves seleccionado.

    > [!TIP]
    > Se recomienda que comunique claramente al SOC los honeytoken que elimine.
    >

    ---

Es posible que tenga que esperar unos minutos mientras se rellenan los datos y se actualizan los permisos. Actualice la página para mostrar las actualizaciones de la implementación del almacén de claves.

## <a name="test-the-solution-functionality"></a>Prueba de la funcionalidad de la solución

**Para probar que recibe una alerta de cualquier intento de acceso a los honeytoken**:

1. En la página **Listas de seguimiento** de Microsoft Sentinel, seleccione la pestaña **Mis listas de seguimiento** y después la lista de seguimiento **HoneyTokens**.

    Seleccione **Ver en Log Analytics** para ver una lista de los valores actuales de honeytoken encontrados. En la página **Registros** se extraen automáticamente los elementos de la lista de reproducción para la consulta. Por ejemplo:

    :::image type="content" source="media/monitor-key-vault-honeytokens/honeytokens-watchlist.png" alt-text="Captura de pantalla de los valores de lista de reproducción de honeytoken en Log Analytics." lightbox="media/monitor-key-vault-honeytokens/honeytokens-watchlist.png":::

    Para obtener más información, vea [Uso de las listas de seguimiento de Microsoft Sentinel](watchlists.md).

1. En la lista de Log Analytics, elija un valor de honeytoken para probarlo.

    Después, vaya a Azure Key Vault y descargue la clave pública o vea el secreto del honeytoken elegido.

    Por ejemplo, seleccione el honeytoken y después **Download public key** (Descargar clave pública). Esta acción crea un registro `KeyGet` o `SecretGet` que desencadena una alerta en Microsoft Sentinel.

    Para más información, consulte la [documentación de Key Vault](/azure/key-vault/).

1. De nuevo en Microsoft Sentinel, vaya a la página **Incidentes**. Es posible que tenga que esperar cinco minutos aproximadamente, pero debería ver un nuevo incidente, denominado por ejemplo **HoneyTokens: KeyVault HoneyTokens key accessed** (HoneyTokens: acceso a la clave HoneyTokens de KeyVault).

    Seleccione el incidente para ver sus detalles, como la operación de clave realizada, el usuario que ha accedido a la clave de honeytoken y el nombre del almacén de claves en peligro.

    > [!TIP]
    > Cualquier acceso u operación con las claves y los secretos de honeytoken genera incidentes que puede investigar en Microsoft Sentinel. Como no hay ninguna razón para usar realmente claves y secretos de honeytoken, cualquier actividad similar en el área de trabajo puede ser malintencionada y se debe investigar.

1. Vea la actividad de honeytoken en el libro **HoneyTokensIncident**. En la página **Libros** de Microsoft Sentinel, busque y abra el libro **HoneyTokensIncident**.

    En este libro se muestran todos los incidentes relacionados con los honeytoken, las entidades relacionadas, los almacenes de claves en peligro, las operaciones de clave realizadas y los honeytoken a los que se ha accedido.

    Seleccione incidentes y operaciones específicos para investigar toda la actividad relacionada.

## <a name="distribute-the-sochtmanagement-workbook"></a>Distribución del libro SOCHTManagement

Se recomienda implementar honeytoken en tantos almacenes de claves como sea posible para garantizar las funciones de detección óptimas en su organización.

Pero muchos equipos de SOC no tienen acceso a los almacenes de claves. Para solucionarlo, distribuya el libro **SOCHTManagement** a todos los propietarios del almacén de claves del inquilino, para que los equipos de SOC puedan implementar honeytoken propios. Es posible que haya modificado el nombre de este libro al [instalar la solución](#install-the-solution).

Siempre puede compartir el vínculo directo al libro. En este procedimiento también se explica cómo usar una plantilla de ARM para implementar una iniciativa de Azure Policy, conectada a una recomendación personalizada de Microsoft Defender for Cloud, que distribuye el libro **SOCHTManagement** a los propietarios de almacenes de claves de la organización.

> [!NOTE]
> Siempre que distribuya el libro, asegúrese de conceder solo acceso de lectura.
>

**Para distribuir el libro SOCHTManagement mediante una iniciativa de Azure Policy**

1. En la tabla siguiente, seleccione un botón **Implementar en Azure** para abrir la plantilla de ARM en la página **Implementación personalizada**, en función de cómo quiera implementar la plantilla de ARM.  Use los vínculos de GitHub para ver los detalles de lo que se incluye en la plantilla de ARM, o bien a fin de personalizarla para el entorno.

    Los botones **Implementar en Azure** usan las mismas direcciones URL que se muestran en la pestaña **Salida** después de la [instalación de la solución](#install-the-solution).

    | Opción de implementación | Descripción | Implementar en Azure | Vínculo de GitHub |
    |-------------------|-------------|-------------|-----------------|
    | Grupo de administración | Recomendado para la implementación en toda la empresa| [![DTA-Button-MG]][DTA-MG]  |[Ejemplo en GitHub][GitHub-MG] |
    | Subscription | Recomendado para realizar pruebas en una sola suscripción | [![DTA-Button-Sub]][DTA-Sub]  | [Ejemplo en GitHub][GitHub-Sub] |

    Inicie sesión cuando se le solicite.

1. En la pestaña **Implementación de directiva Deception Solution** > **Datos básicos** de la plantilla de ARM, seleccione el valor y la región del grupo de administración. Después, seleccione **Siguiente: Destino de implementación >** para continuar.

1. En la pestaña **Destino de implementación**, vuelva a seleccionar el grupo de administración y, después, seleccione **Next: Management Workbook >** (Siguiente > Libro de administración >).

1. En la pestaña **Management Workbook** (Libro de administración), pegue el vínculo al libro **SOCHTManagement**.

    Puede encontrar el vínculo del libro **SOCHTManagement** en Microsoft Sentinel, y también se incluye en la pestaña **Salida** de la implementación de la solución.

    Por ejemplo, para buscar el vínculo en el libro, seleccione **Workbooks** > **My workbooks** > **SOCHTManagement** (Libros > Mis libros > SOCHTManagement) y, después, seleccione **Copiar vínculo** en la barra de herramientas.

1. Después de escribir el vínculo del libro, seleccione **Siguiente: Revisar y crear >** para continuar. Espere un mensaje de confirmación que indica que se ha superado la validación y, después, seleccione **Crear**.

1. Una vez que se haya completado la implementación, verá que incluye una nueva iniciativa **HoneyTokens** y dos directivas nuevas, denominadas **KeyVault HoneyTokens** y **KVReviewTag**. Por ejemplo:

    :::image type="content" source="media/monitor-key-vault-honeytokens/policy-deployment.png" alt-text="Captura de pantalla de una directiva de plantilla de ARM implementada correctamente." lightbox="media/monitor-key-vault-honeytokens/policy-deployment.png":::

1. En Azure **Policy**, asigne la nueva directiva **KVReviewTag** con el ámbito que necesite. Esta asignación agrega la etiqueta **KVReview** y un valor de **ReviewNeeded** a todos los almacenes de claves del ámbito seleccionado.

    1. En Azure Policy, seleccione **Definitions** (Definiciones) en **Authoring** (Creación). Busque la fila de la directiva **KVReviewTag** y seleccione el menú de opciones de la derecha.

    1. En la página **Deploy Diagnostic Settings for Activity Log to Log Analytics workspace** (Implementar configuración de diagnóstico para el registro de actividad en el área de trabajo de Log Analytics), escriba los valores necesarios a fin de implementar la configuración de diagnóstico para el entorno.

        En la pestaña **Corrección**, asegúrese de seleccionar la opción **Crear una tarea de corrección** para aplicar la etiqueta a los almacenes de claves existentes.

    Para más información, consulte la [documentación de Azure Policy](/azure/governance/policy/assign-policy-portal).

1. En Azure **Security Center**, agregue una recomendación de auditoría a todos los almacenes de claves en el ámbito seleccionado:

    1. Seleccione **Cumplimiento normativo > Administrar directivas de cumplimiento** y, después, seleccione el ámbito.

    1. En la página de detalles del ámbito seleccionado, desplácese hacia abajo y, en la sección **Your custom initatives** (Sus iniciativas personalizadas), seleccione **Add custom initiative** (Agregar iniciativa personalizada).

    1. En la fila de iniciativa **HoneyTokens**, seleccione **Agregar**.

Se agrega una recomendación de auditoría, con un vínculo al libro **SOCHTManagement**, a todos los almacenes de claves del ámbito seleccionado. Es posible que haya modificado el nombre de este libro al [instalar la solución](#install-the-solution).

Para obtener más información, vea la [documentación de Microsoft Defender for Cloud](/azure/security-center/security-center-recommendations).

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte:

- [Acerca de las soluciones de Microsoft Sentinel](sentinel-solutions.md)
- [Detección e implementación de soluciones de Microsoft Sentinel](sentinel-solutions-deploy.md)
- [Catálogo de soluciones de Microsoft Sentinel](sentinel-solutions-catalog.md)
- [Detección de amenazas integrada](detect-threats-built-in.md)
- [Libros de Microsoft Sentinel que se usan comúnmente](top-workbooks.md)

<!-- The following section is used to store references to external images and links to reduce maintenance overhead and enable tooltips -->

[//]: # (*******************************)
[//]: # (REFERENCIAS A IMÁGENES EXTERNAS A CONTINUACIÓN)
[//]: # (*******************************)

[DTA-Button-MG]: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true "Implementación de directivas de ASC en el ámbito del grupo de administración."
[DTA-Button-Sub]: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true "Implementación de directivas de ASC en el ámbito de la suscripción."

[//]: # (**************************)
[//]: # (ETIQUETAS DE VÍNCULOS EXTERNOS A CONTINUACIÓN)
[//]: # (**************************)

[GitHub-MG]: https://github.com/Azure/Azure-Sentinel/blob/master/Solutions/HoneyTokens/ASCRecommendationPolicy.json
[GitHub-Sub]: https://github.com/Azure/Azure-Sentinel/blob/master/Solutions/HoneyTokens/ASCRecommendationPolicySub.json

[DTA-MG]: https://portal.azure.com/#blade/Microsoft_Azure_CreateUIDef/CustomDeploymentBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Sentinel%2Fmaster%2FSolutions%2FHoneyTokens%2FASCRecommendationPolicy.json/uiFormDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Sentinel%2Fmaster%2FSolutions%2FHoneyTokens%2FASCRecommendationPolicyUI.json
[DTA-Sub]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Sentinel%2Fmaster%2FSolutions%2FHoneyTokens%2FASCRecommendationPolicySub.json
