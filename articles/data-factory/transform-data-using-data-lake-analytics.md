---
title: U-SQL betiği kullanarak verileri dönüştürme-Azure
description: Azure Data Lake Analytics işlem hizmetinde U-SQL betikleri çalıştırarak verileri nasıl işleyeceğini veya dönüştüreceğinizi öğrenin.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: abnarain
ms.openlocfilehash: 53fb6773becff9f76c9658171965fbd148e94bc8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683865"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Azure Data Lake Analytics 'de U-SQL betikleri çalıştırarak verileri dönüştürme 
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-usql-activity.md)
> * [Geçerli sürüm](transform-data-using-data-lake-analytics.md)

Azure Data Factory 'deki bir işlem hattı bağlı işlem hizmetlerini kullanarak bağlı depolama hizmetlerindeki verileri işler. Her etkinliğin belirli bir işleme işlemi gerçekleştirdiği bir etkinlik dizisi içerir. Bu makalede, **Azure Data Lake Analytics** işlem bağlantılı hizmetinde bir **u-sql** betiği ÇALıŞTıRAN **Data Lake Analytics u-SQL etkinliği** açıklanmaktadır. 

Data Lake Analytics U-SQL etkinliğine sahip bir işlem hattı oluşturmadan önce bir Azure Data Lake Analytics hesabı oluşturun. Azure Data Lake Analytics hakkında bilgi edinmek için bkz. [Azure Data Lake Analytics kullanmaya başlama](../data-lake-analytics/data-lake-analytics-get-started-portal.md).


## <a name="azure-data-lake-analytics-linked-service"></a>Bağlı hizmet Azure Data Lake Analytics
Bir Azure Data Lake Analytics işlem hizmetini bir Azure Data Factory 'ye bağlamak için **Azure Data Lake Analytics** bağlı bir hizmet oluşturursunuz. İşlem hattındaki Data Lake Analytics U-SQL etkinliği, bu bağlı hizmeti ifade eder. 

Aşağıdaki tabloda JSON tanımında kullanılan genel özellikler için açıklamalar verilmiştir. 

| Özellik                 | Açıklama                              | Gerekli                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **type**                 | Type özelliği: **AzureDataLakeAnalytics**olarak ayarlanmalıdır. | Evet                                      |
| **Adı**          | Azure Data Lake Analytics hesap adı.  | Evet                                      |
| **Datalakeanaliz Ticsurı** | Azure Data Lake Analytics URI 'SI.           | Hayır                                       |
| **SubscriptionID**       | Azure abonelik KIMLIĞI                    | Hayır                                       |
| **resourceGroupName**    | Azure kaynak grubu adı                | Hayır                                       |

### <a name="service-principal-authentication"></a>Hizmet sorumlusu kimlik doğrulaması
Azure Data Lake Analytics bağlı hizmeti, Azure Data Lake Analytics hizmetine bağlanmak için bir hizmet sorumlusu kimlik doğrulaması gerektirir. Hizmet sorumlusu kimlik doğrulamasını kullanmak için Azure Active Directory (Azure AD) ' a bir uygulama varlığı kaydedin ve bu kullanıcıya hem Data Lake Analytics hem de kullandığı Data Lake Store erişim izni verin. Ayrıntılı adımlar için bkz. [hizmetten hizmete kimlik doğrulaması](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Bağlı hizmeti tanımlamak için kullandığınız aşağıdaki değerleri unutmayın:

* Uygulama Kimliği
* Uygulama anahtarı 
* Kiracı Kimliği

