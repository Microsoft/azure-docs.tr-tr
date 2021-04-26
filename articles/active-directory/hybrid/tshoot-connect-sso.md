---
title: 'Azure Active Directory Connect: tek Sign-On sorunsuz sorun giderme | Microsoft Docs'
description: Bu konuda Azure Active Directory sorunsuz tek Sign-On nasıl giderebileceğiniz açıklanır
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eef58f6e84fb3b4dec947fa3614b6ec1043ff89e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644655"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory sorunsuz tek Sign-On sorunlarını giderme

Bu makale Azure Active Directory (Azure AD) sorunsuz tek Sign-On (sorunsuz SSO) ile ilgili yaygın sorunlar hakkında sorun giderme bilgilerini bulmanıza yardımcı olur.

## <a name="known-issues"></a>Bilinen sorunlar

- Birkaç durumda, sorunsuz SSO 'nun etkinleştirilmesi 30 dakikaya kadar sürebilir.
- Kiracınızda sorunsuz SSO 'yu devre dışı bırakıp yeniden etkinleştirirseniz, kullanıcılar önbelleğe alınmış Kerberos biletleri, genellikle 10 saat için geçerli olan ve süresi dolana kadar çoklu oturum açma deneyimini almaz.
- Sorunsuz SSO başarılı olursa Kullanıcı Oturumumu **açık tut** seçeneğini belirleme fırsatına sahip değildir. Bu davranış nedeniyle, [SharePoint ve OneDrive eşleme senaryoları](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec) çalışmaz.
- 16.0.8730. xxxx ve üzeri sürümleriyle birlikte Microsoft 365 Win32 istemcileri (Outlook, Word, Excel ve diğerleri) etkileşimli olmayan bir akış kullanılarak desteklenir. Diğer sürümler desteklenmez; Bu sürümlerde, kullanıcılar, oturum açmak için Kullanıcı adlarını girer, ancak parolalarını girmeyecektir. OneDrive için, [OneDrive sessiz yapılandırma özelliğini](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) bir sessiz oturum açma deneyimi için etkinleştirmeniz gerekir.
- Sorunsuz SSO, Firefox 'ta özel göz atma modunda çalışmaz.
- Gelişmiş Korumalı Mod açıkken sorunsuz SSO Internet Explorer 'da çalışmaz.
- Sorunsuz SSO, Microsoft Edge 'de (eski) özel göz atma modunda çalışmaz.
- Sorunsuz SSO, iOS ve Android 'de mobil tarayıcılarda çalışmaz.
- Kullanıcı Active Directory çok sayıda grubun parçasıysa, kullanıcının Kerberos bileti çok büyük olabilir ve bu işlem sorunsuz SSO 'nun başarısız olmasına neden olur. Azure AD HTTPS isteklerinde en fazla 50 KB boyutunda üst bilgi bulunabilir. Kerberos anahtarlarının, tanımlama bilgileri gibi diğer Azure AD yapılarına (genellikle, 2-5 KB) uyum sağlaması için bu sınırdan daha küçük olması gerekir. Önerimiz, kullanıcının grup üyeliklerini azaltmaktır ve yeniden deneyin.
- 30 veya daha fazla Active Directory ormanı eşitliyorsanız, Azure AD Connect aracılığıyla sorunsuz SSO etkinleştirilemez. Geçici bir çözüm olarak, kiracınızda özelliği [el ile etkinleştirebilirsiniz](#manual-reset-of-the-feature) .
- Azure AD hizmeti URL 'sini ( `https://autologon.microsoftazuread-sso.com` ) yerel intranet bölgesi yerine güvenilen siteler bölgesine eklemek *, kullanıcıların oturum açmasını engeller*.
- Sorunsuz SSO, Kerberos için AES256_HMAC_SHA1, AES128_HMAC_SHA1 ve RC4_HMAC_MD5 şifreleme türlerini destekler. AzureADSSOAcc $ hesabı için şifreleme türü AES256_HMAC_SHA1 olarak, ek güvenlik için de AES türlerinden biri de. RC4 olarak ayarlanmalıdır. Şifreleme türü, Active Directory hesabın msDS-SupportedEncryptionTypes özniteliğinde depolanır.  AzureADSSOAcc $ Account ENCRYPTION türü RC4_HMAC_MD5 olarak ayarlandıysa ve bunu AES şifreleme türlerinden biri olarak değiştirmek istiyorsanız, lütfen ilk olarak AzureADSSOAcc $ hesabının Kerberos şifre çözme anahtarını, ilgili sorunun altındaki [SSS belgesinde](how-to-connect-sso-faq.md) açıklandığı gibi aldığınızdan emin olun, aksi takdırde sorunsuz SSO gerçekleşmeyecektir.
-  Orman güveni olan birden fazla ormanınız varsa, ormanlardan birinde SSO 'yu etkinleştirmek, tüm güvenilen ormanlarda SSO 'yu etkinleştirir. SSO 'nun zaten etkinleştirildiği bir ormanda SSO 'yu etkinleştirirseniz, SSO 'nun ormanda zaten etkinleştirildiğini belirten bir hata alırsınız.

## <a name="check-status-of-feature"></a>Özelliğin durumunu denetleme

Kiracınızda sorunsuz SSO özelliğinin hala **etkinleştirildiğinden** emin olun. [Azure Active Directory Yönetim merkezinde](https://aad.portal.azure.com/) **Azure AD Connect** bölmesine giderek durumu kontrol edebilirsiniz.

![Azure Active Directory Yönetim Merkezi: Azure AD Connect bölmesi](./media/tshoot-connect-sso/sso10.png)

Sorunsuz SSO için etkinleştirilen tüm AD Ormanlarını görmek için öğesine tıklayın.

![Azure Active Directory Yönetim Merkezi: sorunsuz SSO bölmesi](./media/tshoot-connect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Azure Active Directory Yönetim merkezinde oturum açma hatası nedenleri (Premium lisansı gerekir)

Kiracınızda ilişkili bir Azure AD Premium lisansı varsa, [Azure Active Directory Yönetim merkezinde](https://aad.portal.azure.com/) [oturum açma etkinlik raporuna](../reports-monitoring/concept-sign-ins.md) da bakabilirsiniz.

![Azure Active Directory Yönetim Merkezi: oturum açma işlemleri raporu](./media/tshoot-connect-sso/sso9.png)

  >  [Azure Active Directory Yönetim merkezinde](https://aad.portal.azure.com/)Azure Active Directory **oturum açma** işlemleri yapın ve ardından belirli bir kullanıcının oturum açma etkinliğini seçin. **Oturum açma hata kodu** alanını bulun. Aşağıdaki tabloyu kullanarak bu alanın değerini bir hata nedeni ve çözümüyle eşleyin:

|Oturum açma hata kodu|Oturum açma hatası nedeni|Çözüm
| --- | --- | ---
| 81001 | Kullanıcının Kerberos anahtarı fazla büyük. | Kullanıcının grup üyeliklerini azaltın ve yeniden deneyin.
| 81002 | Kullanıcının Kerberos bileti doğrulanamıyor. | Bkz. [sorun giderme denetim listesi](#troubleshooting-checklist).
| 81003 | Kullanıcının Kerberos bileti doğrulanamıyor. | Bkz. [sorun giderme denetim listesi](#troubleshooting-checklist).
| 81004 | Kerberos kimlik doğrulaması girişimi başarısız oldu. | Bkz. [sorun giderme denetim listesi](#troubleshooting-checklist).
| 81008 | Kullanıcının Kerberos bileti doğrulanamıyor. | Bkz. [sorun giderme denetim listesi](#troubleshooting-checklist).
| 81009 | Kullanıcının Kerberos bileti doğrulanamıyor. | Bkz. [sorun giderme denetim listesi](#troubleshooting-checklist).
| 81010 | Kullanıcının Kerberos anahtarının süresi dolduğu veya anahtar geçersiz olduğu için sorunsuz SSO başarısız oldu. | Kullanıcının kurumsal ağınız içindeki etki alanına katılmış bir cihazdan oturum açması gerekir.
| 81011 | Kullanıcının Kerberos anahtarındaki bilgilere göre Kullanıcı nesnesi bulunamıyor. | Kullanıcının bilgilerini Azure AD ile eşleştirmek için Azure AD Connect kullanın.
| 81012 | Azure AD 'de oturum açmaya çalışan Kullanıcı, cihazda oturum açmış olan kullanıcıdan farklıdır. | Kullanıcının farklı bir cihazdan oturum açması gerekir.
| 81013 | Kullanıcının Kerberos anahtarındaki bilgilere göre Kullanıcı nesnesi bulunamıyor. |Kullanıcının bilgilerini Azure AD ile eşleştirmek için Azure AD Connect kullanın. 

## <a name="troubleshooting-checklist"></a>Sorun giderme denetim listesi

Sorunsuz SSO sorunlarını gidermek için aşağıdaki denetim listesini kullanın:

- Azure AD Connect 'de sorunsuz SSO özelliğinin etkinleştirildiğinden emin olun. Özelliği etkinleştiremiyorum (örneğin, engellenen bir bağlantı noktası nedeniyle), tüm [önkoşulların](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) sağlandığından emin olun.
- Kiracınızda [Azure AD JOIN](../devices/overview.md) ve sorunsuz SSO 'yu etkinleştirdiyseniz, sorunun Azure AD JOIN ile olmadığından emin olun. Cihaz hem Azure AD hem de etki alanına katılmış olarak kayıtlıysa, Azure AD JOIN 'ten gelen SSO sorunsuz SSO 'dan önceliklidir. Azure AD JOIN 'ten SSO ile Kullanıcı, "Windows 'a bağlı" yazan bir oturum açma kutucuğu görür.
- Azure AD URL 'sinin ( `https://autologon.microsoftazuread-sso.com` ) Kullanıcının Intranet bölgesi ayarlarının bir parçası olduğundan emin olun.
- Şirket cihazının Active Directory etki alanına katılmış olduğundan emin olun. Sorunsuz SSO 'nun çalışması için cihazın [Azure AD 'ye katılmış](../devices/overview.md) _olması gerekmez_ .
- Kullanıcının cihazda Active Directory bir etki alanı hesabı aracılığıyla oturum açtığından emin olun.
- Kullanıcı hesabının, sorunsuz SSO 'nun ayarlandığı bir Active Directory ormanına ait olduğundan emin olun.
- Cihazın kurumsal ağa bağlı olduğundan emin olun.
- Cihaz saatinin hem Active Directory hem de etki alanı denetleyicilerindeki zaman ile eşitlendiğinden ve bunların beş dakika içinde olduklarından emin olun.
- `AZUREADSSOACC`Bilgisayar hesabının mevcut olduğundan ve sorunsuz SSO 'nun etkin olmasını istediğiniz her ad ormanında etkinleştirildiğinden emin olun. Bilgisayar hesabı silinmişse veya eksikse, [PowerShell cmdlet 'lerini](#manual-reset-of-the-feature) kullanarak yeniden oluşturabilirsiniz.
- Komut isteminden komutunu kullanarak cihazdaki mevcut Kerberos biletlerini listeleyin `klist` . Bilgisayar hesabı için verilen anahtarların mevcut olduğundan emin olun `AZUREADSSOACC` . Kullanıcıların Kerberos biletleri genellikle 10 saat için geçerlidir. Active Directory farklı ayarlara sahip olabilirsiniz.
- Kiracınızda sorunsuz SSO 'yu devre dışı bırakırsanız ve yeniden etkinleştirdiyseniz, kullanıcılar önbelleğe alınmış Kerberos biletleri zaman aşımına erene kadar çoklu oturum açma deneyimini almaz.
- Komutunu kullanarak cihazdaki mevcut Kerberos biletlerini temizleyin `klist purge` ve yeniden deneyin.
- JavaScript ile ilgili sorunlar olup olmadığını anlamak için tarayıcının konsol günlüklerini gözden geçirin ( **Geliştirici Araçları** altında).
- [Etki alanı denetleyicisi günlüklerini](#domain-controller-logs)gözden geçirin.

### <a name="domain-controller-logs"></a>Etki alanı denetleyicisi günlükleri

Etki alanı denetleyicinizde başarı denetimini etkinleştirirseniz, Kullanıcı sorunsuz SSO aracılığıyla her oturum açtığında olay günlüğüne bir güvenlik girişi kaydedilir. Aşağıdaki sorguyu kullanarak, bu güvenlik olaylarını bulabilirsiniz. (Bilgisayar hesabı **Azureadssoacc $** ile ilişkili olay **4769** ' i arayın.)

```
  <QueryList>
    <Query Id="0" Path="Security">
      <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
    </Query>
  </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Özelliği el ile sıfırlama

Sorun giderme konusunda yardım olmadıysa, kiracınızdaki özelliği el ile sıfırlayabilirsiniz. Azure AD Connect çalıştırdığınız şirket içi sunucuda bu adımları izleyin.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>1. Adım: sorunsuz SSO PowerShell modülünü Içeri aktarma

1. İlk olarak, [Azure AD PowerShell](/powershell/azure/active-directory/overview)'i indirin ve yükleyin.
2. `%programfiles%\Microsoft Azure Active Directory Connect`Klasöre gidin.
3. Bu komutu kullanarak sorunsuz SSO PowerShell modülünü içeri aktarın: `Import-Module .\AzureADSSO.psd1` .

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>2. Adım: sorunsuz SSO 'nun etkinleştirildiği Active Directory ormanların listesini alın

1. PowerShell'i yönetici olarak çalıştırın. PowerShell 'de, çağırın `New-AzureADSSOAuthenticationContext` . İstendiğinde, kiracınızın genel yönetici kimlik bilgilerini girin.
2. Çağrısı yapın `Get-AzureADSSOStatus` . Bu komut, bu özelliğin etkinleştirildiği Active Directory ormanların listesini ("etki alanları" listesine bakın) sağlar.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>3. Adım: özelliği ayarladığınız her Active Directory orman için sorunsuz SSO 'yu devre dışı bırakın

1. Çağrısı yapın `$creds = Get-Credential` . İstendiğinde, hedeflenen Active Directory ormanın etki alanı yönetici kimlik bilgilerini girin.

   > [!NOTE]
   >Etki alanı yöneticisi kimlik bilgileri Kullanıcı adı, SAM hesap adı biçiminde girilmelidir (contoso\johntikan veya contoso. com\johntikan). DNS kullanarak etki alanı yöneticisinin etki alanı denetleyicisini bulmak için Kullanıcı adının etki alanı bölümünü kullanıyoruz.

   >[!NOTE]
   >Kullanılan etki alanı yönetici hesabı, protected Users grubunun bir üyesi olmamalıdır. Öyleyse, işlem başarısız olur.

2. Çağrısı yapın `Disable-AzureADSSOForest -OnPremCredentials $creds` . Bu komut, `AZUREADSSOACC` Bu belirli Active Directory ormanın şirket içi etki alanı denetleyicisinden bilgisayar hesabını kaldırır.
3. Özelliği ayarladığınız her Active Directory ormanı için önceki adımları tekrarlayın.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>4. Adım: her Active Directory orman için sorunsuz SSO 'yu etkinleştirme

1. Çağrısı yapın `Enable-AzureADSSOForest` . İstendiğinde, hedeflenen Active Directory ormanın etki alanı yönetici kimlik bilgilerini girin.

   > [!NOTE]
   >Etki alanı yöneticisi kimlik bilgileri Kullanıcı adı, SAM hesap adı biçiminde girilmelidir (contoso\johntikan veya contoso. com\johntikan). DNS kullanarak etki alanı yöneticisinin etki alanı denetleyicisini bulmak için Kullanıcı adının etki alanı bölümünü kullanıyoruz.

   >[!NOTE]
   >Kullanılan etki alanı yönetici hesabı, protected Users grubunun bir üyesi olmamalıdır. Öyleyse, işlem başarısız olur.

2. Özelliği ayarlamak istediğiniz her Active Directory ormanı için önceki adımı yineleyin.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>5. Adım. Kiracınızda özelliği etkinleştirin

Kiracınızdaki özelliği açmak için çağırın `Enable-AzureADSSO -Enable $true` .
