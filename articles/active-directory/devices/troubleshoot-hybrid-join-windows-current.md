---
title: Hibrit Azure Active Directory'ye katılmış cihazlarla ilgili sorunları giderme
description: Karma Azure Active Directory katılmış Windows 10 ve Windows Server 2016 cihazlarında sorun giderme.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 63415037b9f91936e04fa715405bfb86bf022cdd
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551773"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>Hibrit Azure Active Directory'ye katılmış cihazlarla ilgili sorunları giderme

Bu makalenin içeriği, Windows 10 veya Windows Server 2016 çalıştıran cihazlar için geçerlidir.

Diğer Windows istemcileri için bkz. [karma Azure Active Directory birleştirilmiş alt düzey cihazlarda sorun giderme](troubleshoot-hybrid-join-windows-legacy.md)makalesi.

Bu makalede, [karma Azure Active Directory katılmış cihazları](hybrid-azuread-join-plan.md) aşağıdaki senaryoları destekleyecek şekilde yapılandırdığınız varsayılır:

- Cihaz tabanlı Koşullu Erişim
- [Ayarların kurumsal dolaşımı](./enterprise-state-roaming-overview.md)
- [İş İçin Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification)

Bu belge olası sorunları çözmek için sorun giderme kılavuzu sağlar.

Windows 10 ve Windows Server 2016 için hibrit Azure Active Directory JOIN, Windows 10 Kasım 2015 güncelleştirmesini ve üstünü destekler.

## <a name="troubleshoot-join-failures"></a>JOIN hatalarıyla ilgili sorunları giderme

### <a name="step-1-retrieve-the-join-status"></a>1. Adım: JOIN durumunu alma

**JOIN durumunu almak için:**

