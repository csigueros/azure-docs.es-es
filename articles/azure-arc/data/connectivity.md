---
title: Modos de conectividad y requisitos
description: Descripción de las opciones de conectividad de los servicios de datos habilitados para Azure Arc desde su entorno a Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: conceptual
ms.openlocfilehash: 0cd1d38bf2a96e8530c9647a6b09ad55893e569a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748742"
---
# <a name="connectivity-modes-and-requirements"></a>Modos de conectividad y requisitos


## <a name="connectivity-modes"></a>Modos de conectividad

Hay varias opciones para el grado de conectividad desde el entorno de los servicios de datos habilitados para Azure Arc a Azure. A medida que los requisitos varían en función de la directiva empresarial, las normativas gubernamentales o la disponibilidad de la conectividad de red con Azure, puede elegir entre los siguientes modos de conectividad.

Los servicios de datos habilitados para Azure Arc le ofrecen la opción de conectarse a Azure con dos *modos de conectividad* diferentes: 

- Conexión directa 
- Conexión indirecta

El modo de conectividad le proporciona la flexibilidad de elegir la cantidad de datos que se envían a Azure y el modo en que los usuarios interactúan con el controlador de datos de Arc. En función del modo de conectividad elegido, es posible que alguna funcionalidad de los servicios de datos habilitados para Azure Arc no esté disponible.

Lo más importante es que si los servicios de datos habilitados para Azure Arc están conectados directamente a Azure, los usuarios pueden usar las [API de Azure Resource Manager](/rest/api/resources/), la CLI de Azure y Azure Portal para operar los servicios de datos de Azure Arc. La experiencia en el modo Conectado directamente es muy parecida a como se haría con cualquier otro servicio de Azure, con el aprovisionamiento y desaprovisionamiento, el escalado, la configuración, etc. mediante Azure Portal.  Si los servicios de datos habilitados para Azure Arc están conectados indirectamente a Azure, Azure Portal es una vista de solo lectura. Puede ver el inventario de las instancias de Azure SQL Managed Instance y las instancias de Hiperescala de PostgreSQL de Azure que ha implementado, así como los detalles sobre ellas, pero no puede realizar acciones en ellas en Azure Portal.  En el modo de conexión indirecta, todas las acciones se deben realizar de forma local mediante Azure Data Studio, la CLI apropiada o herramientas nativas de Kubernetes, como kubectl.

Además, Azure Active Directory y el control de acceso basado en rol de Azure solo se pueden usar en el modo de conexión directa, ya que hay una dependencia de una conexión continua y directa con Azure para proporcionar esta funcionalidad.

Algunos servicios conectados a Azure solo están disponibles cuando se puede llegar directamente a ellos, como los servicios de seguridad de Azure Defender, la información de los contenedores y el almacenamiento de blobs de Azure Backup.

