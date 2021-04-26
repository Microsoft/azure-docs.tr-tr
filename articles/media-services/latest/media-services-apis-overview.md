---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>Zorunlu alanlar. Aka.ms/skyeye/meta hakkında daha fazla bilgi için bkz..
title: v3 API 'Leri ile Geliştirme: Azure Media Services açıklaması: Media Services v3 ile geliştirme sırasında varlıklar ve API 'Ler için uygulanan kurallar hakkında bilgi edinin. Hizmetler: Media-Services belgetationcenter: ' ' Yazar: ınridatmicrosoft Manager: femila Düzenleyicisi: ' '

MS. Service: Media-Services MS. Workload: MS. Topic: kavramsal MS. Date: 10/23/2020 MS. Author: inhenkel MS. Custom: seodec18

---

# <a name="develop-with-media-services-v3-apis"></a>Media Services v3 API’leri ile geliştirme

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Bir geliştirici olarak, özel medya iş akışlarını kolayca oluşturmak, yönetmek ve korumak için REST API ile etkileşime girebilmeniz için Media Services [REST API](/rest/api/media/) veya istemci kitaplıklarını kullanabilirsiniz. [Media Services v3](https://aka.ms/ams-v3-rest-sdk) API 'Si, openapı belirtimini temel alır (önceki adı Swagger olarak bilinir).

Bu makalede, Media Services v3 ile geliştirirken varlıklar ve API 'Ler için uygulanan kurallar açıklanmaktadır.

## <a name="accessing-the-azure-media-services-api"></a>Azure Media Services API'sine erişme

Media Services kaynaklarına ve Media Services API'sine erişim yetkisi almak için önce kimliğinizi doğrulamanız gerekir. Media Services, [Azure Active Directory (Azure AD) tabanlı](../../active-directory/fundamentals/active-directory-whatis.md) kimlik doğrulamasını destekler. İki yaygın kimlik doğrulaması seçeneği vardır:
 
* **Hizmet sorumlusu kimlik doğrulaması**: Hizmetin kimliğini doğrulamak için kullanılır (örneğin: web uygulamaları, işlev uygulamaları, API ve mikro hizmetler). Bu kimlik doğrulama yöntemini yaygın olarak kullanan uygulamalar deamon hizmetleri, orta katman hizmetleri veya zamanlanmış işler çalıştıran uygulamalardır. Örneğin, Web uygulamaları için her zaman bir hizmet sorumlusu ile Media Services bağlanan bir orta katman olmalıdır.
* **Kullanıcı kimlik doğrulaması**: Uygulamayı Media Services kaynaklarıyla etkileşim kurmak amacıyla kullanan bir kişinin kimliğini doğrulamak için kullanılır. Etkileşimli uygulamanın önce kullanıcıdan kimlik bilgilerini istemesi gerekir. Yetkili kullanıcılar tarafından kodlama işlerini veya canlı akışı izlemek için kullanılan yönetim konsolu uygulaması bunun bir örneğidir.

Media Services API'si, REST API isteklerinde bulunan kullanıcının veya uygulamanın Media Services hesabı kaynağına erişimi olmasını ve **Katkıda Bulunan** veya **Sahip** rolü kullanmasını gerektirir. **Okuyucu** rolüyle API'ye erişilebilir ama yalnızca **Get** veya **List** işlemleri kullanılabilir.Daha fazla bilgi için bkz. [Media Services hesapları Için Azure rol tabanlı erişim denetimi (Azure RBAC)](security-rbac-concept.md).

Hizmet sorumlusu oluşturmak yerine, Azure kaynakları için yönetilen kimlikler kullanarak Azure Resource Manager aracılığıyla Media Services API'sine erişmeyi göz önünde bulundurun. Azure kaynakları için yönetilen kimlikler hakkında daha fazla bilgi edinmek için bkz. [Azure kaynakları için yönetilen kimlikler nedir?](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Azure AD hizmet sorumlusu

Azure AD uygulaması ve hizmet sorumlusu aynı kiracıda olmalıdır. Uygulamayı oluşturduktan sonra, uygulamaya **katkıda** bulunan veya **sahip** rolü Media Services hesaba erişim izni verin.

Azure AD uygulaması oluşturma izinlerinizin olup olmadığından emin değilseniz, bkz. [gerekli izinler](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Aşağıdaki şekilde, sayılar isteklerin akışını kronolojik sırada temsil eder:

![Web API 'sinden AAD ile orta katmanlı uygulama kimlik doğrulaması](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Orta katman uygulama, aşağıdaki parametrelere sahip bir Azure AD erişim belirteci ister:  

   * Azure AD kiracı uç noktası.
   * Kaynak URI 'sini Media Services.
   * REST Media Services için kaynak URI 'SI.
   * Azure AD uygulama değerleri: istemci KIMLIĞI ve istemci parolası.

   Gerekli tüm değerleri almak için bkz. [erişim Azure Media Services API 'si](./access-api-howto.md).

2. Azure AD erişim belirteci Orta katmana gönderilir.
4. Orta katman Azure AD belirteci ile Azure Media REST API isteği gönderir.
5. Orta katman Media Services verileri geri alır.

### <a name="samples"></a>Örnekler

Azure AD hizmet sorumlusu ile nasıl bağlanabileceklerini gösteren aşağıdaki örneklere bakın:

* [REST ile bağlan](setup-postman-rest-how-to.md)  
* [Java ile bağlanma](configure-connect-java-howto.md)
* [.NET ile bağlanma](configure-connect-dotnet-howto.md)
* [Node.js ile bağlanma](configure-connect-nodejs-howto.md)
* [Python ile bağlanma](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Adlandırma kuralları

Azure Media Services v3 kaynaklarının adları (Varlıklar, İşler, Dönüşümler gibi), Azure Resource Manager adlandırma kısıtlamalarına tabidir. Azure Resource Manager uyarınca kaynak adları her zaman benzersizdir. Bu nedenle kaynaklarınızda benzersiz tanıtıcı dizeleri (GUID gibi) kullanabilirsiniz.

Media Services kaynak adları şunları içeremez: ' < ', ' > ', '% ', ' & ', ': ', ' &#92; ', '? ', '/', ' * ', ' + ', '. ', tek tırnak karakteri veya herhangi bir denetim karakteri. Diğer tüm karakterlere izin verilir. Bir kaynağın adı en fazla 260 karakter olabilir.

Azure Resource Manager adlandırma hakkında daha fazla bilgi için bkz. [adlandırma gereksinimleri](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) ve [adlandırma kuralları](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="names-of-filesblobs-within-an-asset"></a>Bir varlık içindeki dosyaların/Blobların adları

Bir varlık içindeki dosyaların/Blobların adları, hem [BLOB adı gereksinimlerini](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) hem de [NTFS ad gereksinimlerini](/windows/win32/fileio/naming-a-file)izlemelidir. Bu gereksinimlerin nedeni, dosyaların işlenmek üzere blob depolamadan yerel bir NTFS diskine kopyalanmasını sağlayabilir.

## <a name="long-running-operations"></a>Uzun süre çalışan işlemler

`x-ms-long-running-operation`Azure Media Services [Swagger dosyalarında](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) ile işaretlenen işlemler uzun süren işlemlerdir. 

Zaman uyumsuz Azure işlemlerini izlemeye ilişkin ayrıntılar için bkz. [Async Operations](../../azure-resource-manager/management/async-operations.md).

Media Services, aşağıdaki uzun süren işlemlere sahiptir:

* [Canlı olaylar oluşturma](/rest/api/media/liveevents/create)
* [Canlı olayları Güncelleştir](/rest/api/media/liveevents/update)
* [Canlı etkinliği sil](/rest/api/media/liveevents/delete)
* [Canlı etkinliği Başlat](/rest/api/media/liveevents/start)
* [LiveEvent 'i durdur](/rest/api/media/liveevents/stop)

  `removeOutputsOnStop`Olayı durdururken ilişkili tüm canlı çıkışları silmek için parametresini kullanın.  
* [LiveEvent 'i Sıfırla](/rest/api/media/liveevents/reset)
* [LiveOutput oluştur](/rest/api/media/liveevents/create)
* [LiveOutput silme](/rest/api/media/liveevents/delete)
* [Streammingendpoint oluşturma](/rest/api/media/streamingendpoints/create)
* [Streammingendpoint 'i Güncelleştir](/rest/api/media/streamingendpoints/update)
* [Streammingendpoint 'i Sil](/rest/api/media/streamingendpoints/delete)
* [Streammingendpoint 'i Başlat](/rest/api/media/streamingendpoints/start)
* [Streammingendpoint 'i durdur](/rest/api/media/streamingendpoints/stop)
* [Streammingendpoint ölçeklendirin](/rest/api/media/streamingendpoints/scale)

Uzun bir işlemin başarılı gönderimi sırasında, ' 201 oluşturuldu ' alırsınız ve döndürülen işlem KIMLIĞINI kullanarak işlem tamamlamayı yoklamanız gerekir.

[Zaman uyumsuz Azure işlemlerini izle](../../azure-resource-manager/management/async-operations.md) makalesinde, yanıtta döndürülen değerler aracılığıyla zaman uyumsuz Azure işlemlerinin durumunun nasıl izleneceği ayrıntılı olarak açıklanmaktadır.

Belirli bir canlı olay veya ilişkili canlı çıkışları için yalnızca uzun süreli bir işlem desteklenir. Başlatıldıktan sonra, uzun süre çalışan bir işlem, aynı LiveEvent veya ilgili canlı çıkışlar üzerinde sonraki uzun süreli bir işlem başlatmadan önce tamamlanmalıdır. Birden çok canlı çıkışı olan canlı olaylar için, başka bir canlı çıkışta uzun süre çalışan bir işlemi tetiklemeden önce, bir canlı çıkışta uzun süre çalışan bir işlemin tamamlanmasını beklemiş olmanız gerekir.

## <a name="sdks"></a>SDK

> [!NOTE]
> Azure Media Services v3 SDK 'larının iş parçacığı açısından güvenli olduğu garanti edilmez. Çok iş parçacıklı bir uygulama geliştirirken, istemciyi korumak için kendi iş parçacığı eşitleme mantığınızı eklemeniz veya iş parçacığı başına yeni bir Azudüzeltici Istemci nesnesi kullanmanız gerekir. Ayrıca, kodunuzun istemciye (.NET 'teki bir HttpClient örneği gibi) sağlanan isteğe bağlı nesneler tarafından sunulan çoklu iş parçacığı oluşturma sorunlarından de dikkatli olmanız gerekir.

|SDK|Başvuru|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[.NET başvurusu](/dotnet/api/overview/azure/mediaservices/management)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Java başvurusu](/java/api/overview/azure/mediaservices/management)|
|[Python SDK'sı](https://aka.ms/ams-v3-python-sdk)|[Python başvurusu](/python/api/overview/azure/mediaservices/management)|
|[Node.js SDK’sı](https://aka.ms/ams-v3-nodejs-sdk) |[Node.js başvurusu](/javascript/api/overview/azure/mediaservices/management)| 
|[Go SDK'sı](https://aka.ms/ams-v3-go-sdk) |[Go başvurusu](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Ayrıca bkz.

- [Media Service olaylarını içeren EventGrid .NET SDK 'Sı](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Media Services olaylarının tanımları](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Azure Media Services Gezgini

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (amo), Media Services hakkında bilgi edinmek isteyen Windows müşterilerine sunulan bir araçtır. AMO, Media Services ile karşıya yükleme, indirme, kodlama, akış ve canlı içerik akışı yapan bir WinForms/C# uygulamasıdır. AMO Aracı, herhangi bir kod yazmadan Media Services test etmek isteyen istemcilere yöneliktir. AMI kodu, Media Services geliştirmek isteyen müşteriler için bir kaynak olarak sağlanır.

AMO bir açık kaynak projem, topluluk tarafından destek sağlanır (sorunlar olarak bildirilebilir https://github.com/Azure/Azure-Media-Services-Explorer/issues) . Bu proje [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) (Microsoft Açık Kaynak Kullanım Kuralları) belgesinde listelenen kurallara uygundur. Daha fazla bilgi için bkz. [kullanım KURALLARı SSS](https://opensource.microsoft.com/codeofconduct/faq/) veya opencode@microsoft.com diğer soru veya açıklamalarla iletişim kurma.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Media Services varlıkların filtrelenmesi, sıralanması, sayfalama

Bkz. [Azure Media Services varlıkların filtrelenmesi, sıralanması, sayfalama](filter-order-page-entitites-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Soru sorun, geri bildirimde bulunun, güncelleştirmeleri al

Soru sormak, geri bildirimde bulunmak ve Media Services hakkında güncelleştirmeler almak için [Azure Media Services Community](media-services-community.md) makalesine göz atın.

## <a name="see-also"></a>Ayrıca bkz.

Gerekli tüm değerleri almak için bkz. [erişim Azure Media Services API 'si](./access-api-howto.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Java ile Media Services bağlanma](configure-connect-java-howto.md)
* [.NET ile Media Services bağlanma](configure-connect-dotnet-howto.md)
* [Node.jsMedia Services bağlanma ](configure-connect-nodejs-howto.md)
* [Python ile Media Services bağlanma](configure-connect-python-howto.md)