---
title: Azure Blob depolama erişim katmanlarını otomatikleştirerek maliyetleri iyileştirin
description: Sık erişimli, seyrek erişimli ve arşiv katmanları arasında veri taşımak için otomatik kurallar oluşturun.
author: twooley
ms.author: twooley
ms.date: 10/29/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell, references_regions
ms.openlocfilehash: e0b9f3b5728e4604d7c51c1d49196cfcf1161aef
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278040"
---
# <a name="optimize-costs-by-automating-azure-blob-storage-access-tiers"></a>Azure Blob depolama erişim katmanlarını otomatikleştirerek maliyetleri iyileştirin

Veri kümelerinin benzersiz ömürleri vardır. Yaşam döngüsünün başlarında, insanlar bazı verilere sık erişir. Ancak erişim gereksinimi, verilerin yaş açısından önemli ölçüde büyük ölçüde düşmez. Bazı veriler bulutta boş kalır ve depolandıktan sonra nadiren erişilebilir. Bazı veriler oluşturulduktan sonra gün veya ay sonra dolar, diğer veri kümeleri yaşam süreleri boyunca etkin bir şekilde okunabilir ve değiştirilir. Azure Blob depolama yaşam döngüsü yönetimi, GPv2 ve BLOB depolama hesapları için zengin, kural tabanlı bir ilke sunar. Verilerinizi uygun erişim katmanlarına geçirmeye veya veri yaşam döngüsünün sonunda sona ermesini sağlamak için ilkeyi kullanın.

Yaşam döngüsü yönetimi ilkesi şunları yapmanızı sağlar:

- Performans için optimize etmek üzere erişiliyorsa seyrek erişimli bloblardan sık erişimli 'e geçiş 
- Maliyet için optimize etmek üzere bir süre boyunca erişilmesi veya değiştirilmemişse, geçiş Blobları, blob sürümleri ve BLOB anlık görüntüleri daha soğuk bir depolama katmanına (seyrek erişimli, sık erişimli ve arşiv için sık erişimli)
- Bloblarını, blob sürümlerini ve BLOB anlık görüntülerini, kullanım ömrü sonunda silin
- Depolama hesabı düzeyinde günde bir kez çalıştırılacak kuralları tanımlama
- Kuralları kapsayıcılara veya Blobların bir alt kümesine uygulama (ad öneklerini veya [blob dizini etiketlerini](storage-manage-find-blobs.md) filtre olarak kullanarak)

Yaşam döngüsünün erken aşamaları sırasında, ancak iki hafta sonra zaman içinde sık sık erişim aldığı bir senaryo düşünün. İlk ayın ötesinde veri kümesine nadiren erişilir. Bu senaryoda, etkin depolama, erken aşamalar sırasında en iyisidir. Seyrek Erişimli Depolama, büyük olasılıkla erişim için en uygun. Arşiv depolaması, verilerin bir ay boyunca yaşından sonraki en iyi katman seçeneğidir. Depolama katmanlarını verilerin yaşına göre ayarlayarak, gereksinimlerinize göre en düşük maliyetli depolama seçeneklerini tasarlayabilirsiniz. Bu geçişe ulaşmak için, eskime verilerini daha soğuk katmanlara taşımak amacıyla yaşam döngüsü yönetimi ilke kuralları kullanılabilir.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

>[!NOTE]
>Örneğin, StorSimple tarafından kullanıldığında okunabilir kalabilmeniz için veri gerekiyorsa, Blobları arşiv katmanına taşımak için bir ilke ayarlamayın.

## <a name="availability-and-pricing"></a>Kullanılabilirlik ve fiyatlandırma

Yaşam döngüsü yönetimi özelliği, Genel Amaçlı v2 (GPv2) hesapları, BLOB depolama hesapları, Premium Blok Blobu depolama hesapları ve Azure Data Lake Storage 2. hesapları için tüm Azure bölgelerinde kullanılabilir. Azure portal, var olan bir Genel Amaçlı (GPv1) hesabını bir GPv2 hesabına yükseltebilirsiniz. Depolama hesapları hakkında daha fazla bilgi için bkz. [Azure depolama hesabına genel bakış](../common/storage-account-overview.md).

