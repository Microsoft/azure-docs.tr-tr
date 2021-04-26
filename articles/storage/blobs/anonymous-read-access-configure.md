---
title: Kapsayıcılar ve BLOB 'lar için anonim genel okuma erişimini yapılandırma
titleSuffix: Azure Storage
description: Depolama hesabı için blob verilerine anonim erişim izni verme veya bu erişimi engelleme hakkında bilgi edinin. Kapsayıcıları ve Blobları anonim erişim için kullanılabilir hale getirmek için kapsayıcı genel erişim ayarını ayarlayın.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/03/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: 31812a7b2dddad474ab5cd422a15f6e5368dba5c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774638"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>Kapsayıcılar ve BLOB 'lar için anonim genel okuma erişimini yapılandırma

Azure depolama, kapsayıcılar ve BLOB 'lar için isteğe bağlı anonim genel okuma erişimini destekler. Varsayılan olarak verilerinize anonim erişime izin verilmez. Anonim erişimi açıkça etkinleştirmediğiniz takdirde, bir kapsayıcıya ve bloblarına yapılan tüm isteklere yetki verilmelidir. Anonim erişime izin vermek için bir kapsayıcının ortak erişim düzeyi ayarını yapılandırdığınızda, istemciler bu kapsayıcıdaki verileri, isteği yetkilendirmeden okuyabilir.

> [!WARNING]
> Bir kapsayıcı genel erişim için yapılandırıldığında, herhangi bir istemci o kapsayıcıdaki verileri okuyabilir. Genel erişim olası bir güvenlik riski sunar, bu nedenle senaryonuz bunu gerektirmiyorsa, Microsoft bu BT 'nin depolama hesabı için izin vermemenizi önerir. Daha fazla bilgi için bkz. [kapsayıcılar ve bloblara anonim genel okuma erişimini engelleme](anonymous-read-access-prevent.md).

Bu makalede bir kapsayıcı ve Blobları için anonim genel okuma erişiminin nasıl yapılandırılacağı açıklanır. Blob verilerine bir istemci uygulamasından anonim olarak erişme hakkında daha fazla bilgi için bkz. [.NET ile anonim kapsayıcıları ve Blobları anonim olarak erişim](anonymous-read-access-client.md).

## <a name="about-anonymous-public-read-access"></a>Anonim genel okuma erişimi hakkında

Verilerinize genel erişim her zaman varsayılan olarak yasaktır. Genel erişimi etkileyen iki ayrı ayar vardır:

1. **Depolama hesabı için genel erişime izin verin.** Varsayılan olarak, bir depolama hesabı bir kullanıcıya bir kapsayıcıya genel erişimi etkinleştirmek için uygun izinler sağlar. Blob verileri, kullanıcının kapsayıcının genel erişim ayarını açıkça yapılandırması için ek adım kullanmadığı takdirde genel erişim için kullanılamaz.
1. **Kapsayıcının ortak erişim ayarını yapılandırın.** Varsayılan olarak, kapsayıcının ortak erişim ayarı devre dışıdır, yani kapsayıcıya veya verilerine yönelik her istek için yetkilendirme gereklidir. Uygun izinlere sahip bir Kullanıcı, yalnızca depolama hesabı için anonim erişime izin verildiğinde anonim erişimi etkinleştirmek için bir kapsayıcının ortak erişim ayarını değiştirebilir.

Aşağıdaki tabloda her iki ayar da bir kapsayıcı için genel erişimi nasıl etkiler özetlenmektedir.

| Genel erişim ayarı | Bir kapsayıcı için genel erişim devre dışı bırakıldı (varsayılan ayar) | Bir kapsayıcı için genel erişim kapsayıcı olarak ayarlanmıştır | Ortak erişim bir kapsayıcının blob olarak ayarlandığı |
|--|--|--|--|
| Depolama hesabı için genel erişime izin verilmiyor | Depolama hesabındaki herhangi bir kapsayıcıya genel erişim yok. | Depolama hesabındaki herhangi bir kapsayıcıya genel erişim yok. Depolama hesabı ayarı kapsayıcı ayarını geçersiz kılar. | Depolama hesabındaki herhangi bir kapsayıcıya genel erişim yok. Depolama hesabı ayarı kapsayıcı ayarını geçersiz kılar. |
| Depolama hesabı (varsayılan ayar) için genel erişime izin verilir | Bu kapsayıcıya genel erişim yok (varsayılan yapılandırma). | Bu kapsayıcıya ve bloblarına genel erişime izin verilir. | Ortak erişime bu kapsayıcıdaki bloblara izin verilir, ancak kapsayıcının kendisini etkilemez. |

