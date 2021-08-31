---
title: 'Administración de la integración de Azure con Logz.io: soluciones de asociados de Azure'
description: Aprenda a administrar la integración de Azure con Logz.io.
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 07/28/2021
ms.author: tomfitz
ms.openlocfilehash: 0b00e1b4b5cf868fdd70909a1ce42f0861f2e0b2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780050"
---
# <a name="manage-the-logzio-integration-in-azure"></a>Administración de la integración de Logz.io en Azure

En este artículo se muestra cómo administrar la configuración de la integración de Azure con Logz.io.

## <a name="resource-overview"></a>Información general sobre recursos

La pantalla de **información general** de Logz.io muestra los detalles del recurso que incluyen los siguientes elementos:

- **Grupos de recursos**
- **Ubicación**
- **Suscripción**
- **Etiquetas**
- **SSO de Logz.io**
- **Billing term** (Período de facturación)

Dependiendo de si ha optado por el inicio de sesión único (SSO) o no, el enlace **SSO de Logz.io** será diferente:

- Si ha optado por el inicio de sesión único:

    :::image type="content" source="./media/manage/sso-opt-in.png" alt-text="Participar en el inicio de sesión único.":::

- Si no ha optado por el inicio de sesión único:

    :::image type="content" source="./media/manage/sso-opt-out.png" alt-text="No participar en el inicio de sesión único.":::

## <a name="reconfigure-rules-for-logs-and-metrics"></a>Reconfiguración de las reglas para registros y métricas

