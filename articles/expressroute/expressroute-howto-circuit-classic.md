---
title: 'Azure ExpressRoute: bir devreyi değiştirme: PowerShell: klasik'
description: Bu makalede, durumu, güncelleştirme veya silme denetleyin ve ExpressRoute Klasik dağıtım modeli bağlantı hattı sağlamasını kaldırma adımları gösterilmektedir.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cherylmc
ms.openlocfilehash: a2a3bdeb0ee454185c92c6e49a17bda7e4e5d5f0
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083574"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>PowerShell (Klasik) kullanarak bir ExpressRoute bağlantı hattını değiştirme

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager şablonu](expressroute-howto-circuit-resource-manager-template.md)
> * [Video - Azure portalı](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasik)](expressroute-howto-circuit-classic.md)
>

Bu makalede, durumu, güncelleştirme veya silme denetleyin ve ExpressRoute Klasik dağıtım modeli bağlantı hattı sağlamasını kaldırma adımları gösterilmektedir. Bu makale klasik dağıtım modeli için geçerlidir.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Azure dağıtım modelleri hakkında**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Başlamadan önce

Azure Hizmet Yönetimi (SM) PowerShell modüllerine ve ExpressRoute modülünün en son sürümlerini yükleyin. SM modüllerini çalıştırmak için Azure CloudShell ortamını kullanamazsınız.

1. Azure hizmet yönetimi modülünü yüklemek için [hizmet yönetimi modülünü yükleme](/powershell/azure/servicemanagement/install-azure-ps) makalesindeki yönergeleri kullanın. Az veya RM modülü zaten yüklüyse, '-AllowClobber ' kullandığınızdan emin olun.
2. Yüklü modülleri içeri aktarın. Aşağıdaki örneği kullanırken, yolu yüklü PowerShell modüllerinizin konumunu gösterecek şekilde ayarlayın.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Azure hesabınızda oturum açmak için, PowerShell konsolunuzu yükseltilmiş haklarla açın ve hesabınıza bağlanın. Hizmet yönetimi modülünü kullanarak bağlanmanıza yardımcı olması için aşağıdaki örneği kullanın:

   ```powershell
   Add-AzureAccount
   ```

## <a name="get-the-status-of-a-circuit"></a>Bir bağlantı hattının durumunu Al

Dilediğiniz zaman bu bilgileri kullanarak alabilirsiniz `Get-AzureCircuit` cmdlet'i. Parametre olmadan bir çağrıyı yapan tüm devreler listeler.

```powershell
Get-AzureDedicatedCircuit

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled

Bandwidth                        : 1000
CircuitName                      : MyAsiaCircuit
Location                         : Singapore
ServiceKey                       : #################################
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Çağrı için parametre olarak hizmet anahtarını geçirerek belirli bir ExpressRoute bağlantı hattı üzerinde daha fazla bilgi edinebilirsiniz.

```powershell
Get-AzureDedicatedCircuit -ServiceKey "*********************************"

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Aşağıdaki örnek çalıştırarak tüm parametrelerin ayrıntılı açıklamaları alabilirsiniz:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>Devreyi değiştirme

Belirli bir ExpressRoute bağlantı hattı özelliklerini bağlantıyı etkilemeden değiştirebilirsiniz.

Kapalı kalma süresi olmadan aşağıdaki görevleri gerçekleştirebilirsiniz:

* Etkinleştirmek veya ExpressRoute bağlantı hattı için ExpressRoute premium eklenti devre dışı bırakın.
* ExpressRoute bağlantı hattı bant genişliği var. sağlanan kapasite kullanılabilir bağlantı noktası üzerinde artırın. Bağlantı hattı bant önceki sürüme indirme desteklenmiyor.
* Ölçüm planını, ölçülen verilerden sınırsız veri değiştirin. Ölçüm plan sınırsız verilerden ölçülen veri değiştirme desteklenmiyor.
* Etkinleştirebilir ve devre dışı *Klasik işlemlere izin Ver'i*.

Başvurmak [ExpressRoute SSS](expressroute-faqs.md) sınırlar ve sınırlamalar hakkında daha fazla bilgi için.

### <a name="enable-the-expressroute-premium-add-on"></a>ExpressRoute premium eklentisini etkinleştirmeniz

