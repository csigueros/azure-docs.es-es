---
title: Procedimientos recomendados de seguridad y cumplimiento de Azure Batch
description: Obtenga información sobre los procedimientos recomendados y sugerencias útiles para mejorar la seguridad de las soluciones de Azure Batch.
ms.date: 09/01/2021
ms.topic: conceptual
ms.openlocfilehash: f9bfbeb7a89e3ca1bc71001b173926302548a988
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435553"
---
# <a name="batch-security-and-compliance-best-practices"></a>Procedimientos recomendados de seguridad y cumplimiento de Azure Batch

En este artículo se proporcionan instrucciones y procedimientos recomendados para mejorar la seguridad cuando se usa Azure Batch.

De forma predeterminada, las cuentas de Azure Batch tienen un punto de conexión público y son accesibles públicamente. Cuando se crea un grupo de Azure Batch, el grupo se aprovisiona en la subred especificada de una red virtual de Azure. A las máquinas virtuales del grupo de Batch se accede mediante direcciones IP públicas creadas por Batch. Los nodos de ejecución de un grupo pueden comunicarse entre sí cuando sea necesario; por ejemplo, para ejecutar tareas de instancias múltiples, pero los nodos de un grupo no se pueden comunicar con máquinas virtuales fuera del grupo.

:::image type="content" source="media/security-best-practices/typical-environment.png" alt-text="Diagrama que muestra un entorno de Batch típico.":::

Hay muchas características disponibles para ayudarle a crear una implementación más segura de Azure Batch. Para restringir el acceso a los nodos y reducir la detectabilidad de los nodos desde Internet, [aprovisione el grupo sin direcciones IP públicas](batch-pool-no-public-ip-address.md). Los nodos de ejecución se pueden comunicar de forma segura con otras máquinas virtuales o con una red local mediante el [aprovisionamiento del grupo en una subred de una red virtual de Azure](batch-virtual-network.md). Además, puede habilitar el [acceso privado desde redes virtuales](private-connectivity.md) desde un servicio con Azure Private Link.

:::image type="content" source="media/security-best-practices/secure-environment.png" alt-text="Diagrama que muestra un entorno de Batch más seguro.":::

## <a name="general-security-related-best-practices"></a>Procedimientos recomendados generales relacionados con la seguridad

### <a name="pool-configuration"></a>Configuración del grupo

