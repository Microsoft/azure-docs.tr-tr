---
title: REST API hata kodları-Azure Machine Learning Studio (klasik) | Microsoft Docs
description: Bu hata kodları Azure Machine Learning Web hizmetindeki bir işlem tarafından döndürülebilir.
keywords: ''
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.subservice: studio
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: 15e73740f5d932d0fa4cfb9a56c9fe7b155555cc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468165"
---
# <a name="azure-machine-learning-studio-classic-rest-api-error-codes"></a>Azure Machine Learning Studio (klasik) REST API hata kodları
 
Aşağıdaki hata kodları Azure Machine Learning Studio (klasik) Web hizmetindeki bir işlem tarafından döndürülebilir.
 
## <a name="badargument-http-status-code-400"></a>BadArgument (HTTP durum kodu 400)
 
Geçersiz bağımsız değişken belirtildi.
 
Bu hata sınıfı, bir yerde belirtilen bağımsız değişkenin geçersiz olduğu anlamına gelir. Bu, Web hizmetine geçirilmiş bir kimlik bilgisi veya Azure Storage konumu olabilir. Hangi belirli bağımsız değişkenin geçersiz olduğunu tanılamak için lütfen "Ayrıntılar" bölümündeki hata "Code" alanına bakın.
 
| Hata kodu | Kullanıcı iletisi |
| ---------- |--------------|
| BadParameterValue | Sağlanan parametre değeri, parametresindeki parametre kuralını karşılamıyor |
| Rozsubscriptionıd | Puan vermek için kullanılan abonelik kimliği, kaynakta mevcut değil |
| BadVersionCall | API çağrısı sırasında geçersiz sürüm parametresi geçirildi: {0}. Doğru sürümü geçirmek için API yardım sayfasını denetleyin ve yeniden deneyin. |
| Batchjobınputsnotbelirtildi | Şu gerekli giriş (ler) istekle birlikte belirtilmedi: {0}. Lütfen tüm giriş verilerinin belirtildiğinden emin olun ve yeniden deneyin. |
| Batchjobınputstoomanybelirtildi | İstek, hizmette tanımlanenden daha fazla girdi belirtti. Kabul edilen giriş (lar) listesi: {0}. Lütfen tüm giriş verilerinin doğru belirtildiğinden emin olun ve yeniden deneyin. |
| BlobNameTooLong | Tanılama çıktısı için belirtilen Azure Blob depolama yolu çok uzun: {0}. Yolu kısaltın ve yeniden deneyin. |
| BlobNotFound | Belirtilen Azure Blob 'a erişilemiyor-{0}.  Azure hata iletisi: {1}. |
| ContainerIsEmpty | Azure depolama kapsayıcısı adı sağlanmadı. Geçerli bir kapsayıcı adı girip yeniden deneyin. |
| ContainerSegmentInvalid | Geçersiz kapsayıcı adı. Geçerli bir kapsayıcı adı girip yeniden deneyin. |
| ContainerValidationFailed | Blob kapsayıcısı doğrulaması şu hatayla başarısız oldu: {0}. |
| DataTypeNotSupported | Desteklenmeyen veri türü belirtildi. Geçerli veri türleri sağlayın ve yeniden deneyin. |
| Duplicateınputınbatchcall | Toplu istek geçersiz. Aynı anda hem tek hem de birden çok giriş belirtilemez. Bu öğelerden birini istekten kaldırın ve yeniden deneyin. |
| Expiryıtimeınthepast | Belirtilen sona erme saati geçmişte: {0}. UTC olarak gelecekteki bir süre sonu belirtin ve yeniden deneyin. Süresi dolmasın, süre sonu süresini NULL olarak ayarlayın. |
| Incompletesettings | Tanılama ayarları eksik. |
| Inputblobrelatıvelocationgeçersiz | Azure Depolama Blobu adı sağlanmamış. Geçerli bir blob adı girip yeniden deneyin. |
| Invalidblob | Blob için geçersiz blob belirtimi: {0}. Bağlantı dizesinin/göreli yolun veya SAS belirteci belirtiminin doğru olduğundan emin olun ve yeniden deneyin. |
| Invalidblobconnectionstring | Giriş/çıkış Bloblarından biri için belirtilen bağlantı dizesi geçersiz: {0}. Lütfen bunu düzeltip yeniden deneyin. |
| Invalidblobextension | Blob başvurusu: {0} geçersiz veya eksik bir dosya uzantısına sahip. Bu çıkış türü için desteklenen dosya uzantıları şunlardır: "{1}". |
| Invalidınputnames | İstekte geçersiz hizmet girdisi adları belirtildi: {0}. Lütfen giriş verilerini doğru hizmet girdilerine eşleyin ve yeniden deneyin. |
| InvalidOutputOverrideName | Geçersiz çıkış geçersiz kılma adı: {0}. Hizmetin bu ada sahip bir çıkış düğümü yok. Lütfen geçersiz kılmak için doğru bir çıkış düğümü adı geçirin (büyük/küçük harfe duyarlılık geçerlidir). |
| InvalidQueryParameter | '{0}' sorgu parametresi geçersiz. {1} |
| Missingınputblobınformation | Eksik Azure Storage blob bilgileri. Geçerli bir bağlantı dizesi ve göreli yol veya URI sağlayın ve yeniden deneyin. |
| Missingjobıd | İş kimliği belirtilmedi. Bir iş ilk kez gönderildiğinde bir iş kimliği döndürülür. İş kimliğinin doğru olduğundan emin olun ve yeniden deneyin. |
| MissingKeys | Anahtar sağlanmadı veya birincil veya Ikincil anahtardan biri sağlanmadı. |
| MissingModelPackage | Hiçbir model paket kimliği veya model paketi sağlanmamış. Geçerli bir model paket kimliği veya model paketi sağlayın ve yeniden deneyin. |
| MissingOutputOverrideSpecification | İsteğin çıkış geçersiz kılma {0}için blob belirtimi eksik. Lütfen istekle birlikte geçerli bir blob konumu belirtin veya hiçbir konum geçersiz kılma istenmiyorsa çıkış belirtimini kaldırın. |
| Missingrequestınput | Web hizmeti bir girdi bekliyor, ancak giriş sağlanmadı. Modeldeki yayımlanmış giriş bağlantı noktalarına bağlı olarak geçerli girdilerin sağlandığından emin olun ve yeniden deneyin. |
| MissingRequiredGlobalParameters | Gerekli tüm Web hizmeti parametreleri sağlanmadı. Modüller için beklenen parametrelerin (ler) doğru olduğundan emin olun ve yeniden deneyin. |
| MissingRequiredOutputOverrides | Şifrelenmiş bir hizmet uç noktası çağrılırken, tüm hizmet çıkışları için çıkış geçersiz kılmalarının geçmesi zorunludur. Şu anda bu çıktılar için geçersiz kılmalar eksik: {0} |
| Missingwebservicegroupıd | Hiçbir Web hizmeti grup kimliği sağlanmamış. Geçerli bir Web hizmeti grubu kimliği sağlayın ve yeniden deneyin. |
| Missingwebserviceıd | Web hizmeti kimliği belirtilmedi. Geçerli bir Web hizmeti kimliği sağlayın ve yeniden deneyin. |
| MissingWebServicePackage | Hiçbir Web hizmeti paketi sağlanmamış. Geçerli bir Web hizmeti paketi sağlayın ve yeniden deneyin. |
| Missingworkspace kimliği | Çalışma alanı kimliği belirtilmedi. Geçerli bir çalışma alanı kimliği sağlayın ve yeniden deneyin. |
| Modelconfigurationgeçersiz | Model paketinde geçersiz model yapılandırması. Model yapılandırmasında çıkış uç noktası tanımı, STD hata uç noktası ve std Out uç noktasını içerdiğinden emin olun ve yeniden deneyin. |
| Modelpackageıdgeçersiz | Geçersiz model paket kimliği. model paket kimliğinin doğru olduğundan emin olun ve yeniden deneyin. |
| Requestbodygeçersiz | İstek gövdesinin serisi kaldırılırken istek gövdesi belirtilmedi veya hata oluştu. |
| RequestIsEmpty | İstek sağlanmamış. Geçerli bir istek sağlayın ve yeniden deneyin. |
| UnexpectedParameter | Beklenmeyen parametreler belirtildi. Tüm parametre adlarının doğru yazıldığından, yalnızca beklenen parametrelerin geçirildiğinden emin olun ve yeniden deneyin. |
| UnknownError | Bilinmeyen hata. |
| Userparametergeçersiz | {0} |
| Webserviceconcurrentrequestgereksinimgeçersiz | {0} Web hizmeti için eşzamanlı istek gereksinimleri değiştirilemiyor. |
| Webserviceidgeçersiz | Geçersiz Web hizmeti kimliği belirtildi. Web hizmeti kimliği geçerli bir GUID olmalıdır. |
| WebServiceTooManyConcurrentRequestRequirement | Eşzamanlı istek gereksinimi {0}fazlasına ayarlanamaz. |
| Webservicetypegeçersiz | Geçersiz Web hizmeti türü belirtildi. Geçerli Web hizmeti türünün doğru olduğundan emin olun ve yeniden deneyin. Geçerli Web hizmeti türleri: {0}. |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (HTTP durum kodu 400)
 
