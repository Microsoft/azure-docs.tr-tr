---
title: Azure AD Domain Services oturum açma sorunlarını giderme | Microsoft Docs
description: Azure Active Directory Domain Services genel kullanıcı oturum açma sorunlarını ve hatalarını nasıl giderebileceğinizi öğrenin.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: aa03e388019bf696324ea7af6062ec98386df5fa
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827042"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services yönetilen bir etki alanıyla hesap oturum açma sorunlarını giderme

Bir kullanıcı hesabının Azure AD DS yönetilen bir etki alanında oturum açması için en yaygın nedenleri aşağıdaki senaryolar içerir:

* [Hesap henüz Azure AD DS ile eşitlenmedi.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Azure AD DS hesabının oturum açmasını sağlamak için parola karmalarını yoktur.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [Hesap kilitli.](#the-account-is-locked-out)

> [!TIP]
> Azure AD DS, Azure AD kiracısı dışındaki hesaplara yönelik kimlik bilgileri ile eşitlenemez. Dış kullanıcılar Azure AD DS yönetilen etki alanında oturum açamaz.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>Hesap henüz Azure AD DS eşitlenmedi

Dizininizin boyutuna bağlı olarak, Kullanıcı hesapları ve kimlik bilgisi karmalarının Azure AD DS 'de kullanılabilir olması biraz zaman alabilir. Büyük dizinler için, Azure AD 'den bu ilk tek yönlü eşitleme birkaç saat sürebilir ve günde bir veya iki kez olabilir. Kimlik doğrulamasını yeniden denemeden önce yeterince uzun süre beklemediğinizden emin olun.

Azure AD Connect kullanıcının şirket içi dizin verilerini Azure AD 'ye eşitlemesine yönelik karma ortamlarda, Azure AD Connect en son sürümünü çalıştırdığınızdan ve [Azure 'u etkinleştirdikten sonra tam eşitleme gerçekleştirmek için Azure AD Connect yapılandırdığınızdan emin olun AD DS][azure-ad-connect-phs]. Azure AD DS devre dışı bırakıp yeniden etkinleştirirseniz, bu adımları yeniden izlemeniz gerekir.

Azure AD Connect aracılığıyla eşitlememe hesaplarından sorun yaşamaya devam ederseniz Azure AD Eşitleme hizmeti 'ni yeniden başlatın. Azure AD Connect yüklü olan bilgisayarda, bir komut istemi penceresi açın ve aşağıdaki komutları çalıştırın:

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Azure AD DS, parola karmalarını karşılamıyor

Azure AD, kiracınız için Azure AD DS etkinleştirene kadar NTLM veya Kerberos kimlik doğrulaması için gereken biçimde parola karmaları oluşturmaz veya depolamaz. Azure AD, güvenlik nedenleriyle şifresiz metin biçiminde hiçbir parola kimlik bilgisi depolamaz. Bu nedenle, Azure AD kullanıcıların mevcut kimlik bilgilerini temel alarak bu NTLM veya Kerberos parola karmalarını otomatik olarak üretemiyor.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>Şirket içi eşitlemeyle karma ortamlar

Şirket içi AD DS ortamından eşitlenmek üzere Azure AD Connect kullanan karma ortamlarda, gerekli NTLM veya Kerberos parola karmalarını yerel olarak oluşturup Azure AD 'ye eşitleyebilir. Azure AD DS yönetilen etki alanınızı oluşturduktan sonra, [Azure Active Directory Domain Services için Parola karması eşitlemesini etkinleştirin][azure-ad-connect-phs]. Bu parola karması eşitleme adımını tamamlamadan, Azure AD DS kullanarak bir hesapta oturum açamazsınız. Azure AD DS devre dışı bırakıp yeniden etkinleştirirseniz, bu adımları yeniden izlemeniz gerekir.

Daha fazla bilgi için bkz. [Parola karması eşitlemesi Azure AD DS Için nasıl kullanılır][phs-process].

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>Şirket içi eşitleme olmadan yalnızca bulutta bulunan ortamlar

Şirket içi eşitleme olmadan Azure AD DS yönetilen etki alanları, yalnızca Azure AD 'de bulunan hesapların gerekli NTLM veya Kerberos parola karmalarını üretmesine de ihtiyacı vardır. Yalnızca bulut hesabı oturum açamıyor, Azure AD DS etkinleştirildikten sonra hesap için bir parola değiştirme işlemi başarıyla tamamlandı mı?

* **Hayır, parola değiştirilmedi.**
    * Gerekli parola karmalarını oluşturmak için [hesabın parolasını değiştirin][enable-user-accounts] , sonra yeniden oturum açmayı denemeden önce 15 dakika bekleyin.
    * Azure AD DS devre dışı bırakıp yeniden etkinleştirirseniz, her hesabın, parolasını değiştirmek ve gerekli parola karmalarını oluşturmak için adımları yeniden izlemesi gerekir.
* **Evet, Parola değiştirildi.**
    * @No__t-3 gibi *sAMAccountName* biçimi yerine `driley@contoso.com` gibi *UPN* biçimini kullanarak oturum açmayı deneyin.
    * *SAMAccountName* , UPN ön eki aşırı uzun olan veya yönetilen etki alanındaki başka bir kullanıcıyla aynı olan kullanıcılar için otomatik olarak oluşturulabilir. *UPN* biçiminin BIR Azure AD kiracısı içinde benzersiz olması garanti edilir.

## <a name="the-account-is-locked-out"></a>Hesap kilitli

Başarısız oturum açma girişimleri için tanımlanan bir eşik karşılandığında Azure AD DS bir kullanıcı hesabı kilitlenir. Bu hesap kilitleme davranışı, otomatikleştirilmiş bir dijital saldırıyı gösterebilen yinelenen deneme yanılma girişimlerini korumak için tasarlanmıştır.

Varsayılan olarak, 2 dakika içinde 5 hatalı parola denemesi varsa, hesap 30 dakika boyunca kilitlenir.

Hesap kilitleme sorunlarını giderme hakkında daha fazla bilgi için bkz. [Azure AD DS hesap kilitleme sorunlarını giderme][troubleshoot-account-lockout].

## <a name="next-steps"></a>Sonraki adımlar

VM 'nize Azure AD DS yönetilen etki alanına katılma sorunları yaşıyorsanız, [yardım bulun ve Azure Active Directory için bir destek bileti açın][azure-ad-support].

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: active-directory-ds-getting-started-password-sync-synced-tenant.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
