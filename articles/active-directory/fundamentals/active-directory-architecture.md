---
title: Mimariye genel bakış-Azure Active Directory | Microsoft Docs
description: Azure Active Directory kiracının ne olduğunu ve Azure Active Directory kullanarak Azure 'un nasıl yönetileceğini öğrenin.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 476cf8013f5dc8b5d54efb573cf305d81fc690b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996731"
---
# <a name="what-is-the-azure-active-directory-architecture"></a>Azure Active Directory mimarisi nedir?

Azure Active Directory (Azure AD), kullanıcılarınız için Azure hizmet ve kaynaklarına erişimi güvenli bir şekilde yönetmenizi sağlar. Azure AD ile birlikte eksiksiz kimlik yönetimi olanakları sunulur. Azure AD özellikleri hakkında daha fazla bilgi için bkz. [Azure Active Directory nedir?](active-directory-whatis.md)

Azure AD ile kullanıcı ve gruplar oluşturup bunları yönetebilir, ayrıca kurumsal kaynaklara erişim izni vermek ya da erişimi reddetmek için izinleri etkinleştirebilirsiniz. Kimlik yönetimi hakkında bilgi için bkz. [Azure kimlik yönetimi ile ilgili temel bilgiler](active-directory-whatis.md).

## <a name="azure-ad-architecture"></a>Azure AD mimarisi

Azure AD 'nin coğrafi olarak dağıtılmış mimarisi, müşterilere şirket genelinde kullanılabilirlik ve performans sağlayan kapsamlı izleme, otomatik yeniden yönlendirme, yük devretme ve kurtarma yeteneklerini birleştirir.

Bu makalede aşağıdaki mimari öğeler ele alınmaktadır:

*   Hizmet mimarisi tasarımı
*   Ölçeklenebilirlik
*   Sürekli kullanılabilirlik
*   Veri merkezleri

### <a name="service-architecture-design"></a>Hizmet mimarisi tasarımı

Erişilebilir ve kullanılabilir, veri açısından zengin bir sistem oluşturmanın en yaygın yolu, bağımsız yapı taşları veya ölçek birimleri kullanmaktır. Azure AD veri katmanı için ölçek birimlerine *bölüm* adı verilir.

Veri katmanında, okuma-yazma özelliği sağlayan çok sayıda ön uç hizmeti bulunur. Aşağıdaki diyagramda tek dizin bölümünün bileşenlerinin coğrafi olarak dağıtılan veri merkezlerinde nasıl teslim edileceği gösterilmektedir.

  ![Tek dizin bölüm diyagramı](./media/active-directory-architecture/active-directory-architecture.png)

Azure AD mimarisinin bileşenleri, birincil çoğaltma ve ikincil çoğaltma öğelerini içerir.

#### <a name="primary-replica"></a>Birincil çoğaltma

*Birincil çoğaltma*, ait olduğu bölüm için *yazma* işlemlerini alır. Her yazma işlemi, çağırana başarılı sonucu döndürmeden önce hemen farklı bir veri merkezindeki ikincil çoğaltmaya çoğaltılır, böylece yazma işlemlerinin coğrafi olarak yedekli dayanıklılığı sağlanır.

#### <a name="secondary-replicas"></a>İkincil çoğaltmalar

Tüm Dizin *okumaları* , farklı coğrafi bölgelerde fiziksel olarak bulunan veri merkezlerinde olan *İkincil çoğaltmalara* göre hizmet verilir. Veriler zaman uyumsuz olarak kopyalandığı için çok sayıda ikincil çoğaltma vardır. Kimlik doğrulama istekleri gibi dizin okuma, müşterilere yakın olan veri merkezlerinden alınır. İkincil çoğaltmalar, okuma ölçeklenebilirliğinden sorumludur.

### <a name="scalability"></a>Ölçeklenebilirlik

Ölçeklenebilirlik, bir hizmetin artan performans taleplerine göre genişleyebilme becerisidir. Yazma ölçeklenebilirliği, veri bölümlendirilerek sağlanır. Okuma ölçeklenebilirliği, bir bölümden dünyanın dört bir yanına dağıtılmış birden fazla ikincil çoğaltmaya verilerin çoğaltılması yoluyla sağlanır.

Dizin uygulamalarından gelen istekler, fiziksel olarak en yakın olan veri merkezine yönlendirilir. Yazma işlemleri, okuma-yazma tutarlılığı sağlamak üzere şeffaf bir şekilde birincil çoğaltmaya yönlendirilir. Dizinler çoğu zaman okuma işlemlerini sunduğundan, ikincil çoğaltmalar bölümlerin ölçeğini önemli ölçüde genişletir.

