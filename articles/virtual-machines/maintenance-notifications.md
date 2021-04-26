---
title: Bakım bildirimleri
description: Azure 'da çalışan sanal makineler için bakım bildirimlerine genel bakış.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 8/12/2020
ms.author: shants
ms.openlocfilehash: 92cb780a80f1010fd1c2f5d19fe616e0285de73b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564535"
---
# <a name="handling-planned-maintenance-notifications"></a>Planlı bakım bildirimlerini işleme

Azure sanal makine konak altyapısının güvenilirlik, performans ve güvenliğini iyileştirmek için düzenli olarak güncelleştirmeler yapar. Güncelleştirmeler, barındırma ortamında düzeltme eki uygulama veya donanım yükseltme ve kullanımdan kaldırma gibi değişikliklerdir. Bu güncelleştirmelerin çoğu, barındırılan sanal makinelere herhangi bir etki olmadan tamamlanır. Ancak, güncelleştirmelerin etkisi olan durumlar vardır:

- Bakım için yeniden başlatma gerekli değilse, Azure, ana bilgisayar güncelleştirilirken VM 'yi birkaç saniye duraklatır. Bu tür bakım işlemleri, hata etki alanına göre hata etki alanına göre uygulanır. Herhangi bir uyarı sistem durumu sinyali alındığında ilerleme durdurulur.

- Bakım için yeniden başlatma gerekiyorsa, bakımın ne zaman planlandığına ilişkin bir uyarı alırsınız. Sizin için uygun olduğunda, bakımı kendiniz başlatabileceğiniz yaklaşık 35 günlük bir zaman penceresi sunulur.


Yeniden başlatma gerektiren planlı bakım dalgaları 'nda zamanlanır. Her bir dalga, farklı kapsama (bölgelere) sahiptir.

- Bir dalga, müşterilere yönelik bir bildirimle başlar. Varsayılan olarak, bildirim abonelik yöneticisine ve ortak yöneticilere gönderilir. [Etkinlik günlüğü uyarılarını](../service-health/alerts-activity-log-service-notifications-portal.md)kullanarak e-posta, SMS ve Web kancaları gibi daha fazla alıcı ve mesajlaşma seçeneği ekleyebilirsiniz.  
- Bir bildirim alındıktan sonra, bir *self servis penceresi* kullanılabilir hale getirilir. Bu pencere sırasında, sanal makinelerinizin hangilerinin etkilendiğini sorgulayabilir ve kendi zamanlama gereksinimlerinize göre bakım başlatabilirsiniz. Self servis penceresi genellikle yaklaşık 35 gündür.
- Self Servis penceresinden sonra, *Zamanlanmış bir bakım penceresi* başlar. Bu pencere sırasında, Azure tarafından zamanlama ve gerekli bakım sanal makinenize uygulanıyor. 

İki Windows 'taki amaç, bakım başlatmak için size yeterli zaman sunmaktır ve Azure 'un bakımın otomatik olarak ne zaman başlatılacağını öğrenirken sanal makinenizi yeniden başlatabilir.


Azure portal, PowerShell, REST API ve CLı kullanarak sanal makinelerinize yönelik bakım pencerelerini sorgulayabilir ve self servis bakımını başlatabilirsiniz.

 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>Bakım, self servis penceresi sırasında kullanılarak başlatılsın mı?  

Aşağıdaki kılavuzlar, bu özelliği kullanıp kullanmayacağınızı ve bakım yapıp başlatmayacağınıza karar vermenize yardımcı olmalıdır. 

> [!NOTE] 
> Self servis bakımı, tüm sanal makinelerinize uygun olmayabilir. VM 'niz için öngörülü yeniden dağıtım olup olmadığını öğrenmek için, bakım durumu ' na **Şimdi başlayın** ' a bakın. Self servis bakımı şu anda Cloud Services (Web/çalışan rolü) ve Service Fabric için kullanılamaz.


Self servis bakımı, **kullanılabilirlik kümeleri** kullanan dağıtımlar için önerilmez. Kullanılabilirlik kümeleri aynı anda yalnızca bir güncelleştirme etki alanını güncelledi. 

