---
title: Azure depolama yaşam döngüsünü yönetme
description: Sık erişimli verileri sık erişimli ve arşiv katmanlarına geçirmeye yönelik yaşam döngüsü ilke kuralları oluşturmayı öğrenin.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/21/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.openlocfilehash: 41e1228d127ddbbf0749036fc6f0129da1208bc7
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74077129"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Azure Blob depolama yaşam döngüsünü yönetme

Veri kümelerinin benzersiz ömürleri vardır. Yaşam döngüsünün başlarında, insanlar bazı verilere sık erişir. Ancak erişim gereksinimi, verilerin yaş açısından önemli ölçüde büyük ölçüde düşmez. Bazı veriler bulutta boş kalır ve depolandıktan sonra nadiren erişilebilir. Bazı veriler oluşturulduktan sonra gün veya ay sonra dolar, diğer veri kümeleri yaşam süreleri boyunca etkin bir şekilde okunabilir ve değiştirilir. Azure Blob depolama yaşam döngüsü yönetimi, GPv2 ve BLOB depolama hesapları için zengin, kural tabanlı bir ilke sunar. Verilerinizi uygun erişim katmanlarına geçirmeye veya veri yaşam döngüsünün sonunda sona ermesini sağlamak için ilkeyi kullanın.

Yaşam döngüsü yönetimi ilkesi şunları yapmanızı sağlar:

- Performansı ve maliyeti iyileştirmek için Blobları daha soğuk bir depolama katmanına (seyrek erişimli, sık erişimli, arşiv veya seyrek erişimli) geçiş
- Bloblarını son kullanım ömrü sonunda silin
- Depolama hesabı düzeyinde günde bir kez çalıştırılacak kuralları tanımlama
- Kapsayıcılara veya Blobların bir alt kümesine kuralları uygulama (ön ekleri filtre olarak kullanma)

Yaşam döngüsünün erken aşamaları sırasında, ancak iki hafta sonra zaman içinde sık sık erişim aldığı bir senaryo düşünün. İlk ayın ötesinde veri kümesine nadiren erişilir. Bu senaryoda, etkin depolama, erken aşamalar sırasında en iyisidir. Seyrek Erişimli Depolama, büyük olasılıkla erişim için en uygun. Arşiv depolaması, verilerin bir ay boyunca yaşından sonraki en iyi katman seçeneğidir. Depolama katmanlarını verilerin yaşına göre ayarlayarak, gereksinimlerinize göre en düşük maliyetli depolama seçeneklerini tasarlayabilirsiniz. Bu geçişe ulaşmak için, eskime verilerini daha soğuk katmanlara taşımak amacıyla yaşam döngüsü yönetimi ilke kuralları kullanılabilir.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-account-support"></a>Depolama hesabı desteği

Yaşam döngüsü yönetimi ilkesi Genel Amaçlı v2 (GPv2) hesapları, BLOB depolama hesapları ve Premium Blok Blob depolama hesapları ile kullanılabilir. Azure portal, var olan bir Genel Amaçlı (GPv1) hesabını bir GPv2 hesabına yükseltebilirsiniz. Depolama hesapları hakkında daha fazla bilgi için bkz. [Azure Depolama hesabına genel bakış](../common/storage-account-overview.md).  

## <a name="pricing"></a>Fiyatlandırma

