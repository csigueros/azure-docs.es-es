---
title: Seguridad y gobernanza para empresas
titleSuffix: Azure Machine Learning
description: 'Use Azure Machine Learning de forma segura: autenticación, autorización, seguridad de red, cifrado de datos y supervisión.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jmartens
author: aashishb
ms.reviewer: larryfr
ms.date: 06/02/2021
ms.openlocfilehash: 27bd8124c0b78d1fecd1f7027104c3b5c9b8a8a1
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/06/2021
ms.locfileid: "111541511"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Seguridad de empresa y gobernanza para Azure Machine Learning

En este artículo conocerá las características de seguridad y gobernanza disponibles con Azure Machine Learning. Estas características son útiles para los administradores, DevOps y MLOps que quieren crear una configuración segura que cumpla con las directivas empresariales. Con Azure Machine Learning y la plataforma de Azure, puede:

* Restringir el acceso a los recursos y las operaciones por cuenta de usuario o grupo
* Restringir de las comunicaciones de red entrantes y salientes
* Cifrar los datos en tránsito y en reposo
* Análisis de vulnerabilidades
* Aplicar y auditar directivas de configuración

## <a name="restrict-access-to-resources-and-operations"></a>Restricción del acceso a los recursos y las operaciones

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) es el proveedor de servicios de identidad para Azure Machine Learning. Permite crear y administrar los objetos de seguridad (usuarios, grupos, entidades de servicio e identidades administradas) que se usan para _autenticarse_ en los recursos de Azure. Si Azure AD se ha configurado para usar la autenticación multifactor, también se admite aquí.

Este es el proceso de autenticación de Azure Machine Learning mediante la autenticación multifactor en Azure AD:

1. El cliente inicia sesión en Azure AD y recibe un token de Azure Resource Manager.
1. El cliente presenta el token a Azure Resource Manager y a todas las instancias de Azure Machine Learning.
1. Machine Learning Service proporciona un token de servicio de Machine Learning al destino de proceso de usuario (por ejemplo, clúster de proceso de Azure Machine Learning). El destino de proceso de usuario usa este token para volver a llamar a Machine Learning Service una vez completada la ejecución. El ámbito se limita al área de trabajo.

