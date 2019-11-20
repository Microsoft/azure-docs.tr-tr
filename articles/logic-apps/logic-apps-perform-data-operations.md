---
title: Veriler üzerinde işlem gerçekleştirme-Azure Logic Apps
description: Azure Logic Apps veri çıktılarını ve biçimlerini dönüştürme, yönetme ve değiştirme
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: a21b7f510b6da40d3ab2c72fcfbcb2a746b75db1
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990547"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Azure Logic Apps veri işlemlerini gerçekleştirme

Bu makalede, bu görevlere yönelik eylemler ekleyerek ve daha fazlasını gerçekleştirerek mantıksal uygulamalarınızdaki verilerle nasıl çalışabilmeniz gösterilmektedir:

* Dizilerden tablo oluşturun.
* Bir koşula bağlı olarak diğer dizilerden diziler oluşturun.
* JavaScript Nesne Gösterimi (JSON) nesne özelliklerinden Kullanıcı dostu belirteçler oluşturun, böylece bu özellikleri iş akışınızda kolayca kullanabilirsiniz.

Burada istediğiniz eylemi bulamazsanız Azure Logic Apps sağladığı çeşitli [veri işleme işlevlerine](../logic-apps/workflow-definition-language-functions-reference.md) göz atmayı deneyin.

Bu tablolar, kullanabileceğiniz veri işlemlerini özetler ve işlemlerin üzerinde çalıştığı kaynak veri türlerine göre düzenlenmiştir, ancak her açıklama alfabetik olarak görünür.

**Dizi eylemleri** 

Bu eylemler, dizilerde verilerle çalışmanıza yardımcı olur.

