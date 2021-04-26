---
title: Karma Azure Active Directory katılmayı planlayın-Azure Active Directory
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
ms.openlocfilehash: cadba181ea7d6a12ca64c78f3c7c58654d5f756f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102500817"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Nasıl yapılır: karma Azure Active Directory JOIN Uygulamanızı planlayın

Bir kullanıcıya benzer bir şekilde, bir cihaz korumak istediğiniz başka bir temel kimliktir ve kaynakları dilediğiniz zaman ve herhangi bir konumdan korumak için kullanabilirsiniz. Aşağıdaki yöntemlerden birini kullanarak, Azure AD 'de cihaz kimliklerini taşıyarak ve yöneterek bu hedefi gerçekleştirebilirsiniz:

- Azure AD'ye katılım
- Hibrit Azure AD'ye katılım
- Azure AD kaydı

Cihazlarınızı Azure AD'ye taşıyarak, çoklu oturum açma (SSO) özelliği sayesinde bulut ve şirket içi kaynaklarınız genelinde kullanıcılarınızın üretkenliğini en üst düzeye çıkarırsınız. Aynı zamanda bulut ve şirket içi kaynaklarınız için [koşullu erişimle](../conditional-access/overview.md)erişimi güvenli hale getirebilirsiniz.

Şirket içi Active Directory (AD) ortamınız varsa ve AD alanına katılmış bilgisayarlarınızı Azure AD 'ye eklemek istiyorsanız, karma Azure AD katılımı yaparak bunu yapabilirsiniz. Bu makalede, ortamınızda karma Azure AD katılımı uygulamak için ilgili adımlar sağlanmaktadır. 

## <a name="prerequisites"></a>Önkoşullar

