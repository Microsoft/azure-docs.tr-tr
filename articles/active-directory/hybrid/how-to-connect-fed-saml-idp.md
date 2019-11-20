---
title: 'Azure AD Connect: Çoklu oturum açma için SAML 2,0 kimlik sağlayıcısı kullanma-Azure'
description: Bu belgede çoklu oturum açma için SAML 2,0 ile uyumlu bir IDP kullanılması açıklanmaktadır.
services: active-directory
author: billmath
manager: daveba
ms.custom: it-pro
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff4f1b81f476159c13d3725cd3cb1766aec7dc87
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305109"
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>Çoklu oturum açma için SAML 2,0 kimlik sağlayıcısı (IDP) kullanma

Bu belge, tercih edilen güvenlik belirteci hizmeti (STS)/kimlik sağlayıcısı olarak SAML 2,0 uyumlu bir SP-Lite profili tabanlı kimlik sağlayıcısı kullanmayla ilgili bilgiler içerir. Bu senaryo, şirket içinde SAML 2,0 kullanılarak erişilebilen bir kullanıcı dizini ve parola depoluuzda yararlıdır. Bu mevcut kullanıcı dizini, Office 365 ve diğer Azure AD ile güvenli kaynaklarda oturum açmak için kullanılabilir. SAML 2,0 SP-Lite profili, bir oturum açma ve öznitelik değişim çerçevesi sağlamak için yaygın olarak kullanılan Security Assertion Markup Language (SAML) federal kimlik standardını temel alır.

>[!NOTE]
>Azure AD ile kullanım için test edilmiş üçüncü taraf IDPs listesi için bkz. [Azure AD Federasyon uyumluluğu listesi](how-to-connect-fed-compatibility.md)

Microsoft bu oturum açma deneyimini, Office 365 gibi bir Microsoft bulut hizmetinin tümleştirilmesi (doğru şekilde yapılandırılmış SAML 2,0 profil tabanlı IDP) olarak destekler. SAML 2,0 kimlik sağlayıcıları, üçüncü taraf ürünleridir ve bu nedenle Microsoft, bunlarla ilgili dağıtım, yapılandırma ve sorun giderme konusunda en iyi uygulamalar için destek sağlamaz. Düzgün yapılandırıldıktan sonra, SAML 2,0 kimlik sağlayıcısıyla tümleştirme, aşağıda daha ayrıntılı olarak açıklanan Microsoft bağlantı Çözümleyicisi Aracı kullanılarak uygun yapılandırma için test edilebilir. SAML 2,0 SP-Lite profil tabanlı kimlik sağlayıcınız hakkında daha fazla bilgi için, bunu sağlayan kuruluşa sorun.

> [!IMPORTANT]
> Bu oturum açma senaryosunda SAML 2,0 kimlik sağlayıcılarıyla yalnızca sınırlı sayıda istemci mevcuttur, bu şunları içerir:
> 
> - Outlook Web Erişimi ve SharePoint Online gibi Web tabanlı istemciler
> - Temel kimlik doğrulaması kullanan e-posta-zengin istemciler ve IMAP, POP, etkin eşitleme, MAPI gibi desteklenen bir Exchange erişim yöntemi (dağıtılması gerekir), örneğin:
>     - Microsoft Outlook 2010/Outlook 2013/Outlook 2016, Apple iPhone (çeşitli iOS sürümleri)
>     - Çeşitli Google Android cihazları
>     - Windows Phone 7, Windows Phone 7,8 ve Windows Phone 8,0
>     - Windows 8 posta Istemcisi ve Windows 8.1 posta Istemcisi
>     - Windows 10 posta Istemcisi

Diğer tüm istemciler, SAML 2,0 kimlik sağlayıcınızla bu oturum açma senaryosunda kullanılamaz. Örneğin, Lync 2010 masaüstü istemcisi, tek oturum açma için yapılandırılmış SAML 2,0 kimlik sağlayıcınızda hizmette oturum açamaz.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Azure AD SAML 2,0 protokol gereksinimleri
Bu belge, bir veya daha fazla Microsoft bulut hizmetinde (Office 365 gibi) oturum açmayı etkinleştirmek üzere SAML 2,0 kimlik sağlayıcınızın Azure AD ile federasyona eklemek için uygulaması gereken protokol ve ileti biçimlendirmesinde ayrıntılı gereksinimler içerir. Bu senaryoda kullanılan bir Microsoft bulut hizmeti için SAML 2,0 bağlı olan taraf (SP-STS), Azure AD 'dir.

