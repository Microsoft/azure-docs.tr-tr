---
title: Azure AD Domain Services hesap kilitleme sorunlarını giderme | Microsoft Docs
description: Azure Active Directory Domain Services ' de Kullanıcı hesaplarının kilitlenmesine neden olan yaygın sorunları nasıl giderebileceğinizi öğrenin.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 29789f299f266c86d719d56cfbf8e262907f7264
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827089"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services yönetilen bir etki alanıyla hesap kilitleme sorunlarını giderme

Yinelenen kötü amaçlı oturum açma girişimlerini engellemek için Azure AD DS, tanımlı eşikten sonra hesapları kilitler. Bu hesap kilitleme, bir oturum açma saldırısı olayı olmadan yanlışlıkla de gerçekleşebilir. Örneğin, bir Kullanıcı yanlış parolayı tekrar girerse veya bir hizmet eski bir parola kullanmaya çalışırsa, hesap kilitlenir.

Bu sorun giderme makalesinde, hesap kilitleme işlemlerinin neden olduğu ve davranışı nasıl yapılandırabileceğiniz ve kilitleme olayları sorunlarını gidermek için güvenlik denetimlerinin nasıl inceleyeceği özetlenmektedir.

## <a name="what-is-an-account-lockout"></a>Hesap kilitleme nedir?

Başarısız oturum açma girişimleri için tanımlanan bir eşik karşılandığında Azure AD DS bir kullanıcı hesabı kilitlenir. Bu hesap kilitleme davranışı, otomatikleştirilmiş bir dijital saldırıyı gösterebilen yinelenen deneme yanılma girişimlerini korumak için tasarlanmıştır.

**Varsayılan olarak, 2 dakika içinde 5 hatalı parola denemesi varsa, hesap 30 dakika boyunca kilitlenir.**

Varsayılan hesap kilitleme eşikleri, hassas parola ilkesi kullanılarak yapılandırılır. Belirli bir gereksinim kümesine sahipseniz, bu varsayılan hesap kilitleme eşiklerini geçersiz kılabilirsiniz. Ancak, sayı limitlerini azaltmayı denemek için eşik sınırlarını artırmanız önerilmez. İlk olarak hesap kilitleme davranışının kaynağını giderin.

### <a name="fine-grained-password-policy"></a>Hassas parola ilkesi

Hassas parola ilkeleri (FGPPs), bir etki alanındaki farklı kullanıcılara parola ve hesap kilitleme ilkeleri için özel kısıtlamalar uygulamanıza imkan tanır. FGPP yalnızca Azure AD DS oluşturulan kullanıcıları etkiler. Azure AD 'de Azure AD DS yönetilen etki alanı ile eşitlenen bulut kullanıcıları ve etki alanı kullanıcıları, parola ilkelerinden etkilenmez.

İlkeler, Azure AD DS yönetilen etki alanındaki grup ilişkilendirmesi aracılığıyla dağıtılır ve yaptığınız tüm değişiklikler sonraki Kullanıcı oturumu sırasında uygulanır. İlkenin değiştirilmesi, zaten kilitlenen bir kullanıcı hesabının kilidini açmıyor.

Hassas parola ilkeleri hakkında daha fazla bilgi için bkz. [parola ve hesap kilitleme Ilkelerini yapılandırma][configure-fgpp].

## <a name="common-account-lockout-reasons"></a>Ortak hesap kilitleme nedenleri

Herhangi bir kötü amaçlı amaç veya etken olmadan bir hesabın kilitlenmesinin en yaygın nedenleri şunlardır:

* **Kullanıcı kendini kilitlediği.**
    * Son parola değişikliğinden sonra, Kullanıcı önceki bir parolayı kullanmaya devam eder mi? 2 dakika içinde başarısız olan 5 denemeden varsayılan hesap kilitleme ilkesi, kullanıcının yanlışlıkla eski bir parolayı istememesinin nedeni olabilir.
* **Eski bir parolası olan bir uygulama veya hizmet var.**
    * Bir hesap uygulamalar veya hizmetler tarafından kullanılıyorsa, bu kaynaklar sürekli olarak eski bir parola kullanarak oturum açmaya çalışabilir. Bu davranış, hesabın kilitlenmesine neden olur.
    * Hesap kullanımını birden çok farklı uygulama veya hizmet genelinde en aza indirmenize ve kimlik bilgilerinin kullanıldığı yere kayıt yapmayı deneyin. Hesap parolası değiştirilirse, ilişkili uygulamaları veya hizmetleri uygun şekilde güncelleştirin.
* **Farklı bir ortamda Parola değiştirildi ve yeni parola henüz eşitlenmedi.**
    * Bir hesap parolası Azure AD DS dışında değiştirildiyse (örneğin, şirket içi AD DS ortamında), parola değişikliğinin Azure AD ile ve Azure AD DS arasında eşitlenmesi birkaç dakika sürebilir.
    * Parola eşitleme işlemi tamamlanmadan önce Azure AD DS aracılığıyla bir kaynakta oturum açmaya çalışan bir Kullanıcı, hesaplarının kilitlenmesine neden olur.

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>Güvenlik denetimleri ile hesap kilitlenmelerini giderme

Hesap kilitleme olaylarının gerçekleştiği ve nereden geldiği hakkında sorun gidermek için [Azure AD DS güvenlik denetimlerini etkinleştirin (Şu anda önizlemede)][security-audit-events]. Denetim olayları yalnızca özelliği etkinleştirdiğiniz zamandan itibaren yakalanır. İdeal olarak, sorun gidermek için hesap kilitleme sorunu *olmadan önce* güvenlik denetimlerini etkinleştirmeniz gerekir. Bir kullanıcı hesabında sürekli olarak kilitleme sorunları varsa, bir sonraki durumda güvenlik denetimlerini mümkün hale getirebilirsiniz.

Güvenlik denetimlerini etkinleştirdikten sonra, aşağıdaki örnek sorgularda *Hesap kilitleme olaylarını*gözden geçirme, kod *4740*.

Son yedi güne ait tüm hesap kilitleme olaylarını görüntüleyin:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

*Drley*adlı hesap için son yedi günün tüm hesap kilitleme olaylarını görüntüleyin.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

26 Haziran 2019 arasındaki tüm hesap kilitleme olaylarını 9 saat 00 ' da görüntüleyin ve 1 Temmuz 2019 gece yarısı, tarih ve saate göre artan düzende sıralanır:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

## <a name="next-steps"></a>Sonraki adımlar

Hesap kilitleme eşiklerini ayarlamaya yönelik hassas parola ilkeleri hakkında daha fazla bilgi için bkz. [parola ve hesap kilitleme Ilkeleri yapılandırma][configure-fgpp].

VM 'nize Azure AD DS yönetilen etki alanına katılma sorunları yaşıyorsanız, [yardım bulun ve Azure Active Directory için bir destek bileti açın][azure-ad-support].

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
