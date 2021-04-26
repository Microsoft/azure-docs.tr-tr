---
title: Azure Cosmos DB bir hesabı geri yüklemek için izinleri yapılandırın.
description: Sürekli yedekleme hesabı için geri yükleme izinlerini belirli bir role veya bir sorumluya nasıl ayıracağınızı ve kısıtlayacağınızı öğrenin. Azure portal, CLı veya özel bir rol tanımlama kullanılarak yerleşik bir rolün nasıl atanacağını gösterir.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 8b3ce2c195dc2fa3dd703306e731aa5b807b78b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100648612"
---
# <a name="manage-permissions-to-restore-an-azure-cosmos-db-account"></a>Azure Cosmos DB hesabı geri yüklemek için izinleri yönetme
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB için zaman noktası geri yükleme özelliği (sürekli yedekleme modu) Şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB, sürekli yedekleme (Önizleme) hesabının geri yükleme izinlerini belirli bir role veya bir sorumluya ayırmanıza ve kısıtlayamanıza olanak sağlar. Hesap sahibi bir geri yüklemeyi tetikleyebilir ve geri yükleme işlemini gerçekleştirmek için diğer sorumlularına bir rol atayabilir. Bu izinler, aşağıdaki görüntüde gösterildiği gibi, abonelik kapsamında veya kaynak hesap kapsamında daha ayrıntılı bir şekilde uygulanabilir:

:::image type="content" source="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" alt-text="Geri yükleme işlemi gerçekleştirmek için gerekli rollerin listesi." lightbox="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" border="false":::

Kapsam, erişimi olan bir kaynak kümesidir ve kapsamlar hakkında daha fazla bilgi edinmek için bkz. [Azure RBAC](../role-based-access-control/scope-overview.md) belgeleri. Azure Cosmos DB, geçerli kapsamlar, kullanım örneklerinin birçoğu için kaynak abonelik ve veritabanı hesabıdır. Geri yükleme eylemlerinin gerçekleştirilmesi, hedef kaynak grubu için yazma izinlerine sahip olmalıdır.

## <a name="assign-roles-for-restore-using-the-azure-portal"></a>Azure portal kullanarak geri yükleme için roller atama

Bir geri yükleme gerçekleştirmek için, bir kullanıcının veya sorumlunun geri yükleme izni ( *geri yükleme/eylem* izni) ve yeni bir hesap sağlama ( *yazma* izni) izni olması gerekir.  Bu izinleri vermek için, sahibi `CosmosRestoreOperator` ve `Cosmos DB Operator` yerleşik rollerini bir sorumluya atayabilir.

