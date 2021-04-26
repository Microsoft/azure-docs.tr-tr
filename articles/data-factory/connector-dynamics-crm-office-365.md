---
title: Dynamics 'te veri kopyalama (Common Data Service)
description: Veri Fabrikası ardışık düzeninde bir kopyalama etkinliği kullanarak Microsoft Dynamics CRM veya Microsoft Dynamics 365 (Common Data Service/Microsoft veri deposu) verilerini desteklenen havuz veri depolarına veya desteklenen kaynak veri depolarından Dynamics CRM 'ye veya Dynamics 365 ' e nasıl kopyalayacağınızı öğrenin.
ms.service: data-factory
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: f2db75fdcd4519b5ba0869bf4ef89c8323435539
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565988"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-servicemicrosoft-dataverse-or-dynamics-crm-by-using-azure-data-factory"></a>Ve Azure Data Factory kullanarak Dynamics 365 ' den (Common Data Service/Microsoft Dataverse) veya Dynamics CRM 'den veri kopyalama

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, Microsoft Dynamics 365 ve Microsoft Dynamics CRM 'den verileri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu bağlayıcı aşağıdaki etkinlikler için desteklenir:

- [Etkinliği](copy-activity-overview.md) [Desteklenen kaynak ve havuz matrisi](copy-activity-overview.md) ile Kopyala
- [Arama etkinliği](control-flow-lookup-activity.md)

Dynamics 365 (Common Data Service/Microsoft veri deposu) veya Dynamics CRM 'den, desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Ayrıca, desteklenen herhangi bir kaynak veri deposundan verileri Dynamics 365 (Common Data Service) veya Dynamics CRM 'ye kopyalayabilirsiniz. Kopyalama etkinliğinin kaynak ve havuz olarak desteklediği veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Bu Dynamics Connector hem çevrimiçi hem de şirket içi için 7 ile 9 arasındaki Dynamics sürümlerini destekler. Daha ayrıntılı belirtmek gerekirse:

- Sürüm 7, Dynamics CRM 2015 ile eşlenir.
- Sürüm 8, Dynamics CRM 2016 ile eşlenir ve Dynamics 365 ' nin erken sürümüdür.
- Sürüm 9, Dynamics 365 'in sonraki sürümüyle eşlenir.

Dynamics sürümleri ve ürünleri için desteklenen kimlik doğrulama türleri ve yapılandırmalarının aşağıdaki tablosuna bakın.

