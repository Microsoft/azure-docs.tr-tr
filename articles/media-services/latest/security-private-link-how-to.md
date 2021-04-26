---
title: Özel bağlantıyla bir Media Services ve depolama hesabı oluşturma
titleSuffix: Media Services
description: Bir sanal ağa yönelik özel bağlantılarla bir Media Services hesabı ve depolama hesabı oluşturun. Azure Resource Manager (ARM) şablonu, her iki özel bağlantı için de DNS 'i ayarlar. Son olarak şablon, kullanıcının özel bağlantıları denemesini sağlamak için bir VM oluşturur.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 04/15/2021
ms.author: inhenkel
ms.openlocfilehash: 43997810798322a628490aa72db74944f2fc38c7
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590361"
---
# <a name="create-a-media-services-and-storage-account-with-a-private-link"></a>Özel bağlantıyla bir Media Services ve depolama hesabı oluşturma

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bir sanal ağa yönelik özel bağlantılarla bir Media Services hesabı ve depolama hesabı oluşturun. Azure Resource Manager (ARM) şablonu, her iki özel bağlantı için de DNS 'i ayarlar. Son olarak şablon, kullanıcının özel bağlantıları denemesini sağlamak için bir VM oluşturur.

## <a name="prerequisites"></a>Önkoşullar

