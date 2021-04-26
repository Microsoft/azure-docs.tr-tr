---
title: Azure Virtual Network-Resource Manager şablonunda IPv6 ikili yığın uygulaması dağıtma
titlesuffix: Azure Virtual Network
description: Bu makalede, Azure Resource Manager VM şablonlarını kullanarak Azure sanal ağı 'nda Standart Load Balancer IPv6 ikili yığın uygulamasının nasıl dağıtılacağı gösterilmektedir.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: f98ffde9ce9b5c564e9f9c6c0b95e11f76719597
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95244353"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---template"></a>Azure sanal ağ 'da IPv6 ikili yığın uygulaması dağıtma-şablon

Bu makale, için geçerli Azure Resource Manager VM şablonunun parçası olan IPv6 yapılandırma görevlerinin bir listesini sağlar. IPv4 ve IPv6 alt ağları içeren çift yığın sanal ağını, Çift (IPv4 + IPv6) ön uç yapılandırmalarına sahip bir Standart Load Balancer, çift IP yapılandırması, ağ güvenlik grubu ve genel IP 'Ler olan NIC 'Ler içeren VM 'Ler içeren bir çift yığın (IPv4 + IPv6) Standart Load Balancer uygulaması dağıtmak için bu makalede açıklanan şablonu kullanın. 

## <a name="required-configurations"></a>Gerekli yapılandırma

Şablonun nerede gerçekleşeceğini görmek için şablonda şablon bölümlerini arayın.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>Sanal ağ için IPv6 addressSpace

Eklenecek şablon bölümü:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>IPv6 sanal ağ adresi alanı içindeki IPv6 alt ağı

Eklenecek şablon bölümü:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>NIC için IPv6 yapılandırması

Eklenecek şablon bölümü:
```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

### <a name="ipv6-network-security-group-nsg-rules"></a>IPv6 ağ güvenlik grubu (NSG) kuralları

```JSON
          {
            "name": "default-allow-rdp",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "33819-33829",
              "destinationPortRange": "5000-6000",
              "sourceAddressPrefix": "fd00:db8:deca:deed::/64",
              "destinationAddressPrefix": "fd00:db8:deca:deed::/64",
              "access": "Allow",
              "priority": 1003,
              "direction": "Inbound"
            }
```

## <a name="conditional-configuration"></a>Koşullu yapılandırma

Bir ağ sanal gereci kullanıyorsanız, yönlendirme tablosuna IPv6 yolları ekleyin. Aksi takdirde, bu yapılandırma isteğe bağlıdır.

```JSON
    {
      "type": "Microsoft.Network/routeTables",
      "name": "v6route",
      "apiVersion": "[variables('ApiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "routes": [
          {
            "name": "v6route",
            "properties": {
              "addressPrefix": "fd00:db8:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "fd00:db8:ace:f00d::1"
            }
```

## <a name="optional-configuration"></a>İsteğe bağlı yapılandırma

### <a name="ipv6-internet-access-for-the-virtual-network"></a>Sanal ağ için IPv6 Internet erişimi

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>IPv6 genel IP adresleri

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>Load Balancer için IPv6 ön ucu

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>Load Balancer için IPv6 arka uç adres havuzu

```JSON
              "backendAddressPool": {
                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', 'loadBalancer'), '/backendAddressPools/LBBAP-v6')]"
              },
              "protocol": "Tcp",
              "frontendPort": 8080,
              "backendPort": 8080
            },
            "name": "lbrule-v6"
```

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>Gelen ve giden bağlantı noktalarını ilişkilendireceğiniz IPv6 yük dengeleyici kuralları

```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

## <a name="sample-vm-template-json"></a>Örnek VM şablonu JSON
Azure sanal ağı 'nda Azure Resource Manager şablonu kullanarak bir IPv6 çift yığın uygulaması dağıtmak için, [burada](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-stdlb/)örnek şablonu görüntüleyin.

## <a name="next-steps"></a>Sonraki adımlar

[Genel IP adresleri](https://azure.microsoft.com/pricing/details/ip-addresses/), [ağ bant genişliği](https://azure.microsoft.com/pricing/details/bandwidth/)veya [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/)fiyatlandırmasıyla ilgili ayrıntıları bulabilirsiniz.
