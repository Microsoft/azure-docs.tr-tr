---
title: Bir depolama hesabı için son eşitleme zamanı özelliğini denetleyin
titleSuffix: Azure Storage
description: Coğrafi olarak çoğaltılan bir depolama hesabı için son eşitleme zamanı özelliğini nasıl denetleyeceğinizi öğrenin. Son eşitleme zamanı özelliği, birincil bölgeden yapılan tüm yazmamaların ikincil bölgeye başarıyla yazıldığı son zamanı gösterir.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 242700c05053aa9d07e3a561a21986c8451a46c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91612453"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>Bir depolama hesabı için son eşitleme zamanı özelliğini denetleyin

Bir depolama hesabı yapılandırdığınızda, verilerinizin birincil bölgeden yüzlerce mil olan ikincil bir bölgeye kopyalandığını belirtebilirsiniz. Coğrafi çoğaltma, doğal bir olağanüstü durum gibi birincil bölgede önemli bir kesinti olması durumunda verileriniz için dayanıklılık sağlar. İkincil bölgeye okuma erişimi de etkinleştirirseniz, birincil bölge kullanılamaz hale gelirse verileriniz okuma işlemleri için kullanılabilir kalır. Birincil bölge yanıt vermezse, uygulamanızı ikincil bölgeden okumak için sorunsuz bir şekilde çalışacak şekilde tasarlayabilirsiniz.

Coğrafi olarak yedekli depolama (GRS) ve coğrafi bölge yedekli depolama (GZRS), her ikisi de verilerinizi bir ikincil bölgeye zaman uyumsuz olarak çoğaltır. İkincil bölgeye okuma erişimi için Okuma Erişimli Coğrafi olarak yedekli depolamayı (RA-GRS) veya Okuma Erişimli Coğrafi bölge-yedekli depolamayı (RA-GZRS) etkinleştirin. Azure depolama tarafından sunulan yedekliliğe yönelik çeşitli seçenekler hakkında daha fazla bilgi için bkz. [Azure Storage yedekliği](storage-redundancy.md).

Bu makalede, birincil ve ikincil bölgeler arasında herhangi bir tutarsızlığı değerlendirebilmeniz için depolama hesabınızın **son eşitleme zamanı** özelliğinin nasıl denetleneceği açıklanır.

## <a name="about-the-last-sync-time-property"></a>Son eşitleme zamanı özelliği hakkında

Coğrafi çoğaltma zaman uyumsuz olduğundan, birincil bölgeye yazılan veriler, bir kesinti gerçekleştiği sırada ikinci bölgeye henüz yazılmadı. **Son eşitleme zamanı** özelliği, birincil bölgedeki verilerin ikincil bölgeye başarıyla yazıldığı anlamına gelir. Birincil bölgeye son eşitleme zamanından önce yapılan tüm yazma işlemleri ikincil konumdan okunmadan kullanılabilir. Son eşitleme zamanı özelliğinden sonra birincil bölgeye yapılan yazma işlemleri, henüz okuma için kullanılamayabilir veya kullanılamıyor olabilir.

**Son eşitleme zamanı** ÖZELLIĞI bir GMT Tarih/saat değeri.

## <a name="get-the-last-sync-time-property"></a>Son eşitleme zamanı özelliğini al

**Son eşitleme zamanı** özelliğinin değerini almak için PowerShell veya Azure CLI kullanabilirsiniz.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell ile depolama hesabının son eşitleme zamanını almak için, [az. Storage](https://www.powershellgallery.com/packages/Az.Storage) modülünün Version 1.11.0 veya üzeri sürümünü yüklemelisiniz. Ardından depolama hesabının **Georeplicationstats. LastSyncTime** özelliğini denetleyin. Yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı ile depolama hesabının son eşitleme zamanını almak için depolama hesabının **Georeplicationstats. lastSyncTime** özelliğini denetleyin. `--expand` **Georeplicationstats** altında iç içe yerleştirilmiş özelliklerin değerlerini döndürmek için parametresini kullanın. Yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli-interactive
$lastSyncTime=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --expand geoReplicationStats \
    --query geoReplicationStats.lastSyncTime \
    --output tsv)
```

---

## <a name="see-also"></a>Ayrıca bkz.

- [Azure Depolama yedekliliği](storage-redundancy.md)
- [Depolama hesabı için artıklık seçeneğini değiştirme](redundancy-migration.md)
- [Yüksek oranda kullanılabilir uygulamalar tasarlamak için coğrafi artıklığı kullanın](geo-redundant-design.md)
