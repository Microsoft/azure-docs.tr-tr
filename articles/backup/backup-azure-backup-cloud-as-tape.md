---
title: Bant altyapınızı değiştirme
description: Azure Backup Azure 'da verileri yedeklemeve geri yüklemenize olanak tanıyan bant benzeri semantiğini nasıl sağladığını öğrenin
ms.topic: conceptual
ms.date: 04/30/2017
ms.openlocfilehash: 4659a4d6fcc7213f8323e23d59411680276fcb28
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173305"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Uzun vadeli depolama alanınızı teypten Azure bulutuna taşıma

Azure Backup ve System Center Data Protection Manager müşteriler şunları yapabilir:

* Kurumsal ihtiyaçlarına en uygun olan zamanlamaya göre verileri yedekleyin.
* Yedekleme verilerini uzun süreler boyunca sakla
* Azure 'ı uzun süreli bekletme gereksinimlerinin bir parçası yapın (bant yerine).

Bu makalede, müşterilerin yedekleme ve bekletme ilkelerini nasıl etkinleştirebileceği açıklanmaktadır. Uzun süreli bekletme ihtiyaçlarını karşılamak için bantları kullanan müşterilerin artık bu özelliğin kullanılabilirliğine yönelik güçlü ve uygun bir alternatifi vardır. Özellik, Azure Backup en son sürümünde etkinleştirilir ( [burada](https://aka.ms/azurebackup_agent)kullanılabilir). System Center DPM müşterilerinin, Azure Backup hizmetiyle DPM 'yi kullanmadan önce, en azından DPM 2012 R2 UR5 ' ye güncelleştirilmesi gerekir.

## <a name="what-is-the-backup-schedule"></a>Yedekleme zamanlaması nedir?

Yedekleme zamanlaması, yedekleme işleminin sıklığını gösterir. Örneğin, aşağıdaki ekran ayarları, yedeklemelerin her gün 18:00 ve gece yarısı alınacağını belirtir.

![Günlük zamanlama](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Müşteriler ayrıca haftalık bir yedekleme zamanlayabilir. Örneğin, aşağıdaki ekrandaki ayarlar, yedeklemelerin her alternatif Pazar &, 9:30 ' da Çarşamba ve 1:00:00'DA alındığını gösterir.

![Haftalık zamanlama](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>Bekletme Ilkesi nedir?

Bekletme ilkesi, yedeklemenin depolanması gereken süreyi belirtir. Tüm yedekleme noktaları için yalnızca bir "düz ilke" belirtmek yerine, müşteriler yedeklemenin ne zaman yapılacağını temel alan farklı bekletme ilkeleri belirtebilir. Örneğin, her gün gerçekleştirilen yedekleme noktası, işlemsel kurtarma noktası olarak işlev gören 90 gün boyunca korunur. Denetim amaçları için her bir çeyreğin sonunda gerçekleştirilen yedekleme noktası, daha uzun bir süre boyunca korunur.

![Bekletme İlkesi](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

Bu ilkede belirtilen toplam "bekletme noktası" sayısı 90 (günlük puntolar) + 40 (10 yıl boyunca her üç ayda bir) = 130.

## <a name="example--putting-both-together"></a>Örnek: her ikisini birlikte yerleştirme

![Örnek ekran](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Günlük bekletme ilkesi**: günlük alınan yedeklemeler yedi gün boyunca depolanır.
2. **Haftalık bekletme ilkesi**: gece yarısı ve 18:00 Cumartesi günleri her gün alınan yedeklemeler dört hafta boyunca korunur
3. **Aylık bekletme ilkesi**: her ayın son Cumartesi günü gece yarısı ve 18:00 'de alınan yedeklemeler 12 ay boyunca korunur
4. **Yıllık bekletme ilkesi**: her Mart 'ın son Cumartesi günü gece yarısından alınan yedeklemeler 10 yıl boyunca korunur

Önceki diyagramda bulunan toplam "bekletme noktası" (bir müşterinin verileri geri yükleme) sayısı aşağıdaki şekilde hesaplanır:

* yedi gün için gün başına iki punto = 14 kurtarma noktası
* dört hafta için hafta başına iki punto = 8 kurtarma noktası
* 12 ay boyunca ayda iki punto = 24 kurtarma noktası
* 10 yıl başına yıl başına bir nokta = 10 kurtarma noktası

Toplam kurtarma noktası sayısı 56 ' dir.

> [!NOTE]
> Azure Backup kullanarak, korumalı örnek başına en fazla 9999 kurtarma noktası oluşturabilirsiniz. Korumalı örnek, Azure 'a yedekleyen bir bilgisayar, sunucu (fiziksel veya sanal) veya iş yüküdür.
>

## <a name="advanced-configuration"></a>Gelişmiş yapılandırma

Önceki ekranda **Değiştir** 'e tıkladığınızda müşteriler bekletme zamanlamalarını belirtirken daha fazla esneklik sağlar.

![Değiştir](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Sonraki adımlar

Azure Backup hakkında daha fazla bilgi için bkz.:

* [Azure Backup'a giriş](backup-introduction-to-azure-backup.md)
* [Azure Backup deneyin](backup-try-azure-backup-in-10-mins.md)