SAML 2,0 kimlik sağlayıcısı çıkış iletilerinizin, mümkün olduğunca belirtilen örnek izlemelere benzer olduğundan emin olmanız önerilir. Ayrıca, mümkün olduğunda sağlanan Azure AD meta verilerinden belirli öznitelik değerlerini kullanın. Çıkış iletilerinizde memnun olduktan sonra, aşağıda açıklandığı gibi Microsoft bağlantı Çözümleyicisi ile test edebilirsiniz.

Azure AD meta verileri bu URL 'den indirilebilir: [https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml](https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml).
Çin 'de, Çin 'e özgü Office 365 örneğini kullanan müşteriler için aşağıdaki Federasyon uç noktası kullanılmalıdır: [https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml).

## <a name="saml-protocol-requirements"></a>SAML protokol gereksinimleri
Bu bölümde, iletilerinizi doğru şekilde biçimlendirmeye yardımcı olmak üzere istek ve yanıt iletisi çiftlerinin nasıl birlikte konululacağı açıklanır.

Azure AD, aşağıda listelenen bazı belirli gereksinimlere sahip SAML 2,0 SP Lite profilini kullanan kimlik sağlayıcılarıyla çalışmak üzere yapılandırılabilir. Örnek SAML isteği ve yanıt iletilerini otomatik ve el ile test etme ile birlikte kullanarak Azure AD ile birlikte çalışabilirlik elde edebilirsiniz.

## <a name="signature-block-requirements"></a>İmza bloğu gereksinimleri
SAML yanıt iletisi içinde Imza düğümü, iletinin kendisi için dijital imza hakkında bilgi içerir. İmza bloğu aşağıdaki gereksinimlere sahiptir:

