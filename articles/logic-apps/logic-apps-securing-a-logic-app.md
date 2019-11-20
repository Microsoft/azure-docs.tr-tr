---
title: Güvenli erişim ve veri Azure Logic Apps
description: Girdileri, çıkışları, istek tabanlı Tetikleyicileri, çalıştırma geçmişini, yönetim görevlerini ve Azure Logic Apps içindeki diğer kaynaklara erişimi korumak için güvenlik ekleyin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: c9dfc4ed6fce186fea9474222875a072edb32f59
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084717"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Azure Logic Apps 'da güvenli erişim ve veriler

Azure Logic Apps erişimi denetlemek ve verileri korumak için bu alanlarda güvenliği ayarlayabilirsiniz:

* [İstek tabanlı tetikleyicilere erişim](#secure-triggers)
* [Mantıksal uygulama işlemlerine erişim](#secure-operations)
* [Çalışma geçmişi girdilerine ve çıkışlarına erişim](#secure-run-history)
* [Parametre girdilerine erişim](#secure-action-parameters)
* [Logic Apps 'ten çağrılan hizmetlere ve sistemlere erişim](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>İstek tabanlı tetikleyicilere erişim

Mantıksal uygulamanız istek veya [Web kancası](../connectors/connectors-native-webhook.md) tetikleyicisi gibi gelen çağrıları veya istekleri [alan istek tabanlı](../connectors/connectors-native-reqres.md) bir tetikleyici kullanıyorsa, yalnızca yetkili istemcilerin mantıksal uygulamanızı çağırabilmesi için erişimi sınırlayabilirsiniz. Bir mantıksal uygulama tarafından alınan tüm istekler Güvenli Yuva Katmanı (SSL) protokolü ile şifrelenir ve güvenli hale getirilir.

Bu tetikleyici türüne erişimi güvenli hale getirmek için kullanabileceğiniz yollar şunlardır:

* [Paylaşılan erişim imzaları oluştur](#sas)
* [Gelen IP adreslerini kısıtla](#restrict-inbound-ip-addresses)
* [OAuth veya diğer güvenlik Azure Active Directory ekleyin](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Paylaşılan erişim imzaları oluşturma (SAS)

Bir mantıksal uygulamadaki her istek uç noktası, bitiş noktasının URL 'sinde, bu biçimi izleyen bir [paylaşılan erişim imzasına (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) sahiptir:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Her URL, bu tabloda açıklandığı gibi `sp`, `sv`ve `sig` sorgu parametresini içerir:

| Sorgu parametresi | Açıklama |
|-----------------|-------------|
| `sp` | İzin verilen HTTP yöntemlerinin kullanması için izinleri belirtir. |
| `sv` | İmzayı oluşturmak için kullanılacak SAS sürümünü belirtir. |
| `sig` | Tetikleyiciye erişim kimliğini doğrulamak için kullanılacak imzayı belirtir. Bu imza, tüm URL yollarında ve özelliklerde gizli bir erişim anahtarı ile SHA256 algoritması kullanılarak oluşturulur. Hiçbir koşulda veya yayımlanmadıysa, bu anahtar şifrelenir ve mantıksal uygulamayla birlikte depolanır. Mantıksal uygulamanız yalnızca gizli anahtar ile oluşturulmuş geçerli bir imza içeren Tetikleyicileri yetkilendirir. |
|||

SAS ile erişimin güvenliğini sağlama hakkında daha fazla bilgi için bu konudaki şu bölümlere bakın:

* [Erişim anahtarlarını yeniden oluştur](#access-keys)
* [Süresi dolan geri arama URL 'Leri oluşturma](#expiring-urls)
* [Birincil veya ikincil anahtarla URL oluşturma](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Erişim anahtarlarını yeniden oluştur

Dilediğiniz zaman yeni bir güvenli erişim anahtarı oluşturmak için Azure REST API veya Azure portal kullanın. Eski anahtarı kullanan önceden oluşturulmuş tüm URL 'Ler geçersiz kılınır ve mantıksal uygulamayı tetiklemek için artık yetkilendirmeye sahip olmaz. Yeniden oluşturma işleminden sonra aldığınız URL 'Ler yeni erişim anahtarıyla imzalanır.

1. [Azure Portal](https://portal.azure.com), yeniden oluşturmak istediğiniz anahtarı içeren mantıksal uygulamayı açın.

1. Mantıksal uygulamanın menüsünde, **Ayarlar**altında **erişim anahtarları**' nı seçin.

1. Yeniden oluşturmak istediğiniz anahtarı seçin ve işlemi sona erdirin.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Süresi dolan geri arama URL 'Leri oluşturma

Diğer kişilerle istek tabanlı bir tetikleyici için uç nokta URL 'SI paylaşırsanız, belirli anahtarları kullanan ve sona erme tarihleri olan geri çağırma URL 'Leri oluşturabilirsiniz. Bu şekilde, belirli bir TimeSpan temelinde, mantıksal uygulamanızı tetikleyerek anahtarları sorunsuzca alabilir veya erişimi kısıtlayabilirsiniz. Bir URL için sona erme tarihi belirtmek için [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers)kullanın, örneğin:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

Gövdesinde, bir JSON tarih dizesi kullanarak `NotAfter`özelliğini ekleyin. Bu özellik yalnızca `NotAfter` tarih ve saate kadar geçerli olan bir geri çağırma URL 'SI döndürür.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Birincil veya ikincil gizli anahtar ile URL oluşturma

İstek tabanlı bir tetikleyici için geri çağırma URL 'Leri oluştururken veya listelemeniz durumunda, URL 'YI imzalamak için kullanılacak anahtarı belirtebilirsiniz. Belirli bir anahtarla imzalanmış bir URL oluşturmak için [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers)kullanın, örneğin:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

Gövdede `KeyType` özelliğini `Primary` veya `Secondary`olarak ekleyin. Bu özellik, belirtilen güvenli anahtar tarafından imzalanmış bir URL döndürür.

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Gelen IP adreslerini kısıtla

Paylaşılan erişim Imzası (SAS) ile birlikte, mantıksal uygulamanızı çağırabilen istemcileri özellikle sınırlandırmak isteyebilirsiniz. Örneğin, Azure API Management kullanarak istek uç noktanızı yönetiyorsanız, mantıksal uygulamanızı yalnızca API Management örneği için IP adresinden istekleri kabul edecek şekilde kısıtlayabilirsiniz.

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Azure portal gelen IP aralıklarını kısıtla

1. [Azure Portal](https://portal.azure.com)mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

1. Mantıksal uygulamanızın menüsünde, **Ayarlar**' ın altında, **iş akışı ayarları**' nı seçin.

1. **Erişim denetimi yapılandırması** **ızın verilen gelen IP adreslerine** > , **belirli IP aralıklarını**seçin.

1. **Tetikleyiciler Için IP aralıkları**altında, tetikleyicinin kabul ettiği IP adresi aralıklarını belirtin.

   Geçerli bir IP aralığı şu biçimleri kullanır: *x. x. x. x/x* veya *x. x. x. x-x. x. x.* x

Mantıksal uygulamanızın yalnızca iç içe geçmiş bir mantıksal uygulama olarak tetikleyeceğini istiyorsanız, **Izin verilen gelen IP adresleri** listesinden **yalnızca diğer Logic Apps**' ı seçin. Bu seçenek, mantıksal uygulama kaynağınızın boş bir dizisini yazar. Bu şekilde, yalnızca Logic Apps hizmetinden (üst mantıksal uygulamalar) yapılan çağrılar iç içe geçmiş mantıksal uygulamayı tetikleyebilir.

> [!NOTE]
> IP adresi ne olursa olsun, Azure REST API veya API Management aracılığıyla `/triggers/<trigger-name>/run` kullanarak istek tabanlı tetikleyicisine sahip bir mantıksal uygulamayı çalıştırmaya devam edebilirsiniz. Ancak, bu senaryo Azure REST API için de kimlik doğrulaması gerektirir. Tüm olaylar Azure denetim günlüğünde görüntülenir. Erişim denetim ilkelerini uygun şekilde ayarladığınızdan emin olun.

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Azure Resource Manager şablonunda gelen IP aralıklarını kısıtla

[Mantıksal uygulamalar için dağıtımı Kaynak Yöneticisi şablonları kullanarak otomatikleştirmeniz](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)durumunda, mantıksal uygulamanızın kaynak tanımındaki `triggers` bölümü ile `accessControl` bölümünü kullanarak IP aralıklarını belirtebilirsiniz, örneğin:

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
               "triggers": {
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

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>OAuth veya diğer güvenlik Azure Active Directory ekleyin

Mantıksal uygulamanıza daha fazla Yetkilendirme Protokolü eklemek için [Azure API Management](../api-management/api-management-key-concepts.md) hizmetini kullanmayı göz önünde bulundurun. Bu hizmet, mantıksal uygulamanızı bir API olarak kullanıma sunmanıza yardımcı olur ve herhangi bir uç nokta için zengin izleme, güvenlik, ilke ve belge sağlar. API Management mantıksal uygulamanız için ortak veya özel bir uç nokta sunabilir. Bu uç noktaya erişim yetkisi vermek için, bu uç noktaya erişimi yetkilendirmek üzere OAuth, [istemci sertifikası](#client-certificate-authentication)veya başka güvenlik standartları [Azure Active Directory](#azure-active-directory-oauth-authentication)kullanabilirsiniz. API Management bir istek aldığında, hizmet, isteği mantıksal uygulamanıza gönderir ve aynı zamanda gerekli dönüştürmeleri veya kısıtlamaları da yapar. Mantıksal uygulamanızı yalnızca API Management tetikleyebilmeniz için mantıksal uygulamanızın gelen IP aralığı ayarlarını kullanabilirsiniz.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Mantıksal uygulama işlemlerine erişim

Mantıksal uygulamaları yönetme, düzenleme ve görüntüleme gibi belirli görevleri yalnızca belirli kullanıcıların veya grupların çalıştırmasına izin verebilirsiniz. İzinlerini denetlemek için, Azure aboneliğinizdeki üyelere özelleştirilmiş veya yerleşik roller atayabilmeniz için [Azure rol tabanlı Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md) kullanın:

* [Mantıksal uygulama katılımcısı](../role-based-access-control/built-in-roles.md#logic-app-contributor): mantıksal uygulamaları yönetmenizi sağlar, ancak bunlara erişimi değiştiremezsiniz.

* [Logic App operatörü](../role-based-access-control/built-in-roles.md#logic-app-operator): Logic Apps 'i okumanızı, etkinleştirmenizi ve devre dışı bırakmanızı sağlar, ancak bunları düzenleyemez veya güncelleştiremezsiniz.

Başkalarının mantıksal uygulamanızı değiştirmesini veya silmesini engellemek için [Azure Kaynak kilidi](../azure-resource-manager/resource-group-lock-resources.md)' ni kullanabilirsiniz. Bu özellik başkalarının üretim kaynaklarını değiştirmesini veya silmesini engeller.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Çalışma geçmişi verilerine erişim

Mantıksal uygulama çalıştırma sırasında, Aktarım Katmanı Güvenliği (TLS) ve [bekleyen](../security/fundamentals/encryption-atrest.md)' i kullanarak tüm veriler [geçiş sırasında şifrelenir](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) . Mantıksal uygulamanız çalışmayı bitirdiğinde, her eylem için durum, süre, girişler ve çıkışlarla birlikte çalışan adımlar dahil olmak üzere o çalıştırmaya ilişkin geçmişi görüntüleyebilirsiniz. Bu zengin ayrıntı, mantıksal uygulamanızın nasıl çalıştığı ve ortaya çıkan tüm sorunları gidermeye başlayabileceğiniz hakkında öngörüler sağlar.

Mantıksal uygulamanızın çalıştırma geçmişini görüntülediğinizde Logic Apps erişiminizin kimliğini doğrular ve ardından her bir çalıştırmaya yönelik istekler ve yanıtlara yönelik giriş ve çıkışlara bağlantılar sağlar. Ancak, herhangi bir parolayı, parolayı, anahtarı veya diğer hassas bilgileri işleyen eylemler için başkalarının bu verilere bakmasını ve erişimini engellemek isteyebilirsiniz. Örneğin, mantıksal uygulamanız bir HTTP eyleminin kimlik doğrulaması sırasında kullanmak üzere [Azure Key Vault](../key-vault/key-vault-overview.md) bir gizli dizi alırsa, bu gizli anahtarı görünümden gizlemek istersiniz.

Mantıksal uygulamanızın çalıştırma geçmişinde giriş ve çıkışlara erişimi denetlemek için şu seçeneklere sahipsiniz:

* [IP adresi aralığına göre erişimi kısıtlayın](#restrict-ip).

  Bu seçenek, belirli bir IP adres aralığından gelen isteklere göre çalışma geçmişine erişimi güvenli hale getirmeye olanak sağlar.

* Gizleme [kullanarak çalıştırma geçmişinden verileri gizleyin](#obfuscate).

  Birçok tetikleyici ve eylem içinde, girişlerini, çıkışları veya her ikisini de bir mantıksal uygulamanın çalıştırma geçmişinden gizleyebilirsiniz.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>IP adresi aralığına göre erişimi kısıtla

Yalnızca belirli IP adresi aralıklarından gelen isteklerin bu verileri görüntülemesi için mantıksal uygulamanızın çalıştırma geçmişinde giriş ve çıkışlara erişimi sınırlayabilirsiniz. Örneğin, herhangi bir kişinin girişlere ve çıkışlara erişmesini engellemek için `0.0.0.0-0.0.0.0`gibi bir IP adresi aralığı belirtin. Yalnızca yönetici izinlerine sahip bir kişi, mantıksal uygulamanızın verilerine "tam zamanında" erişim olanağı sunan bu kısıtlamayı kaldırabilir. Azure portal veya mantıksal uygulama dağıtımı için kullandığınız bir Azure Resource Manager şablonunda kısıtlamak için IP aralıklarını belirtebilirsiniz.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Azure portal IP aralıklarını kısıtla

1. Azure portal mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

1. Mantıksal uygulamanızın menüsünde, **Ayarlar**' ın altında, **iş akışı ayarları**' nı seçin.

1. **Erişim denetimi yapılandırması** **ızın verilen gelen IP adreslerine** > , **belirli IP aralıklarını**seçin.

1. İçerik **Için IP aralıkları**altında girişler ve çıkışlardan IÇERIĞE erişebilen IP adresi aralıklarını belirtin. 

   Geçerli bir IP aralığı şu biçimleri kullanır: *x. x. x. x/x* veya *x. x. x. x-x. x. x.* x

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Azure Resource Manager şablonundaki IP aralıklarını kısıtlama

[Mantıksal uygulamalar için dağıtımı Kaynak Yöneticisi şablonları kullanarak otomatikleştirmeniz](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)durumunda, mantıksal uygulamanızın kaynak tanımındaki `contents` bölümü ile `accessControl` bölümünü kullanarak IP aralıklarını belirtebilirsiniz, örneğin:

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

<a name="obfuscate"></a>

### <a name="hide-data-from-run-history-by-using-obfuscation"></a>Gizleme kullanarak çalıştırma geçmişinden verileri gizleme

Birçok tetikleyici ve eylemin, girdileri, çıkışları veya her ikisini de mantıksal uygulamanın çalıştırma geçmişinden gizlemek için ayarları vardır. Bu verileri güvenli hale getirmek için bu ayarları kullandığınızda [göz önünde bulundurmanız gereken bazı noktalar](#obfuscation-considerations) aşağıda verilmiştir.

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>Tasarımcıda güvenli girişler ve çıktılar

1. [Azure Portal](https://portal.azure.com)mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

   ![Mantıksal uygulama Tasarımcısı 'nda mantıksal uygulama açma](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. Verilerin güvenliğini sağlamak istediğiniz tetikleyici veya eylemde, üç nokta ( **...** ) düğmesini ve ardından **Ayarlar**' ı seçin.

   ![Tetikleyici veya eylem ayarlarını aç](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. **Güvenli girişleri**, **güvenli çıktıları**veya her ikisini açın. İşiniz bittiğinde **Bitti**'yi seçin.

   ![Güvenli girişleri veya çıkışları açma](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   Eylem veya tetikleyici artık başlık çubuğunda bir kilit simgesi gösterir.

   ![Eylem veya tetikleyici başlık çubuğu kilit simgesini gösterir](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   Önceki eylemlerden gelen güvenli çıkışları temsil eden belirteçler de kilit simgeleri gösterir. Örneğin, bir eylemde kullanmak üzere dinamik içerik listesinden böyle bir çıktı seçtiğinizde, bu belirteç bir kilit simgesi gösterir.

   ![Güvenli çıkış için belirteç seçin](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Mantıksal uygulama çalıştıktan sonra, o çalıştırmanın geçmişini görüntüleyebilirsiniz.

   1. Mantıksal uygulamanın **genel bakış** bölmesinde, görüntülemek istediğiniz çalıştırmayı seçin.

   1. **Mantıksal uygulama çalıştırma** bölmesinde, gözden geçirmek istediğiniz eylemleri genişletin.

      Hem giriş hem de çıkışları güvenli hale getirmeyi seçerseniz, bu değerler artık gizli olarak görünür.

      ![Çalışma geçmişinde gizli girişler ve çıkışlar](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>Kod görünümünde güvenli girişler ve çıktılar

Temel tetikleyici veya eylem tanımında, `runtimeConfiguration.secureData.properties` diziyi bu değerlerden birini ya da her ikisini de ekleyin veya güncelleştirin:

* `"inputs"`: çalıştırma geçmişindeki girişlerin güvenliğini sağlar.
* `"outputs"`: çalıştırma geçmişindeki çıkışların güvenliğini sağlar.

Bu verileri güvenli hale getirmek için bu ayarları kullandığınızda [göz önünde bulundurmanız gereken bazı noktalar](#obfuscation-considerations) aşağıda verilmiştir.

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

<a name="obfuscation-considerations"></a>

#### <a name="considerations-when-hiding-inputs-and-outputs"></a>Girişleri ve çıkışları gizleme konuları

* Bir tetikleyici veya eylemde girişlerin veya çıkışların güvenliğini uyguladığınızda Logic Apps, güvenli verileri Azure Log Analytics 'a göndermez. Ayrıca, izleme için bu tetikleyiciye veya eyleme [izlenen Özellikler](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details) ekleyemezsiniz.

* [İş akışı geçmişini işlemek için Logic Apps API 'si](https://docs.microsoft.com/rest/api/logic/) güvenli çıktılar döndürmez.

* Girişlerin güvenliğini sağlayan veya güvenli çıkışları açık olarak kullanan bir eylemden gelen çıktıları güvenli hale getirmek için, bu eylemde el ile **güvenli çıktılar** açın.

* Bu verilerin güvenliğini sağlamak için çalıştırma geçmişinin beklendiğini bekleyen aşağı akış eylemlerinde **güvenli girişleri** veya **güvenli çıkışları** etkinleştirdiğinizden emin olun.

  **Güvenli çıkışlar ayarı**

  Bir tetikleyici veya eylemde **güvenli çıkışları** el ile açtığınızda Logic Apps, bu çıkışların çalıştırma geçmişinde güvenliğini sağlar. Bir aşağı akış eylemi açık olarak bu güvenli çıkışları giriş olarak kullanıyorsa Logic Apps, bu eylemin çalışma geçmişinde girişlerini gizler, ancak eylemin **güvenli girişler** ayarını *etkinleştirmez* .

  ![Birçok eylemin giriş ve aşağı akış etkisi olarak güvenli çıktılar](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  Oluşturma, ayrıştırma JSON ve yanıt eylemlerinin yalnızca **güvenli girişler** ayarı vardır. Açık olduğunda, ayar bu eylemlerin çıkışlarını da gizler. Bu eylemler, yukarı akış güvenli çıkışlarını giriş olarak açıkça kullanıyorsa, Logic Apps bu eylemlerin girişlerini ve çıkışlarını gizler, ancak bu eylemlerin **güvenli girişler** ayarını *etkinleştirmez* . Bir aşağı akış eylemi, giriş olarak oluşturma, ayrıştırma JSON veya Yanıt eylemlerdeki gizli çıkışları açıkça kullanıyorsa, Logic Apps *Bu aşağı akış eyleminin giriş veya çıkışlarını gizlemez*.

  ![Belirli eylemlerdeki aşağı akış etkisi olan giriş olarak güvenli çıktılar](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Güvenli girişler ayarı**

  Bir tetikleyici veya eylemde **güvenli girişleri** el ile açtığınızda Logic Apps, bu girişlerin çalıştırma geçmişinde güvenliğini sağlar. Bir aşağı akış eylemi, giriş olarak bu tetikleyici veya eylemden görünür çıkışları açıkça kullanıyorsa, Logic Apps bu aşağı akış eyleminin çalıştırma geçmişinde girişlerini gizler, ancak bu eylemde güvenli girişleri etkinleştirmez ve bu eylemin **gizlenmemesini** *sağlar* çıkışı.

  ![Birçok eylemin güvenli girişler ve aşağı akış etkileri](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Oluşturma, ayrıştırma JSON ve yanıt eylemleri, tetikleyici veya eylemden güvenli girişler içeren görünür çıktıları açıkça kullanıyorsa, Logic Apps bu eylemlerin girişlerini ve çıkışlarını gizler, ancak bu eylemin **güvenli girişlerini** *etkinleştirmez* ayarlanmasını. Bir aşağı akış eylemi, giriş olarak oluşturma, ayrıştırma JSON veya Yanıt eylemlerdeki gizli çıkışları açıkça kullanıyorsa, Logic Apps *Bu aşağı akış eyleminin giriş veya çıkışlarını gizlemez*.

  ![Belirli eylemlerdeki güvenli girişler ve aşağı akış etkisi](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Parametre girdilerine erişim

Farklı ortamlara dağıtırsanız, iş akışı tanımınızdaki bu ortamlara göre farklılık gösteren değerleri parametreleştirmeyi göz önünde bulundurun. Bu şekilde, mantıksal uygulamanızı dağıtmak, güvenli parametreleri tanımlayarak gizli verileri korumak ve bu verileri [şablonun parametreleri](../azure-resource-manager/template-parameters.md) aracılığıyla ayrı girişler olarak geçirmek için [Azure Resource Manager şablonu](../azure-resource-manager/template-deployment-overview.md) kullanarak sabit kodlanmış verilerden kaçınabilirsiniz. bir [parametre dosyası](../azure-resource-manager/resource-manager-parameter-files.md).

Örneğin, [Azure Active Directory OAuth](#azure-active-directory-oauth-authentication)ile http eylemlerinin kimliğini doğruladıysanız, kimlik doğrulaması için kullanılan istemci kimliğini ve istemci gizli anahtarını kabul eden parametreleri tanımlayabilir ve güvenli hale getirebilirsiniz. Mantıksal uygulamanızda bu parametreleri tanımlamak için mantıksal uygulamanızın iş akışı tanımındaki `parameters` bölümünü ve dağıtım için Kaynak Yöneticisi şablonunu kullanın. Mantıksal uygulamanızı düzenlenirken veya çalıştırma geçmişini görüntülerken görüntülenmesini istemediğiniz parametre değerlerini gizlemek için `securestring` veya `secureobject` türünü kullanarak parametreleri tanımlayın ve kodlamayı gereken şekilde kullanın. Bu türe sahip parametreler kaynak tanımıyla döndürülmez ve dağıtımdan sonra kaynak görüntülenirken erişilebilir değildir. Çalışma zamanı sırasında bu parametre değerlerine erişmek için, iş akışı tanımınızın içindeki `@parameters('<parameter-name>')` ifadesini kullanın. Bu ifade yalnızca çalışma zamanında değerlendirilir ve [Iş akışı Tanım Dili](../logic-apps/logic-apps-workflow-definition-language.md)tarafından açıklanmıştır.

> [!NOTE]
> İstek üstbilgisinde veya gövdede bir parametre kullanırsanız, mantıksal uygulamanızın çalıştırma geçmişini ve giden HTTP isteğini görüntülediğinizde bu parametre görülebilir. İçerik erişim ilkelerinizi da uygun şekilde ayarladığınızdan emin olun. Ayrıca, çalıştırma geçmişinizdeki girdileri ve çıkışları gizlemek için [gizleme](#obfuscate) de kullanabilirsiniz. Yetkilendirme üstbilgileri, girişler veya çıktılar aracılığıyla hiçbir şekilde görünmez. Bu nedenle, bir gizli dizi kullanılırsa gizli anahtar alınmaz.

Daha fazla bilgi için bu konudaki aşağıdaki bölümlere bakın:

* [İş akışı tanımlarında güvenli parametreler](#secure-parameters-workflow)
* [Gizleme kullanarak çalıştırma geçmişinden verileri gizleme](#obfuscate)

[Mantıksal uygulamalar için dağıtımı Kaynak Yöneticisi şablonları kullanarak otomatikleştirmeniz](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)durumunda, `securestring` ve `secureobject` türlerini kullanarak dağıtımda değerlendirilen güvenli [şablon parametrelerini](../azure-resource-manager/template-parameters.md)tanımlayabilirsiniz. Şablon parametrelerini tanımlamak için, şablonunuzun en üst düzey `parameters` bölümünü kullanın, bu, iş akışı tanımınızdan `parameters` bölümünden farklıdır. Şablon parametrelerinin değerlerini sağlamak için ayrı bir [parametre dosyası](../azure-resource-manager/resource-manager-parameter-files.md)kullanın.

Örneğin, gizli dizileri kullanıyorsanız, bu gizli dizileri dağıtım [Azure Key Vault](../key-vault/key-vault-overview.md) ' den alan güvenli şablon parametrelerini tanımlayabilir ve kullanabilirsiniz. Daha sonra, parametre dosyanızdaki anahtar kasasına ve gizli dizi ile gizli dizi oluşturabilirsiniz. Daha fazla bilgi için şu konulara bakın:

* [Azure Key Vault kullanarak dağıtımda hassas değerler geçirin](../azure-resource-manager/resource-manager-keyvault-parameter.md)
* Bu konunun ilerleyen kısımlarında [Azure Resource Manager şablonlarda güvenli parametreler](#secure-parameters-deployment-template)

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>İş akışı tanımlarında güvenli parametreler

Mantıksal uygulamanızın iş akışı tanımındaki hassas bilgileri korumak için, mantıksal uygulamanızı kaydettikten sonra bu bilgilerin görünür olmaması için güvenli parametreleri kullanın. Örneğin, bir HTTP eyleminin, bir Kullanıcı adı ve parola kullanan temel kimlik doğrulaması gerektirdiğini varsayalım. İş akışı tanımında `parameters` bölümü, `securestring` türünü kullanarak `basicAuthPasswordParam` ve `basicAuthUsernameParam` parametrelerini tanımlar. Eylem tanımı daha sonra `authentication` bölümünde bu parametrelere başvurur.

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

Mantıksal uygulama için bir [Kaynak Yöneticisi şablonu](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) birden çok `parameters` bölümüne sahiptir. Parolaları, anahtarları, sırları ve diğer hassas bilgileri korumak için, `securestring` veya `secureobject` türünü kullanarak şablon düzeyinde ve iş akışı tanımı düzeyinde güvenli parametreleri tanımlayın. Daha sonra bu değerleri [Azure Key Vault](../key-vault/key-vault-overview.md) saklayabilir ve anahtar kasası ve gizli dizi için [parametre dosyasını](../azure-resource-manager/resource-manager-parameter-files.md) kullanabilirsiniz. Şablonunuz daha sonra bu bilgileri dağıtımda alır. Daha fazla bilgi için bkz. [Azure Key Vault kullanarak dağıtımda gizli değerleri geçirme](../azure-resource-manager/resource-manager-keyvault-parameter.md).

Bu `parameters` bölümler hakkında daha fazla bilgi aşağıda verilmiştir:

* Şablonun en üst düzeyinde, bir `parameters` bölümü, şablonun *dağıtımda*kullandığı değerler için parametreleri tanımlar. Örneğin, bu değerler belirli bir dağıtım ortamı için bağlantı dizeleri içerebilir. Daha sonra bu değerleri ayrı bir [parametre dosyasında](../azure-resource-manager/resource-manager-parameter-files.md)saklayabilirsiniz, bu da bu değerlerin değiştirilmesini kolaylaştırır.

* Mantıksal uygulamanızın kaynak tanımı içinde, ancak iş akışı tanımınızın dışında, bir `parameters` bölümü, iş akışı tanımınızın parametrelerinin değerlerini belirtir. Bu bölümde, şablonunuzun parametrelerine başvuran şablon ifadelerini kullanarak bu değerleri atayabilirsiniz. Bu ifadeler dağıtımda değerlendirilir.

* İş akışı tanımınızın içinde, bir `parameters` bölümü mantıksal uygulamanızın çalışma zamanında kullandığı parametreleri tanımlar. Daha sonra bu parametrelere, çalışma zamanında değerlendirilen iş akışı Tanım ifadelerini kullanarak mantıksal uygulamanızın iş akışının içinde başvurabilirsiniz.

`securestring` türünü kullanan birden çok güvenli parametre tanımına sahip bu örnek şablon:

| Parametre adı | Açıklama |
|----------------|-------------|
| `TemplatePasswordParam` | Daha sonra iş akışı tanımının `basicAuthPasswordParam` parametresine geçirilen bir parolayı kabul eden bir şablon parametresi |
| `TemplateUsernameParam` | Daha sonra iş akışı tanımının `basicAuthUserNameParam` parametresine geçirilen bir kullanıcı adını kabul eden bir şablon parametresi |
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
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-0601/workflowdefinition.json#",
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

<a name="secure-requests"></a>

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>Logic Apps 'ten çağrılan hizmetlere ve sistemlere erişim

Mantıksal uygulamanızdan çağrı veya istek alan uç noktaları güvenli hale getirmek için bazı yollar şunlardır:

* Giden isteklere kimlik doğrulaması ekleyin.

  Http, HTTP + Swagger veya Web kancası gibi giden çağrıları yapan bir HTTP tabanlı tetikleyici veya eylem ile çalışırken, mantıksal uygulamanız tarafından gönderilen isteğe kimlik doğrulaması ekleyebilirsiniz. Örneğin, bu kimlik doğrulama türlerini kullanabilirsiniz:

  * [Temel kimlik doğrulaması](#basic-authentication)

  * [İstemci sertifikası kimlik doğrulaması](#client-certificate-authentication)

  * [OAuth kimlik doğrulamasını Active Directory](#azure-active-directory-oauth-authentication)

  * [Yönetilen kimlik kimlik doğrulaması](#managed-identity-authentication)
  
  Daha fazla bilgi için bu konunun ilerleyen kısımlarında [giden çağrılara kimlik doğrulaması ekleme](#add-authentication-outbound) bölümüne bakın.

* Mantıksal uygulama IP adreslerinden erişimi kısıtlayın.

  Mantıksal uygulamalardan gelen uç noktalara yapılan tüm çağrılar, mantıksal uygulamalarınızın bölgelerini temel alan belirli IP adreslerinden kaynaklardır. Yalnızca bu IP adreslerinden istekleri kabul eden filtre ekleyebilirsiniz. Bu IP adreslerini almak için bkz. [Azure Logic Apps Için sınırlar ve yapılandırma](logic-apps-limits-and-config.md#configuration).

* Şirket içi sistemlere güvenli bağlantılar.

  Azure Logic Apps, güvenli ve güvenilir şirket içi iletişim için bu hizmetlerle tümleştirme sağlar.

  * Şirket içi veri ağ geçidi

    Azure Logic Apps ' de birçok yönetilen bağlayıcı, dosya sistemi, SQL, SharePoint ve DB2 gibi şirket içi sistemlere güvenli bağlantılar sağlar. Ağ Geçidi, Azure Service Bus aracılığıyla şifrelenmiş kanallardaki şirket içi kaynaklardaki verileri gönderir. Tüm trafik ağ geçidi aracısından güvenli giden trafik olarak gelir. [Şirket içi veri ağ geçidinin nasıl çalıştığını](logic-apps-gateway-install.md#gateway-cloud-service)öğrenin.

  * Azure API Management ile bağlanma

    [Azure API Management](../api-management/api-management-key-concepts.md) , siteden siteye sanal özel ağ ve güvenli proxy Için ExpressRoute tümleştirmesi ve şirket içi sistemlerle iletişim gibi şirket içi bağlantı seçenekleri sağlar. Mantıksal uygulama Tasarımcısı ' nda mantıksal uygulamanızın iş akışından, şirket içi sistemlere hızlı erişim sağlayan API Management tarafından açığa çıkarılan bir API seçebilirsiniz.

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>Giden çağrılara kimlik doğrulama ekleme

HTTP ve HTTPS uç noktaları çeşitli kimlik doğrulama türlerini destekler. Giden çağrıları veya bu uç noktalara erişen istekleri yapmak için kullandığınız tetikleyici veya eyleme bağlı olarak, farklı kimlik doğrulama türü aralıkları arasından seçim yapabilirsiniz. Mantıksal uygulamanızın işleyeceği gizli bilgileri koruduğunuzdan emin olmak için, güvenli parametreleri kullanın ve verileri gerektiği şekilde kodlayın. Parametreleri kullanma ve güvenliğini sağlama hakkında daha fazla bilgi için bkz. [parametre girdilerine erişim](#secure-action-parameters).

| Kimlik doğrulaması türü | Desteklediği |
|---------------------|--------------|
| [Temel](#basic-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, HTTP Web kancası |
| [İstemci sertifikası](#client-certificate-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, HTTP Web kancası |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API Management, Azure Uygulama Hizmetleri, Azure Işlevleri, HTTP, HTTP + Swagger, HTTP Web kancası |
| [Madde](#raw-authentication) | Azure API Management, Azure Uygulama Hizmetleri, Azure Işlevleri, HTTP, HTTP + Swagger, HTTP Web kancası |
| [Yönetilen kimlik](#managed-identity-authentication) (yalnızca sistem tarafından atanan) | Azure API Management, Azure Uygulama Hizmetleri, Azure Işlevleri, HTTP, HTTP + Swagger, HTTP Web kancası |
|||

> [!NOTE]
> Mantıksal uygulama tasarımcısında **, kimlik doğrulama özelliği,** kimlik doğrulama türünü belirtebileceğiniz bazı Tetikleyiciler ve eylemler üzerinde gizli olabilir. Özelliğin bu durumlarda görünmesini sağlamak için tetikleyici veya eylemde, **yeni parametre Ekle** listesini açın ve **kimlik doğrulaması**' nı seçin. Daha fazla bilgi için bkz. [yönetilen kimlikle erişim kimlik doğrulaması](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Temel kimlik doğrulama

[Temel](../active-directory-b2c/active-directory-b2c-custom-rest-api-netfw-secure-basic.md) seçenek varsa, bu özellik değerlerini belirtin:

| Özellik (Tasarımcı) | Özellik (JSON) | Gerekli | Değer | Açıklama |
|---------------------|-----------------|----------|-------|-------------|
| **Kimlik doğrulaması** | `type` | Yes | Temel | Kullanılacak kimlik doğrulaması türü |
| **Kullanıcı Adı** | `username` | Yes | <*Kullanıcı adı*>| Hedef hizmet uç noktasına erişim doğrulaması için Kullanıcı adı |
| **Parola** | `password` | Yes | <*parola*> | Hedef hizmet uç noktasına erişim doğrulaması için parola |
||||||

Gizli bilgileri işlemek ve korumak için [güvenli parametreleri](#secure-action-parameters) kullandığınızda (örneğin, [dağıtımı otomatikleştirmek için bir Azure Resource Manager şablonunda](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)), çalışma zamanında bu parametre değerlerine erişmek için ifadeleri kullanabilirsiniz. Bu örnek HTTP eylemi tanımı, kimlik doğrulama `type` `Basic` olarak belirtir ve parametre değerlerini almak için [Parameters () işlevini](../logic-apps/workflow-definition-language-functions-reference.md#parameters) kullanır:

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

### <a name="client-certificate-authentication"></a>İstemci sertifikası kimlik doğrulaması

[Istemci sertifikası](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) seçeneği varsa, bu özellik değerlerini belirtin:

| Özellik (Tasarımcı) | Özellik (JSON) | Gerekli | Değer | Açıklama |
|---------------------|-----------------|----------|-------|-------------|
| **Kimlik doğrulaması** | `type` | Yes | **İstemci sertifikası** <br>veya <br>`ClientCertificate` | Güvenli Yuva Katmanı (SSL) istemci sertifikaları için kullanılacak kimlik doğrulaması türü. Otomatik olarak imzalanan sertifikalar desteklenirken, SSL için otomatik olarak imzalanan sertifikalar desteklenmez. |
| **Türk** | `pfx` | Yes | <*kodlu-pfx-dosya-içerik*> | Kişisel bilgi değişimi (PFX) dosyasından gelen Base64 kodlamalı içerik <p><p>PFX dosyasını Base64 kodlamalı biçime dönüştürmek için aşağıdaki adımları izleyerek PowerShell kullanabilirsiniz: <p>1. sertifika içeriğini bir değişkene kaydedin: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. `ToBase64String()` işlevini kullanarak sertifika içeriğini dönüştürün ve bu içeriği bir metin dosyasına kaydedin: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **Parola** | `password`| Açıklamaya bakın | *pfx dosyası için parola* <> | PFX dosyasına erişim için parola. <p><p>**Not**: Bu özellik değeri, Logic App Designer 'da çalışırken gereklidir ve kod görünümünde çalışırken gerekli *değildir* . |
|||||

Gizli bilgileri işlemek ve korumak için [güvenli parametreleri](#secure-action-parameters) kullandığınızda (örneğin, [dağıtımı otomatikleştirmek için bir Azure Resource Manager şablonunda](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)), çalışma zamanında bu parametre değerlerine erişmek için ifadeleri kullanabilirsiniz. Bu örnek HTTP eylemi tanımı, kimlik doğrulama `type` `ClientCertificate` olarak belirtir ve parametre değerlerini almak için [Parameters () işlevini](../logic-apps/workflow-definition-language-functions-reference.md#parameters) kullanır:

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

* [Azure API Management istemci sertifikası kimlik doğrulaması kullanarak API 'Leri güvenli hale getirme](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Azure API Management istemci sertifikası kimlik doğrulamasını kullanarak arka uç hizmetleri güvenli hale getirme](../api-management/api-management-howto-mutual-certificates.md)
* [İstemci sertifikalarını kullanarak yeniden takip eden hizmetinizi güvenli hale getirin](../active-directory-b2c/active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
* [Uygulama kimlik doğrulaması için sertifika kimlik bilgileri](../active-directory/develop/active-directory-certificate-credentials.md)
* [Uygulama kodunuzda bir SSL sertifikası kullanın Azure App Service](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-oauth-authentication"></a>OAuth kimlik doğrulamasını Azure Active Directory

[Active Directory OAuth](../active-directory/develop/about-microsoft-identity-platform.md) seçeneği varsa, bu özellik değerlerini belirtin:

| Özellik (Tasarımcı) | Özellik (JSON) | Gerekli | Değer | Açıklama |
|---------------------|-----------------|----------|-------|-------------|
| **Kimlik doğrulaması** | `type` | Yes | **Active Directory OAuth** <br>veya <br>`ActiveDirectoryOAuth` | Kullanılacak kimlik doğrulaması türü. Logic Apps Şu anda [OAuth 2,0 protokolünü](../active-directory/develop/v2-overview.md)izler. |
| **Kiracı** | `tenant` | Yes | <*KIRACı kimliği*> | Azure AD kiracısı için kiracı KIMLIĞI |
| **Grubu** | `audience` | Yes | *kaynaktan yetkilendir*> < | Yetkilendirme için kullanmak istediğiniz kaynak (örneğin, `https://management.core.windows.net/`) |
| **İstemci kimliği** | `clientId` | Yes | <*ISTEMCI kimliği*> | Yetkilendirme isteyen uygulamanın istemci KIMLIĞI |
| **Kimlik bilgisi türü** | `credentialType` | Yes | Sertifika <br>veya <br>Gizli dizi | İstemcinin yetkilendirme istemek için kullandığı kimlik bilgisi türü. Bu özellik ve değer mantıksal uygulamanızın temel tanımında görünmez, ancak seçilen kimlik bilgisi türü için görüntülenen özellikleri belirler. |
| **Gizli dizi** | `secret` | Evet, ancak yalnızca "gizli" kimlik bilgisi türü için | <*istemci gizli*> | Yetkilendirme isteğinde bulunan istemci parolası |
| **Türk** | `pfx` | Evet, ancak yalnızca "sertifika" kimlik bilgisi türü için | <*kodlu-pfx-dosya-içerik*> | Kişisel bilgi değişimi (PFX) dosyasından gelen Base64 kodlamalı içerik |
| **Parola** | `password` | Evet, ancak yalnızca "sertifika" kimlik bilgisi türü için | *pfx dosyası için parola* <> | PFX dosyasına erişim parolası |
| **İniz** | `authority` | Hayır | <*URL-yetki-belirteç-veren*> | Kimlik doğrulama belirtecini sağlayan yetkilinin URL 'SI. Varsayılan olarak, bu değer `https://login.windows.net`. <p>**Note**: Bu özelliği tasarımcıda görünür hale getirmek için tetikleyici veya eylemde, **yeni parametre Ekle** listesini açın ve **yetkili**' i seçin. |
|||||

Gizli bilgileri işlemek ve korumak için [güvenli parametreleri](#secure-action-parameters) kullandığınızda (örneğin, [dağıtımı otomatikleştirmek için bir Azure Resource Manager şablonunda](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)), çalışma zamanında bu parametre değerlerine erişmek için ifadeleri kullanabilirsiniz. Bu örnek HTTP eylemi tanımı, kimlik doğrulama `type` `ActiveDirectoryOAuth`olarak belirtir, kimlik bilgisi türü `Secret`olarak belirtir ve parametre değerlerini almak için [Parameters () işlevini](../logic-apps/workflow-definition-language-functions-reference.md#parameters) kullanır:

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

### <a name="raw-authentication"></a>Ham kimlik doğrulaması

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
| **Kimlik doğrulaması** | `type` | Yes | Madde | Kullanılacak kimlik doğrulaması türü |
| **Değer** | `value` | Yes | <*yetkilendirmesi-üst bilgi-değer*> | Kimlik doğrulaması için kullanılacak yetkilendirme üst bilgisi değeri |
||||||

Gizli bilgileri işlemek ve korumak için [güvenli parametreleri](#secure-action-parameters) kullandığınızda (örneğin, [dağıtımı otomatikleştirmek için bir Azure Resource Manager şablonunda](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)), çalışma zamanında bu parametre değerlerine erişmek için ifadeleri kullanabilirsiniz. Bu örnek HTTP eylemi tanımı, kimlik doğrulama `type` `Raw`olarak belirtir ve parametre değerlerini almak için [Parameters () işlevini](../logic-apps/workflow-definition-language-functions-reference.md#parameters) kullanır:

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

### <a name="managed-identity-authentication"></a>Yönetilen kimlik doğrulaması

[Yönetilen kimlik](../active-directory/managed-identities-azure-resources/overview.md) seçeneği kullanılabiliyorsa, mantıksal uygulamanız oturum açmadan diğer Azure Active Directory (Azure AD) kiracılarındaki kaynaklara erişim kimliğini doğrulamak için sistem tarafından atanan kimliği kullanabilir. Azure bu kimliği sizin için yönetir ve gizli dizileri sağlamanız veya döndürmenize gerek olmadığından kimlik bilgilerinizin güvenliğini sağlamaya yardımcı olur. [Azure AD kimlik doğrulaması için yönetilen kimlikleri destekleyen Azure hizmetleri](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)hakkında daha fazla bilgi edinin.

1. Mantıksal uygulamanızın sistem tarafından atanan kimliği kullanabilmesi [için Azure Logic Apps içindeki yönetilen kimlikleri kullanarak Azure kaynaklarına erişimi kimlik doğrulama](../logic-apps/create-managed-service-identity.md)bölümündeki adımları izleyin. Bu adımlar, mantıksal uygulamanızda yönetilen kimliği etkinleştirir ve bu kimliğin hedef Azure kaynağına erişimini ayarlar.

2. Bir Azure işlevinin sistem tarafından atanan kimliği kullanabilmesi [için önce Azure işlevleri için kimlik doğrulamasını etkinleştirin](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

3. Yönetilen kimliği kullanmak istediğiniz tetikleyici veya eylemde, bu özellik değerlerini belirtin:

   | Özellik (Tasarımcı) | Özellik (JSON) | Gerekli | Değer | Açıklama |
   |---------------------|-----------------|----------|-------|-------------|
   | **Kimlik doğrulaması** | `type` | Yes | **Yönetilen kimlik** <br>veya <br>`ManagedServiceIdentity` | Kullanılacak kimlik doğrulaması türü |
   | **Grubu** | `audience` | Yes | <*target-kaynak kimliği*> | Erişmek istediğiniz hedef kaynağın kaynak KIMLIĞI. <p>Örneğin `https://storage.azure.com/`, tüm depolama hesapları için kimlik doğrulaması için erişim belirteçlerini geçerli hale getirir. Ancak, belirli bir depolama hesabı için `https://fabrikamstorageaccount.blob.core.windows.net` gibi bir kök hizmeti URL 'SI de belirtebilirsiniz. <p>**Note**: Bu özellik bazı tetikleyicilere veya eylemlere gizlenmiş olabilir. Bu özelliği görünür hale getirmek için tetikleyici veya eylemde, **yeni parametre Ekle** listesini açın ve **hedef kitle**' i seçin. <p><p>**Önemli**: Bu hedef kaynak kimliğinin, tüm gerekli eğik çizgiler de dahil olmak üzere Azure AD 'nin beklediği değerle tam olarak eşleştiğinden emin olun. Bu nedenle, tüm Azure Blob depolama hesapları için `https://storage.azure.com/` kaynak KIMLIĞI sonunda eğik çizgi gerekir. Ancak, belirli bir depolama hesabının kaynak KIMLIĞI, sonunda eğik çizgi gerektirmez. Bu kaynak kimliklerini bulmak için bkz. [Azure AD 'yi destekleyen Azure hizmetleri](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). |
   |||||

   Gizli bilgileri işlemek ve korumak için [güvenli parametreleri](#secure-action-parameters) kullandığınızda (örneğin, [dağıtımı otomatikleştirmek için bir Azure Resource Manager şablonunda](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)), çalışma zamanında bu parametre değerlerine erişmek için ifadeleri kullanabilirsiniz. Bu örnek HTTP eylemi tanımı, kimlik doğrulama `type` `ManagedServiceIdentity` olarak belirtir ve parametre değerlerini almak için [Parameters () işlevini](../logic-apps/workflow-definition-language-functions-reference.md#parameters) kullanır:

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Logic Apps için dağıtımı otomatikleştirin](logic-apps-azure-resource-manager-templates-overview.md)  
* [Mantıksal uygulamalarınızı izleyin](logic-apps-monitor-your-logic-apps.md)  
* [Mantıksal uygulama başarısızlıklarını ve sorunlarını tanılama](logic-apps-diagnosing-failures.md)  
* [Mantıksal uygulama dağıtımını otomatikleştirme](logic-apps-azure-resource-manager-templates-overview.md)