| Dynamics sürümleri | Kimlik doğrulaması türleri | Bağlı hizmet örnekleri |
|:--- |:--- |:--- |
| Common Data Service <br/><br/> Dynamics 365 çevrimiçi <br/><br/> Dynamics CRM Online | Azure Active Directory (Azure AD) hizmet sorumlusu <br/><br/> Office 365 | [Dynamics Online ve Azure AD hizmeti-asıl veya Office 365 kimlik doğrulaması](#dynamics-365-and-dynamics-crm-online) |
| Internet 'e yönelik dağıtım (ıFD) ile şirket içi Dynamics 365 <br/><br/> Azure 'da ıFD ile Dynamics CRM 2016 <br/><br/> Azure 'da ıFD ile Dynamics CRM 2015 | IFD | [IFD ve ıFD kimlik doğrulaması ile şirket içi Dynamics](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Dynamics 365 özel olarak, aşağıdaki uygulama türleri desteklenir:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

Bu bağlayıcı, finans, Işlemler ve Tatatçiler gibi diğer uygulama türlerini desteklemez.

>[!TIP]
>Dynamics 365 finans ve Işlemlerinden veri kopyalamak için [DYNAMICS AX bağlayıcısını](connector-dynamics-ax.md)kullanabilirsiniz.

Bu Dynamics Connector, [Dynamics XRM araçları](/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools)'nın üzerine kurulmuştur.

## <a name="prerequisites"></a>Önkoşullar

Bu bağlayıcıyı Azure AD hizmet sorumlusu kimlik doğrulaması ile birlikte kullanmak için Common Data Service veya Dynamics 'te sunucu-sunucu (S2S) kimlik doğrulamasını ayarlamanız gerekir. Ayrıntılı adımlar için [Bu makaleye](/powerapps/developer/common-data-service/build-web-applications-server-server-s2s-authentication) bakın.

## <a name="get-started"></a>başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, Dynamics 'e özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgiler sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Dynamics bağlantılı hizmeti için aşağıdaki özellikler desteklenir.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 ve Dynamics CRM Online

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği "Dynamics", "DynamicsCrm" veya "CommonDataServiceForApps" olarak ayarlanmalıdır. | Yes |
| deploymentType | Dynamics örneğinin dağıtım türü. Dynamics Online için değer "çevrimiçi" olmalıdır. | Yes |
| serviceUri | Aynı tarayıcıdan eriş, Dynamics örneğinizin hizmet URL 'SI. Örnek olarak "https:// \<organization-name> . CRM [x]. Dynamics. com" bulunur. | Yes |
| authenticationType | Bir Dynamics sunucusuna bağlanmak için kimlik doğrulaması türü. Geçerli değerler şunlardır "AADServicePrincipal" ve "Office365". | Yes |
| Serviceprincipalıd | Azure AD uygulamasının istemci KIMLIĞI. | Kimlik doğrulaması "AADServicePrincipal" olduğunda Evet |
| servicePrincipalCredentialType | Hizmet sorumlusu kimlik doğrulaması için kullanılacak kimlik bilgisi türü. Geçerli değerler şunlardır "ServicePrincipalKey" ve "ServicePrincipalCert". | Kimlik doğrulaması "AADServicePrincipal" olduğunda Evet |
| servicePrincipalCredential | Hizmet sorumlusu kimlik bilgileri. <br/><br/>Kimlik bilgisi türü olarak "ServicePrincipalKey" kullandığınızda, `servicePrincipalCredential` bağlantılı hizmet dağıtımı üzerinde Azure Data Factory şifreler bir dize olabilir. Veya Azure Key Vault bir gizli dizi başvurusu olabilir. <br/><br/>Kimlik bilgisi olarak "ServicePrincipalCert" kullandığınızda, `servicePrincipalCredential` Azure Key Vault bir sertifikaya başvuru olmalıdır. | Kimlik doğrulaması "AADServicePrincipal" olduğunda Evet |
| username | Dynamics 'e bağlanmak için Kullanıcı adı. | Kimlik doğrulaması "Office365" olduğunda Evet |
| password | Kullanıcı adı olarak belirttiğiniz kullanıcı hesabının parolası. Data Factory güvenli bir şekilde depolamak için bu alanı "SecureString" ile işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | Kimlik doğrulaması "Office365" olduğunda Evet |
| connectVia | Veri deposuna bağlanmak için kullanılacak [tümleştirme çalışma zamanı](concepts-integration-runtime.md) . Hiçbir değer belirtilmemişse, özelliği varsayılan Azure tümleştirme çalışma zamanını kullanır. | No |

>[!NOTE]
>Dynamics Connector, Dynamics CRM veya Dynamics 365 çevrimiçi örneğinizi tanımlamak için daha önce isteğe bağlı **Organizasyonadi** özelliğini kullanıyordu. Bu özellik hala çalışmaya devam ederken, örnek bulma için daha iyi performans elde etmek yerine yeni **ServiceUri** özelliğini belirtmenizi öneririz.

#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-key-authentication"></a>Örnek: Azure AD hizmet sorumlusu ve anahtar kimlik doğrulaması kullanarak Dynamics Online

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",  
            "authenticationType": "AADServicePrincipal",  
            "servicePrincipalId": "<service principal id>",  
            "servicePrincipalCredentialType": "ServicePrincipalKey",  
            "servicePrincipalCredential": "<service principal key>"
        },  
        "connectVia": {  
            "referenceName": "<name of Integration Runtime>",  
            "type": "IntegrationRuntimeReference"  
        }  
    }  
}  
```
#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication"></a>Örnek: Azure AD hizmeti-sorumlusu ve sertifika kimlik doğrulaması kullanarak Dynamics Online

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com", 
            "authenticationType": "AADServicePrincipal", 
            "servicePrincipalId": "<service principal id>", 
            "servicePrincipalCredentialType": "ServicePrincipalCert", 
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            } 
        }, 
        "connectVia": { 
            "referenceName": "<name of Integration Runtime>", 
            "type": "IntegrationRuntimeReference" 
        } 
    } 
} 
```

