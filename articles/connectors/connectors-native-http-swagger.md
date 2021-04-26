---
title: Azure Logic Apps REST uç noktalarına Bağlan
description: Azure Logic Apps kullanarak otomatik görevler, süreçler ve iş akışlarında REST uç noktalarını izleme
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: 8bd46f013680d5cbc9e75a312206fdb2fdcac7ec
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109365"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Azure Logic Apps kullanarak REST uç noktalarını çağırma

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) ve yerleşik http + Swagger bağlayıcısıyla mantıksal uygulamalar oluşturarak bir [Swagger dosyası](https://swagger.io) aracılığıyla düzenlı olarak herhangi bir REST uç noktasını çağıran iş akışlarını otomatikleştirebilirsiniz. HTTP + Swagger tetikleyicisi ve eylemi, [http tetikleyicisi ve eylemiyle](connectors-native-http.md) aynı şekilde çalışır, ancak Swagger dosyası tarafından tanımlanan API yapısını ve çıkışları açığa çıkararak mantıksal uygulama tasarımcısında daha iyi bir deneyim sağlar. Bir yoklama tetikleyicisi uygulamak için, [mantıksal uygulamalardan diğer API 'leri, hizmetleri ve sistemleri çağırmak üzere özel API 'Ler oluşturma](../logic-apps/logic-apps-create-api-app.md#polling-triggers)bölümünde açıklanan yoklama modelini izleyin.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* Hedef REST uç noktasını açıklayan Swagger (Openapı değil) dosyasının URL 'SI

  Genellikle REST uç noktasının, bağlayıcının çalışması için bu ölçütü karşılaması gerekir:

  * Swagger dosyası herkese açık olarak erişilebilen bir HTTPS URL 'SI üzerinde barındırılmalıdır.
  
  * Swagger dosyası, `operationID` tanımdaki her bir işlem için bir içermelidir. Aksi takdirde, bağlayıcı yalnızca Swagger dosyasındaki son işlemi gösterir. 

  * Swagger dosyasında, çıkış noktaları [arası kaynak paylaşımı (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) etkin olmalıdır.

  Barındırılmayan veya güvenlik ve çapraz kaynak gereksinimlerini karşılamayan bir Swagger dosyasına başvurmak için, [Swagger dosyasını bir Azure depolama hesabındaki blob kapsayıcısına yükleyebilir](#host-swagger)ve bu depolama hesabında CORS 'yi etkinleştirerek dosyaya başvurabilirsiniz.

  Bu konudaki örneklerde bilişsel Hizmetler [hesabı ve erişim anahtarı](../cognitive-services/cognitive-services-apis-create-account.md)gerektiren bilişsel [Hizmetler Yüz Tanıma API'si](../cognitive-services/face/overview.md)kullanılır.

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md)hakkında temel bilgi. Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps](../logic-apps/logic-apps-overview.md) olduğunu gözden geçirin.

* Hedef uç noktayı çağırmak istediğiniz mantıksal uygulama. HTTP + Swagger tetikleyicisinden başlamak için [boş bir mantıksal uygulama oluşturun](../logic-apps/quickstart-create-first-logic-app-workflow.md). HTTP + Swagger eylemini kullanmak için, mantıksal uygulamanızı istediğiniz tetikleyiciyle başlatın. Bu örnek, ilk adım olarak HTTP + Swagger tetikleyicisi kullanır.

## <a name="add-an-http--swagger-trigger"></a>HTTP + Swagger tetikleyicisi ekleme

Bu yerleşik tetikleyici, bir REST API açıklayan ve bu dosyanın içeriğini içeren bir yanıt döndüren Swagger dosyası için bir URL 'ye HTTP isteği gönderir.

1. [Azure portalında](https://portal.azure.com) oturum açın. Mantıksal uygulama tasarımcısında boş mantıksal uygulamanızı açın.

1. Tasarımcıda arama kutusuna filtreniz olarak "Swagger" yazın. **Tetikleyiciler** listesinden **http + Swagger** tetikleyicisi ' ni seçin.

   ![HTTP + Swagger tetikleyicisi seçin](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. **Swagger uç nokta URL 'si** kutusuna Swagger dosyasının URL 'sini girin ve **İleri**' yi seçin.

   Bu örnek bilişsel hizmetler için Batı ABD bölgesinde bulunan Swagger URL 'sini kullanır [Yüz Tanıma API'si](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   !["H T T P + Swagger" tetikleyicisi ve "Swagger Endpoint U R L" özelliği bir U R L değeri olarak ayarlanan mantıksal uygulama tasarımcısını gösteren ekran görüntüsü.](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Tasarımcı Swagger dosyası tarafından tanımlanan işlemleri gösterdiğinde, kullanmak istediğiniz işlemi seçin.

   ![Mantıksal uygulama tasarımcısını "H T T P + Swagger" tetikleyicisi ile gösteren ekran görüntüsü ve Swagger işlemlerini görüntüleyen bir liste.](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Son nokta çağrısına dahil etmek istediğiniz, seçilen işleme göre değişiklik gösteren tetikleyici parametreleri için değerler sağlayın. Tetikleyicinin bitiş noktasını ne sıklıkta çağırmasını istediğinizi belirleyin.

   Bu örnek, adımın daha açıklayıcı bir ada sahip olması için tetikleyiciyi "HTTP + Swagger tetikleyicisi: yüz-Algıla" olarak yeniden adlandırır.

   !["Yüz algılama" işlemini görüntüleyen "H T T P + Swagger" tetikleyicisiyle Logic App Designer 'ı gösteren ekran görüntüsü.](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Kullanılabilir başka parametreler eklemek için **yeni parametre Ekle** listesini açın ve istediğiniz parametreleri seçin.

   HTTP + Swagger için kullanılabilir kimlik doğrulama türleri hakkında daha fazla bilgi için bkz. [giden çağrılara kimlik doğrulama ekleme](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Tetikleyici tetiklendiğinde çalıştırılan eylemlerle mantıksal uygulamanızın iş akışını oluşturmaya devam edin.

1. İşiniz bittiğinde mantıksal uygulamanızı kaydetmeyi unutmayın. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

## <a name="add-an-http--swagger-action"></a>HTTP + Swagger eylemi ekleme

Bu yerleşik eylem, bir REST API açıklayan ve bu dosyanın içeriğini içeren bir yanıt döndüren Swagger dosyasının URL 'sine bir HTTP isteği oluşturur.

1. [Azure portalında](https://portal.azure.com) oturum açın. Mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

1. HTTP + Swagger eylemini eklemek istediğiniz adım altında **yeni adım**' ı seçin.

   Adımlar arasında bir eylem eklemek için, işaretçinizi adımlar arasındaki oka taşıyın. Görüntülenen artı işaretini ( **+** ) seçin ve ardından **Eylem Ekle**' yi seçin.

1. Tasarımcıda arama kutusuna filtreniz olarak "Swagger" yazın. **Eylemler** listesinden **http + Swagger** eylemini seçin.

    ![HTTP + Swagger eylemini seç](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. **Swagger uç nokta URL 'si** kutusuna Swagger dosyasının URL 'sini girin ve **İleri**' yi seçin.

   Bu örnek bilişsel hizmetler için Batı ABD bölgesinde bulunan Swagger URL 'sini kullanır [Yüz Tanıma API'si](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Swagger uç noktası için URL girin](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Tasarımcı Swagger dosyası tarafından tanımlanan işlemleri gösterdiğinde, kullanmak istediğiniz işlemi seçin.

   ![Swagger dosyasındaki işlemler](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Uç nokta çağrısına dahil etmek istediğiniz, seçilen işleme göre değişen eylem parametrelerinin değerlerini belirtin.

   Bu örnekte parametre yoktur, ancak adımın daha açıklayıcı bir adı olması için eylemi "HTTP + Swagger eylemi: yüz-tanımla" olarak yeniden adlandırır.

   ![İşlem ayrıntıları](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Kullanılabilir başka parametreler eklemek için **yeni parametre Ekle** listesini açın ve istediğiniz parametreleri seçin.

   HTTP + Swagger için kullanılabilir kimlik doğrulama türleri hakkında daha fazla bilgi için bkz. [giden çağrılara kimlik doğrulama ekleme](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. İşiniz bittiğinde mantıksal uygulamanızı kaydetmeyi unutmayın. Tasarımcı araç çubuğunda **Kaydet**' i seçin.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Azure depolamada ana bilgisayar Swagger

Bu dosyayı bir Azure depolama hesabındaki blob kapsayıcısına yükleyerek ve bu depolama hesabında CORS 'yi etkinleştirerek, barındırılmayan veya güvenlik ve çapraz kaynak gereksinimlerini karşılamayan bir Swagger dosyasına başvurabilirsiniz. Azure depolama 'da Swagger dosyalarını oluşturmak, ayarlamak ve depolamak için şu adımları izleyin:

1. [Bir Azure depolama hesabı oluşturun](../storage/common/storage-account-create.md).

1. Şimdi blob için CORS 'yi etkinleştirin. Depolama hesabınızın menüsünde **CORS**' yi seçin. **BLOB hizmeti** sekmesinde, bu değerleri belirtin ve ardından **Kaydet**' i seçin.

   | Özellik | Değer |
   |----------|-------|
   | **İzin verilen çıkış noktaları** | `*` |
   | **İzin verilen Yöntemler** | `GET`, `HEAD`, `PUT` |
   | **İzin verilen üst bilgiler** | `*` |
   | **Kullanıma sunulan üst bilgiler** | `*` |
   | **En fazla yaş** (saniye) | `200` |
   |||

   Bu örnek [Azure Portal](https://portal.azure.com)kullansa da [Azure Depolama Gezgini](https://storageexplorer.com/)gibi bir araç kullanabilir veya bu örnek [PowerShell betiğini](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1)kullanarak bu ayarı otomatik olarak yapılandırabilirsiniz.

1. [BLOB kapsayıcısı oluşturun](../storage/blobs/storage-quickstart-blobs-portal.md). Kapsayıcının **genel bakış** bölmesinde **erişim düzeyini Değiştir**' i seçin. **Genel erişim düzeyi** listesinden blob ' u seçin **(yalnızca blob 'lar için anonim okuma erişimi)** ve **Tamam**' ı seçin.

1. Swagger dosyasını [Azure Portal](https://portal.azure.com) ya da [Azure Depolama Gezgini](https://storageexplorer.com/)aracılığıyla [BLOB kapsayıcısına yükleyin](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob).

1. Blob kapsayıcısındaki dosyaya başvurmak için, bu biçimi izleyen, Azure Depolama Gezgini büyük/küçük harfe duyarlı HTTPS URL 'sini alın:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<complete-swagger-file-name>?<query-parameters>`

## <a name="connector-reference"></a>Bağlayıcı başvurusu

HTTP + Swagger tetikleyicisinden veya eylemden gelen çıktılar hakkında daha fazla bilgi bulabilirsiniz. HTTP + Swagger çağrısı şu bilgileri döndürür:

| Özellik adı | Tür | Description |
|---------------|------|-------------|
| bilgisinde | object | İstekten gelen üstbilgiler |
| body | object | İstekten gelen gövde içeriğine sahip nesne |
| durum kodu | int | İstekteki durum kodu |
||||

| Durum kodu | Description |
|-------------|-------------|
| 200 | Tamam |
| 202 | Kabul edildi |
| 400 | Hatalı istek |
| 401 | Yetkisiz |
| 403 | Yasak |
| 404 | Bulunamadı |
| 500 | İç sunucu hatası. Bilinmeyen bir hata oluştu. |
|||

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
