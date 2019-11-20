---
title: Azure Data Factory kullanarak Dynamics CRM veya Dynamics 365 ' den (Common Data Service) veri kopyalama
description: Veri Fabrikası ardışık düzeninde bir kopyalama etkinliği kullanarak Microsoft Dynamics CRM 'den veya Microsoft Dynamics 365 ' den (Common Data Service) desteklenen havuz veri depolarına veya desteklenen kaynak veri depolarından, Dynamics CRM veya Dynamics 365 ' e veri kopyalamayı öğrenin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: jingwang
ms.openlocfilehash: c9adcf72eeec82fd4b8f1805fca1f284c0b953b7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680977"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Ve Azure Data Factory kullanarak Dynamics 365 (Common Data Service) veya Dynamics CRM 'den veri kopyalama

Bu makalede, Microsoft Dynamics 365 veya Microsoft Dynamics CRM 'den verileri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

## <a name="supported-capabilities"></a>Desteklenen yetenekler

Bu bağlayıcı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Dynamics 365 (Common Data Service) veya Dynamics CRM 'den, desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Ayrıca, desteklenen herhangi bir kaynak veri deposundan verileri Dynamics 365 (Common Data Service) veya Dynamics CRM 'ye kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak veya havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Bu Dynamics Connector, hem çevrimiçi hem de şirket içi Dynamics sürüm 7. x-9. x ' i destekler. Daha özel olarak,

- Sürüm 7. x, Dynamics CRM 2015 ile eşlenir
- Sürüm 8. x, Dynamics CRM 2016 ve önceki Dynamics 365 sürümüne eşlenir
- Sürüm 9. x, Dynamics 365 'in sonraki sürümüyle eşlenir

