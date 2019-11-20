---
title: Bing Arama bağlanma-Azure Logic Apps
description: Bing Arama REST API 'Leri ve Azure Logic Apps haberleri bulun
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: f7558a5836d8f087e719346fb38bbf24ece2c8fb
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026777"
---
# <a name="find-news-with-bing-search-and-azure-logic-apps"></a>Bing Arama ve Azure Logic Apps haberleri bulun

Bu makalede, Bing Arama Bağlayıcısı ile bir mantıksal uygulama içinden Bing Arama aracılığıyla haberleri, Videoları ve diğer öğeleri nasıl bulabileceğiniz gösterilmektedir. Bu şekilde, arama sonuçlarını işlemek ve bu öğeleri diğer eylemler için kullanılabilir hale getirmek için görevleri ve iş akışlarını otomatikleştiren mantıksal uygulamalar oluşturabilirsiniz. 

Örneğin, arama ölçütlerine göre haber öğelerini bulabilir ve Twitter 'da bu öğeleri Twitter akışınızda farklı bir şekilde ilan edebilirsiniz.

Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/). Logic Apps 'e yeni başladıysanız, [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ve [hızlı başlangıç: Ilk mantıksal uygulamanızı oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md)' yı gözden geçirin.
Bağlayıcıya özgü teknik bilgiler için [Bing arama bağlayıcı başvurusuna](https://docs.microsoft.com/connectors/bingsearch/)bakın.

## <a name="prerequisites"></a>Önkoşullar

* Bilişsel [Hizmetler hesabı](../cognitive-services/cognitive-services-apis-create-account.md)

* Mantıksal uygulamanızdan Bing Arama API'leri erişim sağlayan [Bing arama BIR API anahtarı](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api)

* Olay Hub 'ınıza erişmek istediğiniz mantıksal uygulama. Mantıksal uygulamanızı bir Bing Arama tetikleyicisi ile başlatmak için [boş bir mantıksal uygulama](../logic-apps/quickstart-create-first-logic-app-workflow.md)gerekir.

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Bing Arama tetikleyicisi ekleme

Azure Logic Apps, her mantıksal uygulama, belirli bir olay gerçekleştiğinde veya belirli bir koşul karşılandığında tetiklenen bir [tetikleyiciyle](../logic-apps/logic-apps-overview.md#logic-app-concepts)başlamalıdır. Tetikleyici her tetiklendiğinde Logic Apps altyapısı bir mantıksal uygulama örneği oluşturur ve uygulamanızın iş akışını çalıştırmaya başlar.

1. Azure portal veya Visual Studio 'da mantıksal uygulama Tasarımcısı ' nı açan boş bir mantıksal uygulama oluşturun. Bu örnek Azure portal kullanır.

2. Arama kutusuna filtreniz olarak "Bing arama" yazın. Tetikleyiciler listesinden istediğiniz tetikleyiciyi seçin.

   Bu örnek, bu tetikleyiciyi kullanır: **Bing arama-yeni haber makalesinde**

   ![Bing Arama tetikleyiciyi bul](./media/connectors-create-api-bing-search/add-trigger.png)

3. Bağlantı ayrıntıları istenirse [Bing arama bağlantınızı hemen oluşturun](#create-connection).
Ya da bağlantınız zaten varsa, tetikleyici için gerekli bilgileri sağlayın.

   Bu örnekte, Bing Arama eşleşen haber makalelerini döndürme ölçütlerini belirtin.

   | Özellik | Gereklidir | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | Arama sorgusu | Yes | <*arama-sözcük*> | Kullanmak istediğiniz arama anahtar sözcüklerini girin. |
   | Market | Yes | <*yerel ayar*> | Arama yerel ayarı. Varsayılan "en-US" değeridir, ancak başka bir değer belirleyebilirsiniz. |
   | Güvenli Arama | Yes | <*arama düzeyi*> | Yetişkinlere yönelik içeriği dışlamak için filtre düzeyi. Varsayılan değer "Orta" dır, ancak başka bir düzey seçersiniz. |
   | Sayı | Hayır | <*sonuç-sayı*> | Belirtilen sayıda sonuç döndürün. Varsayılan değer 20 ' dir, ancak başka bir değer belirtebilirsiniz. Döndürülen sonuçların gerçek sayısı belirtilen sayıdan daha az olabilir. |
   | Uzaklık | Hayır | <*Skip-değer*> | Sonuçları döndürmeden önce atlanacak sonuç sayısı |
   |||||

   Örnek:

   ![Tetikleyiciyi ayarla](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Tetikleyicinin sonuçları ne sıklıkta denetlemesini istediğinizi belirlemek için aralığı ve sıklığı seçin.

5. İşiniz bittiğinde, Tasarımcı araç çubuğunda **Kaydet**' i seçin.

6. Şimdi, tetikleyici sonuçlarıyla gerçekleştirmek istediğiniz görevler için mantıksal uygulamanıza bir veya daha fazla eylem eklemeye devam edin.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Bing Arama eylemi ekleme

Azure Logic Apps bir [eylem](../logic-apps/logic-apps-overview.md#logic-app-concepts) , iş akışınızda bir tetikleyiciyi veya başka bir eylemi izleyen bir adımdır. Bu örnekte, mantıksal uygulama, belirtilen ölçütlerle eşleşen haber makalelerini döndüren Bing Arama tetikleyicisiyle başlar.

1. Azure portal veya Visual Studio 'da mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın. Bu örnek Azure portal kullanır.

2. Tetikleyici veya eylem altında **yeni adım** > **Eylem Ekle**' yi seçin.

   Bu örnek, bu tetikleyiciyi kullanır:

   **Bing Arama-yeni haber makalesinde**

   ![Eylem Ekle](./media/connectors-create-api-bing-search/add-action.png)

   Varolan adımlar arasında bir eylem eklemek için farenizi bağlantı oku üzerine taşıyın. 
   Görüntülenen artı işaretini ( **+** ) seçin ve ardından **Eylem Ekle**' yi seçin.

3. Arama kutusuna filtreniz olarak "Bing arama" yazın.
Eylemler listesinden istediğiniz eylemi seçin.

   Bu örnek, bu eylemi kullanır:

   **Bing Arama-sorguya göre haberleri listeleme**

   ![Bing Arama eylemi bul](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Bağlantı ayrıntıları istenirse [Bing arama bağlantınızı hemen oluşturun](#create-connection). Ya da bağlantınız zaten varsa, eylem için gerekli bilgileri sağlayın.

   Bu örnek için, tetikleyicinin sonuçlarının bir alt kümesini döndürme ölçütlerini belirtin.

   | Özellik | Gereklidir | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | Arama sorgusu | Yes | <*Arama ifadesi*> | Tetikleyici sonuçlarını sorgulamak için bir ifade girin. Dinamik içerik listesindeki alanlardan seçim yapabilir veya ifade Oluşturucusu ile bir ifade oluşturabilirsiniz. |
   | Market | Yes | <*yerel ayar*> | Arama yerel ayarı. Varsayılan "en-US" değeridir, ancak başka bir değer belirleyebilirsiniz. |
   | Güvenli Arama | Yes | <*arama düzeyi*> | Yetişkinlere yönelik içeriği dışlamak için filtre düzeyi. Varsayılan değer "Orta" dır, ancak başka bir düzey seçersiniz. |
   | Sayı | Hayır | <*sonuç-sayı*> | Belirtilen sayıda sonuç döndürün. Varsayılan değer 20 ' dir, ancak başka bir değer belirtebilirsiniz. Döndürülen sonuçların gerçek sayısı belirtilen sayıdan daha az olabilir. |
   | Uzaklık | Hayır | <*Skip-değer*> | Sonuçları döndürmeden önce atlanacak sonuç sayısı |
   |||||

   Örneğin, kategori adı "Tech" sözcüğünü içeren bu sonuçların olmasını istediğinizi varsayalım.

   1. Dinamik içerik listesi görünecek şekilde **arama sorgusu** kutusuna tıklayın. 
   Bu **listeden ifade ' u seçerek ifade** oluşturucusunun görünmesini sağlayın. 

      ![Bing Arama tetikleyicisi](./media/connectors-create-api-bing-search/bing-search-action.png)

      Şimdi ifadenizi oluşturmaya başlayabilirsiniz.

   2. İşlevler listesinden, ifade kutusunda görüntülenen **Contains ()** işlevini seçin. Alan listesinin yeniden görünmesi için **dinamik içerik** ' e tıklayın, ancak imlecinizin parantez içinde kalmasını sağlayın.

      ![Bir işlev seçin](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. Alan listesinden, bir parametreye dönüştüren **Kategori**' yi seçin. 
   İlk parametreden sonra bir virgül ekleyin ve virgülden sonra şu kelimeyi ekleyin: `'tech'` 

      ![Bir alan seçin](./media/connectors-create-api-bing-search/expression-select-field.png)

   4. İşiniz bittiğinde **Tamam**’ı seçin.

      İfade şimdi **arama sorgusu** kutusunda şu biçimde görünür:

      ![Tamamlandı ifadesi](./media/connectors-create-api-bing-search/resolved-expression.png)

      Kod görünümünde, bu ifade şu biçimde görünür:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. İşiniz bittiğinde, Tasarımcı araç çubuğunda **Kaydet**' i seçin.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Bing Arama Bağlan

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Bağlantı bilgileri istendiğinde, şu ayrıntıları sağlayın:

   | Özellik | Gereklidir | Değer | Açıklama |
   |----------|----------|-------|-------------|
   | Bağlantı Adı | Yes | <*bağlantı-adı*> | Bağlantınız için oluşturulacak ad |
   | API sürümü | Yes | <*API-version*> | Varsayılan olarak, Bing Arama API sürümü geçerli sürüme ayarlanır. Daha önceki bir sürümü gerektiği gibi seçebilirsiniz. |
   | API anahtarı | Yes | <*API-key*> | Daha önce aldığınız Bing Arama API anahtarı. Anahtarınız yoksa [API anahtarınızı hemen](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api)alın. |  
   |||||  

   Örnek:

   ![Bağlantı oluşturma](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. İşiniz bittiğinde **Oluştur**’u seçin.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Openapı (eski adıyla Swagger) dosyasında açıklandığı gibi Tetikleyiciler, Eylemler ve sınırlar gibi teknik ayrıntılar için [bağlayıcının başvuru sayfasına](/connectors/bingsearch/)bakın.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
