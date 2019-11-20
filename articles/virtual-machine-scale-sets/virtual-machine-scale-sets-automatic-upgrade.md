---
title: Azure sanal makine ölçek kümeleri ile otomatik işletim sistemi görüntüsü yükseltmeleri | Microsoft Docs
description: Ölçek kümesindeki sanal makine örneklerinde işletim sistemi görüntüsünü otomatik olarak yükseltmeyi öğrenin
services: virtual-machine-scale-sets
documentationcenter: ''
author: shandilvarun
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2019
ms.author: vashan
ms.openlocfilehash: 95a313b3c6995d55b86561c685641b447edae127
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72240932"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Azure sanal makine ölçek kümesi otomatik işletim sistemi görüntüsü yükseltmeleri

Ölçek kümesinde otomatik işletim sistemi görüntüsü yükseltmelerini etkinleştirmek, güncelleştirme yönetimini güvenle ve ölçek kümesindeki tüm örnekler için işletim sistemi diskini otomatik olarak yükselterek kolaylaştırır.

Otomatik işletim sistemi yükseltmesi aşağıdaki özelliklere sahiptir:

- Yapılandırıldıktan sonra, görüntü yayımcıları tarafından yayımlanan en son işletim sistemi görüntüsü Kullanıcı müdahalesi olmadan ölçek kümesine otomatik olarak uygulanır.
- Yayımcı tarafından her yeni bir platform görüntüsü yayımlandığında örnek toplu işleri her seferinde bir şekilde yükseltir.
- Uygulama durumu araştırmaları ve [uygulama sistem durumu uzantısı](virtual-machine-scale-sets-health-extension.md)ile tümleşir.
- Tüm VM boyutları ve hem Windows hem de Linux platform görüntüleri için geçerlidir.
- Dilediğiniz zaman Otomatik yükseltmeleri devre dışı bırakabilirsiniz (işletim sistemi yükseltmeleri de el ile başlatılabilir).
- Bir sanal makinenin işletim sistemi diski, en son görüntü sürümüyle oluşturulan yeni işletim sistemi diski ile değiştirilmiştir. Yapılandırılmış Uzantılar ve özel veri betikleri çalışır, ancak kalıcı veri diskleri korunur.
- [Uzantı sıralaması](virtual-machine-scale-sets-extension-sequencing.md) destekleniyor.
- Otomatik işletim sistemi görüntüsü yükseltmesi, herhangi bir boyuttaki ölçek kümesinde etkinleştirilebilir.

## <a name="how-does-automatic-os-image-upgrade-work"></a>Otomatik işletim sistemi görüntüsü yükseltmesi nasıl çalışır?

Bir yükseltme, bir sanal makinenin işletim sistemi diskini en son görüntü sürümü kullanılarak oluşturulan yeni bir diskle değiştirerek işe yarar. Yapılandırılmış Uzantılar ve özel veri betikleri işletim sistemi diskinde çalışır, ancak kalıcı veri diskleri korunur. Uygulamanın kapalı kalma süresini en aza indirmek için, yükseltme işlemleri herhangi bir zamanda, ölçek kümesinin% 20 ' inden daha fazlası olmadan toplu işler halinde gerçekleşir. Ayrıca, bir Azure Load Balancer uygulama durumu araştırması veya [uygulama sistem durumu uzantısı](virtual-machine-scale-sets-health-extension.md)da tümleştirebilirsiniz. Yükseltme işlemindeki her bir yığın için bir uygulama sinyali eklemeyi ve yükseltme başarısını doğrulamanızı öneririz.