#### <a name="example-dynamics-online-using-office-365-authentication"></a>Örnek: Office 365 kimlik doğrulaması kullanarak Dynamics Online

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",
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

Dynamics Online ile karşılaştırılan ek özellikler **konak adı** ve **bağlantı noktasıdır**.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği "Dynamics", "DynamicsCrm" veya "CommonDataServiceForApps" olarak ayarlanmalıdır. | Evet. |
| deploymentType | Dynamics örneğinin dağıtım türü. Bu değer, ıFD ile Dynamics şirket içi için "OnPremisesWithIfd" olmalıdır.| Evet. |
| Konak | Şirket içi Dynamics sunucusunun ana bilgisayar adı. | Evet. |
| port | Şirket içi Dynamics sunucusunun bağlantı noktası. | Hayır. Varsayılan değer 443 ' dir. |
| © | Dynamics örneğinin kuruluş adı. | Evet. |
| authenticationType | Dynamics sunucusuna bağlanmak için kimlik doğrulaması türü. IFD ile Dynamics şirket içi için "IFD" belirtin. | Evet. |
| username | Dynamics 'e bağlanmak için Kullanıcı adı. | Evet. |
| password | Kullanıcı adı için belirttiğiniz kullanıcı hesabının parolası. Bu alanı, Data Factory güvenli bir şekilde depolamak için "SecureString" ile işaretleyebilirsiniz. Ya da bir parolayı Key Vault saklayabilir ve veri kopyalama yapıldığında kopyalama etkinliğinin buradan çekmesini sağlayabilirsiniz. [Key Vault Içindeki mağaza kimlik bilgilerinden](store-credentials-in-key-vault.md)daha fazla bilgi edinin. | Evet. |
| connectVia | Veri deposuna bağlanmak için kullanılacak [tümleştirme çalışma zamanı](concepts-integration-runtime.md) . Hiçbir değer belirtilmemişse, özelliği varsayılan Azure tümleştirme çalışma zamanını kullanır. | No |

#### <a name="example-dynamics-on-premises-with-ifd-using-ifd-authentication"></a>Örnek: ıFD kimlik doğrulaması kullanarak ıFD ile şirket içi Dynamics

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

Ve Dynamics verilerini Dynamics 'ten kopyalamak için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | DataSet 'in Type özelliği "DynamicsEntity", "DynamicsCrmEntity" veya "CommonDataServiceForAppsEntity" olarak ayarlanmalıdır. |Yes |
| entityName | Alınacak varlığın mantıksal adı. | Etkinlik kaynağı "sorgu" ve havuz için Evet olarak belirtilmişse kaynak için Hayır |

#### <a name="example"></a>Örnek

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

Verileri Dynamics 'ten kopyalamak için, kopyalama etkinliği **kaynağı** bölümü aşağıdaki özellikleri destekler:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının Type özelliği "DynamicsSource", "DynamicsCrmSource" veya "CommonDataServiceForAppsSource" olarak ayarlanmalıdır. | Yes |
| sorgu | FetchXML, Dynamics Online 'da ve şirket içinde kullanılan özel bir sorgu dilidir. Aşağıdaki örneğe bakın. Daha fazla bilgi için bkz. [FetchXML Ile derleme sorguları](/previous-versions/dynamicscrm-2016/developers-guide/gg328332(v=crm.8)). | `entityName`Veri kümesi belirtilmemişse Hayır |

