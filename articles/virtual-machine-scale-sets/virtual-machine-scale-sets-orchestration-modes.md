---
title: Azure 'da sanal makine ölçek kümeleri için düzenleme modları
description: Azure 'daki sanal makine ölçek kümeleri için esnek ve Tekdüzen düzenleme modlarını nasıl kullanacağınızı öğrenin.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.date: 02/12/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: d089708ead67891164aee074394e923d2a84a977
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774458"
---
# <a name="preview-orchestration-modes-for-virtual-machine-scale-sets-in-azure"></a>Önizleme: Azure 'da sanal makine ölçek kümeleri için düzenleme modları 

Sanal Makine Ölçek Kümeleri, platform tarafından yönetilen sanal makinelerin mantıksal bir gruplandırmasını sağlar. Ölçek kümeleri ile, bir sanal makine yapılandırma modeli oluşturur, CPU veya bellek yüküne göre ek örnekler ekler veya kaldırır ve otomatik olarak en son işletim sistemi sürümüne yükseltilir. Geleneksel olarak, ölçek kümeleri, ölçek kümesi oluşturma sırasında sağlanmış bir VM yapılandırma modeli kullanarak sanal makineler oluşturmanızı sağlar ve ölçek kümesi yalnızca yapılandırma modeline göre örtük olarak oluşturulan sanal makineleri yönetebilir. 

Ölçek kümesi düzenleme modları, sanal makine örneklerinin ölçek kümesi tarafından yönetilme konusunda daha fazla denetime sahip olmasını sağlar. 

> [!IMPORTANT]
> Düzenleme modu, ölçek kümesi oluşturduğunuzda tanımlanır ve daha sonra değiştirilemez veya güncelleştirilemez.


## <a name="scale-sets-with-uniform-orchestration"></a>Tekdüzen düzenleme ile ölçek kümeleri
Aynı örneklere sahip büyük ölçekli durum bilgisiz iş yükleri için iyileştirilmiştir.

Tekdüzen düzenleme özellikli sanal makine ölçek kümeleri, istenen kapasiteye kadar ölçeklenebilen bir sanal makine profili veya şablonu kullanır. Tek tek sanal makine örneklerini yönetme veya özelleştirme yeteneği olsa da, Tekdüzen özdeş VM örnekleri kullanır. Tek tek Tekdüzen VM örnekleri, sanal makine ölçek kümesi VM API komutları aracılığıyla sunulur. Tek örnekler standart Azure IaaS VM API komutlarıyla, Azure Resource Manager kaynak etiketleme RBAC izinleri, Azure Backup veya Azure Site Recovery gibi Azure yönetim özellikleriyle uyumlu değildir. Tekdüzen düzenleme, 100 'den az örnek ile yapılandırıldığında hata etki alanı yüksek kullanılabilirlik garantisi sağlar. Tekdüzen düzenleme genel olarak kullanılabilir ve ölçüm tabanlı otomatik ölçeklendirme, örnek koruma ve otomatik işletim sistemi yükseltmeleri dahil olmak üzere tam ölçek kümesi Yönetimi ve düzenlemesini destekler. 


## <a name="scale-sets-with-flexible-orchestration"></a>Esnek düzenleme ile ölçek kümeleri 
Özdeş veya birden çok sanal makine türüyle yüksek düzeyde kullanılabilirlik elde edin.

Esnek düzenleme sayesinde Azure, Azure VM ekosistemi üzerinde Birleşik bir deneyim sunar. Esnek düzenleme, bir bölgedeki veya bir kullanılabilirlik bölgesindeki hata etki alanları arasında VM 'Leri yayarak yüksek kullanılabilirlik garantisi (1000 VM 'ye kadar) sunar. Bu, aşağıdaki gibi çekirdek tabanlı veya durum bilgisi olan iş yüklerini çalıştırmak için gereken hata etki alanı yalıtımını sürdürirken uygulamanızı ölçeklendirmenize olanak sağlar:
- Çekirdek tabanlı iş yükleri
- Open-Source veritabanları
- Durum bilgisi olan uygulamalar
- Yüksek kullanılabilirlik ve büyük ölçek gerektiren hizmetler
- Sanal makine türlerini karıştırmak veya nokta ve isteğe bağlı VM 'Lerin birlikte yararlanmak isteyen hizmetler
- Mevcut kullanılabilirlik kümesi uygulamaları  

