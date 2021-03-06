---
title: Yineleme eylemlerine döngüler ekleyin
description: Azure Logic Apps’te iş akışı eylemlerini veya işlem dizilerini tekrarlayacak döngüler oluşturma
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/05/2019
ms.openlocfilehash: aa4be5852b4f8af00346a3ea9a86b13a85f99824
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93358466"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Azure Logic Apps’te iş akışı eylemlerini veya işlem dizilerini tekrarlayacak döngüler oluşturma

Mantıksal uygulamanızdaki bir diziyi işlemek için ["foreach" döngüsü](#foreach-loop)oluşturabilirsiniz. Bu döngü dizideki her öğe için bir veya daha fazla eylemi yineler. Bir "foreach" döngüsünün işleyeme işleminde dizi öğe sayısı sınırı için bkz. [eşzamanlılık, döngü ve toplu işleme sınırları](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

Bir koşul karşılanana veya bir durum değişikliği yapılıncaya kadar eylemleri yinelemek için, ["Until" döngüsünü](#until-loop)oluşturabilirsiniz. Mantıksal uygulamanız öncelikle döngü içindeki tüm eylemleri çalıştırır ve ardından koşulu veya durumu denetler. Koşul karşılanıyorsa, döngü duraklar. Aksi takdirde döngü yinelenir. Mantıksal uygulama çalıştırabileceğine yönelik "Until" döngülerinin sahip olduğu varsayılan ve en yüksek sınırlar için bkz. [eşzamanlılık, döngü ve toplu işleme sınırları](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

> [!TIP]
> Bir diziyi alan ve her dizi öğesi için bir iş akışı çalıştırmak istediğiniz bir Tetikleyiciniz varsa, bu diziyi [ **spton** tetikleyici özelliğiyle](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) *toplu* olarak silebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure hesabı ve aboneliği Aboneliğiniz yoksa, [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/). 

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>"Foreach" döngüsü

Bir "foreach" döngüsü, her dizi öğesinde bir veya daha fazla eylemi yineler ve yalnızca diziler üzerinde kullanılabilir. "Foreach" döngüleri kullandığınızda bazı konular aşağıda verilmiştir:

* "Foreach" döngüsü, sınırlı sayıda dizi öğesini işleyebilir. Bu sınır için bkz. [eşzamanlılık, döngü ve toplu işleme sınırları](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

* Varsayılan olarak, bir "foreach" döngüsünde yinelemeler aynı anda veya paralel olarak çalışır. Bu davranış, yineleme her seferinde bir kez çalıştırılan veya ardışık olarak çalışan [ **her** döngüde Power otomatikleştirmenin](/power-automate/apply-to-each) bir farklılık gösterir. Ancak [sıralı "foreach" döngüsü yinelemelerini ayarlayabilirsiniz](#sequential-foreach-loop). Örneğin, [gecikme eylemini](../connectors/connectors-native-delay.md)kullanarak bir sonraki yinelemeyi bir "foreach" döngüsünde duraklatmak istiyorsanız, döngüyü sırayla çalışacak şekilde ayarlamanız gerekir.

  Varsayılan davranışın özel durumu, tekrarlarının her zaman ardışık olarak değil, her zaman sırayla çalıştırıldığı iç içe döngülerdir. İç içe geçmiş bir döngüdeki öğeler için işlemleri paralel olarak çalıştırmak için [bir alt mantıksal uygulama](../logic-apps/logic-apps-http-endpoint.md)oluşturun ve çağırın.

* Her döngü yinelemesi sırasında değişkenlerdeki işlemlerden öngörülebilir sonuçlar almak için, bu döngüleri sırayla çalıştırın. Örneğin, eşzamanlı olarak çalışan bir döngü sona erdiğinde, artırma, azaltma ve değişken işlemlerine ekleme işlemi öngörülebilir sonuçlar döndürür. Ancak, eşzamanlı çalışan döngüde her yineleme sırasında, bu işlemler öngörülemeyen sonuçlar döndürebilir. 

* "Foreach" döngüsünde eylemler [`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
dizideki her öğeye başvurmak ve işlemek için ifade. Bir dizide olmayan verileri belirtirseniz, mantıksal uygulama iş akışı başarısız olur. 

Bu örnek mantıksal uygulama, Web sitesi RSS akışı için günlük bir Özet gönderir. Uygulama, her yeni öğe için bir e-posta gönderen "foreach" döngüsünü kullanır.

1. [Bu örnek mantıksal uygulamayı](../logic-apps/quickstart-create-first-logic-app-workflow.md) bir Outlook.com hesabı veya iş veya okul hesabıyla oluşturun.

2. RSS tetikleyicisi ve e-posta gönder eylemi arasında bir "foreach" döngüsü ekleyin. 

   1. Adımlar arasında bir döngü eklemek için, işaretçinizi Bu adımlar arasındaki oka taşıyın. 
   Görüntülenen **artı işaretini** () seçin ve **+** ardından **Eylem Ekle**' yi seçin.

      !["Eylem Ekle" yi seçin](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. Arama kutusunda **Tümü**' ni seçin. Arama kutusuna filtreniz olarak "for each" yazın. Eylemler listesinden şu eylemi seçin: **her denetim için**

      !["For each" döngüsünü Ekle](media/logic-apps-control-flow-loops/select-for-each.png)

3. Şimdi döngüyü derleyin. **Dinamik Içerik Ekle** listesinden **önceki adımlardan bir çıktı Seç** ' in altında, RSS tetikleyicisinden çıktı olan **akış bağlantıları** dizisini seçin. 

   ![Dinamik içerik listesinden Seç](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > *Yalnızca* önceki adımdan alınan dizi çıkışlarını seçebilirsiniz.

   Seçilen dizi artık burada görünür:

   ![Dizi Seç](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Her dizi öğesinde bir eylem çalıştırmak için **e-posta gönder** eylemini döngüye sürükleyin. 

   Mantıksal uygulamanız Şu örneğe benzer bir şey görünebilir:

   !["Foreach" döngüsüne adımlar ekleyin](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Mantıksal uygulamanızı kaydedin. Mantıksal uygulamanızı el ile test etmek için tasarımcı araç çubuğunda **Çalıştır**' ı seçin.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>"Foreach" döngü tanımı (JSON)

Mantıksal uygulamanız için kod görünümünde çalışıyorsanız, `Foreach` döngüsünü mantıksal UYGULAMANıZıN JSON tanımında tanımlayabilirsiniz, örneğin:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": {
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "@{item()}",
                  "Subject": "New CNN post @{triggerBody()?['publishDate']}",
                  "To": "me@contoso.com"
               },
               "host": {
                  "api": {
                     "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/office365"
                  },
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {}
   }
}
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>"Foreach" döngüsü: sıralı

Varsayılan olarak, "foreach" döngüsündeki döngüler paralel olarak çalışır. Her döngüyü sırayla çalıştırmak için döngünün **sıralı** seçeneğini ayarlayın. "Foreach" döngüleri, tahmin edilebilir sonuçları bekleyen döngüler içinde iç içe döngüler veya değişkenler olduğunda ardışık olarak çalışmalıdır. 

1. Döngünün sağ üst köşesindeki **üç nokta** (**...**) > **ayarları**' nı seçin.

   !["Foreach" döngüsünde "..." > "Ayarlar" ı seçin](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. **Eşzamanlılık denetimi** altında **eşzamanlılık denetimi** ayarını **Açık** olarak açın. **Paralellik** sürgüsünün derecesini **1**' e taşıyın ve **bitti**' yi seçin.

   ![Eşzamanlılık denetimini Aç](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Mantıksal uygulamanızın JSON tanımıyla çalışıyorsanız, `Sequential` parametresini ekleyerek bu seçeneği kullanabilirsiniz `operationOptions` , örneğin:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": { }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {},
      "operationOptions": "Sequential"
   }
}
```

<a name="until-loop"></a>

## <a name="until-loop"></a>"Until" döngüsü
  
Bir koşul karşılanıncaya veya bir durum değişene kadar eylemleri çalıştırmak ve yinelemek için, bu eylemleri bir "Until" döngüsüne koyun. Mantıksal uygulamanız ilk olarak döngü içindeki tüm eylemleri çalıştırır ve ardından koşulu veya durumu denetler. Koşul karşılanıyorsa, döngü duraklar. Aksi takdirde döngü yinelenir. Mantıksal uygulama çalıştırabileceğine yönelik "Until" döngülerinin sahip olduğu varsayılan ve en yüksek sınırlar için bkz. [eşzamanlılık, döngü ve toplu işleme sınırları](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

"Until" döngüsünü kullanabileceğiniz bazı yaygın senaryolar aşağıda verilmiştir:

* İstediğiniz yanıtı yapana kadar bir uç nokta çağırın.

* Veritabanında bir kayıt oluşturun. Bu kayıttaki belirli bir alan onaylanana kadar bekleyin. İşleme devam edin. 

Her gün 8:00 ' den itibaren bu örnek mantıksal uygulama, değişkenin değeri 10 ' a eşit olana kadar bir değişkeni arttırır. Mantıksal uygulama daha sonra geçerli değeri doğrulayan bir e-posta gönderir. 

> [!NOTE]
> Bu adımlar Office 365 Outlook kullanır, ancak Logic Apps desteklediği herhangi bir e-posta sağlayıcısını kullanabilirsiniz. 
> [Burada bağlayıcılar listesini kontrol edin](/connectors/). Başka bir e-posta hesabı kullanırsanız, genel adımlar aynı kalır, ancak kullanıcı arabirimi biraz farklı görünebilir. 

1. Boş bir mantıksal uygulama oluşturma. Mantıksal uygulama Tasarımcısı ' nda, arama kutusunda **Tümü**' ni seçin. "Yinelenme" araması yapın. 
   Tetikleyiciler listesinden şu tetikleyiciyi seçin: **yineleme-zamanlama**

   !["Yinelenme-zamanlama" tetikleyicisi Ekle](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. Günün Aralık, sıklık ve saatini ayarlayarak tetikleyicinin ne zaman harekete geçtiğini belirtin. Saati ayarlamak için **Gelişmiş seçenekleri göster**' i seçin.

   ![Yinelenme zamanlamasını ayarlama](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Özellik | Değer |
   | -------- | ----- |
   | **Aralık** | 1 | 
   | **Sıklık** | Gün |
   | **Şu saatlerde** | 8 |
   ||| 

1. Tetikleyici altında **yeni adım**' ı seçin. 
   "Değişkenler" araması yapın ve şu eylemi seçin: **değişken değişkenlerini Başlat**

   !["Değişken başlatma-değişkenlerini" eylemi Ekle](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. Değişkeninizi şu değerlerle ayarlayın:

   ![Değişken özelliklerini ayarla](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Özellik | Değer | Açıklama |
   | -------- | ----- | ----------- |
   | **Ad** | Sınır | Değişkeninin adı | 
   | **Tür** | Tamsayı | Değişkeninizin veri türü | 
   | **Değer** | 0 | Değişkeninizin başlangıç değeri | 
   |||| 

1. **Değişken başlatma** eyleminin altında **yeni adım**' ı seçin. 

1. Arama kutusunda **Tümü**' ni seçin. "Until" araması yapın ve şu eylemi seçin: **until-Control**

   !["Until" döngüsünü Ekle](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. **Limit** değişkenini ve **eşittir** işlecini seçerek döngünün çıkış koşulunu oluşturun. 
   Karşılaştırma değeri olarak **10** girin.

   ![Durdurma döngüsü için derleme çıkış koşulu](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. Döngü içinde **Eylem Ekle**' yi seçin. 

1. Arama kutusunda **Tümü**' ni seçin. "Değişkenler" araması yapın ve şu eylemi seçin: **artırma değişkeni-değişkenler**

   ![Değişken artırma için eylem ekleme](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. **Ad** için **sınır** değişkenini seçin. **Değer** için "1" girin. 

     ![1 ile "sınırı" Artır](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. Döngünün dışında ve altında **yeni adım**' ı seçin. 

1. Arama kutusunda **Tümü**' ni seçin. 
     E-posta gönderen bir eylem bulun ve ekleyin, örneğin: 

     ![E-posta gönderen eylem ekleme](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. İstenirse, e-posta hesabınızda oturum açın.

1. E-posta eyleminin özelliklerini ayarlayın. **Sınır** değişkenini konuya ekleyin. Bu şekilde, değişkenin geçerli değerinin belirtilen koşullarınızı karşıladığından emin olabilirsiniz, örneğin:

      ![E-posta özelliklerini ayarlama](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | Özellik | Değer | Açıklama |
      | -------- | ----- | ----------- | 
      | **Kime** | *\<email-address\@domain>* | Alıcının e-posta adresi. Test etmek için kendi e-posta adresinizi kullanın. | 
      | **Konu** | "Limit" için geçerli değer **limit** | E-posta konusunu belirtin. Bu örnekte, **sınır** değişkenini eklediğinizden emin olun. | 
      | **Gövde** | <*e-posta-içerik*> | Göndermek istediğiniz e-posta iletisi içeriğini belirtin. Bu örnek için dilediğiniz metni girin. | 
      |||| 

1. Mantıksal uygulamanızı kaydedin. Mantıksal uygulamanızı el ile test etmek için tasarımcı araç çubuğunda **Çalıştır**' ı seçin.

      Mantığınızı çalıştırmaya başladıktan sonra belirttiğiniz içeriğe sahip bir e-posta alırsınız:

      ![Alınan e-posta](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

<a name="prevent-endless-loops"></a>

## <a name="prevent-endless-loops"></a>Sonsuz döngüleri engelle

"Until" döngüsü, bu özelliklere göre yürütmeyi durdurduğundan, değerlerini uygun şekilde ayarladığınızdan emin olun:

* **Sayı**: Bu değer, döngüden geçmeden önce çalıştırılan en yüksek döngü sayısıdır. Mantıksal uygulama çalıştırabileceğine yönelik "Until" döngülerinin sahip olduğu varsayılan ve en yüksek sınırlar için bkz. [eşzamanlılık, döngü ve toplu işleme sınırları](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

* **Zaman aşımı**: Bu değer, çıkmadan önce döngünün çalıştığı ve [ISO 8601 biçiminde](https://en.wikipedia.org/wiki/ISO_8601)belirtilen en büyük süredir. **Zaman aşımı** değeri için varsayılan ve en büyük sınırlar için bkz. [eşzamanlılık, döngü ve toplu işleme sınırları](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

  Zaman aşımı değeri her döngü döngüsü için değerlendirilir. Döngüdeki herhangi bir eylem zaman aşımı sınırından daha uzun sürerse, geçerli döngü durdurulmaz. Ancak, bir sonraki döngüde, sınır koşulu karşılanmadığı için başlamaz.

Bu sınırları değiştirmek için, döngü eyleminde **sınırları Değiştir**' i seçin.

<a name="until-json"></a>

## <a name="until-definition-json"></a>"Until" tanımı (JSON)

Mantıksal uygulamanız için kod görünümünde çalışıyorsanız, `Until` bunun yerine mantıksal UYGULAMANıZıN JSON tanımında bir döngü tanımlayabilirsiniz. Örneğin:

``` json
"actions": {
   "Initialize_variable": {
      // Definition for initialize variable action
   },
   "Send_an_email": {
      // Definition for send email action
   },
   "Until": {
      "type": "Until",
      "actions": {
         "Increment_variable": {
            "type": "IncrementVariable",
            "inputs": {
               "name": "Limit",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "expression": "@equals(variables('Limit'), 10)",
      // To prevent endless loops, an "Until" loop 
      // includes these default limits that stop the loop. 
      "limit": { 
         "count": 60,
         "timeout": "PT1H"
      },
      "runAfter": {
         "Initialize_variable": [
            "Succeeded"
         ]
      }
   }
}
```

Bu örnek "Until" döngüsü bir kaynak oluşturan bir HTTP uç noktası çağırır. HTTP yanıt gövdesi durum ile döndüğünde döngü durduruluyor `Completed` . Sonsuz döngüleri engellemek için, bu koşullardan herhangi biri gerçekleşiyorsa döngü de duraklar:

* Döngü, öznitelik tarafından belirtilen 10 kez çalıştırıldı `count` . Varsayılan değer 60 zamandır. 

* Döngü, `timeout` ıso 8601 biçimindeki özniteliği tarafından belirtilen iki saat boyunca çalışır. Varsayılan değer bir saattir.
  
``` json
"actions": {
   "myUntilLoopName": {
      "type": "Until",
      "actions": {
         "Create_new_resource": {
            "type": "Http",
            "inputs": {
               "body": {
                  "resourceId": "@triggerBody()"
               },
               "url": "https://domain.com/provisionResource/create-resource",
               "body": {
                  "resourceId": "@triggerBody()"
               }
            },
            "runAfter": {},
            "type": "ApiConnection"
         }
      },
      "expression": "@equals(triggerBody(), 'Completed')",
      "limit": {
         "count": 10,
         "timeout": "PT2H"
      },
      "runAfter": {}
   }
}
```

## <a name="get-support"></a>Destek alın

* Sorular için, [Azure Logic Apps Için Microsoft Q&soru sayfasını](/answers/topics/azure-logic-apps.html)ziyaret edin.
* Özellikleri ve önerileri göndermek veya Oylamak için [Kullanıcı geri bildirim sitesini Azure Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Sonraki adımlar

* [Bir koşula göre adımları çalıştırın (koşullu deyimler)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Farklı değerlere göre adımları Çalıştır (Switch deyimleri)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Paralel adımları çalıştırma veya birleştirme (dallar)](../logic-apps/logic-apps-control-flow-branches.md)
* [Gruplanmış eylem durumu (kapsamlar) temelinde adımları Çalıştır](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
