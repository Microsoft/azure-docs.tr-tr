---
title: Azure'daki bir Service Fabric kümesini ölçeklendirme | Microsoft Docs
description: Bu öğreticide, Azure 'da bir Service Fabric kümesini ölçeklendirmeyi öğreneceksiniz.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 6270237e2319c42ed30fc347b7ab9c1c2a008314
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177737"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>Öğretici: Azure'daki bir Service Fabric kümesini ölçeklendirme

Bu öğretici, bir serinin üçüncü bölümüdür ve mevcut kümenizi nasıl ölçeklendirebilirsiniz ve içinde nasıl ölçeklenebilmeniz gerektiğini gösterir. Tamamladığınızda, kümenizin nasıl ölçekleneceğini ve kalan kaynakların nasıl temizleneceğini öğrenmiş olacaksınız.  Azure 'da çalışan bir kümeyi ölçeklendirme hakkında daha fazla bilgi için [ölçek Service Fabric kümelerini](service-fabric-cluster-scaling.md)okuyun.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Düğüm ekleme ve kaldırma (ölçeği genişletme ve ölçekleme)
> * Düğüm türleri ekleme ve kaldırma (ölçeği genişletme ve ölçekleme)
> * Düğüm kaynaklarını artırma (ölçeği artırma)

Bu öğretici dizisinde şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> * Şablon kullanarak Azure 'da güvenli bir [Windows kümesi](service-fabric-tutorial-create-vnet-and-windows-cluster.md) oluşturma
> * [Bir kümeyi izleme](service-fabric-tutorial-monitor-cluster.md)
> * Bir kümenin ölçeğini daraltma veya genişletme
> * [Bir kümenin çalışma zamanını yükseltme](service-fabric-tutorial-upgrade-cluster.md)
> * [Küme silme](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce:

* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) veya [Azure CLI](/cli/azure/install-azure-cli)'yı yükler.
* Azure 'da güvenli bir [Windows kümesi](service-fabric-tutorial-create-vnet-and-windows-cluster.md) oluşturma

## <a name="important-considerations-and-guidelines"></a>Önemli konular ve yönergeler

