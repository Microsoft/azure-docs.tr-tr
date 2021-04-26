---
title: Sorun giderme Azure Data Factory | Microsoft Docs
description: Azure Data Factory 'de dış denetim etkinliklerinin nasıl giderileceğini öğrenin.
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 12/30/2020
ms.author: abnarain
ms.openlocfilehash: 101e55188b8021040e2fd6bd573c1c6330241e72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382812"
---
# <a name="troubleshoot-azure-data-factory"></a>Azure Data Factory sorunlarını giderme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede Azure Data Factory içindeki dış denetim etkinlikleri için genel sorun giderme yöntemleri incelenmektedir.

## <a name="connector-and-copy-activity"></a>Bağlayıcı ve kopyalama etkinliği

Kopyalama etkinliğini kullanarak sorun giderme gibi bağlayıcı sorunları için bkz. [Azure Data Factory bağlayıcıları sorunlarını giderme](connector-troubleshoot-guide.md).

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code-3200"></a>Hata kodu: 3200

- **İleti**: hata 403.

- **Neden**: `The Databricks access token has expired.`

- **Öneri**: varsayılan olarak, Azure Databricks erişim belirteci 90 gün için geçerlidir. Yeni bir belirteç oluşturun ve bağlı hizmeti güncelleştirin.

### <a name="error-code-3201"></a>Hata kodu: 3201

- **İleti**: `Missing required field: settings.task.notebook_task.notebook_path.`

- **Neden**: `Bad authoring: Notebook path not specified correctly.`

- **Öneri**: Databricks etkinliğinde Not defteri yolunu belirtin.

<br/> 

- **İleti**: `Cluster... does not exist.`

- **Neden**: `Authoring error: Databricks cluster does not exist or has been deleted.`

- **Öneri**: Databricks kümesinin var olduğunu doğrulayın.

<br/> 

