---
title: Azure Virtual Network-Resource Manager şablonunda (Önizleme) temel Load Balancer IPv6 ikili yığın uygulaması dağıtma
titlesuffix: Azure Virtual Network
description: Bu makalede, Azure sanal ağ 'da Azure Resource Manager VM şablonları kullanılarak IPv6 ikili yığın uygulamasının nasıl dağıtılacağı gösterilmektedir.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/26/2019
ms.author: kumud
ms.openlocfilehash: b397c874045a89f5992aeadacfbbd4434a486977
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012831"
---
# <a name="deploy-an-ipv6-dual-stack-application-with-basic-load-balancer-in-azure---template-preview"></a>Azure 'da temel Load Balancer bir IPv6 ikili yığın uygulaması dağıtma-şablon (Önizleme)

Bu makale, için geçerli Azure Resource Manager VM şablonunun parçası olan IPv6 yapılandırma görevlerinin bir listesini sağlar. IPv4 ve IPv6 alt ağları içeren çift yığın sanal ağını içeren temel Load Balancer bir çift yığın (IPv4 + IPv6) uygulaması dağıtmak için bu makalede açıklanan şablonu kullanın, Çift (IPv4 + IPv6) ön uç yapılandırmalarına sahip temel bir Load Balancer, ile sanal makinelere Çift IP yapılandırmasına, ağ güvenlik grubuna ve genel IP 'Lere sahip NIC 'Ler.

Standart Load Balancer kullanarak bir çift yığın (ıPV4 + IPv6) uygulaması dağıtmak için, bkz. [Standart Load Balancer şablonuyla IPv6 ikili yığın uygulaması dağıtma](ipv6-configure-standard-load-balancer-template-json.md).

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
              "sourceAddressPrefix": "ace:cab:deca:deed::/64",
              "destinationAddressPrefix": "cab:ace:deca:deed::/64",
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
              "addressPrefix": "ace:cab:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "deca:cab:ace:f00d::1"
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
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
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
Azure Resource Manager şablonu kullanarak Azure sanal ağ 'da temel Load Balancer bir IPv6 çift yığın uygulaması dağıtmak için, [burada](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/)örnek şablonu görüntüleyin.

## <a name="next-steps"></a>Sonraki adımlar

[Genel IP adresleri](https://azure.microsoft.com/pricing/details/ip-addresses/), [ağ bant genişliği](https://azure.microsoft.com/pricing/details/bandwidth/)veya [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/)fiyatlandırmasıyla ilgili ayrıntıları bulabilirsiniz.
