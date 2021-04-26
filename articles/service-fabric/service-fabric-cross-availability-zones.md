---
title: Kullanılabilirlik Alanları arasında küme dağıtma
description: Kullanılabilirlik Alanları arasında Azure Service Fabric kümesi oluşturmayı öğrenin.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/16/2021
ms.author: pepogors
ms.openlocfilehash: 9cc2a9d189e7a781dc6ba64a65af022150392485
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727771"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Kullanılabilirlik Alanları arasında bir Azure Service Fabric kümesi dağıtma
Azure 'daki Kullanılabilirlik Alanları, uygulamalarınızı ve verilerinizi veri merkezi hatalarından koruyan yüksek kullanılabilirliğe sahip bir tekliftir. Bir kullanılabilirlik alanı, bir Azure bölgesi içinde bağımsız güç, soğutma ve ağ ile donatılmış benzersiz bir fiziksel konumdur.

Service Fabric, belirli bölgelere sabitlenmiş düğüm türlerini dağıtarak Kullanılabilirlik Alanları yayılmış kümeleri destekler. Bu, uygulamalarınızın yüksek kullanılabilirliğe sahip olmasını sağlayacaktır. Azure Kullanılabilirlik Alanları yalnızca belirli bölgelerde kullanılabilir. Daha fazla bilgi için bkz. [Azure kullanılabilirlik alanları genel bakış](../availability-zones/az-overview.md).