[![Autenticación en Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

Cada área de trabajo lleva asociada una [identidad administrada](../active-directory/managed-identities-azure-resources/overview.md) asignada por el sistema con el mismo nombre que el área de trabajo. Esta identidad administrada se usa para acceder de manera segura a los recursos que usa el área de trabajo. Tiene los permisos de Azure RBAC siguientes sobre los recursos asociados:

| Recurso | Permisos |
| ----- | ----- |
| Área de trabajo | Colaborador |
| Cuenta de almacenamiento | Colaborador de datos de blobs de almacenamiento |
| Almacén de claves | Acceso a todas las claves, secretos, certificados |
| Azure Container Registry | Colaborador |
| El grupo de recursos que contiene el área de trabajo | Colaborador |

La identidad administrada asignada por el sistema se usa para la autenticación interna de servicio a servicio entre Azure Machine Learning y otros recursos de Azure. Los usuarios no pueden acceder al token de identidad y no lo pueden usar para acceder a estos recursos. Los usuarios solo pueden acceder a los recursos a través de las [API de control o de plano de datos de Azure Machine Learning](how-to-assign-roles.md), si tienen permisos RBAC suficientes.

La identidad administrada debe tener permisos de colaborador en el grupo de recursos que contiene el área de trabajo para aprovisionar los recursos asociados e [implementar Azure Container Instances para los puntos de conexión de servicio Web](how-to-deploy-azure-container-instance.md).

No se recomienda que los administradores revoquen el acceso de la identidad administrada a los recursos mencionados en la tabla anterior. Puede restaurar el acceso mediante la [operación de resincronización de claves](how-to-change-storage-access-key.md).

> [!NOTE]
> Si las áreas de trabajo de Azure Machine Learning tienen destinos de proceso (clúster de proceso, instancia de proceso, Azure Kubernetes Service, etc.) que se crearon __antes del 14 de mayo de 2021__, también puede tener una cuenta adicional de Azure Active Directory. El nombre de la cuenta comienza por `Microsoft-AzureML-Support-App-` y tiene acceso de nivel de colaborador a la suscripción para cada región del área de trabajo.
> 
> Si el área de trabajo no tiene asociada ninguna instancia de Azure Kubernetes Service (AKS), puede eliminar esta cuenta de Azure AD de forma segura. 
> 
> Si el área de trabajo tiene asociados clústeres de AKS _que se crearon antes del 14 de mayo de 2021_, __no elimine esta cuenta de Azure AD__. En este escenario, primero debe eliminar el clúster de AKS y volver a crearlo para poder eliminar la cuenta de Azure AD.

Puede aprovisionar el área de trabajo para usar una identidad administrada asignada por el usuario y conceder roles adicionales a la identidad administrada, por ejemplo, para acceder a su propia instancia de Azure Container Registry para imágenes base de Docker. Para obtener más información, consulte [Uso de identidades administradas para el control de acceso](how-to-use-managed-identities.md).

También puede configurar identidades administradas para usarlas con el clúster de proceso de Azure Machine Learning. Esta identidad administrada es independiente de la identidad administrada del área de trabajo. Con un clúster de proceso, la identidad administrada se usa para acceder a recursos como los almacenes de datos seguros a los que el usuario que ejecuta el trabajo de entrenamiento podría no tener acceso. Para obtener más información, consulte [Acceso a datos basado en identidad para los servicios de almacenamiento de Azure](how-to-identity-based-data-access.md).

> [!TIP]
> Hay algunas excepciones al uso de Azure AD y Azure RBAC en Azure Machine Learning:
> * Tiene la opción de habilitar el acceso __SSH__ a los recursos de proceso, como el clúster de proceso y la instancia de proceso de Azure Machine Learning. El acceso SSH se basa en pares de claves pública y privada, no en Azure AD. El acceso SSH no se rige por Azure RBAC.
> * Puede autenticarse en modelos implementados como servicios web (puntos de conexión de inferencia) mediante autenticación basada en __claves__ o __tokens__. Las claves son cadenas estáticas, mientras que los tokens se recuperan mediante un objeto de seguridad de Azure AD. Para obtener más información, consulte [Configuración de la autenticación para los modelos implementados como servicios web](how-to-authenticate-web-service.md).

Para más información, consulte los siguientes artículos.
* [Autenticación para áreas de trabajo de Azure Machine Learning](how-to-setup-authentication.md)
* [Administración del acceso a Azure Machine Learning](how-to-assign-roles.md)
* [Conexión a los servicios de almacenamiento](how-to-access-data.md)
* [Uso de Azure Key Vault para los secretos durante el entrenamiento](how-to-use-secrets-in-runs.md)
* [Uso de la identidad administrada de Azure AD con Azure Machine Learning](how-to-use-managed-identities.md)
* [Uso de la identidad administrada de Azure AD con el servicio web](how-to-use-azure-ad-identity.md)

## <a name="network-security-and-isolation"></a>Aislamiento y seguridad de la red

Puede usar [Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md) para restringir el acceso de red a los recursos de Azure Machine Learning. Las redes virtuales le permiten crear entornos de red que están aislados de manera parcial o total del Internet público. Esto reduce la superficie expuesta a ataques de la solución, así como las posibilidades de que se produzca una filtración de datos.

Puede usar una puerta de enlace de red privada virtual (VPN) para conectar clientes individuales, o su propia red, a la red virtual.

El área de trabajo de Azure Machine Learning puede usar [Azure Private Link](../private-link/private-link-overview.md) para crear un punto de conexión privado detrás de la red virtual. Esto proporciona un conjunto de direcciones IP privadas que se pueden usar para acceder al área de trabajo desde dentro de la red virtual. Algunos de los servicios en los que se basa Azure Machine Learning también pueden usar Azure Private Link, pero algunos se basan en grupos de seguridad de red o en el enrutamiento definido por el usuario.

Para obtener más información, vea los documentos siguientes:

* [Información general sobre la privacidad y el aislamiento de la red virtual](how-to-network-security-overview.md)
* [Protección de los recursos de un área de trabajo](how-to-secure-workspace-vnet.md)
* [Protección de un entorno de entrenamiento](how-to-secure-training-vnet.md)
* [Protección del entorno de inferencia](how-to-secure-inferencing-vnet.md)
* [Uso de Studio en una red virtual protegida](how-to-enable-studio-virtual-network.md)
* [Uso de un DNS personalizado](how-to-custom-dns.md)
* [Configuración del firewall](how-to-access-azureml-behind-firewall.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>Cifrado de datos

Azure Machine Learning usa varios recursos de proceso y almacenes de datos en la plataforma de Azure. Para obtener más información sobre cómo cada uno de ellos admite el cifrado de datos en reposo y en tránsito, consulte [Cifrado de datos con Azure Machine Learning](concept-data-encryption.md).

Al implementar modelos como servicios web, puede habilitar la seguridad de la capa de transporte (TLS) para cifrar los datos en tránsito. Para obtener más información, consulte [Configuración de un servicio web seguro](how-to-secure-web-service.md).

## <a name="vulnerability-scanning"></a>Examen de vulnerabilidades

[Azure Security Center](../security-center/security-center-introduction.md) proporciona administración unificada de la seguridad y protección avanzada contra amenazas para cargas de trabajo en la nube híbrida. Para Azure Machine Learning, debe habilitar el examen del recurso de [Azure Container Registry](../container-registry/container-registry-intro.md) y los recursos de Azure Kubernetes Service. Para obtener más información, consulte [Examen de imágenes de Azure Container Registry mediante Security Center](../security-center/defender-for-container-registries-introduction.md) e [Integración de Azure Kubernetes Service en Security Center](../security-center/defender-for-kubernetes-introduction.md).

## <a name="audit-and-manage-compliance"></a>Auditoría y administración del cumplimiento

[Azure Policy](../governance/policy/index.yml) es una herramienta de gobierno que le permite asegurarse de que los recursos de Azure son compatibles con las directivas. Puede establecer directivas para permitir o aplicar configuraciones específicas, como si el área de trabajo de Azure Machine Learning usa un punto de conexión privado. Para obtener más información sobre Azure Policy, consulte la documentación de [Azure Policy](../governance/policy/overview.md). Para obtener más información sobre las directivas específicas de Azure Machine Learning, consulte [Auditoría y administración del cumplimiento con Azure Policy](how-to-integrate-azure-policy.md).

## <a name="next-steps"></a>Pasos siguientes

* [Protección de los servicios web Azure Machine Learning con TLS](how-to-secure-web-service.md)
* [Consumir un modelo de Machine Learning implementado como un servicio web](how-to-consume-web-service.md)
* [Uso de Azure Machine Learning con Azure Firewall](how-to-access-azureml-behind-firewall.md)
* [Uso de Azure Machine Learning con Azure Virtual Network](how-to-network-security-overview.md)
* [Cifrado de datos en reposo y en tránsito](concept-data-encryption.md)
* [Compilación de una API de recomendaciones en tiempo real en Azure](/azure/architecture/reference-architectures/ai/real-time-recommendation)