- Azure 'un Bakımı tetiklemesine izin verin. Yeniden başlatma gerektiren bakım için, bakım etki alanı güncelleştirme etki alanı tarafından güncelleştirme yapılır. Güncelleştirme etki alanları, bakımı sırayla almaz ve güncelleştirme etki alanları arasında 30 dakikalık bir duraklama olur. 
- Bazı kapasitenin geçici bir kaybı (1 güncelleştirme etki alanı) sorun oluşturacaksa, bakım dönemi boyunca örnek ekleyebilirsiniz. 
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
- İki güncelleştirme etki alanı (UDs) arasında 30 dakikadan uzun bir VM kurtarma süresi gerekir. Güncelleştirme etki alanları arasındaki süreyi denetlemek için, sanal makinelerinizdeki her seferinde bir güncelleştirme etki alanında (UD) bakım tetiklemeniz gerekir.


## <a name="faq"></a>SSS


**S: neden sanal makinelerimi şimdi yeniden başlatmanız gerekiyor?**

Y **:** Azure platformunda yapılan güncelleştirmelerin ve yükseltmelerin büyük bölümü sanal makinenin kullanılabilirliğini etkilememesine karşın, Azure 'da barındırılan sanal makinelerin yeniden başlatılmasını önleyemiyorum durumlar vardır. Sanal makinelerin yeniden başlatılmasına neden olacak sunucularımızı yeniden başlatmamızı gerektiren birkaç değişikliği biriktireceğiz.

**S: kullanılabilirlik kümesi kullanarak yüksek kullanılabilirlik önerinizi izlediğimde, güvenli mıyım?**

Y **:** Bir kullanılabilirlik kümesinde veya sanal makine ölçek kümelerinde dağıtılan sanal makinelerin güncelleştirme etki alanları (UD) kavramı vardır. Azure, bakım gerçekleştirirken UD kısıtlamasına sahiptir ve sanal makineleri farklı bir UD 'den (aynı Kullanılabilirlik kümesi içinde) yeniden başlatmaz.  Azure, sonraki sanal makine grubuna geçmeden önce en az 30 dakika bekler. 