Örnek şablonlar mevcuttur: [Service Fabric çapraz kullanılabilirlik bölgesi şablonu](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Kullanılabilirlik Alanları arasında yayılan Azure Service Fabric kümelerinin birincil düğüm türü için önerilen topoloji
Kullanılabilirlik Alanları üzerinde dağıtılan bir Service Fabric kümesi, küme durumunun yüksek düzeyde kullanılabilir olmasını sağlar. Bölgeler arasında bir Service Fabric kümesini yaymak için bölgenin desteklediği her bir kullanılabilirlik alanında bir birincil düğüm türü oluşturmanız gerekir. Bu işlem, çekirdek düğümlerini birincil düğüm türlerinin her biri arasında eşit olarak dağıtır.

Birincil düğüm türü için önerilen topoloji, aşağıda özetlenen kaynakları gerektirir:

* Küme güvenilirlik düzeyi Platinum olarak ayarlanır.
* Birincil olarak işaretlenmiş üç düğüm türü.
    * Her düğüm türü, farklı bölgelerde bulunan kendi sanal makine ölçek kümesine eşlenmelidir.
    * Her sanal makine ölçek kümesi en az beş düğüm (gümüş dayanıklılık) içermelidir.
* Standart SKU kullanan tek bir genel IP kaynağı.
* Standart SKU kullanan tek bir Load Balancer kaynağı.
* Sanal makine ölçek kümelerinizi dağıttığınız alt ağ tarafından başvurulan bir NSG.

>[!NOTE]
> Sanal makine ölçek kümesi tek yerleşim grubu özelliği true olarak ayarlanmalıdır.

![Azure Service Fabric kullanılabilirlik alanı mimarisini gösteren azure Service Fabric kullanılabilirliği bölge mimarisi diyagramını gösteren diyagram.][sf-architecture]

Bir sanal makine ölçek kümesi kapsayıcı bölgelerindeki FD/UD biçimlerini gösteren örnek düğüm listesi

 ![Bir sanal makine ölçek kümesi kapsayıcı bölgelerindeki FD/UD biçimlerini gösteren örnek düğüm listesi.][sf-multi-az-nodes]

**Bölgeler arasında hizmet çoğaltmaları dağıtımı**: bir hizmet, bölgeleri kapsayan nodetypes üzerinde dağıtıldığında çoğaltmalar, ayrı bölgelerde yer aldığından emin olmak için yerleştirilir. Bu nodeTypes ' ın her birinde bulunan düğümlerdeki hata etki alanı bölge bilgileri ile yapılandırıldığı için bu değer gereklidir (ör. FD = FD://ay/1 vb...). Örneğin: 5 çoğaltma veya bir hizmetin örnekleri için dağıtım 2-2-1 olur ve çalışma zamanı AZs genelinde eşit dağıtım sağlamaya çalışacaktır.

**Kullanıcı Hizmeti çoğaltma yapılandırması**: çapraz kullanılabilirlik bölgesi nodetypes üzerinde dağıtılan durum bilgisi olan Kullanıcı hizmetlerinin bu yapılandırmayla yapılandırılması gerekir: hedef = 9, en az = 5 olan çoğaltma sayısı. Bu yapılandırma, 6 çoğaltma diğer iki bölgede hala devam ettiğinden, bir bölgenin bir arada kaldığında bile hizmetin çalışmaya yardımcı olur. Böyle bir senaryoda uygulama yükseltmesi de devam edecektir.

**Küme Rahatlımadüzeyi**: Bu, kümedeki çekirdek düğümlerin sayısını ve ayrıca sistem hizmetlerinin çoğaltma boyutunu tanımlar. Çapraz kullanılabilirlik alanı kurulumu, bölge dayanıklılığı sağlamak için bölgelere yayılan daha yüksek sayıda düğüme sahip olduğundan, daha yüksek güvenilirlik değeri düğüm daha fazla çekirdek düğümlerinin ve sistem hizmeti çoğaltmalarının mevcut olmasını ve bölge genelinde eşit bir şekilde dağıtılmasını sağlar; böylece, küme hatası ve sistem hizmetleri etkilenmeden kalır. "Relidiitylevel = Platinum", her bölgede 3 çekirdekler ile, bu sayede çapraz kullanılabilirlik bölgesi kurulumu için önerilir.

**Bölge aşağı senaryosu**: bir bölge kaldığında, bu bölgedeki tüm düğümler aşağı görünür. Bu düğümlerdeki hizmet çoğaltmaları da kaldırılacak. Diğer bölgelerde çoğaltmalar olduğundan, hizmet, çalışmakta olan bölgelere yük devretmeye yönelik birincil çoğaltmalarla yanıt vermeye devam eder. Hedef çoğaltma sayısı henüz ulaşılmadığından ve VM sayısı en az bir hedef çoğaltma boyutundan fazla olduğundan, hizmetler uyarı durumunda görüntülenir. Daha sonra Service Fabric yük dengeleyici, yapılandırılan hedef çoğaltma sayısıyla eşleşecek şekilde çalışma bölgesindeki çoğaltmaları getirir. Bu noktada hizmetler sağlıklı olarak görünür. Kapatılmış bölge, yük dengelemeye geri geldiğinde, tüm bölgelere eşit olarak tüm hizmet çoğaltmalarını yayacaktır.

## <a name="networking-requirements"></a>Ağ gereksinimleri
### <a name="public-ip-and-load-balancer-resource"></a>Genel IP ve Load Balancer kaynağı
Bir sanal makine ölçek kümesi kaynağında bölgeler özelliğini etkinleştirmek için, bu sanal makine ölçek kümesi tarafından başvurulan yük dengeleyici ve IP kaynağının her ikisi de *Standart* SKU kullanmalıdır. SKU özelliği olmadan bir yük dengeleyici veya IP kaynağı oluşturmak, Kullanılabilirlik Alanları desteklemeyen temel bir SKU oluşturur. Standart SKU yük dengeleyici, dışarıdaki tüm trafiği varsayılan olarak engeller; Dış trafiğe izin vermek için bir NSG 'nin alt ağa dağıtılması gerekir.

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
```

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> Ortak IP ve yük dengeleyici kaynaklarında bir yerinde SKU değişikliği yapmak mümkün değildir. Temel SKU 'SU olan mevcut kaynaklardan geçiş yapıyorsanız, bu makalenin geçiş bölümüne bakın.

### <a name="virtual-machine-scale-set-nat-rules"></a>Sanal makine ölçek kümesi NAT kuralları
Yük dengeleyici gelen NAT kuralları, sanal makine ölçek kümesindeki NAT havuzlarıyla eşleşmelidir. Her sanal makine ölçek kümesinin benzersiz bir gelen NAT havuzu olmalıdır.

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>Standart SKU Load Balancer giden kuralları
Standart Load Balancer ve standart genel IP, temel SKU 'Ları kullanmaya kıyasla giden bağlantılara yeni yetenekler ve farklı davranışlar getirir. Standart SKU 'Lar ile çalışırken giden bağlantı isterseniz, standart genel IP adresleriyle veya standart ortak Load Balancer açıkça tanımlamanız gerekir. Daha fazla bilgi için bkz. [giden bağlantılar](../load-balancer/load-balancer-outbound-connections.md) ve [Azure Standart Load Balancer](../load-balancer/load-balancer-overview.md).

>[!NOTE]
> Standart Şablon, tüm giden trafiğe varsayılan olarak izin veren bir NSG 'ye başvurur. Gelen trafik Service Fabric yönetim işlemleri için gerekli olan bağlantı noktalarıyla sınırlıdır. NSG kuralları gereksinimlerinizi karşılayacak şekilde değiştirilebilir.

>[!NOTE]
> Standart SKU SLB kullanarak her bir Service Fabric kümesi, bağlantı noktası 443 ' de giden trafiğe izin veren her düğüm türünün bir kurala sahip olduğundan emin olunması gerekir. Bu, küme kurulumunu tamamlaması için gereklidir ve bu tür bir kural olmadan herhangi bir dağıtım başarısız olur.


### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>Bir sanal makine ölçek kümesi üzerinde bölgeleri etkinleştirme
Bir bölgeyi etkinleştirmek için, bir sanal makine ölçek kümesinde, sanal makine ölçek kümesi kaynağına aşağıdaki üç değeri eklemeniz gerekir.

* İlk değer, sanal makine ölçek kümesinin hangi kullanılabilirlik bölgesine dağıtılacağını belirten **bölgeler** özelliğidir.
* İkinci değer, true olarak ayarlanması gereken "singlePlacementGroup" özelliğidir.
* Üçüncü değer, Service Fabric sanal makine ölçek kümesi uzantısında "faultDomainOverride" özelliğidir. Bu özelliğin değeri yalnızca bu sanal makine ölçek kümesinin yerleştirileceği bölgeyi içermelidir. Örnek: "faultDomainOverride": "AZ1" tüm sanal makine ölçek kümesi kaynakları aynı bölgeye yerleştirilmelidir çünkü Azure Service Fabric kümelerinde çapraz bölge desteği yoktur.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
    },
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Service Fabric küme kaynağında birden çok birincil düğüm türü etkinleştiriliyor
Bir küme kaynağında birincil olarak bir veya daha fazla düğüm türü ayarlamak için "Isprımary" özelliğini "true" olarak ayarlayın. Kullanılabilirlik Alanları arasında Service Fabric kümesi dağıtımında, farklı bölgelerde üç düğüm türüne sahip olmanız gerekir.

```json
{
    "reliabilityLevel": "Platinum",
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType2Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt2applicationEndPort')]",
            "startPort": "[parameters('nt2applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt2ephemeralEndPort')]",
            "startPort": "[parameters('nt2ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Temel SKU Load Balancer ve temel SKU IP 'si kullanarak bir kümeden Kullanılabilirlik Alanları kullanmak için geçirme
Temel SKU ile Load Balancer ve IP kullanan bir kümeyi geçirmek için, önce standart SKU 'yu kullanarak tamamen yeni bir Load Balancer ve IP kaynağı oluşturmanız gerekir. Bu kaynakları yerinde güncelleştirmek mümkün değildir.

Yeni LB ve IP 'yi kullanmak istediğiniz yeni çapraz kullanılabilirlik bölgesi düğüm türlerinde başvurulmalıdır. Yukarıdaki örnekte, bölge 1, 2 ve 3 ' te üç yeni sanal makine ölçek kümesi kaynağı eklenmiştir. Bu sanal makine ölçek kümeleri, yeni oluşturulan LB ve IP 'ye başvurur ve Service Fabric küme kaynağında birincil düğüm türleri olarak işaretlenir.

Başlamak için, mevcut Kaynak Yöneticisi şablonunuza yeni kaynakları eklemeniz gerekir. Bu kaynaklar şunları içerir:
* Standart SKU kullanan genel bir IP kaynağı.
* Standart SKU kullanan bir Load Balancer kaynağı.
* Sanal makine ölçek kümelerinizi dağıttığınız alt ağ tarafından başvurulan bir NSG.
* Birincil olarak işaretlenmiş üç düğüm türü.
    * Her düğüm türü, farklı bölgelerde bulunan kendi sanal makine ölçek kümesine eşlenmelidir.
    * Her sanal makine ölçek kümesi en az beş düğüm (gümüş dayanıklılık) içermelidir.

[Örnek şablonda](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure)bu kaynaklara bir örnek bulabilirsiniz.

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Kaynakların dağıtımı tamamlandıktan sonra, birincil düğüm türündeki düğümleri orijinal kümeden devre dışı bırakmayı deneyebilirsiniz. Düğümler devre dışı bırakıldığı için, sistem hizmetleri Yukarıdaki adımda dağıtılan yeni birincil düğüm türüne geçirilir.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"

$nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames) {
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Düğümlerin tümü devre dışı bırakıldıktan sonra sistem hizmetleri, bölgeler arasında yayılan birincil düğüm türünde çalışır. Bundan sonra devre dışı bırakılmış düğümleri kümeden kaldırabilirsiniz. Düğümler kaldırıldıktan sonra, özgün IP, Load Balancer ve sanal makine ölçek kümesi kaynaklarını kaldırabilirsiniz.

```powershell
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}

$scaleSetName="nt0"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

$lbname="LB-cluster-nt0"
$oldPublicIpName="LBIP-cluster-0"
$newPublicIpName="LBIP-cluster-1"

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

Daha sonra, bu kaynaklara ait başvuruları dağıttığınız Kaynak Yöneticisi şablondan kaldırmanız gerekir.

Son adım, DNS adının ve genel IP 'nin güncelleştirilmesini içerir.

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

```

## <a name="preview-enable-multiple-availability-zones-in-single-virtual-machine-scale-set"></a>Önizle Tek bir sanal makine ölçek kümesinde birden çok kullanılabilirlik bölgesini etkinleştir

Daha önce bahsedilen çözüm, AZ bir nodeType kullanır. Aşağıdaki çözüm, kullanıcıların aynı nodeType içindeki 3 AZ ' i dağıtmaları için izin verir.

**Bu özellik şu anda önizleme aşamasında olduğundan, şu anda üretim senaryolarında desteklenmemektedir.**

Tam örnek şablon [burada](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/15-VM-Windows-Multiple-AZ-Secure)bulunur.

![Azure Service Fabric kullanılabilirlik alanı mimarisi][sf-multi-az-arch]

### <a name="configuring-zones-on-a-virtual-machine-scale-set"></a>Bir sanal makine ölçek kümesindeki bölgeleri yapılandırma
Bir sanal makine ölçek kümesindeki bölgeleri etkinleştirmek için, sanal makine ölçek kümesi kaynağına aşağıdaki üç değeri eklemeniz gerekir.

* İlk değer, sanal makine ölçek kümesinde var olan Kullanılabilirlik Alanları belirten **Zones** özelliğidir.
* İkinci değer, true olarak ayarlanması gereken "singlePlacementGroup" özelliğidir. **3 AZ ' de yayılmış ölçek kümesi, "singlePlacementGroup = true" ile birlikte 300 VM 'ye ölçeklendirebilir.**
* Üçüncü değer "bölge bakiyesi" olur ve bu da katı bölge dengelemesi sağlar. Bu değer "true" olmalıdır. Bu, bölgelerin tamamında VM dağıtımlarının dengesizolmamasını sağlar. bu sayede bölgelerden biri kaldığında, diğer iki bölgenin, kümenin çalışmayı kesintiye uğratılmasını sağlamak için yeterli VM 'Ler vardır. Dengesiz bir VM dağıtımına sahip bir küme, bu bölge VM 'lerin çoğunluğuna sahip olabileceğinden bölge azaltma senaryosunu sürdürmeyebilir. Bölgeler arasında dengesiz VM dağıtımı, altyapı güncelleştirmelerinin takılmasına & hizmet yerleştirme ile ilgili sorunlara da neden olur. [Bölge Dengeleme](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing)hakkında bilgi edinin.
* FaultDomain ve UpgradeDomain geçersiz kılmalarını yapılandırmak için gerekli değildir.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1", "2", "3"],
    "properties": {
        "singlePlacementGroup": "true",
        "zoneBalance": true
    }
}
```

>[!NOTE]
> * **Service Fabric kümeler en az bir birincil nodeType içermelidir. Birincil nodeTypes 'ın durda daha yüksek düzeyi veya üzeri olması gerekir.**
> * AZ yayılmış sanal makine ölçek kümesi, Dureriþitylevel ' dan bağımsız olarak en az 3 kullanılabilirlik bölgesi ile yapılandırılmalıdır.
> * AZ gümüş dayanıklılık (veya üzeri) ile sanal makine ölçek kümesi, en az 15 VM 'ye sahip olmalıdır.
> * AZ önce bronz dayanıklılık ile sanal makine ölçek kümesi yayılmalı, en az 6 VM olmalıdır.

### <a name="enabling-the-support-for-multiple-zones-in-the-service-fabric-nodetype"></a>Service Fabric nodeType içinde birden çok bölge desteğini etkinleştirme
Birden çok kullanılabilirlik bölgesini desteklemek için Service Fabric nodeType etkinleştirilmelidir.

* İlk değer, nodeType için true olarak ayarlanması gereken **Çoğulkullanılabilirliği Bilityzones** ' dır.
* İkinci değer **Sfzonalupgrademode** ' dır ve isteğe bağlıdır. Kümede birden çok AZ olan NodeType zaten varsa, bu özellik değiştirilemez.
  Özelliği, yükseltme etki alanlarında VM 'lerin mantıksal gruplandırmasını denetler.
  **Değer "Parallel" olarak ayarlandıysa:** NodeType altında bulunan VM 'Ler, 5 UDs 'deki bölge bilgilerini yoksayarak Uılılar halinde gruplandırılır. Bu, tüm bölgelerde UD0 aynı anda yükseltilmesine neden olur. Bu dağıtım modu yükseltmeler için daha hızlıdır, ancak bu durum, güncelleştirmelerin aynı anda yalnızca bir bölge uygulanması gerektiği konusunda, bu durum, SDP yönergelerine karşı bir süredir uygulandığından önerilmez.
  **Değer atlanırsa veya "sıradüzensel" olarak ayarlandıysa:** VM 'Ler, en fazla 15 UDs 'ye kadar olan dağılımı yansıtacak şekilde gruplandırılır. 3 bölgenin her biri 5 UDs 'ye sahip olacaktır. Bu, güncelleştirmelerin bölge temelinde, ilk bölge içinde 5 UDs 'yi tamamladıktan sonra, kümenin ve Kullanıcı uygulamasının perspektifinden daha güvenli bir şekilde 15 UDs (3 bölge, 5 SSD) boyunca yavaş bir şekilde hareket edilmesini sağlar.
  Bu özellik yalnızca ServiceFabric uygulaması ve kod yükseltmeleri için yükseltme davranışını tanımlar. Temel alınan sanal makine ölçek kümesi yükseltmeleri, tüm AZ olarak paralel olmaya devam edecektir.
  Bu özelliğin, birden çok bölgesi etkin olmayan düğüm türleri için UD dağıtımı üzerinde hiçbir etkisi olmayacaktır.
* Üçüncü değer **Vmsszonalupgrademode = Parallel** değeridir. Bu, birden fazla AZs içeren bir nodeType eklenirse kümede yapılandırılacak *zorunlu* bir özelliktir. Bu özellik, tüm az bir kez paralel olarak gerçekleştirilecek sanal makine ölçek kümesi güncelleştirmeleri için yükseltme modunu tanımlar.
  Şimdi bu özellik yalnızca paralel olarak ayarlanabilir.
* Service Fabric küme kaynağı apiVersion, "2020-12-01-Preview" veya üzeri olmalıdır.
* Küme kodu sürümü "7.2.445" veya üzeri olmalıdır.

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
    "name": "[parameters('clusterName')]",
    "location": "[parameters('clusterLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
    ],
    "properties": {
        "reliabilityLevel": "Platinum",
        "SFZonalUpgradeMode": "Hierarchical",
        "VMSSZonalUpgradeMode": "Parallel",
        "nodeTypes": [
          {
                "name": "[parameters('vmNodeType0Name')]",
                "multipleAvailabilityZones": true,
          }
        ]
}
```

>[!NOTE]
> * Genel IP ve Load Balancer kaynakları, makalenin önceki kısımlarında açıklandığı gibi standart SKU 'YU kullanmalıdır.
> * nodeType üzerinde "Çoğulkullanılabilirliği Bilityzones" özelliği yalnızca nodeType oluşturma sırasında tanımlanabilir ve daha sonra değiştirilemez. Bu nedenle, mevcut nodeTypes bu özellikle yapılandırılamaz.
> * "SfZonalUpgradeMode" atlandığında veya "sıradüzenli" olarak ayarlandığında, kümede daha fazla yükseltme etki alanı olduğu için küme ve uygulama dağıtımları daha yavaş olur. Yükseltme ilkesi zaman aşımlarını, 15 yükseltme etki alanı için yükseltme süresi boyunca içerecek şekilde doğru şekilde ayarlamanız önemlidir. Dağıtımın, 12 saatlik Azure Kaynak İlkesi dağıtım zaman aşımlarını aşmadığından emin olmak için hem uygulama hem de küme için yükseltme ilkesi güncelleştirilmeleri gerekir. Bu, dağıtımın 15 saatten fazla sürmeyeceği anlamına gelir (örneğin, 40 dakikadan daha fazla sürmemelidir.
> * Kümenin bir bölge alt senaryosunu kullandığından emin olmak için, küme **Rahatlılılevel = Platinum** değerini ayarlayın.

>[!NOTE]
> En iyi uygulama için sfZonalUpgradeMode sıradüzensel olarak ayarlanmalıdır veya atlanacaktır. Dağıtım, daha küçük bir çoğaltmalar ve/veya örneklerin daha güvenli hale getirilmesi için, VM 'lerin ZGen dağılımını takip edecektir.
> Dağıtım hızı bir öncelik veya yalnızca durum bilgisi olan bir iş yükü birden çok AZ olan düğüm türü üzerinde çalışıyorsa, sfZonalUpgradeMode öğesini paralel olarak ayarlayın. Bu, UD 'nin tüm AZ ' de paralel olarak oluşmasına neden olur.

### <a name="migration-to-the-node-type-with-multiple-availability-zones"></a>Birden çok Kullanılabilirlik Alanları düğüm türüne geçiş
Tüm geçiş senaryoları için, birden çok kullanılabilirlik bölgesi desteklenmeleri gereken yeni bir nodeType eklenmelidir. Mevcut bir nodeType birden çok bölgeyi destekleyecek şekilde geçirilemez.
[Buradaki](./service-fabric-scale-up-primary-node-type.md) makale, yeni bir NodeType ekleme ve IP ve lb kaynakları gibi yeni NodeType için gereken diğer kaynakları ekleme hakkında ayrıntılı adımları yakalar. Aynı makalede, birden fazla kullanılabilirlik bölgesi olan nodeType, kümeye eklendikten sonra var olan nodeType devre dışı bırakılması de açıklanmaktadır.

* Temel LB ve IP kaynaklarını kullanan bir nodeType 'ten geçiş: Bu, AZ önce bir düğüm türü olan çözüm için [burada](#migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip) açıklanmıştır. 
    Yeni düğüm türü için tek fark, yalnızca 1 sanal makine ölçek kümesi ve 1 NodeType, AZ başına 1 değil, her biri için 1 NodeType olacaktır.
* NSG ile standart SKU LB ve IP kaynaklarını kullanan bir nodeType 'dan geçiş: yeni LB, IP ve NSG kaynakları eklemeye gerek olmadığı ve aynı kaynakların yeni nodeType içinde yeniden kullanılabilmesi için yukarıda açıklanan yordamın aynısını Izleyin.


[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
[sf-multi-az-arch]: ./media/service-fabric-cross-availability-zones/sf-multi-az-topology.png
[sf-multi-az-nodes]: ./media/service-fabric-cross-availability-zones/sf-multi-az-nodes.png
