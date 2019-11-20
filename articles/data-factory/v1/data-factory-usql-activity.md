---
title: U-SQL betiği kullanarak verileri dönüştürme-Azure
description: Azure Data Lake Analytics işlem hizmetinde U-SQL betikleri çalıştırarak verileri nasıl işleyeceğini veya dönüştüreceğinizi öğrenin.
services: data-factory
documentationcenter: ''
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/01/2017
author: nabhishek
ms.author: abnarain
manager: craigg
robots: noindex
ms.openlocfilehash: 7608719c4e0c2b9e23f1982efda9789d25f50224
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73665958"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Azure Data Lake Analytics 'de U-SQL betikleri çalıştırarak verileri dönüştürme 
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-usql-activity.md)
> * [Sürüm 2 (geçerli sürüm)](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [v2 'de U-SQL etkinliği](../transform-data-using-data-lake-analytics.md).

Azure Data Factory 'deki bir işlem hattı bağlı işlem hizmetlerini kullanarak bağlı depolama hizmetlerindeki verileri işler. Her etkinliğin belirli bir işleme işlemi gerçekleştirdiği bir etkinlik dizisi içerir. Bu makalede, **Azure Data Lake Analytics** işlem bağlantılı hizmetinde bir **u-sql** betiği ÇALıŞTıRAN **Data Lake Analytics u-SQL etkinliği** açıklanmaktadır. 

Data Lake Analytics U-SQL etkinliğine sahip bir işlem hattı oluşturmadan önce bir Azure Data Lake Analytics hesabı oluşturun. Azure Data Lake Analytics hakkında bilgi edinmek için bkz. [Azure Data Lake Analytics kullanmaya başlama](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Veri Fabrikası, bağlı hizmetler, veri kümeleri ve işlem hattı oluşturma adımları için [ilk işlem hattınızı derleme öğreticisini](data-factory-build-your-first-pipeline.md) inceleyin. Data Factory varlıkları oluşturmak için Data Factory Düzenleyicisi veya Visual Studio veya Azure PowerShell ile JSON kod parçacıklarını kullanın.

## <a name="supported-authentication-types"></a>Desteklenen kimlik doğrulama türleri
U-SQL etkinliği Data Lake Analytics göre kimlik doğrulama türlerini destekler:
* Hizmet sorumlusu kimlik doğrulaması
* Kullanıcı kimlik bilgileri (OAuth) kimlik doğrulaması 

Özellikle zamanlanmış bir U-SQL yürütmesi için hizmet sorumlusu kimlik doğrulamasını kullanmanızı öneririz. Belirteç süre sonu davranışı, Kullanıcı kimlik doğrulaması kimlik doğrulamasıyla oluşabilir. Yapılandırma ayrıntıları için bkz. [bağlı hizmet özellikleri](#azure-data-lake-analytics-linked-service) bölümü.

## <a name="azure-data-lake-analytics-linked-service"></a>Bağlı hizmet Azure Data Lake Analytics
Bir Azure Data Lake Analytics işlem hizmetini bir Azure Data Factory 'ye bağlamak için **Azure Data Lake Analytics** bağlı bir hizmet oluşturursunuz. İşlem hattındaki Data Lake Analytics U-SQL etkinliği, bu bağlı hizmeti ifade eder. 

Aşağıdaki tabloda JSON tanımında kullanılan genel özellikler için açıklamalar verilmiştir. Hizmet sorumlusu ve Kullanıcı kimlik bilgisi kimlik doğrulaması arasında daha fazla seçim yapabilirsiniz.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| **type** |Type özelliği: **AzureDataLakeAnalytics**olarak ayarlanmalıdır. |Evet |
| **Adı** |Azure Data Lake Analytics hesap adı. |Evet |
| **Datalakeanaliz Ticsurı** |Azure Data Lake Analytics URI 'SI. |Hayır |
| **SubscriptionID** |Azure abonelik kimliği |Hayır (belirtilmemişse, Veri Fabrikası aboneliği kullanılır). |
| **resourceGroupName** |Azure kaynak grubu adı |Hayır (belirtilmemişse, veri fabrikasının kaynak grubu kullanılır). |

### <a name="service-principal-authentication-recommended"></a>Hizmet sorumlusu kimlik doğrulaması (önerilir)
Hizmet sorumlusu kimlik doğrulamasını kullanmak için, bir uygulama varlığını Azure Active Directory (Azure AD) olarak kaydedin ve Data Lake Store erişim izni verin. Ayrıntılı adımlar için bkz. [hizmetten hizmete kimlik doğrulaması](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Bağlı hizmeti tanımlamak için kullandığınız aşağıdaki değerleri unutmayın:
* Uygulama Kimliği
* Uygulama anahtarı 
* Kiracı Kimliği

Aşağıdaki özellikleri belirterek hizmet sorumlusu kimlik doğrulamasını kullanın:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| **Serviceprincipalıd** | Uygulamanın istemci KIMLIĞINI belirtin. | Evet |
| **Servicesprincipalkey** | Uygulamanın anahtarını belirtin. | Evet |
| **Kiracı** | Uygulamanızın altında bulunduğu kiracı bilgilerini (etki alanı adı veya kiracı KIMLIĞI) belirtin. Fareyi, Azure portal sağ üst köşesine getirerek alabilirsiniz. | Evet |

**Örnek: hizmet sorumlusu kimlik doğrulaması**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Kullanıcı kimlik bilgisi kimlik doğrulaması
Alternatif olarak, aşağıdaki özellikleri belirterek Data Lake Analytics için Kullanıcı kimlik bilgileri kimlik doğrulaması kullanabilirsiniz:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| **yetkisi** | Data Factory düzenleyicisinde **Yetkilendir** düğmesine tıklayın ve bu özelliğe otomatik olarak oluşturulan yetkilendirme URL 'sini atayan kimlik bilgilerinizi girin. | Evet |
| **Kimliği** | OAuth yetkilendirme oturumundan gelen OAuth oturum KIMLIĞI. Her oturum KIMLIĞI benzersizdir ve yalnızca bir kez kullanılabilir. Bu ayar Data Factory düzenleyicisini kullandığınızda otomatik olarak üretilir. | Evet |

**Örnek: Kullanıcı kimlik bilgisi kimlik doğrulaması**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Belirteç süre sonu
**Yetkilendir** düğmesini kullanarak oluşturduğunuz yetkilendirme kodu bir süre sonra dolar. Farklı kullanıcı hesabı türlerinin sona erme zamanları için aşağıdaki tabloya bakın. Kimlik doğrulama **belirtecinin süresi dolarsa**aşağıdaki hata iletisini görebilirsiniz: kimlik bilgisi işlemi hatası: INVALID_GRANT-AADSTS70002: kimlik bilgileri doğrulanırken hata oluştu. AADSTS70008: belirtilen erişim izni, zaman aşımına uğradı veya iptal edildi. İzleme KIMLIĞI: d18629e8-af88-43c5-88e3-d8419eb1fca1 bağıntı KIMLIĞI: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 zaman damgası: 2015-12-15 21:09:31Z

| Kullanıcı türü | Süre sonu |
|:--- |:--- |
| Azure Active Directory tarafından yönetilmeyen Kullanıcı hesapları (@hotmail.com, @live.com, vb.) |12 saat |
| Azure Active Directory tarafından yönetilen Kullanıcı hesapları (AAD) |Son dilimin çalıştırıldıktan 14 gün sonra. <br/><br/>90 gün, OAuth tabanlı bağlı hizmete dayalı bir dilim 14 günde bir en az bir kez çalışır. |

Bu hatayı önlemek/çözmek için, **belirtecin süresi dolduktan** sonra Yetkilendir düğmesini kullanarak yeniden **Yetkilendirme** yapın ve bağlı hizmeti yeniden dağıtın. Ayrıca, kod kullanarak program aracılığıyla **SessionID** ve **Yetkilendirme** özellikleri için aşağıdaki gibi değerler de oluşturabilirsiniz:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Kodda kullanılan Data Factory sınıfları hakkında daha fazla bilgi için bkz. [AzureDataLakeStoreLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)ve [authorizationsessiongetresponse sınıfı](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) konuları. WindowsFormsWebAuthenticationDialog sınıfı için Microsoft. IdentityModel. clients. ActiveDirectory. WindowsForms. dll öğesine bir başvuru ekleyin. 

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL Etkinliği
Aşağıdaki JSON kod parçacığı bir Data Lake Analytics U-SQL etkinliğine sahip bir işlem hattı tanımlıyor. Etkinlik tanımı, daha önce oluşturduğunuz Azure Data Lake Analytics bağlı hizmete bir başvuru içerir.   

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        "activities": 
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs": 
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00Z",
        "end": "2015-08-08T01:00:00Z",
        "isPaused": false
    }
}
```

Aşağıdaki tabloda, bu etkinliğe özgü özelliklerin adları ve açıklamaları açıklanmaktadır. 

| Özellik            | Açıklama                              | Gerekli                                 |
| :------------------ | :--------------------------------------- | :--------------------------------------- |
| type                | Type özelliği **Datalakeanalticsu-SQL**olarak ayarlanmalıdır. | Evet                                      |
| linkedServiceName   | Data Factory bağlı hizmet olarak kaydedilen Azure Data Lake Analytics başvurusu | Evet                                      |
| scriptPath          | U-SQL betiğini içeren klasörün yolu. Dosyanın adı büyük/küçük harfe duyarlıdır. | Hayır (komut dosyası kullanıyorsanız)                   |
| scriptLinkedService | Betiği içeren depolamayı Data Factory 'ye bağlayan bağlantılı hizmet | Hayır (komut dosyası kullanıyorsanız)                   |
| betiğini çalıştırın              | ScriptPath ve scriptLinkedService belirtmek yerine satır içi betiği belirtin. Örneğin: `"script": "CREATE DATABASE test"`. | Hayır (scriptPath ve scriptLinkedService kullanıyorsanız) |
| Analyticsunits | İşi çalıştırmak için eşzamanlı olarak kullanılan en fazla düğüm sayısı. | Hayır                                       |
| Priority            | Önce kuyruğa alınan tüm işlerin ne kadar önce çalıştırılacağını belirler. Sayı ne kadar düşükse öncelik o kadar yüksektir. | Hayır                                       |
| parametreler          | U-SQL betiği için parametreler          | Hayır                                       |
| runtimeVersion      | Kullanılacak U-SQL altyapısının çalışma zamanı sürümü | Hayır                                       |
| compilationMode     | <p>U-SQL derleme modu. Şu değerlerden biri olmalıdır:</p> <ul><li>**Anlam:** Yalnızca anlam denetimleri ve gerekli sağlamlık denetimleri gerçekleştirin.</li><li>**Tam:** Sözdizimi denetimi, iyileştirme, kod oluşturma vb. dahil olmak üzere tam derlemeyi gerçekleştirin.</li><li>**Tekbox:** Tam derlemeyi, TargetType ayarını SingleBox ile birlikte gerçekleştirin.</li></ul><p>Bu özellik için bir değer belirtmezseniz, sunucu en uygun derleme modunu belirler. </p> | Hayır                                       |

Betik tanımı için [Searchlogprocessing. txt betik tanımına](#sample-u-sql-script) bakın. 

## <a name="sample-input-and-output-datasets"></a>Örnek giriş ve çıkış veri kümeleri
### <a name="input-dataset"></a>Giriş veri kümesi
Bu örnekte, giriş verileri datalake/Input klasöründeki bir Azure Data Lake Store (SearchLog. tsv dosyasında bulunur). 

```json
{
    "name": "DataLakeTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}    
```

### <a name="output-dataset"></a>Çıkış veri kümesi
Bu örnekte, U-SQL betiği tarafından üretilen çıkış verileri bir Azure Data Lake Store depolanır (datalake/output klasörü). 

```json
{
    "name": "EventsByRegionTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="sample-data-lake-store-linked-service"></a>Örnek Data Lake Store bağlı hizmeti
Giriş/çıkış veri kümeleri tarafından kullanılan örnek Azure Data Lake Store bağlantılı hizmetin tanımı aşağıda verilmiştir. 

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
        }
    }
}
```

JSON özelliklerinin açıklamaları için bkz. Azure Data Lake Store makalesine ve bu makaleye [verileri taşıma](data-factory-azure-datalake-connector.md) . 

## <a name="sample-u-sql-script"></a>Örnek U-SQL betiği

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

U-SQL betikindeki **\@** ve **\@Out** parametrelerine ait değerler, ' Parameters ' bölümü kullanılarak ADF tarafından dinamik olarak geçirilir. İşlem hattı tanımındaki ' Parametreler ' bölümüne bakın.

Azure Data Lake Analytics hizmeti üzerinde çalışan işler için ardışık düzen tanımınızda, Degreeofparalellik ve öncelik gibi diğer özellikleri de belirtebilirsiniz.

## <a name="dynamic-parameters"></a>Dinamik parametreler
Örnek işlem hattı tanımında, ve out parametreleri sabit kodlanmış değerlerle atanır. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

Bunun yerine dinamik Parametreler kullanmak mümkündür. Örneğin: 

```json
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

Bu durumda, giriş dosyaları yine/datalake/Input klasöründen çekilir ve çıkış dosyaları/datalake/output klasöründe oluşturulur. Dosya adları, dilim başlangıç zamanına göre dinamiktir.  


