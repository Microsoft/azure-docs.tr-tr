---
title: Azure 'da Windows VM 'Leri için bakım bildirimlerini işleme
description: Azure 'da çalışan Windows sanal makineleri için bakım bildirimlerini görüntüleyin ve self servis bakımı 'nı başlatın.
services: virtual-machines-windows
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/30/2019
ms.author: shants
ms.openlocfilehash: 6e269e9b21fe16a1d77b4e1f714517f91fa531d4
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039186"
---
# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>Windows sanal makineleri için planlı bakım bildirimlerini işleme

Azure sanal makine konak altyapısının güvenilirlik, performans ve güvenliğini iyileştirmek için düzenli olarak güncelleştirmeler yapar. Güncelleştirmeler, barındırma ortamında düzeltme eki uygulama veya donanım yükseltme ve kullanımdan kaldırma gibi değişikliklerdir. Bu güncelleştirmelerin çoğu, barındırılan sanal makinelere herhangi bir etki olmadan gerçekleştirilir. Ancak, güncelleştirmelerin etkisi olan durumlar vardır:

- Bakım için yeniden başlatma gerektirmiyorsa, Azure, konak güncelleştirilirken VM 'yi duraklatmak için yerinde geçiş kullanır. Bu rebootful dışı bakım işlemleri hata etki alanına hata etki alanına göre uygulanır ve herhangi bir uyarı sistem durumu sinyali alındığında ilerleme durdurulur. 

- Bakım için yeniden başlatma gerekiyorsa, bakımın ne zaman planlandığına ilişkin bir uyarı alırsınız. Bu durumlarda, sizin için uygun olduğunda Bakımı kendiniz başlatabileceğiniz 35 gün olan bir zaman penceresi vermiş olursunuz.


Yeniden başlatma gerektiren planlı bakım, dalgalar olarak zamanlanır. Her bir dalga, farklı kapsama (bölgelere) sahiptir.

- Bir dalga, müşterilere yönelik bir bildirimle başlar. Varsayılan olarak, bildirim abonelik sahibine ve ortak sahiplerine gönderilir. Azure [etkinlik günlüğü uyarılarını](../../azure-monitor/platform/activity-logs-overview.md)kullanarak bildirimlere e-posta, SMS ve Web kancaları gibi daha fazla alıcı ve mesajlaşma seçeneği ekleyebilirsiniz.  
- Bildirim sırasında *self servis penceresi* kullanılabilir hale getirilir. Genellikle 35 gün olan bu pencere sırasında, sanal makinelerinizin bu dalgaya dahil edildiğini ve bakımını kendi zamanlama gereksinimlerinize göre proaktif olarak başlatmayı öğrenebilirsiniz.
- Self Servis penceresinden sonra, *Zamanlanmış bir bakım penceresi* başlar. Bu pencere sırasında, Azure tarafından zamanlama ve gerekli bakım sanal makinenize uygulanıyor. 

İki Windows 'taki amaç, bakım başlatmak için size yeterli zaman sunmaktır ve Azure 'un bakımın otomatik olarak ne zaman başlatılacağını öğrenirken sanal makinenizi yeniden başlatabilir.


Azure portal, PowerShell, REST API ve CLı kullanarak sanal makinelerinize yönelik bakım pencerelerini sorgulayabilir ve self servis bakımını başlatabilirsiniz.

  
## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Self servis penceresi sırasında bakım başlatmanız mı gerekiyor?  

Aşağıdaki kılavuzlar, bu özelliği kullanıp kullanmayacağınızı ve bakım yapıp başlatmayacağınıza karar vermenize yardımcı olmalıdır.

> [!NOTE] 
> Self servis bakımı, tüm sanal makinelerinize uygun olmayabilir. VM 'niz için öngörülü yeniden dağıtım olup olmadığını öğrenmek için, bakım durumu ' na **Şimdi başlayın** ' a bakın. Self servis bakımı şu anda Cloud Services (Web/çalışan rolü) ve Service Fabric için kullanılamaz.