Yükseltme işlemi aşağıdaki gibi kullanılabilir:
1. Yükseltme işlemine başlamadan önce, Orchestrator tüm ölçek kümesindeki örneklerin% 20 ' si sağlıksız (herhangi bir nedenle) olmadığından emin olur.
2. Yükseltme Orchestrator, toplam örnek sayısının en fazla% 20 ' sini içeren bir Batch ile Yükseltilecek sanal makine örneklerinin toplu işini tanımlar. 5 veya daha az örnek içeren küçük ölçek kümeleri için, bir yükseltmenin toplu iş boyutu bir sanal makine örneğidir.
3. Seçilen sanal makine örneklerinin işletim sistemi diski, en son görüntüden oluşturulan yeni bir işletim sistemi diski ile değiştirilmiştir. Ölçek kümesi modelindeki tüm belirtilen uzantılar ve Konfigürasyonlar yükseltilen örneğe uygulanır.
4. Yapılandırılmış uygulama durumu araştırmaları veya uygulama sistem durumu uzantısı olan ölçek kümeleri için, yükseltme, sonraki toplu işi yükseltmek üzere geçmeden önce örneğin sağlıklı olması için 5 dakikaya kadar bekler. Bir örnek bir yükseltmeden sonra sistem durumunu 5 dakika içinde kurtarmaz, varsayılan olarak örnek için önceki işletim sistemi diski geri yüklenir.
5. Yükseltme Orchestrator, bir yükseltme sonrası sağlıksız hale gelen örneklerin yüzdesini de izler. Yükseltme işlemi sırasında yükseltilen örneklerin% 20 ' den fazlası sağlıksız hale gelirse yükseltme durdurulur.
6. Yukarıdaki işlem, ölçek kümesindeki tüm örnekler yükseltilene kadar devam eder.

Ölçek kümesi işletim sistemi yükseltme Orchestrator, her toplu işi yükseltmeden önce tüm ölçek kümesi sistem durumunu denetler. Toplu işi yükseltirken, ölçek kümesi örneklerinizin sistem durumunu etkileyebilecek diğer eşzamanlı planlı veya planlanmamış bakım etkinlikleri olabilir. Bu gibi durumlarda, ölçek kümesinin örneklerinin% 20 ' si sağlıksız hale gelirse, ölçek kümesi yükseltmesi geçerli toplu işin sonunda duraklar.

## <a name="supported-os-images"></a>Desteklenen işletim sistemi görüntüleri
Şu anda yalnızca belirli işletim sistemi platformu görüntüleri destekleniyor. Özel görüntüler şu anda desteklenmiyor.

Aşağıdaki SKU 'Lar Şu anda desteklenmektedir (ve daha fazla düzenli olarak eklenir):

| Publisher               | İşletim sistemi teklifi      |  İsteyin               |
|-------------------------|---------------|--------------------|
| Kurallı               | UbuntuServer  | 16,04-LTS          |
| Kurallı               | UbuntuServer  | 18,04-LTS          |
| Standart dışı dalga (OpenLogic)  | CentOS        | 7,5                |
| CoreOS                  | CoreOS        | Kararlı             |
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | 2016-veri merkezi    |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-veri merkezi-kapsayıcılar |
| Microsoft Corporation   | WindowsServer | 2019-veri merkezi |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2019-veri merkezi-kapsayıcılar |
| Microsoft Corporation   | WindowsServer | Datacenter-Core-1903--containers-smalldisk |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>Otomatik işletim sistemi görüntüsünü yükseltmeyi yapılandırma gereksinimleri

- Platform görüntüsünün *Version* özelliği *en son*olarak ayarlanmalıdır.
- Service Fabric olmayan ölçek kümeleri için uygulama durumu araştırmaları veya [uygulama sistem durumu uzantısı](virtual-machine-scale-sets-health-extension.md) kullanın.
- Işlem API 'SI sürüm 2018-10-01 veya üstünü kullanın.
- Ölçek kümesi modelinde belirtilen dış kaynakların kullanılabilir olduğundan ve güncelleştirildiğinden emin olun. Örnek olarak, VM uzantı özelliklerinde önyükleme yükü için SAS URI, depolama hesabındaki yük, modeldeki gizli dizi başvuruları ve daha fazlası bulunur.
- Windows sanal makinelerini kullanan ölçek kümelerinde, Işlem API 'SI sürüm 2019-03-01 ' den başlayarak, ölçek kümesi modelinde *Virtualmachineprofile. osProfile. windowsConfiguration. enableAutomaticUpdates* özelliği *false* olarak ayarlanmalıdır tanımı. Yukarıdaki özellik, "Windows Update" sanal makine sürümlerini değiştirmeden işletim sistemi düzeltme ekleri uyguladığı VM yükseltmeleri etkinleştirilir. Ölçek kümesinde otomatik işletim sistemi görüntüsü yükseltmeleri etkinleştirilmiş olarak, "Windows Update" ile ek bir güncelleştirme gerekli değildir.