1. Yönetici olarak bir komut istemi açın
2. `dsregcmd /status` yazın

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-xxxx-xxxx-xxxx-2d7cd011xxxx
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```

### <a name="step-2-evaluate-the-join-status"></a>2. Adım: JOIN durumunu değerlendirme

Aşağıdaki alanları gözden geçirin ve beklenen değerlere sahip olduklarından emin olun:

#### <a name="domainjoined--yes"></a>Domainkatılmış: Evet

Bu alan, cihazın şirket içi Active Directory katılıp katılmadığını gösterir. Değer **Hayır** ise, cihaz karma Azure AD katılımı gerçekleştiremez.

#### <a name="workplacejoined--no"></a>Iş Placekatılacak: Hayır

Bu alan, cihazın Azure AD 'ye kişisel bir cihaz olarak kaydedilip kaydedilmediğini belirtir ( *çalışma alanına katılmış* olarak işaretlenir). Bu değer, aynı zamanda karma Azure AD 'ye katılmış bir etki alanına katılmış **bilgisayar için olmamalıdır** . Değer **Evet** ise, hibrit Azure AD katılımı tamamlanmadan önce bir iş veya okul hesabı eklenmiştir. Bu durumda, Windows 10 sürüm 1607 veya sonraki bir sürümü kullanılırken hesap yok sayılır.

#### <a name="azureadjoined--yes"></a>Azureadkatıldı: Evet

Bu alan, cihazın katılıp katılmadığını gösterir. Cihaz bir Azure AD 'ye katılmış cihaz ya da karma Azure AD 'ye katılmış bir cihaz ise değer **Evet** olur.
Değer **Hayır** Ise Azure AD 'ye ekleme henüz tamamlanmamıştır.

Daha fazla sorun giderme için sonraki adımlara geçin.

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>3. Adım: birleştirmenin başarısız olduğu aşamayı ve hata kodu bulma işlemini bulun

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 ve üzeri

JOIN durum çıktısının ' Tanılama verileri ' bölümünde ' önceki kayıt ' alt bölümüne bakın. Bu bölüm yalnızca cihaz etki alanına katılmış ise ve Azure AD JOIN 'i karma olarak alıyorsa görüntülenir.
' Hata aşaması ' alanı, ' Istemci ErrorCode ', JOIN işleminin hata kodunu işaret ederken, JOIN hatasının aşamasını gösterir.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>Eski Windows 10 sürümleri

Ekleme hatalarıyla ilgili aşamayı ve hata kodunu bulmak için Olay Görüntüleyicisi günlüklerini kullanın.

1. Olay Görüntüleyicisi 'nde **Kullanıcı cihaz kaydı** olay günlüklerini açın. **Uygulamalar ve hizmetler günlüğü**  >  **Microsoft**  >  **Windows**  >  **Kullanıcı cihaz kaydı** altında bulunur
2. Aşağıdaki EventIDs 304, 305, 307 olan olayları arayın.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/1.png" alt-text="Olay Görüntüleyicisi 'nin ekran görüntüsü. I D 304 içeren bir olay seçilidir ve bilgileri, hata kodu ve aşama vurgulanmış olarak görüntülenir." border="false":::

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/2.png" alt-text="Olay Görüntüleyicisi 'nin ekran görüntüsü. I D 305 ile bir olay görünür ve bu, bilgi, hata kodu vurgulanmış şekilde görüntülenir." border="false":::

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>4. Adım: Aşağıdaki listelerden olası nedenleri ve çözümleri denetleyin

#### <a name="pre-check-phase"></a>Denetim öncesi aşaması

Hatanın olası nedenleri:

- Cihazda etki alanı denetleyicisine bir görüş satırı yok.
   - Cihazın kuruluşun iç ağında veya şirket içi Active Directory (AD) etki alanı denetleyicisine yönelik ağ hattını içeren VPN üzerinde olması gerekir.

#### <a name="discover-phase"></a>Bulma aşaması

Hatanın olası nedenleri:

- Hizmet bağlantı noktası (SCP) nesnesi hatalı yapılandırılmış/DC 'den SCP nesnesi okunamıyor.
   - AD ormanında, cihazın ait olduğu, Azure AD 'de doğrulanmış bir etki alanı adına işaret eden geçerli bir SCP nesnesi gerekir.
   - Ayrıntıları, [hizmet bağlantı noktası yapılandırma](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)bölümünde bulabilirsiniz.
- Bulgu uç noktasından bağlama ve bulma meta verilerini getirme hatası.
   - Cihazın, `https://enterpriseregistration.windows.net` kayıt ve yetkilendirme uç noktalarını bulması IÇIN sistem bağlamına erişebilmesi gerekir.
   - Şirket içi ortamda giden bir ara sunucu gerekiyorsa, BT Yöneticisi, cihazın bilgisayar hesabının giden ara sunucuya keşfedip sessizce kimlik doğrulaması yapabildiğinden emin olmalıdır.
- Kullanıcı bölgesi uç noktasına bağlanılamadı ve bölge bulma işlemi gerçekleştirilemiyor. (Yalnızca Windows 10 sürüm 1809 ve üzeri)
   - Cihaz, `https://login.microsoftonline.com` doğrulanmış etki alanı için bölge bulma işlemini gerçekleştirmek ve etki alanı türünü (yönetilen/Federasyon) belirleyebilmek IÇIN sistem bağlamına erişebilmelidir.
   - Şirket içi ortamda giden bir ara sunucu gerekiyorsa, BT Yöneticisi, cihazdaki SISTEM bağlamının giden ara sunucuya keşfedip sessizce kimlik doğrulaması yapabildiğinden emin olmalıdır.

**Ortak hata kodları:**

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - Neden: SCP nesnesi okunamıyor ve Azure AD kiracı bilgilerini alamıyor.
   - Çözüm: [hizmet bağlantı noktası yapılandırma](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)bölümüne bakın.