> [!IMPORTANT]
> Esnek düzenleme modundaki sanal makine ölçek kümeleri Şu anda genel önizlemededir. Aşağıda açıklanan genel önizleme işlevlerini kullanmak için bir katılım prosedürü gereklidir.
> Bu önizleme sürümü, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="what-has-changed-with-flexible-orchestration-mode"></a>Esnek düzenleme moduyla ne değişti?
Esnek düzenleme 'nin başlıca avantajlarından biri, ölçek kümesi alt sanal makineleri yerine standart Azure IaaS VM 'lerinde düzenleme özellikleri sunmamadır. Bu, Tekdüzen düzenleme ile kullandığınız sanal makine ölçek kümesi VM API 'Leri yerine esnek düzenleme örneklerini yönetirken tüm standart VM API 'Lerini kullanabileceğiniz anlamına gelir. Önizleme dönemi boyunca esnek düzenleme ve Tekdüzen düzenleme içinde örnekleri yönetme arasında çeşitli farklılıklar vardır. Genel olarak, mümkünse standart Azure IaaS VM API 'Lerini kullanmanızı öneririz. Bu bölümde, esnek düzenleme ile sanal makine örneklerini yönetmek için en iyi yöntem örneklerini vurgularız.  

### <a name="assign-fault-domain-during-vm-creation"></a>VM oluşturma sırasında hata etki alanı atama
Esnek düzenleme ölçeği kümesi için hata etki alanı sayısını seçebilirsiniz. Varsayılan olarak, esnek ölçek kümesine bir sanal makine eklediğinizde, Azure bu örnekleri hata etki alanları arasında eşit olarak yayar. Azure 'un hata etki alanını atamasına izin vermekle önerilse de, gelişmiş veya sorun giderme senaryoları için bu varsayılan davranışı geçersiz kılabilir ve örneğin geçilebileceği hata etki alanını belirtebilirsiniz.

