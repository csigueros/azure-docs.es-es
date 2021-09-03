---
title: Seguridad de datos en Azure Australia
description: Configuración de Azure en las regiones de Australia para cumplir con los requisitos específicos de las directivas, los reglamentos y la legislación de la administración pública de Australia.
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: d90dc7dd316e79407069a0c00f9d63659688a640
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2020
ms.locfileid: "117029198"
---
# <a name="data-security-in-azure-australia"></a>Seguridad de datos en Azure Australia

Los principios generales para proteger los datos del cliente son los siguientes:

* Protección de datos del cliente mediante cifrado
* Administración de secretos
* Restricción del acceso a datos

## <a name="encrypting-your-data"></a>Cifrado de datos

El cifrado de datos se puede aplicar en el nivel de disco (en reposo), en las bases de datos (en reposo y en tránsito), en las aplicaciones (en tránsito) y mientras se encuentra en la red (en tránsito). Hay varias maneras de lograr el cifrado en Azure:

|Servicio / característica|Descripción|
|---|---|
|Storage Service Encryption|Cifrado del servicio Azure Storage se habilita en el nivel de cuenta de almacenamiento, lo que conlleva que los blobs en bloques y los blobs en páginas se cifren automáticamente al escribir en Azure Storage. Cuando se lean los datos desde Azure Storage, el servicio de almacenamiento los descifrará antes de devolverlos. Utilice SSE para proteger los datos sin tener que modificar ni agregar código a las aplicaciones.|
|Azure Disk Encryption|El uso de Azure Disk Encryption permite cifrar los discos de datos y del sistema operativo usados por una máquina virtual de Azure. La integración con Azure Key Vault le proporciona control y le ayuda a administrar las claves de cifrado del disco.|
|Cifrado de nivel de aplicación del lado cliente|Cifrado de cliente está integrado en las bibliotecas cliente de almacenamiento de Java y de .NET, que pueden utilizar las API de Azure Key Vault, por lo que su implementación resulta sencilla. Utilice Azure Key Vault para obtener acceso a los secretos de Azure Key Vault para usuarios específicos mediante Azure Active Directory.|
|Cifrado en tránsito|El cifrado básico disponible para la conectividad con Azure Australia es compatible con el protocolo de seguridad de nivel de transporte (TLS) 1.2 y los certificados X.509. Los algoritmos criptográficos del Estándar federal de procesamiento de información (FIPS) 140-2 de nivel 1 también se utilizan para las conexiones de red de infraestructura entre centros de datos de Azure Australia.  Windows Server 2016, Windows 10, Windows Server 2012 R2, Windows 8.1 y los recursos compartidos de archivos de Azure pueden usar SMB 3.0 para el cifrado entre la VM y el recurso compartido de archivos. Utilice Cifrado de cliente para cifrar los datos antes de transferirlos al almacenamiento en una aplicación cliente y descifrarlos una vez transferidos desde este servicio.|
|VM de IaaS|Use Azure Disk Encryption. Active el cifrado del servicio de Storage para cifrar los archivos VHD que se usan para hacer una copia de seguridad de estos discos en Azure Storage, pero esto solo cifra datos recién escritos. Esto significa que si crea una máquina virtual y habilita el cifrado del servicio Storage en la cuenta de almacenamiento que contiene el archivo VHD, se cifrarán solo los cambios, no el archivo VHD original.|
|Cifrado de cliente|Es el método más seguro para cifrar los datos, porque estos se cifran antes del tránsito y en reposo. Sin embargo, requiere que agregue código a sus aplicaciones mediante el almacenamiento y quizá no desee hacerlo. En esos casos, puede utilizar HTTPS para los datos en tránsito y Storage Service Encryption para cifrar los datos en reposo. Cifrado de cliente supone también más carga en el cliente; así pues, debe tener en cuenta este factor en los planes de escalabilidad, especialmente si está cifrando y transfiriendo grandes cantidades de datos.|
|

Para más información acerca de las opciones de cifrado en Azure, consulte la [Guía de seguridad de almacenamiento](../storage/blobs/security-recommendations.md).

## <a name="protecting-data-by-managing-secrets"></a>Protección de datos mediante la administración de secretos

Una administración de claves segura es fundamental para proteger los datos en la nube. Los clientes deben esforzarse por simplificar la administración de claves y mantener el control de las claves usadas por los servicios y aplicaciones en la nube para cifrar los datos.

### <a name="managing-secrets"></a>Administración de secretos

* Use Key Vault para minimizar los riesgos de los secretos expuestos mediante archivos de configuración codificados de forma rígida, scripts o en código fuente. Azure Key Vault cifra claves (por ejemplo, claves de cifrado para Azure Disk Encryption) y secretos (por ejemplo, contraseñas) almacenándolas en módulos de seguridad de hardware (HSM) validados de FIPS 140-2 nivel 2. Para tener mayor seguridad, puede importar o generar las claves en estos HSM.
* Las plantillas y el código de aplicación solo deben contener referencias URI a los secretos (lo que significa que los secretos reales no están en los repositorios de código, configuración o código fuente). Esto impide ataques de suplantación de identidad de claves en repositorios internos o externos, como los robots de recolección de GitHub.
* Utilice controles de Azure RBAC seguros dentro de Key Vault. Si un trabajador de confianza deja la empresa o se traslada a un nuevo grupo dentro de la empresa, se le debería impedir el acceso a los secretos.  

Para más información, consulte [Azure Key Vault](azure-key-vault.md).

## <a name="isolation-to-restrict-data-access"></a>Aislamiento para restringir el acceso a datos

El aislamiento consiste en usar límites, segmentación y contenedores para restringir el acceso a datos solo para usuarios, servicios y aplicaciones autorizados. Por ejemplo, la separación entre los inquilinos es un mecanismo de seguridad esencial para plataformas multiinquilino en la nube como Microsoft Azure. El aislamiento lógico ayuda a impedir que un inquilino interfiera en las operaciones de cualquier otro inquilino.

#### <a name="per-customer-isolation"></a>Aislamiento por cliente

Azure implementa el control de acceso de red y la segregación mediante aislamiento de VLAN de capa 2, listas de control de acceso, equilibradores de carga y filtros IP.

Los clientes pueden aislar aún más sus recursos mediante suscripciones, grupos de recursos, redes virtuales y subredes.

Para más información acerca del aislamiento en Microsoft Azure, consulte [Aislamiento en la nube pública de Azure](../security/fundamentals/isolation-choices.md).

## <a name="next-steps"></a>Pasos siguientes

Revise el artículo sobre [Azure VPN Gateway](vpn-gateway.md).