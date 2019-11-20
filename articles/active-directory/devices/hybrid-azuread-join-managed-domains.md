---
title: Yönetilen etki alanları için karma Azure Active Directory birleştirmesini yapılandırma | Microsoft Docs
description: Yönetilen etki alanları için karma Azure Active Directory birleştirmesini yapılandırmayı öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 787900918035dc8b14d3a173496ab1a23b0f93bb
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68813082"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Öğretici: Yönetilen etki alanları için karma Azure Active Directory birleştirmesini yapılandırma

Kuruluşunuzdaki bir kullanıcı gibi, bir cihaz da korumak istediğiniz çekirdek kimliktir. Kaynaklarınızı istediğiniz zaman ve herhangi bir konumdan korumak için bir cihazın kimliğini kullanabilirsiniz. Aşağıdaki yöntemlerden birini kullanarak cihaz kimliklerini Azure Active Directory (Azure AD) içinde yöneterek, bu hedefi gerçekleştirebilirsiniz:

- Azure AD'ye katılım
- Hibrit Azure AD'ye katılım
- Azure AD kaydı

Cihazlarınızı Azure AD 'ye getirmek, bulut ve şirket içi kaynaklarınız genelinde çoklu oturum açma (SSO) ile Kullanıcı üretkenliğini en üst düzeye çıkarır. Bulut ve şirket içi kaynaklarınız için aynı anda [koşullu erişimle](../active-directory-conditional-access-azure-portal.md) erişimi güvenli hale getirebilirsiniz.

Bu öğreticide, yönetilen bir ortamdaki Active Directory etki alanına katılmış bilgisayarlar cihazları için karma Azure AD JOIN 'i yapılandırmayı öğreneceksiniz. 

Yönetilen bir ortam, [sorunsuz çoklu oturum açma](../hybrid/how-to-connect-sso.md)ile [Parola karması eşitlemesi (PHS)](../hybrid/whatis-phs.md) ya da [geçişli kimlik doğrulaması (PTA)](../hybrid/how-to-connect-pta.md) aracılığıyla dağıtılabilir. Bu senaryolar, kimlik doğrulaması için bir federasyon sunucusu yapılandırmanızı gerektirmez.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Hibrit Azure AD'ye katılımı yapılandırma
> * Windows alt düzey cihazlarını etkinleştirme
> * Katılmış cihazları doğrulama
> * Sorun giderme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide, bu makalelerle ilgili bilgi sahibi olduğunuz varsayılır:

