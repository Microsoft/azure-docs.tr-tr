---
title: Dsregcmd komutunu kullanarak cihazların sorunlarını giderme-Azure Active Directory
description: Azure AD 'de cihazların durumunu anlamak için dsregcmd 'deki çıktıyı kullanma
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4aa8f9a7c6807a2f9505559ea13fb0b4f410346d
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68987162"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>Dsregcmd komutunu kullanarak cihazların sorunlarını giderme

Dsregcmd/Status yardımcı programı, bir etki alanı kullanıcı hesabı olarak çalıştırılmalıdır.

## <a name="device-state"></a>Cihaz durumu

Bu bölümde cihaz JOIN durumu parametreleri listelenmektedir. Aşağıdaki tabloda, cihazın çeşitli JOIN durumlarında olması için ölçütler listelenmiştir.

| Azureadkatıldı | Enterprisekatılmış | Domainkatılmış | Cihaz durumu |
| ---   | ---   | ---   | ---   |
| EVET | NO | NO | Azure AD'ye Katıldı |
| NO | NO | EVET | Etki Alanına Katıldı |
| EVET | NO | EVET | Karma AD 'ye katılmış |
| NO | EVET | EVET | Şirket içi DRS 'ye katılmış |

> [!NOTE]
> Workplace Join (Azure AD kayıtlı) durumu "Kullanıcı durumu" bölümünde görüntülenir

- **Azureadkatıldı:** CIHAZ Azure AD 'ye KATıLıRSA "Evet" olarak ayarlanır. Aksi takdirde "Hayır".
- **Enterprisekatılmış:** cihaz, ŞIRKET içi DRS 'ye KATıLıRSA "Yes" olarak ayarlanır. Bir cihaz hem Enterprisekatılmış hem de Azureadkatılmış olamaz.
- **Domainkatıldığında:** cihaz bir etki ALANıNA (ad) KATıLıRSA "Evet" olarak ayarlanır.
- **DomainName:** -cihaz bir etki alanına katılmışsa, etki alanının adına ayarlanır.

### <a name="sample-device-state-output"></a>Örnek cihaz durumu çıkışı

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+
             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HYBRIDADFS
+----------------------------------------------------------------------+
```

## <a name="device-details"></a>Cihaz ayrıntıları

Yalnızca cihaz Azure AD 'ye katılmış veya karma Azure AD 'ye katılmış olduğunda (Azure AD kayıtlı değil) görüntülenir. Bu bölüm, bulutta depolanan cihaz tanımlama ayrıntılarını listeler.

- **DeviceID:** -Azure AD kiracısındaki CIHAZıN benzersiz kimliği
- **Parmak izi:** -cihaz sertifikasının parmak izi 
- **Devicecercertificate Ategeçerliliği:** -cihaz sertifikasının geçerliliği
- **Keycontainerıd:** -cihaz sertifikasıyla ilişkili cihaz özel anahtarının containerıd değeri
- **Keyprovider:** -cihaz özel anahtarını depolamak için kullanılan keyprovider (donanım/yazılım).
- **Tpmprotected:** -cihaz özel anahtarı donanım TPM 'de depolanıyorsa, "Evet".

### <a name="sample-device-details-output"></a>Örnek cihaz ayrıntıları çıkışı

```
+----------------------------------------------------------------------+
| Device Details                                                       |
+----------------------------------------------------------------------+

                  DeviceId : e92325d0-xxxx-xxxx-xxxx-94ae875dxxxx
                Thumbprint : D293213EF327483560EED8410CAE36BB67208179
 DeviceCertificateValidity : [ 2019-01-11 21:02:50.000 UTC -- 2029-01-11 21:32:50.000 UTC ]
            KeyContainerId : 13e68a58-xxxx-xxxx-xxxx-a20a2411xxxx
               KeyProvider : Microsoft Software Key Storage Provider
              TpmProtected : NO