### <a name="service-fabric-requirements"></a>Service Fabric gereksinimleri

Service Fabric kullanıyorsanız, aşağıdaki koşulların karşılandığından emin olun:
-   Service Fabric [dayanıklılık düzeyi](../service-fabric/service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) gümüş veya altın, bronz değildir.
-   Ölçek kümesi modeli tanımındaki Service Fabric uzantısının TypeHandlerVersion 1,1 veya üzeri olması gerekir.
-   Dayanıklılık düzeyi, ölçek kümesi modeli tanımındaki Service Fabric kümesinde ve Service Fabric uzantısında aynı olmalıdır.

Uyuşmazlık ayarlarının Service Fabric kümesinde ve Service Fabric uzantısında eşleşmediğinden emin olun, bunun eşleşmemesi da yükseltme hatalarına neden olur. Dayanıklılık düzeyleri [Bu sayfada](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels)özetlenen yönergeler başına değiştirilebilir.

## <a name="configure-automatic-os-image-upgrade"></a>Otomatik işletim sistemi görüntüsünü yükseltmeyi yapılandırma
Otomatik işletim sistemi görüntüsü yükseltmesini yapılandırmak için, ölçek kümesi model tanımında *Automatıcosupgradepolicy. Enableautomatıcosupgrad* özelliğinin *true* olarak ayarlandığından emin olun.

### <a name="rest-api"></a>REST API
Aşağıdaki örnek, bir ölçek kümesi modelinde otomatik işletim sistemi yükseltmelerini ayarlamayı açıklar:

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2018-10-01`
```

```json
{
  "properties": {
    "upgradePolicy": {
      "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade":  true
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Ölçek kümesi için otomatik işletim sistemi görüntüsü yükseltmelerini yapılandırmak üzere [Update-AzVmss](/powershell/module/az.compute/update-azvmss) cmdlet 'ini kullanın. Aşağıdaki örnek, *Myresourcegroup*adlı kaynak grubunda *myScaleSet* adlı ölçek kümesi için Otomatik yükseltmeleri yapılandırır:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Azure CLı 2,0
Ölçek kümesi için otomatik işletim sistemi görüntüsü yükseltmelerini yapılandırmak için [az VMSS Update](/cli/azure/vmss#az-vmss-update) kullanın. Azure CLı 2.0.47 veya üstünü kullanın. Aşağıdaki örnek, *Myresourcegroup*adlı kaynak grubunda *myScaleSet* adlı ölçek kümesi için Otomatik yükseltmeleri yapılandırır:

```azurecli-interactive
az vmss update --name myScaleSet --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

## <a name="using-application-health-probes"></a>Uygulama durumu araştırmalarını kullanma

Bir işletim sistemi yükseltmesi sırasında, bir ölçek kümesindeki sanal makine örnekleri aynı anda bir toplu iş yükseltilir. Yükseltmenin yalnızca, yükseltilen sanal makine örneklerinde müşteri uygulaması sağlıklı olması durumunda devam etmesi gerekir. Uygulamanın, ölçek kümesi işletim sistemi yükseltme altyapısına sistem durumu sinyalleri sunmalarını öneririz. Varsayılan olarak, işletim sistemi yükseltmeleri sırasında platform VM güç durumu ve uzantı sağlama durumunu değerlendirir ve bir VM örneğinin bir yükseltmeden sonra sağlıklı olup olmadığını tespit eder. Bir VM örneğinin işletim sistemi yükseltmesi sırasında, bir sanal makine örneğindeki işletim sistemi diski, en son görüntü sürümüne göre yeni bir diskle değiştirilmiştir. İşletim sistemi yükseltmesi tamamlandıktan sonra, yapılandırılan uzantılar bu VM 'lerde çalıştırılır. Uygulama, yalnızca örnekteki tüm uzantılar başarıyla sağlandığında sağlıklı olarak değerlendirilir.