>[!NOTE]
>FetchXML sorgusunda yapılandırdığınız sütun projeksiyonu içermediği halde, PK sütunu her zaman gönderilir.

> [!IMPORTANT]
>- Dynamics 'ten veri kopyaladığınızda, Dynamics 'ten havuza açık sütun eşleme isteğe bağlıdır. Ancak belirleyici bir kopya sonucu sağlamak için eşlemeyi önemle öneririz.
>- Data Factory, yazma Kullanıcı arabirimindeki bir şemayı içeri aktardığında şemayı algılar. Kaynak sütun listesini başlatmak için, Dynamics sorgu sonucundan en üstteki satırları örnekleyerek bunu yapar. Bu durumda, üstteki satırlarda değer içermeyen sütunlar atlanır. Açık eşleme yoksa, aynı davranış yürütmeleri kopyalama için de geçerlidir. Eşlemeye daha fazla sütun ekleyerek, bu, kopyalama çalışma zamanı sırasında kabul edilebilir.

#### <a name="example"></a>Örnek

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

Verileri Dynamics 'e kopyalamak için kopyalama etkinliği **havuzu** bölümü aşağıdaki özellikleri destekler:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği havuzunun Type özelliği "DynamicsSink", "DynamicsCrmSink" veya "CommonDataServiceForAppsSink" olarak ayarlanmalıdır. | Evet. |
| writeBehavior | İşlemin yazma davranışı. Değerin "upsert" olması gerekir. | Yes |
| alternateKeyName | Daha büyük bir çıkış yapmak için varlığınızda tanımlanan alternatif anahtar adı. | Hayır. |
| writeBatchSize | Her toplu işte Dynamics 'e yazılan verilerin satır sayısı. | Hayır. Varsayılan değer 10'dur. |
| ıgnorenullvalues | Bir yazma işlemi sırasında anahtar alanları dışındaki giriş verilerinden null değerlerin yoksayılıp yoksayılmayacağı.<br/><br/>Geçerli değerler **true** ve **false** şeklindedir:<ul><li>**Doğru**: bir yukarı veya güncelleştirme işlemi gerçekleştirdiğinizde verileri hedef nesnede değiştirmeden bırakın. Ekleme işlemi yaparken tanımlanmış bir varsayılan değer ekleyin.</li><li>**Yanlış**: bir yukarı veya güncelleştirme işlemi gerçekleştirdiğinizde, hedef nesnedeki verileri null bir değere güncelleştirin. Ekleme işlemi yaparken null değer ekleyin.</li></ul> | Hayır. Varsayılan değer **false**'dur. |
| maxConcurrentConnections |Etkinlik çalışması sırasında veri deposuna kurulan eşzamanlı bağlantıların üst sınırı. Yalnızca eş zamanlı bağlantıları sınırlandırmak istediğinizde bir değer belirtin.| No |