Yaşam döngüsü yönetimi özelliği ücretsizdir. Müşteriler, [BLOB katmanı](/rest/api/storageservices/set-blob-tier) API çağrıları kümesi için normal işlem maliyeti üzerinden ücretlendirilir. Silme işlemi ücretsizdir. Fiyatlandırma hakkında daha fazla bilgi için bkz. [Blok Blobu fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="add-or-remove-a-policy"></a>İlke ekleme veya kaldırma

Aşağıdaki yöntemlerden birini kullanarak bir ilkeyi ekleyebilir, düzenleyebilir veya kaldırabilirsiniz:

* [Azure portalındaki](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Azure CLI](/cli/azure/install-azure-cli)
* [REST API'leri](/rest/api/storagerp/managementpolicies)

Bir ilke, tam olarak okunabilir veya yazılabilir. Kısmi güncelleştirmeler desteklenmiyor. 

> [!NOTE]
> Depolama hesabınız için güvenlik duvarı kurallarını etkinleştirirseniz, yaşam döngüsü yönetimi istekleri engellenebilir. Güvenilen Microsoft hizmetleri için özel durumlar sağlayarak bu isteklerin engellemesini kaldırabilirsiniz. Daha fazla bilgi için bkz. [güvenlik duvarlarını ve sanal ağları yapılandırma](../common/storage-network-security.md#exceptions)Içindeki özel durumlar bölümü.

Bu makalede Portal ve PowerShell yöntemlerini kullanarak ilkenin nasıl yönetileceği gösterilmektedir.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal bir ilke eklemenin iki yolu vardır. 

* [Azure portal liste görünümü](#azure-portal-list-view)
* [Azure portal kod görünümü](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Azure portal liste görünümü

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Azure portal, depolama hesabınızı arayıp seçin. 

1. **BLOB hizmeti** altında, kurallarınızı görüntülemek veya değiştirmek Için **yaşam döngüsü yönetimi** ' ni seçin.

1. **Liste görünümü** sekmesini seçin.

1. **Kural Ekle** ' yi seçin ve kuralları **Ayrıntılar** formunda adlandırın. **Kural kapsamını**, **BLOB türünü** ve **BLOB alt türü** değerlerini de ayarlayabilirsiniz. Aşağıdaki örnek, blob 'ları filtrelemek için kapsamı ayarlar. Bu, **filtre kümesi** sekmesinin eklenmesine neden olur.

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-details.png" alt-text="Yaşam döngüsü yönetimi Azure portal bir kural ayrıntıları ekleme sayfası":::

1. Kuralınızın koşullarını ayarlamak için **temel blob 'ları** seçin. Aşağıdaki örnekte, Bloblar 30 gün boyunca değiştirilmediyse seyrek erişimli depolamaya taşınır.

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-base-blobs.png" alt-text="Azure portal 'de yaşam döngüsü yönetimi temel blob 'ları sayfası":::

   **Son erişilen** seçenek, önizleme aşamasında aşağıdaki bölgelerde kullanılabilir:

    - Orta Fransa
    - Doğu Kanada
    - Orta Kanada

   > [!IMPORTANT]
   > Son erişim zamanı izleme önizlemesi yalnızca üretim dışı kullanım için geçerlidir. Üretim hizmet düzeyi sözleşmeleri (SLA 'Lar) Şu anda kullanılamıyor.
   
   **Son erişme** seçeneğini kullanmak Için Azure Portal **yaşam döngüsü yönetimi** sayfasında **erişim izleme etkin** ' i seçin. **Son erişilen** seçenek hakkında daha fazla bilgi için bkz. [son erişme tarihine göre verileri taşıma (Önizleme)](#move-data-based-on-last-accessed-date-preview).

1. **Ayrıntılar** sayfasındaki **filtrelerle blob 'ları sınırla** ' yı seçtiyseniz, isteğe bağlı bir filtre eklemek için **filtre ayarla** ' yı seçin. Aşağıdaki örnek, *mylifecyclecontainer* kapsayıcısında "log" ile başlayan bloblarda filtre uygular.

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-filter-set.png" alt-text="Azure portal yaşam döngüsü yönetimi filtre kümesi sayfası":::

1. Yeni ilkeyi eklemek için **Ekle** ' yi seçin.

#### <a name="azure-portal-code-view"></a>Azure portal kod görünümü
1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Azure portal, depolama hesabınızı arayıp seçin.

1. **BLOB hizmeti** altında, ilkenizi görüntülemek veya değiştirmek Için **yaşam döngüsü yönetimi** ' ni seçin.

1. Aşağıdaki JSON, **kod görünümü** sekmesine yapıştırılabilecek bir ilke örneğidir.

   ```json
   {
     "rules": [
       {
         "enabled": true,
         "name": "move-to-cool",
         "type": "Lifecycle",
         "definition": {
           "actions": {
             "baseBlob": {
               "tierToCool": {
                 "daysAfterModificationGreaterThan": 30
               }
             }
           },
           "filters": {
             "blobTypes": [
               "blockBlob"
             ],
             "prefixMatch": [
               "mylifecyclecontainer/log"
             ]
           }
         }
       }
     ]
   }
   ```

1. **Kaydet**’i seçin.

1. Bu JSON örneği hakkında daha fazla bilgi için bkz. [ilke](#policy) ve [kurallar](#rules) bölümleri.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aşağıdaki PowerShell betiği, depolama hesabınıza bir ilke eklemek için kullanılabilir. `$rgname`Değişken, kaynak grubu adınızla başlatılmalıdır. `$accountName`Değişken, depolama hesabı adınızla başlatılmalıdır.

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
Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1
```

# <a name="template"></a>[Şablon](#tab/template)

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
| `name`         | Dize |Bir kural adı en fazla 256 alfasayısal karakter içerebilir. Kural adı büyük/küçük harfe duyarlıdır. Bir ilke içinde benzersiz olmalıdır. | Doğru |
| `enabled`      | Boole | Bir kuralın geçici olarak devre dışı bırakılması için isteğe bağlı bir Boole değeri. Ayarlanmamışsa varsayılan değer true 'dur. | Yanlış | 
| `type`         | Sabit listesi değeri | Geçerli geçerli tür `Lifecycle` . | Doğru |
| `definition`   | Yaşam döngüsü kuralını tanımlayan bir nesne | Her tanım bir filtre kümesinden ve bir eylem kümesinden oluşur. | Doğru |

## <a name="rules"></a>Kurallar

Her kural tanımı bir filtre kümesi ve bir eylem kümesi içerir. [Filtre kümesi](#rule-filters) , kural eylemlerini bir kapsayıcı veya nesne adları içindeki belirli bir nesne kümesiyle sınırlandırır. [Eylem kümesi](#rule-actions) katman veya silme eylemlerini filtrelenmiş nesne kümesine uygular.

### <a name="sample-rule"></a>Örnek kural

Aşağıdaki örnek kural, içinde bulunan ve ile başlayan nesneler üzerinde eylemleri çalıştırmak için hesabı filtreler `container1` `foo` .

>[!NOTE]
>- Yaşam döngüsü yönetimi Blok Blobu ve ekleme blobu türlerini destekler.<br>
>- Yaşam döngüsü yönetimi, $logs ve $web gibi sistem kapsayıcılarını etkilemez.

- Katman blobu son değişiklikten sonra 30 gün sonra seyrek erişimli katmana
- Son değişiklikten sonra katman blobu 90 gün sonra arşiv katmanı
- Son değişiklikten sonra blob 2.555 gün (yedi yıl) silme
- Önceki blob sürümlerini silme sonrasında 90 gün sonra

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "rulefoo",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "delete": {
              "daysAfterCreationGreaterThan": 90
            }
          },
          "baseBlob": {
            "tierToCool": {
              "daysAfterModificationGreaterThan": 30
            },
            "tierToArchive": {
              "daysAfterModificationGreaterThan": 90
            },
            "delete": {
              "daysAfterModificationGreaterThan": 2555
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "container1/foo"
          ]
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>Kural filtreleri

Filtre, kural eylemlerini depolama hesabındaki Blobların bir alt kümesiyle sınırlar. Birden çok filtre tanımlanmışsa, `AND` Tüm filtrelerdeki mantıksal bir çalışır.

Filtreler aşağıdakileri içerir:

| Filtre adı | Filtre türü | Notlar | Gereklidir |
|-------------|-------------|-------|-------------|
| blobTypes   | Önceden tanımlanmış sabit listesi değerleri dizisi. | Geçerli yayın ve destekler `blockBlob` `appendBlob` . İçin yalnızca silme desteklenir `appendBlob` , set Tier desteklenmez. | Yes |
| prefixMatch | Öneklerin eşleştirileceği dizeler dizisi. Her kural en fazla 10 ön ek tanımlayabilir. Önek dizesinin bir kapsayıcı adıyla başlaması gerekir. Örneğin, bir kural için altındaki tüm Blobları eşleştirmek istiyorsanız, `https://myaccount.blob.core.windows.net/container1/foo/...` prefixMatch olur `container1/foo` . | PrefixMatch tanımlayamazsınız, kural depolama hesabındaki tüm Bloblar için geçerlidir. | Hayır |
| blobIndexMatch | Blob dizini etiketi anahtarından ve eşleştirilecek değer koşullarından oluşan Sözlük değerleri dizisi. Her kural, en fazla 10 blob Dizin etiketi koşulunu tanımlayabilir. Örneğin, bir kural için altındaki tüm Blobları eşleştirmek istiyorsanız `Project = Contoso` `https://myaccount.blob.core.windows.net/` , blobIndexMatch olur `{"name": "Project","op": "==","value": "Contoso"}` . | BlobIndexMatch tanımlayamazsınız, kural depolama hesabındaki tüm Bloblar için geçerlidir. | Hayır |

> [!NOTE]
> Blob dizini ortak önizlemededir ve **Kanada Orta**, **Kanada Doğu**, **Fransa orta** ve **Fransa Güney** bölgelerinde kullanılabilir. Bu özellik hakkında bilinen sorunlar ve sınırlamalar hakkında daha fazla bilgi edinmek için bkz. [blob dizini (Önizleme) Ile Azure Blob depolama üzerinde verileri yönetme ve bulma](storage-manage-find-blobs.md).

### <a name="rule-actions"></a>Kural eylemleri

İşlemler, çalışma koşulu karşılandığında filtrelenmiş bloblara uygulanır.

Yaşam döngüsü yönetimi, Blobları, önceki blob sürümlerini ve BLOB anlık görüntülerini katmanlamayı ve silmeyi destekler. Temel Bloblar, önceki blob sürümleri veya blob anlık görüntüleri üzerinde her kural için en az bir eylem tanımlayın.

| Eylem                      | Temel blob                                  | Anlık Görüntü      | Sürüm
|-----------------------------|--------------------------------------------|---------------|---------------|
| tierToCool                  | İçin desteklenir `blockBlob`                  | Desteklenir     | Desteklenir     |
| Enableoto Tiertohotfromcool | İçin desteklenir `blockBlob`                  | Desteklenmez | Desteklenmez |
| tierToArchive               | İçin desteklenir `blockBlob`                  | Desteklenir     | Desteklenir     |
| delete                      | Ve için desteklenir `blockBlob``appendBlob` | Desteklenir     | Desteklenir     |

>[!NOTE]
>Aynı blob üzerinde birden fazla eylem tanımlarsanız, yaşam döngüsü yönetimi Blobun en az maliyetli eylemi uygular. Örneğin eylem, `delete` eylemden daha fazla `tierToArchive` . Eylem `tierToArchive` eylemden daha fazla `tierToCool` .

Çalışma koşulları yaşa göre yapılır. Temel blob 'lar son değiştirme süresini kullanır, blob sürümleri sürüm oluşturma süresini kullanır ve BLOB anlık görüntüleri, yaşı izlemek için anlık görüntü oluşturma süresi kullanır.

| Eylem çalıştırma koşulu               | Koşul değeri                          | Description                                                                      |
|------------------------------------|------------------------------------------|----------------------------------------------------------------------------------|
| daysAfterModificationGreaterThan   | Yaşı gün olarak gösteren tamsayı değeri | Temel blob eylemleri için koşul                                              |
| daysAfterCreationGreaterThan       | Yaşı gün olarak gösteren tamsayı değeri | Blob sürümü ve BLOB anlık görüntü eylemleri için koşul                         |
| daysAfterLastAccessTimeGreaterThan | Yaşı gün olarak gösteren tamsayı değeri | Önizle Son erişim zamanı etkinleştirildiğinde temel blob eylemlerine yönelik koşul |

## <a name="examples"></a>Örnekler

Aşağıdaki örneklerde, yaşam döngüsü ilke kurallarıyla yaygın senaryoların nasıl ele alınacağını gösterilmektedir.

### <a name="move-aging-data-to-a-cooler-tier"></a>Eskime verilerini daha soğuk bir katmana taşıyın

Bu örnek, veya ile ön önekli blok bloblarının nasıl yapılacağını gösterir `container1/foo` `container2/bar` . İlke, 30 gün boyunca seyrek erişimli depolamaya değiştirilmeyen Blobları ve arşiv katmanına 90 gün içinde değiştirilmeyen Blobları geçirir:

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

### <a name="move-data-based-on-last-accessed-date-preview"></a>Son erişme tarihine göre verileri taşıma (Önizleme)

Blob 'unuza son okuma veya yazma yapıldığında bir kaydı tutmak için son erişim zamanı izlemeyi etkinleştirebilirsiniz. Blob verilerinizin katmanlamasını ve bekletilmesini yönetmek için filtre olarak son erişim zamanı ' nı kullanabilirsiniz.

**Son erişilen** seçenek, önizleme aşamasında aşağıdaki bölgelerde kullanılabilir:

 - Orta Fransa
 - Doğu Kanada
 - Orta Kanada

> [!IMPORTANT]
> Son erişim zamanı izleme önizlemesi yalnızca üretim dışı kullanım için geçerlidir. Üretim hizmet düzeyi sözleşmeleri (SLA 'Lar) Şu anda kullanılamıyor.

**Son erişme** seçeneğini kullanmak Için Azure Portal **yaşam döngüsü yönetimi** sayfasında **erişim izleme etkin** ' i seçin.

#### <a name="how-last-access-time-tracking-works"></a>Son erişim zamanı izleme nasıl kullanılır

Son erişim zaman izleme etkinleştirildiğinde, `LastAccessTime` bir blob okuma veya yazma sırasında çağrılan blob özelliği güncellenir. [BLOB al](/rest/api/storageservices/get-blob) işlemi erişim işlemi olarak kabul edilir. [BLOB özelliklerini al](/rest/api/storageservices/get-blob-properties), [BLOB meta verilerini al](/rest/api/storageservices/get-blob-metadata)ve [BLOB etiketlerini al](/rest/api/storageservices/get-blob-tags) , erişim işlemleri değildir ve bu nedenle son erişim zamanını güncelleştirmez.

Okuma erişimi gecikmesi üzerindeki etkiyi en aza indirmek için, son 24 saatin yalnızca ilk okuması son erişim zamanını güncelleştirir. Aynı 24 saatlik dönemdeki sonraki okumalar, son erişim zamanını güncelleştirmez. Okuma arasında bir blob değiştirilirse, son erişim zamanı iki değerden daha yeni bir yöntemdir.

Aşağıdaki örnekte, Bloblar 30 gün boyunca erişilmediyse seyrek erişimli depolamaya taşınır. `enableAutoTierToHotFromCool`Özelliği, bir Blobun, seyrek erişimli olduktan sonra yeniden erişilmesi durumunda bir blob 'un otomatik olarak soğuk geri dönerek etkin hale getirimeyeceğini belirten bir Boole değeridir.

```json
{
  "enabled": true,
  "name": "last-accessed-thirty-days-ago",
  "type": "Lifecycle",
  "definition": {
    "actions": {
      "baseBlob": {
        "enableAutoTierToHotFromCool": true,
        "tierToCool": {
          "daysAfterLastAccessTimeGreaterThan": 30
        }
      }
    },
    "filters": {
      "blobTypes": [
        "blockBlob"
      ],
      "prefixMatch": [
        "mylifecyclecontainer/log"
      ]
    }
  }
}
```

#### <a name="storage-account-support"></a>Depolama hesabı desteği

Son erişim zamanı izleme, aşağıdaki depolama hesabı türleri için kullanılabilir:

 - Genel amaçlı v2 depolama hesapları
 - BLOB depolama hesaplarını engelle
 - Blob Storage hesapları

Depolama Hesabınız genel amaçlı bir v1 hesabıdır, genel amaçlı v2 hesabına yükseltmek için Azure portal kullanın.

Azure Data Lake Storage 2. ile kullanım için etkinleştirilmiş hiyerarşik bir ad alanı olan depolama hesapları artık desteklenmektedir.

#### <a name="pricing-and-billing"></a>Fiyatlandırma ve Faturalama

Her bir son erişim zamanı güncelleştirmesi başka bir [işlem](https://azure.microsoft.com/pricing/details/storage/blobs/)olarak kabul edilir.

### <a name="archive-data-after-ingest"></a>Aldıktan sonra verileri Arşivle

Bazı veriler bulutta boş kalır ve şimdiye kadar, kaydedildikten sonra erişiliyorsa nadiren olur. Aşağıdaki yaşam döngüsü ilkesi, verileri alındıktan sonra arşivlemek üzere yapılandırılmıştır. Bu örnek, kapsayıcı içindeki depolama hesabındaki Blobları `archivecontainer` bir arşiv katmanına geçirir. Geçiş, son değiştirilme zamanından sonra blob 0 gün sonra işlem yaparak gerçekleştirilir:

> [!NOTE] 
> Bloblarınızı doğrudan arşiv katmanının daha verimli olması için yüklemeniz önerilir. [PutBlob](/rest/api/storageservices/put-blob) veya [putblocklist](/rest/api/storageservices/put-block-list) için x-MS-Access-Tier üst bilgisini Rest sürüm 2018-11-09 ve daha yeni veya en son BLOB depolama istemci kitaplıklarımızla birlikte kullanabilirsiniz. 

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

### <a name="delete-data-with-blob-index-tags"></a>Blob dizini etiketleriyle verileri silme
Bazı verilerin yalnızca silinmek üzere işaretlenmişse zaman aşımına uğradı. Bir yaşam döngüsü yönetim ilkesini, blob dizini anahtar/değer öznitelikleriyle etiketlenmiş verilerin süresi dolacak şekilde yapılandırabilirsiniz. Aşağıdaki örnek, ile etiketlenmiş tüm blok bloblarını silen bir ilke gösterir `Project = Contoso` . Blob dizini hakkında daha fazla bilgi edinmek için bkz. [blob dizini (Önizleme) Ile Azure Blob depolama üzerinde verileri yönetme ve bulma](storage-manage-find-blobs.md).

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "DeleteContosoData",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "delete": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Project",
                            "op": "==",
                            "value": "Contoso"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ]
                }
            }
        }
    ]
}
```

### <a name="manage-versions"></a>Sürümleri yönetme

Düzenli olarak değiştirilen ve erişilen veriler için, bir nesnenin önceki sürümlerini otomatik olarak sürdürmek üzere blob Storage sürümü oluşturmayı etkinleştirebilirsiniz. Daha önceki sürümleri katmana veya silmeye yönelik bir ilke oluşturabilirsiniz. Sürüm yaşı, sürüm oluşturma saati hesaplanarak belirlenir. Bu ilke kuralı `activedata` , kapsayıcıda sürüm oluşturulduktan sonra, seyrek erişimli katmana 90 gün veya daha eski olan önceki sürümleri katmanlar ve 365 gün ya da daha eski sürümleri siler.

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "versionrule",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "tierToCool": {
              "daysAfterCreationGreaterThan": 90
            },
            "delete": {
              "daysAfterCreationGreaterThan": 365
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "activedata"
          ]
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

Güncelleştirilmiş ilkenin etkili olması 24 saate kadar sürer. İlke etkin olduktan sonra, eylemlerin çalışması 24 saate kadar sürebilir. Bu nedenle, ilke eylemlerinin tamamlanması 48 saat kadar sürebilir.

**Arşivlenmiş bir blobu el ile yeniden aldım, bunun arşiv katmanına geçici olarak geri taşınmasını nasıl önleyebilirim?**

Bir blob bir erişim katmanından diğerine taşındığında, son değiştirilme zamanı değişmez. Arşivlenmiş bir blobu sık erişimli katmana el ile yeniden alırsanız, yaşam döngüsü yönetim altyapısı tarafından arşiv katmanına geri taşınır. Yeniden arşivlenmesini engellemek için bu blobu geçici olarak etkileyen kuralı devre dışı bırakın. Blob güvenli bir şekilde arşiv katmanına geri taşınabileceği zaman kuralı yeniden etkinleştirin. Blob 'u, etkin veya seyrek erişimli katmanda kalıcı olarak kalması gerekiyorsa başka bir konuma da kopyalayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Yanlışlıkla silinmeden sonra verileri nasıl kurtaracağınızı öğrenin:

- [Azure Depolama blobları için geçici silme](./soft-delete-blob-overview.md)

Blob diziniyle verileri yönetme ve bulma hakkında bilgi edinin:

- [Blob Dizini ile Azure Blob Depolama'da verileri bulma ve yönetme](storage-manage-find-blobs.md)