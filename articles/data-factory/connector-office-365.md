---
title: Azure Data Factory kullanarak Office 365 ' den veri kopyalama
description: Azure Data Factory bir işlem hattındaki kopyalama etkinliğini kullanarak Office 365 ' den desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/20/2019
ms.author: jingwang
ms.openlocfilehash: a7df69e7c5701074b40d6fa8340d8a0e247f00de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393012"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Azure Data Factory kullanarak Office 365 ' deki verileri Azure 'a kopyalama
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory, [Microsoft Graph veri bağlantısı](/graph/data-connect-concept-overview)ile tümleşir. böylece, zengin kurumsal 365 verileri Azure 'a ölçeklenebilir bir şekilde Azure 'a getirip analiz uygulamaları oluşturabilir ve bu değerli veri varlıklarına göre öngörüleri ayıklayabilirsiniz. Privileged Access Management tümleştirme, Office 365 ' deki değerli seçkin veriler için güvenli erişim denetimi sağlar.  Lütfen Microsoft Graph veri bağlantısı hakkında genel bakış için [Bu bağlantıya](/graph/data-connect-concept-overview) başvurun ve lisans bilgileri için [Bu bağlantıya](/graph/data-connect-policies#licensing) başvurun.

Bu makalede, Office 365 ' den veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

## <a name="supported-capabilities"></a>Desteklenen özellikler
ADF Office 365 Bağlayıcısı ve Microsoft Graph veri bağlantısı, Exchange e-posta etkin posta kutularındaki, adres defteri kişileri, takvim olayları, e-posta iletileri, Kullanıcı bilgileri, posta kutusu ayarları vb. gibi farklı veri kümesi türlerinin ölçeğini alma işlemi sırasında etkinleştirilir.  Kullanılabilir veri kümelerinin tüm listesini görmek için [buraya](/graph/data-connect-datasets) başvurun.

