---
title: Azure AD Domain Services hesap kilitleme sorunlarını giderme | Microsoft Docs
description: Azure Active Directory Domain Services ' de Kullanıcı hesaplarının kilitlenmesine neden olan yaygın sorunları nasıl giderebileceğinizi öğrenin.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 3341f290a5a5bb169b6e70ea22459a2afafedbbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103198955"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services yönetilen bir etki alanıyla hesap kilitleme sorunlarını giderme

Yinelenen kötü amaçlı oturum açma girişimlerini engellemek için, bir Azure Active Directory Domain Services (Azure AD DS) yönetilen etki alanı, tanımlı eşikten sonra hesapları kilitler. Bu hesap kilitleme, bir oturum açma saldırısı olayı olmadan yanlışlıkla de gerçekleşebilir. Örneğin, bir Kullanıcı yanlış parolayı tekrar girerse veya bir hizmet eski bir parola kullanmaya çalışırsa, hesap kilitlenir.

Bu sorun giderme makalesinde, hesap kilitleme işlemlerinin neden olduğu ve davranışı nasıl yapılandırabileceğiniz ve kilitleme olayları sorunlarını gidermek için güvenlik denetimlerinin nasıl inceleyeceği özetlenmektedir.

## <a name="what-is-an-account-lockout"></a>Hesap kilitleme nedir?

Azure AD DS yönetilen etki alanındaki bir kullanıcı hesabı, başarısız oturum açma girişimleri için tanımlanan bir eşik karşılandığında kilitlenir. Bu hesap kilitleme davranışı, otomatikleştirilmiş bir dijital saldırıyı gösterebilen yinelenen deneme yanılma girişimlerini korumak için tasarlanmıştır.

**Varsayılan olarak, 2 dakika içinde 5 hatalı parola denemesi varsa, hesap 30 dakika boyunca kilitlenir.**

Varsayılan hesap kilitleme eşikleri, hassas parola ilkesi kullanılarak yapılandırılır. Belirli bir gereksinim kümesine sahipseniz, bu varsayılan hesap kilitleme eşiklerini geçersiz kılabilirsiniz. Ancak, sayı limitlerini azaltmayı denemek için eşik sınırlarını artırmanız önerilmez. İlk olarak hesap kilitleme davranışının kaynağını giderin.

### <a name="fine-grained-password-policy"></a>Hassas parola ilkesi

Hassas parola ilkeleri (FGPPs), bir etki alanındaki farklı kullanıcılara parola ve hesap kilitleme ilkeleri için özel kısıtlamalar uygulamanıza imkan tanır. FGPP yalnızca yönetilen bir etki alanı içindeki kullanıcıları etkiler. Azure AD 'de yönetilen etki alanı ile eşitlenen bulut kullanıcıları ve etki alanı kullanıcıları yalnızca yönetilen etki alanındaki parola ilkelerinden etkilenir. Azure AD 'deki hesapları veya şirket içi bir dizin etkilenmemektedir.

İlkeler, yönetilen etki alanındaki grup ilişkilendirmesi aracılığıyla dağıtılır ve yaptığınız tüm değişiklikler bir sonraki Kullanıcı oturumu sırasında uygulanır. İlkenin değiştirilmesi, zaten kilitlenen bir kullanıcı hesabının kilidini açmıyor.

Hassas parola ilkeleri hakkında daha fazla bilgi ve Azure AD 'de doğrudan Azure AD DS tarafından oluşturulan kullanıcılar arasındaki farklılıklar, bkz. [parola ve hesap kilitleme Ilkeleri yapılandırma][configure-fgpp].

## <a name="common-account-lockout-reasons"></a>Ortak hesap kilitleme nedenleri

Herhangi bir kötü amaçlı amaç veya etken olmadan bir hesabın kilitlenmesinin en yaygın nedenleri şunlardır:

* **Kullanıcı kendini kilitlediği.**
    * Son parola değişikliğinden sonra, Kullanıcı önceki bir parolayı kullanmaya devam eder mi? 2 dakika içinde başarısız olan beş denemeden kaynaklanan varsayılan hesap kilitleme ilkesi, kullanıcı yanlışlıkla eski bir parolayı yeniden deniyor olabilir.
* **Eski bir parolası olan bir uygulama veya hizmet var.**
    * Bir hesap uygulamalar veya hizmetler tarafından kullanılıyorsa, bu kaynaklar sürekli olarak eski bir parola kullanarak oturum açmaya çalışabilir. Bu davranış, hesabın kilitlenmesine neden olur.
    * Hesap kullanımını birden çok farklı uygulama veya hizmet genelinde en aza indirmenize ve kimlik bilgilerinin kullanıldığı yere kayıt yapmayı deneyin. Hesap parolası değiştirilirse, ilişkili uygulamaları veya hizmetleri uygun şekilde güncelleştirin.