## <a name="allow-or-disallow-public-read-access-for-a-storage-account"></a>Depolama hesabı için genel okuma erişimine izin ver veya izin verme

Varsayılan olarak, bir depolama hesabı bir kullanıcıya bir kapsayıcıya genel erişimi etkinleştirmek için uygun izinlere sahip olacak şekilde yapılandırılır. Ortak erişime izin verildiğinde, uygun izinlere sahip bir Kullanıcı, bu kapsayıcıdaki verilere anonim genel erişimi etkinleştirmek için bir kapsayıcının ortak erişim ayarını değiştirebilir. Blob verileri, kullanıcının kapsayıcının genel erişim ayarını açıkça yapılandırması için ek bir adım kullanmadığı takdirde, genel erişim için hiçbir zaman kullanılamaz.

Bir kapsayıcıya genel erişimin her zaman varsayılan olarak kapalı olduğunu ve anonim isteklere izin verecek şekilde açıkça yapılandırılması gerektiğini aklınızda bulundurun. Depolama hesabındaki ayardan bağımsız olarak, uygun izinlere sahip bir Kullanıcı, kapsayıcıda genel erişimi etkinleştirmek için bu ek adımı alırsa, verileriniz hiçbir zaman ortak erişime açık olmayacaktır.

Depolama hesabı için genel erişime izin vermemek, bu hesaptaki tüm kapsayıcılara ve bloblara anonim erişimi engeller. Hesap için genel erişime izin verilse, bir kapsayıcı için genel erişim ayarını anonim erişime izin verecek şekilde yapılandırmak mümkün değildir. Microsoft, daha iyi güvenlik için, senaryolarınız kullanıcıların blob kaynaklarına anonim olarak erişmelerini gerektirmediği takdirde depolama hesaplarınız için genel erişime izin vermemeyi önerir.

> [!IMPORTANT]
> Bir depolama hesabı için genel erişime izin vermemek, bu depolama hesabındaki tüm kapsayıcılar için genel erişim ayarlarını geçersiz kılar. Depolama hesabı için genel erişime izin verilse, bu hesaba yönelik ileride yapılacak anonim istekler başarısız olur. Bu ayarı değiştirmeden önce, Depolama hesabınızdaki verilere anonim olarak erişebilecek istemci uygulamalarının etkilerini anladığınızdan emin olun. Daha fazla bilgi için bkz. [kapsayıcılar ve bloblara anonim genel okuma erişimini engelleme](anonymous-read-access-prevent.md).

Bir depolama hesabı için genel erişime izin vermek veya bu erişimi engellemek için, hesabın **Allowblobpublicaccess** özelliğini yapılandırın. Bu özellik, Azure Resource Manager dağıtım modeliyle oluşturulan tüm depolama hesapları için kullanılabilir. Daha fazla bilgi için bkz. [depolama hesabına genel bakış](../common/storage-account-overview.md).

**Allowblobpublicaccess** özelliği bir depolama hesabı için varsayılan olarak ayarlı değildir ve açıkça ayarlanana kadar bir değer döndürmez. Özellik değeri **null** ya da **doğru** olduğunda depolama hesabı ortak erişime izin verir.

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Azure portal bir depolama hesabı için genel erişime izin vermek veya bu erişimi engellemek için şu adımları izleyin:

1. Azure portalda depolama hesabınıza gidin.
1. **Ayarlar**' ın altındaki **yapılandırma** ayarını bulun.
1. **BLOB ortak erişimini** **etkin** veya **devre dışı** olarak ayarlayın.

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Hesap için blob genel erişimine izin verme veya bu erişimi engelleme ile ilgili ekran görüntüsü":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell ile bir depolama hesabı için genel erişime izin vermek veya bu erişimi engellemek için, [Azure PowerShell Version 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) veya üstünü yüklersiniz. Ardından, **Allowblobpublicaccess** özelliğini yeni veya mevcut bir depolama hesabı için yapılandırın.

