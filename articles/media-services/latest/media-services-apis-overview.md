---
title: V3 API 'Leri ile geliştirme-Azure | Microsoft Docs
description: Bu makalede, Media Services v3 ile geliştirme sırasında varlıklar ve API 'Ler için uygulanan kurallar açıklanmaktadır.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 79f1bd95451709485f92050a882c790f9e281eb5
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74049024"
---
# <a name="developing-with-media-services-v3-apis"></a>Media Services v3 API 'Leri ile geliştirme

Bir geliştirici olarak, özel medya iş akışlarını kolayca oluşturmak, yönetmek ve korumak için REST API ile etkileşime girebilmeniz için Media Services [REST API](https://aka.ms/ams-v3-rest-ref) veya istemci kitaplıklarını kullanabilirsiniz. [Media Services v3](https://aka.ms/ams-v3-rest-sdk) API 'Si, openapı belirtimini temel alır (önceki adı Swagger olarak bilinir).

Bu makalede, Media Services v3 ile geliştirme sırasında varlıklar ve API 'Ler için uygulanan kurallar açıklanmaktadır.

## <a name="accessing-the-azure-media-services-api"></a>Azure Media Services API 'sine erişme

Media Services kaynaklarına ve Media Services API 'sine erişme yetkisi sağlamak için, önce kimliğiniz doğrulanmalıdır. Media Services, [Azure Active Directory (Azure AD) tabanlı](../../active-directory/fundamentals/active-directory-whatis.md) kimlik doğrulamasını destekler. İki ortak kimlik doğrulama seçeneği şunlardır:
 
* **Hizmet sorumlusu kimlik doğrulaması** : bir hizmetin kimliğini doğrulamak için kullanılır (örneğin, Web Apps, işlev uygulamaları, Logic Apps, API ve mikro hizmetler). Bu kimlik doğrulama yöntemini yaygın olarak kullanan uygulamalar, Daemon Hizmetleri, orta katman hizmetleri veya zamanlanmış işleri çalıştıran uygulamalardır. Örneğin, Web uygulamaları için her zaman bir hizmet sorumlusu ile Media Services bağlanan bir orta katman olmalıdır.
* **Kullanıcı kimlik doğrulaması** -Media Services kaynaklarla etkileşim kurmak üzere uygulamayı kullanan bir kişinin kimliğini doğrulamak için kullanılır. Etkileşimli uygulama öncelikle kullanıcıdan kullanıcının kimlik bilgilerini istemelidir. Bu örnek, yetkili kullanıcılar tarafından kodlama işlerini veya canlı akışı izlemek için kullanılan bir yönetim konsolu uygulamasıdır.

Media Services API 'SI, REST API isteklerini yapan kullanıcı veya uygulamanın Media Services hesap kaynağına erişmesini ve **katkıda** bulunan veya **sahip** rolü kullanmasını gerektirir. API 'ye **okuyucu** rolüyle erişilebilir, ancak yalnızca **Get** veya **list** işlemleri kullanılabilir. Daha fazla bilgi için bkz. [Media Services hesapları Için rol tabanlı erişim denetimi](rbac-overview.md).

Hizmet sorumlusu oluşturmak yerine, Azure Resource Manager aracılığıyla Media Services API 'sine erişmek için Azure kaynakları için Yönetilen kimlikler kullanmayı göz önünde bulundurun. Azure kaynakları için Yönetilen kimlikler hakkında daha fazla bilgi edinmek için bkz. [Azure kaynakları için Yönetilen kimlikler](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Azure AD hizmet sorumlusu 

Bir Azure AD uygulaması ve hizmet sorumlusu oluşturuyorsanız, uygulamanın kendi kiracısında olması gerekir. Uygulamayı oluşturduktan sonra, uygulamaya **katkıda** bulunan veya **sahip** rolü Media Services hesaba erişim izni verin. 

Azure AD uygulaması oluşturma izinlerine sahip olup olmadığından emin değilseniz, bkz. [gerekli izinler](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Aşağıdaki şekilde, sayılar isteklerin akışını kronolojik sırada temsil eder:

![Orta katman uygulamalar](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Orta katman uygulama, aşağıdaki parametrelere sahip bir Azure AD erişim belirteci ister:  

   * Azure AD kiracı uç noktası.
   * Kaynak URI 'sini Media Services.
   * REST Media Services için kaynak URI 'SI.
   * Azure AD uygulama değerleri: istemci KIMLIĞI ve istemci parolası.
   
   Gerekli tüm değerleri almak için bkz [. Azure CLI Ile erişim Azure Media Services API 'si](access-api-cli-how-to.md)

2. Azure AD erişim belirteci Orta katmana gönderilir.
4. Orta katman Azure AD belirteci ile Azure Media REST API isteği gönderir.
5. Orta katman Media Services verileri geri alır.

### <a name="samples"></a>Örnekler

Azure AD hizmet sorumlusu ile nasıl bağlanabileceklerini gösteren aşağıdaki örneklere bakın:

* [REST ile bağlan](media-rest-apis-with-postman.md)  
* [Java ile bağlanma](configure-connect-java-howto.md)
* [.NET ile bağlanma](configure-connect-dotnet-howto.md)
* [Node.js ile bağlanma](configure-connect-nodejs-howto.md)
* [Python ile bağlanma](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Adlandırma kuralları

Azure Media Services v3 kaynaklarının adları (Varlıklar, İşler, Dönüşümler gibi), Azure Resource Manager adlandırma kısıtlamalarına tabidir. Azure Resource Manager uyarınca kaynak adları her zaman benzersizdir. Bu nedenle kaynaklarınızda benzersiz tanıtıcı dizeleri (GUID gibi) kullanabilirsiniz. 

Media Services kaynak adları şu karakterleri içeremez: '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', tek tırnak karakteri veya kontrol karakterleri. Diğer tüm karakterlere izin verilir. Bir kaynağın adı en fazla 260 karakter olabilir. 

Azure Resource Manager adlandırma kuralları hakkında daha fazla bilgi için bkz. [Adlandırma gereksinimleri](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) ve [Adlandırma kuralları](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="names-of-filesblobs-within-an-asset"></a>Bir varlık içindeki dosyaların/Blobların adları

Bir varlık içindeki dosyaların/Blobların adları, hem [BLOB adı gereksinimlerini](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) hem de [NTFS ad gereksinimlerini](https://docs.microsoft.com/windows/win32/fileio/naming-a-file)izlemelidir. Bu gereksinimlerin nedeni, dosyaların işlenmek üzere blob depolamadan yerel bir NTFS diskine kopyalanmasını sağlayabilir.

## <a name="long-running-operations"></a>Uzun süre çalışan işlemler

Azure Media Services [Swagger dosyalarında](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) `x-ms-long-running-operation` işaretli işlemler uzun süren işlemlerdir. 

Zaman uyumsuz Azure işlemlerini izlemeye ilişkin ayrıntılar için bkz. [Async Operations](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Media Services, aşağıdaki uzun süren işlemlere sahiptir:

* [Canlı olaylar oluşturma](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Canlı olayları Güncelleştir](https://docs.microsoft.com/rest/api/media/liveevents/update)
* [Canlı etkinliği sil](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Canlı etkinliği Başlat](https://docs.microsoft.com/rest/api/media/liveevents/start)
* [LiveEvent 'i durdur](https://docs.microsoft.com/rest/api/media/liveevents/stop)

  Olayı durdururken ilişkili tüm canlı çıkışları silmek için `removeOutputsOnStop` parametresini kullanın.  
* [LiveEvent 'i Sıfırla](https://docs.microsoft.com/rest/api/media/liveevents/reset)
* [LiveOutput oluştur](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [LiveOutput silme](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Streammingendpoint oluşturma](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)
* [Streammingendpoint 'i Güncelleştir](https://docs.microsoft.com/rest/api/media/streamingendpoints/update)
* [Streammingendpoint 'i Sil](https://docs.microsoft.com/rest/api/media/streamingendpoints/delete)
* [Streammingendpoint 'i Başlat](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* [Streammingendpoint 'i durdur](https://docs.microsoft.com/rest/api/media/streamingendpoints/stop)
* [Streammingendpoint ölçeklendirin](https://docs.microsoft.com/rest/api/media/streamingendpoints/scale)

Uzun bir işlemin başarıyla gönderilmesi için bir ' 202 kabul edildi ' ve döndürülen işlem KIMLIĞINI kullanarak işlem tamamlamayı yoklamalıdır.

[Zaman uyumsuz Azure işlemlerini izle](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) makalesinde, yanıtta döndürülen değerler aracılığıyla zaman uyumsuz Azure işlemlerinin durumunun nasıl izleneceği ayrıntılı olarak açıklanmaktadır.

Belirli bir canlı olay veya ilişkili canlı çıkışları için yalnızca uzun süreli bir işlem desteklenir. Başlatıldıktan sonra, uzun süre çalışan bir işlem, aynı LiveEvent veya ilgili canlı çıkışlar üzerinde sonraki uzun süreli bir işlem başlatmadan önce tamamlanmalıdır. Birden çok canlı çıkışı olan canlı olaylar için, başka bir canlı çıkışta uzun süre çalışan bir işlemi tetiklemeden önce, bir canlı çıkışta uzun süre çalışan bir işlemin tamamlanmasını beklemiş olmanız gerekir. 

## <a name="sdks"></a>SDK’lar

> [!NOTE]
> Azure Media Services v3 SDK 'larının iş parçacığı açısından güvenli olması garanti edilmez. Çok iş parçacıklı bir uygulama geliştirirken, istemciyi korumak için kendi iş parçacığı eşitleme mantığınızı eklemeniz veya iş parçacığı başına yeni bir Azudüzeltici Istemci nesnesi kullanmanız gerekir. Ayrıca, kodunuzun istemciye (.NET 'teki bir HttpClient örneği gibi) sağlanan isteğe bağlı nesneler tarafından sunulan çoklu iş parçacığı oluşturma sorunlarından de dikkatli olmanız gerekir.

|SDK|Başvuru|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[.NET başvurusu](https://aka.ms/ams-v3-dotnet-ref)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Java başvurusu](https://aka.ms/ams-v3-java-ref)|
|[Python SDK'sı](https://aka.ms/ams-v3-python-sdk)|[Python başvurusu](https://aka.ms/ams-v3-python-ref)|
|[Node.js SDK’sı](https://aka.ms/ams-v3-nodejs-sdk) |[Node.js başvurusu](/javascript/api/overview/azure/mediaservices/management)| 
|[Go SDK'sı](https://aka.ms/ams-v3-go-sdk) |[Go başvurusu](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Ayrıca bkz.

- [Media Service olaylarını içeren EventGrid .NET SDK 'Sı](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Media Services olaylarının tanımları](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Azure Media Services Gezgini

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (amo), Media Services hakkında bilgi edinmek isteyen Windows müşterilerine sunulan bir araçtır. AMO, Media Services ile karşıyaC# yükleme, indirme, kodlama, akış ve canlı içerik akışı yapan bir WinForms/uygulamadır. AMO Aracı, herhangi bir kod yazmadan Media Services test etmek isteyen istemcilere yöneliktir. AMI kodu, Media Services geliştirmek isteyen müşteriler için bir kaynak olarak sağlanır.

AMO, bir açık kaynak projem, topluluk tarafından destek sağlanır (https://github.com/Azure/Azure-Media-Services-Explorer/issues), sorunlar bildirilebilir. Bu proje [Microsoft Açık Kaynak Davranış Kuralları](https://opensource.microsoft.com/codeofconduct/)’nı benimsemiştir. Daha fazla bilgi için bkz. [kullanım KURALLARı SSS](https://opensource.microsoft.com/codeofconduct/faq/) veya ek soru ya da açıklamalarla iletişim opencode@microsoft.com.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Media Services varlıkların filtrelenmesi, sıralanması, sayfalama

Bkz. [filtreleme, sıralama, Azure Media Services varlıkların sayfalaması](entities-overview.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Soru sorun, geri bildirimde bulunun, güncelleştirmeleri al

Soru sormak, geri bildirimde bulunmak ve Media Services hakkında güncelleştirmeler almak için [Azure Media Services Community](media-services-community.md) makalesine göz atın.

## <a name="see-also"></a>Ayrıca bkz.

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Sonraki adımlar

* [Java ile Media Services bağlanma](configure-connect-java-howto.md)
* [.NET ile Media Services bağlanma](configure-connect-dotnet-howto.md)
* [Node. js ile Media Services bağlanma](configure-connect-nodejs-howto.md)
* [Python ile Media Services bağlanma](configure-connect-python-howto.md)