1. Onaylama düğümünün kendisi imzalanması gerekir
2.  RSA-SHA1 algoritması, DigestMethod olarak kullanılmalıdır. Diğer dijital imza algoritmaları kabul edilmez.
   `<ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  XML belgesini de imzalayabiliriz. 
4.  Dönüştürme algoritmasının aşağıdaki örnekteki değerlerle eşleşmesi gerekir:`<ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  SignatureMethod algoritması aşağıdaki örnekle eşleşmelidir:`<ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Desteklenen bağlamalar
Bağlamalar, gereken aktarımlarla ilgili iletişim parametreleridir. Bağlamalar için aşağıdaki gereksinimler geçerlidir

1. HTTPS, gerekli taşıdır.
2.  Oturum açma sırasında Azure AD, belirteç gönderimi için HTTP POST gerektirir.
3.  Azure AD kimlik sağlayıcısına kimlik doğrulama isteği için HTTP POST kullanır ve kimlik sağlayıcısına oturum kapatma iletisini yeniden yönlendirir.

## <a name="required-attributes"></a>Gerekli öznitelikler
Bu tabloda SAML 2,0 iletisindeki belirli özniteliklerin gereksinimleri gösterilmektedir.
 
|Öznitelik|Açıklama|
| ----- | ----- |
|NameID|Bu assertion 'ın değeri, Azure AD kullanıcısının ImmutableID ile aynı olmalıdır. En fazla 64 alfa sayısal karakter olabilir. HTML olmayan tüm güvenli karakterler kodlanmalıdır, örneğin "+" karakteri ". 2B" olarak gösterilir.|
|Idpemail|Kullanıcı asıl adı (UPN), SAML yanıtında Kullanıcı olarak Azure AD/Office 365 ' de kullanıcının UserPrincipalName (UPN) adlı bir öğe olarak listelenir. UPN, e-posta adresi biçimindedir. Windows Office 365 'deki UPN değeri (Azure Active Directory).|
|Sertifikayı Veren|Kimlik sağlayıcısının URI 'SI olması gerekir. Sertifikayı örnek iletilerden yeniden kullanmayın. Azure AD kiracılarınızda birden çok üst düzey etki alanınız varsa, verenin etki alanı başına yapılandırılan belirtilen URI ayarıyla eşleşmesi gerekir.|

>[!IMPORTANT]
>Azure AD Şu anda SAML 2.0 için şu NameID biçim URI 'sini destekliyor: urn: oassıs: names: TC: SAML: 2.0: NameID-Format: persistent.

## <a name="sample-saml-request-and-response-messages"></a>Örnek SAML isteği ve yanıt iletileri
Oturum açma iletisi değişimi için bir istek ve yanıt iletisi çifti gösterilmiştir.
Aşağıda, Azure AD 'den örnek bir SAML 2,0 kimlik sağlayıcısına gönderilen örnek bir istek iletisi verilmiştir. Örnek SAML 2,0 kimlik sağlayıcısı, SAML-P protokolünü kullanmak üzere yapılandırılmış Active Directory Federasyon Hizmetleri (AD FS) (AD FS). Birlikte çalışabilirlik testi, diğer SAML 2,0 kimlik sağlayıcılarıyla de tamamlanmıştır.

    `<samlp:AuthnRequest xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" IssueInstant="2014-01-30T16:18:35Z" Version="2.0" AssertionConsumerServiceIndex="0" >
    <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
    <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>`

Örnek SAML 2,0 uyumlu kimlik sağlayıcısından Azure AD/Office 365 ' ye gönderilen örnek bir yanıt iletisi aşağıda verilmiştir.

    `<samlp:Response ID="_592c022f-e85e-4d23-b55b-9141c95cd2a5" Version="2.0" IssueInstant="2014-01-31T15:36:31.357Z" Destination="https://login.microsoftonline.com/login.srf" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
    </samlp:Status>
    <Assertion ID="_7e3c1bcd-f180-4f78-83e1-7680920793aa" IssueInstant="2014-01-31T15:36:31.279Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
        <ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1" />
        <ds:Reference URI="#_7e3c1bcd-f180-4f78-83e1-7680920793aa">
          <ds:Transforms>
            <ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature" />
            <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
          </ds:Transforms>
          <ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1" />
          <ds:DigestValue>CBn/5YqbheaJP425c0pHva9PhNY=</ds:DigestValue>
        </ds:Reference>
      </ds:SignedInfo>
      <ds:SignatureValue>TciWMyHW2ZODrh/2xrvp5ggmcHBFEd9vrp6DYXp+hZWJzmXMmzwmwS8KNRJKy8H7XqBsdELA1Msqi8I3TmWdnoIRfM/ZAyUppo8suMu6Zw+boE32hoQRnX9EWN/f0vH6zA/YKTzrjca6JQ8gAV1ErwvRWDpyMcwdYCiWALv9ScbkAcebOE1s1JctZ5RBXggdZWrYi72X+I4i6WgyZcIGai/rZ4v2otoWAEHS0y1yh1qT7NDPpl/McDaTGkNU6C+8VfjD78DrUXEcAfKvPgKlKrOMZnD1lCGsViimGY+LSuIdY45MLmyaa5UT4KWph6dA==</ds:SignatureValue>
      <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
        <ds:X509Data>
          <ds:X509Certificate>MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyhBREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDTE1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9ybWVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/+3ZWxd9T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEMb2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcCAwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvyJOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBySx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==</ds:X509Certificate>
        </ds:X509Data>
      </KeyInfo>
    </ds:Signature>
    <Subject>
      <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">ABCDEG1234567890</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" NotOnOrAfter="2014-01-31T15:41:31.357Z" Recipient="https://login.microsoftonline.com/login.srf" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2014-01-31T15:36:31.263Z" NotOnOrAfter="2014-01-31T16:36:31.263Z">
      <AudienceRestriction>
        <Audience>urn:federation:MicrosoftOnline</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="IDPEmail">
        <AttributeValue>administrator@contoso.com</AttributeValue>
      </Attribute>
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2014-01-31T15:36:30.200Z" SessionIndex="_7e3c1bcd-f180-4f78-83e1-7680920793aa">
      <AuthnContext>
        <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
    </Assertion>
    </samlp:Response>`

## <a name="configure-your-saml-20-compliant-identity-provider"></a>SAML 2,0 uyumlu kimlik sağlayıcınızı yapılandırma
Bu bölümde SAML 2,0 kimlik sağlayıcınızı, SAML 2,0 protokolünü kullanarak bir veya daha fazla Microsoft bulut hizmetine (Office 365 gibi) çoklu oturum açma erişimi sağlamak için Azure AD ile federasyona eklemek üzere yapılandırma yönergeleri yer almaktadır. Bu senaryoda kullanılan bir Microsoft bulut hizmeti için SAML 2,0 bağlı olan taraf Azure AD 'dir.

## <a name="add-azure-ad-metadata"></a>Azure AD meta verileri ekleme
SAML 2,0 kimlik sağlayıcınız, Azure AD bağlı olan taraf hakkındaki bilgilere bağlı olmalıdır. Azure AD, konumunda https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml meta verileri yayımlar.

SAML 2,0 kimlik sağlayıcınızı yapılandırırken her zaman en son Azure AD meta verilerini içeri aktarmanız önerilir.

>[!NOTE]
>Azure AD, kimlik sağlayıcısından meta verileri okumaz.

## <a name="add-azure-ad-as-a-relying-party"></a>Azure AD 'yi bağlı olan taraf olarak ekleme
SAML 2,0 kimlik sağlayıcınız ile Azure AD arasındaki iletişimi etkinleştirmeniz gerekir. Bu yapılandırma, belirli kimlik sağlayıcınıza bağlı olacak ve bunun belgelerine başvurmanız gerekir. Genellikle bağlı olan taraf KIMLIĞINI Azure AD meta verilerindeki EntityId ile aynı olarak ayarlarsınız.

>[!NOTE]
>SAML 2,0 kimlik sağlayıcı sunucunuzdaki saatin doğru bir zaman kaynağıyla eşitlendiğini doğrulayın. Yanlış saat saat, Federasyon oturumlarının başarısız olmasına neden olabilir.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>SAML 2,0 kimlik sağlayıcısı ile oturum açmak için Windows PowerShell 'i yükler
SAML 2,0 kimlik sağlayıcınızı Azure AD oturum açma ile kullanmak üzere yapılandırdıktan sonra, sonraki adım Windows PowerShell için Azure Active Directory modülünü indirip yüklemektir. Yüklendikten sonra, Azure AD etki alanlarınızı Federasyon etki alanları olarak yapılandırmak için bu cmdlet 'leri kullanacaksınız.

Windows PowerShell için Azure Active Directory modülü, Azure AD 'de kuruluş verilerinizi yönetmeye yönelik bir indirmektir. Bu modül, Windows PowerShell 'e bir cmdlet kümesi yükleme; Azure AD 'ye çoklu oturum açma erişimi ayarlamak ve abone olduğunuz tüm bulut hizmetlerini kapatmak için bu cmdlet 'leri çalıştırırsınız. Cmdlet 'leri indirme ve yükleme hakkında yönergeler için bkz.[https://technet.microsoft.com/library/jj151815.aspx](https://technet.microsoft.com/library/jj151815.aspx)

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>SAML kimlik sağlayıcınız ile Azure AD arasında bir güven ayarlayın
Bir Azure AD etki alanında Federasyonu yapılandırmadan önce, özel bir etki alanı yapılandırılmış olmalıdır. Microsoft tarafından sunulan varsayılan etki alanını federasyona yükleyemezsiniz. Microsoft 'un varsayılan etki alanı "onmicrosoft.com" ile biter.
Etki alanlarını çoklu oturum açma için eklemek veya dönüştürmek üzere Windows PowerShell komut satırı arabiriminde bir dizi cmdlet 'i çalıştıracaksınız.

SAML 2,0 kimlik sağlayıcınızı kullanarak federasyona eklemek istediğiniz her bir Azure Active Directory etki alanı, tek bir oturum açma etki alanı olarak eklenmelidir veya standart bir etki alanından çoklu oturum açma etki alanı olacak şekilde dönüştürülemelidir. Bir etki alanını ekleme veya dönüştürme, SAML 2,0 kimlik sağlayıcınız ile Azure AD arasında bir güven oluşturur.

Aşağıdaki yordam, SAML 2,0 SP-Lite kullanarak mevcut bir standart etki alanını Federasyon etki alanına dönüştürmeyi adım adım göstermektedir. 

>[!NOTE]
>Etki alanınız, bu adımı gerçekleştirdikten sonra kullanıcıları 2 saate kadar etkileyen bir kesinti yaşar.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Federasyon için Azure AD dizininizde bir etki alanı yapılandırma


1. Azure AD dizininize kiracı yöneticisi olarak bağlanın: Connect-MsolService.
2.  İstediğiniz Office 365 etki alanınızı SAML 2,0 ile Federasyon kullanacak şekilde yapılandırın:`$dom = "contoso.com" $BrandName - "Sample SAML 2.0 IDP" $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" $MyURI = "urn:uri:MySamlp2IDP" $MySigningCert = @" MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" "@ $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" $Protocol = "SAMLP" Set-MsolDomainAuthentication -DomainName $dom -FederationBrandName $BrandName -Authentication Federated -PassiveLogOnUri $LogOnUrl -ActiveLogOnUri $ecpUrl -SigningCertificate $MySigningCert -IssuerUri $MyURI -LogOffUri $LogOffUrl -PreferredAuthenticationProtocol $Protocol` 

