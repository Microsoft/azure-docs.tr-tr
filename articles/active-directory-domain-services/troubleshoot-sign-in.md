---
title: Azure AD Domain Services oturum açma sorunlarını giderme | Microsoft Docs
description: Azure Active Directory Domain Services genel kullanıcı oturum açma sorunları ve hatalarıyla ilgili sorunları giderme hakkında bilgi edinin.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 85b261b8754f35c7705690d15671144b858c0a43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618578"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services yönetilen bir etki alanıyla hesap oturum açma sorunlarını giderme

Bir kullanıcı hesabının Azure Active Directory Domain Services (Azure AD DS) tarafından yönetilen bir etki alanında oturum açmasını sağlayan en yaygın nedenleri aşağıdaki senaryolar içerir:

* [Hesap henüz Azure AD DS ile eşitlenmedi.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Azure AD DS hesabının oturum açmasını sağlamak için parola karmalarını yoktur.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [Hesap kilitli.](#the-account-is-locked-out)

> [!TIP]
> Azure AD DS, Azure AD kiracısı dışındaki hesaplara yönelik kimlik bilgileri ile eşitlenemez. Dış kullanıcılar Azure AD DS yönetilen etki alanında oturum açamaz.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>Hesap henüz Azure AD DS eşitlenmedi

Dizininizin boyutuna bağlı olarak, Kullanıcı hesapları ve kimlik bilgisi karmalarının yönetilen bir etki alanında kullanılabilir olması biraz zaman alabilir. Büyük dizinler için, Azure AD 'den bu ilk tek yönlü eşitleme birkaç saat sürebilir ve günde bir veya iki kez olabilir. Kimlik doğrulamasını yeniden denemeden önce yeterince uzun süre beklemediğinizden emin olun.

Azure AD Connect kullanıcının şirket içi dizin verilerini Azure AD 'ye eşitlemesine yönelik karma ortamlarda, Azure AD Connect en son sürümünü çalıştırdığınızdan ve [Azure AD DS etkinleştirildikten sonra tam eşitleme gerçekleştirmek için Azure AD Connect yapılandırdığınızdan][azure-ad-connect-phs]emin olun. Azure AD DS devre dışı bırakıp yeniden etkinleştirirseniz, bu adımları yeniden izlemeniz gerekir.

Azure AD Connect aracılığıyla eşitlememe hesaplarından sorun yaşamaya devam ederseniz Azure AD Eşitleme hizmeti 'ni yeniden başlatın. Azure AD Connect yüklü olan bilgisayarda, bir komut istemi penceresi açın ve aşağıdaki komutları çalıştırın:

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Azure AD DS, parola karmalarını karşılamıyor

Azure AD, kiracınız için Azure AD DS etkinleştirene kadar NTLM veya Kerberos kimlik doğrulaması için gereken biçimde parola karmaları oluşturmaz veya depolamaz. Azure AD, güvenlik nedenleriyle şifresiz metin biçiminde hiçbir parola kimlik bilgisi depolamaz. Bu nedenle, Azure AD kullanıcıların mevcut kimlik bilgilerini temel alarak bu NTLM veya Kerberos parola karmalarını otomatik olarak üretemiyor.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>Şirket içi eşitlemeyle karma ortamlar

Şirket içi AD DS ortamından eşitlenmek üzere Azure AD Connect kullanan karma ortamlarda, gerekli NTLM veya Kerberos parola karmalarını yerel olarak oluşturup Azure AD 'ye eşitleyebilir. Yönetilen etki alanınızı oluşturduktan sonra, [Azure Active Directory Domain Services için Parola karması eşitlemesini etkinleştirin][azure-ad-connect-phs]. Bu parola karması eşitleme adımını tamamlamadan, yönetilen etki alanını kullanarak bir hesapta oturum açamazsınız. Azure AD DS devre dışı bırakıp yeniden etkinleştirirseniz, bu adımları yeniden izlemeniz gerekir.

Daha fazla bilgi için bkz. [Parola karması eşitlemesi Azure AD DS Için nasıl kullanılır][phs-process].

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>Şirket içi eşitleme olmadan yalnızca bulutta bulunan ortamlar

Şirket içi eşitleme olmadan yönetilen etki alanları, yalnızca Azure AD 'de bulunan hesapların gerekli NTLM veya Kerberos parola karmalarını üretmesine de gerek vardır. Yalnızca bulut hesabı oturum açamıyor, Azure AD DS etkinleştirildikten sonra hesap için bir parola değiştirme işlemi başarıyla tamamlandı mı?

* **Hayır, parola değiştirilmedi.**
    * Gerekli parola karmalarını oluşturmak için [hesabın parolasını değiştirin][enable-user-accounts] , sonra yeniden oturum açmayı denemeden önce 15 dakika bekleyin.
    * Azure AD DS devre dışı bırakıp yeniden etkinleştirirseniz, her hesabın, parolasını değiştirmek ve gerekli parola karmalarını oluşturmak için adımları yeniden izlemesi gerekir.
* **Evet, Parola değiştirildi.**
    * Gibi sAMAccountName formatı yerine *UPN* biçimini kullanarak oturum açmayı deneyin `driley@aaddscontoso.com`  `AADDSCONTOSO\deeriley` .
    * *SAMAccountName* , UPN ön eki aşırı uzun olan veya yönetilen etki alanındaki başka bir kullanıcıyla aynı olan kullanıcılar için otomatik olarak oluşturulabilir. *UPN* biçiminin BIR Azure AD kiracısı içinde benzersiz olması garanti edilir.

## <a name="the-account-is-locked-out"></a>Hesap kilitli

Yönetilen etki alanındaki bir kullanıcı hesabı, başarısız oturum açma girişimleri için tanımlanan bir eşik karşılandığında kilitlenir. Bu hesap kilitleme davranışı, otomatikleştirilmiş bir dijital saldırıyı gösterebilen yinelenen deneme yanılma girişimlerini korumak için tasarlanmıştır.

Varsayılan olarak, 2 dakika içinde 5 hatalı parola denemesi varsa, hesap 30 dakika boyunca kilitlenir.

Hesap kilitleme sorunlarını giderme hakkında daha fazla bilgi için bkz. [Azure AD DS hesap kilitleme sorunlarını giderme][troubleshoot-account-lockout].

## <a name="next-steps"></a>Sonraki adımlar

VM 'nize yönetilen etki alanına katılma sorunları yaşamaya devam ediyorsanız Yardım ' ı [bulun ve Azure Active Directory için bir destek bileti açın][azure-ad-support].

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: ./tutorial-configure-password-hash-sync.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md