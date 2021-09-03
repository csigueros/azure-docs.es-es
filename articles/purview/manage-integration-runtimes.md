---
title: Creación y administración de entornos de ejecución de integración
description: En este artículo se explican los pasos para crear y administrar entornos de ejecución de integración en Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: 2c1f967e596b4ba19d121f3c0332259b92f78d06
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730617"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>Creación y administración de un entorno de ejecución de integración autohospedado

En este artículo se describe cómo crear y administrar un entorno de ejecución de integración autohospedado (SHIR) que permite examinar orígenes de datos en Azure Purview.

> [!NOTE]
> Integration Runtime de Purview no se puede compartir con una instancia de Integration Runtime de Azure Data Factory o Azure Synapse Analytics en la misma máquina. Debe instalarse en una máquina independiente.

## <a name="create-a-self-hosted-integration-runtime"></a>Creación de una instancia de Integration Runtime autohospedada

1. En la página principal de Purview Studio, seleccione **Management Center** (Centro de administración) en el panel de navegación izquierdo.

2. En **Sources and scanning** (Orígenes y examen) en el panel izquierdo, seleccione **Integration runtimes** (Entornos de ejecución de integración) y, a continuación, **+ New** (+ Nuevo).

   :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="Haga clic en el entorno de ejecución de integración.":::

3. En la página **Integration runtime setup** (Configuración de Integration Runtime), seleccione **Self-Hosted** (Autohospedado) para crear un entorno de ejecución de integración autohospedado y, luego, seleccione **Continuar** (Continuar).

   :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="Cree un nuevo entorno de ejecución de integración autohospedado.":::

4. Escriba un nombre para el entorno de ejecución de integración y seleccione Create (Crear).

5. En la página **Integration Runtime settings** (Parámetros de Integration Runtime), siga los pasos descritos en la sección **Manual setup** (Instalación manual). Tendrá que descargar el entorno de ejecución de integración desde el sitio de descarga en una máquina virtual o la máquina en la que desee ejecutarlo.

   :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="Obtener la clave.":::

   - Copie y pegue la clave de autenticación.

   - Descargue el entorno de ejecución de integración autohospedado desde [Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) en una máquina Windows local. Ejecute al programa de instalación. Se admiten versiones del entorno de ejecución de integración autohospedado, como 5.4.7803.1 y 5.6.7795.1. 

   - En la página **Registro de Integration Runtime (autohospedado)** , pegue una de las dos claves guardadas anteriormente y seleccione **Registrar**.

     :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="Introducir la clave.":::

   - En la página **Nuevo nodo de Integration Runtime (autohospedado)** , seleccione **Finalizar**.

6. Verá la siguiente ventana cuando el entorno de ejecución de integración autohospedado se haya registrado correctamente:

   :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="Se ha registrado correctamente.":::

## <a name="networking-requirements"></a>Requisitos de red

Para que funcione correctamente, la máquina del entorno de ejecución de integración autohospedado se tendrá que conectar a varios recursos:

* Los orígenes que quiera examinar mediante el entorno de ejecución de integración autohospedado.
* Cualquier instancia de Azure Key Vault que se use para almacenar las credenciales del recurso de Purview.
* La cuenta administrada de Storage y los recursos de centro de eventos creados por Purview.

Los recursos administrados de Storage y centro de eventos se pueden encontrar en la suscripción, en un grupo de recursos que contiene el nombre del recurso de Purview. Azure Purview usa estos recursos para ingerir los resultados del examen, entre otras muchas cosas, por lo que el entorno de ejecución de integración autohospedado tendrá que poder conectarse directamente con estos recursos.

Estos son los dominios y puertos que se deben permitir por medio de firewalls corporativos y de máquina.

> [!NOTE]
> Para los dominios indicados con "\<managed Purview storage account>", agregará el nombre de la cuenta de almacenamiento administrada asociada al recurso de Purview. Puede encontrar este recurso en el portal. Busque en los grupos de recursos un grupo denominado managed-rg-\<your Purview Resource name>. Por ejemplo: managed-rg-contosoPurview. Usará el nombre de la cuenta de almacenamiento en este grupo de recursos.
> 
> Para los dominios indicados con "\<managed Event Hub resource>", agregará el nombre de la cuenta de centro de eventos administrada asociada al recurso de Purview. Puede encontrarlo en el mismo grupo de recursos que la cuenta de almacenamiento administrada.

| Nombres de dominio                  | Puertos de salida | Descripción                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net` | 443            | Infraestructura global que usa Purview para ejecutar sus exámenes. Se necesita un carácter comodín, ya que no hay ningún recurso dedicado. |
| `<managed Event Hub resource>.servicebus.windows.net` | 443            | Purview lo usa para conectarse al bus de servicio asociado. Se tendrá en cuenta al permitir el dominio anterior, pero si usa puntos de conexión privados, tendrá que probar el acceso a este dominio único.|
| `*.frontend.clouddatahub.net` | 443            | Infraestructura global que usa Purview para ejecutar sus exámenes. Se necesita un carácter comodín, ya que no hay ningún recurso dedicado. |
| `<managed Purview storage account>.core.windows.net`          | 443            | Lo usa el entorno de ejecución de integración autohospedado para conectarse a la cuenta de almacenamiento de Azure administrada.|
| `<managed Purview storage account>.queue.core.windows.net` | 443            | Colas que usa Purview para ejecutar el proceso de examen. |
| `<your Key Vault Name>.vault.azure.net` | 443           | Obligatorio si alguna credencial se almacena en Azure Key Vault. |
| `download.microsoft.com` | 443           | Opcional para las actualizaciones de SHIR. |
| Varios dominios | Dependiente          | Dominios para cualquier otro origen al que se conectará SHIR. |
  
  
> [!IMPORTANT]
> En la mayoría de los entornos, también tendrá que confirmar que el DNS está configurado correctamente. Para confirmarlo, puede usar **nslookup** desde la máquina SHIR a fin de comprobar la conectividad a cada uno de los dominios anteriores. Cada operación nslookup debe devolver la dirección IP del recurso. Si usa [puntos de conexión privados](catalog-private-link.md), se debe devolver la dirección IP privada y no la dirección IP pública. Si no se devuelve ninguna dirección IP, o si al usar puntos de conexión privados se devuelve la dirección IP pública, tendrá que solucionar la asociación entre DNS y VNET, o el emparejamiento entre el punto de conexión privado y la red virtual.

## <a name="manage-a-self-hosted-integration-runtime"></a>Administración de un entorno de ejecución de integración autohospedado

Para editar un entorno de ejecución de integración autohospedado, vaya a **Integration runtimes** (Entornos de ejecución de integración) en **Management center** (Centro de administración), seleccione el entorno de ejecución de integración y haga clic en Editar. Ahora puede actualizar la descripción, copiar la clave o volver a generar nuevas claves.

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="Editar IR.":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="Editar los detalles de IR.":::

Para eliminar un entorno de ejecución de integración autohospedado, vaya a **Integration runtimes** (Entornos de ejecución de integración) en el centro de administración, seleccione el entorno de ejecución de integración y, a continuación, haga clic en **Eliminar**. Una vez eliminado el entorno de ejecución de integración, se producirá un error en cualquier examen en curso que dependa de él.

## <a name="next-steps"></a>Pasos siguientes

[Cómo detectan los exámenes los recursos eliminados](concept-detect-deleted-assets.md)