- **İleti**: `Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **Neden**: `Bad authoring.`

- **Öneri**: çalışma alanı adresleme şemaları için ya da `dbfs:/folder/subfolder/foo.py` Databricks dosya sisteminde (DFS) depolanan dosyalar için mutlak yollar belirtin.

<br/> 

- **İleti**: `{0} LinkedService should have domain and accessToken as required properties.`

- **Neden**: `Bad authoring.`

- **Öneri**: [bağlı hizmet tanımını](compute-linked-services.md#azure-databricks-linked-service)doğrulayın.

<br/> 

- **İleti**: `{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Neden**: `Bad authoring.`

- **Öneri**: [bağlı hizmet tanımını](compute-linked-services.md#azure-databricks-linked-service)doğrulayın.

<br/> 

- **İleti**: `Node type Standard_D16S_v3 is not supported. Supported node types: Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2.`

- **Neden**: `Bad authoring.`

- **Öneri**: hata iletisine bakın.

<br/>

### <a name="error-code-3202"></a>Hata kodu: 3202

- **İleti**: `There were already 1000 jobs created in past 3600 seconds, exceeding rate limit: 1000 job creations per 3600 seconds.`

- **Neden**: `Too many Databricks runs in an hour.`

- **Öneri**: Bu Databricks çalışma alanını kullanan tüm işlem hatlarını iş oluşturma oranı için denetleyin. İşlem hatları çok fazla Databricks çalıştırıyorsa, bazı işlem hatlarını yeni bir çalışma alanına geçirin.

<br/> 

- **İleti**: `Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Neden**: `Authoring error: No value provided for the parameter.`

- **Öneri**: JSON işlem hattını inceleyin ve baseparameters not defterindeki tüm parametrelerin boş olmayan bir değer belirttiğinden emin olun.

<br/> 

- **İleti**: `User: ` simpleusercontext {UserID =..., Name = user@company.com , OrgID =...}` is not authorized to access cluster.`

- **Neden**: erişim belirtecini oluşturan kullanıcının bağlı hizmette belirtilen Databricks kümesine erişmesine izin verilmiyor.

- **Öneri**: kullanıcının çalışma alanında gerekli izinlere sahip olduğundan emin olun.

### <a name="error-code-3203"></a>Hata kodu: 3203

- **İleti**: `The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Neden**: küme sonlandırıldı. Etkileşimli kümeler için bu sorun bir yarış durumu olabilir.

- **Öneri**: Bu hatadan kaçınmak için iş kümelerini kullanın.

### <a name="error-code-3204"></a>Hata kodu: 3204

- **İleti**: `Job execution failed.`

- **Neden**: hata iletileri beklenmeyen küme durumu veya belirli bir etkinlik gibi çeşitli sorunları gösterir. Genellikle, hata iletisi görüntülenmez.

- **Öneri**: yok

### <a name="error-code-3208"></a>Hata kodu: 3208

- **İleti**: `An error occurred while sending the request.`

- **Neden**: Databricks hizmetine ağ bağlantısı kesildi.

- **Öneri**: şirket içinde barındırılan bir tümleştirme çalışma zamanı kullanıyorsanız, tümleştirme çalışma zamanı düğümlerinden ağ bağlantısının güvenilir olduğundan emin olun. Azure Integration Runtime kullanıyorsanız, yeniden deneme genellikle işe yarar.
 
## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

Aşağıdaki tablo U-SQL için geçerlidir.
 
### <a name="error-code-2709"></a>Hata kodu: 2709

- **İleti**: `The access token is from the wrong tenant.`

- **Neden**: yanlış Azure Active Directory (Azure AD) kiracısı.

- **Öneri**: yanlış Azure Active Directory (Azure AD) kiracısı.

<br/>

- **İleti**: `We cannot accept your job at this moment. The maximum number of queued jobs for your account is 200. `

- **Neden**: Bu hata, Data Lake Analytics azaltma nedeniyle oluşur.

- **Öneri**: Data Lake Analytics gönderilen işlerin sayısını azaltın. Etkinliklerde Data Factory Tetikleyicileri ve eşzamanlılık ayarlarını değiştirin ya da Data Lake Analytics sınırları artırın.

<br/> 

- **İleti**: `This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Neden**: Bu hata, Data Lake Analytics azaltma nedeniyle oluşur.

- **Öneri**: Data Lake Analytics gönderilen işlerin sayısını azaltın. Etkinliklerde Data Factory Tetikleyicileri ve eşzamanlılık ayarlarını değiştirin ya da Data Lake Analytics sınırları artırın.

### <a name="error-code-2705"></a>Hata kodu: 2705

- **İleti**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Neden**: hizmet sorumlusu veya sertifikasının depolama alanındaki dosyaya erişimi yok.

- **Öneri**: kullanıcının Data Lake Analytics işleri için sağladığı hizmet sorumlusu veya sertifikanın hem Data Lake Analytics hesabına hem de kök klasördeki varsayılan Data Lake Storage örneğine erişimi olduğunu doğrulayın.

### <a name="error-code-2711"></a>Hata kodu: 2711

- **İleti**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Neden**: hizmet sorumlusu veya sertifikasının depolama alanındaki dosyaya erişimi yok.

- **Öneri**: kullanıcının Data Lake Analytics işleri için sağladığı hizmet sorumlusu veya sertifikanın hem Data Lake Analytics hesabına hem de kök klasördeki varsayılan Data Lake Storage örneğine erişimi olduğunu doğrulayın.

<br/> 

- **İleti**: `Cannot find the 'Azure Data Lake Store' file or folder.`

- **Neden**: U-SQL dosyasının yolu yanlış veya bağlı hizmet kimlik bilgilerinin erişimi yok.

- **Öneri**: bağlı hizmette belirtilen yolu ve kimlik bilgilerini doğrulayın.

### <a name="error-code-2704"></a>Hata kodu: 2704

- **İleti**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Neden**: hizmet sorumlusu veya sertifikasının depolama alanındaki dosyaya erişimi yok.

- **Öneri**: kullanıcının Data Lake Analytics işleri için sağladığı hizmet sorumlusu veya sertifikanın hem Data Lake Analytics hesabına hem de kök klasördeki varsayılan Data Lake Storage örneğine erişimi olduğunu doğrulayın.

### <a name="error-code-2707"></a>Hata kodu: 2707

- **İleti**: `Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and 'DataLakeAnalyticsUri'.`

- **Neden**: bağlı hizmette Data Lake Analytics hesabı yanlış.

- **Öneri**: doğru hesabın sağlandığını doğrulayın.

### <a name="error-code-2703"></a>Hata kodu: 2703

- **İleti**: `Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error Id:").`

- **Neden**: hata Data Lake Analytics.

- **Öneri**: iş Data Lake Analytics gönderildi ve betiği, her ikisi de başarısız oldu. Data Lake Analytics araştırın. Portalda Data Lake Analytics hesabına gidin ve Data Factory etkinlik çalıştırma KIMLIĞINI kullanarak işi arayın (işlem hattı çalıştırma KIMLIĞINI kullanmayın). İş, hata hakkında daha fazla bilgi sağlar ve sorun gidermenize yardımcı olur.

   Çözüm açık değilse, Data Lake Analytics destek ekibine başvurun ve hesap adınızı ve iş KIMLIĞINI içeren iş Evrensel Kaynak Bulucusu 'nı (URL) sağlayın.
 
## <a name="azure-functions"></a>Azure işlevleri

### <a name="error-code-3602"></a>Hata kodu: 3602

- **İleti**: `Invalid HttpMethod: '%method;'.`

- **Neden**: etkinlik yükünde belirtilen HttpMethod, Azure Function etkinliği tarafından desteklenmiyor.

- **Öneri**: desteklenen httpmethods ŞUNLARDıR: put, Post, Get, DELETE, OPTIONS, Head ve Trace.

### <a name="error-code-3603"></a>Hata kodu: 3603

- **İleti**: `Response Content is not a valid JObject.`

- **Neden**: çağrılan Azure işlevi YANıTTA bir JSON yükü döndürmedi. Azure Data Factory (ADF) Azure işlevi etkinliği yalnızca JSON yanıt içeriğini destekler.

- **Öneri**: Azure Işlevini, C# işlevi gibi GEÇERLI bir JSON yükü döndürecek şekilde güncelleştirme `(ActionResult)new OkObjectResult("{\"Id\":\"123\"}");`

### <a name="error-code-3606"></a>Hata kodu: 3606

- **İleti**: Azure işlevi etkinliğinde işlev anahtarı eksik.

- **Neden**: Azure işlevi etkinlik tanımı tamamlanmadı.

- **Öneri**: giriş Azure IşLEVI etkinliği JSON tanımının adlı bir özelliğe sahip olup olmadığını denetleyin `functionKey` .

### <a name="error-code-3607"></a>Hata kodu: 3607

- **İleti**: `Azure function activity missing function name.`

- **Neden**: Azure işlevi etkinlik tanımı tamamlanmadı.

- **Öneri**: giriş Azure IşLEVI etkinliği JSON tanımının adlı bir özelliğe sahip olup olmadığını denetleyin `functionName` .

### <a name="error-code-3608"></a>Hata kodu: 3608

- **İleti**: `Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **Neden**: etkinlik tanımındaki Azure işlevi ayrıntıları hatalı olabilir.

- **Öneri**: Azure işlevi ayrıntılarını düzelttikten sonra yeniden deneyin.

### <a name="error-code-3609"></a>Hata kodu: 3609

- **İleti**: `Azure function activity missing functionAppUrl.`

- **Neden**: Azure işlevi etkinlik tanımı tamamlanmadı.

- **Öneri**: giriş Azure IşLEVI etkinliği JSON tanımının adlı bir özelliğe sahip olup olmadığını denetleyin `functionAppUrl` .

### <a name="error-code-3610"></a>Hata kodu: 3610

- **İleti**: `There was an error while calling endpoint.`

- **Neden**: Işlev URL 'si yanlış olabilir.

- **Öneri**: `functionAppUrl` JSON etkinliğinde ait değerin doğru olduğundan emin olun ve yeniden deneyin.

### <a name="error-code-3611"></a>Hata kodu: 3611

- **İleti**: `Azure function activity missing Method in JSON.`

- **Neden**: Azure işlevi etkinlik tanımı tamamlanmadı.

- **Öneri**: giriş Azure IşLEVI etkinliği JSON tanımının adlı bir özelliğe sahip olup olmadığını denetleyin `method` .

### <a name="error-code-3612"></a>Hata kodu: 3612

- **İleti**: `Azure function activity missing LinkedService definition in JSON.`

- **Neden**: Azure işlevi etkinlik tanımı tamamlanmadı.

- **Öneri**: giriş Azure IşLEVI etkinliği JSON tanımında bağlı hizmet ayrıntılarına sahip olup olmadığını denetleyin.

## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code-4101"></a>Hata kodu: 4101

- **İleti**: `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Neden**: Hatalı biçim veya özelliğin tanımı eksik `%propertyName;` .

- **Öneri**: etkinliğin `%activityName;` `%propertyName;` doğru verilerle tanımlanmış özelliği olup olmadığını denetleyin.

### <a name="error-code-4110"></a>Hata kodu: 4110

- **İleti**: `AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Neden**: AzureMLExecutePipeline etkinlik tanımı tamamlanmadı.

- **Öneri**: giriş AZUREMLEXECUTEPIPELINE etkinliği JSON tanımının doğru bağlı hizmet ayrıntılarına sahip olup olmadığını denetleyin.

### <a name="error-code-4111"></a>Hata kodu: 4111

- **İleti**: `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Neden**: etkinlik tanımı yanlış.

- **Öneri**: giriş AZUREMLEXECUTEPIPELINE etkinliği JSON tanımının doğru bağlı hizmet ayrıntılarına sahip olup olmadığını denetleyin.

### <a name="error-code-4112"></a>Hata kodu: 4112

- **İleti**: `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Neden**: '% PropertyName; ' özelliğinin hatalı biçimi veya tanımı eksik.

- **Öneri**: bağlı hizmetin, doğru verilerle tanımlanmış özelliğe sahip olup olmadığını denetleyin `%propertyName;` .

### <a name="error-code-4121"></a>Hata kodu: 4121

- **İleti**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Neden**: Azure Machine Learning erişmek Için kullanılan kimlik bilgilerinin süresi doldu.

- **Öneri**: kimlik bilgisinin geçerli olduğunu doğrulayın ve yeniden deneyin.

### <a name="error-code-4122"></a>Hata kodu: 4122

- **İleti**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Neden**: Azure Machine Learning bağlantılı hizmette belirtilen kimlik bilgileri geçersiz veya işlem için izne sahip değil.

- **Öneri**: bağlı hizmette kimlik bilgisinin geçerli olduğunu ve Azure Machine Learning erişim izni olduğunu doğrulayın.

### <a name="error-code-4123"></a>Hata kodu: 4123

- **İleti**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Neden**: gibi etkinliğin özellikleri `pipelineParameters` Azure Machine Learning (ml) işlem hattı için geçersizdir.

- **Öneri**: etkinlik özellikleri değerinin, bağlı hizmette belirtilen YAYıMLANMıŞ Azure ML işlem hattının beklenen yüküyle eşleşip eşleşmediğini denetleyin.

### <a name="error-code-4124"></a>Hata kodu: 4124

- **İleti**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Neden**: Yayınlanan Azure ML ardışık düzen uç noktası yok.

- **Öneri**: bağlı hizmette belirtilen yayınlanan Azure Machine Learning ardışık düzen uç noktasının Azure Machine Learning içinde mevcut olduğunu doğrulayın.

### <a name="error-code-4125"></a>Hata kodu: 4125

- **İleti**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Neden**: Azure Machine Learning sunucu hatası var.

- **Öneri**: daha sonra yeniden deneyin. Sorun devam ederse yardım için Azure Machine Learning ekibine başvurun.

### <a name="error-code-4126"></a>Hata kodu: 4126

- **İleti**: `Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **Neden**: Azure ML işlem hattı çalıştırması başarısız oldu.

- **Öneri**: daha fazla hata günlüğü için Azure Machine Learning denetleyin ve ardından ml işlem hattını onarın.

## <a name="common"></a>Common

### <a name="error-code-2103"></a>Hata kodu: 2103

- **İleti**: `Please provide value for the required property '%propertyName;'.`

- **Neden**: özellik için gereken değer sağlanmadı.

- **Öneri**: iletiden değeri girip yeniden deneyin.

### <a name="error-code-2104"></a>Hata kodu: 2104

- **İleti**: `The type of the property '%propertyName;' is incorrect.`

- **Neden**: belirtilen özellik türü doğru değil.

- **Öneri**: özelliğin türünü düzelttikten sonra yeniden deneyin.

### <a name="error-code-2105"></a>Hata kodu: 2105

- **İleti**: `An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Neden**: özelliğin değeri geçersiz veya beklenen biçimde değil.

- **Öneri**: özelliğin belgelerine başvurun ve verilen değerin doğru biçimi ve türü içerdiğini doğrulayın.

### <a name="error-code-2106"></a>Hata kodu: 2106

- **İleti**: `The storage connection string is invalid. %errorMessage;`

- **Neden**: depolama için bağlantı dizesi geçersiz veya biçimi yanlış.

- **Öneri**: Azure Portal gidin ve depolama alanınızı bulun, sonra bağlantı dizesini kopyalayın ve bağlı hizmetinize yapıştırın ve yeniden deneyin.

### <a name="error-code-2108"></a>Hata kodu: 2108

- **İleti**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Neden**: ağ BAĞLANTıSı, DNS hatası, sunucu sertifikası doğrulama veya zaman aşımı gibi temel bir sorun nedeniyle istek başarısız oldu.

- **Öneri**: isteği doğrulamak Için Fiddler/Postman kullanın.

### <a name="error-code-2110"></a>Hata kodu: 2110

- **İleti**: `The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Neden**: etkinlikte belirtilen bağlı hizmet yanlış.

- **Öneri**: bağlı hizmet türünün etkinlik için desteklenen türlerden biri olduğunu doğrulayın. Örneğin, HDI etkinlikleri için bağlı hizmet türü HDInsight veya Hdınsightondemand olabilir.

### <a name="error-code-2111"></a>Hata kodu: 2111

- **İleti**: `The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Neden**: belirtilen özelliğin türü doğru değil.

- **Öneri**: Özellik türünü düzelttikten sonra yeniden deneyin.

### <a name="error-code-2112"></a>Hata kodu: 2112

- **İleti**: `The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Neden**: bulut türü desteklenmiyor veya endpointsuffix 'dan depolama için belirlenemedi.

- **Öneri**: depolama alanını başka bir bulutta kullanıp yeniden deneyin.

### <a name="error-code-2128"></a>Hata kodu: 2128

- **İleti**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Neden**: ağ BAĞLANTıSı, DNS hatası, sunucu sertifikası doğrulama veya zaman aşımı.

- **Öneri**: vurılmaya çalıştığınız bitiş noktasının isteklere yanıt verdiğini doğrulayın. Fiddler/Postman gibi araçları kullanabilirsiniz.

## <a name="custom"></a>Özel

Aşağıdaki tablo Azure Batch için geçerlidir.
 
### <a name="error-code-2500"></a>Hata kodu: 2500

- **İleti**: `Hit unexpected exception and execution failed.`

- **Neden**: `Can't launch command, or the program returned an error code.`

- **Öneri**: yürütülebilir dosyanın var olduğundan emin olun. Program başlatılmışsa *stdout.txt* ve *stderr.txt* depolama hesabına yüklendiğini doğrulayın. Hata ayıklama için kodunuzda günlükleri eklemek iyi bir uygulamadır.

### <a name="error-code-2501"></a>Hata kodu: 2501

- **İleti**: `Cannot access user batch account; please check batch account settings.`

- **Neden**: Hatalı toplu erişim anahtarı veya havuz adı.

- **Öneri**: bağlı hizmette havuz adını ve Batch erişim anahtarını doğrulayın.

### <a name="error-code-2502"></a>Hata kodu: 2502

- **İleti**: `Cannot access user storage account; please check storage account settings.`

- **Neden**: Hatalı depolama hesabı adı veya erişim anahtarı.

- **Öneri**: depolama hesabı adını ve bağlı hizmette erişim anahtarını doğrulayın.

### <a name="error-code-2504"></a>Hata kodu: 2504

- **İleti**: `Operation returned an invalid status code 'BadRequest'.`

- **Neden**: özel etkinliğin içinde çok fazla dosya vardır `folderPath` . Toplam Boyut `resourceFiles` 32.768 karakterden uzun olamaz.

- **Öneri**: gereksiz dosyaları kaldırın veya ZIP dosyalarını ayıklayın ve ayıklamak için bir unzip komutu ekleyin.
   
   Örneğin, `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2505"></a>Hata kodu: 2505

- **İleti**: `Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Neden**: özel etkinlikler yalnızca bir erişim anahtarı kullanan depolama hesaplarını destekler.

- **Öneri**: hata açıklamasına bakın.

### <a name="error-code-2507"></a>Hata kodu: 2507

- **İleti**: `The folder path does not exist or is empty: ...`

- **Neden**: belirtilen yoldaki depolama hesabında hiçbir dosya yok.

- **Öneri**: klasör yolu çalıştırmak istediğiniz yürütülebilir dosyaları içermelidir.

### <a name="error-code-2508"></a>Hata kodu: 2508

- **İleti**: `There are duplicate files in the resource folder.`

- **Neden**: aynı ada sahip birden çok dosya FolderPath 'in farklı alt klasörlerinde.

- **Öneri**: özel etkinlikler FolderPath altındaki klasör yapısını düzleştirebilir. Klasör yapısını korumanız gerekiyorsa, dosyaları zip halinde ve bir unzip komutu kullanarak Azure Batch ayıklayın.
   
   Örneğin, `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2509"></a>Hata kodu: 2509

- **İleti**: `Batch url ... is invalid; it must be in Uri format.`

- **Neden**: Batch URL 'leri şuna benzer olmalıdır: `https://mybatchaccount.eastus.batch.azure.com`

- **Öneri**: hata açıklamasına bakın.

### <a name="error-code-2510"></a>Hata kodu: 2510

- **İleti**: `An error occurred while sending the request.`

- **Neden**: Batch URL 'si geçersiz.

- **Öneri**: Batch URL 'sini doğrulayın.
 
## <a name="hdinsight"></a>HDInsight

### <a name="error-code-206"></a>Hata kodu: 206

- **İleti**: `The batch ID for Spark job is invalid. Please retry your job.`

- **Neden**: Bu hataya neden olan hizmette bir iç sorun oluştu.

- **Öneri**: Bu sorun geçici olabilir. İşinizi bir süre sonra yeniden deneyin.

### <a name="error-code-207"></a>Hata kodu: 207

- **İleti**: `Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI.`

- **Neden**: birincil depolama hesabından bölge belirlemeye çalışırken bir iç hata oluştu.

- **Öneri**: başka bir depolama alanı deneyin. 

### <a name="error-code-208"></a>Hata kodu: 208

- **İleti**: `Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again.`

- **Neden**: hizmet sorumlusu okunmaya ÇALıŞıLıRKEN veya MSI kimlik doğrulamasının örneği oluşturulurken bir iç hata oluştu.

- **Öneri**: sağlanan abonelikte HDInsight kümesi oluşturma ve yeniden deneme izinlerine sahip bir hizmet sorumlusu sağlamayı düşünün. [Manage kimliklerinin doğru şekilde ayarlandığını](../hdinsight/hdinsight-managed-identities.md)doğrulayın.

### <a name="error-code-2300"></a>Hata kodu: 2300

- **İleti**: `Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

- **Neden**: hata iletisi şuna benzer bir ileti içerir `The remote name could not be resolved.` . Belirtilen küme URI 'SI geçersiz olabilir.

- **Öneri**: kümenin silinmediğini ve belirtilen URI 'nin doğru olduğunu doğrulayın. URI 'yi bir tarayıcıda açtığınızda, ambarı Kullanıcı arabirimini görmeniz gerekir. Küme bir sanal ağda ise, URI özel URI olmalıdır. Açmak için, aynı sanal ağın parçası olan bir sanal makine (VM) kullanın.

   Daha fazla bilgi için bkz. [Apache Hadoop hizmetlerine doğrudan bağlanma](../hdinsight/hdinsight-plan-virtual-network-deployment.md#directly-connect-to-apache-hadoop-services).
 
 </br>

- **Neden**: hata iletisi öğesine benzer bir ileti içeriyorsa `A task was canceled.` iş gönderimi zaman aşımına uğradı.

- **Öneri**: sorun genel HDInsight bağlantısı ya da ağ bağlantısı olabilir. Önce HDInsight ambarı Kullanıcı arabiriminin herhangi bir tarayıcıdan kullanılabilir olduğunu doğrulayın. Ardından, kimlik bilgilerinizin hala geçerli olduğundan emin olun.
   
   Şirket içinde barındırılan tümleşik çalışma zamanı (IR) kullanıyorsanız, bu adımı şirket içinde barındırılan IR 'nin yüklü olduğu VM veya makineden gerçekleştirin. Sonra Data Factory işi yeniden göndermeyi deneyin.

   Daha fazla bilgi için, [ambarı Web Kullanıcı arabirimini](../hdinsight/hdinsight-hadoop-manage-ambari.md#ambari-web-ui)okuyun.

 </br>

- **Neden**: hata iletisi veya ile benzer bir ileti Içerdiğinde `User admin is locked out in Ambari` `Unauthorized: Ambari user name or password is incorrect` , HDInsight için kimlik bilgileri yanlış veya zaman aşımına uğradı.

- **Öneri**: kimlik bilgilerini düzeltin ve bağlı hizmeti yeniden dağıtın. Önce, herhangi bir tarayıcıda küme URI 'sini açıp oturum açmaya çalışarak, kimlik bilgilerinin HDInsight 'ta çalıştığını doğrulayın. Kimlik bilgileri çalışmazsa, bunları Azure portal sıfırlayabilirsiniz.

   ESP kümesi için, parolayı [self servis parola sıfırlama](../active-directory/user-help/active-directory-passwords-update-your-own-password.md)aracılığıyla sıfırlayın.

 </br>

- **Neden**: hata iletisi öğesine benzer bir ileti içerdiğinde `502 - Web server received an invalid response while acting as a gateway or proxy server` , bu hata HDInsight hizmeti tarafından döndürülür.

- **Öneri**: 502 hatası genellikle, ambarı sunucu işleminiz kapatıldığında oluşur. Baş düğümü yeniden başlatarak, ambarı hizmetlerini yeniden başlatabilirsiniz.

    1. SSH kullanarak HDInsight 'ta düğümlerinizin birine bağlanın.
    1. ' İ çalıştırarak etkin baş düğüm ana bilgisayarınızı tespit edin `ping headnodehost` .
    1. Active baş düğümünüz için, SSH kullanarak etkin baş düğümde bulunan bir ambarı sunucusu olarak bağlanın. 
    1. Etkin baş düğümü yeniden başlatın.

       Daha fazla bilgi için Azure HDInsight sorun giderme belgelerine bakın. Örnek:

       * [Ambari kullanıcı arabirimi 502 hatası](https://hdinsight.github.io/ambari/ambari-ui-502-error.html)
       * [Apache Spark Thrift Server için RpcTimeoutException](../hdinsight/spark/apache-spark-troubleshoot-rpctimeoutexception.md)
       * [Application Gateway 'de hatalı ağ geçidi hatalarıyla Ilgili sorunları giderme](../application-gateway/application-gateway-troubleshooting-502.md).

 </br>

- **Neden**: hata iletisi veya ile benzer bir ileti içerdiğinde, `Unable to service the submit job request as templeton service is busy with too many submit job requests` `Queue root.joblauncher already has 500 applications, cannot accept submission of application` aynı anda HDInsight 'a çok fazla iş gönderiliyor.

- **Öneri**: HDInsight 'a gönderilen eşzamanlı işlerin sayısını sınırlayın. İşlerin aynı etkinlik tarafından gönderiliyorsa Data Factory etkinlik eşzamanlılık bölümüne bakın. Yinelenen işlem hattı çalıştırmaları zaman içinde yayma için Tetikleyicileri değiştirin.

   Hatanın önereceği şekilde ayarlamak için [HDInsight belgelerine](../hdinsight/hdinsight-hadoop-templeton-webhcat-debug-errors.md) bakın `templeton.parallellism.job.submit` .

### <a name="error-code-2301"></a>Hata kodu: 2301

- **İleti**: `Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Neden**: HDInsight kümesinde veya hizmetinde sorunlar vardır.

- **Öneri**: Bu hata, ADF, çalışan işin durumunu doğrulamaya çalışırken HDInsight kümesinden bir yanıt almazsa oluşur. Bu sorun kümenin kendisinde olabilir veya HDInsight hizmeti bir kesinti olabilir.

   ' De HDInsight sorun giderme belgelerine başvurun https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guide veya daha fazla yardım için destek ekibiyle iletişime geçin.

### <a name="error-code-2302"></a>Hata kodu: 2302

- **İleti**: `Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Neden**: Iş HDI kümesine gönderildi ve burada başarısız oldu.

- **Öneri:** 

 1. Ambarı Kullanıcı arabirimini denetle:
    1. Tüm hizmetlerin hala çalıştığından emin olun.
    1. Ambarı kullanıcı arabiriminden, panonuzdaki uyarı bölümünü kontrol edin.
       1. Uyarılar ve uyarı çözümleri hakkında daha fazla bilgi için bkz. [bir kümeyi yönetme ve izleme](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
    1. YARN belleğinizin gözden geçirilmesini inceleyin. YARN belleğinizin yüksek olması durumunda, işlerinizin işlenmesi gecikebilir. Spark uygulamanızı/işinizi karşılayacak yeterli kaynağınız yoksa, kümede yeterli bellek ve çekirdek olduğundan emin olmak için kümeyi ölçeklendirin. 
 1. Örnek bir test işi çalıştırın.
    1. Aynı işi HDInsight arka ucunda çalıştırırsanız, başarılı olup olmadığını kontrol edin. Örnek çalıştırmaların örnekleri için bkz [. HDInsight 'ta bulunan MapReduce örneklerini çalıştırma](../hdinsight/hadoop/apache-hadoop-run-samples-linux.md) 
 1. İş HDInsight 'ta hala başarısız olduysa, destek sağlamak için uygulama günlüklerini ve bilgileri kontrol edin:
    1. İşin YARN 'ye gönderilip gönderilmediğini denetleyin. İş Yarn 'ye gönderilmediyse kullanın `--master yarn` .
    1. Uygulama yürütmeyi bitirdiğinde YARN uygulamasının başlangıç saatini ve bitiş saatini toplayın. Uygulama yürütmeyi tamammadıysa, başlangıç zamanı/başlatma süresini toplayın.
    1. İle uygulama günlüğünü denetleyip toplayın `yarn logs -applicationId <Insert_Your_Application_ID>` .
    1. Dizinin altındaki Yarn Kaynak Yöneticisi günlüklerini denetleyin ve toplayın `/var/log/hadoop-yarn/yarn` .
    1. Bu adımlar sorunu çözmek için yeterli değilse, destek için Azure HDInsight ekibine başvurun ve yukarıdaki günlükleri ve zaman damgalarını belirtin.

### <a name="error-code-2303"></a>Hata kodu: 2303

- **İleti**: `Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Neden**: Iş HDI kümesine gönderildi ve burada başarısız oldu.

- **Öneri:** 

 1. Ambarı Kullanıcı arabirimini denetle:
    1. Tüm hizmetlerin hala çalıştığından emin olun.
    1. Ambarı kullanıcı arabiriminden, panonuzdaki uyarı bölümünü kontrol edin.
       1. Uyarılar ve uyarı çözümleri hakkında daha fazla bilgi için bkz. [bir kümeyi yönetme ve izleme](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
    1. YARN belleğinizin gözden geçirilmesini inceleyin. YARN belleğinizin yüksek olması durumunda, işlerinizin işlenmesi gecikebilir. Spark uygulamanızı/işinizi karşılayacak yeterli kaynağınız yoksa, kümede yeterli bellek ve çekirdek olduğundan emin olmak için kümeyi ölçeklendirin. 
 1. Örnek bir test işi çalıştırın.
    1. Aynı işi HDInsight arka ucunda çalıştırırsanız, başarılı olup olmadığını kontrol edin. Örnek çalıştırmaların örnekleri için bkz [. HDInsight 'ta bulunan MapReduce örneklerini çalıştırma](../hdinsight/hadoop/apache-hadoop-run-samples-linux.md) 
 1. İş HDInsight 'ta hala başarısız olduysa, destek sağlamak için uygulama günlüklerini ve bilgileri kontrol edin:
    1. İşin YARN 'ye gönderilip gönderilmediğini denetleyin. İş Yarn 'ye gönderilmediyse kullanın `--master yarn` .
    1. Uygulama yürütmeyi bitirdiğinde YARN uygulamasının başlangıç saatini ve bitiş saatini toplayın. Uygulama yürütmeyi tamammadıysa, başlangıç zamanı/başlatma süresini toplayın.
    1. İle uygulama günlüğünü denetleyip toplayın `yarn logs -applicationId <Insert_Your_Application_ID>` .
    1. Dizinin altındaki Yarn Kaynak Yöneticisi günlüklerini denetleyin ve toplayın `/var/log/hadoop-yarn/yarn` .
    1. Bu adımlar sorunu çözmek için yeterli değilse, destek için Azure HDInsight ekibine başvurun ve yukarıdaki günlükleri ve zaman damgalarını belirtin.

### <a name="error-code-2304"></a>Hata kodu: 2304

- **İleti**: `MSI authentication is not supported on storages for HDI activities.`

- **Neden**: HDInsight (HDI) bağlı hizmeti veya HDI etkinliğinde kullanılan depolama bağlı hizmetleri, desteklenmeyen bir MSI kimlik doğrulamasıyla yapılandırılır.

- **Öneri**: HDI bağlı hizmeti veya HDI etkinliğinde kullanılan depolama hesapları için tam bağlantı dizeleri sağlayın.

### <a name="error-code-2305"></a>Hata kodu: 2305

- **İleti**: `Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Neden**: HDI kümesi için bağlantı bilgileri yanlış, belirtilen kullanıcının gerekli eylemi gerçekleştirme izni yok veya HDıNSIGHT hizmeti ADF 'den gelen isteklere yanıt veren sorunlar içeriyor.

- **Öneri**: Kullanıcı bilgilerinin doğru olduğunu ve HDI kümesinin ambarı kullanıcı ARABIRIMININ, IR 'nin yüklü olduğu VM 'deki bir tarayıcıda açılabildiğini (Şirket içinde BARıNDıRıLAN bir IR için) veya herhangi bir makineden açılabildiğini doğrulayın (Azure IR için).

### <a name="error-code-2306"></a>Hata kodu: 2306

- **İleti**: `An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Neden**: betik eylemi IÇIN belirtilen JSON geçersiz.

- **Öneri**: hata iletisi sorunu tanımaya yardımcı olmalıdır. JSON yapılandırmasını düzelttikten sonra yeniden deneyin.

   Daha fazla bilgi için [Azure HDInsight isteğe bağlı hizmetine](./compute-linked-services.md#azure-hdinsight-on-demand-linked-service) bakın.

### <a name="error-code-2310"></a>Hata kodu: 2310

- **İleti**: `Failed to submit Spark job. Error: '%message;'`

- **Neden**: ADF, LIVY API (Livy/Batch) kullanarak Spark kümesinde toplu işlem oluşturmaya çalıştı, ancak bir hata alındı.

- **Öneri**: sorunu giderecek hata iletisini izleyin. Çözümlenmeden ulaşmak için yeterli bilgi yoksa, HDI ekibine başvurun ve bunları, ADF Izlemesi sırasında etkinlik çalıştırma çıktısı sayfasında bulunan toplu iş KIMLIĞI ve iş KIMLIĞI sağlayın. Daha fazla sorun gidermek için toplu işin tam günlüğünü toplayın.

   Tam günlüğün nasıl toplanacağı hakkında daha fazla bilgi için bkz. [Batch işinin tam günlüğünü alma](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job).

### <a name="error-code-2312"></a>Hata kodu: 2312

- **İleti**: `Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Neden**: Iş HDInsight Spark kümesinde başarısız oldu.

- **Öneri**: HDInsight Spark kümesinde çalıştırma sorunlarını gidermek IÇIN, ADF izleme 'de etkinlik çalıştırma çıktısı sayfasında bulunan bağlantıları izleyin. Daha fazla yardım için HDInsight destek ekibine başvurun.

   Tam günlüğün nasıl toplanacağı hakkında daha fazla bilgi için bkz. [Batch işinin tam günlüğünü alma](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job).

### <a name="error-code-2313"></a>Hata kodu: 2313

- **İleti**: `The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Neden**: Batch, HDInsight Spark kümesinde silindi.

- **Öneri**: HDInsight Spark kümesindeki toplu işlerle ilgili sorunları giderin. Daha fazla yardım için HDInsight desteğiyle iletişim kurun. 

   Tam günlüğün nasıl toplanacağı hakkında daha fazla bilgi için bkz. [Batch işinin tam günlüğünü alma](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job)ve daha fazla yardım için HDInsight desteğiyle tam günlüğü paylaşma.

### <a name="error-code-2328"></a>Hata kodu: 2328

- **İleti**: `Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Neden**: hata iletisinde neyin yanlış olduğuna ilişkin ayrıntılar gösterilmelidir.

- **Öneri**: hata iletisi sorunu gidermeye yardımcı olmalıdır.

### <a name="error-code-2329"></a>Hata kodu: 2329

- **İleti**: `Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Neden**: hata iletisinde neyin yanlış olduğuna ilişkin ayrıntılar gösterilmelidir.

- **Öneri**: hata iletisi sorunu gidermeye yardımcı olmalıdır.

### <a name="error-code-2331"></a>Hata kodu: 2331

- **İleti**: `The file path should not be null or empty.`

- **Neden**: belirtilen dosya yolu boş.

- **Öneri**: var olan bir dosya için bir yol sağlayın.

### <a name="error-code-2340"></a>Hata kodu: 2340

- **İleti**: `HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Neden**: hdınsightondemand bağlı hizmeti, kendını barındıran IR aracılığıyla yürütmeyi desteklemiyor.

- **Öneri**: bir Azure IR seçip yeniden deneyin.

### <a name="error-code-2341"></a>Hata kodu: 2341

- **İleti**: `HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Neden**: belirtilen URL doğru biçimde değil.

- **Öneri**: küme URL 'sini düzelttikten sonra yeniden deneyin.

### <a name="error-code-2342"></a>Hata kodu: 2342

- **İleti**: `Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Neden**: belirtilen kimlik bilgileri küme için yanlış ya da bir ağ yapılandırması ya da bağlantı sorunu vardı ya da IR, kümeye bağlanırken sorun yaşıyor.

- **Öneri:** 
    1. HDInsight kümesinin ambarı Kullanıcı arabirimini bir tarayıcıda açarak kimlik bilgilerinin doğru olduğundan emin olun.
    1. Küme sanal ağ (VNet) ve şirket içinde barındırılan bir IR kullanılıyorsa, HDI URL 'SI VNET 'lerdeki özel URL olmalıdır ve küme adından sonra '-int ' listelenmiş olmalıdır.
    
       Örneğin, olarak değiştirin `https://mycluster.azurehdinsight.net/` `https://mycluster-int.azurehdinsight.net/` . `-int`Sonra `mycluster` , önce, daha önce`.azurehdinsight.net`
    1. Küme VNet 'daysa, şirket içinde barındırılan IR kullanılır ve özel URL kullanılmıştır, ancak bağlantı hala başarısız olduysa, IR 'nin yüklü olduğu VM HDI 'ye bağlanmada sorun yaşadı. 
    
       IR 'nin yüklendiği VM 'ye bağlanın ve bir tarayıcıda ambarı Kullanıcı arabirimini açın. Küme için özel URL 'YI kullanın. Bu bağlantı tarayıcıdan çalışmalıdır. Değilse, daha fazla yardım için HDInsight destek ekibine başvurun.
    1. Şirket içinde barındırılan IR kullanılmıyorsa, HDI kümesine genel olarak erişilebilir olması gerekir. Bir tarayıcıda ambarı Kullanıcı arabirimini açın ve açıldığından emin olun. Küme veya hizmetlerle ilgili herhangi bir sorun varsa, yardım için HDInsight destek ekibine başvurun.

       ADF bağlantılı hizmetinde kullanılan HDI kümesi URL 'sine, test bağlantısının geçişine ve çalıştırmaların çalışması için ADF IR (Şirket içinde barındırılan veya Azure) için erişilebilir olması gerekir. Bu durum, VM 'den ya da herhangi bir genel makineden URL 'YI bir tarayıcıdan açarak doğrulanabilir.

### <a name="error-code-2343"></a>Hata kodu: 2343

- **İleti**: `User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Neden**: Kullanıcı adı ya da parola boş.

- **Öneri**: HDI 'ya bağlanmak için doğru kimlik bilgilerini sağlayın ve yeniden deneyin.

### <a name="error-code-2345"></a>Hata kodu: 2345

- **İleti**: `Failed to read the content of the hive script. Error: '%message;'`

- **Neden**: betik dosyası yok ya da ADF betiğin konumuna bağlanamadı.

- **Öneri**: betiğin var olduğunu ve ilişkili bağlı hizmetin bir bağlantı için uygun kimlik bilgilerine sahip olduğunu doğrulayın.

### <a name="error-code-2346"></a>Hata kodu: 2346

- **İleti**: `Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Neden**: ADF, HDI kümesine açık bir veritabanı BAĞLANTıSı (ODBC) bağlantısı kurmaya çalıştı ve hata vererek başarısız oldu.

- **Öneri:** 

   1. ODBC/Java veritabanı bağlantısı (JDBC) bağlantınızı doğru şekilde ayarladığınıza emin olun.
      1. JDBC için aynı sanal ağı kullanıyorsanız bu bağlantıyı şuradan edinebilirsiniz:<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. Doğru JDBC ayarlamış olduğunuzdan emin olmak için bkz. [HDInsight 'TA JDBC sürücüsü aracılığıyla sorgu Apache Hive](../hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver.md).
      1. Açık veritabanı (ODB) için, doğru kuruluma sahip olduğunuzdan emin olmak için bkz. [öğretici: ODBC ve PowerShell Ile sorgu Apache Hive](../hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell.md) . 
   1. Hiveserver2, Hive Metasarı ve Hiveserver2 Interactive 'in etkin ve çalışır olduğunu doğrulayın. 
   1. Ambarı Kullanıcı arabirimini (UI) denetleyin:
      1. Tüm hizmetlerin hala çalıştığından emin olun.
      1. Ambarı kullanıcı arabiriminden, panonuzdaki uyarı bölümünü kontrol edin.
         1. Uyarılar ve uyarı çözümleri hakkında daha fazla bilgi için bkz. [bir kümeyi yönetme ve izleme ](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
   1. Bu adımlar sorunu çözmek için yeterli değilse, Azure HDInsight ekibine başvurun.

### <a name="error-code-2347"></a>Hata kodu: 2347

- **İleti**: `Hive execution through ODBC failed with error message '%message;'.`

- **Neden**: ADF, ODBC bağlantısı aracılığıyla HDI kümesine yürütülmek üzere Hive betiği gönderdi ve betik HDI üzerinde başarısız oldu.

- **Öneri:** 

   1. ODBC/Java veritabanı bağlantısı (JDBC) bağlantınızı doğru şekilde ayarladığınıza emin olun.
      1. JDBC için aynı sanal ağı kullanıyorsanız bu bağlantıyı şuradan edinebilirsiniz:<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. Doğru JDBC ayarlamış olduğunuzdan emin olmak için bkz. [HDInsight 'TA JDBC sürücüsü aracılığıyla sorgu Apache Hive](../hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver.md).
      1. Açık veritabanı (ODB) için, doğru kuruluma sahip olduğunuzdan emin olmak için bkz. [öğretici: ODBC ve PowerShell Ile sorgu Apache Hive](../hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell.md) . 
   1. Hiveserver2, Hive Metasarı ve Hiveserver2 Interactive 'in etkin ve çalışır olduğunu doğrulayın. 
   1. Ambarı Kullanıcı arabirimini (UI) denetleyin:
      1. Tüm hizmetlerin hala çalıştığından emin olun.
      1. Ambarı kullanıcı arabiriminden, panonuzdaki uyarı bölümünü kontrol edin.
         1. Uyarılar ve uyarı çözümleri hakkında daha fazla bilgi için bkz. [bir kümeyi yönetme ve izleme ](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
   1. Bu adımlar sorunu çözmek için yeterli değilse, Azure HDInsight ekibine başvurun.

### <a name="error-code-2348"></a>Hata kodu: 2348

- **İleti**: `The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Neden**: depolama bağlı hizmeti özellikleri doğru ayarlanmadı.

- **Öneri**: HDI etkinlikleri için ana depolama bağlı hizmetinde yalnızca tam bağlantı dizeleri desteklenir. MSI yetkilendirmelerini veya uygulamalarını kullandığınızdan emin olun.

### <a name="error-code-2350"></a>Hata kodu: 2350

- **İleti**: `Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Neden**: dosyaların bulunması gereken depolama alanına bağlanmak için girilen kimlik bilgileri yanlış veya dosyalar burada yok.

- **Öneri**: Bu hata, ADF HDI etkinliklerini hazırlarken oluşur ve ışı HDI 'ye göndermeden önce dosyaları ana depolamaya kopyalamaya çalışır. Belirtilen konumda dosyaların mevcut olduğundan ve depolama bağlantısının doğru olduğundan emin olun. ADF HDI etkinlikleri, HDI etkinlikleriyle ilgili depolama hesaplarında MSI kimlik doğrulamasını desteklemediğinden, bu bağlı hizmetlerin tam anahtarlara sahip olduğunu veya Azure Key Vault kullandığını doğrulayın.

### <a name="error-code-2351"></a>Hata kodu: 2351

- **İleti**: `Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Neden**: dosya belirtilen yolda yok.

- **Öneri**: dosyanın gerçekten varolup olmadığını ve bu dosyayı işaret eden bağlantı bilgileriyle bağlantılı hizmetin doğru kimlik bilgilerine sahip olup olmadığını denetleyin.

### <a name="error-code-2352"></a>Hata kodu: 2352

- **İleti**: `The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Neden**: dosya depolama bağlı hizmeti özellikleri doğru ayarlanmadı.

- **Öneri**: dosya depolama bağlı hizmetinin özelliklerinin düzgün yapılandırıldığını doğrulayın.

### <a name="error-code-2353"></a>Hata kodu: 2353

- **İleti**: `The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Neden**: betik depolama bağlı hizmeti özellikleri doğru ayarlanmadı.

- **Öneri**: betik depolama bağlı hizmetinin özelliklerinin düzgün yapılandırıldığını doğrulayın.

### <a name="error-code-2354"></a>Hata kodu: 2354

- **İleti**: `The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Neden**: depolama bağlı hizmet türü etkinlik tarafından desteklenmiyor.

- **Öneri**: seçili bağlı hizmetin etkinlik için desteklenen türlerden birine sahip olduğunu doğrulayın. HDI etkinlikleri AzureBlobStorage ve AzureBlobFSStorage bağlı hizmetlerini destekler.

   Daha fazla bilgi için [Azure HDInsight kümeleri ile kullanmak üzere depolama seçeneklerini karşılaştırma](../hdinsight/hdinsight-hadoop-compare-storage-options.md) konusunu okuyun

### <a name="error-code-2355"></a>Hata kodu: 2355

- **İleti**: `The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Neden**: için belirtilen değer `commandEnvironment` yanlış.

- **Öneri**: belirtilen değerin şuna benzediğini doğrulayın:
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    Ayrıca, her değişkenin listede yalnızca bir kez göründüğünü doğrulayın.

### <a name="error-code-2356"></a>Hata kodu: 2356

- **İleti**: `The commandEnvironment already contains a variable named '%variableName;'.`

- **Neden**: için belirtilen değer `commandEnvironment` yanlış.

- **Öneri**: belirtilen değerin şuna benzediğini doğrulayın:
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    Ayrıca, her değişkenin listede yalnızca bir kez göründüğünü doğrulayın.

### <a name="error-code-2357"></a>Hata kodu: 2357

- **İleti**: `The certificate or password is wrong for ADLS Gen 1 storage.`

- **Neden**: belirtilen kimlik bilgileri yanlış.

- **Öneri**: ADLS Gen 1 ' deki bağlantı bilgilerinin hizmetle bağlantılı olduğunu doğrulayın ve test bağlantısının başarılı olduğunu doğrulayın.

### <a name="error-code-2358"></a>Hata kodu: 2358

- **İleti**: `The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Neden**: gerekli özelliğin belirtilen değeri `TimeToLive` geçersiz bir biçime sahip. 

- **Öneri**: değeri önerilen aralığa göre güncelleştirip yeniden deneyin.

### <a name="error-code-2359"></a>Hata kodu: 2359

- **İleti**: `The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Neden**: özellik için girilen değer `roles` geçersiz.

- **Öneri**: değeri önerilerden biri olacak şekilde güncelleştirip yeniden deneyin.

### <a name="error-code-2360"></a>Hata kodu: 2360

- **İleti**: `The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Neden**: için belirtilen bağlantı dizesi `HCatalogLinkedService` geçersiz.

- **Öneri**: değeri doğru BIR Azure SQL bağlantı dizesi olarak güncelleştirip yeniden deneyin.

### <a name="error-code-2361"></a>Hata kodu: 2361

- **İleti**: `Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Neden**: küme oluşturma başarısız oldu ve ADF, HDInsight hizmetinden geri bir hata almadı.

- **Öneri**: Azure Portal açın ve girilen ada sahip HDI kaynağını bulmayı deneyin, sonra sağlama durumunu denetleyin. Daha fazla yardım için HDInsight destek ekibine başvurun.

### <a name="error-code-2362"></a>Hata kodu: 2362

- **İleti**: `Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Neden**: belirtilen ek depolama alanı Azure Blob depolama değildi.

- **Öneri**: Azure Blob Depolama hesabını, HDInsight isteğe bağlı hizmeti için ek depolama alanı olarak sağlayın.

### <a name="ssl-error-when-adf-linked-service-using-hdinsight-esp-cluster"></a>HDInsight ESP kümesi kullanılarak ADF bağlantılı hizmet sırasında SSL hatası

- **İleti**: `Failed to connect to HDInsight cluster: 'ERROR [HY000] [Microsoft][DriverSupport] (1100) SSL certificate verification failed because the certificate is missing or incorrect.`

- **Neden**: sorun büyük olasılıkla sistem güven deposuyla ilgilidir.

- **Çözüm**: ayarı değiştirmek Için **Microsoft Integration Runtime\4.0\Shared\ODBC DRIVERS\MICROSOFT Hive ODBC driver\lib** yoluna gidebilir ve DriverConfiguration64.exe açabilirsiniz.

    ![Sistem güven deposunun kullan işaretini kaldır](./media/connector-troubleshoot-guide/system-trust-store-setting.png)

## <a name="web-activity"></a>Web Etkinliği

### <a name="error-code-2128"></a>Hata kodu: 2128

- **İleti**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Neden**: Bu sorun ağ BAĞLANTıSı, DNS hatası, sunucu sertifikası doğrulama veya zaman aşımı nedeniyle yapılır.

- **Öneri**: vurılmaya çalıştığınız bitiş noktasının isteklere yanıt verdiğini doğrulayın. **Fiddler/Postman** gibi araçları kullanabilirsiniz.

### <a name="error-code-2108"></a>Hata kodu: 2108

- **İleti**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Neden**: ağ BAĞLANTıSı, DNS hatası, sunucu sertifikası doğrulama veya zaman aşımı gibi temel bir sorundan dolayı istek başarısız oldu.

- **Öneri**: isteği doğrulamak Için Fiddler/Postman kullanın.

#### <a name="more-details"></a>Daha fazla ayrıntı’yı seçin
**Fiddler** 'i kullanarak izlenen Web uygulamasının http oturumunu oluşturma:

1. [Fiddler](https://www.telerik.com/download/fiddler)'i indirin, yükleyin ve açın.

1. Web uygulamanız https kullanıyorsa, **Araçlar**  >  **Fiddler seçenekler**  >  **https**' ye gidin.

   1. HTTPS sekmesinde, her iki **yakalama https** ve **https trafiğinin şifresini çöz**' ü seçin.

      ![Fiddler seçenekleri](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Uygulamanız TLS/SSL sertifikaları kullanıyorsa, Fiddler sertifikasını cihazınıza ekleyin.

   Git: **Araçlar**  >  **Fiddler seçenekler**  >  **https**  >  **eylemleri**  >  **kök sertifikayı masaüstüne dışarı aktarma**.

1. **Dosya**  >  **yakalama trafiğine** giderek yakalamayı kapatın. Ya da **F12** tuşuna basın.

1. Tüm önbelleğe alınmış öğelerin kaldırılması ve yeniden indirilmesi gereken şekilde tarayıcınızın önbelleğini temizleyin.

1. İstek oluştur:

1. **Besteci** sekmesini seçin.

   1. HTTP yöntemini ve URL 'YI ayarlayın.
 
   1. Gerekirse, üst bilgiler ve istek gövdesi ekleyin.

   1. **Yürüt**’ü seçin.

1. Trafiği yeniden yakalamayı açın ve sayfada sorunlu işlemi doldurun.

1. Git: **Dosya**  >    >  **tüm oturumları** Kaydet.

Daha fazla bilgi için bkz. [Fiddler ile çalışmaya](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)başlama.

## <a name="general"></a>Genel

### <a name="activity-stuck-issue"></a>Etkinlik takıldı sorun

Etkinliğin normal çalışmalarından çok daha uzun süre sürdiğini gözlemlerseniz, bu durum takılmış olabilir. İptal edip yardımcı olup olmadığını görmek için yeniden deneyebilirsiniz. Bir kopyalama etkinliği ise, performans izleme ve sorun giderme [etkinliklerini kopyalama etkinliği performansını sorun](copy-activity-performance-troubleshooting.md)giderme hakkında bilgi edinebilirsiniz. veri [akışıdır, veri akışı performansını](concepts-data-flow-performance.md) ve ayarlama kılavuzunu eşleştirmeyi öğrenin.

### <a name="payload-is-too-large"></a>Yük çok büyük

**Hata iletisi:**`The payload including configurations on activity/dataSet/linked service is too large. Please check if you have settings with very large value and try to reduce its size.`

**Neden:** Her bir etkinlik için yük, etkinlik yapılandırması, ilişkili veri kümeleri ve varsa bağlı hizmet yapılandırmalarını ve etkinlik türü başına oluşturulan sistem özelliklerinin küçük bir bölümünü içerir. Bu tür yük boyutu sınırı, [Data Factory sınırları](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits) bölümünde belirtildiği gıbı 896 KB 'dir.

**Öneri:** Özellikle de gerçek verileri denetim akışındaki etkinliklerde geçirirseniz, bu sınıra büyük bir veya daha fazla büyük parametre değeri geçirin. Büyük parametre değerlerinin boyutunu azaltabilir veya işlem hattı mantığınızı, bu tür değerleri etkinliklerde geçirmeden ve bunun yerine etkinliğin içinde işleyecek şekilde ayarlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla sorun giderme Yardım için şu kaynakları deneyin:

* [Data Factory blogu](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory Özellik istekleri](https://feedback.azure.com/forums/270578-data-factory)
* [Data Factory için Stack Overflow Forumu](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Data Factory hakkında Twitter bilgileri](https://twitter.com/hashtag/DataFactory)
* [Azure videoları](https://azure.microsoft.com/resources/videos/index/)
* [Soru sayfası Microsoft Q&](/answers/topics/azure-data-factory.html)
