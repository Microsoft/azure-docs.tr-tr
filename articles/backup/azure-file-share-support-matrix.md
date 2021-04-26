---
title: Azure dosya paylaşma yedeklemesi için destek matrisi
description: Azure dosya paylaşımları yedeklenirken destek ayarlarının ve sınırlamaların özetini sağlar.
ms.topic: conceptual
ms.date: 5/07/2020
ms.custom: references_regions
ms.openlocfilehash: 38c066c20399b39d676fb0c25aa158993258b979
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100370997"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Azure dosya paylaşma yedeklemesi için destek matrisi

Azure dosya paylaşımlarını yedeklemek için [Azure Backup hizmetini](./backup-overview.md) kullanabilirsiniz. Bu makale, Azure Backup ile Azure dosya paylaşımlarını yedeklerken destek ayarlarını özetler.

> [!NOTE]
> Azure Backup Şu anda NFS paylaşımlarını desteklemiyor.

## <a name="supported-regions"></a>Desteklenen bölgeler

### <a name="ga-regions-for-azure-file-shares-backup"></a>Azure dosya paylaşımları yedeklemesi için GA bölgeleri

Azure dosya paylaşımları yedeklemesi, **şunlar hariç** tüm bölgelerde kullanılabilir: Almanya Orta (Sovereign), Almanya Kuzeydoğu (Sovereign), Çin Doğu, Çin Doğu 2, Çin Kuzey, Çin Kuzey 2, US gov Iowa

## <a name="supported-storage-accounts"></a>Desteklenen depolama hesapları

| Depolama hesabı ayrıntıları | Destek                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Hesap türü            | Azure Backup, genel amaçlı v1, genel amaçlı v2 ve dosya depolama türü depolama hesaplarında bulunan Azure dosya paylaşımlarını destekler |
| Performans              | Azure Backup, hem standart hem de Premium Depolama hesaplarında dosya paylaşımlarını destekler |
| Çoğaltma              | Depolama hesaplarında bulunan Azure dosya paylaşımları, herhangi bir çoğaltma türüyle desteklenir |
| Güvenlik Duvarı etkin         | Microsoft Azure hizmetlerinin depolama hesabına erişmesine izin veren güvenlik duvarı kuralları olan depolama hesaplarında Azure dosya paylaşımları desteklenir|

## <a name="supported-file-shares"></a>Desteklenen dosya paylaşımları

| Dosya paylaşma türü                                   | Destek   |
| -------------------------------------------------- | --------- |
| Standart                                           | Desteklenir |
| Büyük                                              | Desteklenir |
| Premium                                            | Desteklenir |
| Azure dosya eşitleme hizmeti ile bağlantılı dosya paylaşımları | Desteklenir |

## <a name="protection-limits"></a>Koruma sınırları

| Ayar                                                      | Sınır |
| ------------------------------------------------------------ | ----- |
| Her gün kasa başına korunabilecek en fazla dosya paylaşımı sayısı| 200   |
| Her gün kasa başına kaydedileen fazla depolama hesabı sayısı | 50    |
| Kasa başına korunabilecek en fazla dosya paylaşımı sayısı | 2000   |
| Kasa başına kaydedileen fazla depolama hesabı sayısı | 200   |

## <a name="backup-limits"></a>Yedekleme limitleri

| Ayar                                      | Sınır |
| -------------------------------------------- | ----- |
| Gün başına en fazla isteğe bağlı yedekleme sayısı | 10   |
| Gün başına en fazla zamanlanmış yedekleme sayısı | 1     |

## <a name="restore-limits"></a>Geri yükleme sınırları

| Ayar                                                      | Sınır   |
| ------------------------------------------------------------ | ------- |
| Gün başına en fazla geri yükleme sayısı                           | 10      |
| Geri yükleme başına en fazla dosya sayısı                         | 99      |
| Büyük dosya paylaşımları için geri yükleme başına önerilen en yüksek geri yükleme boyutu | 15 TiB |

## <a name="retention-limits"></a>Bekletme sınırları

| Ayar                                                      | Sınır    |
| ------------------------------------------------------------ | -------- |
| Her zaman bir noktada dosya paylaşımında en fazla toplam kurtarma noktası | 200      |
| İsteğe bağlı yedekleme tarafından oluşturulan kurtarma noktası için en fazla bekletme | 10 yıl |
| Dosya paylaşma başına günlük kurtarma noktası (anlık görüntü) en fazla bekletme| 200 gün |
| Dosya paylaşma başına haftalık kurtarma noktası (anlık görüntü) en fazla bekletme | 200 hafta |
| Dosya paylaşma başına aylık kurtarma noktaları (anlık görüntüler) en fazla bekletme | 120 ay |
| Dosya paylaşma başına yıllık en fazla kurtarma noktası (anlık görüntü) saklama | 10 yıl |

## <a name="supported-restore-methods"></a>Desteklenen geri yükleme yöntemleri

| Restore yöntemi     | Ayrıntılar                                                      |
| ------------------ | ------------------------------------------------------------ |
| Tam paylaşma geri yükleme | Dosya paylaşımının tamamını orijinal veya alternatif bir konuma geri yükleyebilirsiniz |
| Öğe düzeyinde geri yükleme | Tek tek dosyaları ve klasörleri özgün veya alternatif bir konuma geri yükleyebilirsiniz |

## <a name="next-steps"></a>Sonraki adımlar

* [Azure dosya paylaşımlarını nasıl yedekleyeceğinizi](backup-afs.md) öğrenin
* [Azure dosya paylaşımlarını nasıl geri](restore-afs.md) yükleyeceğinizi öğrenin
* [Azure dosya paylaşımının yedeklerini yönetme](manage-afs-backup.md) hakkında bilgi edinin