Belirli bir zamanda yalnızca bir güncelleştirme etki alanının etkilediği yüksek kullanılabilirliğe sahip olduğundan, **kullanılabilirlik kümeleri** kullanan dağıtımlar için self servis bakımı önerilmez. 
- Azure 'un Bakımı tetiklemesine izin verin. Yeniden başlatma gerektiren bakım için, bakımın etki alanını güncelleştirme etki alanı ile güncelleştirme yapılacağını, güncelleştirme etki alanlarının ise sırasıyla bakım almamasını ve güncelleştirme etki alanları arasında 30 dakikalık bir duraklama olduğunu unutmayın. 
- Kapasiteniz bir kısmının (1/güncelleştirme etki alanı sayısı) geçici olarak kaybolması sorun oluşturacaksa, bakım dönemi boyunca ek örnekler ayırarak kolayca telafi edilebilir.
- Yeniden başlatma gerektirmeyen bakım için, güncelleştirmeler hata etki alanı düzeyinde uygulanır.
    
Aşağıdaki senaryolarda self servis **bakımını kullanmayın:** 
- Sanal makinelerinizi sıklıkla el ile kapatırsanız DevTest Labs kullanarak otomatik kapatmayı veya bir zamanlamayı izleyerek, bakım durumunu döndürebilir ve bu nedenle ek kesinti yapılmasına neden olabilir.
- Bildiğiniz kısa süreli VM 'lerde, bakım dalgasının sonundan önce silineceğini görürsünüz. 
- Güncelleştirme sonrasında sürdürülmesi istenen yerel (kısa ömürlü) diskte büyük bir duruma sahip iş yükleri için. 
- VM 'nizi sık kullandığınız durumlar için bakım durumunu döndürebilir.
- İş yükünüzün proaktif yük devretmesini veya düzgün şekilde kapatılmasını sağlayan zamanlanmış olayları benimsediğiniz takdirde, bakım kapanmadan önce 15 dakika önce

Şirket içinde zamanlanmış bakım aşamasında sanal makineyi kesintisiz olarak çalıştırmayı planlıyorsanız ve yukarıda bahsedilen sayaç göstergeleri yoksa, self servis bakımını **kullanın** . 

Aşağıdaki durumlarda self servis bakımı kullanmak en iyisidir:

- Yönetim veya son müşteri ile tam bir bakım penceresi iletmeli. 
- Bakımı belirli bir tarihle doldurmanız gerekir. 
- Güvenli kurtarmayı güvence altına almak için örneğin, çok katmanlı uygulama gibi bakım sırasını kontrol etmeniz gerekir.
- İki güncelleştirme etki alanı (UDs) arasında 30 dakikadan daha fazla VM kurtarma süresi gerekir. Güncelleştirme etki alanları arasındaki süreyi denetlemek için, sanal makinelerinizdeki her seferinde bir güncelleştirme etki alanında (UD) bakım tetiklemeniz gerekir.

 

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="check-maintenance-status-using-powershell"></a>PowerShell kullanarak bakım durumunu denetleme

Ayrıca, sanal makinelerin bakım için zamanlandığını görmek için Azure PowerShell 'i de kullanabilirsiniz. Planlı bakım bilgileri, `-status` parametresini kullandığınızda [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) cmdlet 'inden kullanılabilir.
 
Bakım bilgileri yalnızca bakım planlandı durumunda döndürülür. VM 'yi etkileyen bakım zamanlanmamışsa, cmdlet herhangi bir bakım bilgisi döndürmez. 

 

```powershell
Get-AzVM -ResourceGroupName rgName -Name vmName -Status
```

Aşağıdaki özellikler MaintenanceRedeployStatus altında döndürülür: 

| Değer | Açıklama   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Şu anda VM 'de bakım başlatılıp başlatılmayacağını belirtir |
| PreMaintenanceWindowStartTime         | VM 'niz üzerinde bakım başlatabilmeniz için bakım self servis penceresinin başlangıcı |
| PreMaintenanceWindowEndTime           | VM 'niz üzerinde bakım başlatabilmeniz için bakım self servis penceresinin sonu |
| MaintenanceWindowStartTime            | Azure 'un VM 'niz üzerinde bakım başlattığı zamanlanan bakım başlangıcı |
| MaintenanceWindowEndTime              | Azure 'un VM 'niz üzerinde Bakımı başlattığı bakım zamanlandı penceresinin sonu |
| LastOperationResultCode               | VM 'de son bakım başlatma denemesinin sonucu |