3.  IDP meta veri dosyanızdaki imza sertifikası Base64 kodlamalı dizeyi elde edebilirsiniz. Bu konuma bir örnek sağlanmış ancak uygulamanıza göre biraz farklı olabilir.

    `<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"> <KeyDescriptor use="signing"> <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#"> <X509Data> <X509Certificate>MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate> </X509Data> </KeyInfo> </KeyDescriptor>` 

"Set-MsolDomainAuthentication" hakkında daha fazla bilgi için bkz. [https://technet.microsoft.com/library/dn194112.aspx](https://technet.microsoft.com/library/dn194112.aspx):.

>[!NOTE]
>Yalnızca kimlik sağlayıcınız için `$ecpUrl = "https://WS2012R2-0.contoso.com/PAOS"` bir ECP uzantısı ayarlarsanız Use öğesini çalıştırmanız gerekir. Outlook Web uygulaması (OWA) dışında Exchange Online istemcileri, bir POST tabanlı etkin uç noktası kullanır. SAML 2,0 STS 'niz, Shibboseth 'ın etkin bir uç noktasının ECP uygulamasına benzer bir etkin uç noktası uygularsa, bu zengin istemcilerin Exchange Online hizmetiyle etkileşime geçmesini mümkün olabilir.

Federasyon yapılandırıldıktan sonra, "federe olmayan" (veya "yönetilen") durumuna geçebilirsiniz, ancak bu değişikliğin tamamlanabilmesi iki saate kadar sürer ve her kullanıcı için bulut tabanlı oturum açma için yeni rastgele parolaların atanmasını gerektirir. Ayarlarınızdaki bir hatayı sıfırlamak için bazı senaryolarda "Managed" öğesine geri dönmek gerekebilir. Etki alanı dönüştürme hakkında daha fazla bilgi için [https://msdn.microsoft.com/library/windowsazure/dn194122.aspx](https://msdn.microsoft.com/library/windowsazure/dn194122.aspx)bkz.

## <a name="provision-user-principals-to-azure-ad--office-365"></a>Azure AD/Office 365 'e Kullanıcı sorumluları sağlama
Kullanıcılarınızın kimliğini Office 365 ' e doğrulayabilmeniz için önce SAML 2,0 talebinde bulunan onaylama 'ya karşılık gelen Kullanıcı sorumluları ile Azure AD sağlamalısınız. Bu Kullanıcı sorumluları Azure AD tarafından önceden bilinmiyorsa, Federasyon oturum açma için kullanılamaz. Azure AD Connect ya da Windows PowerShell, kullanıcı sorumlularını sağlamak için kullanılabilir.

Azure AD Connect, şirket içi Active Directory Azure AD dizininizde bulunan etki alanlarına sorumluları sağlamak için kullanılabilir. Daha ayrıntılı bilgi için bkz. Şirket [içi dizinlerinizi Azure Active Directory tümleştirme](whatis-hybrid-identity.md).

Windows PowerShell Ayrıca, Azure AD 'ye yeni kullanıcı ekleme ve şirket içi dizindeki değişiklikleri eşitlemeye otomatik hale getirmek için de kullanılabilir. Windows PowerShell cmdlet 'lerini kullanmak için [Azure Active Directory modüllerini](https://docs.microsoft.com/powershell/azure/install-adv2?view=azureadps-2.0)indirmeniz gerekir.

Bu yordam, Azure AD 'ye tek bir kullanıcının nasıl ekleneceğini gösterir.


1. Azure AD dizininize kiracı yöneticisi olarak bağlanın: Connect-MsolService.
2.  Yeni bir Kullanıcı sorumlusu oluşturun:
    ```powershell
    New-MsolUser
      -UserPrincipalName elwoodf1@contoso.com
      -ImmutableId ABCDEFG1234567890
      -DisplayName "Elwood Folk"
      -FirstName Elwood 
      -LastName Folk 
      -AlternateEmailAddresses "Elwood.Folk@contoso.com" 
      -LicenseAssignment "samlp2test:ENTERPRISEPACK" 
      -UsageLocation "US" 
    ```

"New-MsolUser" kullanıma alma hakkında daha fazla bilgi için[https://technet.microsoft.com/library/dn194096.aspx](https://technet.microsoft.com/library/dn194096.aspx)

>[!NOTE]
>"Userprenlname" değeri, SAML 2,0 talepinizdeki "ıdpemail" için göndereceğiniz değerle eşleşmelidir ve "ImmutableID" değeri "NameID" onayyıklarınızın gönderdiği değerle eşleşmelidir.

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>SAML 2,0 ıDP ile çoklu oturum açmayı doğrulama
Yönetici olarak, tekli oturum açmayı doğruladıktan ve yönettiğinizde (Kimlik Federasyonu olarak da bilinir), SAML 2,0 SP-Lite tabanlı kimlik sağlayıcınızda çoklu oturum açmayı ayarlamak için bilgileri gözden geçirin ve aşağıdaki makalelerdeki adımları gerçekleştirin:


1.  Azure AD SAML 2,0 protokol gereksinimlerini incelendi
2.  SAML 2,0 kimlik sağlayıcınızı yapılandırdınız
3.  SAML 2,0 kimlik sağlayıcısı ile çoklu oturum açma için Windows PowerShell 'i yükler
4.  SAML 2,0 kimlik sağlayıcısı ile Azure AD arasında güven ayarlama
5.  Windows PowerShell ya da Azure AD Connect aracılığıyla Azure Active Directory (Office 365) bilinen bir test kullanıcısı sorumlusu sağlandı.
6.  [Azure AD Connect](whatis-hybrid-identity.md)kullanarak dizin eşitlemeyi yapılandırın.

SAML 2,0 SP Lite tabanlı kimlik sağlayıcınızda çoklu oturum açmayı ayarladıktan sonra, doğru çalıştığını doğrulamanız gerekir.

>[!NOTE]
>Bir etki alanını, bir tane eklemek yerine dönüştürdüyseniz, çoklu oturum açmayı ayarlamak 24 saate kadar sürebilir.
Çoklu oturum açmayı doğruladıktan önce, Active Directory eşitlemeyi ayarlamayı, dizinlerinizi eşitlemeniz ve eşitlenmiş kullanıcılarınızı etkinleştirmeniz gerekir.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Çoklu oturum açma 'nın doğru şekilde ayarlandığını doğrulamak için aracını kullanın
Çoklu oturum açma 'nın doğru şekilde ayarlandığını doğrulamak için, bulut hizmetinde kurumsal kimlik bilgilerinizle oturum açabildiğinizi doğrulamak üzere aşağıdaki yordamı gerçekleştirebilirsiniz.

Microsoft, SAML 2,0 tabanlı kimlik sağlayıcınızı test etmek için kullanabileceğiniz bir araç sağladı. Test aracını çalıştırmadan önce, kimlik sağlayıcınızla federasyona eklemek için bir Azure AD kiracısı yapılandırmış olmanız gerekir.

>[!NOTE]
>Bağlantı çözümleyici, Internet Explorer 10 veya üstünü gerektirir.



1. Bağlantı çözümleyici 'yi [https://testconnectivity.microsoft.com/?tabid=Client](https://testconnectivity.microsoft.com/?tabid=Client)konumundan indirin.
2.  Aracı indirmeye ve yüklemeye başlamak için şimdi yükleme ' ye tıklayın.
3.  "Office 365, Azure veya Azure Active Directory kullanan diğer hizmetlerle Federasyonu ayarlayamıyorum" seçeneğini belirleyin.
4.  Araç indirilip çalışmaya başladıktan sonra bağlantı tanılama penceresini görürsünüz. Araç, Federasyon bağlantınızı test etmek için size adım adım kılavuzluk eder.
5.  Bağlantı çözümleyici, oturum açmak için SAML 2,0 ıDP 'nizi açar, test ettiğiniz Kullanıcı sorumlusuna ait kimlik bilgilerini girin: ![SAML](./media/how-to-connect-fed-saml-idp/saml1.png)
6.  Federasyon testi oturum açma penceresinde, SAML 2,0 kimlik sağlayıcınızla federe olarak yapılandırılmış Azure AD kiracısı için bir hesap adı ve parola girmeniz gerekir. Araç bu kimlik bilgilerini kullanarak oturum açmaya çalışır ve oturum açma girişimi sırasında gerçekleştirilen testlerin ayrıntılı sonuçları çıktı olarak sağlanacaktır.
![SAML](./media/how-to-connect-fed-saml-idp/saml2.png)
7. Bu pencere testin başarısız sonucunu gösterir. Ayrıntılı sonuçları gözden geçir ' i tıklatmak, gerçekleştirilen her test için sonuçlar hakkındaki bilgileri gösterir. Ayrıca, sonuçları paylaşmak için diske kaydedebilirsiniz.
 
>[!NOTE]
>Bağlantı çözümleyici Ayrıca, WS * tabanlı ve ECP/PAOS protokollerini kullanarak etkin Federasyonu sınar. Bunları kullanmıyorsanız, aşağıdaki hatayı yoksayabilirsiniz: Kimlik sağlayıcınızın etkin federasyon uç noktasını kullanarak etkin oturum açma akışını test etme.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Çoklu oturum açma 'nın doğru şekilde ayarlandığını el ile doğrulama
El ile doğrulama, SAML 2,0 kimlik sağlayıcınızın birçok senaryoda düzgün çalıştığından emin olmak için uygulayabileceğiniz ek adımlar sağlar.
Çoklu oturum açma işleminin doğru şekilde ayarlandığını doğrulamak için aşağıdaki adımları izleyin:


1. Etki alanına katılmış bir bilgisayarda, kurumsal kimlik bilgileriniz için kullandığınız oturum açma adını kullanarak bulut hizmetinize oturum açın.
2.  Parola kutusunun içine tıklayın. Çoklu oturum açma ayarlandıysa, parola kutusu gölgeli olur ve aşağıdaki iletiyi görürsünüz: "Şimdi şirketinizde &lt;&gt;oturum açmanız gerekiyor."
3.  &lt;Şirket&gt; bağlantılarınızın oturum açma bağlantısına tıklayın. Oturum açabiliyor olmanız halinde çoklu oturum açma ayarlanır.

## <a name="next-steps"></a>Sonraki Adımlar


- [Azure AD Connect ile yönetimi ve özelleştirmeyi Active Directory Federasyon Hizmetleri (AD FS)](how-to-connect-fed-management.md)
- [Azure AD federasyonu uyumluluk listesi](how-to-connect-fed-compatibility.md)
- [Özel Yükleme Azure AD Connect](how-to-connect-install-custom.md)
