---
title: JSON verilerini sıvı dönüştürmeleri ile Dönüştür-Azure Logic Apps | Microsoft Docs
description: Logic Apps ve sıvı şablonu kullanarak gelişmiş JSON dönüştürmeleri için dönüşümler veya haritalar oluşturma
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/16/2018
ms.openlocfilehash: 203c57a2755a3287566a774e2878a87b847337b9
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900654"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Azure Logic Apps 'te likit şablonlar ile gelişmiş JSON dönüştürmeleri gerçekleştirin

Mantıksal uygulamalarınızda, JSON **oluşturma** veya **ayrıştırma**gibi yerel veri Işleme eylemleriyle temel JSON dönüşümleri gerçekleştirebilirsiniz. Gelişmiş JSON dönüştürmeleri gerçekleştirmek için, esnek Web uygulamalarına yönelik açık kaynaklı bir şablon dili olan [likit](https://shopify.github.io/liquid/)ile şablonlar veya haritalar oluşturabilirsiniz. Bir likit şablon, JSON çıkışının nasıl dönüştürüleceğini ve yinelemeler, denetim akışları, değişkenler vb. gibi daha karmaşık JSON dönüştürmelerinin nasıl desteklediğine ilişkin tanımlar. 

Mantıksal uygulamanızda bir likit dönüştürme gerçekleştirebilmek için önce JSON ile JSON eşlemeyi bir sıvı şablonuyla tanımlamanız ve bu eşlemeyi tümleştirme hesabınızda depolamanız gerekir. Bu makalede, bu likit şablon veya haritanın nasıl oluşturulduğu ve kullanılacağı gösterilir. 

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Bir aboneliğiniz yoksa [ücretsiz bir Azure hesabı ile başlayabilirsiniz](https://azure.microsoft.com/free/). Ya da [bir Kullandıkça Öde aboneliğine kaydolun](https://azure.microsoft.com/pricing/purchase-options/).

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

* Temel bir [tümleştirme hesabı](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* [Sıvı şablonu dili](https://shopify.github.io/liquid/) hakkında temel bilgi.

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Tümleştirme hesabınız için likit şablon veya eşleme oluşturma

1. Bu örnekte, bu adımda açıklanan örnek likit şablonunu oluşturun. Sıvı şablonunuzda, [dotlikit](https://dotliquidmarkup.org/) ve C# adlandırma kurallarını kullanan [sıvı filtrelerini](https://shopify.github.io/liquid/basics/introduction/#filters)kullanabilirsiniz. 

   > [!NOTE]
   > Filtre adlarının şablonunuzda *tümce* kullanmasına dikkat edin. Aksi takdirde, filtreler çalışmaz.

   ```json
   {%- assign deviceList = content.devices | Split: ', ' -%}
   
   {
      "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
      "firstNameUpperCase": "{{content.firstName | Upcase}}",
      "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
      "devices" : [
         {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
        {%- endfor -%}
        ]
   }
   ```

2. [Azure Portal](https://portal.azure.com) oturum açın. Ana Azure menüsünde **tüm kaynaklar**' ı seçin. Arama kutusunda, tümleştirme hesabınızı bulun ve seçin.

   ![Tümleştirme hesabı seçin](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

3.  **Bileşenler**altında **haritalar**' ı seçin.

    ![Haritaları Seç](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

4. **Ekle** ' yi seçin ve Haritalarınız için bu ayrıntıları sağlayın:

   | Özellik | Value | Açıklama | 
   |----------|-------|-------------|
   | **Name** | JsonToJsonTemplate | Bu örnekteki "JsonToJsonTemplate" olan haritaınızın adı | 
   | **Eşleme türü** | **sıvı** | Haritalarınızın türü. JSON-JSON dönüştürmesi için **likit**' i seçmeniz gerekir. | 
   | **Harita** | "SimpleJsonToJsonTemplate. sıvı" | Bu örnekte "SimpleJsonToJsonTemplate. sıvı" olan dönüştürme için kullanılacak mevcut bir likit şablon veya eşleme dosyası. Bu dosyayı bulmak için dosya seçiciyi kullanabilirsiniz. |
   ||| 

   ![Likit Şablon Ekle](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>JSON dönüştürmesi için likit eylemi ekleme

1. Azure portal [boş bir mantıksal uygulama oluşturmak](../logic-apps/quickstart-create-first-logic-app-workflow.md)için aşağıdaki adımları izleyin.

2. Mantıksal uygulama Tasarımcısı ' nda, mantıksal uygulamanıza [istek tetikleyiciyi](../connectors/connectors-native-reqres.md#add-request) ekleyin.

3. Tetikleyici altında **yeni adım**' ı seçin. 
   Arama kutusuna filtreniz olarak "likit" yazın ve şu eylemi seçin: **JSON 'ı JSON 'a Dönüştür-sıvı**

   ![Sıvı eylemini bul ve Seç](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. Dinamik içerik listesinin görünmesi için **içerik** kutusunun içine tıklayın ve **gövde** belirtecini seçin.
  
   ![Gövde Seç](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. **Eşleme** listesinden, bu örnekte "JsonToJsonTemplate" olan sıvı şablonunuzu seçin.

   ![Eşleme Seç](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Haritalar listesi boşsa, büyük olasılıkla mantıksal uygulamanız tümleştirme hesabınıza bağlı değildir. 
   Mantıksal uygulamanızı likit şablon veya eşleme içeren tümleştirme hesabına bağlamak için şu adımları izleyin:

   1. Mantıksal uygulama menünüzde **Iş akışı ayarları**' nı seçin.

   2. **Tümleştirme hesabı seçin** listesinden tümleştirme hesabınızı seçin ve **Kaydet**' i seçin.

      ![Mantıksal uygulamayı tümleştirme hesabına bağlama](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>Mantıksal uygulamanızı test etme

JSON girişini [Postman](https://www.getpostman.com/postman) veya benzer bir araçtan mantıksal uygulamanıza gönderin. Mantıksal uygulamanızdan dönüştürülmüş JSON çıktısı şu örneğe benzer şekilde görünür:
  
![Örnek çıkış](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Diğer likit eylem örnekleri
Sıvı yalnızca JSON dönüşümlerine sınırlı değildir. Aşağıda, sıvı kullanan diğer kullanılabilir dönüştürme eylemleri verilmiştir.

* JSON 'ı metne Dönüştür
  
  Bu örnek için kullanılan likit şablon aşağıda verilmiştir:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Örnek girişler ve çıktılar aşağıda verilmiştir:
  
   ![Örnek çıkış JSON-Text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* XML-JSON dönüştürmesi uygula
  
  Bu örnek için kullanılan likit şablon aşağıda verilmiştir:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Örnek girişler ve çıktılar aşağıda verilmiştir:

   ![Örnek çıkış XML-JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* XML 'e metin dönüştürme
  
  Bu örnek için kullanılan likit şablon aşağıda verilmiştir:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Örnek girişler ve çıktılar aşağıda verilmiştir:

   ![Örnek çıkış XML-Text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Enterprise Integration Pack hakkında daha fazla bilgi edinin] (../logic-apps/logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack hakkında bilgi edinin")  
* [Haritalar hakkında daha fazla bilgi edinin] (../logic-apps/logic-apps-enterprise-integration-maps.md "Kurumsal tümleştirme haritaları hakkında bilgi edinin")  

