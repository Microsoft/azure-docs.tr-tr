---
title: Application Insights özel ilkelerle ilgili sorunları giderme
titleSuffix: Azure AD B2C
description: Özel ilkelerinizin yürütülmesini izlemek için Application Insights ayarlama.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/05/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 074bffb8614be1f71ba1956fd5a238bc19354c58
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028752"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Application Insights ile Azure Active Directory B2C günlüklerini toplayın

Bu makalede, özel ilkelerinizle ilgili sorunları tanılamanıza olanak tanımak için Active Directory B2C (Azure AD B2C) günlüklerini toplama adımları sunulmaktadır. Application Insights özel durumları tanılamak ve uygulama performansı sorunlarını görselleştirmek için bir yol sağlar. Azure AD B2C'nin Application Insights'a veri göndermek için bir özelliği vardır.

Burada açıklanan ayrıntılı etkinlik günlükleri **yalnızca** özel ilkelerinizin geliştirilmesi sırasında etkinleştirilmelidir.

> [!WARNING]
> Öğesini `DeploymentMode` üretim ortamlarında olarak ayarlamayın `Development` . Günlükler, kimlik sağlayıcılarından ve bu kaynaklardan gönderilen tüm talepleri toplar. Geliştirici, Application Insights günlüklerinde toplanan kişisel verilerin sorumluluğunu kabul eder. Bu ayrıntılı Günlükler yalnızca, ilke **GELIŞTIRICI moduna** yerleştirildiğinde toplanır.

## <a name="set-up-application-insights"></a>Application Insights ayarlama

Henüz bir tane yoksa, aboneliğinizde bir Application Insights örneği oluşturun.

