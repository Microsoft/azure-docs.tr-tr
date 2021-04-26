---
title: Yönetilen etki alanları için karma Azure Active Directory birleştirmesini yapılandırma | Microsoft Docs
description: Yönetilen etki alanları için karma Azure Active Directory birleştirmesini yapılandırmayı öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 01/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4545e92767a427b8cd89af07ed4d06053685977a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578023"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Öğretici: Yönetilen etki alanları için hibrit Azure Active Directory'ye katılımı yapılandırma

Bu öğreticide, etki alanına katılmış Active Directory cihazları için karma Azure Active Directory (Azure AD) katılmayı nasıl yapılandıracağınızı öğreneceksiniz. Bu yöntem, hem şirket içi Active Directory hem de Azure AD içeren bir yönetilen ortamı destekler.

Kuruluşunuzdaki bir kullanıcı gibi, bir cihaz da korumak istediğiniz çekirdek kimliktir. Kaynaklarınızı istediğiniz zaman ve herhangi bir konumdan korumak için bir cihazın kimliğini kullanabilirsiniz. Azure AD 'de cihaz kimliklerini yöneterek bu hedefi gerçekleştirebilirsiniz. Aşağıdaki yöntemlerden birini kullanın:

- Azure AD'ye katılım
- Hibrit Azure AD'ye katılım
- Azure AD kaydı

Bu makale, karma Azure AD JOIN 'e odaklanır.

Cihazlarınızı Azure AD 'ye getirmek, bulut ve şirket içi kaynaklarınız genelinde çoklu oturum açma (SSO) ile Kullanıcı üretkenliğini en üst düzeye çıkarır. Bulut ve şirket içi kaynaklarınız için aynı anda [koşullu erişimle](../conditional-access/howto-conditional-access-policy-compliant-device.md) erişimi güvenli hale getirebilirsiniz.

Yönetilen bir ortamı, [sorunsuz çoklu oturum açma](../hybrid/how-to-connect-sso.md)ile [Parola karması eşitlemesi (PHS)](../hybrid/whatis-phs.md) veya [geçişli kimlik doğrulaması (PTA)](../hybrid/how-to-connect-pta.md) kullanarak dağıtabilirsiniz. Bu senaryolar, kimlik doğrulaması için bir federasyon sunucusu yapılandırmanızı gerektirmez.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Hibrit Azure AD'ye katılımı yapılandırma
> * Windows alt düzey cihazlarını etkinleştirme
> * Katılmış cihazları doğrulama
> * Sorun giderme

## <a name="prerequisites"></a>Önkoşullar

- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 veya üzeri)
- Azure AD kiracınız için genel yönetici kimlik bilgileri
- Ormanların her biri için Kuruluş Yöneticisi kimlik bilgileri

Bu makalelerle ilgili bilgi edinin:

- [Cihaz kimliği nedir?](overview.md)
- [Nasıl yapılır: karma Azure Active Directory JOIN Uygulamanızı planlayın](hybrid-azuread-join-plan.md)
- [Hibrit Azure AD’ye katılıma yönelik denetimli doğrulama](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD, yönetilen etki alanlarında akıllı kartlar veya sertifikaları desteklemez.

Azure AD Connect, karma Azure AD 'ye katılmış olmasını istediğiniz cihazların bilgisayar nesnelerini Azure AD 'ye eşitlemediğini doğrulayın. Bilgisayar nesneleri belirli kuruluş birimlerine (OU) aitse, OU 'Ları Azure AD Connect eşitlenecek şekilde yapılandırın. Azure AD Connect kullanarak bilgisayar nesnelerini eşitleme hakkında daha fazla bilgi için bkz. [kuruluş birimi tabanlı filtreleme](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

> [!NOTE]
> Cihaz kaydı eşitleme işleminin başarılı olması için, cihaz kayıt yapılandırması kapsamında, varsayılan cihaz özniteliklerini Azure AD Connect eşitleme yapılandırmanızla hariç tutmayın. AAD ile eşitlenen varsayılan cihaz öznitelikleri hakkında daha fazla bilgi için bkz. [Azure AD Connect tarafından eşitlenen öznitelikler](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-attributes-synchronized#windows-10).

Sürüm 1.1.819.0 ile başlayarak, Azure AD Connect karma Azure AD JOIN 'i yapılandırmaya yönelik bir sihirbaz içerir. Sihirbaz yapılandırma işlemini önemli ölçüde basitleştirir. Sihirbaz, cihaz kaydı için hizmet bağlantı noktalarını (SCP) yapılandırır.

Bu makaledeki yapılandırma adımları Azure AD Connect ' deki Sihirbazı kullanmayı temel alır.

Karma Azure AD katılımı, cihazların kuruluşunuzun ağının içinden aşağıdaki Microsoft kaynaklarına erişmesini gerektirir:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (Sorunsuz SSO kullanmayı planlıyorsanız veya kullanıyorsanız)

> [!WARNING]
> Kuruluşunuz, veri kaybı önleme veya Azure AD kiracı kısıtlamaları gibi senaryolar için SSL trafiğini kesintiye uğratan ara sunucular kullanıyorsa, ' https://device.login.microsoftonline.com ' TRAFIĞININ TLS kesme ve İnceleme dışında dışlandığından emin olun. ' ' Dışlanmaması https://device.login.microsoftonline.com , istemci sertifikası kimlik doğrulaması ile kesintiye neden olabilir ve cihaz kaydı ve cihaz tabanlı koşullu erişimle ilgili sorunlara neden olabilir.

Kuruluşunuzun giden bir ara sunucu üzerinden İnternet 'e erişmesi gerekiyorsa, Azure AD ile cihaz kaydı için Windows 10 bilgisayarlarını etkinleştirmek üzere [Web proxy otomatik bulma 'yı (WPAD) uygulamayı](/previous-versions/tn-archive/cc995261(v=technet.10)) kullanabilirsiniz. WPAD yapılandırma ve yönetme sorunlarını gidermek için bkz. [otomatik algılama sorunlarını giderme](/previous-versions/tn-archive/cc302643(v=technet.10)). Windows 10 cihazlarında 1709 güncelleştirmesinden önce, WPAD, karma Azure AD JOIN ile çalışmak üzere bir proxy yapılandırmak için kullanılabilen tek seçenektir. 

WPAD kullanmıyorsanız, bilgisayarınızda Windows 10 1709 ile başlayarak WinHTTP proxy ayarlarını yapılandırabilirsiniz. Daha fazla bilgi için bkz. [GPO tarafından dağıtılan WinHTTP proxy ayarları](/archive/blogs/netgeeks/winhttp-proxy-settings-deployed-by-gpo).

> [!NOTE]
> Bilgisayarınızda, WinHTTP ayarlarını kullanarak ara sunucu ayarlarını yapılandırırsanız, yapılandırılmış ara sunucuya bağlanamamakta olan tüm bilgisayarlar internet 'e bağlanamaz.

Kuruluşunuzun kimliği doğrulanmış bir giden ara sunucu üzerinden İnternet 'e erişmesi gerekiyorsa, Windows 10 bilgisayarlarınızın giden ara sunucuda başarıyla kimlik doğrulaması yapamadığından emin olun. Windows 10 bilgisayarları makine bağlamını kullanarak cihaz kaydı çalıştırdığından, makine bağlamını kullanarak giden proxy kimlik doğrulamasını yapılandırın. Yapılandırma gereksinimleri için giden bağlantı proxy'si sağlayıcınızı izleyin.

Cihazın, [test cihazı kayıt bağlantısı](/samples/azure-samples/testdeviceregconnectivity/testdeviceregconnectivity/) betiğini kullanarak sistem hesabı altında yukarıdaki Microsoft kaynaklarına erişebileceğini doğrulayın.

## <a name="configure-hybrid-azure-ad-join"></a>Hibrit Azure AD'ye katılımı yapılandırma

Azure AD Connect kullanarak hibrit bir Azure AD katılımı yapılandırmak için:

1. Azure AD Connect başlatın ve ardından **Yapılandır**' ı seçin.

1. **Ek görevler**' de **cihaz seçeneklerini yapılandır**' ı seçin ve ardından **İleri**' yi seçin.

   ![Ek görevler](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-additional-tasks.png)

1. **Genel bakış**' da **İleri**' yi seçin.

1. **Azure AD 'ye Bağlan**' da Azure AD kiracınız için genel bir yöneticinin kimlik bilgilerini girin.  

1. **Cihaz seçenekleri**' nde **karma Azure AD birleştirmesini Yapılandır**' ı seçin ve ardından **İleri**' yi seçin.

   ![Cihaz seçenekleri](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-options.png)

1. **Cihaz işletim sistemlerinde** Active Directory ortamınızdaki cihazların kullandığı işletim sistemlerini seçin ve ardından **İleri**' yi seçin.

   ![Cihaz işletim sistemi](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-operating-systems.png)

1. **SCP yapılandırmasında** Azure AD Connect SCP 'yi yapılandırmak istediğiniz her orman için aşağıdaki adımları tamamlayıp **İleri**' yi seçin.

   1. **Ormanı** seçin.
   1. Bir **kimlik doğrulama hizmeti** seçin.
   1. Kuruluş Yöneticisi kimlik bilgilerini girmek için **Ekle** ' yi seçin.

   ![SCP](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-scp-configuration.png)

1. **Yapılandırmaya hazırlanma** bölümünde **Yapılandır**' ı seçin.

1. **Yapılandırma Tamam** bölümünde **Çıkış**' ı seçin.

## <a name="enable-windows-down-level-devices"></a>Windows alt düzey cihazlarını etkinleştirme

Etki alanına katılmış cihazlarınızdan bazıları Windows alt düzey aygıtlardır, şunları yapmanız gerekir:

- Cihaz kaydı için yerel intranet ayarlarını yapılandırma
- Sorunsuz SSO yapılandırma
- Windows alt düzey bilgisayarlar için Microsoft Workplace Join 'yi yükler

> [!NOTE]
> Windows 7 desteği 14 Ocak 2020 tarihinde sona erdi. Daha fazla bilgi için bkz. [Windows 7 desteği sona erdi](https://support.microsoft.com/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Cihaz kaydı için yerel intranet ayarlarını yapılandırma

Windows alt düzey cihazlarınızın karma Azure AD katılmasını tamamlayıp cihazların Azure AD 'de kimlik doğrulaması yaparken sertifika istemlerine engel olmak için, Internet Explorer 'daki yerel intranet bölgesine aşağıdaki URL 'Leri eklemek üzere etki alanına katılmış cihazlarınıza bir ilke gönderebilirsiniz:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Ayrıca, kullanıcının yerel intranet bölgesindeki **komut dosyası aracılığıyla durum çubuğu güncelleştirmelerine Izin ver** ' i etkinleştirmeniz gerekir.

### <a name="configure-seamless-sso"></a>Sorunsuz SSO yapılandırma

Windows alt düzey cihazlarınızın karma Azure AD birleştirmesini, [Parola karması eşitleme](../hybrid/whatis-phs.md) veya Azure AD bulut kimlik doğrulama yöntemi olarak [geçişli kimlik doğrulaması](../hybrid/how-to-connect-pta.md) kullanan yönetilen bir etkı alanında, [sorunsuz SSO](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature)'yu da yapılandırmanız gerekir.

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Windows alt düzey bilgisayarlar için Microsoft Workplace Join 'yi yükler

Windows alt düzey cihazlarını kaydettirmek için kuruluşların [Windows 10 olmayan bilgisayarlar Için Microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554)yüklemesi gerekir. Windows 10 olmayan bilgisayarlar için Microsoft Workplace Join, Microsoft Indirme Merkezi ' nde bulunabilir.

Paketi [Microsoft uç noktası Configuration Manager](/configmgr/)gibi bir yazılım dağıtım sistemi kullanarak dağıtabilirsiniz. Paket, parametresiyle standart sessiz yükleme seçeneklerini destekler `quiet` . Configuration Manager geçerli sürümü, tamamlanan kayıtları izleme özelliği gibi önceki sürümlerden daha fazla avantaj sunar.

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
3. **Kayıtlı** sütun **bekliyor** Ifadesini IÇERIYORSA, karma Azure AD katılımı tamamlanmaz.
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

Etki alanına katılmış Windows cihazlarına yönelik karma Azure AD katılımı tamamlanırken sorunlarla karşılaşırsanız, bkz.:

- [Dsregcmd komutunu kullanan cihazların sorunlarını giderme](./troubleshoot-device-dsregcmd.md)
- [Hibrit Azure Active Directory'ye katılmış cihazlarla ilgili sorunları giderme](troubleshoot-hybrid-join-windows-current.md)
- [Karma Azure Active Directory katılmış alt düzey cihazlarda sorun giderme](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Sonraki adımlar

Azure portal kullanarak cihaz kimliklerini yönetmeyi öğrenmek için sonraki makaleye ilerleyin.
> [!div class="nextstepaction"]
> [Cihaz kimliklerini yönetme](device-management-azure-portal.md)
