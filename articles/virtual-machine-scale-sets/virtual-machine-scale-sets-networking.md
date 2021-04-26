---
title: Azure sanal makine ölçek kümeleri için ağ hizmeti
description: Azure sanal makine ölçek kümeleri için daha gelişmiş ağ özelliklerinden bazılarını yapılandırma.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: networking
ms.date: 06/25/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: e427d51068115db27a36243d738c0e93a10d3cb1
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375925"
---
# <a name="networking-for-azure-virtual-machine-scale-sets"></a>Azure sanal makine ölçek kümeleri için ağ hizmeti

Portal aracılığıyla Azure sanal makine ölçek kümesinin dağıtımını yaptığınızda gelen NAT kurallarıyla Azure Load Balancer gibi bazı ağ özellikleri varsayılan olarak gelir. Bu makalede, ölçek kümeleriyle yapılandırabileceğiniz daha gelişmiş ağ özelliklerinden bazılarının nasıl kullanıldığı açıklanır.

Bu makalede ele alınan özelliklerin tümünü Azure Resource Manager şablonlarını kullanarak yapılandırabilirsiniz. Belirli özellikler için Azure CLI ve PowerShell örnekleri de eklenmiştir.

## <a name="accelerated-networking"></a>Hızlandırılmış Ağ
Azure Hızlandırılmış Ağ, sanal makineye tek kökte G/Ç sanallaştırmasına (SR-IV) olanak tanıyarak ağ performansını geliştirir. Hızlandırılmış ağ hakkında daha fazla bilgi edinmek için [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) veya [Linux](../virtual-network/create-vm-accelerated-networking-cli.md) sanal makineler için Hızlandırılmış ağ sayfalarını inceleyin. Ölçek kümeleriyle hızlandırılmış ağ kullanmak için, ölçek kümenizin networkInterfaceConfigurations ayarlarında enableAcceleratedNetworking değerini **true** olarak ayarlayın. Örnek:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
      "name": "niconfig1",
      "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
          ...
        ]
      }
    }
   ]
}
```

## <a name="azure-virtual-machine-scale-sets-with-azure-load-balancer"></a>Azure Load Balancer ile Azure sanal makine ölçek kümeleri
Senaryonuza bağlı olarak sanal makine ölçek kümeleri ile Standart Load Balancer yapılandırma hakkında daha fazla bilgi edinmek için bkz. [Azure Load Balancer ve sanal makine ölçek kümeleri](../load-balancer/load-balancer-standard-virtual-machine-scale-sets.md) .

## <a name="create-a-scale-set-that-references-an-application-gateway"></a>Bir Application Gateway’e başvuran bir ölçek kümesi oluşturma
Bir uygulama ağ geçidi kullanan bir ölçek kümesi oluşturmak için, bu ARM şablonu yapılandırmasında olduğu gibi ölçek kümenizin ipConfigurations bölümündeki uygulama ağ geçidinin arka uç adres havuzuna başvurun:

```json
"ipConfigurations": [{
  "name": "{config-name}",
  "properties": {
  "subnet": {
    "id": "{subnet-id}"
  },
  "ApplicationGatewayBackendAddressPools": [{
    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/applicationGateways/{gateway-name}/backendAddressPools/{pool-name}"
  }]
}]
```

>[!NOTE]
> Uygulama ağ geçidinin ölçek kümesi ile aynı sanal ağda ancak ölçek kümesinden farklı bir alt ağda olması gerektiğine dikkat edin.


## <a name="configurable-dns-settings"></a>Yapılandırılabilir DNS Ayarları
Varsayılan olarak, ölçek kümeleri içinde oluşturuldukları VNET ve alt ağın belirli DNS ayarlarını alır. Bununla birlikte, ölçek kümesi için DNS ayarlarını doğrudan oluşturmanız da mümkündür.

### <a name="creating-a-scale-set-with-configurable-dns-servers"></a>Yapılandırılabilir DNS sunucularıyla ölçek kümesi oluşturma
Azure CLI kullanarak özel DNS yapılandırmasıyla bir ölçek kümesi oluşturmak için, **vmss create** komutuna sunucu ip adreslerini ayıran boşluktan sonra **--dns-servers** bağımsız değişkenini ekleyin. Örnek:

```bash
--dns-servers 10.0.0.6 10.0.0.5
```

Azure şablonunda özel DNS sunucularını yapılandırmak için, ölçek kümesinin networkInterfaceConfigurations bölümüne bir dnsSettings özelliği ekleyin. Örnek:

```json
"dnsSettings":{
    "dnsServers":["10.0.0.6", "10.0.0.5"]
}
```

### <a name="creating-a-scale-set-with-configurable-virtual-machine-domain-names"></a>Yapılandırılabilir sanal makine etki alanı adlarıyla ölçek kümesi oluşturma
CLI kullanarak sanal makineler için özel bir DNS adıyla bir ölçek kümesi oluşturmak için, **virtual machine scale set create** komutuna etki alanı adını temsil eden dizeden sonra **--vm-domain-name** bağımsız değişkenini ekleyin.

Bir Azure şablonunda etki alanı adını ayarlamak için, ölçek kümesi **Networkınterfaceconfigurations** bölümüne bir **dnssettings** özelliği ekleyin. Örnek:

```json
"networkProfile": {
  "networkInterfaceConfigurations": [
    {
    "name": "nic1",
    "properties": {
      "primary": true,
      "ipConfigurations": [
      {
        "name": "ip1",
        "properties": {
          "subnet": {
            "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
          },
          "publicIPAddressconfiguration": {
            "name": "publicip",
            "properties": {
            "idleTimeoutInMinutes": 10,
              "dnsSettings": {
                "domainNameLabel": "[parameters('vmssDnsName')]"
              }
            }
          }
        }
      }
    ]
    }
}
```

Tek bir sanal makine dns adı için çıkış şu biçimde olabilir:

```output
<vm><vmindex>.<specifiedVmssDomainNameLabel>
```

## <a name="public-ipv4-per-virtual-machine"></a>Sanal makine başına genel IPv4
Genel olarak, Azure ölçek kümesi sanal makinelerinin kendi genel IP adresleri olması gerekmez. Çoğu senaryoda, genel bir IP adresini bir yük dengeleyiciye veya tek bir sanal makineye (sıçrama kutusu olarak da bilinir) ilişkilendirmek, daha sonra gelen bağlantıları ölçek kümesi sanal makinelerine (örneğin, gelen NAT kuralları aracılığıyla) yönlendirir.

Öte yandan, bazı senaryolarda ölçek kümesi sanal makinelerinin kendi genel IP adresleri olması gerekir. Buna örnek olarak, konsolun oyunun fizik işlemlerini yapan bir bulut sanal makinesine doğrudan bağlanmasını gerektiren oyunları verebiliriz. Bir diğer örnek de, dağıtılmış bir veritabanında sanal makinelerin birbiriyle dış bağlantılar kurması gerektiği durumlardır.

### <a name="creating-a-scale-set-with-public-ip-per-virtual-machine"></a>Sanal makine başına bir genel IP ile ölçek kümesi oluşturma
CLI ile her sanal makineye bir genel IP adresi atayan bir ölçek kümesi oluşturmak için, **vmss create** komutuna **--public-ip-per-vm** parametresini ekleyin. 

Azure şablonu kullanarak bir ölçek kümesi oluşturmak için, Microsoft. COMPUTE/virtualMachineScaleSets kaynağının API sürümünün en az **2017-03-30** olduğundan emin olun ve ölçek kümesi ipconfigurations bölümüne **Publicıpaddressconfiguration** JSON özelliği ekleyin. Örnek:

```json
"publicIpAddressConfiguration": {
    "name": "pub1",
    "properties": {
      "idleTimeoutInMinutes": 15
    }
}
```

Örnek şablon: [201-vmss-public-ip-linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-public-ip-linux)

### <a name="querying-the-public-ip-addresses-of-the-virtual-machines-in-a-scale-set"></a>Ölçek kümesinde sanal makinelerin genel IP adreslerini sorgulama
CLI kullanarak ölçek kümesi sanal makinelerine atanmış genel IP adreslerini listelemek için, **az vmss list-instance-public-ips** komutunu kullanın.

PowerShell kullanarak ölçek kümesi genel IP adreslerini listelemek için _Get-Azpublicıpaddress_ komutunu kullanın. Örnek:

```powershell
Get-AzPublicIpAddress -ResourceGroupName myrg -VirtualMachineScaleSetName myvmss
```

Doğrudan genel IP adresi yapılandırmasının kaynak kimliğine başvuruda bulunarak da genel IP adreslerini sorgulayabilirsiniz. Örnek:

```powershell
Get-AzPublicIpAddress -ResourceGroupName myrg -Name myvmsspip
```

Ayrıca, ölçek kümesi sanal makinelerine atanan genel IP adreslerini, [Azure Kaynak Gezgini](https://resources.azure.com)'ni ve Azure REST API'nin **2017-03-30** veya üstü sürümlerini sorgulayarak da görüntüleyebilirsiniz.

[Azure Kaynak Gezgini](https://resources.azure.com)'ni sorgulamak için:

1. Bir web tarayıcısında [Azure Kaynak Gezgini](https://resources.azure.com)'ni açın.
1. Sol taraftaki *abonelikler*'i, yanındaki *+* işaretine tıklayarak genişletin. *Abonelikler* altında yalnızca bir öğeniz varsa zaten genişletilmiştir.
1. Aboneliğinizi genişletin.
1. Kaynak grubunuzu genişletin.
1. *Sağlayıcılar*'ı genişletin.
1. *Microsoft. COMPUTE*' ı genişletin.
1. *virtualMachineScaleSets*'i genişletin.
1. Ölçek kümenizi genişletin.
1. *publicipaddresses* üzerine tıklayın.

Azure REST API'sine sorgulamak için:

```bash
GET https://management.azure.com/subscriptions/{your sub ID}/resourceGroups/{RG name}/providers/Microsoft.Compute/virtualMachineScaleSets/{scale set name}/publicipaddresses?api-version=2017-03-30
```

[Azure Kaynak Gezgini](https://resources.azure.com) ve Azure REST API'den örnek çıktı:

```json
{
  "value": [
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/pipvmss/virtualMachines/0/networkInterfaces/pipvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"a64060d5-4dea-4379-a11d-b23cd49a3c8d\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "ee8cb20f-af8e-4cd6-892f-441ae2bf701f",
        "ipAddress": "13.84.190.11",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/0/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    },
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"5f6ff30c-a24c-4818-883c-61ebd5f9eee8\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "036ce266-403f-41bd-8578-d446d7397c2f",
        "ipAddress": "13.84.159.176",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    }
```

## <a name="multiple-ip-addresses-per-nic"></a>NIC başına birden çok IP adresi
Ölçek kümesinde bir sanal makineye bağlanan her NIC ile ilişkilendirilmiş bir veya birden çok IP yapılandırması olabilir. Her yapılandırma bir özel IP adresine atanır. Her yapılandırmayla ilişkilendirilmiş bir genel IP adresi kaynağı da olabilir. NIC’ye kaç IP adresi atanabileceğini ve Azure aboneliğinde kaç genel IP adresi kullanabileceğinizi anlamak için, [Azure sınırlarına](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) bakın.

## <a name="multiple-nics-per-virtual-machine"></a>Sanal makine başına birden çok NIC
Sanal makinenin boyutuna bağlı olarak, sanal makine başına en çok 8 NIC’niz olabilir. Makine başına NIC sayısı üst sınırı, [Sanal makine boyutu makalesinde](../virtual-machines/sizes.md) verilmiştir. Bir VM örneğine bağlı tüm NIC'ler aynı sanal ağa bağlanmalıdır. NIC'ler farklı alt ağlara bağlanabilir, ancak tüm alt ağların aynı sanal ağ kapsamında olması gerekir.

Aşağıdaki örnek, sanal makine başına birden çok NIC girdisi ve birden çok genel IP gösteren bir ölçek kümesi ağ profilidir:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
        "name": "nic1",
        "properties": {
            "primary": true,
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        },
        {
        "name": "nic2",
        "properties": {
            "primary": false,
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        }
    ]
}
```

## <a name="nsg--asgs-per-scale-set"></a>Ölçek kümesi başına NSG ve ASG'ler
[Ağ Güvenlik Grupları](../virtual-network/network-security-groups-overview.md), bir Azure sanal ağındaki Azure kaynaklarının gelen ve giden trafiğini güvenlik kurallarıyla filtrelemenize izin verir. [Uygulama Güvenlik Grupları](../virtual-network/network-security-groups-overview.md#application-security-groups), Azure kaynaklarınızın ağ güvenliğini işlemenizi ve bunları uygulamanızın yapısının bir uzantısı olarak gruplamanızı sağlar.

Ağ Güvenlik Grupları, ölçek kümesi sanal makine özelliklerinin ağ arabirimi yapılandırması bölümüne bir başvuru eklemek yoluyla doğrudan ölçek kümesine uygulanabilir.

Uygulama Güvenlik Grupları da ölçek kümesi sanal makine özelliklerinin ağ arabirimi IP yapılandırmaları bölümüne bir başvuru eklemek yoluyla doğrudan ölçek kümesine uygulanabilir.

Örnek:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": true,
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            },
                            "applicationSecurityGroups": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/applicationSecurityGroups/', variables('asgName'))]"
                                }
                            ],
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

Ağ Güvenlik Grubunuzun ölçek kümeniz ile ilişkilendirildiğini doğrulamak için `az vmss show` komutunu kullanın. Aşağıdaki örnekte sonuçları filtrelemek ve çıktının yalnızca ilgili bölümünü göstermek için `--query` kullanılmaktadır.

```azurecli
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].networkSecurityGroup

[
  {
    "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/nsgName",
    "resourceGroup": "myResourceGroup"
  }
]
```

Uygulama Güvenlik Grubunuzun ölçek kümeniz ile ilişkilendirildiğini doğrulamak için `az vmss show` komutunu kullanın. Aşağıdaki örnekte sonuçları filtrelemek ve çıktının yalnızca ilgili bölümünü göstermek için `--query` kullanılmaktadır.

```azurecli
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].ipConfigurations[].applicationSecurityGroups

[
  [
    {
      "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationSecurityGroups/asgName",
      "resourceGroup": "myResourceGroup"
    }
  ]
]
```

## <a name="make-networking-updates-to-specific-instances"></a>Belirli örneklere ağ güncelleştirmeleri yapma

Belirli sanal makine ölçek kümesi örneklerine ağ güncelleştirmeleri yapabilirsiniz. 

`PUT`Ağ yapılandırmasını güncelleştirmek için örneğe karşı izleyebilirsiniz. Bu, ağ arabirimi kartları (NIC 'ler) ekleme veya kaldırma gibi işlemleri yapmak veya bir arka uç havuzundan bir örneği kaldırmak için kullanılabilir.

```
PUT https://management.azure.com/subscriptions/.../resourceGroups/vmssnic/providers/Microsoft.Compute/virtualMachineScaleSets/vmssnic/virtualMachines/1/?api-version=2019-07-01
```

Aşağıdaki örnek, NIC 'nize ikinci bir IP yapılandırmasının nasıl ekleneceğini gösterir.

1. `GET` belirli bir sanal makine ölçek kümesi örneği için ayrıntılar.
    
    ``` 
    GET https://management.azure.com/subscriptions/.../resourceGroups/vmssnic/providers/Microsoft.Compute/virtualMachineScaleSets/vmssnic/virtualMachines/1/?api-version=2019-07-01
    ```

    *Aşağıda bu örnek için yalnızca ağ parametrelerini göstermek üzere basitleştirilmiştir.*

    ```json
    {
      ...
      "properties": {
        ...
        "networkProfileConfiguration": {
          "networkInterfaceConfigurations": [
            {
              "name": "vmssnic-vnet-nic01",
              "properties": {
                "primary": true,
                "enableAcceleratedNetworking": false,
                "networkSecurityGroup": {
                  "id": "/subscriptions/123a1a12-a123-1ab1-12a1-12a1a1234ab1/resourceGroups/vmssnic/providers/Microsoft.Network/networkSecurityGroups/basicNsgvmssnic-vnet-nic01"
                },
                "dnsSettings": {
                  "dnsServers": []
                },
                "enableIPForwarding": false,
                "ipConfigurations": [
                  {
                    "name": "vmssnic-vnet-nic01-defaultIpConfiguration",
                    "properties": {
                      "publicIPAddressConfiguration": {
                        "name": "publicIp-vmssnic-vnet-nic01",
                        "properties": {
                          "idleTimeoutInMinutes": 15,
                          "ipTags": [],
                          "publicIPAddressVersion": "IPv4"
                        }
                      },
                      "primary": true,
                      "subnet": {
                        "id": "/subscriptions/123a1a12-a123-1ab1-12a1-12a1a1234ab1/resourceGroups/vmssnic/providers/Microsoft.Network/virtualNetworks/vmssnic-vnet/subnets/default"
                      },
                      "privateIPAddressVersion": "IPv4"
                    }
                  }
                ]
              }
            }
          ]
        },
        ...
      }
    }
    ```
 
2. `PUT` örnek olarak, ek IP yapılandırması eklemek için güncelleştiriliyor. Bu, ek ekleme için benzerdir `networkInterfaceConfiguration` .

    
    ```
    PUT https://management.azure.com/subscriptions/.../resourceGroups/vmssnic/providers/Microsoft.Compute/virtualMachineScaleSets/vmssnic/virtualMachines/1/?api-version=2019-07-01
    ```

    *Aşağıda bu örnek için yalnızca ağ parametrelerini göstermek üzere basitleştirilmiştir.*

    ```json
      {
      ...
      "properties": {
        ...
        "networkProfileConfiguration": {
          "networkInterfaceConfigurations": [
            {
              "name": "vmssnic-vnet-nic01",
              "properties": {
                "primary": true,
                "enableAcceleratedNetworking": false,
                "networkSecurityGroup": {
                  "id": "/subscriptions/123a1a12-a123-1ab1-12a1-12a1a1234ab1/resourceGroups/vmssnic/providers/Microsoft.Network/networkSecurityGroups/basicNsgvmssnic-vnet-nic01"
                },
                "dnsSettings": {
                  "dnsServers": []
                },
                "enableIPForwarding": false,
                "ipConfigurations": [
                  {
                    "name": "vmssnic-vnet-nic01-defaultIpConfiguration",
                    "properties": {
                      "publicIPAddressConfiguration": {
                        "name": "publicIp-vmssnic-vnet-nic01",
                        "properties": {
                          "idleTimeoutInMinutes": 15,
                          "ipTags": [],
                          "publicIPAddressVersion": "IPv4"
                        }
                      },
                      "primary": true,
                      "subnet": {
                        "id": "/subscriptions/123a1a12-a123-1ab1-12a1-12a1a1234ab1/resourceGroups/vmssnic/providers/Microsoft.Network/virtualNetworks/vmssnic-vnet/subnets/default"
                      },
                      "privateIPAddressVersion": "IPv4"
                    }
                  },
                  {
                    "name": "my-second-config",
                    "properties": {
                      "subnet": {
                        "id": "/subscriptions/123a1a12-a123-1ab1-12a1-12a1a1234ab1/resourceGroups/vmssnic/providers/Microsoft.Network/virtualNetworks/vmssnic-vnet/subnets/default"
                      },
                      "privateIPAddressVersion": "IPv4"
                    }
                  }
                ]
              }
            }
          ]
        },
        ...
      }
    }
    ```



## <a name="next-steps"></a>Sonraki adımlar
Azure sanal ağları hakkında daha fazla bilgi için bkz. [Azure sanal ağlarına genel bakış](../virtual-network/virtual-networks-overview.md).
