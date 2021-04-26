---
title: Erişimin ve verilerin güvenliğini sağlama
description: Giriş, çıkış, istek tabanlı tetikleyiciler, çalıştırma geçmişi, yönetim görevleri ve Azure Logic Apps içindeki diğer kaynaklara erişimi güvenli hale getirme
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla, rarayudu
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 7b082c226b38633d6c34ee2fe4d5227252b2bfcb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556392"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Azure Logic Apps 'da güvenli erişim ve veriler

Azure Logic Apps, [bekleyen verileri](../security/fundamentals/encryption-atrest.md)depolamak ve otomatik olarak şifrelemek Için [Azure Storage](../storage/index.yml) 'ı kullanır. Bu şifreleme, verilerinizi korur ve kurumsal güvenlik ve uyumluluk taahhütlerinizi karşılamanıza yardımcı olur. Azure depolama, verilerinizi şifrelemek için varsayılan olarak Microsoft tarafından yönetilen anahtarları kullanır. Daha fazla bilgi için bkz. [bekleyen veriler Için Azure depolama şifrelemesi](../storage/common/storage-service-encryption.md).

Azure Logic Apps erişimi daha fazla denetlemek ve gizli verileri korumak için bu alanlarda ek güvenlik ayarlayabilirsiniz:

* [İstek tabanlı tetikleyicilere gelen çağrılar için erişim](#secure-inbound-requests)
* [Mantıksal uygulama işlemlerine erişim](#secure-operations)
* [Çalışma geçmişi girdilerine ve çıkışlarına erişim](#secure-run-history)
* [Parametre girdilerine erişim](#secure-action-parameters)
* [Diğer hizmetlere ve sistemlere giden çağrılar için erişim](#secure-outbound-requests)
* [Belirli bağlayıcılar için bağlantı oluşturmayı engelle](#block-connections)
* [Logic Apps için yalıtım Kılavuzu](#isolation-logic-apps)
* [Azure Logic Apps için Azure Güvenlik temeli](../logic-apps/security-baseline.md)

Azure 'da güvenlik hakkında daha fazla bilgi için şu konulara bakın:

* [Azure'da şifrelemeye genel bakış](../security/fundamentals/encryption-overview.md)
* [Azure veri şifreleme-Rest](../security/fundamentals/encryption-atrest.md)
* [Azure Güvenlik Karşılaştırması](../security/benchmarks/overview.md)

<a name="secure-inbound-requests"></a>

## <a name="access-for-inbound-calls-to-request-based-triggers"></a>İstek tabanlı tetikleyicilere gelen çağrılar için erişim

Bir mantıksal uygulamanın istek tetikleyicisi veya [http Web kancası](../connectors/connectors-native-webhook.md) tetikleyicisi [gibi istek tabanlı](../connectors/connectors-native-reqres.md) bir tetikleyici aracılığıyla aldığı gelen çağrılar, şifrelemeyi destekler ve daha önce Güvenli Yuva Katmanı (SSL) olarak bilinen [Aktarım katmanı güvenliği (TLS) 1,2](https://en.wikipedia.org/wiki/Transport_Layer_Security)ile korunmaktadır. Logic Apps, Istek tetikleyicisine gelen bir çağrı veya HTTP Web kancası tetikleyicisi ya da eylemine geri çağrı alırken bu sürümü zorlar. TLS el sıkışma hataları alırsanız TLS 1,2 kullandığınızdan emin olun. Daha fazla bilgi için bkz. [TLS 1,0 sorununu çözme](/security/solving-tls1-problem).

Gelen çağrılar bu şifreleme paketlerini destekler:

* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

Mantıksal uygulamanızı yalnızca yetkili istemcilerin çağırabilmesi için, mantıksal uygulamanıza gelen çağrıları alan tetikleyicilere erişimi sınırlamanıza olanak sağlayan ek yollar şunlardır:

* [Paylaşılan erişim imzaları oluşturma (SAS)](#sas)
* [Azure Active Directory açma kimlik doğrulamasını etkinleştirme (Azure AD OAuth)](#enable-oauth)
* [Mantıksal uygulamanızı Azure API Management kullanıma sunma](#azure-api-management)
* [Gelen IP adreslerini kısıtla](#restrict-inbound-ip-addresses)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Paylaşılan erişim imzaları oluşturma (SAS)

Bir mantıksal uygulamadaki her istek uç noktası, bitiş noktasının URL 'sinde, bu biçimi izleyen bir [paylaşılan erişim imzasına (SAS)](/rest/api/storageservices/constructing-a-service-sas) sahiptir:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Her URL `sp` , `sv` `sig` Bu tabloda açıklandığı gibi,, ve sorgu parametresini içerir:

| Sorgu parametresi | Açıklama |
|-----------------|-------------|
| `sp` | İzin verilen HTTP yöntemlerinin kullanması için izinleri belirtir. |
| `sv` | İmzayı oluşturmak için kullanılacak SAS sürümünü belirtir. |
| `sig` | Tetikleyiciye erişim kimliğini doğrulamak için kullanılacak imzayı belirtir. Bu imza, tüm URL yollarında ve özelliklerde gizli bir erişim anahtarı ile SHA256 algoritması kullanılarak oluşturulur. Hiçbir koşulda veya yayımlanmadıysa, bu anahtar şifrelenir ve mantıksal uygulamayla birlikte depolanır. Mantıksal uygulamanız yalnızca gizli anahtar ile oluşturulmuş geçerli bir imza içeren Tetikleyicileri yetkilendirir. |
|||

İstek uç noktasına gelen çağrılar, SAS veya [Azure Active Directory açık kimlik doğrulaması](#enable-oauth)yalnızca bir yetkilendirme şeması kullanabilir. Tek bir düzenin kullanılması diğer düzeni devre dışı bırakmakla birlikte, her iki düzeni de aynı anda kullanmak bir hataya neden olur çünkü hizmet hangi düzenin seçeceğini bilmez.

SAS ile erişimin güvenliğini sağlama hakkında daha fazla bilgi için bu konudaki şu bölümlere bakın:

* [Erişim anahtarlarını yeniden oluştur](#access-keys)
* [Süresi dolan geri arama URL 'Leri oluşturma](#expiring-urls)
* [Birincil veya ikincil anahtarla URL oluşturma](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Erişim anahtarlarını yeniden oluştur

Dilediğiniz zaman yeni bir güvenlik erişim anahtarı oluşturmak için Azure REST API veya Azure portal kullanın. Eski anahtarı kullanan önceden oluşturulmuş tüm URL 'Ler geçersiz kılınır ve mantıksal uygulamayı tetiklemek için artık yetkilendirmeye sahip olmaz. Yeniden oluşturma işleminden sonra aldığınız URL 'Ler yeni erişim anahtarıyla imzalanır.

1. [Azure Portal](https://portal.azure.com), yeniden oluşturmak istediğiniz anahtarı içeren mantıksal uygulamayı açın.

1. Mantıksal uygulamanın menüsünde, **Ayarlar** altında **erişim anahtarları**' nı seçin.

1. Yeniden oluşturmak istediğiniz anahtarı seçin ve işlemi sona erdirin.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Süresi dolan geri arama URL 'Leri oluşturma

Diğer kişilerle istek tabanlı bir tetikleyici için uç nokta URL 'SI paylaşırsanız, belirli anahtarları kullanan ve sona erme tarihleri olan geri çağırma URL 'Leri oluşturabilirsiniz. Bu şekilde, belirli bir TimeSpan temelinde, mantıksal uygulamanızı tetikleyerek anahtarları sorunsuzca alabilir veya erişimi kısıtlayabilirsiniz. Bir URL için sona erme tarihi belirtmek için [Logic Apps REST API](/rest/api/logic/workflowtriggers)kullanın, örneğin:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

Gövdesinde, `NotAfter` BIR JSON tarih dizesi kullanarak özelliğini ekleyin. Bu özellik yalnızca tarih ve saate kadar geçerli olan bir geri çağırma URL 'SI döndürür `NotAfter` .

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Birincil veya ikincil gizli anahtar ile URL oluşturma

İstek tabanlı bir tetikleyici için geri çağırma URL 'Leri oluştururken veya listelemeniz durumunda, URL 'YI imzalamak için kullanılacak anahtarı belirtebilirsiniz. Belirli bir anahtarla imzalanmış bir URL oluşturmak için [Logic Apps REST API](/rest/api/logic/workflowtriggers)kullanın, örneğin:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

Gövdesinde, `KeyType` ya da olarak özelliğini ekleyin `Primary` `Secondary` . Bu özellik, belirtilen güvenlik anahtarı tarafından imzalanan bir URL döndürür.

<a name="enable-oauth"></a>

### <a name="enable-azure-active-directory-open-authentication-azure-ad-oauth"></a>Azure Active Directory açma kimlik doğrulamasını etkinleştirme (Azure AD OAuth)

İstek tabanlı tetikleyici tarafından oluşturulan bir uç noktaya gelen çağrılar için, mantıksal uygulamanıza yönelik bir yetkilendirme ilkesi tanımlayarak veya ekleyerek [Azure AD OAuth](../active-directory/develop/index.yml) 'ı etkinleştirebilirsiniz. Bu şekilde, gelen çağrılar yetkilendirme için OAuth [erişim belirteçleri](../active-directory/develop/access-tokens.md) kullanır.

Mantıksal uygulamanız bir OAuth erişim belirteci içeren bir gelen istek aldığında, Azure Logic Apps hizmeti belirtecin taleplerini her yetkilendirme ilkesi tarafından belirtilen taleplerle karşılaştırır. Belirtecin talepleri ve en az bir ilkedeki tüm talepler arasında bir eşleşme varsa, gelen istek için Yetkilendirme başarılı olur. Belirteç, yetkilendirme ilkesi tarafından belirtilen sayıdan daha fazla talebe sahip olabilir.

#### <a name="considerations-before-you-enable-azure-ad-oauth"></a>Azure AD OAuth 'ı etkinleştirmeden önce dikkat edilecek noktalar

* İstek uç noktasına gelen çağrı yalnızca Azure AD OAuth veya [paylaşılan erişim imzası (SAS)](#sas)olan bir yetkilendirme şemasını kullanabilir. Tek bir düzenin kullanılması diğer düzeni devre dışı bırakmakla birlikte, her iki düzeni de aynı anda kullanmak bir hataya neden olur çünkü Logic Apps hizmeti hangi düzenin seçeceğini bilmez.

* Azure AD OAuth erişim belirteçleri için yalnızca [taşıyıcı türü](../active-directory/develop/active-directory-v2-protocols.md#tokens) yetkilendirme şemaları desteklenir, bu da `Authorization` erişim belirtecinin üst bilgisinin türü belirtmesi gerektiği anlamına gelir `Bearer` .

* Mantıksal uygulamanız en fazla sayıda Yetkilendirme ilkesiyle sınırlıdır. Her yetkilendirme ilkesinin Ayrıca en fazla sayıda [talep](../active-directory/develop/developer-glossary.md#claim)vardır. Daha fazla bilgi için bkz. [Azure Logic Apps Için sınırlar ve yapılandırma](../logic-apps/logic-apps-limits-and-config.md#authentication-limits).

* Yetkilendirme ilkesi,  `https://sts.windows.net/` `https://login.microsoftonline.com/` Azure AD veren kimliği olarak ya da (OAuth v2) ile başlayan bir değere sahip en az veren talebi içermelidir.

  Örneğin, mantıksal uygulamanızın iki talep türü, **hedef kitle** ve **veren** gerektiren bir yetkilendirme ilkesi olduğunu varsayalım. Kodu çözülen erişim belirtecinin Bu örnek [Yük bölümü](../active-directory/develop/access-tokens.md#payload-claims) , hem `aud` **hedef kitle** değeri hem de `iss` **veren** değeri olan talep türlerini içerir:

  ```json
  {
      "aud": "https://management.core.windows.net/",
      "iss": "https://sts.windows.net/<Azure-AD-issuer-ID>/",
      "iat": 1582056988,
      "nbf": 1582056988,
      "exp": 1582060888,
      "_claim_names": {
         "groups": "src1"
      },
      "_claim_sources": {
         "src1": {
            "endpoint": "https://graph.windows.net/7200000-86f1-41af-91ab-2d7cd011db47/users/00000-f433-403e-b3aa-7d8406464625d7/getMemberObjects"
         }
      },
      "acr": "1",
      "aio": "AVQAq/8OAAAA7k1O1C2fRfeG604U9e6EzYcy52wb65Cx2OkaHIqDOkuyyr0IBa/YuaImaydaf/twVaeW/etbzzlKFNI4Q=",
      "amr": [
         "rsa",
         "mfa"
      ],
      "appid": "c44b4083-3bb0-00001-b47d-97400853cbdf3c",
      "appidacr": "2",
      "deviceid": "bfk817a1-3d981-4dddf82-8ade-2bddd2f5f8172ab",
      "family_name": "Sophia Owen",
      "given_name": "Sophia Owen (Fabrikam)",
      "ipaddr": "167.220.2.46",
      "name": "sophiaowen",
      "oid": "3d5053d9-f433-00000e-b3aa-7d84041625d7",
      "onprem_sid": "S-1-5-21-2497521184-1604012920-1887927527-21913475",
      "puid": "1003000000098FE48CE",
      "scp": "user_impersonation",
      "sub": "KGlhIodTx3XCVIWjJarRfJbsLX9JcdYYWDPkufGVij7_7k",
      "tid": "72f988bf-86f1-41af-91ab-2d7cd011db47",
      "unique_name": "SophiaOwen@fabrikam.com",
      "upn": "SophiaOwen@fabrikam.com",
      "uti": "TPJ7nNNMMZkOSx6_uVczUAA",
      "ver": "1.0"
   }
   ```

#### <a name="enable-azure-ad-oauth-for-your-logic-app"></a>Mantıksal uygulamanız için Azure AD OAuth 'ı etkinleştirin

Azure portal veya Azure Resource Manager şablonunuz için şu adımları izleyin:

<a name="define-authorization-policy-portal"></a>

#### <a name="portal"></a>[Portal](#tab/azure-portal)

[Azure Portal](https://portal.azure.com), mantıksal uygulamanıza bir veya daha fazla yetkilendirme ilkesi ekleyin:

1. [Azure Portal](https://portal.microsoft.com)mantıksal uygulama tasarımcısında mantıksal uygulamanızı bulun ve açın.

1. Mantıksal uygulama menüsünde, **Ayarlar** altında, **Yetkilendirme**' yi seçin. Yetkilendirme bölmesi açıldıktan sonra **Ilke Ekle**' yi seçin.

   !["İlke Ekle" > "yetkilendirme" yi seçin](./media/logic-apps-securing-a-logic-app/add-azure-active-directory-authorization-policies.png)

1. Mantıksal uygulamanızın Istek tetikleyicisine her gelen çağrı tarafından sunulan erişim belirtecinde beklediği [talep türlerini](../active-directory/develop/developer-glossary.md#claim) ve değerlerini belirterek yetkilendirme ilkesi hakkında bilgi sağlayın:

   ![Yetkilendirme ilkesi için bilgi sağlama](./media/logic-apps-securing-a-logic-app/set-up-authorization-policy.png)

   | Özellik | Gerekli | Açıklama |
   |----------|----------|-------------|
   | **İlke adı** | Yes | Yetkilendirme ilkesi için kullanmak istediğiniz ad |
   | **Talepler** | Yes | Mantıksal uygulamanızın gelen çağrılardan kabul ettiği talep türleri ve değerleri. Talep değeri, [en fazla karakter sayısıyla](logic-apps-limits-and-config.md#authentication-limits)sınırlıdır. Kullanılabilir talep türleri şunlardır: <p><p>- **Enden** <br>- **Grubu** <br>- **Konu** <br>- **JWT kimliği** (JSON Web Token tanımlayıcı) <p><p>**Talep** listesi, en azından,  `https://sts.windows.net/` `https://login.microsoftonline.com/` Azure AD veren kimliği ile başlayan bir değere sahip veren talebini içermelidir. Bu talep türleri hakkında daha fazla bilgi için bkz. [Azure AD güvenlik belirteçlerinde talepler](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens). Kendi talep türünü ve değerini de belirtebilirsiniz. |
   |||

1. Başka bir talep eklemek için şu seçeneklerden birini seçin:

   * Başka bir talep türü eklemek için **Standart talep Ekle**' yi seçin, talep türünü seçin ve talep değerini belirtin.

   * Kendi taleplerinizi eklemek için **özel talep Ekle**' yi seçin. Daha fazla bilgi için bkz. [uygulamanıza isteğe bağlı talepler sağlama](../active-directory/develop/active-directory-optional-claims.md). Özel talep daha sonra JWT KIMLIĞINIZIN bir parçası olarak depolanır; Örneğin, `"tid": "72f988bf-86f1-41af-91ab-2d7cd011db47"` . 

1. Başka bir yetkilendirme ilkesi eklemek için **Ilke Ekle**' yi seçin. İlkeyi ayarlamak için önceki adımları tekrarlayın.

1. İşiniz bittiğinde **Kaydet**'i seçin.

1. `Authorization`İstek tabanlı tetikleyici çıktılarında erişim belirtecinden üstbilgiyi eklemek için, bkz. [istek tetikleme çıktılarına ' Authorization ' üst bilgisini ekleme](#include-auth-header).


İlkeler gibi iş akışı özellikleri, Azure portal mantıksal uygulamanızın kod görünümünde görünmez. İlkelerinize programlı bir şekilde erişmek için Azure Resource Manager (ARM) aracılığıyla aşağıdaki API 'yi çağırın: `https://management.azure.com/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group-name}/providers/Microsoft.Logic/workflows/{your-workflow-name}?api-version=2016-10-01&_=1612212851820` . Azure abonelik KIMLIĞINIZ, kaynak grubu adı ve iş akışı adı için yer tutucu değerlerini değiştirdiğinizden emin olun.


<a name="define-authorization-policy-template"></a>

#### <a name="resource-manager-template"></a>[Kaynak Yöneticisi şablonu](#tab/azure-resource-manager)

ARM şablonunuzda aşağıdaki adımları ve söz dizimini takip eden bir yetkilendirme ilkesi tanımlayın:

1. `properties` [Mantıksal uygulamanızın kaynak tanımına](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition)ait bölümünde, yoksa `accessControl` bir nesne içeren bir nesne ekleyin `triggers` .

   Nesne hakkında daha fazla bilgi için `accessControl` bkz. [Azure Resource Manager ŞABLONUNDA gelen IP aralıklarını kısıtlama](#restrict-inbound-ip-template) ve [Microsoft. Logic iş akışları şablon başvurusu](/azure/templates/microsoft.logic/2019-05-01/workflows).

1. `triggers`Nesnesinde, `openAuthenticationPolicies` `policies` bir veya daha fazla yetkilendirme ilkesi tanımladığınız nesneyi içeren bir nesne ekleyin.

1. Yetkilendirme ilkesi için bir ad belirtin, ilke türünü olarak ayarlayın `AAD` ve `claims` bir veya daha fazla talep türü belirttiğiniz bir dizi ekleyin.

   En azından, dizi, `claims` talebin özelliğini olarak ayarladığınız veren talep türünü içermeli `name` `iss` ve ' ı `value` `https://sts.windows.net/` veya `https://login.microsoftonline.com/` Azure AD veren kimliği ile başlangıç olarak ayarlanır. Bu talep türleri hakkında daha fazla bilgi için bkz. [Azure AD güvenlik belirteçlerinde talepler](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens). Kendi talep türünü ve değerini de belirtebilirsiniz.

1. `Authorization`İstek tabanlı tetikleyici çıktılarında erişim belirtecinden üstbilgiyi eklemek için, bkz. [istek tetikleme çıktılarına ' Authorization ' üst bilgisini ekleme](#include-auth-header).

İzlenecek sözdizimi aşağıda verilmiştir:

```json
"resources": [
   {
      // Start logic app resource definition
      "properties": {
         "state": "<Enabled-or-Disabled>",
         "definition": {<workflow-definition>},
         "parameters": {<workflow-definition-parameter-values>},
         "accessControl": {
            "triggers": {
               "openAuthenticationPolicies": {
                  "policies": {
                     "<policy-name>": {
                        "type": "AAD",
                        "claims": [
                           {
                              "name": "<claim-name>",
                              "value": "<claim-value>"
                           }
                        ]
                     }
                  }
               }
            },
         },
      },
      "name": "[parameters('LogicAppName')]",
      "type": "Microsoft.Logic/workflows",
      "location": "[parameters('LogicAppLocation')]",
      "apiVersion": "2016-06-01",
      "dependsOn": [
      ]
   }
   // End logic app resource definition
],
```

---

<a name="include-auth-header"></a>

#### <a name="include-authorization-header-in-request-trigger-outputs"></a>İstek tetikleyicisi çıktılarına ' Authorization ' üst bilgisini dahil et

İstek tabanlı tetikleyicilere erişim için gelen çağrıları yetkilendirmek üzere [Azure Active Directory açma kimlik doğrulamasını (Azure AD OAuth) etkinleştiren](#enable-oauth) Logic Apps Için, `Authorization` OAuth erişim belirtecinden üstbilgiyi dahil etmek üzere Istek tetikleyicisi veya http Web kancası tetikleyicisi çıkışlarını etkinleştirebilirsiniz. Tetikleyicinin temel alınan JSON tanımında özelliği ekleyin ve öğesini `operationOptions` olarak ayarlayın `IncludeAuthorizationHeadersInOutputs` . Istek tetikleyicisi için bir örnek aşağıda verilmiştir:

```json
"triggers": {
   "manual": {
      "inputs": {
         "schema": {}
      },
      "kind": "Http",
      "type": "Request",
      "operationOptions": "IncludeAuthorizationHeadersInOutputs"
   }
}
```

Daha fazla bilgi için şu konulara bakın:

* [Tetikleyici ve eylem türleri için şema başvurusu-Istek tetikleyicisi](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)
* [Tetikleyici ve eylem türleri için şema başvurusu-HTTP Web kancası tetikleyicisi](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Tetikleyici ve eylem türleri için şema başvurusu-Işlem seçenekleri](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)

<a name="azure-api-management"></a>

### <a name="expose-your-logic-app-with-azure-api-management"></a>Mantıksal uygulamanızı Azure API Management kullanıma sunma

Mantıksal uygulamanıza daha fazla [kimlik doğrulama protokolü](../active-directory/develop/authentication-vs-authorization.md) eklemek için [Azure API Management](../api-management/api-management-key-concepts.md) hizmetini kullanmayı göz önünde bulundurun. Bu hizmet, mantıksal uygulamanızı bir API olarak kullanıma sunmanıza yardımcı olur ve herhangi bir uç nokta için zengin izleme, güvenlik, ilke ve belge sağlar. API Management mantıksal uygulamanız için ortak veya özel bir uç nokta sunabilir. Bu uç noktaya erişim yetkisi vermek için, bu uç noktaya erişimi yetkilendirmek üzere Azure AD OAuth, [istemci sertifikası](#client-certificate-authentication)veya diğer güvenlik standartlarını kullanabilirsiniz. API Management bir istek aldığında, hizmet, isteği mantıksal uygulamanıza gönderir ve aynı zamanda gerekli dönüştürmeleri veya kısıtlamaları da yapar. Mantıksal uygulamanızı yalnızca API Management çağrısına izin vermek için, [mantıksal uygulamanızın gelen IP adreslerini kısıtlayabilirsiniz](#restrict-inbound-ip).

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Gelen IP adreslerini kısıtla

Paylaşılan erişim Imzası (SAS) ile birlikte, mantıksal uygulamanızı çağırabilen istemcileri özellikle sınırlandırmak isteyebilirsiniz. Örneğin, [Azure API Management](../api-management/api-management-key-concepts.md)kullanarak istek uç noktanızı yönetiyorsanız, mantıksal uygulamanızı yalnızca [sizin oluşturduğunuz API Management hizmet örneğinin](../api-management/get-started-create-service-instance.md)IP adresinden gelen istekleri kabul edecek şekilde kısıtlayabilirsiniz.

Belirttiğiniz herhangi bir IP adresi ne olursa olsun, [Logic Apps REST API: Iş akışı Tetikleyicileri-Çalıştır](/rest/api/logic/workflowtriggers/run) isteği veya API Management kullanarak istek tabanlı tetikleyicisine sahip bir mantıksal uygulamayı çalıştırmaya devam edebilirsiniz. Ancak, bu senaryo Azure REST API için de [kimlik doğrulaması](../active-directory/develop/authentication-vs-authorization.md) gerektirir. Tüm olaylar Azure denetim günlüğünde görüntülenir. Erişim denetim ilkelerini uygun şekilde ayarladığınızdan emin olun.

Mantıksal uygulamanızın gelen IP adreslerini kısıtlamak için Azure portal veya Azure Resource Manager şablonunuz için şu adımları izleyin:

<a name="restrict-inbound-ip-portal"></a>

#### <a name="portal"></a>[Portal](#tab/azure-portal)

[Azure Portal](https://portal.azure.com), bu filtre hem Tetikleyicileri hem de eylemleri etkiler *ve* Bu, PORTALDA **izin verilen gelen IP adresleri** altındaki açıklamanın aksine. Bu filtreyi Tetikleyiciler ve eylemler için ayrı olarak ayarlamak için, `accessControl` mantıksal uygulamanız için Azure Resource Manager şablonda nesnesini kullanın veya [REST API: Workflow-Create veya Update işlemi Logic Apps](/rest/api/logic/workflows/createorupdate).

1. [Azure Portal](https://portal.azure.com)mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

1. Mantıksal uygulamanızın menüsünde, **Ayarlar**' ın altında, **iş akışı ayarları**' nı seçin.

1. **Erişim denetimi yapılandırması** bölümünde, **ızın verilen gelen IP adresleri** altında senaryonuz için yolu seçin:

   * Mantıksal uygulamanızı yalnızca yerleşik [Azure Logic Apps eylemini](../logic-apps/logic-apps-http-endpoint.md)kullanarak iç içe bir mantıksal uygulama olarak çağrılabilir hale getirmek için yalnızca, iç içe geçmiş mantıksal uygulamayı çağırmak için **Azure Logic Apps** eylemini *kullandığınızda yalnızca* **diğer Logic Apps** seçin.

     Bu seçenek, mantıksal uygulama kaynağınızın boş bir dizisini yazar ve yalnızca yerleşik **Azure Logic Apps** eylemini kullanan üst mantıksal uygulamalardan gelen çağrıların iç içe mantıksal uygulamayı tetikleyebilmesine gerek duyar.

   * Mantıksal uygulamanızı yalnızca HTTP eylemini kullanarak iç içe geçmiş bir uygulama olarak çağrılabilir hale getirmek için, **yalnızca diğer Logic Apps** *DEĞIL* , **belirli IP aralıklarını** seçin. **Tetikleyiciler Için IP aralıkları** kutusu göründüğünde, ana mantıksal UYGULAMANıN [giden IP adreslerini](../logic-apps/logic-apps-limits-and-config.md#outbound)girin. Geçerli bir IP aralığı şu biçimleri kullanır: *x. x. x. x/x* veya *x. x. x. x-x. x. x.* x.

     > [!NOTE]
     > İç içe mantıksal uygulamanızı çağırmak için **yalnızca diğer Logic Apps** SEÇENEĞINI ve http eylemini kullanırsanız, çağrı engellenir ve "401 Yetkisiz" hatası alırsınız.

   * Diğer IP 'lerden gelen çağrıları kısıtlamak istediğiniz senaryolarda, **Tetikleyiciler Için IP aralıkları** kutusu göründüğünde, tetikleyicinin kabul ettiği IP adresi aralıklarını belirtin. Geçerli bir IP aralığı şu biçimleri kullanır: *x. x. x. x/x* veya *x. x. x. x-x. x. x.* x.

1. İsteğe bağlı olarak, **çalıştırma geçmişinden GIRILEN IP adreslerine giriş ve çıkış iletileri almak için çağrıları kısıtla**' nın altında, çalıştırma geçmişinde giriş ve çıkış iletilerine erişebilen gelen ÇAĞRıLAR için IP adresi aralıklarını belirtebilirsiniz.

<a name="restrict-inbound-ip-template"></a>

#### <a name="resource-manager-template"></a>[Kaynak Yöneticisi şablonu](#tab/azure-resource-manager)

ARM şablonunuzda, bölümünü kullanarak mantıksal uygulamanızın kaynak tanımında izin verilen gelen IP adresi aralıklarını belirtin `accessControl` . Bu bölümde, `triggers` `actions` `contents` özelliği ile bölümü dahil ederek,, ve isteğe bağlı bölümleri uygun şekilde kullanın `allowedCallerIpAddresses` `addressRange` ve özellik değerini *x. x. x. x/x* veya *x. x. x. x-x* . x. x. x biçimindeki izin verilen IP aralığı olarak ayarlayın.

* İç içe mantıksal uygulamanız yalnızca yerleşik Azure Logic Apps eylemini kullanan diğer mantıksal uygulamalardan gelen çağrılara izin veren **yalnızca diğer Logic Apps** seçeneğini kullanıyorsa, `allowedCallerIpAddresses` özelliği boş bir diziye (**[]**) ayarlayın ve özelliği *atlayın* `addressRange` .

* İç içe mantıksal uygulamanız, HTTP eylemini kullanan diğer mantıksal uygulamalar gibi diğer gelen çağrılar için **belırlı IP aralıkları** seçeneğini kullanıyorsa, bölümünü dahil eder `allowedCallerIpAddresses` ve `addressRange` özelliği izin verilen IP aralığı olarak ayarlayın.

Bu örnekte, yalnızca yerleşik Azure Logic Apps eylemini kullanan Logic Apps 'ten gelen çağrılara izin veren iç içe mantıksal uygulama için bir kaynak tanımı gösterilmektedir:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               <workflow-definition>
            },
            "parameters": {
            },
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": []
               },
               "actions": {
                  "allowedCallerIpAddresses": []
               },
               // Optional
               "contents": {
                  "allowedCallerIpAddresses": []
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

Bu örnekte, HTTP eylemini kullanan Logic Apps 'ten gelen çağrılara izin veren iç içe mantıksal uygulama için bir kaynak tanımı gösterilmektedir:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               <workflow-definition>
            },
            "parameters": {
            },
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     }
                  ]
               },
               "actions": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     }
                  ]
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

---

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Mantıksal uygulama işlemlerine erişim

Mantıksal uygulamaları yönetme, düzenleme ve görüntüleme gibi belirli görevleri yalnızca belirli kullanıcıların veya grupların çalıştırmasına izin verebilirsiniz. İzinlerini denetlemek için, Azure aboneliğinizdeki üyelere özelleştirilmiş veya yerleşik roller atayabilmeniz için [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) kullanın:

* [Mantıksal uygulama katılımcısı](../role-based-access-control/built-in-roles.md#logic-app-contributor): mantıksal uygulamaları yönetmenizi sağlar, ancak bunlara erişimi değiştiremezsiniz.

* [Logic App operatörü](../role-based-access-control/built-in-roles.md#logic-app-operator): Logic Apps 'i okumanızı, etkinleştirmenizi ve devre dışı bırakmanızı sağlar, ancak bunları düzenleyemez veya güncelleştiremezsiniz.

Başkalarının mantıksal uygulamanızı değiştirmesini veya silmesini engellemek için [Azure Kaynak kilidi](../azure-resource-manager/management/lock-resources.md)' ni kullanabilirsiniz. Bu özellik başkalarının üretim kaynaklarını değiştirmesini veya silmesini engeller.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Çalışma geçmişi verilerine erişim

Mantıksal uygulama çalıştırma sırasında, Aktarım Katmanı Güvenliği (TLS) ve [bekleyen](../security/fundamentals/encryption-atrest.md)' i kullanarak tüm veriler [geçiş sırasında şifrelenir](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) . Mantıksal uygulamanız çalışmayı bitirdiğinde, her eylem için durum, süre, girişler ve çıkışlarla birlikte çalışan adımlar dahil olmak üzere o çalıştırmaya ilişkin geçmişi görüntüleyebilirsiniz. Bu zengin ayrıntı, mantıksal uygulamanızın nasıl çalıştığı ve ortaya çıkan tüm sorunları gidermeye başlayabileceğiniz hakkında öngörüler sağlar.

Mantıksal uygulamanızın çalıştırma geçmişini görüntülediğinizde Logic Apps erişiminizin kimliğini doğrular ve ardından her bir çalıştırmaya yönelik istekler ve yanıtlara yönelik giriş ve çıkışlara bağlantılar sağlar. Ancak, herhangi bir parolayı, parolayı, anahtarı veya diğer hassas bilgileri işleyen eylemler için başkalarının bu verilere bakmasını ve erişimini engellemek isteyebilirsiniz. Örneğin, mantıksal uygulamanız bir HTTP eyleminin kimlik doğrulaması sırasında kullanmak üzere [Azure Key Vault](../key-vault/general/overview.md) bir gizli dizi alırsa, bu gizli anahtarı görünümden gizlemek istersiniz.

Mantıksal uygulamanızın çalıştırma geçmişinde giriş ve çıkışlara erişimi denetlemek için şu seçeneklere sahipsiniz:

* [IP adresi aralığına göre erişimi kısıtlayın](#restrict-ip).

  Bu seçenek, belirli bir IP adres aralığından gelen isteklere göre çalışma geçmişine erişimi güvenli hale getirmeye yardımcı olur.

* [Gizleme kullanarak çalıştırma geçmişindeki verileri güvenli hale](#obfuscate)getirin.

  Birçok tetikleyici ve eylem içinde, bir mantıksal uygulamanın çalıştırma geçmişinde girdileri, çıkışları veya her ikisini de güvenli hale getirebilirsiniz.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>IP adresi aralığına göre erişimi kısıtla

Yalnızca belirli IP adresi aralıklarından gelen isteklerin bu verileri görüntülemesi için mantıksal uygulamanızın çalıştırma geçmişinde giriş ve çıkışlara erişimi sınırlayabilirsiniz.

Örneğin, herhangi bir kişinin girişlere ve çıkışlara erişmesini engellemek için gibi bir IP adresi aralığı belirtin `0.0.0.0-0.0.0.0` . Yalnızca yönetici izinlerine sahip bir kişi, mantıksal uygulamanızın verilerine "tam zamanında" erişim olanağı sunan bu kısıtlamayı kaldırabilir.

İzin verilen IP aralıklarını belirtmek için, Azure portal veya Azure Resource Manager şablonunuz için şu adımları izleyin:

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

1. Mantıksal uygulamanızın menüsünde, **Ayarlar**' ın altında, **iş akışı ayarları**' nı seçin.

1. **Erişim denetimi yapılandırması**  >  **izin verilen gelen IP adresleri** altında **belirli IP aralıkları**' nı seçin.

1. İçerik **Için IP aralıkları** altında girişler ve çıkışlardan IÇERIĞE erişebilen IP adresi aralıklarını belirtin.

   Geçerli bir IP aralığı şu biçimleri kullanır: *x. x. x. x/x* veya *x. x. x. x-x. x. x.* x

#### <a name="resource-manager-template"></a>[Kaynak Yöneticisi şablonu](#tab/azure-resource-manager)

ARM şablonunuzda, `accessControl` mantıksal uygulamanızın kaynak tanımındaki bölümünün bulunduğu bölümü kullanarak IP aralıklarını belirtin `contents` , örneğin:

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "contents": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

---

<a name="obfuscate"></a>

### <a name="secure-data-in-run-history-by-using-obfuscation"></a>Gizleme kullanarak çalıştırma geçmişindeki verileri güvenli hale getirme

Birçok tetikleyici ve eylemin, girdileri, çıkışları veya her ikisini de mantıksal uygulamanın çalıştırma geçmişinden güvenli hale getirmek için ayarları vardır. Bu ayarları kullanarak bu verileri güvenli hale getirmenize yardımcı olması için şu hususları gözden geçirin:

* Bir tetikleyici veya eylemde girişleri veya çıkışları gizlemeniz durumunda Logic Apps, güvenli verileri Azure Log Analytics 'a göndermez. Ayrıca, izleme için bu tetikleyiciye veya eyleme [izlenen Özellikler](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data) ekleyemezsiniz.

* [İş akışı geçmişini işlemek için Logic Apps API 'si](/rest/api/logic/) güvenli çıktılar döndürmez.

* Girişleri çıkardığı veya çıkışları açıkça çıkardığı bir eylemden çıkışları güvenli hale getirmek için bu eylemde el ile **güvenli çıktılar** açın.

* Açık **girişleri** veya **güvenli çıkışları** , çalışma geçmişinin verileri gizleyebileceği şekilde bekleyen aşağı akış eylemlerinde etkinleştirdiğinizden emin olun.

  **Güvenli çıkışlar ayarı**

  Bir tetikleyici veya eylemde **güvenli çıktıları** el ile açtığınızda, Logic Apps çalıştırma geçmişinde bu çıktıları gizler. Bir aşağı akış eylemi açık olarak bu güvenli çıkışları giriş olarak kullanıyorsa Logic Apps, bu eylemin çalışma geçmişinde girişlerini gizler, ancak eylemin **güvenli girişler** ayarını *etkinleştirmez* .

  ![Birçok eylemin giriş ve aşağı akış etkisi olarak güvenli çıktılar](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  Oluşturma, ayrıştırma JSON ve yanıt eylemlerinin yalnızca **güvenli girişler** ayarı vardır. Açık olduğunda, ayar bu eylemlerin çıkışlarını da gizler. Bu eylemler, yukarı akış güvenli çıkışlarını giriş olarak açıkça kullanıyorsa, Logic Apps bu eylemlerin girişlerini ve çıkışlarını gizler, ancak bu eylemlerin **güvenli girişler** ayarını *etkinleştirmez* . Bir aşağı akış eylemi, giriş olarak oluşturma, ayrıştırma JSON veya Yanıt eylemlerdeki gizli çıkışları açıkça kullanıyorsa, Logic Apps *Bu aşağı akış eyleminin giriş veya çıkışlarını gizlemez*.

  ![Belirli eylemlerdeki aşağı akış etkisi olan giriş olarak güvenli çıktılar](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Güvenli girişler ayarı**

  Bir tetikleyici veya eylemde **güvenli girişleri** el ile açtığınızda Logic Apps, bu girdileri çalıştırma geçmişinde gizler. Bir aşağı akış eylemi, giriş olarak bu tetikleyici veya eylemden görünür çıkışları açıkça kullanıyorsa, Logic Apps bu aşağı akış eyleminin çalıştırma geçmişindeki girişlerini gizler, ancak bu eylemde **güvenli girişleri** *etkinleştirmez* ve bu eylemin çıktılarını gizlemez.

  ![Birçok eylemin güvenli girişler ve aşağı akış etkileri](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Oluşturma, ayrıştırma JSON ve yanıt eylemleri, tetikleyici veya eylemden güvenli girişler içeren görünür çıktıları açıkça kullanıyorsa, Logic Apps bu eylemlerin girişlerini ve çıkışlarını gizler, ancak bu eylemin **güvenli girişler** ayarını *etkinleştirmez* . Bir aşağı akış eylemi, giriş olarak oluşturma, ayrıştırma JSON veya Yanıt eylemlerdeki gizli çıkışları açıkça kullanıyorsa, Logic Apps *Bu aşağı akış eyleminin giriş veya çıkışlarını gizlemez*.

  ![Belirli eylemlerdeki güvenli girişler ve aşağı akış etkisi](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>Tasarımcıda güvenli girişler ve çıktılar

1. [Azure Portal](https://portal.azure.com)mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

   ![Mantıksal uygulama Tasarımcısı 'nda mantıksal uygulama açma](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. Hassas verileri güvenli hale getirmek istediğiniz tetikleyici veya eylemde, üç nokta (**...**) düğmesini ve ardından **Ayarlar**' ı seçin.

   ![Tetikleyici veya eylem ayarlarını aç](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. **Güvenli girişleri**, **güvenli çıktıları** veya her ikisini açın. İşiniz bittiğinde **Bitti**'yi seçin.

   !["Güvenli girişler" veya "güvenli çıktılar" i açın](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   Eylem veya tetikleyici artık başlık çubuğunda bir kilit simgesi gösterir.

   ![Eylem veya tetikleyici başlık çubuğu kilit simgesini gösterir](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   Önceki eylemlerden gelen güvenli çıkışları temsil eden belirteçler de kilit simgeleri gösterir. Örneğin, bir eylemde kullanmak üzere dinamik içerik listesinden böyle bir çıktı seçtiğinizde, bu belirteç bir kilit simgesi gösterir.

   ![Güvenli çıkış için belirteç seçin](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Mantıksal uygulama çalıştıktan sonra, o çalıştırmanın geçmişini görüntüleyebilirsiniz.

   1. Mantıksal uygulamanın **genel bakış** bölmesinde, görüntülemek istediğiniz çalıştırmayı seçin.

   1. **Mantıksal uygulama çalıştırma** bölmesinde, gözden geçirmek istediğiniz eylemleri genişletin.

      Hem giriş hem de çıkışları gizlemeyi seçtiyseniz, bu değerler artık gizli olarak görünür.

      ![Çalışma geçmişinde gizli girişler ve çıkışlar](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>Kod görünümünde güvenli girişler ve çıktılar

Temel tetikleyici veya eylem tanımında `runtimeConfiguration.secureData.properties` diziyi bu değerlerden birini ya da her ikisini birden ekleyin veya güncelleştirin:

* `"inputs"`: Çalıştırma geçmişindeki girişlerin güvenliğini sağlar.
* `"outputs"`: Çalıştırma geçmişindeki çıkışların güvenliğini sağlar.

```json
"<trigger-or-action-name>": {
   "type": "<trigger-or-action-type>",
   "inputs": {
      <trigger-or-action-inputs>
   },
   "runtimeConfiguration": {
      "secureData": {
         "properties": [
            "inputs",
            "outputs"
         ]
      }
   },
   <other-attributes>
}
```

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Parametre girdilerine erişim

Farklı ortamlara dağıtırsanız, iş akışı tanımınızdaki bu ortamlara göre farklılık gösteren değerleri parametreleştirmeyi göz önünde bulundurun. Bu şekilde, mantıksal uygulamanızı dağıtmak, güvenli parametreleri tanımlayarak gizli verileri korumak ve bir [parametre dosyası](../azure-resource-manager/templates/parameter-files.md)kullanarak bu verileri [şablon parametreleri](../azure-resource-manager/templates/template-parameters.md) aracılığıyla ayrı girişler olarak geçirmek için [Azure Resource Manager şablonu](../azure-resource-manager/templates/overview.md) kullanarak sabit kodlanmış verilerden kaçınabilirsiniz.

Örneğin, [Azure Active Directory açık kimlik doğrulaması](#azure-active-directory-oauth-authentication) (Azure AD OAuth) ile http eylemlerinin kimliğini doğruladıysanız, kimlik doğrulaması için kullanılan istemci kimliğini ve istemci gizli anahtarını kabul eden parametreleri tanımlayabilir ve gizlemelisiniz. Mantıksal uygulamanızda bu parametreleri tanımlamak için `parameters` mantıksal uygulamanızın iş akışı tanımının ve dağıtım için Kaynak Yöneticisi şablonun bölümünü kullanın. Mantıksal uygulamanızı düzenlenirken veya çalıştırma geçmişini görüntülerken görüntülenmesini istemediğiniz parametre değerlerinin güvenli hale getirilmesine yardımcı olmak için, veya türünü kullanarak parametreleri tanımlayın `securestring` `secureobject` ve kodlamayı gereken şekilde kullanın. Bu türe sahip parametreler kaynak tanımıyla döndürülmez ve dağıtımdan sonra kaynak görüntülenirken erişilebilir değildir. Çalışma zamanı sırasında bu parametre değerlerine erişmek için, `@parameters('<parameter-name>')` iş akışı tanımınızın içindeki ifadeyi kullanın. Bu ifade yalnızca çalışma zamanında değerlendirilir ve [Iş akışı Tanım Dili](../logic-apps/logic-apps-workflow-definition-language.md)tarafından açıklanmıştır.

> [!NOTE]
> İstek üstbilgisinde veya gövdede bir parametre kullanırsanız, mantıksal uygulamanızın çalıştırma geçmişini ve giden HTTP isteğini görüntülediğinizde bu parametre görülebilir. İçerik erişim ilkelerinizi da uygun şekilde ayarladığınızdan emin olun.
> Ayrıca, çalıştırma geçmişinizdeki girdileri ve çıkışları gizlemek için [gizleme](#obfuscate) de kullanabilirsiniz. Yetkilendirme üstbilgileri, girişler veya çıktılar aracılığıyla hiçbir şekilde görünmez. Bu nedenle, bir gizli dizi kullanılırsa gizli anahtar alınmaz.

Daha fazla bilgi için bu konudaki aşağıdaki bölümlere bakın:

* [İş akışı tanımlarında güvenli parametreler](#secure-parameters-workflow)
* [Gizleme kullanarak çalıştırma geçmişindeki verileri güvenli hale getirme](#obfuscate)

[Mantıksal uygulamalar için dağıtımı Kaynak Yöneticisi şablonları kullanarak otomatikleştirmeniz](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)durumunda, ve türlerini kullanarak dağıtımda değerlendirilen güvenli [şablon parametrelerini](../azure-resource-manager/templates/template-parameters.md)tanımlayabilirsiniz `securestring` `secureobject` . Şablon parametrelerini tanımlamak için, şablonunuzun en üst düzey bölümünü kullanın `parameters` , bu, iş akışı tanımınızdan ayrı ve farklı olan bölümdür `parameters` . Şablon parametrelerinin değerlerini sağlamak için ayrı bir [parametre dosyası](../azure-resource-manager/templates/parameter-files.md)kullanın.

Örneğin, gizli dizileri kullanıyorsanız, bu gizli dizileri dağıtım [Azure Key Vault](../key-vault/general/overview.md) ' den alan güvenli şablon parametrelerini tanımlayabilir ve kullanabilirsiniz. Daha sonra, parametre dosyanızdaki anahtar kasasına ve gizli dizi ile gizli dizi oluşturabilirsiniz. Daha fazla bilgi için şu konulara bakın:

* [Azure Key Vault kullanarak dağıtımda hassas değerler geçirin](../azure-resource-manager/templates/key-vault-parameter.md)
* Bu konunun ilerleyen kısımlarında [Azure Resource Manager şablonlarda güvenli parametreler](#secure-parameters-deployment-template)

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>İş akışı tanımlarında güvenli parametreler

Mantıksal uygulamanızın iş akışı tanımındaki hassas bilgileri korumak için, mantıksal uygulamanızı kaydettikten sonra bu bilgilerin görünür olmaması için güvenli parametreleri kullanın. Örneğin, bir HTTP eyleminin, bir Kullanıcı adı ve parola kullanan temel kimlik doğrulaması gerektirdiğini varsayalım. İş akışı tanımında, `parameters` bölümü, `basicAuthPasswordParam` `basicAuthUsernameParam` türünü kullanarak ve parametrelerini tanımlar `securestring` . Sonra eylem tanımı, bölümünde bu parametrelere başvurur `authentication` .

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('basicAuthUsernameParam')",
               "password": "@parameters('basicAuthPasswordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "basicAuthPasswordParam": {
         "type": "securestring"
      },
      "basicAuthUsernameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Azure Resource Manager şablonlarda güvenli parametreler

Mantıksal uygulama için bir [Kaynak Yöneticisi şablonu](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) birden çok `parameters` bölüme sahiptir. Parolaları, anahtarları, sırları ve diğer hassas bilgileri korumak için, veya türünü kullanarak şablon düzeyinde ve iş akışı tanımı düzeyinde güvenli parametreleri tanımlayın `securestring` `secureobject` . Daha sonra bu değerleri [Azure Key Vault](../key-vault/general/overview.md) saklayabilir ve anahtar kasası ve gizli dizi için [parametre dosyasını](../azure-resource-manager/templates/parameter-files.md) kullanabilirsiniz. Şablonunuz daha sonra bu bilgileri dağıtımda alır. Daha fazla bilgi için bkz. [Azure Key Vault kullanarak dağıtımda gizli değerleri geçirme](../azure-resource-manager/templates/key-vault-parameter.md).

Bu bölümler hakkında daha fazla bilgi aşağıda verilmiştir `parameters` :

* Şablonun en üst düzeyinde bir `parameters` bölüm, şablonun *dağıtımda* kullandığı değerler için parametreleri tanımlar. Örneğin, bu değerler belirli bir dağıtım ortamı için bağlantı dizeleri içerebilir. Daha sonra bu değerleri ayrı bir [parametre dosyasında](../azure-resource-manager/templates/parameter-files.md)saklayabilirsiniz, bu da bu değerlerin değiştirilmesini kolaylaştırır.

* Mantıksal uygulamanızın kaynak tanımı içinde, ancak iş akışı tanımınızın dışında, bir `parameters` bölüm iş akışı tanımınızın parametrelerinin değerlerini belirtir. Bu bölümde, şablonunuzun parametrelerine başvuran şablon ifadelerini kullanarak bu değerleri atayabilirsiniz. Bu ifadeler dağıtımda değerlendirilir.

* İş akışı tanımınızın içinde, bir `parameters` bölüm mantıksal uygulamanızın çalışma zamanında kullandığı parametreleri tanımlar. Daha sonra bu parametrelere, çalışma zamanında değerlendirilen iş akışı Tanım ifadelerini kullanarak mantıksal uygulamanızın iş akışının içinde başvurabilirsiniz.

Bu örnek, türü kullanan birden çok güvenli parametre tanımına sahip olan şablon `securestring` :

| Parametre adı | Açıklama |
|----------------|-------------|
| `TemplatePasswordParam` | Daha sonra iş akışı tanımının parametresine geçirilen bir parolayı kabul eden bir şablon parametresi `basicAuthPasswordParam` |
| `TemplateUsernameParam` | Daha sonra iş akışı tanımının parametresine geçirilen bir kullanıcı adını kabul eden bir şablon parametresi `basicAuthUserNameParam` |
| `basicAuthPasswordParam` | HTTP eyleminde temel kimlik doğrulama parolasını kabul eden bir iş akışı Tanım parametresi |
| `basicAuthUserNameParam` | HTTP eyleminde temel kimlik doğrulaması için Kullanıcı adını kabul eden bir iş akışı Tanım parametresi |
|||

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "TemplatePasswordParam": {
         "type": "securestring"
      },
      "TemplateUsernameParam": {
         "type": "securestring"
      },
      "LogicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('basicAuthUsernameParam')",
                           "password": "@parameters('basicAuthPasswordParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "basicAuthPasswordParam": {
                     "type": "securestring"
                  },
                  "basicAuthUsernameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "basicAuthPasswordParam": {
                  "value": "[parameters('TemplatePasswordParam')]"
               },
               "basicAuthUsernameParam": {
                  "value": "[parameters('TemplateUsernameParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-outbound-requests"></a>

## <a name="access-for-outbound-calls-to-other-services-and-systems"></a>Diğer hizmetlere ve sistemlere giden çağrılar için erişim

Hedef uç noktanın özelliğine, [http tetikleyicisi veya http eylemi](../connectors/connectors-native-http.md)tarafından gönderilen giden çağrılar, şifrelemeyi destekler ve daha önce GÜVENLI yuva KATMANı (SSL) olarak bilinen [Aktarım KATMANı güvenliği (TLS) 1,0, 1,1 veya 1,2](https://en.wikipedia.org/wiki/Transport_Layer_Security)ile güvenli hale getirilir. Logic Apps, desteklenen en yüksek olası sürümü kullanarak hedef uç noktayla görüşür. Örneğin, hedef uç nokta 1,2 ' i destekliyorsa, HTTP tetikleyicisi veya eylemi önce 1,2 ' i kullanır. Aksi halde, bağlayıcı desteklenen bir sonraki en yüksek sürümü kullanır.

TLS/SSL otomatik olarak imzalanan sertifikalar hakkında bilgi aşağıda verilmiştir:

* Genel, çok kiracılı Azure ortamındaki Logic Apps için, HTTP Bağlayıcısı kendinden imzalı TLS/SSL sertifikalarına izin vermez. Mantıksal uygulamanız sunucuya HTTP çağrısı yapar ve bir TLS/SSL otomatik olarak imzalanan sertifika sunduğunda, HTTP çağrısı hata vererek başarısız olur `TrustFailure` .

* Bir [tümleştirme hizmeti ortamındaki (ıSE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Logic Apps için http BAĞLAYıCıSı, TLS/SSL el sıkışmaları için otomatik olarak imzalanan sertifikalara izin verir. Ancak, önce Logic Apps REST API kullanarak mevcut bir ıSE veya yeni bir ıSE için [otomatik olarak imzalanan sertifika desteğini etkinleştirmeniz](../logic-apps/create-integration-service-environment-rest-api.md#request-body) ve ortak sertifikayı konuma yüklemeniz gerekir `TrustedRoot` .

Mantıksal uygulamanızdan gönderilen çağrıları işleyen uç noktaların güvenli hale getirilmesine yardımcı olmak için daha fazla yol aşağıda verilmiştir:

* [Giden isteklere kimlik doğrulaması ekleyin](#add-authentication-outbound).

  Giden çağrıları göndermek için HTTP tetikleyicisi veya eylemini kullandığınızda, mantıksal uygulamanız tarafından gönderilen isteğe kimlik doğrulaması ekleyebilirsiniz. Örneğin, şu kimlik doğrulama türlerini seçebilirsiniz:

  * [Temel kimlik doğrulaması](#basic-authentication)

  * [İstemci sertifikası kimlik doğrulaması](#client-certificate-authentication)

  * [OAuth kimlik doğrulamasını Active Directory](#azure-active-directory-oauth-authentication)

  * [Yönetilen kimlik doğrulaması](#managed-identity-authentication)

* Mantıksal uygulama IP adreslerinden erişimi kısıtlayın.

  Mantıksal uygulamalardan gelen uç noktalara yapılan tüm çağrılar, mantıksal uygulamalarınızın bölgelerini temel alan belirli IP adreslerinden kaynaklardır. Yalnızca bu IP adreslerinden istekleri kabul eden filtre ekleyebilirsiniz. Bu IP adreslerini almak için bkz. [Azure Logic Apps Için sınırlar ve yapılandırma](logic-apps-limits-and-config.md#firewall-ip-configuration).

* Şirket içi sistemlerle bağlantı için güvenliği geliştirme.

  Azure Logic Apps, daha güvenli ve güvenilir şirket içi iletişim sağlamaya yardımcı olmak için bu hizmetlerle tümleştirme sağlar.

  * Şirket içi veri ağ geçidi

    Azure Logic Apps ' de birçok yönetilen bağlayıcı, dosya sistemi, SQL, SharePoint ve DB2 gibi şirket içi sistemlere güvenli bağlantıları kolaylaştırır. Ağ Geçidi, Azure Service Bus aracılığıyla şifrelenmiş kanallardaki şirket içi kaynaklardaki verileri gönderir. Tüm trafik ağ geçidi aracısından güvenli giden trafik olarak gelir. [Şirket içi veri ağ geçidinin nasıl çalıştığını](logic-apps-gateway-install.md#gateway-cloud-service)öğrenin.

  * Azure API Management ile bağlanma

    [Azure API Management](../api-management/api-management-key-concepts.md) , siteden siteye sanal özel ağ ve güvenli proxy Için [ExpressRoute](../expressroute/expressroute-introduction.md) tümleştirmesi ve şirket içi sistemlerle iletişim gibi şirket içi bağlantı seçenekleri sağlar. Şirket içi sisteminize erişim sağlayan bir API 'niz varsa ve bir [API Management hizmet örneği](../api-management/get-started-create-service-instance.md)oluşturarak bu API 'yi kullanıma sundıysanız, Logic App Designer 'daki yerleşik API Management tetikleyiciyi veya eylemini seçerek mantıksal uygulamanızın iş AKıŞıNDA bu API 'yi çağırabilirsiniz.

    > [!NOTE]
    > Bağlayıcı yalnızca görüntüleme ve bağlanma izninizin olduğu API Management Hizmetleri gösterir, ancak tüketim tabanlı API Management hizmetlerini göstermez.

    1. Mantıksal uygulama Tasarımcısı ' nda, `api management` Arama kutusuna girin. Bir tetikleyici mi yoksa eylem mi eklemeyeceğinizi temel alarak adımı seçin:<p>

       * İş akışınızın her zaman ilk adımı olan bir tetikleyici ekliyorsanız, **Azure API Management tetikleyicisi Seç**' i seçin.

       * Bir eylem ekliyorsanız, **Azure API Management eylemi Seç**' i seçin.

       Bu örnek, bir tetikleyici ekler:

       ![Azure API Management tetikleyicisi ekleme](./media/logic-apps-securing-a-logic-app/select-api-management.png)

    1. Daha önce oluşturduğunuz API Management hizmeti örneğinizi seçin.

       ![API Management hizmet örneği seçin](./media/logic-apps-securing-a-logic-app/select-api-management-service-instance.png)

    1. Kullanılacak API çağrısını seçin.

       ![Mevcut API 'YI seçin](./media/logic-apps-securing-a-logic-app/select-api.png)

<a name="add-authentication-outbound"></a>

### <a name="add-authentication-to-outbound-calls"></a>Giden çağrılara kimlik doğrulama ekleme

HTTP ve HTTPS uç noktaları çeşitli kimlik doğrulama türlerini destekler. Bu uç noktalara giden çağrılar veya istekler göndermek için kullandığınız bazı Tetikleyiciler ve eylemler üzerinde bir kimlik doğrulama türü belirtebilirsiniz. Mantıksal uygulama tasarımcısında, bir kimlik doğrulama türü seçmeyi destekleyen Tetikleyiciler ve eylemler bir **kimlik doğrulama** özelliğine sahiptir. Ancak, bu özellik her zaman varsayılan olarak görünmeyebilir. Bu durumlarda, tetikleyici veya eylemde, **yeni parametre Ekle** listesini açın ve **kimlik doğrulaması**' nı seçin.

> [!IMPORTANT]
> Mantıksal uygulamanızın işleyeceği hassas bilgileri korumak için, güvenli parametreleri kullanın ve verileri gerektiği şekilde kodlayın.
> Parametreleri kullanma ve güvenliğini sağlama hakkında daha fazla bilgi için bkz. [parametre girdilerine erişim](#secure-action-parameters).

<a name="authentication-types-supported-triggers-actions"></a>

#### <a name="authentication-types-for-triggers-and-actions-that-support-authentication"></a>Kimlik doğrulamasını destekleyen Tetikleyiciler ve eylemler için kimlik doğrulama türleri

Bu tabloda, bir kimlik doğrulama türünü seçebileceğiniz Tetikleyiciler ve eylemlerde kullanılabilir olan kimlik doğrulama türleri tanımlanmaktadır:

| Kimlik doğrulaması türü | Desteklenen Tetikleyiciler ve eylemler |
|---------------------|--------------------------------|
| [Temel](#basic-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, HTTP Web kancası |
| [İstemci sertifikası](#client-certificate-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, HTTP Web kancası |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API Management, Azure Uygulama Hizmetleri, Azure Işlevleri, HTTP, HTTP + Swagger, HTTP Web kancası |
| [Ham](#raw-authentication) | Azure API Management, Azure Uygulama Hizmetleri, Azure Işlevleri, HTTP, HTTP + Swagger, HTTP Web kancası |
| [Yönetilen kimlik](#managed-identity-authentication) | **Yerleşik Tetikleyiciler ve eylemler** <p><p>Azure API Management, Azure Uygulama Hizmetleri, Azure Işlevleri, HTTP, HTTP Web kancası <p><p>**Yönetilen bağlayıcılar** <p><p>Azure AD Kimlik Koruması, Azure Otomasyonu, Azure Container Instance, Azure Veri Gezgini, Azure Data Factory, Azure Data Lake, Azure Event Grid, Azure IoT Central v3, Azure Key Vault, Azure Resource Manager, Azure Sentinel, Azure AD ile HTTP <p><p>**Note**: yönetilen bağlayıcılar için destek şu anda önizlemededir. |
|||

<a name="basic-authentication"></a>

#### <a name="basic-authentication"></a>Temel kimlik doğrulama

[Temel](../active-directory-b2c/secure-rest-api.md) seçenek varsa, bu özellik değerlerini belirtin:

| Özellik (Tasarımcı) | Özellik (JSON) | Gerekli | Değer | Açıklama |
|---------------------|-----------------|----------|-------|-------------|
| **Kimlik Doğrulaması** | `type` | Yes | Temel | Kullanılacak kimlik doğrulaması türü |
| **Kullanıcı adı** | `username` | Yes | <*Kullanıcı adı*>| Hedef hizmet uç noktasına erişim doğrulaması için Kullanıcı adı |
| **Parola** | `password` | Yes | <*parolayı*> | Hedef hizmet uç noktasına erişim doğrulaması için parola |
||||||

Gizli bilgileri işlemek ve güvenli hale getirmek için [güvenli parametreleri](#secure-action-parameters) kullandığınızda (örneğin, [dağıtımı otomatikleştirmek için bir Azure Resource Manager şablonunda](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)), çalışma zamanında bu parametre değerlerine erişmek için ifadeleri kullanabilirsiniz. Bu örnek HTTP eylemi tanımı, kimlik doğrulamasını `type` olarak belirtir `Basic` ve parametre değerlerini almak için [Parameters () işlevini](../logic-apps/workflow-definition-language-functions-reference.md#parameters) kullanır:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

<a name="client-certificate-authentication"></a>

#### <a name="client-certificate-authentication"></a>İstemci sertifikası kimlik doğrulaması

[Istemci sertifikası](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) seçeneği varsa, bu özellik değerlerini belirtin:

| Özellik (Tasarımcı) | Özellik (JSON) | Gerekli | Değer | Açıklama |
|---------------------|-----------------|----------|-------|-------------|
| **Kimlik Doğrulaması** | `type` | Yes | **İstemci sertifikası** <br>veya <br>`ClientCertificate` | Kullanılacak kimlik doğrulaması türü. Sertifikaları [Azure API Management](../api-management/api-management-howto-mutual-certificates.md)yönetebilirsiniz. <p></p>**Note**: özel bağlayıcılar hem gelen hem de giden çağrılar için sertifika tabanlı kimlik doğrulamasını desteklemez. |
| **Türk** | `pfx` | Yes | <*kodlanmış-pfx-dosya-içerik*> | Kişisel bilgi değişimi (PFX) dosyasından gelen Base64 kodlamalı içerik <p><p>PFX dosyasını Base64 kodlamalı biçime dönüştürmek için aşağıdaki adımları izleyerek PowerShell kullanabilirsiniz: <p>1. sertifika içeriğini bir değişkene kaydedin: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. işlevi kullanarak sertifika içeriğini dönüştürün `ToBase64String()` ve bu içeriği bir metin dosyasına kaydedin: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` <p><p>**Sorun giderme**: `cert mmc/PowerShell` komutunu kullanırsanız, şu hatayı alabilirsiniz: <p><p>`Could not load the certificate private key. Please check the authentication certificate password is correct and try again.` <p><p>Bu hatayı çözmek için PFX dosyasını bir ped dosyasına dönüştürmeyi ve komutunu kullanarak yeniden geri dönüştürmeyi deneyin `openssl` : <p><p>`openssl pkcs12 -in certificate.pfx -out certificate.pem` <br>`openssl pkcs12 -in certificate.pem -export -out certificate2.pfx` <p><p>Daha sonra, sertifikanın yeni dönüştürülen PFX dosyası için Base64 kodlamalı dizeyi aldığınızda, dize artık Azure Logic Apps ' de çalışmaktadır. |
| **Parola** | `password`| No | <*-pfx dosyası için parola*> | PFX dosyasına erişim parolası |
|||||

Gizli bilgileri işlemek ve güvenli hale getirmek için [güvenli parametreleri](#secure-action-parameters) kullandığınızda (örneğin, [dağıtımı otomatikleştirmek için bir Azure Resource Manager şablonunda](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)), çalışma zamanında bu parametre değerlerine erişmek için ifadeleri kullanabilirsiniz. Bu örnek HTTP eylemi tanımı, kimlik doğrulamasını `type` olarak belirtir `ClientCertificate` ve parametre değerlerini almak için [Parameters () işlevini](../logic-apps/workflow-definition-language-functions-reference.md#parameters) kullanır:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

İstemci sertifikası kimlik doğrulamasını kullanarak hizmetlerin güvenliğini sağlama hakkında daha fazla bilgi için şu konulara bakın:

* [Azure API Management istemci sertifikası kimlik doğrulaması kullanarak API 'Leri güvenliği geliştirme](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Azure API Management istemci sertifikası kimlik doğrulaması kullanarak arka uç hizmetleri için güvenliği geliştirme](../api-management/api-management-howto-mutual-certificates.md)
* [İstemci sertifikalarını kullanarak, yeniden takip eden hizmetiniz için güvenliği geliştirme](../active-directory-b2c/secure-rest-api.md)
* [Uygulama kimlik doğrulaması için sertifika kimlik bilgileri](../active-directory/develop/active-directory-certificate-credentials.md)
* [Azure App Service'te kodunuzda TLS/SSL sertifikası kullanma](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

#### <a name="azure-active-directory-open-authentication"></a>Azure Active Directory açma kimlik doğrulaması

Istek Tetikleyicileri üzerinde, mantıksal uygulamanız için [Azure AD yetkilendirme ilkeleri ayarladıktan](#enable-oauth) sonra gelen çağrıların kimliğini doğrulamak Için [Azure Active Directory açma kimlik doğrulaması (Azure AD OAuth)](../active-directory/develop/index.yml)kullanabilirsiniz. Seçmeniz için **Active Directory OAuth** kimlik doğrulaması türünü sağlayan diğer tüm tetikleyiciler ve eylemler için şu özellik değerlerini belirtin:

| Özellik (Tasarımcı) | Özellik (JSON) | Gerekli | Değer | Açıklama |
|---------------------|-----------------|----------|-------|-------------|
| **Kimlik Doğrulaması** | `type` | Yes | **Active Directory OAuth** <br>veya <br>`ActiveDirectoryOAuth` | Kullanılacak kimlik doğrulaması türü. Logic Apps Şu anda [OAuth 2,0 protokolünü](../active-directory/develop/v2-overview.md)izler. |
| **Yetkili** | `authority` | No | <*URL-for-Authority-Token-Issuer*> | Erişim belirtecini sağlayan yetkilinin URL 'SI. Varsayılan olarak, bu değer `https://login.windows.net` . |
| **Kiracı** | `tenant` | Yes | <*Kiracı KIMLIĞI*> | Azure AD kiracısı için kiracı KIMLIĞI |
| **Hedef kitle** | `audience` | Yes | <*kaynaktan yetkilendirme*> | Yetkilendirme için kullanmak istediğiniz kaynak (örneğin, `https://management.core.windows.net/` |
| **İstemci Kimliği** | `clientId` | Yes | <*istemci KIMLIĞI*> | Yetkilendirme isteyen uygulamanın istemci KIMLIĞI |
| **Kimlik bilgisi türü** | `credentialType` | Yes | Sertifika <br>veya <br>Gizli dizi | İstemcinin yetkilendirme istemek için kullandığı kimlik bilgisi türü. Bu özellik ve değer mantıksal uygulamanızın temel tanımında görünmez, ancak seçilen kimlik bilgisi türü için görüntülenen özellikleri belirler. |
| **Gizlilikle** | `secret` | Evet, ancak yalnızca "gizli" kimlik bilgisi türü için | <*istemci parolası*> | Yetkilendirme isteğinde bulunan istemci parolası |
| **Türk** | `pfx` | Evet, ancak yalnızca "sertifika" kimlik bilgisi türü için | <*kodlanmış-pfx-dosya-içerik*> | Kişisel bilgi değişimi (PFX) dosyasından gelen Base64 kodlamalı içerik |
| **Parola** | `password` | Evet, ancak yalnızca "sertifika" kimlik bilgisi türü için | <*-pfx dosyası için parola*> | PFX dosyasına erişim parolası |
|||||

Gizli bilgileri işlemek ve güvenli hale getirmek için [güvenli parametreleri](#secure-action-parameters) kullandığınızda (örneğin, [dağıtımı otomatikleştirmek için bir Azure Resource Manager şablonunda](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)), çalışma zamanında bu parametre değerlerine erişmek için ifadeleri kullanabilirsiniz. Bu örnek HTTP eylemi tanımı, kimlik `type` `ActiveDirectoryOAuth` bilgisini olarak kimlik bilgisi türü olarak belirtir `Secret` ve parametre değerlerini almak için [Parameters () işlevini](../logic-apps/workflow-definition-language-functions-reference.md#parameters) kullanır:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "@parameters('tenantIdParam')",
         "audience": "https://management.core.windows.net/",
         "clientId": "@parameters('clientIdParam')",
         "credentialType": "Secret",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

<a name="raw-authentication"></a>

#### <a name="raw-authentication"></a>Ham kimlik doğrulaması

**Ham** seçenek kullanılabiliyorsa, [OAuth 2,0 protokolünü](https://oauth.net/2/)izleyen [kimlik doğrulama düzenlerini](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) kullanmanız gerektiğinde bu kimlik doğrulama türünü kullanabilirsiniz. Bu tür ile, giden istekle birlikte göndereceğiniz yetkilendirme üstbilgi değerini el ile oluşturursunuz ve bu üst bilgi değerini Tetikleyiciniz veya eyleminiz içinde belirtirsiniz.

Örneğin, [OAuth 1,0 protokolünü](https://tools.ietf.org/html/rfc5849)IZLEYEN bir https isteği için örnek üst bilgi aşağıda verilmiştir:

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

Ham kimlik doğrulamasını destekleyen tetikleyici veya eylemde, bu özellik değerlerini belirtin:

| Özellik (Tasarımcı) | Özellik (JSON) | Gerekli | Değer | Açıklama |
|---------------------|-----------------|----------|-------|-------------|
| **Kimlik Doğrulaması** | `type` | Yes | Ham | Kullanılacak kimlik doğrulaması türü |
| **Değer** | `value` | Yes | <*Yetkilendirme-üst bilgi-değer*> | Kimlik doğrulaması için kullanılacak yetkilendirme üst bilgisi değeri |
||||||

Gizli bilgileri işlemek ve güvenli hale getirmek için [güvenli parametreleri](#secure-action-parameters) kullandığınızda (örneğin, [dağıtımı otomatikleştirmek için bir Azure Resource Manager şablonunda](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)), çalışma zamanında bu parametre değerlerine erişmek için ifadeleri kullanabilirsiniz. Bu örnek HTTP eylemi tanımı, kimlik doğrulamasını `type` olarak belirtir `Raw` ve parametre değerlerini almak için [Parameters () işlevini](../logic-apps/workflow-definition-language-functions-reference.md#parameters) kullanır:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Raw",
         "value": "@parameters('authHeaderParam')"
      }
   },
   "runAfter": {}
}
```

<a name="managed-identity-authentication"></a>

#### <a name="managed-identity-authentication"></a>Yönetilen kimlik doğrulaması

Yönetilen kimlik [kimlik doğrulamasını destekleyen tetikleyici veya eylemde](#add-authentication-outbound) [yönetilen kimlik](../active-directory/managed-identities-azure-resources/overview.md) doğrulama seçeneği kullanılabilir olduğunda, mantıksal uygulamanız, kimlik bilgileri, gizlilikler veya azure ad belirteçleri yerıne Azure Active Directory (Azure AD) tarafından korunan Azure kaynaklarına erişimi kimlik doğrulaması için sistem tarafından atanan kimliği veya *tek* el ile oluşturulmuş bir kimliği kullanabilir. Azure, bu kimliği sizin için yönetir ve gizli dizileri yönetmediğinden veya doğrudan Azure AD belirteçlerini kullanmıyorsanız, kimlik bilgilerinizi güvenli hale getirmeye yardımcı olur. [Azure AD kimlik doğrulaması için yönetilen kimlikleri destekleyen Azure hizmetleri](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)hakkında daha fazla bilgi edinin.

1. Mantıksal uygulamanızın yönetilen bir kimlik kullanabilmesi [için Azure Logic Apps ' deki yönetilen kimlikleri kullanarak Azure kaynaklarına erişimi kimlik doğrulama](../logic-apps/create-managed-service-identity.md)bölümündeki adımları izleyin. Bu adımlar, mantıksal uygulamanızda yönetilen kimliği etkinleştirir ve bu kimliğin hedef Azure kaynağına erişimini ayarlar.

1. Bir Azure işlevinin yönetilen bir kimlik kullanabilmesi [için önce Azure işlevleri için kimlik doğrulamasını etkinleştirin](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-functions).

1. Yönetilen bir kimlik kullanmayı destekleyen tetikleyici veya eylemde, şu bilgileri sağlayın:

   **Yerleşik Tetikleyiciler ve eylemler**

   | Özellik (Tasarımcı) | Özellik (JSON) | Gerekli | Değer | Açıklama |
   |---------------------|-----------------|----------|-------|-------------|
   | **Kimlik Doğrulaması** | `type` | Yes | **Yönetilen Kimlik** <br>veya <br>`ManagedServiceIdentity` | Kullanılacak kimlik doğrulaması türü |
   | **Yönetilen Kimlik** | `identity` | Yes | * **Sistem tarafından atanan yönetilen kimlik** <br>veya <br>`SystemAssigned` <p><p>* <*Kullanıcı tarafından atanan kimlik-adı*> | Kullanılacak yönetilen kimlik |
   | **Hedef kitle** | `audience` | Yes | <*hedef-kaynak KIMLIĞI*> | Erişmek istediğiniz hedef kaynağın kaynak KIMLIĞI. <p>Örneğin, `https://storage.azure.com/` tüm depolama hesapları için kimlik doğrulaması için [erişim belirteçlerini](../active-directory/develop/access-tokens.md) geçerli hale getirir. Bununla birlikte, belirli bir depolama hesabı gibi bir kök hizmeti URL 'SI de belirtebilirsiniz `https://fabrikamstorageaccount.blob.core.windows.net` . <p>**Note**: **hedef kitle** özelliği bazı tetikleyicilere veya eylemlere gizlenmiş olabilir. Bu özelliği görünür hale getirmek için tetikleyici veya eylemde, **yeni parametre Ekle** listesini açın ve **hedef kitle**' i seçin. <p><p>**Önemli**: Bu hedef kaynak kimliğinin, tüm gerekli eğik çizgiler de dahil olmak üzere Azure AD 'nin beklediği değerle *tam olarak eşleştiğinden* emin olun. Bu nedenle, `https://storage.azure.com/` tüm Azure Blob depolama hesapları için kaynak kimliği sonunda eğik çizgi gerekir. Ancak, belirli bir depolama hesabının kaynak KIMLIĞI, sonunda eğik çizgi gerektirmez. Bu kaynak kimliklerini bulmak için bkz. [Azure AD 'yi destekleyen Azure hizmetleri](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). |
   |||||

   Gizli bilgileri işlemek ve güvenli hale getirmek için [güvenli parametreleri](#secure-action-parameters) kullandığınızda (örneğin, [dağıtımı otomatikleştirmek için bir Azure Resource Manager şablonunda](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)), çalışma zamanında bu parametre değerlerine erişmek için ifadeleri kullanabilirsiniz. Örneğin, bu HTTP eylemi tanımı kimlik doğrulamasını olarak belirtir `type` `ManagedServiceIdentity` ve parametre değerlerini almak için [Parameters () işlevini](../logic-apps/workflow-definition-language-functions-reference.md#parameters) kullanır:

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "identity": "SystemAssigned",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

   **Yönetilen bağlayıcı Tetikleyicileri ve eylemleri**

   | Özellik (Tasarımcı) | Gerekli | Değer | Açıklama |
   |---------------------|----------|-------|-------------|
   | **Bağlantı adı** | Yes | <*bağlantı adı*> ||
   | **Yönetilen kimlik** | Yes | **Sistem tarafından atanan yönetilen kimlik** <br>veya <br> <*Kullanıcı tarafından atanan-yönetilen kimlik-adı*> | Kullanılacak kimlik doğrulaması türü |
   |||||


<a name="block-connections"></a>

## <a name="block-creating-connections"></a>Bağlantı oluşturmayı engelle

Kuruluşunuz Azure Logic Apps bağlayıcılarını kullanarak belirli kaynaklara bağlanmasına izin vermezse, [Azure ilkesi](../governance/policy/overview.md)kullanarak mantıksal uygulama iş akışlarında belirli bağlayıcılar için [Bu bağlantıları oluşturma özelliğini engelleyebilirsiniz](../logic-apps/block-connections-connectors.md) . Daha fazla bilgi için bkz. [Azure Logic Apps içindeki belirli bağlayıcılar tarafından oluşturulan bağlantıları engelleyin](../logic-apps/block-connections-connectors.md).

<a name="isolation-logic-apps"></a>

## <a name="isolation-guidance-for-logic-apps"></a>Logic Apps için yalıtım Kılavuzu

[Azure Kamu 'da,](../azure-government/documentation-government-welcome.md) [Azure Kamu etki düzeyi 5 yalıtım Kılavuzu](../azure-government/documentation-government-impact-level-5.md#azure-logic-apps) ve [ABD Savunma bulut bilgi işlem güvenlik gereksinimleri Kılavuzu (SRG)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/SRG/index.html)tarafından açıklanan bölgelerde tüm etki düzeylerini destekleyen Azure Logic Apps kullanabilirsiniz. Bu gereksinimleri karşılamak için Logic Apps, mantıksal uygulamalarınızda diğer Azure kiracılarının performans etkisini azaltmak ve diğer kiracılarla bilgi işlem kaynaklarını paylaşmayı önlemek için adanmış kaynaklarla bir ortamda iş akışları oluşturma ve çalıştırma yeteneğini destekler.

* Kendi kodunuzu çalıştırmak veya XML dönüşümü gerçekleştirmek için, [satır içi kod özelliğini](../logic-apps/logic-apps-add-run-inline-code.md) kullanmak yerine [bir Azure işlevi oluşturun ve çağırın](../logic-apps/logic-apps-azure-functions.md)ya da sırasıyla [haritalar olarak kullanılacak derlemeler](../logic-apps/logic-apps-enterprise-integration-maps.md)sağlayın. Ayrıca, yalıtım gereksinimlerinize uymak üzere işlev uygulamanız için barındırma ortamı ayarlayın.

  Örneğin, etkisi düzeyi 5 gereksinimlerini karşılamak için, yalıtılmış fiyatlandırma katmanını kullanan [App Service planı](../azure-functions/dedicated-plan.md) ile işlev uygulamanızı, **yalıtılmış** fiyatlandırma katmanını da kullanan bir [App Service ortamı (Ao)](../app-service/environment/intro.md) [  ile birlikte oluşturun](../app-service/overview-hosting-plans.md) . Bu ortamda, işlev uygulamaları, özel Azure sanal makineleri ve adanmış Azure sanal ağları üzerinde çalışır ve bu, uygulamalarınız için işlem yalıtımı ve en yüksek genişleme özellikleri üzerinde ağ yalıtımı sağlar. Daha fazla bilgi için bkz. [Azure Kamu etki düzeyi 5 yalıtım kılavuzu-Azure işlevleri](../azure-government/documentation-government-impact-level-5.md#azure-functions).

  Daha fazla bilgi için şu konulara bakın:<p>

  * [Azure App Service planları](../app-service/overview-hosting-plans.md)
  * [Azure İşlevleri ağ seçenekleri](../azure-functions/functions-networking-options.md)
  * [Sanal makineler için Azure ayrılmış Konakları](../virtual-machines/dedicated-hosts.md)
  * [Azure 'da sanal makine yalıtımı](../virtual-machines/isolation.md)
  * [Adanmış Azure hizmetlerini sanal ağlara dağıtma](../virtual-network/virtual-network-for-azure-services.md)

* Adanmış kaynaklar üzerinde çalışan ve bir Azure sanal ağı tarafından korunan kaynaklara erişebilen Logic Apps oluşturmak için bir [tümleştirme hizmeti ortamı (ıSE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)oluşturabilirsiniz.

  * Azure depolama, Azure Cosmos DB veya Azure SQL veritabanı, iş ortağı hizmetleri veya Azure 'da barındırılan müşteri hizmetleri gibi Azure PaaS hizmetlerine erişim sağlamak için bazı Azure sanal ağları özel uç noktalar ([Azure özel bağlantı](../private-link/private-link-overview.md)) kullanır. Mantıksal uygulamalarınızın özel uç noktaları kullanan sanal ağlara erişmesi gerekiyorsa, bu mantık uygulamalarını bir ıSE içinde oluşturmanız, dağıtmanız ve çalıştırmanız gerekir.

  * Azure depolama tarafından kullanılan şifreleme anahtarları üzerinde daha fazla denetim için, [Azure Key Vault](../key-vault/general/overview.md)kullanarak kendi anahtarınızı ayarlayabilir, kullanabilir ve yönetebilirsiniz. Bu özellik "Kendi Anahtarını Getir" (BYOK) olarak da bilinir ve anahtarınız "müşteri tarafından yönetilen anahtar" olarak adlandırılır. Daha fazla bilgi için bkz. [Azure Logic Apps içindeki tümleştirme hizmeti ortamları için bekleyen verileri şifrelemek için müşteri tarafından yönetilen anahtarları ayarlama](../logic-apps/customer-managed-keys-integration-service-environment.md).

Daha fazla bilgi için şu konulara bakın:

* [Azure genel bulutunda yalıtım](../security/fundamentals/isolation-choices.md)
* [Azure 'da yüksek oranda hassas IaaS uygulamalarına yönelik güvenlik](/azure/architecture/reference-architectures/n-tier/high-security-iaas)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Logic Apps için Azure Güvenlik temeli](../logic-apps/security-baseline.md)
* [Azure Logic Apps için dağıtımı otomatikleştirin](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Mantıksal uygulamaları izleme](../logic-apps/monitor-logic-apps-log-analytics.md)
