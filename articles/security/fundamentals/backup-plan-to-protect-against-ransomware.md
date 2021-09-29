---
title: Plan de copia de seguridad y restauración de Azure para protegerse contra el ransomware | Microsoft Docs
description: Aprenda sobre qué hacer antes de un ataque de ransomware —y durante— para proteger los sistemas empresariales críticos y garantizar una rápida recuperación de las operaciones empresariales.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 08/27/2021
ms.openlocfilehash: 83caa5e93a8a9fd9df13646f867b176208655450
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124763355"
---
# <a name="backup-and-restore-plan-to-protect-against-ransomware"></a>Plan de copia de seguridad y restauración para protegerse contra el ransomware

Los ataques de ransomware cifran o borran deliberadamente los datos y los sistemas para obligar a una organización a pagar dinero a los atacantes. Estos ataques tienen como objetivo los datos, las copias de seguridad y también la documentación clave necesaria para que pueda recuperarse sin pagar a los atacantes (como medio para aumentar las posibilidades de que la organización pague).

En este artículo se aborda lo que debe hacer antes de un ataque de ransomware para proteger los sistemas empresariales críticos, y durante un ataque para garantizar una rápida recuperación de las operaciones empresariales.

> [!NOTE]
> La preparación contra el ransomware también mejora la resiliencia ante desastres naturales y ataques rápidos, como [WannaCry](https://en.wikipedia.org/wiki/WannaCry_ransomware_attack) y [(Not)Petya](https://attack.mitre.org/software/S0368/).

## <a name="what-is-ransomware"></a>¿Qué es el ransomware?

El ransomware es un tipo de malware que cifra archivos y carpetas, lo que impide el acceso a archivos y sistemas importantes. Los atacantes usan el ransomware para extorsionar a las victimas al exigirles dinero, normalmente en forma de criptomonedas, a cambio de una clave de descifrado o a cambio de no publicar datos confidenciales en la web oscura o en la red pública de Internet.

Aunque en un principio el ransomware usaba principalmente malware que se propagaba mediante suplantación de identidad (phishing) o entre dispositivos, ha surgido un [ransomware controlado por personas](/security/compass/human-operated-ransomware), donde un grupo de atacantes activos, controlados por operadores de ataque humanos, tienen como objetivo todos los sistemas de una organización (en lugar de un único dispositivo o conjunto de dispositivos). Un ataque puede incluir:

- Cifrado de los datos
- Filtración de los datos
- Daño de las copias de seguridad

El ransomware aprovecha el conocimiento que los atacantes humanos tienen sobre los errores de configuración y vulnerabilidades comunes del sistema y de seguridad, para infiltrarse en la organización, navegar por la red empresarial y adaptarse al entorno y sus puntos débiles a medida que se desplazan.

El ransomware puede actuar por fases para primero filtrar los datos, durante varias semanas o meses, antes de que el ransomware se ejecute realmente en una fecha específica.

El ransomware también puede cifrar lentamente los datos mientras mantiene la clave en el sistema. Dado que la clave está disponible, los datos se pueden usar, y el ransomware pasa desapercibido. Sin embargo, las copias de seguridad corresponden a los datos cifrados. Una vez que todos los datos están cifrados y las copias de seguridad recientes también incluyen los datos cifrados, la clave se quita para que ya no pueda leer los datos.

El daño real se suele materializar cuando el ataque filtra archivos, a la vez que se dejan puertas traseras en la red para futuras actividades malintencionadas, y estos riesgos persisten independientemente de si se paga o no el rescate. Estos ataques pueden ser catastróficos para las operaciones empresariales y son difíciles de limpiar, porque requieren una expulsión completa del adversario para protegerse frente a ataques futuros. A diferencia de las primeras formas de ransomware, que solo requerían corregir el malware, el ransomware controlado por personas seguirá amenazando sus operaciones empresariales después del ataque inicial.

### <a name="impact-of-an-attack"></a>El impacto de un ataque

El impacto de un ataque de ransomware en cualquier organización es difícil de cuantificar con exactitud. En función del ámbito del ataque, el impacto podría incluir:

- Pérdida de acceso a los datos
- Interrupción de la operaciones empresariales
- Pérdidas financieras
- Robo de la propiedad intelectual
- Daños a la confianza de los clientes o la reputación
- Gastos jurídicos

## <a name="how-can-you-protect-yourself"></a>¿Cómo puede protegerse?

La mejor manera de evitar ser víctima del ransomware es implementar medidas preventivas y contar con herramientas que protejan a la organización de cada paso que los atacantes dar para infiltrarse en los sistemas.

Para reducir la exposición en el entorno local, puede mover la organización a un servicio en la nube. Microsoft ha invertido en funcionalidades de seguridad nativas que hacen que Microsoft Azure sea resistente frente a ataques de ransomware, y ayude a las organizaciones a anular las técnicas de ataque de ransomware. Para obtener una vista completa del ransomware y la extorsión y cómo proteger su organización, use la información de la presentación en PowerPoint [Plan del proyecto de mitigación de ransomware controlado por personas](https://download.microsoft.com/download/7/5/1/751682ca-5aae-405b-afa0-e4832138e436/RansomwareRecommendations.pptx) .

Debe suponer que, en algún momento, será víctima de un ataque de ransomware. Uno de los pasos más importantes que puede dar para proteger los datos y evitar pagar un rescate es tener un plan confiable de copia de seguridad y restauración para la información crítica para la empresa. Dado que los atacantes de ransomware han invertido mucho en la neutralización de las aplicaciones de copia de seguridad y las características de los sistemas operativos, como la instantánea de volúmenes, es fundamental tener copias de seguridad a las que un atacante malintencionado no pueda acceder.

### <a name="azure-backup"></a>Azure Backup

[Azure Backup](../../backup/backup-overview.md) ofrece seguridad al entorno de copia de seguridad, tanto si los datos están en tránsito como en reposo. Con Azure Backup, [puede hacer una copia de seguridad](../../backup/backup-overview.md#what-can-i-back-up) de lo siguiente:

- Archivos, carpetas y estado del sistema locales
- VM Windows o Linux enteras
- Azure Managed Disks
- Recursos compartidos de archivos de Azure en una cuenta de almacenamiento
- Bases de datos de SQL Server que se ejecutan en VM de Azure

Los datos de copia de seguridad se almacenan en Azure Storage, y el invitado o atacante no tiene acceso directo al almacenamiento de copia de seguridad ni a su contenido. En las copias de seguridad de máquina virtual, la creación y el almacenamiento de la instantánea de copia de seguridad se realizan mediante el tejido de Azure, donde el invitado o atacante no tiene más implicación que poner en modo inactivo la carga de trabajo para conseguir copias de seguridad coherentes. En el caso de SQL y SAP HANA, la extensión de copia de seguridad obtiene acceso temporal para escribir en blobs específicos. De esta manera, incluso en un entorno en peligro, el atacante no podrá alterar ni eliminar las copias de seguridad existentes.

Azure Backup proporciona funcionalidades integradas de supervisión y creación de alertas para ver y configurar acciones en eventos relacionados con Azure Backup. La solución Informes de Backup sirve como destino único para realizar el seguimiento del uso, para llevar a cabo la auditoría de copias de seguridad y restauraciones, y para identificar las tendencias clave en diferentes niveles de granularidad. El uso de las herramientas de supervisión e informes de Azure Backup puede avisarle de cualquier actividad no autorizada, sospechosa o malintencionada en cuanto se produzca.

Se han agregado comprobaciones para asegurarse de que los usuarios válidos son los únicos que pueden realizar varias operaciones. Entre estas se incluyen la adición de una capa de autenticación adicional. Como parte de la adición de una capa extra de autenticación para las operaciones críticas, se le pedirá que escriba un PIN de seguridad antes de [modificar las copias de seguridad en línea](../../backup/backup-azure-security-feature.md#prevent-attacks).

Averigüe más sobre las [características de seguridad](../../backup/security-overview.md) integradas en Azure Backup.

### <a name="validate-backups"></a>Validación de copias de seguridad

Compruebe que la copia de seguridad esté correcta en el momento de su creación y antes de la restauración. Se recomienda usar un [almacén de Recovery Services](../../backup/backup-azure-recovery-services-vault-overview.md), que es una entidad de almacenamiento de Azure que aloja datos. Normalmente, los datos son copias de datos o información de configuración de máquinas virtuales (VM), cargas de trabajo, servidores o estaciones de trabajo. Puede usar almacenes de Recovery Services para almacenar datos de copia de seguridad de varios servicios de Azure, como VM de IaaS (Linux o Windows) y bases de datos de Azure SQL, así como recursos locales. Los almacenes de Recovery Services permiten organizar fácilmente los datos de copia de seguridad y proporcionan características como:

- Mejores funcionalidades para garantizar que pueda proteger las copias de seguridad y recuperar datos de manera segura, incluso si los servidores de producción y copia de seguridad están en peligro. [Más información](../../backup/backup-azure-security-feature.md).
- Supervisión del entorno de TI híbrido (VM de IaaS de Azure y recursos locales) desde un portal central. [Más información](../../backup/backup-azure-monitoring-built-in-monitor.md).
- Compatibilidad con el control de acceso basado en roles de Azure (Azure RBAC), que restringe el acceso de copia de seguridad y restauración al conjunto definido de roles de usuario. Azure RBAC ofrece diversos roles integrados, y Azure Backup tiene tres roles integrados para administrar los puntos de recuperación. [Más información](../../backup/backup-rbac-rs-vault.md).
- Protección contra eliminación temporal, incluso si un actor malintencionado elimina una copia de seguridad (o si los datos de copia de seguridad se eliminan por accidente). Los datos de la copia de seguridad se conservan durante 14 días más, lo que permite la recuperación de un elemento de copia de seguridad sin pérdida de datos. [Más información](../../backup/backup-azure-security-feature-cloud.md).
- Restauración entre regiones, que permite restaurar VM de Azure en una región secundaria, que es una región emparejada de Azure. Puede restaurar los datos replicados en la región secundaria en cualquier momento. Esto le permite restaurar los datos de la región secundaria para la compatibilidad con auditorías y, durante los escenarios de interrupción, sin esperar a que Azure declare un desastre (a diferencia de la configuración de GRS del almacén). [Más información](../../backup/backup-azure-arm-restore-vms.md#cross-region-restore).

> [!NOTE]
> Hay dos tipos de almacenes en Azure Backup. Además de almacenes de Recovery Services, también hay [almacenes de Backup](../../backup/backup-vault-overview.md) que hospedan datos para cargas de trabajo más recientes compatibles con Azure Backup.

## <a name="what-to-do-before-an-attack"></a>Qué hacer antes de un ataque

Como ya se dijo, tiene que suponer que, en algún momento, será víctima de un ataque de ransomware. La identificación de los sistemas críticos para la empresa y la aplicación de procedimientos recomendados antes de un ataque le permitirán volver a estar en funcionamiento lo antes posible. 

### <a name="determine-what-is-most-important-to-you"></a>Qué es lo que más le importa

Puede recibir un ataque de ransomware mientras planea cómo defenderse frente a un ataque, por lo que la prioridad debe ser identificar los sistemas críticos para la empresa que son más importantes para usted y empezar a hacer copias de seguridad periódicas de esos sistemas.

Según nuestra experiencia, las cinco aplicaciones más importantes para los clientes se divide en las siguientes categorías, en este orden de prioridad:

- Sistemas de identidad, necesarios para que los usuarios accedan a los sistemas (incluidos todos los demás que se describen a continuación), como Active Directory, [Azure AD Connect](../../active-directory/hybrid/whatis-azure-ad-connect.md), controladores de dominio de AD.
- Vida humana, cualquier sistema que sustente la vida humana o pueda ponerla en riesgo, como sistemas médicos o de soporte vital, sistemas de seguridad (ambulancias, sistemas de envío, control de semáforos), maquinaria de gran tamaño, sistemas químicos o biológicos, producción de alimentos o productos personales, etc.
- Sistemas financieros, sistemas que procesan transacciones monetarios y mantienen la empresa en funcionamiento, como sistemas de pago y bases de datos relacionadas, sistema financiero para informes trimestrales.
- Habilitación de productos o servicios, los sistemas necesarios para brindar los servicios empresariales, o producir o entregar productos físicos por los que los clientes le pagan, sistemas de control de fábricas, sistemas de entrega o distribución de productos, y similares.
- Seguridad (mínimo), también debe priorizar los sistemas de seguridad necesarios para supervisar los ataques y proporcionar servicios de seguridad mínimos. Debe centrarse en garantizar que los ataques actuales (o los oportunistas) no puedan obtener (o recuperar) acceso inmediato a los sistemas restaurados.

La lista de copia de seguridad prioritaria también se convierte en la lista de restauración prioritaria. Una vez que haya señalado los sistemas críticos y haga copias de seguridad periódicas, siga los pasos para reducir el nivel de exposición.

### <a name="steps-to-take-before-an-attack"></a>Pasos que debe seguir antes de un ataque

Aplique estos procedimientos recomendados antes de un ataque.

| Tarea | Detail |
| --- | --- |
| Identifique los sistemas importantes que tiene que volver a poner en línea primero (siguiendo las cinco categorías principales anteriores), y comenzar de inmediato a hacer copias de seguridad periódicas de esos sistemas. | Para volver a ponerse en funcionamiento lo antes posible después de un ataque, decida hoy qué es lo que más le importa. |
| Migre la organización a la nube. <br><br>Considere la posibilidad de comprar un plan de Soporte técnico unificado de Microsoft o consulte a un asociado de Microsoft para que le ayude en el pasaje a la nube. | Reduzca la exposición local mediante; para ello, mueva los datos a servicios en la nube con copias de seguridad automáticas y reversión de autoservicio. Microsoft Azure dispone de un sólido conjunto de herramientas que le ayudarán a hacer copias de seguridad de los sistemas críticos para la empresa y a restaurar las copias de seguridad con mayor rapidez. <br><br>El [Soporte técnico unificado de Microsoft](https://www.microsoft.com/en-us/msservices/unified-support-solutions) es un modelo de soporte técnico de servicios en la nube que le ayudará siempre que lo necesite. Soporte técnico unificado: <br><br>Ofrece un equipo designado que está disponible las 24 horas del día, los 7 días de la semana, y brinda resolución de problemas y escalación de incidentes críticos según sea necesario. <br><br>Le ayuda a supervisar el estado del entorno de TI y funciona de forma proactiva para asegurarse de evitar los problemas antes de que tengan lugar. |
| Mueva los datos de los usuarios a soluciones en la nube, como OneDrive y SharePoint, para sacar partido de las [funcionalidades de papelera de reciclaje y control de versiones](/compliance/assurance/assurance-malware-and-ransomware-protection#sharepoint-online-and-onedrive-for-business-protection-against-ransomware). <br><br>Instruya a los usuarios sobre cómo recuperar los archivos por sí mismos para reducir los retrasos y los costos de recuperación.   Por ejemplo, si los archivos de OneDrive de un usuario se han infectado por malware, puede [restaurar](https://support.microsoft.com/office/restore-your-onedrive-fa231298-759d-41cf-bcd0-25ac53eb8a15?ui=en-US&rs=en-US&ad=US) toda la instancia de OneDrive a un momento anterior. <br><br>Analice una estrategia de defensa, como [Microsoft 365 Defender](/microsoft-365/security/defender/microsoft-365-defender), antes de permitir a los usuarios restaurar sus propios archivos. | Los datos de usuario en la nube de Microsoft se pueden proteger mediante características integradas de administración de datos y seguridad. <br><br>Está bien enseñar a los usuarios a restaurar sus propios archivos, pero debe tener cuidado de que los usuarios no restauren el malware usado para llevar a cabo el ataque. Necesita: <br><br>Asegurarse de que los usuarios no restauren sus archivos hasta que esté seguro de que el atacante ha sido expulsado. <br><br>Poner en práctica un mecanismo de mitigación en caso de que un usuario restaure parte del malware. <br><br>Microsoft 365 Defender acciones automáticas con tecnología de inteligencia artificial y cuadernos de estrategia para corregir los recursos afectados de nuevo a un estado seguro. Microsoft 365 Defender aprovecha las funcionalidades de corrección automática de los productos del conjunto para asegurarse de que todos los recursos afectados relacionados con un incidente se corrijan automáticamente siempre que sea posible. |
| Implemente [Azure Security Benchmark](/security/benchmark/azure/introduction). | Azure Security Benchmark es el marco de control de seguridad propio de Azure, basado en marcos de control de seguridad basados en el sector, como SP800-53 de NIST o CIS Controls v7.1. Da instrucciones a las organizaciones sobre cómo configurar Azure y los servicios de Azure, e implementar los controles de seguridad. Consulte [Copia de seguridad y recuperación](/security/benchmark/azure/security-controls-v2-backup-recovery). |
| Practique con regularidad el plan de continuidad empresarial y recuperación ante desastres (BC/DR). <br><br>Simulación de escenarios de respuesta a incidentes. Los ejercicios que realice al prepararse para un ataque deben planearse y realizarse en torno a las listas prioritarias de copia de seguridad y restauración. <br><br>Pruebe periódicamente el escenario de "recuperación desde cero" para asegurarse de que su plan de continuidad del negocio (BC) y recuperación de desastres (DR) puede poner en línea rápidamente las operaciones empresariales críticas desde la funcionalidad nula (todos los sistemas están fuera de servicio). | Garantiza la rápida recuperación de las operaciones empresariales al tratar un ataque de ransomware o extorsión con la misma importancia que un desastre natural. <br><br>Realice ejercicios prácticos para validar los procesos y procedimientos técnicos entre equipos, incluidas las comunicaciones fuera de banda de empleados y clientes (suponga que todo correo electrónico y chat están fuera de servicio). |
| Considere la posibilidad de crear un registro de riesgos para detectar posibles riesgos y abordar cómo mediará a través de controles y acciones preventivos. Agregue ransomware al registro de riesgos como escenario de alta probabilidad y de alto impacto. | Un registro de riesgos puede ayudarle a priorizar los riesgos en función de la probabilidad de que se produzca ese riesgo, y de la gravedad para su empresa en caso de que se produzca ese riesgo. <br><br>Haga un seguimiento del estado de mitigación a través del ciclo de evaluación de [administración de riesgos empresariales (ERM)](/compliance/assurance/assurance-risk-management). |
| Haga una copia de seguridad automática de todos los sistemas empresariales críticos según una programación periódica (incluida la copia de seguridad de dependencias críticas, como Active Directory). <br><br>Compruebe que la copia de seguridad esté bien a medida que se cree. | Permite recuperar los datos hasta la última copia de seguridad. |
| Proteja (o imprima) los documentos y sistemas auxiliares necesarios para la recuperación, como documentos de procedimientos de restauración, CMDB y diagramas de red e instancias de SolarWinds. | Los atacantes tendrán deliberadamente como objetivo estos recursos porque afectan a su capacidad de recuperación. |
| Asegúrese de tener procedimientos bien documentados para interactuar con el soporte técnico de cualquier tercero, especialmente el soporte técnico de proveedores de inteligencia sobre amenazas, proveedores de soluciones antimalware y del proveedor de análisis de malware. Proteja (o imprima) estos procedimientos. | Los contactos de terceros pueden ser útiles si la variante de ransomware dada tiene puntos débiles conocidos o hay herramientas de descifrado disponibles. |
| Asegúrese de que la estrategia de copia de seguridad y recuperación incluya: <br><br>La capacidad de hacer una copia de seguridad de los datos en un momento dado específico. <br><br>Varias copias de seguridad almacenadas en ubicaciones aisladas y sin conexión. <br><br>Los objetivos de tiempo de recuperación que establezcan la rapidez con la que se puede recuperar y colocar la información de copia de seguridad en el entorno de producción. <br><br>La restauración rápida de las copias de seguridad en un espacio aislado o entorno de producción. | Las copias de seguridad son fundamentales para lograr la resistencia después de que una organización haya sido vulnerada. Aplique la regla 3-2-1 para obtener la máxima protección y disponibilidad: 3 copias (original + 2 copias de seguridad), 2 tipos de almacenamiento, y 1 copia fuera del sitio o inactiva. |
| Proteger las copias de seguridad contra el borrado y el cifrado deliberados: <br><br>Almacene las copias de seguridad en un almacenamiento sin conexión o fuera del sitio o en un almacenamiento inmutable. <br><br>Exija pasos fuera de banda (como [MFA](../../active-directory/authentication/concept-mfa-howitworks.md) o un PIN de seguridad) antes de permitir que se modifique o borre una copia de seguridad en línea. <br><br>Cree puntos de conexión privados en Azure Virtual Network para hacer una copia de seguridad de los datos y restaurarlos a partir del almacén de Recovery Services. | Las copias de seguridad a las que pueden acceder los atacantes pueden volverse inutilizables para la recuperación empresarial. <br><br>El almacenamiento sin conexión garantiza una transferencia sólida de datos de copia de seguridad sin tener que usar ningún ancho de banda de red. Azure Backup admite la [copia de seguridad sin conexión](../../backup/offline-backup-overview.md), que permite transferir los datos de copia de seguridad inicial sin conexión, sin el uso de ancho de banda de red. Proporciona un mecanismo para copiar los datos de copia de seguridad en dispositivos de almacenamiento físico. Los dispositivos se envían después a un centro de recursos de Azure cercano y se cargan en un [almacén de Recovery Services](../../backup/backup-azure-recovery-services-vault-overview.md). <br><br>El almacenamiento inmutable en línea (como [Azure Blob](../../storage/blobs/immutable-storage-overview.md)) le permite almacenar objetos de datos críticos para la empresa en un estado WORM (escribir una vez, leer muchas). En este estado, los usuarios no pueden borrar ni modificar los datos durante el intervalo de tiempo especificado por el usuario. <br><br>La [autenticación multifactor (MFA)](../../active-directory/authentication/concept-mfa-howitworks.md) debe ser obligatoria para todas las cuentas de administrador, y se recomienda encarecidamente para todos los usuarios. El método preferido es usar una aplicación de autenticador en lugar de SMS o voz, siempre que sea posible. Al configurar Azure Backup, puede configurar los servicios de recuperación para habilitar MFA mediante un PIN de seguridad generado en Azure Portal. Esto garantiza que se genere un PIN de seguridad para las operaciones críticas, como actualizar o quitar un punto de recuperación. |
| Designe [carpetas protegidas](/windows/security/threat-protection/microsoft-defender-atp/controlled-folders). | Dificulta que las aplicaciones no autorizadas modifiquen los datos de estas carpetas. |
| Revisar los permisos: <br><br>Detecte permisos amplios de escritura y eliminación en recursos compartidos de archivos, SharePoint y otras soluciones. Por amplio se entiende numerosos usuarios con permisos de escritura o de eliminación para los datos críticos de la empresa. <br><br>Reduzca permisos amplios a la vez que se cumplen los requisitos de colaboración empresarial. <br><br>Audite y supervise para asegurarse de que no vuelvan a aparecer permisos amplios. | Reduce el riesgo de actividades de ransomware que permiten un acceso amplio. |
| Protección contra un intento de suplantación de identidad (phishing): <br><br>Realice periódicamente cursos de reconocimiento de la seguridad para ayudar a los usuarios a identificar un intento de suplantación de identidad y evitar hacer clic en algo que pueda crear un punto de entrada inicial para un riesgo. <br><br>Aplique controles de filtrado de seguridad al correo electrónico para detectar y minimizar la probabilidad de un intento de suplantación de identidad correcto. | El método más común que usan los atacantes para infiltrarse en una organización son los intentos de suplantación de identidad por correo electrónico. [Exchange Online Protection (EOP)](/microsoft-365/security/office-365-security/exchange-online-protection-overview) es el servicio de filtrado basado en la nube, que protege su organización frente a correo no deseado, malware y otras amenazas de correo electrónico. EOP se incluye en todas las organizaciones de Microsoft 365 con buzones de Exchange Online. <br><br>Un ejemplo de un control de filtrado de seguridad para el correo electrónico es [Vínculos seguros](/microsoft-365/security/office-365-security/safe-links). Vínculos seguros es una característica de Defender para Office 365, que proporciona análisis de direcciones URL y reescritura de mensajes de correo electrónico entrantes en el flujo de correo, así como comprobación de la hora de clic de las direcciones URL y los vínculos en mensajes de correo electrónico y otras ubicaciones. El análisis de Vínculos seguros se produce además de la protección contra correo no deseado y antimalware normal en los mensajes de correo electrónico entrantes en EOP. El análisis de Vínculos seguros puede ayudar a proteger su organización frente a vínculos malintencionados que se usan en la suplantación de identidad y otros ataques. <br><br>Obtenga más información sobre la [protección contra la suplantación de identidad (phishing)](/microsoft-365/security/office-365-security/tuning-anti-phishing). |

## <a name="what-to-do-during-an-attack"></a>Qué hacer durante un ataque

Si está bajo ataque, la lista de copia de seguridad prioritaria también se convierte en la lista de restauración prioritaria. Antes de restaurar, valide de nuevo que la copia de seguridad esté bien. Es posible que pueda buscar malware dentro de la copia de seguridad.

### <a name="steps-to-take-during-an-attack"></a>Pasos que debe seguir durante un ataque

Aplique estos procedimientos recomendados durante un ataque.

| Tarea | Detail |
| --- | --- |
| Al principio del ataque, comuníquese con soporte técnico de terceros, especialmente soporte técnico de proveedores de inteligencia sobre amenazas, proveedores de soluciones antimalware y el proveedor de análisis de malware. | Estos contactos pueden ser útiles si la variante de ransomware dada tiene un punto débil conocido o hay herramientas de descifrado disponibles. <br><br>El [Equipo de detección y respuesta de Microsoft (DART)](https://www.microsoft.com/security/blog/2019/03/25/dart-the-microsoft-cybersecurity-team-we-hope-you-never-meet/) puede ayudarle a protegerse frente a ataques. DART se relaciona con clientes de todo el mundo para ayudarles a protegerse y afirmarse contra los ataques antes de que se produzcan, así como a investigar y corregir cuando se un ataque ha tenido lugar. <br><br>Microsoft también brinda servicios de recuperación rápida de ransomware. Los servicios se prestan exclusivamente mediante la [Práctica de seguridad de recuperación de riesgos (CRSP)](https://www.microsoft.com/security/blog/2021/06/09/crsp-the-emergency-team-fighting-cyber-attacks-beside-customers/) global de Microsoft. El objetivo de este equipo durante un ataque de ransomware es restaurar el servicio de autenticación y limitar el impacto del ransomware. <br><br>DART y CRSP forman parte de la línea del servicio de seguridad de [Industry Solutions Delivery](https://www.microsoft.com/en-us/msservices/security) de Microsoft. |
| Póngase en contacto con las autoridades judiciales locales o nacionales. | Si se encuentra en el Estados Unidos, póngase en contacto con el FBI para denunciar una vulneración por ransomware mediante el [formulario de referencia de reclamación IC3](https://ransomware.ic3.gov/default.aspx). |
| Tome medidas para quitar la carga de malware o ransomware del entorno y detener la propagación. <br><br>Ejecute un examen del software antivirus completo y actual en todos los equipos y dispositivos sospechosos para detectar y quitar la carga que está asociada al ransomware. <br><br>Examine los dispositivos que sincronizan datos, o los destinos de las unidades de red asignadas. | Puede usar [Windows Defender](https://www.microsoft.com/windows/comprehensive-security) o (para clientes anteriores) [Microsoft Security Essentials](https://www.microsoft.com/download/details.aspx?id=5201). <br><br>Una alternativa que también le ayudará a quitar ransomware o malware es la [Herramienta de eliminación de software malintencionado (MSRT)](https://www.microsoft.com/download/details.aspx?id=9905). |
| Restaure primero los sistemas críticos para la empresa. Recuerde validar de nuevo que la copia de seguridad esté bien antes de restaurar.| En este momento, no es necesario restaurar todo. Céntrate en los principales cinco sistemas críticos para la empresa de la lista de restauración. |
| Si tiene copias de seguridad sin conexión, probablemente pueda restaurar los datos cifrados **después** de quitar la carga de ransomware (malware) del entorno. | Para evitar ataques futuros, asegúrese de que no haya ransomware ni malware en la copia de seguridad sin conexión antes de restaurarla. |
| Busque una imagen de copia de seguridad segura que se sepa que no esté infectada. <br><br>Si usa el almacén de Recovery Services, revise con cuidado la escala de tiempo de incidentes para comprender el momento dado adecuado para restaurar una copia de seguridad. | Para evitar ataques futuros, busque ransomware o malware en la copia de seguridad antes de restaurarla. |
| Use una herramienta de análisis de seguridad y otras herramientas para la restauración completa del sistema operativo, así como para los escenarios de restauración de datos. | [Microsoft Safety Scanner](/windows/security/threat-protection/intelligence/safety-scanner-download) es una herramienta de examen diseñada para buscar y quitar malware de equipos Windows. Basta con descargarlo y ejecutar un examen para buscar malware e intentar revertir los cambios realizados por las amenazas identificadas. |
| Asegúrese de que el antivirus o la solución de detección y respuesta de puntos de conexión (EDR) estén actualizados. También debe contar con revisiones actualizadas. | Se prefiere una solución EDR, como [Microsoft Defender para punto de conexión](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint). |
| Una vez que los sistemas críticos para la empresa estén en funcionamiento, restaure otros sistemas. <br><br>A medida que se restauran los sistemas, empiece a recopilar datos de telemetría para poder tomar decisiones formativas sobre lo que va a restaurar. | Los datos de telemetría deberían ayudarle a identificar si todavía hay malware en los sistemas. |

## <a name="post-attack-or-simulation"></a>Simulación o luego de un ataque

Después de un ataque de ransomware o una simulación de respuesta a incidentes, siga estos pasos para mejorar los planes de copia de seguridad y restauración, así como su posición de seguridad:

1. Identifique las conclusiones que puede sacar de lo que no funcionó del proceso (y de las oportunidades para simplificar, acelerar o mejorar el proceso).
2. Haga análisis de la causa principal sobre los mayores desafíos (con suficiente detalle para garantizar que las soluciones aborden el problema correcto, teniendo en cuenta las personas, el proceso y la tecnología).
3. Investigue y corrija la vulneración original (acuda al [Equipo de detección y respuesta de Microsoft (DART)](https://www.microsoft.com/security/blog/2019/03/25/dart-the-microsoft-cybersecurity-team-we-hope-you-never-meet/) para recibir ayudar).
4. Actualice la estrategia de copia de seguridad y restauración en función de las conclusiones que sacó y de las oportunidades, y priorice los pasos de mayor impacto y más rápida implementación.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a mejorar el plan de copia de seguridad y restauración para protegerse contra el ransomware. Para conocer los procedimientos recomendados sobre la implementación de la protección contra el ransomware, consulte [Protección rápida frente al ransomware y la extorsión](/security/compass/protect-against-ransomware).

Información clave del sector:

- [Informe de defensa digital de Microsoft](https://www.microsoft.com/security/business/security-intelligence-report?rtc=1) (consulte las páginas 22 a 24)

Microsoft Azure:

- [Ayuda para protegerse contra ransomware con Microsoft Azure Backup](https://www.youtube.com/watch?v=VhLOr2_1MCg) (vídeo de 26 minutos)
- [Recuperación de un riesgo de identidad sistemático](./recover-from-identity-compromise.md)
- [Detección avanzada de ataques de varias fases en Azure Sentinel](../../sentinel/fusion.md#ransomware)

Microsoft 365:

- [Recuperación en caso de un ataque de ransomware](/microsoft-365/security/office-365-security/recover-from-ransomware)
- [Protección contra malware y ransomware](/compliance/assurance/assurance-malware-and-ransomware-protection)
- [Proteger el PC contra el ransomware](https://support.microsoft.com/windows/protect-your-pc-from-ransomware-08ed68a7-939f-726c-7e84-a72ba92c01c3)
- [Control del ransomware en SharePoint Online](/sharepoint/troubleshoot/security/handling-ransomware-in-sharepoint-online)

Microsoft 365 Defender:

- [Búsqueda de ransomware con búsqueda avanzada](/microsoft-365/security/defender/advanced-hunting-find-ransomware)

Entradas de blog del equipo de seguridad de Microsoft:

- [Mayor resistencia mediante la comprensión de los riesgos de ciberseguridad: Parte 4: Análisis de las amenazas actuales (mayo de 2021)](https://www.microsoft.com/security/blog/2021/05/26/becoming-resilient-by-understanding-cybersecurity-risks-part-4-navigating-current-threats/). Consulte la sección Ransomware.
- [Ataques de ransomware controlados por personas: Un desastre evitable (marzo de 2020)](https://www.microsoft.com/security/blog/2020/03/05/human-operated-ransomware-attacks-a-preventable-disaster/). Incluye el análisis de cadenas de ataques reales de ransomware controlados por personas.
- [Respuesta al ransomware: ¿pagar o no pagar? (diciembre de 2019)](https://www.microsoft.com/security/blog/2019/12/16/ransomware-response-to-pay-or-not-to-pay/).
- [Norsk Hydro responde a un ataque de ransomware con transparencia (diciembre de 2019)](https://www.microsoft.com/security/blog/2019/12/17/norsk-hydro-ransomware-attack-transparency/)