- **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607)
   - Neden: genel bulma hatası. DRS 'nin bulma meta verileri alınamadı.
   - Çözüm: daha fazla araştırmak için aşağıdaki alt hatayı bulun.
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT**  (0x801c001f/-2145648609)
   - Neden: bulma Işlemi gerçekleştirilirken Işlem zaman aşımına uğradı.
   - Çözüm: `https://enterpriseregistration.windows.net` sistem bağlamında erişilebilir olduğundan emin olun. Daha fazla bilgi için [ağ bağlantısı gereksinimleri](hybrid-azuread-join-managed-domains.md#prerequisites)bölümüne bakın.
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c0021/-2145648611)
   - Neden: genel bölge bulma hatası. STS 'den etki alanı türü (yönetilen/Federasyon) saptanamadı.
   - Çözüm: daha fazla araştırmak için aşağıdaki alt hatayı bulun.

**Ortak alt hata kodları:**

Bulma hata kodu için alt hata kodunu bulmak için aşağıdaki yöntemlerden birini kullanın.

##### <a name="windows-10-1803-and-above"></a>Windows 10 1803 ve üzeri

JOIN durum çıktısının ' Tanılama verileri ' bölümünde ' DRS bulma testi ' ' ni arayın. Bu bölüm yalnızca cihaz etki alanına katılmış ise ve Azure AD JOIN 'i karma olarak alıyorsa görüntülenir.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : UN-ELEVATED User
               Client Time : 2019-06-05 08:25:29.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x80072ee2]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

