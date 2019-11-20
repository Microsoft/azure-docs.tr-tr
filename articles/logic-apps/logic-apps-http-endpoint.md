---
title: Çağrı, tetikleyici veya iç içe mantıksal uygulamalar-Azure Logic Apps
description: Azure Logic Apps içinde Logic App iş akışlarını çağırmak, tetiklemek veya iç içe aktarmak için HTTP uç noktalarını ayarlama
services: logic-apps
ms.service: logic-apps
ms.workload: integration
author: ecfan
ms.author: klam
ms.reviewer: klam, jehollan, LADocs
ms.topic: article
ms.date: 11/04/2019
ms.openlocfilehash: 27a3bf036cce27a5f215068ff71928cb7e181452
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73833960"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-http-endpoints-in-azure-logic-apps"></a>Azure Logic Apps 'de HTTP uç noktalarını kullanarak çağrı, tetikleyici veya iç içe mantıksal uygulamalar

Mantıksal uygulamanızın diğer hizmetlerden gelen istekleri alabilmesi için mantıksal uygulamanızı bir URL aracılığıyla çağrılabilir hale getirmek için, zaman uyumlu bir HTTP uç noktasını o mantıksal uygulamada bir tetikleyici olarak yerel kullanıma sunabilirsiniz. Bu özelliği ayarlarken, mantıksal uygulamanızı diğer mantıksal uygulamalarda iç içe geçirebilirsiniz, bu da çağrılabilir bir uç nokta stili oluşturmanızı sağlar.

Bir HTTP uç noktası ayarlamak için, mantıksal uygulamaların gelen istekleri almasını sağlayan bu tetikleyici türlerinden herhangi birini kullanabilirsiniz:

* [İstek](../connectors/connectors-native-reqres.md)
* [HTTP Web kancası](../connectors/connectors-native-webhook.md)
* [Apiconnectionweb kancası türüne](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) sahip yönetilen bağlayıcı Tetikleyicileri ve gelen http isteklerini alabilir

> [!NOTE]
> Bu örnekler, Istek tetikleyicisini kullanır, ancak önceki listede bulunan HTTP istek tabanlı bir tetikleyiciyi kullanabilirsiniz. Tüm ilkeler bu diğer tetikleyici türleri için de geçerlidir.

Logic Apps 'e yeni başladıysanız, bkz. [Azure Logic Apps nedir](../logic-apps/logic-apps-overview.md) ve [hızlı başlangıç: Ilk mantıksal uygulamanızı oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Ön koşullar

