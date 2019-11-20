---
title: Azure AD Domain Services 'de askıya alınan etki alanları | Microsoft Docs
description: Azure AD DS yönetilen bir etki alanına yönelik farklı sistem durumları ve askıya alınmış bir etki alanını geri yükleme hakkında bilgi edinin.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: iainfou
ms.openlocfilehash: 31a1c7cd72d57b9c680452d5e84f8fe78f47cebb
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71693310"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services içindeki sistem durumlarını anlayın ve askıya alınmış etki alanlarını çözün

Azure Active Directory Domain Services (Azure AD DS), uzun bir süre boyunca yönetilen bir etki alanına hizmet verilemediğinde, yönetilen etki alanını askıya alınma durumuna geçirir. Yönetilen bir etki alanı daha sonra askıya alınma durumunda kalırsa, otomatik olarak silinir. Azure AD DS yönetilen etki alanınızı sağlıklı tutmak ve askıya alma önlemek için tüm uyarıları olabildiğince çabuk çözün.

Bu makalede, yönetilen etki alanlarının neden askıya alındığı ve askıya alınmış bir etki alanının nasıl kurtarılacağı açıklanmaktadır.

## <a name="overview-of-managed-domain-states"></a>Yönetilen etki alanı durumlarına genel bakış

Azure AD DS yönetilen bir etki alanının yaşam döngüsü boyunca, sistem durumunu gösteren farklı durumlar vardır. Yönetilen etki alanı bir sorunu bildirirse, durumu düşürmeye devam etmek için temel nedeni hızlıca çözün.

