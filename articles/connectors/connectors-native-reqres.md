---
title: HTTPS çağrılarını alma ve yanıtlama-Azure Logic Apps
description: Azure Logic Apps kullanarak HTTPS isteklerini ve olaylarını gerçek zamanlı olarak işleyin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewers: klam, LADocs
manager: carmonm
ms.assetid: 566924a4-0988-4d86-9ecd-ad22507858c0
ms.topic: conceptual
ms.date: 10/11/2019
tags: connectors
ms.openlocfilehash: 6062ca1ce09eb243825b1fb9ae4ecb3d5ac95d1a
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264359"
---
# <a name="receive-and-respond-to-incoming-https-calls-by-using-azure-logic-apps"></a>Azure Logic Apps kullanarak gelen HTTPS çağrılarını alma ve yanıtlama

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) ve yerleşik istek tetikleyicisi veya yanıtı eylemiyle, gelen https isteklerini alıp yanıtlayan otomatik görevler ve iş akışları oluşturabilirsiniz. Örneğin, mantıksal uygulamanızı kullanabilirsiniz:

* Şirket içi bir veritabanındaki veriler için bir HTTPS isteği alın ve yanıt verin.
* Dış Web kancası olayı gerçekleştiğinde iş akışı tetikleyin.
* Başka bir mantıksal uygulamadan bir HTTPS çağrısını alın ve yanıtlayın.

> [!NOTE]
> Istek tetikleyicisi, gelen çağrılar için *yalnızca* aktarım katmanı GÜVENLIĞI (TLS) 1,2 ' i destekler. Giden çağrılar TLS 1,0, 1,1 ve 1,2 desteğini desteklemeye devam eder. SSL el sıkışma hataları görürseniz, TLS 1,2 kullandığınızdan emin olun.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolabilirsiniz](https://azure.microsoft.com/free/).

* [Logic Apps](../logic-apps/logic-apps-overview.md)hakkında temel bilgi. Logic Apps 'e yeni başladıysanız, [ilk mantıksal uygulamanızı oluşturmayı](../logic-apps/quickstart-create-first-logic-app-workflow.md)öğrenin.

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Istek tetikleyicisi Ekle

Bu yerleşik tetikleyici, *yalnızca* gelen https isteklerini alabilen el ile ÇAĞRıLABILIR bir HTTPS uç noktası oluşturur. Bu olay gerçekleştiğinde tetikleyici ateşlenir ve mantıksal uygulamayı çalıştırır. Tetikleyicinin temel alınan JSON tanımı ve bu tetikleyiciyi çağırma hakkında daha fazla bilgi için, bkz. [istek tetikleme türü](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) ve [Azure Logic Apps içindeki HTTP uç noktalarına çağrı, tetikleyici veya iç içe geçme iş akışları](../logic-apps/logic-apps-http-endpoint.md).