1. [Azure Portal](https://portal.azure.com/) oturum açın

1. Aboneliğinize gidin ve **erişim denetimi (IAM)** sekmesine gidin ve   >  **Rol Ekle ataması** Ekle ' yi seçin.

1. Rol **ataması Ekle** bölmesinde, **rol** alanı ' nda **cosmosrestoreoperator** rolü ' nü seçin. **Erişim ata** alanına **Kullanıcı, Grup veya hizmet sorumlusu ' nı** seçin ve aşağıdaki görüntüde gösterildiği gibi bir kullanıcının adını veya e-posta kimliğini arayın:

   :::image type="content" source="./media/continuous-backup-restore-permissions/assign-restore-operator-roles.png" alt-text="CosmosRestoreOperator ve Cosmos DB Işleç rolleri atayın." border="true":::

1. *Geri yükleme/eylem* iznini vermek için **Kaydet** ' i seçin.

1. Yazma iznini vermek için adım 3 ' ü **Cosmos DB operatör** rolüyle tekrarlayın. Bu rolü Azure portal atarken, aboneliğin tamamına geri yükleme izni verir.

## <a name="permission-scopes"></a>İzin kapsamları

|Kapsam  |Örnek  |
|---------|---------|
|Abonelik | /Subscriptions/00000000-0000-0000-0000-000000000000 |
|Kaynak grubu | /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-cosmosdb-rg |
|CosmosDB geri yüklenebilen hesap kaynağı | /Subscriptions/00000000-0000-0000-0000-000000000000/Providers/Microsoft.DocumentDB/Locations/Batı ABD/Restokıbledatabaseaccounts/23e99a35-cd36-4df4-9614-f767a03b9995|

Geri yüklenebilen hesap kaynağı, `az cosmosdb restorable-database-account list --name <accountname>` PowerShell 'de CLI veya cmdlet 'teki komutun çıktısından ayıklanabilir `Get-AzCosmosDBRestorableDatabaseAccount -DatabaseAccountName <accountname>` . Çıkışdaki Name özniteliği, geri yüklenebilen hesabın öğesini temsil eder `instanceID` . Daha fazla bilgi için bkz. [PowerShell](continuous-backup-restore-powershell.md) veya [CLI](continuous-backup-restore-command-line.md) makalesi.

## <a name="permissions"></a>İzinler

Sürekli yedekleme modu hesapları için geri yükleme ile ilgili farklı etkinlikleri gerçekleştirmek için aşağıdaki izinler gereklidir:

|İzin  |Etki  |En düşük kapsam  |En yüksek kapsam  |
|---------|---------|---------|---------|
|`Microsoft.Resources/deployments/validate/action`, `Microsoft.Resources/deployments/write` | Bu izinler, geri yüklenen hesabı oluşturmak için ARM şablon dağıtımı için gereklidir. Bu rolün nasıl ayarlanacağı için aşağıdaki örnek izin [Restokıbleaction](#custom-restorable-action) bölümüne bakın. | Uygulanamaz | Uygulanamaz  |
|`Microsoft.DocumentDB/databaseAccounts/write` | Bu izin, bir hesabı bir kaynak grubuna geri yüklemek için gereklidir | Geri yüklenen hesabın oluşturulduğu kaynak grubu. | Geri yüklenen hesabın oluşturulduğu abonelik |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action` |Bu izin, geri yükleme eylemlerinin üzerinde gerçekleştirilmesine izin vermek için kaynak yeniden yüklenebilen veritabanı hesap kapsamında gereklidir.  | Geri yüklenmekte olan kaynak hesaba ait olan *Restooybledatabaseaccount* kaynağı. Bu değer, geri `ID` yüklenebilen veritabanı hesabı kaynağının özelliği tarafından da verilir. Geri yüklenebilen hesap örneği */Subscriptions/SubscriptionID/Providers/Microsoft.DocumentDB/Locations/regionName/Restokbledatabaseaccounts/<GUID-ınstanceıd>* | Geri yüklenebilen veritabanı hesabını içeren abonelik. Kaynak grubu kapsam olarak seçilemez.  |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` |Bu izin, geri yüklenebilen veritabanı hesaplarını listelemek için kaynak restozlenebilir veritabanı hesabı kapsamında gereklidir.  | Geri yüklenmekte olan kaynak hesaba ait olan *Restooybledatabaseaccount* kaynağı. Bu değer, geri `ID` yüklenebilen veritabanı hesabı kaynağının özelliği tarafından da verilir. Geri yüklenebilen hesap örneği */Subscriptions/SubscriptionID/Providers/Microsoft.DocumentDB/Locations/regionName/Restokbledatabaseaccounts/<GUID-ınstanceıd>*| Geri yüklenebilen veritabanı hesabını içeren abonelik. Kaynak grubu kapsam olarak seçilemez.  |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` | Bu izin, geri yüklenebilen bir hesaba yönelik veritabanları ve kapsayıcılar listesi gibi geri yüklenebilen kaynakların okunmasına izin vermek için kaynak yeniden yüklenebilen hesap kapsamında gereklidir.  | Geri yüklenmekte olan kaynak hesaba ait olan *Restooybledatabaseaccount* kaynağı. Bu değer, geri `ID` yüklenebilen veritabanı hesabı kaynağının özelliği tarafından da verilir. Geri yüklenebilen hesap örneği */Subscriptions/SubscriptionID/Providers/Microsoft.DocumentDB/Locations/regionName/Restokbledatabaseaccounts/<GUID-ınstanceıd>*| Geri yüklenebilen veritabanı hesabını içeren abonelik. Kaynak grubu kapsam olarak seçilemez. |

## <a name="azure-cli-role-assignment-scenarios-to-restore-at-different-scopes"></a>Farklı kapsamlardan geri yüklemek için Azure CLı rol atama senaryoları

İzin içeren roller, bir abonelikte veya belirli bir hesapta geri yükleme işlemini gerçekleştirebilen bir ayrıntılı denetim elde etmek için farklı kapsamlara atanabilir.

### <a name="assign-capability-to-restore-from-any-restorable-account-in-a-subscription"></a>Bir abonelikte yeniden yüklenebilen herhangi bir hesaptan geri yükleme özelliği atama

`CosmosRestoreOperator`Yerleşik rolü abonelik düzeyinde ata

```azurecli-interactive
az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope /subscriptions/<subscriptionId>
```

### <a name="assign-capability-to-restore-from-a-specific-account"></a>Belirli bir hesaptan geri yükleme özelliği atama

* Belirli bir kaynak grubuna bir Kullanıcı yazma eylemi atayın. Bu eylem, kaynak grubunda yeni bir hesap oluşturmak için gereklidir.

* *Cosmosrestoreoperator* yerleşik rolünü, geri yüklenmesi gereken, geri yüklenebilen belirli bir veritabanı hesabına atayın. Aşağıdaki komutta, *Restokıbledatabaseaccount* KAPSAMı `ID` `az cosmosdb restorable-database-account` (CLI kullanılıyorsa) veya  `Get-AzCosmosDBRestorableDatabaseAccount` (PowerShell kullanılıyorsa) çıktısından özelliğinden alınır.

  ```azurecli-interactive
   az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope <RestorableDatabaseAccount>
  ```

### <a name="assign-capability-to-restore-from-any-source-account-in-a-resource-group"></a>Kaynak grubundaki herhangi bir kaynak hesaptan geri yükleme özelliği atayın.
Bu işlem şu anda desteklenmiyor.

## <a name="custom-role-creation-for-restore-action-with-cli"></a><a id="custom-restorable-action"></a>CLı ile geri yükleme eylemi için özel rol oluşturma

Abonelik sahibi diğer Azure AD kimliğine geri yükleme izni verebilir. Geri yükleme izni, eyleme dayalıdır: `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action` ve geri yükleme iznine dahil edilmelidir. Bu rolün dahil olduğu *Cosmosrestoreoperator* adlı yerleşik bir rol vardır. Bu yerleşik rolü kullanarak izni atayabilir ya da özel bir rol oluşturabilirsiniz.

Aşağıdaki Restokıbleaction özel bir rolü temsil eder. Bu rolü açıkça oluşturmanız gerekir. Aşağıdaki JSON şablonu geri yükleme izniyle birlikte bir özel rol *Restokıbleaction* oluşturur:

```json
{
  "assignableScopes": [
    "/subscriptions/23587e98-b6ac-4328-a753-03bcd3c8e744"
  ],
  "description": "Can do a restore request for any Cosmos DB database account with continuous backup",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/deployments/validate/action",
        "Microsoft.DocumentDB/databaseAccounts/write",
        "Microsoft.Resources/deployments/write",  
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "Name": "RestorableAction",
    "roleType": "CustomRole"
}
```

Ardından, ARM şablonunu kullanarak geri yükleme izniyle bir rol oluşturmak için aşağıdaki şablon dağıtım komutunu kullanın:

```azurecli-interactive
az role definition create --role-definition <JSON_Role_Definition_Path>
```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)veya [Azure Resource Manager](continuous-backup-restore-template.md)kullanarak sürekli yedeklemeyi yapılandırın ve yönetin.
* [Sürekli yedekleme modunun kaynak modeli](continuous-backup-restore-resource-model.md)
