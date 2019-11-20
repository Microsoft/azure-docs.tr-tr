---
title: Azure Data Factory sorunlarını giderme
description: Azure Data Factory 'de dış denetim etkinliklerinin nasıl giderileceğini öğrenin.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 66590e40ff2440a166310ec3d57026f5a2cdbd1d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676856"
---
# <a name="troubleshoot-azure-data-factory"></a>Azure Data Factory sorunlarını giderme

Bu makalede Azure Data Factory içindeki dış denetim etkinlikleri için genel sorun giderme yöntemleri incelenmektedir.

## <a name="connector-and-copy-activity"></a>Bağlayıcı ve kopyalama etkinliği

Bağlayıcı sorunları için ör. kopyalama etkinliğini kullanarak hatayla karşılaşırsanız, [Azure Data Factory bağlayıcılarına sorun giderme](connector-troubleshoot-guide.md)bölümüne bakın.

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Hata kodu: 3200

- **İleti**: hata 403.

- **Neden**: `The Databricks access token has expired.`

- **Öneri**: varsayılan olarak, Azure Databricks erişim belirteci 90 gün için geçerlidir. Yeni bir belirteç oluşturun ve bağlı hizmeti güncelleştirin.


### <a name="error-code--3201"></a>Hata kodu: 3201

- **İleti**: `Missing required field: settings.task.notebook_task.notebook_path.`

- **Neden**: `Bad authoring: Notebook path not specified correctly.`

- **Öneri**: Databricks etkinliğinde Not defteri yolunu belirtin.

<br/>

- **İleti**: `Cluster   ... does not exist.`

- **Neden**: `Authoring error: Databricks cluster does not exist or has been deleted.`

- **Öneri**: Databricks kümesinin var olduğunu doğrulayın.

<br/>

- **İleti**: `Invalid Python file URI.... Please visit Databricks user guide for supported URI schemes.`

- **Neden**: `Bad authoring.`

- **Öneri**: çalışma alanı adresleme şemaları için mutlak yollar ya da Databricks dosya sisteminde depolanan dosyalar için `dbfs:/folder/subfolder/foo.py` belirtin.

<br/>

- **İleti**: `{0} LinkedService should have domain and accessToken as required properties.`

- **Neden**: `Bad authoring.`

