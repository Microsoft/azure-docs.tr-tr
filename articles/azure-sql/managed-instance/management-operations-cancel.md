---
title: Yönetim işlemlerini iptal et
titleSuffix: Azure SQL Managed Instance
description: Azure SQL yönetilen örnek yönetimi işlemlerini iptal etmeyi öğrenin.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, bonova, MashaMSFT
ms.date: 09/03/2020
ms.openlocfilehash: 342491178d55dacbdc68e6c9042623d381dff898
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861553"
---
# <a name="canceling-azure-sql-managed-instance-management-operations"></a>Azure SQL yönetilen örnek yönetimi işlemlerini iptal etme
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL yönetilen örneği, yeni bir yönetilen örnek dağıtırken veya örnek özelliklerini güncelleştirdiğinizde olduğu gibi bazı [yönetim işlemlerini](management-operations-overview.md)iptal etme yeteneği sağlar. 

## <a name="overview"></a>Genel Bakış

 Tüm yönetim işlemleri aşağıdaki gibi kategorilere ayrılabilir:

- Örnek dağıtımı (yeni örnek oluşturma).
- Örnek Güncelleştirmesi (sanal çekirdekler veya ayrılmış depolama gibi örnek özellikleri değiştirme).
- Örnek silme.

[Yönetim işlemlerinin ilerlemesini ve durumunu izleyebilir](management-operations-monitor.md) ve gerekirse bunlardan bazılarını iptal edebilirsiniz. 

Aşağıdaki tabloda yönetim işlemleri özetlenir, bunlar iptal edilip edilmeyeceğini ve bu sürenin genel toplam süresini özetler:

Kategori  |İşlem  |İptal edilebilir  |Tahmini iptal süresi  |
|---------|---------|---------|---------|
|Dağıtım |Örnek oluşturma |Yes |İşlem %90, 5 dakika içinde tamamlanır. |
|Güncelleştir |Örnek depolama ölçeği artırma/azaltma (Genel Amaçlı) |No |  |
|Güncelleştir |Örnek depolama ölçeği artırma/azaltma (İş Açısından Kritik) |Yes |İşlem %90, 5 dakika içinde tamamlanır. |
|Güncelleştir |Örnek işlem (Vçekirdekler) ölçeği artırma ve azaltma (Genel Amaçlı) |Yes |İşlem %90, 5 dakika içinde tamamlanır. |
|Güncelleştir |Örnek işlem (Vçekirdekler) ölçeği artırma ve azaltma (İş Açısından Kritik) |Yes |İşlem %90, 5 dakika içinde tamamlanır. |
|Güncelleştir |Örnek hizmeti katmanı değişikliği (İş Açısından Kritik Genel Amaçlı ve tam tersi) |Yes |İşlem %90, 5 dakika içinde tamamlanır. |
|Sil |Örnek silme |No |  |
|Sil |Sanal küme silme (Kullanıcı tarafından başlatılan işlem olarak) |No |  |

## <a name="cancel-management-operation"></a>Yönetim işlemini iptal et

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak yönetim işlemlerini iptal etmek için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com) git
1. SQL yönetilen örneğinizin **genel bakış** dikey penceresine gidin. 
1. Devam **eden işlem sayfasını açmak** için devam eden Işlemin yanındaki **bildirim** kutusunu seçin. 

   :::image type="content" source="media/management-operations-cancel/open-ongoing-operation.png" alt-text="Devam eden işlem sayfasını açmak için devam eden işlem kutusunu seçin.":::

1. Sayfanın alt kısmındaki **Işlemi Iptal et** ' i seçin. 

   :::image type="content" source="media/management-operations-cancel/cancel-operation.png" alt-text="İşlemi iptal etmek için iptal 'i seçin.":::

1. İşlemi iptal etmek istediğinizi onaylayın. 


İptal isteği başarılı olursa, yönetim işlemi iptal edilir ve hata oluşur. İptal işleminin başarılı veya başarısız olduğunu belirten bir bildirim alırsınız.