+----------------------------------------------------------------------+
```

##### <a name="older-windows-10-versions"></a>Eski Windows 10 sürümleri

Ekleme hatalarıyla ilgili aşamayı ve hata kodu hatasını bulmak için Olay Görüntüleyicisi günlüklerini kullanın.

1. Olay Görüntüleyicisi 'nde **Kullanıcı cihaz kaydı** olay günlüklerini açın. **Uygulamalar ve hizmetler günlüğü**  >  **Microsoft**  >  **Windows**  >  **Kullanıcı cihaz kaydı** altında bulunur
2. Aşağıdaki EventIDs 201 'e sahip olayları arayın

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/5.png" alt-text="Olay Görüntüleyicisi 'nin ekran görüntüsü. I D 201 içeren bir olay seçilidir ve bu bilgi, hata kodu vurgulanmış şekilde görüntülenir." border="false":::

###### <a name="network-errors"></a>Ağ hataları

- **WININET_E_CANNOT_CONNECT** (0x80072EFD/-2147012867)
   - Neden: sunucuyla bağlantı kurulamadı
   - Çözüm: gerekli Microsoft kaynaklarıyla ağ bağlantısının olduğundan emin olun. Daha fazla bilgi için bkz. [ağ bağlantısı gereksinimleri](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_TIMEOUT** (0x80072EE2/-2147012894)
   - Neden: genel ağ zaman aşımı.
   - Çözüm: gerekli Microsoft kaynaklarıyla ağ bağlantısının olduğundan emin olun. Daha fazla bilgi için bkz. [ağ bağlantısı gereksinimleri](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_DECODING_FAILED** (0x80072F8F/-2147012721)
   - Neden: ağ yığını sunucudan gelen yanıtın kodunu çözemedi.
   - Çözüm: ağ proxy 'sinin engel olmadığından ve sunucu yanıtını değiştirirken emin olun.

###### <a name="http-errors"></a>HTTP hataları

- **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582)
   - Neden: SCP nesnesi yanlış kiracı KIMLIĞIYLE yapılandırıldı. Veya kiracıda etkin abonelik bulunamadı.
   - Çözüm: SCP nesnesinin doğru Azure AD kiracı KIMLIĞI ve etkin abonelikler ile yapılandırıldığından veya kiracıda mevcut olduğundan emin olun.
- **DSREG_SERVER_BUSY** (0x801c0025/-2145648603)
   - Neden: DRS sunucusundan HTTP 503.
   - Çözüm: sunucu şu anda kullanılamıyor. sunucu yeniden çevrimiçi olduğunda gelecek birleştirme denemeleri büyük olasılıkla başarılı olur.

###### <a name="other-errors"></a>Diğer hatalar

- **E_INVALIDDATA** (0x8007000d/-2147024883)
   - Neden: Sunucu yanıtı JSON 'SI ayrıştırılamadı. Büyük olasılıkla, bir HTML kimlik doğrulama sayfası ile HTTP 200 döndüren proxy nedeniyle.
   - Çözüm: şirket içi ortamda giden bir ara sunucu gerekiyorsa, BT Yöneticisi, cihazdaki SISTEM bağlamının giden ara sunucuya keşfedip sessizce kimlik doğrulaması yapabildiğinden emin olmalıdır.

#### <a name="authentication-phase"></a>Kimlik doğrulama aşaması

Yalnızca Federal etki alanı hesapları için geçerlidir.

Hatanın nedenleri:

- DRS kaynağı için sessizce erişim belirteci alınamadı.
   - Windows 10 cihazları, tümleşik Windows kimlik doğrulamasını etkin bir WS-Trust uç noktasına kullanarak Federasyon hizmetinden kimlik doğrulama belirteci elde eden bir. Ayrıntılar: [Federasyon Hizmeti yapılandırma](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)

**Ortak hata kodları:**

Hata kodu, alt hata kodu, sunucu hata kodu ve sunucu hata iletisini bulmak için Olay Görüntüleyicisi günlükleri kullanın.

1. Olay Görüntüleyicisi 'nde **Kullanıcı cihaz kaydı** olay günlüklerini açın. **Uygulamalar ve hizmetler günlüğü**  >  **Microsoft**  >  **Windows**  >  **Kullanıcı cihaz kaydı** altında bulunur
2. Aşağıdaki EventID 305 'e sahip olayları arayın

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/3.png" alt-text="Olay Görüntüleyicisi 'nin ekran görüntüsü. I D 305 içeren bir olay görünür. Bilgileri, D A L hata kodları ve durumu vurgulanmış şekilde görüntülenir." border="false":::

##### <a name="configuration-errors"></a>Yapılandırma hataları

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057)
   - Neden: kimlik doğrulama protokolü WS-Trust değil.
   - Çözüm: şirket içi kimlik sağlayıcısı WS-Trust desteklemelidir
- **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036)
   - Neden: şirket içi Federasyon Hizmeti bir XML yanıtı döndürmedi.
   - Çözüm: MEX uç noktasının geçerli bir XML döndürürken emin olun. Proxy 'nin müdahale olmadığından ve XML olmayan yanıtları döndürmediğinden emin olun.
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045)
   - Neden: Kullanıcı adı/parola kimlik doğrulaması için uç nokta bulunamadı.
   - Çözüm: şirket içi kimlik sağlayıcısı ayarlarını kontrol edin. WS-Trust uç noktalarının etkinleştirildiğinden ve MEX yanıtının bu doğru uç noktaları içerdiğinden emin olun.

##### <a name="network-errors"></a>Ağ hataları

- **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82ee2/-894947614)
   - Neden: genel ağ zaman aşımı.
   - Çözüm: `https://login.microsoftonline.com` sistem bağlamında erişilebilir olduğundan emin olun. Şirket içi kimlik sağlayıcısına SISTEM bağlamında erişilebildiğinden emin olun. Daha fazla bilgi için bkz. [ağ bağlantısı gereksinimleri](hybrid-azuread-join-managed-domains.md#prerequisites).
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586)
   - Neden: kimlik doğrulama uç noktası ile bağlantı iptal edildi.
   - Çözüm: bir süre sonra yeniden deneyin veya alternatif bir kararlı ağ konumundan katılmayı deneyin.