- **Öneri**: [bağlı hizmet tanımını](compute-linked-services.md#azure-databricks-linked-service)doğrulayın.

<br/>

- **İleti**: `{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Neden**: `Bad authoring.`

- **Öneri**: [bağlı hizmet tanımını](compute-linked-services.md#azure-databricks-linked-service)doğrulayın.

<br/>

- **İleti**: `Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Neden**: `Bad authoring.`

- **Öneri**: hata iletisine bakın. 

<br/>

### <a name="error-code--3202"></a>Hata kodu: 3202

- **İleti**: `There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Neden**: `Too many Databricks runs in an hour.`

- **Öneri**: Bu Databricks çalışma alanını kullanan tüm işlem hatlarını iş oluşturma oranı için denetleyin.  İşlem hatları çok fazla Databricks çalıştırıyorsa, bazı işlem hatlarını yeni bir çalışma alanına geçirin.

<br/>

- **İleti**: `Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Neden**: `Authoring error: No value provided for the parameter.`

- **Öneri**: JSON işlem hattını inceleyin ve baseparameters not defterindeki tüm parametrelerin boş olmayan bir değer belirttiğinden emin olun.

<br/>

- **İleti**: `User: `SimpleUserContext {UserID =..., name =user@company.com, OrgID =...}` is not   authorized to access cluster.`

- **Neden**: erişim belirtecini oluşturan kullanıcının bağlı hizmette belirtilen Databricks kümesine erişmesine izin verilmiyor.

- **Öneri**: kullanıcının çalışma alanında gerekli izinlere sahip olduğundan emin olun.


### <a name="error-code--3203"></a>Hata kodu: 3203

- **İleti**: `The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Neden**: küme sonlandırıldı. Etkileşimli kümeler için bu bir yarış durumu olabilir.

- **Öneri**: Bu hatayı önlemenin en iyi yolu iş kümelerini kullanmaktır.


### <a name="error-code--3204"></a>Hata kodu: 3204

- **İleti**: `Job execution failed.`

- **Neden**: hata iletileri beklenmeyen küme durumu veya belirli bir etkinlik gibi çeşitli sorunları gösterir. Çoğu zaman hiç bir hata iletisi görüntülenmez. 

- **Öneri**: yok


## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

Aşağıdaki tablo U-SQL için geçerlidir.


### <a name="error-code--2709"></a>Hata kodu: 2709

- **İleti**: `The access token is from the wrong tenant.`

- **Neden**: yanlış Azure Active Directory (Azure AD) kiracısı.

- **Öneri**: yanlış Azure Active Directory (Azure AD) kiracısı.

<br/>

- **İleti**: `We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Neden**: Bu hata, Data Lake Analytics azaltma nedeniyle oluşur.

- **Öneri**: Data Factory Tetikleyicileri ve etkinliklerdeki eşzamanlılık ayarlarını değiştirerek Data Lake Analytics gönderilen işlerin sayısını azaltın. Veya Data Lake Analytics limitlerini artırın.

<br/>

- **İleti**: `This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Neden**: Bu hata, Data Lake Analytics azaltma nedeniyle oluşur. 

- **Öneri**: Data Factory Tetikleyicileri ve etkinliklerdeki eşzamanlılık ayarlarını değiştirerek Data Lake Analytics gönderilen işlerin sayısını azaltın. Veya Data Lake Analytics limitlerini artırın.


### <a name="error-code--2705"></a>Hata kodu: 2705

- **İleti**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Neden**: hizmet sorumlusu veya sertifikasının depolama alanındaki dosyaya erişimi yok.

- **Öneri**: kullanıcının Data Lake Analytics işlerin Data Lake Analytics hesabına ve kök klasörden varsayılan Data Lake Storage örneğine erişimi olduğundan emin olun.


### <a name="error-code--2711"></a>Hata kodu: 2711

- **İleti**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Neden**: hizmet sorumlusu veya sertifikasının depolama alanındaki dosyaya erişimi yok.

- **Öneri**: kullanıcının Data Lake Analytics işlerin Data Lake Analytics hesabına ve kök klasörden varsayılan Data Lake Storage örneğine erişimi olduğundan emin olun.

<br/>

- **İleti**: `Cannot find the 'Azure Data Lake Store' file or folder.`

- **Neden**: U-SQL dosyasının yolu yanlış veya bağlı hizmet kimlik bilgilerinin erişimi yok.

- **Öneri**: bağlı hizmette belirtilen yolu ve kimlik bilgilerini doğrulayın.


### <a name="error-code--2704"></a>Hata kodu: 2704

- **İleti**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Neden**: hizmet sorumlusu veya sertifikasının depolama alanındaki dosyaya erişimi yok.

- **Öneri**: kullanıcının Data Lake Analytics işlerin Data Lake Analytics hesabına ve kök klasörden varsayılan Data Lake Storage örneğine erişimi olduğundan emin olun.


### <a name="error-code--2707"></a>Hata kodu: 2707

- **İleti**: `Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Neden**: bağlı hizmette Data Lake Analytics hesabı yanlış.

- **Öneri**: doğru hesabın sağlandığını doğrulayın.


### <a name="error-code--2703"></a>Hata kodu: 2703

- **İleti**: `Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Neden**: hata Data Lake Analytics. 

- **Öneri**: örnek gibi bir hata, işin Data Lake Analytics gönderildiği ve betiği başarısız olduğu anlamına gelir. Data Lake Analytics araştırın. Portalda Data Lake Analytics hesabına gidin ve Data Factory etkinlik çalıştırma KIMLIĞINI (işlem hattı çalıştırma KIMLIĞI değil) kullanarak işi arayın. İş, hata hakkında daha fazla bilgi sağlar ve sorun gidermenize yardımcı olur. Çözüm açık değilse, Data Lake Analytics destek ekibine başvurun ve hesap adınızı ve iş KIMLIĞINI içeren iş URL 'sini sağlayın.



## <a name="azure-functions"></a>Azure functions (Azure işlevleri)

### <a name="error-code--3602"></a>Hata kodu: 3602

- **İleti**: `Invalid HttpMethod: {method}.`

- **Neden**: etkinlik yükünde belirtilen http yöntemi Azure işlevi etkinliği tarafından desteklenmiyor. 

- **Öneri**: desteklenen HTTP yöntemleri put, Post, Get, DELETE, OPTIONS, Head ve Trace ' dir.


### <a name="error-code--3603"></a>Hata kodu: 3603

- **İleti**: `Response content is not a valid JObject.`

- **Neden**: çağrılan Azure işlevi YANıTTA bir JSON yükü döndürmedi. Data Factory içindeki Azure işlevi etkinliği yalnızca JSON yanıt içeriğini destekler. 

- **Öneri**: Azure işlevini, GEÇERLI bir JSON yükü döndürecek şekilde güncelleştirin. Örneğin, bir C# işlev `(ActionResult)new<OkObjectResult("{`\"kimliği döndürebilir\":\"123\"`}");`.


### <a name="error-code--3606"></a>Hata kodu: 3606

- **İleti**: `Azure function activity missing function key.`

- **Neden**: Azure işlevi etkinlik tanımı tamamlanmadı. 

- **Öneri**: lütfen Input AZUREFUNCTION Activity JSON tanımının ' functionkey ' adlı özelliği olduğunu denetleyin.


### <a name="error-code--3607"></a>Hata kodu: 3607

- **İleti**: `Azure function activity missing function name.`

- **Neden**: Azure işlevi etkinlik tanımı tamamlanmadı. 

- **Öneri**: lütfen Input AZUREFUNCTION Activity JSON tanımının ' fonksiyonadı ' adlı özelliği olduğunu denetleyin.


### <a name="error-code--3608"></a>Hata kodu: 3608

- **İleti**: `Call to provided Azure function '{FunctionName}' failed with status-'{statusCode}' and message - '{message}'.` 

- **Neden**: etkinlik tanımındaki Azure işlevi ayrıntıları hatalı olabilir. 

- **Öneri**: Azure işlev ayrıntılarını düzeltip yeniden deneyin.


### <a name="error-code--3609"></a>Hata kodu: 3609

- **İleti**: `Azure function activity missing functionAppUrl.` 

- **Neden**: Azure işlevi etkinlik tanımı tamamlanmadı. 

- **Öneri**: lütfen Input AZUREFUNCTION Activity JSON tanımının ' functionAppUrl ' adlı özelliği olduğunu denetleyin.


### <a name="error-code--3610"></a>Hata kodu: 3610

- **İleti**: `There was an error while calling endpoint.`

- **Neden**: Işlev URL 'si yanlış olabilir.

- **Öneri**: lütfen JSON etkinliğinde ' functionAppUrl ' değerinin doğru olduğundan emin olun ve yeniden deneyin.


### <a name="error-code--3611"></a>Hata kodu: 3611

- **İleti**: `Azure function activity missing Method in JSON.` 

- **Neden**: Azure işlevi etkinlik tanımı tamamlanmadı.

- **Öneri**: Input AZUREFUNCTION Activity JSON tanımının ' Method ' adlı özelliğe sahip olup olmadığını denetleyin.


### <a name="error-code--3612"></a>Hata kodu: 3612

- **İleti**: `Azure function activity missing LinkedService definition in JSON.`

- **Neden**: Azure işlevi etkinlik tanımı tamamlanmamış olabilir.

- **Öneri**: lütfen Input AZUREFUNCTION Activity JSON tanımının bağlı hizmet ayrıntılarına sahip olduğunu denetleyin.


## <a name="azure-machine-learning"></a>Azure Machine Learning


### <a name="error-code--4101"></a>Hata kodu: 4101

- **İleti**: `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Neden**: Hatalı biçim veya bir özelliğin tanımı eksik.

- **Öneri**: lütfen etkinliğin doğru verilerle tanımlanıp tanımlanmadığında emin olun.


### <a name="error-code--4110"></a>Hata kodu: 4110

- **İleti**: AZUREMLEXECUTEPIPELINE etkinliği JSON içinde LinkedService tanımı eksik.

- **Neden**: AzureMLExecutePipeline etkinlik tanımı tamamlanmadı.

- **Öneri**: lütfen Input AZUREMLEXECUTEPIPELINE Activity JSON tanımının bağlı hizmet ayrıntılarına sahip olup olmadığını denetleyin.


### <a name="error-code--4111"></a>Hata kodu: 4111

- **İleti**: `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Neden**: etkinlik tanımı yanlış.

- **Öneri**: lütfen Input AZUREMLEXECUTEPIPELINE Activity JSON tanımının doğru bağlı hizmet ayrıntılarına sahip olup olmadığını denetleyin.


### <a name="error-code--4112"></a>Hata kodu: 4112

- **İleti**: `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Neden**: Hatalı biçim veya bir özelliğin tanımı eksik.

- **Öneri**: lütfen bağlı hizmet tanımının doğru verilere sahip olup olmadığını denetleyin.


### <a name="error-code--4121"></a>Hata kodu: 4121

- **İleti**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Neden**: Azure ML hizmetine erişmek için kullanılan kimlik bilgilerinin süresi doldu.

- **Öneri**: Lütfen kimlik bilgisinin geçerli olduğunu doğrulayın ve yeniden deneyin


### <a name="error-code--4122"></a>Hata kodu: 4122

- **İleti**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Neden**: AzureML hizmeti bağlantılı hizmette belirtilen kimlik bilgileri geçersiz veya işlem için izne sahip değil.

- **Öneri**: lütfen bağlı hizmette kimlik bilgisinin geçerli olduğunu ve AzureML hizmeti 'ne erişim izni olduğunu doğrulayın.


### <a name="error-code--4123"></a>Hata kodu: 4123

- **İleti**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Neden**: `Properties of the activity such as pipelineParamters are invalid for the Azure ML pipeline.`

- **Öneri**: Lütfen, bağlı hizmette belirtilen YAYıMLANAN Azure ML işlem hattının beklenen yükünü eşleştirmek için etkinlik özelliklerinin değerini denetleyin.


### <a name="error-code--4124"></a>Hata kodu: 4124

- **İleti**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Neden**: Yayınlanan Azure ML ardışık düzen uç noktası yok.

- **Öneri**: lütfen Azure ML hizmeti 'Nde bağlı hizmette belirtilen YAYıMLANAN Azure ML ardışık düzen uç noktasının mevcut olduğunu doğrulayın.


### <a name="error-code--4125"></a>Hata kodu: 4125

- **İleti**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Neden**: Azure ML hizmetinde sunucu hatası.

- **Öneri**: lütfen daha sonra yeniden deneyin. Sorun devam ettiğinden yardım için Azure ML hizmeti ekibine başvurun.


### <a name="error-code--4126"></a>Hata kodu: 4126

- **İleti**: `AzureML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in AzureMLService for more error loggings.`

- **Neden**: AzureML işlem hattı çalıştırması başarısız oldu.

- **Öneri**: daha fazla hata günlüğü için AzureMLService BAKıN ve ml işlem hattını onarın


## <a name="custom"></a>Özel

Aşağıdaki tablo Azure Batch için geçerlidir.


### <a name="error-code--2500"></a>Hata kodu: 2500

- **İleti**: `Hit unexpected exception and execution failed.`

- **Neden**: `Can't launch command, or the program returned an error code.`

- **Öneri**: yürütülebilir dosyanın var olduğundan emin olun. Program başlatılmışsa, *stdout. txt* ve *stderr. txt* ' nin depolama hesabına yüklendiğinden emin olun. Hata ayıklama için kodunuzda bir kopan günlüklerini yayma iyi bir uygulamadır.


### <a name="error-code--2501"></a>Hata kodu: 2501

- **İleti**: `Cannot access user batch account; please check batch account settings.`

- **Neden**: Hatalı toplu erişim anahtarı veya havuz adı.

- **Öneri**: bağlı hizmette havuz adını ve Batch erişim anahtarını doğrulayın.


### <a name="error-code--2502"></a>Hata kodu: 2502

- **İleti**: `Cannot access user storage account; please check storage account settings.`

- **Neden**: Hatalı depolama hesabı adı veya erişim anahtarı.

- **Öneri**: depolama hesabı adını ve bağlı hizmette erişim anahtarını doğrulayın.


### <a name="error-code--2504"></a>Hata kodu: 2504

- **İleti**: `Operation returned an invalid status code 'BadRequest'.` 

- **Neden**: özel etkinliğin FolderPath öğesinde çok fazla dosya yok. ResourceFiles 'ın toplam boyutu 32.768 karakterden fazla olamaz.

- **Öneri**: gereksiz dosyaları kaldırın. Ya da bunları ayıklayın ve ayıklamak için bir unzip komutu ekleyin. Örneğin, `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe` kullanın


### <a name="error-code--2505"></a>Hata kodu: 2505

- **İleti**: `Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Neden**: özel etkinlikler yalnızca bir erişim anahtarı kullanan depolama hesaplarını destekler.

- **Öneri**: hata açıklamasına bakın.


### <a name="error-code--2507"></a>Hata kodu: 2507

- **İleti**: `The folder path does not exist or is empty: ....`

- **Neden**: belirtilen yoldaki depolama hesabında hiçbir dosya yok.

- **Öneri**: klasör yolu çalıştırmak istediğiniz yürütülebilir dosyaları içermelidir.


### <a name="error-code--2508"></a>Hata kodu: 2508

- **İleti**: `There are duplicate files in the resource folder.`

- **Neden**: aynı ada sahip birden çok dosya FolderPath 'in farklı alt klasörlerinde.

- **Öneri**: özel etkinlikler FolderPath altındaki klasör yapısını düzleştirebilir.  Klasör yapısını korumanız gerekiyorsa, dosyaları zip halinde ve bir unzip komutu kullanarak Azure Batch ayıklayın. Örneğin, `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe` kullanın


### <a name="error-code--2509"></a>Hata kodu: 2509

- **İleti**: `Batch   url ... is invalid; it must be in Uri format.` 

- **Neden**: Batch url 'lerinin `https://mybatchaccount.eastus.batch.azure.com` benzer olması gerekir

- **Öneri**: hata açıklamasına bakın.


### <a name="error-code--2510"></a>Hata kodu: 2510

- **İleti**: `An   error occurred while sending the request.`

- **Neden**: Batch URL 'si geçersiz. 

- **Öneri**: Batch URL 'sini doğrulayın.


## <a name="hdinsight"></a>HDInsight

Aşağıdaki tablo Spark, Hive, MapReduce, Pig ve Hadoop akışı için geçerlidir.


### <a name="error-code--2300"></a>Hata kodu: 2300

- **İleti**: `Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **Neden**: belirtilen küme URI 'si geçersiz. 

- **Öneri**: kümenin silinmediğinden ve belirtilen URI 'nin doğru olduğundan emin olun. URI 'yi bir tarayıcıda açtığınızda, ambarı Kullanıcı arabirimini görmeniz gerekir. Küme bir sanal ağda ise, URI özel URI olmalıdır. Açmak için, aynı sanal ağın parçası olan bir VM kullanın. Daha fazla bilgi için bkz. [Apache Hadoop hizmetlerine doğrudan bağlanma](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services).

<br/>

- **İleti**: `Hadoop job submission failed. Job: …, Cluster: …/. Error: A task was canceled.`

- **Neden**: iş gönderimi zaman aşımına uğradı. 

- **Öneri**: sorun genel HDInsight bağlantısı ya da ağ bağlantısı olabilir. Önce HDInsight ambarı Kullanıcı arabiriminin herhangi bir tarayıcıdan kullanılabilir olduğunu doğrulayın. Kimlik bilgilerinizin hala geçerli olduğunu doğrulayın. Şirket içinde barındırılan tümleşik çalışma zamanı (IR) kullanıyorsanız bunu, şirket içinde barındırılan IR 'nin yüklü olduğu VM veya makineden gerçekleştirdiğinizden emin olun. Sonra Data Factory işi yeniden göndermeyi deneyin. Hala başarısız olduysa, destek için Data Factory ekibine başvurun.


- **İleti**: `Unauthorized: Ambari user name or password is incorrect  <br/><br/>Unauthorized: User admin is locked out in Ambari.   <br/><br/>403 - Forbidden: Access is denied.`

- **Neden**: HDInsight kimlik bilgileri yanlış veya zaman aşımına uğradı.

- **Öneri**: kimlik bilgilerini düzeltin ve bağlı hizmeti yeniden dağıtın. Önce, herhangi bir tarayıcıda küme URI 'sini açarak ve oturum açmaya çalışarak, kimlik bilgilerinin HDInsight üzerinde çalıştığından emin olun. Kimlik bilgileri çalışmazsa, bunları Azure portal sıfırlayabilirsiniz.

<br/>

- **İleti**: `502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **Neden**: Bu hata HDInsight 'tan.

- **Öneri**: Bu hata HDInsight kümesinden. Daha fazla bilgi için bkz. [ambarı Kullanıcı arabirimi 502 hatası](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 Spark thrift sunucusuna bağlanma](https://hdinsight.github.io/spark/spark-thriftserver-errors.html) [502, Spark Thrift sunucusuna bağlanma](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)ve [Application Gateway hatalı ağ geçidi hatalarıyla ilgili sorunları giderme](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br/>

- **İleti**: `Hadoop job submission failed. Job: …, Cluster: ... Error:   {\"error\":\"Unable to service the submit job request as   templeton service is busy with too many submit job requests. Please wait for some time before retrying the operation. Please refer to the config   templeton.parallellism.job.submit to configure concurrent requests. <br/><br/>Hadoop job submission failed. Job: xx, Cluster: name.   Error: {\"error\":\"java.io.IOException:   org.apache.hadoop.yarn.exceptions.YarnException: Failed to submit   application_1561147195099_3730 to YARN :   org.apache.hadoop.security.AccessControlException: Queue root.joblauncher already has 500 applications, cannot accept submission of application:   application_1561147195099_3730\`

- **Neden**: aynı anda HDInsight 'a çok fazla iş gönderiliyor.

- **Öneri**: HDInsight 'a gönderilen eşzamanlı iş sayısını sınırlamayı düşünün. İşlerin aynı etkinlik tarafından gönderiliyorsa Data Factory etkinlik eşzamanlılık bölümüne bakın. Yinelenen işlem hattı çalıştırmaları zaman içinde yayma için Tetikleyicileri değiştirin. Hatanın önereceği `templeton.parallellism.job.submit` ayarlamak için HDInsight belgelerine bakın.


### <a name="error-code--2303"></a>Hata kodu: 2303

- **İleti**: `Hadoop job failed with exit code '5'. See   'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **Neden**: iş HDInsight 'a gönderildi ve HDInsight üzerinde başarısız oldu.

- **Öneri**: iş HDInsight 'a başarıyla gönderildi. Kümede başarısız oldu. İş ve günlükleri HDInsight ambarı Kullanıcı arabiriminde açın ya da hata iletisi önerirken dosyayı depolamadan açın. Dosya hata ayrıntılarını gösterir.


### <a name="error-code--2310"></a>Hata kodu: 2310

- **İleti**: `Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **Neden**: belirtilen küme URI 'si geçersiz. 

- **Öneri**: kümenin silinmediğinden ve belirtilen URI 'nin doğru olduğundan emin olun. URI 'yi bir tarayıcıda açtığınızda, ambarı Kullanıcı arabirimini görmeniz gerekir. Küme bir sanal ağda ise, URI özel URI olmalıdır. Açmak için, aynı sanal ağın parçası olan bir VM kullanın. Daha fazla bilgi için bkz. [Apache Hadoop hizmetlerine doğrudan bağlanma](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services).

<br/>

- **İleti**: `502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **Neden**: Bu hata HDInsight 'tan.

- **Öneri**: Bu hata HDInsight kümesinden. Daha fazla bilgi için bkz. [ambarı Kullanıcı arabirimi 502 hatası](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 Spark thrift sunucusuna bağlanma](https://hdinsight.github.io/spark/spark-thriftserver-errors.html) [502, Spark Thrift sunucusuna bağlanma](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)ve [Application Gateway hatalı ağ geçidi hatalarıyla ilgili sorunları giderme](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br/>

- **İleti**: `java.lang.NullPointerException`

- **Neden**: Iş bir Spark kümesine gönderildiğinde bu hata oluşur. 

- **Öneri**: Bu özel durum HDInsight 'tan gelir. Asıl sorunu gizler. Destek için HDInsight ekibine başvurun. Bunları küme adı ve etkinlik çalışma zamanı aralığı ile birlikte sağlayın.


### <a name="error-code--2347"></a>Hata kodu: 2347

- **İleti**: `Hadoop job failed with exit code '5'. See 'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **Neden**: iş HDInsight 'a gönderildi ve HDInsight üzerinde başarısız oldu.

- **Öneri**: iş HDInsight 'a başarıyla gönderildi. Kümede başarısız oldu. İş ve günlükleri HDInsight ambarı Kullanıcı arabiriminde açın ya da hata iletisi önerirken dosyayı depolamadan açın. Dosya hata ayrıntılarını gösterir.


### <a name="error-code--2328"></a>Hata kodu: 2328

- **İleti**: `Internal server error occurred while processing the request. Please retry the request or contact support. `

- **Neden**: Bu hata HDInsight 'ta istek üzerine oluşur.

- **Öneri**: Bu hata HDInsight hizmetinden, HDInsight sağlama başarısız olduğunda gelir. HDInsight ekibine başvurun ve isteğe bağlı küme adını sağlayın.



## <a name="web-activity"></a>Web Etkinliği

### <a name="error-code--2108"></a>Hata kodu: 2108

- **İleti**: `Invalid HttpMethod: '...'.`

- **Neden**: Web etkinliği, etkinlik YÜKÜNDE belirtilen http yöntemini desteklemiyor.

- **Öneri**: desteklenen HTTP yöntemleri konur, Post, al ve Sil.

<br/>

- **İleti**: `Invalid Server Error 500.`

- **Neden**: uç noktada iç hata.

- **Öneri**: URL 'deki işlevselliği denetlemek Için Fiddler veya Postman kullanın.

<br/>

- **İleti**: `Unauthorized 401.`

- **Neden**: istekte geçerli kimlik doğrulaması yok.

- **Öneri**: belirtecin geçerliliği bitmiş olabilir. Geçerli bir kimlik doğrulama yöntemi sağlayın. URL 'deki işlevselliği denetlemek için Fiddler veya Postman kullanın.

<br/>

- **İleti**: `Forbidden 403.`

- **Neden**: gerekli izinler eksik.

- **Öneri**: erişilen kaynaktaki Kullanıcı izinlerini denetleyin. URL 'deki işlevselliği denetlemek için Fiddler veya Postman kullanın.

<br/>

- **İleti**: `Bad Request 400.`

- **Neden**: geçersiz http isteği.

- **Öneri**: isteğin URL 'sini, fiilini ve gövdesini denetleyin. İsteği doğrulamak için Fiddler veya Postman kullanın.

<br/>

- **İleti**: `Not found 404.` 

- **Neden**: kaynak bulunamadı.   

- **Öneri**: isteği doğrulamak Için Fiddler veya Postman kullanın.

<br/>

- **İleti**: `Service unavailable.`

- **Neden**: hizmet kullanılamıyor.

- **Öneri**: isteği doğrulamak Için Fiddler veya Postman kullanın.

<br/>

- **İleti**: `Unsupported Media Type.`

- **Neden**: Içerik türü Web etkinliği gövdesi ile eşleşmiyor.

- **Öneri**: yük biçimiyle eşleşen içerik türünü belirtin. İsteği doğrulamak için Fiddler veya Postman kullanın.

<br/>

- **İleti**: `The resource you are looking for has been removed, has had its name changed, or is temporarily unavailable.`

- **Neden**: kaynak kullanılamıyor. 

- **Öneri**: uç noktayı denetlemek Için Fiddler veya Postman kullanın.

<br/>

- **İleti**: `The page you are looking for cannot be displayed because an invalid method (HTTP verb) is being used.`

- **Neden**: istekte yanlış bir Web etkinliği yöntemi belirtildi.

- **Öneri**: uç noktayı denetlemek Için Fiddler veya Postman kullanın.

<br/>

- **İleti**: `invalid_payload`

- **Neden**: Web etkinliği gövdesi yanlış.

- **Öneri**: uç noktayı denetlemek Için Fiddler veya Postman kullanın.


### <a name="error-code--2208"></a>Hata kodu: 2208

- **İleti**: `Invoking Web Activity failed with HttpStatusCode - {0}.`

- **Neden**: hedef hizmet hata durumunu döndürdü.

- **Öneri**: isteği doğrulamak Için Fiddler/Postman kullanın.


### <a name="error-code--2308"></a>Hata kodu: 2308

- **İleti**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Neden**: ağ BAĞLANTıSı, DNS hatası, sunucu sertifikası doğrulama veya zaman aşımı gibi bu hatanın birden çok nedeni olabilir.

- **Öneri**: isteği doğrulamak Için Fiddler/Postman kullanın.


Fiddler 'i kullanarak izlenen Web uygulamasının HTTP oturumunu oluşturma:

1. [Fiddler](https://www.telerik.com/download/fiddler)'i indirin, yükleyin ve açın.

1. Web **UYGULAMANıZ https kullanıyorsa**, **Araçlar** >  > **Fiddler seçenekleri** ' ne gidin. **Yakalama HTTPS bağlantıları** ve **https trafiğinin şifresini çöz**' ü seçin. 
   
   ![Fiddler seçenekleri](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Uygulamanız SSL sertifikaları kullanıyorsa, Fiddler sertifikasını cihazınıza ekleyin. **HTTPS** > **eylemleri** > , > **kök sertifikayı masaüstüne dışarı aktarmak**Için **Araçlar** > **Fiddler seçenekleri** ' ne gidin.

1. **Dosya** > **yakalama trafiği**' ne giderek yakalamayı kapatın. Ya da **F12**tuşuna basın.

1. Tüm önbelleğe alınmış öğelerin kaldırılması ve yeniden indirilmesi gereken şekilde tarayıcınızın önbelleğini temizleyin.

1. İstek oluştur: 

   a. **Besteci** sekmesini seçin.

   b. HTTP yöntemini ve URL 'YI ayarlayın.

   c. Gerekiyorsa üstbilgiler ve istek gövdesi ekleyin.

   d. **Yürüt**’ü seçin.

9. Trafiği yeniden yakalamayı açın ve sayfada sorunlu işlemi doldurun.

10. **Tüm oturumları** > **kaydetmek** > **Dosya** ' ya gidin.

Daha fazla bilgi için bkz. [Fiddler ile çalışmaya](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)başlama.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla sorun giderme Yardım için şu kaynakları deneyin:

*  [Data Factory blogu](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory Özellik istekleri](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videoları](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN forumu](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory için Stack Overflow Forumu](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory hakkında Twitter bilgileri](https://twitter.com/hashtag/DataFactory)



