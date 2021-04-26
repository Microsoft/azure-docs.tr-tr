---
title: Cloud Services için Kaynak Durumu (klasik)
description: Bu makalede, Microsoft Azure Cloud Services (klasik) için Kaynak Durumu denetimi (RHC) desteği ele bir konuşuyor
ms.topic: article
ms.service: cloud-services
ms.subservice: resource-health
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 46a27e42d6b3440914ca0d32e1ae2138b128021e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935167"
---
# <a name="resource-health-check-rhc-support-for-azure-cloud-services-classic"></a>Azure Cloud Services için Kaynak Durumu denetimi (RHC) desteği (klasik)

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).

Bu makalede, [Microsoft Azure Cloud Services (klasik)](https://azure.microsoft.com/services/cloud-services) Için kaynak durumu denetimi (RHC) desteği ele bir konuşuyor

Bulut hizmetleri için [Azure Kaynak durumu](../service-health/resource-health-overview.md) , bulut hizmeti dağıtımınızı, rolleri & rol örneklerini etkileyen hizmet sorunlarını tanılamanıza ve destek almanıza yardımcı olur. Dağıtım, rol & rol örneği düzeyinde bulut hizmetlerinizin güncel ve geçmiş durumunu raporlar.

Azure durum, geniş bir Azure müşterisi kümesini etkileyen sorunlar hakkında raporlar. Kaynak Durumu kaynaklarınızın sistem durumunun kişiselleştirilmiş bir panosunu sağlar. Kaynak Durumu, Azure hizmet sorunları nedeniyle kaynaklarınızın kullanılamadığı tüm süreleri gösterir. Bu veriler, bir SLA 'nın ihlal edildiğini görmenizi kolaylaştırır.

:::image type="content" source="media/cloud-services-allocation-failure/rhc-blade-cloud-services.png" alt-text="Görüntü Azure portal kaynak sistem durumu denetimi dikey penceresini gösterir.":::

## <a name="how-health-is-checked-and-reported"></a>Sağlık nasıl denetlenir ve bildirilir?
Kaynak sistem durumu bir dağıtım veya rol düzeyinde bildirilir. Durum denetimi rol örneği düzeyinde gerçekleşir, durumu topladık ve rol düzeyinde raporlarız. Örneğin Tüm rol örnekleri varsa, rol durumu kullanılabilir olur. Benzer şekilde, tüm rollerin sistem durumunu toplar ve dağıtım düzeyinde rapor edin. Örneğin Tüm roller varsa dağıtım durumu kullanılabilir hale gelir. 

## <a name="why-i-cannot-see-health-status-for-my-staging-slot-deployment"></a>Hazırlama yuvası dağıtımımın sistem durumunu neden göremiyorum?
Kaynak sistem durumu denetimleri yalnızca üretim yuvası dağıtımı için çalışır. Hazırlama yuvası dağıtımı henüz desteklenmiyor. 

## <a name="does-resource-health-check-also-check-the-health-of-the-application"></a>Kaynak Durumu denetim Ayrıca uygulamanın sistem durumunu denetler mi?
Hayır, yalnızca rol örnekleri için sistem durumu denetimi gerçekleşir ve uygulama durumunu izlemez. Örneğin 3 ' ün 3 rol örneği sağlıksız olsa bile, uygulama kullanılabilir olmaya devam edebilir. RHC [yük dengeleyici araştırmaları](../load-balancer/load-balancer-custom-probe-overview.md) veya Konuk Aracısı araştırması kullanmaz. Bu nedenle, müşterilerin uygulamalarının sistem durumunu izlemek için yük dengeleyici araştırmalarını kullanmaya devam etmesi gerekir. 

## <a name="what-are-the-annotations-for-cloud-services"></a>Cloud Services ek açıklamaları nelerdir?
Ek açıklamalar, dağıtımın veya rollerin sistem durumudur. Sistem durumunu temel alan farklı ek açıklamalar vardır, durum değişikliği nedeni, vb. 

## <a name="what-does-it-mean-by-role-instance-being-unavailable"></a>Rol örneği "kullanılamıyor" olarak ne anlama geliyor?
Bu, rol örneğinin platforma sağlıklı bir sinyal yaymadığını gösterir. Sağlıklı sinyalin neden yayınlanamadığına ilişkin ayrıntılı açıklama için lütfen rol örneği durumunu kontrol edin.

## <a name="what-does-it-mean-by-deployment-being-unknown"></a>Dağıtıma "Bilinmeyen" olarak ne anlama geliyor?
Bilinmiyor, bulut hizmeti dağıtımının toplu sistem durumunun belirlenemediği anlamına gelir. Genellikle bu, bulut hizmeti için oluşturulan üretim dağıtımı olmadığını, dağıtımın yeni oluşturulduğunu (ve Azure 'un sistem durumu olaylarını toplamaya başladığı) veya platformun bu dağıtım için sistem durumu olaylarını toplama konusunda sorun yaşadığını gösterir.

## <a name="why-does-role-instance-annotations-mentions-vms-instead-of-role-instances"></a>Rol örneği ek açıklamaları neden rol örnekleri yerine VM 'Leri bahsetmektedir?
Rol örnekleri temelde sanal makineler olduğundan ve VM 'Ler için sistem durumu denetimi rol örnekleri için yeniden kullanıldığından, VM terimi rol örneklerini temsil etmek için kullanılır. 

## <a name="cloud-services-deployment-level-annotations--their-meanings"></a>Cloud Services (dağıtım düzeyi) ek açıklamaları anlamları &
| Ek Açıklama | Description | 
| --- | --- | 
| Kullanılabilir| Bu bulut hizmeti dağıtımını etkileyen bilinen bir Azure platformu sorunu yok |
| Bilinmiyor | Şu anda bu bulut hizmeti dağıtımının sistem durumunu belirleyemiyoruz | 
| Kaynak Durumu ayarlama | Bu kaynak için kaynak durumu ayarlanıyor. Kaynak sistem durumu, Azure kaynaklarınızı etkileyen devam eden ve geçmiş olaylar hakkında ayrıntılı bilgi sağlamak için bunları izler|
| Düzeyi düşürüldü | Bulut Hizmeti dağıtımınızın düzeyi düşürüldü. Cloud Service dağıtımınızı otomatik olarak kurtarmaya ve sorunun kaynağını belirlemeye çalışıyoruz. Şu anda başka bir işlem yapmanız gerekmez |
| Uygun Değil | {0}Rol örneklerinden hiçbiri kullanılamadığından, bulut hizmeti dağıtımınız sağlıklı {1} değil |
| Düzeyi düşürüldü | {0}Rol örneklerinden hiçbiri kullanılamadığından bulut hizmeti dağıtımınız düşürüldü {1} | 
| Kullanılabilir ve etkilenmiş olabilir | Bulut hizmeti dağıtımınız çalışıyor, ancak devam eden bir Azure hizmeti kesintisi ile bağlantı kurmanızı engelliyor olabilir. Kesinti çözümlendikten sonra bağlantı geri yüklenecek |
| Kullanılamaz ve etkilenmiş olabilir | Bu bulut hizmeti dağıtımının sistem durumu bir Azure hizmet kesintisinden etkilenebilir. Kesinti çözümlendiğinde bulut hizmeti dağıtımınız otomatik olarak kurtarılır |
| Bilinmiyor ve etkilenmiş olabilir | Şu anda bu bulut hizmeti dağıtımının sistem durumunu belirleyemedik. Bunun nedeni, bu sanal makineyi etkileyebilecek devam eden bir Azure hizmet kesintisi olabilir ve bu durum, kesinti çözümlendiğinde otomatik olarak kurtarılır |

## <a name="cloud-services-role-instance-level-annotations--their-meanings"></a>Cloud Services (rol örneği düzeyi) ek açıklamaları anlamları &
| Ek Açıklama | Description | 
| --- | --- | 
| Kullanılabilir | Bu sanal makineyi etkileyen bilinen bir Azure platformu sorunu yok | 
| Bilinmiyor | Şu anda bu sanal makinenin durumunu belirleyemedik |
| Durdurulmuş ve ayırmayı kaldırma | Yetkili kullanıcı veya işlem tarafından yapılan istek üzerine bu sanal makine durduruluyor veya serbest bırakılıyor |
| Kaynak Durumu ayarlama | Bu kaynak için kaynak durumu ayarlanıyor. Kaynak sistem durumu, Azure kaynaklarınızı etkileyen devam eden ve geçmiş olaylar hakkında ayrıntılı bilgi sağlamak için bunları izler |
| Kullanılamaz | Sanal makineniz kullanılamıyor. Sanal makinenizi otomatik olarak kurtarmaya ve sorunun kaynağını belirlemeye çalışıyoruz. Şu anda başka bir işlem yapmanız gerekmez |
| Düzeyi düşürüldü | Sanal makinenizin düzeyi düşürüldü. Sanal makinenizi otomatik olarak kurtarmaya ve sorunun kaynağını belirlemeye çalışıyoruz. Şu anda başka bir işlem yapmanız gerekmez |
| Konak sunucusu donanım hatası | Bu sanal makine, ana bilgisayar sunucusundaki önemli bir {HardwareCategory} hatasından etkilendi. Azure, sanal makinenizi sağlıklı bir ana bilgisayar sunucusuna yeniden dağıtırsınız |
| Azaltılmış donanım nedeniyle geçiş zamanlandı | Azure, konak sunucuda yakında başarısız olacağı tahmin edilen düzeyi düşürülmüş bir {0} olduğunu tespit etti. Mümkün olursa sanal makinenizi en kısa içinde dinamik olarak geçireceğiz veya {1} UTC sonrasında yeniden dağıtacağız. Hizmetinizin riskini en aza indirmek için ve sistem tarafından başlatılan geçiş gerçekleşmeden önce donanımın başarısız olması durumunda, sanal makinenizi mümkün olan en kısa sürede otomatik olarak yeniden dağıtmanıza önerilir |
| Kullanılabilir ve etkilenmiş olabilir | Sanal makineniz çalışıyor, ancak devam eden bir Azure hizmeti kesintisi ile bağlantı kurmanızı engelliyor olabilir. Kesinti çözümlendikten sonra bağlantı geri yüklenecek |
| Kullanılamaz ve etkilenmiş olabilir | Bu sanal makinenin sistem durumu bir Azure hizmet kesintisinden etkilenebilir. Kesinti çözümlendiğinde sanal makineniz otomatik olarak kurtarılır |
| Bilinmiyor ve etkilenmiş olabilir | Şu anda bu sanal makinenin sistem durumunu belirleyemedik. Bunun nedeni, bu sanal makineyi etkileyebilecek devam eden bir Azure hizmet kesintisi olabilir ve bu durum, kesinti çözümlendiğinde otomatik olarak kurtarılır |
| Ayrılan donanım kaynakları | Donanım kaynakları atandı ve sanal makine kısa süre içinde çevrimiçi olacak |
| Durduruluyor ve ayırmayı kaldırma | Yetkili kullanıcı veya işlem tarafından yapılan istek üzerine bu sanal makine durduruluyor veya serbest bırakılıyor |
| Yapılandırma güncelleştiriliyor | Yetkili kullanıcı veya işlem tarafından yapılan istek üzerine bu sanal makinenin yapılandırması güncelleştiriliyor |
| Kullanıcı tarafından yeniden başlatıldı | Yetkili kullanıcı veya işlem tarafından yapılan istek üzerine bu sanal makine yeniden başlatılıyor. Yeniden başlatma işlemi tamamlandıktan sonra yeniden çevrimiçi olacak |
| Farklı bir konağa yeniden dağıtım | Yetkili kullanıcı veya işlem tarafından yapılan istek üzerine bu sanal makine farklı bir konak üzerinde yeniden dağıtılıyor. Yeniden dağıtım tamamlandıktan sonra yeniden çevrimiçi olacak |
| Kullanıcı tarafından durduruldu | Yetkili kullanıcı veya işlem tarafından yapılan istek üzerine bu sanal makine durduruluyor |
| Kullanıcı veya işlem tarafından durduruldu | Yetkili kullanıcı veya sanal makine içinde çalışan bir işlem tarafından yapılan istek üzerine bu sanal makine durduruluyor |
| Kullanıcı tarafından başlatıldı | Yetkili kullanıcı veya işlem tarafından yapılan istek üzerine bu sanal makine başlatılıyor. Kısa süre içinde çevrimiçi olacak |
| Farklı bir konağa bakım yeniden dağıtma | Bu sanal makine, planlı bakım etkinliği kapsamında farklı bir konak sunucu üzerinde yeniden dağıtılıyor. Yeniden dağıtım tamamlandıktan sonra yeniden çevrimiçi olacak |
| Makinenin içinden yeniden başlatma başlatıldı | Sanal makinenin içinden bir yeniden başlatma tetiklendi. Bunun nedeni sanal makine işletim sistemi hatası veya yetkili kullanıcı ya da işlem isteği olabilir. Yeniden başlatma işlemi tamamlandıktan sonra sanal makine yeniden çevrimiçi kalacak |
| Kullanıcı tarafından yeniden boyutlandırıldı | Yetkili kullanıcı veya işlem tarafından yapılan istek üzerine bu sanal makine yeniden boyutlandırılıyor. Yeniden boyutlandırma tamamlandıktan sonra yeniden çevrimiçi olacak |
| Makine kilitlendi | Sanal makinenin içinden bir yeniden başlatma tetiklendi. Bunun nedeni sanal makine işletim sistemi hatası veya yetkili kullanıcı ya da işlem isteği olabilir. Yeniden başlatma işlemi tamamlandıktan sonra sanal makine yeniden çevrimiçi kalacak |
| Konak güncelleştirmesi için bakım yeniden başlatılıyor | Bu sanal makineyi çalıştıran konak sunucuya bakım güncelleştirmeleri uygulanıyor. Şu anda başka hiçbir eylem gerçekleştirmeniz gerekmiyor. Bakım tamamlandıktan sonra yeniden çevrimiçi olacak |
| Yeni donanıma bakım yeniden dağıtma | Bu sanal makine planlı bakım etkinliği kapsamında daha yeni bir donanıma yeniden dağıtıldığından kullanılamaz durumda. Şu anda başka hiçbir eylem gerçekleştirmeniz gerekmiyor. Planlı bakım tamamlandıktan sonra yeniden çevrimiçi olacak |
| Düşük öncelikli makine ayrıldı | Bu sanal makine etkisiz hale getirildi. Bu düşük öncelikli sanal makine durduruluyor ve serbest bırakılıyor |
| Konak sunucusu yeniden başlatma | Konak sunucudaki beklenmeyen yeniden başlatma işlemi nedeniyle sanal makineniz kullanılamıyor. Konak sunucu şu anda yeniden başlatılıyor. Yeniden başlatma işlemi tamamlandıktan sonra sanal makine yeniden çevrimiçi olacak. Şu anda başka bir işlem yapmanız gerekmez |
| Ana bilgisayar arızası nedeniyle yeniden dağıtılıyor | Konak sunucuda oluşan beklenmeyen bir hata nedeniyle sanal makineniz kullanılamıyor ve yeniden dağıtılacak. Azure, otomatik kurtarma işlemini başlattı ve şu anda sanal makineyi farklı bir konakta başlatıyor. Şu anda başka bir işlem yapmanız gerekmez |
| Beklenmeyen ana bilgisayar hatası | Konak sunucudaki beklenmeyen bir hata nedeniyle sanal makineniz kullanılamıyor. Azure, otomatik kurtarma işlemini başlattı ve şu anda konak sunucuyu yeniden başlatıyor. Şu anda başka hiçbir eylem gerçekleştirmeniz gerekmiyor. Yeniden başlatma işlemi tamamlandıktan sonra sanal makine yeniden çevrimiçi kalacak |
| Planlanmamış konak bakımı nedeniyle yeniden dağıtılıyor | Konak sunucuda oluşan beklenmeyen bir hata nedeniyle sanal makineniz kullanılamıyor ve yeniden dağıtılacak. Azure, otomatik kurtarma işlemini başlattı ve şu anda sanal makineyi farklı bir konak sunucuda başlatıyor. Şu anda başka bir işlem yapmanız gerekmez |
| Sağlama hatası | Beklenmeyen sağlama sorunları nedeniyle sanal makineniz kullanılamıyor. Sanal makinenizin sağlanması beklenmeyen bir hata nedeniyle başarısız oldu |
| Dinamik Geçiş | Bu sanal makine, bellek tasarruflu Dinamik Geçiş işlemi nedeniyle duraklatıldı. Sanal makine genellikle 10 saniye içinde sürdürülür. Şu anda başka bir işlem yapmanız gerekmez |
| Dinamik Geçiş | Bu sanal makine, bellek tasarruflu Dinamik Geçiş işlemi nedeniyle duraklatıldı. Sanal makine genellikle 10 saniye içinde sürdürülür. Şu anda başka bir işlem yapmanız gerekmez | 
| Uzak disk bağlantısı kesildi | Uzak disk bağlantısı kesildiği için sanal makineniz kullanılamıyor. Disk bağlantısını yeniden kurmaya çalışıyoruz. Şu anda başka bir işlem yapmanız gerekmez |
| Azure hizmet sorunu | Sanal makineniz Azure hizmet sorunundan etkilendi |
| Ağ sorunu | Bu sanal makine, raf üstü ağ cihazından etkilendi |
| Kullanılamaz | Sanal makineniz kullanılamıyor. Şu anda bu kapalı kalma süresinin nedenini belirleyemedik |
| Konak sunucusu yeniden başlatma | Konak sunucudaki beklenmeyen yeniden başlatma işlemi nedeniyle sanal makineniz kullanılamıyor. Ana bilgisayar sunucusuyla ilgili beklenmedik bir sorun, sanal makinenizi otomatik olarak kurtarmamızı engellemektedir |
| Ana bilgisayar arızası nedeniyle yeniden dağıtılıyor | Konak sunucudaki beklenmeyen bir hata nedeniyle sanal makineniz kullanılamıyor. Ana bilgisayarla ilgili beklenmedik bir sorun, sanal makinenizi otomatik olarak kurtarmamızı engellemektedir |
| Beklenmeyen ana bilgisayar hatası | Konak sunucudaki beklenmeyen bir hata nedeniyle sanal makineniz kullanılamıyor. Ana bilgisayarla ilgili beklenmedik bir sorun, sanal makinenizi otomatik olarak kurtarmamızı engellemektedir |
| Planlanmamış konak bakımı nedeniyle yeniden dağıtılıyor | Konak sunucudaki beklenmeyen bir hata nedeniyle sanal makineniz kullanılamıyor. Ana bilgisayarla ilgili beklenmedik bir sorun, sanal makinenizi otomatik olarak kurtarmamızı engellemektedir |
| Sağlama hatası | Beklenmeyen sağlama sorunları nedeniyle sanal makineniz kullanılamıyor. Sanal makinenizin sağlanması beklenmeyen bir hata nedeniyle başarısız oldu |
| Uzak disk bağlantısı kesildi | Uzak disk bağlantısı kesildiği için sanal makineniz kullanılamıyor. Beklenmeyen bir sorun sanal makinenizi otomatik olarak kurtarmamızı engellemektedir |
| Konuk işletim sistemi güncelleştirmesi nedeniyle yeniden başlatma | Azure platformu tarafından yeni bir konuk işletim sistemi güncelleştirmesi uygulamak için yeniden başlatma başlatıldı. Yeniden başlatma işlemi tamamlandıktan sonra sanal makine yeniden çevrimiçi kalacak |