1. [Azure Portal](https://portal.azure.com)’ında oturum açın. Boş bir mantıksal uygulama oluşturma.

1. Mantıksal uygulama Tasarımcısı açıldıktan sonra, arama kutusuna filtreniz olarak "http isteği" yazın. Tetikleyiciler listesinden, mantıksal uygulama iş akışınızın ilk adımı olan **BIR http isteği alındığında** tetiklenir ' ı seçin.

   ![Istek tetikleyicisi seçin](./media/connectors-native-reqres/select-request-trigger.png)

   Istek tetikleyicisi şu özellikleri gösterir:

   ![İstek tetikleyicisi](./media/connectors-native-reqres/request-trigger.png)

   | Özellik adı | JSON Özellik adı | Gereklidir | Açıklama |
   |---------------|--------------------|----------|-------------|
   | **HTTP POST URL 'SI** | seçim | Yes | Mantıksal uygulamayı kaydettikten sonra oluşturulan ve mantıksal uygulamanızı çağırmak için kullanılan uç nokta URL 'SI |
   | **İstek gövdesi JSON şeması** | `schema` | Hayır | Gelen istek gövdesindeki özellikleri ve değerleri açıklayan JSON şeması |
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

   Bir JSON şeması girdiğinizde tasarımcı, isteğinize `Content-Type` başlığını dahil etmek için bir anımsatıcı gösterir ve bu üst bilgi değerini `application/json` olarak ayarlar. Daha fazla bilgi için bkz. [içerik türlerini işleme](../logic-apps/logic-apps-content-type.md).

   !["Content-Type" üst bilgisini dahil etmek için anımsatıcı](./media/connectors-native-reqres/include-content-type.png)

   Bu üst bilgi, JSON biçiminde şöyle görünür:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   Beklenen yük (veri) temelinde bir JSON şeması oluşturmak için, [JSONSchema.net](https://jsonschema.net)gibi bir araç kullanabilir veya aşağıdaki adımları izleyebilirsiniz:

   1. Istek tetikleyicisinde, **şema oluşturmak için örnek yük kullan**' ı seçin.

      ![Yük 'ten şema oluştur](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Örnek yükü girin ve **bitti**' yi seçin.

      ![Yük 'ten şema oluştur](./media/connectors-native-reqres/enter-payload.png)

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

1. Ek özellikler belirtmek için **yeni parametre Ekle** listesini açın ve eklemek istediğiniz parametreleri seçin.

   | Özellik adı | JSON Özellik adı | Gereklidir | Açıklama |
   |---------------|--------------------|----------|-------------|
   | **Yöntem** | `method` | Hayır | Gelen isteğin mantıksal uygulamayı çağırmak için kullanması gereken Yöntem |
   | **Göreli yol** | `relativePath` | Hayır | Mantıksal uygulamanın uç nokta URL 'sinin kabul edebileceği parametrenin göreli yolu |
   |||||

   Bu örnek, **Yöntem** özelliğini ekler:

   ![Yöntem parametresi Ekle](./media/connectors-native-reqres/add-parameters.png)

   **Yöntem** özelliği tetikleyicide görünür, böylece listeden bir yöntem seçebilirsiniz.

   ![Select yöntemi](./media/connectors-native-reqres/select-method.png)

1. Şimdi, iş akışınızda sonraki adım olarak başka bir eylem ekleyin. Tetikleyici altında, eklemek istediğiniz eylemi bulabilmeniz için bir **sonraki adım** ' ı seçin.

   Örneğin, özel bir yanıt döndürmek için kullanabileceğiniz ve bu konunun ilerleyen kısımlarında açıklanan [bir yanıt eylemi ekleyerek](#add-response)isteğe yanıt verebilirsiniz.

   Mantıksal uygulamanız gelen isteği yalnızca bir dakika boyunca açık tutar. Mantıksal uygulama iş akışınızın bir yanıt eylemi içerdiğini varsayarsak, mantıksal uygulama bu süre geçtikten sonra bir yanıt döndürmezse, mantıksal uygulamanız çağırana `504 GATEWAY TIMEOUT` döndürür. Aksi takdirde, mantıksal uygulamanız bir yanıt eylemi içermiyorsa, mantıksal uygulamanız çağrı yapana hemen `202 ACCEPTED` yanıtı döndürür.

1. İşiniz bittiğinde mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin. 

   Bu adım, mantıksal uygulamayı tetikleyen isteği göndermek için kullanılacak URL 'YI oluşturur. Bu URL 'yi kopyalamak için URL 'nin yanındaki Kopyala simgesini seçin.

   ![Mantıksal uygulamanızı tetiklemenin kullanılacağı URL](./media/connectors-native-reqres/generated-url.png)

1. Mantıksal uygulamanızı tetiklemek için, oluşturulan URL 'ye bir HTTP GÖNDERISI gönderin. Örneğin [Postman](https://www.getpostman.com/)gibi bir araç kullanabilirsiniz.

### <a name="trigger-outputs"></a>Tetikleme çıkışları

Istek tetikleyicisinden alınan çıktılar hakkında daha fazla bilgi aşağıda verilmiştir:

| JSON Özellik adı | Veri türü | Açıklama |
|--------------------|-----------|-------------|
| `headers` | Nesne | İstekten üstbilgileri açıklayan bir JSON nesnesi |
| `body` | Nesne | İstekten gelen gövde içeriğini açıklayan bir JSON nesnesi |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Yanıt eylemi ekleme

Yanıt eylemini, gelen HTTPS isteğine bir yük (veri) ile yanıt vermek için, ancak yalnızca HTTPS isteği tarafından tetiklenen bir mantıksal uygulamada kullanabilirsiniz. Yanıt eylemini iş akışınızın herhangi bir noktasına ekleyebilirsiniz. Bu tetikleyicinin temel alınan JSON tanımı hakkında daha fazla bilgi için bkz. [yanıt eylem türü](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action).

Mantıksal uygulamanız gelen isteği yalnızca bir dakika boyunca açık tutar. Mantıksal uygulama iş akışınızın bir yanıt eylemi içerdiğini varsayarsak, mantıksal uygulama bu süre geçtikten sonra bir yanıt döndürmezse, mantıksal uygulamanız çağırana `504 GATEWAY TIMEOUT` döndürür. Aksi takdirde, mantıksal uygulamanız bir yanıt eylemi içermiyorsa, mantıksal uygulamanız çağrı yapana hemen `202 ACCEPTED` yanıtı döndürür.

1. Mantıksal uygulama Tasarımcısı ' nda, yanıt eklemek istediğiniz adım altında **yeni adım**' ı seçin.

   Örneğin, daha önce Istek tetikleyicisi kullanımı:

   ![Yeni adım Ekle](./media/connectors-native-reqres/add-response.png)

   Adımlar arasında bir eylem eklemek için, işaretçinizi Bu adımlar arasındaki oka taşıyın. Görüntülenen artı işaretini ( **+** ) seçin ve ardından **Eylem Ekle**' yi seçin.

1. **Eylem seçin**altında, arama kutusuna filtreniz olarak "yanıt" yazın ve **Yanıt** eylemini seçin.

   ![Yanıt eylemini seçin](./media/connectors-native-reqres/select-response-action.png)

   Istek tetikleyicisi kolaylık sağlaması için bu örnekte daraltılır.

1. Yanıt iletisi için gereken tüm değerleri ekleyin. 

   Bazı alanlarda, kutularının içine tıklanması dinamik içerik listesini açar. Daha sonra iş akışındaki önceki adımlardan bulunan mevcut çıkışları temsil eden belirteçleri seçebilirsiniz. Önceki örnekte belirtilen şemadan özellikler artık dinamik içerik listesinde görünür.

   Örneğin, **üst bilgiler** kutusu için, anahtar adı olarak `Content-Type` ' i ekleyin ve anahtar değerini bu konunun önceki kısımlarında belirtildiği gibi `application/json` olarak ayarlayın. **Gövde** kutusu için dinamik içerik listesinden tetikleyiciyi ilet çıktısını seçebilirsiniz.

   ![Yanıt eylemi ayrıntıları](./media/connectors-native-reqres/response-details.png)

   Üst bilgileri JSON biçiminde görüntülemek için **metin görünümüne geç**' i seçin.

   ![Üstbilgiler-metin görünümüne geç](./media/connectors-native-reqres/switch-to-text-view.png)

   Yanıt eyleminde ayarlayabileceğiniz özellikler hakkında daha fazla bilgi bulabilirsiniz. 

   | Özellik adı | JSON Özellik adı | Gereklidir | Açıklama |
   |---------------|--------------------|----------|-------------|
   | **Durum Kodu** | `statusCode` | Yes | Yanıtta döndürülecek durum kodu |
   | **Üst Bilgiler** | `headers` | Hayır | Yanıta eklenecek bir veya daha fazla üstbilgiyi açıklayan bir JSON nesnesi |
   | **Gövde** | `body` | Hayır | Yanıt gövdesi |
   |||||

1. Yanıt gövdesi için JSON şeması gibi ek özellikler belirtmek için **yeni parametre Ekle** listesini açın ve eklemek istediğiniz parametreleri seçin.

1. İşiniz bittiğinde mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin. 

## <a name="next-steps"></a>Sonraki adımlar

* [Logic Apps için bağlayıcılar](../connectors/apis-list.md)
