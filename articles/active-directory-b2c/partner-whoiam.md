---
title: Azure Active Directory B2C, Whoıam ile yapılandırma öğreticisi
titleSuffix: Azure AD B2C
description: Bu öğreticide, kullanıcı doğrulaması için Whoıam ile Azure AD B2C kimlik doğrulamasını tümleştirmeyi öğrenin.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 32c288476c8491747cc80f0115daa72919aa05a8
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257747"
---
# <a name="tutorial-for-configuring-whoiam-with-azure-active-directory-b2c"></a>Azure Active Directory B2C ile Whoıam yapılandırma öğreticisi

Bu örnek öğreticide, ortamınızda [Whoıam](https://www.whoiam.ai/brims/) markalı kimlik yönetimi sisteminin (brims) nasıl yapılandırılacağı ve Active Directory B2C (Azure AD B2C) ile tümleştirileceği hakkında rehberlik sağlıyoruz.

BRIMS, ortamınızda dağıtılan bir uygulama ve hizmet kümesidir. Kullanıcı tabanınız için ses, SMS ve e-posta doğrulaması sağlar. BRIT, mevcut kimlik ve erişim yönetimi çözümünüzle birlikte çalışarak platform belirsiz.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için şunlar gerekir:

- Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.

- Azure aboneliğinize bağlı [bir Azure AD B2C kiracısı](./tutorial-create-tenant.md) .

- Bir Whoıam [deneme hesabı](https://www.whoiam.ai/contact-us/).

## <a name="scenario-description"></a>Senaryo açıklaması

Whoıam tümleştirmesi aşağıdaki bileşenleri içerir:

- Azure AD B2C kiracısı. Bu, içinde tanımlanan özel ilkelere bağlı olarak kullanıcının kimlik bilgilerini doğrulayan yetkilendirme sunucusudur. Kimlik sağlayıcısı olarak da bilinir.

- İstemcileri ve bunların yapılandırmalarının yönetilmesi için bir yönetim portalı.

- Uç noktalar aracılığıyla çeşitli özellikler sunan bir API hizmeti.  

- Azure Cosmos DB, hem BRIMS yönetim portalı hem de API hizmeti için arka uç işlevi görür.

Aşağıdaki mimari diyagram uygulamayı gösterir.

![Whoıam ile Azure AD B2C tümleştirme mimarisinin diyagramı.](media/partner-whoiam/whoiam-architecture-diagram.png)

|Adım | Description |
|:-----| :-----------|
| 1. | Kullanıcı, kimlik sağlayıcısı olarak Azure AD B2C kullanan bir uygulamaya kaydolma veya oturum açma isteğini başlatmak için bir sayfaya ulaşır.
| 2. | Kimlik doğrulamanın bir parçası olarak Kullanıcı, e-posta veya telefonunun sahipliğini doğrulamayı ister veya sesini bir biyometrik doğrulama faktörü olarak kullanır.  
| 3. | Azure AD B2C BRIMS API hizmetine bir çağrı yapar ve kullanıcının e-posta adresini, telefon numarasını ve ses kaydını geçirir.
| 4. | BRIMS, kullanıcının kendi dillerinde uygulamanın stiliyle tutarlı bir şekilde etkileşimde bulunmak için tamamen özelleştirilebilir e-posta ve SMS şablonları gibi önceden tanımlanmış konfigürasyonları kullanır.
| 5. | Kullanıcının kimlik doğrulaması tamamlandıktan sonra, BRIMS, doğrulamanın sonucunu göstermek için Azure AD B2C bir belirteç döndürür. Azure AD B2C, kullanıcıya uygulamaya erişim izni verir veya kimlik doğrulama denemelerini başarısız olur.  

## <a name="sign-up-with-whoiam"></a>Whoıam ile kaydolun

1. [Whoıam](https://www.whoiam.ai/contact-us/) ile iletişim kurun ve bır brims hesabı oluşturun.

2. Kullanıma sunulan kaydolma yönergelerini kullanın ve aşağıdaki Azure hizmetlerini yapılandırın:

    - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): posta hizmeti parolaları gibi güvenli parolaların depolanması için kullanılır.

    - [Azure App Service](https://azure.microsoft.com/services/app-service/): BRIMS API ve yönetim portalı hizmetlerini barındırmak için kullanılır.

    - [Azure Active Directory](https://azure.microsoft.com/services/active-directory/): yönetim portalı için yönetici kullanıcıların kimliğini doğrulamak için kullanılır.

    - [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): ayarları depolamak ve almak için kullanılır.

    - [Application Insights](../azure-monitor/app/app-insights-overview.md) (isteğe bağlı): hem API hem de yönetici portalında oturum açmak için kullanılır.

3. BRIMS API ve BRIMS yönetim portalını Azure ortamınızda dağıtın.

4. Azure AD B2C özel ilke örnekleri, BRIMS kaydolma belgelerinizde mevcuttur. Uygulamanızı yapılandırmak ve Kullanıcı kimlik doğrulaması için BRIMS platformunu kullanmak için belgeleri izleyin.  

Whoıam 'in BRIMS hakkında daha fazla bilgi için [ürün belgelerine](https://www.whoiam.ai/brims/)bakın.

## <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

1. Azure AD B2C kiracıyı açın. **İlkeler** altında **kimlik deneyimi çerçevesi**' ni seçin.

2. Daha önce oluşturduğunuz **Signupsignın**' ı seçin.

3. **Kullanıcı akışını Çalıştır** ' ı seçin ve ardından:

   a. **Uygulama** için kayıtlı uygulamayı (örnek JWT) seçin.

   b. **Yanıt URL 'si** için **yeniden yönlendirme URL**'sini seçin.

   c. **Kullanıcı akışını Çalıştır**' ı seçin.

4. Kaydolma akışı ' na gidip bir hesap oluşturun.

5. Kullanıcı özniteliği oluşturulduktan sonra BRIMS hizmeti akış sırasında çağrılacaktır. Akış tamamlanmamışsa, kullanıcının dizine kaydedilip kaydedilmediğini kontrol edin.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makaleleri gözden geçirin:

- [Azure AD B2C'deki özel ilkeler](./custom-policy-overview.md)

- [Azure AD B2C özel ilkeleri kullanmaya başlama](tutorial-create-user-flows.md?pivots=b2c-custom-policy)