ExpressRoute premium eklentisi aşağıdaki PowerShell cmdlet'ini kullanarak, varolan bağlantı hattınız için etkinleştirebilirsiniz:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Premium
Status                           : Enabled
```

Bağlantı hattınız şimdi ExpressRoute premium eklenti özellikleri etkin olacaktır. Premium eklenti özelliğini faturalandırması, komut başarılı şekilde gerçekleştirildikten hemen sonra başlar.

### <a name="disable-the-expressroute-premium-add-on"></a>ExpressRoute premium eklentisi devre dışı bırak

> [!IMPORTANT]
> Bu işlem için standart devreyi izin daha büyük olan kaynaklar kullanıyorsanız, başarısız olabilir.
>
>

#### <a name="considerations"></a>Dikkat edilmesi gerekenler

* Premium katmanından standart sürümüne düşürme önce işlem hattına bağlı sanal ağları sayısı 10'dan küçük olduğundan emin olun. Bunu yapmazsanız, güncelleştirme isteği başarısız olur ve faturalandırılırsınız premium ücretler.
* Diğer jeopolitik bölgede tüm sanal ağları bağlantısını kaldırmanız gerekir. Sizin için güncelleştirme isteği başarısız olur ve faturalandırılırsınız premium ücretler.
* Özel eşdüzey hizmet sağlama için 4000'den az yollar yol tablonuz olması gerekir. Rota tablosu boyutunuz 4000 yollara kıyasla daha büyükse, BGP oturumu bırakır ve 4.000 tanıtılan ön ek sayısı ölçeklendirilinceye kadar yeniden iler hale gerekmez.

#### <a name="to-disable-the-premium-add-on"></a>Premium eklentiyi devre dışı bırakmak için

Aşağıdaki PowerShell cmdlet'ini kullanarak mevcut bağlantı hattınız için ExpressRoute premium eklentisi devre dışı bırakabilirsiniz:

```powershell

Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

### <a name="update-the-expressroute-circuit-bandwidth"></a>ExpressRoute bağlantı hattı bant genişliğini güncelleştir

Denetleme [ExpressRoute SSS](expressroute-faqs.md) desteklenen sağlayıcınız için bant genişliği seçenekleri. (Bağlantı hattınızın oluşturulduğu) fiziksel bağlantı noktası izin verdiği sürece, mevcut bir devreyi boyutundan büyük boyut seçebilirsiniz.

> [!IMPORTANT]
> ExpressRoute bağlantı hattı mevcut bağlantı noktası üzerinde yetersiz kapasite ise yeniden oluşturmanız gerekebilir. Yoksa hiçbir ek kapasite kullanılabilir o konumda devre yükseltemezsiniz.
>
> Kesintisiz bir ExpressRoute bağlantı hattı bant indiremezsiniz. Bant genişliği eski sürüme düşürme, ExpressRoute bağlantı hattının sağlamasını kaldırma ve ardından yeni ExpressRoute bağlantı hattı yeniden sağlamak istiyor.
>
>

#### <a name="resize-a-circuit"></a>Bir devreyi yeniden boyutlandırma

Gereksinim boyutu karar verdikten sonra bağlantı hattınızı yeniden boyutlandırmak için aşağıdaki komutu kullanabilirsiniz:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Bağlantı hattınızın Microsoft tarafında boyutta sonra bu değişiklik eşleşecek şekilde kendi tarafında yapılandırmaları güncelleştirmek için bağlantı sağlayıcınız başvurmanız gerekir. Faturalandırma üzerinde bu noktadan itibaren güncelleştirilmiş bant genişliği seçenek için başlar.

Bağlantı hattı bant genişliğini artırmak, aşağıdaki hatayı görürseniz, bu var. mevcut bağlantı hattınızın oluşturulduğu fiziksel bağlantı noktası üzerinde hiçbir yeterli bant genişliği bırakılır anlamına gelir. Bu bağlantı hattını Sil ve yeni bir bağlantı hattı ihtiyacınız boyutunun oluşturmanız gerekir.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Sağlamasını kaldırma ve bir bağlantı hattını Sil

### <a name="considerations"></a>Dikkat edilmesi gerekenler

* ExpressRoute bağlantı hattı için bu işlemin başarılı olması için tüm sanal ağlardan bağlantısını kaldırmanız gerekir. Bu işlem başarısız olursa işlem hattına bağlı sanal ağlara sahip olup olmadığını denetleyin.
* ExpressRoute bağlantı hattı Hizmet Sağlayıcısı sağlama durumu ise **sağlama** veya **sağlanan** kendi tarafında bağlantı hattını sağlamasını kaldırmak için hizmet sağlayıcınızla birlikte çalışmanız gerekir. Kaynak ayırmanıza ve hizmeti sağlayıcısı devreyi sağlamayı kaldırma tamamlandıktan ve bize bildiren kadar faturalandırılırsınız devam ediyoruz.
* Hizmet sağlayıcısı devreyi sağlamayı durdurduğunda varsa (Hizmet Sağlayıcısı sağlama durumu ayarlamak **sağlanmadı**), bağlantı hattının sonra silebilirsiniz. Bu durumda bağlantı hattının faturalandırılması durdurulur.

#### <a name="delete-a-circuit"></a>Bir bağlantı hattını Sil

Aşağıdaki komutu çalıştırarak, ExpressRoute devreniz silebilirsiniz:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```
