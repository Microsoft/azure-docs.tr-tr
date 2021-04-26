---
title: Federasyon etki alanları için karma Azure Active Directory birleştirmesini yapılandırma | Microsoft Docs
description: Federasyon etki alanları için karma Azure Active Directory birleştirmesini yapılandırmayı öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 221b7bdbb8ab5d0121e9c8032be8f18d8ae60d1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578065"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Öğretici: Federasyon etki alanları için hibrit Azure Active Directory'ye katılımı yapılandırma

Kuruluşunuzdaki bir kullanıcı gibi, bir cihaz da korumak istediğiniz çekirdek kimliktir. Kaynaklarınızı istediğiniz zaman ve herhangi bir konumdan korumak için bir cihazın kimliğini kullanabilirsiniz. Aşağıdaki yöntemlerden birini kullanarak cihaz kimliklerini Azure Active Directory (Azure AD) içinde yöneterek, bu hedefi gerçekleştirebilirsiniz:

- Azure AD'ye katılım
- Hibrit Azure AD'ye katılım
- Azure AD kaydı

Cihazlarınızı Azure AD 'ye getirmek, bulut ve şirket içi kaynaklarınız genelinde çoklu oturum açma (SSO) ile Kullanıcı üretkenliğini en üst düzeye çıkarır. Bulut ve şirket içi kaynaklarınız için aynı anda [koşullu erişimle](../conditional-access/howto-conditional-access-policy-compliant-device.md) erişimi güvenli hale getirebilirsiniz.

Federasyon ortamında, aşağıdaki gereksinimleri destekleyen bir kimlik sağlayıcısı olmalıdır. Active Directory Federasyon Hizmetleri (AD FS) (AD FS) kullanan bir Federasyon ortamınız varsa, aşağıdaki gereksinimler zaten desteklenmektedir.

- **Wiaormultiauthn talebi:** Bu talep, Windows alt düzey cihazlara yönelik karma Azure AD katılımı yapmak için gereklidir.
- **WS-Trust protokolü:** Bu protokol, Azure AD ile Windows geçerli karma Azure AD 'ye katılmış cihazların kimliğini doğrulamak için gereklidir.
  AD FS kullanırken, aşağıdaki WS-Trust uç noktaları etkinleştirmeniz gerekir: `/adfs/services/trust/2005/windowstransport`
   `/adfs/services/trust/13/windowstransport`
   `/adfs/services/trust/2005/usernamemixed`
   `/adfs/services/trust/13/usernamemixed`
   `/adfs/services/trust/2005/certificatemixed`
   `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **ADFS/Service/Trust/2005/windowstransport** ve **ADFS/Services/Trust/13/windowstransport** , yalnızca intranet 'e yönelik uç noktalar olarak etkinleştirilmelidir ve Web uygulaması ara sunucusu aracılığıyla extranet 'e yönelik uç noktalar olarak gösterilmemelidir. WS-Trust Windows uç noktalarını devre dışı bırakma hakkında daha fazla bilgi için bkz. [proxy üzerinde WS-Trust Windows uç noktalarını devre dışı bırakma](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). **Hizmet**  >  **uç noktaları** altında AD FS Yönetim Konsolu aracılığıyla hangi uç noktaların etkinleştirildiğini görebilirsiniz.

Bu öğreticide, AD FS kullanarak Federasyon ortamındaki Active Directory etki alanına katılmış bilgisayarlar için karma Azure AD birleştirmesini yapılandırmayı öğreneceksiniz.

Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Hibrit Azure AD'ye katılımı yapılandırma
> * Windows alt düzey cihazlarını etkinleştir
> * Kaydı doğrulama
> * Sorun giderme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide, bu makalelerle ilgili bilgi sahibi olduğunuz varsayılır:

- [Cihaz kimliği nedir?](overview.md)
- [Hibrit Azure AD JOIN Uygulamanızı planlayın](hybrid-azuread-join-plan.md)
- [Karma Azure AD JOIN 'in denetimli doğrulaması nasıl yapılır?](hybrid-azuread-join-control.md)

Bu öğreticide senaryoyu yapılandırmak için şunlar gereklidir:

- AD FS içeren Windows Server 2012 R2
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) sürüm 1.1.819.0 veya üzeri

Sürüm 1.1.819.0 ile başlayarak, Azure AD Connect karma Azure AD JOIN 'i yapılandırmak için kullanabileceğiniz bir sihirbaz içerir. Sihirbaz yapılandırma işlemini önemli ölçüde basitleştirir. İlgili sihirbaz:

- Cihaz kaydı için hizmet bağlantı noktalarını (SCP) yapılandırır
- Mevcut Azure AD bağlı olan tarafınıza yedekleme yapar
- Azure AD Trust'ınızdaki talep kurallarını güncelleştirir

Bu makaledeki yapılandırma adımları Azure AD Connect Sihirbazı 'nı kullanmayı temel alır. Azure AD Connect daha önceki bir sürümü yüklüyse, Sihirbazı kullanmak için 1.1.819 veya sonraki bir sürüme yükseltmeniz gerekir. Azure AD Connect en son sürümünü yüklemek sizin için bir seçenek değilse, [karma Azure AD JOIN 'i el ile yapılandırma](hybrid-azuread-join-manual.md)konusuna bakın.

Karma Azure AD katılımı, cihazların kuruluşunuzun ağının içinden aşağıdaki Microsoft kaynaklarına erişmesini gerektirir:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- Kuruluşunuzun güvenlik belirteci hizmeti (STS) (federe etki alanları Için)
- `https://autologon.microsoftazuread-sso.com` (Sorunsuz SSO kullanmayı planlıyorsanız veya kullanıyorsanız)

