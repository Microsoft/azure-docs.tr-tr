---
title: Application Insights Azure Active Directory B2C özel ilkelerle ilgili sorunları giderme
description: Özel ilkelerinizin yürütülmesini izlemek için Application Insights ayarlama.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b8bf26791ca6489c12e4f9538d56ae0f0f66cc8c
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73602031"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Application Insights ile Azure Active Directory B2C günlüklerini toplayın

Bu makalede, özel ilkelerinizle ilgili sorunları tanılamanıza olanak tanımak için Active Directory B2C (Azure AD B2C) günlüklerini toplama adımları sunulmaktadır. Application Insights, özel durumları tanılamanıza ve uygulama performansı sorunlarını görselleştirebileceğiniz bir yol sağlar. Azure AD B2C, Application Insights veri gönderme özelliği içerir.

Burada açıklanan ayrıntılı etkinlik günlükleri **yalnızca** özel ilkelerinizin geliştirilmesi sırasında etkinleştirilmelidir.

> [!WARNING]
> Üretimde geliştirme modunu etkinleştirmeyin. Günlükler, kimlik sağlayıcılarından ve bu kaynaklardan gönderilen tüm talepleri toplar. Geliştirici, Application Insights günlüklerinde toplanan kişisel verilerin sorumluluğunu kabul eder. Bu ayrıntılı Günlükler yalnızca, ilke **GELIŞTIRICI moduna**yerleştirildiğinde toplanır.

## <a name="set-up-application-insights"></a>Application Insights ayarlama

Henüz bir tane yoksa, aboneliğinizde bir Application Insights örneği oluşturun.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve Azure aboneliğinizi içeren dizini (Azure AD B2C dizininiz değil) seçin.
1. Sol taraftaki gezinti menüsünde **kaynak oluştur** ' u seçin.
1. **Application Insights**arayıp seçin ve ardından **Oluştur**' u seçin.
1. Formu doldurun, **gözden geçir + oluştur**' u seçin ve ardından **Oluştur**' u seçin.
1. Dağıtım tamamlandıktan sonra **Kaynağa Git**' i seçin.
1. Application Insights menüsünde **Yapılandır** menüsünde **Özellikler**' i seçin.
1. **Izleme anahtarını** sonraki bir adımda kullanmak üzere kaydedin.

## <a name="configure-the-custom-policy"></a>Özel ilkeyi yapılandırma

1. Bağlı olan taraf (RP) dosyasını açın. Örneğin, *Signuporsignın. xml*.
1. Aşağıdaki öznitelikleri `<TrustFrameworkPolicy>` öğesine ekleyin:

   ```XML
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Zaten mevcut değilse, `<RelyingParty>` düğümüne bir `<UserJourneyBehaviors>` alt düğümü ekleyin. `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`hemen sonra konumlandırmalıdır.
1. Aşağıdaki düğümü `<UserJourneyBehaviors>` öğesinin bir alt öğesi olarak ekleyin. `{Your Application Insights Key}`, daha önce kaydettiğiniz Application Insights **Izleme anahtarıyla** değiştirdiğinizden emin olun.

    ```XML
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"`, ApplicationInsights 'ın işleme işlem hattı aracılığıyla Telemetriyi hızlandırmasını söyler. Geliştirme için iyi, ancak yüksek birimlerde kısıtlanmıştır.
    * `ClientEnabled="true"`, izleme sayfası görünümü ve istemci tarafı hataları için ApplicationInsights istemci tarafı betiği gönderir. Bunları, Application Insights portalındaki **Browserzamanlamalar** tablosunda görüntüleyebilirsiniz. `ClientEnabled= "true"`ayarlayarak, sayfa betiklerine Application Insights ekler ve sayfa yüklerinin ve AJAX çağrılarının, sayımların, tarayıcı özel durumlarının ve AJAX hatalarının ayrıntılarının yanı sıra Kullanıcı ve oturum sayımlarının zamanlamalarını alırsınız. Bu alan **isteğe bağlıdır**ve varsayılan olarak `false` olarak ayarlanır.
    * `ServerEnabled="true"`, Application Insights için mevcut kullanıcı etkinliği JSON JSON 'sini özel bir olay olarak gönderir.

    Örneğin:

    ```XML
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
1. **Genel bakış** menüsünde **analiz**' ı seçin.
1. Application Insights yeni bir sekme açın.

Günlükleri görmek için kullanabileceğiniz bir sorgu listesi aşağıda verilmiştir:

| Sorgu | Açıklama |
|---------------------|--------------------|
`traces` | Azure AD B2C tarafından oluşturulan tüm günlüklere bakın |
`traces | where timestamp > ago(1d)` | Son gün için Azure AD B2C tarafından oluşturulan tüm günlüklere bakın

Girişler uzun olabilir. Daha yakından bir görünüm için CSV 'ye aktarın.

Sorgulama hakkında daha fazla bilgi için bkz. [Azure izleyici 'de günlük sorgularına genel bakış](../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Sonraki adımlar

Topluluk, kimlik geliştiricilerine yardımcı olmak için bir Kullanıcı yolculuğu Görüntüleyicisi geliştirmiştir. Application Insights örneğinden okur ve Kullanıcı yolculuğu olaylarının iyi yapılandırılmış bir görünümünü sağlar. Kaynak kodu elde edersiniz ve kendi çözümünüze dağıtırsınız.

Kullanıcı yolculuğu oyuncusu Microsoft tarafından desteklenmez ve tamamen olduğu gibi kullanılabilir hale getirilir.

GitHub 'daki Application Insights olayları okuyan görüntüleyici sürümünü buradan öğrenebilirsiniz:

[Azure-Samples/Active-Directory-B2C-Advanced-policies](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)