- **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/-894947441)
   - Neden: daha önce Güvenli Yuva Katmanı (SSL) olarak bilinen Aktarım Katmanı Güvenliği (TLS), sunucu tarafından gönderilen sertifika doğrulanamadı.
   - Çözüm: istemci saati eğriliğini denetleyin. Bir süre sonra yeniden deneyin veya alternatif bir kararlı ağ konumundan katılmayı deneyin.
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/-894947587)
   - Neden: bağlanma girişimi `https://login.microsoftonline.com` başarısız oldu.
   - Çözüm: ağ bağlantısını denetleyin `https://login.microsoftonline.com` .

##### <a name="other-errors"></a>Diğer hatalar

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829)
   - Neden: şirket içi kimlik sağlayıcısından gelen SAML belirteci Azure AD tarafından kabul edilmedi.
   - Çözüm: Federasyon sunucusu ayarlarını kontrol edin. Kimlik doğrulama günlüklerinde sunucu hata kodunu arayın.
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060)
   - Neden: sunucu WS-Trust yanıtı hata özel durumu bildirdi ve onaylama işlemi alamadı
   - Çözüm: Federasyon sunucusu ayarlarını kontrol edin. Kimlik doğrulama günlüklerinde sunucu hata kodunu arayın.
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xcaa90006/-894894074)
   - Neden: belirteç uç noktasından erişim belirteci alınmaya çalışılırken bir hata alındı.
   - Çözüm: ADAL günlüğünde temeldeki hataya bakın.
- **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323)
   - Neden: genel ADAL hatası
   - Çözüm: kimlik doğrulama günlüklerinden alt hata kodunu veya sunucu hata kodunu bulun.

#### <a name="join-phase"></a>JOIN aşaması

Hatanın nedenleri:

Kayıt türünü bulun ve aşağıdaki listeden hata kodunu arayın.

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 ve üzeri

JOIN durum çıktısının ' Tanılama verileri ' bölümünde ' önceki kayıt ' alt bölümüne bakın. Bu bölüm yalnızca cihaz etki alanına katılmış ise ve Azure AD JOIN 'i karma olarak alıyorsa görüntülenir.
' Kayıt türü ' alanı, gerçekleştirilen birleştirmenin türünü gösterir.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>Eski Windows 10 sürümleri

Ekleme hatalarıyla ilgili aşamayı ve hata kodu hatasını bulmak için Olay Görüntüleyicisi günlüklerini kullanın.

1. Olay Görüntüleyicisi 'nde **Kullanıcı cihaz kaydı** olay günlüklerini açın. **Uygulamalar ve hizmetler günlüğü**  >  **Microsoft**  >  **Windows**  >  **Kullanıcı cihaz kaydı** altında bulunur
2. Aşağıdaki EventIDs 204 'e sahip olayları arayın

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/4.png" alt-text="Olay Görüntüleyicisi 'nin ekran görüntüsü. I D 204 ile bir olayla ilgili bilgiler, hata kodu, H t T P durumu ve ileti vurgulanmış olarak görüntülenir." border="false":::

##### <a name="http-errors-returned-from-drs-server"></a>DRS sunucusundan gelen HTTP hataları döndürüldü

- **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630)
   - Neden: hata kodu: "DirectoryError" ile DRS 'den bir hata yanıtı alındı
   - Çözüm: olası nedenler ve çözümler için sunucu hata koduna bakın.
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c0002/-2145648638)
   - Neden: hata kodu: "AuthenticationError" ve Erroralt kod "DeviceNotFound" DEĞIL, DRS 'den bir hata yanıtı alındı.
   - Çözüm: olası nedenler ve çözümler için sunucu hata koduna bakın.
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c0006/-2145648634)
   - Neden: hata kodu: "DirectoryError" ile DRS 'den bir hata yanıtı alındı
   - Çözüm: olası nedenler ve çözümler için sunucu hata koduna bakın.

##### <a name="tpm-errors"></a>TPM hataları

- **NTE_BAD_KEYSET** (0x80090016/-2146893802)
   - Neden: TPM işlemi başarısız oldu veya geçersiz
   - Çözüm: muhtemelen kötü bir Sysprep görüntüsü nedeniyle. Sysprep görüntüsünün oluşturulduğu makinenin Azure AD 'ye katılmış, karma Azure AD 'ye katılmış veya Azure AD 'nin kayıtlı olduğundan emin olun.