Dizin uygulamaları en yakın veri merkezlerine bağlanır. Bu bağlantı, performansı artırır ve bu nedenle ölçeği genişletme mümkündür. Bir dizin bölümünde çok sayıda ikincil çoğaltma olabileceğinden, ikincil çoğaltmalar dizin istemcilerine daha yakın yerleştirilebilir. Yalnızca yazma yoğunluklu olan iç dizin hizmeti bileşenleri, etkin birincil çoğaltmayı doğrudan hedefler.

### <a name="continuous-availability"></a>Sürekli kullanılabilirlik

Kullanılabilirlik (veya çalışma süresi) bir sistemin kesintisiz çalışma yeteneğini tanımlar. Azure AD 'nin yüksek kullanılabilirliğe yönelik anahtar, hizmetlerin trafiği coğrafi olarak dağıtılan birden çok veri merkezinde hızlı bir şekilde kaydırabileceği bir anahtardır. Her veri merkezi bağımsız olduğundan, ilişkili hata modlarını mümkün bir şekilde sunar. Bu yüksek kullanılabilirliğe sahip tasarımda, Azure AD bakım etkinlikleri için kapalı kalma süresi gerektirmez.

Azure AD 'nin bölüm tasarımı, tam olarak düzenlenmiş ve belirleyici bir birincil çoğaltma yük devretme işlemi içeren tek yöneticili bir tasarım kullanılarak kurumsal AD tasarımına kıyasla basitleştirilmiştir.

#### <a name="fault-tolerance"></a>Hataya dayanıklılık

Bir sistem donanım, ağ ve yazılım hatalarına dayanıklı ise kullanılabilirliği daha yüksektir. Dizin üzerindeki her bölüm için yüksek oranda kullanılabilir bir ana çoğaltma mevcuttur: birincil çoğaltma. Bu çoğaltma üzerinde yalnızca bölüme yazma işlemleri gerçekleştirilir. Bu çoğaltma sürekli olarak ve yakından izlenirken, bir hata algılanması durumunda yazma işlemleri hemen başka bir çoğaltmaya kaydırılabilir (bu çoğaltma yeni birincil çoğaltma olur). Yük devretme sırasında genellikle 1-2 dakikalık yazma kullanılabilirliği kaybı olabilir. Bu süre boyunca okuma kullanılabilirliği etkilenmez.

Okuma işlemleri (yazma işlemlerinden onlarca kat fazladır) yalnızca ikincil çoğaltmalara gider. İkincil çoğaltmalar bir kez etkili olduğundan, okumalar genellikle aynı veri merkezinde bulunan başka bir çoğaltmaya yönlendirilerek belirli bir bölümdeki herhangi bir çoğaltmanın kaybı kolayca telafi edilebilir.

#### <a name="data-durability"></a>Veri dayanıklılığı

Bir yazma işlemi, onaylanmadan önce en az iki veri merkezine işlenir. Bu, öncelikle birincili yazmayı kaydederek ve daha sonra yazmayı en az bir diğer veri merkezine çoğaltarak oluşur. Bu yazma eylemi, birincili barındıran veri merkezinin potansiyel bir şekilde kaybının veri kaybına neden olmamasını sağlar.

