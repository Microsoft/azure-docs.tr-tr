---
title: Öğretici-Zscaler ile Azure Active Directory B2C yapılandırma
titleSuffix: Azure AD B2C
description: Azure AD B2C kimlik doğrulamasını Zscaler ile tümleştirmeyi öğrenin.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 9cd193eb6ff2858440f1cd9a62bdd53d58d6047d
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256302"
---
# <a name="tutorial-configure-zscaler-private-access-with-azure-active-directory-b2c"></a>Öğretici: Azure Active Directory B2C ile Zscaler özel erişimini yapılandırma

Bu öğreticide, Azure Active Directory B2C (Azure AD B2C) kimlik doğrulamasını [Zscaler özel erişimi (ZPA)](https://www.zscaler.com/products/zscaler-private-access)ile tümleştirmeyi öğreneceksiniz. ZPA, bir sanal özel ağın (VPN) maliyet, sorun veya güvenlik riskleri olmadan özel uygulamalara ve varlıklara ilke tabanlı, güvenli erişim sağlar. Zscaler güvenli karma erişim teklifi, Azure AD B2C ile birleştirildiğinde tüketiciye yönelik uygulamalar için sıfır saldırı yüzeyi sunar.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce şunları yapmanız gerekir:

- Azure aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.  
- Azure aboneliğinize bağlı [bir Azure AD B2C kiracısı](./tutorial-create-tenant.md) .  
- [ZPA aboneliği](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview).

## <a name="scenario-description"></a>Senaryo açıklaması

ZPA tümleştirmesi aşağıdaki bileşenleri içerir:

- **Azure AD B2C**: kullanıcının kimlik bilgilerini doğrulamaktan sorumlu kimlik sağlayıcısı (IDP). Yeni bir kullanıcının kaydolmasından de sorumludur.  
- **ZPA**: [sıfır güvenine erişimi](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.)zorunlu tutarak Web uygulamasını güvenli hale getirmekten sorumlu olan hizmet.  
- **Web uygulaması**: kullanıcının erişmeye çalıştığı hizmeti barındırır.

Aşağıdaki diyagramda ZPA 'nin Azure AD B2C ile tümleştirilme şekli gösterilmektedir.

![Zscaler mimarisinin diyagramı, ZPA 'nın Azure AD B2C ile nasıl tümleştirildiğini gösterir.](media/partner-zscaler/zscaler-architecture-diagram.png)

Sıra, aşağıdaki tabloda açıklanmıştır:

|Adım | Açıklama |
| :-----:| :-----------|
| 1 | Kullanıcı bir ZPA Kullanıcı portalına veya ZPA tarayıcı erişimi uygulamasına ulaşır.
| 2 | ZPA, kullanıcının Web uygulamasına erişmesine izin verip vermeyeceğine karar vermeden önce Kullanıcı bağlamı bilgileri gerektirir. ZPA kullanıcının kimliğini doğrulamak için Azure AD B2C oturum açma sayfasına bir SAML yönlendirmesi uygular.  
| 3 | Kullanıcı Azure AB B2C oturum açma sayfasına ulaşır. Yeni kullanıcılar hesap oluşturmak için kaydolun ve mevcut kullanıcılar mevcut kimlik bilgileriyle oturum açın. Azure AD B2C kullanıcının kimliğini doğrular.
| 4 | Başarılı kimlik doğrulamasından sonra, Azure AD B2C, kullanıcıyı SAML onaylama işlemi ile birlikte ZPA 'ye yeniden yönlendirir. ZPA SAML onaylama işlemi doğrular ve kullanıcı bağlamını ayarlar.
| 5 | ZPA Kullanıcı için erişim ilkelerini değerlendirir. Kullanıcının Web uygulamasına erişmesine izin veriliyorsa, bağlantının geçmesine izin verilir.

## <a name="onboard-to-zpa"></a>ZPA 'ya ekleme

Bu öğreticide, zaten çalışan bir ZPA kurulumu olduğunu varsaymaktadır. ZPA 'yi kullanmaya başladıysanız, [ZPA için adım adım yapılandırma kılavuzu](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)' na bakın.

## <a name="integrate-zpa-with-azure-ad-b2c"></a>ZPA 'yi Azure AD B2C ile tümleştirin

### <a name="step-1-configure-azure-ad-b2c-as-an-idp-on-zpa"></a>1. Adım: ZPA 'de Azure AD B2C IDP olarak yapılandırma

Azure AD B2C, [ZPA üzerinde bir IDP](https://help.zscaler.com/zpa/configuring-idp-single-sign)olarak yapılandırmak için aşağıdakileri yapın:

1. [ZPA yönetim portalında](https://admin.private.zscaler.com)oturum açın.

1. **Yönetim**  >  **IDP yapılandırmasına** gidin.

1. **IDP Yapılandırması Ekle**' yi seçin.

   **IDP yapılandırma Ekle** bölmesi açılır.

   !["IDP Yapılandırması Ekle" bölmesindeki "IDP Information" sekmesinin ekran görüntüsü.](media/partner-zscaler/add-idp-configuration.png)

1. **IDP bilgileri** sekmesini seçin ve ardından aşağıdakileri yapın:

   a. **Ad** kutusuna **Azure AD B2C** girin.  
   b. **Çoklu oturum açma** altında **Kullanıcı**' yı seçin.  
   c. **Etki alanları** açılan listesinde, bu IDP ile ilişkilendirmek istediğiniz kimlik doğrulama etki alanlarını seçin.

1. **İleri**’yi seçin.

1. **SP meta verileri** sekmesini seçin ve ardından aşağıdakileri yapın:

   a. **Hizmet sağlayıcı URL 'si** altında daha sonra kullanmak için değeri kopyalayın veya aklınızda olması gerekir.  
   b. **Hizmet sağlayıcısı VARLıK kimliği** altında daha sonra kullanmak için değeri kopyalayın veya aklınızda olması gerekir.

   !["IDP Yapılandırması Ekle" bölmesindeki "SP meta verileri" sekmesinin ekran görüntüsü.](media/partner-zscaler/sp-metadata.png)

1. **Duraklat**' ı seçin.

Azure AD B2C yapılandırdıktan sonra, IDP yapılandırmasının geri kalanı devam eder.

### <a name="step-2-configure-custom-policies-in-azure-ad-b2c"></a>2. Adım: Azure AD B2C özel ilkeleri yapılandırma

>[!Note]
>Bu adım yalnızca özel ilkeleri henüz yapılandırmadıysanız gereklidir. Zaten bir veya daha fazla özel ilkeleriniz varsa, bu adımı atlayabilirsiniz.

Azure AD B2C kiracınızda özel ilkeleri yapılandırmak için, bkz. [Azure Active Directory B2C özel ilkeleri kullanmaya başlama](./tutorial-create-user-flows.md?pivots=b2c-custom-policy).

### <a name="step-3-register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>3. Adım: ZPA 'yi bir SAML uygulaması olarak kaydetme Azure AD B2C

Azure AD B2C bir SAML uygulaması yapılandırmak için, bkz. [Azure AD B2C BIR SAML uygulaması kaydetme](./saml-service-provider.md). 

["Ilkenizi karşıya yükleme"](./saml-service-provider.md#upload-your-policy)adımında, Azure AD B2C tarafından kullanılan ıDP SAML meta veri URL 'sini kopyalayın veya unutmayın. Buna daha sonra ihtiyacınız olacak.

["Uygulamanızı Azure AD B2C 'de yapılandırma"](./saml-service-provider.md#configure-your-application-in-azure-ad-b2c)adımındaki yönergeleri izleyin. Adım 4,2 ' de, uygulama bildirimi özelliklerini aşağıdaki şekilde güncelleştirin:

- **Identifieruris** için: daha önce "Adım 1.6. b" içinde kopyaladığınız veya not ettiğiniz hizmet sağlayıcısı varlık kimliğini kullanın.  
- **Samlmetadataurl** IÇIN: ZPA bir SAML meta veri URL 'si barındırmadığından bu özelliği atlayın.  
- **Replyurlswithtype** için: daha önce "Adım 1.6. a" içinde kopyaladığınız veya not ettiğiniz hizmet sağlayıcı URL 'sini kullanın.  
- **LogoutURL** IÇIN: ZPA bir oturum kapatma URL 'sini desteklemediğinden bu özelliği atlayın.

Adımların geri kalanı bu öğreticiyle ilgili değildir.

### <a name="step-4-extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>4. Adım: IDP SAML meta verilerini Azure AD B2C Ayıkla

Ardından, aşağıdaki biçimde bir SAML meta veri URL 'SI edinmeniz gerekir:

```https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata```

`<tenant-name>`Azure AD B2C kiracınızın adı olduğunu ve `<policy-name>` önceki adımda oluşturduğunuz özel SAML ilkesinin adı olduğunu unutmayın.

Örneğin, URL olabilir `https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml//Samlp/metadata` .

Bir Web tarayıcısı açın ve SAML meta veri URL 'sine gidin. Sayfada herhangi bir yere sağ tıklayın, **farklı kaydet**' i seçin ve ardından bir sonraki adımda kullanmak üzere dosyayı bilgisayarınıza kaydedin.

### <a name="step-5-complete-the-idp-configuration-on-zpa"></a>5. Adım: ZPA 'de IDP yapılandırmasını doldurun

Daha önce "1. Adım: Azure AD B2C NZPA 'de IDP olarak yapılandırma" bölümünde kısmen yapılandırdığınız [ZPA yönetim portalındaki IDP yapılandırmasını](https://help.zscaler.com/zpa/configuring-idp-single-sign) doldurun.

1. [ZPA yönetim portalında](https://admin.private.zscaler.com) **Yönetim**  >  **IDP yapılandırması**' na gidin.

1. "Adım 1" içinde yapılandırdığınız IDP **'yi seçin ve** sonra da yeniden göster ' i seçin.

1. **IDP Yapılandırması Ekle** bölmesinde, **IDP oluştur** sekmesini seçin ve ardından aşağıdakileri yapın:

   a. **IDP meta veri dosyası** altında, daha önce kaydettiğiniz meta veri dosyasını "Adım 4: ıDP SAML meta verilerini ayıklama Azure AD B2C" içinde karşıya yükleyin.  
   b. IDP yapılandırması **durumunun** **etkin** olduğunu doğrulayın.  
   c. **Kaydet**’i seçin.

   !["IDP Yapılandırması Ekle" bölmesinde "IDP oluşturma" sekmesinin ekran görüntüsü.](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>Çözümü test etme

Bir ZPA Kullanıcı portalına veya tarayıcı erişimi uygulamasına gidin ve kaydolma veya oturum açma sürecini test edin. Test, başarılı bir SAML kimlik doğrulamasına neden olur.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makaleleri gözden geçirin:

- [Azure AD B2C özel ilkeleri kullanmaya başlama](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)
- [Azure AD B2C bir SAML uygulaması kaydetme](./saml-service-provider.md)
- [ZPA için adım adım yapılandırma kılavuzu](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)
- [Bir IDP 'yi çoklu oturum açma için yapılandırma](https://help.zscaler.com/zpa/configuring-idp-single-sign)