* Azure aboneliği. Aboneliğiniz yoksa, [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* HTTP uç noktasını tetikleyici olarak kurmak istediğiniz mantıksal uygulama. Geçerli tetikleyiciyi değiştirmek istediğiniz boş bir mantıksal uygulama ya da mevcut bir mantıksal uygulama ile başlayabilirsiniz. Bu örnek, boş bir mantıksal uygulamayla başlar.

## <a name="create-a-callable-endpoint"></a>Çağrılabilir bir uç nokta oluşturma

1. [Azure portalında](https://portal.azure.com) oturum açın. Mantıksal uygulama tasarımcısında boş bir mantıksal uygulama oluşturun ve açın.

   Bu örnek, Istek tetikleyicisini kullanır, ancak gelen HTTP isteklerini alabilen herhangi bir tetikleyiciyi kullanabilirsiniz. Tüm ilkeler bu Tetikleyiciler için aynı şekilde geçerlidir. Istek tetikleyicisi hakkında daha fazla bilgi için, bkz. [Azure Logic Apps kullanarak gelen https çağrılarını alma ve yanıtlama](../connectors/connectors-native-reqres.md).

1. Arama kutusunda, **yerleşik**' i seçin. Arama kutusuna filtreniz olarak `request` girin. Tetikleyiciler listesinden **BIR http isteği alındığında**öğesini seçin.

   ![Istek tetikleyicisini bul ve Seç](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. İsteğe bağlı olarak, **Istek GÖVDESI JSON şeması** kutusunda, tetikleyicinin almasını istediğiniz yükü veya verileri açıklayan bir JSON şeması girebilirsiniz.

   Tasarımcı, tetikleyici çıkışlarını temsil eden belirteçler oluşturmak için bu şemayı kullanır. Daha sonra mantıksal uygulamanızın iş akışının tamamında bu çıkışlara kolayca başvurabilirsiniz. [JSON şemalardan oluşturulan belirteçler](#generated-tokens)hakkında daha fazla bilgi edinin.

   Bu örnek için şu şemayı girin:

   ```json
      {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "streetNumber": {
                  "type": "string"
               },
               "streetName": {
                  "type": "string"
               },
               "town": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "string"
               }
            }
         }
      }
   }
    ```

   ![Istek eylemi için JSON şeması sağla](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   Veya örnek yük sağlayarak bir JSON şeması oluşturabilirsiniz:

   1. **İstek** tetikleyicisinde, **şema oluşturmak Için örnek yük kullan**' ı seçin.

   1. **Örnek BIR JSON yükü girin veya yapıştırın** kutusuna örnek yükünüzü girin, örneğin:

      ```json
      {
         "address": {
            "streetNumber": "00000",
            "streetName": "AnyStreet",
            "town": "AnyTown",
            "postalCode": "11111-1111"
        }
      }
      ```

   1. Hazırsanız, **bitti**' yi seçin.

      **Istek GÖVDESI JSON şeması** kutusu artık oluşturulan şemayı gösterir.

1. Mantıksal uygulamanızı kaydedin.

   **Bu URL 'ye gönderme** kutusu artık diğer hizmetlerin mantıksal uygulamanızı çağırmak ve tetiklemek için kullanabileceği, oluşturulan GERI çağırma URL 'sini gösterir. Bu URL, sorgu parametrelerinde kimlik doğrulama için kullanılan bir paylaşılan erişim Imzası (SAS) anahtarı içerir, örneğin:

   ![Uç nokta için geri çağırma URL 'SI oluşturuldu](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

   Mantıksal uygulamanızın **genel bakış** bölmesinden HTTP uç noktası URL 'sini de alabilirsiniz.

   1. Mantıksal uygulamanızın menüsünde **genel bakış**' ı seçin.

   1. **Özet** bölümünde **tetikleyici geçmişini göster**' i seçin.

      ![Azure portal HTTP uç noktası URL 'SI al](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

   1. **Geri arama URL 'si [Post]** altında, URL 'yi kopyalayın:

      ![Azure portal HTTP Endpoint URL 'sini Kopyala](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url.png)

      Ya da bu çağrıyı yaparak URL 'YI edinebilirsiniz:

      ```http
      POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01
      ```

<a name="set-method"></a>

## <a name="set-expected-http-method"></a>Beklenen HTTP yöntemini ayarla

Varsayılan olarak, Istek tetikleyicisi bir HTTP POST isteği bekler. Ancak, yalnızca bir yöntem beklendiğinde farklı bir yöntem belirtebilirsiniz.

1. Istek tetikleyicisinde **yeni parametre Ekle** listesini açın ve bu özelliği tetikleyicisine ekleyen **Yöntem**' i seçin.

   ![Tetikleyiciye "method" özelliği Ekle](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. **Yöntem** listesinden tetikleyicinin beklediği başka bir yöntem seçin. Ya da özel bir yöntem belirtebilirsiniz.

   Örneğin, HTTP uç noktanızın URL 'sini daha sonra sınayabilmeniz için **Get** yöntemini seçin.

   ![Tetikleyici için kullanılacak HTTP yöntemini seçin](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

## <a name="accept-parameters-in-endpoint-url"></a>Uç nokta URL 'sindeki parametreleri kabul et

Endpoint URL 'nizin parametreleri kabul etmesini istediğinizde, tetikleyicinizin göreli yolunu belirtin. Ayrıca, HTTP isteğinizin beklediği [yöntemi açıkça ayarlamanız](#set-method) gerekir.

1. Istek tetikleyicisinde **yeni parametre Ekle** listesini açın ve bu özelliği **tetikleyiciye ekleyen göreli yol**' ı seçin.

   ![Tetikleyiciye "göreli yol" özelliği Ekle](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. **Göreli yol** ÖZELLIĞINDE, JSON ŞEMANıZDA URL 'nizin kabul etmesini istediğiniz parametrenin göreli yolunu belirtin, örneğin `address/{postalCode}`.

   ![Parametre için göreli yolu belirtin](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. Parametresini kullanmak için, mantıksal uygulamanıza bir **Yanıt** eylemi bulun ve ekleyin.

   1. Istek tetikleyicisi altında **yeni adım** > **Eylem Ekle**' yi seçin.

   1. **Eylem seçin**altında, arama kutusuna filtreniz olarak `response` girin.

   1. Eylemler listesinden, **Yanıt** eylemini seçin.

1. Yanıt eyleminin **gövde** özelliğinde, tetikleyicinizin göreli yolunda belirttiğiniz parametreyi temsil eden belirteci ekleyin.

   Örneğin, yanıt eyleminin `Postal Code: {postalCode}`döndürmesini istediğinizi varsayalım.

   **Body** özelliğine, sonunda boşluk girerek `Postal Code: ` girin. Görüntülenen dinamik içerik listesinden **PostaKodu** belirtecini seçin.

   ![Belirtilen parametreyi yanıt gövdesine ekleyin](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

   **Body** özelliği artık seçili parametreyi içeriyor:

   ![Parametreli örnek yanıt gövdesi](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. Mantıksal uygulamanızı kaydedin.

    HTTP uç noktası URL 'niz artık göreli yolu içerir, örneğin:

    ```http
    https://prod-25.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}
    ```

1. HTTP uç noktanızı test etmek için, güncelleştirilmiş URL 'YI kopyalayıp başka bir tarayıcı penceresine yapıştırın, ancak `{postalCode}` `123456`ve ENTER ' a basın.

   Tarayıcınızda şu metin görüntülenir: `Postal Code: 123456`

## <a name="call-logic-app-through-http-endpoint"></a>HTTP uç noktası aracılığıyla mantıksal uygulama çağırma

HTTP uç noktasını oluşturduktan sonra, uç noktanın tam URL 'sine bir HTTP `POST` isteği göndererek mantıksal uygulamayı tetikleyebilirsiniz. Logic Apps, doğrudan erişimli uç noktalar için yerleşik desteğe sahiptir.

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>Şemadan oluşturulan belirteçler

Istek tetikleyicisinde bir JSON şeması sağladığınızda, mantıksal uygulama Tasarımcısı bu şemadaki özellikler için belirteçler oluşturur. Daha sonra bu belirteçleri, mantıksal uygulama iş akışınız aracılığıyla veri geçirmek için kullanabilirsiniz.

Örneğin, JSON şemanıza `"suite"`gibi daha fazla özellik eklerseniz, bu özelliklerin belirteçleri mantıksal uygulamanız için sonraki adımlarda kullanabileceğiniz şekilde kullanılabilir. Tüm JSON şeması aşağıda verilmiştir:

```json
   {
   "type": "object",
   "properties": {
      "address": {
         "type": "object",
         "properties": {
            "streetNumber": {
               "type": "string"
            },
            "streetName": {
               "type": "string"
            },
            "suite": {
               "type": "string"
            },
            "town": {
               "type": "string"
            },
            "postalCode": {
               "type": "string"
            }
         }
      }
   }
}
```

## <a name="create-nested-logic-apps"></a>İç içe mantıksal uygulamalar oluşturma

İstekleri alabilen diğer mantıksal uygulamaları ekleyerek mantıksal uygulamanıza iş akışlarını iç içe yerleştirebilirsiniz. Bu mantıksal uygulamaları eklemek için aşağıdaki adımları izleyin:

1. Başka bir mantıksal uygulamayı çağırmak istediğiniz adım altında, **yeni adım** > **Eylem Ekle**' yi seçin.

1. **Eylem seçin**altında, **yerleşik**' i seçin. Arama kutusuna filtreniz olarak `logic apps` girin. Eylemler listesinden **Logic Apps iş akışı**Seç ' i seçin.

   ![Geçerli mantıksal uygulamanın içinde iç içe mantıksal uygulama](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   Tasarımcı, seçmeniz için uygun mantıksal uygulamaları gösterir.

1. Geçerli mantıksal uygulamanızdan çağrılacak mantıksal uygulamayı seçin.

   ![Geçerli mantıksal uygulamadan çağırmak için mantıksal uygulama seçin](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>Gelen bir istekten başvuru içeriği

Gelen isteğin içerik türü `application/json`ise, gelen istekteki özelliklere başvurabilirsiniz. Aksi takdirde, bu içerik diğer API 'lere geçirebilmeniz için tek bir ikili birim olarak değerlendirilir. Mantıksal uygulamanızın iş akışı içindeki bu içeriğe başvurmak için, önce bu içeriği dönüştürmeniz gerekir.

Örneğin, `application/xml` türüne sahip içerik geçirıyorsanız, bir XPath ayıklama işlemi gerçekleştirmek için [`@xpath()` ifadesini](../logic-apps/workflow-definition-language-functions-reference.md#xpath) KULLANABILIR veya XML 'yi JSON 'ye dönüştürmek için [`@json()` ifadesini](../logic-apps/workflow-definition-language-functions-reference.md#json) kullanabilirsiniz. Desteklenen [içerik türleriyle](../logic-apps/logic-apps-content-type.md)çalışma hakkında daha fazla bilgi edinin.

Gelen bir istekten gelen çıktıyı almak için [`@triggerOutputs` ifadesini](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs)kullanabilirsiniz. Örneğin, şu örneğe benzer bir çıktı olduğunu varsayalım:

```json
{
   "headers": {
      "content-type" : "application/json"
   },
   "body": {
      "myProperty" : "property value"
   }
}
```

Özellikle `body` özelliğine erişmek için [`@triggerBody()` ifadesini](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) kısayol olarak kullanabilirsiniz.

## <a name="respond-to-requests"></a>İsteklere yanıt verme

Bazen, çağrı yapana içerik döndürerek mantıksal uygulamanızı tetikleyen belirli isteklere yanıt vermek isteyebilirsiniz. Yanıtınız için durum kodunu, üstbilgiyi ve gövdesini oluşturmak için, yanıt eylemini kullanın. Bu eylem, yalnızca iş akışınızın sonunda değil, mantıksal uygulamanızda herhangi bir yerde görünebilir. Mantıksal uygulamanız bir yanıt eylemi içermiyorsa, HTTP uç noktası **202 kabul edilen** durumla *hemen* yanıt verir.

Özgün çağıranın yanıtı başarıyla alması için, tetiklenen mantıksal uygulama iç içe mantıksal uygulama olarak çağrıdıkça yanıt için gereken tüm adımların [istek zaman aşımı sınırı](./logic-apps-limits-and-config.md) içinde bitmesi gerekir. Bu sınır içinde yanıt döndürülmezse, gelen istek zaman aşımına uğrar ve **408 istemci zaman aşımı** yanıtını alır.

İç içe mantıksal uygulamalar için, gerekli olan sürenin ne olursa olsun, ana mantıksal uygulama, tüm adımlar tamamlanana kadar yanıt beklemeye devam eder.

### <a name="construct-the-response"></a>Yanıtı oluşturun

Yanıt gövdesinde, birden fazla üstbilgiyi ve herhangi bir içerik türünü dahil edebilirsiniz. Örneğin, bu yanıtın üst bilgisi, yanıtın içerik türünün `application/json` olduğunu ve gövdenin, Istek tetikleyicisi için bu konuda daha önce açıklanan JSON şemasına bağlı olarak `town` ve `postalCode` özelliklerine ilişkin değerler içerdiğini belirtir.

![HTTP yanıtı eylemi için yanıt içeriği sağlama](./media/logic-apps-http-endpoint/content-for-response-action.png)

Yanıtlar şu özelliklere sahiptir:

| Özellik (görüntüleme) | Özellik (JSON) | Açıklama |
|--------------------|-----------------|-------------|
| **Durum Kodu** | `statusCode` | Gelen istek için yanıtta kullanılacak HTTP durum kodu. Bu kod, 2xx, 4xx veya 5xx ile başlayan geçerli bir durum kodu olabilir. Ancak, 3xx durum kodlarına izin verilmez. |
| **Üst Bilgiler** | `headers` | Yanıta eklenecek bir veya daha fazla üst bilgi |
| **Gövde** | `body` | Bir dize, JSON nesnesi veya hatta önceki adımdan başvurulan ikili içerik olabilecek bir gövde nesnesi |
||||

Yanıt eyleminin JSON tanımını ve mantıksal uygulamanızın tamamlanmış JSON tanımını görüntülemek için mantıksal uygulama Tasarımcısı araç çubuğunda **kod görünümü**' nü seçin.

``` json
"Response": {
   "type": "Response",
   "kind": "http",
   "inputs": {
      "body": {
         "postalCode": "@triggerBody()?['address']?['postalCode']",
         "town": "@triggerBody()?['address']?['town']"
      },
      "headers": {
         "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {}
}
```

## <a name="q--a"></a>Soru-Cevap

#### <a name="q-what-about-url-security"></a>S: URL güvenliği hakkında ne olacak?

Y **: Azure**, [paylaşılan ERIŞIM imzasını (SAS)](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature)kullanarak mantıksal uygulama geri çağırma URL 'lerini güvenli şekilde oluşturur. Bu imza bir sorgu parametresi olarak geçirilir ve mantıksal uygulamanızın çalıştırılabilmesi için önce doğrulanması gerekir. Azure, mantıksal uygulama başına bir gizli anahtar birleşimi, tetikleyici adı ve gerçekleştirilen işlem ile imza oluşturur. Bu nedenle, gizli mantıksal uygulama anahtarına birisi erişemediği takdirde, geçerli bir imza üretemiyor.

> [!IMPORTANT]
> Üretim ve güvenli sistemler için aşağıdaki nedenlerden dolayı mantıksal uygulamanızı doğrudan tarayıcıdan çağırmayı önemle tavsiye ederiz:
>
> * Paylaşılan erişim anahtarı URL 'de görüntülenir.
> * Azure Logic Apps müşteriler genelinde paylaşılan etki alanları nedeniyle güvenli içerik ilkelerini yönetemezsiniz.

#### <a name="q-can-i-configure-http-endpoints-further"></a>S: HTTP uç noktalarını başka bir şekilde yapılandırabilir miyim?

Y **: Evet**, HTTP uç noktaları [Azure API Management](../api-management/api-management-key-concepts.md)aracılığıyla daha gelişmiş yapılandırmayı destekler. Bu hizmet ayrıca Logic Apps, özel etki alanı adları ayarlama, daha fazla kimlik doğrulama yöntemi kullanma ve daha fazlasını içeren tüm API 'lerinizi sürekli olarak yönetmenize olanak sağlar. Örneğin:

* [İstek yöntemini değiştirme](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [İsteğin URL segmentlerini değiştirme](../api-management/api-management-transformation-policies.md#RewriteURL)
* [Azure portal](https://portal.azure.com/) API Management etki alanlarınızı ayarlama
* Temel kimlik doğrulamasını denetlemek için ilke ayarlama

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Logic Apps kullanarak gelen HTTPS çağrılarını alma ve yanıtlama](../connectors/connectors-native-reqres.md)