Yüksek kullanılabilirlik hakkında daha fazla bilgi için bkz. [Azure 'da sanal makineler Için kullanılabilirlik](availability.md).

**S: planlı bakım hakkında bilgi almak Nasıl yaparım? mı?**

Y **:** Planlı bir bakım dalgası, bir veya daha fazla Azure bölgesine zamanlama ayarlayarak başlar. Yakında, abonelik yöneticileri, ortak yöneticiler, sahipler ve katkıda bulunanlar (abonelik başına bir e-posta) ile bir e-posta bildirimi gönderilir. Bu bildirimin ek kanalları ve alıcıları, etkinlik günlüğü uyarıları kullanılarak yapılandırılabilir. Planlanmış bakımın zaten zamanlandığı bir bölgeye bir sanal makine dağıtırsanız, bildirimi almazsınız ancak VM 'nin bakım durumunu denetlemeniz gerekir.

**S: Portal, PowerShell veya CLı 'de planlı bakım hakkında herhangi bir gösterge görmüyorum. Ne oldu?**

Y **:** Planlı bakımla ilgili bilgiler yalnızca, planlanan bir bakım dalgası sırasında, yalnızca bundan etkilenecek VM 'Ler için kullanılabilir. Diğer bir deyişle, veri olmadığını görürseniz, bakım dalgası zaten tamamlanmış (veya başlatılmamış) veya sanal makineniz zaten güncelleştirilmiş bir sunucuda barındırılıyor olabilir.

**S: Sanal makinem etkilendiğinde tam olarak bilmeniz için bir yol var mı?**

Y **:** Zamanlamayı ayarlarken, birkaç güne ait bir zaman penceresi tanımladık. Ancak, bu pencere içindeki sunucuların (ve VM 'Lerin) tam sıralaması bilinmiyor. VM 'Leri için tam zamanı bildirmek isteyen müşteriler, sanal makine içinden [zamanlanan olayları](./linux/scheduled-events.md) ve sorgu KULLANABILIR ve VM yeniden başlatmadan önce 15 dakikalık bir bildirim alabilir.

**S: sanal makinmi yeniden başlatmak için ne kadar sürer?**

Y **:**  SANAL makinenizin boyutuna bağlı olarak, yeniden başlatmanın, self servis bakım penceresi sırasında birkaç dakika geçmesi gerekebilir. Zamanlanan bakım penceresinde Azure tarafından başlatılan yeniden başlatmalar sırasında, yeniden başlatma işlemi genellikle yaklaşık 25 dakika sürer. Cloud Services (Web/çalışan rolü), sanal makine ölçek kümeleri veya kullanılabilirlik kümelerini kullanmanız durumunda, zamanlanan bakım penceresi sırasında her bir VM grubu (UD) arasında 30 dakika verildiğini unutmayın.

**S: sanal makine ölçek kümeleri söz konusu olduğunda deneyim nedir?**

Y **:** Planlı bakım artık sanal makine ölçek kümeleri için kullanılabilir. Self servis bakımını başlatma hakkında yönergeler için bkz. [sanal makine ölçek kümeleri için planlı bakım](../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md) belgesi.

**S: Cloud Services (Web/çalışan rolü) ve Service Fabric söz konusu olduğunda deneyim nedir?**

Y **:** Bu platformlar planlı bakımda etkilenirken, bu platformları kullanan müşteriler, belirli bir zamanda yalnızca tek bir yükseltme etki alanındaki (UD) VM 'Lerin etkilendiğinin güvenli olduğu kabul edilir. Self servis bakımı şu anda Cloud Services (Web/çalışan rolü) ve Service Fabric için kullanılamaz.

**S: sanal makinelerimde hiçbir bakım bilgisi görmüyorum. Sorun nedir?**

Y **:** VM 'leriniz üzerinde herhangi bir bakım bilgisi görmemenizin birkaç nedeni vardır:
1.  Microsoft iç olarak işaretlenmiş bir abonelik kullanıyorsunuz.
2.  VM 'niz bakım için zamanlanmadı. VM 'nizin artık bundan etkilenmemesi için bakım dalgasının sonlandırmasını, iptal edildiğini veya değiştirilmesini sağlayabilirsiniz.
3. VM 'yi serbest bırakırsanız ve sonra başlatıdıysanız. Bu, VM 'nin planlı bakım dalga zamanlamalı bir konuma taşınmasına neden olabilir. Bu nedenle VM, bakım bilgilerini daha fazla göstermez. 
4.  VM listesi görünüminizdeki **bakım** sütunu eklenmemiş. Bu sütunu varsayılan görünüme ekledik, ancak varsayılan olmayan sütunları görmek üzere yapılandırılmış müşterilerin, **bakım** sütununu VM listesi görünümüne el ile eklemesi gerekir.

**S: VM 'IM, ikinci kez bakım için zamanlandı. Kaydol?**

Y **:** Bakım için yeniden dağıtmayı tamamladıktan sonra sanal makineyi bakım için zamanlanmış olarak görebileceğiniz birkaç kullanım durumu vardır:
1.  Bakım dalgasını iptal etmiş ve farklı bir yük ile yeniden başlattık. Hatalı yük algıladık ve yalnızca ek yük dağıtmemiz gerekiyor olabilir.
2.  VM 'niz bir donanım hatası nedeniyle başka bir düğüme *hizmet ısımıştır* .
3.  Durdurmayı (serbest bırakma) seçtiniz ve VM 'yi yeniden başlatın.
4.  VM için **otomatik olarak kapatılacak** .



## <a name="next-steps"></a>Sonraki adımlar

[Azure CLI](maintenance-notifications-cli.md), [Azure PowerShell](maintenance-notifications-powershell.md) veya [portalını](maintenance-notifications-portal.md)kullanarak planlı Bakımı işleyebilirsiniz.
