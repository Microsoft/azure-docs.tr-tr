---
title: Yönetim işlemlerini izleme
titleSuffix: Azure SQL Managed Instance
description: Azure SQL yönetilen örnek yönetimi işlemlerini izlemeye yönelik farklı yollar hakkında bilgi edinin.
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
ms.openlocfilehash: 6cdd3137798e221974dadda78dd55b6ae944bc78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589352"
---
# <a name="monitoring-azure-sql-managed-instance-management-operations"></a>Azure SQL yönetilen örnek yönetimi işlemlerini izleme
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL yönetilen örneği, yeni yönetilen örnekler dağıtmak, örnek özelliklerini güncelleştirmek veya artık gerekli olmadığında örnekleri silmek için kullandığınız [Yönetim işlemlerinin](management-operations-overview.md) izlenmesini sağlar. 

## <a name="overview"></a>Genel Bakış

Tüm yönetim işlemleri aşağıdaki gibi kategorilere ayrılabilir:

- Örnek dağıtımı (yeni örnek oluşturma).
- Örnek Güncelleştirmesi (sanal çekirdekler veya ayrılmış depolama gibi örnek özellikleri değiştirme).
- Örnek silme.

Çoğu yönetim işlemi [uzun süren işlemlerdir](management-operations-overview.md#duration). Bu nedenle, durumu izlemek veya işlem adımlarının ilerlemesini izlemek için bir gereksinim vardır. 

Yönetilen örnek yönetimi işlemlerini izlemenin birkaç yolu vardır:

- [Kaynak grubu dağıtımları](../../azure-resource-manager/templates/deployment-history.md)
- [Etkinlik günlüğü](../../azure-monitor/essentials/activity-log.md)
- [Yönetilen örnek işlemleri API 'SI](#managed-instance-operations-api)


Aşağıdaki tabloda yönetim işlemi izleme seçenekleri karşılaştırılmaktadır: 

| Seçenek | Bekletme | İptali destekler | Oluştur | Güncelleştir | Sil | İptal | Adımlar |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Kaynak grubu dağıtımları | Sonsuz<sup>1</sup> | <sup>2</sup> yok | Görünür | Görünür | Görünür değil | Görünür | Görünür değil |
| Etkinlik günlüğü | 90 gün | No | Görünür | Görünür | Görünür | Görünür |  Görünür değil |
| Yönetilen örnek işlemleri API 'SI | 24 saat | [Evet](management-operations-cancel.md) | Görünür | Görünür | Görünür | Görünür | Görünür |
|  |  |  |  |  |  |  | |

<sup>1</sup> bir kaynak grubunun dağıtım geçmişi 800 dağıtım ile sınırlıdır.

<sup>2</sup> kaynak grubu dağıtımları iptal işlemini destekler. Ancak, iptal mantığı nedeniyle, yalnızca iptal eylemi gerçekleştirildikten sonra dağıtım için zamanlanan bir işlem iptal edilir. Kaynak grubu dağıtımı iptal edildiğinde devam eden dağıtım iptal edilmez. Yönetilen örnek dağıtımı, uzun süreli çalışan bir adımdan (Azure Kaynak Yöneticisi perspektifinden) oluşuyorsa, kaynak grubu dağıtımının iptal edilmesi yönetilen örnek dağıtımını iptal etmez ve işlem tamamlanır. 

## <a name="managed-instance-operations-api"></a>Yönetilen örnek işlemleri API 'SI

Yönetim işlemleri API 'Leri, işlemleri izlemek için özel olarak tasarlanmıştır. Yönetilen örnek işlemlerini izlemek, işlem parametreleri ve işlem adımları hakkında öngörüler sağlayabilir ve [belirli işlemleri iptal](management-operations-cancel.md)edebilir. İşlem ayrıntılarının ve Cancel komutunun yanı sıra, bu API çok kaynak dağıtımlarıyla Otomasyon betiklerinizde kullanılabilir; ilerleme adımına göre, bazı bağımlı kaynak dağıtımını başlatabilirsiniz.

Bunlar API 'lardır: 

| Komut | Açıklama |
| --- | --- |
|[Yönetilen örnek Işlemleri-Get](/rest/api/sql/managedinstanceoperations/get)|Yönetilen bir örnek üzerinde bir yönetim işlemi alır.|
|[Yönetilen örnek Işlemleri-Iptal](/rest/api/sql/managedinstanceoperations/cancel)|Yönetilen örnekteki zaman uyumsuz işlemi iptal eder.|
|[Yönetilen örnek Işlemleri-yönetilen örneğe göre listeleme](/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|Yönetilen örnek üzerinde gerçekleştirilen işlemlerin listesini alır.|

> [!NOTE]
> İşlem listesinde yönetilen örnek oluşturma işlemini görmek için API sürüm 2020-02-02 ' i kullanın. Bu, Azure portal ve en son PowerShell ve Azure CLı paketlerinde kullanılan varsayılan sürümdür.

## <a name="monitor-operations"></a>İşlemleri izleme

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal yönetilen örnek **genel bakış** sayfasını kullanarak yönetilen örnek işlemlerini izleyin. 

Örneğin, oluşturma **işlemi** , **genel bakış** sayfasında oluşturma işleminin başlangıcında görünür: 

![Yönetilen örnek oluşturma ilerlemesi](./media/management-operations-monitor/monitoring-create-operation.png)

**Devam eden işlem sayfasını açmak** ve **oluşturma** veya **güncelleştirme** işlemlerini görüntülemek için **devam eden işlem** ' ı seçin. Ayrıca, bu sayfadaki işlemleri de [iptal](management-operations-cancel.md) edebilirsiniz.  

![Yönetilen örnek işlemi ayrıntıları](./media/management-operations-monitor/monitoring-operation-details.png)

> [!NOTE]
> Azure portal, PowerShell, Azure CLı veya diğer araçlar aracılığıyla gönderilen oluşturma işlemleri, REST API sürüm 2020-02-02 ' i kullanarak [iptal](management-operations-cancel.md)edilebilir. Bir oluşturma işlemi göndermek için 2020-02-02 'den eski REST API sürümler örnek dağıtımını başlatacak, ancak dağıtım Işlemler API 'sinde listelenmez ve iptal edilemez.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Get-AzSqlInstanceOperation cmdlet 'i yönetilen bir örnekteki işlemler hakkında bilgi alır. Yönetilen bir örnek üzerinde tüm işlemleri görüntüleyebilir veya işlem adı sağlayarak belirli bir işlemi görüntüleyebilirsiniz.

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation `
    -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup
```

Ayrıntılı komutların açıklaması için bkz. [Get-Azsqlınstanceoperation](/powershell/module/az.sql/get-azsqlinstanceoperation).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az SQL mı op listesi, yönetilen örnek üzerinde gerçekleştirilen işlemlerin bir listesini alır. Azure CLı yüklü değilse, bkz. [Azure CLI 'Yı yükleme](/cli/azure/install-azure-cli).

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName 
```

Ayrıntılı komutların açıklaması için bkz. [az SQL mı op](/cli/azure/sql/mi/op).

---

## <a name="next-steps"></a>Sonraki adımlar

- İlk yönetilen örneğinizi oluşturmayı öğrenmek için bkz. [Hızlı Başlangıç Kılavuzu](instance-create-quickstart.md).
- Özellikler ve karşılaştırma listesi için bkz. [ortak SQL özellikleri](../database/features-comparison.md).
- VNet yapılandırması hakkında daha fazla bilgi için bkz. [SQL yönetilen örnek VNET yapılandırması](connectivity-architecture-overview.md).
- Yönetilen bir örnek oluşturan ve bir veritabanını bir yedekleme dosyasından geri yükleyen hızlı başlangıç için bkz. [yönetilen örnek oluşturma](instance-create-quickstart.md).
- Geçiş için Azure veritabanı geçiş hizmeti 'ni kullanma hakkında bir öğretici için bkz. [veritabanı geçiş hizmeti kullanılarak SQL yönetilen örnek geçişi](../../dms/tutorial-sql-server-to-managed-instance.md).