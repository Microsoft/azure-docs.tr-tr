---
title: Azure VM 'Leri için otomatik VM Konuk düzeltme eki uygulama
description: Azure 'daki sanal makinelerin otomatik olarak nasıl değiştirileceğini öğrenin.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-guest-patching
ms.collection: windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/17/2021
ms.author: manayar
ms.openlocfilehash: 1a6a67fe43d4e0a6086154d71e61fe51680dbcd0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762596"
---
# <a name="preview-automatic-vm-guest-patching-for-azure-vms"></a>Önizleme: Azure VM 'Leri için otomatik VM Konuk düzeltme eki uygulama

Azure VM 'leriniz için otomatik sanal makine konuk düzeltme eki uygulama, güvenlik uyumluluğunu sürdürmek üzere güvenli ve otomatik olarak sanal makinelere yama yaparak güncelleştirme yönetimini kolaylaştırır.

Otomatik VM Konuk düzeltme eki uygulama aşağıdaki özelliklere sahiptir:
- *Kritik* veya *güvenlik* olarak sınıflandırılan yamalar sanal makineye otomatik olarak indirilir ve uygulanır.
- Düzeltme ekleri, VM 'nin saat diliminde yoğun olmayan saatlerde uygulanır.
- Düzeltme Eki düzenleme Azure tarafından yönetilir ve düzeltme ekleri, [kullanılabilirlik-ilk ilkelere](#availability-first-patching)göre uygulanır.
- Platform sistem durumu sinyalleriyle belirlenen sanal makine sistem durumu, düzeltme eki uygulama başarısızlıklarını algılamak için izlenir.
- Tüm VM boyutları için geçerlidir.

> [!IMPORTANT]
> Otomatik VM Konuk düzeltme eki uygulama şu anda genel önizleme aşamasındadır. Aşağıda açıklanan genel önizleme işlevlerini kullanmak için bir katılım prosedürü gereklidir.
> Bu önizleme sürümü, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-automatic-vm-guest-patching-work"></a>Otomatik VM Konuk düzeltme eki uygulama nasıl çalışır?

VM 'de otomatik VM Konuk düzeltme eki uygulama etkinse, kullanılabilir *kritik* ve *GÜVENLIK* düzeltme ekleri VM 'de otomatik olarak indirilir ve uygulanır. Bu işlem, yeni düzeltme noktaları serbest bırakıldığında otomatik olarak her ay açılır. Düzeltme Eki değerlendirmesi ve yükleme otomatiktir ve işlem VM 'nin gerektiği şekilde yeniden başlatılmasını içerir.

VM, bu VM 'ye yönelik uygun düzeltme eklerini tespit etmek için her birkaç günde bir ve her 30 günlük dönemde birden çok kez değerlendirilir. Düzeltme ekleri, sanal makine için yoğun olmayan saatlerde sanal makineye herhangi bir gün yüklenebilir. Bu otomatik değerlendirme, eksik olan tüm düzeltme eklerinin olası en erken fırsatta bulunmasını sağlar.

Düzeltme ekleri, aşağıda açıklanan kullanılabilirlik ilk Orchestration sonrasında aylık düzeltme sürümlerinin 30 gün içinde yüklenir. Düzeltme ekleri, VM 'nin saat dilimine bağlı olarak VM için yalnızca yoğun olmayan saatlerde yüklenir. Yamaların otomatik olarak yüklenmesi için sanal makinenin yoğun olmayan saatlerde çalışıyor olması gerekir. Bir sanal makine düzenli bir değerlendirme sırasında kapatılmışsa, sanal makine otomatik olarak değerlendirilir ve uygun düzeltme ekleri, sanal makinenin gücü açık olan bir sonraki dönemsel değerlendirme sırasında (genellikle birkaç gün içinde) otomatik olarak yüklenir.

Tanım güncelleştirmeleri ve *kritik* veya *güvenlik* olarak sınıflandırılmayan DIĞER düzeltme ekleri otomatik VM Konuk düzeltme eki uygulama aracılığıyla yüklenmez. Diğer düzeltme eki sınıflandırmaları ile düzeltme eklerini yüklemek veya kendi özel bakım pencerenizde düzeltme eki yüklemeyi zamanlamak için [güncelleştirme yönetimi](./windows/tutorial-config-management.md#manage-windows-updates)kullanabilirsiniz.

### <a name="availability-first-patching"></a>Kullanılabilirlik-ilk düzeltme eki uygulama

Düzeltme eki yükleme işlemi, otomatik VM Konuk düzeltme eki etkin olan tüm VM 'Ler için Azure tarafından genel olarak düzenlenir. Bu düzenleme, Azure tarafından sunulan farklı kullanılabilirlik seviyeleri genelinde kullanılabilirlik-ilk ilkeleri izler.

Güncelleştirme yapılmakta olan bir sanal makine grubu için, Azure platformu güncelleştirmeleri düzenler:

**Bölgeler arasında:**
- Aylık güncelleştirme, genel dağıtım başarısızlıklarını engellemek için Azure genelinde küresel olarak düzenlenir.
- Bir aşamada bir veya daha fazla bölge olabilir ve bir güncelleştirme, bir aşamadaki uygun VM 'Ler başarıyla güncelleşirse bir sonraki aşamalara gider.
- Coğrafi eşleştirilmiş bölgeler eşzamanlı olarak güncellenmez ve aynı bölgesel aşamada olamaz.
- Bir güncelleştirmenin başarısı, sanal makinenin sistem durumu gönderi güncelleştirmesi izlenerek ölçülür. VM sistem durumu, VM için platform sistem durumu göstergeleri aracılığıyla izlenir.

**Bir bölge içinde:**
- Farklı Kullanılabilirlik Alanları sanal makineler eşzamanlı olarak güncellenmez.
- Bir abonelik içindeki tüm VM 'Lerin eş zamanlı güncelleştirmelerini önlemek için bir kullanılabilirlik kümesinin parçası olmayan VM 'Ler en iyi çaba temelinde toplu olarak oluşturulur.

**Bir kullanılabilirlik kümesi içinde:**
- Ortak bir kullanılabilirlik kümesindeki tüm VM 'Ler eşzamanlı olarak güncellenmez.
-   Ortak bir kullanılabilirlik kümesindeki VM 'Ler güncelleştirme etki alanı sınırları içinde güncelleştirilir ve birden çok güncelleştirme etki alanındaki VM 'Ler eşzamanlı olarak güncellenmez.

Belirli bir sanal makıne için düzeltme eki yükleme tarihi, aylık düzeltme eki uygulama döngülerine göre farklı bir toplu işte alınabilir.

### <a name="which-patches-are-installed"></a>Hangi düzeltme ekleri yüklü?
Yüklü düzeltme ekleri VM 'nin piyasaya çıkma aşamasına bağlıdır. Her ay, bu VM için tek bir VM için değerlendirilen tüm güvenlik ve kritik düzeltme eklerinin yüklendiği yeni küresel bir dağıtım başlatılır. Dağıtım, tüm Azure bölgelerinde toplu olarak düzenlenir (yukarıdaki kullanılabilirlik-ilk düzeltme eki uygulama bölümünde açıklanmıştır).

Yüklenecek düzeltme eklerinin tam kümesi, işletim sistemi türü ve değerlendirme zamanlaması dahil olmak üzere VM yapılandırmasına göre değişir. Farklı bölgelerdeki iki özdeş VM 'nin, yama düzenlemesi farklı zamanlarda farklı bölgelere eriştiğinde daha fazla veya daha az sayıda düzeltme eki varsa, farklı düzeltme eklerinin yüklenmesini sağlamak mümkündür. Benzer şekilde, ancak daha az sıklıkta, aynı bölgedeki sanal makineler, ancak farklı zamanlarda (farklı kullanılabilirlik bölgesi veya kullanılabilirlik kümesi toplu işleri nedeniyle) farklı düzeltme ekleri alabilir.

Otomatik VM Konuk düzeltme eki uygulama düzeltme eki kaynağını yapılandırmadığından, ortak depo vs özel deposu gibi farklı düzeltme eki kaynaklarına yapılandırılmış iki benzer VM 'Ler, tam düzeltme eklerinin yüklendiği bir farkı de görebilir.

Sabit bir temposunda üzerinde düzeltme ekleri oluşturan işletim sistemi türleri için, işletim sistemi için ortak depoya yapılandırılmış VM 'Ler, bir ayda farklı dağıtım aşamaları genelinde aynı düzeltme eki kümesini almayı bekleyebilir. Örneğin, genel Windows Update deposuna yapılandırılmış Windows VM 'Leri.

Her ay yeni bir dağıtım tetiklenirken, VM yoğun olmayan saatlerde kullanılıyorsa VM her ay en az bir düzeltme eki dağıtımı alır. Bu, sanal makinenin en son kullanılabilir güvenlik ve kritik düzeltme ekleriyle aylık olarak düzeltme ekleri olmasını sağlar. Yüklü düzeltme ekleri kümesinde tutarlılık sağlamak için, sanal makinelerinizi kendi özel depolarınızdan düzeltme eklerini değerlendirmek ve indirmek üzere yapılandırabilirsiniz.

## <a name="supported-os-images"></a>Desteklenen işletim sistemi görüntüleri
Önizlemede yalnızca belirli işletim sistemi platformu görüntülerinden oluşturulan VM 'Ler desteklenir. Özel görüntüler şu anda önizlemede desteklenmiyor.

Aşağıdaki platform SKU 'Ları Şu anda desteklenmektedir (ve daha fazla düzenli olarak eklenir):

| Publisher               | İşletim sistemi teklifi      |  Sku               |
|-------------------------|---------------|--------------------|
| Canonical  | UbuntuServer | 18,04-LTS |
| RedHat  | RHEL | 'i |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016-veri merkezi    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Server-Core |
| MicrosoftWindowsServer  | WindowsServer | 2019-veri merkezi |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-Core |

## <a name="patch-orchestration-modes"></a>Düzeltme Eki düzenleme modları
Azure 'da VM 'Ler artık aşağıdaki düzeltme eki düzenleme modlarını desteklemektedir:

**AutomaticByPlatform:**
- Bu mod hem Linux hem de Windows VM 'Leri için desteklenir.
- Bu mod, sanal makine için otomatik VM Konuk düzeltme eki uygulamayı sağlar ve sonraki düzeltme eki yüklemesi Azure tarafından düzenlenir.
- Bu mod, kullanılabilirlik için ilk düzeltme eki uygulama için gereklidir.
- Bu mod yalnızca yukarıda desteklenen işletim sistemi platformu görüntüleri kullanılarak oluşturulan VM 'Ler için desteklenir.
- Windows VM 'Leri için, bu modun ayarlanması aynı zamanda Windows sanal makinesinde yerel otomatik güncelleştirmeleri devre dışı bırakarak çoğaltmayı önler.
- Linux VM 'lerde bu modu kullanmak için VM şablonundaki özelliği ayarlayın `osProfile.linuxConfiguration.patchSettings.patchMode=AutomaticByPlatform` .
- Windows VM 'lerde bu modu kullanmak için, özelliği ayarlayın `osProfile.windowsConfiguration.enableAutomaticUpdates=true` ve  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform` VM şablonundaki özelliği ayarlayın.

**Otomatik olarak:**
- Bu mod yalnızca Windows VM 'Leri için desteklenir.
- Bu mod, Windows sanal makinesinde otomatik güncelleştirme yapılmasını ve Otomatik Güncelleştirmeler aracılığıyla sanal makineye düzeltme ekleri yüklenmesini mümkün.
- Bu mod, kullanılabilirliği ilk düzeltme ekini desteklemez.
- Bu mod, bir Windows sanal makinesi için başka bir düzeltme eki modu belirtilmemişse varsayılan olarak ayarlanır.
- Windows VM 'lerde bu modu kullanmak için, özelliği ayarlayın `osProfile.windowsConfiguration.enableAutomaticUpdates=true` ve  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` VM şablonundaki özelliği ayarlayın.

**El ile:**
- Bu mod yalnızca Windows VM 'Leri için desteklenir.
- Bu mod, Windows sanal makinesinde otomatik güncelleştirmeleri devre dışı bırakır.
- Bu mod, kullanılabilirliği ilk düzeltme ekini desteklemez.
- Bu mod, özel düzeltme eki uygulama çözümleri kullanılırken ayarlanmalıdır.
- Windows VM 'lerde bu modu kullanmak için, özelliği ayarlayın `osProfile.windowsConfiguration.enableAutomaticUpdates=false` ve  `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` VM şablonundaki özelliği ayarlayın.

**Imagedefault:**
- Bu mod yalnızca Linux sanal makineleri için desteklenir.
- Bu mod, kullanılabilirliği ilk düzeltme ekini desteklemez.
- Bu mod, sanal makineyi oluşturmak için kullanılan görüntüde varsayılan düzeltme eki uygulama yapılandırmasını geliştirir.
- Bu mod, Linux VM için başka bir düzeltme eki modu belirtilmemişse varsayılan olarak ayarlanır.
- Linux VM 'lerde bu modu kullanmak için VM şablonundaki özelliği ayarlayın `osProfile.linuxConfiguration.patchSettings.patchMode=ImageDefault` .

> [!NOTE]
>Windows VM 'Leri için, özelliği `osProfile.windowsConfiguration.enableAutomaticUpdates` Şu anda yalnızca VM ilk oluşturulduğunda ayarlanabilir. El Ile bir otomatik moda veya otomatik moddan El Ile moduna geçiş Şu anda desteklenmiyor. AutomaticByOS modundan Automaticbyplatfod moduna geçiş desteklenir.

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>Otomatik VM Konuk düzeltme eki uygulamayı etkinleştirme gereksinimleri

- Sanal makinede [Windows](./extensions/agent-windows.md) veya [LINUX](./extensions/agent-linux.md) için Azure VM Aracısı yüklü olmalıdır.
- Linux VM 'Leri için Azure Linux aracısının sürüm 2.2.53.1 veya üzeri olması gerekir. Geçerli sürüm gereken sürümden düşükse [Linux aracısını güncelleştirin](./extensions/update-linux-agent.md) .
- Windows VM 'Leri için Windows Update hizmeti sanal makinede çalışıyor olmalıdır.
- Sanal makine, yapılandırılmış güncelleştirme uç noktalarına erişebilmelidir. Sanal makineniz Windows VM 'Ler için Linux veya Windows Server Update Services (WSUS) için özel depoları kullanacak şekilde yapılandırıldıysa, ilgili güncelleştirme uç noktalarına erişilebilir olması gerekir.
- Işlem API 'SI sürüm 2020-12-01 veya üstünü kullanın. İşlem API 'SI sürüm 2020-06-01, sınırlı işlevselliğe sahip Windows VM 'Ler için kullanılabilir.

Önizleme işlevselliğinin etkinleştirilmesi, aşağıdaki bölümde açıklandığı gibi, abonelik başına **InGuestAutoPatchVMPreview** ve **InGuestPatchVMPreview** özellikleri için tek seferlik bir kabul gerektirir.

### <a name="rest-api"></a>REST API
Aşağıdaki örnekte, aboneliğiniz için önizlemenin nasıl etkinleştirileceği açıklanmaktadır:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
```
```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

Özellik kaydı 15 dakikaya kadar sürebilir. Kayıt durumunu denetlemek için:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
```
```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```
Aboneliğiniz için özellik kaydedildikten sonra, değişikliği işlem kaynak sağlayıcısına yayarak katılım işlemini doldurun.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Aboneliğiniz için Önizlemeyi etkinleştirmek üzere [register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) cmdlet 'ini kullanın.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Özellik kaydı 15 dakikaya kadar sürebilir. Kayıt durumunu denetlemek için:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Aboneliğiniz için özellik kaydedildikten sonra, değişikliği işlem kaynak sağlayıcısına yayarak katılım işlemini doldurun.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Aboneliğiniz için Önizlemeyi etkinleştirmek üzere [az Feature Register](/cli/azure/feature#az_feature_register) kullanın.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Özellik kaydı 15 dakikaya kadar sürebilir. Kayıt durumunu denetlemek için:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Aboneliğiniz için özellik kaydedildikten sonra, değişikliği işlem kaynak sağlayıcısına yayarak katılım işlemini doldurun.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="enable-automatic-vm-guest-patching"></a>Otomatik VM konuk düzeltme eki uygulamayı etkinleştirme
Windows VM 'de otomatik VM Konuk düzeltme eki uygulamayı etkinleştirmek için, sanal makine şablon tanımında *Osprofile. windowsConfiguration. enableAutomaticUpdates* özelliğinin *true* olarak ayarlandığından emin olun. Bu özellik yalnızca VM oluşturulurken ayarlanabilir. Bu ek özellik, Linux VM 'Leri için geçerli değildir.

### <a name="rest-api-for-linux-vms"></a>Linux sanal makineleri için REST API
Aşağıdaki örnekte, otomatik VM Konuk düzeltme eki uygulama 'nın nasıl etkinleştirileceği açıklanmaktadır:

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
  "properties": {
    "osProfile": {
      "linuxConfiguration": {
        "provisionVMAgent": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="rest-api-for-windows-vms"></a>Windows VM 'Leri için REST API
Aşağıdaki örnekte, otomatik VM Konuk düzeltme eki uygulama 'nın nasıl etkinleştirileceği açıklanmaktadır:

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-06-01`
```

```json
{
  "properties": {
    "osProfile": {
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="azure-powershell-for-windows-vms"></a>Windows VM 'Leri için Azure PowerShell
VM oluştururken veya güncelleştirirken otomatik VM Konuk düzeltme eki uygulamayı etkinleştirmek için [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) cmdlet 'ini kullanın.

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-for-windows-vms"></a>Windows VM 'Leri için Azure CLı
Yeni bir VM oluştururken otomatik VM Konuk düzeltme eki uygulamayı etkinleştirmek için [az VM Create](/cli/azure/vm#az_vm_create) kullanın. Aşağıdaki örnek, *Myresourcegroup* adlı kaynak grubunda *myvm* adlı BIR VM için otomatik VM Konuk düzeltme eki uygulamayı yapılandırır:

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

Var olan bir VM 'yi değiştirmek için [az VM Update](/cli/azure/vm#az_vm_update) kullanın

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## <a name="enablement-and-assessment"></a>Etkinleştirme ve değerlendirme

> [!NOTE]
>VM 'nin yoğun olmayan saatlerde etkinleştirilmesi durumunda, etkinleştirme tamamlanana kadar, VM 'de otomatik VM Konuk güncelleştirmelerini etkinleştirmek üç saatten fazla sürebilir. Değerlendirme ve düzeltme eki yüklemesi yalnızca yoğun olmayan saatlerde Gerçekleşmede, yama uygulamak için sanal makinenizin yoğun olmayan saatlerde da çalışıyor olması gerekir.

Bir VM için otomatik sanal makine konuk düzeltme eki uygulama etkinleştirildiğinde, bir Linux sanal makinesine türünde bir VM Uzantısı `Microsoft.CPlat.Core.LinuxPatchExtension` yüklenir veya bir WINDOWS VM 'ye türünde BIR VM Uzantısı yüklüdür `Microsoft.CPlat.Core.WindowsPatchExtension` . Bu uzantı, otomatik VM Konuk düzeltme eki uygulama işleminin bir parçası olarak Azure platformu tarafından yönetildiğinden, bu uzantının el ile yüklenmesi veya güncellenmesi gerekmez.

VM 'nin yoğun olmayan saatlerde etkinleştirilmesi durumunda, etkinleştirme tamamlanana kadar, VM 'de otomatik VM Konuk güncelleştirmelerini etkinleştirmek üç saatten fazla sürebilir. Uzantı Ayrıca VM için yoğun olmayan saatlerde yüklenir ve güncelleştirilir. Etkinleştirme tamamlanmadan önce VM 'nin yoğun olmayan saatleri sona erdirmek için etkinleştirme işlemi, sonraki kullanılabilir yoğun süre boyunca sürdürülür.

Otomatik Güncelleştirmeler Çoğu senaryoda devre dışıdır ve düzeltme eki yükleme, ileri doğru uzantı aracılığıyla yapılır. Aşağıdaki koşullar geçerlidir.
- Bir Windows VM daha önce otomatik Windows Update otomatik olarak açıldığından, otomatik Windows Update, uzantı yüklendiğinde VM için devre dışıdır.
- Ubuntu VM 'Leri için otomatik sanal makine konuk düzeltme eki uygulamayı etkinleştirme tamamlandığında otomatik olarak varsayılan Otomatik Güncelleştirmeler devre dışı bırakılır.
- RHEL için otomatik güncelleştirmelerin el ile devre dışı bırakılması gerekir (Bu bir önizleme sınırlamasıdır). Yürütme

```
systemctl stop packagekit
```

```
systemctl mask packagekit
```

Otomatik VM Konuk düzeltme eki 'nin tamamlanıp tamamlanmadığını ve düzeltme eki uygulama 'nın VM 'de yüklü olup olmadığını doğrulamak için VM 'nin örnek görünümünü gözden geçirebilirsiniz. Etkinleştirme işlemi tamamlandıktan sonra uzantı yüklenir ve VM 'nin değerlendirme sonuçları altında kullanılabilir olacaktır `patchStatus` . VM 'nin örnek görünümüne aşağıda açıklandığı gibi birden çok yolla erişilebilir.

### <a name="rest-api"></a>REST API

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-12-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
VM 'niz için örnek görünümüne erişmek üzere parametresiyle [Get-AzVM](/powershell/module/az.compute/get-azvm) cmdlet 'ini kullanın `-Status` .

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

PowerShell Şu anda yalnızca yama uzantısı hakkında bilgi sağlamaktadır. Hakkındaki bilgiler `patchStatus` yakında PowerShell aracılığıyla da kullanılabilir.

### <a name="azure-cli"></a>Azure CLI
VM 'niz için örnek görünümüne erişmek için [az VM Get-instance-View](/cli/azure/vm#az_vm_get_instance_view) kullanın.

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>VM 'niz için düzeltme eki durumunu anlama

`patchStatus`Örnek görünümü yanıtının bölümü, sanal makinenizin en son değerlendirmesi ve son düzeltme eki yüklemesiyle ilgili ayrıntıları sağlar.

SANAL makinenizin değerlendirme sonuçları, bölümünde incelenebilir `availablePatchSummary` . Otomatik VM Konuk düzeltme eki uygulama özelliği etkin olan bir sanal makine için bir değerlendirme düzenli olarak yürütülür. Bir değerlendirme sonrasında kullanılabilir düzeltme eklerinin sayısı `criticalAndSecurityPatchCount` ve `otherPatchCount` sonuçları. Otomatik VM Konuk düzeltme eki uygulama, *kritik* ve *güvenlik* düzeltme eki sınıflandırmaları kapsamında değerlendirilen tüm düzeltme eklerini yükler. Diğer değerlendirilmiş düzeltme ekleri atlanır.

VM 'niz için düzeltme eki yükleme sonuçları, bölümünde incelenebilir `lastPatchInstallationSummary` . Bu bölümde, yüklenen, bekleyen, başarısız veya Atlanan düzeltme eklerinin sayısı dahil olmak üzere VM 'deki son düzeltme eki yükleme girişimine ilişkin ayrıntılar sağlanmaktadır. Düzeltme ekleri yalnızca VM için yoğun olmayan saatler bakım penceresi sırasında yüklenir. Bekleyen ve başarısız düzeltme ekleri, sonraki yoğun olmayan saatlerde bakım penceresi sırasında otomatik olarak yeniden denenir.

## <a name="on-demand-patch-assessment"></a>İsteğe bağlı düzeltme eki değerlendirmesi
VM 'niz için otomatik VM Konuk düzeltme eki uygulama özelliği zaten etkinse, VM 'de sanal makinenin yoğun olmayan saatlerde düzenli aralıklarla bir düzeltme eki değerlendirmesi yapılır. Bu işlem otomatiktir ve en son değerlendirmenin sonuçları, bu belgede daha önce açıklandığı gibi VM 'nin örnek görünümü aracılığıyla incelenebilir. Ayrıca, istediğiniz zaman VM 'niz için isteğe bağlı bir yama değerlendirmesi tetikleyebilirsiniz. Düzeltme Eki değerlendirmesi işleminin tamamlanması birkaç dakika sürebilir ve en son değerlendirmenin durumu VM 'nin örnek görünümünde güncelleştirilir.

Önizleme işlevselliğinin etkinleştirilmesi, abonelik başına **InGuestPatchVMPreview** özelliği için tek seferlik bir kabul gerektirir. Bu özellik önizlemesi, **InGuestAutoPatchVMPreview** için daha önce gerçekleştirilen otomatik sanal makine konuk düzeltme eki uygulama özelliği kaydından farklıdır. Ek özellik önizlemesini etkinleştirmek ayrı ve ek bir gereksinimdir. İsteğe bağlı düzeltme eki değerlendirmesi için özellik önizlemesi, daha önce otomatik VM Konuk düzeltme eki uygulama için açıklanan [Önizleme etkinleştirme işleminden](automatic-vm-guest-patching.md#requirements-for-enabling-automatic-vm-guest-patching) sonra etkinleştirilebilir.

> [!NOTE]
>İsteğe bağlı yama değerlendirmesi, düzeltme eki yüklemesini otomatik olarak tetiklemez. Otomatik VM Konuk düzeltme eki uygulamayı etkinleştirdiyseniz, sanal makine için değerlendirilen ve uygulanabilir düzeltme ekleri, bu belgede daha önce açıklanan kullanılabilirlik için ilk düzeltme eki uygulama işlemi sonrasında VM 'nin yoğun olmayan saatlerde yüklenir.

### <a name="rest-api"></a>REST API
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Sanal makineniz için kullanılabilir düzeltme eklerini değerlendirmek üzere [Invoke-AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment) cmdlet 'ini kullanın.

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli"></a>Azure CLI
Sanal makineniz için kullanılabilir düzeltme eklerini değerlendirmek için [az VM değerlendir-Patches](/cli/azure/vm#az_vm_assess_patches) kullanın.

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Windows sanal makinelerini oluşturma ve yönetme hakkında daha fazla bilgi edinin](./windows/tutorial-manage-vm.md)