> [!TIP]
> Application Insights tek bir örneği birden çok Azure AD B2C kiracıda kullanılabilir. Daha sonra sorgunuzda, kiracıya veya ilke adına göre filtreleme yapabilirsiniz. Daha fazla bilgi için [Application Insights örnekleri içindeki günlüklere bakın](#see-the-logs-in-application-insights) .

Aboneliğinizdeki Application Insights çıkış örneğini kullanmak için şu adımları izleyin:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve Azure aboneliğinizi içeren dizini (Azure AD B2C dizininiz değil) seçin.
1. Daha önce oluşturduğunuz Application Insights kaynağını açın.
1. **Genel bakış** sayfasında, **izleme anahtarını** kaydedin

Aboneliğinizde bir Application Insights örneği oluşturmak için aşağıdaki adımları izleyin:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve Azure aboneliğinizi içeren dizini (Azure AD B2C dizininiz değil) seçin.
1. Sol taraftaki gezinti menüsünde **kaynak oluştur** ' u seçin.
1. **Application Insights** arayıp seçin ve ardından **Oluştur**' u seçin.
1. Formu doldurun, **gözden geçir + oluştur**' u seçin ve ardından **Oluştur**' u seçin.
1. Dağıtım tamamlandıktan sonra **Kaynağa Git**' i seçin.
1. Application Insights menüsünde **Yapılandır** menüsünde **Özellikler**' i seçin.
1. **Izleme anahtarını** sonraki bir adımda kullanmak üzere kaydedin.

## <a name="configure-the-custom-policy"></a>Özel ilkeyi yapılandırma

1. Bağlı olan taraf (RP) dosyasını açın, örneğin *SignUpOrSignin.xml*.
1. Aşağıdaki öznitelikleri `<TrustFrameworkPolicy>` öğesine ekleyin:

   ```xml
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Zaten mevcut değilse, `<UserJourneyBehaviors>` düğüme bir alt düğüm ekleyin `<RelyingParty>` . Bu, öğesinden sonra yerleştirilmelidir `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />` .
1. Aşağıdaki düğümü öğesinin alt öğesi olarak ekleyin `<UserJourneyBehaviors>` . ' İ `{Your Application Insights Key}` daha önce kaydettiğiniz Application Insights **izleme anahtarıyla** değiştirdiğinizden emin olun.

    ```xml
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"` ApplicationInsights 'ın işleme işlem hattı aracılığıyla Telemetriyi hızlandırmasını söyler. Geliştirme için iyi, ancak yüksek birimlerde kısıtlanmıştır. Üretimde öğesini `DeveloperMode` olarak ayarlayın `false` .
    * `ClientEnabled="true"` İzleme sayfası görünümü ve istemci tarafı hataları için ApplicationInsights istemci tarafı betiği gönderir. Bunları, Application Insights portalındaki **Browserzamanlamalar** tablosunda görüntüleyebilirsiniz. Ayar olarak `ClientEnabled= "true"` , sayfa betiklerine Application Insights ekler ve sayfa yüklerinin ve Ajax çağrılarının, sayımların, tarayıcı özel durumlarının ve Ajax hatalarının ayrıntılarının yanı sıra Kullanıcı ve oturum sayımlarının zamanlamalarını alırsınız. Bu alan **isteğe bağlıdır** ve varsayılan olarak olarak ayarlanır `false` .
    * `ServerEnabled="true"` Application Insights için, var olan Kullanıcıgünneyıkaydedicisi JSON 'sini özel bir olay olarak gönderir.

    Örnek:

    ```xml
    <TrustFrameworkPolicy
      ...
      TenantId="fabrikamb2c.onmicrosoft.com"
      PolicyId="SignUpOrSignInWithAAD"
      DeploymentMode="Development"
      UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
    >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
    </TrustFrameworkPolicy>
    ```

1. İlkeyi karşıya yükleyin.

## <a name="see-the-logs-in-application-insights"></a>Günlüklere bakın Application Insights

Application Insights yeni Günlükler görebilmeniz için genellikle beş dakikadan kısa bir gecikme olur.

1. [Azure Portal](https://portal.azure.com)oluşturduğunuz Application Insights kaynağını açın.
1. **Genel bakış** sayfasında **Günlükler**' i seçin.
1. Application Insights yeni bir sekme açın.

Günlükleri görmek için kullanabileceğiniz bir sorgu listesi aşağıda verilmiştir:

| Sorgu | Description |
|---------------------|--------------------|
| `traces` | Azure AD B2C tarafından oluşturulan tüm günlükleri al |
| `traces | where timestamp > ago(1d)` | Son gün için Azure AD B2C tarafından oluşturulan tüm günlükleri alın.|
| `traces | where message contains "exception" | where timestamp > ago(2h)`|  Son iki saatten hata içeren tüm günlükleri alın.|
| `traces | where customDimensions.Tenant == "contoso.onmicrosoft.com" and customDimensions.UserJourney  == "b2c_1a_signinandup"` | Azure AD B2C *contoso.onmicrosoft.com* kiracısı tarafından oluşturulan tüm günlükleri alın ve Kullanıcı yolculuğu *b2c_1a_signinandup*. |
| `traces | where customDimensions.CorrelationId == "00000000-0000-0000-0000-000000000000"`| İlişki KIMLIĞI için Azure AD B2C tarafından oluşturulan tüm günlükleri alın. Bağıntı KIMLIĞINI bağıntı KIMLIĞINIZ ile değiştirin. | 

Girişler uzun olabilir. Daha yakından bir görünüm için CSV 'ye aktarın.

Sorgulama hakkında daha fazla bilgi için bkz. [Azure izleyici 'de günlük sorgularına genel bakış](../azure-monitor/logs/log-query-overview.md).

## <a name="see-the-logs-in-vs-code-extension"></a>VS Code uzantılı günlüklere bakın

[Vs Code](https://code.visualstudio.com/)için [Azure AD B2C uzantısını](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) yüklemenizi öneririz. Azure AD B2C Uzantısı ile, Günlükler sizin için ilke adı, bağıntı KIMLIĞI (Application Insights bağıntı KIMLIĞI için ilk basamağı gösterir) ve günlük zaman damgası tarafından düzenlenir. Bu özellik, yerel zaman damgasına göre ilgili günlüğü bulmanıza ve Azure AD B2C tarafından yürütülen kullanıcının yolculuğunu görmenizi sağlar.

> [!NOTE]
> Topluluk, kimlik geliştiricilerine yardımcı olmak için Azure AD B2C vs Code uzantısını geliştirmiştir. Uzantı Microsoft tarafından desteklenmez ve tamamen olduğu gibi kullanılabilir hale getirilir.

### <a name="set-application-insights-api-access"></a>Application Insights API erişimi ayarla

Application Insights ayarladıktan ve özel ilkeyi yapılandırdıktan sonra, Application Insights **API kimliğinizi** almanız ve **API anahtarı** oluşturmanız gerekir. Hem API KIMLIĞI hem de API anahtarı, Application Insights olaylarını okumak için Azure AD B2C uzantısı tarafından kullanılır (Telemetriler). API anahtarlarınızın parolalar gibi yönetilmesi gerekir. Gizli tut.

> [!NOTE]
> Daha önce oluşturduğunuz Application Insights izleme anahtarı, Application Insights Telemetriler göndermek için Azure AD B2C tarafından kullanılır. İzleme anahtarını, vs Code uzantısında değil yalnızca Azure AD B2C ilkenizde kullanırsınız.

Application Insights KIMLIĞI ve anahtarı almak için:

1. Azure portal, uygulamanız için Application Insights kaynağını açın.
1. **Ayarlar**' ı ve ardından **API erişimi**' ni seçin.
1. **Uygulama kimliğini** kopyalama
1. **API anahtarı oluştur** ' u seçin
1. **Telemetriyi oku** kutusunu işaretleyin.
1. API anahtarı oluştur dikey penceresini kapatmadan önce **anahtarı** kopyalayın ve güvenli bir yere kaydedin. Anahtarı kaybederseniz, bir tane oluşturmanız gerekir.

    ![API erişim anahtarı oluşturmayı gösteren ekran görüntüsü.](./media/troubleshoot-with-application-insights/application-insights-api-access.png)

### <a name="set-up-azure-ad-b2c-vs-code-extension"></a>Azure AD B2C VS Code uzantısını ayarlama

Artık Azure Application Insights API 'SI KIMLIĞINIZ ve anahtarınız varsa, vs Code uzantısını günlükleri okumak için yapılandırabilirsiniz. Azure AD B2C VS Code uzantısı, ayarlar için iki kapsam sağlar:

- **Kullanıcı genel ayarları** -açtığınız vs Code herhangi bir örneğine küresel olarak uygulanan ayarlar.
- **Çalışma alanı ayarları** -çalışma alanınızda depolanan ayarlar ve yalnızca çalışma alanı açıldığında (vs Code **açık klasör** kullanılarak) geçerlidir.

1. **Azure AD B2C izleme** Gezgini ' nden, **Ayarlar** simgesine tıklayın.

    ![Uygulamasının Application Insights ayarlarını seçmesini gösteren ekran görüntüsü.](./media/troubleshoot-with-application-insights/app-insights-settings.png)

1. Azure Application Insights **kimliği** ve **anahtarını** sağlayın.
1. **Kaydet**’e tıklayın

Ayarları kaydettikten sonra, Application Insights günlükleri **Azure AD B2C trace (App Insights)** penceresinde görüntülenir.

![Vscode için Azure AD B2C uzantısının ekran görüntüsü, Azure Application Insights izlemesini sunuyor.](./media/troubleshoot-with-application-insights/vscode-extension-application-insights-trace.png)


## <a name="configure-application-insights-in-production"></a>Üretimde Application Insights yapılandırma

Üretim ortamınızın performansını ve daha iyi kullanıcı deneyimini geliştirmek için, ilkenizi önemli olmayan iletileri yoksayacak şekilde yapılandırmak önemlidir. Application Insights yalnızca kritik hata iletileri göndermek için aşağıdaki yapılandırmayı kullanın. 

1. `DeploymentMode` [TrustFrameworkPolicy](trustframeworkpolicy.md) özniteliğini olarak ayarlayın `Production` . 

   ```xml
   <TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_signup_signin"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin"
   DeploymentMode="Production"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights">
   ```

1. Sürekli ' `DeveloperMode` ın ' [](relyingparty.md#journeyinsights) i olarak ayarlayın `false` .

   ```xml
   <UserJourneyBehaviors>
     <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="false" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
   </UserJourneyBehaviors>
   ```
   
1. İlkenizi karşıya yükleyin ve test edin.



## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD B2C özel ilkelerle ilgili sorunları nasıl giderebileceğinizi](troubleshoot-custom-policies.md) öğrenin
