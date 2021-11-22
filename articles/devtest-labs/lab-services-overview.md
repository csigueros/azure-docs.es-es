---
title: Azure Lab Services y Azure DevTest Labs
description: Compare características, escenarios y casos de uso entre Azure DevTest Labs y Azure Lab Services.
ms.topic: overview
ms.date: 11/15/2021
ms.openlocfilehash: 9ae4061713cbb9dd2863ee4f7c236c1474aae4ee
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132722247"
---
# <a name="compare-azure-devtest-labs-and-azure-lab-services"></a>Comparación entre Azure DevTest Labs y Azure Lab Services

Puede usar dos servicios de Azure diferentes para configurar entornos de laboratorio en la nube:

- [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab) ofrece entornos de desarrollo o prueba en la nube para su equipo.

  En DevTest Labs, un propietario de laboratorio [crea un laboratorio](devtest-lab-create-lab.md) y lo pone a disposición de los usuarios del laboratorio. El propietario aprovisiona el laboratorio con máquinas virtuales (VM) Windows o Linux que tienen todo el software y las herramientas necesarios. Los usuarios del laboratorio se conectan a las VM de laboratorio para el trabajo diario y proyectos a corto plazo. Los administradores del laboratorio pueden analizar el uso de recursos y los costos en varios laboratorios, y establecer directivas generales para optimizar los costos para la organización o el equipo.