>[!NOTE]
>Hem havuz **Writebatchsize** hem de kopyalama etkinliği **[Parallelcopy](copy-activity-performance-features.md#parallel-copy)** for the Dynamics Sink için varsayılan değer 10 ' dur. Bu nedenle, 100 kayıt aynı anda varsayılan olarak Dynamics 'e gönderilir.

Dynamics 365 Online için, [her kuruluş için iki eş zamanlı toplu çağrı](/previous-versions/dynamicscrm-2016/developers-guide/jj863631(v=crm.8)#Run-time%20limitations)sınırı vardır. Bu sınır aşılırsa, ilk istek çalıştırılmadan önce bir "sunucu meşgul" özel durumu oluşturulur. Eşzamanlı aramaların bu tür azaltmasını önlemek için **Writebatchsize** 10 veya daha az bir durumda tutun.

**Writebatchsize** ve **parallelkopyaların** en iyi birleşimi, varlığınızın şemasına bağlıdır. Şema öğeleri; sütun sayısını, satır boyutunu ve bu çağrılardan oluşan eklenti, iş akışı veya iş akışı etkinliklerinin sayısını içerir. **Writebatchsize** (10) &times; **parallelkopyaların** (10) varsayılan ayarı, Dynamics hizmetine göre öneri. Bu değer, en iyi performansa sahip olmasa da çoğu Dynamics varlık için geçerlidir. Kopyalama etkinliği ayarlarınızda birleşimi ayarlayarak performansı ayarlayabilirsiniz.

#### <a name="example"></a>Örnek

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

## <a name="retrieving-data-from-views"></a>Görünümlerden veri alma

Dynamics görünümlerinden verileri almak için, görünümün kayıtlı sorgusunu almanız ve verileri almak için sorguyu kullanmanız gerekir.

Farklı görünüm türlerini depolayan iki varlık vardır: "kaydedilen sorgu" sistem görünümünü depolar ve "Kullanıcı sorgusu" Kullanıcı görünümünü depolar. Görünümlerin bilgilerini almak için aşağıdaki FetchXML sorgusuna başvurun ve "TARGETENTITY" ifadesini veya ile değiştirin `savedquery` `userquery` . Her varlık türünün, ihtiyaınıza göre sorguya ekleyebileceğiniz daha fazla kullanılabilir özniteliği vardır. [Savedquery varlığı](/dynamics365/customer-engagement/web-api/savedquery) ve [userquery varlığı](/dynamics365/customer-engagement/web-api/userquery)hakkında daha fazla bilgi edinin.

```xml
<fetch top="5000" >
  <entity name="<TARGETENTITY>">
    <attribute name="name" />
    <attribute name="fetchxml" />
    <attribute name="returnedtypecode" />
    <attribute name="querytype" />
  </entity>
</fetch>
```

Görünümleri filtrelemek için de filtre ekleyebilirsiniz. Örneğin, hesap varlığındaki "etkin Firmalarım" adlı bir görünüm almak için aşağıdaki filtreyi ekleyin.

```xml
<filter type="and" >
    <condition attribute="returnedtypecode" operator="eq" value="1" />
    <condition attribute="name" operator="eq" value="My Active Accounts" />
</filter>
```

## <a name="data-type-mapping-for-dynamics"></a>Dynamics için veri türü eşlemesi

Dynamics 'ten veri kopyaladığınızda aşağıdaki tablo, Dynamics veri türlerinden gelen eşlemeleri Data Factory geçici veri türleri olarak gösterir. Bir kopyalama etkinliğinin bir kaynak şemasına nasıl eşlendiğini ve veri türünün bir havuza nasıl eşlendiğini öğrenmek için bkz. [şema ve veri türü eşlemeleri](copy-activity-schema-and-type-mapping.md).

Aşağıdaki eşleme tablosunu kullanarak kaynak Dynamics veri türüne dayalı bir veri kümesi yapısında karşılık gelen Data Factory veri türünü yapılandırın:

| Dynamics veri türü | Data Factory geçici veri türü | Kaynak olarak desteklenir | Havuz olarak destekleniyor |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode. BigInt | Kalacağını | ✓ | ✓ |
| AttributeTypeCode. Boolean | Boole | ✓ | ✓ |
| AttributeType. müşterisi | GUID | ✓ | ✓ (Bkz. [rehberlik](#writing-data-to-a-lookup-field)) |
| AttributeType. DateTime | Tarih saat | ✓ | ✓ |
| AttributeType. Decimal | Ondalık | ✓ | ✓ |
| AttributeType. Double | Çift | ✓ | ✓ |
| AttributeType. EntityName | Dize | ✓ | ✓ |
| AttributeType. Integer | Int32 | ✓ | ✓ |
| AttributeType. Lookup | GUID | ✓ | ✓ (Bkz. [rehberlik](#writing-data-to-a-lookup-field)) |
| AttributeType. ManagedProperty | Boole | ✓ | |
| AttributeType. memo | Dize | ✓ | ✓ |
| AttributeType. para | Ondalık | ✓ | ✓ |
| AttributeType. Owner | GUID | ✓ | ✓ (Bkz. [rehberlik](#writing-data-to-a-lookup-field)) |
| AttributeType. seçim listesi | Int32 | ✓ | ✓ |
| AttributeType. uniqueidentifier | GUID | ✓ | ✓ |
| AttributeType. String | Dize | ✓ | ✓ |
| AttributeType. State | Int32 | ✓ | ✓ |
| AttributeType. Status | Int32 | ✓ | ✓ |

> [!NOTE]
> **AttributeType. CalendarRules**, **attributeType. multiselectseçim listesi** ve **attributeType. partylist** Dynamics veri türleri desteklenmez.

## <a name="writing-data-to-a-lookup-field"></a>Arama alanına veri yazma

Müşteri ve sahip gibi birden çok hedefi olan bir arama alanına veri yazmak için bu kılavuzu ve örneği izleyin:

1. Kaynağınızın hem alan değerini hem de karşılık gelen hedef varlık adını içerdiğini doğrulayın.
   - Tüm kayıtlar aynı hedef varlığa eşlenmiştir, aşağıdaki koşullardan birini sağlayın:
      - Kaynak verilerinizde hedef varlık adını depolayan bir sütun vardır.
      - Hedef varlığı tanımlamak için kopyalama etkinliği kaynağına ek bir sütun eklediniz.
   - Farklı kayıtlar farklı hedef varlıklarla eşlenmiştir, kaynak verilerinizde karşılık gelen hedef varlık adını depolayan bir sütun olduğundan emin olun.

1. Hem değer hem de varlık başvurusu sütunlarını kaynaktan havuza eşleyin. Varlık başvurusu sütunu, Özel adlandırma düzeniyle bir sanal sütuna eşlenmelidir `{lookup_field_name}@EntityReference` . Bu sütun, Dynamics 'te gerçekten mevcut değil. Bu sütunun belirtilen MultiTarget arama alanının meta veri sütunu olduğunu göstermek için kullanılır.

Örneğin, kaynağın şu iki sütuna sahip olduğunu varsayalım:

- **GUID** türünde, Dynamics 'teki hedef varlığın birincil anahtar değeri olan **customerfield** sütunu.
- Hedef varlığın mantıksal adı olan **dize** türünde **hedef** sütun.

Ayrıca, bu tür verileri **Müşteri** türünde bir Sink Dynamics varlık alanı **customerfield** 'a kopyalamak istediğinizi varsayın.

Kopyalama etkinliği sütun eşlemesi ' nde, iki sütunu aşağıdaki gibi eşleyin:

- **Customerfield** 'ı **customerfield**. Bu eşleme, normal alan eşlemedir.
- **Müşteri alanı \@ EntityReference**'a **hedefleyin** . Havuz sütunu, varlık başvurusunu temsil eden bir sanal sütundur. Şemaların içeri aktarılmasıyla gösterilmeyeceği şekilde, bir eşlemede bu tür alan adlarını girin.

![Dynamics arama alanı sütun eşleme](./media/connector-dynamics-crm-office-365/connector-dynamics-lookup-field-column-mapping.png)

Tüm kaynak kayıtlarınız aynı hedef varlığa eşlenir ve kaynak verileriniz hedef varlık adını içermiyorsa, burada bir kısayol verilmiştir: kopyalama etkinliği kaynağı ' nda, ek bir sütun ekleyin. Yeni sütunu, kalıbı kullanarak adlandırın `{lookup_field_name}@EntityReference` , değeri hedef varlık adına ayarlayın ve ardından her zamanki gibi sütun eşleme ile devam edin. Kaynak ve havuz sütun adlarınız aynıysa, varsayılan olarak sütunları ada göre eşleyerek kopyalama etkinliği için de açık sütun eşlemeyi atlayabilirsiniz.

![Dynamics Lookup-varlık başvurusu sütunu ekleme alanı](./media/connector-dynamics-crm-office-365/connector-dynamics-add-entity-reference-column.png)

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için bkz. [arama etkinliği](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Sonraki adımlar
Veri listesi için Data Factory kopyalama etkinliği kaynak ve havuz olarak desteklenir, bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).