* **Farklı bir ortamda Parola değiştirildi ve yeni parola henüz eşitlenmedi.**
    * Bir hesap parolası, şirket içi AD DS ortamında olduğu gibi, yönetilen etki alanının dışında değiştirilirse, parola değişikliğinin Azure AD ile yönetilen etki alanına eşitlenmesi birkaç dakika sürebilir.
    * Parola eşitleme işlemi tamamlanmadan önce yönetilen etki alanındaki bir kaynakta oturum açmaya çalışan bir Kullanıcı, hesaplarının kilitlenmesine neden olur.

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>Güvenlik denetimleri ile hesap kilitlenmelerini giderme

Hesap kilitleme olaylarının gerçekleştiği ve nereden geldiği hakkında sorun gidermek için [Azure AD DS güvenlik denetimlerini etkinleştirin][security-audit-events]. Denetim olayları yalnızca özelliği etkinleştirdiğiniz zamandan itibaren yakalanır. İdeal olarak, sorun gidermek için hesap kilitleme sorunu *olmadan önce* güvenlik denetimlerini etkinleştirmeniz gerekir. Bir kullanıcı hesabında sürekli olarak kilitleme sorunları varsa, bir sonraki durumda güvenlik denetimlerini mümkün hale getirebilirsiniz.

Güvenlik denetimlerini etkinleştirdikten sonra, aşağıdaki örnek sorgularda *Hesap kilitleme olaylarını* gözden geçirme, kod *4740*.

Son yedi güne ait tüm hesap kilitleme olaylarını görüntüleyin:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

*Drley* adlı hesap için son yedi günün tüm hesap kilitleme olaylarını görüntüleyin.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

26 Haziran 2020 arasındaki tüm hesap kilitleme olaylarını 9 saat 00 ' da görüntüleyin ve 1 Temmuz 2020 gece yarısı, tarih ve saate göre artan düzende sıralanır:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-26 09:00) and TimeGenerated <= datetime(2020-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

**Not**

"Kaynak Iş Istasyonu:" ile ilgili 4776 ve 4740 olay ayrıntılarında bulunabilir. Bunun nedeni, hatalı parolanın başka bazı cihazlar aracılığıyla ağ oturum açma üzerinden gerçekleşmektir.
Örneğin: RADIUS sunucusuna sahipseniz, kimlik doğrulamasını AAD 'ler 'e iletebilirler. DC 'nin arka uca RDP 'yi etkinleştirdiğini doğrulamak için Netlogon günlüklerini yapılandırın.

03/04 19:07:29 [oturum açma] [10752] contoso: SamLogon: Contoso\nagappan,veerappa 'dan (LOB11-RADIUS aracılığıyla) bir geçişli ağ oturumu girildi 

03/04 19:07:29 [oturum açma] [10752] contoso: SamLogon: Contoso\nagappan.exe 'ten gelen geçişli ağ oturumu açma (LOB11-RADIUS aracılığıyla), 0xC000006A döndürür

03/04 19:07:35 [oturum açma] [10753] contoso: SamLogon: Contoso\nagappan,veerappa 'dan (LOB11-RADIUS aracılığıyla) bir geçişli ağ oturumu girildi 

03/04 19:07:35 [oturum açma] [10753] contoso: SamLogon: Contoso\nagappan.exe 'ten gelen geçişli ağ oturumu açma (LOB11-RADIUS aracılığıyla), 0xC000006A döndürür

Tanılama yakalamayı yapılandırmak için NSG ile arka uca olan DC 'larınız için RDP 'yi etkinleştirin (ör. Netlogon) https://docs.microsoft.com/azure/active-directory-domain-services/alert-nsg#inbound-security-rules varsayılan NSG 'yi zaten değiştirdiyseniz, lütfen etkinleştirmek Için PSlet yolunu izleyin https://docs.microsoft.com/azure/active-directory-domain-services/network-considerations#port-3389---management-using-remote-desktop

Herhangi bir sunucuda Netlogon günlüğünü etkinleştirmek için https://docs.microsoft.com/troubleshoot/windows-client/windows-security/enable-debug-logging-netlogon-service

## <a name="next-steps"></a>Sonraki adımlar

Hesap kilitleme eşiklerini ayarlamaya yönelik hassas parola ilkeleri hakkında daha fazla bilgi için bkz. [parola ve hesap kilitleme Ilkeleri yapılandırma][configure-fgpp].

VM 'nize yönetilen etki alanına katılma sorunları yaşamaya devam ediyorsanız Yardım ' ı [bulun ve Azure Active Directory için bir destek bileti açın][azure-ad-support].

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
