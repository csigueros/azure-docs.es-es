---
title: Azure Key Vault en Azure Australia
description: Guía sobre la configuración y el uso de Azure Key Vault para la administración de claves dentro de las regiones de Australia a fin de cumplir los requisitos específicos de la política, las normativas y las leyes de la administración pública de Australia.
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: f14a2264769e1bb1fca8e4372ba1359e1b99e560
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727287"
---
# <a name="azure-key-vault-in-azure-australia"></a>Azure Key Vault en Azure Australia

El almacenamiento seguro de las claves criptográficas y la administración de su ciclo de vida son elementos críticos en los sistemas criptográficos.  El servicio que proporciona esta funcionalidad en Azure es Azure Key Vault. Key Vault guarda conformidad con el acceso de seguridad de IRAP y está certificado por ACSC como PROTEGIDO.  En este artículo se describen los principales aspectos que se deben tener en cuenta al usar Key Vault para cumplir los [controles del manual de seguridad de la información](https://acsc.gov.au/infosec/ism/) (ISM) de Australian Signals Directorate (ASD).

Azure Key Vault es un servicio en la nube que protege las claves de cifrado y los secretos. Dado que estos datos son confidenciales y críticos para la empresa, Key Vault permite proteger el acceso a los almacenes de claves, de modo que solo se admiten las aplicaciones y los usuarios autorizados. Hay tres artefactos principales que administra y controla Key Vault:

- claves
- secrets
- certificates

Este artículo se centra en la administración de claves mediante Key Vault.

![Azure Key Vault](media/azure-key-vault-overview.png)

*Diagrama 1: Azure Key Vault*

## <a name="key-design-considerations"></a>Consideraciones clave sobre el diseño

### <a name="deployment-options"></a>Opciones de implementación

Existen dos opciones para crear instancias de Azure Key Vault. Ambas opciones usan la familia nCipher nShield de módulos de seguridad de hardware (HSM), son estándares federales de procesamiento de información (FIPS) validados y están aprobadas para almacenar claves en entornos protegidos. Las opciones son:

- **Almacenes protegidos por software:** FIPS 140-2 nivel 1 validado. Claves almacenadas en un HSM. Las operaciones de cifrado y descifrado se realizan en recursos de proceso de Azure.
- **Almacenes protegidos con HSM:** FIPS 140-2 nivel 2 validado. Claves almacenadas en un HSM. Las operaciones de cifrado y descifrado se realizan en el HSM.

Key Vault admite claves Rivest-Shamir-Adleman (RSA) y criptografía de curva elíptica (ECC). Las claves RSA de 2048 bits son el valor predeterminado, pero hay una opción avanzada para las claves RSA de 3072-bits, RSA de 4096 bits y ECC.  Aunque todas las claves cumplen los controles de ISM, se prefieren las claves de curva elíptica.

### <a name="resource-operations"></a>Operaciones de recursos

Son varias las personas que intervienen en Azure Key Vault:

- **Administrador de Key Vault:** administra el ciclo de vida del almacén.
- **Administrador de claves:** administra el ciclo de vida de las claves del almacén.
- **Desarrollador/operador:** integra las claves del almacén en aplicaciones y servicios.
- **Auditor:** supervisa el uso de las claves y el acceso a estas.
- **Aplicaciones:** usan las claves para proteger la información.

Azure Key Vault está protegido con dos interfaces independientes:

- **Plano de administración:** este plano se ocupa de la administración del almacén y cuenta con la protección de RBAC de Azure.
- **Plano de datos:** este plano se ocupa de la administración y el acceso a los artefactos del almacén.  Está protegido mediante la directiva de acceso de Key Vault.

Tal y como se exige en el ISM, para que una persona que llama (un usuario o una aplicación) pueda acceder al almacén de claves con cualquiera de los planos, se necesitan la autorización y la autenticación adecuadas.

RBAC de Azure tiene un rol integrado para Key Vault, [colaborador de Key Vault](../role-based-access-control/built-in-roles.md#key-vault-contributor), para controlar la administración de las instancias de Key Vault. Se recomienda la creación de roles personalizados alineados con roles más específicos para administrar las instancias de Key Vault.

>[!WARNING]
>Cuando se habilita el acceso a las claves mediante la directiva de acceso de Key Vault, el usuario o la aplicación tienen ese acceso a todas las claves del almacén de claves (por ejemplo, si un usuario tiene acceso para "eliminar", puede eliminar todas las claves).  Por lo tanto, se deben implementar varios almacenes de claves para que se alineen con los límites y los dominios de seguridad.

### <a name="networking"></a>Redes

Puede configurar firewalls y redes virtuales de Key Vault para controlar el acceso al plano de datos.  Puede permitir el acceso a los usuarios o las aplicaciones de redes especificadas y denegarlo a los usuarios o las aplicaciones de todas las demás redes. Los [servicios de confianza](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services) son una excepción a este control si está habilitada la opción "permitir servicios de confianza".  El control de red virtual no se aplica al plano de administración.

El acceso a las instancias de Key Vault se debe restringir explícitamente al conjunto mínimo de redes que tienen usuarios o aplicaciones que requieren acceso a las claves.

### <a name="bring-your-own-key-byok"></a>Bring Your Own Key (BYOK)

Key Vault admite BYOK.  BYOK permite a los usuarios importar claves desde sus infraestructuras de claves existentes. El conjunto de herramientas de BYOK admite la transferencia y la importación de claves de forma segura desde un HSM externo (por ejemplo, las claves generadas con una estación de trabajo sin conexión) en Key Vault.

Vaya al Centro de descarga de Microsoft y [descargue el conjunto de herramientas de BYOK para Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45345) para Australia. El nombre del paquete para descargar y su hash de paquete SHA-256 correspondiente:

|Nombre del paquete|Hash SHA-256|
|---|---|
|KeyVault-BYOK-Tools-Australia.zip|CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A|
|

### <a name="key-vault-auditing-and-logging"></a>Auditoría y registro de Key Vault

ACSC exige que las entidades de la Commonwealth usen los servicios de Azure adecuados para realizar la supervisión y la generación de informes en tiempo real en sus cargas de trabajo de Azure.

Para habilitar el registro es necesario activar la opción de diagnóstico **_"AuditEvent"_** en Valores de clave.  Los eventos de auditoría se registrarán en la cuenta de almacenamiento especificada.  El período de **_"RetentionInDays"_** debe establecerse según la directiva de retención de datos.  Las [operaciones](../key-vault/general/logging.md#interpret-your-key-vault-logs) en el plano de administración y el plano de datos se auditan y registran. La [solución de Azure Key Vault de Azure Monitor](../azure-monitor/insights/key-vault-insights-overview.md) se puede usar para revisar los registros de AuditEvent de Key Vault.  Se pueden usar otros diversos servicios de Azure para procesar y distribuir elementos AuditEvent de Key Vault.

### <a name="key-rotation"></a>Rotación de claves

El almacenamiento de claves en Key Vault proporcionaba un único punto para mantener las claves fuera de las aplicaciones que permiten que se actualicen sin afectar al comportamiento de estas. El almacenamiento de claves en Azure Key Vault permite diversas estrategias para admitir la rotación de claves:

- Manualmente
- Mediante programación a través de API
- Scripts de automatización (por ejemplo, con PowerShell y Azure Automation)

Estas opciones permiten la rotación de las claves de forma periódica para satisfacer los requisitos de cumplimiento, o ad hoc, en caso de que se tenga la sospecha de que las claves pudieran estar en peligro.

#### <a name="key-rotation-strategies"></a>Estrategias de rotación de claves

Es importante desarrollar una estrategia de rotación de claves adecuada para las claves que se almacenan en KeyVault.  El uso de la clave equivocada puede dar lugar a que la información se descifre incorrectamente, y el extravío de las claves puede provocar la pérdida total del acceso a la información.  Algunos ejemplos de estrategias de rotación de claves para diferentes escenarios son:

- **Datos en proceso**: se transmite información volátil entre dos partes.  Cuando se rota una clave, ambas partes deben tener un mecanismo para recuperar sincrónicamente las claves actualizadas del almacén de claves.
- **Datos en reposo:** una entidad almacena datos cifrados y los descifra en el futuro para usarlos.  Cuando se va a rotar una clave, los datos deben descifrarse con la clave anterior y, luego, cifrarse con la nueva clave rotada.  Existen enfoques para minimizar el impacto del proceso de descifrado y cifrado mediante claves de cifrado de claves (consulte el ejemplo).  Microsoft administra la mayor parte del proceso relacionado con la rotación de claves para Azure Storage (consulte...)
- **Claves de acceso:** varios servicios de Azure tienen claves de acceso que se pueden almacenar en Key Vault (por ejemplo, CosmosDB).  Los servicios de Azure tienen claves de acceso principal y secundaria.  Es importante que ambas claves no se roten al mismo tiempo.  Por lo tanto, se debe rotar una clave y, después de un tiempo, y que el funcionamiento de la clave se haya comprobado, rotar la segunda.

### <a name="high-availability"></a>Alta disponibilidad

El ISM cuenta con varios controles que se relacionan con la continuidad empresarial.
Azure Key Vault tiene varias capas de redundancia con contenido replicado en la región y en la [región emparejada](../best-practices-availability-paired-regions.md) secundaria.

Cuando el almacén de claves esté en estado de conmutación por error, esté en modo de solo lectura y vuelva al modo de solo lectura, el servicio principal se restaurará.

El ISM tiene varios controles relacionados con la copia de seguridad.  Es importante desarrollar y ejecutar planes de copia de seguridad y restauración adecuados para los almacenes y sus claves.

## <a name="key-lifecycle"></a>Ciclo de vida de una clave

### <a name="key-operations"></a>Operaciones con claves

Key Vault admiten las siguientes operaciones en una clave:

- **crear:** permite a un cliente crear una clave en Key Vault. El valor de la clave lo genera y almacena Key Vault y no se entrega al cliente. Las claves asimétricas pueden crearse en Key Vault.
- **importar:** permite a un cliente importar una clave existente en Key Vault. Se pueden importar claves asimétricas en Key Vault mediante una serie de métodos de empaquetado diferentes dentro de una construcción JWK.
- **actualizar:** permite a un cliente con los permisos suficientes modificar los metadatos (atributos de la clave) asociados con una clave almacenada previamente en Key Vault.
- **eliminar:** permite a un cliente con los permisos suficientes eliminar una clave de Key Vault.
- **enumerar:** permite a un cliente enumerar todas las claves de un determinado almacén de Key Vault.
- **enumerar versiones:** permite a un cliente enumerar todas las versiones de una clave determinada en un determinado almacén de Key Vault.
- **obtener:** permite a un cliente recuperar las partes públicas de una clave determinada en un almacén de Key Vault.
- **copia de seguridad:** exporta una clave en un formato protegido.
- **restaurar:** importa una clave desde una copia de seguridad previa.

Existe un conjunto de permisos correspondiente que se puede conceder a los usuarios, las entidades de servicio o las aplicaciones que usan entradas de control de acceso de Key Vault para permitirles ejecutar operaciones de claves.

Key Vault tiene una característica de eliminación temporal que permite la recuperación de las claves y los almacenes eliminados. De forma predeterminada, la **_eliminación temporal_** no está habilitada, pero una vez habilitada, los objetos se mantienen durante 90 días (el período de retención) aunque aparentemente están eliminados.  Un permiso adicional para **_"purgar"_** permite la eliminación permanente de claves si la opción de **_"Protección de purga"_** está deshabilitada.

Al crear o importar una clave existente, se crea una nueva versión de la clave.

### <a name="cryptographic-operations"></a>Operaciones criptográficas

Key Vault también admite operaciones criptográficas mediante claves:

- **firmar y comprobar:** esta operación es de "firma de hash" o "comprobación de hash". Key Vault no admite el hash de contenido como parte de la creación de firmas.
- **cifrado y ajuste de claves:** esta operación se usa para proteger otra clave.
- **cifrar y descifrar:** la clave almacenada se usa para cifrar o descifrar un único bloque de datos.

Existe un conjunto de permisos correspondiente que se puede conceder a los usuarios, las entidades de servicio o las aplicaciones que usan entradas de control de acceso de Key Vault para permitirles ejecutar operaciones criptográficas.

Hay tres atributos clave que se pueden establecer para controlar si una clave está habilitada y se puede usar en las operaciones criptográficas:

- **enabled**
- **nbf:** not before enabled (no habilitado antes) antes de la fecha especificada
- **exp:** fecha de expiración

## <a name="storage-and-keys"></a>Almacenamiento y claves

Las claves administradas por el cliente son más flexibles y permiten acceder a las claves que se van a controlar, y evaluarlas. También permiten la auditoría de las claves de cifrado usadas para proteger los datos.
Existen tres aspectos relativos al almacenamiento y las claves almacenadas en Key Vault:

- Claves de la cuenta de almacenamiento administradas por Key Vault
- Azure Storage Service Encryption (SSE) para datos en reposo
- Discos administrados y Azure Disk Encryption

La administración de claves de la cuenta de Azure Storage de Key Vault es una extensión del servicio de claves de Key Vault que admite la sincronización y regeneración (rotación) de las claves de la cuenta de almacenamiento.  La [integración de Azure Storage con Azure Active Directory](../storage/blobs/authorize-access-azure-active-directory.md) (versión preliminar) se recomienda cuando se publique, ya que proporciona una mayor seguridad y facilidad de uso.
SSE usa dos claves para administrar el cifrado de datos en reposo:

- Clave de cifrado de claves (KEK)
- Claves de cifrado de datos (DEK)

Aunque Microsoft administra las DEK, SSE tiene una opción para usar KEK administradas por el cliente que se pueden almacenar en Key Vault. Esto permite la rotación de claves en Azure Key Vault según las directivas de cumplimiento adecuadas. Cuando se rotan las claves, Azure Storage vuelve a cifrar la clave de cifrado de esa cuenta de almacenamiento. Esto no significa que se vuelvan a cifrar todos los datos y no se necesite ninguna otra acción del usuario.

SSE se usa con discos administrados, pero no se admiten claves administradas por el cliente.  El cifrado de discos administrados puede realizarse mediante Azure Disk Encryption con claves KEK administradas por el cliente en Key Vault.

## <a name="next-steps"></a>Pasos siguientes

Revise el artículo sobre la [federación de identidades](identity-federation.md).

Revise la documentación y los tutoriales adicionales de Azure Key Vault de la [biblioteca de referencias](reference-library.md).