Uygulama iş yükleri zaman içinde değişiklik yaparken, mevcut hizmetlerinizin daha fazla (veya daha az) kaynağa ihtiyacı var mı?  Küme kaynaklarını artırmak veya azaltmak için düğüm türünden [düğüm ekleyin veya kaldırın](#add-nodes-to-or-remove-nodes-from-a-node-type) .

Kümenize 100 'den fazla düğüm eklemeniz gerekiyor mu?  Tek bir Service Fabric düğüm türü/ölçek kümesi 100 taneden fazla düğüm/VM içeremez.  Bir kümeyi 100 düğümden daha fazla ölçeklendirmek için [ek düğüm türleri ekleyin](#add-nodes-to-or-remove-nodes-from-a-node-type).

Uygulamanızda birden fazla hizmet var mı ve bunların herkese açık veya internet 'e yönelik olması gerekiyor mu?  Tipik uygulamalar, bir istemciden giriş ve ön uç hizmetleriyle iletişim kuran bir veya daha fazla arka uç hizmeti içeren bir ön uç ağ geçidi hizmeti içerir. Bu durumda, kümeye [en az iki düğüm türü eklemenizi](#add-nodes-to-or-remove-nodes-from-a-node-type) öneririz.  

Hizmetleriniz, daha fazla RAM veya daha yüksek CPU döngüsü gibi farklı altyapı ihtiyaçlarına sahip mi? Örneğin, uygulamanız bir ön uç hizmeti ve arka uç hizmeti içerir. Ön uç hizmeti, internet 'e açık bağlantı noktaları olan daha küçük VM 'lerde (D2 gibi VM boyutları) çalıştırılabilir. Ancak arka uç hizmeti, hesaplama için yoğun ve internet 'e yönelik olmayan daha büyük VM 'lerde (D4, D6, D15 gibi VM boyutları ile) çalıştırılması gerekir. Bu durumda, kümenize [iki veya daha fazla düğüm türü eklemenizi](#add-nodes-to-or-remove-nodes-from-a-node-type) öneririz. Bu, her düğüm türünün internet bağlantısı veya VM boyutu gibi farklı özelliklere sahip olmasına olanak sağlar. VM 'lerin sayısı bağımsız olarak ölçeklendirilebilir.

Bir Azure kümesini ölçeklendirirken aşağıdaki yönergeleri göz önünde bulundurun:

* Tek bir Service Fabric düğüm türü/ölçek kümesi 100 taneden fazla düğüm/VM içeremez.  Bir kümeyi 100 düğümden daha fazla ölçeklendirmek için ek düğüm türleri ekleyin.
* Üretim iş yüklerini çalıştıran birincil düğüm türleri için bir [dayanıklılık düzeyi][durability] altın veya gümüş olmalıdır ve her zaman beş veya daha fazla düğüm olmalıdır.
* Durum bilgisi olmayan üretim iş yükleri çalıştıran birincil düğüm türleri her zaman beş veya daha fazla düğüme sahip olmalıdır.
* Durum bilgisi olmayan üretim iş yükleri çalıştıran birincil düğüm türleri her zaman iki veya daha fazla düğüme sahip olmalıdır.
* Altın veya gümüş herhangi bir düğüm [türü, her][durability] zaman beş veya daha fazla düğüme sahip olmalıdır.
* Ölçeklendirme (düğümleri öğesinden kaldırma) bir birincil düğüm türü ise, [güvenilirlik düzeyinin][reliability] gerektirdiği örnek sayısını asla azaltmalısınız.

Daha fazla bilgi için [küme kapasitesi Kılavuzu](service-fabric-cluster-capacity.md)' nu okuyun.

## <a name="export-the-template-for-the-resource-group"></a>Kaynak grubu için şablonu dışarı aktarma

Güvenli bir [Windows kümesi](service-fabric-tutorial-create-vnet-and-windows-cluster.md) oluşturup kaynak grubunuzu başarıyla ayarladıktan sonra, kaynak grubunun Kaynak Yöneticisi şablonunu dışarı aktarın. Şablonu dışarı aktarmak, tüm tüm altyapıyı içerdiğinden, kümenin ve kaynaklarının gelecekteki dağıtımlarını otomatikleştirmenizi sağlar.  Şablonları dışarı aktarma hakkında daha fazla bilgi için [Azure Portal kullanarak Azure Resource Manager kaynak gruplarını yönetme](/azure/azure-resource-manager/manage-resource-groups-portal)makalesini okuyun.

1. [Azure Portal](https://portal.azure.com), kümeyi içeren kaynak grubuna gidin (Bu öğreticiyi takip ediyorsanız**sfclustertutorialgroup**). 

2. Sol bölmede **dağıtımlar**' ı seçin veya **dağıtımlar**' ın altındaki bağlantıyı seçin. 

3. Listeden en son başarılı dağıtımı seçin.

4. Sol bölmede **şablon** ' u ve ardından **İndir** ' i seçerek şablonu ZIP dosyası olarak dışarı aktarın.  Şablonu ve parametreleri yerel bilgisayarınıza kaydedin.

## <a name="add-nodes-to-or-remove-nodes-from-a-node-type"></a>Düğüm türüne düğüm ekleme veya düğüm türünden düğüm kaldırma

Ölçek Genişletme ve genişletme veya yatay ölçeklendirme, kümedeki düğümlerin sayısını değiştirir. Ölçeği büyütme veya uzaklaştırma sırasında, ölçek kümesine daha fazla sanal makine örneği eklersiniz. Bu örnekler, Service Fabric tarafından kullanılan düğümler haline gelir. Service Fabric, ölçek kümesine ne zaman daha fazla örnek eklendiğini bilir (ölçek genişletilerek) ve otomatik olarak tepki verir. Küme üzerinde iş yükleri çalışırken bile kümeyi istediğiniz zaman ölçeklendirebilirsiniz.

### <a name="update-the-template"></a>Şablonu güncelleştirme

En son dağıtım için kaynak grubundan [bir şablon ve parametreler dosyası dışarı aktarın](#export-the-template-for-the-resource-group) .  *Parameters. JSON* dosyasını açın.  Bu öğreticide [örnek şablonu][template] kullanarak kümeyi dağıttıysanız, kümede üç düğüm türü vardır ve her düğüm türü için düğüm sayısını ayarlanmış üç parametre vardır: *nt0InstanceCount*, *nt1InstanceCount*ve  *nt2InstanceCount*.  Örneğin, *nt1InstanceCount* parametresi, ikinci düğüm türü için örnek sayısını ayarlar ve ilişkili sanal makine ölçek kümesindeki VM sayısını ayarlar.

Bu nedenle, *nt1InstanceCount* değerini güncelleştirerek ikinci düğüm türündeki düğümlerin sayısını değiştirirsiniz.  Bir düğüm türünü 100 ' dan fazla düğüme ölçeklendiremezsiniz.  Durum bilgisi olmayan üretim iş yükleri çalıştıran birincil düğüm türleri her zaman beş veya daha fazla düğüme sahip olmalıdır. Durum bilgisi olmayan üretim iş yükleri çalıştıran birincil düğüm türleri her zaman iki veya daha fazla düğüme sahip olmalıdır.

İçinde ölçeklendirme yapıyorsanız, düğüm kaldırma [dayanıklılığı düzeyi][durability] olan düğüm türü, [Bu düğümlerin durumunu el ile kaldırmanız](service-fabric-cluster-scale-up-down.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set)gerekir.  Gümüş ve altın dayanıklılık katmanı için, bu adımlar platform tarafından otomatik olarak yapılır.

### <a name="deploy-the-updated-template"></a>Güncelleştirilmiş şablonu dağıtma
*Template. JSON* ve *Parameters. JSON* dosyalarındaki değişiklikleri kaydedin.  Güncelleştirilmiş şablonu dağıtmak için aşağıdaki komutu çalıştırın:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ChangingInstanceCount"
```
Veya aşağıdaki Azure CLı komutu:
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="add-a-node-type-to-the-cluster"></a>Kümeye düğüm türü ekleme

Azure 'da çalışan bir Service Fabric kümesinde tanımlanan her düğüm türü [ayrı bir sanal makine ölçek kümesi](service-fabric-cluster-nodetypes.md)olarak ayarlanır. Her düğüm türü ayrıca yönetilebilir. Her bir düğüm türünü yukarı veya aşağı ölçeklendirebilir, farklı bağlantı noktası kümelerine sahip olabilir ve farklı kapasite ölçümleri kullanabilirsiniz. Ayrıca, her bir küme düğümünde çalışan işletim sistemi SKU 'sunu bağımsız olarak değiştirebilirsiniz, ancak örnek kümede çalışan Windows ve Linux 'un bir karışımına sahip olamayacağını unutmayın. Tek düğümlü bir tür/ölçek kümesi 100 taneden fazla düğüm içeremez.  Ek düğüm türleri/ölçek kümeleri ekleyerek bir kümeyi 100 düğümden daha fazlasına ölçeklendirebilirsiniz. Küme üzerinde iş yükleri çalışırken bile kümeyi istediğiniz zaman ölçeklendirebilirsiniz.

### <a name="update-the-template"></a>Şablonu güncelleştirme

En son dağıtım için kaynak grubundan [bir şablon ve parametreler dosyası dışarı aktarın](#export-the-template-for-the-resource-group) .  *Parameters. JSON* dosyasını açın.  Kümeyi Bu öğreticide [örnek şablonu][template] kullanarak dağıttıysanız, kümede üç düğüm türü vardır.  Bu bölümde bir Kaynak Yöneticisi şablonunu güncelleştirerek ve dağıtarak dördüncü düğüm türü eklersiniz. 

Yeni düğüm türüne ek olarak, ilişkili sanal makine ölçek kümesini (sanal ağın ayrı alt ağında çalışan) ve ağ güvenlik grubunu da eklersiniz.  Yeni ölçek kümesi için yeni veya var olan genel IP adresi ve Azure yük dengeleyici kaynakları eklemeyi seçebilirsiniz.  Yeni düğüm türünün bir [dayanıklılık düzeyi][durability] ve "Standard_D2_V2" boyutu vardır.

*Template. JSON* dosyasında aşağıdaki yeni parametreleri ekleyin:
```json
"nt3InstanceCount": {
    "defaultValue": 5,
    "type": "Int",
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType3Size": {
    "defaultValue": "Standard_D2_V2",
    "type": "String"
},
```

*Template. JSON* dosyasında aşağıdaki yeni değişkenleri ekleyin:
```json
"lbID3": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
"lbIPConfig3": "[concat(variables('lbID3'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
"lbPoolID3": "[concat(variables('lbID3'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
"lbProbeID3": "[concat(variables('lbID3'),'/probes/FabricGatewayProbe')]",
"lbHttpProbeID3": "[concat(variables('lbID3'),'/probes/FabricHttpGatewayProbe')]",
"lbNatPoolID3": "[concat(variables('lbID3'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
"vmNodeType3Name": "[toLower(concat('NT4', variables('vmName')))]",
"vmStorageAccountName3": "[toLower(concat(uniqueString(resourceGroup().id), '1', '3' ))]",
"nt3applicationStartPort": "20000",
"nt3applicationEndPort": "30000",
"nt3ephemeralStartPort": "49152",
"nt3ephemeralEndPort": "65534",
"nt3fabricTcpGatewayPort": "19000",
"nt3fabricHttpGatewayPort": "19080",
"nt3reverseProxyEndpointPort": "19081",
"subnet3Name": "Subnet-3",
"subnet3Prefix": "10.0.3.0/24",
"subnet3Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet3Name'))]",
```

*Template. JSON* dosyasında, sanal ağ kaynağına yeni bir alt ağ ekleyin:
```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[variables('addressPrefix')]"
            ]
        },
        "subnets": [
            ...
            {
                "name": "[variables('subnet3Name')]",
                "properties": {
                    "addressPrefix": "[variables('subnet3Prefix')]",
                    "networkSecurityGroup": {
                        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', variables('subnet3Name')))]"
                    }
                }
            }
        ]
    },
    "dependsOn": [
        ...
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', variables('subnet3Name')))]"
    ]
},
```

*Template. JSON* dosyasında, yenı genel IP adresi ve yük dengeleyici kaynakları ekleyin:
```json
{
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "dnsSettings": {
            "domainNameLabel": "[concat(variables('dnsName'),'-','nt4')]"
        },
        "publicIPAllocationMethod": "Dynamic"
    }
},
        {
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "frontendIPConfigurations": [
            {
                "name": "LoadBalancerIPConfig",
                "properties": {
                    "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
                    }
                }
            }
        ],
        "backendAddressPools": [
            {
                "name": "LoadBalancerBEAddressPool",
                "properties": {}
            }
        ],
        "loadBalancingRules": [
            {
                "name": "LBRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricTcpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "LBHttpRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[variables('nt3fabricHttpGatewayPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[variables('lbHttpProbeID3')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule1",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort1')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort1')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe1')]"
                    },
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortLBRule2",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID3')]"
                    },
                    "backendPort": "[parameters('loadBalancedAppPort2')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPort": "[parameters('loadBalancedAppPort2')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID3'),'/probes/AppPortProbe2')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            {
                "name": "FabricGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricTcpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "FabricHttpGatewayProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[variables('nt3fabricHttpGatewayPort')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe1",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort1')]",
                    "protocol": "tcp"
                }
            },
            {
                "name": "AppPortProbe2",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port": "[parameters('loadBalancedAppPort2')]",
                    "protocol": "tcp"
                }
            }
        ],
        "inboundNatPools": [
            {
                "name": "LoadBalancerBEAddressNatPool",
                "properties": {
                    "backendPort": "3389",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig3')]"
                    },
                    "frontendPortRangeEnd": "4500",
                    "frontendPortRangeStart": "3389",
                    "protocol": "tcp"
                }
            }
        ]
    },
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType3Name')))]"
    ]
},
```

*Template. JSON* dosyasında, yeni ağ güvenlik grubu ve sanal makine ölçek kümesi kaynakları ekleyin.  Sanal makine ölçek kümesinin Service Fabric uzantısı özellikleri içindeki NodeTypeRef özelliği, belirtilen düğüm türünü ölçek kümesine eşler.

```json
{
    "type": "Microsoft.Network/networkSecurityGroups",
    "name": "[concat('nsg', variables('subnet3Name'))]",
    "apiVersion": "2018-08-01",
    "location": "[resourceGroup().location]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "securityRules": [
            {
                "name": "allowSvcFabCluser",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "1025-1027",
                    "direction": "Inbound",
                    "priority": 3920,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow ports within vnet that are used by the fabric to talk between nodes"
                }
            },
            {
                "name": "allowSvcFabEphemeral",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3ephemeralStartPort'), '-', variables('nt3ephemeralEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3930,
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "sourcePortRange": "*",
                    "description": "allow fabric ephemeral ports within the vnet"
                }
            },
            {
                "name": "allowSvcFabPortal",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricHttpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3900,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster web portal"
                }
            },
            {
                "name": "allowSvcFabClient",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3fabricTcpGatewayPort')]",
                    "direction": "Inbound",
                    "priority": 3910,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used by the fabric client (includes powershell)"
                }
            },
            {
                "name": "allowSvcFabApplication",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[concat(variables('nt3applicationStartPort'), '-', variables('nt3applicationEndPort'))]",
                    "direction": "Inbound",
                    "priority": 3940,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow fabric application ports within the vnet"
                }
            },
            {
                "name": "blockAll",
                "properties": {
                    "access": "Deny",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "*",
                    "direction": "Inbound",
                    "priority": 4095,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "block all traffic except what we've explicitly allowed"
                }
            },
            {
                "name": "allowVNetRDP",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "3389",
                    "direction": "Inbound",
                    "priority": 3960,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow RDP within the net"
                }
            },
            {
                "name": "allowSvcFabReverseProxy",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[variables('nt3reverseProxyEndpointPort')]",
                    "direction": "Inbound",
                    "priority": 3980,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow port used to access the fabric cluster using reverse proxy"
                }
            },
            {
                "name": "allowAppPort1",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort1')]",
                    "direction": "Inbound",
                    "priority": 2001,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 1"
                }
            },
            {
                "name": "allowAppPort2",
                "properties": {
                    "access": "Allow",
                    "destinationAddressPrefix": "*",
                    "destinationPortRange": "[parameters('loadBalancedAppPort2')]",
                    "direction": "Inbound",
                    "priority": 2002,
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "sourcePortRange": "*",
                    "description": "allow public application port 2"
                }
            }
        ]
    }
},
{
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "sku": {
        "name": "[parameters('vmNodeType3Size')]",
        "capacity": "[parameters('nt3InstanceCount')]",
        "tier": "Standard"
    },
    "name": "[variables('vmNodeType3Name')]",
    "apiVersion": "2018-10-01",
    "location": "[variables('computeLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "overprovision": "[variables('overProvision')]",
        "upgradePolicy": {
            "mode": "Automatic"
        },
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                    {
                        "name": "[concat(variables('vmNodeType3Name'),'OMS')]",
                        "properties": {
                            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                            "type": "MicrosoftMonitoringAgent",
                            "typeHandlerVersion": "1.0",
                            "autoUpgradeMinorVersion": true,
                            "settings": {
                                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                            },
                            "protectedSettings": {
                                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                            }
                        }
                    },
                    {
                        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "ServiceFabricNode",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                            },
                            "publisher": "Microsoft.Azure.ServiceFabric",
                            "settings": {
                                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                "nodeTypeRef": "[variables('vmNodeType3Name')]",
                                "dataPath": "D:\\SvcFab",
                                "durabilityLevel": "Silver",
                                "enableParallelJobs": true,
                                "nicPrefixOverride": "[variables('subnet3Prefix')]",
                                "certificate": {
                                    "thumbprint": "[parameters('certificateThumbprint')]",
                                    "x509StoreName": "[parameters('certificateStoreValue')]"
                                }
                            },
                            "typeHandlerVersion": "1.0"
                        }
                    },
                    {
                        "name": "[concat('VMDiagnosticsVmExt','_vmNodeType3Name')]",
                        "properties": {
                            "type": "IaaSDiagnostics",
                            "autoUpgradeMinorVersion": true,
                            "protectedSettings": {
                                "storageAccountName": "[variables('applicationDiagnosticsStorageAccountName')]",
                                "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                                "storageAccountEndPoint": "https://core.windows.net/"
                            },
                            "publisher": "Microsoft.Azure.Diagnostics",
                            "settings": {
                                "WadCfg": {
                                    "DiagnosticMonitorConfiguration": {
                                        "overallQuotaInMB": "50000",
                                        "EtwProviders": {
                                            "EtwEventSourceProviderConfiguration": [
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Actors",
                                                    "scheduledTransferKeywordFilter": "1",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableActorEventTable"
                                                    }
                                                },
                                                {
                                                    "provider": "Microsoft-ServiceFabric-Services",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricReliableServiceEventTable"
                                                    }
                                                }
                                            ],
                                            "EtwManifestProviderConfiguration": [
                                                {
                                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                                    "scheduledTransferLogLevelFilter": "Information",
                                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                                    "scheduledTransferPeriod": "PT5M",
                                                    "DefaultEvents": {
                                                        "eventDestination": "ServiceFabricSystemEventTable"
                                                    }
                                                }
                                            ]
                                        }
                                    }
                                },
                                "StorageAccount": "[variables('applicationDiagnosticsStorageAccountName')]"
                            },
                            "typeHandlerVersion": "1.5"
                        }
                    },
                    {
                        "name": "[concat('VMIaaSAntimalware','_vmNodeType3Name')]",
                        "properties": {
                            "publisher": "Microsoft.Azure.Security",
                            "type": "IaaSAntimalware",
                            "typeHandlerVersion": "1.5",
                            "settings": {
                                "AntimalwareEnabled": "true",
                                "Exclusions": {
                                    "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                                    "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
                                },
                                "RealtimeProtectionEnabled": "true",
                                "ScheduledScanSettings": {
                                    "isEnabled": "true",
                                    "scanType": "Quick",
                                    "day": "7",
                                    "time": "120"
                                }
                            },
                            "protectedSettings": null
                        }
                    }
                ]
            },
            "networkProfile": {
                "networkInterfaceConfigurations": [
                    {
                        "name": "[concat(variables('nicName'), '-2')]",
                        "properties": {
                            "ipConfigurations": [
                                {
                                    "name": "[concat(variables('nicName'),'-',2)]",
                                    "properties": {
                                        "loadBalancerBackendAddressPools": [
                                            {
                                                "id": "[variables('lbPoolID3')]"
                                            }
                                        ],
                                        "loadBalancerInboundNatPools": [
                                            {
                                                "id": "[variables('lbNatPoolID3')]"
                                            }
                                        ],
                                        "subnet": {
                                            "id": "[variables('subnet3Ref')]"
                                        }
                                    }
                                }
                            ],
                            "primary": true
                        }
                    }
                ]
            },
            "osProfile": {
                "adminPassword": "[parameters('adminPassword')]",
                "adminUsername": "[parameters('adminUsername')]",
                "computernamePrefix": "[variables('vmNodeType3Name')]",
                "secrets": [
                    {
                        "sourceVault": {
                            "id": "[parameters('sourceVaultValue')]"
                        },
                        "vaultCertificates": [
                            {
                                "certificateStore": "[parameters('certificateStoreValue')]",
                                "certificateUrl": "[parameters('certificateUrlValue')]"
                            }
                        ]
                    }
                ]
            },
            "storageProfile": {
                "imageReference": {
                    "publisher": "[parameters('vmImagePublisher')]",
                    "offer": "[parameters('vmImageOffer')]",
                    "sku": "[parameters('vmImageSku')]",
                    "version": "[parameters('vmImageVersion')]"
                },
                "osDisk": {
                    "caching": "ReadOnly",
                    "createOption": "FromImage",
                    "managedDisk": {
                        "storageAccountType": "[parameters('storageAccountType')]"
                    }
                }
            }
        }
    },
    "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType3Name')))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]",
        "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
    ]
},
```

*Template. JSON* dosyasında, küme kaynağını güncelleştirin ve yeni bir düğüm türü ekleyin:
```json
{
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "apiVersion": "2018-02-01",
    "location": "[parameters('clusterLocation')]",
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    },
    "properties": {
        "nodeTypes": [
            ...
            {
                "name": "[variables('vmNodeType3Name')]",
                "applicationPorts": {
                    "endPort": "[variables('nt3applicationEndPort')]",
                    "startPort": "[variables('nt3applicationStartPort')]"
                },
                "clientConnectionEndpointPort": "[variables('nt3fabricTcpGatewayPort')]",
                "durabilityLevel": "Silver",
                "ephemeralPorts": {
                    "endPort": "[variables('nt3ephemeralEndPort')]",
                    "startPort": "[variables('nt3ephemeralStartPort')]"
                },
                "httpGatewayEndpointPort": "[variables('nt3fabricHttpGatewayPort')]",
                "isPrimary": false,
                "reverseProxyEndpointPort": "[variables('nt3reverseProxyEndpointPort')]",
                "vmInstanceCount": "[parameters('nt3InstanceCount')]"
            }
        ],    
    }
}                
```

*Parameters. JSON* dosyasında aşağıdaki yeni parametreleri ve değerleri ekleyin:
```json
"nt3InstanceCount": {
    "Value": 5    
},
"vmNodeType3Size": {
    "Value": "Standard_D2_V2"
},
```

### <a name="deploy-the-updated-template"></a>Güncelleştirilmiş şablonu dağıtma
*Template. JSON* ve *Parameters. JSON* dosyalarındaki değişiklikleri kaydedin.  Güncelleştirilmiş şablonu dağıtmak için aşağıdaki komutu çalıştırın:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "AddingNodeType"
```
Veya aşağıdaki Azure CLı komutu:
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="remove-a-node-type-from-the-cluster"></a>Kümeden düğüm türünü kaldırma
Bir Service Fabric kümesi oluşturduktan sonra, düğüm türünü (sanal makine ölçek kümesi) ve tüm düğümlerini kaldırarak bir kümeyi yatay olarak ölçeklendirebilirsiniz. Küme üzerinde iş yükleri çalışırken bile kümeyi istediğiniz zaman ölçeklendirebilirsiniz. Küme ölçeklenirken uygulamalarınız da otomatik olarak ölçeklendirilir.

