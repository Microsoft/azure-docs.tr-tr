---
title: HTTP ve HTTPS uç noktalarını çağırma-Azure Logic Apps
description: Azure Logic Apps kullanarak giden istekleri HTTP ve HTTPS uç noktalarına gönderin
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: 4f585778808b133fe5c731ab6a3189594cbfc0f9
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72804219"
---
# <a name="send-outgoing-calls-to-http-or-https-endpoints-by-using-azure-logic-apps"></a>Azure Logic Apps kullanarak HTTP veya HTTPS uç noktalarına giden çağrıları gönderin

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) ve yerleşik http tetikleyicisi veya eylemiyle, istekleri HERHANGI bir http veya HTTPS uç noktasına düzenli olarak gönderen otomatikleştirilmiş görevler ve iş akışları oluşturabilirsiniz. Bunun yerine gelen HTTP veya HTTPS çağrılarını almak ve yanıtlamak için yerleşik [istek tetikleyicisi veya Yanıt eylemini](../connectors/connectors-native-reqres.md)kullanın.

Örneğin, belirli bir zamanlamaya göre bu uç noktayı denetleyerek Web siteniz için hizmet uç noktasını izleyebilirsiniz. Bu uç noktada ilgili Web siteniz gibi belirli bir olay gerçekleştiğinde, olay mantıksal uygulamanızın iş akışını tetikler ve belirtilen eylemleri çalıştırır.

Bir uç noktayı düzenli bir zamanlamaya göre denetlemek veya *yoklamak* IÇIN, http tetikleyicisini iş akışınızın ilk adımı olarak kullanabilirsiniz. Her denetim için tetikleyici, uç noktaya bir çağrı veya *istek* gönderir. Uç noktanın yanıtı, mantıksal uygulamanızın iş akışının çalışıp çalışmadığını belirler. Tetikleyici, mantıksal uygulamanızdaki eylemlere karşılık gelen tüm içerik üzerinde geçiş yapar.

HTTP eylemini, istediğiniz zaman uç noktayı çağırmak için iş akışınızda başka herhangi bir adım olarak kullanabilirsiniz. Uç noktanın yanıtı, iş akışınızın kalan eylemlerinin nasıl çalışacağını belirler.

Hedef uç noktanın özelliği temel alınarak, HTTP Bağlayıcısı Aktarım Katmanı Güvenliği (TLS) 1,0, 1,1 ve 1,2 sürümlerini destekler. Logic Apps, uç nokta ile mümkün olan en yüksek desteklenen sürümü kullanarak görüşür. Bu nedenle, örneğin, uç nokta 1,2 ' ı destekliyorsa, bağlayıcı önce 1,2 ' i kullanır. Aksi halde, bağlayıcı desteklenen bir sonraki en yüksek sürümü kullanır.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* Çağırmak istediğiniz hedef uç noktanın URL 'SI

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md)hakkında temel bilgi. Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps](../logic-apps/logic-apps-overview.md)olduğunu gözden geçirin.

* Hedef uç noktayı çağırmak istediğiniz mantıksal uygulama. HTTP tetikleyicisiyle başlamak için [boş bir mantıksal uygulama oluşturun](../logic-apps/quickstart-create-first-logic-app-workflow.md). HTTP eylemini kullanmak için mantıksal uygulamanızı istediğiniz tetikleyiciyle başlatın. Bu örnek, ilk adım olarak HTTP tetikleyicisini kullanır.

## <a name="add-an-http-trigger"></a>HTTP tetikleyicisi ekleme

Bu yerleşik tetikleyici, bir uç nokta için belirtilen URL 'ye HTTP çağrısı yapar ve bir yanıt döndürür.