```azurecli-interactive 
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

### <a name="instance-naming"></a>Örnek adlandırma 
Bir VM oluşturup esnek ölçek kümesine eklediğinizde, Azure adlandırma kuralı kurallarında örnek adları üzerinde tam denetime sahip olursunuz. VM 'Ler otomatik ölçeklendirme ile ölçek kümesine otomatik olarak eklendiğinde, bir ön ek sağlarsınız ve Azure adının sonuna benzersiz bir sayı ekler.

### <a name="query-instances-for-power-state"></a>Güç durumu için sorgu örnekleri
Tercih edilen yöntem, bir sanal makine ölçek kümesindeki tüm VM 'Leri sorgulamak için Azure Kaynak Grafiği 'ni kullanmaktır. Azure Kaynak Grafiği, Azure kaynakları için abonelikler arasında ölçeklendirerek verimli sorgu özellikleri sağlar. 

``` 
| where type =~ 'Microsoft.Compute/virtualMachines' 
| where properties.virtualMachineScaleSet contains "demo" 
| extend powerState = properties.extended.instanceView.powerState.code 
| project name, resourceGroup, location, powerState 
| order by resourceGroup desc, name desc 
```

Kaynakları [Azure Kaynak Graph](../governance/resource-graph/overview.md) ile sorgulamak, Azure kaynaklarını sorgulamak ve kaynak SAĞLAYıCıSıNA yönelik API çağrılarını en aza indirecek kullanışlı ve verimli bir yoldur. Azure Kaynak Grafiği, yeni veya güncelleştirilmiş kaynakların 60 saniyeye kadar yansıtılmamış, sonuçta tutarlı bir önbellektir. Seçenekleriniz şunlardır:
- Bir kaynak grubundaki veya abonelikteki VM 'Leri listeleyin.
- Aboneliğinizdeki tüm sanal makinelerin örnek görünümünü (hata etki alanı ataması, güç ve sağlama durumları) almak için Genişlet seçeneğini kullanın.
- Tek bir örnek için model ve örnek görünümü almak için VM al API 'SI ve komutlarını kullanın.

### <a name="scale-sets-vm-batch-operations"></a>Ölçek Kümeleri VM Batch işlemleri
Sanal makine ölçek kümesi VM API 'Leri yerine örnekleri başlatmak, durdurmak, yeniden başlatmak, silmek için standart VM komutlarını kullanın. Sanal makine ölçek kümesi VM toplu işlem işlemleri (tümünü Başlat, Tümünü Durdur, Tümünü Durdur, vb.) esnek düzenleme moduyla kullanılmaz. 

### <a name="monitor-application-health"></a>Uygulamanın durumunu izleme 
Uygulama durumu izleme, uygulamanızın, uygulamanızın sağlıklı veya sağlıklı olup olmadığını belirlemede bir sinyal ile Azure sağlamasına izin verir. Azure, sağlıksız olan sanal makine örneklerini otomatik olarak değiştirebilir. Esnek ölçek kümesi örnekleri için, sanal makinede uygulama durumu uzantısını yükleyip yapılandırmanız gerekir. Tekdüzen ölçek kümesi örnekleri için, uygulama durumu uzantısını kullanabilir veya Azure Load Balancer özel durum araştırmasıyla sistem durumunu ölçebilirsiniz. 

### <a name="list-scale-sets-vm-api-changes"></a>Ölçek Kümeleri VM API değişikliklerini listeleme 
Sanal Makine Ölçek Kümeleri, ölçek kümesine ait örnekleri listelemenize izin verir. Esnek düzenleme ile sanal makine ölçek kümeleri VM komutu, ölçek kümeleri VM kimliklerinin bir listesini sağlar. Daha sonra ölçek kümesinin VM örneğiyle nasıl çalıştığı hakkında daha fazla bilgi edinmek için sanal makine ölçek kümelerini Al VM komutlarını çağırabilirsiniz. VM 'nin tüm ayrıntılarını almak için standart VM Al komutlarını veya [Azure Kaynak grafiğini](../governance/resource-graph/overview.md)kullanın. 

### <a name="retrieve-boot-diagnostics-data"></a>Önyükleme tanılama verilerini al 
Örnek önyükleme tanılama verilerini ve ekran görüntülerini almak için standart VM API 'Lerini ve komutlarını kullanın. Sanal Makine Ölçek Kümeleri VM önyükleme tanılaması API 'Leri ve komutları esnek düzenleme modu örnekleriyle kullanılmaz.

### <a name="vm-extensions"></a>VM uzantıları 
Tekdüzen düzenleme modu örnekleri için hedeflenen uzantılar yerine standart sanal makineler için hedeflenen uzantıları kullanın.


## <a name="a-comparison-of-flexible-uniform-and-availability-sets"></a>Esnek, Tekdüzen ve kullanılabilirlik kümelerinin karşılaştırması 
Aşağıdaki tabloda esnek düzenleme modu, Tekdüzen düzenleme modu ve kullanılabilirlik kümeleri özelliklerine göre karşılaştırılmaktadır.

| Özellik | Esnek düzenleme (Önizleme) tarafından desteklenir | Tekdüzen düzenleme tarafından desteklenir (genel kullanılabilirlik) | AvSets tarafından destekleniyor (genel kullanılabilirlik) |
|-|-|-|-|
|         Sanal makine türü  | Standart Azure IaaS VM (Microsoft. COMPUTE/virtualmachines)  | Ölçek kümesi belirli VM 'Ler (Microsoft. COMPUTE/virtualmachinescalesets/virtualmachines)  | Standart Azure IaaS VM (Microsoft. COMPUTE/virtualmachines)  |
|         Desteklenen SKU 'Lar  |            D serisi, E serisi, F serisi, bir seri, B serisi, Intel, AMD  |            Tüm SKU 'Lar  |            Tüm SKU 'Lar  |
|         Kullanılabilirlik Alanları  |            İsteğe bağlı olarak tüm örnekleri tek bir kullanılabilirlik alanında belirtin |            1, 2 veya 3 kullanılabilirlik bölgesindeki örnekleri belirtin  |            Desteklenmez  |
|         VM, NIC 'ler, diskler üzerinde tam denetim  |            Yes  |            Sanal Makine Ölçek Kümeleri VM API 'SI ile sınırlı denetim  |            Yes  |
|         Otomatik ölçeklendirme  |            Hayır  |            Yes  |            Hayır  |
|         VM 'yi belirli bir hata etki alanına atama  |            Yes  |             Hayır   |            Hayır  |
|         Sanal makine örneklerini silerken NIC 'Leri ve diskleri kaldırma  |            Hayır  |            Yes  |            Hayır  |
|         Yükseltme Ilkesi (VM Ölçek Kümeleri) |            No  |            Otomatik, kayan, El Ile  |            Yok  |
|         Otomatik işletim sistemi güncelleştirmeleri (VM Ölçek Kümeleri) |            Hayır  |            Yes  |            Yok  |
|         Konuk güvenlik düzeltme eki uygulama  |            Yes  |            Hayır  |            Yes  |
|         Bildirimleri sonlandır (VM Ölçek Kümeleri) |            Hayır  |            Yes  |            Yok  |
|         Örnek onarımı (VM Ölçek Kümeleri) |            Hayır  |            Yes   |            Yok  |
|         Hızlandırılmış ağ iletişimi  |            Yes  |            Yes  |            Yes  |
|         Spot örnekleri ve fiyatlandırma   |            Evet, hem nokta hem de normal öncelik örneklerine sahip olabilirsiniz  |            Evet, örneklerin tümü nokta veya hepsi normal olmalıdır  |            Hayır, yalnızca normal öncelik örnekleri  |
|         İşletim sistemlerini karıştır  |            Evet, Linux ve Windows aynı esnek ölçek kümesinde bulunabilir |            Hayır, örnek aynı işletim sistemidir  |               Evet, Linux ve Windows aynı esnek ölçek kümesinde bulunabilir |
|         Uygulama durumunu izleme  |            Uygulama durumu uzantısı  |            Uygulama durumu uzantısı veya Azure yük dengeleyici araştırması  |            Uygulama durumu uzantısı  |
|         UltraSSD diskler   |            Yes  |            Evet, yalnızca bölgesel dağıtımları için  |            No  |
|         InfiniBand   |            No  |            Evet, yalnızca tek yerleşim grubu  |            Yes  |
|         Yazma Hızlandırıcısı   |            Hayır  |            Yes  |            Yes  |
|         Yakınlık yerleştirme grupları   |            Yes  |            Yes  |            Yes  |
|         Azure ayrılmış Konakları   |            Hayır  |            Yes  |            Yes  |
|         Temel SLB   |            Hayır  |            Yes  |            Yes  |
|         Standart SKU 'YU Azure Load Balancer |            Yes  |            Yes  |            Yes  |
|         Application Gateway  |            Hayır  |            Yes  |            Yes  |
|         Bakım denetimi   |            Hayır  |            Yes  |            Yes  |
|         Küme içindeki VM 'Leri Listele  |            Yes  |            Yes  |            Evet, AvSet 'teki VM 'Leri Listele  |
|         Azure uyarıları  |            Hayır  |            Yes  |            Yes  |
|         VM öngörüleri  |            Hayır  |            Yes  |            Yes  |
|         Azure Backup  |            Yes  |            Yes  |            Yes  |
|         Azure Site Recovery  |     Hayır  |            Hayır  |            Yes  |
|         Mevcut VM 'yi gruba ekle/kaldır  |            Hayır  |            Hayır  |            Hayır  | 


## <a name="register-for-flexible-orchestration-mode"></a>Esnek düzenleme moduna kaydolun
Esnek düzenleme modunda sanal makine ölçek kümelerini dağıtabilmeniz için önce önizleme özelliği aboneliğinizi kaydetmeniz gerekir. Kaydın tamamlanması birkaç dakika sürebilir. Kaydolmak için aşağıdaki Azure PowerShell veya Azure CLı komutlarını kullanabilirsiniz.

### <a name="azure-portal"></a>Azure Portalı
Esnek düzenleme modunda bir ölçek kümesi oluşturmak istediğiniz aboneliğin ayrıntılar sayfasına gidin ve menüden Önizleme özellikleri ' ni seçin. Etkinleştirmek için iki Orchestrator özelliğini seçin: _VMOrchestratorSingleFD_ ve _VMOrchestratorMultiFD_ ve Kaydet düğmesine basın. Özellik kaydı 15 dakikaya kadar sürebilir.

![Özellik kaydı.](https://user-images.githubusercontent.com/157768/110361543-04d95880-7ff5-11eb-91a7-2e98f4112ae0.png)

Aboneliğiniz için özellikler kaydedildikten sonra, değişikliği işlem kaynak sağlayıcısına yayarak katılım işlemini doldurun. Aboneliğiniz için kaynak sağlayıcıları sekmesine gidin, Microsoft. COMPUTE ' i seçin ve yeniden Kaydet ' e tıklayın.

![Yeniden Kaydet](https://user-images.githubusercontent.com/157768/110362176-cd1ee080-7ff5-11eb-8cc8-36aa967e267a.png)


### <a name="azure-powershell"></a>Azure PowerShell 
Aboneliğiniz için Önizlemeyi etkinleştirmek üzere [register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) cmdlet 'ini kullanın. 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMOrchestratorSingleFD -ProviderNamespace Microsoft.Compute  
```