Şimdilik tek bir kopyalama etkinliği içinde, yalnızca **Office 365 ' den [Azure Blob depolama](connector-azure-blob-storage.md)alanına, [Azure Data Lake Storage 1.](connector-azure-data-lake-store.md)ve [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md) JSON biçiminde** (setofobjects) veri kopyalayabilirsiniz. Office 365 ' i diğer veri deposu türlerine veya başka biçimlerde yüklemek istiyorsanız, [desteklenen ADF hedef depolarından](copy-activity-overview.md#supported-data-stores-and-formats) birine veri yüklemek için ilk kopyalama etkinliğini sonraki bir kopyalama etkinliğiyle zincirleyebilirsiniz ("desteklenen veri depoları ve biçimleri" tablosunda "havuz olarak desteklenir" sütununa bakın).

>[!IMPORTANT]
>- Data Factory ve havuz veri deposu içeren Azure aboneliğinin, Office 365 kiracısı ile aynı Azure Active Directory (Azure AD) kiracısı altında olması gerekir.
>- Kopyalama etkinliği için kullanılan Azure Integration Runtime bölgesinin ve hedefin, Office 365 Kiracı kullanıcılarının posta kutusunun bulunduğu bölgede bulunduğundan emin olun. Azure IR konumunun nasıl belirlendiğini anlamak için [buraya](concepts-integration-runtime.md#integration-runtime-location) başvurun. Desteklenen Office bölgelerinin ve ilgili Azure bölgelerinin listesi için [buradaki tabloya](/graph/data-connect-datasets#regions) bakın.
>- Hizmet sorumlusu kimlik doğrulaması, Azure Blob depolama, Azure Data Lake Storage 1. ve Azure Data Lake Storage 2. hedef depoları olarak desteklenen tek kimlik doğrulama mekanizmasıdır.

## <a name="prerequisites"></a>Önkoşullar

Office 365 ' deki verileri Azure 'a kopyalamak için aşağıdaki önkoşul adımlarını gerçekleştirmeniz gerekir:

- Office 365 Kiracı yöneticinizin, [burada](/graph/data-connect-get-started)açıklandığı gibi taslak eylemleri tamamlaması gerekir.
- Azure Active Directory bir Azure AD Web uygulaması oluşturun ve yapılandırın.  Yönergeler için bkz. [Azure AD uygulaması oluşturma](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal).
- Office 365 için bağlı hizmeti tanımlamak üzere kullanacağınız aşağıdaki değerleri unutmayın:
    - Kiracı KIMLIĞI. Yönergeler için bkz. [KIRACı kimliği Al](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
    - Uygulama KIMLIĞI ve uygulama anahtarı.  Yönergeler için bkz. [uygulama kimliği ve kimlik doğrulama anahtarı alın](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
- Azure AD Web uygulaması 'nın sahibi olarak veri erişim isteği oluşturacak Kullanıcı kimliğini (Azure AD Web uygulamasından > ayarları > Owners > sahip Ekle) ekleyin. 
    - Kullanıcı kimliği, verileri aldığınız Office 365 kuruluşunda olmalıdır ve Konuk Kullanıcı olmamalıdır.

## <a name="approving-new-data-access-requests"></a>Yeni veri erişim isteklerini onaylama

Bu bağlam için ilk kez veri isteğinde bulunduğunuzu (veri tablosu erişimi olan bir birleşimi) hangi hedef hesabı içine yüklenmekte olan veri ve veri erişim isteğini yapan kullanıcı kimliği), kopyalama etkinliği durumunu "sürüyor" olarak görürsünüz ve yalnızca [Eylemler bölümündeki "Ayrıntılar" bağlantısına](copy-activity-overview.md#monitoring) tıkladığınızda durum "Requestingonay" olarak görüntülenir.  Veri ayıklama işleminin devam edebilmesi için, veri erişimi onaylayan grubunun bir üyesinin Privileged Access Management isteği onaylaması gerekir.

Onaylayanın veri erişim isteğini nasıl onaylayabilmesi için [buraya](/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) başvurun ve veri erişimi onaylayan grubunu ayarlama da dahil olmak üzere Privileged Access Management ile genel tümleştirme hakkında açıklama için [buraya](/graph/data-connect-pam) bakın.

## <a name="policy-validation"></a>Doğrulamayı yeniden deneme

ADF, yönetilen bir uygulamanın parçası olarak oluşturulduysa ve yönetim kaynak grubundaki kaynaklarda Azure ilkeleri atamaları yapılırsa, her kopyalama etkinliği çalıştırması için ADF, ilke atamalarının zorlandığından emin olmak için kontrol eder. Desteklenen ilkelerin listesi için [buraya](/graph/data-connect-policies#policies) bakın.

## <a name="getting-started"></a>Kullanmaya başlama

>[!TIP]
>Office 365 bağlayıcısını kullanma hakkında yönergeler için bkz. [Office 'teki verileri yükleme 365](load-office-365-data.md) makalesi.

Aşağıdaki araçlardan veya SDK 'Lardan birini kullanarak kopyalama etkinliğiyle bir işlem hattı oluşturabilirsiniz. Bir kopyalama etkinliği ile işlem hattı oluşturmak için adım adım yönergeler içeren öğreticiye gitmek üzere bir bağlantı seçin. 

- [Azure portalı](quickstart-create-data-factory-portal.md)
- [.NET SDK](quickstart-create-data-factory-dot-net.md)
- [Python SDK'sı](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [REST API](quickstart-create-data-factory-rest-api.md)
- [Azure Resource Manager şablonu](quickstart-create-data-factory-resource-manager-template.md). 

Aşağıdaki bölümlerde, Office 365 bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Aşağıdaki özellikler Office 365 bağlı hizmeti için desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği: **Office365** olarak ayarlanmalıdır | Yes |
| office365TenantId | Office 365 hesabının ait olduğu Azure kiracı KIMLIĞI. | Yes |
| Serviceprincipaltenantıd | Azure AD Web uygulamanızın bulunduğu kiracı bilgilerini belirtin. | Yes |
| Serviceprincipalıd | Uygulamanın istemci KIMLIĞINI belirtin. | Yes |
| Servicesprincipalkey | Uygulamanın anahtarını belirtin. Bu alanı, Data Factory güvenli bir şekilde depolamak için SecureString olarak işaretleyin. | Yes |
| connectVia | Veri deposuna bağlanmak için kullanılacak Integration Runtime.  Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. | No |

>[!NOTE]
> **Office365TenantId** ve **serviceprincipaltenantıd** ile buna karşılık gelen değer arasındaki fark:
>- Kendi kuruluşunuzun kullanımı için Office 365 verilerine yönelik bir uygulama geliştiren bir kurumsal geliştiricisiyseniz, kuruluşunuzun AAD kiracı KIMLIĞI olan her iki özellik için de aynı kiracı KIMLIĞINI sağlamanız gerekir.
>- Müşterileriniz için bir uygulama geliştiren bir ISV geliştiricisiyseniz office365TenantId, müşterinizin (uygulama yükleyicisi) AAD kiracı KIMLIĞI ve Serviceprincipaltenantıd, şirketinizin AAD Kiracı kimliği olacaktır.

**Örnek:**

```json
{
    "name": "Office365LinkedService",
    "properties": {
        "type": "Office365",
        "typeProperties": {
            "office365TenantId": "<Office 365 tenant id>",
            "servicePrincipalTenantId": "<AAD app service principal tenant id>",
            "servicePrincipalId": "<AAD app service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<AAD app service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm, Office 365 veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

Office 365 ' den veri kopyalamak için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | DataSet 'in Type özelliği: **Office365Table** olarak ayarlanmalıdır | Yes |
| tableName | Office 365 ' den Ayıklanacak veri kümesinin adı. Ayıklama için kullanılabilen Office 365 veri kümelerinin listesi için [buraya](/graph/data-connect-datasets#datasets) bakın. | Yes |

,, Ve ' i ayarlıyorsanız,, ve ' de olduğu `dateFilterColumn` `startTime` gibi, `endTime` `userScopeFilterUri` etkinlik kaynağı ' nda yeni modeli kullanmanız önerilir.

**Örnek**

```json
{
    "name": "DS_May2019_O365_Message",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, Office 365 kaynağı tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="office-365-as-source"></a>Kaynak olarak Office 365

Office 365 ' den veri kopyalamak için aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının Type özelliği: **Office365Source** olarak ayarlanmalıdır | Yes |
| allowedGroups | Grup Seçimi koşulu.  Verilerin alınacağı en fazla 10 kullanıcı grubunu seçmek için bu özelliği kullanın.  Hiçbir grup belirtilmemişse, veriler tüm kuruluş için döndürülür. | No |
| userScopeFilterUri | `allowedGroups`Özellik belirtilmediğinde, Office 365 ' den Ayıklanacak belirli satırları filtrelemek için kiracının tamamına uygulanan bir koşul ifadesi kullanabilirsiniz. Koşul biçimi Microsoft Graph API 'lerin sorgu biçimiyle eşleşmelidir, örn. `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'` . | No |
| Tarih FilterColumn | Tarih saat filtresi sütununun adı. Office 365 verilerinin ayıklandığı zaman aralığını sınırlamak için bu özelliği kullanın. | Veri kümesinde bir veya daha fazla DateTime sütunu varsa evet. Bu tarih saat filtresini gerektiren veri kümelerinin listesi için [buraya](/graph/data-connect-filtering#filtering) bakın. |
| startTime | Filtrelemek için tarih saat değerini başlatın. | Belirtilmişse Evet `dateFilterColumn` |
| endTime | Filtrelemek için tarih saat değeri sonu. | Belirtilmişse Evet `dateFilterColumn` |
| outputColumns | Havuza kopyalanacak sütunların dizisi. | No |

**Örnek:**

```json
"activities": [
    {
        "name": "CopyFromO365ToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Office 365 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "Office365Source",
                "dateFilterColumn": "CreatedDateTime",
                "startTime": "2019-04-28T16:00:00.000Z",
                "endTime": "2019-05-05T16:00:00.000Z",
                "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'",
                "outputColumns": [
                    {
                        "name": "Id"
                    },
                    {
                        "name": "CreatedDateTime"
                    },
                    {
                        "name": "LastModifiedDateTime"
                    },
                    {
                        "name": "ChangeKey"
                    },
                    {
                        "name": "Categories"
                    },
                    {
                        "name": "OriginalStartTimeZone"
                    },
                    {
                        "name": "OriginalEndTimeZone"
                    },
                    {
                        "name": "ResponseStatus"
                    },
                    {
                        "name": "iCalUId"
                    },
                    {
                        "name": "ReminderMinutesBeforeStart"
                    },
                    {
                        "name": "IsReminderOn"
                    },
                    {
                        "name": "HasAttachments"
                    },
                    {
                        "name": "Subject"
                    },
                    {
                        "name": "Body"
                    },
                    {
                        "name": "Importance"
                    },
                    {
                        "name": "Sensitivity"
                    },
                    {
                        "name": "Start"
                    },
                    {
                        "name": "End"
                    },
                    {
                        "name": "Location"
                    },
                    {
                        "name": "IsAllDay"
                    },
                    {
                        "name": "IsCancelled"
                    },
                    {
                        "name": "IsOrganizer"
                    },
                    {
                        "name": "Recurrence"
                    },
                    {
                        "name": "ResponseRequested"
                    },
                    {
                        "name": "ShowAs"
                    },
                    {
                        "name": "Type"
                    },
                    {
                        "name": "Attendees"
                    },
                    {
                        "name": "Organizer"
                    },
                    {
                        "name": "WebLink"
                    },
                    {
                        "name": "Attachments"
                    },
                    {
                        "name": "BodyPreview"
                    },
                    {
                        "name": "Locations"
                    },
                    {
                        "name": "OnlineMeetingUrl"
                    },
                    {
                        "name": "OriginalStart"
                    },
                    {
                        "name": "SeriesMasterId"
                    }
                ]
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).