[Kullanıcı Ekleme Sihirbazı 'nı](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user)kullanarak Azure Data Lake Anatlyıcs 'nize hizmet sorumlusu izni verin.

Aşağıdaki özellikleri belirterek hizmet sorumlusu kimlik doğrulamasını kullanın:

| Özellik                | Açıklama                              | Gerekli |
| :---------------------- | :--------------------------------------- | :------- |
| **Serviceprincipalıd**  | Uygulamanın istemci KIMLIĞINI belirtin.     | Evet      |
| **Servicesprincipalkey** | Uygulamanın anahtarını belirtin.           | Evet      |
| **Kiracı**              | Uygulamanızın altında bulunduğu kiracı bilgilerini (etki alanı adı veya kiracı KIMLIĞI) belirtin. Fareyi, Azure portal sağ üst köşesine getirerek alabilirsiniz. | Evet      |

**Örnek: hizmet sorumlusu kimlik doğrulaması**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "<azure data lake analytics URI>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }       
    }
}
```

Bağlı hizmet hakkında daha fazla bilgi edinmek için bkz. [işlem bağlantılı hizmetleri](compute-linked-services.md).

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL Etkinliği
Aşağıdaki JSON kod parçacığı bir Data Lake Analytics U-SQL etkinliğine sahip bir işlem hattı tanımlıyor. Etkinlik tanımı, daha önce oluşturduğunuz Azure Data Lake Analytics bağlı hizmete bir başvuru içerir. Bir Data Lake Analytics U-SQL betiğini yürütmek için, Data Factory belirttiğiniz betiği Data Lake Analytics gönderir ve gerekli girişler ve çıktılar, komut Data Lake Analytics dosyasında getirme ve çıkış yapmak için tanımlanır. 

```json
{
    "name": "ADLA U-SQL Activity",
    "description": "description",
    "type": "DataLakeAnalyticsU-SQL",
    "linkedServiceName": {
        "referenceName": "<linked service name of Azure Data Lake Analytics>",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "<linked service name of Azure Data Lake Store or Azure Storage which contains the U-SQL script>",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
        "degreeOfParallelism": 3,
        "priority": 100,
        "parameters": {
            "in": "/datalake/input/SearchLog.tsv",
            "out": "/datalake/output/Result.tsv"
        }
    }   
}
```

Aşağıdaki tabloda, bu etkinliğe özgü özelliklerin adları ve açıklamaları açıklanmaktadır. 

| Özellik            | Açıklama                              | Gerekli |
| :------------------ | :--------------------------------------- | :------- |
| ad                | İşlem hattındaki etkinliğin adı     | Evet      |
| açıklama         | Etkinliğin ne yaptığını açıklayan metin.  | Hayır       |
| type                | Data Lake Analytics U-SQL etkinliği için etkinlik türü **Datalakeanalsu-SQL**' dir. | Evet      |
| linkedServiceName   | Azure Data Lake Analytics bağlı hizmet. Bu bağlı hizmet hakkında bilgi edinmek için bkz. [işlem bağlı hizmetleri](compute-linked-services.md) makalesi.  |Evet       |
| scriptPath          | U-SQL betiğini içeren klasörün yolu. Dosyanın adı büyük/küçük harfe duyarlıdır. | Evet      |
| scriptLinkedService | Komut dosyasını içeren **Azure Data Lake Store** veya **Azure depolama alanını** Data Factory 'ye bağlayan bağlantılı hizmet | Evet      |
| Analyticsunits | İşi çalıştırmak için eşzamanlı olarak kullanılan en fazla düğüm sayısı. | Hayır       |
| Priority            | Önce kuyruğa alınan tüm işlerin ne kadar önce çalıştırılacağını belirler. Sayı ne kadar düşükse öncelik o kadar yüksektir. | Hayır       |
| parametreler          | U-SQL betiğine geçirilecek parametreler.    | Hayır       |
| runtimeVersion      | Kullanılacak U-SQL altyapısının çalışma zamanı sürümü. | Hayır       |
| compilationMode     | <p>U-SQL derleme modu. Şu değerlerden biri olmalıdır: **anlam:** yalnızca anlam denetimleri ve gerekli sağlamlık denetimleri gerçekleştirin, **tam:** sözdizimi denetimi, iyileştirme, kod oluşturma, vb., **singlebox** dahil olmak üzere tam derlemeyi gerçekleştirin: tam olarak gerçekleştirin TargetType, Tekbox olarak TargetType ayarını içeren derleme. Bu özellik için bir değer belirtmezseniz, sunucu en uygun derleme modunu belirler. | Hayır |

Betik tanımı için [Searchlogprocessing. txt](#sample-u-sql-script) dosyasına bakın. 

## <a name="sample-u-sql-script"></a>Örnek U-SQL betiği

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int,
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

Yukarıdaki betik örneğinde, komut dosyasına giriş ve çıkış **\@içinde** ve **\@Out** parametrelerinde tanımlanmıştır. U-SQL betikindeki **\@** ve **\@Out** parametrelerine ait değerler, ' parameters ' bölümü kullanılarak Data Factory tarafından dinamik olarak geçirilir. 

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
    "in": "/datalake/input/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/data.tsv",
    "out": "/datalake/output/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/result.tsv"
}
```

Bu durumda, giriş dosyaları yine/datalake/Input klasöründen çekilir ve çıkış dosyaları/datalake/output klasöründe oluşturulur. Dosya adları, işlem hattı tetiklendiğinde geçirilen pencere başlangıç saatine göre dinamiktir.  

## <a name="next-steps"></a>Sonraki adımlar
Verileri başka yollarla nasıl dönüştürebileceğinizi açıklayan aşağıdaki makalelere bakın: 

* [Hive etkinliği](transform-data-using-hadoop-hive.md)
* [Pig etkinliği](transform-data-using-hadoop-pig.md)
* [MapReduce etkinliği](transform-data-using-hadoop-map-reduce.md)
* [Hadoop akışı etkinliği](transform-data-using-hadoop-streaming.md)
* [Spark etkinliği](transform-data-using-spark.md)
* [.NET özel etkinliği](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch yürütme etkinliği](transform-data-using-machine-learning.md)
* [Saklı yordam etkinliği](transform-data-using-stored-procedure.md)
