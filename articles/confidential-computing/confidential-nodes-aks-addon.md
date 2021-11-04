---
title: Complemento de Azure Kubernetes Service para máquinas virtuales confidenciales
description: Cómo usar el complemento de dispositivo Intel SGX y los conjuntos de demonios del asistente de citas Intel SGX para máquinas virtuales confidenciales con Azure Kubernetes Service.
author: agowdamsft
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: article
ms.date: 11/01/2021
ms.author: amgowda
ms.custom: ignite-fall-2021
ms.openlocfilehash: f4a00e1c70427c6b467a52694340e10109b5f4f5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093755"
---
# <a name="confidential-computing-plugin-for-confidential-vms"></a>Complemento de computación confidencial para máquinas virtuales confidenciales

Azure Kubernetes Service (AKS) proporciona un complemento para máquinas virtuales (VM) de computación confidencial de Azure. El complemento, `confcom`, es un conjunto de demonios. El complemento solo se ejecuta para máquinas virtuales confidenciales de Intel Software Guard Extensions (SGX) en un clúster de AKS. Este complemento se registra en el nivel de clúster de AKS. Puede usar el complemento para administrar nodos confidenciales fácilmente. [Habilite el complemento en el clúster de AKS](./confidential-enclave-nodes-aks-get-started.md) antes de empezar.

## <a name="intel-sgx-device-plugin-for-aks"></a>Complemento de dispositivo Intel SGX para AKS

El complemento de dispositivo SGX implementa la interfaz del complemento de dispositivos Kubernetes para la memoria de Enclave Page Cache (EPC). De hecho, este complemento convierte la memoria EPC en otro tipo de recurso en Kubernetes. Los usuarios pueden especificar límites en EPC igual que en otros recursos. Además de la función de programación, el complemento del dispositivo ayuda a asignar los permisos del controlador de dispositivos de SGX a los contenedores de cargas de trabajo confidenciales. [Hay disponible una implementación de muestra de la implementación basada en memoria de EPC ](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml) (`kubernetes.azure.com/sgx_epc_mem_in_MiB`).

## <a name="psm-with-sgx-quote-helper"></a>PSM con el asistente de citas SGX

Las aplicaciones de enclave que realizan la atestación remota necesitan generar una oferta. La cita proporciona una prueba criptográfica de la identidad y del estado de la aplicación junto con el entorno en que se hospeda el enclave. La generación de una cita utiliza determinados componentes de software de confianza de Intel, que forman parte de los componentes de software de plataforma (PSW/DCAP) de SGX. Este PSW se empaqueta como un DaemonSet que se ejecuta por nodo. Puede utilizar el PSW al solicitar la cita de atestación de las aplicaciones de enclave. El uso del servicio proporcionado por AKS ayuda a mantener mejor la compatibilidad entre el PSW y otros componentes de software del host. Lea los detalles de la característica a continuación.

Las [aplicaciones de enclave](confidential-computing-enclaves.md) que realizan la atestación remota requieren que se genere una cita. Esta cita proporciona una prueba criptográfica de la identidad, el estado y el entorno de ejecución de la aplicación. La generación requiere componentes de software de confianza que formen parte del PSW de Intel.

### <a name="overview"></a>Información general

> [!NOTE]
> Esta característica solo es necesaria para las máquinas virtuales DCsv2/DCsv3 que usan hardware Intel SGX especializado. 
 
