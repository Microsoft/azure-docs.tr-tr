---
title: 'Azure AD Connect: Azure Kamu bulutunda karma kimlik konuları'
description: Azure Kamu bulutuyla Azure AD Connect dağıtmaya yönelik özel konular.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bbb4298d0d9a9d7589c4a2055b4d55a0b852f951
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97504364"
---
# <a name="hybrid-identity-considerations-for-the-azure-government-cloud"></a>Azure Kamu Bulutu için karma kimlik konuları

Bu makalede, karma bir ortamı Microsoft Azure Kamu bulutu ile tümleştirmeyle ilgili konular açıklanmaktadır. Bu bilgiler, Azure Kamu bulutuyla çalışan yöneticiler ve Mimarlar için bir başvuru olarak sağlanır.

> [!NOTE]
> Azure Kamu bulutu ile bir Microsoft Active Directory ortamını (Şirket içinde veya aynı bulut örneğinin parçası olan bir IaaS 'de barındırılan) bütünleştirmek için, en son [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)sürümüne yükseltmeniz gerekir.

Savunma uç noktalarının Birleşik Devletler kamu departmanı tam listesi için [belgelere](/office365/enterprise/office-365-u-s-government-dod-endpoints)bakın.

## <a name="azure-ad-pass-through-authentication"></a>Azure AD geçişli kimlik doğrulaması

Aşağıdaki bilgiler doğrudan kimlik doğrulamanın ve Azure Kamu bulutunun uygulanmasını açıklamaktadır.

### <a name="allow-access-to-urls"></a>URL 'lere erişime izin ver

Doğrudan kimlik doğrulama aracısını dağıtmadan önce, sunucularınız ve Azure AD arasında bir güvenlik duvarının var olup olmadığını doğrulayın. Güvenlik duvarınız veya ara sunucunuz etki alanı adı sistemi (DNS) Engellenen veya güvenli programlara izin veriyorsa, aşağıdaki bağlantıları ekleyin.

> [!NOTE]
> Azure Kamu ortamları için [azure ad uygulama ara sunucusu Bağlayıcısı](../manage-apps/what-is-application-proxy.md) 'nı yüklemek için aşağıdaki kılavuz de geçerlidir.

|URL |Nasıl kullanılır?|
|-----|-----|
|&#42;. msappproxy.us</br>&#42;.servicebus.usgovcloudapi.net|Aracı bu URL 'Leri Azure AD bulut hizmeti ile iletişim kurmak için kullanır. |
|`mscrl.microsoft.us:80` </br>`crl.microsoft.us:80` </br>`ocsp.msocsp.us:80` </br>`www.microsoft.us:80`| Aracı, sertifikaları doğrulamak için bu URL 'Leri kullanır.|
|login.windows.us </br>secure.aadcdn.microsoftonline-p.com </br>&#42;. microsoftonline.us </br>&#42;. microsoftonline-p.us </br>&#42;. msauth.net </br>&#42;. msauthimages.net </br>&#42;. msecnd.net</br>&#42;. msftauth.net </br>&#42;. msftauthimages.net</br>&#42;. phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctldl.windowsupdate.us:80| Aracı, kayıt işlemi sırasında bu URL 'Leri kullanır.

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Azure Kamu Bulutu için aracıyı yükler

Azure Kamu Bulutu için aracıyı yüklemek üzere aşağıdaki adımları izleyin:

1. Komut satırı terminalinde, aracıyı yükleyen yürütülebilir dosyayı içeren klasöre gidin.
1. Yüklemesinin Azure Kamu için olduğunu belirten aşağıdaki komutları çalıştırın.

   Geçişli kimlik doğrulaması için:

   ```
   AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
   ```

   Uygulama proxy 'Si için:

   ```
   AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
   ```

## <a name="single-sign-on"></a>Çoklu oturum açma

### <a name="set-up-your-azure-ad-connect-server"></a>Azure AD Connect sunucunuzu ayarlama

Geçiş kimlik doğrulamasını oturum açma yönteminiz olarak kullanırsanız, ek önkoşul denetimi gerekli değildir. Oturum açma yönteminiz olarak parola karması eşitlemeyi kullanırsanız ve Azure AD Connect ile Azure AD arasında bir güvenlik duvarı varsa, aşağıdakileri doğrulayın:

- Azure AD Connect sürüm 1.1.644.0 veya sonraki bir sürümü kullanıyorsunuz.
- Güvenlik duvarınız veya ara sunucunuz DNS Engellenen veya güvenli programlara izin veriyorsa, bağlantı noktası 443 üzerinden &#42;. msappproxy.us URL 'Lerine bağlantı ekleyin.

  Aksi takdirde, haftalık olarak güncellenen Azure veri merkezi IP aralıklarına erişime izin verin. Bu önkoşul yalnızca özelliği etkinleştirdiğinizde geçerlidir. Gerçek Kullanıcı oturum açma bileşenleri için gerekli değildir.

### <a name="roll-out-seamless-single-sign-on"></a>Kesintisiz tek Sign-On kullanıma al

Aşağıdaki yönergeleri kullanarak, Azure AD sorunsuz tek Sign-On kullanıcılarınıza giderek ekleyebilirsiniz. Azure AD URL 'sini, `https://autologon.microsoft.us` Active Directory Grup İlkesi kullanarak tüm kullanıcıların Intranet bölgesi ayarlarına ekleyerek başlayın.

Ayrıca, **Grup İlkesi aracılığıyla betik aracılığıyla durum çubuğu güncelleştirmelerine Izin ver** intranet bölgesi ilkesi ayarını etkinleştirmeniz gerekir.

## <a name="browser-considerations"></a>Tarayıcı konuları

### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (tüm platformlar)

Mozilla Firefox, Kerberos kimlik doğrulamasını otomatik olarak kullanmaz. Aşağıdaki adımları izleyerek her bir kullanıcının, Azure AD URL 'sini Firefox ayarlarına el ile eklemesi gerekir:

1. Firefox 'u çalıştırın ve adres çubuğuna **about: config** yazın   . Görebileceğiniz tüm bildirimleri kapatın.
1.  **Network. Negotiate-Auth. Trusted-uris**   tercihini arayın. Bu tercih, Kerberos kimlik doğrulaması için Firefox tarafından güvenilen siteleri listeler.
1. Tercih adına sağ tıklayın ve ardından **Değiştir**' i seçin.
1. `https://autologon.microsoft.us`Kutuya yazın.
1.  **Tamam**   ' ı seçin ve ardından tarayıcıyı yeniden açın.

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Kmıum temelinde Microsoft Edge (tüm platformlar)

 `AuthNegotiateDelegateAllowlist`   Ortamınızdaki veya ilke ayarlarını geçersiz kıldıysanız `AuthServerAllowlist`   , Azure AD URL 'sini bunlara eklemediğinizden emin olun `https://autologon.microsoft.us` .

### <a name="google-chrome-all-platforms"></a>Google Chrome (tüm platformlar)

 `AuthNegotiateDelegateWhitelist`   Ortamınızdaki veya ilke ayarlarını geçersiz kıldıysanız `AuthServerWhitelist`   , Azure AD URL 'sini bunlara eklemediğinizden emin olun `https://autologon.microsoft.us` .

## <a name="next-steps"></a>Sonraki adımlar

- [Geçişli kimlik doğrulaması](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
- [Çoklu oturum açma](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)