Özellik kaydı 15 dakikaya kadar sürebilir. Kayıt durumunu denetlemek için: 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute 
```

Aboneliğiniz için özellik kaydedildikten sonra, değişikliği işlem kaynak sağlayıcısına yayarak katılım işlemini doldurun. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

### <a name="azure-cli-20"></a>Azure CLI 2.0 
Aboneliğiniz için Önizlemeyi etkinleştirmek üzere [az Feature Register](/cli/azure/feature#az_feature_register) kullanın. 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD 
```

Özellik kaydı 15 dakikaya kadar sürebilir. Kayıt durumunu denetlemek için: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD 
```

Aboneliğiniz için özellik kaydedildikten sonra, değişikliği işlem kaynak sağlayıcısına yayarak katılım işlemini doldurun. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```


## <a name="get-started-with-flexible-orchestration-mode"></a>Esnek düzenleme moduna çalışmaya başlama

Ölçek kümeleriniz için Azure portal, Azure CLı, Terrayform veya REST API aracılığıyla esnek düzenleme modu ile çalışmaya başlayın.

### <a name="azure-portal"></a>Azure portalı

Azure portal aracılığıyla esnek düzenleme modunda bir sanal makine ölçek kümesi oluşturun.

1. [Azure Portal](https://portal.azure.com)oturum açın.
1. Arama çubuğunda, **sanal makine ölçek kümelerini** arayıp seçin. 
1. **Sanal Makine Ölçek Kümeleri** sayfasında **Oluştur** ' u seçin.
1. **Sanal makine ölçek kümesi oluştur** sayfasında, **düzenleme** bölümünü görüntüleyin.
1. **Düzenleme modu** için **Esnek** seçeneğini belirleyin.
1. **Hata etki alanı sayısını** ayarlayın.
1. Ölçek kümesini oluşturmayı tamamlayın. Ölçek kümesi oluşturma hakkında daha fazla bilgi için, bkz. [Azure Portal ölçek kümesi oluşturma](quick-create-portal.md#create-virtual-machine-scale-set) .

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/portal-create-orchestration-mode-flexible.png" alt-text="Ölçek kümesi oluştururken portalda düzenleme modu":::

Ardından, esnek düzenleme modundaki ölçek kümesine bir sanal makine ekleyin.

1. Arama çubuğunda, **sanal makineleri** arayın ve seçin.
1. **Sanal makineler** sayfasında **Ekle** ' yi seçin.
1. **Temel bilgiler** sekmesinde, **örnek ayrıntıları** bölümünü görüntüleyin.
1. **Kullanılabilirlik seçeneklerinde** ölçek KÜMESINI seçerek VM 'nizi esnek düzenleme modundaki ölçek kümesine ekleyin. Sanal makineyi aynı bölgedeki, bölgedeki ve kaynak grubundaki bir ölçek kümesine ekleyebilirsiniz.
1. Sanal makinenizi oluşturmayı tamamlayın. 

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/vm-portal-orchestration-mode-flexible.png" alt-text="Esnek düzenleme modu ölçek kümesine VM ekleme":::


### <a name="azure-cli-20"></a>Azure CLI 2.0
Azure CLı ile esnek bir sanal makine ölçek kümesi oluşturun. Aşağıdaki örnek, hata etki alanı sayısının 3 olarak ayarlandığı, bir sanal makinenin oluşturulduğu ve esnek ölçek kümesine eklendiği esnek ölçek kümesi oluşturmayı gösterir. 

```azurecli-interactive
vmssflexname="my-vmss-vmssflex"  
vmname="myVM"  
rg="my-resource-group"  

az group create -n "$rg" -l $location  
az vmss create -n "$vmssflexname" -g "$rg" -l $location --orchestration-mode flexible --platform-fault-domain-count 3  
az vm create -n "$vmname" -g "$rg" -l $location --vmss $vmssflexname --image UbuntuLTS 
```

### <a name="terraform"></a>Terraform
Terrayform ile esnek bir sanal makine ölçek kümesi oluşturun. Bu işlem **Terupform Azurerd sağlayıcı v 2.15.0** veya üstünü gerektirir. Aşağıdaki parametrelere göz önünde edin:
- Hiçbir bölge belirtilmediğinde, `platform_fault_domain_count` bölgeye bağlı olarak 1, 2 veya 3 olabilir.
- Bir bölge belirtildiğinde, `the fault domain count` 1 olabilir.
- `single_placement_group` parametre `false` Esnek sanal makine ölçek kümeleri için olmalıdır.
- Bölgesel dağıtım yapıyorsanız, belirtmeniz gerekmez `zones` .

```terraform
resource "azurerm orchestrated_virtual_machine_scale_set" "tf_vmssflex" {
name = "tf_vmssflex"
location = azurerm_resource_group.myterraformgroup.location
resource_group_name = azurerm_resource_group.myterraformgroup.name
platform_fault_domain_count = 1
single_placement_group = false
zones = ["1"]
}
```


### <a name="rest-api"></a>REST API

1. Boş bir ölçek kümesi oluşturun. Aşağıdaki parametreler gereklidir:
    - API sürüm 2019-12-01 (veya üstü) 
    - `false`Esnek ölçek kümesi oluşturulurken tek yerleşim grubu olmalıdır

    ```json
    {
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('virtualMachineScaleSetName')]",
    "apiVersion": "2019-12-01",
    "location": "[parameters('location')]",
    "properties": {
        "singlePlacementGroup": false,
        "platformFaultDomainCount": "[parameters('virtualMachineScaleSetPlatformFaultDomainCount')]"
        },
    "zones": "[variables('selectedZone')]"
    }
    ```

2. Ölçek kümesine sanal makine ekleyin.
    1. `virtualMachineScaleSet`Özelliği daha önce oluşturduğunuz ölçek kümesine atayın. VM oluşturma sırasında özelliğini belirtmeniz gerekir `virtualMachineScaleSet` . 
    1. Aynı anda birden çok VM oluşturmak için **Copy ()** Azure Resource Manager şablon işlevini kullanabilirsiniz. Azure Resource Manager şablonlarda [kaynak yinelemesine](../azure-resource-manager/templates/copy-resources.md#iteration-for-a-child-resource) bakın. 

    ```json
    {
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(parameters('virtualMachineNamePrefix'), copyIndex(1))]",
    "apiVersion": "2019-12-01",
    "location": "[parameters('location')]",
    "copy": {
        "name": "VMcopy",
        "count": "[parameters('virtualMachineCount')]"
        },
    "dependsOn": [
        "
        [resourceID('Microsoft.Compute/virtualMachineScaleSets', parameters('virtualMachineScaleSetName'))]",
        "
        [resourceID('Microsoft.Storage/storageAccounts', variables('diagnosticsStorageAccountName'))]",
        "
        [resourceID('Microsoft.Network/networkInterfaces', concat(parameters('virtualMachineNamePrefix'), copyIndex(1), '-NIC1'))]"
        ],
    "properties": {
        "virtualMachineScaleSet": {
            "id": "[resourceID('Microsoft.Compute/virtualMachineScaleSets', parameters('virtualMachineScaleSetName'))]"
        }
    }
    ```

Tam bir örnek için bkz. [Azure hızlı başlangıç](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-vmss-flexible-orchestration-mode) .


## <a name="frequently-asked-questions"></a>Sık sorulan sorular

**Esnek düzenleme ne kadar ölçek destekler?**

Esnek düzenleme modundaki bir ölçek kümesine en fazla 1000 VM ekleyebilirsiniz.

**Esnek düzenleme ile kullanılabilirlik, kullanılabilirlik kümeleri veya Tekdüzen düzenleme ile nasıl karşılaştırılır?**

| Kullanılabilirlik özniteliği  | Esnek düzenleme  | Tekdüzen düzenleme  | Kullanılabilirlik Kümeleri  |
|-|-|-|-|
| Kullanılabilirlik alanları arasında dağıtma  | Hayır  | Yes  | Hayır  |
| Bölge içindeki hata etki alanı kullanılabilirliği garantisi  | Evet, en fazla 1000 örnek, bölgedeki en fazla 3 hata etki alanına yayılabilecek. En fazla hata etki alanı sayısı bölgeye göre değişir  | Evet, en fazla 100 örnek  | Evet, en fazla 200 örnek  |
| Yerleştirme grupları  | Esnek mod her zaman birden çok yerleştirme grubu kullanır (Tekplacementgroup = false)  | Tek yerleşim grubu veya birden çok yerleştirme grubu seçebilirsiniz | Yok  |
| Güncelleme etki alanları  | Hiçbiri, bakım veya ana bilgisayar güncelleştirmeleri hata etki alanına göre gerçekleştirilen hata etki alanı  | En fazla 5 güncelleştirme etki alanı  | En fazla 20 güncelleştirme etki alanı  |


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Esnek düzenleme ile ölçek kümeleri sorunlarını giderme
Sorun giderme senaryonuz için doğru çözümü bulun. 

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**Neden:** Abonelik esnek düzenleme modu genel önizlemesi için kayıtlı değil. 

**Çözüm:** Esnek düzenleme modu genel önizlemesine kaydolmak için yukarıdaki yönergeleri izleyin. 

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**Neden:** `platformFaultDomainCount` Parametresi, seçilen bölge veya bölge için geçersiz. 

**Çözüm:** Geçerli bir `platformFaultDomainCount` değer seçmelisiniz. Bölgesel dağıtımları için maksimum `platformFaultDomainCount` değer 1 ' dir. Hiçbir bölgenin belirtilmediği bölgesel dağıtımlar için, en büyük değer `platformFaultDomainCount` bölgeye göre değişir. Bölge başına en fazla hata etki alanı sayısını öğrenmek için bkz. [betikler Için VM 'lerin kullanılabilirliğini yönetme](../virtual-machines/availability.md) . 

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**Neden:** Bir veya daha fazla sanal makineyle ilişkili esnek düzenleme modundaki bir ölçek kümesini silmeye çalışırken. 

**Çözüm:** Esnek düzenleme modundaki ölçek kümesiyle ilişkili tüm sanal makineleri silin, ardından ölçek kümesini silebilirsiniz.

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**Neden:** Abonelik esnek düzenleme modu önizlemesi için kaydedilir; Ancak, `singlePlacementGroup` parametresi *true* olarak ayarlanır. 

**Çözüm:** , `singlePlacementGroup` *False* olarak ayarlanmalıdır. 


## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Uygulamanızı ölçek kümesi üzerinde dağıtmayı öğrenin.](virtual-machine-scale-sets-deploy-app.md)