||**Conexión indirecta**|**Conexión directa**|**Nunca conectado**|
|---|---|---|---|
|**Descripción**|El modo de conexión indirecta ofrece la mayoría de los servicios de administración de forma local en su entorno sin conexión directa con Azure.  Se debe enviar una cantidad mínima de datos a Azure _solo_ para fines de inventario y facturación. Se exporta a un archivo y se carga en Azure al menos una vez al mes.  No se requiere ninguna conexión directa ni continua con Azure.  Algunas características y servicios que requieren una conexión a Azure no estarán disponibles.|El modo de conexión directa ofrece todos los servicios disponibles cuando se puede establecer una conexión directa con Azure. Las conexiones siempre se inician _desde_ su entorno a Azure y usan puertos y protocolos estándar como HTTPS/443.|No se pueden enviar datos a Azure ni recibirlos de ningún modo.|
|**Disponibilidad actual**| Disponible |disponible en versión preliminar.|No se admite actualmente.|
|**Casos de uso típicos**|Centros de datos locales que no permiten la conectividad dentro o fuera de la región de datos del centro de datos debido a las directivas de cumplimiento normativo o de la empresa o a las preocupaciones con respecto a ataques externos o filtración de datos.  Ejemplos típicos: Instituciones financieras, servicios de salud, administración pública. <br/><br/>Ubicaciones de sitios perimetrales en las que el sitio perimetral normalmente no tiene conectividad a Internet.  Ejemplos típicos: aplicaciones para petróleo o gas o campos militares.  <br/><br/>Ubicaciones de sitios perimetrales que tienen conectividad intermitente con períodos largos de interrupciones.  Ejemplos típicos: estadios, barcos de crucero. | Organizaciones que usan nubes públicas.  Ejemplos típicos: Azure, AWS o Google Cloud.<br/><br/>Ubicaciones de sitios perimetrales donde hay normalmente conectividad a Internet y está permitida.  Ejemplos típicos: tiendas minoristas, fabricación.<br/><br/>Centros de datos corporativos con directivas más permisivas para la conectividad entre la región de datos del centro de datos e Internet.  Ejemplos típicos: Empresas no reguladas, pequeñas y medianas empresas|Entornos verdaderamente "aislados" en los que ningún dato puede entrar ni salir del entorno de datos bajo ninguna circunstancia. Ejemplos típicos: instalaciones confidenciales del gobierno.|
|**Cómo se envían los datos a Azure**|Hay tres opciones para la forma en la que se pueden enviar a Azure los datos de inventario y facturación:<br><br> 1) los datos se exportan fuera de la región de datos mediante un proceso automatizado que tiene conectividad con la región de datos segura y con Azure.<br><br>2) los datos se exportan fuera de la región de datos mediante un proceso automatizado dentro de la región de datos, se copian automáticamente en una región menos segura y un proceso automatizado en la región menos segura carga los datos en Azure.<br><br>3) un usuario exporta manualmente los datos dentro de la región segura, los saca manualmente de la región segura y los carga manualmente en Azure. <br><br>Las dos primeras opciones son un proceso continuo automatizado que se puede programar para ejecutarse con frecuencia, por lo que hay un retraso mínimo en la transferencia de datos a Azure, sujeto solo a la conectividad disponible con Azure.|Los datos se envían automáticamente y continuamente a Azure.|Los datos nunca se envían a Azure.|

## <a name="feature-availability-by-connectivity-mode"></a>Disponibilidad de características según el modo de conectividad