Muchas características de seguridad solo están disponibles para los grupos configurados mediante [Configuración de máquina virtual](nodes-and-pools.md#configurations) y no para los grupos con configuración de Cloud Services. Se recomienda el uso de grupos de configuración de máquina virtual, que usan [conjuntos de escalado de máquinas virtuales](../virtual-machine-scale-sets/overview.md), siempre que sea posible.

### <a name="batch-account-authentication"></a>Autenticación de cuentas de Batch

El acceso a la cuenta de Batch admite dos métodos de autenticación: clave compartida y [Azure Active Directory (Azure AD)](batch-aad-auth.md).

Se recomienda usar Azure AD para la autenticación de la cuenta de Batch. Algunas funcionalidades de Batch requieren este método de autenticación, incluidas muchas de las características relacionadas con la seguridad que se describen aquí. El mecanismo de autenticación de API de servicio para una cuenta de Batch puede restringirse solo a Azure AD mediante la propiedad [allowedAuthenticationModes](/rest/api/batchmanagement/batch-account/create). Cuando se establece esta propiedad, se rechazarán las llamadas API que usan la autenticación de clave compartida.

### <a name="batch-account-pool-allocation-mode"></a>Modo de asignación de grupos de la cuenta de Batch

Al crear una cuenta de Batch, puede elegir entre dos [modos de asignación de grupos](accounts.md#batch-accounts):

- **Servicio Batch**: es la opción predeterminada, en la que los recursos del conjunto de escalado de máquinas virtuales o el servicio en la nube subyacentes que se usan para asignar y administrar los nodos de grupo se crean en suscripciones internas y no están directamente visibles en Azure Portal. Solo están visibles los grupos de Batch y los nodos.
- **Suscripción de usuario**: los recursos subyacentes del conjunto de escalado de máquinas virtuales o el servicio en la nube se crean en la misma suscripción que la cuenta de Batch. Por lo tanto, estos recursos están visibles en la suscripción, además de los recursos de Batch correspondientes.

En el modo de suscripción de usuario, las máquinas virtuales de Batch y otros recursos se crean directamente en la suscripción cuando se crea un grupo. El modo de suscripción de usuario es necesario si desea crear grupos de Batch con Azure Reserved VM Instances, usar Azure Policy en los recursos del conjunto de escalado de máquinas virtuales o administrar la cuota de núcleos de la suscripción (compartida entre todas las cuentas de Batch de la suscripción). Para crear una cuenta de Batch en modo de suscripción de usuario, también debe registrar su suscripción a Azure Batch y asociar la cuenta a una instancia de Azure Key Vault.

## <a name="restrict-network-endpoint-access"></a>Restricción del acceso al punto de conexión de red

### <a name="batch-network-endpoints"></a>Puntos de conexión de red de Batch

Tenga en cuenta que, de forma predeterminada, los puntos de conexión con direcciones IP públicas se usan para comunicarse con las cuentas de Batch, los grupos de Batch y los nodos de grupo.

### <a name="batch-account-api"></a>API de la cuenta de Batch

 Cuando se crea una cuenta de Batch, se crea un punto de conexión público que se usa para invocar la mayoría de las operaciones de la cuenta mediante una [API REST](/rest/api/batchservice/). El punto de conexión de la cuenta tiene una dirección URL base con el formato `https://{account-name}.{region-id}.batch.azure.com`. Se protege el acceso a la cuenta de Batch con el cifrado de la comunicación con el punto de conexión de la cuenta mediante HTTPS y cada solicitud se autentica mediante la autenticación de clave compartida o de Azure Active Directory (Azure AD).

### <a name="azure-resource-manager"></a>Azure Resource Manager

Además de las operaciones específicas de una cuenta de Batch, las [operaciones de administración](/rest/api/batchmanagement/) se aplican a una o varias cuentas de Batch. Se accede a estas operaciones de administración mediante Azure Resource Manager.

Las operaciones de administración de Batch mediante Azure Resource Manager se cifran con HTTPS y se autentican todas las solicitudes mediante la autenticación de Azure AD.

### <a name="batch-pool-nodes"></a>Nodos del grupo de Batch

El servicio Batch se comunica con un agente del nodo de Batch que se ejecuta en cada nodo del grupo. Por ejemplo, el servicio indica al agente del nodo que ejecute una tarea, detenga una tarea u obtenga los archivos de una tarea. La comunicación con el agente del nodo está habilitada por uno o más equilibradores de carga, cuyo número depende del número de nodos de un grupo. El equilibrador de carga reenvía la comunicación al nodo deseado, donde cada nodo se especifica mediante un número de puerto único. De forma predeterminada, los equilibradores de carga tienen direcciones IP públicas asociadas. También puede acceder de forma remota a los nodos del grupo mediante RDP o SSH (este acceso está habilitado de forma predeterminada, con comunicación mediante equilibradores de carga).

### <a name="restricting-access-to-batch-endpoints"></a>Restricción del acceso a los puntos de conexión de Batch 

Hay varias funcionalidades disponibles para limitar el acceso a los distintos puntos de conexión de Batch, especialmente cuando la solución usa una red virtual. 

#### <a name="use-private-endpoints"></a>Usar puntos de conexión privados

[Azure Private Link](../private-link/private-link-overview.md) permite acceder a los servicios PaaS de Azure y a los servicios hospedados en Azure que son propiedad de los clientes, o a los servicios de asociados, mediante un punto de conexión privado de la red virtual. Puede usar Private Link para restringir el acceso a una cuenta de Batch desde dentro de la red virtual o desde cualquier red virtual emparejada. También se puede acceder a los recursos asignados a Private Link en el entorno local a través de un emparejamiento privado a través de VPN o Azure ExpressRoute.

Para usar puntos de conexión privados, se debe configurar adecuadamente una cuenta de Batch cuando se crea; la configuración de acceso a la red pública debe estar deshabilitada. Una vez creada, los puntos de conexión privados se pueden crear y asociar a la cuenta de Batch. Para más información, consulte [Uso de puntos de conexión privados con cuentas de Azure Batch](private-connectivity.md).

#### <a name="create-pools-in-virtual-networks"></a>Creación de grupos en redes virtuales

Los nodos de ejecución de un grupo pueden comunicarse entre sí, por ejemplo, para ejecutar tareas de varias instancias, sin necesidad de una red virtual independiente. Sin embargo, de forma predeterminada, los nodos de un grupo no se pueden comunicar con máquinas virtuales que están fuera del grupo en una red virtual y tienen direcciones IP privadas, como los servidores de licencias o los servidores de archivos.

Para permitir que los nodos de ejecución se comuniquen de manera segura con otras máquinas virtuales o con una red local, puede configurar el grupo para que esté en una subred de una red virtual de Azure.

Cuando los grupos tienen puntos de conexión de dirección IP pública, la subred debe permitir la comunicación de entrada desde el servicio Batch para poder programar tareas y realizar otras operaciones en los nodos de ejecución y la comunicación de salida para comunicarse con Azure Storage u otros recursos, en función de lo que necesite la carga de trabajo. Para los grupos de la configuración de la máquina virtual, Batch agrega grupos de seguridad de red en el nivel de las interfaces de red conectadas a los nodos de ejecución. Estos grupos de seguridad de red tienen reglas que se deben habilitar:

- Tráfico TCP de entrada desde las direcciones IP del servicio Batch
- Tráfico TCP de entrada para el acceso remoto
- Tráfico de salida en cualquier puerto a la red virtual (se puede modificar según las reglas de grupo de seguridad de red de nivel de subred)
- Tráfico de salida en cualquier puerto a Internet (se puede modificar según las reglas de grupo de seguridad de red de nivel de subred)

No es preciso que especifique grupos de seguridad de red en el nivel de subred de la red virtual, ya que Batch configura los suyos propios. Si tiene un grupo de seguridad de red asociado a la subred en la que se implementan los nodos de ejecución de Batch o si desea aplicar reglas de NSG personalizadas que reemplacen a las reglas aplicadas de forma predeterminada, debe configurar el grupo de seguridad de red, como mínimo, con las reglas de seguridad de entrada y de salida para permitir la comunicación del servicio Batch con los nodos de ejecución y la comunicación de los nodos del grupo con Azure Storage.

Para más información, consulte [Creación de un grupo de Azure Batch en una red virtual](batch-virtual-network.md).

#### <a name="create-pools-with-static-public-ip-addresses"></a>Creación de grupos con direcciones IP públicas estáticas

De forma predeterminada, las direcciones IP públicas asociadas a los grupos son dinámicas; se crean cuando se crea un grupo y se pueden agregar o eliminar direcciones IP cuando se cambia el tamaño de un grupo. Cuando las aplicaciones de las tareas que se ejecutan en los nodos del grupo necesitan acceder a servicios externos, puede que sea necesario restringir el acceso a esos servicios a direcciones IP específicas.  En este caso, no será posible administrar direcciones IP dinámicas.

Puede crear recursos de direcciones IP públicas estáticas en la misma suscripción que la cuenta de Batch antes de la creación del grupo. Después, puede especificar estas direcciones al crear el grupo.

Para más información, consulte [Creación de un grupo de Azure Batch con direcciones IP públicas especificadas](create-pool-public-ip.md).

#### <a name="create-pools-without-public-ip-addresses"></a>Creación de grupos sin direcciones IP públicas

De forma predeterminada, todos los nodos de ejecución de un grupo de configuración de máquinas virtuales de Azure Batch tienen asignada una o varias direcciones IP públicas. El servicio Batch usa estos puntos de conexión para programar tareas y para la comunicación con los nodos de ejecución, incluido el acceso de salida a Internet.

Para restringir el acceso a estos nodos y reducir la detectabilidad de estos nodos desde Internet, puede aprovisionar el grupo sin direcciones IP públicas.

Para más información, consulte [Creación de un grupo de Azure Batch sin direcciones IP públicas](batch-pool-no-public-ip-address.md).

#### <a name="limit-remote-access-to-pool-nodes"></a>Limitación del acceso remoto a los nodos del grupo

De forma predeterminada, Batch permite a un usuario del nodo con conectividad de red conectarse externamente a un nodo de ejecución de un grupo de Batch mediante RDP o SSH.

Para limitar el acceso remoto a los nodos, use uno de los métodos siguientes:

- Configure la opción [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) para denegar el acceso. Se asociará el grupo de seguridad de red adecuado al grupo.
- Cree el grupo [sin direcciones IP públicas](batch-pool-no-public-ip-address.md). De forma predeterminada, no se puede acceder a estos grupos fuera de la red virtual.
- Asocie un grupo de seguridad de red a la red virtual para denegar el acceso a los puertos RDP y SSH.
- No cree ningún usuario en el nodo. Sin usuarios del nodo, no es posible el acceso remoto.

## <a name="encrypt-data"></a>Cifrado de datos

### <a name="encrypt-data-in-transit"></a>Cifrado de los datos en tránsito

Todas las comunicaciones con el punto de conexión de la cuenta de Batch (o mediante Azure Resource Manager) deben usar HTTPS. Debe usar `https://` en las direcciones URL de la cuenta de Batch especificadas en las API al conectarse al servicio Batch.

Los clientes que se comunican con el servicio Batch se deben configurar para usar la Seguridad de la capa de transporte (TLS) 1.2.

### <a name="encrypt-batch-data-at-rest"></a>Cifrado de los datos en reposo de Batch

Parte de la información especificada en las API de Batch, como los certificados de cuenta, los metadatos de los trabajos y las tareas, y las líneas de comandos de tareas, se cifra automáticamente cuando la almacena el servicio Batch. De forma predeterminada, estos datos se cifran mediante claves administradas por la plataforma de Azure Batch exclusivas de cada cuenta de Batch.

También puede cifrar estos datos mediante [claves administradas por el cliente](batch-customer-managed-key.md). [Azure Key Vault](../key-vault/general/overview.md) se usa para generar y almacenar la clave, con el identificador de clave registrado en la cuenta de Batch.

### <a name="encrypt-compute-node-disks"></a>Cifrado de los discos del nodo de ejecución

Los nodos de ejecución de Batch tienen dos discos de forma predeterminada: un disco del sistema operativo y el disco SSD temporal local. Los [archivos y directorios](files-and-directories.md) administrados por Batch se encuentran en el disco SSD temporal, que es la ubicación predeterminada para archivos como los archivos de salida de la tarea. Las aplicaciones de la tarea de Batch pueden utilizar la ubicación predeterminada en el disco SSD o en el disco del sistema operativo.

Para mayor seguridad, cifre estos discos con una de estas funcionalidades de Azure Disk Encryption:

- [Cifrado en reposo con claves administradas por la plataforma](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [Cifrado en el host con una clave administrada por la plataforma](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure Disk Encryption](disk-encryption.md)

## <a name="securely-access-services-from-compute-nodes"></a>Acceso seguro a los servicios desde los nodos de ejecución

Los nodos de Batch pueden acceder de forma segura a las credenciales que se almacenan en [Azure Key Vault](../key-vault/general/overview.md), que las aplicaciones de las tareas pueden utilizar para acceder a otros servicios. Se usa un certificado para conceder acceso a Key Vault a los nodos del grupo. Al [habilitar la rotación automática de certificados en el grupo de Batch](automatic-certificate-rotation.md), las credenciales se renovarán automáticamente. Esta es la opción recomendada para que los nodos de Batch accedan a las credenciales almacenadas en Azure Key Vault, aunque también puede [configurar nodos de Batch para acceder de forma segura a credenciales y secretos con un certificado](credential-access-key-vault.md) sin rotación automática de certificados.

## <a name="governance-and-compliance"></a>Gobernanza y cumplimiento

### <a name="compliance"></a>Cumplimiento

Para ayudar a los clientes a cumplir sus propias obligaciones de cumplimiento en los sectores y mercados regulados de todo el mundo, Azure mantiene una [amplia cartera de ofertas de cumplimiento](https://azure.microsoft.com/overview/trusted-cloud/compliance).

Estas ofertas se basan en varios tipos de controles, como certificaciones oficiales, atestaciones, validaciones, autorizaciones y evaluaciones generadas por empresas de auditoría de terceros independientes, así como enmiendas contractuales, autoevaluaciones y documentos de instrucciones para el cliente generados por Microsoft. Revise la [información general completa de las ofertas de cumplimiento](https://aka.ms/AzureCompliance) para determinar cuáles pueden ser adecuadas para sus soluciones de Batch.

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](../governance/policy/overview.md) ayuda a aplicar los estándares de la organización y a evaluar el cumplimiento a gran escala. Entre los casos de uso comunes de Azure Policy se incluye la implementación de la gobernanza para la coherencia de los recursos, el cumplimiento normativo, la seguridad, el costo y la administración.

En función del modo de asignación de grupos y de los recursos a los que se debe aplicar una directiva, utilice Azure Policy con Batch de una de las siguientes maneras:

- Directamente, mediante el recurso Microsoft.Batch/batchAccounts. Se puede usar un subconjunto de las propiedades de una cuenta de Batch. Por ejemplo, la directiva puede incluir las regiones de cuentas de Batch válidas, el modo de asignación de grupos permitido y si hay una red pública habilitada para las cuentas.
- Indirectamente, mediante el recurso Microsoft.Compute/virtualMachineScaleSets. Las cuentas de Batch con el modo de asignación de grupos de suscripciones de usuario pueden tener la directiva establecida en los recursos del conjunto de escalado de máquinas virtuales que se crean en la suscripción de la cuenta de Batch. Por ejemplo, los tamaños de máquina virtual permitidos y asegurarse de que se ejecutan determinadas extensiones en cada nodo del grupo.

## <a name="next-steps"></a>Pasos siguientes

- Consulte [Línea de base de seguridad de Azure para Batch](security-baseline.md).
- Más información en [Procedimientos recomendados de Azure Batch](best-practices.md).