Geçersiz Kullanıcı bağımsız değişkeni belirtildi.
 
| Hata kodu | Kullanıcı iletisi |
| ---------- |--------------|
| Inputmismatcherror | Giriş verileri, giriş bağlantı noktası şemasıyla eşleşmiyor. |
| Inputparseerror | Giriş vektörünü ayrıştırma başarısız oldu.  Giriş vektörünün doğru sayıda sütun ve veri türüne sahip olduğunu doğrulayın.  Ek ayrıntılar: {0}. |
| MissingRequiredGlobalParameters | Web hizmeti tarafından beklenen parametre (ler) eksik. Web hizmeti tarafından beklenen tüm gerekli parametrelerin doğru olduğundan emin olun ve yeniden deneyin. |
| UnexpectedParameter | Yalnızca Web hizmeti tarafından beklenen gerekli parametrelerin geçirildiğinden emin olun ve yeniden deneyin. |
| Userparametergeçersiz | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InvalidOperation (HTTP durum kodu 400)
 
Geçerli bağlamda istek geçersiz.
 
| Hata kodu | Kullanıcı iletisi |
| ---------- |--------------|
| CannotStartJob | İş {0} durumunda olduğundan başlatılamıyor. |
| Incompatiblemodel | Model, istek sürümüyle uyumlu değil. İstek sürümü yalnızca tek DataTable çıkış modellerini destekler. |
| Multipleınputsnotallowed | Model birden çok girişe izin vermiyor. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (HTTP durum kodu 400)
 
