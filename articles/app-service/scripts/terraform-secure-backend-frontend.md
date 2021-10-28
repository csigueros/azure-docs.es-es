---
title: 'Terraform: Implementación de aplicaciones web de front-end y back-end conectadas de forma segura con Integración con red virtual y Punto de conexión privado'
description: Aprenda a usar el proveedor Terraform para App Service a fin de implementar dos aplicaciones web conectadas de forma segura con Punto de conexión privado e Integración con red virtual
author: ericgre
ms.assetid: 3e5d1bbd-5581-40cc-8f65-bc74f1802156
ms.topic: sample
ms.date: 08/10/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 99afad4cad2cddb706957c8a8b860c6dd55d3b71
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257164"
---
# <a name="create-two-web-apps-connected-securely-with-private-endpoint-and-vnet-integration"></a>Cree dos aplicaciones web conectadas de forma segura con Punto de conexión privado e Integración con red virtual

En este artículo se muestra un ejemplo de uso de [Punto de conexión privado](../networking/private-endpoint.md) y de [Integración de red virtual](../overview-vnet-integration.md) para conectar dos aplicaciones web (front-end y back-end) de forma segura siguiendo estos pasos:
- Implementación de una red virtual
- Creación de la primera subred para la integración
- Creación de la segunda subred para el punto de conexión privado; para ello, tiene que establecer un parámetro específico y deshabilitar las directivas de red
- Implementación de un plan de App Service de tipo PremiumV2 o PremiumV3, necesario para la característica Punto de conexión privado
- Cree la aplicación web de front-end con una configuración de aplicación específica para usar la zona DNS privada; [consulte aquí más detalles](../overview-vnet-integration.md#azure-dns-private-zones)
- Conexión de la aplicación web front-end a la subred de integración
- Creación de la aplicación web back-end
- Creación de la zona privada de DNS con el nombre de la zona de vínculo privado para la aplicación web privatelink.azurewebsites.net
- Vínculo de esta zona a la red virtual
- Creación del punto de conexión privado para la aplicación web back-end en la subred del punto de conexión y registro de los nombres DNS (sitio web y SCM) en la zona DNS privada creada anteriormente

## <a name="how-to-use-terraform-in-azure"></a>Procedimiento para usar Terraform en Azure

Vaya a la [documentación de Azure](/azure/developer/terraform/) para aprender a usar Terraform con Azure.

## <a name="the-complete-terraform-file"></a>El archivo Terraform completo

Para usar este archivo, debe cambiar la propiedad Name de los recursos frontwebapp y backwebapp (el nombre de webapp debe ser un nombre DNS único en todo el mundo). 

```hcl
terraform {
  required_providers {
    azurerm = {
      source = "hashicorp/azurerm"
      version = "~>2.0"
    }
  }
}
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "rg" {
  name     = "appservice-rg"
  location = "francecentral"
}

resource "azurerm_virtual_network" "vnet" {
  name                = "vnet"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  address_space       = ["10.0.0.0/16"]
}

resource "azurerm_subnet" "integrationsubnet" {
  name                 = "integrationsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.1.0/24"]
  delegation {
    name = "delegation"
    service_delegation {
      name = "Microsoft.Web/serverFarms"
    }
  }
}

resource "azurerm_subnet" "endpointsubnet" {
  name                 = "endpointsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.2.0/24"]
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_app_service_plan" "appserviceplan" {
  name                = "appserviceplan"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  sku {
    tier = "Premiumv2"
    size = "P1v2"
  }
}

resource "azurerm_app_service" "frontwebapp" {
  name                = "frontwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id

  app_settings = {
    "WEBSITE_DNS_SERVER": "168.63.129.16",
    "WEBSITE_VNET_ROUTE_ALL": "1"
  }
}

resource "azurerm_app_service_virtual_network_swift_connection" "vnetintegrationconnection" {
  app_service_id  = azurerm_app_service.frontwebapp.id
  subnet_id       = azurerm_subnet.integrationsubnet.id
}

resource "azurerm_app_service" "backwebapp" {
  name                = "backwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id
}

resource "azurerm_private_dns_zone" "dnsprivatezone" {
  name                = "privatelink.azurewebsites.net"
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_private_dns_zone_virtual_network_link" "dnszonelink" {
  name = "dnszonelink"
  resource_group_name = azurerm_resource_group.rg.name
  private_dns_zone_name = azurerm_private_dns_zone.dnsprivatezone.name
  virtual_network_id = azurerm_virtual_network.vnet.id
}

resource "azurerm_private_endpoint" "privateendpoint" {
  name                = "backwebappprivateendpoint"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  subnet_id           = azurerm_subnet.endpointsubnet.id

  private_dns_zone_group {
    name = "privatednszonegroup"
    private_dns_zone_ids = [azurerm_private_dns_zone.dnsprivatezone.id]
  }

  private_service_connection {
    name = "privateendpointconnection"
    private_connection_resource_id = azurerm_app_service.backwebapp.id
    subresource_names = ["sites"]
    is_manual_connection = false
  }
}
```




## <a name="next-steps"></a>Pasos siguientes


> [Más información sobre el uso de Terraform en Azure](/azure/developer/terraform/)