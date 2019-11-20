---
title: B2B için bir kimlik sağlayıcısıyla doğrudan Federasyonu ayarlama-Azure Active Directory | Microsoft Docs
description: Konukların Azure AD uygulamalarınızda oturum açmasını sağlamak için bir SAML veya WS-beslik kimliği sağlayıcısıyla doğrudan federasyona ayırın
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3aea108ed87debac56b18b5959d492f2bcb291d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853612"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Konuk kullanıcılar için AD FS ve üçüncü taraf sağlayıcılarla doğrudan Federasyon (Önizleme)
|     |
| --- |
| Doğrudan Federasyon, Azure Active Directory genel önizleme özelliğidir. Önizlemeler hakkında daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Bu makalede, B2B işbirliği için başka bir kuruluşla doğrudan Federasyonun nasıl ayarlanacağı açıklanır. Kimlik sağlayıcısı (IDP) SAML 2,0 veya WS-beslenir protokolünü destekleyen herhangi bir kuruluşla doğrudan Federasyon oluşturabilirsiniz.
Bir iş ortağının IDP 'si ile doğrudan Federasyon ayarladığınızda, bu etki alanındaki yeni Konuk kullanıcılar, Azure AD kiracınızda oturum açmak ve sizinle işbirliği yapmaya başlamak için kendi IDP tarafından yönetilen kurumsal hesaplarını kullanabilir. Konuk kullanıcının ayrı bir Azure AD hesabı oluşturmalarına gerek yoktur.
> [!NOTE]
> Doğrudan Federasyon Konuk kullanıcılarının kiracı bağlamını içeren bir bağlantı kullanarak oturum açması gerekir (örneğin, `https://myapps.microsoft.com/?tenantid=<tenant id>` veya `https://portal.azure.com/<tenant id>`doğrulanmış bir etki alanı `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`olması durumunda). Uygulama ve kaynakların doğrudan bağlantıları, kiracı bağlamını dahil ettikleri sürece da çalışır. Doğrudan Federasyon kullanıcıları, kiracı bağlamı olmayan ortak uç noktaları kullanarak oturum açamıyor. Örneğin,, veya `https://myapps.microsoft.com` `https://teams.microsoft.com` kullanarak `https://portal.azure.com`bir hatayla sonuçlanır.
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Bir Konuk kullanıcının kimliği doğrudan Federasyonla mı?
Bir kuruluşla doğrudan Federasyonu ayarladıktan sonra, davet ettiğiniz tüm yeni Konuk kullanıcılardan kimlik doğrulaması doğrudan Federasyon kullanılarak yapılır. Doğrudan Federasyonu ayarlamanın, sizin için bir davet zaten kullanılmış olan Konuk kullanıcılar için kimlik doğrulama yöntemini değiştirmediğini unutmayın. Bazı örnekler şunlardır:
 - Konuk kullanıcılar sizinle davetleri zaten kullanıyor ve daha sonra kuruluşlarıyla doğrudan Federasyon ayarladıysanız, bu Konuk kullanıcılar doğrudan Federasyonu ayarlamadan önce kullandıkları kimlik doğrulama yöntemini kullanmaya devam eder.
 - Bir iş ortağı organizasyonu ile doğrudan Federasyon ayarlayıp Konuk kullanıcıları davet ederseniz ve daha sonra iş ortağı organizasyonu daha sonra Azure AD 'ye geçerse, davetiyeleri zaten kullanan Konuk kullanıcılar doğrudan Federasyonu kullanmaya devam eder. kiracınızdaki Federasyon ilkesi var.
 - Doğrudan Federasyonu bir iş ortağı kuruluşla silerseniz, şu anda doğrudan Federasyon kullanan tüm konuk kullanıcılar oturum açamıyor.

Bu senaryoların hiçbirinde, Konuk Kullanıcı hesabını dizininizden silip yeniden davet ederek bir Konuk kullanıcının kimlik doğrulama yöntemini güncelleştirebilirsiniz.

Doğrudan Federasyon, contoso.com ve fabrikam.com gibi etki alanı ad alanlarına bağlıdır. AD FS veya bir üçüncü taraf IDP ile doğrudan Federasyon yapılandırması kurarken, kuruluşlar bir veya daha fazla etki alanı ad alanını bu IDPs ile ilişkilendirir. 

