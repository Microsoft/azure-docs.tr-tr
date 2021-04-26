---
title: Azure 'da sanal makine ölçek kümeleri için bakım bildirimleri
description: Azure 'da bakım bildirimlerini görüntüleyin ve sanal makine ölçek kümeleri için self servis bakımını başlatın.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: maintenance-control
ms.date: 11/12/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: ec8d211bd25eb04f9e000af950cea9a28a0d1874
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762848"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Sanal makine ölçek kümeleri için planlı bakım bildirimleri


Azure, sanal makineler (VM) için konak altyapısının güvenilirliğini, performansını ve güvenliğini geliştirmek üzere düzenli aralıklarla güncelleştirmeler gerçekleştirir. Güncelleştirmeler, barındırma ortamına düzeltme eki uygulama veya donanım yükseltme ve yetkisini alma içerebilir. Çoğu güncelleştirme barındırılan VM 'Leri etkilemez. Ancak, güncelleştirmeler şu senaryolarda VM 'Leri etkiler:

- Bakım için yeniden başlatma gerekli değilse, Azure, ana bilgisayar güncelleştirilirken VM 'yi birkaç saniye duraklatır. Bu tür bakım işlemleri, hata etki alanına göre hata etki alanına göre uygulanır. Herhangi bir uyarı sistem durumu sinyali alındığında ilerleme durdurulur.

- Bakım için yeniden başlatma gerekiyorsa, bakımın ne zaman planlandığına ilişkin bir uyarı alırsınız. Bu durumlarda, sizin için uygun olduğunda Bakımı kendiniz başlatabileceğiniz 35 gün olan bir zaman penceresi vermiş olursunuz.


Yeniden başlatma gerektiren planlı bakım dalgaları 'nda zamanlanır. Her bir dalga farklı kapsama (bölgelere) sahiptir:

- Bir dalga, müşterilere yönelik bir bildirimle başlar. Varsayılan olarak, bildirim abonelik sahibine ve ortak sahiplerine gönderilir. Azure [etkinlik günlüğü uyarılarını](../azure-monitor/essentials/platform-logs-overview.md)kullanarak e-posta, SMS ve Web kancaları gibi alıcılar ve mesajlaşma seçeneklerini bildirimlere ekleyebilirsiniz.  
- Bildirim ile *self servis penceresi* kullanılabilir hale getirilir. Genellikle 35 gün olan bu pencere sırasında, sanal makinelerinizi dalgaya dahil edildiğini bulabilirsiniz. Bakım, kendi zamanlama gereksinimlerinize göre proaktif olarak başlatılabilir.
- Self Servis penceresinden sonra, *Zamanlanmış bir bakım penceresi* başlar. Bu pencere sırasında bir noktada, Azure tarafından zamanlama ve gerekli bakım sanal makinenize uygulanıyor. 

İki Windows 'taki amaç, bakım başlatmak için size yeterli zaman sunmaktır ve Azure 'un bakımın otomatik olarak ne zaman başlatılacağını öğrenirken VM 'nizi yeniden başlatabilir.

Azure portal, PowerShell, REST API ve Azure CLı kullanarak sanal makine ölçek kümesi VM 'lerinize yönelik bakım pencerelerini sorgulayabilir ve self servis bakımını başlatabilirsiniz.

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Self servis penceresi sırasında bakım başlatmanız mı gerekiyor?  

Aşağıdaki kılavuzlar, seçtiğiniz zamanda bakımın başlatılıp başlatılmayacağını belirlemenize yardımcı olabilir.

> [!NOTE] 
> Self servis bakımı, tüm sanal makinelerinize uygun olmayabilir. VM 'niz için öngörülü yeniden dağıtma 'nın kullanılabilir olup olmadığını anlamak için bakım durumu ' nda **Şimdi Başlat** ' a bakın. Şu anda self servis bakımı Azure Cloud Services (Web/çalışan rolü) ve Azure Service Fabric için kullanılamaz.


*Kullanılabilirlik kümelerini* kullanan dağıtımlar için self servis bakımı önerilmez. Kullanılabilirlik kümeleri, her zaman yalnızca bir güncelleştirme etki alanının etkilediği yüksek oranda kullanılabilir kurulumlardır. Kullanılabilirlik kümeleri için:

- Azure 'un Bakımı tetiklemesine izin verin. Yeniden başlatma gerektiren bakım için, bakım, etki alanını güncelleştir etki alanı ile güncelleştirme yapılır. Güncelleştirme etki alanları, bakım sırayla her zaman almamıştır. Güncelleştirme etki alanları arasında 30 dakikalık bir duraklama vardır.
- Kapasiteniz (1/güncelleştirme etki alanı sayısı) için geçici bir sorun varsa, bakım dönemi boyunca ek örnekler ayırarak kaybı için kolayca telafi sağlayabilirsiniz.
- Yeniden başlatma gerektirmeyen bakım için, güncelleştirmeler hata etki alanı düzeyinde uygulanır. 
    
Aşağıdaki senaryolarda self servis **bakımını kullanmayın:** 

- Sanal makinelerinizi, el ile, DevTest Labs kullanarak, otomatik kapatmayı kullanarak ya da bir zamanlamayı izleyerek kapatırsanız. Bu senaryolarda self servis bakımı, bakım durumunu döndürebilir ve ek kapalı kalma süresi oluşmasına neden olabilir.
- Bildiğiniz kısa süreli VM 'lerde, bakım dalgasının sonundan önce silineceğini görürsünüz. 
- Güncelleştirmeden sonra sürdürmek istediğiniz yerel (kısa ömürlü) diskte büyük bir duruma sahip iş yükleri için. 
- VM 'nizi sık sık yeniden boyutlandırırsanız. Bu senaryo bakım durumunu döndürebilir. 
- Bakım kapatması başlamadan önce proaktif yük devretme veya daha kısa süre önce iş yükünüz için 15 dakika kapanma sağlayan zamanlanmış olayları benimsediğiniz takdirde.

Zamanlanmış bakım aşamasında sanal makineyi kesintisiz olarak çalıştırmayı planlıyorsanız ve önceki onay göstergelerden hiçbiri uygulanmamışsa self servis **bakımını kullanın.** 

Aşağıdaki durumlarda self servis bakımını kullanmak en iyisidir:

- Yönetim veya müşterinizin tam bir bakım penceresi ile iletişim kurması gerekir. 
- Bakımı belirli bir tarihle doldurmanız gerekir. 
- Güvenli kurtarmayı güvence altına almak için örneğin, çok katmanlı bir uygulamada bakım sırasını kontrol etmeniz gerekir.
- İki güncelleştirme etki alanı arasında 30 dakikadan daha fazla VM kurtarma süresi gerekir. Güncelleştirme etki alanları arasındaki süreyi denetlemek için, sanal makinelerinizdeki her seferinde tek bir güncelleştirme etki alanında bakım tetiklemeniz gerekir.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>Portalda bakımın etkilediği sanal makine ölçek kümelerini görüntüle

Planlı bir bakım dalgası zamanlandığında, Azure portal kullanarak yaklaşan bakım dalgasının etkilediği sanal makine ölçek kümelerinin listesini görüntüleyebilirsiniz. 

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Sol taraftaki menüden **tüm hizmetler**' i seçin ve ardından **Sanal Makine Ölçek Kümeleri**' ni seçin.
3. **Sanal Makine Ölçek Kümeleri** altında, **Sütunları Düzenle** ' yi seçerek kullanılabilir sütunların listesini açın.
4. **Kullanılabilir sütunlar** bölümünde **self servis bakımı**' nı seçin ve ardından **Seçili sütunlar** listesine taşıyın. **Uygula**’yı seçin.  

    **Self servis bakım** öğesinin daha kolay bulunmasını kolaylaştırmak Için, **kullanılabilir sütunlar** bölümündeki açılır seçeneği **Tüm** **Özellikler**' e değiştirebilirsiniz.

**Self servis bakım** sütunu artık sanal makine Ölçek Kümeleri listesinde görünür. Her sanal makine ölçek kümesi, self servis bakım sütunu için aşağıdaki değerlerden birine sahip olabilir:

| Değer | Açıklama |
|-------|-------------|
| Evet | Sanal makine ölçek kümesindeki en az bir VM self servis penceresinde. Bu self servis penceresi sırasında istediğiniz zaman bakım başlatabilirsiniz. | 
| No | Etkilenen sanal makine ölçek kümesindeki bir self servis penceresinde VM yok. | 
| - | Sanal makineleriniz ölçek kümeleriniz planlı bir bakım dalgasının parçası değildir.| 

## <a name="notification-and-alerts-in-the-portal"></a>Portalda bildirim ve uyarılar

Azure, abonelik sahibine ve ortak sahipler grubuna bir e-posta göndererek planlı bakım için bir zamanlama iletişim kurar. Etkinlik günlüğü uyarıları oluşturarak bu iletişime alıcılar ve kanallar ekleyebilirsiniz. Daha fazla bilgi için bkz. [Azure etkinlik günlüğü ile abonelik izleme etkinliği](../azure-monitor/essentials/platform-logs-overview.md).

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Sol taraftaki menüden **izleyici**' yi seçin. 
3. **İzleyici-uyarılar (klasik)** bölmesinde **+ etkinlik günlüğü uyarısı Ekle**' yi seçin.
4. **Etkinlik günlüğü uyarısı Ekle** sayfasında istenen bilgileri seçin veya girin. **Ölçütlerde** aşağıdaki değerleri ayarladığınızdan emin olun:
   - **Olay kategorisi**: **hizmet durumunu** seçin.
   - **Hizmetler**: **sanal makine ölçek kümelerini ve sanal makineleri** seçin.
   - **Yazın**: **Planlı bakım**' ı seçin. 
    