|**Característica**|**Conexión indirecta**|**Conexión directa**|
|---|---|---|
|**Alta disponibilidad automática**|Compatible|Compatible|
|**Aprovisionamiento de autoservicio**|Compatible<br/>La creación se puede realizar mediante Azure Data Studio, la CLI apropiada o las herramientas nativas de Kubernetes (Helm, kubectl, oc, etc.) o mediante el aprovisionamiento de GitOps de Kubernetes habilitado para Azure Arc.|Compatible<br/>Además de las opciones de creación del modo de conexión indirecta, también puede crear lo que necesite mediante Azure Portal, las API de Azure Resource Manager, la CLI de Azure o plantillas de ARM. **Pendiente de la disponibilidad del modo de conexión directa**
|**Escalabilidad elástica**|Compatible|Compatible<br/>**Pendiente de la disponibilidad del modo de conexión directa**|
|**Facturación**|Compatible<br/>Los datos de facturación se exportan periódicamente y se envían a Azure.|Compatible<br/>Los datos de facturación se envían automáticamente y continuamente a Azure y se reflejan casi en tiempo real. **Pendiente de la disponibilidad del modo de conexión directa**|
|**Administración de inventario**|Compatible<br/>Los datos de inventario se exportan periódicamente y se envían a Azure.<br/><br/>Use herramientas de cliente como Azure Data Studio, la CLI de datos de Azure o `kubectl` para ver y administrar el inventario de forma local.|Compatible<br/>Los datos de inventario se envían automáticamente y continuamente a Azure y se reflejan casi en tiempo real. Como tal, puede administrar el inventario directamente desde Azure Portal.|
|**Actualizaciones y revisiones automáticas**|Compatible<br/>El controlador de datos debe tener acceso directo a Microsoft Container Registry (MCR) o se deben extraer de MCR las imágenes de contenedor e insertarlas en un registro de contenedor privado local al que el controlador de datos tenga acceso.|Compatible<br/>**Pendiente de la disponibilidad del modo de conexión directa**|
|**Copia de seguridad y restauración automáticas**|Compatible<br/>Copias de seguridad y restauración locales automáticas.|Compatible<br/>Además de la copia de seguridad y restauración locales automatizadas, puede enviar _de forma opcional_ copias de seguridad a Azure Backup para la retención a largo plazo y fuera del sitio. **Pendiente de la disponibilidad en el modo de conexión directa**|
|**Supervisión**|Compatible<br/>Supervisión local con los paneles de Grafana y Kibana.|Compatible<br/>Además de los paneles de supervisión locales, puede _opcionalmente_ enviar datos de supervisión y registros a Azure Monitor para la supervisión a escala de varios sitios en un solo lugar. **Pendiente de la disponibilidad del modo de conexión directa**|
|**Autenticación**|Use el nombre de usuario y la contraseña locales para el controlador de datos y la autenticación del panel. Use inicios de sesión de SQL y Postgres o Active Directory (AD no se admite actualmente, estará en versión preliminar pronto) para la conectividad con instancias de base de datos.  Use los proveedores de autenticación de K8s para la autenticación en la API de Kubernetes.|Además o en lugar de los métodos de autenticación para el modo conexión indirecta, puede usar _opcionalmente_ Azure Active Directory. **Pendiente de la disponibilidad en el modo de conexión directa**|
|**Control de acceso basado en rol (RBAC)**|Use RBAC de Kubernetes en la API de Kubernetes. Use RBAC de SQL y Postgres para las instancias de base de datos.|Puede usar Azure Active Directory y Azure RBAC.|
|**Azure Defender**|No compatible|Planeada para el futuro.|

## <a name="connectivity-requirements"></a>Requisitos de conectividad

**Algunas funcionalidades requieren una conexión a Azure.**

**Toda la comunicación con Azure se inicia siempre desde su entorno.** Esto es así incluso para las operaciones que un usuario inicia en Azure Portal.  En ese caso, hay una tarea que se pone en cola en Azure.  Un agente de su entorno inicia la comunicación con Azure para ver qué tareas hay en la cola, ejecuta las tareas e informa del estado, la finalización o el error a Azure.