## <a name="end-user-experience"></a>Son kullanıcı deneyimi 
Doğrudan Federasyon sayesinde, Konuk kullanıcılar kendi kurumsal hesaplarını kullanarak Azure AD kiracınızda oturum açabilirler. Paylaşılan kaynaklara erişirken ve oturum açma isteminde bulunulduklarında, doğrudan Federasyon kullanıcıları IDP 'ye yeniden yönlendirilir. Başarılı oturum açma işleminden sonra, kaynaklara erişmek için Azure AD 'ye döndürülür. Doğrudan federasyon kullanıcılarının yenileme belirteçleri, Azure AD 'de [geçiş yenileme belirteci için varsayılan uzunluk](../develop/active-directory-configurable-token-lifetimes.md#exceptions) olan 12 saat için geçerlidir. Federal IDP SSO etkin ise, Kullanıcı SSO ile karşılaşacaktır ve ilk kimlik doğrulamasından sonra herhangi bir oturum açma istemi görmeyecektir.

## <a name="limitations"></a>Sınırlamalar

### <a name="dns-verified-domains-in-azure-ad"></a>Azure AD 'de DNS tarafından doğrulanan etki alanları
Federasyona eklemek istediğiniz etki alanının Azure AD 'de DNS doğrulanmamış olması gerekir. DNS doğrulanmadığı için, yönetilmeyen (e-posta doğrulandı veya "viral") Azure AD kiracılarıyla doğrudan Federasyon ayarlama iznine sahip olursunuz.
### <a name="authentication-url"></a>Kimlik doğrulama URL 'SI
Doğrudan federasyona yalnızca kimlik doğrulama URL 'sinin hedef etki alanı ile eşleştiği veya kimlik doğrulama URL 'sinin izin verilen kimlik sağlayıcılarından biri olduğu ilkeler için izin verilir (Bu liste değişebilir):
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Örneğin, **fabrikam.com**için doğrudan Federasyon ayarlarken, kimlik doğrulama URL 'si `https://fabrikam.com/adfs` doğrulamayı geçilecektir. Örneğin `https://sts.fabrikam.com/adfs`, aynı etki alanındaki bir konak da geçirilecek. Ancak, kimlik doğrulama URL `https://fabrikamconglomerate.com/adfs` 'si `https://fabrikam.com.uk/adfs` veya aynı etki alanı için geçiş gerçekleştirilmez.

### <a name="signing-certificate-renewal"></a>İmza sertifikası yenileme
Kimlik sağlayıcısı ayarlarında meta veri URL 'sini belirtirseniz, Azure AD, oturum sona erdiğinde imza sertifikasını otomatik olarak yenileyecek. Ancak, sertifika, süre sonu zamanından önce herhangi bir nedenle döndürülürse veya bir meta veri URL 'SI sağlamazsanız, Azure AD onu yenileyemeyecektir. Bu durumda, imzalama sertifikasını el ile güncelleştirmeniz gerekir.

