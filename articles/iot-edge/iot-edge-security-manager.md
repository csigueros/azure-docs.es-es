---
title: 'Entorno de ejecución del módulo o administrador de seguridad de Azure IoT Edge: Azure IoT Edge'
description: Administra la posición de seguridad del dispositivo IoT Edge y la integridad de los servicios de seguridad.
services: iot-edge
keywords: seguridad, elemento seguro, enclave, TEE, IoT Edge
author: kgremban
ms.author: kgremban
ms.reviewer: eustacea
ms.date: 09/17/2021
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: fedb06a23a71dbe30954154daa6d314267268551
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129659685"
---
# <a name="azure-iot-edge-security-manager"></a>Administrador de seguridad de IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

El Administrador de seguridad de Azure IoT Edge es un núcleo de seguridad bien delimitado para proteger el dispositivo IoT Edge y todos sus componentes mediante la abstracción de hardware de silicio seguro. El responsable de seguridad es el punto focal para fortalecer la seguridad y proporciona a los fabricantes de equipos originales (OEM) el punto de integración de la tecnología.

<!--1.1-->
:::moniker range="iotedge-2018-06"
El administrador de seguridad abstrae el hardware de silicio seguro en un dispositivo IoT Edge.

![Administrador de seguridad de IoT Edge](media/edge-security-manager/iot-edge-security-manager.png)
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
El administrador de seguridad abstrae el hardware de silicio seguro en un dispositivo IoT Edge y proporciona un marco de extensibilidad para servicios de seguridad adicionales.

<!-- add new graphic here -->
:::moniker-end

El objeto del administrador de seguridad de IoT Edge es defender la integridad del dispositivo IoT Edge y todas las operaciones de software inherentes. El administrador de seguridad transiciona la confianza desde la raíz de confianza de hardware subyacente (si está disponible) para arrancar en el entorno de ejecución de IoT Edge y supervisar la integridad de sus operaciones.  El administrador de seguridad de IoT Edge es software que funciona junto con el hardware de silicio seguro (donde esté disponible) para ayudar a entregar las garantías de seguridad más altas posible.  

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Además, el administrador de seguridad de IoT Edge proporciona un marco seguro para las extensiones de servicio de seguridad a través de módulos de nivel de host. Estos servicios incluyen la supervisión de seguridad y las actualizaciones que requieren agentes dentro del dispositivo con acceso con privilegios a algunos componentes del dispositivo. El marco de extensibilidad garantiza que dichas integraciones cumplan de forma coherente la seguridad general del sistema.
:::moniker-end

Las responsabilidades del administrador de seguridad de IoT Edge incluyen, entre otras:

<!--1.1-->
:::moniker range="iotedge-2018-06"

* Arranque del dispositivo Azure IoT Edge.
* Control del acceso en la raíz de confianza de hardware de dispositivo a través de servicios notariales.
* Supervisión de la integridad de las operaciones de IoT Edge en el entorno de ejecución.
* Recepción de la delegación de confianza del módulo de seguridad de hardware (HSM).
* Aprovisionamiento de la identidad del dispositivo y administración de la transición de confianza cuando corresponda.
* Hospedaje y protección de los componentes del dispositivo de servicios en la nube como servicio Device Provisioning.
* Aprovisionamiento de los módulos de IoT Edge con identidades únicas.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

* Arranque del dispositivo Azure IoT Edge.
* Control del acceso en la raíz de confianza de hardware de dispositivo a través de servicios notariales.
* Supervisión de la integridad de las operaciones de IoT Edge en el entorno de ejecución.
* Aprovisionamiento de la identidad del dispositivo y administración de la transición de confianza cuando corresponda.
* Garantía del funcionamiento seguro de los agentes cliente para los servicios, incluidos Device Update for IoT Hub y Azure Defender para IoT.
:::moniker-end

El administrador de seguridad de IoT Edge consta de tres componentes:

<!--1.1-->
:::moniker range="iotedge-2018-06"

