---
title: Biçimleri arasında XML dönüştürme
description: Enterprise Integration Pack ile Azure Logic Apps biçimler arasında XML dönüştüren dönüşümler veya haritalar oluşturma
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 038c1d4c0f0b5ffd7b9aabea2de32e3a44e3b221
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97654141"
---
# <a name="create-maps-that-transform-xml-between-formats-in-azure-logic-apps-with-enterprise-integration-pack"></a>Enterprise Integration Pack ile Azure Logic Apps’te XML biçimini dönüştüren eşlemeler oluşturma

Kurumsal tümleştirme Dönüşüm bağlayıcısı, verileri bir biçimden başka bir biçime dönüştürür. Örneğin günün tarihini YearMonthDay biçiminde içeren bir gelen ileti olabilir. Dönüşüm kullanarak bu tarihin MonthDayYear biçiminde olmasını sağlayabilirsiniz.

## <a name="what-does-a-transform-do"></a>Dönüştürme ne yapar?
Eşleme olarak da bilinen bir dönüşüm, kaynak XML şemasından (giriş) ve hedef XML şemasından (çıktı) oluşur. Dize işlemeleri, koşullu atamalar, aritmetik ifadeler, tarih saat formatları ve hatta döngü yapıları dahil olmak üzere verileri düzenlemeye veya denetlemeye yardımcı olması için farklı yerleşik işlevleri kullanabilirsiniz.