### <a name="limit-on-federation-relationships"></a>Federasyon ilişkilerinde sınırlama
Şu anda en fazla 1.000 Federasyon ilişkisi desteklenir. Bu sınır hem [iç Federasyonları](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) hem de doğrudan Federasyonları içerir.
## <a name="frequently-asked-questions"></a>Sık sorulan sorular
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Yönetilmeyen (e-posta doğrulanmış) kiracının bulunduğu bir etki alanı ile doğrudan federasyonı ayarlayabilir miyim? 
Evet. Etki alanı doğrulanmadıysa ve kiracı bir [yönetici tarafından ele](../users-groups-roles/domains-admin-takeover.md)geçirmemişse, bu etki alanıyla doğrudan Federasyon oluşturabilirsiniz. Yönetilmeyen ya da e-posta doğrulandı, bir Kullanıcı bir B2B davetini önceden kullanıyorsa veya şu anda mevcut olmayan bir etki alanını kullanarak Azure AD 'ye self servis kaydolma gerçekleştirdiğinde oluşturulur. Bu etki alanlarıyla doğrudan Federasyon oluşturabilirsiniz. Doğrudan Federasyonu, Azure portal veya PowerShell aracılığıyla DNS tarafından doğrulanan bir etki alanıyla ayarlamaya çalışırsanız bir hata görürsünüz.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Doğrudan Federasyon ve e-posta tek seferlik geçiş kodu kimlik doğrulaması etkinse, hangi yöntem öncelikli olur?
Bir iş ortağı organizasyonu ile doğrudan Federasyon oluşturulduğunda, bu kuruluştan yeni Konuk kullanıcılar için e-posta tek seferlik geçiş kimlik doğrulamasından göre önceliklidir. Bir Konuk Kullanıcı, doğrudan Federasyonu ayarlamadan önce bir kerelik geçiş kodu kimlik doğrulaması kullanarak bir davet kullandıysanız, tek seferlik geçiş kodu kimlik doğrulamasını kullanmaya devam eder. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>Kısmen eşitlenen bir kiracı nedeniyle doğrudan Federasyon adresi oturum açma sorunları mı var?
Hayır, [e-posta bir kerelik geçiş kodu](one-time-passcode.md) özelliğinin bu senaryoda kullanılması gerekir. "Kısmen eşitlenen bir kiracı", şirket içi kullanıcı kimliklerinin bulutla tam olarak eşitlenmediği bir iş ortağı Azure AD kiracısını ifade eder. Kimliği henüz bulutta mevcut olmayan ancak B2B davetinizi kullanmaya çalışan bir konuk, oturum açamaz. Tek seferlik geçiş kodu özelliği, bu konuğun oturum açmasını sağlar. Doğrudan Federasyon özelliği, konuğun kendi IDP tarafından yönetilen kuruluş hesabına sahip olduğu, ancak kuruluşun hiçbir Azure AD varlığı olmadığı senaryolara yöneliktir.

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>1\. adım: İş ortağı kuruluşun kimlik sağlayıcısını yapılandırma
İlk olarak, iş ortağı kuruluşunuzun kimlik sağlayıcısını gerekli talepler ve bağlı olan taraf güvenleri ile yapılandırması gerekir. 

> [!NOTE]
> Doğrudan Federasyon için bir kimlik sağlayıcısının nasıl yapılandırılacağını göstermek için, örnek olarak Active Directory Federasyon Hizmetleri (AD FS) (AD FS) kullanacağız. Doğrudan Federasyonu hazırlama bölümünde SAML 2,0 veya WS-beslik kimlik sağlayıcısı olarak AD FS yapılandırma örnekleri veren [AD FS doğrudan Federasyonu yapılandırma](direct-federation-adfs.md)makalesine bakın.

### <a name="saml-20-configuration"></a>SAML 2,0 yapılandırması

Azure AD B2B, SAML protokolünü kullanan kimlik sağlayıcılarıyla federasyona eklemek için aşağıda listelenen belirli gereksinimlere sahip olacak şekilde yapılandırılabilir. SAML kimlik sağlayıcınız ile Azure AD arasında güven ayarlama hakkında daha fazla bilgi için bkz. [Çoklu oturum açma IÇIN saml 2,0 kimlik sağlayıcısı (IDP) kullanma](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp).  