Bir ölçek kümesi, isteğe bağlı olarak uygulamanın devam eden durumu hakkında doğru bilgiler sağlamak için uygulama durumu araştırmaları ile yapılandırılabilir. Uygulama durumu araştırmaları, sistem durumu sinyali olarak kullanılan özel Load Balancer araştırmalar. Ölçek kümesi VM örneğinde çalışan uygulama, sağlıklı olup olmadığını gösteren dış HTTP veya TCP isteklerine yanıt verebilir. Özel Load Balancer araştırmalarının nasıl çalıştığı hakkında daha fazla bilgi için bkz. [yük dengeleyici araştırmalarını anlamak](../load-balancer/load-balancer-custom-probe-overview.md)için. Uygulama durumu araştırmaları Service Fabric ölçek kümeleri için desteklenmez. Service Fabric olmayan ölçek kümeleri, Load Balancer uygulama durumu araştırmaları ya da [uygulama sistem durumu uzantısı](virtual-machine-scale-sets-health-extension.md)gerektirir.

Ölçek kümesi birden çok yerleştirme grubu kullanacak şekilde yapılandırıldıysa, [Standart Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) kullanan yoklamaların kullanılması gerekir.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Ölçek kümesinde uygulama durumu araştırması olarak özel bir Load Balancer araştırması yapılandırma
En iyi uygulama olarak, ölçek kümesi sistem durumu için açık bir yük dengeleyici araştırması oluşturun. Var olan bir HTTP araştırması veya TCP araştırması için aynı uç nokta kullanılabilir, ancak bir sistem durumu araştırması geleneksel bir yük dengeleyici araştırmasından farklı davranış gerektirebilir. Örneğin, örnekteki yükün çok yüksek olması, ancak otomatik işletim sistemi yükseltmesi sırasında örnek durumunu belirlemek için uygun olmaması durumunda geleneksel bir yük dengeleyici araştırması sağlıksız bir şekilde dönebilir. Araştırmayı iki dakikadan kısa bir süre sonra yüksek bir yoklama oranına sahip olacak şekilde yapılandırın.