![Azure AD DS yönetilen bir etki alanının askıya alınma durumuna geçen durumların ilerleme durumu](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Azure AD DS yönetilen bir etki alanı aşağıdaki durumlardan birinde olabilir:

* [Çalıştıran](#running-state)
* [İlgilenilmesi gerekiyor](#needs-attention-state)
* [Alın](#suspended-state)
* [Miyor](#deleted-state)

## <a name="running-state"></a>Çalışma durumu

Doğru şekilde yapılandırılmış ve sorunsuz şekilde çalışan bir Azure AD DS yönetilen etki alanı *çalışır* durumda. Bu, yönetilen bir etki alanı için istenen durumdur.

### <a name="what-to-expect"></a>Sizi neler bekliyor

* Azure platformu, yönetilen etki alanının durumunu düzenli olarak izleyebilir.
* Yönetilen etki alanı için etki alanı denetleyicileri düzeltme eki uygulanmış ve düzenli olarak güncelleştirilir.
* Azure Active Directory yapılan değişiklikler, yönetilen etki alanı ile düzenli olarak eşitlenir.
* Yönetilen etki alanı için düzenli yedeklemeler alınır.

## <a name="needs-attention-state"></a>Dikkat edilmesi gereken durum

Düzeltilmesi gereken bir veya daha fazla soruna sahip Azure AD DS yönetilen etki alanı, *dikkat edilmesi gereken* bir durumdur. Yönetilen etki alanı için sistem durumu sayfası, uyarıları listeler ve nerede bir sorun olduğunu gösterir. Bazı uyarılar geçicidir ve Azure platformu tarafından otomatik olarak çözümlenir. Diğer uyarılar için, sunulan çözüm adımlarını izleyerek sorunu çözebilirsiniz. Kritik bir uyarı var, ek sorun giderme yardımı için [bir Azure destek isteği açın][azure-support] .

Bir uyarı örneği, kısıtlayıcı ağ güvenlik grubu olduğunda oluşur. Bu yapılandırmada Azure platformu, yönetilen etki alanını güncelleştiremeyebilir ve izleyemeyebilir. Bir uyarı oluşturulur ve durum, *dikkat edilmesi gereken*şekilde değişir.

Daha fazla bilgi için bkz. [Azure AD DS yönetilen etki alanı için uyarılarla ilgili sorun giderme][resolve-alerts].

### <a name="what-to-expect"></a>Sizi neler bekliyor

Azure AD DS yönetilen bir etki alanı, *dikkat edilmesi* durumunda olduğunda, Azure platformu verileri düzenli olarak izleyebilmeyebilir, güncelleştiremez veya yedeklemeyebilir. Bazı durumlarda, örneğin, geçersiz bir ağ yapılandırmasıyla, yönetilen etki alanı için etki alanı denetleyicilerine ulaşılamıyor olabilir.

* Yönetilen etki alanı sağlıksız bir durumda ve devam eden sistem durumu izleme, uyarı çözümlenene kadar durabilir.
* Yönetilen etki alanı için etki alanı denetleyicileri düzeltme eki uygulanabilir veya güncelleştirilemiyor.
* Azure Active Directory yapılan değişiklikler yönetilen etki alanıyla eşitlenmeyebilir.
* Yönetilen etki alanı için yedeklemeler alınmayabilir.
* Yönetilen etki alanını etkileyen kritik olmayan uyarıları çözümlediğinizde, sistem durumu *çalışır* duruma döndürmelidir.
* Kritik uyarılar, Azure platformunun etki alanı denetleyicilerine ulaşamabileceği yapılandırma sorunları için tetiklenir. Bu kritik uyarılar 15 gün içinde çözümlenmiyorsa, yönetilen etki alanı *askıya alınma* durumuna girer.

## <a name="suspended-state"></a>Askıya alınmış durum

Azure AD DS yönetilen bir etki alanı, aşağıdaki nedenlerden biri için **askıya alınmış** duruma girer:

* 15 gün içinde bir veya daha fazla kritik uyarı çözülmedi.
    * Kritik uyarılara Azure AD DS için gereken kaynaklara erişimi engelleyen bir yanlış yapılandırma neden olabilir. Örneğin, uyarı [AADDS104: ağ hatası][alert-nsg] , yönetilen etki alanında 15 günden uzun süredir çözülmedi.
* Azure aboneliğiyle ilgili bir faturalandırma sorunu var veya Azure aboneliğinin süresi doldu.

Yönetilen etki alanları, Azure platformu etki alanını yönetmediği, izleyemediğinde veya yedeklemediğinde askıya alınır. Yönetilen bir etki alanı, 15 gün boyunca *askıya alınmış* durumda kalır. Yönetilen etki alanına erişimi sürdürmek için, kritik uyarıları hemen çözümleyin.

### <a name="what-to-expect"></a>Sizi neler bekliyor

Azure AD DS yönetilen bir etki alanı *askıya alınmış* durumda olduğunda aşağıdaki davranışla karşılaşmış olur:

* Yönetilen etki alanı için etki alanı denetleyicileri, sanal ağ içinde sağlanmış ve ulaşılamaz durumda değildir.
* Internet üzerinden yönetilen etki alanına erişim Güvenli LDAP, etkinleştirilirse, çalışmayı durduruyor.
* Yönetilen etki alanında kimlik doğrulaması, etki alanına katılmış VM 'Lerde oturum açma veya LDAP/LDAPS üzerinden bağlantı kurma sırasında oluşan sorunlar vardır.
* Yönetilen etki alanı için yedeklemeler artık alınmaz.
* Azure AD ile eşitleme durduruluyor.

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>Yönetilen etki alanınız askıya alınmış olup olmadığını nasıl anlarsınız?

Azure AD DS sistem durumu sayfasında, etki alanının askıya alındığını Not eden Azure portal bir [Uyarı][resolve-alerts] görürsünüz. Etki alanının durumu da *askıya alındı*olarak gösterilir.

### <a name="restore-a-suspended-domain"></a>Askıya alınmış bir etki alanını geri yükleme

*Askıya alınmış* durumda olan Azure AD DS yönetilen bir etki alanının durumunu geri yüklemek için aşağıdaki adımları izleyin:

1. Azure portal **etki alanı Hizmetleri**' ni arayıp seçin.
1. Listeden Azure AD DS yönetilen etki alanınızı ( *contoso.com*gibi) seçin ve ardından **sistem durumu**' nu seçin.
1. Askıya alma nedenine bağlı olarak, *AADDS503* veya *AADDS504*gibi bir uyarı seçin.
1. Uyarıda belirtilen çözümleme bağlantısını seçin ve sorunu gidermek için adımları izleyin.

Yönetilen bir etki alanı yalnızca son yedeklemenin tarihine geri yüklenebilir. Son yedeklemelerinizin tarihi, yönetilen etki alanının **sistem durumu** sayfasında görüntülenir. Son yedekleme sonrasında gerçekleşen tüm değişiklikler geri yüklenmez. Yönetilen bir etki alanı için yedeklemeler, en fazla 30 gün boyunca depolanır. 30 günden eski olan yedeklemeler silinir.

Yönetilen etki alanı *askıda* durumdayken uyarıları çözümledikten sonra, sağlıklı bir duruma dönmek Için [bir Azure destek isteği açın][azure-support] . 30 günden daha az bir yedekleme varsa, Azure desteği yönetilen etki alanını geri yükleyebilir.

## <a name="deleted-state"></a>Durum silindi

Azure AD DS yönetilen bir etki alanı 15 gün boyunca *askıya alınma* durumunda kalırsa, silinir. Bu işlem kurtarılamaz.

### <a name="what-to-expect"></a>Sizi neler bekliyor

Azure AD DS yönetilen bir etki alanı *Silinmiş* duruma girdiğinde, aşağıdaki davranış görülür:

* Yönetilen etki alanı için tüm kaynaklar ve yedeklemeler silinir.
* Yönetilen etki alanını geri yüklemenize ve Azure AD DS yeniden kullanmak için değiştirme yönetilen etki alanı oluşturmanız gerekir.
* Silindikten sonra, yönetilen etki alanı için faturalandırılmaz.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD DS yönetilen etki alanınızı sağlıklı tutmak ve askıya alınma riskini en aza indirmek için, [yönetilen etki alanınız için uyarıları çözmeyi][resolve-alerts]öğrenin.

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
