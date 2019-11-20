---
title: Azure Active Directory 'de SAML belirteci şifrelemesi
description: Azure Active Directory SAML belirteci şifrelemesini yapılandırmayı öğrenin.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: mimart
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: eafd209073b36265d24dbad4a66b3870d8f593db
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73148630"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption-preview"></a>Nasıl yapılır: Azure AD SAML belirteci şifrelemesini yapılandırma (Önizleme)

> [!NOTE]
> Belirteç şifreleme bir Azure Active Directory (Azure AD) Premium özelliğidir. Azure AD sürümleri, Özellikler ve fiyatlandırma hakkında daha fazla bilgi edinmek için bkz. [Azure AD fiyatlandırması](https://azure.microsoft.com/pricing/details/active-directory/).

SAML belirteci şifrelemesi, şifrelenmiş SAML onaylamaları tarafından desteklenen bir uygulamayla kullanılmasına izin vermez. Bir uygulama için yapılandırıldığında Azure AD, Azure AD 'de depolanan bir sertifikadan alınan ortak anahtarı kullanarak bu uygulama için yaydığı SAML onaylamalarını şifreler. Uygulamanın, oturum açan kullanıcı için kimlik doğrulama kanıtı olarak kullanılmadan önce belirtecin şifresini çözmek için eşleşen özel anahtarı kullanması gerekir.

Azure AD ile uygulama arasındaki SAML onaylamalarını şifrelemek, belirtecin içeriğinin yakalanmadığı ve kişisel veya kurumsal verilerin tehlikeye girdiği ek güvence sağlar.

Belirteç şifrelemesi olmadan bile, Azure AD SAML belirteçleri hiçbir şekilde ağda hiçbir şekilde hiçbir şekilde geçirilmez. Azure AD,, ıDP, tarayıcı ve uygulama arasındaki iletişimin şifreli bağlantıları yerine gelmesi için, belirteç isteği/yanıt değişimlerinin şifreli HTTPS/TLS kanalları üzerinde gerçekleşmesini gerektirir. Ek sertifikaları yönetme yüküyle karşılaştırıldığında, durumunuz için belirteç şifreleme değerini göz önünde bulundurun.   

Belirteç şifrelemesini yapılandırmak için, uygulamayı temsil eden Azure AD Uygulama nesnesine ortak anahtarı içeren bir X. 509.440 sertifika dosyası yüklemeniz gerekir. X. 509.952 sertifikasını almak için, uygulamayı uygulamanın kendisinden indirebilir veya uygulama satıcısının şifreleme anahtarları sağladığı veya uygulamanın özel bir anahtar sağlamanızı beklediği durumlarda uygulama satıcısından elde edebilirsiniz. şifreleme araçları kullanılarak oluşturulan özel anahtar bölümü, uygulamanın anahtar deposuna ve eşleşen ortak anahtar sertifikasına Azure AD 'ye yüklenir.

Azure AD, SAML onaylama verilerini şifrelemek için AES-256 kullanır.

## <a name="configure-saml-token-encryption"></a>SAML belirteci şifrelemesini yapılandırma

SAML belirteci şifrelemesini yapılandırmak için aşağıdaki adımları izleyin:

1. Uygulamada yapılandırılmış özel anahtarla eşleşen bir ortak anahtar sertifikası alın.

    Şifreleme için kullanılacak asimetrik bir anahtar çifti oluşturun. Ya da uygulama şifreleme için kullanmak üzere bir ortak anahtar sağlarsa, X. 509.440 sertifikasını indirmek için uygulamanın yönergelerini izleyin.

    Ortak anahtar,. cer biçimindeki bir X. 509.440 sertifika dosyasında depolanmalıdır.

    Uygulama örneğiniz için oluşturduğunuz bir anahtarı kullanıyorsa, uygulamanın Azure AD kiracınızdaki belirteçlerin şifresini çözmek için kullanacağı özel anahtarı yüklemek üzere uygulamanız tarafından belirtilen yönergeleri izleyin.

1. Sertifikayı Azure AD 'de uygulama yapılandırmasına ekleyin.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>Azure portal belirteç şifrelemesini yapılandırmak için

Ortak sertifikayı, Azure portal içindeki uygulama yapılandırmanıza ekleyebilirsiniz.

1. [Azure Portal](https://portal.azure.com) gidin.

1. **Azure Active Directory > Enterprise Applications** dikey penceresine gidin ve belirteç şifrelemesini yapılandırmak istediğiniz uygulamayı seçin.

1. Uygulamanın sayfasında, **belirteç şifrelemesi**' ni seçin.

    ![Azure portal belirteç şifreleme seçeneği](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > **Belirteç şifreleme** seçeneği yalnızca, uygulama galerisinden veya Galeri dışı bir uygulamadan Azure Portal **Kurumsal uygulamalar** dikey penceresinden ayarlanmış SAML uygulamalarında kullanılabilir. Diğer uygulamalar için bu menü seçeneği devre dışıdır. Azure portal **uygulama kayıtları** deneyimi aracılığıyla kaydedilen uygulamalarda, uygulama bildirimini kullanarak, Microsoft Graph veya POWERSHELL aracılığıyla SAML belirteçleri için şifrelemeyi yapılandırabilirsiniz.

1. **Belirteç şifreleme** sayfasında, ortak X. 509.440 sertifikanızı içeren. cer dosyasını içeri aktarmak Için **sertifikayı içeri aktar** ' ı seçin.

    ![X. 509.440 sertifikasını içeren. cer dosyasını içeri aktarma](./media/howto-saml-token-encryption/import-certificate-small.png)

1. Sertifika içeri aktarıldıktan sonra ve özel anahtar uygulama tarafında kullanılmak üzere yapılandırıldıktan sonra, parmak izi durumunun yanındaki **...** ' yi seçerek şifrelemeyi etkinleştirin ve sonra, şu seçenekler arasından **belirteç şifrelemesini etkinleştir** ' i seçin. açılan menü.

1. Belirteç şifreleme sertifikasının etkinleştirilmesini onaylamak için **Evet** ' i seçin.

1. Uygulama için uygulanan SAML onaylarınızın şifrelendiğinden emin olun.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Azure portal belirteç şifrelemesini devre dışı bırakmak için

1. Azure portal **Azure Active Directory Kurumsal uygulamalar >** ' e gıdın ve SAML belirteci Şifrelemesi etkin olan uygulamayı seçin.

1. Uygulamanın sayfasında, **belirteç şifrelemesi**' ni seçin, sertifikayı bulun ve ardından açılan menüyü göstermek için **...** seçeneğini belirleyin.

1. **Belirteç şifrelemesini devre dışı bırak**' ı seçin.

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>Graph API, PowerShell veya uygulama bildirimi kullanarak SAML belirteci şifrelemesini yapılandırma

Şifreleme sertifikaları, Azure AD 'de uygulama nesnesinde `encrypt` kullanım etiketiyle depolanır. Birden çok şifreleme sertifikası yapılandırabilirsiniz ve belirteçleri şifrelemek için etkin olan bir öznitelik `tokenEncryptionKeyID` özniteliği tarafından tanımlanır.

Microsoft Graph API veya PowerShell kullanarak belirteç şifrelemesini yapılandırmak için uygulamanın nesne KIMLIĞINE ihtiyacınız olacaktır. Bu değeri programlı bir şekilde veya Azure portal uygulamanın **Özellikler** sayfasına gıdıp **nesne kimliği** değerini belirterek bulabilirsiniz.

Graph, PowerShell veya uygulama bildiriminde bir keyCredential yapılandırdığınızda, keyId için kullanmak üzere bir GUID oluşturmalısınız.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>Microsoft Graph kullanarak belirteç şifrelemesini yapılandırmak için

1. Şifreleme için uygulamanın `keyCredentials` bir X. 509.440 sertifikasıyla güncelleştirin. Aşağıdaki örnek bunun nasıl yapılacağını göstermektedir.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid>

    { 
       "keyCredentials":[ 
          { 
             "type":"AsymmetricX509Cert","usage":"Encrypt",
             "keyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35",    (Use a GUID generator to obtain a value for the keyId)
             "key": "MIICADCCAW2gAwIBAgIQ5j9/b+n2Q4pDvQUCcy3…"  (Base64Encoded .cer file)
          }
        ]
    }
    ```

1. Belirteçleri şifrelemek için etkin olan şifreleme sertifikasını belirler. Aşağıdaki örnek bunun nasıl yapılacağını göstermektedir.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>PowerShell kullanarak belirteç şifrelemesini yapılandırmak için

Bu işlevsellik yakında sunulacak. 

<!--
1. Use the latest Azure AD PowerShell module to connect to your tenant.

1. Set the token encryption settings using the **[Set-AzureApplication](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview)** command.

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials "<KeyCredentialsObject>"  -TokenEncryptionKeyId <keyID>
    ```

1. Read the token encryption settings using the following commands.

    ```powershell
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```

-->

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>Uygulama bildirimini kullanarak belirteç şifrelemesini yapılandırmak için

1. Azure portal **Azure Active Directory > uygulama kayıtları**sayfasına gidin.

1. Tüm uygulamaları görüntülemek için açılan listeden **tüm uygulamalar** ' ı seçin ve ardından yapılandırmak istediğiniz kurumsal uygulamayı seçin.

1. Uygulamanın sayfasında, [uygulama bildirimini](../develop/reference-app-manifest.md)düzenlemek için **bildirim** ' ı seçin.

1. `tokenEncryptionKeyId` özniteliği için değeri ayarlayın.

    Aşağıdaki örnek, iki şifreleme sertifikası ile yapılandırılmış bir uygulama bildirimini ve Tokenencryptotionkeyıd ' yi kullanarak etkin bir tane olarak seçili olanı gösterir.

    ```json
    { 
      "id": "3cca40e2-367e-45a5-8440-ed94edd6cc35",
      "accessTokenAcceptedVersion": null,
      "allowPublicClient": false,
      "appId": "cb2df8fb-63c4-4c35-bba5-3d659dd81bf1",
      "appRoles": [],
      "oauth2AllowUrlPathMatching": false,
      "createdDateTime": "2017-12-15T02:10:56Z",
      "groupMembershipClaims": "SecurityGroup",
      "informationalUrls": { 
         "termsOfService": null, 
         "support": null, 
         "privacy": null, 
         "marketing": null 
      },
      "identifierUris": [ 
        "https://testapp"
      ],
      "keyCredentials": [ 
        { 
          "customKeyIdentifier": "Tog/O1Hv1LtdsbPU5nPphbMduD=", 
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "8be4cb65-59d9-404a-a6f5-3d3fb4030351", 
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest" 
        }, 
        {
          "customKeyIdentifier": "U5nPphbMduDmr3c9Q3p0msqp6eEI=",
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851",
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest2" 
        } 
      ], 
      "knownClientApplications": [], 
      "logoUrl": null, 
      "logoutUrl": null, 
      "name": "Test SAML Application", 
      "oauth2AllowIdTokenImplicitFlow": true, 
      "oauth2AllowImplicitFlow": false, 
      "oauth2Permissions": [], 
      "oauth2RequirePostResponse": false, 
      "orgRestrictions": [], 
      "parentalControlSettings": { 
         "countriesBlockedForMinors": [], 
         "legalAgeGroupRule": "Allow" 
        }, 
      "passwordCredentials": [], 
      "preAuthorizedApplications": [], 
      "publisherDomain": null, 
      "replyUrlsWithType": [], 
      "requiredResourceAccess": [], 
      "samlMetadataUrl": null, 
      "signInUrl": "https://127.0.0.1:444/applications/default.aspx?metadata=customappsso|ISV9.1|primary|z" 
      "signInAudience": "AzureADMyOrg",
      "tags": [], 
      "tokenEncryptionKeyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851" 
    }  
    ```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD 'nın SAML protokolünü nasıl kullandığını](../develop/active-directory-saml-protocol-reference.md) öğrenin
* [Azure AD 'de SAML belirteçlerinin](../develop/reference-saml-tokens.md) biçimini, güvenlik özelliklerini ve içeriğini öğrenin