Aşağıdaki örnek bir depolama hesabı oluşturur ve **Allowblobpublicaccess** özelliğini açıkça **true** olarak ayarlar. Ardından, **Allowblobpublicaccess** özelliğini **false** olarak ayarlamak için depolama hesabını güncelleştirir. Örnek, her durumda özellik değerini de alır. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with AllowBlobPublicAccess set to true (or null).
New-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -Location $location `
    -SkuName Standard_GRS
    -AllowBlobPublicAccess $false

# Read the AllowBlobPublicAccess property for the newly created storage account.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowBlobPublicAccess

# Set AllowBlobPublicAccess set to false
Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -AllowBlobPublicAccess $false

# Read the AllowBlobPublicAccess property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowBlobPublicAccess
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı ile bir depolama hesabı için genel erişime izin vermek veya bu erişimi engellemek için Azure CLı sürüm 2.9.0 veya üstünü yüklersiniz. Daha fazla bilgi için bkz. [Azure CLI 'Yı yüklerken](/cli/azure/install-azure-cli). Ardından, **Allowblobpublicaccess** özelliğini yeni veya mevcut bir depolama hesabı için yapılandırın.

Aşağıdaki örnek bir depolama hesabı oluşturur ve **Allowblobpublicaccess** özelliğini açıkça **true** olarak ayarlar. Ardından, **Allowblobpublicaccess** özelliğini **false** olarak ayarlamak için depolama hesabını güncelleştirir. Örnek, her durumda özellik değerini de alır. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --allow-blob-public-access true

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowBlobPublicAccess \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --allow-blob-public-access false

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowBlobPublicAccess \
    --output tsv
```

# <a name="template"></a>[Şablon](#tab/template)

Şablonla bir depolama hesabı için genel erişime izin vermek veya bu erişimi engellemek için, **Allowblobpublicaccess** özelliği **true** veya **false** olarak ayarlanmış bir şablon oluşturun. Aşağıdaki adımlar Azure portal bir şablonun nasıl oluşturulacağını açıklamaktadır.

1. Azure portal **kaynak oluştur**' u seçin.
1. **Market 'Te ara**' te, **şablon dağıtımı** yazın ve ardından **ENTER** tuşuna basın.
1. **Şablon dağıtımı seçin (özel şablonlar kullanarak dağıtın) (Önizleme)**, **Oluştur**' u seçin ve ardından **düzenleyicide kendi şablonunuzu oluşturun**' i seçin.
1. Şablon Düzenleyicisi 'nde, yeni bir hesap oluşturmak için aşağıdaki JSON öğesine yapıştırın ve **Allowblobpublicaccess** özelliğini **true** veya **false** olarak ayarlayın. Açılı ayraçlar içindeki yer tutucuları kendi değerlerinizle değiştirmeyi unutmayın.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'template')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "allowBlobPublicAccess": false
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. Şablonu kaydedin.
1. Kaynak grubu parametresini belirtin, ardından şablonu dağıtmak için **gözden geçir + oluştur** düğmesini seçin ve **Allowblobpublicaccess** özelliği yapılandırılmış bir depolama hesabı oluşturun.

---

> [!NOTE]
> Bir depolama hesabı için genel erişime izin vermemek, bu depolama hesabında barındırılan herhangi bir statik Web sitesini etkilemez. **$Web** kapsayıcı her zaman herkese açık olarak erişilebilir.
>
> Depolama hesabı için genel erişim ayarını güncelleştirdikten sonra, değişiklik tamamen yayılmadan önce 30 saniye kadar sürebilir.

Blob genel erişimine izin vermek veya bu erişimi vermemek, Azure depolama kaynak sağlayıcısı 'nın 2019-04-01 veya sonraki bir sürümünü gerektirir. Daha fazla bilgi için bkz. [Azure depolama kaynak sağlayıcısı REST API](/rest/api/storagerp/).

