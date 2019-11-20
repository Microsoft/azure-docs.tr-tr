---
title: include dosyası
description: include dosyası
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 8/22/2019
ms.author: shants
ms.custom: include file
ms.openlocfilehash: b2a7bbef2c421281780c0191fa32381468899bbf
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020362"
---
Azure, sanal makineler için konak altyapısının güvenilirliğini, performansını ve güvenliğini artırmak üzere platformunu düzenli olarak güncelleştirir. Bu güncelleştirmelerin amacı, ağ bileşenlerini yükseltmek veya donanım yetkisini almak için barındırma ortamındaki yazılım bileşenlerinde düzeltme eki uygulama bileşenlerinden oluşur. 

Güncelleştirmeler barındırılan VM 'Leri nadiren etkiler. Güncelleştirmelerin bir etkisi olduğunda Azure, güncelleştirmeler için en az ımpacımpact yöntemini seçer:

- Güncelleştirme için bir yeniden başlatma gerektirmiyorsa, konak güncelleştirilirken VM duraklatılır veya VM canlı olarak zaten güncelleştirilmiş bir konağa geçirilir.

- Bakım için yeniden başlatma gerekiyorsa, planlı bakım hakkında bilgilendirilirsiniz. Azure aynı zamanda, sizin için uygun bir zamanda bakımı kendiniz başlatabileceğinizi bir zaman penceresi sağlar. Bakım acil değilse, kendi kendine bakım penceresi genellikle 35 gün olur. Azure, planlı platform bakımının VM 'Lerin yeniden başlatılmasını gerektirdiği durum sayısını azaltmak için teknolojiden yatırım yapıyor. 

Bu sayfa, Azure 'un her iki türde bakım gerçekleştirmesini açıklar. Planlanmamış Olaylar (kesintiler) hakkında daha fazla bilgi için bkz. [Windows Için VM 'lerin kullanılabilirliğini yönetme](../articles/virtual-machines/windows/manage-availability.md) veya [Linux](../articles/virtual-machines/linux/manage-availability.md)için ilgili makale.

Bir VM içinde, Windows veya [Linux](../articles/virtual-machines/linux/scheduled-events.md)için [zamanlanan olaylar kullanarak](../articles/virtual-machines/windows/scheduled-events.md) yaklaşan bakım hakkında bildirim alabilirsiniz.

Planlı bakım yönetimi hakkında yönergeler için bkz. [Linux için planlı bakım bildirimlerini işleme](../articles/virtual-machines/linux/maintenance-notifications.md) veya [Windows](../articles/virtual-machines/windows/maintenance-notifications.md)için ilgili makale.

## <a name="maintenance-that-doesnt-require-a-reboot"></a>Yeniden başlatma gerektirmeyen bakım

Daha önce belirtildiği gibi, çoğu platform güncelleştirmesi müşteri sanal makinelerini etkilemez. Etkili olmayan bir güncelleştirme mümkün olmadığında Azure, müşteri VM 'lerine en az olumsuz bir güncelleştirme mekanizması seçer. 

Sıfır olmayan etki çok etkili bakım, VM 'yi 10 saniyeden daha az süreyle duraklatır. Azure, belirli durumlarda, bellek koruma bakım mekanizmalarını kullanır. Bu mekanizmalar, VM 'yi en fazla 30 saniye duraklatıp RAM 'teki belleği korur. VM daha sonra sürdürülür ve saati otomatik olarak eşitlenir. 

Bellek koruma bakımı, Azure VM 'lerinin yüzde 90 ' sinden daha fazlası için geçerlidir. G, M, N ve H serisi için çalışmaz. Azure, dinamik geçiş teknolojileri kullanır ve duraklatma sürelerini azaltmak için bellek koruma bakım mekanizmalarını geliştirir.  

Yeniden başlatma gerektirmeyen bu bakım işlemleri, tek seferde bir hata etki alanı uygulanır. Herhangi bir uyarı sistem durumu sinyali aldıklarında bunları durdurur. 