## <a name="how-to-create-a-transform"></a>Dönüşüm nasıl oluşturulur?
Visual Studio [Kurumsal TÜMLEŞTIRME SDK](https://aka.ms/vsmapsandschemas)kullanarak bir dönüşüm/eşleme oluşturabilirsiniz. Dönüşümü oluşturma ve test etmeyi bitirdiğinizde, dönüşümü tümleştirme hesabınıza yüklersiniz. 

## <a name="how-to-use-a-transform"></a>Dönüştürme kullanma
Dönüştürme/eşlemeyi tümleştirme hesabınıza yükledikten sonra, mantıksal uygulama oluşturmak için bunu kullanabilirsiniz. Mantıksal uygulama her tetiklendiğinde (ve dönüştürülmesi gereken giriş içeriği), mantıksal uygulama, dönüştürmelerinizi çalıştırır.

**Dönüştürme kullanma adımları şunlardır**:

### <a name="prerequisites"></a>Önkoşullar

* Bir tümleştirme hesabı oluşturun ve buna bir eşleme ekleyin  

Artık önkoşulları karşıladığınıza göre, mantıksal uygulamanızı oluşturmanız zaman atalım:  

1. Bir mantıksal uygulama oluşturun ve [bunu eşlemeyi içeren tümleştirme hesabınıza bağlayın](./logic-apps-enterprise-integration-create-integration-account.md "Tümleştirme hesabını bir Logic App 'e bağlamayı öğrenin") .
2. Mantıksal uygulamanıza **istek** tetikleyicisi ekleme  
   ![Istek tetikleyicisi seçiliyken "Microsoft tarafından yönetilen API 'Leri göster" açılır listesinin ekran görüntüsü. Açılan menü, Visual Studio Enterprise tümleştirme SDK 'Sı kullanılarak oluşturulan bir mantıksal uygulamadır.](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. Önce **Eylem Ekle** ÖĞESINI seçerek **XML dönüştürme** eylemini ekleyin   
   ![Istek tetikleyicisi ekranında seçili "Eylem Ekle" düğmesinin gösterildiği ekran görüntüsü.](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. Tüm eylemleri kullanmak istediğiniz bir şekilde filtrelemek için, arama kutusuna *dönüştürme* sözcüğü yazın  
   ![Istek tetikleyicisine eklenebilmesi için "Microsoft tarafından yönetilen API 'Leri göster" açılan menüsünde XML dönüşümü eyleminin nasıl aranacağını gösteren ekran görüntüsü.](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. **XML dönüştürme** eylemini seçin   
6. Dönüştürmek istediğiniz XML **içeriğini** ekleyin. HTTP isteğinde aldığınız XML verilerini **içerik** olarak kullanabilirsiniz. Bu örnekte, mantıksal uygulamayı tetikleyen HTTP isteğinin gövdesini seçin.

   > [!NOTE]
   > **Transform XML** içeriğinin XML olduğundan emin olun. İçerik XML 'de değilse veya Base64 kodlamalı ise, içeriği işleyen bir ifade belirtmeniz gerekir. Örneğin, [](logic-apps-workflow-definition-language.md#functions) ```@base64ToBinary``` içeriği kod çözme veya ```@xml``` içeriği XML olarak işleme gibi işlevleri kullanabilirsiniz.
 

7. Dönüştürmeyi gerçekleştirmek için kullanmak istediğiniz **haritanın** adını seçin. Eşleme, tümleştirme hesabınızda zaten olmalıdır. Önceki bir adımda, mantıksal uygulama erişiminizi eşlemenizi içeren tümleştirme hesabınıza zaten verdin.      
   ![Istek tetikleyicisi için dönüştürme XML ekranındaki Içerik ve harita alanlarını gösteren ekran görüntüsü.](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. Çalışmanızı kaydetme  
    ![Logic Apps tasarımcısında Kaydet düğmesini gösteren ekran görüntüsü.](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

Bu noktada, eşlemenizi ayarlamayı tamamladınız. Gerçek bir dünya uygulamasında, dönüştürülmüş verileri SalesForce gibi bir LOB uygulamasında depolamak isteyebilirsiniz. Dönüştürmenin çıkışını Salesforce 'a göndermek için kolayca bir eylem olarak kullanabilirsiniz. 

Artık HTTP uç noktasına istek yaparak dönüşümlerinizi test edebilirsiniz.  


## <a name="features-and-use-cases"></a>Özellikler ve kullanım örnekleri
* Bir haritada oluşturulan dönüşüm, bir belgeden diğerine ad ve adres kopyalama gibi basit olabilir. Ya da, kullanıma hazır eşleme işlemlerini kullanarak daha karmaşık dönüştürmeler oluşturabilirsiniz.  
* Dizeler, tarih saat işlevleri vb. dahil olmak üzere birden çok harita işlemi veya işlevi hazır.  
* Şemalar arasında doğrudan veri kopyalama yapabilirsiniz. SDK 'ya dahil olan eşleştiricisindeki bu, kaynak şemadaki öğeleri hedef şemadaki karşılıklarıyla bağlayan bir çizgi çizmek kadar basittir.  
* Bir harita oluştururken, eşlemenin, oluşturduğunuz tüm ilişkileri ve bağlantıları gösteren bir grafik gösterimini görüntüleyebilirsiniz.
* Örnek bir XML iletisi eklemek için test eşleme özelliğini kullanın. Basit bir tıklama ile oluşturduğunuz Haritayı test edebilir ve oluşturulan çıktıyı görebilirsiniz.  
* Mevcut haritaları karşıya yükle  
* XML biçimi desteğini içerir.

## <a name="advanced-features"></a>Gelişmiş özellikler

### <a name="reference-assembly-or-custom-code-from-maps"></a>Eşlemlerden başvuru derleme veya özel kod 
Dönüştürme eylemi, dış derlemeye yönelik başvuru ile haritaları veya dönüşümleri de destekler. Bu özellik, doğrudan XSLT haritalarından özel .NET kodu çağrıları sağlar. Eşlemlerde derlemeyi kullanmak için Önkoşullar aşağıda verilmiştir.

* Eşlemden başvurulan haritanın ve derlemenin [tümleştirme hesabına yüklenmesi](./logic-apps-enterprise-integration-maps.md)gerekir. 

  > [!NOTE]
  > Haritanın ve derlemenin belirli bir sırada karşıya yüklenmesi gerekir. Derlemeye başvuran Haritayı karşıya yüklemeden önce derlemeyi karşıya yüklemeniz gerekir.

* Haritada Ayrıca bu öznitelikler ve derleme kodu çağrısını içeren bir CDATA bölümü bulunmalıdır:

    * **ad** özel derleme adıdır.
    * **ad alanı** , derlemeinizdeki özel kodu içeren ad alanıdır.

  Bu örnekte, "XslUtilitiesLib" adlı bir derlemeye başvuran ve derlemeden yöntemi çağıran bir harita gösterilmektedir `circumreference` .

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:msxsl="urn:schemas-microsoft-com:xslt" xmlns:user="urn:my-scripts">
  <msxsl:script language="C#" implements-prefix="user">
    <msxsl:assembly name="XsltHelperLib"/>
    <msxsl:using namespace="XsltHelpers"/>
    <![CDATA[public double circumference(int radius){ XsltHelper helper = new XsltHelper(); return helper.circumference(radius); }]]>
  </msxsl:script>
  <xsl:template match="data">
   <circles>
    <xsl:for-each select="circle">
      <circle>
        <xsl:copy-of select="node()"/>
          <circumference>
            <xsl:value-of select="user:circumference(radius)"/>
          </circumference>
      </circle>
    </xsl:for-each>
   </circles>
  </xsl:template>
    </xsl:stylesheet>
  ```


### <a name="byte-order-mark"></a>Bayt sıra Işareti
Varsayılan olarak, dönüşümden gelen yanıt bayt sıra Işaretiyle (BOM) başlar. Bu işlevselliğe yalnızca kod görünümü düzenleyicisinde çalışırken erişebilirsiniz. Bu işlevi devre dışı bırakmak için, `disableByteOrderMark` özelliği için şunu belirtin `transformOptions` :

```json
"Transform_XML": {
    "inputs": {
        "content": "@{triggerBody()}",
        "integrationAccount": {
            "map": {
                "name": "TestMap"
            }
        },
        "transformOptions": "disableByteOrderMark"
    },
    "runAfter": {},
    "type": "Xslt"
}
```





## <a name="learn-more"></a>Daha fazla bilgi edinin
* [Enterprise Integration Pack hakkında daha fazla bilgi edinin](../logic-apps/logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack hakkında bilgi edinin")  
* [Haritalar hakkında daha fazla bilgi edinin](../logic-apps/logic-apps-enterprise-integration-maps.md "Kurumsal tümleştirme haritaları hakkında bilgi edinin")  