Intel admite dos modos de atestación para ejecutar la generación de citas. Para saber cómo elegir qué tipo, consulte las diferencias de [tipo de atestación](#attestation-type-differences).

- **En proceso**: hospeda los componentes de software de confianza que hay en el proceso de la aplicación de enclave.

- **Fuera del proceso**: hospeda los componentes de software de confianza que están fuera del proceso de la aplicación de enclave.
 
Las aplicaciones de SGX creadas con el SDK Open Enclave usan de manera predeterminada el modo de atestación en proceso. Las aplicaciones basadas en SGX permiten el modo "Fuera de proceso" y requieren hospedaje adicional. Estas aplicaciones exponen los componentes necesarios, como Architectural Enclave Service Manager (AESM), externos a la aplicación.

Se recomienda encarecidamente usar esta característica. El uso de esta característica mejora el tiempo de actividad de las aplicaciones de enclave durante las actualizaciones de la plataforma Intel o de los controladores de DCAP.

### <a name="attestation-type-differences"></a>Diferencias del tipo de atestación

No se requieren actualizaciones de los componentes de generación de citas de PSW para cada aplicación contenedorizada.

Con el modo fuera del proceso, los propietarios de los contenedores no necesitan administrar las actualizaciones de su contenedor. En su lugar, los propietarios de los contenedores confían en la interfaz proporcionada, que invoca el servicio centralizado fuera del contenedor. El proveedor actualiza y administra este servicio.

En el caso del modo "Fuera de proceso", no hay ningún problema de errores debido a los componentes de PSW no actualizados. La generación de citas implica a los componentes de software de confianza, Quoting Enclave (QE) y Provisioning Certificate Enclave (PCE), que forman parte de la base de la computación de confianza (TCB). Estos componentes de software deben estar actualizados para mantener los requisitos de atestación. El proveedor administra las actualizaciones de estos componentes. Los clientes nunca tienen que tratar con errores de atestación debido a componentes de SW de confianza no actualizados dentro de su contenedor.

El modo "Fuera de proceso" usa mejor la memoria EPC. En el modo de atestación "En proceso", cada aplicación de enclave crea una instancia de la copia de QE y PCE para la atestación remota. Con el modo "Fuera de proceso", el contenedor no hospeda esos enclaves, por lo que no consume memoria de enclave de la cuota del contenedor.

También hay medidas de seguridad contra la aplicación del kernel. Cuando el controlador SGX se transmite al kernel de Linux, un enclave tiene más privilegios. Este privilegio permite que el enclave invoque a PCE, lo que interrumpe la aplicación de enclave que se ejecuta en el modo en proceso. De forma predeterminada, los enclaves no obtienen este permiso. La concesión de este privilegio a una aplicación de enclave requiere cambios en el proceso de instalación de la misma. El proveedor del servicio que controla las solicitudes fuera de proceso se asegura de que el servicio está instalado con este privilegio.

No es preciso comprobar la compatibilidad con versiones anteriores de PSW y DCAP. El proveedor valida las actualizaciones de los componentes de generación de citas de PSW para la compatibilidad con versiones anteriores. En este paso se controlan los problemas de compatibilidad por adelantado y se abordan antes de implementar las actualizaciones en las cargas de trabajo confidenciales.

### <a name="out-of-proc-attestation-for-confidential-workloads"></a>Atestación fuera de proceso para cargas de trabajo confidenciales

El modelo de atestación fuera de proceso funciona para cargas de trabajo confidenciales. El solicitante de la cita y la generación de la cita se ejecutan por separado, pero en la misma máquina física. La generación de la cita ocurre de forma centralizada y atiende solicitudes de citas de todas las entidades. Defina correctamente la interfaz y haga que la interfaz sea reconocible para que cualquier entidad solicite citas.

![Diagrama del solicitante de citas y la interfaz de generación de citas.](./media/confidential-nodes-out-of-proc-attestation/aesmmanager.png)

El modelo abstracto se aplica a escenarios de cargas de trabajo confidenciales. Este modelo usa el servicio AESM ya disponible. AESM se encuentra en un contenedor y se implementa como un conjunto de demonios en el clúster de Kubernetes. Kubernetes garantiza que, en cada nodo de agente, se implementará una única instancia de un contenedor de servicios AESM, encapsulada en un pod. El nuevo conjunto de demonios de citas de SGX tiene una dependencia del conjunto de demonios `sgx-device-plugin`, ya que el contenedor del servicio AESM solicitará memoria EPC de `sgx-device-plugin` para iniciar los enclaves QE y PCE.

Todos los contenedores deben participar para usar la generación de citas fuera del proceso. Para ello, es preciso establecer la variable de entorno `SGX_AESM_ADDR=1` durante la creación. El contenedor también debe incluir el paquete `libsgx-quote-ex`, que dirige la solicitud al socket de dominio de Unix predeterminado

Una aplicación todavía puede usar la atestación en proceso como antes. Sin embargo, no puede usar simultáneamente tanto el modo "En proceso" como "Fuera de proceso" dentro de una aplicación. La infraestructura del modo fuera del proceso está disponible de forma predeterminada y consume recursos.

### <a name="sample-implementation"></a>Implementación de ejemplo

El siguiente archivo de Docker es un ejemplo de una aplicación basada en Open Enclave. Establezca la variable de entorno `SGX_AESM_ADDR=1` en el archivo de Docker. O bien, establezca la variable en el archivo de implementación. Para más información sobre el archivo de Docker y sobre el archivo YAML de la implementación, siga esta muestra. 

> [!Note] 
> Para que el modo fuera del proceso funcione correctamente, es necesario que el paquete **libsgx-quote-ex** de Intel se empaquete en el contenedor de la aplicación.
    
```yaml
# Refer to Intel_SGX_Installation_Guide_Linux for detail
FROM ubuntu:18.04 as sgx_base
RUN apt-get update && apt-get install -y \
    wget \
    gnupg

# Add the repository to sources, and add the key to the list of
# trusted keys used by the apt to authenticate packages
RUN echo "deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main" | tee /etc/apt/sources.list.d/intel-sgx.list \
    && wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | apt-key add -
# Add Microsoft repo for az-dcap-client
RUN echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | tee /etc/apt/sources.list.d/msprod.list \
    && wget -qO - https://packages.microsoft.com/keys/microsoft.asc | apt-key add -

FROM sgx_base as sgx_sample
RUN apt-get update && apt-get install -y \
    clang-7 \
    libssl-dev \
    gdb \
    libprotobuf10 \
    libsgx-dcap-ql \
    libsgx-quote-ex \
    az-dcap-client \
    open-enclave
WORKDIR /opt/openenclave/share/openenclave/samples/remote_attestation
RUN . /opt/openenclave/share/openenclave/openenclaverc \
    && make build
# this sets the flag for out of proc attestation mode. alternatively you can set this flag on the deployment files
ENV SGX_AESM_ADDR=1 

CMD make run
```
En su lugar, establezca el modo de atestación "Fuera de proceso" en el archivo YAML de implementación como se muestra a continuación:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
spec:
  template:
    spec:
      containers:
      - name: sgxtest
        image: <registry>/<repository>:<version>
        env:
        - name: SGX_AESM_ADDR
          value: 1
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10
        volumeMounts:
        - name: var-run-aesmd
          mountPath: /var/run/aesmd
      restartPolicy: "Never"
      volumes:
      - name: var-run-aesmd
        hostPath:
          path: /var/run/aesmd
```

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de nodos confidenciales (serie DCsv2/DCsv3) en AKS](./confidential-enclave-nodes-aks-get-started.md)
- [Inicio rápido con muestras de contenedores confidenciales](https://github.com/Azure-Samples/confidential-container-samples)
- [Lista de SKU de DCsv2](../virtual-machines/dcv2-series.md)
- [Lista de SKU de DCSv3](../virtual-machines/dcv3-series.md)
- [Azure Attestation](../attestation/index.yml)
- [Máquinas virtuales confidenciales de Intel SGX en Azure](/confidential-computing/virtual-machine-solutions-sgx.md)