* El demonio de seguridad de IoT Edge
* Capa de abstracción de plataforma del módulo de seguridad de hardware (HSM PAL).
* HSM o raíz de confianza de silicio de hardware (opcional, pero altamente recomendado).
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

* Entorno de ejecución del módulo de IoT Edge.
* Abstracciones del módulo de seguridad de hardware (HSM) a través de implementaciones estándar como PKCS#11 y Módulo de plataforma segura (TPM).
* HSM o raíz de confianza de silicio de hardware (opcional, pero altamente recomendado).

## <a name="changes-in-version-12"></a>Cambios en la versión 1.2

En las versiones 1.0 y 1.1 de IoT Edge, un componente denominado **demonio de seguridad** era responsable de las operaciones de seguridad lógicas del administrador de seguridad. En la actualización a la versión 1.2, se delegaron varias responsabilidades clave en el subsistema de seguridad del [servicio de identidad de Azure IoT](https://azure.github.io/iot-identity-service/). Una vez que estas tareas basadas en seguridad se quitaron del demonio de seguridad, su nombre ya no tenía sentido. Para reflejar mejor el trabajo que este componente realiza en la versión 1.2 y posteriores, se ha cambiado el nombre a **entorno de ejecución del módulo**.
:::moniker-end

<!--1.1-->
:::moniker range="iotedge-2018-06"
## <a name="the-iot-edge-security-daemon"></a>El demonio de seguridad de IoT Edge

El demonio de seguridad de IoT Edge es el responsable de las operaciones lógicas de seguridad del administrador de seguridad. Representa una porción importante de la base informática de confianza del dispositivo IoT Edge.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
## <a name="the-iot-edge-module-runtime"></a>Entorno de ejecución del módulo de IoT Edge.

El entorno de ejecución del módulo de IoT Edge delega la confianza del subsistema de seguridad del [servicio de identidad de Azure IoT](https://azure.github.io/iot-identity-service/) para proteger el entorno de ejecución del contenedor de IoT Edge. El entorno de ejecución del módulo es el responsable de las operaciones lógicas de seguridad del administrador de seguridad. Representa una porción importante de la base informática de confianza del dispositivo IoT Edge. El entorno de ejecución del módulo usa servicios de seguridad del servicio de identidad de IoT, que a su vez está protegido por la elección del módulo de seguridad de hardware (HSM) del fabricante del dispositivo. Se recomienda encarecidamente el uso de HSM para la protección de dispositivos.
:::moniker-end

### <a name="design-principles"></a>Principios de diseño

IoT Edge sigue dos principios básicos: maximizar la integridad operativa y minimizar el sobredimensionamiento y la renovación.

#### <a name="maximize-operational-integrity"></a>Maximización de la integridad de las operaciones

<!--1.1-->
:::moniker range="iotedge-2018-06"
El demonio de seguridad de IoT Edge funciona con la mayor integridad posible dentro de la funcionalidad de defensa de cualquier hardware de raíz de confianza determinado. Con una integración correcta, el hardware de raíz de confianza mide y supervisa el demonio de seguridad de manera estática y en el entorno de ejecución para resistir manipulaciones.
:::moniker-end

<!--1.1-->
:::moniker range=">=iotedge-2020-11"
El entorno de ejecución del módulo de IoT Edge funciona con la mayor integridad posible dentro de la funcionalidad de defensa de cualquier hardware de raíz de confianza determinado. Con una integración correcta, el hardware de raíz de confianza mide y supervisa el demonio de seguridad de manera estática y en el entorno de ejecución para resistir manipulaciones.
:::moniker-end

El acceso físico malintencionado a los dispositivos siempre es una amenaza en IoT. La raíz de confianza de hardware juega un papel importante en la defensa de la integridad del dispositivo IoT Edge.  La raíz de confianza de hardware tiene dos variantes:

* Elementos seguros para la protección de información confidencial, como secretos y claves criptográficas.
* Enclaves seguros para la protección de secretos, como claves, y cargas de trabajo confidenciales, como modelos de aprendizaje automático confidenciales y operaciones de medición.

Existen dos tipos de entornos de ejecución para usar la raíz de confianza del hardware:

* El entorno de ejecución estándar o enriquecido (REE) que se basa en el uso de elementos seguros para proteger información confidencial.
* El entorno de ejecución de confianza (TEE) que se basa en el uso de la tecnología de enclave seguro para proteger información confidencial y ofrecer protección para la ejecución de software.

<!--1.1-->
:::moniker range="iotedge-2018-06"
En el caso de los dispositivos que usan los enclaves seguros como raíz de confianza de hardware, la lógica confidencial dentro del demonio de seguridad de IoT Edge debe encontrarse dentro del enclave.  Las partes no confidenciales del demonio de seguridad pueden estar fuera del entorno de ejecución de confianza.  En cualquier caso, se recomienda encarecidamente que los original design manufacturers (ODM) y los fabricantes de equipos originales (OEM) amplíen la confianza desde su HSM para medir y defender la integridad del demonio de seguridad de IoT Edge en el arranque y en el entorno de ejecución.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
En el caso de los dispositivos que usan los enclaves seguros como raíz de confianza de hardware, la lógica confidencial dentro del entorno de ejecución del módulo de IoT Edge debe encontrarse dentro del enclave.  Las partes no confidenciales del entorno de ejecución del módulo pueden estar fuera de TEE.  En cualquier caso, se recomienda encarecidamente que los original design manufacturers (ODM) y los fabricantes de equipos originales (OEM) amplíen la confianza desde su HSM para medir y defender la integridad del entorno de ejecución del módulo de IoT Edge en el arranque y en el entorno de ejecución.
:::moniker-end

#### <a name="minimize-bloat-and-churn"></a>Minimización del sobredimensionamiento y la renovación

<!--1.1-->
:::moniker range="iotedge-2018-06"
Otro principio básico para el demonio de seguridad de IoT Edge es minimizar la renovación.  Para obtener el máximo nivel de confianza, el demonio de seguridad de IoT Edge puede acoplarse estrechamente con la raíz de confianza de hardware de dispositivo y funcionar como código nativo.  En estos casos, es habitual actualizar el software de IoT Edge a través de las rutas de actualización seguras de la raíz de confianza del hardware en lugar de los mecanismos de actualización del sistema operativo, lo que puede ser complicado.  La renovación de seguridad es una recomendación para los dispositivos IoT, pero los requisitos excesivos de actualización o las cargas de actualizaciones de gran tamaño pueden expandir de muchas maneras la superficie expuesta a amenazas. Por ejemplo, puede verse tentado a omitir algunas actualizaciones para maximizar la disponibilidad del dispositivo. Por tanto, el diseño del demonio de seguridad de IoT Edge es conciso para que la base informática de confianza bien aislada no crezca con el fin de incentivar las actualizaciones frecuentes.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Otro principio básico para el entorno de ejecución del módulo de IoT Edge es minimizar la renovación.  Para obtener el máximo nivel de confianza, el entorno de ejecución del módulo de IoT Edge puede acoplarse estrechamente con la raíz de confianza de hardware de dispositivo y funcionar como código nativo.  En estos casos, es habitual actualizar el software de IoT Edge a través de las rutas de actualización seguras de la raíz de confianza del hardware en lugar de los mecanismos de actualización del sistema operativo, lo que puede ser complicado.  La renovación de seguridad es una recomendación para los dispositivos IoT, pero los requisitos excesivos de actualización o las cargas de actualizaciones de gran tamaño pueden expandir de muchas maneras la superficie expuesta a amenazas. Por ejemplo, puede verse tentado a omitir algunas actualizaciones para maximizar la disponibilidad del dispositivo. Por tanto, el diseño del entorno de ejecución del módulo de IoT Edge es conciso para que la base informática de confianza bien aislada no crezca con el fin de incentivar las actualizaciones frecuentes.
:::moniker-end

### <a name="architecture"></a>Architecture

<!--1.1-->
:::moniker range="iotedge-2018-06"

El demonio de seguridad de IoT Edge aprovecha las ventajas de cualquier tecnología de raíz de confianza de hardware disponible para fortalecer la seguridad.  También permite la operación de división global entre un entorno de ejecución estándar o enriquecido (REE) y un entorno de ejecución de confianza (TEE) cuando las tecnologías de hardware ofrecen los entornos de ejecución de confianza. Las interfaces específicas de rol habilitan los componentes importantes de IoT Edge para garantizar la integridad del dispositivo IoT Edge y sus operaciones.

![Arquitectura del demonio de seguridad de Azure IoT Edge](media/edge-security-manager/iot-edge-security-daemon.png)
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

El entorno de ejecución del módulo de IoT Edge aprovecha las ventajas de cualquier tecnología de raíz de confianza de hardware disponible para fortalecer la seguridad.  También permite la operación de división global entre un entorno de ejecución estándar o enriquecido (REE) y un entorno de ejecución de confianza (TEE) cuando las tecnologías de hardware ofrecen los entornos de ejecución de confianza. Las interfaces específicas de rol habilitan los componentes importantes de IoT Edge para garantizar la integridad del dispositivo IoT Edge y sus operaciones.

![Arquitectura del entorno de ejecución del módulo de Azure IoT Edge](media/edge-security-manager/iot-edge-module-runtime.png)
:::moniker-end

#### <a name="cloud-interface"></a>Interfaz de la nube

La interfaz en la nube permite el acceso a servicios en la nube que complementan la seguridad de los dispositivos.  Por ejemplo, esta interfaz permite el acceso al [servicio de aprovisionamiento de dispositivos](../iot-dps/index.yml) para la administración del ciclo de vida de la identidad del dispositivo.  

#### <a name="management-api"></a>API de administración

<!--1.1-->
:::moniker range="iotedge-2018-06"
El agente de IoT Edge llama a la API de administración en el momento de crear, iniciar, detener o eliminar un módulo de IoT Edge. El demonio de seguridad almacena "registros" para todos los módulos activos. Estos registros asignan la identidad de un módulo a algunas propiedades del módulo. Por ejemplo, estas propiedades del módulo incluyen el identificador de proceso (pid) del proceso que se ejecuta en el contenedor y el hash del contenido del contenedor de Docker.

La API de carga de trabajo (que se describe a continuación) usa estas propiedades para comprobar que el autor de llamada está autorizado para hacer una acción.

La API de administración es una API con privilegios, a la que solo se puede llamar desde el agente de IoT Edge.  Como el demonio de seguridad de IoT Edge arranca e inicia al agente de IoT Edge, comprueba que el agente de IoT Edge no se ha alterado y entonces puede crear un registro implícito para él. El mismo proceso de atestación que usa la API de carga de trabajo restringe también el acceso a la API de administración solo para el agente de IoT Edge.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
El agente de IoT Edge llama a la API de administración en el momento de crear, iniciar, detener o eliminar un módulo de IoT Edge. El entorno de ejecución del módulo almacena "registros" para todos los módulos activos. Estos registros asignan la identidad de un módulo a algunas propiedades del módulo. Por ejemplo, estas propiedades del módulo incluyen el identificador de proceso (pid) del proceso que se ejecuta en el contenedor y el hash del contenido del contenedor de Docker.

La API de carga de trabajo (que se describe a continuación) usa estas propiedades para comprobar que el autor de llamada está autorizado para hacer una acción.

La API de administración es una API con privilegios, a la que solo se puede llamar desde el agente de IoT Edge.  Como el entorno de ejecución del módulo de IoT Edge arranca e inicia el agente de IoT Edge, comprueba que el agente de IoT Edge no se ha alterado y entonces puede crear un registro implícito para él. El mismo proceso de atestación que usa la API de carga de trabajo restringe también el acceso a la API de administración solo para el agente de IoT Edge.
:::moniker-end

#### <a name="container-api"></a>API de contenedor

La API de contenedor interactúa con el sistema de contenedor en uso para la administración de módulos, como Moby o Docker.

#### <a name="workload-api"></a>API de carga de trabajo

La API de carga de trabajo es accesible para todos los módulos. Proporciona una prueba de identidad, en forma de un certificado X509 o token firmado liberado de HSM, y la agrupación de confianza correspondiente a un módulo. La agrupación de confianza contiene certificados de entidad de certificación para todos los demás servidores en que deben confiar los módulos.

<!--1.1-->
:::moniker range="iotedge-2018-06"
El demonio de seguridad de IoT Edge usa un proceso de atestación para proteger esta API. Cuando un módulo llama a esta API, el demonio de seguridad intenta encontrar un registro para la identidad. Si eso se completa correctamente, usa las propiedades del registro para medir el módulo. Si el resultado del proceso de medición coincide con el registro, se genera una nueva prueba de identidad. Los certificados de entidad de certificación correspondientes (agrupación de confianza) se devuelven al módulo.  El módulo usa este certificado para conectarse a IoT Hub, otros módulos, o iniciar un servidor. Cuando el certificado o un token firmado se acerca a la expiración, es responsabilidad del módulo solicitar un certificado nuevo.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
El entorno de ejecución del módulo de IoT Edge usa un proceso de atestación para proteger esta API. Cuando un módulo llama a esta API, el entorno de ejecución del módulo intenta encontrar un registro para la identidad. Si eso se completa correctamente, usa las propiedades del registro para medir el módulo. Si el resultado del proceso de medición coincide con el registro, se genera una nueva prueba de identidad. Los certificados de entidad de certificación correspondientes (agrupación de confianza) se devuelven al módulo.  El módulo usa este certificado para conectarse a IoT Hub, otros módulos, o iniciar un servidor. Cuando el certificado o un token firmado se acerca a la expiración, es responsabilidad del módulo solicitar un certificado nuevo.
:::moniker-end

### <a name="integration-and-maintenance"></a>Integración y mantenimiento

<!--1.1-->
:::moniker range="iotedge-2018-06"
Microsoft mantiene el código base principal para el [demonio de seguridad de IoT Edge en GitHub](https://github.com/Azure/iotedge/tree/release/1.1/edgelet).
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Microsoft mantiene la base de código principal para el [entorno de ejecución del módulo de IoT Edge](https://github.com/Azure/iotedge/tree/master/edgelet) y el [servicio de identidad de Azure IoT](https://github.com/Azure/iot-identity-service) en GitHub.

Al leer el código base de IoT Edge, recuerde que el **entorno de ejecución del módulo** evolucionó a partir del **demonio de seguridad**. El código base todavía puede contener referencias al demonio de seguridad.
:::moniker-end

#### <a name="installation-and-updates"></a>Instalación y actualizaciones

<!--1.1-->
:::moniker range="iotedge-2018-06"
La instalación y las actualizaciones del demonio de seguridad de IoT Edge se administra a través del sistema de administración de paquetes del sistema operativo. Los dispositivos IoT Edge con raíz de confianza de hardware deben proporcionar una protección adicional a la integridad del demonio a través de la administración de su ciclo de vida mediante los sistemas de administración de actualizaciones y el arranque seguro. Los fabricantes de dispositivos deben explorar estas vías en función de sus respectivas funcionalidades del dispositivo.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
La instalación y las actualizaciones del entorno de ejecución del módulo de IoT Edge se administra a través del sistema de administración de paquetes del sistema operativo. Los dispositivos IoT Edge con raíz de confianza de hardware deben proporcionar una protección adicional a la integridad del entorno de ejecución del módulo a través de la administración de su ciclo de vida mediante los sistemas de administración de actualizaciones y el arranque seguro. Los fabricantes de dispositivos deben explorar estas vías en función de sus respectivas funcionalidades del dispositivo.
:::moniker-end

#### <a name="versioning"></a>Control de versiones

<!--1.1-->
:::moniker range="iotedge-2018-06"
El entorno de ejecución de IoT Edge hace el seguimiento de la versión del demonio de seguridad de IoT Edge y la notifica. La versión se notifica como el atributo *runtime.platform.version* de la propiedad notificada del módulo del agente de IoT Edge.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
El entorno de ejecución de IoT Edge supervisa la versión del entorno de ejecución del módulo de IoT Edge y la notifica. La versión se notifica como el atributo *runtime.platform.version* de la propiedad notificada del módulo del agente de IoT Edge.
:::moniker-end

## <a name="hardware-security-module"></a>Módulo de seguridad de hardware

<!--1.1-->
:::moniker range="iotedge-2018-06"
La capa de abstracción de la plataforma del módulo de seguridad de hardware (HSM PAL) abstrae todo el hardware de raíz de confianza para aislar al desarrollador o usuario de IoT Edge de sus complejidades.  Incluye una combinación de la interfaz de programación de aplicaciones (API) y procedimientos de comunicaciones entre dominios, por ejemplo, la comunicación entre un entorno de ejecución estándar y un enclave seguro.  La implementación real de HSM PAL depende del hardware seguro específico que esté en uso. Su existencia permite el uso de prácticamente cualquier hardware de silicio seguro.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
El administrador de seguridad de IoT Edge implementa los estándares de interfaz PKCS#11 y el Módulo de plataforma segura y para integrar módulos de seguridad de hardware (HSM). Con estos estándares, se puede integrar prácticamente cualquier HSM, incluidos los que tienen interfaces de su propiedad. Se recomienda encarecidamente usar HSM para la protección de la seguridad.
:::moniker-end

## <a name="secure-silicon-root-of-trust-hardware"></a>Hardware de raíz de confianza de silicio seguro

El silicio seguro es necesario para delimitar la confianza dentro del hardware del dispositivo IoT Edge.  El silicio seguro viene en una gran variedad de formas que incluyen Módulo de plataforma segura (TPM), Elemento seguro integrado (eSE), Arm TrustZone, Intel SGX y tecnologías de silicio seguro.  Se recomienda usar la raíz de confianza de silicio seguro en dispositivos, dadas las amenazas asociadas con la accesibilidad física de los dispositivos IoT.

El objetivo del administrador de seguridad de IoT Edge es identificar y aislar los componentes que se encargan de defender la seguridad y la integridad de la plataforma de Azure IoT Edge para el sistema de protección personalizado. Los terceros (como fabricantes de dispositivos) deben usar las características de seguridad personalizadas disponibles con su hardware de dispositivo.  

Aprenda a proteger el administrador de seguridad de Azure IoT con el Módulo de plataforma segura (TPM) mediante TPM virtuales o de software:  

Cree y aprovisione un [dispositivo IoT Edge con un TPM virtual en una máquina virtual con Linux](how-to-provision-devices-at-scale-linux-tpm.md).

<!--1.1-->
:::moniker range="iotedge-2018-06"
Cree y aprovisione un [dispositivo IoT Edge con un TPM simulado en Windows](how-to-provision-devices-at-scale-windows-tpm.md).
:::moniker-end

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre la protección de dispositivos IoT Edge, lea las siguientes entradas de blog:

* [Protección del perímetro inteligente](https://azure.microsoft.com/blog/securing-the-intelligent-edge/)
* [Plano técnico para resolver de forma segura el difícil aprovisionamiento de dispositivos IoT a gran escala sin intervención](https://azure.microsoft.com/blog/the-blueprint-to-securely-solve-the-elusive-zerotouch-provisioning-of-iot-devices-at-scale/)
* [Resolución de la seguridad de dispositivos IoT a gran escala mediante estándares](https://azure.microsoft.com/blog/solving-iot-device-security-at-scale-through-standards/)