- **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641)
   - Neden: Genel TPM hatası.
   - Çözüm: Bu hatayla birlikte cihazlarda TPM 'YI devre dışı bırakın. Windows 10 sürüm 1809 ve üzeri, TPM başarısızlıklarını otomatik olarak algılar ve TPM 'YI kullanmadan karma Azure AD JOIN işlemini tamamlar.
- **TPM_E_NOTFIPS** (0x80280036/-2144862154)
   - Neden: FIPS modundaki TPM şu anda desteklenmiyor.
   - Çözüm: Bu hatayla birlikte cihazlarda TPM 'YI devre dışı bırakın. Windows 1809 TPM başarısızlıklarını otomatik olarak algılar ve karma Azure AD JOIN 'i TPM kullanmadan tamamlar.
- **NTE_AUTHENTICATION_IGNORED** (0x80090031/-2146893775)
   - Neden: TPM kilitlendi.
   - Çözüm: geçici hata. Coolazaltma dönemi için bekleyin. Bir süre sonra birleştirme denemesi başarılı olmalıdır. [TPM temelleri](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering) makalesinde daha fazla bilgi bulabilirsiniz

##### <a name="network-errors"></a>Ağ hataları

- **WININET_E_TIMEOUT** (0x80072EE2/-2147012894)
   - Neden: cihaz DRS 'ye kaydettirilmeye çalışılırken genel ağ zaman aşımı
   - Çözüm: ağ bağlantısını kontrol edin `https://enterpriseregistration.windows.net` .
- **WININET_E_NAME_NOT_RESOLVED** (0x80072EE7/-2147012889)
   - Neden: sunucu adı veya adresi çözümlenemedi.
   - Çözüm: ağ bağlantısını kontrol edin `https://enterpriseregistration.windows.net` . Ana bilgisayar adı için DNS çözümlemenin, n/w ve cihazda doğru olduğundan emin olun.
- **WININET_E_CONNECTION_ABORTED** (0x80072efe/-2147012866)
   - Neden: sunucu bağlantısı anormal olarak sonlandırıldı.
   - Çözüm: bir süre sonra yeniden deneyin veya alternatif bir kararlı ağ konumundan katılmayı deneyin.

##### <a name="other-errors"></a>Diğer hatalar

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - Neden: EventID 220, Kullanıcı cihaz kaydı olay günlüklerinde bulunur. Windows, Active Directory bilgisayar nesnesine erişemiyor. Olaya bir Windows hata kodu dahil edilebilir. ERROR_NO_SUCH_LOGON_SESSION (1312) ve ERROR_NO_SUCH_USER (1317) hata kodları için, bunlar şirket içi AD 'deki çoğaltma sorunlarıyla ilgilidir.
   - Çözüm: AD 'de çoğaltma sorunlarını giderin. Çoğaltma sorunları geçici olabilir ve bir süre sonra değişebilir.

##### <a name="federated-join-server-errors"></a>Federasyon birleşimi sunucu hataları

| Sunucu hata kodu | Sunucu hata iletisi | Olası nedenler | Çözüm |
| --- | --- | --- | --- |
| DirectoryError | İsteğiniz geçici olarak kısıtlandı. Lütfen 300 saniye sonra yeniden deneyin. | Beklenen hata. Büyük olasılıkla birden çok kayıt isteğinin hemen art arda getirilmesi nedeniyle. | Coolafter döneminden sonra katılmayı yeniden dene |

##### <a name="sync-join-server-errors"></a>Eşitleme sunucusu hatalarını Eşitle