Bu tür güncelleştirmeler bazı uygulamaları etkileyebilir. SANAL makine dinamik olarak farklı bir konağa geçirildiğinde, bazı hassas iş yükleri VM duraklatmaya en az birkaç dakika sonunda küçük bir performans düşüşü gösterebilir. VM bakımına hazırlanmak ve Azure Bakımı sırasında etkileri azaltmak için, bu tür uygulamalar için Windows veya [Linux](../articles/virtual-machines/linux/scheduled-events.md) [için zamanlanan olaylar kullanmayı](../articles/virtual-machines/windows/scheduled-events.md) deneyin. Azure Ayrıca, [Azure adanmış konaklar](../articles/virtual-machines/windows/dedicated-hosts.md) ve [yalıtılmış VM](../articles/security/fundamentals/isolation-choices.md)'lerde bu tür sıfır olmayan etki platformu bakımı üzerinde tam denetim sağlar. Bakım denetimi özelliği önizlemededir ve [Kaydolma formunu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6lJf7DwiQxNmz51ksQvxV9UNUM3UllWUjBMTFZQUFhHUDI0VTBPQlJFNS4u)göndererek erişim isteğinde bulunabilir. Bu, sıfır olmayan etki platformu güncelleştirmelerini atlama ve güncelleştirmeleri 35 günlük bir işlem penceresinde dilediğiniz zaman toplu olarak uygulama seçeneği sunar.

### <a name="live-migration"></a>Dinamik geçiş

Dinamik geçiş, bir yeniden başlatma gerektirmeyen ve VM için belleği koruyan bir işlemdir. Genellikle 5 saniyeden fazla bir duraklama veya dondurma ile devam etmesine neden olur. G, M, N ve H serisi dışında tüm hizmet olarak altyapı (IaaS) VM 'Leri dinamik geçiş için uygun değildir. Uygun VM 'Ler, Azure Fleet 'e dağıtılan IaaS sanal makinelerinin yüzde 90 ' inden fazlasını temsil eder. 

Azure platformu, aşağıdaki senaryolarda dinamik geçiş başlatır:
- Planlı bakım
- Donanım hatası
- Ayırma iyileştirmeleri

Bazı planlı bakım senaryoları dinamik geçiş kullanır ve dinamik geçiş işlemleri başlatıldığında Zamanlanan Olaylar kullanımı için kullanabilirsiniz.

Dinamik geçiş Ayrıca, Azure Machine Learning algoritmalarda olası bir donanım hatası tahmin edildiğinde veya VM ayırmalarını iyileştirmek istediğinizde VM 'Leri taşımak için de kullanılabilir. Azaltılmış donanım örneklerini algılayan tahmine dayalı modelleme hakkında daha fazla bilgi için bkz. tahmine [dayalı makine öğrenimi ve dinamik geçiş Ile Azure VM dayanıklılığı geliştirme](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure). Canlı geçiş bildirimleri Izleyici ve hizmet durumu günlüklerinde Azure portal ve bu Hizmetleri kullanıyorsanız Zamanlanan Olaylar görüntülenir.

## <a name="maintenance-that-requires-a-reboot"></a>Yeniden başlatma gerektiren bakım

Planlanan bakım için VM 'Lerin yeniden başlatılması gereken nadir bir durumda, önceden bildirilir. Planlı bakım iki aşamaya sahiptir: Self Servis aşaması ve zamanlanmış bakım aşaması.

Genellikle dört hafta içinde olan *self servis aşaması*sırasında, VM 'lerinize bakım başlatabilirsiniz. Self Servis 'nin bir parçası olarak, her bir VM 'yi sorgulayabilir ve son bakım isteğinizin sonucunu görebilirsiniz.