Para cambiar las reglas de configuración, en **Configuración de Logz** seleccione **Registros y métricas**. Para obtener más información, consulte [Configuración de registros](./create.md#configure-logs).

:::image type="content" source="./media/manage/logs-metrics.png" alt-text="Configuración de registros y métricas.":::

## <a name="configure-diagnostic-settings"></a>Configuración de valores de diagnóstico

Para configurar los valores de diagnóstico, seleccione el recurso. En el panel izquierdo, seleccione **Configuración de diagnóstico**.

En la columna de **detalles de destino**, active la opción para **enviar a soluciones de asociados** para elegir Logz.io como destino. Esta opción solo está disponible después de crear un recurso de Logz.

:::image type="content" source="./media/manage/configure-diagnostics.png" alt-text="Configuración de los valores de diagnóstico.":::

## <a name="view-monitored-resources"></a>Vista de los recursos supervisados

Para ver la lista de recursos que envían registros a Logz.io (a través de las reglas de negocio configuradas), vaya a **Configuración de Logz** y seleccione **Recursos supervisados**.

:::image type="content" source="./media/manage/monitored-resources.png" alt-text="Configuración de recursos supervisados.":::

Puede filtrar la lista por tipo de recurso, nombre de grupo de recursos, ubicación y en función de si el recurso envía registros o no.

A continuación se describen las columnas que aparecen en la pantalla **Recursos supervisados**:

- **Nombre del recurso**: nombre de recurso de Azure.
- **Tipo de recurso**: tipo de recurso de Azure.
- **Grupo de recursos**: muestra el nombre del grupo de recursos para el recurso de Azure.
- **Región**: muestra la región o ubicación del recurso de Azure.
- **Registros para Logz**: indica si el recurso envía registros a Logz.io. Si el recurso no envía registros, este campo indica el motivo pertinente por el cual no se envían los registros a Logz.io. Los motivos pueden ser:
  - El recurso no permite el envío de registros: solo los registros de recursos de Azure para todos los tipos de recursos y [categorías de registro definidas aquí](../../azure-monitor/essentials/resource-logs-categories.md) se pueden configurar para enviar registros a Logz.io.
  - Límite de cinco configuraciones de diagnóstico alcanzado: cada recurso de Azure puede tener un máximo de cinco configuraciones de diagnóstico. Para más información, consulte [Creación de una configuración de diagnóstico para enviar registros de plataforma y métricas a diferentes destinos](../../azure-monitor/essentials/diagnostic-settings.md).
  - Error: un error impide que los registros se envíen a Logz.io.
  - Registros no configurados: solo los recursos que tienen las etiquetas de recursos adecuadas pueden enviar registros a Logz.io. Consulte [Configuración de registros](./create.md#configure-logs).
  - Región no admitida: el recurso de Azure está en una región que actualmente no admite el envío de registros a Logz.io.

Para editar la lista de etiquetas del recurso de Azure, seleccione **Editar etiquetas**. Puede agregar nuevas etiquetas o eliminar etiquetas existentes.

## <a name="monitor-vm-using-logzio-agent"></a>Supervisión de máquinas virtuales mediante el agente de Logz.io

Puede instalar agentes de Logz.io en las máquinas virtuales (VM) como una extensión. Vaya a **Configuración de Logz** y seleccione **Agente de máquina virtual**. Esta pantalla muestra la lista de todas las máquinas virtuales de la suscripción.

Para cada máquina virtual, se muestran los datos siguientes:

- **Nombre de recurso**: nombre de la máquina virtual.
- **Tipo de recurso**: tipo de recurso de Azure.
- **Grupo de recursos**: muestra el nombre del grupo de recursos para el recurso de Azure.
- **Región**: muestra la región o ubicación del recurso de Azure.
- **Versión del agente**: número de versión del agente de Logz.io.
- **Estado de la extensión**: indica si el agente de Logz.io está **Installed** (Instalado) o **Not installed** (No instalado) en la máquina virtual.
- **Registros en Logz**: especifica si el Logz.io envía registros a Logz.io.

:::image type="content" source="./media/manage/vm-agent.png" alt-text="Agente de máquina virtual.":::

### <a name="install-vm-agent"></a>Instalación del agente de máquina virtual

Para instalar el agente de máquina virtual, siga estos pasos.

1. Seleccione la máquina virtual en la que desea instalar el agente de Logz.io y, a continuación, seleccione **Instalar agente**.

   El portal le pide confirmación de que desea instalar el agente con la autenticación predeterminada.

1. Seleccione **OK** (Aceptar) para comenzar la instalación.

   Azure muestra el estado **Installing** (Instalando) hasta que el agente se instala y aprovisiona.

   Una vez instalado el agente de Logz.io, el estado cambia a **Installed** (Instalado).

1. Para comprobar que el agente de Logz.io se ha instalado, seleccione la VM y vaya a la ventana **Extensions** (Extensiones).

> [!NOTE]
> Después de instalar el agente de máquina virtual, si la máquina se detiene, la columna **Logs to Logz** (Registros en Logz) mostrará **Not Sending** (No se envía) para esa máquina virtual.

### <a name="uninstall-vm-agent"></a>Desinstalación del agente de máquina virtual

Puede desinstalar los agentes de Logz.io de una máquina virtual del **agente de máquina virtual**. Seleccione la máquina virtual y **Uninstall agent** (Desinstalar agente).

## <a name="create-logzio-sub-account"></a>Creación de una cuenta secundaria de Logz.io

Solo un administrador de la cuenta de Logz.io puede crear una cuenta secundaria. De forma predeterminada, el creador de la cuenta de Logz.io está establecido como administrador. Inicie sesión en la cuenta de Logz.io para conceder permiso de administrador a otros usuarios.

Para obtener más información, consulte [Logz.io Sub Accounts](https://logz.io/blog/diving-deeper-logz-io-sub-accounts/) (Cuentas secundarias de Logz.io) en la página web de Logz.io.

Puede crear una cuenta secundaria de Logz.io utilizando el botón **Overview** (Información general) > **Add Sub Account** (Añadir cuenta secundaria).

:::image type="content" source="./media/manage/create-sub-account-overview.png" alt-text="Creación de una cuenta secundaria de Logz.io en Información general":::

Otro método es ir a **Logz Configuration** (Configuración de Logz) y seleccionar **Add Sub account** (Agregar cuenta secundaria). Siga las indicaciones para configurar la cuenta secundaria y habilitar el envío de registros a través de la cuenta secundaria.

:::image type="content" source="./media/manage/create-sub-account-logz.png" alt-text="Creación de una cuenta secundaria de Logz.io desde la configuración de Logz.":::

## <a name="delete-logzio-sub-account"></a>Eliminación de una cuenta secundaria de Logz.io

1. Para el recurso de Logz.io, seleccione **Logz Configuration** (Configuración de Logz) y **Logz Sub accounts** (Cuentas secundarias de Logz).
1. Seleccione la cuenta secundaria que quiera eliminar.
1. Seleccione **Eliminar**.

    :::image type="content" source="./media/manage/delete-sub-account-1.png" alt-text="Eliminación de una cuenta secundaria de Logz.io desde la configuración de Logz.":::

1. Seleccione _yes_ (sí) para confirmar que quiere eliminar el recurso de Logz.io.
1. Seleccione **Eliminar**.

    :::image type="content" source="./media/manage/delete-sub-account-2.png" alt-text="Confirme para eliminar una cuenta secundaria de Logz.io.":::

Los registros ya no se envían a Logz.io y toda la facturación de Logz.io se detiene.

## <a name="delete-logzio-account"></a>Eliminación de una cuenta secundaria de Logz.io

1. En el recurso de Logz.io, seleccione **Overview** (Información general) y **Delete** (Eliminar).
1. Confirme que quiere eliminar el recurso de Logz.io.
1. Seleccione **Eliminar**.

    :::image type="content" source="./media/manage/delete-resource-1.png" alt-text="Elimine un recurso Logz.io desde Información general.":::

1. Seleccione _yes_ (sí) para confirmar que quiere eliminar el recurso de Logz.io.
1. Seleccione **Eliminar**.

    :::image type="content" source="./media/manage/delete-resource-2.png" alt-text="Confirme para eliminar un recurso de Logz.io.":::

> [!NOTE]
> La opción **Delete** (Eliminar) de la cuenta principal solo se activa cuando todas las cuentas secundarias asignadas a la cuenta principal ya están eliminadas. Para obtener más información sobre cómo eliminar cuentas secundarias, consulte [Eliminación de cuentas secundarias de Logz.io.](#delete-logzio-sub-account)

Una vez eliminado el recurso, los registros ya no se envían a Logz.io y toda la facturación de Logz.io en Azure Marketplace se detiene.

## <a name="get-support"></a>Obtención de soporte técnico

Para ponerse en contacto con el soporte técnico sobre la integración de Azure Logz.io, vaya a **Soporte + solución de problemas** y **Nueva solicitud de soporte**. Al seleccionar esta opción, se accede al [portal de Logz.io](https://app.logz.io/). Use el soporte técnico en chat o envíe un correo electrónico a [help@logz.io](mailto:help@logz.io).

## <a name="next-steps"></a>Pasos siguientes

- Para solucionar los problemas de la integración, consulte la [solución de problemas](troubleshoot.md).
