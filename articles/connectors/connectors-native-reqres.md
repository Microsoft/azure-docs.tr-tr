---
title: HTTPS kullanarak çağrıları alma ve yanıtlama
description: Azure Logic Apps kullanarak dış hizmetlerden gelen HTTPS isteklerini işleyin
services: logic-apps
ms.suite: integration
ms.reviewers: jonfan, logicappspm
ms.topic: conceptual
ms.date: 11/19/2020
tags: connectors
ms.openlocfilehash: 83ffccb7bae4fabc10796c36e782e72c661bd346
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99063021"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Azure Logic Apps gelen HTTPS isteklerini alın ve bunlara yanıt verin

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) ve yerleşik istek tetikleyicisi ve yanıtı EYLEMIYLE, https üzerinden gelen istekleri alabilen otomatikleştirilmiş görevler ve iş akışları oluşturabilirsiniz. Bunun yerine giden istekleri göndermek için yerleşik [http tetikleyicisi veya http eylemini](../connectors/connectors-native-http.md)kullanın.

Örneğin, mantıksal uygulamanızı kullanabilirsiniz:

* Şirket içi bir veritabanındaki veriler için bir HTTPS isteği alın ve yanıt verin.

* Dış Web kancası olayı gerçekleştiğinde iş akışı tetikleyin.

* Başka bir mantıksal uygulamadan bir HTTPS çağrısını alın ve yanıtlayın.

Bu makalede, mantıksal uygulamanızın gelen çağrıları alabilmesi ve yanıtlayabilmesi için Istek tetikleyicisi ve yanıt eyleminin nasıl kullanılacağı gösterilmektedir.

Mantıksal uygulamanıza yönelik güvenlik, yetkilendirme ve şifreleme hakkında daha fazla bilgi için, [Aktarım Katmanı Güvenliği (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security), daha önce GÜVENLI yuva KATMANı (SSL), [Azure Active Directory açma kimlik doğrulaması (Azure AD OAuth)](../active-directory/develop/index.yml), mantıksal uygulamanızı Azure API Management ile gösterme veya gelen çağrıları yapan IP adreslerini kısıtlama gibi) hakkında daha fazla bilgi için bkz. [İstek tabanlı tetikleyicilere gelen çağrılar Için güvenli erişim ve veri erişimi](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure hesabı ve aboneliği Aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolabilirsiniz](https://azure.microsoft.com/free/).

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md)hakkında temel bilgi. Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps](../logic-apps/logic-apps-overview.md)olduğunu gözden geçirin.

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Istek tetikleyicisi Ekle