Ayrıca, [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) ' i kullanarak bir kaynak grubundaki tüm VM 'lerin bakım durumunu alabılır ve VM belirtmemelidir.
 
```powershell
Get-AzVM -ResourceGroupName rgName -Status
```

Aşağıdaki PowerShell işlevi abonelik KIMLIĞINIZI alır ve bakım için zamanlanmış VM 'lerin bir listesini yazdırır.

```powershell

function MaintenanceIterator
{
    Select-AzSubscription -SubscriptionId $args[0]

    $rgList= Get-AzResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>PowerShell kullanarak VM 'niz üzerinde bakım başlatma

Önceki bölümde bulunan işlevin bilgilerini kullanarak, **IsCustomerInitiatedMaintenanceAllowed** true olarak ayarlanırsa, AŞAĞıDAKI bir VM üzerinde bakım başlatır.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Klasik dağıtımlar

Klasik dağıtım modeli kullanılarak dağıtılmış eski VM 'Ler varsa, PowerShell kullanarak VM 'Leri sorgulayabilir ve bakım başlatabilirsiniz.

Bir sanal makinenin bakım durumunu almak için şunu yazın:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Klasik sanal makinenizde bakım başlatmak için şunu yazın:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```


## <a name="faq"></a>SSS


**S: neden sanal makinelerimi şimdi yeniden başlatmanız gerekiyor?**

Y **:** Azure platformunda yapılan güncelleştirmelerin ve yükseltmelerin büyük bölümü sanal makinenin kullanılabilirliğini etkilememesine karşın, Azure 'da barındırılan sanal makinelerin yeniden başlatılmasını önleyemiyorum durumlar vardır. Sanal makinelerin yeniden başlatılmasına neden olacak sunucularımızı yeniden başlatmamızı gerektiren birkaç değişikliği biriktireceğiz.

**S: kullanılabilirlik kümesi kullanarak yüksek kullanılabilirlik önerinizi izlediğimde, güvenli mıyım?**

Y **:** Bir kullanılabilirlik kümesinde veya sanal makine ölçek kümelerinde dağıtılan sanal makinelerin güncelleştirme etki alanları (UD) kavramı vardır. Azure, bakım gerçekleştirirken UD kısıtlamasına sahiptir ve sanal makineleri farklı bir UD 'den (aynı Kullanılabilirlik kümesi içinde) yeniden başlatmaz.  Azure, sonraki sanal makine grubuna geçmeden önce en az 30 dakika bekler. 