Karşılık gelen Dynamics sürümleri/ürünleri için desteklenen kimlik doğrulama türleri ve yapılandırmalarında aşağıdaki tabloya bakın. (IFD, internet 'e yönelik dağıtım için kısadır.)

| Dynamics sürümleri | Kimlik doğrulama türleri | Bağlı hizmet örnekleri |
|:--- |:--- |:--- |
| Dynamics 365 çevrimiçi <br> Dynamics CRM Online | Office365 | [Dynamics Online + Office365 auth](#dynamics-365-and-dynamics-crm-online) |
| Şirket içinde ıFD ile Dynamics 365 <br> Azure 'da ıFD ile Dynamics CRM 2016 <br> Azure 'da ıFD ile Dynamics CRM 2015 | UYGULAMASıNı | [IFD ve ıFD kimlik doğrulaması ile şirket içi Dynamics](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Dynamics 365 özel olarak, aşağıdaki uygulama türleri desteklenir:

- Sales için Dynamics 365
- Müşteri Hizmetleri için Dynamics 365
- Alan hizmeti için Dynamics 365
- Project Service Automation için Dynamics 365
- Pazarlama için Dynamics 365

Finans ve Işlemler, Taödünvs. gibi diğer uygulama türleri bu bağlayıcı tarafından desteklenmez.

Bu Dynamics Connector, [Dynamics XRM araçları](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools)'nın üzerine kurulmuştur.

>[!TIP]
>**Dynamics 365 finans ve işlemlerinden**veri kopyalamak IÇIN [Dynamics AX bağlayıcısını](connector-dynamics-ax.md)kullanabilirsiniz.

## <a name="get-started"></a>Başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, Dynamics 'e özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgiler sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Dynamics bağlantılı hizmeti için aşağıdaki özellikler desteklenir.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 ve Dynamics CRM Online

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Type özelliği **Dynamics**, **DynamicsCRM**veya **commondataserviceforapps**olarak ayarlanmalıdır. | Evet |
| deploymentType | Dynamics örneğinin dağıtım türü. Dynamics Online için **"çevrimiçi"** olması gerekir. | Evet |
| serviceUri | Dynamics örneğinizin hizmet URL 'SI, ör. `https://adfdynamics.crm.dynamics.com`. | Evet |
| authenticationType | Bir Dynamics sunucusuna bağlanmak için kimlik doğrulaması türü. Dynamics Online için **"Office365"** belirtin. | Evet |
| kullanıcı adı | Dynamics 'e bağlanmak için Kullanıcı adını belirtin. | Evet |
| password | Kullanıcı adı için belirttiğiniz kullanıcı hesabının parolasını belirtin. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | Evet |
| connectVia | Veri deposuna bağlanmak için kullanılacak [tümleştirme çalışma zamanı](concepts-integration-runtime.md) . Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. | Kaynak için Hayır, kaynak bağlı hizmetin bir tümleştirme çalışma zamanı yoksa, havuz için Evet |

>[!NOTE]
>Dynamics Connector, Dynamics CRM/365 çevrimiçi örneğinizi tanımlamak için isteğe bağlı "Organizasyonadi" özelliğini kullanmak için kullanılır. Çalışmaya devam ederken, örnek bulma için daha iyi performans kazanmak üzere yeni "serviceUri" özelliğini belirtmeniz önerilir.

**Örnek: Office365 kimlik doğrulaması kullanarak Dynamics Online**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://adfdynamics.crm.dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 ve şirket içi Dynamics CRM 'yi ıFD ile

*Dynamics Online ile karşılaştırılan ek özellikler şunlardır. "ana bilgisayar adı" ve "bağlantı noktası".*

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Type özelliği **Dynamics**, **DynamicsCRM**veya **commondataserviceforapps**olarak ayarlanmalıdır. | Evet |
| deploymentType | Dynamics örneğinin dağıtım türü. Bu, ıFD ile Dynamics şirket içi için **"OnPremisesWithIfd"** olmalıdır.| Evet |
| Konak | Şirket içi Dynamics sunucusunun ana bilgisayar adı. | Evet |
| port | Şirket içi Dynamics sunucusunun bağlantı noktası. | Hayır, varsayılan değer 443 ' dir |
| © | Dynamics örneğinin kuruluş adı. | Evet |
| authenticationType | Dynamics sunucusuna bağlanmak için kimlik doğrulaması türü. IFD ile Dynamics şirket içi için **"IFD"** belirtin. | Evet |
| kullanıcı adı | Dynamics 'e bağlanmak için Kullanıcı adını belirtin. | Evet |
| password | Kullanıcı adı için belirttiğiniz kullanıcı hesabının parolasını belirtin. Bu alanı, ADF 'de güvenli bir şekilde depolamak veya Azure Key Vault parolayı depolamak için bir SecureString olarak işaretlemeyi veya veri kopyalama işlemi gerçekleştirirken kopyalama etkinliğinin buradan daha fazla bilgi edinmesini sağlayabilirsiniz. [Key Vault mağaza kimlik bilgilerinden](store-credentials-in-key-vault.md)daha fazla bilgi edinin. | Evet |
| connectVia | Veri deposuna bağlanmak için kullanılacak [tümleştirme çalışma zamanı](concepts-integration-runtime.md) . Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. | Kaynak için Hayır, havuz için Evet |

**Örnek: ıFD kimlik doğrulaması kullanarak ıFD ile şirket içi Dynamics**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm, Dynamics veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

Ve Dynamics verilerini Dynamics 'ten kopyalamak için aşağıdaki özellikler desteklenir.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Veri kümesinin Type özelliği, **Dynamicsentity**, **dynamicscrmentity**veya **commondataserviceforappsentity**olarak ayarlanmalıdır. |Evet |
| entityName | Alınacak varlığın mantıksal adı. | Kaynak için Hayır (etkinlik kaynağında "sorgu" belirtilmişse), havuz için Evet |

**Örnek:**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, Dynamics kaynak ve havuz türleri tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="dynamics-as-a-source-type"></a>Kaynak türü olarak Dynamics

Dynamics 'ten veri kopyalamak için, etkinlik **kaynağını** kopyalama bölümünde aşağıdaki özellikler desteklenir.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği kaynağının Type özelliği **Dynamicssource**, **Dynamicscrmsource**veya **commondataserviceforappssource**olarak ayarlanmalıdır. | Evet |
| sorgu | FetchXML, Dynamics 'te (çevrimiçi ve şirket içi) kullanılan özel bir sorgu dilidir. Aşağıdaki örneğe bakın. Daha fazla bilgi için bkz. [FetchXML Ile derleme sorguları](https://msdn.microsoft.com/library/gg328332.aspx). | Hayır (veri kümesindeki "entityName" belirtilmişse) |

>[!NOTE]
>FetchXML sorgusunda yapılandırdığınız sütun projeksiyonu içermediği halde, PK sütunu her zaman gönderilir.

> [!IMPORTANT]
>- Dynamics 'ten veri kopyaladığınızda, Dynamics 'ten havuza açık sütun eşlemesi isteğe bağlıdır, ancak belirleyici bir kopyalama sonucunu sağlamak için yüksek oranda yeniden belirlenir.
>- Yazar Kullanıcı arabiriminde şemayı içeri aktarırken, ADF, kaynak sütun listesini başlatmak üzere Dynamics sorgu sonucundan en üstteki satırları örnekleyerek şemayı algılar. Bu durumda, en üstteki satırlarda hiçbir değer olmayan sütunlar atlanır. Açık eşleme yoksa, aynı davranış yürütmeleri kopyalama için de geçerlidir. Eşlemeye daha fazla sütun ekleyerek, bu, kopyalama çalışma zamanı sırasında kabul edilecek.

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>Örnek FetchXML sorgusu

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>Havuz türü olarak Dynamics

Verileri Dynamics 'e kopyalamak için aşağıdaki özellikler, etkinlik **havuzunu** Kopyala bölümünde desteklenir.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği havuzunun Type özelliği, **Dynamicssink**, **dynamicscrmsink**veya **commondataserviceforappssink**olarak ayarlanmalıdır. | Evet |
| WriteBehavior | İşlemin yazma davranışı.<br/>İzin verilen değer **"upsert"** . | Evet |
| alternateKeyName | "Upsert" yapmak için varlığınızda tanımlanmış alternatif anahtar adını belirtin. | Hayır |
| writeBatchSize | Her toplu işte Dynamics 'e yazılan verilerin satır sayısı. | Hayır (varsayılan değer 10 ' dur) |
| ıgnorenullvalues | Bir yazma işlemi sırasında giriş verilerinden (anahtar alanları hariç) null değerlerin yoksayılıp yoksayılmayacağını gösterir.<br/>İzin verilen değerler **true** ve **false**şeklindedir.<br>- **true**: bir yukarı/güncelleştirme işlemi gerçekleştirdiğinizde verileri hedef nesnede değiştirmeden bırakın. Ekleme işlemi yaparken tanımlanmış bir varsayılan değer ekleyin.<br/>- **false**: bir yukarı/güncelleştirme işlemi gerçekleştirdiğinizde hedef NESNESINDEKI verileri null olarak güncelleştirin. Ekleme işlemi yaparken NULL değer ekleyin. | Hayır (varsayılan değer false) |

>[!NOTE]
>"**Writebatchsize**" havuzunun varsayılan değeri ve Dynamics havuzu için kopyalama etkinliğinin " **[parallelcopy](copy-activity-performance.md#parallel-copy)** " değeri 10 ' dur. Bu nedenle, 100 kayıt aynı anda Dynamics 'e gönderilir.

Dynamics 365 Online için, [kuruluş başına 2 eşzamanlı toplu iş çağrısı](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations)sınırı vardır. Bu sınır aşılırsa, ilk istek yürütülmeden önce bir "sunucu meşgul" hatası oluşturulur. "WriteBatchSize" öğesinin daha az veya 10 ' a eşit tutulması, eşzamanlı çağrıların bu şekilde azaltılmasını önler.

"**Writebatchsize**" ve "**parallelkopyaların**" en iyi birleşimi, varlığınızın şemasına bağlıdır, örneğin sütun sayısı, satır boyutu, eklentiler/iş akışları/iş akışı etkinliklerinin sayısı, bu çağrılara bağlanır, vb. 10 writeBatchSize * 10 Parallelkopyaların varsayılan ayarı, Dynamics Service 'e göre öneri, ancak çoğu Dynamics varlık için de en iyi performansa sahip olmayabilir. Kopyalama etkinliği ayarlarınızda birleşimi ayarlayarak performansı ayarlayabilirsiniz.

**Örnek:**

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Dynamics için veri türü eşlemesi

Verileri Dynamics 'ten kopyaladığınızda, Dynamics veri türlerinden aşağıdaki eşlemeler, geçici veri türleri Data Factory için kullanılır. Kopyalama etkinliğinin kaynak şemayı ve veri türünü havuza nasıl eşlediğini öğrenmek için bkz. [şema ve veri türü eşlemeleri](copy-activity-schema-and-type-mapping.md).

Aşağıdaki eşleme tablosunu kullanarak kaynak Dynamics veri türüne göre bir veri kümesi yapısında karşılık gelen Data Factory veri türünü yapılandırın.

| Dynamics veri türü | Data Factory geçici veri türü | Kaynak olarak desteklenir | Havuz olarak destekleniyor |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode. BigInt | Uzun | ✓ | ✓ |
| AttributeTypeCode. Boolean | Boole | ✓ | ✓ |
| AttributeType. müşterisi | Guid | ✓ | |
| AttributeType. DateTime | Hem | ✓ | ✓ |
| AttributeType. Decimal | Kategori | ✓ | ✓ |
| AttributeType. Double | Çift | ✓ | ✓ |
| AttributeType. EntityName | Dize | ✓ | ✓ |
| AttributeType. Integer | Int32 | ✓ | ✓ |
| AttributeType. Lookup | Guid | ✓ | ✓ (tek hedefle ilişkili) |
| AttributeType. ManagedProperty | Boole | ✓ | |
| AttributeType. memo | Dize | ✓ | ✓ |
| AttributeType. para | Kategori | ✓ | ✓ |
| AttributeType. Owner | Guid | ✓ | |
| AttributeType. seçim listesi | Int32 | ✓ | ✓ |
| AttributeType. uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType. String | Dize | ✓ | ✓ |
| AttributeType. State | Int32 | ✓ | ✓ |
| AttributeType. Status | Int32 | ✓ | ✓ |

> [!NOTE]
> AttributeType. CalendarRules, AttributeType. Multiselectseçim listesi ve AttributeType. PartyList Dynamics veri türleri desteklenmez.

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="next-steps"></a>Sonraki adımlar
Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).
