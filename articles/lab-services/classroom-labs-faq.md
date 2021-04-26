---
title: Azure Lab Services 'de Labs-SSS | Microsoft Docs
description: Bu makale, Azure Lab Services Labs hakkında sık sorulan soruların (SSS) yanıtlarını sağlar.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 757af8f30e9a71a3889d9f625c87a002af2e1302
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96437193"
---
# <a name="labs-in-azure-lab-services--frequently-asked-questions-faq"></a>Azure Lab Services Labs — sıkça sorulan sorular (SSS)
Azure Lab Services laboratuarlar hakkında en yaygın soruların yanıtlarını alın. 

## <a name="quotas"></a>Kotalar

### <a name="is-the-quota-per-user-or-per-week-or-per-entire-duration-of-the-lab"></a>Kullanıcı başına veya haftaya göre mi yoksa laboratuvarın tamamına mı göre kota mi? 
Laboratuvar için ayarladığınız kota, tüm laboratuvarın süresi boyunca her öğrenciye yöneliktir. Ve [VM 'lerin zamanlanan çalışma süresi](how-to-create-schedules.md) , bir kullanıcıya ayrılan kotaya göre sayılmaz. Kota, bir öğrencinin VM 'lerde harcadığı zamanlama saatleri dışında bir süredir.  Kotalar hakkında daha fazla bilgi için bkz. [kullanıcılar için kotaları ayarlama](how-to-configure-student-usage.md#set-quotas-for-users).

### <a name="if-educator-turns-on-a-student-vm-does-that-affect-the-student-quota"></a>Eğitimci bir öğrenci sanal makinesini kullanıyorsa, öğrenci kotasını da etkiler mi? 
Hayır. Hiç rolü yoktur. Eğitimci, öğrenci sanal makinesini açtığında öğrenciye ayrılan kotayı etkilemez. 

## <a name="schedules"></a>Zamanlamalar

### <a name="do-all-vms-in-the-lab-start-automatically-when-a-schedule-is-set"></a>Bir zamanlama ayarlandığında laboratudaki tüm VM 'Ler otomatik olarak başlatılır mi? 
Hayır. Tüm VM 'Ler değil. Yalnızca kullanıcılara bir zamanlamaya göre atanan VM 'Ler. Bir kullanıcıya atanmamış VM 'Ler otomatik olarak başlatılmaz. Tasarıma göre. 

## <a name="lab-accounts"></a>Laboratuvar hesapları

### <a name="why-am-i-not-able-to-create-a-lab-because-of-unavailability-of-the-address-range"></a>Adres aralığının kullanım dışı kalması nedeniyle neden laboratuvar oluşturmdayım? 

Labs, Azure portal laboratuvar hesabınızı oluştururken belirlediğiniz bir IP adresi aralığı içinde laboratuvar VM 'Leri oluşturabilir. Bir adres aralığı sağlandığında, bu laboratuvar VM 'Ler için 512 IP adresi ayrılır. Laboratuvar hesabı için adres aralığı, laboratuvar hesabı altında oluşturmak istediğiniz tüm laboratuvarlara uyum sağlayacak kadar büyük olmalıdır. 

Örneğin,/19-10.0.0.0/19 bloğuna sahipseniz, bu adres aralığı 8192 IP adresine ve 16 laboratuvara (8192/512 = 16 Labs) uyum sağlar. Bu durumda, Laboratuvar oluşturma, 17. Laboratuvar oluşturma sırasında başarısız olur.

### <a name="what-port-ranges-should-i-open-on-my-organizations-firewall-setting-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Laboratuvar sanal makinelerine RDP/SSH aracılığıyla bağlanmak için Kuruluşumun güvenlik duvarı ayarında hangi bağlantı noktası aralıklarının açılmalıyım?

Bağlantı noktaları: 49152 – 65535. Labs bir yük dengeleyicinin arkasına yaslar. Her laboratuvarın tek bir genel IP adresi vardır ve laboratuvardaki her sanal makinenin benzersiz bir bağlantı noktası vardır. 

Ayrıca, Azure portal laboratuvar için ana sayfanın **sanal makine havuzu** sekmesinde her bir sanal makınenın özel IP adresini görebilirsiniz. Bir laboratuvarı yeniden yayımlarsanız, laboratuvarın genel IP adresi değişmez, ancak laboratuvardaki her bir sanal makinenin özel IP ve bağlantı noktası numarası değişebilir. Makalesinde daha fazla bilgi edinebilirsiniz: [Azure Lab Services Için güvenlik duvarı ayarları](how-to-configure-firewall-settings.md).

### <a name="what-public-ip-address-range-should-i-open-on-my-organizations-firewall-settings-to-connect-to-lab-virtual-machines-via-rdpssh"></a>Laboratuvar sanal makinelerine RDP/SSH aracılığıyla bağlanmak için Kuruluşumun güvenlik duvarı ayarlarında ne genel IP adresi aralığı açılmalıyım?
Azure 'da veri merkezleri için genel IP adresi aralığı sağlayan [genel bulut olan Azure IP aralıkları ve hizmet etiketleri](https://www.microsoft.com/download/details.aspx?id=56519)' ne bakın. Laboratuvar hesaplarınızın bulunduğu bölgelerin IP adreslerini açabilirsiniz.

## <a name="virtual-machine-images"></a>Sanal makine görüntüleri

### <a name="as-a-lab-creator-why-cant-i-enable-additional-image-options-in-the-virtual-machine-images-dropdown-when-creating-a-new-lab"></a>Laboratuvar Oluşturucusu olarak, yeni bir laboratuvar oluştururken sanal makine görüntüleri açılan menüsünde neden ek görüntü seçeneklerini etkinleştiremiyorum?

Bir yönetici sizi laboratuvar hesabına laboratuvar Oluşturucu olarak eklediğinde, laboratuvarları oluşturma izinleri verilir. Ancak, etkinleştirilmiş sanal makine görüntülerinin listesi de dahil olmak üzere, laboratuvar hesabındaki herhangi bir ayarı düzenleme izniniz yok. Ek görüntüleri etkinleştirmek için laboratuvar hesabı yöneticinizle iletişime geçerek bunu sizin için yapın veya yöneticiden laboratuvar hesabına katkıda bulunan bir rol olarak eklemesini isteyin. Katkıda bulunan rolü, laboratuvar hesabındaki sanal makine görüntüsü listesini düzenleme izinleri verecektir.

### <a name="can-i-attach-additional-disks-to-a-virtual-machine"></a>Bir sanal makineye ek diskler ekleyebilir miyim?
Hayır. sınıf laboratuvarında bir VM 'ye ek diskler eklemek mümkün değildir. 

## <a name="users"></a>Kullanıcılar

### <a name="how-many-users-can-be-in-a-classroom-lab"></a>Bir derslik laboratuvarında kaç Kullanıcı olabilir?
Bir derslik laboratuvarına en fazla 400 kullanıcı ekleyebilirsiniz. 

## <a name="blog-post"></a>Blog gönderisi
[Azure Lab Services bloguna](https://aka.ms/azlabs-blog)abone olun.

## <a name="update-notifications"></a>Güncelleştirme bildirimleri
Laboratuvar Hizmetleri 'ndeki yeni özellikler hakkında bilgi sahibi olmak için [Laboratuvar Hizmetleri güncelleştirmelerine](https://azure.microsoft.com/updates/?product=lab-services) abone olun.

## <a name="general"></a>Genel
### <a name="what-if-my-question-isnt-answered-here"></a>Sorum burada yanıtlanmazsa ne olacak?
Sorunuz burada listelenmiyorsa bize bilgi verin. bu nedenle bir yanıt bulmanıza yardımcı olabiliriz.

- Bu SSS sonunda bir soru gönderin. 
- Daha geniş bir hedef kitleye ulaşmak için Azure Lab Services bir soru gönderin [(Tech Community Forumu)](https://techcommunity.microsoft.com/t5/azure-lab-services/bd-p/AzureLabServices). 
- Özellik istekleri için isteklerinizi ve fikirlerinizi [Azure Lab Services — Kullanıcı seslerine](https://feedback.azure.com/forums/320373-lab-services?category_id=352774)gönderebilirsiniz.