Bu makalede, [Azure Active Directory ' deki cihaz kimliği yönetimine giriş](./overview.md)hakkında bilgi sahibi olduğunuz varsayılır.

> [!NOTE]
> Windows 10 karma Azure AD JOIN için gereken en düşük etki alanı denetleyicisi sürümü Windows Server 2008 R2 'dir.

## <a name="plan-your-implementation"></a>Uygulamanızı planlayın

Hibrit Azure AD uygulamanızı planlamak için şunu öğrenmeniz gerekir:

> [!div class="checklist"]
> - Desteklenen cihazları gözden geçir
> - Bilmeniz gereken işlemleri gözden geçirin
> - Karma Azure AD JOIN 'in denetimli doğrulamasını gözden geçirin
> - Kimlik altyapınıza göre senaryonuzu seçin
> - Karma Azure AD katılımı için şirket içi AD UPN desteğini gözden geçirin

## <a name="review-supported-devices"></a>Desteklenen cihazları gözden geçir

Karma Azure AD katılımı, çok çeşitli Windows cihazlarını destekler. Windows 'un eski sürümlerini çalıştıran cihazların Yapılandırması ek veya farklı adımlar gerektirdiğinden, desteklenen cihazlar iki kategoride gruplandırılır:

### <a name="windows-current-devices"></a>Windows geçerli cihazlar

- Windows 10
- Windows Server 2016
  - **Note**: Azure Ulusal bulut müşterileri sürüm 1803 gerektirir
- Windows Server 2019

Windows masaüstü işletim sistemi çalıştıran cihazlarda desteklenen sürüm, [Windows 10 sürüm bilgileri](/windows/release-information/)makalesinde listelenmiştir. En iyi uygulama olarak Microsoft, en son Windows 10 sürümüne yükseltmenizi önerir.

### <a name="windows-down-level-devices"></a>Windows alt düzey cihazlar

- Windows 8.1
- Windows 7 desteği 14 Ocak 2020 tarihinde sona erdi. Daha fazla bilgi için bkz. [Windows 7 desteği sona erdi](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. Windows Server 2008 ve 2008 R2 hakkında Destek bilgileri için bkz. [Windows Server Için hazırlanma 2008 destek sonu](https://www.microsoft.com/cloud-platform/windows-server-2008).

İlk planlama adımı olarak, ortamınızı gözden geçirmeniz ve Windows alt düzey cihazlarını desteklemeniz gerekip gerekmediğini belirlemeniz gerekir.

## <a name="review-things-you-should-know"></a>Bilmeniz gereken işlemleri gözden geçirin

### <a name="unsupported-scenarios"></a>Desteklenmeyen senaryolar
- Ortamınız tek bir AD ormanından oluşuyorsa, kimlik verilerini birden çok Azure AD kiracısıyla eşitlerken karma Azure AD katılımı Şu anda desteklenmiyor.

- Karma Azure AD katılımı, etki alanı denetleyicisi (DC) rolünü çalıştıran Windows Server için desteklenmez.

- Karma Azure AD katılımı, kimlik bilgisi dolaşımı veya Kullanıcı profili dolaşımı veya zorunlu profili kullanılırken Windows alt düzey cihazlarda desteklenmez.

- Sunucu çekirdeği işletim sistemi herhangi bir cihaz kaydı türünü desteklemez.

- Kullanıcı Durumu Taşıma Aracı (USMT) cihaz kaydıyla birlikte çalışmaz.  

### <a name="os-imaging-considerations"></a>İşletim sistemi görüntüsü konuları
- Sistem Hazırlama Aracı 'nı (Sysprep) kullanıyorsanız ve yükleme için **Windows 10 1809 öncesi** bir görüntü kullanıyorsanız, görüntünün Azure AD 'ye karma Azure AD katılımı olarak zaten kayıtlı olan bir cihazdan olmadığından emin olun.

- Ek VM 'Ler oluşturmak için bir sanal makine (VM) anlık görüntüsüne güvenmek istiyorsanız, anlık görüntünün Azure AD 'ye karma Azure AD katılımı olarak zaten kayıtlı olan bir VM 'den olmadığından emin olun.

- Yeniden başlatma sırasında diskte yapılan değişiklikleri temizleyecek [birleştirilmiş yazma Filtresi](/windows-hardware/customize/enterprise/unified-write-filter) ve benzer teknolojiler kullanıyorsanız, cihaz hibrit Azure AD 'ye katılmış olduktan sonra uygulanmaları gerekir. Karma Azure AD JOIN 'in tamamlanmasından önce bu tür teknolojilerin etkinleştirilmesi, cihazın her yeniden başlatmada katılmasına neden olur

### <a name="handling-devices-with-azure-ad-registered-state"></a>Cihazları Azure AD kayıtlı durumuyla işleme
Windows 10 etki alanına katılmış cihazlarınız, kiracınıza [kayıtlı Azure AD](overview.md#getting-devices-in-azure-ad) Ise, karma Azure AD 'ye katılmış ve Azure AD 'ye kayıtlı cihazın iki durumuna yol açabilir. Bu senaryoyu otomatik olarak çözmek için Windows 10 1803 ' e (KB4489894 uygulanmış olan) veya üstüne yükseltmeniz önerilir. 1803 öncesi sürümlerde, hibrit Azure AD JOIN 'i etkinleştirmeden önce Azure AD kayıtlı durumunu el ile kaldırmanız gerekecektir. 1803 ve üzeri sürümlerde, bu iki durumdan kaçınmak için aşağıdaki değişiklikler yapılmıştır:

- Bir kullanıcı için mevcut Azure AD kayıtlı durumu, <i>cihaz karma Azure AD 'ye katılmış olduktan ve aynı kullanıcı oturum açtığında</i>otomatik olarak kaldırılacaktır. Örneğin, Kullanıcı A 'nın cihazda bir Azure AD kayıtlı durumu varsa, Kullanıcı a 'nın çift durumu yalnızca cihazda oturum açan kullanıcı tarafından temizlenir. Aynı cihazda birden fazla kullanıcı varsa, bu kullanıcılar oturum açarken iki durum tek tek temizlenir. Azure AD kayıtlı durumunun kaldırılmasına ek olarak, kayıt, Azure AD kaydının bir parçası olarak otomatik kayıt yoluyla gerçekleştiyse, Windows 10 da cihazın Intune veya diğer MDM 'den kaydını kaldırır.
- Cihazdaki herhangi bir yerel hesap üzerinde Azure AD kayıtlı durumu bu değişiklikten etkilenmez. Yalnızca etki alanı hesapları için geçerlidir. Bu nedenle, Kullanıcı bir etki alanı kullanıcısı olmadığından, yerel hesaplarda Azure AD kayıtlı durumu, Kullanıcı oturum açmadan sonra da otomatik olarak kaldırılmaz. 
- Hklm\software\policies\microsoft\windows\workplacejoın: "Blockaadworkplacejoın" = DWORD: 00000001 ' ye aşağıdaki kayıt defteri değerini ekleyerek etki alanına katılmış cihazın Azure AD 'den kaydedilmesini engelleyebilirsiniz.
- Windows 10 1803 ' de, Iş için Windows Hello yapılandırılmışsa, ikili durum temizleme sonrasında kullanıcının Iş için Windows Hello 'Yu yeniden kurulumu gerekir. Bu sorun KB4512509 ile giderilmiştir

> [!NOTE]
> Windows 10 yerel olarak Azure AD kayıtlı durumunu otomatik olarak kaldırsa da, Intune tarafından yönetiliyorsa Azure AD 'deki cihaz nesnesi hemen silinmez. Dsregcmd/Status ' i çalıştırarak Azure AD kayıtlı durumunun kaldırılmasını doğrulayabilir ve bu cihazı, Azure AD 'ye göre kayıtlı değil olarak kabul edebilirsiniz.

### <a name="additional-considerations"></a>Diğer konular
- Ortamınız sanal masaüstü altyapısı (VDı) kullanıyorsa, bkz. [cihaz kimliği ve Masaüstü Sanallaştırması](./howto-device-identity-virtual-desktop-infrastructure.md).

- Karma Azure AD katılımı, FIPS uyumlu TPM 2,0 için desteklenir ve TPM 1,2 için desteklenmez. Cihazlarınızda FIPS uyumlu TPM 1,2 varsa, hibrit Azure AD JOIN ile devam etmeden önce bunları devre dışı bırakmanız gerekir. Microsoft, TPM üreticisine bağlı olduğundan, TPMs için FIPS modunu devre dışı bırakmaya yönelik herhangi bir araç sağlamaz. Destek için lütfen donanımınızın OEM 'nize başvurun. 

- Windows 10 1903 sürümünden itibaren, TPMs 1,2 karma Azure AD katılımı ile kullanılmaz ve bu TPMs 'Leri içeren cihazlar bir TPM 'ye sahip olmadıkları sürece kabul edilir.

- UPN değişiklikleri yalnızca Windows 10 2004 Güncelleştirmesi ile desteklenir. Windows 10 2004 güncelleştirmesinden önceki cihazlarda, kullanıcıların cihazlarında SSO ve koşullu erişim sorunları olur. Bu sorunu çözmek için, cihazın Azure AD 'den (yükseltilmiş ayrıcalıklarla "dsregcmd/Leave" komutunu çalıştırın) ve yeniden katılmayı (otomatik olarak gerçekleşir) açmanız gerekir. Ancak, Iş için Windows Hello ile oturum açan kullanıcılar bu sorunu etkilemez.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Karma Azure AD JOIN 'in denetimli doğrulamasını gözden geçirin

Tüm önkoşulların yerine Windows cihazları otomatik olarak Azure AD kiracınızda cihaz olarak kaydedilir. Azure AD 'deki bu cihaz kimliklerinin durumu karma Azure AD katılımı olarak adlandırılır. Bu makalede ele alınan kavramlar hakkında daha fazla bilgi [Azure Active Directory içinde cihaz kimliği yönetimine giriş](overview.md)makalesinde bulabilirsiniz.

Kuruluşlar, karma Azure AD JOIN 'in tüm kuruluşları genelinde tek seferde etkinleştirmeden önce denetimli bir doğrulama yapmak isteyebilir. Nasıl yapılacağını anlamak için [hibrit Azure AD JOIN 'in denetlenen doğrulaması](hybrid-azuread-join-control.md) makalesini gözden geçirin.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Kimlik altyapınıza göre senaryonuzu seçin

Karma Azure AD katılımı, UPN 'nin yönlendirilebilir veya yönlendirilemeyen olmasına bağlı olarak hem yönetilen hem de Federasyon ortamları ile birlikte kullanılabilir. Desteklenen senaryolarda tablo için sayfanın alt bölümüne bakın.  

### <a name="managed-environment"></a>Yönetilen ortam

Yönetilen bir ortam, [kesintisiz çoklu oturum açma](../hybrid/how-to-connect-sso.md)Ile [Parola karması eşitlemesi (PHS)](../hybrid/whatis-phs.md) veya [doğrudan kimlik doğrulama (PTA)](../hybrid/how-to-connect-pta.md) aracılığıyla dağıtılabilir.

Bu senaryolar, kimlik doğrulaması için bir federasyon sunucusu yapılandırmanızı gerektirmez.

> [!NOTE]
> [Hazırlanan dağıtım kullanılarak bulut kimlik doğrulaması](../hybrid/how-to-connect-staged-rollout.md) yalnızca Windows 10 1903 güncelleştirmesi ile desteklenir

### <a name="federated-environment"></a>Federasyon ortamı

Federasyon ortamında, aşağıdaki gereksinimleri destekleyen bir kimlik sağlayıcısı olmalıdır. Active Directory Federasyon Hizmetleri (AD FS) (AD FS) kullanan bir Federasyon ortamınız varsa, aşağıdaki gereksinimler zaten desteklenmektedir.

- **Wiaormultiauthn talebi:** Bu talep, Windows alt düzey cihazlara yönelik karma Azure AD katılımı yapmak için gereklidir.
- **WS-Trust protokolü:** Bu protokol, Azure AD ile Windows geçerli karma Azure AD 'ye katılmış cihazların kimliğini doğrulamak için gereklidir. AD FS kullanırken, aşağıdaki WS-Trust uç noktaları etkinleştirmeniz gerekir: `/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Hem **ADFS/Service/Trust/2005/windowstransport** ya da **ADFS/Services/Trust/13/windowstransport** , yalnızca intranet 'e yönelik uç noktalar olarak etkinleştirilmelidir ve Web uygulaması ara sunucusu aracılığıyla extranet 'e yönelik uç noktalar olarak gösterilmemelidir. WS-Trust Windows uç noktalarını devre dışı bırakma hakkında daha fazla bilgi için bkz. [proxy üzerinde WS-Trust Windows uç noktalarını devre dışı bırakma](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). **Hizmet**  >  **uç noktaları** altında AD FS Yönetim Konsolu aracılığıyla hangi uç noktaların etkinleştirildiğini görebilirsiniz.

> [!NOTE]
> Azure AD, yönetilen etki alanlarında akıllı kartlar veya sertifikaları desteklemez.

1.1.819.0 sürümünden itibaren Azure AD Connect hibrit Azure AD'ye katılımı yapılandırmak için bir sihirbaz sağlar. Sihirbaz, yapılandırma işlemini önemli ölçüde basitleştirebilmenizi sağlar. Gerekli Azure AD Connect sürümünün yüklenmesi sizin için bir seçenek değilse, bkz. [cihaz kaydını el ile yapılandırma](hybrid-azuread-join-manual.md). 

Kimlik altyapınızla eşleşen senaryoya bağlı olarak, bkz:

- [Federasyon ortamına yönelik karma Azure Active Directory birleştirmesini yapılandırma](hybrid-azuread-join-federated-domains.md)
- [Yönetilen ortam için karma Azure Active Directory birleştirmesini yapılandırma](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>Karma Azure AD katılımı için şirket içi AD kullanıcıları UPN desteğini gözden geçirin

Bazen, şirket içi AD kullanıcılarınızın UPN 'leri Azure AD UPN 'inizden farklı olabilir. Bu gibi durumlarda, Windows 10 hibrit Azure AD katılımı, [kimlik doğrulama yöntemine](../hybrid/choose-ad-authn.md), etki alanı türüne ve Windows 10 sürümüne bağlı olarak ŞIRKET Içi ad UPN 'leri için sınırlı destek sağlar. Ortamınızda mevcut olabilir iki tür şirket içi AD UPN vardır:

- Yönlendirilebilir kullanıcılar UPN: yönlendirilebilir bir UPN, bir etki alanı kaydedicisinde kayıtlı geçerli bir doğrulanmış etki alanı içerir. Örneğin, contoso.com, Azure AD 'deki birincil etki alanı ise, contoso.org, şirket içi AD 'de contoso 'ya ait olan ve [Azure AD 'de doğrulanan](../fundamentals/add-custom-domain.md) birincil etki alanıdır.
- Yönlendirilemeyen kullanıcılar UPN: yönlendirilemeyen bir UPN 'de doğrulanmış bir etki alanı yok. Yalnızca kuruluşunuzun özel ağı dahilinde geçerlidir. Örneğin, contoso.com, Azure AD 'deki birincil etki alanı ise, contoso. Local şirket içi AD 'deki birincil etki alanıdır, ancak internet 'te doğrulanabilir bir etki alanı değildir ve yalnızca contoso ağı içinde kullanılır.

> [!NOTE]
> Bu bölümdeki bilgiler yalnızca şirket içi kullanıcıların UPN 'si için geçerlidir. Şirket içi bilgisayar etki alanı sonekine (örnek: Bilgisayar1. contoso. Local) uygulanamaz.

Aşağıdaki tabloda, Windows 10 karma Azure AD 'ye yönelik bu şirket içi AD UPN 'leri için destek ayrıntıları verilmiştir

| Şirket içi AD UPN türü | Etki alanı türü | Windows 10 sürümü | Description |
| ----- | ----- | ----- | ----- |
| Lemez | Federe | 1703 sürümünden | Genel kullanıma sunuldu |
| Yönlendirilemeyen | Federe | 1803 sürümünden | Genel kullanıma sunuldu |
| Lemez | Yönetilen | 1803 sürümünden | Genel olarak kullanılabilir, Windows kilit ekranı üzerinde Azure AD SSPR desteklenmez. Şirket içi UPN,     `onPremisesUserPrincipalName` Azure AD 'deki öznitelikle eşitlenmelidir |
| Yönlendirilemeyen | Yönetilen | Desteklenmez | |

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Federasyon ortamına](hybrid-azuread-join-federated-domains.md) 
>  yönelik karma Azure Active Directory birleştirmesini yapılandırma [Yönetilen ortam için karma Azure Active Directory birleştirmesini yapılandırma](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
