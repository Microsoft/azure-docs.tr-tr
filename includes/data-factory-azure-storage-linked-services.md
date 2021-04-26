---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 37917e0ed663675677f1d0452b5796120ca2694e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96001768"
---
### <a name="azure-storage-linked-service"></a>Azure Storage Bağlı Hizmeti
**Azure depolama bağlı hizmeti** , veri fabrikasını Azure depolama 'ya genel erişimle sağlayan **hesap anahtarını** kullanarak bir Azure Depolama hesabını Azure veri fabrikasına bağlayabilmeniz için izin verir. Aşağıdaki tabloda, Azure Storage bağlı hizmetine özgü JSON öğelerine ilişkin açıklama verilmiştir.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür |Type özelliği: **Azurestorage** olarak ayarlanmalıdır |Yes |
| Dizisi |ConnectionString özelliği için Azure depolama 'ya bağlanmak için gereken bilgileri belirtin. |Yes |

Depolama hesabı erişim anahtarlarını alma hakkında daha fazla bilgi için bkz. [depolama hesabı erişim anahtarlarını yönetme](../articles/storage/common/storage-account-keys-manage.md).

**Örnek:**  

```json
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

### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS bağlı hizmeti
Paylaşılan erişim imzası (SAS), depolama hesabınızdaki kaynaklara temsilci erişimi sağlar. Bu, belirli bir süre boyunca ve hesap erişim anahtarlarınızı paylaşmak zorunda kalmadan, belirtilen bir süre ve belirli bir izin kümesiyle bir istemci sınırlı izinleri vermenizi sağlar. SAS, bir depolama kaynağına kimlik doğrulamalı erişim için gereken tüm bilgileri sorgu parametrelerinde kapsayan bir URI 'dir. SAS ile depolama kaynaklarına erişmek için, istemcinin yalnızca SAS 'yi uygun oluşturucuya veya yönteme geçirmesi gerekir. SAS hakkında daha fazla bilgi için bkz. [paylaşılan erişim imzaları (SAS) kullanarak Azure depolama kaynaklarına sınırlı erişim verme](../articles/storage/common/storage-sas-overview.md).

> [!IMPORTANT]
> Azure Data Factory artık yalnızca **HIZMET SAS** 'yi destekliyor ancak hesap SAS 'sı değil. Azure portal veya Depolama Gezgini tarafından kullanılabilen SAS URL 'sinin, desteklenmeyen bir hesap SAS 'si olduğunu not edin.

> [!TIP]
> Depolama hesabınız için bir hizmet SAS oluşturmak için aşağıdaki PowerShell komutlarını çalıştırabilirsiniz (yer tutucuları değiştirin ve gerekli izni verin): `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Azure Storage SAS bağlı hizmeti, bir Azure Depolama hesabını, paylaşılan erişim Imzası (SAS) kullanarak bir Azure Data Factory 'ye bağlayabilmeniz için izin verir. Veri fabrikasını depolama alanındaki tüm/belirli kaynaklara (blob/kapsayıcı) kısıtlı/zamana göre erişim ile sağlar. Aşağıdaki tabloda, Azure Storage SAS bağlı hizmetine özgü JSON öğeleri için açıklama verilmiştir. 

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür |Type özelliği: **Azurestorampasas** olarak ayarlanmalıdır |Yes |
| sasUri |Blob, kapsayıcı veya tablo gibi Azure depolama kaynakları için paylaşılan erişim Imzası URI 'SI belirtin.  |Yes |

**Örnek:**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<Specify SAS URI of the Azure Storage resource>"
        }
    }
}
```

**SAS URI 'si** oluştururken şunları göz önünde bulundurarak:  

* Veri fabrikanızdaki bağlantılı hizmetin (okuma, yazma, okuma/yazma) nasıl kullanıldığını temel alarak nesneler üzerinde uygun okuma/yazma **izinleri** ayarlayın.
* **Süre sonu süresini** uygun şekilde ayarlayın. Azure Storage nesnelerine erişimin, işlem hattının etkin döneminde sona ermediğinden emin olun.
* URI, ihtiyacınıza göre doğru kapsayıcı/blob veya tablo düzeyinde oluşturulmalıdır. Bir Azure blobuna bir SAS URI 'Si, Data Factory hizmetinin bu Blobun erişmesini sağlar. Bir Azure Blob kapsayıcısının SAS URI 'Si, Data Factory hizmetinin o kapsayıcıdaki Bloblar arasında yineleme yapmasına izin verir. Daha sonra daha fazla/daha az nesneye erişim sağlamanız veya SAS URI 'sini güncelleştirmeniz gerekiyorsa, bağlantılı hizmeti yeni URI ile güncelleştirmeyi unutmayın.   