Self servis bakımı 'nı başlattığınızda, VM 'niz zaten güncelleştirilmiş bir düğüme yeniden dağıtılır. VM yeniden başlatıldığında geçici disk kaybedilir ve sanal ağ arabirimiyle ilişkili dinamik IP adresleri güncelleştirilir.

Self servis bakımı sırasında bir hata oluşursa, işlem duraklar, VM güncellenmez ve self servis bakımını yeniden deneme seçeneğini alırsınız. 

Self Servis aşaması sona erdiğinde, *zamanlanmış bakım aşaması* başlar. Bu aşamada bakım aşaması için sorgulama devam edebilir, ancak Bakımı kendiniz başlatamazsınız.

Yeniden başlatma gerektiren bakım yönetimi hakkında daha fazla bilgi için bkz. [Linux için planlı bakım bildirimlerini işleme](../articles/virtual-machines/linux/maintenance-notifications.md) veya [Windows](../articles/virtual-machines/windows/maintenance-notifications.md)için ilgili makale. 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Zamanlanmış bakım sırasında kullanılabilirlik konuları 

Zamanlanan bakım aşamasına kadar beklemek isterseniz, VM 'lerinizin en yüksek kullanılabilirliğini sürdürmek için göz önünde bulundurmanız gereken birkaç nokta vardır. 

#### <a name="paired-regions"></a>Eşleştirilmiş bölgeler

Her Azure bölgesi aynı coğrafi yakın çevre kapsamında başka bir bölgeyle eşleştirilir. Birlikte, bir bölge çifti oluşturur. Zamanlanan Bakım aşamasında, Azure yalnızca bölge çiftinin tek bölgesindeki VM 'Leri güncelleştirir. Örneğin, Orta Kuzey ABD ' de sanal makineyi güncelleştirirken, Azure Orta Güney ABD aynı anda hiçbir sanal makineyi güncelleştirmez. Ancak, Kuzey Avrupa gibi diğer bölgelerde, Doğu ABD ile aynı zamanda bakım yapılabilir. Bölge çiftlerinin nasıl çalıştığını anlamak, sanal makinelerinizi bölgeler arasında daha iyi dağıtmanıza yardımcı olabilir. Daha fazla bilgi için bkz. [Azure bölge çiftleri](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Kullanılabilirlik kümeleri ve ölçek kümeleri

Azure VM 'lerinde bir iş yükü dağıttığınızda, uygulamanız için yüksek kullanılabilirlik sağlamak üzere VM 'Leri bir *kullanılabilirlik kümesi* içinde oluşturabilirsiniz. Kullanılabilirlik kümelerini kullanarak, yeniden başlatma gerektiren kesinti veya bakım olayları sırasında en az bir VM 'nin kullanılabilir olmasını sağlayabilirsiniz.

Bir kullanılabilirlik kümesi içinde, tek tek VM 'Ler en fazla 20 güncelleştirme etki alanına (UDs) yayılır. Zamanlanan Bakım sırasında, belirli bir zamanda yalnızca bir UD güncellenir. UDs, her zaman ardışık olarak güncellenmez. 

Sanal makine *Ölçek Kümeleri* , aynı VM 'lerin bir kümesini tek bir kaynak olarak dağıtmak ve yönetmek için kullanabileceğiniz bir Azure işlem kaynağıdır. Ölçek kümesi, bir kullanılabilirlik kümesindeki VM 'Ler gibi UDs 'lerde otomatik olarak dağıtılır. Kullanılabilirlik kümelerinde olduğu gibi, ölçek kümeleri kullandığınızda, zamanlanan bakım sırasında belirli bir zamanda yalnızca bir UD güncelleştirilir.

Sanal makinelerinizi yüksek kullanılabilirlik için ayarlama hakkında daha fazla bilgi için bkz. [Windows Için sanal makinelerinizin kullanılabilirliğini yönetme](../articles/virtual-machines/windows/manage-availability.md) veya [Linux](../articles/virtual-machines/linux/manage-availability.md)için ilgili makale.
