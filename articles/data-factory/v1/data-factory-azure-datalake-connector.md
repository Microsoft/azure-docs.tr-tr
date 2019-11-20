---
title: Azure Data Lake Storage 1. veri kopyalama
description: Azure Data Factory kullanarak Data Lake Store veri kopyalama hakkında bilgi edinin
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4cafc9cf67255d44e5c89947f3da8a7b7b3e4b5f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683168"
---
# <a name="copy-data-to-and-from-data-lake-storage-gen1-by-using-data-factory"></a>Data Factory kullanarak Data Lake Storage 1. veri kopyalama
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-azure-datalake-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-azure-data-lake-store.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, [v2 'de Azure Data Lake Storage 1. Bağlayıcısı](../connector-azure-data-lake-store.md)' na bakın.

Bu makalede, verileri Azure Data Lake Storage 1. 'ye taşımak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı açıklanmaktadır (daha önce Azure Data Lake Store olarak bilinir). [Veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde, kopyalama etkinliğiyle veri hareketine genel bir bakış için oluşturulur.

## <a name="supported-scenarios"></a>Desteklenen senaryolar
**Azure Data Lake Store** verileri aşağıdaki veri depolarına kopyalayabilirsiniz:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

**Azure Data Lake Store için**aşağıdaki veri depolarından veri kopyalayabilirsiniz:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Kopyalama etkinliğine sahip bir işlem hattı oluşturmadan önce bir Data Lake Store hesabı oluşturun. Daha fazla bilgi için bkz. [Azure Data Lake Store kullanmaya başlama](../../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="supported-authentication-types"></a>Desteklenen kimlik doğrulama türleri
Data Lake Store Bağlayıcısı bu kimlik doğrulama türlerini destekler:
* Hizmet sorumlusu kimlik doğrulaması
* Kullanıcı kimlik bilgileri (OAuth) kimlik doğrulaması

Özellikle zamanlanmış bir veri kopyası için hizmet sorumlusu kimlik doğrulaması kullanmanızı öneririz. Belirteç süre sonu davranışı, Kullanıcı kimlik doğrulaması kimlik doğrulamasıyla oluşabilir. Yapılandırma ayrıntıları için bkz. [bağlı hizmet özellikleri](#linked-service-properties) bölümü.

## <a name="get-started"></a>Başlarken
Farklı araçlar/API 'Ler kullanarak bir Azure Data Lake Store veri taşıyan kopyalama etkinliği ile bir işlem hattı oluşturabilirsiniz.

Verileri kopyalamak için bir işlem hattı oluşturmanın en kolay yolu **Kopyalama Sihirbazı**' nı kullanmaktır. Kopyalama Sihirbazı 'nı kullanarak bir işlem hattı oluşturmaya yönelik bir öğretici için bkz. [öğretici: kopyalama sihirbazını kullanarak işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md).

İşlem hattı oluşturmak için aşağıdaki araçları da kullanabilirsiniz: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager şablonu**, **.NET API**ve **REST API**. Kopyalama etkinliğine sahip bir işlem hattı oluşturmak için adım adım yönergeler için bkz. [kopyalama etkinliği öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Araçları veya API 'Leri kullanıp kullanmayacağınızı bir kaynak veri deposundan havuz veri deposuna veri taşınan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirirsiniz:

1. Bir **Veri Fabrikası**oluşturun. Bir veri fabrikası bir veya daha fazla işlem hattı içerebilir.
2. Giriş ve çıkış veri depolarını veri fabrikanıza bağlamak için **bağlı hizmetler** oluşturun. Örneğin, bir Azure Blob depolamadan Azure Data Lake Store veri kopyalıyorsanız, Azure depolama hesabınızı ve Azure Data Lake deponuzu veri fabrikanıza bağlamak için iki bağlı hizmet oluşturursunuz. Azure Data Lake Store özgü bağlı hizmet özellikleri için bkz. [bağlı hizmet özellikleri](#linked-service-properties) bölümü.
2. Kopyalama işlemi için girdi ve çıktı verilerini temsil edecek **veri kümeleri** oluşturun. Son adımda bahsedilen örnekte, blob kapsayıcısını ve girdi verilerini içeren klasörü belirtmek için bir veri kümesi oluşturursunuz. Ve, blob depolamadan kopyalanmış verileri tutan Data Lake deposundaki klasör ve dosya yolunu belirtmek için başka bir veri kümesi oluşturursunuz. Azure Data Lake Store özgü veri kümesi özellikleri için bkz. [veri kümesi özellikleri](#dataset-properties) bölümü.
3. Bir veri kümesini girdi olarak ve bir veri kümesini çıkış olarak alan kopyalama etkinliği ile bir işlem **hattı** oluşturun. Daha önce bahsedilen örnekte, BlobSource değerini kaynak olarak ve AzureDataLakeStoreSink olarak kopyalama etkinliği için havuz olarak kullanırsınız. Benzer şekilde, Azure Data Lake Store 'den Azure Blob depolamaya kopyalama yapıyorsanız kopyalama etkinliğinde AzureDataLakeStoreSource ve BlobSink kullanın. Azure Data Lake Store özgü kopyalama etkinliği özellikleri için bkz. [kopyalama etkinliği özellikleri](#copy-activity-properties) bölümü. Bir veri deposunu kaynak veya havuz olarak kullanma hakkında ayrıntılı bilgi için, veri deponuzdaki önceki bölümde yer alan bağlantıya tıklayın.

Sihirbazı kullandığınızda, bu Data Factory varlıkların JSON tanımları (bağlı hizmetler, veri kümeleri ve işlem hattı) sizin için otomatik olarak oluşturulur. Araçlar/API 'Leri (.NET API hariç) kullandığınızda, bu Data Factory varlıkları JSON biçimini kullanarak tanımlarsınız. Azure Data Lake Store veri kopyalamak için kullanılan Data Factory varlıkların JSON tanımlarına sahip örnekler için, bu makalenin [JSON örnekleri](#json-examples-for-copying-data-to-and-from-data-lake-store) bölümüne bakın.

Aşağıdaki bölümler Data Lake Store özgü Data Factory varlıkları tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılı bilgi sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri
Bağlı hizmet bir veri deposunu veri fabrikasına bağlar. Data Lake Store verilerinizi veri fabrikanıza bağlamak için **AzureDataLakeStore** türünde bağlı bir hizmet oluşturursunuz. Aşağıdaki tabloda Data Lake Store bağlı hizmetlere özgü JSON öğeleri açıklanmaktadır. Hizmet sorumlusu ve Kullanıcı kimlik bilgisi kimlik doğrulaması arasında seçim yapabilirsiniz.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| **type** | Type özelliği **AzureDataLakeStore**olarak ayarlanmalıdır. | Evet |
| **dataLakeStoreUri** | Azure Data Lake Store hesabıyla ilgili bilgiler. Bu bilgiler şu biçimlerden birini alır: `https://[accountname].azuredatalakestore.net/webhdfs/v1` veya `adl://[accountname].azuredatalakestore.net/`. | Evet |
| **SubscriptionID** | Data Lake Store hesabının ait olduğu Azure abonelik KIMLIĞI. | Havuz için gerekli |
| **resourceGroupName** | Data Lake Store hesabının ait olduğu Azure Kaynak grubu adı. | Havuz için gerekli |

### <a name="service-principal-authentication-recommended"></a>Hizmet sorumlusu kimlik doğrulaması (önerilir)
Hizmet sorumlusu kimlik doğrulamasını kullanmak için, bir uygulama varlığını Azure Active Directory (Azure AD) olarak kaydedin ve Data Lake Store erişim izni verin. Ayrıntılı adımlar için bkz. [hizmetten hizmete kimlik doğrulaması](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Bağlı hizmeti tanımlamak için kullandığınız aşağıdaki değerleri unutmayın:
* Uygulama Kimliği
* Uygulama anahtarı
* Kiracı Kimliği

> [!IMPORTANT]
> Azure Data Lake Store ' de hizmet sorumlusu uygun iznini verdiğinizden emin olun:
>- **Kaynak olarak Data Lake Store kullanmak için**, bir klasörün içeriğini listelemek ve kopyalamak üzere en az **okuma ve yürütme** izni verin ya da tek bir dosyayı kopyalamak için **Oku** iznini verin. Hesap düzeyinde erişim denetimi gereksinimi yoktur.
>- **Havuz olarak Data Lake Store kullanmak için**, klasörde alt öğeler oluşturmak üzere en az **yazma + yürütme** veri erişimi izni verin. Kopyalama sağlamak için Azure IR kullanırsanız (hem kaynak hem de havuz bulutta bulunur), Data Factory Data Lake Store bölgesini algılamaya izin vermek için hesap erişim denetimi 'nde (ıAM) en az **okuyucu** rolü verin. Bu ıAM rolünü önlemek istiyorsanız, kopyalama etkinliğinde Data Lake Store konum konumuyla birlikte [executionLocation](data-factory-data-movement-activities.md#global) ' ı belirtin.
>- İşlem **hatlarını yazmak Için kopyalama Sihirbazı 'nı kullanıyorsanız**, hesap erişim denetimi 'NDE (IAM) en az **okuyucu** rolü verin. Ayrıca, Data Lake Store köke ("/") ve alt öğelerine en az **okuma ve yürütme** izni verin. Aksi takdirde "belirtilen kimlik bilgileri geçersiz." iletisini görebilirsiniz.

Aşağıdaki özellikleri belirterek hizmet sorumlusu kimlik doğrulamasını kullanın:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| **Serviceprincipalıd** | Uygulamanın istemci KIMLIĞINI belirtin. | Evet |
| **Servicesprincipalkey** | Uygulamanın anahtarını belirtin. | Evet |
| **Kiracı** | Uygulamanızın altında bulunduğu kiracı bilgilerini (etki alanı adı veya kiracı KIMLIĞI) belirtin. Fareyi, Azure portal sağ üst köşesine getirerek alabilirsiniz. | Evet |

**Örnek: hizmet sorumlusu kimlik doğrulaması**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Kullanıcı kimlik bilgisi kimlik doğrulaması
Alternatif olarak, aşağıdaki özellikleri belirterek veya Data Lake Store kopyalamak için Kullanıcı kimlik bilgisi kimlik doğrulamasını kullanabilirsiniz:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| **yetkisi** | Data Factory düzenleyicisinde **Yetkilendir** düğmesine tıklayın ve bu özelliğe otomatik olarak oluşturulan yetkilendirme URL 'sini atayan kimlik bilgilerinizi girin. | Evet |
| **Kimliği** | OAuth yetkilendirme oturumundan gelen OAuth oturum KIMLIĞI. Her oturum KIMLIĞI benzersizdir ve yalnızca bir kez kullanılabilir. Bu ayar Data Factory düzenleyicisini kullandığınızda otomatik olarak üretilir. | Evet |

> [!IMPORTANT]
> Azure Data Lake Store ' de kullanıcıya uygun izni verdiğinizden emin olun:
>- **Kaynak olarak Data Lake Store kullanmak için**, bir klasörün içeriğini listelemek ve kopyalamak üzere en az **okuma ve yürütme** izni verin ya da tek bir dosyayı kopyalamak için **Oku** iznini verin. Hesap düzeyinde erişim denetimi gereksinimi yoktur.
>- **Havuz olarak Data Lake Store kullanmak için**, klasörde alt öğeler oluşturmak üzere en az **yazma + yürütme** veri erişimi izni verin. Kopyalama sağlamak için Azure IR kullanırsanız (hem kaynak hem de havuz bulutta bulunur), Data Factory Data Lake Store bölgesini algılamaya izin vermek için hesap erişim denetimi 'nde (ıAM) en az **okuyucu** rolü verin. Bu ıAM rolünü önlemek istiyorsanız, kopyalama etkinliğinde Data Lake Store konum konumuyla birlikte [executionLocation](data-factory-data-movement-activities.md#global) ' ı belirtin.
>- İşlem **hatlarını yazmak Için kopyalama Sihirbazı 'nı kullanıyorsanız**, hesap erişim denetimi 'NDE (IAM) en az **okuyucu** rolü verin. Ayrıca, Data Lake Store köke ("/") ve alt öğelerine en az **okuma ve yürütme** izni verin. Aksi takdirde "belirtilen kimlik bilgileri geçersiz." iletisini görebilirsiniz.

**Örnek: Kullanıcı kimlik bilgisi kimlik doğrulaması**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

#### <a name="token-expiration"></a>Belirteç süre sonu
**Yetkilendir** düğmesini kullanarak oluşturduğunuz yetkilendirme kodu belirli bir süre sonra dolar. Aşağıdaki ileti, kimlik doğrulama belirtecinin süresi dolduğunda anlamına gelir:

Kimlik bilgisi işlemi hatası: invalid_grant-AADSTS70002: kimlik bilgileri doğrulanırken hata oluştu. AADSTS70008: belirtilen erişim izni, zaman aşımına uğradı veya iptal edildi. İzleme KIMLIĞI: d18629e8-af88-43c5-88e3-d8419eb1fca1 bağıntı KIMLIĞI: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 zaman damgası: 2015-12-15 21-09-31Z.

Aşağıdaki tabloda farklı kullanıcı hesabı türlerinin sona erme zamanları gösterilmektedir:

| Kullanıcı türü | Süre sonu |
|:--- |:--- |
| Azure Active Directory tarafından *yönetilmeyen Kullanıcı* hesapları (örneğin, @hotmail.com veya @live.com) |12 saat |
| Azure Active Directory tarafından yönetilen Kullanıcı hesapları |Son dilimin çalıştırıldıktan 14 gün sonra <br/><br/>90 gün, OAuth tabanlı bağlı bir hizmete dayalı bir dilim 14 günde bir en az bir kez çalışırsa |

Parolanızı belirtecin süre sonu zamanından önce değiştirirseniz, belirtecin süresi hemen dolar. Bu bölümün önceki kısımlarında bahsedilen iletiyi görürsünüz.

Belirteç, bağlı hizmeti yeniden dağıtmak için zaman aşımına uğradığında **Yetkilendir** düğmesini kullanarak hesabı yeniden yetkilendirmek için kullanabilirsiniz. Aşağıdaki kodu kullanarak, program aracılığıyla **SessionID** ve **Yetkilendirme** özellikleri için değerler de oluşturabilirsiniz:


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
Kodda kullanılan Data Factory sınıfları hakkında daha fazla bilgi için bkz. [AzureDataLakeStoreLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)ve [authorizationsessiongetresponse sınıfı](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) konuları. Kodda kullanılan `WindowsFormsWebAuthenticationDialog` sınıfı için `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` sürüm `2.9.10826.1824` bir başvuru ekleyin.

## <a name="troubleshooting-tips"></a>Sorun giderme ipuçları

**Belirti:** **Verileri Azure Data Lake Store** kopyalanırken kopyalama etkinliğinizi aşağıdaki hatayla başarısız olur:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Kök nedeni:** 2 olası neden vardır:

1. Azure Data Lake Store bağlantılı hizmette belirtilen `resourceGroupName` ve/veya `subscriptionId` yanlış;
2. Kullanıcı veya hizmet sorumlusu gerekli izne sahip değil.

**Çözünürlüğüne**

1. Bağlı hizmette belirttiğiniz `subscriptionId` ve `resourceGroupName` gerçekten Data Lake hesabınızın ait olduğu `typeProperties` olduğundan emin olun.

2. Data Lake hesabındaki Kullanıcı veya hizmet sorumlusu için en az **okuyucu** rolü verdiğinizden emin olun. Bunun nasıl yapılacağı aşağıda verilmiştir:

    1. Data Lake Store hesabınıza > Azure portal gidin
    2. Data Lake Store dikey penceresinde **erişim denetimi (IAM)** öğesine tıklayın
    3. **Rol ataması Ekle** ' ye tıklayın
    4. **Rolü** **okuyucu**olarak ayarlayın ve erişim izni vermek için, kopyalamak üzere kullandığınız kullanıcıyı veya hizmet sorumlusunu seçin

3. Kullanıcıya veya hizmet sorumlusuna **okuyucu** rolü vermek istemiyorsanız, alternatif olarak, kopyalama etkinliğinde Data Lake Store konumuyla birlikte [bir yürütme konumu belirtmektir](data-factory-data-movement-activities.md#global) . Örnek:

    ```json
    {
      "name": "CopyToADLS",
      "type": "Copy",
      ......
      "typeProperties": {
        "source": {
          "type": "<source type>"
        },
        "sink": {
          "type": "AzureDataLakeStoreSink"
        },
        "exeuctionLocation": "West US"
      }
    }
    ```

## <a name="dataset-properties"></a>Veri kümesi özellikleri
Data Lake Store giriş verilerini temsil edecek bir veri kümesi belirtmek için, veri kümesinin **Type** özelliğini **AzureDataLakeStore**olarak ayarlarsınız. Veri kümesinin **Linkedservicename** özelliğini Data Lake Store bağlantılı hizmetin adı olarak ayarlayın. Veri kümelerini tanımlamaya yönelik JSON bölümlerinin ve özelliklerinin tam listesi için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md) makalesi. **Yapı**, **kullanılabilirlik**ve **ilke**gibi JSON 'daki veri kümesinin bölümleri, tüm veri KÜMESI türleri (örneğin, Azure SQL veritabanı, Azure Blob ve Azure tablosu) için benzerdir. **Typeproperties** bölümü her bir veri kümesi türü için farklıdır ve veri deposundaki verilerin konumu ve biçimi gibi bilgiler sağlar.

**AzureDataLakeStore** türündeki bir veri kümesi Için **typeproperties** bölümü aşağıdaki özellikleri içerir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| **folderPath** |Data Lake Store kapsayıcı ve klasörün yolu. |Evet |
| **Kısaltın** |Azure Data Lake Store içindeki dosyanın adı. **Filename** özelliği isteğe bağlıdır ve büyük/küçük harfe duyarlıdır. <br/><br/>**Dosya adı**belirtirseniz, etkinlik (kopyalama dahil) belirli dosya üzerinde çalışmaktadır.<br/><br/>**Dosya adı** belirtilmediğinde, Copy, giriş veri kümesindeki **FolderPath** içindeki tüm dosyaları içerir.<br/><br/>Bir çıkış veri kümesi için **dosya adı** belirtilmediğinde ve etkinlik havuzunda **preservehierarchy** belirtilmemişse, oluşturulan dosyanın adı `Data._Guid_.txt`biçimindedir. Örneğin: Data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. |Hayır |
| **partitionedBy** |**Partitionedby** özelliği isteğe bağlıdır. Bu uygulamayı, zaman serisi verileri için dinamik bir yol ve dosya adı belirtmek üzere kullanabilirsiniz. Örneğin, **FolderPath** her saat veri için parametreleştirilebilirler. Ayrıntılar ve örnekler için bkz. partitionedBy özelliği. |Hayır |
| **formatını** | Şu biçim türleri desteklenir: **TextFormat**, **jsonformat**, **avroformat**, **Orcformat**ve **parquetformat**. **Biçim** ' in altındaki **Type** özelliğini bu değerlerden birine ayarlayın. Daha fazla bilgi için, [Azure Data Factory makale tarafından desteklenen dosya ve sıkıştırma biçimlerinde](data-factory-supported-file-and-compression-formats.md) [metin biçimi](data-factory-supported-file-and-compression-formats.md#text-format), [JSON biçimi](data-factory-supported-file-and-compression-formats.md#json-format), [avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [orc biçimi](data-factory-supported-file-and-compression-formats.md#orc-format)ve [Parquet biçim](data-factory-supported-file-and-compression-formats.md#parquet-format) bölümlerine bakın. <br><br> Dosya tabanlı mağazalar (ikili kopya) arasında "olduğu gibi" dosyaları kopyalamak istiyorsanız hem giriş hem de çıkış veri kümesi tanımlarında `format` bölümünü atlayın. |Hayır |
| **masıyla** | Verilerin türünü ve sıkıştırma düzeyini belirtin. Desteklenen türler **gzip**, **söndür**, **bzip2**ve **zipsöndür**. Desteklenen düzeyler **en iyi** ve **en hızlardır**. Daha fazla bilgi için bkz. [Azure Data Factory tarafından desteklenen dosya ve sıkıştırma biçimleri](data-factory-supported-file-and-compression-formats.md#compression-support). |Hayır |

### <a name="the-partitionedby-property"></a>PartitionedBy özelliği
**Partitionedby** özelliği, Data Factory işlevleri ve sistem değişkenleri ile zaman serisi verileri Için dinamik **FolderPath** ve **filename** özellikleri belirtebilirsiniz. Ayrıntılar için bkz. [Azure Data Factory-işlevler ve sistem değişkenleri](data-factory-functions-variables.md) makalesi.


Aşağıdaki örnekte `{Slice}`, belirtilen biçimde `SliceStart` Data Factory sistem değişkeninin değeri ile değiştirilmiştir (`yyyyMMddHH`). Ad `SliceStart`, dilimin başlangıç saatine başvurur. `folderPath` özelliği, `wikidatagateway/wikisampledataout/2014100103` veya `wikidatagateway/wikisampledataout/2014100104`gibi her bir dilim için farklıdır.

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Aşağıdaki örnekte, yıl, ay, gün ve saat `SliceStart`, `folderPath` ve `fileName` özellikleri tarafından kullanılan ayrı değişkenlere ayıklanır:
```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
Zaman serisi veri kümeleri, zamanlama ve dilimler hakkında daha fazla bilgi için [Azure Data Factory Içindeki veri kümelerine](data-factory-create-datasets.md) bakın ve [zamanlama ve yürütme makalelerini Data Factory](data-factory-scheduling-and-execution.md) .


## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri
Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için, işlem [hatları oluşturma](data-factory-create-pipelines.md) makalesine bakın. Ad, açıklama, giriş ve çıkış tabloları ve ilke gibi özellikler, tüm etkinlik türleri için kullanılabilir.

Etkinliğin **typeproperties** bölümünde kullanılabilen özellikler her etkinlik türüyle farklılık gösterir. Kopyalama etkinliği için, kaynak ve havuz türlerine göre farklılık gösterir.

**AzureDataLakeStoreSource** , **typeproperties** bölümünde aşağıdaki özelliği destekler:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| **öz** |Verilerin alt klasörlerden veya yalnızca belirtilen klasörden özyinelemeli olarak okunup okunmadığını gösterir. |True (varsayılan değer), yanlış |Hayır |

**AzureDataLakeStoreSink** , **typeproperties** bölümünde aşağıdaki özellikleri destekler:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| **copyBehavior** |Kopyalama davranışını belirtir. |<b>Preservehierarchy</b>: Hedef klasördeki dosya hiyerarşisini korur. Kaynak dosyanın kaynak klasöre göreli yolu hedef dosyanın hedef klasöre göreli yolu ile aynıdır.<br/><br/><b>DÜZEDEN hiyerarşi</b>: kaynak klasördeki tüm dosyalar hedef klasörün ilk düzeyinde oluşturulur. Hedef dosyalar otomatik olarak oluşturulan adlarla oluşturulur.<br/><br/><b>Mergefiles</b>: kaynak klasördeki tüm dosyaları tek bir dosya ile birleştirir. Dosya veya blob adı belirtilmişse, birleştirilmiş dosya adı belirtilen addır. Aksi takdirde, dosya adı otomatik olarak oluşturulur. |Hayır |

### <a name="recursive-and-copybehavior-examples"></a>özyinelemeli ve copyBehavior örnekleri
Bu bölümde, özyinelemeli ve copyBehavior değerlerinin farklı birleşimleri için kopyalama işleminin ortaya çıkan davranışı açıklanmaktadır.

| öz | copyBehavior | Sonuç davranışı |
| --- | --- | --- |
| doğru |preserveHierarchy |Aşağıdaki yapıyla Klasör1 kaynak klasörü için: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1<br/>&nbsp;&nbsp;&nbsp;&nbsp;dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>hedef klasör Klasör1, kaynak ile aynı yapıyla oluşturulmuştur<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1<br/>&nbsp;&nbsp;&nbsp;&nbsp;dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| doğru |DÜZEDEN hiyerarşisi |Aşağıdaki yapıyla Klasör1 kaynak klasörü için: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1<br/>&nbsp;&nbsp;&nbsp;&nbsp;dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Hedef Klasör1 aşağıdaki yapıyla oluşturulur: <br/><br/>Folder1<br/>Fıle1 için otomatik olarak oluşturulan ad &nbsp;&nbsp;&nbsp;&nbsp;<br/>dosya2 için otomatik olarak oluşturulan ad &nbsp;&nbsp;&nbsp;&nbsp;<br/>file3 için otomatik olarak oluşturulan ad &nbsp;&nbsp;&nbsp;&nbsp;<br/>File4 için otomatik olarak oluşturulan ad &nbsp;&nbsp;&nbsp;&nbsp;<br/>File5 için otomatik olarak oluşturulan ad &nbsp;&nbsp;&nbsp;&nbsp; |
| doğru |mergeFiles |Aşağıdaki yapıyla Klasör1 kaynak klasörü için: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1<br/>&nbsp;&nbsp;&nbsp;&nbsp;dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Hedef Klasör1 aşağıdaki yapıyla oluşturulur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1 + dosya2 + File3 + File4 + dosya 5 içerikleri, otomatik olarak oluşturulan dosya adı ile tek bir dosyada birleştirilir |
| false |preserveHierarchy |Aşağıdaki yapıyla Klasör1 kaynak klasörü için: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1<br/>&nbsp;&nbsp;&nbsp;&nbsp;dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Klasör1 hedef klasörü aşağıdaki yapıyla oluşturulur<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1<br/>&nbsp;&nbsp;&nbsp;&nbsp;dosya2<br/><br/><br/>Subfolder1, File3, File4 ve File5 ile birlikte alınmaz. |
| false |DÜZEDEN hiyerarşisi |Aşağıdaki yapıyla Klasör1 kaynak klasörü için:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1<br/>&nbsp;&nbsp;&nbsp;&nbsp;dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Klasör1 hedef klasörü aşağıdaki yapıyla oluşturulur<br/><br/>Folder1<br/>Fıle1 için otomatik olarak oluşturulan ad &nbsp;&nbsp;&nbsp;&nbsp;<br/>dosya2 için otomatik olarak oluşturulan ad &nbsp;&nbsp;&nbsp;&nbsp;<br/><br/><br/>Subfolder1, File3, File4 ve File5 ile birlikte alınmaz. |
| false |mergeFiles |Aşağıdaki yapıyla Klasör1 kaynak klasörü için:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1<br/>&nbsp;&nbsp;&nbsp;&nbsp;dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Klasör1 hedef klasörü aşağıdaki yapıyla oluşturulur<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1 + dosya2 içerikleri, otomatik olarak oluşturulan dosya adı ile tek bir dosyada birleştirilir. Fıle1 için otomatik olarak oluşturulan ad<br/><br/>Subfolder1, File3, File4 ve File5 ile birlikte alınmaz. |

## <a name="supported-file-and-compression-formats"></a>Desteklenen dosya ve sıkıştırma biçimleri
Ayrıntılar için [Azure Data Factory makalesindeki dosya ve sıkıştırma biçimlerine](data-factory-supported-file-and-compression-formats.md) bakın.

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>Data Lake Store veri kopyalamaya yönelik JSON örnekleri
Aşağıdaki örnekler örnek JSON tanımlarını sağlar. Bu örnek tanımları, [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) veya [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)kullanarak bir işlem hattı oluşturmak için kullanabilirsiniz. Örneklerde, Data Lake Store ve Azure Blob Storage 'a ve bunlara verilerin nasıl kopyalanacağı gösterilmektedir. Ancak, veriler herhangi bir kaynaktan, desteklenen herhangi bir havuza _doğrudan_ kopyalanabilir. Daha fazla bilgi için bkz. [kopyalama etkinliği kullanarak verileri taşıma](data-factory-data-movement-activities.md) makalesindeki "desteklenen veri depoları ve biçimleri" bölümüne bakın.

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Örnek: Azure Blob depolamadan Azure Data Lake Store verileri kopyalama
Bu bölümdeki örnek kod şunları gösterir:

* [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde bağlı bir hizmet.
* [AzureDataLakeStore](#linked-service-properties)türünde bağlı bir hizmet.
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
* [AzureDataLakeStore](#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
* [Blobsource](data-factory-azure-blob-connector.md#copy-activity-properties) ve [AzureDataLakeStoreSink](#copy-activity-properties)kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md) .

Örneklerde Azure Blob depolama alanındaki saat serisi verilerinin her saat Data Lake Store nasıl kopyalandığı gösterilmektedir.

**Azure Storage bağlı hizmeti**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Bağlı hizmet Azure Data Lake Store**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

> [!NOTE]
> Yapılandırma ayrıntıları için bkz. [bağlı hizmet özellikleri](#linked-service-properties) bölümü.
>

**Azure Blob girdi veri kümesi**

Aşağıdaki örnekte, veriler her saat yeni bir bloba alınır (`"frequency": "Hour", "interval": 1`). Blob için klasör yolu ve dosya adı, işlenmekte olan dilimin başlangıç zamanına göre dinamik olarak değerlendirilir. Klasör yolu başlangıç zamanının yıl, ay ve gün kısmını kullanır. Dosya adı başlangıç zamanının saat kısmını kullanır. `"external": true` ayarı, Data Factory hizmetine tablonun veri fabrikasının dışında olduğunu ve veri fabrikasındaki bir etkinlik tarafından üretilmediğini bildirir.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Azure Data Lake Store çıktı veri kümesi**

Aşağıdaki örnek Data Lake Store verileri kopyalar. Yeni veriler her saat Data Lake Store kopyalanır.

```JSON
{
    "name": "AzureDataLakeStoreOutput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Blob kaynağı ve bir Data Lake Store havuzu ile işlem hattındaki etkinliği kopyalama**

Aşağıdaki örnekte, işlem hattı giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir. Kopyalama etkinliği her saat çalışacak şekilde zamanlanır. Ardışık düzen JSON tanımında `source` türü `BlobSource`olarak ayarlanır ve `sink` türü `AzureDataLakeStoreSink`olarak ayarlanır.

```json
{
    "name":"SamplePipeline",
    "properties":
    {
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":
        [
            {
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureDataLakeStoreOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

### <a name="example-copy-data-from-azure-data-lake-store-to-an-azure-blob"></a>Örnek: Azure Data Lake Store verileri Azure Blob 'a kopyalama
Bu bölümdeki örnek kod şunları gösterir:

* [AzureDataLakeStore](#linked-service-properties)türünde bağlı bir hizmet.
* [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde bağlı bir hizmet.
* [AzureDataLakeStore](#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
* [AzureDataLakeStoreSource](#copy-activity-properties) ve [blobsink](data-factory-azure-blob-connector.md#copy-activity-properties)kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md) .

Kod, saat serisi verilerini her saat Data Lake Store bir Azure blobuna kopyalar.

**Bağlı hizmet Azure Data Lake Store**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        }
    }
}
```

> [!NOTE]
> Yapılandırma ayrıntıları için bkz. [bağlı hizmet özellikleri](#linked-service-properties) bölümü.
>

**Azure Storage bağlı hizmeti**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure Data Lake girişi veri kümesi**

Bu örnekte, `"external"` `true` olarak ayarlamak, Data Factory hizmetini tablonun veri fabrikasında dış olduğunu ve veri fabrikasındaki bir etkinlik tarafından üretilmediğini bildirir.

```json
{
    "name": "AzureDataLakeStoreInput",
    "properties":
    {
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
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
**Azure Blob çıktı veri kümesi**

Aşağıdaki örnekte, veriler her saat yeni bir bloba yazılır (`"frequency": "Hour", "interval": 1`). Blob 'un klasör yolu, işlenmekte olan dilimin başlangıç zamanına göre dinamik olarak değerlendirilir. Klasör yolu başlangıç zamanının yıl, ay, gün ve saat kısmını kullanır.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Bir işlem hattındaki bir Azure Data Lake Store kaynağı ve bir blob havuzu içeren bir kopyalama etkinliği**

Aşağıdaki örnekte, işlem hattı giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir. Kopyalama etkinliği her saat çalışacak şekilde zamanlanır. Ardışık düzen JSON tanımında `source` türü `AzureDataLakeStoreSource`olarak ayarlanır ve `sink` türü `BlobSink`olarak ayarlanır.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureDakeLaketoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureDataLakeStoreInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureDataLakeStoreSource",
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

Kopyalama etkinliği tanımında, kaynak veri kümesindeki sütunları havuz veri kümesindeki sütunlara de eşleyebilirsiniz. Ayrıntılar için bkz. [Azure Data Factory veri kümesi sütunlarını eşleme](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Performans ve ayar
Kopyalama etkinliği performansını ve bunun nasıl iyileştirileceği faktörleri hakkında bilgi edinmek için, [etkinlik performansını kopyalama ve ayarlama Kılavuzu](data-factory-copy-activity-performance.md) makalesini inceleyin.
