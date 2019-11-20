---
title: Azure Lab Services sınıf laboratuvarları — SSS | Microsoft Docs
description: Azure Lab Services ' deki sınıf laboratuvarları hakkında sık sorulan sorulara yanıtlar bulun.
services: lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 91c5c1480669829bad7f8d89ce360bb806d6c997
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646862"
---
# <a name="classroom-labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Azure Lab Services sınıf laboratuvarları — sık sorulan sorular (SSS)
Azure Lab Services ders laboratuvarları hakkında en sık sorulan soruların yanıtlarını alın. 

## <a name="quotas"></a>Kotalar

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>Kullanıcı başına veya haftaya göre mi yoksa laboratuvarın tamamına mı göre kota mi? 
Laboratuvar için ayarladığınız kota, tüm laboratuvarın süresi boyunca her öğrenciye yöneliktir. Ve [VM 'lerin zamanlanan çalışma süresi](how-to-create-schedules.md) , bir kullanıcıya ayrılan kotaya göre sayılmaz. Kota, bir öğrencinin VM 'lerde harcadığı zamanlama saatleri dışında bir süredir.  Kotalar hakkında daha fazla bilgi için bkz. [kullanıcılar için kotaları ayarlama](how-to-configure-student-usage.md#set-quotas-for-users).

## <a name="schedules"></a>Zamanlamalar

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Bir zamanlama ayarlandığında laboratudaki tüm VM 'Ler otomatik olarak başlatılır mi? 
Hayır. Tüm VM 'Ler değil. Yalnızca kullanıcılara bir zamanlamaya göre atanan VM 'Ler. Bir kullanıcıya atanmamış VM 'Ler otomatik olarak başlatılmaz. Tasarıma göre. 

## <a name="lab-accounts"></a>Laboratuvar hesapları

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Adres aralığının kullanım dışı kalması nedeniyle neden laboratuvar oluşturmdayım? 
Sınıf laboratuvarları, Azure portal laboratuvar hesabınızı oluştururken belirlediğiniz bir IP adresi aralığı içinde laboratuvar VM 'Leri oluşturabilir. Bir adres aralığı sağlandığında, bu laboratuvar VM 'Ler için 512 IP adresi ayrılır. Laboratuvar hesabı için adres aralığı, laboratuvar hesabı altında oluşturmak istediğiniz tüm laboratuvarlara uyum sağlayacak kadar büyük olmalıdır. 

Örneğin,/19-10.0.0.0/19 bloğuna sahipseniz, bu adres aralığı 8192 IP adresine ve 16 laboratuvara (8192/512 = 16 Labs) uyum sağlar. Bu durumda, Laboratuvar oluşturma, 17. Laboratuvar oluşturma sırasında başarısız olur.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Laboratuvar sanal makinelerine RDP/SSH aracılığıyla bağlanmak için Kuruluşumun güvenlik duvarı ayarında hangi bağlantı noktası aralıklarının açılmalıyım?

Bağlantı noktaları: 49152 – 65535. Sınıf laboratuvarları bir yük dengeleyicinin arkasına oturdum, böylece bir laboratuvardaki tüm sanal makinelerin tek IP adresi vardır ve laboratuvardaki her sanal makinenin benzersiz bir bağlantı noktası vardır. Laboratuvar her yeniden yayımlandığında bağlantı noktası numaraları ve genel IP adresi değişebilir.

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Laboratuvar sanal makinelerine RDP/SSH aracılığıyla bağlanmak için Kuruluşumun güvenlik duvarı ayarlarında ne genel IP adresi aralığı açılmalıyım?
Azure 'da veri merkezleri için genel IP adresi aralığı sağlayan [genel bulut olan Azure IP aralıkları ve hizmet etiketleri](https://www.microsoft.com/download/details.aspx?id=56519)' ne bakın. Laboratuvar hesaplarınızın bulunduğu bölgelerin IP adreslerini açabilirsiniz.

## <a name="blog-post"></a>Blog gönderisi
[Azure Lab Services bloguna](https://azure.microsoft.com/blog/tag/azure-lab-services/)abone olun.

## <a name="update-notifications"></a>Güncelleştirme bildirimleri
Laboratuvar Hizmetleri 'ndeki yeni özellikler hakkında bilgi sahibi olmak için [Laboratuvar Hizmetleri güncelleştirmelerine](https://azure.microsoft.com/updates/?product=lab-services) abone olun.

## <a name="general"></a>Genel
### <a name="what-if-my-question-isnt-answered-here"></a>Sorum burada yanıtlanmazsa ne olacak?
Sorunuz burada listelenmiyorsa bize bilgi verin. bu nedenle bir yanıt bulmanıza yardımcı olabiliriz.

- Bu SSS sonunda bir soru gönderin. 
- Daha geniş bir hedef kitleye ulaşmak için [Azure Lab Services Stack Overflow forumuna](https://stackoverflow.com/questions/tagged/azure-lab-services)bir soru gönderin. 
- Özellik istekleri için isteklerinizi ve fikirlerinizi [Azure Lab Services — Kullanıcı seslerine](https://feedback.azure.com/forums/320373-lab-services?category_id=352774)gönderebilirsiniz.

