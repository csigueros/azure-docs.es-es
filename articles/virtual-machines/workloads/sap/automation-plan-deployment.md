---
title: Planeación de la implementación de SAP con el marco de automatización en Azure
description: Prepárese para usar el marco de automatización de la implementación de SAP en Azure. Los pasos incluyen la planeación de la administración de credenciales, la estructura de DevOps y los escenarios de implementación.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: 5f31987c1ce3cdde11cf97fa994ed8c030a0020e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725305"
---
# <a name="plan-your-deployment-of-sap-automation-framework"></a>Planeación de la implementación del marco de automatización de SAP

Hay varias consideraciones para ejecutar el [marco de automatización de la implementación de SAP en Azure](automation-deployment-framework.md); esto incluye temas como la administración de credenciales de implementación y el diseño de redes virtuales.

Para más información sobre las condiciones genéricas de diseño de SAP en Azure, visite [Introducción a un escenario de adopción de SAP](/azure/cloud-adoption-framework/scenarios/sap).

> [!NOTE]
> La implementación de Terraform usa plantillas de Terraform proporcionadas por Microsoft desde el [repositorio del marco de automatización de la implementación de SAP](https://github.com/Azure/sap-hana/). Las plantillas usan archivos de parámetros con la información específica del sistema para realizar la implementación.

## <a name="credentials-management"></a>Administración de credenciales

El marco de automatización usa [entidades de servicio](#service-principal-creation) de Azure Active Directory (Azure AD) para la implementación. Puede usar diferentes credenciales de implementación para cada [zona de cargas de trabajo](#workload-zone-structure). El marco mantiene estas credenciales en el almacén de claves del [implementador](automation-deployment-framework.md#deployment-components) de Azure Key Vault. A continuación, el marco recupera estas credenciales dinámicamente durante el proceso de implementación.

El marco de automatización también usa credenciales para las cuentas de máquina virtual (VM) predeterminadas, tal como se proporciona en el momento de la creación de la máquina virtual. Estas credenciales incluyen:

| Credential: | Ámbito | Storage | Identificador | Descripción |
| ---------- | ----- | ------- | ---------- | ----------- |
| Usuario local | Implementador | - | Usuario actual | Arranca el implementador. |
| [Entidad de servicio](#service-principal-creation) | Entorno | Almacén de claves del implementador | Identificador de entorno | Realiza actividades de implementación. |
| Credenciales de máquina virtual | Entorno | Almacén de claves de la carga de trabajo | Identificador de entorno | Establece la información predeterminada del usuario de la máquina virtual. |

### <a name="service-principal-creation"></a>Creación de la entidad de servicio

Cree las entidades de servicio:

1. Inicie sesión en la [interfaz de la línea de comandos de Azure (CLI de Azure)](/cli/azure/) con una cuenta que tenga los privilegios adecuados para crear una entidad de servicio.
1. Cree una nueva entidad de servicio mediante la ejecución del comando `az ad sp create-for-rbac`. Asegúrese de usar un nombre de descripción para `--name`. Por ejemplo:
    ```azurecli
    az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --name="DEV-Deployment-Account"
    ```
1. Observe la salida. Necesita el identificador de aplicación (`appId`), la contraseña (`password`) y el identificador de inquilino (`tenant`) para el paso siguiente. Por ejemplo:
    ```json
    {
        "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "displayName": "DEV-Deployment-Account",
        "name": "http://DEV-Deployment-Account",
        "password": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```
1. Asigne el rol Administrador de acceso de usuario a la entidad de servicio. Por ejemplo: 
    ```azurecli
    az role assignment create --assignee <your-application-ID> --role "User Access Administrator"
    ```

Para más información, consulte la [documentación de la CLI de Azure para crear una entidad de servicio](/cli/azure/create-an-azure-service-principal-azure-cli).

## <a name="devops-structure"></a>Estructura de DevOps

Las plantillas de automatización de Terraform se encuentran en el [repositorio del marco de automatización de implementación de SAP](https://github.com/Azure/sap-hana/). En la mayoría de los casos de uso, considere este repositorio como de solo lectura y no modifique sus plantillas de Terraform.

Para sus propios archivos de parámetros, es un procedimiento recomendado mantener estos archivos en un repositorio de control de código fuente que administre. Clone el [repositorio del marco de automatización de la implementación de SAP](https://github.com/Azure/sap-hana/) y el repositorio en la misma carpeta raíz. A continuación, [cree una estructura de carpetas adecuada](#folder-structure).

> [!IMPORTANT]
> El nombre del archivo de parámetros se convierte en el nombre del archivo de estado de Terraform. Asegúrese de usar un nombre de archivo de parámetro único por este motivo.

### <a name="folder-structure"></a>Estructura de carpetas

En la siguiente jerarquía de carpetas de ejemplo se muestra cómo estructurar los archivos de configuración junto con los archivos del marco de automatización. La primera carpeta de nivel superior, denominada **sap-hana**, tiene los archivos del marco de automatización que no es necesario cambiar en la mayoría de los casos de uso. La segunda carpeta de nivel superior, denominada **WORKSPACES**, contiene subcarpetas con archivos de configuración para la configuración de implementación.

| Nombre de la carpeta | Contenido | Descripción |
| ----------- | -------- | ----------- |
| Ninguno (nivel raíz) | Archivos de configuración, archivos de plantilla | Carpeta raíz de todos los sistemas que se administran desde este entorno de implementación. |
| CONFIGURATION | Archivos de configuración compartida | Una carpeta compartida para hacer referencia a archivos de configuración personalizados desde varios lugares. Por ejemplo, archivos de configuración de tamaño de disco personalizados. |
| IMPLEMENTADOR | Archivos de configuración para el implementador | Una carpeta con [archivos de configuración del implementador](automation-configure-control-plane.md) para todas las implementaciones que administra el entorno. Asigne un nombre a cada subcarpeta por la convención de nomenclatura **Entorno - Región - Virtual Network**. Por ejemplo, **PROD-WEEU-DEP00-INFRASTRUCTURE**. |
| LIBRARY | Archivos de configuración de la biblioteca SAP | Una carpeta con [archivos de configuración de la biblioteca SAP](automation-configure-control-plane.md) para todas las implementaciones que administra el entorno. Asigne un nombre a cada subcarpeta por la convención de nomenclatura **Entorno - Región - Virtual Network**. Por ejemplo, **PROD-WEEU-SAP-LIBRARY**. |
| HORIZONTAL | Archivos de configuración para implementaciones horizontales | Una carpeta con [archivos de configuración para todas las zonas de la carga de trabajo](automation-deploy-workload-zone.md) que administra el entorno. Asigne un nombre a cada subcarpeta por la convención de nomenclatura **Entorno - Región - Virtual Network**. Por ejemplo, **PROD-WEEU-SAP00-INFRASTRUCTURE**. |
| SYSTEM | Archivos de configuración para los sistemas SAP | Una carpeta con [archivos de configuración para todas las implementaciones de identificación del sistema SAP (SID)](automation-configure-system.md) que administra el entorno. Asigne un nombre a cada subcarpeta por la convención de nomenclatura **Entorno - Región - Virtual Network - SID**. Por ejemplo, **PROD-WEEU-SAPO00-ABC**. |

:::image type="content" source="./media/automation-plan-deployment/folder-structure.png" alt-text="Captura de pantalla de la estructura de carpetas de ejemplo, en la que se muestran carpetas independientes para SAP HANA y varios entornos de carga de trabajo.":::

## <a name="supported-deployment-scenarios"></a>Escenarios de implementación habilitados

El marco de automatización admite la [implementación en escenarios nuevos y existentes](automation-new-vs-existing.md).

## <a name="azure-regions"></a>Regiones de Azure

Antes de implementar una solución, es importante tener en cuenta qué regiones de Azure usar. Las diferentes regiones de Azure pueden estar en el ámbito en función de su escenario específico.

El marco de automatización admite implementaciones en varias regiones de Azure. Cada región hospeda:

- La infraestructura de implementación
- Biblioteca SAP con archivos de estado y medios de instalación
- De 1 a N zonas de cargas de trabajo
- De 1 a N sistemas SAP en las zonas de cargas de trabajo

## <a name="deployment-environments"></a>Entornos de implementación

Si va a admitir varias zonas de carga de trabajo en una región, use un identificador único para el entorno de implementación y la biblioteca SAP. No use el identificador para la zona de carga de trabajo. Por ejemplo, use `MGMT` con fines de administración.

El marco de automatización también admite tener el entorno de implementación y la biblioteca SAP en suscripciones independientes de las zonas de carga de trabajo.

El entorno de implementación proporciona los siguientes servicios:

- Una máquina virtual de implementación, que realiza implementaciones de Terraform y la configuración de Ansible.
- Un almacén de claves, que contiene información de identidad de la entidad de servicio para su uso por las implementaciones de Terraform.
- Un componente de Azure Firewall, que proporciona conectividad saliente a Internet.

El archivo de configuración de implementación define la región, el nombre del entorno y la información de red virtual. Por ejemplo:

```json
{
    "infrastructure": {
        "environment": "MGMT",
        "region": "westeurope",
        "vnets": {
            "management": {
                "address_space": "0.0.0.0/25",
                "subnet_mgmt": {
                    "prefix": "0.0.0.0/28"
                },
                "subnet_fw": {
                    "prefix": "0.0.0.0/26"
                }
            }
        }
    },
    "options": {
        "enable_deployer_public_ip": true
    },
    "firewall_deployment": true
}
```

Para obtener más información, vea la [explicación detallada de cómo configurar el implementador](automation-configure-control-plane.md).

## <a name="sap-library-configuration"></a>Configuración de la biblioteca SAP

La biblioteca SAP proporciona almacenamiento para medios de instalación de SAP, archivos de lista de materiales (BOM) y archivos de estado de Terraform. El archivo de configuración define la región y el nombre del entorno de la biblioteca SAP. Para obtener información de parámetros y ejemplos, vea [cómo configurar la biblioteca SAP para la automatización](automation-configure-control-plane.md).

## <a name="workload-zone-structure"></a>Estructura de la zona de carga de trabajo

La mayoría de las configuraciones de SAP tienen varias [zonas de carga de trabajo](automation-deployment-framework.md#deployment-components) para diferentes niveles de aplicación. Por ejemplo, puede tener diferentes zonas de carga de trabajo para desarrollo, control de calidad y producción.

Va a crear o conceder acceso a los siguientes servicios en cada zona de carga de trabajo:

* Instancias de Azure Virtual Network, para redes virtuales, subredes y grupos de seguridad de red.
* Azure Key Vault, para las credenciales del sistema y la entidad de servicio de implementación.
* Cuentas de Azure Storage, para diagnósticos de arranque y testigo en la nube.

Antes de realizar el diseño de la zona de carga de trabajo, tenga en cuenta las siguientes preguntas:

* ¿Cuántas zonas de carga de trabajo necesita el escenario?
* ¿En qué regiones necesita implementar cargas de trabajo?
* ¿Cuál es su [escenario de implementación](#supported-deployment-scenarios)?

Para más información, consulte [configuración de una implementación de zona de carga de trabajo para la automatización](automation-deploy-workload-zone.md).

## <a name="sap-system-setup"></a>Configuración del sistema SAP

El sistema SAP contiene todos los componentes de Azure necesarios para hospedar la aplicación SAP.

Antes de configurar el sistema SAP, tenga en cuenta las siguientes preguntas:

* ¿Qué back-end de base de datos desea usar?
* ¿Cuántos servidores de base de datos necesita?
* ¿Su escenario requiere alta disponibilidad?
* ¿Cuántos servidores de aplicaciones necesita?
* ¿Cuántos distribuidores web necesita, si los hay? 
* ¿Cuántas instancias de servicios centrales necesita?
* ¿Qué tamaño de máquina virtual (VM) necesita?
* ¿Qué imagen de máquina virtual desea usar? ¿La imagen está en Azure Marketplace o es personalizada?
* ¿Va a implementar en [un escenario de implementación nuevo o existente](#supported-deployment-scenarios)?
* ¿Cuál es su estrategia de asignación de IP? ¿Desea que Azure establezca direcciones IP o use una configuración personalizada?

Para más información, consulte [configuración del sistema SAP para la automatización](automation-configure-system.md).

## <a name="deployment-flow"></a>Flujo de implementación

Al planear una implementación, es importante tener en cuenta el flujo general. La implementación de SAP en Azure con el marco de automatización consta de tres pasos principales.

1. Preparar la región. En este paso se implementan componentes para admitir el marco de automatización de SAP en una región de Azure especificada. Algunas partes de este paso son:
    1. Creación del entorno de implementación
    1. Creación de almacenamiento compartido para archivos de estado de Terraform
    1. Creación de almacenamiento compartido para medios de instalación de SAP
1. Preparar la zona de cargas de trabajo. En este paso se implementan los [componentes de la zona de cargas de trabajo](#workload-zone-structure), como la red virtual y los almacenes de claves.
1. Implementar el sistema. Este paso incluye la [infraestructura del sistema SAP](#sap-system-setup).

## <a name="orchestration-environment"></a>Entorno de orquestación

Para el marco de automatización, debe ejecutar plantillas y scripts desde uno de los siguientes entornos admitidos:

* Azure Cloud Shell
* Una máquina virtual Linux hospedada en Azure
* PowerShell en el equipo Windows local

## <a name="naming-conventions"></a>Convenciones de nomenclatura

El marco de automatización usa una convención de nomenclatura predeterminada. Si desea usar una convención de nomenclatura personalizada, planee y defina los nombres personalizados antes de la implementación.

## <a name="disk-sizing"></a>Dimensionamiento del disco

Si desea configurar [tamaños de disco personalizados](automation-configure-extra-disks.md), asegúrese de planear la instalación personalizada antes de la implementación.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Acerca de las implementaciones manuales del marco de automatización](automation-manual-deployment.md)
