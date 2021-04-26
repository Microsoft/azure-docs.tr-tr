---
title: Azure spot sanal makineleri kullanan bir ölçek kümesi oluşturma
description: Maliyetlerden tasarruf etmek için Azure spot sanal makinelerini kullanan Azure sanal makine ölçek kümeleri oluşturmayı öğrenin.
author: JagVeerappan
ms.author: jagaveer
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 61bb87d84b96f988ae065a70b85d445fc8b96ccf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762956"
---
# <a name="azure-spot-virtual-machines-for-virtual-machine-scale-sets"></a>Sanal Makine Ölçek Kümeleri için Azure spot sanal makineleri 

Ölçek kümelerinde Azure spot sanal makineleri kullanmak, önemli maliyet tasarruflarından kullanılmamış kapasitemizden yararlanmanızı sağlar. Azure 'un kapasiteyi yeniden sağlaması gerektiğinde Azure altyapısı, Azure spot sanal makine örneklerini çıkarır. Bu nedenle, Azure spot sanal makine örnekleri toplu işleme işleri, geliştirme/test ortamları, büyük işlem iş yükleri ve daha fazlası gibi kesintileri işleyebilen iş yükleri için mükemmeldir.

Kullanılabilir kapasite miktarı boyut, bölge, günün saati ve daha fazlası temelinde farklılık gösterebilir. Azure spot sanal makine örneklerini ölçek kümelerinde dağıttığınızda Azure, örneği yalnızca kullanılabilir kapasite varsa ayırır, ancak bu örnekler için SLA yoktur. Azure spot sanal makine ölçek kümesi, tek bir hata etki alanında dağıtılır ve yüksek kullanılabilirlik garantisi vermez.


## <a name="pricing"></a>Fiyatlandırma

Azure spot sanal makine örnekleri için fiyatlandırma, bölge ve SKU temel alınarak değişkendir. Daha fazla bilgi için bkz. [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) ve [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/)fiyatlandırması. 


Değişken fiyatlandırmayla, en fazla beş ondalık basamak kullanarak ABD Doları (USD) cinsinden maksimum fiyat ayarlama seçeneğiniz vardır. Örneğin, değer, `0.98765` saat başına $0,98765 ABD Doları olan en yüksek fiyat olacaktır. En yüksek fiyatı olacak şekilde ayarlarsanız `-1` , örnek fiyata göre çıkarılmaz. Örnek fiyatı, Azure spot sanal makinesi için geçerli fiyat veya kapasite ve kota kullanılabilir olduğu sürece daha az olan standart bir örnek için fiyat olacaktır.


## <a name="limitations"></a>Sınırlamalar

Azure spot sanal makineler için aşağıdaki boyutlar desteklenmez:
 - B serisi
 - Her boyuttaki promosyon sürümleri (dv2, NV, NC, H promosyon boyutları gibi)

Azure spot sanal makinesi, Microsoft Azure Çin 21Vianet dışında herhangi bir bölgeye dağıtılabilir.

<a name="channel"></a>

