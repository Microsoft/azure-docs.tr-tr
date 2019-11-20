---
title: Azure Active Directory (Azure AD) içinde karma Azure Active Directory JOIN uygulamasını planlayın | Microsoft Docs
description: Hibrit Azure Active Directory'ye katılmış cihazları elle nasıl yapılandıracağınızı öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a188349e15c5e4c486d37daed9f8b611a325dcf
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74167322"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Nasıl yapılır: karma Azure Active Directory JOIN Uygulamanızı planlayın

Bir kullanıcıya benzer bir şekilde, bir cihaz korumak istediğiniz başka bir temel kimliktir ve kaynakları dilediğiniz zaman ve herhangi bir konumdan korumak için kullanabilirsiniz. Aşağıdaki yöntemlerden birini kullanarak, Azure AD 'de cihaz kimliklerini taşıyarak ve yöneterek bu hedefi gerçekleştirebilirsiniz:

- Azure AD'ye katılım
- Hibrit Azure AD'ye katılım
- Azure AD kaydı

Cihazlarınızı Azure AD'ye taşıyarak, çoklu oturum açma (SSO) özelliği sayesinde bulut ve şirket içi kaynaklarınız genelinde kullanıcılarınızın üretkenliğini en üst düzeye çıkarırsınız. Aynı zamanda bulut ve şirket içi kaynaklarınız için [koşullu erişimle](../active-directory-conditional-access-azure-portal.md)erişimi güvenli hale getirebilirsiniz.

Şirket içi Active Directory (AD) ortamınız varsa ve AD alanına katılmış bilgisayarlarınızı Azure AD 'ye eklemek istiyorsanız, karma Azure AD katılımı yaparak bunu yapabilirsiniz. Bu makalede, ortamınızda karma Azure AD katılımı uygulamak için ilgili adımlar sağlanmaktadır. 

## <a name="prerequisites"></a>Önkoşullar