Yük dengeleyici araştırmasına ölçek kümesinin *Networkprofile* öğesine başvurulabilir ve bir iç veya genel kullanıma yönelik yük dengeleyiciyle aşağıdaki şekilde ilişkilendirilebilir:

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
}
```

> [!NOTE]
> Service Fabric ile otomatik işletim sistemi yükseltmeleri kullanırken, yeni işletim sistemi görüntüsü Service Fabric ' de çalışan hizmetlerin yüksek kullanılabilirliğini sürdürmek için etki alanını güncelleştirme etki alanını güncelleştir olarak almıştır. Service Fabric otomatik işletim sistemi yükseltmelerini kullanmak için, kümenizin gümüş dayanıklılık katmanını veya üstünü kullanacak şekilde yapılandırılması gerekir. Service Fabric kümelerinin dayanıklılık özellikleri hakkında daha fazla bilgi için lütfen [Bu belgelere](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)bakın.

### <a name="keep-credentials-up-to-date"></a>Kimlik bilgilerini güncel tut
Ölçek ayarlandıysa, depolama hesabı için bir SAS belirteci kullanmak üzere yapılandırılmış bir VM uzantısı gibi dış kaynaklara erişmek için herhangi bir kimlik bilgisi kullanılıyorsa, kimlik bilgilerinin güncelleştirildiğinden emin olun. Sertifikalar ve belirteçler dahil olmak üzere herhangi bir kimlik bilgisi dolmuşsa, yükseltme başarısız olur ve ilk VM toplu işi başarısız durumda bırakılır.

Kaynak kimlik doğrulama hatası varsa, VM 'Leri kurtarmak ve otomatik işletim sistemi yükseltmesini yeniden etkinleştirmek için önerilen adımlar şunlardır:

* Uzantlarınız içinde (veya diğer kimlik bilgileri) geçirilen belirteci yeniden oluşturun.
* VM 'nin içinden dış varlıklarla iletişim kurmak için kullanılan kimlik bilgilerinin güncel olduğundan emin olun.
* Ölçek kümesi modelindeki uzantıları yeni belirteçlerle güncelleştirin.
* Başarısız olanlar dahil tüm sanal makine örneklerini güncelleştirecek güncelleştirilmiş ölçek kümesini dağıtın.

## <a name="using-application-health-extension"></a>Uygulama durumu uzantısını kullanma
Uygulama sistem durumu uzantısı bir sanal makine ölçek kümesi örneği içine dağıtılır ve VM sistem durumu üzerinde, ölçek kümesi örneği içinden raporlar. Uzantıyı bir uygulama uç noktasında araştırma yapmak ve bu örnekteki uygulamanın durumunu güncelleştirmek için yapılandırabilirsiniz. Bu örnek durumu, bir örneğin yükseltme işlemlerine uygun olup olmadığını öğrenmek için Azure tarafından denetlenir.

Uzantı, bir VM içinden sistem durumunu raporladığında, uzantı, uygulama sistem durumu Araştırmaları (özel Azure Load Balancer [yoklamaları](../load-balancer/load-balancer-custom-probe-overview.md)kullanan) gibi dış araştırmaların kullanılamaz durumda olabilir.

[Bu makaledeki](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension)örneklerde açıklandığı şekilde, uygulama durumu uzantısını ölçek kümelerinizi dağıtmanın birden çok yolu vardır.

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>Otomatik işletim sistemi görüntüsü yükseltmelerinden oluşan geçmişi al
Azure PowerShell, Azure CLı 2,0 veya REST API 'Leri ile ölçek kümesinde gerçekleştirilen en son işletim sistemi yükseltmesinin geçmişini kontrol edebilirsiniz. Son iki ay içinde son beş işletim sistemi yükseltme girişiminin geçmişini alabilirsiniz.

### <a name="rest-api"></a>REST API
Aşağıdaki örnek, *Myresourcegroup*adlı kaynak grubunda *myScaleSet* adlı ölçek kümesinin durumunu denetlemek için [REST API](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) kullanır:

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```

GET çağrısı aşağıdaki örnek çıkışına benzer özellikler döndürür:

```json
{
    "value": [
        {
            "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Ölçek kümesi için işletim sistemi yükseltme geçmişini denetlemek üzere [Get-AzVmss](/powershell/module/az.compute/get-azvmss) cmdlet 'ini kullanın. Aşağıdaki örnek, *Myresourcegroup*adlı kaynak grubunda *myScaleSet* adlı bir ölçek kümesi için işletim sistemi yükseltme durumunu nasıl gözden geçiceğiniz ayrıntılardır:

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLı 2,0
Ölçek kümesi için işletim sistemi yükseltme geçmişini denetlemek için [az VMSS Get-OS-Upgrade-History](/cli/azure/vmss#az-vmss-get-os-upgrade-history) komutunu kullanın. Azure CLı 2.0.47 veya üstünü kullanın. Aşağıdaki örnek, *Myresourcegroup*adlı kaynak grubunda *myScaleSet* adlı bir ölçek kümesi için işletim sistemi yükseltme durumunu nasıl gözden geçiceğiniz ayrıntılardır:

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myScaleSet
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>Platform işletim sistemi görüntüsünün en son sürümü nasıl alınır?

Aşağıdaki örnekleri kullanarak otomatik işletim sistemi yükseltme desteklenen SKU 'Ların kullanılabilir görüntü sürümlerini edinebilirsiniz:

### <a name="rest-api"></a>REST API
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>Azure CLı 2,0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="manually-trigger-os-image-upgrades"></a>İşletim sistemi görüntüsü yükseltmelerini el ile tetikleme
Ölçek kümesinde otomatik işletim sistemi görüntüsü yükseltmesi etkin olduğunda, ölçek kümesinde görüntü güncelleştirmelerini el ile tetiklemeniz gerekmez. İşletim sistemi yükseltme Orchestrator, el ile müdahale olmadan ölçek kümesi örneklerinizin en son kullanılabilir görüntü sürümünü otomatik olarak uygular.

Orchestrator 'ın en son görüntüyü uygulamasını beklemek istemediğiniz belirli durumlarda, aşağıdaki örnekleri kullanarak bir işletim sistemi görüntüsünü yükseltmeyi el ile tetikleyebilirsiniz.

> [!NOTE]
> İşletim sistemi görüntüsü yükseltmelerinden el ile tetikleyici otomatik geri alma özellikleri sağlamaz. Bir örnek, bir yükseltme işleminden sonra durumunu kurtarmaz, önceki işletim sistemi diski geri yüklenemez.

### <a name="rest-api"></a>REST API
Tüm sanal makine ölçek kümesi örneklerini kullanılabilir en son Platform görüntüsü işletim sistemi sürümüne taşımak için bir sıralı yükseltme başlatmak üzere [işletim sistemi yükseltme](/rest/api/compute/virtualmachinescalesetrollingupgrades/startosupgrade) API 'si çağrısını kullanın. Zaten kullanılabilir olan en son işletim sistemi sürümünü çalıştıran örnekler etkilenmez. Aşağıdaki örnek, *Myresourcegroup*adlı kaynak grubunda *myScaleSet* adlı bir ölçek kümesi üzerinde sıralı bir işletim sistemi yükseltmesini nasıl başlayakullanabileceğinizi ayrıntılardır:

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osRollingUpgrade?api-version=2018-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Ölçek kümesi için işletim sistemi yükseltme geçmişini denetlemek için [Start-Azvmssrollingosupgrad](/powershell/module/az.compute/Start-AzVmssRollingOSUpgrade) cmdlet 'ini kullanın. Aşağıdaki örnek, *Myresourcegroup*adlı kaynak grubunda *myScaleSet* adlı bir ölçek kümesi üzerinde sıralı bir işletim sistemi yükseltmesini nasıl başlayakullanabileceğinizi ayrıntılardır:

```azurepowershell-interactive
Start-AzVmssRollingOSUpgrade -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

### <a name="azure-cli-20"></a>Azure CLı 2,0
Ölçek kümesi için işletim sistemi yükseltme geçmişini denetlemek için [az VMSS yuvarlama-yükseltme Başlat](/cli/azure/vmss/rolling-upgrade#az-vmss-rolling-upgrade-start) komutunu kullanın. Azure CLı 2.0.47 veya üstünü kullanın. Aşağıdaki örnek, *Myresourcegroup*adlı kaynak grubunda *myScaleSet* adlı bir ölçek kümesi üzerinde sıralı bir işletim sistemi yükseltmesini nasıl başlayakullanabileceğinizi ayrıntılardır:

```azurecli-interactive
az vmss rolling-upgrade start --resource-group "myResourceGroup" --name "myScaleSet" --subscription "subscriptionId"
```

## <a name="deploy-with-a-template"></a>Şablon ile dağıtma

[Ubuntu 16,04-LTS](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json)gibi desteklenen görüntülerde otomatik işletim sistemi yükseltmeleri ile bir ölçek kümesi dağıtmak için şablonları kullanabilirsiniz.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

## <a name="next-steps"></a>Sonraki adımlar
Ölçek kümeleri ile otomatik işletim sistemi yükseltmelerini kullanma hakkında daha fazla örnek için [GitHub deposu](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade)' nu gözden geçirin.