> [!WARNING]
> Kuruluşunuz, veri kaybı önleme veya Azure AD kiracı kısıtlamaları gibi senaryolar için SSL trafiğini kesintiye uğratan ara sunucular kullanıyorsa, ' https://device.login.microsoftonline.com ' TRAFIĞININ TLS kesme ve İnceleme dışında dışlandığından emin olun. ' ' Dışlanmaması https://device.login.microsoftonline.com , istemci sertifikası kimlik doğrulaması ile kesintiye neden olabilir ve cihaz kaydı ve cihaz tabanlı koşullu erişimle ilgili sorunlara neden olabilir.

Windows 10 1803 ' den itibaren, AD FS kullanarak Federasyon ortamına anında karma Azure AD katılımı başarısız olursa, Azure AD 'de karma Azure AD 'ye yönelik cihaz kaydını tamamlamaya yönelik olarak kullanılan bilgisayar nesnesini eşitlemek için Azure AD Connect güveniyoruz. Azure AD Connect, karma Azure AD 'ye katılmış olmasını istediğiniz cihazların bilgisayar nesnelerini Azure AD 'ye eşitlemediğini doğrulayın. Bilgisayar nesneleri belirli kuruluş birimlerine (OU) aitse, OU 'Ları Azure AD Connect de eşitlenecek şekilde yapılandırmanız gerekir. Azure AD Connect kullanarak bilgisayar nesnelerini eşitleme hakkında daha fazla bilgi için bkz. [Azure AD Connect kullanarak filtrelemeyi yapılandırma](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

> [!NOTE]
> Cihaz kaydı eşitleme işleminin başarılı olması için, cihaz kayıt yapılandırması kapsamında, varsayılan cihaz özniteliklerini Azure AD Connect eşitleme yapılandırmanızla hariç tutmayın. AAD ile eşitlenen varsayılan cihaz öznitelikleri hakkında daha fazla bilgi için bkz. [Azure AD Connect tarafından eşitlenen öznitelikler](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-attributes-synchronized#windows-10).

Kuruluşunuz, bir giden proxy üzerinden internet erişimi gerektiriyorsa, Microsoft, Windows 10 bilgisayarlarını Azure AD 'ye cihaz kaydı için etkinleştirmek üzere [Web proxy otomatik bulma (WPAD)](/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) uygulamasını önerir. WPAD yapılandırma ve yönetme sorunlarıyla karşılaşırsanız bkz. [otomatik algılama sorunlarını giderme](/previous-versions/tn-archive/cc302643(v=technet.10)). 

WPAD kullanmıyorsanız ve bilgisayarınızda proxy ayarlarını yapılandırmak istiyorsanız, Windows 10 1709 ' den başlayarak bunu yapabilirsiniz. Daha fazla bilgi için bkz. [Grup İlkesi nesnesi (GPO) kullanarak WinHTTP ayarlarını yapılandırma](/archive/blogs/netgeeks/winhttp-proxy-settings-deployed-by-gpo).

> [!NOTE]
> Bilgisayarınızda, WinHTTP ayarlarını kullanarak ara sunucu ayarlarını yapılandırırsanız, yapılandırılmış ara sunucuya bağlanamamakta olan tüm bilgisayarlar internet 'e bağlanamaz.

Kuruluşunuzun kimliği doğrulanmış bir giden ara sunucu üzerinden İnternet 'e erişmesi gerekiyorsa, Windows 10 bilgisayarlarınızın giden ara sunucuda başarıyla kimlik doğrulayabileceği için emin olmanız gerekir. Windows 10 bilgisayarları makine bağlamını kullanarak cihaz kaydı çalıştırdığı için, makine bağlamını kullanarak giden proxy kimlik doğrulamasını yapılandırmanız gerekir. Yapılandırma gereksinimleri için giden bağlantı proxy'si sağlayıcınızı izleyin.

Cihazın sistem hesabı altında yukarıdaki Microsoft kaynaklarına erişip erişemediğinizi doğrulamak için, [test cihazı kayıt bağlantı](/samples/azure-samples/testdeviceregconnectivity/testdeviceregconnectivity/) betiği ' ni kullanabilirsiniz.

## <a name="configure-hybrid-azure-ad-join"></a>Hibrit Azure AD'ye katılımı yapılandırma

Azure AD Connect kullanarak hibrit bir Azure AD katılımı yapılandırmak için şunlar gerekir:

- Azure AD kiracınız için genel yönetici kimlik bilgileri  
- Ormanların her biri için Kuruluş Yöneticisi kimlik bilgileri
- AD FS yöneticinizin kimlik bilgileri

**Azure AD Connect kullanarak hibrit bir Azure AD katılımı yapılandırmak için**:

1. Azure AD Connect başlatın ve ardından **Yapılandır**' ı seçin.

   ![Hoş Geldiniz](./media/hybrid-azuread-join-federated-domains/11.png)

1. **Ek görevler** sayfasında **cihaz seçeneklerini yapılandır**' ı seçin ve ardından **İleri**' yi seçin.

   ![Ek görevler](./media/hybrid-azuread-join-federated-domains/12.png)

1. **Genel bakış** sayfasında **İleri**' yi seçin.

   ![Genel Bakış](./media/hybrid-azuread-join-federated-domains/13.png)

1. **Azure AD 'ye Bağlan** sayfasında, Azure AD kiracınız için genel bir yöneticinin kimlik bilgilerini girin ve ardından **İleri**' yi seçin.

   ![Azure AD'ye Bağlanma](./media/hybrid-azuread-join-federated-domains/14.png)

1. **Cihaz seçenekleri** sayfasında, **karma Azure AD birleştirmesini Yapılandır**' ı seçin ve ardından **İleri**' yi seçin.

   ![Cihaz seçenekleri](./media/hybrid-azuread-join-federated-domains/15.png)

1. **SCP** sayfasında, aşağıdaki adımları tamamlayarak **İleri**' yi seçin:

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Ormanı seçin.
   1. Kimlik doğrulama hizmetini seçin. Kuruluşunuzun özel olarak Windows 10 istemcileri yoksa ve bilgisayar/cihaz eşitlemesini yapılandırdıysanız ya da kuruluşunuz sorunsuz SSO 'yu kullanıyorsa, **AD FS Server** ' ı seçmeniz gerekir.
   1. Kuruluş Yöneticisi kimlik bilgilerini girmek için **Ekle** ' yi seçin.

1. **Cihaz işletim sistemleri** sayfasında, Active Directory ortamınızdaki cihazların kullandığı işletim sistemlerini seçin ve ardından **İleri**' yi seçin.

   ![Cihaz işletim sistemi](./media/hybrid-azuread-join-federated-domains/17.png)

1. **Federasyon yapılandırması** sayfasında, AD FS yöneticinizin kimlik bilgilerini girin ve ardından **İleri**' yi seçin.

   ![Federasyon yapılandırması](./media/hybrid-azuread-join-federated-domains/18.png)

1. **Yapılandırmaya hazırlanma** sayfasında **Yapılandır**' ı seçin.

   ![Yapılandırma için hazır](./media/hybrid-azuread-join-federated-domains/19.png)

1. **Yapılandırma Tamam** sayfasında **Çıkış**' ı seçin.

   ![Yapılandırma tamamlandı](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Windows alt düzey cihazlarını etkinleştir

Etki alanına katılmış cihazlarınızdan bazıları Windows alt düzey aygıtlardır, şunları yapmanız gerekir:

- Cihaz kaydı için yerel intranet ayarlarını yapılandırma
- Windows alt düzey bilgisayarlar için Microsoft Workplace Join 'yi yükler

> [!NOTE]
> Windows 7 desteği 14 Ocak 2020 tarihinde sona erdi. Daha fazla bilgi için [Windows 7 desteği sona erdi](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Cihaz kaydı için yerel intranet ayarlarını yapılandırma

Windows alt düzey cihazlarınızın karma Azure AD katılmasını başarıyla tamamlayıp cihazların Azure AD 'de kimlik doğrulaması yaparken sertifika istemlerine engel olmak için, etki alanına katılmış cihazlarınıza aşağıdaki URL 'Leri Internet Explorer 'da yerel intranet bölgesine eklemek üzere bir ilke gönderebilirsiniz:

- `https://device.login.microsoftonline.com`
- Kuruluşunuzun STS 'si (federe etki alanları Için)
- `https://autologon.microsoftazuread-sso.com` (Sorunsuz SSO için)

Ayrıca, kullanıcının yerel intranet bölgesindeki **komut dosyası aracılığıyla durum çubuğu güncelleştirmelerine Izin ver** ' i etkinleştirmeniz gerekir.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Windows alt düzey bilgisayarlar için Microsoft Workplace Join 'yi yükler

Windows alt düzey cihazlarını kaydettirmek için kuruluşların [Windows 10 olmayan bilgisayarlar Için Microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554)yüklemesi gerekir. Windows 10 olmayan bilgisayarlar için Microsoft Workplace Join, Microsoft Indirme Merkezi ' nde bulunabilir.

Paketi [Microsoft uç noktası Configuration Manager](/configmgr/)gibi bir yazılım dağıtım sistemi kullanarak dağıtabilirsiniz. Paket, parametresiyle standart sessiz yükleme seçeneklerini destekler `quiet` . Configuration Manager geçerli dalı, tamamlanan kayıtları izleme özelliği gibi önceki sürümlerden daha fazla avantaj sunar.

Yükleyici, kullanıcı bağlamında çalışan sistemde zamanlanmış bir görev oluşturur. Görev, Kullanıcı Windows 'da oturum açtığında tetiklenir. Görev, Azure AD ile kimlik doğrulamasından geçtikten sonra Kullanıcı kimlik bilgilerini kullanarak cihazı Azure AD ile sessizce birleştirir.

## <a name="verify-the-registration"></a>Kaydı doğrulama

Cihaz durumunu bulup doğrulamak için 3 yol aşağıda verilmiştir:

### <a name="locally-on-the-device"></a>Cihazda yerel olarak

1. Windows PowerShell'i açın.
2. `dsregcmd /status` yazın.
3. Hem **Azureadkatılmış** hem de **Domainkatılmış** öğelerinin **Evet** olarak ayarlandığını doğrulayın.
4. **DeviceID** 'yi kullanabilir ve Azure Portal veya PowerShell kullanarak hizmet durumunu karşılaştırabilirsiniz.

### <a name="using-the-azure-portal"></a>Azure portalını kullanma

1. [Doğrudan bağlantı](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices)kullanarak cihazlar sayfasına gidin.
2. Cihazı bulma hakkında bilgi [, Azure Portal kullanarak cihaz kimliklerini yönetme](./device-management-azure-portal.md)bölümünde bulunabilir.
3. **Kayıtlı** sütun **bekliyor** Ifadesini IÇERIYORSA, karma Azure AD katılımı tamamlanmaz. Federasyon ortamlarında, bu durum yalnızca kayıt başarısız olduysa ve AAD Connect cihazları eşitlemek üzere yapılandırılmışsa gerçekleşebilir.
4. **Kayıtlı** sütun bir **Tarih/saat** IÇERIYORSA, karma Azure AD katılımı tamamlanmıştır.

### <a name="using-powershell"></a>PowerShell’i kullanma

**[Get-MsolDevice](/powershell/module/msonline/get-msoldevice)** kullanarak Azure kiracınızdaki cihaz kayıt durumunu doğrulayın. Bu cmdlet [Azure Active Directory PowerShell modülüdür](/powershell/azure/active-directory/install-msonlinev1).

Hizmet ayrıntılarını denetlemek için **Get-MSolDevice** cmdlet 'ini kullandığınızda:

- Windows istemcisinde KIMLIĞIYLE eşleşen **CIHAZ kimliğine** sahip bir nesne var olmalıdır.
- **Devicetrusttype** değeri **etki alanına katılmış**. Bu ayar, Azure AD portalındaki **cihazlar** sayfasında **karma Azure AD 'ye katılmış** durumuna eşdeğerdir.
- Koşullu erişimde kullanılan cihazlarda, **Enabled** değeri **true** 'Dur ve **devicetrustlevel** **yönetilir**.

1. Windows PowerShell'i yönetici olarak açın.
2. `Connect-MsolService`Azure kiracınıza bağlanmak için girin.

#### <a name="count-all-hybrid-azure-ad-joined-devices-excluding-pending-state"></a>Tüm karma Azure AD 'ye katılmış cihazları say ( **bekleme** durumu hariç)

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="count-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>**Bekleme** durumundaki tüm karma Azure AD 'ye katılmış cihazları say

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="list-all-hybrid-azure-ad-joined-devices"></a>Tüm karma Azure AD 'ye katılmış cihazları listeleyin

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>**Bekleme** durumundaki tüm karma Azure AD 'ye katılmış cihazları listeleyin

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-details-of-a-single-device"></a>Tek bir cihazın ayrıntılarını listeleyin:

1. Yazın `get-msoldevice -deviceId <deviceId>` (Bu, cihazda yerel olarak alınan **DeviceID** 'dir).
2. **Enabled** değerinin **True** olarak ayarlandığını doğrulayın.

## <a name="troubleshoot-your-implementation"></a>Uygulamanızda sorun giderme

Etki alanına katılmış Windows cihazları için karma Azure AD JOIN 'i tamamlamada sorunlarla karşılaşırsanız, bkz.:

- [Dsregcmd komutunu kullanan cihazların sorunlarını giderme](./troubleshoot-device-dsregcmd.md)
- [Windows geçerli cihazlarda karma Azure AD katılımı sorunlarını giderme](troubleshoot-hybrid-join-windows-current.md)
- [Windows alt düzey cihazlarda karma Azure AD katılımı sorunlarını giderme](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Sonraki adımlar

[Azure Portal kullanarak cihaz kimliklerini yönetmeyi](device-management-azure-portal.md)öğrenin.

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