Bu bölümdeki örneklerde, genel erişimin Şu anda izin verildiğini veya izin verilmediğini tespit etmek üzere depolama hesabı için **Allowblobpublicaccess** özelliğinin nasıl okunacağı gösterilmektedir. Bir hesabın ortak erişim ayarının anonim erişimi engelleyecek şekilde yapılandırıldığını doğrulama hakkında daha fazla bilgi edinmek için bkz. [anonim genel erişimi](anonymous-read-access-prevent.md#remediate-anonymous-public-access)düzeltme.

## <a name="set-the-public-access-level-for-a-container"></a>Bir kapsayıcı için genel erişim düzeyini ayarlama

Anonim kullanıcılara bir kapsayıcıya ve bloblarına okuma erişimi sağlamak için, önce depolama hesabı için genel erişime izin verin, sonra kapsayıcının ortak erişim düzeyini ayarlayın. Depolama hesabı için genel erişim reddedilirse, bir kapsayıcı için genel erişimi yapılandırameyeceksiniz.

Bir depolama hesabı için genel erişime izin verildiğinde, bir kapsayıcıyı aşağıdaki izinlerle yapılandırabilirsiniz:

- **Genel okuma erişimi yok:** Kapsayıcıya ve bloblarına yalnızca yetkili bir istekle erişilebilir. Bu seçenek, tüm yeni kapsayıcılar için varsayılandır.
- **Yalnızca Bloblar Için genel okuma erişimi:** Kapsayıcı içindeki Bloblar anonim istek tarafından okunabilir, ancak kapsayıcı verileri anonim olarak kullanılamaz. Anonim istemciler kapsayıcı içindeki Blobları numaralandıramaz.
- **Kapsayıcı ve Blobları Için genel okuma erişimi:** Kapsayıcı ve blob verileri, kapsayıcı izin ayarları ve kapsayıcı meta verileri dışında anonim istek tarafından okunabilir. İstemciler kapsayıcı içindeki Blobları anonim istek ile numaralandırabilirler, ancak depolama hesabındaki kapsayıcıları numaralandıramaz.

Tek bir blob için genel erişim düzeyini değiştiremezsiniz. Genel erişim düzeyi yalnızca kapsayıcı düzeyinde ayarlanır. Kapsayıcıyı oluştururken kapsayıcının ortak erişim düzeyini ayarlayabilir veya var olan bir kapsayıcıdaki ayarı güncelleştirebilirsiniz.

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Azure portal bir veya daha fazla mevcut kapsayıcının genel erişim düzeyini güncelleştirmek için şu adımları izleyin:

1. Azure portal depolama hesabınıza genel bakış bölümüne gidin.
1. Menü dikey penceresinde **BLOB hizmeti** altında **kapsayıcılar**' ı seçin.
1. Ortak erişim düzeyini ayarlamak istediğiniz kapsayıcıları seçin.
1. Genel erişim ayarlarını göstermek için **erişim düzeyini Değiştir** düğmesini kullanın.
1. **Genel erişim düzeyi** açılan menüsünde istenen genel erişim düzeyini seçin ve değişikliği seçili kapsayıcılara uygulamak için Tamam düğmesine tıklayın.

    ![Portalda genel erişim düzeyinin nasıl ayarlanacağını gösteren ekran görüntüsü](./media/anonymous-read-access-configure/configure-public-access-container.png)

Depolama hesabı için genel erişime izin verilmedikçe, kapsayıcının ortak erişim düzeyi ayarlanamaz. Kapsayıcının ortak erişim düzeyini ayarlamaya çalışırsanız, hesap için genel erişime izin verilmediğinden ayarın devre dışı bırakıldığını görürsünüz.

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="Ortak erişime izin verilmediği zaman, Setting kapsayıcısının genel erişim düzeyinin engellendiğini gösteren ekran görüntüsü":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell ile bir veya daha fazla kapsayıcının genel erişim düzeyini güncelleştirmek için [set-AzStorageContainerAcl](/powershell/module/az.storage/set-azstoragecontaineracl) komutunu çağırın. Hesap anahtarınızı, bir bağlantı dizesini veya paylaşılan erişim imzasını (SAS) geçirerek bu işlemi yetkilendirin. Kapsayıcının ortak erişim düzeyini ayarlayan kapsayıcı [ACL 'si](/rest/api/storageservices/set-container-acl) IŞLEMI Azure AD ile yetkilendirmeyi desteklemez. Daha fazla bilgi için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

Aşağıdaki örnek, ortak erişime devre dışı bırakılmış bir kapsayıcı oluşturur ve sonra kapsayıcının ve bloblarına anonim erişime izin vermek için kapsayıcının ortak erişim ayarını güncelleştirir. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
# Set variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Get context object.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

# Create a new container with public access setting set to Off.
$containerName = "<container>"
New-AzStorageContainer -Name $containerName -Permission Off -Context $ctx

# Read the container's public access setting.
Get-AzStorageContainerAcl -Container $containerName -Context $ctx

# Update the container's public access setting to Container.
Set-AzStorageContainerAcl -Container $containerName -Permission Container -Context $ctx

# Read the container's public access setting.
Get-AzStorageContainerAcl -Container $containerName -Context $ctx
```

Depolama hesabı için genel erişime izin verilmedikçe, kapsayıcının ortak erişim düzeyi ayarlanamaz. Kapsayıcının ortak erişim düzeyini ayarlamaya çalışırsanız, Azure depolama, depolama hesabında ortak erişime izin verilmediğini belirten bir hata döndürür.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı ile bir veya daha fazla kapsayıcının genel erişim düzeyini güncelleştirmek için [az Storage Container set Permission](/cli/azure/storage/container#az_storage_container_set_permission) komutunu çağırın. Hesap anahtarınızı, bir bağlantı dizesini veya paylaşılan erişim imzasını (SAS) geçirerek bu işlemi yetkilendirin. Kapsayıcının ortak erişim düzeyini ayarlayan kapsayıcı [ACL 'si](/rest/api/storageservices/set-container-acl) IŞLEMI Azure AD ile yetkilendirmeyi desteklemez. Daha fazla bilgi için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

Aşağıdaki örnek, ortak erişime devre dışı bırakılmış bir kapsayıcı oluşturur ve sonra kapsayıcının ve bloblarına anonim erişime izin vermek için kapsayıcının ortak erişim ayarını güncelleştirir. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli-interactive
az storage container create \
    --name <container-name> \
    --account-name <account-name> \
    --resource-group <resource-group>
    --public-access off \
    --account-key <account-key> \
    --auth-mode key

az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key> \
    --auth-mode key

az storage container set-permission \
    --name <container-name> \
    --account-name <account-name> \
    --public-access container \
    --account-key <account-key> \
    --auth-mode key

az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key> \
    --auth-mode key
```

Depolama hesabı için genel erişime izin verilmedikçe, kapsayıcının ortak erişim düzeyi ayarlanamaz. Kapsayıcının ortak erişim düzeyini ayarlamaya çalışırsanız, Azure depolama, depolama hesabında ortak erişime izin verilmediğini belirten bir hata döndürür.

# <a name="template"></a>[Şablon](#tab/template)

Yok.

---

## <a name="check-the-public-access-setting-for-a-set-of-containers"></a>Bir kapsayıcı kümesi için genel erişim ayarını denetleyin

Bir veya daha fazla depolama hesabında, kapsayıcıları listeleyerek ve genel erişim ayarını denetleyerek, genel erişim için hangi kapsayıcıların yapılandırıldığını denetlemek mümkündür. Bu yaklaşım, bir depolama hesabı çok sayıda kapsayıcı içermediği ya da ayarı az sayıda depolama hesabı üzerinde denetlerken pratik bir seçenektir. Ancak, çok sayıda kapsayıcıyı numaralandırmaya çalışırsanız performans düşebilir.

Aşağıdaki örnek, bir depolama hesabındaki tüm kapsayıcılar için genel erişim ayarını almak üzere PowerShell kullanır. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="next-steps"></a>Sonraki adımlar

- [Kapsayıcılara ve bloblara anonim genel okuma erişimini engelleyin](anonymous-read-access-prevent.md)
- [.NET ile ortak kapsayıcılara ve bloblara anonim erişim](anonymous-read-access-client.md)
- [Azure depolama 'ya erişimi yetkilendirme](../common/storage-auth.md)