| Eylem | Açıklama |
|--------|-------------|
| [**CSV tablosu oluştur**](#create-csv-table-action) | Bir diziden bir virgülle ayrılmış değer (CSV) tablosu oluşturun. |
| [**HTML tablosu oluştur**](#create-html-table-action) | Diziden bir HTML tablosu oluşturun. |
| [**Diziyi filtrele**](#filter-array-action) | Belirtilen filtre veya koşula göre diziden bir dizi alt kümesi oluşturun. |
| [**Ayrılma**](#join-action) | Bir dizideki tüm öğelerden bir dize oluşturun ve her öğeyi belirtilen karakterle ayırın. |
| [**Seçin**](#select-action) | Farklı bir dizideki tüm öğeler için belirtilen özelliklerden bir dizi oluşturun. |
||| 

**JSON eylemleri**

Bu eylemler JavaScript Nesne Gösterimi (JSON) biçimindeki verilerle çalışmanıza yardımcı olur.

| Eylem | Açıklama |
|--------|-------------|
| [**İletinizi**](#compose-action) | Çeşitli veri türlerine sahip olan birden fazla girişe bir ileti veya dize oluşturun. Daha sonra bu dizeyi, aynı girdileri tekrar tekrar girmek yerine tek bir girdi olarak kullanabilirsiniz. Örneğin, çeşitli girdilerden tek bir JSON iletisi oluşturabilirsiniz. |
| [**JSON Ayrıştır**](#parse-json-action) | Mantıksal uygulamalarınızda özellikleri daha kolay kullanabilmek için JSON içeriğindeki özellikler için Kullanıcı dostu veri belirteçleri oluşturun. |
|||

Daha karmaşık JSON dönüştürmeleri oluşturmak için bkz. [sıvı şablonları ile GELIŞMIŞ JSON dönüştürmeleri gerçekleştirme](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md).

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Aboneliğiniz yoksa, [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* Verilerle çalışmaya yönelik işlemin gerekli olduğu mantıksal uygulama

  Logic Apps 'e yeni başladıysanız, [Azure Logic Apps nedir?](../logic-apps/logic-apps-overview.md) ve [hızlı başlangıç: Ilk mantıksal uygulamanızı oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md)' yı gözden geçirin.

* Mantıksal uygulamanızda ilk adım olarak bir [tetikleyici](../logic-apps/logic-apps-overview.md#logic-app-concepts) 

  Veri işlemleri yalnızca eylem olarak kullanılabilir; Bu nedenle, bu eylemleri kullanabilmeniz için mantıksal uygulamanızı bir tetikleyici ile başlatın ve istediğiniz çıktıları oluşturmak için gereken diğer eylemleri dahil edin.

<a name="compose-action"></a>

## <a name="compose-action"></a>Oluşturma eylemi

Birden çok giriş nesnesinden JSON nesnesi gibi tek bir çıktı oluşturmak için, **oluşturma** eylemini kullanabilirsiniz. Girdileriniz tamsayılar, Boole değerleri, diziler, JSON nesneleri ve Azure Logic Apps desteklediği diğer yerel türler (örneğin, ikili ve XML) gibi çeşitli türlerde olabilir. Daha sonra, **oluşturma** eyleminden sonra izleyen eylemlerde çıktıyı kullanabilirsiniz. **Oluşturma** eylemi, mantıksal uygulamanızın iş akışını oluştururken aynı girdileri tekrar tekrar girerek da sizi kaydedebilir.

Örneğin, kişilerin ilk adlarını ve soyadlarını depolayan dize değişkenleri ve kişilerin yaşlarını depolayan bir tamsayı değişkeni gibi birden çok değişkenden bir JSON iletisi oluşturabilirsiniz. Burada, **oluşturma** eylemi şu girişleri kabul eder:

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

ve bu çıktıyı oluşturur:

`{"age":35,"fullName":"Owens,Sophie"}`

Bir örnek denemek için, mantıksal uygulama Tasarımcısı ' nı kullanarak bu adımları izleyin. Ya da kod görünümü düzenleyicisinde çalışmayı tercih ediyorsanız, bu makaledeki örnek **oluşturma** ve **değişken** eylem tanımlarını, kendi mantıksal uygulamanızın temel alınan iş akışı tanımına kopyalayabilir ve kullanabilirsiniz: [veri işlemi kodu örnekleri- Oluştur](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. [Azure Portal](https://portal.azure.com) veya Visual Studio 'Da mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

   Bu örnek Azure portal ve bir mantıksal uygulamayı **yineleme** tetikleyicisi ve birkaç **değişken başlatma** eylemi ile kullanır. Bu eylemler iki dize değişkeni ve bir tamsayı değişkeni oluşturmak için ayarlanır. Mantıksal uygulamanızı daha sonra test ettiğinizde, tetikleyicinin tetiklenmesi gerekmeden uygulamanızı el ile çalıştırabilirsiniz.

   !["Oluşturma" eylemi için örnek mantıksal uygulama başlatılıyor](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

1. Çıktıyı oluşturmak istediğiniz mantıksal uygulamanızda şu adımlardan birini izleyin: 

   * Son adım altında bir eylem eklemek için **yeni adım**' ı seçin.

     !["Oluştur" eylemi için "yeni adım" seçeneğini belirleyin](./media/logic-apps-perform-data-operations/add-compose-operation-action.png)

   * Adımlar arasında bir eylem eklemek için farenizi bağlantı oku üzerine taşıyarak artı işareti ( **+** ) belirir. Artı işaretini seçin ve ardından **Eylem Ekle**' yi seçin.

1. **Eylem seçin**altında, arama kutusuna filtreniz olarak `compose` girin. Eylemler listesinden, **oluşturma** eylemini seçin.

   !["Oluştur" eylemini seçin](./media/logic-apps-perform-data-operations/select-compose-action.png)

1. **Girişler** kutusunda, çıktıyı oluşturmak için istediğiniz girişleri sağlayın.

   Bu örnekte, **girişler** kutusunun içine tıkladığınızda, daha önce oluşturulan değişkenleri seçebilmeniz için dinamik içerik listesi görüntülenir:

   !["Oluştur" eylemi için kullanılacak girdileri seçin](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   İşte tamamlanan örnek **oluşturma** eylemi: 

   !["Oluşturma" eylemi için tamamlanmış örnek](./media/logic-apps-perform-data-operations/finished-compose-action.png)

1. Mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

Temel alınan iş akışı tanımınızda bu eylem hakkında daha fazla bilgi için, [oluşturma eylemine](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action)bakın.

### <a name="test-your-logic-app"></a>Mantıksal uygulamanızı test etme

**Oluşturma** eyleminin beklenen sonuçları oluşturup oluşturmadığını doğrulamak Için, **oluşturma** eyleminden çıkış içeren bir bildirim gönderin.

1. Mantıksal uygulamanızda, **oluşturma** eyleminden sonuçları gönderebilen bir eylem ekleyin.

1. Bu eylemde, sonuçların görünmesini istediğiniz herhangi bir yere tıklayın. Dinamik içerik listesi açıldığında, **oluşturma** eylemi altında **Çıkış**' ı seçin.

   Bu örnek **e-posta gönder** eylemini kullanır ve e-postanın gövdesinde ve konusunun **Çıkış** alanlarını içerir:

   !["Oluşturma" eylemi için "çıkış" alanları](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

1. Şimdi mantıksal uygulamanızı el ile çalıştırın. Tasarımcı araç çubuğunda **Çalıştır**' ı seçin.

   Kullandığınız e-posta bağlayıcısını temel alarak Alacağınız sonuçlar şunlardır:

   !["Compose" eylem sonuçlarıyla e-posta](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>CSV tablosu oluşturma eylemi

Bir dizideki JavaScript Nesne Gösterimi (JSON) nesnelerinden Özellikler ve değerler içeren bir virgülle ayrılmış değer (CSV) tablosu oluşturmak için **CSV tablosu oluşturma** eylemini kullanın. Daha sonra, sonuç tablosunu **CSV tablosu oluşturma** eylemini izleyen eylemlerde kullanabilirsiniz.

Kod görünümü Düzenleyicisi 'nde çalışmayı tercih ediyorsanız, örnek **CSV tablosu oluştur** ' u kopyalayabilir ve bu makaleden kendi mantıksal uygulamanızın temel alınan iş akışı tanımına değişken eylem tanımlarını **başlatabilirsiniz** : [veri işlemi kodu örnekleri- CSV tablosu oluştur](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example)

1. [Azure Portal](https://portal.azure.com) veya Visual Studio 'Da mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

   Bu örnek Azure portal ve bir mantıksal uygulamayı **yineleme** tetikleyicisi ve bir **değişken Başlat** eylemiyle kullanır. Eylem, ilk değeri JSON biçiminde bazı özellikler ve değerler içeren bir dizi olan bir değişken oluşturmak için ayarlanır. Mantıksal uygulamanızı daha sonra test ettiğinizde, tetikleyicinin tetiklenmesi gerekmeden uygulamanızı el ile çalıştırabilirsiniz.

   !["CSV tablosu oluşturma" eylemi için örnek mantıksal uygulama başlatılıyor](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. CSV tablosunu oluşturmak istediğiniz mantıksal uygulamanızda şu adımlardan birini izleyin: 

   * Son adım altında bir eylem eklemek için **yeni adım**' ı seçin.

     !["CSV tablosu oluşturma" eylemi için "yeni adım" seçeneğini belirleyin](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Adımlar arasında bir eylem eklemek için farenizi bağlantı oku üzerine taşıyarak artı işareti ( **+** ) belirir. Artı işaretini seçin ve ardından **Eylem Ekle**' yi seçin.

1. **Eylem seçin**altında, arama kutusuna filtreniz olarak `create csv table` girin. Eylemler listesinden **CSV tablosu oluştur** eylemini seçin.

   !["CSV tablosu oluştur" eylemini seçin](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

1. **Kimden** kutusunda, tablo oluşturmak için istediğiniz diziyi veya ifadeyi belirtin.

   Bu örnekte, **Kimden** kutusunun içine tıkladığınızda, daha önce oluşturulan değişkeni seçebilmeniz için dinamik içerik listesi görüntülenir:

   ![CSV tablosu oluşturmak için dizi çıkışı seçin](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   > [!TIP]
   > JSON nesnelerinde özellikler için Kullanıcı dostu belirteçler oluşturmak üzere bu özellikleri giriş olarak seçebilmeniz için, **CSV tablosu oluştur** eylemini ÇAĞıRMADAN önce [JSON 'u Ayrıştır](#parse-json-action) ' ı kullanın.

   İşte tamamlanan örnek **CSV tablosu oluşturma** eylemi: 

   !["CSV tablosu oluşturma" eylemi için tamamlanmış örnek](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

1. Mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

### <a name="customize-table-format"></a>Tablo biçimini Özelleştir

Varsayılan olarak, **Columns** özelliği, dizi öğelerine göre tablo sütunlarını otomatik olarak oluşturmak üzere ayarlanır. Özel üst bilgileri ve değerleri belirtmek için şu adımları izleyin:

1. **Sütunlar** listesini açın ve **özel**' i seçin.

1. **Üstbilgi** özelliğinde, yerine kullanılacak özel üst bilgi metnini belirtin.

1. **Değer** özelliğinde, bunun yerine kullanılacak özel değeri belirtin.

Diziden değer döndürmek için, [`item()` IŞLEVINI](../logic-apps/workflow-definition-language-functions-reference.md#item) **CSV tablosu oluşturma** eylemiyle birlikte kullanabilirsiniz. `For_each` döngüsünde, [`items()` işlevi](../logic-apps/workflow-definition-language-functions-reference.md#items)kullanabilirsiniz.

Örneğin, bir dizideki özellik adlarını değil, yalnızca özellik değerlerine sahip tablo sütunlarının istediğinizi varsayalım. Yalnızca bu değerleri döndürmek için, tasarımcı görünümünde veya kod görünümünde çalışmak üzere bu adımları izleyin. Bu örnekte döndürülen sonuç aşağıda verilmiştir:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Tasarımcı görünümünde çalışma

Eylemde **başlık** sütununu boş tutun. **Değer** sütunundaki her satırda, istediğiniz her bir dizi özelliğine başvuru yapın. **Değer** altındaki her satır, belirtilen dizi özelliği için tüm değerleri döndürür ve tablonuzda bir sütun olur.

1. **Değer**' in altında, istediğiniz her satırda, dinamik içerik listesinin görünmesi için düzenleme kutusunun içine tıklayın.

1. Dinamik içerik listesinde **ifade**' yi seçin.

1. İfade düzenleyicisinde, istediğiniz dizi özelliği değerini belirten bu ifadeyi girin ve **Tamam**' ı seçin.

   `item()?['<array-property-name>']`

   Örnek:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   !["CSV tablosu oluşturma" için "Description" başvurusu](./media/logic-apps-perform-data-operations/csv-table-expression.png)

1. İstediğiniz her dizi özelliği için önceki adımları tekrarlayın. İşiniz bittiğinde, eyleminiz Şu örneğe benzer şekilde görünür:

   !["CSV tablosu oluşturma" içindeki "item ()" işlevi](./media/logic-apps-perform-data-operations/finished-csv-expression.png)

1. İfadeleri daha açıklayıcı sürümlere dönüştürmek için, kod görünümüne geçin ve tasarımcı görünümüne dönün ve sonra daraltılan eylemi yeniden açın:

   **CSV tablosu oluştur** eylemi şu örnekte olduğu gibi görünür:

   !["CSV tablosu oluşturma"-çözümlenen ifadeler, üst bilgi yok](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

#### <a name="work-in-code-view"></a>Kod görünümünde çalışma

Eylemin JSON tanımında, `columns` dizisinin içinde, `header` özelliğini boş bir dize olarak ayarlayın. Her bir `value` özelliği için istediğiniz her bir dizi özelliğine başvuru yapın.

1. Tasarımcı araç çubuğunda **kod görünümü**' nü seçin.

1. Kod düzenleyicisinde, eylemin `columns` dizisindeki boş `header` özelliğini ve istediğiniz dizi değerlerinin her sütunu için bu `value` ifadesini ekleyin:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Örnek:

   ```json
   "Create_CSV_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "CSV",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Tasarımcı görünümüne dönün ve daraltılmış eylemi yeniden açın.

   **CSV tablosu oluştur** eylemi şimdi bu örnekte olduğu gibi görünür ve ifadeler daha açıklayıcı sürümlere çözümlenmektedir:

   !["CSV tablosu oluşturma"-çözümlenmiş ifadeler ve üst bilgi yok](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

Temel alınan iş akışı tanımınızda bu eylem hakkında daha fazla bilgi için, bkz. [tablo eylemi](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Mantıksal uygulamanızı test etme

**CSV tablosu oluşturma** eyleminin beklenen sonuçları oluşturup oluşturmadığını doğrulamak IÇIN, **CSV tablosu oluşturma** eyleminden çıkış içeren bir bildirim gönderin.

1. Mantıksal uygulamanızda, **CSV tablosu oluşturma** eyleminden sonuçları gönderebilen bir eylem ekleyin.

1. Bu eylemde, sonuçların görünmesini istediğiniz herhangi bir yere tıklayın. Dinamik içerik listesi açıldığında, **CSV tablosu oluştur** eyleminin altında **Çıkış**' ı seçin. 

   Bu örnek, Office 365 Outlook **e-posta gönder** eylemini kullanır ve e-postanın gövdesinde **Çıkış** alanını içerir:

   !["CSV tablosu oluşturma" eylemi için "çıkış" alanları](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

1. Şimdi mantıksal uygulamanızı el ile çalıştırın. Tasarımcı araç çubuğunda **Çalıştır**' ı seçin.

   Kullandığınız e-posta bağlayıcısını temel alarak Alacağınız sonuçlar şunlardır:

   !["CSV tablosu oluşturma" eylem sonuçlarıyla e-posta](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>HTML tablosu eylemi oluştur

Bir dizideki JavaScript Nesne Gösterimi (JSON) nesnelerinden Özellikler ve değerler içeren bir HTML tablosu oluşturmak için **HTML tablosu oluştur** eylemini kullanın. Daha sonra, sonuç tablosunu **HTML tablosu oluştur** eylemini izleyen eylemlerde kullanabilirsiniz.

Kod görünümü Düzenleyicisi 'nde çalışmayı tercih ediyorsanız, örnek **HTML tablosu oluştur** ' u kopyalayabilir ve bu makaleden kendi mantıksal uygulamanızın temel alınan iş akışı tanımına değişken eylem tanımlarını **başlatabilirsiniz** : [veri işlemi kodu örnekleri- HTML tablosu oluştur](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. [Azure Portal](https://portal.azure.com) veya Visual Studio 'Da mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

   Bu örnek Azure portal ve bir mantıksal uygulamayı **yineleme** tetikleyicisi ve bir **değişken Başlat** eylemiyle kullanır. Eylem, ilk değeri JSON biçiminde bazı özellikler ve değerler içeren bir dizi olan bir değişken oluşturmak için ayarlanır. Mantıksal uygulamanızı daha sonra test ettiğinizde, tetikleyicinin tetiklenmesi gerekmeden uygulamanızı el ile çalıştırabilirsiniz.

   !["HTML tablosu oluşturma" için örnek mantıksal uygulama başlatılıyor](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. Bir HTML tablosu oluşturmak istediğiniz mantıksal uygulamanızda şu adımlardan birini izleyin:

   * Son adım altında bir eylem eklemek için **yeni adım**' ı seçin.

     !["HTML tablosu oluşturma" eylemi için "yeni adım" seçeneğini belirleyin](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Adımlar arasında bir eylem eklemek için farenizi bağlantı oku üzerine taşıyarak artı işareti ( **+** ) belirir. Artı işaretini seçin ve ardından **Eylem Ekle**' yi seçin.

1. **Eylem seçin**altında, arama kutusuna filtreniz olarak `create html table` girin. Eylemler listesinden **HTML tablosu oluştur** eylemini seçin.

   !["HTML tablosu oluştur" eylemini seçin](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

1. **Kimden** kutusunda, tablo oluşturmak için istediğiniz diziyi veya ifadeyi belirtin.

   Bu örnekte, **Kimden** kutusunun içine tıkladığınızda, daha önce oluşturulan değişkeni seçebilmeniz için dinamik içerik listesi görüntülenir:

   ![HTML tablosu oluşturmak için dizi çıkışı seçin](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   > [!TIP]
   > JSON nesnelerinde özellikler için Kullanıcı dostu belirteçler oluşturmak üzere bu özellikleri giriş olarak seçebilmeniz için, **HTML tablosu oluştur** eylemini ÇAĞıRMADAN önce [JSON 'u Ayrıştır](#parse-json-action) ' ı kullanın.

   Aşağıda, tamamlanmış örnek **HTML tablosu oluşturma** eylemi verilmiştir:

   !["HTML tablosu oluşturma" için tamamlanmış örnek](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

1. Mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

### <a name="customize-table-format"></a>Tablo biçimini Özelleştir

Varsayılan olarak, **Columns** özelliği, dizi öğelerine göre tablo sütunlarını otomatik olarak oluşturmak üzere ayarlanır. Özel üst bilgileri ve değerleri belirtmek için şu adımları izleyin:

1. **Sütunlar** listesini açın ve **özel**' i seçin.

1. **Üstbilgi** özelliğinde, yerine kullanılacak özel üst bilgi metnini belirtin.

1. **Değer** özelliğinde, bunun yerine kullanılacak özel değeri belirtin.

Diziden değer döndürmek için, [`item()` IŞLEVINI](../logic-apps/workflow-definition-language-functions-reference.md#item) **HTML tablosu oluştur** eylemiyle birlikte kullanabilirsiniz. `For_each` döngüsünde, [`items()` işlevi](../logic-apps/workflow-definition-language-functions-reference.md#items)kullanabilirsiniz.

Örneğin, bir dizideki özellik adlarını değil, yalnızca özellik değerlerine sahip tablo sütunlarının istediğinizi varsayalım. Yalnızca bu değerleri döndürmek için, tasarımcı görünümünde veya kod görünümünde çalışmak üzere bu adımları izleyin. Bu örnekte döndürülen sonuç aşağıda verilmiştir:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Tasarımcı görünümünde çalışma

Eylemde **başlık** sütununu boş tutun. **Değer** sütunundaki her satırda, istediğiniz her bir dizi özelliğine başvuru yapın. **Değer** altındaki her satır, belirtilen özelliğin tüm değerlerini döndürür ve tablonuzda bir sütun olur.

1. **Değer**' in altında, istediğiniz her satırda, dinamik içerik listesinin görünmesi için düzenleme kutusunun içine tıklayın.

1. Dinamik içerik listesinde **ifade**' yi seçin.

1. İfade düzenleyicisinde, istediğiniz dizi özelliği değerini belirten bu ifadeyi girin ve **Tamam**' ı seçin.

   `item()?['<array-property-name>']`

   Örnek:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   !["HTML tablosu oluşturma" eyleminde başvuru özelliği](./media/logic-apps-perform-data-operations/html-table-expression.png)

1. İstediğiniz her dizi özelliği için önceki adımları tekrarlayın. İşiniz bittiğinde, eyleminiz Şu örneğe benzer şekilde görünür:

   !["HTML tablosu oluşturma" içindeki "item ()" işlevi](./media/logic-apps-perform-data-operations/finished-html-expression.png)

1. İfadeleri daha açıklayıcı sürümlere dönüştürmek için, kod görünümüne geçin ve tasarımcı görünümüne dönün ve sonra daraltılan eylemi yeniden açın:

   **HTML tablosu oluştur** eylemi şu örnekte olduğu gibi görünür:

   !["HTML tablosu oluşturma"-çözümlenen ifadeler, üst bilgi yok](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

#### <a name="work-in-code-view"></a>Kod görünümünde çalışma

Eylemin JSON tanımında, `columns` dizisinin içinde, `header` özelliğini boş bir dize olarak ayarlayın. Her bir `value` özelliği için istediğiniz her bir dizi özelliğine başvuru yapın.

1. Tasarımcı araç çubuğunda **kod görünümü**' nü seçin.

1. Kod düzenleyicisinde, eylemin `columns` dizisindeki boş `header` özelliğini ve istediğiniz dizi değerlerinin her sütunu için bu `value` ifadesini ekleyin:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Örnek:

   ```json
   "Create_HTML_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "HTML",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Tasarımcı görünümüne dönün ve daraltılmış eylemi yeniden açın.

   **HTML tablosu oluştur** eylemi artık bu örnekte olduğu gibi görünür ve ifadeler daha açıklayıcı sürümlere çözümlenmektedir:

   !["HTML tablosu oluşturma"-çözümlenen ifadeler ve üst bilgi yok](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

Temel alınan iş akışı tanımınızda bu eylem hakkında daha fazla bilgi için, bkz. [tablo eylemi](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action).

### <a name="test-your-logic-app"></a>Mantıksal uygulamanızı test etme

**HTML tablosu oluşturma** eyleminin beklenen sonuçları oluşturup oluşturmadığını doğrulamak IÇIN, **HTML tablosu oluşturma** eyleminden çıkış içeren bir bildirim gönderin.

1. Mantıksal uygulamanızda, **HTML tablosu oluştur** eyleminden sonuçları gönderebilen bir eylem ekleyin.

1. Bu eylemde, sonuçların görünmesini istediğiniz herhangi bir yere tıklayın. Dinamik içerik listesi açıldığında, **HTML tablosu oluştur** eyleminin altında **Çıkış**' ı seçin. 

   Bu örnek, Office 365 Outlook **e-posta gönder** eylemini kullanır ve e-postanın gövdesinde **Çıkış** alanını içerir:

   !["HTML tablosu oluşturma" için "çıkış" alanları](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)

   > [!NOTE]
   > HTML tablosu çıkışını bir e-posta eylemine dahil ettiğinizde, e-posta eyleminin gelişmiş seçeneklerinde **, HTML** özelliğini **Evet** olarak ayarladığınızdan emin olun. Bu şekilde, e-posta eylemi HTML tablosunu doğru şekilde biçimlendirir.

1. Şimdi mantıksal uygulamanızı el ile çalıştırın. Tasarımcı araç çubuğunda **Çalıştır**' ı seçin.

   Kullandığınız e-posta bağlayıcısını temel alarak Alacağınız sonuçlar şunlardır:

   !["HTML tablosu oluşturma" sonuçları ile e-posta](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>Dizi eylemini filtrele

Belirli ölçütlere uyan öğeleri olan daha küçük bir dizi oluşturmak için, var olan bir dizide, **diziyi filtrele** eylemini kullanın. Daha sonra filtrelenmiş diziyi, **filtre dizisi** eyleminden sonra gelen eylemler ' de kullanabilirsiniz.

> [!NOTE]
> Koşulunuz içinde kullandığınız herhangi bir filtre metni büyük/küçük harfe duyarlıdır. Ayrıca, bu eylem dizideki öğelerin biçimini veya bileşenlerini değiştiremez. 
> 
> Dizi çıktısını **filtre dizisi** eyleminde kullanmak için, bu eylemler dizileri girdi olarak kabul etmelidir ya da çıktı dizisini başka bir uyumlu biçime dönüştürmeniz gerekebilir.

Kod görünümü düzenleyicisinde çalışmayı tercih ediyorsanız, örnek **filtre dizisini** kopyalayabilir ve bu makaleden kendi mantıksal uygulamanızın temel alınan iş akışı tanımına değişken eylem tanımlarını **başlatabilirsiniz** : [veri işlemi kodu örnekleri- Diziyi filtrele](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example)

1. [Azure Portal](https://portal.azure.com) veya Visual Studio 'Da mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

   Bu örnek Azure portal ve bir mantıksal uygulamayı **yineleme** tetikleyicisi ve bir **değişken Başlat** eylemiyle kullanır. Eylem, ilk değeri bazı örnek tamsayılar içeren bir dizi olan bir değişken oluşturmak için ayarlanır. Mantıksal uygulamanızı daha sonra test ettiğinizde, tetikleyicinin tetiklenmesi gerekmeden uygulamanızı el ile çalıştırabilirsiniz.

   > [!NOTE]
   > Bu örnekte basit bir tamsayı dizisi kullanılsa da, bu eylem özellikle nesneleri özelliklerine ve değerlerine göre filtreleyebileceğiniz JSON nesne dizileri için yararlıdır.

   !["Diziyi filtrele" eylemi için örnek mantıksal uygulama başlatılıyor](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

1. Filtrelenmiş diziyi oluşturmak istediğiniz mantıksal uygulamanızda şu adımlardan birini izleyin: 

   * Son adım altında bir eylem eklemek için **yeni adım**' ı seçin.

     !["Diziyi filtrele" eylemi için "yeni adım" seçeneğini belirleyin](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * Adımlar arasında bir eylem eklemek için farenizi bağlantı oku üzerine taşıyarak artı işareti ( **+** ) belirir. Artı işaretini seçin ve ardından **Eylem Ekle**' yi seçin.

1. Arama kutusuna filtreniz olarak `filter array` girin. Eylemler listesinden, **diziyi filtrele** eylemini seçin.

   !["Diziyi filtrele" eylemini seçin](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

1. **Kimden** kutusunda, filtrelemek istediğiniz diziyi veya ifadeyi belirtin.

   Bu örnekte, **Kimden** kutusunun içine tıkladığınızda, daha önce oluşturulan değişkeni seçebilmeniz için dinamik içerik listesi görüntülenir:

   ![Filtrelenmiş dizi oluşturmak için dizi çıkışı seçin](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

1. Koşul için, Karşılaştırılacak dizi öğelerini belirtin, karşılaştırma işlecini seçin ve karşılaştırma değerini belirtin.

   Bu örnek dizideki her öğeye erişmek için `item()` işlevini kullanır, çünkü **filtre dizisi** eylemi, değeri birden büyük olan dizi öğelerini arar:

   !["Diziyi filtrele" eylemi için tamamlanmış örnek](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

1. Mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

Temel alınan iş akışı tanımınızda bu eylem hakkında daha fazla bilgi için bkz. [sorgu eylemi](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action).

### <a name="test-your-logic-app"></a>Mantıksal uygulamanızı test etme

**Filtre dizisi** eyleminin beklenen sonuçları oluşturup oluşturmadığını doğrulamak Için kendinize **filtre dizisi** eyleminden çıkış içeren bir bildirim gönderin.

1. Mantıksal uygulamanızda, size **filtre dizisi** eyleminden sonuçları gönderebilen bir eylem ekleyin.

1. Bu eylemde, sonuçların görünmesini istediğiniz herhangi bir yere tıklayın. Dinamik içerik listesi açıldığında, **ifade**' ı seçin. Diziyi **Filtrele** eyleminden dizi çıkışını almak Için, **filtre dizisi** eyleminin adını içeren bu ifadeyi girin:

   `@actionBody('Filter_array')`

   Bu örnek, Office 365 Outlook **e-posta gönder** eylemini kullanır ve e-postanın gövdesinde **Actionbody (' Filter_array ')** ifadesinden çıkışları içerir:

   !["Diziyi filtrele" eyleminden eylem çıkışları](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

1. Şimdi mantıksal uygulamanızı el ile çalıştırın. Tasarımcı araç çubuğunda **Çalıştır**' ı seçin.

   Kullandığınız e-posta bağlayıcısını temel alarak Alacağınız sonuçlar şunlardır:

   !["Diziyi filtrele" eylem sonuçlarıyla e-posta](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>JOIN eylemi

Bir dizideki tüm öğeleri içeren ve bu öğeleri belirli bir sınırlayıcı karakterle ayıran bir dize oluşturmak için, **JOIN** eylemini kullanın. Daha sonra bu dizeyi, **JOIN** eyleminden sonra gelen eylemler ' de kullanabilirsiniz.

Kod görünümü Düzenleyicisi 'nde çalışmayı tercih ediyorsanız, örnek **birleşimi** kopyalayabilir ve **değişken** eylem tanımlarını bu makaleden kendi mantıksal uygulamanızın temel alınan iş akışı tanımına atayabilirsiniz: [veri işlemi kodu örnekleri-Birleştir](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example)

1. [Azure Portal](https://portal.azure.com) veya Visual Studio 'Da mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

   Bu örnek Azure portal ve bir mantıksal uygulamayı **yineleme** tetikleyicisi ve bir **değişken Başlat** eylemiyle kullanır. Bu eylem, ilk değeri bazı örnek tamsayılar içeren bir dizi olan bir değişken oluşturmak için ayarlanır. Mantıksal uygulamanızı daha sonra test ettiğinizde, tetikleyicinin tetiklenmesi beklemeden uygulamanızı el ile çalıştırabilirsiniz.

   !["JOIN" eylemi için örnek mantıksal uygulama başlatılıyor](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

1. Bir diziden dize oluşturmak istediğiniz mantıksal uygulamanızda şu adımlardan birini izleyin:

   * Son adım altında bir eylem eklemek için **yeni adım**' ı seçin.

     !["Yeni adım" eylemini "Birleştir" eylemi için seçin](./media/logic-apps-perform-data-operations/new-step-add-join-action.png)

   * Adımlar arasında bir eylem eklemek için farenizi bağlantı oku üzerine taşıyarak artı işareti ( **+** ) belirir. Artı işaretini seçin ve ardından **Eylem Ekle**' yi seçin.

1. Arama kutusuna filtreniz olarak `join` girin. Eylemler listesinden şu eylemi seçin: **Birleştir**

   !["Birleştir" eylemini seçin](./media/logic-apps-perform-data-operations/select-join-operation-action.png)

1. **Kimden** kutusunda, bir dize olarak birleştirmek istediğiniz öğeleri içeren diziyi belirtin.

   Bu örnek için, daha önce oluşturulan değişkeni seçebileceğiniz olan, **Kimden** kutusunun içine tıkladığınızda görüntülenen dinamik içerik listesidir:  

   ![Dizeyi oluşturmak için dizi çıkışı seçin](./media/logic-apps-perform-data-operations/configure-join-action.png)

1. **Birlikte birleştir** kutusuna her bir dizi öğesini ayırmak istediğiniz karakteri girin. 

   Bu örnek, iki nokta üst üste kullanır (:) ayırıcı olarak.

   ![Ayırıcı karakteri sağlama](./media/logic-apps-perform-data-operations/finished-join-action.png)

1. Mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

Temel alınan iş akışı tanımınızda bu eylem hakkında daha fazla bilgi için, bkz. [JOIN eylemi](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action).

### <a name="test-your-logic-app"></a>Mantıksal uygulamanızı test etme

**JOIN** eyleminin beklenen sonuçları oluşturup oluşturmadığını doğrulamak Için, **JOIN** eyleminden çıkış içeren bir bildirim gönderin.

1. Mantıksal uygulamanızda, size **JOIN** eyleminden sonuçları gönderebilen bir eylem ekleyin.

1. Bu eylemde, sonuçların görünmesini istediğiniz herhangi bir yere tıklayın. Dinamik içerik listesi açıldığında, **Birleştir** eylemi altında **Çıkış**' ı seçin. 

   Bu örnek, Office 365 Outlook **e-posta gönder** eylemini kullanır ve e-postanın gövdesinde **Çıkış** alanını içerir:

   !["JOIN" eylemi için "çıkış" alanları](./media/logic-apps-perform-data-operations/send-email-join-action.png)

1. Şimdi mantıksal uygulamanızı el ile çalıştırın. Tasarımcı araç çubuğunda **Çalıştır**' ı seçin.

   Kullandığınız e-posta bağlayıcısını temel alarak Alacağınız sonuçlar şunlardır:

   !["JOIN" eylem sonuçlarıyla e-posta](./media/logic-apps-perform-data-operations/join-send-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>JSON eylemini Ayrıştır

JavaScript Nesne Gösterimi (JSON) içeriğindeki özelliklere başvurmak veya erişmek için, **JSON 'U Ayrıştır** eylemini kullanarak bu özellikler için Kullanıcı dostu alanlar veya belirteçler oluşturabilirsiniz. Bu şekilde, mantıksal uygulamanız için giriş belirttiğinizde bu özellikleri dinamik içerik listesinden seçebilirsiniz. Bu eylem için, bir JSON şeması sağlayabilir veya örnek JSON içeriğinizi ya da yükünüzü bir JSON şeması oluşturabilirsiniz.

Kod görünümü Düzenleyicisi 'nde çalışmayı tercih ediyorsanız, örnek **JSON ayrışını** kopyalayabilir ve değişken eylem tanımlarını bu makaleden kendi mantıksal uygulamanızın temel alınan iş akışı tanımına **başlatabilirsiniz** : [veri işlemi kodu örnekleri-Parse JSON](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example)

1. [Azure Portal](https://portal.azure.com) veya Visual Studio 'Da mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

   Bu örnek Azure portal ve bir mantıksal uygulamayı **yineleme** tetikleyicisi ve bir **değişken Başlat** eylemiyle kullanır. Eylem, ilk değeri özellikleri ve değerleri içeren bir JSON nesnesi olan bir değişken oluşturmak için ayarlanır. Mantıksal uygulamanızı daha sonra test ettiğinizde, tetikleyicinin tetiklenmesi gerekmeden uygulamanızı el ile çalıştırabilirsiniz.

   !["JSON ayrıştırma" eylemi için örnek mantıksal uygulama başlatılıyor](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

1. JSON içeriğini ayrıştırmak istediğiniz mantıksal uygulamanızda şu adımlardan birini izleyin:

   * Son adım altında bir eylem eklemek için **yeni adım**' ı seçin.

     !["JSON ayrıştırma" eylemi için "yeni adım" seçeneğini belirleyin](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * Adımlar arasında bir eylem eklemek için farenizi bağlantı oku üzerine taşıyarak artı işareti ( **+** ) belirir. Artı işaretini seçin ve ardından **Eylem Ekle**' yi seçin.

1. Arama kutusuna filtreniz olarak `parse json` girin. Eylemler listesinden **JSON 'U Ayrıştır** eylemini seçin.

   !["JSON ayrıştırma" eylemini seçin](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

1. **İçerik** kutusunda, AYRıŞTıRMAK istediğiniz JSON içeriğini girin.

   Bu örnekte, **içerik** kutusunun içine tıkladığınızda, daha önce oluşturulan değişkeni seçebilmeniz için dinamik içerik listesi görüntülenir:

   ![JSON eylemini ayrıştırmak için JSON nesnesi seçin](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

1. Ayrıştırmakta olduğunuz JSON içeriğini açıklayan JSON şemasını girin.

   Bu örnekte, JSON şeması aşağıda verilmiştir:

   ![Ayrıştırmak istediğiniz JSON nesnesi için JSON şeması sağlayın](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   Şemaya sahip değilseniz, bu şemayı, ayrıştırılıyorsunuz JSON içeriğinden veya *yükünde*oluşturabilirsiniz. 
   
   1. JSON 'u **Ayrıştır** eyleminde **şema oluşturmak Için örnek yük kullan**' ı seçin.

   1. **Örnek BIR JSON yükü girin veya yapıştırın**, JSON içeriğini sağlayın ve **bitti**' yi seçin.

      ![Şemayı oluşturmak için JSON içeriğini girin](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

1. Mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

Temel alınan iş akışı tanımınızda bu eylem hakkında daha fazla bilgi için bkz. [JSON eylemini ayrıştırma](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Mantıksal uygulamanızı test etme

**JSON eylemini Ayrıştır** eyleminin beklenen sonuçları oluşturup oluşturmadığını doğrulamak IÇIN, **JSON 'u Ayrıştır** eyleminden çıkış içeren bir bildirim gönderin.

1. Mantıksal uygulamanızda, **JSON 'U Ayrıştır** eyleminden sonuçları gönderebilen bir eylem ekleyin.

1. Bu eylemde, sonuçların görünmesini istediğiniz herhangi bir yere tıklayın. Dinamik içerik listesi açıldığında, **JSON 'U Ayrıştır** eylemi altında, artık ayrıştırılmış JSON içeriğinden özellikleri seçebilirsiniz.

   Bu örnek, Office 365 Outlook **e-posta gönder** eylemini kullanır ve e-postanın gövdesinde **FirstName**, **LastName**ve **email** alanlarını içerir:

   !["E-posta gönder" eyleminde JSON özellikleri](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   Tamamlanan e-posta eylemi aşağıda verilmiştir:

   ![E-posta eylemi için tamamlanmış örnek](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

1. Şimdi mantıksal uygulamanızı el ile çalıştırın. Tasarımcı araç çubuğunda **Çalıştır**' ı seçin. 

   Kullandığınız e-posta bağlayıcısını temel alarak Alacağınız sonuçlar şunlardır:

   !["JSON ayrıştırma" eylem sonuçlarıyla e-posta](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>Eylem Seç

Varolan bir dizideki değerlerden oluşturulan JSON nesnelerine sahip bir dizi oluşturmak için, **seçme** eylemini kullanın. Örneğin, her bir JSON nesnesinin sahip olması gereken özellikleri belirterek ve kaynak dizideki değerleri bu özelliklerle eşlemek için bir tamsayı dizisindeki her bir değer için bir JSON nesnesi oluşturabilirsiniz. Bu JSON nesnelerindeki bileşenleri değiştirebilseniz de, çıkış dizisi her zaman kaynak dizisiyle aynı sayıda öğe içeriyor.

> [!NOTE]
> **Seçim** eyleminin dizi çıktısını kullanması için, bu eylemler dizileri girdi olarak kabul etmelidir ya da çıktı dizisini başka bir uyumlu biçime dönüştürmeniz gerekebilir. 

Kod görünümü Düzenleyicisi 'nde çalışmayı tercih ediyorsanız **, örneği kopyalayabilir** ve bu makaledeki **değişken** eylem tanımlarını kendi mantıksal uygulamanızın temel alınan iş akışı tanımına ekleyebilirsiniz: [veri işlemi kodu örnekleri-Seç ](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. [Azure Portal](https://portal.azure.com) veya Visual Studio 'Da mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

   Bu örnek Azure portal ve bir mantıksal uygulamayı **yineleme** tetikleyicisi ve bir **değişken Başlat** eylemiyle kullanır. Eylem, ilk değeri bazı örnek tamsayılar içeren bir dizi olan bir değişken oluşturmak için ayarlanır. Mantıksal uygulamanızı daha sonra test ettiğinizde, tetikleyicinin tetiklenmesi gerekmeden uygulamanızı el ile çalıştırabilirsiniz.

   !["Select" eylemi için örnek mantıksal uygulama başlatılıyor](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

1. Diziyi oluşturmak istediğiniz mantıksal uygulamanızda şu adımlardan birini izleyin: 

   * Son adım altında bir eylem eklemek için **yeni adım**' ı seçin.

     !["Seç" eylemi için "yeni adım" seçeneğini belirleyin](./media/logic-apps-perform-data-operations/add-select-operation-action.png)

   * Adımlar arasında bir eylem eklemek için farenizi bağlantı oku üzerine taşıyarak artı işareti ( **+** ) belirir. Artı işaretini seçin ve ardından **Eylem Ekle**' yi seçin.

1. **Eylem seçin**altında, **yerleşik**' i seçin. Arama kutusuna filtreniz olarak `select` girin. Eylemler listesinden, **seçme** eylemini seçin.

   !["Seç" eylemini seçin](./media/logic-apps-perform-data-operations/select-select-action.png)

1. **Kimden** kutusunda istediğiniz kaynak diziyi belirtin.

   Bu örnekte, **Kimden** kutusunun içine tıkladığınızda, daha önce oluşturulan değişkeni seçebilmeniz için dinamik içerik listesi görüntülenir:

   ![Seçim eylemi için kaynak diziyi seçin](./media/logic-apps-perform-data-operations/configure-select-action.png)

1. **Harita** kutusunun sol sütununda, kaynak dizide her bir değere atamak istediğiniz özellik adını sağlayın. Sağ sütunda, özelliği atamak istediğiniz değeri temsil eden bir ifade belirtin.

   Bu örnek, her bir dizi öğesine erişen bir ifadede `item()` işlevini kullanarak tamsayı dizisindeki her bir değeri atamak için özellik adı olarak "Product_ID" değerini belirtir. 

   ![Dizi oluşturmak için JSON nesnesi özelliğini ve değerlerini belirtin](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   Tamamlanan eylem aşağıda verilmiştir:

   !["Select" eylemi için tamamlanmış örnek](./media/logic-apps-perform-data-operations/finished-select-action.png)

1. Mantıksal uygulamanızı kaydedin. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

Temel alınan iş akışı tanımınızda bu eylem hakkında daha fazla bilgi için bkz. [eylem seçme](../logic-apps/logic-apps-workflow-actions-triggers.md).

### <a name="test-your-logic-app"></a>Mantıksal uygulamanızı test etme

**Seçme** eyleminin beklenen sonuçları oluşturup oluşturmadığını doğrulamak Için, **Select** eyleminden çıkış içeren bir bildirim gönderin.

1. Mantıksal uygulamanızda, **seçim** eyleminden sonuçları gönderebilen bir eylem ekleyin.

1. Bu eylemde, sonuçların görünmesini istediğiniz herhangi bir yere tıklayın. Dinamik içerik listesi açıldığında, **ifade**' ı seçin. **Select** eyleminden dizi çıkışını almak Için, **Select** eyleminin adını içeren bu ifadeyi girin:

   `@actionBody('Select')`

   Bu örnek, Office 365 Outlook **e-posta gönder** eylemini kullanır ve e-postanın gövdesinde `@actionBody('Select')` ifadeden çıktıları içerir:

   !["Select" eyleminden eylem çıkışları](./media/logic-apps-perform-data-operations/send-email-select-action.png)

1. Şimdi mantıksal uygulamanızı el ile çalıştırın. Tasarımcı araç çubuğunda **Çalıştır**' ı seçin.

   Kullandığınız e-posta bağlayıcısını temel alarak Alacağınız sonuçlar şunlardır:

   !["Select" eylem sonuçlarıyla e-posta](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