1. [Azure Portal](https://portal.azure.com)’ında oturum açın. Mantıksal uygulama tasarımcısında boş mantıksal uygulamanızı açın.

1. **Eylem seçin**altında, arama kutusuna filtreniz olarak "http" yazın. **Tetikleyiciler** listesinden **http** tetikleyicisi ' ni seçin.

   ![HTTP tetikleyicisi seçin](./media/connectors-native-http/select-http-trigger.png)

   Bu örnek, adımın daha açıklayıcı bir ada sahip olması için tetikleyiciyi "HTTP tetikleyicisi" olarak yeniden adlandırır. Ayrıca, örnek daha sonra bir HTTP eylemi ekler ve her iki ad de benzersiz olmalıdır.

1. Hedef uç noktaya çağrısına dahil etmek istediğiniz [http tetikleyici parametrelerinin](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger) değerlerini belirtin. Tetikleyicinin hedef uç noktayı denetlemesini istediğiniz sıklık için yinelemeyi ayarlayın.

   **Hiçbiri**dışında bir kimlik doğrulama türü seçerseniz, kimlik doğrulama ayarları seçiminize göre farklılık gösterir. Daha fazla bilgi için bkz. [giden çağrılara kimlik doğrulama ekleme](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

   ![HTTP tetikleyici parametrelerini girin](./media/connectors-native-http/http-trigger-parameters.png)

1. Kullanılabilir başka parametreler eklemek için **yeni parametre Ekle** listesini açın ve istediğiniz parametreleri seçin.

1. Tetikleyici tetiklendiğinde çalıştırılan eylemlerle mantıksal uygulamanızın iş akışını oluşturmaya devam edin.

1. İşiniz bittiğinde mantıksal uygulamanızı kaydetmeyi unutmayın. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

## <a name="add-an-http-action"></a>HTTP eylemi ekleme

Bu yerleşik eylem, bir uç nokta için belirtilen URL 'ye HTTP çağrısı yapar ve bir yanıt döndürür.

1. [Azure Portal](https://portal.azure.com)’ında oturum açın. Mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

   Bu örnek, ilk adım olarak HTTP tetikleyicisini kullanır.

1. HTTP eylemini eklemek istediğiniz adım altında **yeni adım**' ı seçin.

   Adımlar arasında bir eylem eklemek için, işaretçinizi adımlar arasındaki oka taşıyın. Görüntülenen artı işaretini ( **+** ) seçin ve ardından **Eylem Ekle**' yi seçin.

1. **Eylem seçin**altında, arama kutusuna filtreniz olarak "http" yazın. **Eylemler** listesinden **http** eylemini seçin.

   ![HTTP eylemi seçin](./media/connectors-native-http/select-http-action.png)

   Bu örnek, adımın daha açıklayıcı bir ada sahip olması için eylemi "HTTP eylemi" olarak yeniden adlandırır.

1. Hedef uç noktaya çağrısına dahil etmek istediğiniz [http eylem parametrelerinin](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action) değerlerini belirtin.

   **Hiçbiri**dışında bir kimlik doğrulama türü seçerseniz, kimlik doğrulama ayarları seçiminize göre farklılık gösterir. Daha fazla bilgi için bkz. [giden çağrılara kimlik doğrulama ekleme](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

   ![HTTP eylem parametrelerini girin](./media/connectors-native-http/http-action-parameters.png)

1. Kullanılabilir başka parametreler eklemek için **yeni parametre Ekle** listesini açın ve istediğiniz parametreleri seçin.

1. İşiniz bittiğinde mantıksal uygulamanızı kaydetmeyi unutmayın. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

## <a name="content-with-multipartform-data-type"></a>Multipart/form veri türü olan içerik

HTTP isteklerinde `multipart/form-data` türüne sahip içeriği işlemek için, bu biçimi kullanarak HTTP isteğinin gövdesine `$content-type` ve `$multipart` özniteliklerini içeren bir JSON nesnesi ekleyebilirsiniz.

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

Örneğin, bir Web sitesine bir Excel dosyası için HTTP POST isteği gönderen ve `multipart/form-data` türünü destekleyen bu sitenin API 'sini kullanarak bir mantıksal uygulamanız olduğunu varsayalım. Bu eylemin şu şekilde görünebileceğini aşağıda görebilirsiniz:

![Çok parçalı form verileri](./media/connectors-native-http/http-action-multipart.png)

Aşağıda, temel alınan iş akışı tanımında HTTP eyleminin JSON tanımını gösteren aynı örnek verilmiştir:

```json
{
   "HTTP_action": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Tetikleyici ve eylem parametreleri hakkında daha fazla bilgi için aşağıdaki bölümlere bakın:

* [HTTP tetikleyici parametreleri](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)
* [HTTP eylem parametreleri](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action)

### <a name="output-details"></a>Çıkış ayrıntıları

Bu bilgileri döndüren bir HTTP tetikleyicisinden veya eylemden çıktılar hakkında daha fazla bilgi bulabilirsiniz:

| Özellik adı | Tür | Açıklama |
|---------------|------|-------------|
| Bilgisinde | object | İstekten gelen üstbilgiler |
| bölümü | object | JSON nesnesi | İstekten gelen gövde içeriğine sahip nesne |
| durum kodu | int | İstekteki durum kodu |
|||

| Durum kodu | Açıklama |
|-------------|-------------|
| 200 | TAMAM |
| 202 | Eden |
| 400 | Hatalı istek |
| 401 | Yetkilendirilmemiş |
| 403 | Yasak |
| 404 | Bulunamadı |
| 500 | İç sunucu hatası. Bilinmeyen bir hata oluştu. |
|||

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