> [!WARNING]
> Bir üretim kümesinden düğüm türünü kaldırmak için Remove-AzServiceFabricNodeType kullanılması, sıklıkla kullanılması önerilmez. Düğüm türünün arkasındaki sanal makine ölçek kümesi kaynağını sildiği için bu, tehlikeli bir komuttur. 

Düğüm türünü kaldırmak için [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype) cmdlet 'ini çalıştırın.  Düğüm türü gümüş veya altın [dayanıklılık düzeyinde][durability] olmalıdır cmdlet, düğüm türüyle ilişkili ölçek kümesini siler ve işlemin tamamlanmasını biraz zaman alır.  Ardından, kaldırılacak düğümlerin her birinde [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) cmdlet 'ini çalıştırın, bu, düğüm durumunu siler ve düğümleri kümeden kaldırır. Düğümlerde hizmetler varsa, hizmetler önce başka bir düğüme taşınır. Küme Yöneticisi çoğaltma/hizmet için bir düğüm bulamazsa, işlem gecikmeli/engellenir.

```powershell
$groupname = "sfclustertutorialgroup"
$nodetype = "nt4vm"
$clustername = "mysfcluster123"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="increase-node-resources"></a>Düğüm kaynaklarını artırma 
Service Fabric kümesi oluşturduktan sonra, küme düğümü türünü dikey olarak ölçeklendirebilir (düğümlerin kaynaklarını değiştirebilir) veya düğüm türü VM 'lerinin işletim sistemini yükseltebilirsiniz.  

> [!WARNING]
> Bir ölçek kümesi/düğüm türünün sanal makine SKU 'sunu, gümüş dayanıklılık veya daha büyük bir zamanda çalışmadığı takdirde değiştirmenizi öneririz. VM SKU 'SU boyutunun değiştirilmesi, veri bozucu bir yerinde altyapı işlemidir. Bu değişikliği gecikme veya izlemeye yönelik bir özellik olmadan, işlem durum bilgisi olmayan hizmetler için veri kaybına neden olabilir veya durum bilgisiz iş yükleri için bile öngörülemeyen çalışma sorunlarına neden olabilir.

> [!WARNING]
> Tehlikeli bir işlem ve desteklenmeyen birincil düğüm türünün VM SKU 'sunu değiştirmenizi öneririz.  Daha fazla küme kapasitesine ihtiyaç duyuyorsanız daha fazla sanal makine örneği veya ek düğüm türü ekleyebilirsiniz.  Bu mümkün değilse, yeni bir küme oluşturabilir ve eski kümenizde uygulama durumunu (varsa) [geri yükleyebilirsiniz](service-fabric-reliable-services-backup-restore.md) .  Bu mümkün değilse, [birincil düğüm türünün VM SKU 'sunu değiştirebilirsiniz](service-fabric-scale-up-node-type.md).

### <a name="update-the-template"></a>Şablonu güncelleştirme

En son dağıtım için kaynak grubundan [bir şablon ve parametreler dosyası dışarı aktarın](#export-the-template-for-the-resource-group) .  *Parameters. JSON* dosyasını açın.  Kümeyi Bu öğreticide [örnek şablonu][template] kullanarak dağıttıysanız, kümede üç düğüm türü vardır.  

İkinci düğüm türündeki VM 'lerin boyutu *vmNodeType1Size* parametresinde ayarlanır.  *VmNodeType1Size* parametre değerini, her bir sanal makine örneğinin kaynaklarını iki katına çıkarır. [Standard_D2_V2 olarak değiştirin](/azure/virtual-machines/windows/sizes-general#dv2-series).

Her üç düğüm türünün VM SKU 'SU *Vmımagesku* parametresinde ayarlanır.  Yine, bir düğüm türünün VM SKU 'sunu değiştirmek approached olmalıdır ve birincil düğüm türü için önerilmez.

### <a name="deploy-the-updated-template"></a>Güncelleştirilmiş şablonu dağıtma
*Template. JSON* ve *Parameters. JSON* dosyalarındaki değişiklikleri kaydedin.  Güncelleştirilmiş şablonu dağıtmak için aşağıdaki komutu çalıştırın:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName sfclustertutorialgroup -TemplateFile c:\temp\template.json -TemplateParameterFile c:\temp\parameters.json -Name "ScaleUpNodeType"
```
Veya aşağıdaki Azure CLı komutu:
```azure-cli
az group deployment create --resource-group sfclustertutorialgroup --template-file c:\temp\template.json --parameters c:\temp\parameters.json
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Düğüm ekleme ve kaldırma (ölçeği genişletme ve ölçekleme)
> * Düğüm türleri ekleme ve kaldırma (ölçeği genişletme ve ölçekleme)
> * Düğüm kaynaklarını artırma (ölçeği artırma)

Ardından, kümenin çalışma zamanının nasıl yükseltileceğini öğrenmek için aşağıdaki öğreticiye geçin.
> [!div class="nextstepaction"]
> [Bir kümenin çalışma zamanını yükseltme](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json

> * Düğüm türleri ekleme ve kaldırma (ölçeği genişletme ve ölçekleme)
> * Düğüm kaynaklarını artırma (ölçeği artırma)

Ardından, kümenin çalışma zamanının nasıl yükseltileceğini öğrenmek için aşağıdaki öğreticiye geçin.
> [!div class="nextstepaction"]
> [Bir kümenin çalışma zamanını yükseltme](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
