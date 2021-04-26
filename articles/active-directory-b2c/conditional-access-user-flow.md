---
title: Azure AD B2C bir Kullanıcı akışına koşullu erişim ekleme
description: Azure AD B2C Kullanıcı akışlarınıza koşullu erişim eklemeyi öğrenin. İlke zorlamak ve riskli oturum açma işlemlerini düzeltmek için Kullanıcı akışlarınızda Multi-Factor Authentication (MFA) ayarlarını ve koşullu erişim ilkelerini yapılandırın.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 03/03/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celested
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0e6a872891f09f60ea963fa783e6f49dc4e94a54
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861868"
---
# <a name="add-conditional-access-to-user-flows-in-azure-active-directory-b2c"></a>Azure Active Directory B2C Kullanıcı akışlarına koşullu erişim ekleme

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Uygulamalarınıza yönelik riskli oturum açma işlemlerini yönetmek için, Azure Active Directory B2C (Azure AD B2C) Kullanıcı akışlarına veya özel ilkelere koşullu erişim eklenebilir. Azure Active Directory (Azure AD) koşullu erişim, Azure AD B2C tarafından sinyalleri bir araya getirmek, kararlar almak ve kuruluş ilkelerini zorlamak için kullanılan bir araçtır.

![Koşullu erişim akışı](media/conditional-access-user-flow/conditional-access-flow.png)

Risk değerlendirmesi ilke koşulları ile otomatikleştirilmesi, riskli oturum açma işlemlerinin hemen ve sonra düzeltilen ya da engellenen olarak belirlenmesidir.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="service-overview"></a>Hizmete genel bakış

Azure AD B2C her oturum açma olayını değerlendirir ve Kullanıcı erişimi vermeden önce tüm ilke gereksinimlerinin karşılanmasını sağlar. Bu **değerlendirme** aşamasında, koşullu erişim hizmeti, oturum açma olayları sırasında kimlik koruması risk algılamaları tarafından toplanan sinyalleri değerlendirir. Bu değerlendirme işleminin sonucu, oturum açma işleminin verilmesi veya engellenip engellenmeyeceğini gösteren bir talepler kümesidir. Azure AD B2C ilkesi, Kullanıcı akışında bir eylem gerçekleştirmek için bu talepleri kullanır. Örneğin, erişimi engelleme veya Multi-Factor Authentication (MFA) gibi belirli bir düzeltme ile zorlayıcı Kullanıcı. "Erişimi engelle" diğer tüm ayarları geçersiz kılar.

::: zone pivot="b2c-custom-policy"
Aşağıdaki örnek, oturum açma tehdidi değerlendirmek için kullanılan koşullu erişim teknik profilini gösterir.

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

::: zone-end

Aşağıdaki **Düzeltme** aşamasında, kullanıcıya MFA ile ilgili bir sorun ortaya çıktı. Tamamlandıktan sonra, Azure AD B2C kimlik koruması, tanımlanan oturum açma tehdidine düzeltilen ve hangi yönteme göre olduğunu bildirir. Bu örnekte, Azure AD B2C kullanıcının Multi-Factor Authentication sınamasını başarıyla tamamladığını bildirir. 

::: zone pivot="b2c-custom-policy"

Aşağıdaki örnekte, tanımlanan tehdidi düzeltmek için kullanılan bir koşullu erişim teknik profili gösterilmektedir:

```XML
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

::: zone-end

## <a name="components-of-the-solution"></a>Çözümün bileşenleri

Azure AD B2C, koşullu erişimi etkinleştiren bileşenlerdir:

- Oturum açma ve kaydolma işlemi aracılığıyla kullanıcıya kılavuzluk eden **Kullanıcı akışı** veya **özel ilke** .
- Kararları almak ve kuruluş ilkelerini zorlamak için sinyalleri birlikte getiren **koşullu erişim ilkesi** . Bir Kullanıcı uygulamanızda Azure AD B2C ilkesi aracılığıyla oturum açtığında, koşullu erişim ilkesi riskli oturum açma işlemlerini belirlemek için Azure AD Kimlik Koruması sinyalleri kullanır ve uygun düzeltme eylemini gösterir.
- Kullanıcıları uygun Azure AD B2C Kullanıcı akışına veya özel ilkeye yönlendiren **kayıtlı uygulama** .
- Bir riskli oturum açma benzetimi için [Tor tarayıcısı](https://www.torproject.org/download/) .

## <a name="service-limitations-and-considerations"></a>Hizmet sınırlamaları ve konuları

Azure AD koşullu erişimini kullanırken aşağıdakileri göz önünde bulundurun:

- Kimlik koruması hem yerel hem de sosyal kimlikler (örneğin, Google veya Facebook) için kullanılabilir. Sosyal kimlikler için, koşullu erişimi el ile etkinleştirmeniz gerekir. Sosyal hesap kimlik bilgileri dış kimlik sağlayıcısı tarafından yönetildiğinden algılama sınırlıdır.
- Azure AD B2C kiracılar ' da, [Azure AD koşullu erişim](../active-directory/conditional-access/overview.md) ilkelerinin yalnızca bir alt kümesi kullanılabilir.


## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="pricing-tier"></a>Fiyatlandırma katmanı

Riskli oturum açma ilkeleri oluşturmak için Azure AD B2C **Premium P2** gereklidir. **Premium P1** kiracılar konum, uygulama, Kullanıcı tabanlı veya grup tabanlı ilkeleri temel alan bir ilke oluşturabilir. Daha fazla bilgi için bkz. [Azure AD B2C fiyatlandırma katmanınızı değiştirme](billing.md#change-your-azure-ad-pricing-tier)

## <a name="prepare-your-azure-ad-b2c-tenant"></a>Azure AD B2C kiracınızı hazırlayın

Koşullu erişim ilkesi eklemek için, güvenlik varsayılanlarını devre dışı bırak:

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
3. **Azure hizmetleri** altında **Azure AD B2C**' yi seçin. İsterseniz arama kutusunu kullanarak **Azure AD B2C** bulun ve seçin.
4. **Özellikler**' i seçin ve ardından **güvenlik varsayılanlarını Yönet**' i seçin.

   ![Güvenlik varsayılanlarını devre dışı bırak](media/conditional-access-user-flow/disable-security-defaults.png)

5. **Güvenlik varsayılanlarını etkinleştir** altında **Hayır**' ı seçin.

   ![Güvenlik varsayılanlarını etkinleştir ' i ayarla Hayır olarak ayarlayın](media/conditional-access-user-flow/enable-security-defaults-toggle.png)

## <a name="add-a-conditional-access-policy"></a>Koşullu erişim ilkesi ekleme

Koşullu erişim ilkesi, atamalar ve erişim denetimleri için bir if-then deyimidir. Koşullu erişim ilkesi, kararları almak ve kuruluş ilkelerini zorlamak için sinyalleri bir araya getirir. Atamalar arasındaki mantıksal işleç *ve* olur. Her atamadaki işleç *veya* olur.

![Koşullu erişim atamaları](media/conditional-access-user-flow/conditional-access-assignments.png)

Koşullu erişim ilkesi eklemek için:

1. Azure portal, araması yapın ve **Azure AD B2C** seçin.
1. **Güvenlik** altında **koşullu erişim (Önizleme)** öğesini seçin. **Koşullu erişim ilkeleri** sayfası açılır.
1. **+ Yeni ilke**' yi seçin.
1. İlke için *riskli oturum açmayı engelle* gibi bir ad girin.
1. **Atamalar**' ın altında, **Kullanıcılar ve gruplar**' ı seçin ve ardından aşağıdaki desteklenen yapılandırmalardan birini seçin:

    |Şunları Dahil Et:  |Lisans | Notlar  |
    |---------|---------|---------|
    |**Tüm kullanıcılar** | P1, P2 |**Tüm kullanıcıları** eklemeyi seçerseniz, bu ilke tüm kullanıcılarınızı etkiler. Kendinizi kilitlediğinizden emin olmak için, **Dışla**, **Dizin rolleri** ve ardından listede **genel yönetici** ' yi seçerek yönetim hesabınızı dışlayın. Ayrıca, **Kullanıcılar ve gruplar** ' ı seçip **hariç tutulan kullanıcıları seç** listesinden hesabınızı seçebilirsiniz.  | 
 
1. **Bulut uygulamaları veya eylemler**' i seçin ve ardından uygulamalar ' ı **seçin**. [Bağlı olan taraf uygulamanıza](tutorial-register-applications.md)gözatın.

1. **Koşullar**' ı seçin ve ardından aşağıdaki koşullardan seçim yapın. Örneğin, **oturum açma riski** ve **yüksek**, **Orta** ve **düşük** riskli Düzeyler ' i seçin.
    
    |Koşul  |Lisans  |Notlar  |
    |---------|---------|---------|
    |**Kullanıcı riski**|P2|Kullanıcı riski, belirli bir kimliğin veya hesabın güvenliğinin aşıldığına ilişkin olasılığı temsil eder.|
    |**Oturum açma riski**|P2|Oturum açma riski, belirli bir kimlik doğrulama isteğinin kimlik sahibi tarafından yetkilendirilmemiş olma olasılığını temsil eder.|
    |**Cihaz platformları**|Desteklenmez| Bir cihazda çalışan işletim sistemine göre belirlenir. Daha fazla bilgi için bkz. [cihaz platformları](../active-directory/conditional-access/concept-conditional-access-conditions.md#device-platforms).|
    |**Konumlar**|P1, P2|Adlandırılmış konumlar, genel IPv4 ağ bilgilerini, ülkeyi veya bölgeyi ya da belirli ülkelere veya bölgelere eşlenmeyen bilinmeyen alanları içerebilir. Daha fazla bilgi için bkz. [konumlar](../active-directory/conditional-access/concept-conditional-access-conditions.md#locations). |
 
1. **Erişim denetimleri** altında **Ver**’i seçin. Ardından, erişimi engellemeyi veya vermeyi seçin:
    
    |Seçenek  |Lisans |Not  |
    |---------|---------|---------|
    |**Hizmete erişimi**|P1, P2| Bu koşullu erişim ilkesinde belirtilen koşullara göre erişimi engeller.|
    |**Multi-Factor Authentication gerektir** Ile **erişim izni verme**|P1, P2|Bu koşullu erişim ilkesinde belirtilen koşullara bağlı olarak, kullanıcının Multi-Factor Authentication Azure AD B2C geçmesi gerekir.|

1. **Ilkeyi etkinleştir** altında aşağıdakilerden birini seçin:
    
    |Seçenek  |Lisans |Not  |
    |---------|---------|---------|
    |**Yalnızca rapor**|P1, P2| Rapor-yöneticilerin, koşullu erişim ilkelerinin ortamlarında etkinleştirilmeden önce etkisini değerlendirmelerini sağlar. İlkeyi bu durumla kontrol etmenizi ve Multi-Factor Authentication veya kullanıcıları engellemeye gerek kalmadan son kullanıcılara etkisini belirlemenizi öneririz. Daha fazla bilgi için bkz [. denetim raporundaki koşullu erişim sonuçlarını gözden geçirme](#review-conditional-access-outcomes-in-the-audit-report)|
    | **Açık**| P1, P2| Erişim ilkesi değerlendirilir ve zorlanmaz. |
    | **Kapalı** | P1, P2| Erişim ilkesi etkinleştirilmemiştir ve kullanıcılar üzerinde hiçbir etkisi olmaz. |

1. **Oluştur**' a tıklayarak test koşullu erişim ilkenizi etkinleştirin.

## <a name="add-conditional-access-to-a-user-flow"></a>Kullanıcı akışına koşullu erişim ekleme

Azure AD koşullu erişim ilkesini ekledikten sonra, Kullanıcı akışınız veya özel ilkenizde koşullu erişimi etkinleştirin. Koşullu erişimi etkinleştirdiğinizde bir ilke adı belirtmeniz gerekmez.

Her zaman tek bir kullanıcı için birden fazla koşullu erişim ilkesi uygulanabilir. Bu durumda, en katı erişim denetimi ilkesi önceliklidir. Örneğin, bir ilke çok faktörlü kimlik doğrulaması (MFA) gerektiriyorsa, diğer erişimi engelliyorsa Kullanıcı engellenir.

## <a name="conditional-access-template-1-sign-in-risk-based-conditional-access"></a>Koşullu erişim şablonu 1: oturum açma riski tabanlı koşullu erişim

Çoğu kullanıcı, takip edilebilen normal bir davranışa sahiptir ve davranışları normalin dışına çıktığında oturum açmalarına izin vermek riskli olabilir. Bu kullanıcıyı engellemek veya belki de gerçekten söylediklerini kanıtlamak üzere çok faktörlü kimlik doğrulaması gerçekleştirmesini istemeniz gerekebilir.

Bir oturum açma riski, belirli bir kimlik doğrulama isteğinin kimlik sahibi tarafından yetkilendirilmemiş olma olasılığını temsil eder. P2 lisanslarına sahip kuruluşlar, [Azure AD kimlik koruması oturum açma riski algılamalarını](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk)Içeren koşullu erişim ilkeleri oluşturabilir. Lütfen [B2C Için kimlik koruması algılamalarıyla ilgili sınırlamalar](https://docs.microsoft.com/azure/active-directory-b2c/identity-protection-investigate-risk?pivots=b2c-user-flow#service-limitations-and-considerations)olduğunu unutmayın.

Risk algılanırsa, kullanıcılar, yöneticilerin gereksiz gürültüsünü engellemek için riskli oturum açma olayını kendi kendine düzeltebileceği ve kapatan çok faktörlü kimlik doğrulaması gerçekleştirebilir.

Kuruluşlar, oturum açma riski orta veya yüksek olduğunda Multi-Factor Authentication (MFA) gerektiren bir oturum açma riski tabanlı koşullu erişim ilkesini etkinleştirmek için aşağıdaki seçeneklerden birini tercih etmelidir.

### <a name="enable-with-conditional-access-policy"></a>Koşullu erişim ilkesiyle etkinleştir

1. **Azure portalında** oturum açın.
2. **Azure AD B2C**  >  **güvenlik**  >  **koşullu erişimi**'ne gidin.
3. **Yeni ilke**' yi seçin.
4. İlkenize bir ad verin. Kuruluşların ilkelerinin adları için anlamlı bir standart oluşturmasını öneririz.
5. **Atamalar** altında **Kullanıcılar ve gruplar**’ı seçin.
   1. **Ekle**' nin altında **tüm kullanıcılar**' ı seçin.
   2. **Dışla** altında, **Kullanıcılar ve gruplar** ' ı seçin ve kuruluşunuzun acil erişim veya kesme camı hesaplarını seçin. 
   3. **Bitti** seçeneğini belirleyin.
6. **Bulut uygulamaları veya eylemleri**  >  **dahil**, **tüm bulut uygulamaları**' nı seçin.
7. **Koşullar**  >  **oturum açma riski** altında **Yapılandır** ' ı **Evet** olarak ayarlayın. **Bu ilkenin uygulanacağı oturum açma risk düzeyini seçin** altında 
   1. **Yüksek** ve **Orta**' yı seçin.
   2. **Bitti** seçeneğini belirleyin.
8. **Erişim denetimleri**  >  **izni** altında **erişim ver**' i seçin, **Multi-Factor Authentication gerektir**' i seçin ve **Seç**' i seçin
9. Ayarlarınızı doğrulayın ve **ilke** ayarını **Açık** olarak ayarlayın.
10. İlkenizi etkinleştirmek için oluşturmak **için Oluştur ' u seçin.**

### <a name="enable-with-conditional-access-apis"></a>Koşullu erişim API 'Leri ile etkinleştirme

Koşullu erişim API 'Leri içeren bir oturum açma riski tabanlı koşullu erişim ilkesi oluşturmak için lütfen [koşullu erişim API 'leri](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-apis#graph-api)belgelerine bakın.

Aşağıdaki şablon, "CA002: Orta ve oturum açma riski için MFA gerektir" görünen adına sahip bir koşullu erişim ilkesi oluşturmak için kullanılabilir.

```json
{
    "displayName": "Template 1: Require MFA for medium+ sign-in risk",
    "state": "enabledForReportingButNotEnforced",
    "conditions": {
        "signInRiskLevels": [ "high" ,
            "medium"
        ],
        "applications": {
            "includeApplications": [
                "All"
            ]
        },
        "users": {
            "includeUsers": [
                "All"
            ],
            "excludeUsers": [
                "f753047e-de31-4c74-a6fb-c38589047723"
            ]
        }
    },
    "grantControls": {
        "operator": "OR",
        "builtInControls": [
            "mfa"
        ]
    }
}
```

## <a name="enable-multi-factor-authentication-optional"></a>Multi-Factor Authentication 'ı etkinleştir (isteğe bağlı)

Bir Kullanıcı akışına koşullu erişim eklerken, **Multi-Factor Authentication (MFA)** kullanımını göz önünde bulundurun. Kullanıcılar, çok faktörlü kimlik doğrulaması için SMS veya Voice aracılığıyla tek seferlik bir kod ya da e-posta aracılığıyla bir kerelik parola kullanabilir. MFA ayarları, koşullu erişim ayarlarından bağımsızdır. MFA 'yı **her zaman açık** olarak ayarlayabilirsiniz, bu sayede, koşullu erişim kurulumundan BAĞıMSıZ olarak MFA her zaman gereklidir. Ya da MFA 'yı **koşullu** olarak ayarlayabilirsiniz, böylelikle MFA yalnızca etkin bir koşullu erişim ilkesi gerektirdiğinde gereklidir.

> [!IMPORTANT]
> Koşullu erişim ilkeniz MFA ile erişim izni veriyorsa ancak kullanıcı bir telefon numarası kaydetmediyse, Kullanıcı engellenebilir.

::: zone pivot="b2c-user-flow"

Bir Kullanıcı akışı için koşullu erişimi etkinleştirmek üzere, sürümün koşullu erişimi desteklediğinden emin olun. Bu Kullanıcı akışı sürümlerinin **kullanılması önerilir**.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.

1. **Azure hizmetleri** altında **Azure AD B2C**' yi seçin. İsterseniz arama kutusunu kullanarak **Azure AD B2C** bulun ve seçin.

1. **İlkeler** altında **Kullanıcı akışları**' nı seçin. Ardından Kullanıcı akışını seçin.

1. **Özellikler** ' i seçin ve **koşullu erişim** etiketli ayarı arayarak Kullanıcı akışının koşullu erişimi desteklediğinden emin olun.
 
   ![Özelliklerde MFA ve koşullu erişimi yapılandırma](media/conditional-access-user-flow/add-conditional-access.png)

1. **Multi-Factor Authentication** bölümünde, istenen **MFA yöntemini** seçin ve ardından **MFA zorlaması** altında **koşullu (önerilen)** seçeneğini belirleyin.
 
1. **Koşullu erişim** bölümünde **koşullu erişim ilkelerini zorla** onay kutusunu seçin.

1. **Kaydet**’i seçin.


::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-conditional-access-to-your-policy"></a>İlkenize koşullu erişim ekleme

1. [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/conditional-access)'da koşullu erişim ilkesi örneğini alın.
1. Her dosyada, dizeyi `yourtenant` Azure AD B2C kiracınızın adıyla değiştirin. Örneğin, B2C kiracınızın adı *contosob2c* ise, tüm örnekleri `yourtenant.onmicrosoft.com` olur `contosob2c.onmicrosoft.com` .
1. İlke dosyalarını karşıya yükleyin.

## <a name="test-your-custom-policy"></a>Özel ilkenizi test etme

1. `B2C_1A_signup_signin_with_ca` `B2C_1A_signup_signin_with_ca_whatif` Genel bakış sayfasını açmak için veya ilkesini seçin. Ardından **Kullanıcı akışını Çalıştır**' ı seçin. **Uygulama** altında *WebApp1* öğesini seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Kullanıcı akış uç noktası çalıştırma** altındaki URL 'yi kopyalayın.

1. Riskli oturum açma benzetimi yapmak için, [Tor tarayıcısını](https://www.torproject.org/download/) açın ve kayıtlı uygulamada oturum açmak için önceki adımda kopyaladığınız URL 'yi kullanın.

1. Oturum açma sayfasında istenen bilgileri girin ve oturum açmayı deneyin. Belirteç öğesine döner `https://jwt.ms` ve size gösterilmesi gerekir. Jwt.ms kodu çözülen belirteçte, oturum açma 'nın engellendiğini görmeniz gerekir.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-user-flow"></a>Kullanıcı akışınızı test etme