+----------------------------------------------------------------------+
```

## <a name="tenant-details"></a>Kiracı ayrıntıları

Yalnızca cihaz Azure AD 'ye katılmış veya karma Azure AD 'ye katılmış olduğunda (Azure AD kayıtlı değil) görüntülenir. Bu bölüm, bir cihaz Azure AD 'ye katıldığında ortak Kiracı ayrıntılarını listeler.

> [!NOTE]
> MDM URL 'Lerini görseniz bile, cihazın bir MDM tarafından yönetildiği anlamına gelir. Kiracı, cihazın kendisi yönetilmese bile otomatik kayıt için MDM yapılandırmasına sahipse bilgiler görüntülenir. 

### <a name="sample-tenant-details-output"></a>Örnek kiracı ayrıntıları çıkışı

```
+----------------------------------------------------------------------+
| Tenant Details                                                       |
+----------------------------------------------------------------------+

                TenantName : HybridADFS
                  TenantId : 96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
                       Idp : login.windows.net
               AuthCodeUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/authorize
            AccessTokenUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token
                    MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
                 MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
          MdmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance
               SettingsUrl : eyJVxxxxIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyxxxx==
            JoinSrvVersion : 1.0
                JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
                 JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
             KeySrvVersion : 1.0
                 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
                  KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
        WebAuthNSrvVersion : 1.0
            WebAuthNSrvUrl : https://enterpriseregistration.windows.net/webauthn/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
             WebAuthNSrvId : urn:ms-drs:enterpriseregistration.windows.net
    DeviceManagementSrvVer : 1.0
    DeviceManagementSrvUrl : https://enterpriseregistration.windows.net/manage/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
     DeviceManagementSrvId : urn:ms-drs:enterpriseregistration.windows.net
+----------------------------------------------------------------------+
```

## <a name="user-state"></a>Kullanıcı durumu

Bu bölümde, şu anda cihazda oturum açmış olan kullanıcı için çeşitli özniteliklerin durumu listelenir.

> [!NOTE]
> Komutun geçerli durum almak için bir kullanıcı bağlamında çalışması gerekir.

- **NgcSet:** -geçerli oturum açmış kullanıcı Için bir Windows Hello anahtarı ayarlandıysa, "Evet" olarak ayarlayın.
- **NgcKeyId:** -geçerli oturum açmış kullanıcı için ayarlanmış bir Windows Hello anahtarı kimliği.
- **Canreset:** -Windows Hello anahtarının Kullanıcı tarafından sıfırlanıp sıfırlanlamayacağını belirtir. 
- **Olası değerler:** -yalnızca geri dönüşlü, geri dönüşlü, geri dönüşlü ya da hata durumunda bilinmeyen. 
- **Çalışma yerleşimi:** -geçerli Ntuser bağlamındaki CIHAZA Azure AD kayıtlı hesapları EKLENDIYSE "Evet" olarak ayarlanır.
- **Wamdefaultset:** -oturum açmış kullanıcı IÇIN bir WAM varsayılan webaccount OLUŞTURULDUYSA "Yes" olarak ayarlayın. Bu alan, dsreg/Status yönetici bağlamında çalıştırıldığında bir hata görüntüleyebilir. 
- **Wamdefaultauthority:** -Azure AD için "kuruluşlar" olarak ayarlanır.
- Azure AD için **wamdefaultıd:** -Always "https://login.microsoft.com".
- **Wamdefaultguid:** -varsayılan WAM webaccount için WAM sağlayıcısının (Azure AD/Microsoft hesabı) GUID 'i. 

### <a name="sample-user-state-output"></a>Örnek Kullanıcı durumu çıktısı

```
+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

                    NgcSet : YES
                  NgcKeyId : {FA0DB076-A5D7-4844-82D8-50A2FB42EC7B}
                  CanReset : DestructiveAndNonDestructive
           WorkplaceJoined : NO
             WamDefaultSet : YES
       WamDefaultAuthority : organizations
              WamDefaultId : https://login.microsoft.com
            WamDefaultGUID : { B16898C6-A148-4967-9171-64D755DA8520 } (AzureAd)