|**Tipo de datos**|**Dirección**|**Obligatorio/opcional**|**Costos adicionales**|**Modo requerido**|**Notas**|
|---|---|---|---|---|---|
|**Imágenes de contenedor**|Microsoft Container Registry -> Cliente|Obligatorio|No|Indirecto o directo|Las imágenes de contenedor son el método para distribuir el software.  En un entorno que se puede conectar a Microsoft Container Registry (MCR) a través de Internet, las imágenes de contenedor se pueden extraer directamente de MCR.  En caso de que el entorno de implementación no tenga conectividad directa, puede extraer las imágenes de MCR e insertarlas en un registro de contenedor privado en el entorno de implementación.  En el momento de la creación, puede configurar el proceso de creación para que se realice la extracción desde el registro de contenedor privado en lugar de MCR. Esto también se aplicará a las actualizaciones automatizadas.|
|**Inventario de recursos**|Entorno del cliente -> Azure|Obligatorio|No|Indirecto o directo|En Azure se mantiene un inventario de los controladores de datos y las instancias de base de datos (PostgreSQL y SQL) con fines de facturación y también con el fin de crear un inventario de todos los controladores de datos e instancias de base de datos en un único lugar, lo que es especialmente útil si tiene más de un entorno con servicios de datos de Azure Arc.  El inventario se actualiza en Azure a medida que las instancias se aprovisionan o se desaprovisionan y se escalan o se reducen horizontal y verticalmente.|
|**Datos de telemetría de facturación**|Entorno del cliente -> Azure|Obligatorio|No|Indirecto o directo|Se debe enviar a Azure la utilización de las instancias de base de datos con fines de facturación. |
|**Supervisión de datos y registros**|Entorno del cliente -> Azure|Opcionales|Es posible en función del volumen de datos (consulte [Precios de Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/)).|Indirecto o directo|Es posible que desee enviar los registros y datos de supervisión recopilados localmente a Azure Monitor para agregar los datos de varios entornos en un solo lugar y usar servicios de Azure Monitor como las alertas, utilizar los datos en Azure Machine Learning, etc.|
|**Control de acceso basado en rol de Azure (Azure RBAC)**|Entorno del cliente -> Azure -> Entorno del cliente|Opcional|No|Solo directo|Si desea usar Azure RBAC, la conectividad con Azure debe estar establecida en todo momento.  Si no desea usar Azure RBAC, puede usar RBAC de Kubernetes local.|
|**Azure Active Directory (AAD) (en un futuro)**|Entorno del cliente -> Azure -> Entorno del cliente|Opcionales|Es posible, pero puede que ya esté pagando por Azure AD.|Solo directo|Si desea usar Azure AD para la autenticación, la conectividad con Azure debe estar establecida en todo momento. Si no desea usar Azure AD para la autenticación, puede usar los Servicios de federación de Active Directory (ADFS) sobre Active Directory. **Pendiente de la disponibilidad en el modo de conexión directa**|
|**Copia de seguridad y restauración**|Entorno del cliente -> Entorno del cliente|Obligatorio|No|Directa o indirecta|El servicio de copia de seguridad y restauración se puede configurar para que apunte a clases de almacenamiento local. **Pendiente de la disponibilidad en el modo de conexión directa**|
|**Copia de seguridad de Azure - Retención a largo plazo (en un futuro)**| Entorno del cliente -> Azure | Opcionales| Sí, para Azure Storage | Solo directo |Es posible que desee enviar copias de seguridad que se realizan localmente a Azure Backup para la retención a largo plazo y fuera del sitio de las copias de seguridad y devolverlas al entorno local para su restauración. **Pendiente de la disponibilidad en el modo de conexión directa**|
|**Servicios de seguridad de Azure Defender (en un futuro)**|Entorno del cliente -> Azure -> Entorno del cliente|Opcionales|Sí|Solo directo|**Pendiente de la disponibilidad en el modo de conexión directa**|
|**Aprovisionamiento y cambios de configuración desde Azure Portal**|Entorno del cliente -> Azure -> Entorno del cliente|Opcional|No|Solo directo|El aprovisionamiento y los cambios de configuración se pueden realizar localmente mediante Azure Data Studio o la CLI apropiada.  En el modo de conexión directa, también podrá aprovisionar y realizar cambios de configuración desde Azure Portal.|


## <a name="details-on-internet-addresses-ports-encryption-and-proxy-server-support"></a>Detalles sobre las direcciones de Internet, los puertos, el cifrado y la compatibilidad con servidores proxy

Actualmente, solo el modo de conexión indirecta está disponible con carácter general. En este modo, solo hay tres conexiones necesarias para los servicios disponibles en Internet. Estas conexiones incluyen las siguientes:

- [Microsoft Container Registry (MCR)](#microsoft-container-registry-mcr)
- [API de Azure Resource Manager](#azure-resource-manager-apis):
- [API de Azure Monitor](#azure-monitor-apis)

Todas las conexiones HTTPS a Azure y a Microsoft Container Registry se cifran mediante SSL/TLS con certificados oficialmente firmados y comprobables.

En las secciones siguientes se proporcionan detalles para estas conexiones. 

### <a name="microsoft-container-registry-mcr"></a>Microsoft Container Registry (MCR)

Microsoft Container Registry hospeda las imágenes de contenedor de los servicios de datos habilitados para Azure Arc.  Puede extraer estas imágenes de MCR e insertarlas en un registro de contenedor privado y configurar el proceso de implementación del controlador de datos para extraer las imágenes de contenedor de ese registro de contenedor privado.

#### <a name="connection-source"></a>Origen de la conexión

El kubelet de Kubernetes de cada uno de los nodos de Kubernetes extrae las imágenes del contenedor.

#### <a name="connection-target"></a>Destino de la conexión

`mcr.microsoft.com`

#### <a name="protocol"></a>Protocolo

HTTPS

#### <a name="port"></a>Port

443

#### <a name="can-use-proxy"></a>Se puede usar servidor proxy

Sí

#### <a name="authentication"></a>Authentication

Ninguno

### <a name="azure-resource-manager-apis"></a>API de Azure Resource Manager:
Azure Data Studio y la CLI de Azure se conectan a las API de Azure Resource Manager para enviar y recuperar datos de Azure para algunas características.

#### <a name="connection-source"></a>Origen de la conexión

Un equipo que ejecuta Azure Data Studio o la CLI de Azure que se conecta a Azure.

#### <a name="connection-target"></a>Destino de la conexión

- `login.microsoftonline.com`
- `management.azure.com`
- `san-af-eastus-prod.azurewebsites.net`
- `san-af-eastus2-prod.azurewebsites.net`
- `san-af-australiaeast-prod.azurewebsites.net`
- `san-af-centralus-prod.azurewebsites.net`
- `san-af-westus2-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-southeastasia-prod.azurewebsites.net`
- `san-af-koreacentral-prod.azurewebsites.net`
- `san-af-northeurope-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-uksouth-prod.azurewebsites.net`
- `san-af-francecentral-prod.azurewebsites.net`

#### <a name="protocol"></a>Protocolo

HTTPS

#### <a name="port"></a>Port

443

#### <a name="can-use-proxy"></a>Se puede usar servidor proxy

Sí

#### <a name="authentication"></a>Authentication 

Azure Active Directory

### <a name="azure-monitor-apis"></a>API de Azure Monitor

Azure Data Studio y la CLI de Azure se conectan a las API de Azure Resource Manager para enviar y recuperar datos de Azure para algunas características.

#### <a name="connection-source"></a>Origen de la conexión

Un equipo que ejecuta la CLI de Azure que carga las métricas o los registros de supervisión en Azure Monitor.

#### <a name="connection-target"></a>Destino de la conexión

- `login.microsoftonline.com`
- `management.azure.com`
- `*.ods.opinsights.azure.com`
- `*.oms.opinsights.azure.com`
- `*.monitoring.azure.com`

#### <a name="protocol"></a>Protocolo

HTTPS

#### <a name="port"></a>Port

443

#### <a name="can-use-proxy"></a>Se puede usar servidor proxy

Sí

#### <a name="authentication"></a>Authentication 

Azure Active Directory

> [!NOTE]
> Por ahora, todas las conexiones HTTPS/443 del explorador al controlador de datos para ejecutar el comando `az arcdata dc export` y los paneles de Grafana y Kibana se cifran mediante SSL con certificados autofirmados.  En el futuro estará disponible una característica que le permitirá proporcionar sus propios certificados para el cifrado de estas conexiones SSL.

La conectividad desde Azure Data Studio al servidor de API de Kubernetes usa el cifrado y la autenticación de Kubernetes que haya establecido.  Cada usuario que usa Azure Data Studio o la CLI debe tener una conexión autenticada a la API de Kubernetes para realizar muchas de las acciones relacionadas con los servicios de datos habilitados para Azure Arc.