1. Genel Bakış sayfasını açmak için oluşturduğunuz kullanıcı akışını seçin ve ardından **Kullanıcı akışını Çalıştır**' ı seçin. **Uygulama** altında *WebApp1* öğesini seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .

1. **Kullanıcı akış uç noktası çalıştırma** altındaki URL 'yi kopyalayın.

1. Riskli oturum açma benzetimi yapmak için, [Tor tarayıcısını](https://www.torproject.org/download/) açın ve kayıtlı uygulamada oturum açmak için önceki adımda kopyaladığınız URL 'yi kullanın.

1. Oturum açma sayfasında istenen bilgileri girin ve oturum açmayı deneyin. Belirteç öğesine döner `https://jwt.ms` ve size gösterilmesi gerekir. Jwt.ms kodu çözülen belirteçte, oturum açma 'nın engellendiğini görmeniz gerekir.

::: zone-end

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>Denetim raporundaki koşullu erişim sonuçlarını gözden geçirme

Koşullu erişim olayının sonucunu gözden geçirmek için:

1. [Azure portalında](https://portal.azure.com/) oturum açın.

2. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.

3. **Azure hizmetleri** altında **Azure AD B2C**' yi seçin. İsterseniz arama kutusunu kullanarak **Azure AD B2C** bulun ve seçin.

4. **Etkinlikler**' in altında, **Denetim günlükleri**' ni seçin.

5. **Kategoriyi** **B2C** olarak ayarlayarak ve **etkinlik kaynak türünü** **ıdentityprotection** olarak ayarlayarak denetim günlüğünü filtreleyin. Sonra **Uygula**'yı seçin.

6. Son yedi güne kadar denetim etkinliğini gözden geçirin. Aşağıdaki etkinlik türleri dahildir:

   - **Koşullu erişim Ilkelerini değerlendir**: Bu denetim günlüğü girdisi, bir kimlik doğrulaması sırasında koşullu erişim değerlendirmesinin gerçekleştirildiğini gösterir.
   - **Kullanıcıyı Düzelt**: Bu giriş, koşullu erişim ilkesinin verme veya gereksinimlerinin Son Kullanıcı tarafından karşılandığını ve bu etkinliğin, Kullanıcı riskini azaltmak (riskini azaltmak) için risk altyapısına rapor edildiği anlamına gelir.

7. Listede bir **koşullu erişim ilkesi** günlüğü seçin: denetim günlüğü tanımlayıcılarını gösteren **Denetim** günlüğü, **ek ayrıntılar** bölümünde bu bilgilerle birlikte:

   - **Conditionalaccessresult**: koşullu ilke değerlendirmesi için gereken izin.
   - **AppliedPolicies**: koşulların karşılandığı ve ilkelerin açık olduğu tüm koşullu erişim ilkelerinin bir listesi.
   - **Reportingpolicies**: yalnızca rapor moduna ayarlanmış koşullu erişim ilkelerinin ve koşulların karşılandığından oluşan bir liste.

## <a name="next-steps"></a>Sonraki adımlar

[Azure AD B2C Kullanıcı akışında Kullanıcı arabirimini özelleştirme](customize-ui-with-html.md)