+----------------------------------------------------------------------+
```

## <a name="sso-state"></a>SSO durumu

Bu bölüm, Azure AD kayıtlı cihazlar için yoksayılabilir.

> [!NOTE]
> Bu kullanıcının geçerli durumunu almak için komutun bir kullanıcı bağlamında çalıştırılması gerekir.

- **Azureadprt:** cihazda oturum açmış kullanıcı için bir prt varsa, "Evet" olarak ayarlayın.
- **Azureadprtupdatetime:** -prt en son GÜNCELLEŞTIRILDIĞI zaman UTC olarak saat olarak ayarlanır.
- **Azureadprtexpirytime:** -YENILENMEDIĞINDE prt 'nin SÜRESI dolduğunda UTC olarak saat olarak ayarlanır.
- **Azureadprtauthority:** -Azure AD yetkilisi URL 'si
- **Enterpriseprt:** -cihazın ŞIRKET içi ADFS 'den yanıt varsa "Evet" olarak ayarlanır. Hibrit Azure AD 'ye katılmış cihazlarda cihazın hem Azure AD hem de şirket içi AD 'den aynı anda EŞIT olması olabilir. Şirket içi katılmış cihazlarda yalnızca kurumsal bir PRT vardır.
- **Enterpriseprtupdatetime:** -kurumsal prt 'nin son GÜNCELLEŞTIRILDIĞI zaman UTC olarak saat olarak ayarlanır.
- **Enterpriseprtexpirytime:** -YENILENMEDIĞINDE prt 'nin SÜRESI dolduğunda UTC olarak saat olarak ayarlanır.
- **Enterpriseprtauthority:** -ADFS yetkili URL 'si

### <a name="sample-sso-state-output"></a>Örnek SSO durum çıkışı

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2019-01-24 19:15:26.000 UTC
      AzureAdPrtExpiryTime : 2019-02-07 19:15:26.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2019-01-24 19:15:33.000 UTC
   EnterprisePrtExpiryTime : 2019-02-07 19:15:33.000 UTC
    EnterprisePrtAuthority : https://fs.hybridadfs.nttest.microsoft.com:443/adfs

+----------------------------------------------------------------------+
```

## <a name="diagnostic-data"></a>Tanılama verileri

### <a name="pre-join-diagnostics"></a>Ön ekleme tanılaması

Bu bölüm yalnızca cihaz etki alanına katılmış ise ve Azure AD JOIN 'i karma olarak alıyorsa görüntülenir.

Bu bölüm, ekleme hatalarının tanılanmasına yardımcı olmak için çeşitli testler gerçekleştirir. Bu bölüm, önceki (?) ayrıntılarını da içerir. Bu bilgiler hata aşaması, hata kodu, sunucu istek KIMLIĞI, sunucu yanıtı http durumu, sunucu yanıtı hata iletisi içerir.

- **Kullanıcı bağlamı:** -Tanılamanın çalıştırıldığı bağlam. Olası değerler: SISTEM, YÜKSELTILMIŞ Kullanıcı, YÜKSELTILMIŞ Kullanıcı. 

   > [!NOTE]
   > Gerçek JOIN SISTEM bağlamında gerçekleştirildiğinden, SISTEM bağlamında tanılamayı çalıştırmak, gerçek JOIN senaryosuna en yakın. Tanılama 'yı SISTEM bağlamında çalıştırmak için, dsregcmd/Status komutunun yükseltilmiş bir komut isteminden çalıştırılması gerekir.

- **Istemci saati:** -UTC olarak sistem süresi.
- **Ad bağlantısı testi:** -test, etki alanı denetleyicisine bir bağlantı testi gerçekleştirir. Bu testte hata, büyük olasılıkla denetim öncesi aşamasında ekleme hatalarıyla sonuçlanır.
- **Ad yapılandırma testi:** -test, SCP nesnesinin ŞIRKET içi ad ormanında doğru yapılandırılıp yapılandırılmadığını doğrular ve doğrular. Bu testteki hatalar, bulma aşamasında 0x801c001d hata koduyla birlikte hatalara neden olabilir.
- **DRS bulma testi:** -test, bulma meta verileri uç noktasından DRS uç noktalarını alır ve bir Kullanıcı bölgesi isteği gerçekleştirir. Bu testteki hatalar, bulma aşamasında hata oluşmasına neden olabilir.
- **DRS bağlantı testi:** -test, DRS uç noktasına temel bağlantı testi gerçekleştirir.
- **Belirteç alma testi:** -test, Kullanıcı kiracının federe olması halinde bır Azure AD kimlik doğrulama belirteci almaya çalışır. Bu testteki hatalar, kimlik doğrulama aşamasında hata oluşmasına neden olabilir. Kimlik doğrulama başarısız olursa, geri dönüş bir kayıt defteri anahtarı ile açıkça devre dışı bırakılmadığı sürece, geri dönüş olarak denenmeyecektir.
- **Eşitlemeye geri dön:** -kayıt defteri anahtarı, kimlik doğrulama hatalarıyla eşitlemeye izin vermek Için "etkin" olarak ayarlanır. Bu seçenek Windows 10 1803 ve üzeri sürümlerde kullanılabilir.
- **Önceki kayıt:** -önceki ekleme girişiminin gerçekleştiği zaman. Yalnızca başarısız olan ekleme denemeleri günlüğe kaydedilir.
- **Hata aşaması:** -durdurulan birleştirmenin aşaması. Olası değerler, ön denetim, bulma, kimlik doğrulama, JOIN.
- **Istemci ErrorCode:** -döndürülen istemci hata kodu (HRESULT).
- **Sunucu** hata kodu:-sunucuya bir istek gönderildiyse ve sunucu bir hata koduyla geri yanıt verdiğinde sunucu hata kodu. 
- **Sunucu iletisi:** -hata koduyla birlikte sunucu iletisi döndürüldü.
- **Https durumu:** -sunucu tarafından döndürülen http durumu.
- **Istek kimliği:** -istemci RequestId sunucuya gönderildi. Sunucu tarafı günlükleriyle ilişkilendirmek için faydalıdır.