Bu yerleşik tetikleyici, HTTPS üzerinden *yalnızca* gelen istekleri işleyebilen el ile çağrılabilir bir uç nokta oluşturur. Bir çağıran bu uç noktaya bir istek gönderdiğinde, [istek tetikleyicisi](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) ateşlenir ve mantıksal uygulamayı çalıştırır. Bu tetikleyiciyi çağırma hakkında daha fazla bilgi için, bkz. [Azure Logic Apps ' de HTTPS uç noktalarına çağrı, tetikleyici veya iç içe geçme iş akışları](../logic-apps/logic-apps-http-endpoint.md).

Mantıksal uygulamanız bir gelen isteği yalnızca [sınırlı bir süre](../logic-apps/logic-apps-limits-and-config.md#http-limits)için açık tutar. Mantıksal uygulamanız bir [yanıt eylemi](#add-response)içerdiğinde, mantıksal uygulamanız bu süre geçtikten sonra arayana geri yanıt göndermezse, mantıksal uygulamanız `504 GATEWAY TIMEOUT` çağırana bir durum döndürür. Mantıksal uygulamanız bir yanıt eylemi içermiyorsa, mantıksal uygulamanız hemen `202 ACCEPTED` çağırana bir durum döndürür.

1. [Azure portalında](https://portal.azure.com) oturum açın. Boş bir mantıksal uygulama oluşturma.

1. Mantıksal uygulama Tasarımcısı açıldıktan sonra arama kutusuna `http request` filtreniz olarak girin. Tetikleyiciler listesinden **BIR http isteği alındığında** tetiklenir ' ı seçin.

   ![Istek tetikleyicisi seçin](./media/connectors-native-reqres/select-request-trigger.png)

   Istek tetikleyicisi şu özellikleri gösterir:

   ![İstek tetikleyicisi](./media/connectors-native-reqres/request-trigger.png)

   | Özellik adı | JSON Özellik adı | Gerekli | Açıklama |
   |---------------|--------------------|----------|-------------|
   | **HTTP POST URL 'SI** | seçim | Yes | Mantıksal uygulamayı kaydettikten sonra oluşturulan ve mantıksal uygulamanızı çağırmak için kullanılan uç nokta URL 'SI |
   | **İstek gövdesi JSON şeması** | `schema` | No | Gelen istek gövdesindeki özellikleri ve değerleri açıklayan JSON şeması |
   |||||

1. **Istek GÖVDESI JSON şeması** kutusunda, isteğe bağlı olarak, gelen istekteki gövdeyi açıklayan bir JSON şeması girin, örneğin:

   ![Örnek JSON şeması](./media/connectors-native-reqres/provide-json-schema.png)

   Tasarımcı, istekteki özelliklerin belirteçlerini oluşturmak için bu şemayı kullanır. Bu şekilde, mantıksal uygulamanız, iş akışınıza tetikleyici aracılığıyla istekten verileri ayrıştırtırabilir, tüketebilir ve geçirebilir.

   Örnek şema aşağıda verilmiştir:

   ```json
   {
      "type": "object",
      "properties": {
         "account": {
            "type": "object",
            "properties": {
               "name": {
                  "type": "string"
               },
               "ID": {
                  "type": "string"
               },
               "address": {
                  "type": "object",
                  "properties": {
                     "number": {
                        "type": "string"
                     },
                     "street": {
                        "type": "string"
                     },
                     "city": {
                        "type": "string"
                     },
                     "state": {
                        "type": "string"
                     },
                     "country": {
                        "type": "string"
                     },
                     "postalCode": {
                        "type": "string"
                     }
                  }
               }
            }
         }
      }
   }
   ```

   Bir JSON şeması girdiğinizde, tasarımcı, bu üstbilgiyi isteğinize eklemek için bir anımsatıcı gösterir `Content-Type` ve bu üst bilgi değerini olarak ayarlar `application/json` . Daha fazla bilgi için bkz. [içerik türlerini işleme](../logic-apps/logic-apps-content-type.md).

   !["Content-Type" üst bilgisini dahil etmek için anımsatıcı](./media/connectors-native-reqres/include-content-type.png)

   Bu üst bilgi, JSON biçiminde şöyle görünür:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   Beklenen yük (veri) temelinde bir JSON şeması oluşturmak için, [JSONSchema.net](https://jsonschema.net)gibi bir araç kullanabilir veya aşağıdaki adımları izleyebilirsiniz:

   1. Istek tetikleyicisinde, **şema oluşturmak için örnek yük kullan**' ı seçin.

      !["Şema oluşturmak için örnek yük kullan" seçiliyken ekran görüntüsü](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Örnek yükü girin ve **bitti**' yi seçin.

      ![Şema oluşturmak için örnek yük girin](./media/connectors-native-reqres/enter-payload.png)

      Örnek yük aşağıda verilmiştir:

      ```json
      {
         "account": {
            "name": "Contoso",
            "ID": "12345",
            "address": {
               "number": "1234",
               "street": "Anywhere Street",
               "city": "AnyTown",
               "state": "AnyState",
               "country": "USA",
               "postalCode": "11111"
            }
         }
      }
      ```

1. Gelen çağrının, belirtilen şemadan eşleşen bir istek gövdesine sahip olup olmadığını denetlemek için şu adımları izleyin:

   1. Istek tetikleyicisinin başlık çubuğunda üç nokta düğmesini (**...**) seçin.

   1. Tetikleyicinin ayarlarında, **şema doğrulamasını** açın ve **bitti**' yi seçin.

      Gelen çağrının istek gövdesi şemanıza eşleşmezse, tetikleyici bir `HTTP 400 Bad Request` hata döndürür.

1. Ek özellikler belirtmek için **yeni parametre Ekle** listesini açın ve eklemek istediğiniz parametreleri seçin.

   | Özellik adı | JSON Özellik adı | Gerekli | Açıklama |
   |---------------|--------------------|----------|-------------|
   | **Yöntem** | `method` | No | Gelen isteğin mantıksal uygulamayı çağırmak için kullanması gereken Yöntem |
   | **Göreli yol** | `relativePath` | No | Mantıksal uygulamanın uç nokta URL 'sinin kabul edebileceği parametrenin göreli yolu |
   |||||

   Bu örnek, **Yöntem** özelliğini ekler:

   ![Yöntem parametresi Ekle](./media/connectors-native-reqres/add-parameters.png)

   **Yöntem** özelliği tetikleyicide görünür, böylece listeden bir yöntem seçebilirsiniz.

   ![Select yöntemi](./media/connectors-native-reqres/select-method.png)

1. Şimdi, iş akışınızda sonraki adım olarak başka bir eylem ekleyin. Tetikleyici altında, eklemek istediğiniz eylemi bulabilmeniz için bir **sonraki adım** ' ı seçin.

   Örneğin, özel bir yanıt döndürmek için kullanabileceğiniz ve bu konunun ilerleyen kısımlarında açıklanan [bir yanıt eylemi ekleyerek](#add-response)isteğe yanıt verebilirsiniz.

   Mantıksal uygulamanız gelen isteği yalnızca [sınırlı bir süre](../logic-apps/logic-apps-limits-and-config.md#http-limits)için açık tutar. Mantıksal uygulama iş akışınızın bir yanıt eylemi içerdiğini varsayarsak, mantıksal uygulama bu süre geçtikten sonra bir yanıt döndürmezse, mantıksal uygulamanız bir `504 GATEWAY TIMEOUT` arayana döndürür. Aksi takdirde, mantıksal uygulamanız bir yanıt eylemi içermiyorsa, mantıksal uygulamanız hemen `202 ACCEPTED` çağırana bir yanıt döndürür.

1. İşiniz bittiğinde mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

   Bu adım, mantıksal uygulamayı tetikleyen isteği göndermek için kullanılacak URL 'YI oluşturur. Bu URL 'yi kopyalamak için URL 'nin yanındaki Kopyala simgesini seçin.

   ![Mantıksal uygulamanızı tetiklemenin kullanılacağı URL](./media/connectors-native-reqres/generated-url.png)

   > [!NOTE]
   > **#** İstek tetikleyicisine çağrı yaparken URI 'ye karma veya sterlin () eklemek istiyorsanız, bunun yerine bu kodlanmış sürümü kullanın:`%25%23`

1. Mantıksal uygulamanızı test etmek için, oluşturulan URL 'ye bir HTTP isteği gönderin.

   Örneğin, HTTP isteğini göndermek için [Postman](https://www.getpostman.com/) gibi bir araç kullanabilirsiniz. Tetikleyicinin temel alınan JSON tanımı ve bu tetikleyiciyi çağırma hakkında daha fazla bilgi için, bu konulara bakın, [tetikleyici türü isteyin](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) ve [Azure Logic Apps içindeki HTTP uç noktalarına çağrı, tetikleyici veya iç Içe iş akışları çağırın](../logic-apps/logic-apps-http-endpoint.md).

Mantıksal uygulamanıza yönelik güvenlik, yetkilendirme ve şifreleme hakkında daha fazla bilgi için, [Aktarım Katmanı Güvenliği (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security), daha önce GÜVENLI yuva KATMANı (SSL), [Azure Active Directory açma kimlik doğrulaması (Azure AD OAuth)](../active-directory/develop/index.yml), mantıksal uygulamanızı Azure API Management ile gösterme veya gelen çağrıları yapan IP adreslerini kısıtlama gibi) hakkında daha fazla bilgi için bkz. [İstek tabanlı tetikleyicilere gelen çağrılar Için güvenli erişim ve veri erişimi](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

## <a name="trigger-outputs"></a>Tetikleme çıkışları

Istek tetikleyicisinden alınan çıktılar hakkında daha fazla bilgi aşağıda verilmiştir:

| JSON Özellik adı | Veri türü | Açıklama |
|--------------------|-----------|-------------|
| `headers` | Nesne | İstekten üstbilgileri açıklayan bir JSON nesnesi |
| `body` | Nesne | İstekten gelen gövde içeriğini açıklayan bir JSON nesnesi |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Yanıt eylemi ekleme

Gelen istekleri işlemek için Istek tetikleyicisi kullandığınızda, yanıtı modelleyebilir ve yerleşik [Yanıt eylemini](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action)kullanarak yük sonuçlarını çağırana geri gönderebilirsiniz. *Yalnızca* istek tetikleyicisi ile yanıt eylemini kullanabilirsiniz. Istek tetikleyicisi ve yanıt eylemiyle Bu birleşim, [istek-yanıt modelini](https://en.wikipedia.org/wiki/Request%E2%80%93response)oluşturur. Foreach döngüleri ve Until döngüleri ve paralel dallar dışında, yanıt eylemini iş akışınızda istediğiniz yere ekleyebilirsiniz.

> [!IMPORTANT]
> Bir yanıt eylemi bu üstbilgileri içeriyorsa Logic Apps, bu üst bilgileri herhangi bir uyarı veya hata göstermeden oluşturulan yanıt iletisinden kaldırır:
>
> * `Allow`
> * `Content-*``Content-Disposition`,, `Content-Encoding` , ve için `Content-Type` post ve put işlemlerini KULLANıRKEN, ancak get işlemlerine dahil olmayan üstbilgiler
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Logic Apps, bu üst bilgilerle bir yanıt eylemi olan mantıksal uygulamaları kaydetmenizi durdurmasa da, Logic Apps bu üst bilgileri yoksayar.

1. Mantıksal uygulama Tasarımcısı ' nda, yanıt eklemek istediğiniz adım altında **yeni adım**' ı seçin.

   Örneğin, daha önce Istek tetikleyicisi kullanımı:

   ![Yeni adım Ekle](./media/connectors-native-reqres/add-response.png)

   Adımlar arasında bir eylem eklemek için, işaretçinizi Bu adımlar arasındaki oka taşıyın. Görüntülenen artı işaretini ( **+** ) seçin ve ardından **Eylem Ekle**' yi seçin.

1. **Eylem seçin** altında, arama kutusuna `response` filtreniz olarak girin ve **Yanıt** eylemini seçin.

   ![Yanıt eylemini seçin](./media/connectors-native-reqres/select-response-action.png)

   Istek tetikleyicisi kolaylık sağlaması için bu örnekte daraltılır.

1. Yanıt iletisi için gereken tüm değerleri ekleyin.

   Bazı alanlarda, kutularının içine tıklanması dinamik içerik listesini açar. Daha sonra iş akışındaki önceki adımlardan bulunan mevcut çıkışları temsil eden belirteçleri seçebilirsiniz. Önceki örnekte belirtilen şemadan özellikler artık dinamik içerik listesinde görünür.

   Örneğin, **üst bilgiler** kutusu için, `Content-Type` anahtar adı olarak ekleyin ve anahtar değerini `application/json` Bu konunun önceki kısımlarında belirtildiği gibi olarak ayarlayın. **Gövde** kutusu için dinamik içerik listesinden tetikleyiciyi ilet çıktısını seçebilirsiniz.

   ![Yanıt eylemi ayrıntıları](./media/connectors-native-reqres/response-details.png)

   Üst bilgileri JSON biçiminde görüntülemek için **metin görünümüne geç**' i seçin.

   ![Üstbilgiler-metin görünümüne geç](./media/connectors-native-reqres/switch-to-text-view.png)

   Yanıt eyleminde ayarlayabileceğiniz özellikler hakkında daha fazla bilgi bulabilirsiniz.

   | Özellik adı | JSON Özellik adı | Gerekli | Açıklama |
   |---------------|--------------------|----------|-------------|
   | **Durum kodu** | `statusCode` | Yes | Yanıtta döndürülecek durum kodu |
   | **Üst Bilgiler** | `headers` | No | Yanıta eklenecek bir veya daha fazla üstbilgiyi açıklayan bir JSON nesnesi |
   | **Gövde** | `body` | No | Yanıt gövdesi |
   |||||

1. Yanıt gövdesi için JSON şeması gibi ek özellikler belirtmek için **yeni parametre Ekle** listesini açın ve eklemek istediğiniz parametreleri seçin.

1. İşiniz bittiğinde mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

* [İstek tabanlı tetikleyicilere gelen çağrılar için güvenli erişim ve veri erişimi](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
* [Logic Apps için bağlayıcılar](../connectors/apis-list.md)