Etkinlik günlüğü uyarılarını yapılandırma hakkında daha fazla bilgi edinmek için bkz. [etkinlik günlüğü uyarıları oluşturma](../azure-monitor/alerts/activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Portaldan sanal makine ölçek kümesi üzerinde bakım başlatın

Daha fazla bakım ile ilgili ayrıntıları sanal makine ölçek kümelerine genel bakış bölümüne bakabilirsiniz. Planlı bakım dalgasına sanal makine ölçek kümesindeki en az bir VM eklenirse, sayfanın üstüne yakın yeni bir bildirim şeridi eklenir. **Bakım** sayfasına gitmek için bildirim şeridini seçin. 

**Bakım** sayfasında, hangi VM örneğinin planlı bakımın etkilendiğini görebilirsiniz. Bakımı başlatmak için, etkilenen VM 'ye karşılık gelen onay kutusunu seçin. Ardından,  **Bakımı Başlat**' ı seçin.

Bakım başladıktan sonra, sanal makine ölçek kümesindeki etkilenen VM 'Ler yerinde bakımda ve geçici olarak kullanılamaz. Self servis penceresini kaçırdıysanız, sanal makine ölçek kümesi Azure tarafından korunacağı zaman penceresini yine de görebilirsiniz.
 
## <a name="check-maintenance-status-by-using-powershell"></a>PowerShell kullanarak bakım durumunu denetleme

Sanal makine ölçek kümelerindeki VM 'Lerin bakım için zamanlandığını görmek için Azure PowerShell kullanabilirsiniz. Planlı bakım bilgileri, parametresini kullandığınızda [Get-AzVmss](/powershell/module/az.compute/get-azvmss) cmdlet 'i kullanılarak kullanılabilir `-InstanceView` .
 
Bakım bilgileri yalnızca bakım planlandığından döndürülür. VM örneğini etkileyen bakım zamanlanmamışsa, cmdlet herhangi bir bakım bilgisi döndürmez. 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

Aşağıdaki özellikler **MaintenanceRedeployStatus** altında döndürülür: 

| Değer | Açıklama   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Şu anda VM 'de bakım başlatılıp başlatılmayacağını belirtir. |
| PreMaintenanceWindowStartTime         | VM 'niz üzerinde bakım başlatabilmeniz için bakım self servis penceresinin başlangıcı. |
| PreMaintenanceWindowEndTime           | VM 'niz üzerinde bakım başlatabilmeniz için bakım self servis penceresinin sonu. |
| MaintenanceWindowStartTime            | Azure 'un VM 'niz üzerinde bakım başlattığı zamanlanan bakım başlangıcı. |
| MaintenanceWindowEndTime              | Azure 'un VM 'niz üzerinde Bakımı başlattığı bakım zamanlandı penceresinin sonu. |
| LastOperationResultCode               | VM 'de son bakım başlatma denemesinin sonucu. |



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>PowerShell kullanarak VM örneğiniz üzerinde bakım başlatma

**IsCustomerInitiatedMaintenanceAllowed** **true** olarak ayarlandıysa, bir VM üzerinde bakım başlatabilirsiniz. Parametresiyle [set-AzVmss](/powershell/module/az.compute/set-azvmss) cmdlet 'ini kullanın `-PerformMaintenance` .

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>CLı kullanarak bakım durumunu denetleme

Planlanan bakım bilgilerini [az VMSS List-Instances](/cli/azure/vmss#az_vmss_list_instances)kullanarak görüntüleyebilirsiniz.
 
Bakım bilgileri yalnızca bakım planlandığından döndürülür. VM örneğini etkileyen bakım zamanlanmamışsa, komut herhangi bir bakım bilgisi döndürmez. 

```azurecli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

Aşağıdaki özellikler her bir sanal makine örneği için **MaintenanceRedeployStatus** altında döndürülür: 

| Değer | Açıklama   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Şu anda VM 'de bakım başlatılıp başlatılmayacağını belirtir. |
| PreMaintenanceWindowStartTime         | VM 'niz üzerinde bakım başlatabilmeniz için bakım self servis penceresinin başlangıcı. |
| PreMaintenanceWindowEndTime           | VM 'niz üzerinde bakım başlatabilmeniz için bakım self servis penceresinin sonu. |
| MaintenanceWindowStartTime            | Azure 'un VM 'niz üzerinde bakım başlattığı zamanlanan bakım başlangıcı. |
| MaintenanceWindowEndTime              | Azure 'un VM 'niz üzerinde Bakımı başlattığı bakım zamanlandı penceresinin sonu. |
| LastOperationResultCode               | VM 'de son bakım başlatma denemesinin sonucu. |


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>CLı kullanarak VM örneğiniz üzerinde bakım başlatın

Aşağıdaki çağrı, `IsCustomerInitiatedMaintenanceAllowed` **doğru** olarak ayarlandıysa bir VM örneği üzerinde bakım başlatır:

```azurecli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>SSS

**S: neden VM 'lerimi şimdi yeniden başlatmanız gerekiyor?**

Y **:** Azure platformunda yapılan güncelleştirmelerin ve yükseltmelerin çoğu VM kullanılabilirliğini etkilemese de, bazı durumlarda Azure 'da barındırılan VM 'Leri yeniden başlatmayı önleyemiyorum. VM 'nin yeniden başlatılmasına neden olacak sunucularımızı yeniden başlatmamızı gerektiren birkaç değişikliği biriktireceğiz.

**S: kullanılabilirlik kümesi kullanarak yüksek kullanılabilirlik önerinizi izlediğimde, güvenli mıyım?**

Y **:** Bir kullanılabilirlik kümesinde veya sanal makine ölçek kümelerinde dağıtılan sanal makineler güncelleştirme etki alanlarını kullanır. Azure, bakım gerçekleştirirken güncelleştirme etki alanı kısıtlamasını kabul eder ve farklı bir güncelleştirme etki alanından (aynı Kullanılabilirlik kümesi içinde) VM 'Leri yeniden başlatmaz. Azure, sonraki VM grubuna geçmeden önce en az 30 dakika bekler. 

Yüksek kullanılabilirlik hakkında daha fazla bilgi için bkz. [Azure 'da sanal makineler Için bölgeler ve kullanılabilirlik](../virtual-machines/availability.md).

**S: planlı bakım hakkında nasıl bildirim alabilir?**

Y **:** Planlı bir bakım dalgası, bir veya daha fazla Azure bölgesine zamanlama ayarlayarak başlar. Yakında, abonelik yöneticileri, ortak yöneticiler, sahipler ve katkıda bulunanlar (abonelik başına bir e-posta) ile bir e-posta bildirimi gönderilir. Bu bildirimin ek kanalları ve alıcıları, etkinlik günlüğü uyarıları kullanılarak yapılandırılabilir. Bir sanal makineyi planlı bakımın zaten zamanlandığı bir bölgeye dağıtırsanız, bildirimi almazsınız. Bunun yerine, VM 'nin bakım durumunu kontrol edin.

**S: Portal, PowerShell veya CLı 'de planlı bakım hakkında herhangi bir gösterge görmüyorum. Ne oldu?**

Y **:** Planlı bakımla ilgili bilgiler, planlanan bakım dalgasının yalnızca planlanmış bakımın etkilediği VM 'Ler için kullanılabilir. Veri görmüyorsanız, bakım dalgası zaten bitmiş (veya başlatılmamış) veya VM 'niz güncelleştirilmiş bir sunucuda zaten barındırılıyor olabilir.

**S: VM 'im etkilenmeden tam olarak bilmeniz için bir yol var mı?**

Y **:** Zamanlamayı belirlediğimiz zaman, birkaç güne ait bir zaman penceresi tanımladık. Sunucuların (ve VM’lerin) bu pencere içindeki sırası tam olarak bilinmez. VM 'nizin güncelleştirileceği tam süreyi biliyorsanız, [zamanlanan olayları](../virtual-machines/windows/scheduled-events.md)kullanabilirsiniz. Zamanlanmış olayları kullandığınızda VM 'nin içinden sorgulama yapabilir ve VM yeniden başlatmadan önce 15 dakikalık bir bildirim alabilirsiniz.

**S: VM 'mi yeniden başlatmak için ne kadar sürer?**

Y **:**  SANAL makinenizin boyutuna bağlı olarak, yeniden başlatma işlemi, self servis bakım penceresi sırasında birkaç dakika sürebilir. Zamanlanan bakım penceresinde Azure tarafından başlatılan yeniden başlatmalar sırasında, yeniden başlatma işlemi genellikle yaklaşık 25 dakika sürer. Cloud Services (Web/çalışan rolü), sanal makine ölçek kümeleri veya kullanılabilirlik kümeleri kullanıyorsanız, zamanlanan bakım penceresi sırasında her bir VM grubu (güncelleştirme etki alanı) arasında 30 dakika verilir. 

**S: sanal makinelerimde hiçbir bakım bilgisi görmüyorum. Sorun nedir?**

Y **:** Sanal makinelerinizdeki bakım bilgilerini görmemenizin birkaç nedeni vardır:
   - *Microsoft iç* olarak işaretlenmiş bir abonelik kullanıyorsunuz.
   - VM 'niz bakım için zamanlanmadı. Bakım dalgasının sonlandırılması, iptal edilmiş olması veya sanal makinelerinizin bundan sonra bundan etkilenmemesi için değiştirilmiş olabilir.
   - VM listesi görünüminizdeki **bakım** sütunu eklenmemiş. Bu sütunu varsayılan görünüme eklediğimiz halde, görünümünüzü varsayılan olmayan sütunları görmek üzere yapılandırırsanız, **bakım** sütununu VM listesi görünümünizin el ile eklemeniz gerekir.

**S: VM 'IM, ikinci kez bakım için zamanlandı. Kaydol?**

Y **:** Birkaç kullanım durumunda, bakımını zaten tamamladıktan ve yeniden dağıtıldıktan sonra sanal ağınız bakım için zamanlanır:
   - Bakım dalgasını iptal etmiş ve farklı bir yük ile yeniden başlattık. Hatalı bir yük algıladığımız ve yalnızca ek yük dağıtmemiz gereken bir durum olabilir.
   - VM 'niz bir donanım hatası nedeniyle başka bir düğüme *hizmet ısımıştır* .
   - Durdurmayı (serbest bırakma) seçtiniz ve VM 'yi yeniden başlatın.
   - VM için **otomatik olarak kapatılacak** .

## <a name="next-steps"></a>Sonraki adımlar

[Zamanlanan olayları](../virtual-machines/windows/scheduled-events.md)kullanarak VM içinden bakım olaylarına nasıl kaydolacağınızı öğrenin.