Yüksek kullanılabilirlik hakkında daha fazla bilgi için bkz. [Azure 'da sanal makineler Için kullanılabilirlik](availability.MD).

**S: planlı bakım hakkında bilgi almak Nasıl yaparım? mı?**

Y **:** Planlı bir bakım dalgası, bir veya daha fazla Azure bölgesine zamanlama ayarlayarak başlar. Yakında, abonelik sahiplerine bir e-posta bildirimi gönderilir (abonelik başına bir e-posta). Bu bildirimin ek kanalları ve alıcıları, etkinlik günlüğü uyarıları kullanılarak yapılandırılabilir. Planlanmış bakımın zaten zamanlandığı bir bölgeye bir sanal makine dağıtırsanız, bildirimi almazsınız ancak VM 'nin bakım durumunu denetlemeniz gerekir.

**S: Portal, PowerShell veya CLı 'de planlı bakım hakkında herhangi bir gösterge görmüyorum. Ne oldu?**

Y **:** Planlı bakımla ilgili bilgiler yalnızca, planlanan bir bakım dalgası sırasında, yalnızca bundan etkilenecek VM 'Ler için kullanılabilir. Diğer bir deyişle, veri olmadığını görürseniz, bakım dalgası zaten tamamlanmış (veya başlatılmamış) veya sanal makineniz zaten güncelleştirilmiş bir sunucuda barındırılıyor olabilir.

**S: Sanal makinem etkilendiğinde tam olarak bilmeniz için bir yol var mı?**

Y **:** Zamanlamayı ayarlarken, birkaç güne ait bir zaman penceresi tanımladık. Ancak, bu pencere içindeki sunucuların (ve VM 'Lerin) tam sıralaması bilinmiyor. VM 'Leri için tam zamanı bildirmek isteyen müşteriler, sanal makine içinden [zamanlanan olayları](scheduled-events.md) ve sorgu KULLANABILIR ve VM yeniden başlatmadan önce 15 dakikalık bir bildirim alabilir.

**S: sanal makinmi yeniden başlatmak için ne kadar sürer?**

Y **:**  SANAL makinenizin boyutuna bağlı olarak, yeniden başlatmanın, self servis bakım penceresi sırasında birkaç dakika geçmesi gerekebilir. Zamanlanan bakım penceresinde Azure tarafından başlatılan yeniden başlatmalar sırasında, yeniden başlatma işlemi genellikle yaklaşık 25 dakika sürer. Cloud Services (Web/çalışan rolü), sanal makine ölçek kümeleri veya kullanılabilirlik kümelerini kullanmanız durumunda, zamanlanan bakım penceresi sırasında her bir VM grubu (UD) arasında 30 dakika verildiğini unutmayın. 

**S: sanal makine ölçek kümeleri söz konusu olduğunda deneyim nedir?**

Y **:** Planlı bakım artık sanal makine ölçek kümeleri için kullanılabilir. Self servis bakımını başlatma yönergeleri için, [VMSS belgesi için planlı bakım](../../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md) bölümüne bakın.

**S: Cloud Services (Web/çalışan rolü) ve Service Fabric söz konusu olduğunda deneyim nedir?**

Y **:** Bu platformlar planlı bakımda etkilenirken, bu platformları kullanan müşteriler, belirli bir zamanda yalnızca tek bir yükseltme etki alanındaki (UD) VM 'Lerin etkilendiğinin güvenli olduğu kabul edilir. Self servis bakımı şu anda Cloud Services (Web/çalışan rolü) ve Service Fabric için kullanılamaz.

**S: sanal makinelerimde hiçbir bakım bilgisi görmüyorum. Sorun nedir?**

Y **:** VM 'leriniz üzerinde herhangi bir bakım bilgisi görmemenizin birkaç nedeni vardır:
1.  Microsoft iç olarak işaretlenmiş bir abonelik kullanıyorsunuz.
2.  VM 'niz bakım için zamanlanmadı. VM 'nizin artık bundan etkilenmemesi için bakım dalgasının sonlandırmasını, iptal edildiğini veya değiştirilmesini sağlayabilirsiniz.
3.  VM listesi görünüminizdeki **bakım** sütunu eklenmemiş. Bu sütunu varsayılan görünüme ekledik, ancak varsayılan olmayan sütunları görmek üzere yapılandırılmış müşterilerin, **bakım** sütununu VM listesi görünümüne el ile eklemesi gerekir.

**S: VM 'IM, ikinci kez bakım için zamanlandı. Kaydol?**

Y **:** Bakım için yeniden dağıtmayı tamamladıktan sonra sanal makineyi bakım için zamanlanmış olarak görebileceğiniz birkaç kullanım durumu vardır:
1.  Bakım dalgasını iptal etmiş ve farklı bir yük ile yeniden başlattık. Hatalı yük algıladık ve yalnızca ek yük dağıtmemiz gerekiyor olabilir.
2.  VM 'niz bir donanım hatası nedeniyle başka bir düğüme *hizmet ısımıştır*
3.  VM 'yi durdurmayı (serbest bırakma) seçtiniz ve yeniden başlatın
4.  VM için **otomatik olarak kapatılacak**


## <a name="next-steps"></a>Sonraki adımlar

[Zamanlanan olaylar](scheduled-events.md)kullanarak VM içinden bakım olaylarını nasıl kaydedebileceğinizi öğrenin.
