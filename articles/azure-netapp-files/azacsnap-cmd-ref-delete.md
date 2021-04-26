---
title: Azure NetApp Files için Azure uygulaması tutarlı anlık görüntü aracı 'nı kullanarak silme | Microsoft Docs
description: Azure NetApp Files ile kullanabileceğiniz Azure uygulaması tutarlı anlık görüntü aracının Sil komutunu çalıştırmaya yönelik bir kılavuz sağlar.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 1f2c767d45bb08e25a057c7db1f380ceb250f607
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864916"
---
# <a name="delete-using-azure-application-consistent-snapshot-tool-preview"></a>Azure uygulaması tutarlı anlık görüntü aracı 'nı (Önizleme) kullanarak Sil

Bu makalede, Azure uygulaması tutarlı anlık görüntü aracının Azure NetApp Files ile kullanabileceğiniz Delete komutunu çalıştırmaya yönelik bir kılavuz sunulmaktadır.

## <a name="introduction"></a>Giriş

Komutuyla birim anlık görüntülerini ve veritabanı kataloğu girdilerini silmek mümkündür `azacsnap -c delete` .

> [!IMPORTANT]
> Bu komutun çalıştırılması için 10 dakikadan az bir süre önce oluşturulan anlık görüntüler, anlık görüntü çoğaltmasıyla girişim olasılığı nedeniyle silinmemelidir.

## <a name="command-options"></a>Komut seçenekleri

`-c delete`Komut aşağıdaki seçeneklere sahiptir:

- `--delete hana` seçeneklerle birlikte kullanıldığında `--dbsid <SID>` ve `--hanabackupid <HANA backup id>` ölçütlere uyan SAP HANA yedekleme kataloğundan girişleri silecek.

- `--delete storage` seçeneğiyle birlikte kullanıldığında, `--snapshot <snapshot name>` arka uç depolama sisteminden anlık görüntüyü siler.

- `--delete sync` seçeneklerle birlikte kullanıldığında `--dbsid <SID>` ve `--hanabackupid <HANA backup id>` için yedekleme kataloğundan depolama anlık görüntüsü adını alır `<HANA backup id>` ve ardından yedekleme kataloğundaki girişi _ve_ anlık görüntüyü, adlandırılmış anlık görüntüyü içeren birimlerden siler.

- `--delete sync`ile birlikte kullanıldığında `--snapshot <snapshot name>` , için yedekleme kataloğunda herhangi bir girişi kontrol eder `<snapshot name>` , SAP HANA yedekleme kimliğini alır ve hem yedekleme kataloğunda girişi hem de adlandırılmış anlık görüntüyü içeren birimlerden  herhangi bir anlık görüntüyü siler.

- `[--force]` seçim *Dikkatli kullanın*.  Bu işlem, onay istenmeden silmeye zorlayacaktır.

- `[--configfile <config filename>]` , özel yapılandırma dosyası adlarına izin veren isteğe bağlı bir parametredir.

### <a name="delete-a-snapshot-using-sync-option"></a>' Seçeneğini kullanarak anlık görüntüyü Sil `sync`

```bash
azacsnap -c delete --delete sync --dbsid H80 --hanabackupid 157979797979
```

> [!NOTE]
> SAP HANA yedekleme KIMLIĞI 157979797979 için yedekleme kataloğunda herhangi bir girişi denetler, depolama anlık görüntü adını alır ve hem yedekleme kataloğunda girişi hem de adlı anlık görüntüyü içeren tüm birimleri siler.

```bash
azacsnap -c delete --delete sync --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> Hana_hourly .2020-01 -22 _2358 adlı anlık görüntü için yedekleme kataloğundaki tüm girdileri denetler, SAP HANA yedekleme KIMLIĞINI alır ve hem yedekleme kataloğunda girişi hem de adlandırılmış anlık görüntüyü içeren birimlerden herhangi bir anlık görüntüyü siler.

### <a name="delete-a-snapshot-using-hana-option"></a>' Seçeneğini kullanarak anlık görüntüyü Sil `hana`

```bash
azacsnap -c delete --delete hana --dbsid H80 --hanabackupid 157979797979
```

> [!NOTE]
> SID H80 için yedekleme kataloğundan 157979797979 SAP HANA yedekleme KIMLIĞINI siler.

### <a name="delete-a-snapshot-using-storage-option"></a>' Seçeneğini kullanarak anlık görüntüyü Sil `storage`

```bash
azacsnap -c delete --delete storage --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> Anlık görüntüyü hana_hourly .2020-01 -22 _2358 adlı anlık görüntü içeren birimlerden siler.

**Seçeneğini kullanarak çıkış `--delete storage`**

Kullanıcıdan silme işlemini onaylamasını istenir.

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T221702.2535255Z
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z'.
Are you sure you want to permanently delete the snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z' from all storage volumes.? (y/n) [n]: y
Snapshot deletion completed
```

İsteğe bağlı parametresini aşağıdaki gibi kullanarak Kullanıcı onayını kullanmaktan kaçınmak mümkündür `--force` :

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T222201.4988618Z --force
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T222201.4988618Z'.
Snapshot deletion completed
```

## <a name="next-steps"></a>Sonraki adımlar

- [Anlık görüntü ayrıntılarını al](azacsnap-cmd-ref-details.md)
- [Bir yedekleme yapın](azacsnap-cmd-ref-backup.md)
