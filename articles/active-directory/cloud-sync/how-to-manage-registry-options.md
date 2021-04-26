---
title: 'Azure AD Connect Cloud sağlama Aracısı: kayıt defteri seçeneklerini yönetme | Microsoft Docs'
description: Bu makalede Azure AD Connect bulut sağlama aracısında kayıt defteri seçeneklerinin nasıl yönetileceği açıklanır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2020
ms.subservice: hybrid
ms.reviewer: chmutali
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f39ef611e2ea15ef3bc3dbfcf09e9624cbcf8b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678230"
---
# <a name="manage-agent-registry-options"></a>Aracı kayıt defteri seçeneklerini yönetme

Bu bölümde, Azure AD Connect sağlama aracısının çalışma zamanı işleme davranışını denetlemek için ayarlayabileceğiniz kayıt defteri seçenekleri açıklanmaktadır. 

## <a name="configure-ldap-connection-timeout"></a>LDAP bağlantısı zaman aşımını yapılandırma
Yapılandırılmış Active Directory etki alanı denetleyicilerinde LDAP işlemlerini gerçekleştirirken, varsayılan olarak, sağlama Aracısı varsayılan bağlantı zaman aşımı değeri olan 30 saniye kullanır. Etki alanı denetleyicinizin yanıt vermesi daha fazla sürerse, aracı günlük dosyasında aşağıdaki hata iletisini görebilirsiniz: 

`
System.DirectoryServices.Protocols.LdapException: The operation was aborted because the client side timeout limit was exceeded.
`

Arama özniteliğinin dizini oluşturulmadıysa LDAP arama işlemleri daha uzun sürebilir. İlk adım olarak, yukarıdaki hatayı alırsanız, önce arama/arama özniteliğinin [dizine alınmış](/windows/win32/ad/indexed-attributes)olup olmadığını kontrol edin. Arama öznitelikleri dizine alınmışsa ve hata devam ederse, aşağıdaki adımları kullanarak LDAP bağlantısı zaman aşımını artırabilirsiniz: 

1. Azure AD Connect sağlama aracısını çalıştıran Windows Server 'da yönetici olarak oturum açın.
1. *Çalıştır* menü öğesini kullanarak kayıt defteri düzenleyicisini açın (regedit.exe) 
1. Anahtar klasörünü bulun **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Sağ tıklayın ve "New-> dize değeri" öğesini seçin
1. Adı girin: `LdapConnectionTimeoutInMilliseconds`
1. **Değer adına** çift tıklayın ve değer verisini milisaniye olarak girin `60000` .
    > [!div class="mx-imgBorder"]
    > ![LDAP bağlantısı zaman aşımı](media/how-to-manage-registry-options/ldap-connection-timeout.png)
1. Azure AD Connect sağlama hizmetini *Hizmetler* konsolundan yeniden başlatın.
1. Birden çok sağlama Aracısı dağıttıysanız, bu kayıt defteri değişikliğini tutarlılık için tüm aracılara uygulayın. 

## <a name="configure-referral-chasing"></a>Başvuru işlemesini yapılandırma
Varsayılan olarak, Azure AD Connect sağlama Aracısı [başvuruları](/windows/win32/ad/referrals)içermez. Belirli HR gelen sağlama senaryolarını desteklemek için başvuru işlemesini etkinleştirmek isteyebilirsiniz: 
* Birden çok etki alanı genelinde UPN 'nin benzersizliği denetleniyor
* Etki alanları arası yönetici başvurularını çözme

Başvuru işlemesini açmak için aşağıdaki adımları kullanın:

1. Azure AD Connect sağlama aracısını çalıştıran Windows Server 'da yönetici olarak oturum açın.
1. *Çalıştır* menü öğesini kullanarak kayıt defteri düzenleyicisini açın (regedit.exe) 
1. Anahtar klasörünü bulun **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Sağ tıklayın ve "New-> dize değeri" öğesini seçin
1. Adı girin: `ReferralChasingOptions`
1. **Değer adına** çift tıklayın ve değer verisini olarak girin `96` . Bu değer, için sabit değere karşılık gelir `ReferralChasingOptions.All` ve hem alt ağacın hem de taban düzeyi başvuruların, aracı tarafından izlenen olduğunu belirtir. 
    > [!div class="mx-imgBorder"]
    > ![Başvuru izleme](media/how-to-manage-registry-options/referral-chasing.png)
1. Azure AD Connect sağlama hizmetini *Hizmetler* konsolundan yeniden başlatın.
1. Birden çok sağlama Aracısı dağıttıysanız, bu kayıt defteri değişikliğini tutarlılık için tüm aracılara uygulayın.

## <a name="skip-gmsa-configuration"></a>GMSA yapılandırmasını atla
Aracı sürümü 1.1.281.0 + ile, varsayılan olarak, aracı yapılandırma sihirbazını çalıştırdığınızda, [Grup yönetilen hizmet hesabı (GMSA)](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)kurulumunu yapmanız istenir. Sihirbaza göre GMSA kurulumu, tüm eşitleme ve sağlama işlemleri için çalışma zamanında kullanılır. 

Aracının önceki bir sürümünden yükseltiyorsanız ve Active Directory topolojinize özgü bir OU düzeyi izinlere sahip özel bir hizmet hesabı ayarladıysanız, GMSA yapılandırmasını atlamak/ertelemeyi ve bu değişikliği planlamak isteyebilirsiniz. 

> [!NOTE]
> Bu kılavuz, 1.1.281.0 ' den önceki Aracı sürümleri ile gelen sağlama (Workday/başarılı faktörleri) ve aracı işlemleri için bir özel hizmet hesabı kurulumuna sahip olan müşteriler için özellikle geçerlidir. Uzun çalıştırmada, en iyi uygulama olarak GMSA 'ya geçiş yapmanızı öneririz.  

Bu senaryoda, hala aracı ikililerini yükseltebilir ve aşağıdaki adımları kullanarak GMSA yapılandırmasını atlayabilirsiniz: 

1. Azure AD Connect sağlama aracısını çalıştıran Windows Server 'da yönetici olarak oturum açın.
1. Yeni Aracı ikili dosyalarını yüklemek için Aracı yükleyicisini çalıştırın. Yükleme başarılı olduktan sonra otomatik olarak açılan aracı yapılandırma sihirbazını kapatın. 
1. *Çalıştır* menü öğesini kullanarak kayıt defteri düzenleyicisini açın (regedit.exe) 
1. Anahtar klasörünü bulun **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Sağ tıklayın ve "New-> DWORD value" öğesini seçin
1. Adı girin: `UseCredentials`
1. **Değer adına** çift tıklayın ve değer verisini olarak girin `1` .  
    > [!div class="mx-imgBorder"]
    > ![Kimlik bilgilerini kullan](media/how-to-manage-registry-options/use-credentials.png)
1. Azure AD Connect sağlama hizmetini *Hizmetler* konsolundan yeniden başlatın.
1. Birden çok sağlama Aracısı dağıttıysanız, bu kayıt defteri değişikliğini tutarlılık için tüm aracılara uygulayın.
1. Masaüstü kısa kesme 'den Aracı Yapılandırma Sihirbazı 'nı çalıştırın. Sihirbaz GMSA yapılandırmasını atlayacak. 


> [!NOTE]
> [Ayrıntılı günlük kaydını](how-to-troubleshoot.md#log-files)etkinleştirerek kayıt defteri seçeneklerinin ayarlandığını doğrulayabilirsiniz. Aracı başlatma sırasında yayınlanan Günlükler, kayıt defterinden çekilen yapılandırma değerlerini görüntüler. 

## <a name="next-steps"></a>Sonraki adımlar 

- [Sağlama nedir?](what-is-provisioning.md)
- [Azure AD Connect bulut eşitlemesi nedir?](what-is-cloud-sync.md)