[Hızlı başlangıç: Azure Portal kullanarak ARM şablonları oluşturun ve dağıtın](https://docs.microsoft.com/azure/azure-resource-manager/templates/quickstart-create-templates-use-the-portal).

## <a name="limitations"></a>Sınırlamalar

- Media Services için, şablon yalnızca anahtar teslimi için özel bağlantı kurar.
- VM için bir ağ güvenlik grubu oluşturulmaz.
- Depolama hesabı veya anahtar teslimi için ağ erişim denetimi yapılandırılmadı.

Şablon şunları oluşturur:

- Media Services hesabı ve depolama hesabı (normal olarak)
- Alt ağı olan VNet
- Media Services hesabı ve depolama hesabı için:
  - Özel Uç Noktalar
  - Özel DNS bölgeler
  - Bağlantılar arasındaki bağlantılar (özel DNS bölgelerini VNet 'e bağlamak için)
  - Bölge grupları Özel DNS (özel DNS bölgelerinde DNS kayıtlarının otomatik olarak oluşturulmasını tetiklemek için)
- Bir VM (ilişkili genel IP adresi ve ağ arabirimi ile)

## <a name="azure-resource-manager-arm-template-for-private-link"></a>Özel bağlantı için Azure Resource Manager (ARM) şablonu

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "vmAdminUsername": {
      "type": "string"
    },
    "vmAdminPassword": {
      "type": "secureString"
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D2_v3"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "storageAccountName": {
      "type": "string"
    },
    "mediaServicesAccountName": {
      "type": "string"
    }
  },
  "functions": [],
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-01-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "StorageV2"
    },
    {
      "type": "Microsoft.Media/mediaservices",
      "apiVersion": "2020-05-01",
      "name": "[parameters('mediaServicesAccountName')]",
      "location": "[parameters('location')]",
      "properties": {
        "storageAccounts": [
          {
            "type": "Primary",
            "id": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]"
          }
        ]
      },
      "identity": {
        "type": "SystemAssigned"
      },
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2020-08-01",
      "name": "myVnet",
      "location": "[parameters('location')]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "10.0.0.0/16"
          ]
        },
        "subnets": [
          {
            "name": "mySubnet",
            "properties": {
              "addressPrefix": "10.0.0.0/24",
              "privateEndpointNetworkPolicies": "Disabled"
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.Network/privateEndpoints",
      "apiVersion": "2020-08-01",
      "name": "storagePrivateEndpoint",
      "location": "[parameters('location')]",
      "properties": {
        "subnet": {
          "id": "[reference(resourceId('Microsoft.Network/virtualNetworks', 'myVnet')).subnets[0].id]"
        },
        "privateLinkServiceConnections": [
          {
            "name": "storagePrivateEndpointConnection",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
              "groupIds": [
                "blob"
              ]
            }
          }
        ]
      },
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
      ]
    },
    {
      "type": "Microsoft.Network/privateDnsZones",
      "apiVersion": "2020-06-01",
      "name": "privatelink.blob.core.windows.net",
      "location": "global"
    },
    {
      "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
      "apiVersion": "2020-06-01",
      "name": "[format('{0}/storageDnsZoneLink', 'privatelink.blob.core.windows.net')]",
      "location": "global",
      "properties": {
        "registrationEnabled": false,
        "virtualNetwork": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateDnsZones', 'privatelink.blob.core.windows.net')]",
        "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
      ]
    },
    {
      "type": "Microsoft.Network/privateEndpoints/privateDnsZoneGroups",
      "apiVersion": "2020-08-01",
      "name": "[format('{0}/storagePrivateDnsZoneGroup', 'storagePrivateEndpoint')]",
      "properties": {
        "privateDnsZoneConfigs": [
          {
            "name": "config1",
            "properties": {
              "privateDnsZoneId": "[resourceId('Microsoft.Network/privateDnsZones', 'privatelink.blob.core.windows.net')]"
            }
          }
        ]
      },
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateDnsZones', 'privatelink.blob.core.windows.net')]",
        "[resourceId('Microsoft.Network/privateEndpoints', 'storagePrivateEndpoint')]"
      ]
    },
    {
      "type": "Microsoft.Network/privateEndpoints",
      "apiVersion": "2020-08-01",
      "name": "mediaServicesPrivateEndpoint",
      "location": "[parameters('location')]",
      "properties": {
        "subnet": {
          "id": "[reference(resourceId('Microsoft.Network/virtualNetworks', 'myVnet')).subnets[0].id]"
        },
        "privateLinkServiceConnections": [
          {
            "name": "mediaServicesPrivateEndpointConnection",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.Media/mediaservices', parameters('mediaServicesAccountName'))]",
              "groupIds": [
                "keydelivery"
              ]
            }
          }
        ]
      },
      "dependsOn": [
        "[resourceId('Microsoft.Media/mediaservices', parameters('mediaServicesAccountName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
      ]
    },
    {
      "type": "Microsoft.Network/privateDnsZones",
      "apiVersion": "2020-06-01",
      "name": "privatelink.media.azure.net",
      "location": "global"
    },
    {
      "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
      "apiVersion": "2020-06-01",
      "name": "[format('{0}/mediaServicesDnsZoneLink', 'privatelink.media.azure.net')]",
      "location": "global",
      "properties": {
        "registrationEnabled": false,
        "virtualNetwork": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateDnsZones', 'privatelink.media.azure.net')]",
        "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
      ]
    },
    {
      "type": "Microsoft.Network/privateEndpoints/privateDnsZoneGroups",
      "apiVersion": "2020-08-01",
      "name": "[format('{0}/mediaServicesPrivateDnsZoneGroup', 'mediaServicesPrivateEndpoint')]",
      "properties": {
        "privateDnsZoneConfigs": [
          {
            "name": "config1",
            "properties": {
              "privateDnsZoneId": "[resourceId('Microsoft.Network/privateDnsZones', 'privatelink.media.azure.net')]"
            }
          }
        ]
      },
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateDnsZones', 'privatelink.media.azure.net')]",
        "[resourceId('Microsoft.Network/privateEndpoints', 'mediaServicesPrivateEndpoint')]"
      ]
    },
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2020-08-01",
      "name": "publicIp",
      "location": "[parameters('location')]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[toLower(parameters('vmName'))]"
        }
      }
    },
    {
      "type": "Microsoft.Network/networkInterfaces",
      "apiVersion": "2020-08-01",
      "name": "vmNetworkInterface",
      "location": "[parameters('location')]",
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipConfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'publicIp')]"
              },
              "subnet": {
                "id": "[reference(resourceId('Microsoft.Network/virtualNetworks', 'myVnet')).subnets[0].id]"
              }
            }
          }
        ]
      },
      "dependsOn": [
        "[resourceId('Microsoft.Network/publicIPAddresses', 'publicIp')]",
        "[resourceId('Microsoft.Network/virtualNetworks', 'myVnet')]"
      ]
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2020-12-01",
      "name": "myVM",
      "location": "[parameters('location')]",
      "properties": {
        "hardwareProfile": {
          "vmSize": "[parameters('vmSize')]"
        },
        "osProfile": {
          "computerName": "[parameters('vmName')]",
          "adminUsername": "[parameters('vmAdminUsername')]",
          "adminPassword": "[parameters('vmAdminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2019-Datacenter",
            "version": "latest"
          },
          "osDisk": {
            "name": "osDisk",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "managedDisk": {
              "storageAccountType": "Standard_LRS"
            },
            "diskSizeGB": 128
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', 'vmNetworkInterface')]"
            }
          ]
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.Network/networkInterfaces', 'vmNetworkInterface')]"
      ]
    }
  ],
  "metadata": {
    "_generator": {
      "name": "bicep",
      "version": "0.3.126.58533",
      "templateHash": "2006367938138350540"
    }
  }
}
```