Bu makalede, [Azure Active Directory ' deki cihaz kimliği yönetimine giriş](../device-management-introduction.md)hakkında bilgi sahibi olduğunuz varsayılır.

> [!NOTE]
> Windows 10 karma Azure AD JOIN için gereken en düşük etki alanı denetleyicisi sürümü Windows Server 2008 R2 'dir.

## <a name="plan-your-implementation"></a>Uygulamanızı planlama

Hibrit Azure AD uygulamanızı planlamak için şunu öğrenmeniz gerekir:

|   |   |
| --- | --- |
| ![Onay][1] | Desteklenen cihazları gözden geçir |
| ![Onay][1] | Bilmeniz gereken işlemleri gözden geçirin |
| ![Onay][1] | Karma Azure AD JOIN 'in denetimli doğrulamasını gözden geçirin |
| ![Onay][1] | Kimlik altyapınıza göre senaryonuzu seçin |
| ![Onay][1] | Karma Azure AD katılımı için şirket içi AD UPN desteğini gözden geçirin |

## <a name="review-supported-devices"></a>Desteklenen cihazları gözden geçir

Karma Azure AD katılımı, çok çeşitli Windows cihazlarını destekler. Windows 'un eski sürümlerini çalıştıran cihazların Yapılandırması ek veya farklı adımlar gerektirdiğinden, desteklenen cihazlar iki kategoride gruplandırılır:

### <a name="windows-current-devices"></a>Windows geçerli cihazlar

- Windows 10
- Windows Server 2016
- Windows Server 2019

Windows masaüstü işletim sistemi çalıştıran cihazlarda desteklenen sürüm, [Windows 10 sürüm bilgileri](https://docs.microsoft.com/windows/release-information/)makalesinde listelenmiştir. En iyi uygulama olarak Microsoft, en son Windows 10 sürümüne yükseltmenizi önerir.

### <a name="windows-down-level-devices"></a>Windows alt düzey cihazlar

- Windows 8.1
- Windows 7. Windows 7 ile ilgili destek bilgileri için bkz. [Windows 7 Için destek sonlandırılıyor](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. Windows Server 2008 ve 2008 R2 hakkında Destek bilgileri için bkz. [Windows Server Için hazırlanma 2008 destek sonu](https://www.microsoft.com/cloud-platform/windows-server-2008).

İlk planlama adımı olarak, ortamınızı gözden geçirmeniz ve Windows alt düzey cihazlarını desteklemeniz gerekip gerekmediğini belirlemeniz gerekir.

## <a name="review-things-you-should-know"></a>Bilmeniz gereken işlemleri gözden geçirin

Ortamınız tek bir AD ormanından oluşuyorsa, kimlik verilerini birden çok Azure AD kiracısıyla eşitlerken karma Azure AD katılımı Şu anda desteklenmiyor.

Ortamınız sanal masaüstü altyapısı (VDı) kullanıyorsa, bkz. [cihaz kimliği ve Masaüstü Sanallaştırması](https://docs.microsoft.com/azure/active-directory/devices/howto-device-identity-virtual-desktop-infrastructure).

Karma Azure AD katılımı, FIPS uyumlu TPM 2,0 için desteklenir ve TPM 1,2 için desteklenmez. Cihazlarınızda FIPS uyumlu TPM 1,2 varsa, hibrit Azure AD JOIN ile devam etmeden önce bunları devre dışı bırakmanız gerekir. Microsoft, TPM üreticisine bağlı olduğundan, TPMs için FIPS modunu devre dışı bırakmaya yönelik herhangi bir araç sağlamaz. Destek için lütfen donanımınızın OEM 'nize başvurun. WIndows 10 1903 sürümünden itibaren, TPMs 1,2 karma Azure AD katılımı için kullanılmaz ve bu TPMs 'Leri içeren cihazlar TPM 'ye sahip olmadıkları sürece kabul edilir.

Karma Azure AD katılımı, etki alanı denetleyicisi (DC) rolünü çalıştıran Windows Server için desteklenmez.

Karma Azure AD katılımı, kimlik bilgisi dolaşımı veya Kullanıcı profili dolaşımı kullanılırken Windows alt düzey cihazlarda desteklenmez.

Sistem Hazırlama Aracı 'nı (Sysprep) kullanıyorsanız ve yükleme için **Windows 10 1809 öncesi** bir görüntü kullanıyorsanız, görüntünün Azure AD 'ye karma Azure AD katılımı olarak zaten kayıtlı olan bir cihazdan olmadığından emin olun.

Ek VM 'Ler oluşturmak için bir sanal makine (VM) anlık görüntüsüne güvenmek istiyorsanız, anlık görüntünün Azure AD 'ye karma Azure AD katılımı olarak zaten kayıtlı olan bir VM 'den olmadığından emin olun.

Windows 10 etki alanına katılmış cihazlarınız, kiracınıza [kayıtlı Azure AD](overview.md#getting-devices-in-azure-ad) Ise, karma Azure AD 'ye katılmış ve Azure AD 'ye kayıtlı cihazın iki durumuna yol açabilir. Bu senaryoyu otomatik olarak çözmek için Windows 10 1803 ' e (KB4489894 uygulanmış olan) veya üstüne yükseltmeniz önerilir. 1803 öncesi sürümlerde, hibrit Azure AD JOIN 'i etkinleştirmeden önce Azure AD kayıtlı durumunu el ile kaldırmanız gerekecektir. 1803 ve üzeri sürümlerde, bu iki durumdan kaçınmak için aşağıdaki değişiklikler yapılmıştır:

- <i>Cihaz hibrit Azure AD 'ye katılmış olduktan sonra</i>mevcut Azure AD kayıtlı durumu otomatik olarak kaldırılır.
- Bu kayıt defteri anahtarını ekleyerek, etki alanına katılmış cihazın Azure AD 'ye kaydedilmesini engelleyebilirsiniz-Hklm\software\policies\microsoft\windows\workplacejoın, "Blockaadworkplacejoın" = DWORD: 00000001.
- Windows 10 1803 ' de, Iş için Windows Hello yapılandırılmışsa, ikili durum temizleme sonrasında kullanıcının Iş için Windows Hello 'Yu yeniden kurulumu gerekir. Bu sorun KB4512509 ile giderilmiştir

> [!NOTE]
> Azure AD kayıtlı cihaz, Intune tarafından yönetiliyorsa otomatik olarak kaldırılmaz.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Karma Azure AD JOIN 'in denetimli doğrulamasını gözden geçirin

Tüm önkoşulların yerine Windows cihazları otomatik olarak Azure AD kiracınızda cihaz olarak kaydedilir. Azure AD 'deki bu cihaz kimliklerinin durumu karma Azure AD katılımı olarak adlandırılır. Bu makalede ele alınan kavramlar hakkında daha fazla bilgi [Azure Active Directory içinde cihaz kimliği yönetimine giriş](overview.md)makalesinde bulabilirsiniz.

Kuruluşlar, karma Azure AD JOIN 'in tüm kuruluşları genelinde tek seferde etkinleştirmeden önce denetimli bir doğrulama yapmak isteyebilir. Nasıl yapılacağını anlamak için [hibrit Azure AD JOIN 'in denetlenen doğrulaması](hybrid-azuread-join-control.md) makalesini gözden geçirin.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Kimlik altyapınıza göre senaryonuzu seçin

Karma Azure AD katılımı, UPN 'nin yönlendirilebilir veya yönlendirilemeyen olmasına bağlı olarak hem yönetilen hem de Federasyon ortamları ile birlikte kullanılabilir. Desteklenen senaryolarda tablo için sayfanın alt bölümüne bakın.  

### <a name="managed-environment"></a>Yönetilen ortam

Yönetilen bir ortam, [kesintisiz çoklu oturum açma](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)Ile [Parola karması eşitlemesi (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) veya [doğrudan kimlik doğrulama (PTA)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) aracılığıyla dağıtılabilir.

Bu senaryolar, kimlik doğrulaması için bir federasyon sunucusu yapılandırmanızı gerektirmez.

### <a name="federated-environment"></a>Federasyon ortamı

Federasyon ortamında, aşağıdaki gereksinimleri destekleyen bir kimlik sağlayıcısı olmalıdır. Active Directory Federasyon Hizmetleri (AD FS) (AD FS) kullanan bir Federasyon ortamınız varsa, aşağıdaki gereksinimler zaten desteklenmektedir.

- **Wiaormultiauthn talebi:** Bu talep, Windows alt düzey cihazlara yönelik karma Azure AD katılımı yapmak için gereklidir.
- **WS-Trust protokolü:** Bu protokol, Azure AD ile Windows geçerli karma Azure AD 'ye katılmış cihazların kimliğini doğrulamak için gereklidir. AD FS kullanırken, aşağıdaki WS-Trust uç noktalarını etkinleştirmeniz gerekir: `/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Hem **ADFS/Service/Trust/2005/windowstransport** ya da **ADFS/Services/Trust/13/windowstransport** , yalnızca intranet 'e yönelik uç noktalar olarak etkinleştirilmelidir ve Web uygulaması ara sunucusu aracılığıyla extranet 'e yönelik uç noktalar olarak gösterilmemelidir. WS-Trust Windows uç noktalarını devre dışı bırakma hakkında daha fazla bilgi için, bkz. [proxy 'de WS-Trust Windows uç noktalarını devre dışı bırakma](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). **Hizmet** > **uç noktaları**altında AD FS Yönetim Konsolu aracılığıyla hangi uç noktaların etkinleştirildiğini görebilirsiniz.

> [!NOTE]
> Azure AD, yönetilen etki alanlarında akıllı kartlar veya sertifikaları desteklemez.

1\.1.819.0 sürümünden itibaren Azure AD Connect hibrit Azure AD'ye katılımı yapılandırmak için bir sihirbaz sağlar. Sihirbaz, yapılandırma işlemini önemli ölçüde basitleştirebilmenizi sağlar. Gerekli Azure AD Connect sürümünün yüklenmesi sizin için bir seçenek değilse, bkz. [cihaz kaydını el ile yapılandırma](hybrid-azuread-join-manual.md). 

Kimlik altyapınızla eşleşen senaryoya bağlı olarak, bkz:

- [Federasyon ortamına yönelik karma Azure Active Directory birleştirmesini yapılandırma](hybrid-azuread-join-federated-domains.md)
- [Yönetilen ortam için karma Azure Active Directory birleştirmesini yapılandırma](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>Karma Azure AD katılımı için şirket içi AD UPN desteğini gözden geçirin

Bazen, şirket içi AD UPN 'niz Azure AD UPN 'inizden farklı olabilir. Bu gibi durumlarda, Windows 10 hibrit Azure AD katılımı, [kimlik doğrulama yöntemine](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn), etki alanı türüne ve Windows 10 sürümüne bağlı olarak ŞIRKET Içi ad UPN 'leri için sınırlı destek sağlar. Ortamınızda mevcut olabilir iki tür şirket içi AD UPN vardır:

- Yönlendirilebilir UPN: yönlendirilebilir bir UPN, bir etki alanı kaydedicisinde kayıtlı geçerli bir doğrulanmış etki alanı içerir. Örneğin, contoso.com, Azure AD 'deki birincil etki alanı ise, contoso.org, şirket içi AD 'de contoso 'ya ait olan ve [Azure AD 'de doğrulanan](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain) birincil etki alanıdır.
- Yönlendirilebilir olmayan UPN: yönlendirilemeyen bir UPN 'de doğrulanmış bir etki alanı yoktur. Yalnızca kuruluşunuzun özel ağı dahilinde geçerlidir. Örneğin, contoso.com, Azure AD 'deki birincil etki alanı ise, contoso. Local şirket içi AD 'deki birincil etki alanıdır, ancak internet 'te doğrulanabilir bir etki alanı değildir ve yalnızca contoso ağı içinde kullanılır.

Aşağıdaki tabloda, Windows 10 karma Azure AD 'ye yönelik bu şirket içi AD UPN 'leri için destek ayrıntıları verilmiştir

| Şirket içi AD UPN türü | Etki alanı türü | Windows 10 sürümü | Açıklama |
| ----- | ----- | ----- | ----- |
| Lemez | Federasyon | 1703 sürümünden | Genel kullanıma sunuldu |
| Yönlendirilemeyen | Federasyon | 1803 sürümünden | Genel kullanıma sunuldu |
| Lemez | Yönetilen | 1803 sürümünden | Genel olarak kullanılabilir, Windows kilit ekranı üzerinde Azure AD SSPR desteklenmez |
| Yönlendirilemeyen | Yönetilen | Desteklenmiyor | |

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Federasyon ortamına yönelik karma Azure Active Directory katılmayı yapılandırın](hybrid-azuread-join-federated-domains.md)
> [yönetilen ortama yönelik karma Azure Active Directory birleştirmesini yapılandırın](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
