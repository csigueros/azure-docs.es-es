---
title: Uso de cuentas de almacenamiento administradas por el cliente en Log Analytics de Azure Monitor
description: Use su propia cuenta de almacenamiento para los escenarios de Log Analytics
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 09/03/2020
ms.openlocfilehash: b8fa3c329afe26979e6f557d075aff478e79d10e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727227"
---
# <a name="using-customer-managed-storage-accounts-in-azure-monitor-log-analytics"></a>Uso de cuentas de almacenamiento administradas por el cliente en Log Analytics de Azure Monitor

Log Analytics se basa en Azure Storage en varios escenarios. Normalmente, este uso se administra automáticamente. Sin embargo, en algunos casos es necesario proporcionar y administrar la propia cuenta de almacenamiento, también denominada cuenta de almacenamiento administrada por el cliente. En este documento se trata el uso del almacenamiento administrado por el cliente para registros de WAD/LAD, Private Link y el cifrado de clave administrada por el cliente (CMK). 

> [!NOTE]
> Se recomienda no depender del contenido que Log Analytics carga en el almacenamiento administrado por el cliente, dado que el formato y el contenido pueden cambiar.

## <a name="ingesting-azure-diagnostics-extension-logs-wadlad"></a>Ingesta de registros de extensión de Azure Diagnostics (WAD/LAD)
Los agentes de extensión de Azure Diagnostics (también denominados WAD y LAD para los agentes de Windows y Linux, respectivamente) recopilan varios registros del sistema operativo y los almacenan en una cuenta de almacenamiento administrada por el cliente. Después, puede ingerir estos registros en Log Analytics para revisarlos y analizarlos.
### <a name="how-to-collect-azure-diagnostics-extension-logs-from-your-storage-account"></a>Recopilación de registros de extensión de Azure Diagnostics desde la cuenta de almacenamiento
Conecte la cuenta de almacenamiento a su área de trabajo de Log Analytics como origen de datos de almacenamiento mediante [Azure Portal](../agents/diagnostics-extension-logs.md#collect-logs-from-azure-storage) o mediante una llamada a la [API de Storage Insights](/rest/api/loganalytics/storage-insights/create-or-update).

Tipos de datos admitidos:
* syslog
* Eventos de Windows
* Service Fabric
* Eventos de ETW
* Registros IIS

## <a name="using-private-links"></a>Uso de vínculos privados
Las cuentas de almacenamiento administradas por el cliente se utilizan para ingerir registros personalizados o registros de IIS cuando se usan vínculos privados para conectarse a recursos de Azure Monitor. En primer lugar, el proceso de ingesta de estos tipos de datos carga los registros en una cuenta de Azure Storage intermediaria y, a continuación, se ingieren en un área de trabajo. 

### <a name="using-a-customer-managed-storage-account-over-a-private-link"></a>Uso de una cuenta de almacenamiento administrada por el cliente a través de Private Link
#### <a name="workspace-requirements"></a>Requisitos del área de trabajo
Al conectarse a Azure Monitor a través de un vínculo privado, los agentes de Log Analytics solo pueden enviar registros a las áreas de trabajo accesibles a través de un vínculo privado. Este requisito significa que debe:
* Configurar un objeto de ámbito de Private Link de Azure Monitor (AMPLS)
* Conectarlo a las áreas de trabajo
* Conectar el AMPLS a la red a través de un vínculo privado 

Para más información sobre el procedimiento para la configuración de AMPLS, consulte [Uso de Azure Private Link para conectar redes a Azure Monitor de forma segura](./private-link-security.md). 

#### <a name="storage-account-requirements"></a>Requisitos de la cuenta de almacenamiento
Para que la cuenta de almacenamiento se conecte correctamente a su vínculo privado, debe:
* Encontrarse en la red virtual o en una red emparejada y conectada a la red virtual a través de un vínculo privado.
* Debe encontrarse en la misma región que el área de trabajo a la que está vinculada.
* Permitir que Azure Monitor tenga acceso a la cuenta de almacenamiento. Si decide permitir que solo las redes seleccionadas tengan acceso a la cuenta de almacenamiento, debe seleccionar la excepción: "Permitir que los servicios de Microsoft de confianza accedan a esta cuenta de almacenamiento".
![Imagen de servicios de MS de confianza de la cuenta de almacenamiento](./media/private-storage/storage-trust.png)
* Si el área de trabajo también controla el tráfico de otras redes, debe configurar la cuenta de almacenamiento para permitir el tráfico entrante procedente de las redes pertinentes o Internet.
* Coordine la versión de TLS entre los agentes y la cuenta de almacenamiento; se recomienda que envíe datos a Log Analytics mediante TLS 1.2 o superior. Revise la [guía específica de la plataforma](./data-security.md#sending-data-securely-using-tls-12) y, si es necesario, [configure los agentes para que usen TLS 1.2](../agents/agent-windows.md#configure-agent-to-use-tls-12). Si por alguna razón no es posible, configure la cuenta de almacenamiento para que acepte TLS 1.0.

### <a name="using-a-customer-managed-storage-account-for-cmk-data-encryption"></a>Uso de una cuenta de almacenamiento administrada por el cliente para el cifrado de datos de CMK
Azure Storage cifra todos los datos en reposo de una cuenta de almacenamiento. De forma predeterminada, usa claves administradas por Microsoft (MMK) para cifrar los datos; sin embargo, Azure Storage también le permite usar CMK desde Azure Key Vault para cifrar los datos de almacenamiento. Puede importar sus propias claves a Azure Key Vault, o puede usar las API de Azure Key Vault para generarlas.
#### <a name="cmk-scenarios-that-require-a-customer-managed-storage-account"></a>Escenarios de CMK que requieren una cuenta de almacenamiento administrada por el cliente
* Cifrado de consultas de alertas de registro con CMK
* Cifrado de consultas guardadas con CMK

#### <a name="how-to-apply-cmk-to-customer-managed-storage-accounts"></a>Cómo aplicar CMK a cuentas de almacenamiento administradas por el cliente
##### <a name="storage-account-requirements"></a>Requisitos de la cuenta de almacenamiento
La cuenta de almacenamiento y el almacén de claves deben estar en la misma región, pero pueden estar en distintas suscripciones. Para más información sobre cifrado de Azure Storage y la administración de claves, consulte [Cifrado de Azure Storage para datos en reposo](../../storage/common/storage-service-encryption.md).

##### <a name="apply-cmk-to-your-storage-accounts"></a>Aplicación de CMK a cuentas de almacenamiento
Para configurar la cuenta de Azure Storage para usar CMK con Azure Key Vault, use [Azure Portal](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json), [PowerShell](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json) o la [CLI](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json). 

## <a name="link-storage-accounts-to-your-log-analytics-workspace"></a>Vinculación de cuentas de almacenamiento al área de trabajo de Log Analytics
### <a name="using-the-azure-portal"></a>Uso de Azure Portal
En Azure Portal, abra el menú del área de trabajo y seleccione *Cuentas de almacenamiento vinculadas*. Se abrirá una hoja que muestra las cuentas de almacenamiento vinculadas por los casos de uso mencionados anteriormente (ingesta sobre Private Link, aplicación de CMK a consultas guardadas o alertas).
![Imagen de la hoja de cuentas de almacenamiento vinculadas](./media/private-storage/all-linked-storage-accounts.png) Al seleccionar un elemento de la tabla se abrirán los detalles de su cuenta de almacenamiento, donde puede establecer o actualizar la cuenta de almacenamiento vinculada para este tipo. 
![Imagen de vinculación de una hoja de cuentas de almacenamiento](./media/private-storage/link-a-storage-account-blade.png) Puede usar la misma cuenta para los distintos casos de uso si lo prefiere.

### <a name="using-the-azure-cli-or-rest-api"></a>Uso de la CLI de Azure o la API REST
También puede vincular una cuenta de almacenamiento a su área de trabajo a través de la [CLI de Azure](/cli/azure/monitor/log-analytics/workspace/linked-storage) o la [API REST](/rest/api/loganalytics/linkedstorageaccounts).

Los valores de dataSourceType aplicables son:
* CustomLogs: para usar la cuenta de almacenamiento para los registros personalizados y la ingesta de registros de IIS
* Consulta: para usar la cuenta de almacenamiento para almacenar las consultas guardadas (necesario para el cifrado de CMK)
* Alertas: para usar la cuenta de almacenamiento para almacenar las alertas basadas en registros (necesario para el cifrado de CMK)


## <a name="managing-linked-storage-accounts"></a>Administración de cuentas de almacenamiento vinculadas

### <a name="create-or-modify-a-link"></a>Creación o modificación de un vínculo
Una vez que vincule una cuenta de almacenamiento a un área de trabajo, Log Analytics comenzará a usarla en lugar de la cuenta de almacenamiento propiedad del servicio. Puede 
* Registrar varias cuentas de almacenamiento para distribuir la carga de registros entre ellas
* Reutilizar la misma cuenta de almacenamiento para varias áreas de trabajo

### <a name="unlink-a-storage-account"></a>Desvinculación de una cuenta de almacenamiento
Para dejar de usar una cuenta de almacenamiento, desvincule el almacenamiento del área de trabajo. Al desvincular todas las cuentas de almacenamiento de un área de trabajo, Log Analytics intentará basarse en las cuentas de almacenamiento administradas por el servicio. Si la red tiene acceso limitado a Internet, es posible que estos almacenamientos no estén disponibles y que se produzca un error en cualquier escenario que se base en el almacenamiento.

### <a name="replace-a-storage-account"></a>Reemplazo de una cuenta de almacenamiento
Para reemplazar una cuenta de almacenamiento usada para la ingesta,
1.  **Cree un vínculo a una nueva cuenta de almacenamiento.** Los agentes de registro obtendrán la configuración actualizada y comenzarán a enviar datos también al almacenamiento nuevo. El proceso puede tardar unos minutos.
2.  **Después, desvincule la cuenta de almacenamiento anterior para que los agentes dejen de escribir en la cuenta quitada.** El proceso de ingesta sigue leyendo los datos de esta cuenta hasta que se realice la ingesta de todos. No elimine la cuenta de almacenamiento hasta que vea que se ingirieron todos los registros.

### <a name="maintaining-storage-accounts"></a>Mantenimiento de cuentas de almacenamiento
#### <a name="manage-log-retention"></a>Administración de la retención de registros
Cuando use su propia cuenta de almacenamiento, la retención depende de usted. Log Analytics no eliminará los registros almacenados en el almacenamiento privado. En su lugar, debe configurar una directiva para controlar la carga según sus preferencias.

#### <a name="consider-load"></a>Consideración de la carga
Las cuentas de almacenamiento pueden controlar cierta carga de solicitudes de lectura y escritura antes de empezar a limitar las solicitudes (para obtener más información, consulte [Objetivos de escalabilidad y rendimiento de Blob Storage](../../storage/common/scalability-targets-standard-account.md)). La limitación afecta al tiempo que se tarda en ingerir registros. Si la cuenta de almacenamiento está sobrecargada, registre una cuenta de almacenamiento adicional para distribuir la carga entre ellas. Para supervisar la capacidad y el rendimiento de la cuenta de almacenamiento, revise su [Información en Azure Portal](../../storage/common/storage-insights-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json).

### <a name="related-charges"></a>Cargos relacionados
Las cuentas de almacenamiento se cobran según el volumen de datos almacenados, el tipo de almacenamiento y el tipo de redundancia. Para detalles, consulte [Precios de los blobs en bloques](https://azure.microsoft.com/pricing/details/storage/blobs) y [Precios de Azure Table Storage](https://azure.microsoft.com/pricing/details/storage/tables).


## <a name="next-steps"></a>Pasos siguientes

- Información sobre el [uso de Azure Private Link para conectar redes a Azure Monitor de forma segura](private-link-security.md)
- Información sobre las [claves administradas por el cliente de Azure Monitor](../logs/customer-managed-keys.md)