Şu [teklif türleri](https://azure.microsoft.com/support/legal/offer-details/) Şu anda destekleniyor:

-   Kurumsal Anlaşma
-   Kullandıkça Öde teklifi kodu (003P)
-   Sponsorlu (0036P ve 0136P)
- Bulut hizmeti sağlayıcısı (CSP) için [Iş Ortağı Merkezi](/partner-center/azure-plan-get-started) 'ne bakın veya doğrudan iş ortağınızla iletişim kurun.

## <a name="eviction-policy"></a>Çıkarma ilkesi

Azure spot sanal makineleri kullanarak bir ölçek kümesi oluştururken, çıkarma ilkesini *serbest bırakma* (varsayılan) veya *silme* işlemleri için ayarlayabilirsiniz. 

*Serbest bırakma* ilkesi, çıkarılan örnekleri yeniden dağıtmanıza izin vererek, çıkarılan örneklerinizi durdurulmuş serbest bırakılmış duruma kaydırır. Ancak, ayırmanın başarılı olacağını garanti etmez. Serbest bırakılmış VM 'Ler, ölçek kümesi örneği kotanıza göre sayılır ve temel disklerinizin ücreti alınır. 

Örneklerin çıkarıldıklarında silinmesini isterseniz, çıkarma ilkesini *silinmek* üzere ayarlayabilirsiniz. Çıkarma ilkesi silinmek üzere ayarlandığında, ölçek kümesi örnek sayısı özelliğini artırarak yeni VM 'Ler oluşturabilirsiniz. Çıkarılan VM 'Ler, temel disklerle birlikte silinir ve bu nedenle depolama alanı için ücretlendirilmeyecektir. Ayrıca, çıkarılan VM 'Leri otomatik olarak denemek ve dengelemek için ölçek kümelerinin otomatik ölçeklendirme özelliğini de kullanabilirsiniz; ancak, ayırmanın başarılı olacağını garanti etmez. Disklerinizin maliyetini önlemek ve kota limitlerine ulaşmak üzere çıkarma ilkesini silinmek üzere ayarladığınızda Azure spot sanal makine ölçek kümelerinde yalnızca otomatik ölçeklendirme özelliğini kullanmanız önerilir. 

Kullanıcılar [Azure zamanlanan olaylar](../virtual-machines/linux/scheduled-events.md)aracılığıyla VM içi bildirimler almayı kabul edebilir. Bu, VM 'leriniz çıkarıldıktan sonra herhangi bir işi tamamlamak ve çıkarma öncesi görevleri gerçekleştirmek için 30 saniyelik bir işlem yapmanız durumunda size bildirir. 

<a name="bkmk_try"></a>
## <a name="try--restore-preview"></a>& geri yüklemeyi dene (Önizleme)

Bu yeni platform düzeyi özelliği, hedef örnek sayısını korumak için otomatik olarak bir ölçek kümesi içinde çıkarılan Azure spot sanal makine örneklerini geri yüklemeyi denemek üzere AI 'yi kullanacaktır. 

> [!IMPORTANT]
> & geri yükleme işlemi şu anda genel önizlemede.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

& geri yükleme avantajlarını deneyin:
- Kapasite nedeniyle çıkarılan Azure spot sanal makinelerini geri yükleme girişimleri.
- Geri yüklenen Azure spot sanal makinelerinin, daha uzun bir süre boyunca bir kapasite tetikleme çıkarmasının daha düşük bir olasılığı olması beklenir.
- , Bir Azure spot sanal makinesinin kullanım süresini iyileştirir, böylelikle iş yükleri daha uzun bir süre çalışır.
- , Sanal makine ölçek kümelerinin, Kullandıkça Öde VM 'Leri için zaten mevcut olan hedef sayısı özelliğini koruyun 'e benzer şekilde, Azure spot sanal makinelerinin hedef sayısını korumasına yardımcı olur.

[Otomatik ölçeklendirme](virtual-machine-scale-sets-autoscale-overview.md)kullanan ölçek kümelerinde & geri yükleme işlemi devre dışı bırakıldı. Ölçek kümesindeki sanal makinelerin sayısı otomatik ölçeklendirme kuralları tarafından çalıştırılır.

### <a name="register-for-try--restore"></a>TRY & restore için kaydolun

& geri yüklemeyi dene özelliğini kullanabilmeniz için aboneliğinizi önizleme için kaydetmeniz gerekir. Kaydın tamamlanması birkaç dakika sürebilir. Özellik kaydını gerçekleştirmek için Azure CLı veya PowerShell kullanabilirsiniz.


**CLI’yi kullanma**

Aboneliğiniz için Önizlemeyi etkinleştirmek üzere [az Feature Register](/cli/azure/feature#az_feature_register) kullanın. 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SpotTryRestore 
```

Özellik kaydı 15 dakikaya kadar sürebilir. Kayıt durumunu denetlemek için: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SpotTryRestore 
```

Aboneliğiniz için özellik kaydedildikten sonra, değişikliği işlem kaynak sağlayıcısına yayarak katılım işlemini doldurun. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```
**PowerShell kullanma** 

Aboneliğiniz için Önizlemeyi etkinleştirmek üzere [register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) cmdlet 'ini kullanın. 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

Özellik kaydı 15 dakikaya kadar sürebilir. Kayıt durumunu denetlemek için: 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

Aboneliğiniz için özellik kaydedildikten sonra, değişikliği işlem kaynak sağlayıcısına yayarak katılım işlemini doldurun. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

## <a name="placement-groups"></a>Yerleştirme grupları

Yerleştirme grubu, kendi hata etki alanları ve yükseltme etki alanları ile Azure kullanılabilirlik kümesine benzer bir yapıdır. Varsayılan olarak, bir ölçek kümesi en fazla 100 VM boyutuna sahip tek bir yerleştirme grubundan oluşur. Çağrılan ölçek kümesi özelliği `singlePlacementGroup` *false* olarak ayarlandıysa, ölçek kümesi birden çok yerleştirme grubundan oluşabilir ve 0-1000 VM aralığı vardır. 

> [!IMPORTANT]
> , HPC ile InfiniBand kullanmıyorsanız, `singlePlacementGroup` bölge veya bölge genelinde daha iyi ölçekleme için birden çok yerleştirme grubunu etkinleştirmek üzere ölçek kümesi özelliğini *false* olarak ayarlamanız önemle önerilir. 

## <a name="deploying-azure-spot-virtual-machines-in-scale-sets"></a>Ölçek kümelerinde Azure spot sanal makineleri dağıtma

Ölçek kümelerinde Azure spot sanal makineleri dağıtmak için yeni *Öncelik* bayrağını *nokta* olarak ayarlayabilirsiniz. Ölçek kümesindeki tüm sanal makineler, spot olarak ayarlanacak. Azure spot sanal makinelerle bir ölçek kümesi oluşturmak için aşağıdaki yöntemlerden birini kullanın:
- [Azure portalı](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure Resource Manager şablonları](#resource-manager-templates)

## <a name="portal"></a>Portal

Azure spot sanal makineleri kullanan bir ölçek kümesi oluşturma işlemi Başlarken [makalesinde](quick-create-portal.md)ayrıntılı olarak aynıdır. Bir ölçek kümesi dağıttığınızda, nokta bayrağını ve çıkarma ilkesini ayarlamayı seçebilirsiniz: ![ Azure spot sanal makineler ile ölçek kümesi oluşturma](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

Azure spot sanal makineler ile bir ölçek kümesi oluşturma işlemi Başlarken [makalesinde](quick-create-cli.md)ayrıntılı olarak aynıdır. '--Priority noktası ' ve Ekle ' yi eklemeniz yeterlidir `--max-price` . Bu örnekte, `-1` Örneğin `--max-price` fiyata göre çıkarılamadığı için kullanırız.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --single-placement-group false \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

Azure spot sanal makineler ile bir ölçek kümesi oluşturma işlemi Başlarken [makalesinde](quick-create-powershell.md)ayrıntılı olarak aynıdır.
' Öncelikli nokta ' ekleyin ve `-max-price` [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig)için bir sağlayın.

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    -max-price -1
```

## <a name="resource-manager-templates"></a>Resource Manager şablonları

Azure spot sanal makineleri kullanan bir ölçek kümesi oluşturma işlemi, [Linux](quick-create-template-linux.md) veya [Windows](quick-create-template-windows.md)için Başlarken makalesinde ayrıntılı olarak aynıdır. 

Azure spot sanal makine şablonu dağıtımları için `"apiVersion": "2019-03-01"` veya sonraki bir sürümünü kullanın. 

`priority`, `evictionPolicy` Ve özelliklerini, `billingProfile` `"virtualMachineProfile":` şablonlarınızın bölümüne ve özelliğini bölümüne ekleyin `"singlePlacementGroup": false,` `"Microsoft.Compute/virtualMachineScaleSets"` :

```json

{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  },
  "properties": {
    "singlePlacementGroup": false,
    }

        "virtualMachineProfile": {
              "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
            },
```

Çıkarıldıktan sonra örneği silmek için `evictionPolicy` parametresini olarak değiştirin `Delete` .


## <a name="simulate-an-eviction"></a>Çıkargı benzetimi yap

Uygulamanızın ani bir çıkarmasına ne kadar iyi yanıt vereceğini test etmek için bir Azure spot sanal makinesinin [çıkarması benzetimi](/rest/api/compute/virtualmachines/simulateeviction) yapabilirsiniz. 

Aşağıdaki bilgilerinizi ile değiştirin: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` , sanal çıkaralma işleminin başarılı olduğu anlamına gelir. 

## <a name="faq"></a>SSS

**S:** Bir Azure spot sanal makine örneği oluşturulduktan sonra standart örnekle aynı mı?

Y **:** Evet, Azure spot sanal makineler için SLA olmaması ve herhangi bir zamanda çıkartılanlar haricinde.


**S:** Ne yapmalı, ancak yine de kapasiteye ihtiyaç duydunuz?

Y **:** Kapasiteye ihtiyacınız varsa Azure spot sanal makineler yerine standart VM 'Ler kullanmanızı öneririz.


**S:** Azure spot sanal makinesi için kota nasıl yönetilir?

Y **:** Azure spot sanal makine örnekleri ve standart örneklerin ayrı kota havuzları olacaktır. Azure spot sanal makine kotası, VM 'Ler ve ölçek kümesi örnekleri arasında paylaşılır. Daha fazla bilgi için bkz. [Azure aboneliği ve hizmet limitleri, kotalar ve kısıtlamalar](../azure-resource-manager/management/azure-subscription-service-limits.md).


**S:** Azure spot sanal makinesi için ek kota isteyebilir miyim?

Y **:** Evet, [Standart kota istek süreci](../azure-portal/supportability/per-vm-quota-requests.md)aracılığıyla Azure spot sanal makineleri için kotayı artırmak üzere isteği gönderebilirsiniz.


**S:** Mevcut ölçek kümelerini Azure spot sanal makine ölçek kümelerine dönüştürebilir miyim?

Y **:** Hayır, bayrak ayarı `Spot` yalnızca oluşturma sırasında desteklenir.


**S:** `low` Düşük öncelikli ölçek kümeleri için kullanılıyorsa, bunun yerine kullanmaya başlamam gerekir `Spot` mi?

Y **:** Şimdilik her ikisi de `low` `Spot` çalışır, ancak kullanmaya geçiş başlatmanız gerekir `Spot` .


**S:** Hem normal VM 'Ler hem de Azure spot sanal makineleri olan bir ölçek kümesi oluşturabilir miyim?

Y **:** Hayır, ölçek kümesi birden fazla öncelik türünü desteklemez.


**S:**  Azure spot sanal makine ölçek kümeleri ile otomatik ölçeklendirmeyi kullanabilir miyim?

Y **:** Evet, Azure spot sanal makine ölçek kümesinde otomatik ölçeklendirme kuralları belirleyebilirsiniz. VM 'niz çıkarıldıysanız, otomatik ölçeklendirme yeni Azure spot sanal makineleri oluşturmayı deneyebilir. Bu kapasitenin da garanti edilmediği unutulmamalıdır. 


**S:**  Otomatik ölçeklendirme, çıkarma ilkeleriyle (serbest bırakma ve silme) çalışır mi?

Y **:** Evet, ancak çıkarma ilkenizi otomatik ölçeklendirme kullanırken silinmek üzere ayarlamanız önerilir. Bunun nedeni, serbest bırakılmış örneklerin ölçek kümesindeki kapasite saymanız ile sayılmasıyla kaynaklanır. Otomatik ölçeklendirme kullanırken, serbest bırakılmış, çıkarılan örnekler nedeniyle hedef örnek sayınıza büyük olasılıkla ulaşmanız gerekir. Ayrıca, ölçeklendirme operasyonlarınız, nokta çıkarmaları tarafından etkilenebilir. Örneğin, sanal makine ölçek kümesi örnekleri, ölçek işlemleri sırasında birden çok spot çıkarmalar nedeniyle en az sayıda küme altına düşecek. 


**S:** Sorularınızı nereden gönderebilirim?

Y **:** Soru- `azure-spot` [cevap A&](/answers/topics/azure-spot.html), sorunuzu gönderebilirsiniz ve etiketleyebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Fiyatlandırma ayrıntıları için [sanal makine ölçek kümesi fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) göz atın.