Azure AD, yük devretme sırasında verilerin kaybedilmediği sıfır [Kurtarma süresi hedefini (RTO)](https://en.wikipedia.org/wiki/Recovery_time_objective) korur. Şunları içerir:

* Belirteç verme ve dizin okuma
* Dizin yazmaları için yalnızca 5 dakikalık bir RTO 'A izin verme

### <a name="datacenters"></a>Veri merkezleri

Azure AD çoğaltmaları, dünyanın dört bir yanında bulunan veri merkezlerinde depolanır. Daha fazla bilgi için bkz. [Azure genel altyapısı](https://azure.microsoft.com/global-infrastructure/).

Azure AD, aşağıdaki özelliklerle veri merkezleri arasında çalışır:

* Kimlik doğrulama, grafik ve diğer AD Hizmetleri Ağ Geçidi hizmetinin arkasında bulunur. Gateway bu hizmetlerin yük dengelemesini yönetir. İşlem durumu araştırmaları kullanılarak sağlıksız bir sunucu algılanırsa otomatik olarak yük devreder. Ağ Geçidi, bu sistem durumu araştırmalarını temel alarak trafiği sağlıklı veri merkezlerine dinamik olarak yönlendirir.
* *Okumalar* için, dizinde ikincil çoğaltmalar ve karşılık gelen ön uç Hizmetleri, birden çok veri merkezinde çalışan etkin-etkin bir yapılandırmada bulunur. Tüm veri merkezinde hata olması durumunda, trafik otomatik olarak farklı bir veri merkezine yönlendirilir.
 * *Yazma işlemleri* için dizin, planlı (yeni birincil, eski birincil ile eşitlenir) veya acil durum yük devretme yordamlarına göre veri merkezleri genelinde birincil (ana) çoğaltmayı devreder. Veri dayanıklılığı, en az iki veri merkezine yapılan herhangi bir yürütmeyi çoğaltarak elde edilir.

#### <a name="data-consistency"></a>Veri tutarlılığı

Dizin modeli, nihai bir şekilde kullanılır. Dağıtılmış zaman uyumsuz olarak çoğaltılan sistemlerin tipik bir sorunu, "belirli" bir çoğaltmadan döndürülen verilerin güncel olmaması olabilir. 

Azure AD, yazma işlemlerini birincil çoğaltmaya yönlendirerek ve eşzamanlı olarak ikincil çoğaltmaya geri çekerek, ikincil çoğaltmayı hedefleyen uygulamalar için okuma-yazma tutarlılığı sağlar.

Azure AD 'nin Microsoft Graph API 'sini kullanarak uygulama yazma işlemleri, okuma-yazma tutarlılığı için bir dizin çoğaltmasıyla Benzeşimin korunmasından soyutlanır. Microsoft Graph API hizmeti, okuma için kullanılan bir ikincil çoğaltma benzeşimine sahip bir mantıksal oturum sağlar; benzeşim, hizmetin ikincil çoğaltma veri merkezinde dağıtılmış bir önbellek kullanarak önbelleğe aldığı bir "çoğaltma belirteci" içinde yakalanır. Bu belirteç aynı mantıksal oturumun daha sonraki işlemleri için kullanılır. Aynı mantıksal oturumu kullanmaya devam etmek için sonraki isteklerin aynı Azure AD veri merkezine yönlendirilmesi gerekir. Dizin istemci istekleri birden çok Azure AD veri merkezine yönlendiriliyorsa, mantıksal bir oturuma devam etmek mümkün değildir; Bu durumda, istemci, bağımsız okuma-yazma işlemi gibi birden çok mantıksal oturum içerir.

 >[!NOTE]
 >Yazma işlemleri, mantıksal oturumdaki okumaların verildiği ikincil çoğaltmaya hemen çoğaltılır.

#### <a name="backup-protection"></a>Yedekleme koruması

Dizin, bir müşterinin yanlışlıkla silmesi durumunda kolay kurtarma amacıyla kullanıcılar ve kiracılar için sabit silme yerine geçici silme gerçekleştirir. Kiracı yöneticiniz kullanıcıları yanlışlıkla silerse silinen kullanıcıları kolayca geri alabilir ve geri yükleyebilir.

Azure AD tüm verilerin günlük yedeklemesini yapar ve bu nedenle mantıksal silme veya bozulma durumunda verileri yetkili olarak geri yükleyebilir. Veri katmanı hata düzeltme kodları kullanır, böylece hataları denetleyebilir ve belirli disk hatalarının türlerini otomatik olarak düzeltebilir.

#### <a name="metrics-and-monitors"></a>Ölçümler ve izleyiciler

Yüksek oranda kullanılabilir bir hizmetin çalıştırılması için birinci sınıf ölçüm ve izleme özellikleri gerekir. Azure AD, temel hizmet durumu ölçümlerini ve her bir hizmetinin başarı ölçütlerini sürekli olarak analiz edip raporlar. Her bir Azure AD hizmeti ve tüm hizmetler içinde her bir senaryoya yönelik ölçüm ve izleme ve uyarı ayarlarını sürekli olarak geliştirmeye ve ayarlamaya de devam edebilirsiniz.

Herhangi bir Azure AD hizmeti beklendiği gibi çalışmıyorsa, işlevselliği olabildiğince çabuk geri yüklemek için hemen işlem yapılır. Azure AD 'nin en önemli ölçümü, canlı site sorunlarının müşteriler için ne kadar hızlı bir şekilde algılanabilmesi ve hafifletildiğini izler. Algılama süresini azaltmak (TTD Hedefi: <5 dakika) için izleme ve uyarılara, önlem alma süresini azaltmak için (TTM Hedefi: <30 dakika) operasyonel hazırlığa önemli ölçüde yatırım yapıyoruz.

#### <a name="secure-operations"></a>Güvenli işlemler

Her türlü işlem için çok faktörlü kimlik doğrulaması (MFA) gibi işlemsel denetimleri kullanma ve tüm işlemleri denetleme. Ayrıca, isteğe bağlı olarak herhangi bir işlem görevi için gerekli geçici erişimi sağlamak üzere tam zamanında yükseltme sistemi kullanma. Daha fazla bilgi için bkz. [Güvenilir Bulut](https://azure.microsoft.com/support/trust-center).

## <a name="next-steps"></a>Sonraki adımlar

[Azure Active Directory geliştirici kılavuzu](../develop/index.yml)