### <a name="sample-pre-join-diagnostics-output"></a>Örnek ön ekleme tanılama çıkışı

Aşağıdaki örnekte, bulma hatasıyla başarısız olan tanılama testi gösterilmektedir.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:25:31.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x801c000c]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:23:30.000 UTC
               Error Phase : discover
          Client ErrorCode : 0x801c0021

+----------------------------------------------------------------------+
```

Aşağıdaki örnek, tanılama testlerinin geçtiğini ancak kayıt denemesinin eşitleme birleşimi için beklenen bir dizin hatası ile başarısız olduğunu gösterir. Azure AD Connect eşitleme işi tamamlandıktan sonra cihaz katılabilecektir.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:16:50.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : PASS
     DRS Connectivity Test : PASS
    Token acquisition Test : PASS
     Fallback to Sync-Join : ENABLED

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

### <a name="post-join-diagnostics"></a>JOIN sonrası tanılama

Bu bölümde, buluta katılmış bir cihazda gerçekleştirilen sağlamlık denetimlerinin çıktısı görüntülenir.

- **Aadrecoveryenabled:** -"Yes" ise, cihazda depolanan anahtarlar kullanılamaz ve cihaz kurtarma için işaretlenir. Sonraki oturum açma, kurtarma akışını tetikler ve cihazı yeniden kaydeder.
- **Keysigntest:** -"başarılı" olduğunda cihaz anahtarları iyi bir durumlardır. KeySignTest başarısız olursa, cihaz genellikle kurtarma için işaretlenir. Sonraki oturum açma, kurtarma akışını tetikler ve cihazı yeniden kaydeder. Hibrit Azure AD 'ye katılmış cihazlar için kurtarma sessiz olur. Azure AD 'ye katılmış veya Azure AD kaydı sırasında, cihazlar, gerekirse cihazı kurtarmak ve yeniden kaydetmek için Kullanıcı kimlik doğrulamasını ister. **KeySignTest için yükseltilmiş ayrıcalıklar gerekir.**

#### <a name="sample-post-join-diagnostics-output"></a>Örnek son ekleme tanılama çıkışı

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>NGC önkoşul denetimi

Bu bölüm, bir NGC anahtarının sağlanması için önkoşul denetimlerini gerçekleştirir. 

> [!NOTE]
> Kullanıcı zaten NGC kimlik bilgilerini başarıyla yapılandırdıysa dsregcmd/Status içinde NGC önkoşul denetimi ayrıntılarını göremeyebilirsiniz.

### <a name="sample-ngc-prerequisite-check-output"></a>Örnek NGC önkoşul denetimi çıkışı

```
+----------------------------------------------------------------------+
| Ngc Prerequisite Check                                               |
+----------------------------------------------------------------------+

            IsDeviceJoined : YES
             IsUserAzureAD : YES
             PolicyEnabled : YES
          PostLogonEnabled : YES
            DeviceEligible : YES
        SessionIsNotRemote : YES
            CertEnrollment : enrollment authority
          AdfsRefreshToken : YES
             AdfsRaIsReady : YES
    LogonCertTemplateReady : YES ( StateReady )
              PreReqResult : WillProvision
+----------------------------------------------------------------------+
```

## <a name="next-steps"></a>Sonraki adımlar

Sorular için bkz. [cihaz YÖNETIMI SSS](faq.md)