Yaşam döngüsü yönetimi özelliği ücretsizdir. Müşteriler, [liste Blobları](https://docs.microsoft.com/rest/api/storageservices/list-blobs) için normal işlem maliyeti üzerinden ücretlendirilir ve [BLOB katmanı](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) API çağrılarını ayarlar. Silme işlemi ücretsizdir. Fiyatlandırma hakkında daha fazla bilgi için bkz. [Blok Blobu fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability"></a>Bölgesel kullanılabilirlik

Yaşam döngüsü yönetimi özelliği tüm Azure bölgelerinde kullanılabilir.

## <a name="add-or-remove-a-policy"></a>İlke ekleme veya kaldırma

Aşağıdaki yöntemlerden birini kullanarak bir ilkeyi ekleyebilir, düzenleyebilir veya kaldırabilirsiniz:

* [Azure Portal](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [REST API'leri](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

Bu makalede Portal ve PowerShell yöntemlerini kullanarak ilkenin nasıl yönetileceği gösterilmektedir.  

> [!NOTE]
> Depolama hesabınız için güvenlik duvarı kurallarını etkinleştirirseniz, yaşam döngüsü yönetimi istekleri engellenebilir. Güvenilen Microsoft Hizmetleri için özel durumlar sağlayarak bu isteklerin engellemesini kaldırabilirsiniz. Daha fazla bilgi için bkz. [güvenlik duvarlarını ve sanal ağları yapılandırma](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)Içindeki özel durumlar bölümü.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Azure portal bir ilke eklemenin iki yolu vardır. 

* [Azure portal liste görünümü](#azure-portal-list-view)
* [Azure portal kod görünümü](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Azure portal liste görünümü

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. **Tüm kaynaklar** ' ı seçin ve ardından depolama hesabınızı seçin.

3. **BLOB hizmeti**altında, kurallarınızı görüntülemek veya değiştirmek Için **yaşam döngüsü yönetimi** ' ni seçin.

4. **Liste görünümü** sekmesini seçin.

5. **Kural Ekle** ' yi seçin ve ardından **eylem kümesi** form alanlarını doldurun. Aşağıdaki örnekte, Bloblar 30 gün boyunca değiştirilmediyse seyrek erişimli depolamaya taşınır.

   ![Yaşam döngüsü yönetimi eylem kümesi sayfası Azure portal](media/storage-lifecycle-management-concepts/lifecycle-management-action-set.png)

6. İsteğe bağlı bir filtre eklemek için **filtre ayarla** ' yı seçin. Ardından, Filtrelenecek bir kapsayıcı ve klasör belirtmek için, **Araştır** ' ı seçin.

   ![Azure portal yaşam döngüsü yönetimi filtre kümesi sayfası](media/storage-lifecycle-management-concepts/lifecycle-management-filter-set-browse.png)

8. İlke ayarlarını gözden geçirmek için **gözden geçir + Ekle** ' yi seçin.

9. Yeni ilkeyi eklemek için **Ekle** ' yi seçin.

#### <a name="azure-portal-code-view"></a>Azure portal kod görünümü
1. [Azure portalında](https://portal.azure.com) oturum açın.

2. **Tüm kaynaklar** ' ı seçin ve ardından depolama hesabınızı seçin.

3. **BLOB hizmeti**altında, ilkenizi görüntülemek veya değiştirmek Için **yaşam döngüsü yönetimi** ' ni seçin.

4. Aşağıdaki JSON, **kod görünümü** sekmesine yapıştırılabilecek bir ilke örneğidir.

   ```json
   {
     "rules": [
       {
         "name": "ruleFoo",
         "enabled": true,
         "type": "Lifecycle",
         "definition": {
           "filters": {
             "blobTypes": [ "blockBlob" ],
             "prefixMatch": [ "container1/foo" ]
           },
           "actions": {
             "baseBlob": {
               "tierToCool": { "daysAfterModificationGreaterThan": 30 },
               "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
               "delete": { "daysAfterModificationGreaterThan": 2555 }
             },
             "snapshot": {
               "delete": { "daysAfterCreationGreaterThan": 90 }
             }
           }
         }
       }
     ]
   }
   ```

5. **Kaydet**’i seçin.

6. Bu JSON örneği hakkında daha fazla bilgi için bkz. [ilke](#policy) ve [kurallar](#rules) bölümleri.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Aşağıdaki PowerShell betiği, depolama hesabınıza bir ilke eklemek için kullanılabilir. `$rgname` değişkeni, kaynak grubu adınızla başlatılmalıdır. `$accountName` değişkeni, depolama hesabı adınızla başlatılmalıdır.

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery

#Initialize the following with your resource group and storage account names
$rgname = ""
$accountName = ""

#Create a new action object
$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete -daysAfterModificationGreaterThan 2555
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToArchive -daysAfterModificationGreaterThan 90
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToCool -daysAfterModificationGreaterThan 30
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -SnapshotAction Delete -daysAfterCreationGreaterThan 90

# Create a new filter object
# PowerShell automatically sets BlobType as “blockblob” because it is the only available option currently
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd

#Create a new rule object
#PowerShell automatically sets Type as “Lifecycle” because it is the only available option currently
$rule1 = New-AzStorageAccountManagementPolicyRule -Name Test -Action $action -Filter $filter

#Set the policy
$policy = Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1
```

# <a name="templatetabtemplate"></a>[Şablon](#tab/template)

Yaşam döngüsü yönetimini Azure Resource Manager şablonları kullanarak tanımlayabilirsiniz. Bir RA-GRS GPv2 depolama hesabını bir yaşam döngüsü yönetimi ilkesiyle dağıtmaya yönelik örnek bir şablon aşağıda verilmiştir.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "storageAccountName": "[uniqueString(resourceGroup().id)]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-04-01",
      "sku": {
        "name": "Standard_RAGRS"
      },
      "kind": "StorageV2",
      "properties": {
        "networkAcls": {}
      }
    },
    {
      "name": "[concat(variables('storageAccountName'), '/default')]",
      "type": "Microsoft.Storage/storageAccounts/managementPolicies",
      "apiVersion": "2019-04-01",
      "dependsOn": [
        "[variables('storageAccountName')]"
      ],
      "properties": {
        "policy": {...}
      }
    }
  ],
  "outputs": {}
}
```

---

## <a name="policy"></a>İlke

Yaşam döngüsü yönetimi ilkesi, bir JSON belgesindeki kuralların koleksiyonudur:

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```

İlke bir kural koleksiyonudur:

| Parametre adı | Parametre türü | Notlar |
|----------------|----------------|-------|
| `rules`        | Kural nesneleri dizisi | İlkede en az bir kural gereklidir. Bir ilkede en fazla 100 kuralı tanımlayabilirsiniz.|

İlke içindeki her bir kural birkaç parametreye sahiptir:

| Parametre adı | Parametre türü | Notlar | Gerekli |
|----------------|----------------|-------|----------|
| `name`         | Dize |Bir kural adı en fazla 256 alfasayısal karakter içerebilir. Kural adı büyük/küçük harfe duyarlıdır.  Bir ilke içinde benzersiz olmalıdır. | True |
| `enabled`      | Boole | Bir kuralın geçici olarak devre dışı bırakılması için isteğe bağlı bir Boole değeri. Ayarlanmamışsa varsayılan değer true 'dur. | False | 
| `type`         | Sabit listesi değeri | Geçerli geçerli tür `Lifecycle`. | True |
| `definition`   | Yaşam döngüsü kuralını tanımlayan bir nesne | Her tanım bir filtre kümesinden ve bir eylem kümesinden oluşur. | True |

## <a name="rules"></a>Kurallar

Her kural tanımı bir filtre kümesi ve bir eylem kümesi içerir. [Filtre kümesi](#rule-filters) , kural eylemlerini bir kapsayıcı veya nesne adları içindeki belirli bir nesne kümesiyle sınırlandırır. [Eylem kümesi](#rule-actions) katman veya silme eylemlerini filtrelenmiş nesne kümesine uygular.

### <a name="sample-rule"></a>Örnek kural

Aşağıdaki örnek kural, `container1` içinde bulunan nesneler üzerinde eylemleri çalıştırmak ve `foo`başlamak için hesabı filtreler.  

- Katman blobu son değişiklikten sonra 30 gün sonra seyrek erişimli katmana
- Son değişiklikten sonra katman blobu 90 gün sonra arşiv katmanı
- Son değişiklikten sonra blob 2.555 gün (yedi yıl) silme
- Anlık görüntü oluşturulduktan 90 gün sonra blob anlık görüntülerini Sil

```json
{
  "rules": [
    {
      "name": "ruleFoo",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
            "delete": { "daysAfterModificationGreaterThan": 2555 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>Kural filtreleri

Filtre, kural eylemlerini depolama hesabındaki Blobların bir alt kümesiyle sınırlar. Birden çok filtre tanımlanmışsa, bir mantıksal `AND` tüm filtreler üzerinde çalışır.

Filtreler şunları içerir:

| Filtre adı | Filtre türü | Notlar | Gereklidir |
|-------------|-------------|-------|-------------|
| blobTypes   | Önceden tanımlanmış sabit listesi değerleri dizisi. | Geçerli yayın `blockBlob`destekler. | Yes |
| prefixMatch | Öneklerin eşleşmesi için bir dize dizisi. Her kural en fazla 10 ön ek tanımlayabilir. Önek dizesinin bir kapsayıcı adıyla başlaması gerekir. Örneğin, bir kural için `https://myaccount.blob.core.windows.net/container1/foo/...` altındaki tüm Blobları eşleştirmek istiyorsanız, prefixMatch `container1/foo`. | PrefixMatch tanımlayamazsınız, kural depolama hesabındaki tüm Bloblar için geçerlidir.  | Hayır |

### <a name="rule-actions"></a>Kural eylemleri

İşlemler, çalışma koşulu karşılandığında filtrelenmiş bloblara uygulanır.

Yaşam döngüsü yönetimi, Blobları ve silme işlemini ve BLOB anlık görüntülerinin silinmesini destekler. Bloblarda veya blob anlık görüntülerinde her kural için en az bir eylem tanımlayın.

| Eylem        | Temel blob                                   | Anlık Görüntü      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Şu anda sık erişimli bir katmanda blob 'ları destekle         | Desteklenmiyor |
| tierToArchive | Şu anda sık erişimli veya seyrek erişimli bir katmanda blob 'ları destekleme | Desteklenmiyor |
| delete        | Destekleniyor                                   | Destekleniyor     |

>[!NOTE]
>Aynı blob üzerinde birden fazla eylem tanımlarsanız, yaşam döngüsü yönetimi Blobun en az maliyetli eylemi uygular. Örneğin eylem `delete`, eylem `tierToArchive`göre daha ucuz. Eylem `tierToArchive` `tierToCool`eylem.

Çalışma koşulları yaşa göre yapılır. Temel Bloblar, yaşı izlemek için son değiştirme süresini kullanır ve BLOB anlık görüntüleri, yaşı izlemek için anlık görüntü oluşturma süresini kullanır.

| Eylem çalıştırma koşulu             | Koşul değeri                          | Açıklama                             |
|----------------------------------|------------------------------------------|-----------------------------------------|
| daysAfterModificationGreaterThan | Yaşı gün olarak gösteren tamsayı değeri | Temel blob eylemleri için koşul     |
| daysAfterCreationGreaterThan     | Yaşı gün olarak gösteren tamsayı değeri | Blob anlık görüntü eylemleri için koşul |

## <a name="examples"></a>Örnekler

Aşağıdaki örneklerde, yaşam döngüsü ilke kurallarıyla yaygın senaryoların nasıl ele alınacağını gösterilmektedir.

### <a name="move-aging-data-to-a-cooler-tier"></a>Eskime verilerini daha soğuk bir katmana taşıyın

Bu örnek, `container1/foo` veya `container2/bar`ön önekli blok bloblarının nasıl yapılacağını gösterir. İlke, 30 gün boyunca seyrek erişimli depolamaya değiştirilmeyen Blobları ve arşiv katmanına 90 gün içinde değiştirilmeyen Blobları geçirir:

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo", "container2/bar" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="archive-data-at-ingest"></a>Alma sırasında verileri arşivleme

Bazı veriler bulutta boş kalır ve şimdiye kadar, kaydedildikten sonra erişiliyorsa nadiren olur. Aşağıdaki yaşam döngüsü ilkesi, verileri alındıktan sonra arşivlemek üzere yapılandırılmıştır. Bu örnek, kapsayıcı içindeki depolama hesabındaki blok bloblarını bir arşiv katmanına `archivecontainer` geçirir. Geçiş, son değiştirilme zamanından sonra blob 0 gün sonra işlem yaparak gerçekleştirilir:

> [!NOTE] 
> Bloblarınızı doğrudan arşiv katmanının daha verimli olması için yüklemeniz önerilir. [PutBlob](https://docs.microsoft.com/rest/api/storageservices/put-blob) veya [putblocklist](https://docs.microsoft.com/rest/api/storageservices/put-block-list) için x-MS-acess-tier başlığını Rest sürüm 2018-11-09 ve daha yeni veya en son BLOB depolama istemci kitaplıklarımızla birlikte kullanabilirsiniz. 

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

### <a name="expire-data-based-on-age"></a>Yaş temelinde verilerin süresi doluyor

Bazı verilerin oluşturulduktan sonra gün veya ay sonu bekleniyor. Bir yaşam döngüsü yönetim ilkesini, verileri veri yaşı temelinde silerek sona ermeyecek şekilde yapılandırabilirsiniz. Aşağıdaki örnek 365 günden eski olan tüm blok bloblarını silen bir ilke gösterir.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-old-snapshots"></a>Eski anlık görüntüleri Sil

Düzenli aralıklarla değiştirilen ve erişilen veriler için, anlık görüntüler genellikle verilerin eski sürümlerini izlemek için kullanılır. Anlık görüntü yaşı temelinde eski anlık görüntüleri silen bir ilke oluşturabilirsiniz. Anlık görüntü yaşı, anlık görüntü oluşturma süresi hesaplanarak belirlenir. Bu ilke kuralı, anlık görüntü oluşturulduktan sonra 90 gün veya daha eski olan kapsayıcı içindeki blok blob anlık görüntülerini siler `activedata`.

```json
{
  "rules": [
    {
      "name": "snapshotRule",
      "enabled": true,
      "type": "Lifecycle",
    "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "activedata" ]
        },
        "actions": {
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="faq"></a>SSS

**Yeni bir ilke oluşturdum, neden eylemler hemen çalıştırılmadı?**  
Platform yaşam döngüsü ilkesini günde bir kez çalıştırır. Bir ilkeyi yapılandırdıktan sonra, bazı eylemlerin ilk kez çalışması 24 saate kadar sürebilir.  

**Mevcut bir ilkeyi güncelleştirdiğimde, eylemlerin çalışması ne kadar sürer?**  
Güncelleştirilmiş ilkenin etkili olması 24 saate kadar sürer. İlke etkin olduktan sonra, eylemlerin çalışması 24 saate kadar sürebilir. Bu nedenle, ilkenin yürütülmesi 48 saat kadar sürebilir.   

**Arşivlenmiş bir blobu el ile yeniden aldım, bunun arşiv katmanına geçici olarak geri taşınmasını nasıl önleyebilirim?**  
Bir blob bir erişim katmanından diğerine taşındığında, son değiştirilme zamanı değişmez. Arşivlenmiş bir blobu sık erişimli katmana el ile yeniden alırsanız, yaşam döngüsü yönetim altyapısı tarafından arşiv katmanına geri taşınır. Yeniden arşivlenmesini engellemek için bu blobu geçici olarak etkileyen kuralı devre dışı bırakın. Blob güvenli bir şekilde arşiv katmanına geri taşınabileceği zaman kuralı yeniden etkinleştirin. Blob 'u, etkin veya seyrek erişimli katmanda kalıcı olarak kalması gerekiyorsa başka bir konuma da kopyalayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Yanlışlıkla silinmeden sonra verileri nasıl kurtaracağınızı öğrenin:

- [Azure Depolama blobları için geçici silme](../blobs/storage-blob-soft-delete.md)
