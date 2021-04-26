---
title: Windows Server Azure Edition için hotpatch (Önizleme)
description: Windows Server Azure Edition için hotpatch 'in nasıl çalıştığını ve nasıl etkinleştirileceği hakkında bilgi edinin
author: ju-shim
ms.service: virtual-machines
ms.subservice: hotpatch
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1b3fc9f12dfa6ad4edcc120ac7c9592c9435a0e4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830187"
---
# <a name="hotpatch-for-new-virtual-machines-preview"></a>Yeni sanal makineler için hotpatch (Önizleme)

Anında yükleme, yüklemeden sonra yeniden başlatma gerektirmeyen yeni Windows Server Azure sürüm sanal makinelerine (VM 'Ler) Güncelleştirme yüklemenin yeni bir yoludur. Bu makalede, aşağıdaki avantajları içeren Windows Server Azure sürüm VM 'Leri için hotpatch hakkında bilgiler ele alınmaktadır:
* Daha az yeniden başlatmalar ile alt iş yükü etkisi
* Paketler daha hızlı, daha hızlı bir şekilde yüklenir ve Azure Update Manager ile daha kolay bir düzeltme eki düzenlemesi sağlar
* Hotpatch güncelleştirme paketleri, yeniden başlatmadan daha hızlı yüklenen Windows güvenlik güncelleştirmeleri kapsamında daha iyi koruma sağlar

## <a name="how-hotpatch-works"></a>Hotpatch nasıl kullanılır?

Hotpatch, ilk olarak en son birikimli güncelleştirme Windows Update bir temel kurarak çalışmaktadır. Hotpatches, bu taban çizgisinde oluşturulan düzenli aralıklarla (örneğin, ayın ikinci Salı günü) yayımlanır. Hotpatches, yeniden başlatma gerektirmeyen güncelleştirmeleri içerir. Düzenli olarak (üç ayda bir başlayarak) taban çizgisi yeni bir en son toplu güncelleştirmeyle yenilenir.

:::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Hotpatch örnek zamanlaması.":::

İki tür taban çizgisi vardır: **Planlanan temeller** ve **planlanmamış taban çizgileri**.
*  **Planlı taban çizgileri** , arasında hotpatch sürümleriyle birlikte düzenli bir temposunda yayımlanır.  Planlı taban çizgileri, söz konusu ay için karşılaştırılabilir bir _en son toplu güncelleştirmede_ bulunan tüm güncelleştirmeleri içerir ve yeniden başlatma gerektirir.
    * Yukarıdaki örnek zamanlama, bir takvim yılında (diyagramdaki beş toplam) ve sekiz hotpatch sürümünde dört adet planlı temel sürüm göstermektedir.
* **Planlanmamış taban çizgileri** , önemli bir güncelleştirme (örneğin, sıfır günlük bir düzeltme) yayınlandığında ve bu güncelleştirme bir hotpatch olarak yayımlanamadığında serbest bırakılır.  Planlanmamış taban çizgileri yayınlandığında, bu ay içinde planlanmamış bir taban çizgisi olan bir hotpatch sürümü kullanılacaktır.  Planlanmamış taban çizgileri, söz konusu ay için benzer bir _en son toplu güncelleştirmede_ bulunan tüm güncelleştirmeleri de içerir ve ayrıca yeniden başlatma gerektirir.
    * Yukarıdaki örnek zamanlama, bu aylar için hotpatch sürümlerinin yerini alacak iki plansız taban çizgisi gösterir (bir yılda gerçekleşen plansız taban çizgilerinin gerçek sayısı önceden bilinmiyor).

## <a name="regional-availability"></a>Bölgesel kullanılabilirlik
Hotpatch, önizleme aşamasında tüm genel Azure bölgelerinde kullanılabilir. Azure Kamu bölgeleri önizlemede desteklenmez.

## <a name="how-to-get-started"></a>Nasıl kullanmaya başlarım

> [!NOTE]
> Önizleme aşamasında, Azure portal yalnızca [Bu bağlantıyı](https://aka.ms/AzureAutomanageHotPatch)kullanarak kullanmaya başlayabilir.

Yeni bir VM 'de hotpatch kullanmaya başlamak için şu adımları izleyin:
1.  Önizlemeyi erişimi etkinleştir
    * Abonelik başına bir kerelik önizleme erişimi etkinleştirme gerekir.
    * Önizleme erişimi, aşağıdaki bölümde açıklandığı gibi API, PowerShell veya CLı aracılığıyla etkinleştirilebilir.
1.  Azure portal VM oluşturma
    * Önizleme sırasında [Bu bağlantıyı](https://aka.ms/AzureAutomanageHotPatch)kullanmaya başlamanıza gerek duyarsınız.
1.  VM bilgilerini sağlayın
    * Görüntü açılan menüsünde _Windows Server 2019 Datacenter: Azure sürümünün_ seçildiğinden emin olun.
    * Yönetim sekmesi adımında, ' Konuk işletim sistemi güncelleştirmeleri ' bölümüne gidin. Düzeltme Eki açık olarak ayarlanır ve düzeltme eki yüklemesi, Azure tarafından düzenlenen düzeltme eki uygulama olarak görüntülenir.
    * En Iyi VM uygulamalarını oto Yönet varsayılan olarak etkinleştirilecek
1. Yeni VM 'nizi oluşturma

## <a name="enabling-preview-access"></a>Önizleme erişimini etkinleştirme

### <a name="rest-api"></a>REST API

Aşağıdaki örnekte, aboneliğiniz için önizlemenin nasıl etkinleştirileceği açıklanmaktadır:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/ InGuestHotPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

Özellik kaydı 15 dakikaya kadar sürebilir. Kayıt durumunu denetlemek için:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestHotPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```

Aboneliğiniz için özellik kaydedildikten sonra, değişikliği işlem kaynak sağlayıcısına yayarak katılım işlemini doldurun.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell

```Register-AzProviderFeature```Aboneliğiniz için Önizlemeyi etkinleştirmek üzere cmdlet 'ini kullanın.

``` PowerShell
Register-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Özellik kaydı 15 dakikaya kadar sürebilir. Kayıt durumunu denetlemek için:

``` PowerShell
Get-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Aboneliğiniz için özellik kaydedildikten sonra, değişikliği işlem kaynak sağlayıcısına yayarak katılım işlemini doldurun.

``` PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI

```az feature register```Aboneliğiniz için Önizlemeyi etkinleştirmek üzere kullanın.

```
az feature register --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Özellik kaydı 15 dakikaya kadar sürebilir. Kayıt durumunu denetlemek için:
```
az feature show --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Aboneliğiniz için özellik kaydedildikten sonra, değişikliği işlem kaynak sağlayıcısına yayarak katılım işlemini doldurun.

```
az provider register --namespace Microsoft.Compute
```

## <a name="patch-installation"></a>Düzeltme eki yüklemesi

Önizleme sırasında, [OTOMATIK VM Konuk düzeltme eki uygulama](../virtual-machines/automatic-vm-guest-patching.md) _Windows Server 2019 Datacenter: Azure Edition_ Ile oluşturulan tüm VM 'ler için otomatik olarak etkinleştirilir. Otomatik VM Konuk düzeltme eki uygulama ile etkin:
* Kritik veya güvenlik olarak sınıflandırılan yamalar sanal makineye otomatik olarak indirilir ve uygulanır.
* Düzeltme ekleri, VM 'nin saat diliminde yoğun olmayan saatlerde uygulanır.
* Düzeltme Eki düzenleme Azure tarafından yönetilir ve düzeltme ekleri, [kullanılabilirlik-ilk ilkelere](../virtual-machines/automatic-vm-guest-patching.md#availability-first-patching)göre uygulanır.
* Platform sistem durumu sinyalleriyle belirlenen sanal makine sistem durumu, düzeltme eki uygulama başarısızlıklarını algılamak için izlenir.

### <a name="how-does-automatic-vm-guest-patching-work"></a>Otomatik VM Konuk düzeltme eki uygulama nasıl çalışır?

VM 'de [OTOMATIK VM Konuk düzeltme eki uygulama](../virtual-machines/automatic-vm-guest-patching.md) etkinleştirildiğinde, kullanılabilir kritik ve güvenlik düzeltme ekleri otomatik olarak indirilir ve uygulanır. Bu işlem, yeni düzeltme noktaları serbest bırakıldığında otomatik olarak her ay açılır. Düzeltme Eki değerlendirmesi ve yükleme otomatiktir ve işlem VM 'nin gerektiği şekilde yeniden başlatılmasını içerir.

_Windows Server 2019 Datacenter: Azure Edition_ VM 'Lerinde hotpatch etkinken, aylık güvenlik güncelleştirmelerinin çoğu yeniden başlatma gerektirmeyen hotpatches olarak sunulur. Planlı veya planlanmamış temel aylarda gönderilen en son toplu güncelleştirmeler VM yeniden başlatmaları gerektirir. Ayrıca, sanal makine yeniden başlatmaları gerektirebilecek ek kritik veya güvenlik yamaları düzenli olarak kullanılabilir.

VM, bu VM 'ye yönelik uygun düzeltme eklerini tespit etmek üzere 30 günlük süre içinde her gün otomatik olarak değerlendirilir. Bu otomatik değerlendirme, eksik olan tüm düzeltme eklerinin olası en erken fırsatta bulunmasını sağlar.

Düzeltme ekleri, aylık düzeltme sürümlerinin 30 gün içinde, [önce kullanılabilirlik-ilk ilkelere](../virtual-machines/automatic-vm-guest-patching.md#availability-first-patching)göre yüklenir. Düzeltme ekleri, VM 'nin saat dilimine bağlı olarak VM için yalnızca yoğun olmayan saatlerde yüklenir. Yamaların otomatik olarak yüklenmesi için sanal makinenin yoğun olmayan saatlerde çalışıyor olması gerekir. Bir sanal makine düzenli bir değerlendirme sırasında kapatılmışsa, VM değerlendirilir ve sanal makine açıldığında bir sonraki dönemsel değerlendirme sırasında geçerli düzeltme ekleri otomatik olarak yüklenir. Sonraki düzenli değerlendirme genellikle birkaç gün içinde gerçekleşir.

Tanım güncelleştirmeleri ve kritik veya güvenlik olarak sınıflandırılmayan diğer düzeltme ekleri otomatik VM Konuk düzeltme eki uygulama aracılığıyla yüklenmez.

## <a name="understanding-the-patch-status-for-your-vm"></a>VM 'niz için düzeltme eki durumunu anlama

SANAL makinenizin düzeltme eki durumunu görüntülemek için, Azure portal sanal makinenizin **Konuk + ana bilgisayar güncelleştirmeleri** bölümüne gidin. **Konuk işletim sistemi güncelleştirmeleri** bölümünde, sanal makinenizin en son düzeltme eki durumunu görüntülemek Için ' hotpatch 'e git (Önizleme) ' seçeneğine tıklayın.

Bu ekranda, sanal makinenizin hotpatch durumunu görürsünüz. Ayrıca, VM 'niz için yüklü olmayan herhangi bir düzeltme eki olup olmadığını da inceleyebilirsiniz. Yukarıdaki ' düzeltme eki yükleme ' bölümünde açıklandığı gibi, tüm güvenlik ve kritik güncelleştirmeler [OTOMATIK VM Konuk düzeltme eki uygulama](../virtual-machines/automatic-vm-guest-patching.md) ve ek eylem gereklı olmadan sanal makinenize otomatik olarak yüklenir. Diğer güncelleştirme sınıflandırmalarına sahip düzeltme ekleri otomatik olarak yüklenmez. Bunun yerine, ' güncelleştirme uyumluluğu ' sekmesi altındaki kullanılabilir düzeltme ekleri listesinde görüntülenebilir. Ayrıca, ' güncelleştirme geçmişi ' aracılığıyla sanal makinenizde güncelleştirme dağıtımlarının geçmişini görüntüleyebilirsiniz. Son 30 günden itibaren güncelleştirme geçmişi, düzeltme eki yükleme ayrıntılarıyla birlikte görüntülenir.


:::image type="content" source="media\automanage-hotpatch\hotpatch-management-ui.png" alt-text="Hotpatch yönetimi.":::

Otomatik VM Konuk düzeltme eki uygulama ile, VM 'niz düzenli aralıklarla ve kullanılabilir güncelleştirmeler için otomatik olarak değerlendirilir. Bu düzenli değerlendirmeler, kullanılabilir düzeltme eklerinin algılanarak emin olmanızı sağlamaktır. En son değerlendirme saati de dahil olmak üzere, yukarıdaki güncelleştirmeler ekranında değerlendirmenin sonuçlarını görüntüleyebilirsiniz. Ayrıca, ' Şimdi değerlendir ' seçeneğini kullanarak istediğiniz zaman VM 'niz için isteğe bağlı bir yama değerlendirmesi tetiklemeyi seçebilirsiniz ve değerlendirme tamamlandıktan sonra sonuçları gözden geçirin.

İsteğe bağlı değerlendirmede benzer şekilde, ' güncelleştirmeleri şimdi yüklensin ' seçeneğini kullanarak VM 'niz için isteğe bağlı düzeltme ekleri de yükleyebilirsiniz. Burada, tüm güncelleştirmeleri belirli düzeltme eki sınıflandırmalarının altına yüklemeyi tercih edebilirsiniz. Ayrıca, bireysel Bilgi Bankası makalelerinin bir listesini sunarak dahil edilecek veya hariç tutulacak güncelleştirmeleri de belirtebilirsiniz. İsteğe bağlı düzeltme ekleri, kullanılabilirlik ilk ilkeleri kullanılarak yüklenmez ve güncelleştirme yüklemesi için daha fazla yeniden başlatma ve VM kapalı kalma süresi gerektirebilir.

## <a name="supported-updates"></a>Desteklenen Güncelleştirmeler

Hotpatch, Windows güvenlik güncelleştirmelerini içerir ve normal (hotpatch olmayan) Windows Update kanalında verilen güvenlik güncelleştirmelerinin içeriğiyle eşlik sağlar.

Hotpatch etkin bir Windows Server Azure sürüm VM 'si çalıştırmanın bazı önemli noktaları vardır. Hotpatch programına dahil olmayan güncelleştirmeleri yüklemek için yeniden başlatmalar hala gereklidir. Yeni bir taban çizgisi yüklendikten sonra, yeniden başlatmalar de düzenli olarak gereklidir. Bu yeniden başlatmalar, en son toplu güncelleştirmede yer alan güvenlikle ilgili olmayan düzeltme ekleriyle VM 'yi eşitlenmiş halde tutar.
* Şu anda hotpatch programına dahil edilmeyen düzeltme ekleri, Windows için yayınlanan güvenlikle ilgili olmayan güncelleştirmeleri ve Windows dışı güncelleştirmeleri (.NET yamaları gibi) içerir.  Bu tür düzeltme eklerinin temel bir ay boyunca yüklenmesi gerekir ve yeniden başlatma gerekir.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="what-is-hotpatching"></a>Anında düzeltme nedir?

* Anında Düzeltme, Windows Server 2019 Datacenter 'a Güncelleştirme yüklemenin yeni bir yoludur: Azure 'da, yüklemeden sonra yeniden başlatma gerektirmeyen Azure sürüm VM. İşlemin yeniden başlatılmasına gerek kalmadan çalışan işlemlerin bellek içi kodunda düzeltme eki uygulayarak çalışır.

### <a name="how-does-hotpatching-work"></a>Anında Düzeltme nasıl çalışır?

* Anında Düzeltme, en son birikimli güncelleştirme Windows Update bir taban çizgisi kurarak, daha sonra yeniden başlatma işleminin etkili olması için gerekli güncelleştirmeler ile bu taban çizgisine göre oluşturulur.  Taban çizgisi, yeni bir toplu güncelleştirmeyle düzenli aralıklarla güncelleştirilir. Toplu güncelleştirme tüm güvenlik ve kalite güncelleştirmelerini içerir ve yeniden başlatma gerektirir.

### <a name="why-should-i-use-hotpatch"></a>Neden hotpatch kullanmalıyım?

* Windows Server 2019 Datacenter: Azure Edition 'da hotpatch kullandığınızda, VM 'niz daha yüksek kullanılabilirliğe sahip olur (daha az önyükleme) ve daha hızlı güncelleştirme (işlemleri yeniden başlatmaya gerek kalmadan daha hızlı yüklenen daha hızlı paketler). Bu işlem, her zaman güncel ve güvenli bir VM 'ye neden olur.

### <a name="what-types-of-updates-are-covered-by-hotpatch"></a>Hotpatch hangi türlerde güncelleştirmeler ele alınmaktadır?

* Hotpatch Şu anda Windows güvenlik güncelleştirmelerini kapsıyor.

### <a name="when-will-i-receive-the-first-hotpatch-update"></a>İlk hotpatch güncelleştirmesi ne zaman gönderilir?

* Hotpatch güncelleştirmeleri genellikle her ayın ikinci Salı günü yayımlanır. Daha fazla bilgi için aşağıya bakın.

### <a name="what-will-the-hotpatch-schedule-look-like"></a>Hotpatch zamanlaması ne gibi görünür?

* Anında Düzeltme, en son birikimli güncelleştirme Windows Update bir taban çizgisi kurarak ve sonra aylık olarak yayınlanan hotpatch güncelleştirmeleriyle Bu taban çizgisine göre oluşturulur.  Önizleme sırasında, taban çizgileri üç ayda bir başlayacak şekilde serbest bırakılır. Yıllık üç aylık zamanlamanın bir örneği için aşağıdaki resme bakın (örneğin, sıfır günlük düzeltmeler nedeniyle planlanmamış taban çizgileri dahil).

    :::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Hotpatch örnek zamanlaması.":::

### <a name="are-reboots-still-needed-for-a-vm-enrolled-in-hotpatch"></a>Hotpatch 'e kayıtlı bir VM için yeniden başlatmalar hala gerekir mi?

* Hotpatch programına dahil olmayan güncelleştirmeleri yüklemek için yeniden başlatmalar gerekir ve bir taban çizgisi (Windows Update en son toplu güncelleştirme) yüklendikten sonra düzenli olarak gereklidir. Bu yeniden başlatma, toplu güncelleştirmede bulunan tüm düzeltme ekleriyle VM 'nizi eşitlenmiş olarak tutacaktır. Taban çizgileri (yeniden başlatma gerektiren), üç aylık bir temposunda başlayıp zaman içinde artar.

### <a name="are-my-applications-affected-when-a-hotpatch-update-is-installed"></a>Hotpatch güncelleştirmesi yüklendiğinde uygulamalarım etkileniyor mu?

* Hotpatch, işlemi yeniden başlatmaya gerek kalmadan çalışan işlemlerin bellek içi kodunu yadığından, uygulamalar düzeltme eki uygulama işleminden etkilenmez. Bunun, düzeltme ekinin kendisinin olası performans ve işlevsellik etkilerine göre ayrı olduğunu unutmayın.

### <a name="can-i-turn-off-hotpatch-on-my-vm"></a>Sanal makinede hotpatch 'i kapatabilir miyim?

* Azure portal aracılığıyla bir sanal makinede hotpatch 'i kapatabilirsiniz.  Hotpatch devre dışı bırakmak, sanal MAKINEYI, Windows Server için tipik güncelleştirme davranışına geri döndüren hotpatch 'ten kaldırır.  Bir VM 'de hotpatch 'ten kaydolduktan sonra, sonraki hotpatch temeli serbest bırakıldığında bu sanal makineyi yeniden kaydedebilirsiniz.

### <a name="can-i-upgrade-from-my-existing-windows-server-os"></a>Var olan Windows Server IŞLETIM sisteminden yükseltme yapabilir miyim?

* Mevcut Windows Server sürümlerinden yükseltme (yani, Windows Server 2016 veya 2019 Azure olmayan sürümler) Şu anda desteklenmiyor. Windows Server Azure Edition 'ın gelecek yayınlarına yükseltme desteklenecektir.

### <a name="can-i-use-hotpatch-for-production-workloads-during-the-preview"></a>Önizleme sırasında üretim iş yükleri için hotpatch kullanabilir miyim?

* Önizlemeler yalnızca sınama amaçlıdır ve üretim ortamlarında kullanım için tasarlanmıştır.

### <a name="will-i-be-charged-during-the-preview"></a>Önizleme sırasında ücretlendirilecektir mi?

* Windows Server Azure sürümü lisansı önizleme sırasında ücretsizdir. Bununla birlikte, VM 'niz için ayarlanmış olan temeldeki altyapının maliyeti (depolama, işlem, ağ, vb.), aboneliğiniz için de ücretlendirilmeye devam edecektir.

### <a name="how-can-i-get-troubleshooting-support-for-hotpatching"></a>Anında düzeltme için nasıl sorun giderme desteği alabilirim?

* [Teknik destek talebi bileti](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)oluşturabilirsiniz. Hizmet seçeneği için, Işlem altında **Windows çalıştıran sanal makine** arayıp seçin. Sorun türü için **Azure özelliklerini** ve sorun alt türü IÇIN **Otomatik VM Konuk düzeltme eki uygulamayı** seçin.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Güncelleştirme Yönetimi hakkında [buradan](../automation/update-management/overview.md)bilgi edinin.
* [Burada](../virtual-machines/automatic-vm-guest-patching.md) otomatik VM Konuk düzeltme eki uygulama hakkında daha fazla bilgi edinin
