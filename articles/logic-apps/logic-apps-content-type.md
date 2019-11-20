---
title: İçerik türlerini işleme-Azure Logic Apps
description: Logic Apps tasarım zamanında ve çalışma zamanında içerik türlerini nasıl işleyeceğinizi öğrenin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/20/2018
ms.openlocfilehash: 97897da13c70c29834b1fc276829b316416efd8d
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868922"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>Azure Logic Apps içerik türlerini işleme

Çeşitli içerik türleri bir mantıksal uygulama aracılığıyla (örneğin, JSON, XML, düz dosyalar ve ikili veriler) akabilir. Logic Apps tüm içerik türlerini desteklese de, bazıları yerel desteğe sahiptir ve mantıksal uygulamalarınızda atama veya dönüştürme gerektirmez. Diğer türler gerektiğinde atama veya dönüştürme gerektirebilir. Bu makalede, Logic Apps içerik türlerini nasıl işleyeceği ve gerektiğinde bu türleri nasıl doğru şekilde dönüştürebileceğiniz ve dönüştürebileceğiniz açıklanır.

Logic Apps içerik türlerini işlemenin uygun yolunu öğrenmek için, http çağrılarında `Content-Type` üst bilgi değerine dayanır, örneğin:

* [uygulama/JSON](#application-json) (yerel tür)
* [metin/düz](#text-plain) (yerel tür)
* [Uygulama/XML ve uygulama/sekizli-akış](#application-xml-octet-stream)
* [Diğer içerik türleri](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>uygulama/json

Logic Apps, *uygulama/JSON* içerik türüne sahip tüm Istekleri JavaScript GÖSTERIMI (JSON) nesnesi olarak depolar ve işler. Varsayılan olarak, herhangi bir atama olmadan JSON içeriğini ayrıştırabilirsiniz. "Application/JSON" içerik türüne sahip bir üst bilgisine sahip bir isteği ayrıştırmak için bir ifade kullanabilirsiniz. Bu örnek, `animal-type` diziden değeri `dog` , atama olmadan döndürür: 
 
`@body('myAction')['animal-type'][0]` 
  
  ```json
  {
    "client": {
       "name": "Fido",
       "animal-type": [ "dog", "cat", "rabbit", "snake" ]
    }
  }
  ```

Bir üst bilgi belirtmeyen JSON verileriyle çalışıyorsanız, [JSON () işlevini](../logic-apps/workflow-definition-language-functions-reference.md#json)kullanarak bu verileri JSON 'a el ile dönüştürebilirsiniz, örneğin: 
  
`@json(triggerBody())['animal-type']`

### <a name="create-tokens-for-json-properties"></a>JSON özellikleri için belirteçler oluşturma

Logic Apps, bu özellikleri mantıksal uygulamanızın iş akışında daha kolay bir şekilde başvurabilmeniz ve kullanabilmeniz için JSON içeriğindeki özellikleri temsil eden Kullanıcı dostu belirteçler oluşturma özelliğini sağlar.

* **İstek tetikleyicisi**

  Bu tetikleyiciyi Logic App Designer 'da kullandığınızda, almayı düşündüğünüz yükü açıklayan bir JSON şeması sağlayabilirsiniz. 
  Tasarımcı, bu şemayı kullanarak JSON içeriğini ayrıştırır ve JSON içeriğinizin özelliklerini temsil eden Kullanıcı dostu belirteçler oluşturur. 
  Daha sonra mantıksal uygulamanızın iş akışının tamamında bu özellikleri kolayca başvurabilir ve kullanabilirsiniz. 
  
  Bir şemanız yoksa şemayı oluşturabilirsiniz. 
  
  1. Istek tetikleyicisinde, **şema oluşturmak için örnek yük kullan**' ı seçin.  
  
  2. **Örnek BIR JSON yükü girin veya yapıştırın**, örnek bir yük sağlayın ve **bitti**' yi seçin. Örneğin: 

     ![Örnek JSON yükü sağlama](./media/logic-apps-content-type/request-trigger.png)

     Oluşturulan şema şimdi Tetikleyiciniz içinde görüntülenir.

     ![Örnek JSON yükü sağlama](./media/logic-apps-content-type/generated-schema.png)

     Kod görünümü düzenleyicisinde Istek Tetikleyiciniz için temel alınan tanım aşağıdadır:

     ```json
     "triggers": { 
        "manual": {
           "type": "Request",
           "kind": "Http",
           "inputs": { 
              "schema": {
                 "type": "object",
                 "properties": {
                    "client": {
                       "type": "object",
                       "properties": {
                          "animal-type": {
                             "type": "array",
                             "items": {
                                "type": "string"
                             },
                          },
                          "name": {
                             "type": "string"
                          }
                       }
                    }
                 }
              }
           }
        }
     }
     ```

  3. İsteğiniz içinde bir `Content-Type` üst bilgi eklediğinizden ve üstbilginin değerini olarak `application/json`ayarladığınızdan emin olun.

* **JSON eylemini Ayrıştır**

  Bu eylemi Logic App Designer 'da kullandığınızda JSON çıkışını ayrıştırarak JSON içeriğinizdeki özellikleri temsil eden Kullanıcı dostu belirteçler oluşturabilirsiniz. 
  Daha sonra mantıksal uygulamanızın iş akışının tamamında bu özellikleri kolayca başvurabilir ve kullanabilirsiniz. Istek tetikleyicisine benzer şekilde, ayrıştırmak istediğiniz JSON içeriğini açıklayan bir JSON şeması sağlayabilir veya oluşturabilirsiniz. 
  Böylece, Azure Service Bus, Azure Cosmos DB vb. verileri daha kolay bir şekilde kullanabilirsiniz.

  ![JSON Ayrıştır](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>metin/düz

Mantıksal uygulamanız `Content-Type` üst bilgisi olarak `text/plain`ayarlanmış http iletileri aldığında, mantıksal uygulamanız bu iletileri ham biçimde depolar. Bu iletileri, atama olmadan sonraki eylemlere dahil ederseniz, istekler `Content-Type` üst bilgiyle olarak `text/plain`ayarlanır. 

Örneğin, düz bir dosyayla çalışırken, `Content-Type` üst bilgiyle `text/plain` içerik türüne ayarlanmış bir http isteği alabilirsiniz:

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

Daha sonra bu isteği başka bir isteğin gövdesi olarak sonraki bir eylemde gönderirseniz, örneğin `@body('flatfile')`, bu ikinci isteğin de olarak `text/plain`ayarlanmış bir `Content-Type` üstbilgisi vardır. Düz metin olan ancak bir başlık belirtmeyen verilerle çalışıyorsanız, bu ifade gibi [String () işlevini](../logic-apps/workflow-definition-language-functions-reference.md#string) kullanarak bu verileri el ile metne çevirebilirsiniz: 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>Uygulama/XML ve uygulama/sekizli-akış

Logic Apps, `Content-Type` alınan bir http isteği veya yanıtı tarafından her zaman korunur. `Content-Type` Mantıksal uygulamanız, olarak `application/octet-stream`ayarlanmış içeriğe sahip içerik alırsa ve bu içeriği atama olmadan sonraki bir eyleme eklerseniz, giden istek de `Content-Type` olarak `application/octet-stream`ayarlanır. Bu şekilde Logic Apps, iş akışı boyunca taşırken verilerin kaybolmamasını garanti edebilir. Ancak, eylem durumu veya girişler ve çıktılar, durum iş akışı üzerinden hareket ederken bir JSON nesnesinde depolanır. 

## <a name="converter-functions"></a>Dönüştürücü işlevleri

Logic Apps, bazı veri türlerini korumak için, otomatik olarak dönüştürülen hem `$content` yükü `$content-type`hem de öğesini koruyan uygun meta verileri içeren ikili base64 kodlamalı bir dizeye içerik dönüştürür. 

Bu liste, bu [işlevleri](../logic-apps/workflow-definition-language-functions-reference.md)kullandığınızda Logic Apps içeriği nasıl dönüştüreceğini açıklar:

* `json()`: Verileri uygulamasına yayınlar`application/json`
* `xml()`: Verileri uygulamasına yayınlar`application/xml`
* `binary()`: Verileri uygulamasına yayınlar`application/octet-stream`
* `string()`: Verileri uygulamasına yayınlar`text/plain`
* `base64()`: İçeriği Base64 kodlamalı bir dizeye dönüştürür
* `base64toString()`: Base64 ile kodlanmış bir dizeyi öğesine dönüştürür`text/plain`
* `base64toBinary()`: Base64 ile kodlanmış bir dizeyi öğesine dönüştürür`application/octet-stream`
* `dataUri()`: Bir dizeyi veri URI 'sine dönüştürür
* `dataUriToBinary()`: Bir veri URI 'sini ikili dizeye dönüştürür
* `dataUriToString()`: Bir veri URI 'sini dizeye dönüştürür

Örneğin, şu içerik gibi olarak `Content-Type` `application/xml`ayarlandığı bir http isteği alırsanız:

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Ve`xml()` `@xml(triggerBody())` işlevleriileifadesinikullanarakbuiçeriğiçevirebilirsinizvesonrabuiçeriği`triggerBody()` daha sonra kullanabilirsiniz. Ya da `@xpath(xml(triggerBody()), '/CustomerName')` ifadesini `xpath()` ve `xml()` işlevleri ile birlikte kullanabilirsiniz. 

## <a name="other-content-types"></a>Diğer içerik türleri

Logic Apps ile birlikte çalışarak diğer içerik türlerini destekler, ancak `$content` değişkenin kodunu çözerek ileti gövdesini el ile almanızı gerektirebilir.

Örneğin, mantıksal uygulamanızın `application/x-www-url-formencoded` içerik türüne sahip bir istek tarafından tetiklendiğini varsayalım. Tüm verileri korumak için, `$content` istek gövdesindeki değişkenin Base64 dizesi olarak kodlanmış bir yükü vardır:

`CustomerName=Frank&Address=123+Avenue`

İstek düz metin veya JSON olmadığından, istek eylemde şu şekilde depolanır:

```json
"body": {
   "$content-type": "application/x-www-url-formencoded",
   "$content": "AAB1241BACDFA=="
}
```

Logic Apps, form verilerini işlemeye yönelik yerel işlevler sağlar, örneğin: 

* [triggerFormDataValue ()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue)
* [triggerFormDataMultiValues ()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues)
* [formDataValue ()](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) 
* [formDataMultiValues ()](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues)

Ya da bu örnek gibi bir ifade kullanarak verilere el ile erişebilirsiniz:

`@string(body('formdataAction'))` 

Giden isteğin aynı `application/x-www-url-formencoded` içerik türü başlığına sahip olmasını istiyorsanız, gibi `@body('formdataAction')`bir ifade kullanarak, isteği herhangi bir atama yapmadan eylemin gövdesine ekleyebilirsiniz. Ancak, bu yöntem yalnızca gövde `body` girişte tek parametre olduğunda işe yarar. `@body('formdataAction')` İfadeyi bir`application/json` istekte kullanmaya çalışırsanız, gövde kodlanmış olarak gönderildiği için bir çalışma zamanı hatası alırsınız.