> [!NOTE]
> Not doğrudan Federasyonun hedef etki alanının Azure AD 'de DNS doğrulanmamış olması gerekir. Kimlik doğrulama URL 'SI etki alanı, hedef etki alanı ile eşleşmelidir veya izin verilen bir kimlik sağlayıcısının etki alanı olması gerekir. Ayrıntılar için [sınırlamalar](#limitations) bölümüne bakın. 

#### <a name="required-saml-20-attributes-and-claims"></a>Gerekli SAML 2,0 öznitelikleri ve talepleri
Aşağıdaki tablolarda, üçüncü taraf kimlik sağlayıcısında yapılandırılması gereken belirli öznitelikler ve talepler için gereksinimler gösterilmektedir. Doğrudan Federasyonu ayarlamak için, kimlik sağlayıcısından SAML 2,0 yanıtında aşağıdaki öznitelikler alınmalıdır. Bu öznitelikler, çevrimiçi güvenlik belirteci hizmeti XML dosyasına bağlanarak veya el ile girilerek yapılandırılabilir.

IDP 'den SAML 2,0 yanıtı için gerekli öznitelikler:

|Öznitelik  |Değer  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Hedef kitle     |`urn:federation:MicrosoftOnline`         |
|Sertifikayı Veren     |İş ortağı IDP veren URI 'SI, örneğin`http://www.example.com/exk10l6w90DHM0yi...`         |


IDP tarafından verilen SAML 2,0 belirteci için gerekli talepler:

|Öznitelik  |Value  |
|---------|---------|
|NameID biçimi     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|EmailAddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-beslenir yapılandırma 
Azure AD B2B, aşağıda listelenen bazı belirli gereksinimlere sahip WS-Besme protokolünü kullanan kimlik sağlayıcılarıyla federasyona eklemek üzere yapılandırılabilir. Şu anda, iki WS-beslemeli sağlayıcı Azure AD dahil AD FS ve Shibbomath ile uyumluluk için sınanmıştır. Azure AD ile WS-Beslikli bir sağlayıcı arasında bağlı olan taraf güveni oluşturma hakkında daha fazla bilgi için bkz. [Azure AD kimlik sağlayıcısı uyumluluk belgeleri](https://www.microsoft.com/download/details.aspx?id=56843)'nde bulunan "ws PROTOKOLLERINI kullanan STS tümleştirme sayfası".

> [!NOTE]
> Doğrudan Federasyonun hedef etki alanı, Azure AD 'de DNS doğrulanmamış olmalıdır. Kimlik doğrulama URL 'SI etki alanı, hedef etki alanı ya da izin verilen bir kimlik sağlayıcısının etki alanı ile aynı olmalıdır. Ayrıntılar için [sınırlamalar](#limitations) bölümüne bakın. 

#### <a name="required-ws-fed-attributes-and-claims"></a>Gerekli WS-beslenir öznitelikleri ve talepleri

Aşağıdaki tablolarda, üçüncü taraf WS-beslik kimlik sağlayıcısında yapılandırılması gereken belirli öznitelikler ve talepler için gereksinimler gösterilmektedir. Doğrudan Federasyonu ayarlamak için, kimlik sağlayıcısından gelen WS-Besme iletisinde aşağıdaki öznitelikler alınmalıdır. Bu öznitelikler, çevrimiçi güvenlik belirteci hizmeti XML dosyasına bağlanarak veya el ile girilerek yapılandırılabilir.

IDP 'den WS-BESM iletisinde gerekli öznitelikler:
 
|Öznitelik  |Value  |
|---------|---------|
|Passıverequestorendpoint     |`https://login.microsoftonline.com/login.srf`         |
|Hedef kitle     |`urn:federation:MicrosoftOnline`         |
|Sertifikayı Veren     |İş ortağı IDP veren URI 'SI, örneğin`http://www.example.com/exk10l6w90DHM0yi...`         |

IDP tarafından verilen WS-Besme belirteci için gerekli talepler:

|Öznitelik  |Value  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|EmailAddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>2\. adım: Azure AD 'de doğrudan Federasyonu yapılandırma 
Daha sonra, Azure AD 'de adım 1 ' de yapılandırılan kimlik sağlayıcısıyla Federasyonu yapılandıracaksınız. Azure AD portalını veya PowerShell 'i kullanabilirsiniz. Doğrudan Federasyon ilkesinin etkili olabilmesi için bu işlem 5-10 dakika sürebilir. Bu süre boyunca, doğrudan Federasyon etki alanı için bir davet kullanmaya çalışmayın. Aşağıdaki öznitelikler gereklidir:
- İş ortağı IdP'nin sertifikayı veren URI'sı
- İş ortağı IDP pasif kimlik doğrulama uç noktası (yalnızca https desteklenir)
- Sertifika

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Azure AD portalında doğrudan Federasyonu yapılandırmak için

1. [Azure Portal](https://portal.azure.com/) gidin. Sol bölmede **Azure Active Directory**’yi seçin. 
2. **Kuruluş ilişkilerini**seçin.
3. **Kimlik sağlayıcıları**' nı seçin ve ardından **yenı SAML/WS-besıop**' ı seçin.

    ![Yeni SAML veya WS-Besıop ekleme düğmesini gösteren ekran görüntüsü](media/direct-federation/new-saml-wsfed-idp.png)

4. **Yenı SAML/WS-beslik** kimliği sayfasında, **kimlik sağlayıcısı Protokolü**altında **SAML** veya **WS-beslik**' i seçin.

    ![SAML veya WS-Besıop sayfasında ayrıştırma düğmesini gösteren ekran görüntüsü](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. İş ortağı kuruluşunuzun etki alanı adını girin, bu, doğrudan Federasyon için hedef etki alanı adı olacak
6. Meta veri ayrıntılarını doldurmak için bir meta veri dosyası yükleyebilirsiniz. Meta verileri el ile girmeyi seçerseniz, aşağıdaki bilgileri girin:
   - İş ortağı IdP'nin etki alanı adı
   - İş ortağı IdP'nin varlık kimliği
   - İş ortağı IdP'nin pasif istek sahibi uç noktası
   - Sertifika
   > [!NOTE]
   > Meta veri URL 'SI isteğe bağlıdır, ancak bunu kesinlikle öneririz. Meta veri URL 'SI sağlarsanız, Azure AD, süresi sona erdiğinde imza sertifikasını otomatik olarak yenileyebilirler. Sertifika, süre sonu zamanından önce herhangi bir nedenle döndürülürse veya bir meta veri URL 'SI sağlamazsanız, Azure AD bunu yenileyemeyecektir. Bu durumda, imzalama sertifikasını el ile güncelleştirmeniz gerekir.

7. **Kaydet**’i seçin. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Azure AD 'de PowerShell kullanarak doğrudan Federasyonu yapılandırmak için

1. Graph modülü için Azure AD PowerShell 'in en son sürümünü ([Azureadpreview](https://www.powershellgallery.com/packages/AzureADPreview)) yükler. (Ayrıntılı adımlara ihtiyaç duyuyorsanız, Konuk kullanıcı eklemeye yönelik hızlı başlangıç, [en son AzureADPreview modülünü Install](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module)bölümünü içerir.) 
2. Şu komutu çalıştırın: 
   ```powershell
   Connect-AzureAD
   ```
1. Oturum açma isteminde, yönetilen genel yönetici hesabıyla oturum açın. 
2. Federasyon meta verileri dosyasındaki değerleri değiştirerek aşağıdaki komutları çalıştırın. AD FS Server ve okta için, Federasyon dosyası federationmetadata. xml ' dir, örneğin: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`. 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-3-test-direct-federation-in-azure-ad"></a>3\. adım: Azure AD 'de doğrudan Federasyonu test etme
Şimdi yeni bir B2B Konuk kullanıcısını davet ederek doğrudan Federasyon kurulumunuzu test edin. Ayrıntılar için [Azure Portal Azure AD B2B işbirliği kullanıcılarını ekleme](add-users-administrator.md)bölümüne bakın.
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Doğrudan bir Federasyon ilişkisini Nasıl yaparım? mi düzenleyin?

1. [Azure Portal](https://portal.azure.com/) gidin. Sol bölmede **Azure Active Directory**’yi seçin. 
2. **Kuruluş ilişkilerini**seçin.
3. **Kimlik sağlayıcılarını** seçin
4. **SAML/WS-beslik kimlik sağlayıcıları**altında sağlayıcıyı seçin.
5. Kimlik sağlayıcısı ayrıntıları bölmesinde değerleri güncelleştirin.
6. **Kaydet**’i seçin.


## <a name="how-do-i-remove-direct-federation"></a>Doğrudan Federasyonu kaldırmak Nasıl yaparım? mı?
Doğrudan Federasyon kurulumunuzu kaldırabilirsiniz. Bunu yaparsanız, doğrudan davetlerini daha önce kullanan Federasyon Konuk kullanıcıları oturum açamaz. Ancak onları dizinden silerek ve yeniden davet ederek kaynaklarınıza erişim izni verebilirsiniz. Azure AD portalındaki bir kimlik sağlayıcısıyla doğrudan Federasyonu kaldırmak için:

1. [Azure Portal](https://portal.azure.com/) gidin. Sol bölmede **Azure Active Directory**’yi seçin. 
2. **Kuruluş ilişkilerini**seçin.
3. **Kimlik sağlayıcıları**' nı seçin.
4. Kimlik sağlayıcısını seçin ve **Sil**' i seçin. 
5. Silmeyi onaylamak için **Evet** ' i seçin. 

PowerShell kullanarak doğrudan Federasyonu bir kimlik sağlayıcısıyla kaldırmak için:
1. Graph modülü için Azure AD PowerShell 'in en son sürümünü ([Azureadpreview](https://www.powershellgallery.com/packages/AzureADPreview)) yükler.
2. Şu komutu çalıştırın: 
   ```powershell
   Connect-AzureAD
   ```
3. Oturum açma isteminde, yönetilen genel yönetici hesabıyla oturum açın. 
4. Aşağıdaki komutu girin:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```