- [Cihaz kimliği nedir?](overview.md)
- [Hibrit Azure AD JOIN Uygulamanızı planlayın](hybrid-azuread-join-plan.md)
- [Karma Azure AD JOIN 'in denetimli doğrulaması nasıl yapılır?](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD, yönetilen etki alanlarında akıllı kartlar veya sertifikaları desteklemez.

Bu makaledeki senaryoyu yapılandırmak için Azure AD Connect (1.1.819.0 veya üzeri) [en son sürümünün](https://www.microsoft.com/download/details.aspx?id=47594) yüklü olması gerekir.

Azure AD Connect, karma Azure AD 'ye katılmış olmasını istediğiniz cihazların bilgisayar nesnelerini Azure AD 'ye eşitlemediğini doğrulayın. Bilgisayar nesneleri belirli kuruluş birimlerine (OU) aitse, OU 'Ları Azure AD Connect de eşitlenecek şekilde yapılandırmanız gerekir. Azure AD Connect kullanarak bilgisayar nesnelerini eşitleme hakkında daha fazla bilgi için bkz. [Azure AD Connect kullanarak filtrelemeyi yapılandırma](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Sürüm 1.1.819.0 ile başlayarak, Azure AD Connect karma Azure AD JOIN 'i yapılandırmak için kullanabileceğiniz bir sihirbaz içerir. Sihirbaz yapılandırma işlemini önemli ölçüde basitleştirir. Sihirbaz, cihaz kaydı için hizmet bağlantı noktalarını (SCP) yapılandırır.

Bu makaledeki yapılandırma adımları Azure AD Connect ' deki Sihirbazı kullanmayı temel alır.

Karma Azure AD katılımı, cihazların kuruluşunuzun ağının içinden aşağıdaki Microsoft kaynaklarına erişmesini gerektirir:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`(Sorunsuz SSO kullanmayı planlıyorsanız veya kullanıyorsanız)

Kuruluşunuz, bir giden proxy üzerinden internet erişimi gerektiriyorsa, Microsoft, Windows 10 bilgisayarlarını Azure AD 'ye cihaz kaydı için etkinleştirmek üzere [Web proxy otomatik bulma (WPAD)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) uygulamasını önerir. WPAD yapılandırma ve yönetme sorunlarıyla karşılaşırsanız bkz. [otomatik algılama sorunlarını giderme](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)). 

WPAD kullanmıyorsanız ve bilgisayarınızda proxy ayarlarını yapılandırmanız gerekiyorsa, bunu Windows 10 1709 ' den başlayarak yapabilirsiniz. Daha fazla bilgi için bkz. [Grup İlkesi nesnesi (GPO) kullanarak WinHTTP ayarlarını yapılandırma](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Bilgisayarınızda, WinHTTP ayarlarını kullanarak ara sunucu ayarlarını yapılandırırsanız, yapılandırılmış ara sunucuya bağlanamamakta olan tüm bilgisayarlar internet 'e bağlanamaz.

Kuruluşunuzun kimliği doğrulanmış bir giden ara sunucu üzerinden İnternet 'e erişmesi gerekiyorsa, Windows 10 bilgisayarlarınızın giden ara sunucuda başarıyla kimlik doğrulayabileceği için emin olmanız gerekir. Windows 10 bilgisayarları makine bağlamını kullanarak cihaz kaydı çalıştırdığı için, makine bağlamını kullanarak giden proxy kimlik doğrulamasını yapılandırmanız gerekir. Yapılandırma gereksinimleri için giden bağlantı proxy'si sağlayıcınızı izleyin.

Cihazın sistem hesabı altında yukarıdaki Microsoft kaynaklarına erişip erişemediğinizi doğrulamak için, [test cihazı kayıt bağlantı](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0) betiği ' ni kullanabilirsiniz.

## <a name="configure-hybrid-azure-ad-join"></a>Hibrit Azure AD'ye katılımı yapılandırma

Azure AD Connect kullanarak bir hibrit Azure AD'ye katılımı yapılandırmak için şunlar gereklidir:

- Azure AD kiracınız için genel yönetici kimlik bilgileri
- Ormanların her biri için Kuruluş Yöneticisi kimlik bilgileri

**Azure AD Connect kullanarak hibrit bir Azure AD katılımı yapılandırmak için:**

1. Azure AD Connect başlatın ve ardından **Yapılandır**' ı seçin.

   ![Hoş Geldiniz](./media/hybrid-azuread-join-managed-domains/11.png)

1. **Ek görevler** sayfasında **cihaz seçeneklerini yapılandır**' ı seçin ve ardından **İleri**' yi seçin.

   ![Ek görevler](./media/hybrid-azuread-join-managed-domains/12.png)

1. **Genel bakış** sayfasında **İleri**' yi seçin.

   ![Genel Bakış](./media/hybrid-azuread-join-managed-domains/13.png)

1. **Azure AD'ye Bağlanma** sayfasında Azure AD kiracınızın genel yöneticisinin kimlik bilgilerini girin.  

   ![Azure AD'ye bağlan](./media/hybrid-azuread-join-managed-domains/14.png)

1. **Cihaz seçenekleri** sayfasında, **karma Azure AD birleştirmesini Yapılandır**' ı seçin ve ardından **İleri**' yi seçin.

   ![Cihaz seçenekleri](./media/hybrid-azuread-join-managed-domains/15.png)

1. SCP 'yi yapılandırmak istediğiniz her Azure AD Connect orman için **SCP** sayfasında, aşağıdaki adımları uygulayın ve ardından **İleri**' yi seçin:

   ![SCP](./media/hybrid-azuread-join-managed-domains/16.png)

   1. Ormanı seçin.
   1. Kimlik doğrulama hizmetini seçin.
   1. Kuruluş Yöneticisi kimlik bilgilerini girmek için **Ekle** ' yi seçin.

1. **Cihaz işletim sistemleri** sayfasında, Active Directory ortamınızdaki cihazların kullandığı işletim sistemlerini seçin ve ardından **İleri**' yi seçin.

   ![Cihaz işletim sistemi](./media/hybrid-azuread-join-managed-domains/17.png)

1. **Yapılandırmaya hazırlanma** sayfasında **Yapılandır**' ı seçin.

   ![Yapılandırma için hazır](./media/hybrid-azuread-join-managed-domains/19.png)

1. **Yapılandırma Tamam** sayfasında **Çıkış**' ı seçin.

   ![Yapılandırma tamamlandı](./media/hybrid-azuread-join-managed-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Windows alt düzey cihazlarını etkinleştir

Etki alanına katılmış cihazlarınızdan bazıları Windows alt düzey aygıtlardır, şunları yapmanız gerekir:

- Cihaz kaydı için yerel intranet ayarlarını yapılandırma
- Sorunsuz SSO yapılandırma
- Windows alt düzey bilgisayarlar için Microsoft Workplace Join 'yi yükler

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Cihaz kaydı için yerel intranet ayarlarını yapılandırma

Windows alt düzey cihazlarınızın karma Azure AD katılmasını başarıyla tamamlayıp cihazların Azure AD 'de kimlik doğrulaması yaparken sertifika istemlerine engel olmak için, etki alanına katılmış cihazlarınıza bir ilke göndererek Internet 'teki yerel intranet bölgesine aşağıdaki URL 'Leri ekleyebilirsiniz Gezgini

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Ayrıca, kullanıcının yerel intranet bölgesindeki **komut dosyası aracılığıyla durum çubuğu güncelleştirmelerine Izin ver** ' i etkinleştirmeniz gerekir.

### <a name="configure-seamless-sso"></a>Sorunsuz SSO yapılandırma

Azure AD bulut kimlik doğrulama yönteminiz olarak [PHS](../hybrid/whatis-phs.md) veya [PTA](../hybrid/how-to-connect-pta.md) kullanan yönetilen bir etki alanında Windows alt düzey CIHAZLARıNıZıN karma Azure AD birleştirmesini BAŞARıYLA tamamlaması için [sorunsuz SSO yapılandırmanız](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature)da gerekir.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Windows alt düzey bilgisayarlar için Microsoft Workplace Join 'yi yükler

Windows alt düzey cihazlarını kaydettirmek için kuruluşların [Windows 10 olmayan bilgisayarlar Için Microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554)yüklemesi gerekir. Windows 10 olmayan bilgisayarlar için Microsoft Workplace Join, Microsoft Indirme Merkezi ' nde bulunabilir.

Paketi, [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager)gibi bir yazılım dağıtım sistemi kullanarak dağıtabilirsiniz. Paket, `quiet` parametresiyle standart sessiz yükleme seçeneklerini destekler. Configuration Manager geçerli dalı, tamamlanan kayıtları izleme özelliği gibi önceki sürümlerden daha fazla avantaj sunar.

Yükleyici, kullanıcı bağlamında çalışan sistemde zamanlanmış bir görev oluşturur. Görev, Kullanıcı Windows 'da oturum açtığında tetiklenir. Görev, Azure AD ile kimlik doğrulamasından geçtikten sonra Kullanıcı kimlik bilgilerini kullanarak cihazı Azure AD ile sessizce birleştirir.

## <a name="verify-the-registration"></a>Kaydı doğrulama

Azure kiracınızdaki cihaz kayıt durumunu doğrulamak için [Azure Active Directory PowerShell modülündeki](/powershell/azure/install-msonlinev1?view=azureadps-2.0) **[Get-msoldevice](/powershell/msonline/v1/get-msoldevice)** cmdlet 'ini kullanabilirsiniz.

Hizmet ayrıntılarını denetlemek için **Get-MSolDevice** cmdlet 'ini kullandığınızda:

- Windows istemcisinde KIMLIĞIYLE eşleşen **CIHAZ kimliğine** sahip bir nesne var olmalıdır.
- **DeviceTrustType** değerinin **Etki Alanına Katılmış** olması gerekir. Bu ayar, Azure AD portalındaki **cihazlar** sayfasında **karma Azure AD 'ye katılmış** durumuna eşdeğerdir.
- Koşullu erişimde kullanılan cihazlarda, **Enabled** değeri **true** olmalı ve **devicetrustlevel** **yönetilmelidir**.

**Hizmet ayrıntılarını denetlemek için**:

1. Windows PowerShell 'i yönetici olarak açın.
1. Azure `Connect-MsolService` kiracınıza bağlanmak için girin.  
1. `get-msoldevice -deviceId <deviceId>` yazın.
1. **Enabled** değerinin **True** olarak ayarlandığını doğrulayın.

## <a name="troubleshoot-your-implementation"></a>Uygulamanızda sorun giderme

Etki alanına katılmış Windows cihazları için karma Azure AD JOIN 'i tamamlamada sorunlarla karşılaşırsanız, bkz.:

- [Windows geçerli cihazlarda karma Azure AD katılımı sorunlarını giderme](troubleshoot-hybrid-join-windows-current.md)
- [Windows alt düzey cihazlarda karma Azure AD katılımı sorunlarını giderme](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Sonraki adımlar

[Azure Portal kullanarak cihaz kimliklerini yönetmeyi](device-management-azure-portal.md)öğrenin.