| Sunucu hata kodu | Sunucu hata iletisi | Olası nedenler | Çözüm |
| --- | --- | --- | --- |
| DirectoryError | AADSTS90002: kiracı <UUID> bulunamadı. Kiracı için etkin abonelik yoksa bu hata oluşabilir. Abonelik yöneticinizle görüşün. | SCP nesnesindeki kiracı KIMLIĞI yanlış | SCP nesnesinin doğru Azure AD kiracı KIMLIĞI ve etkin abonelikler ile yapılandırıldığından ve kiracıda mevcut olduğundan emin olun. |
| DirectoryError | Belirtilen KIMLIĞE göre cihaz nesnesi bulunamadı. | Eşitleme katılımı için beklenen hata. Cihaz nesnesi AD 'den Azure AD 'ye eşitlenmedi | Azure AD Connect eşitlemenin tamamlanmasını bekleyin ve eşitleme tamamlandıktan sonra bir sonraki JOIN denemesi sorunu çözecektir |
| AuthenticationError | Hedef bilgisayarın SID doğrulaması | Azure AD cihazındaki sertifika, eşitleme birleşimi sırasında Blobun imzalanacak sertifikayla eşleşmiyor. Bu hata genellikle eşitlemenin henüz tamamlanmamış olduğu anlamına gelir. |  Azure AD Connect eşitlemenin tamamlanmasını bekleyin ve eşitleme tamamlandıktan sonra bir sonraki JOIN denemesi sorunu çözecektir |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>5. Adım: günlükleri ve iletişim Microsoft Desteği toplayın

Dosya Auth.zip şuradan indirin [https://github.com/CSS-Identity/DRS/tree/main/Auth](https://github.com/CSS-Identity/DRS/tree/main/Auth)

1. Dosyaları sıkıştırmasını açın ve eklenen dosyaları **start-auth.txt** yeniden adlandırın ve **Start-Auth. cmd** ve **stop-auth. cmd**' ye **stop-auth.txt** .
1. Yükseltilmiş bir komut isteminden **Start-Auth. cmd**' yi çalıştırın.
1. Sorun kullanıcısı ile başka bir oturuma geçiş yapmak için anahtar hesabını kullanın.
1. Sorunu yeniden üretin.
1. İzlemeyi çalıştıran yönetici oturumuna geri dönmek için anahtar hesabını kullanın.
1. Yükseltilmiş bir komut isteminden **stop-Auth. cmd**' yi çalıştırın.
1. ZIP ve klasör **kimlik doğrulama günlüklerini** komut dosyalarının yürütüldüğü klasörden gönderin.

## <a name="troubleshoot-post-join-issues"></a>Ekleme sonrası sorunlar hakkında sorun giderme

### <a name="retrieve-the-join-status"></a>JOIN durumunu al

#### <a name="wamdefaultset-yes-and-azureadprt-yes"></a>WamDefaultSet: YES ve AzureADPrt: YES

Bu alanlar, kullanıcının cihazda oturum açarken Azure AD 'ye başarıyla kimlik doğrulaması yapıp belirtmediğini belirtir.
Değerler **Hayır** ise bunun nedeni şu olabilir:

- Kayıt sırasında cihazla ilişkili TPM 'de hatalı depolama anahtarı (yükseltilmiş çalıştırılırken KeySignTest denetimini denetleyin).
- Alternatif oturum açma KIMLIĞI
- HTTP proxy bulunamadı

## <a name="known-issues"></a>Bilinen sorunlar
- Ayarlar-> hesaplar-Iş veya okul > erişin, karma Azure AD 'ye katılmış cihazlar, Mobil etkin noktalara veya dış WiFi ağlarına bağlıyken, biri Azure AD ve bir şirket içi AD için olmak üzere iki farklı hesap gösterebilir. Bu yalnızca bir kullanıcı arabirimi sorunudur ve işlevselliği etkilemez.

## <a name="next-steps"></a>Sonraki adımlar

[Dsregcmd komutunu kullanarak cihazlarda sorun gidermeye](troubleshoot-device-dsregcmd.md) devam edin

Sorular için bkz. [cihaz YÖNETIMI SSS](faq.yml)