- [Azure Lab Services](https://azure.microsoft.com/services/lab-services) ofrece laboratorios educativos administrados.

  Lab Services se encarga de toda la administración de la infraestructura, desde el arranque de las VM, pasando por el escalado de la infraestructura, hasta el control de errores. Después de que un administrador de TI crea una cuenta de laboratorio de Lab Services, los instructores pueden [crear laboratorios educativos](/azure/lab-services/how-to-manage-classroom-labs#create-a-classroom-lab) en la cuenta. Un instructor especifica el número y el tipo de VM que se necesitan para la clase y agrega usuarios a la clase. Una vez que los usuarios se registran en la clase, pueden acceder a las VM para realizar ejercicios de clase y deberes.

## <a name="key-capabilities"></a>Principales capacidades

DevTest Labs y Lab Services admiten las siguientes características y funcionalidades clave:

- **Configuración rápida y flexible del laboratorio**. Los propietarios e instructores de laboratorio pueden configurar rápidamente laboratorios según sus necesidades. Lab Services se encarga de todo el trabajo de infraestructura de Azure, y proporciona escalado y resistencia integrados de la infraestructura para laboratorios administrados. En DevTest Labs, los propietarios de laboratorio pueden administrar y personalizar la infraestructura de manera autónoma.

- **Experiencia simplificada para los usuarios de laboratorios.** En un laboratorio educativo de Lab Services, los usuarios pueden registrarse con un código y acceder al laboratorio para usar los recursos. El propietario de un laboratorio de DevTest Labs puede conceder permisos a los usuarios del laboratorio para crear VM, acceder a ellas, administrar y reutilizar discos de datos, y configurar secretos reutilizables.

- **Optimización de costos y análisis**. En Lab Services, puede asignar a cada alumno un número limitado de horas para el uso de VM. Un propietario de laboratorio de DevTest Labs puede establecer una programación de laboratorio para especificar cuándo los usuarios pueden acceder a las VM del laboratorio. La programación puede apagar e iniciar automáticamente las VM a las horas especificadas. El propietario del laboratorio puede establecer directivas de uso por usuario o por laboratorio para optimizar los costos. Los propietarios de laboratorio pueden analizar el uso y las tendencias de actividad del laboratorio. Los laboratorios educativos ofrecen un subconjunto más pequeño de opciones de análisis y optimización de costos.

DevTest Labs también admite las características siguientes:

- **Seguridad incrustada**. Un propietario de laboratorio puede configurar una red virtual privada y subredes para un laboratorio, además de habilitar una dirección IP pública compartida. Los usuarios del laboratorio de DevTest Labs pueden acceder de forma segura a los recursos de la red virtual mediante Azure ExpressRoute o una red privada virtual (VPN) de sitio a sitio.

- **Integración de herramientas y flujos de trabajo.** En DevTest Labs, puede aprovisionar automáticamente entornos a partir de las herramientas de integración continua e implementación continua (CI/CD). Puede integrar DevTest Labs en el sitio web y los sistemas de administración de su organización.

## <a name="scenarios"></a>Escenarios

Estos son los escenarios típicos de Lab Services y DevTest Labs:

### <a name="set-up-a-resizable-classroom-computer-lab-in-the-cloud"></a>Configuración de un laboratorio educativo de equipos redimensionables en la nube

- Para crear un laboratorio educativo administrado, solo tiene que indicar a Lab Services lo que necesita. El servicio crea y administra la infraestructura del laboratorio para que usted pueda centrarse en la enseñanza en la clase, no en los detalles técnicos.
- Lab Services brinda a los alumnos un laboratorio de VM que están configuradas exactamente con lo que se necesita. Puede asignar a cada alumno un número limitado de horas para el uso de VM.
- Puede mover un laboratorio de equipos físicos del centro educativo a la nube. Lab Services escala automáticamente el número de VM a solo el umbral de uso y costo máximo que se establezca.
- Puede eliminar laboratorios con un solo clic cuando ya no los necesite.

### <a name="use-devtest-labs-for-development-and-test-environments"></a>Uso de DevTest Labs para entornos de desarrollo y pruebas

Puede usar DevTest Labs para muchos escenarios clave. Un escenario principal es hospedar máquinas de desarrollo y pruebas. DevTest Labs proporciona estas ventajas para desarrolladores y evaluadores:

- Los propietarios y usuarios de laboratorios pueden aprovisionar entornos de Windows y Linux con artefactos y plantillas reutilizables.
- Los desarrolladores pueden aprovisionar rápidamente máquinas de desarrollo a petición, y personalizar fácilmente las máquinas cuando sea necesario.
- Los evaluadores pueden probar la versión más reciente de la aplicación, y escalar verticalmente las pruebas de carga mediante el aprovisionamiento de varios agentes de prueba.
- Los administradores pueden controlar los costos al asegurarse de que los desarrolladores y evaluadores no puedan obtener más VM de las que necesitan.
- Los administradores pueden asegurarse de que las máquinas virtuales se apagan cuando no están en uso.

Para obtener más información, consulte [Uso de DevTest Labs para desarrollo](devtest-lab-developer-lab.md) y [Uso de DevTest Labs para pruebas](devtest-lab-test-env.md).

## <a name="types-of-labs"></a>Tipos de laboratorios

Puede crear dos tipos de laboratorio: **laboratorios administrados** con Lab Services, o **laboratorios** con DevTest Labs. Si solo quiere indicar sus necesidades y dejar que el servicio configure y administre la infraestructura de laboratorio necesaria, seleccione un **laboratorio educativo** de los **tipos de laboratorio administrados** en Lab Services. Si quiere administrar su propia infraestructura, cree un laboratorio con DevTest Labs.

En las siguientes secciones, se proporcionan más detalles de estos tipos de laboratorio.

### <a name="managed-labs"></a>Laboratorios administrados

Los laboratorios administrados son laboratorios de Lab Services con infraestructura que Azure administra. Los tipos de laboratorio administrados pueden ajustarse a necesidades específicas, como laboratorios educativos.

Con los laboratorios administrados, puede comenzar inmediatamente, con una configuración mínima. Para crear un laboratorio educativo, primero debe crear una cuenta de laboratorio para su organización. La cuenta de laboratorio actúa como cuenta central para administrar todos los laboratorios de la organización.

En el caso de los laboratorios administrados, Lab Services crea y administra los recursos de Azure en suscripciones internas de Microsoft, no en su propia suscripción de Azure. El servicio hace un seguimiento del uso de los recursos en las suscripciones internas y factura el uso a la suscripción de Azure que contiene la cuenta de laboratorio.

Estos son algunos de los casos de uso de los tipos de laboratorio administrados:

- Brinde a los alumnos un laboratorio de VM que tengan exactamente lo que se necesita para una clase.
- Limite el número de horas que los alumnos pueden usar las VM.
- Configure un grupo de VM de alto rendimiento para realizar investigaciones de consumo intensivo de procesos o gráficos.
- Mueva un laboratorio de equipos físicos del centro educativo a la nube.
- Aprovisione rápidamente un laboratorio de VM para hospeda un hackatón.

### <a name="devtest-labs"></a>DevTest Labs

Es posible que quiera administrar toda la infraestructura y configuración del laboratorio usted mismo, dentro de su propia suscripción de Azure. En este escenario, cree un laboratorio de DevTest Labs en Azure Portal. No cree ni use una cuenta de laboratorio para DevTest Labs.

Estos son algunos casos de uso de DevTest Labs:

- Aprovisionar rápidamente un laboratorio de VM para hospedar un hackatón o una sesión de conferencia práctica.
- Crear un grupo de VM configuradas con una aplicación que se usará para las búsquedas de errores.
- Proporcionar a los desarrolladores VM configuradas con todas las herramientas que necesitan.
- Crear repetidamente laboratorios de máquinas de prueba para probar los bits más recientes.
- Establecer VM con configuraciones diferentes y varios agentes de pruebas para escalado y pruebas de rendimiento.
- Ofrecer sesiones de entrenamiento a los clientes en un laboratorio configurado con la versión más reciente de un producto.

## <a name="lab-services-vs-devtest-labs"></a>Lab Services frente a DevTest Labs

En la tabla siguiente se comparan los dos tipos de entornos de laboratorio de Azure: 

| Característica | Lab Services | DevTest Labs |
| -------- | ----------------- | ---------- |
| Administración de la infraestructura de Azure. | El servicio se administra automáticamente. | Usted lo administra.  |
| Resistencia de la infraestructura. | El servicio se encarga automáticamente. | Usted se encarga.  |
| Administración de suscripciones. | El servicio controla la asignación de recursos en las suscripciones internas. | Usted administra en su propia suscripción de Azure. |
| Escalabilidad automática. | El servicio se encarga automáticamente. | Sin escalado automático. |
| Implementaciones de Azure Resource Manager. | No disponible. | Disponible. |

## <a name="next-steps"></a>Pasos siguientes

Vea los artículos siguientes: 

- [Acerca de los laboratorios educativos](../lab-services/classroom-labs-overview.md)
- [Acerca de DevTest Labs](devtest-lab-overview.md)