Modül yürütmesi dahili bir kitaplık hatasıyla karşılaştı.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (HTTP durum kodu 400)
 
Modül yürütmesi bir hatayla karşılaştı.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (HTTP durum kodu 400)
 
Geçersiz Web hizmeti paketi. Belirtilen Web hizmeti paketinin doğru olduğundan emin olun ve yeniden deneyin.
 
| Hata kodu | Kullanıcı iletisi |
| ---------- |--------------|
| FormatError | Web hizmeti paketi hatalı biçimlendirilmiş. Ayrıntılar: {0} |
| RuntimesError | Web hizmeti paketi grafiği geçersiz. Ayrıntılar: {0} |
| Doğrulama hatası | Web hizmeti paketi grafiği geçersiz. Ayrıntılar: {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Yetkisiz (HTTP durum kodu 401)
 
İsteğin kaynağa erişim yetkisi yok.
 
| Hata kodu | Kullanıcı iletisi |
| ---------- |--------------|
| Adminrequestyetkilendirilmemiş | Yetkisiz |
| Managementrequestyetkilendirilmemiş | Yetkisiz |
| ScoreRequestUnauthorized | Belirtilen kimlik bilgileri geçersiz. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (HTTP durum kodu 404)
 
Kaynak bulunamadı.
 
| Hata kodu | Kullanıcı iletisi |
| ---------- |--------------|
| ModelPackageNotFound | Model paketi bulunamadı. Model paketi kimliğinin doğru olduğundan emin olun ve yeniden deneyin. |
| WebServiceIdNotFoundInWorkspace | Bu çalışma alanı altındaki Web hizmeti bulunamadı. Webserviceıd ve Workspace ID arasında bir uyuşmazlık var. Belirtilen Web hizmetinin çalışma alanının bir parçası olduğundan emin olun ve yeniden deneyin. |
| WebServiceNotFound | Web hizmeti bulunamadı. Web hizmeti kimliğinin doğru olduğundan emin olun ve yeniden deneyin. |
| WorkspaceNotFound | Çalışma alanı bulunamadı. Çalışma alanı kimliğinin doğru olduğundan emin olun ve yeniden deneyin. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (HTTP durum kodu 408)
 
İşlem izin verilen süre içinde tamamlanamadı.
 
| Hata kodu | Kullanıcı iletisi |
| ---------- |--------------|
| Requestiptal edildi | İstek, istemci tarafından iptal edildi. |
| ScoreRequestTimeout | Yürütme isteği zaman aşımına uğradı. |
 
## <a name="conflict-http-status-code-409"></a>Çakışma (HTTP durum kodu 409)
 
Kaynak zaten var.
 
| Hata kodu | Kullanıcı iletisi |
| ---------- |--------------|
| Modeloutputmetadatauyuşmazlığını | Geçersiz çıkış parametresi adı. Sütunları yeniden adlandırmak için meta veri Düzenleyicisi modülünü kullanmayı deneyin ve yeniden deneyin. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>Memoryquotaihla(HTTP durum kodu 413)
 
Model, kendisine atanan bellek kotasını aştı.
 
| Hata kodu | Kullanıcı iletisi |
| ---------- |--------------|
| OutOfMemoryLimit | Model, kendisi için ayrılmış olandan daha fazla bellek tüketti. Model için izin verilen en yüksek bellek {0} MB 'tır. Lütfen modelinize sorun olup olmadığını denetleyin. |
 
## <a name="internalerror-http-status-code-500"></a>InternalError (HTTP durum kodu 500)
 
Yürütme bir iç hatayla karşılaştı.
 
| Hata kodu | Kullanıcı iletisi |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| Clusterconfigblobyanlış yapılandırılmış |  |
| Containerprocessterminişlenwithsystemerror | Kapsayıcı işlemi sistem hatası ile kilitlendi |
| Containerprocessterminişlenwithunknownerror | Kapsayıcı işlemi bilinmeyen bir hatayla kilitlendi |
| ContainerValidationFailed | Blob kapsayıcısı doğrulaması şu hatayla başarısız oldu: {0}. |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| Invalidmemoryconfiguration | Invalidmemoryconfiguration, ConfigValue: {0} |
| Invalidresourcecacheconfiguration |  |
| Invalidresourcedownloadconfiguration |  |
| InvalidWebServiceResources |  |
| Missingtaskınstance | Bağımsız değişken belirtilmedi. Geçerli bağımsız değişkenlerin geçtiğini doğrulayıp yeniden deneyin. |
| Modelpackagegeçersiz |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | Bağlantı noktası kimliği ={0} desteklenmeyen bir veri türüne sahip: {1}. |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | Swagger oluşturma başarısız oldu, Ayrıntılar: {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| UnknownError |  |
| UnknownJobStatusCode | Bilinmeyen iş durum kodu {0}. |
| UnknownModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| Webservicepackagegeçersiz | Invalidwebservicepackage, Ayrıntılar: {0} |
| WorkerAuthorizationFailed |  |
| WorkerUnreachable |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>Internalerrorsystemlowonmemory (HTTP durum kodu 500)
 
Yürütme bir iç hatayla karşılaştı. Sistem belleği yetersiz. Lütfen yeniden deneyin.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (HTTP durum kodu 500)
 
Geçersiz model paketi. Belirtilen model paketinin doğru olduğundan emin olun ve yeniden deneyin.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>Webservicepackageınternalerror (HTTP durum kodu 500)
 
Geçersiz Web hizmeti paketi. Belirtilen Web paketinin doğru olduğundan emin olun ve yeniden deneyin.
 
| Hata kodu | Kullanıcı iletisi |
| ---------- |--------------|
| ModuleError | Web hizmeti paketi grafiği geçersiz. Ayrıntılar: {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>Initialingcontainers (HTTP durum kodu 503)
 
Kapsayıcılar başlatılırken istek yürütülemiyor.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceUnavailable (HTTP durum kodu 503)
 
Hizmet geçici olarak kullanılamıyor.
 
| Hata kodu | Kullanıcı iletisi |
| ---------- |--------------|
| NoMoreResources | İstek için kullanılabilir kaynak yok. |
| Istek kısıtlanıyor | {0} uç noktası için istek kısıtlandı. Uç nokta için en fazla eşzamanlılık {1}. |
| TooManyConcurrentRequests | Çok sayıda eşzamanlı istek gönderildi. |
| Toomanyhostsbeingınitialized | Aynı anda çok fazla sayıda ana bilgisayar başlatılıyor. Azaltmayı/yeniden denemeyi değerlendirin. |
| Toomanyhostsbeingınitializedpermodel | Aynı anda çok fazla sayıda ana bilgisayar başlatılıyor. Azaltmayı/yeniden denemeyi değerlendirin. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (HTTP durum kodu 504)
 
İşlem izin verilen süre içinde tamamlanamadı.
 
| Hata kodu | Kullanıcı iletisi |
| ---------- |--------------|
| BackendInitializationTimeout | Web hizmeti başlatması izin verilen süre içinde tamamlanamadı. |
| BackendScoreTimeout | Web hizmeti isteği yürütmesi izin verilen süre içinde tamamlanamadı. |
 