![İşlem sonucu iptal ediliyor](./media/management-operations-cancel/canceling-operation-result.png)


İptal isteği başarısız olursa veya iptal düğmesi etkin değilse, yönetim işleminin iptal edilemez durumuna girdiği ve kısa süre içinde tamamlayacağı anlamına gelir.  Yönetim işlemi tamamlanana kadar yürütmeye devam eder.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Zaten yüklü Azure PowerShell yoksa, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps).

Bir yönetim işlemini iptal etmek için, yönetim işlemi adı belirtmeniz gerekir. Bu nedenle, önce işlem listesini almak için Get komutunu kullanın ve ardından işlemi özel olarak iptal edin.

```powershell-interactive
$managedInstance = "<Your-instance-name>"
$resourceGroup = "<Your-resource-group-name>"

$managementOperations = Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup

foreach ($mo in $managementOperations ) {
    if($mo.State -eq "InProgress" -and $mo.IsCancellable){
        $cancelRequest = Stop-AzSqlInstanceOperation -ResourceGroupName $resourceGroup -ManagedInstanceName $managedInstance -Name $mo.Name
        Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup -Name $mo.Name
    }
}
```

Ayrıntılı komutların açıklaması için bkz. [Get-Azsqlınstanceoperation](/powershell/module/az.sql/get-azsqlinstanceoperation) ve [stop-azsqlınstanceoperation](/powershell/module/az.sql/stop-azsqlinstanceoperation).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı yüklü değilse, bkz. [Azure CLI 'Yı yükleme](/cli/azure/install-azure-cli).

Yönetim işlemini iptal etmek için, yönetim işlemi adını belirtmeniz gerekir. Bu nedenle, önce işlem listesini almak için Get komutunu kullanın ve ardından belirli işlemi iptal edin.

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName |
   --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do

az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

Ayrıntılı komutların açıklaması için bkz. [az SQL mı op](/cli/azure/sql/mi/op).

---

## <a name="canceled-deployment-request"></a>Dağıtım isteği iptal edildi

API sürüm 2020-02-02 ile örnek oluşturma isteği kabul edildiğinde örnek, dağıtım işleminin ilerlemesi (yönetilen örnek durumu **sağlama**) ne olduğuna bakılmaksızın, kaynak olarak mevcut olmaya başlar. Örnek dağıtım isteğini iptal ederseniz (yeni örnek oluşturma), yönetilen örnek **sağlama** durumundan **failedtocreate** durumuna geçer.

Oluşturmakta olan örnekler yine de kaynak olarak mevcuttur: 

- Ücretlendirilmiyor
- Kaynak limitlerini (alt ağ veya vCore kotası) doğru sayma


> [!NOTE]
> Kaynak veya yönetilen örnek listesinde paraziti en aza indirmek için, dağıtmayan örnekleri veya iptal edilen dağıtımlarla örnekleri silin. 


## <a name="next-steps"></a>Sonraki adımlar

- İlk yönetilen örneğinizi oluşturmayı öğrenmek için bkz. [Hızlı Başlangıç Kılavuzu](instance-create-quickstart.md).
- Özellikler ve karşılaştırma listesi için bkz. [ortak SQL özellikleri](../database/features-comparison.md).
- VNet yapılandırması hakkında daha fazla bilgi için bkz. [SQL yönetilen örnek VNET yapılandırması](connectivity-architecture-overview.md).
- Yönetilen bir örnek oluşturan ve bir veritabanını bir yedekleme dosyasından geri yükleyen hızlı başlangıç için bkz. [yönetilen örnek oluşturma](instance-create-quickstart.md).
- Geçiş için Azure veritabanı geçiş hizmeti 'ni kullanma hakkında bir öğretici için bkz. [veritabanı geçiş hizmeti kullanılarak SQL yönetilen örnek geçişi](../../dms/tutorial-sql-server-to-managed-instance.md).
