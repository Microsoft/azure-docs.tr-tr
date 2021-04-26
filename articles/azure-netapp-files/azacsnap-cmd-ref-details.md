---
title: Azure NetApp Files için Azure uygulaması tutarlı anlık görüntü aracı 'nı kullanarak ayrıntıları edinin | Microsoft Docs
description: Azure NetApp Files ile kullanabileceğiniz Azure uygulaması tutarlı anlık görüntü aracının Ayrıntılar komutunu çalıştırmaya yönelik bir kılavuz sağlar.
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
ms.openlocfilehash: 1d6a5488ee761acec57e6a1030bcb17872b09258
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865307"
---
# <a name="obtain-details-using-azure-application-consistent-snapshot-tool-preview"></a>Azure uygulaması tutarlı anlık görüntü aracı 'nı (Önizleme) kullanarak ayrıntıları edinin

Bu makale, Azure NetApp Files ile kullanabileceğiniz Azure uygulaması tutarlı anlık görüntü aracının Ayrıntılar komutunu çalıştırmaya yönelik bir kılavuz sağlar.

## <a name="introduction"></a>Giriş

`azacsnap -c details`Komut, yapılandırma dosyasındaki tüm birimlerde depolanan anlık görüntülerin (birim adı, anlık görüntü adı, oluşturma saati, açıklamalar, anlık görüntü boyutu) ayrıntılarını listeler.  Konsol çıktısı, daha fazla analiz için bir elektronik tabloya yapıştırılabilir.

Arka uç sistemlerinde depolanan bilgiler nedeniyle, **Azure büyük örnek** üzerinde çalıştırıldığında ve **Azure NetApp Files** karşı komutun çıktısı biraz farklıdır.

## <a name="command-options"></a>Komut seçenekleri

`-c details`Komut aşağıdaki seçeneklere sahiptir:

- `--details snapshots` Yapılandırılmış her bir birimin anlık görüntülerle ilgili temel ayrıntıların bir listesini sağlar. Bu komut, birincil sunucuda veya olağanüstü durum kurtarma konumundaki bir sunucuda çalıştırılabilir. Bu komut, anlık görüntüler içeren her bir birim tarafından aşağıdaki bilgilerin dökümünü sağlar:
  - Bir birimdeki toplam anlık görüntülerin boyutu.
  - Söz konusu birimdeki her anlık görüntü aşağıdaki ayrıntıları içerir:
  - Anlık görüntü adı
  - Oluşturma zamanı
  - Anlık görüntünün boyutu
  - Anlık görüntünün sıklığı

- `--details replication` Üretim sitesinden olağanüstü durum kurtarma sitesine çoğaltma durumu etrafında temel ayrıntılar sağlar. Komut, çoğaltmanın gerçekleşmesini sağlamak için izlemeye izin vermek üzere çıkış sağlar.  Bu, çoğaltılan öğelerin boyutunu gösterir. Çoğaltma çok uzun sürüyorsa veya bağlantı kapalıysa kılavuz de sağlar.

- `[--configfile <config filename>]` , özel yapılandırma dosyası adlarına izin veren isteğe bağlı bir parametredir.

### <a name="output-of-the-azacsnap--c-details---details-snapshots-command"></a>`azacsnap -c details --details snapshots`Komutun çıkışı

Aşağıdaki örnek bir **Azure büyük örneği** üzerinde yürütüldü ve çıktı, kısaltma için kırpılmıştır.  Ayrıca, bazı satırlardan bazılarına sığacak şekilde kaydırılmış olabileceğini unutmayın.

```bash
azacsnap -c details --details snapshots
```

```output
List snapshot details called with snapshotFilter ''
#, Volume, Snapshot, Create Time, HANA Backup ID, Snapshot Size
#1, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, hana_hourly.2020-06-17T113043.1586971Z, "Wed Jun 17 11:31:14 2020", "HANA Backup ID: 1592393444174, 702.6MB
#2, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, shoasnap-hsr-ha.2020-07-02_2200.5, "Thu Jul 02 22:01:37 2020", "Backup ID: 1593727205201, 342.3MB
#3, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, azacsnap-hsr-ha.2020-07-02T220201.5332158Z, "Thu Jul 02 22:03:34 2020", "HANA Backup ID: 1593727322533|azacsnap version: 5.0 Preview (20200617.75879)", 3.27MB
#4, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, shoasnap-hsr-ha.2020-07-02_2205.4, "Thu Jul 02 22:06:36 2020", "Backup ID: 1593727504776, 3.14MB
#5, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, azacsnap-hsr-ha.2020-07-02T220702.3283669Z, "Thu Jul 02 22:08:37 2020", "HANA Backup ID: 1593727623339|azacsnap version: 5.0 Preview (20200617.75879)", 3.50MB
#6, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, shoasnap-hsr-ha.2020-07-02_2210.3, "Thu Jul 02 22:11:37 2020", "Backup ID: 1593727805216, 2.85MB
#7, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, azacsnap-hsr-ha.2020-07-02T221201.7013700Z, "Thu Jul 02 22:13:36 2020", "HANA Backup ID: 1593727922724|azacsnap version: 5.0 Preview (20200617.75879)", 3.34MB
#8, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, shoasnap-hsr-ha.2020-07-02_2215.2, "Thu Jul 02 22:16:36 2020", "Backup ID: 1593728104772, 2.73MB
#9, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, azacsnap-hsr-ha.2020-07-02T221702.2535255Z, "Thu Jul 02 22:18:35 2020", "HANA Backup ID: 1593728223274|azacsnap version: 5.0 Preview (20200617.75879)", 3.39MB
#10, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, shoasnap-hsr-ha.2020-07-02_2220.1, "Thu Jul 02 22:21:37 2020", "Backup ID: 1593728405346, 3.29MB
#11, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, azacsnap-hsr-ha.2020-07-02T222201.4988618Z, "Thu Jul 02 22:23:36 2020", "HANA Backup ID: 1593728522505|azacsnap version: 5.0 Preview (20200617.75879)", 3.68MB
#12, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, shoasnap-hsr-ha.2020-07-02_2225.0, "Thu Jul 02 22:26:37 2020", "Backup ID: 1593728705321, 2.80MB
#13, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, azacsnap-hsr-ha.2020-07-02T222702.0521995Z, "Thu Jul 02 22:28:37 2020", "HANA Backup ID: 1593728823058|azacsnap version: 5.0 Preview (20200617.75879)", 1.04MB
, hana_data_h31_azsollabbl20a31_mnt00001_t210_vol, , , Size used by Snapshots, 1.05GB
#1, hana_shared_h31_azsollabbl20a31_t210_vol, hana_hourly.2020-06-17T113043.1586971Z, "Wed Jun 17 11:31:12 2020", "HANA Backup ID: 1592393444174, 2.55GB
#2, hana_shared_h31_azsollabbl20a31_t210_vol, shoasnap-hsr-ha.2020-07-02_2200.5, "Thu Jul 02 22:01:37 2020", "Backup ID: 1593727205201, 4.30MB
#3, hana_shared_h31_azsollabbl20a31_t210_vol, azacsnap-hsr-ha.2020-07-02T220201.5332158Z, "Thu Jul 02 22:03:36 2020", "HANA Backup ID: 1593727322533|azacsnap version: 5.0 Preview (20200617.75879)", 8.04MB
#4, hana_shared_h31_azsollabbl20a31_t210_vol, shoasnap-hsr-ha.2020-07-02_2205.4, "Thu Jul 02 22:06:37 2020", "Backup ID: 1593727504776, 8.12MB
#5, hana_shared_h31_azsollabbl20a31_t210_vol, azacsnap-hsr-ha.2020-07-02T220702.3283669Z, "Thu Jul 02 22:08:35 2020", "HANA Backup ID: 1593727623339|azacsnap version: 5.0 Preview (20200617.75879)", 4.28MB
#6, hana_shared_h31_azsollabbl20a31_t210_vol, shoasnap-hsr-ha.2020-07-02_2210.3, "Thu Jul 02 22:11:37 2020", "Backup ID: 1593727805216, 4.33MB
#7, hana_shared_h31_azsollabbl20a31_t210_vol, azacsnap-hsr-ha.2020-07-02T221201.7013700Z, "Thu Jul 02 22:13:34 2020", "HANA Backup ID: 1593727922724|azacsnap version: 5.0 Preview (20200617.75879)", 4.31MB
#8, hana_shared_h31_azsollabbl20a31_t210_vol, shoasnap-hsr-ha.2020-07-02_2215.2, "Thu Jul 02 22:16:37 2020", "Backup ID: 1593728104772, 4.30MB
#9, hana_shared_h31_azsollabbl20a31_t210_vol, azacsnap-hsr-ha.2020-07-02T221702.2535255Z, "Thu Jul 02 22:18:37 2020", "HANA Backup ID: 1593728223274|azacsnap version: 5.0 Preview (20200617.75879)", 7.84MB
#10, hana_shared_h31_azsollabbl20a31_t210_vol, shoasnap-hsr-ha.2020-07-02_2220.1, "Thu Jul 02 22:21:37 2020", "Backup ID: 1593728405346, 8.09MB
#11, hana_shared_h31_azsollabbl20a31_t210_vol, azacsnap-hsr-ha.2020-07-02T222201.4988618Z, "Thu Jul 02 22:23:34 2020", "HANA Backup ID: 1593728522505|azacsnap version: 5.0 Preview (20200617.75879)", 4.34MB
#12, hana_shared_h31_azsollabbl20a31_t210_vol, shoasnap-hsr-ha.2020-07-02_2225.0, "Thu Jul 02 22:26:37 2020", "Backup ID: 1593728705321, 4.31MB
#13, hana_shared_h31_azsollabbl20a31_t210_vol, azacsnap-hsr-ha.2020-07-02T222702.0521995Z, "Thu Jul 02 22:28:35 2020", "HANA Backup ID: 1593728823058|azacsnap version: 5.0 Preview (20200617.75879)", 4.31MB
, hana_shared_h31_azsollabbl20a31_t210_vol, , , Size used by Snapshots, 2.62GB
#1, hana_log_backups_h31_azsollabbl20a31_t210_vol, azacsnap_vesangam_other_test.2020-06-17T113215.4462696Z, "Wed Jun 17 11:32:43 2020", "HANA Log-Backups, 156KB
#2, hana_log_backups_h31_azsollabbl20a31_t210_vol, azacsnap_vesangam_other_test2.2020-06-17T114205.1041364Z, "Wed Jun 17 11:42:35 2020", "HANA Log-Backups, 1.34MB
, hana_log_backups_h31_azsollabbl20a31_t210_vol, , , Size used by Snapshots, 1.49MB
```

> [!NOTE]
> Bu örnek, önceki sürüm (v 4.3) ve en son sürümle (5,0) alınan anlık görüntüler kullanılarak çalıştırılan anlık görüntülerin çıkışını gösterir.

Aşağıdaki örnekte **Azure NetApp Files** çıkışı vardır. bu komutla ilgili bilgiler, hizmet tarafından gösterilmediğinden bu komutla daha az bilgi olduğunu not edin.

```bash
azacsnap -c details --details snapshots
```

```output
List snapshot details called with snapshotFilter ''
#, Volume, SnapshotName
#1, HANADATA_P, snapmirror.dd59bda4-d507-11ea-99fc-00a098f31b77_2155201518.2021-03-22_020000
#2, HANADATA_P, quarter_hourly__2021-03-22T020002-3678211Z
#3, HANADATA_P, quarter_hourly__2021-03-22T014502-0716526Z
#4, HANADATA_P, quarter_hourly__2021-03-22T013002-4080651Z
#5, HANADATA_P, quarter_hourly__2021-03-22T011502-8376045Z
#6, HANADATA_P, quarter_hourly__2021-03-22T010002-8810203Z
#7, HANADATA_P, quarter_hourly__2021-03-22T004502-5983306Z
#8, HANADATA_P, quarter_hourly__2021-03-22T003002-1168834Z
#9, HANADATA_P, quarter_hourly__2021-03-22T001501-9781108Z
#10, HANADATA_P, quarter_hourly__2021-03-22T000002-0865483Z
#1, HANASHARED_P, quarter_hourly__2021-03-22T020002-3678211Z
#2, HANASHARED_P, quarter_hourly__2021-03-22T014502-0716526Z
#3, HANASHARED_P, quarter_hourly__2021-03-22T013002-4080651Z
#4, HANASHARED_P, quarter_hourly__2021-03-22T011502-8376045Z
#5, HANASHARED_P, quarter_hourly__2021-03-22T010002-8810203Z
#6, HANASHARED_P, quarter_hourly__2021-03-22T004502-5983306Z
#7, HANASHARED_P, quarter_hourly__2021-03-22T003002-1168834Z
#8, HANASHARED_P, quarter_hourly__2021-03-22T001501-9781108Z
#9, HANASHARED_P, quarter_hourly__2021-03-22T000002-0865483Z
#10, HANASHARED_P, quarter_hourly__2021-03-21T234502-3935816Z
#1, HANALOGBACKUP_P, logs_5mins__2021-03-22T021002-5462356Z
#2, HANALOGBACKUP_P, logs_5mins__2021-03-22T020502-2390356Z
#3, HANALOGBACKUP_P, logs_5mins__2021-03-22T015502-3928726Z
#4, HANALOGBACKUP_P, logs_5mins__2021-03-22T015001-9228768Z
#5, HANALOGBACKUP_P, logs_5mins__2021-03-22T014002-5554548Z
#6, HANALOGBACKUP_P, logs_5mins__2021-03-22T013502-1781392Z
#7, HANALOGBACKUP_P, logs_5mins__2021-03-22T012502-6686004Z
#8, HANALOGBACKUP_P, logs_5mins__2021-03-22T012002-7348198Z
#9, HANALOGBACKUP_P, logs_5mins__2021-03-22T011002-2234413Z
```

### <a name="output-of-the-azacsnap--c-details---details-replication-command"></a>`azacsnap -c details --details replication`Komutun çıkışı

Bu komut, birincil siteden DR konumuna depolama çoğaltma durumunu denetler ve **Dr site sunucusunda** yürütülmesi *gerekir* . Bu komut **yalnızca** yapılandırma dosyasındaki birimleri denetler.

> [!NOTE]
> Bu seçenek yalnızca **Azure büyük örnek** (hLi) sistemlerinde kullanılabilir.

```bash
azacsnap -c details --details replication
```

```output
Getting replication details for HLI systems
Volume, Link status, Current Replication Activity, Latest Snapshot Replicated, Size of Latest Snapshot Replicated, Current Lag Time between snapshots (HH:MM:SS)
hana_data_h80_mnt00001_t250_xdp, Active, Idle, snapmirror.d4b34832-8922-11e9-9b18-00a098f706fa_2151036590.2020-11-02_031000, 7.53MB, 00h 06m 02s
hana_shared_h80_t250_xdp, Active, Idle, snapmirror.d4b34832-8922-11e9-9b18-00a098f706fa_2151036594.2020-11-02_031000, 1.64MB, 00h 06m 04s
hana_log_backups_h80_t250_xdp, Active, Idle, snapmirror.d4b34832-8922-11e9-9b18-00a098f706fa_2151036635.2020-11-02_031500, 2.32KB, 00h 01m 04s
```

Aşağıdaki örnek, çoğaltma durumunu boztur ve bu senaryoda DR 'yi etkinleştirmek, DR sitesinde muhtemelen tamamlanmamış veriler oluşmasına neden olabilir.

> [!NOTE]
> Aşağıdaki örnekte, iki birimde de çoğaltma kesildi hakkında uyarı dikkat edin.

```bash
azacsnap -c details --details replication
```

```output
Getting replication details for HLI systems
Volume, Link status, Current Replication Activity, Latest Snapshot Replicated, Size of Latest Snapshot Replicated, Current Lag Time between snapshots (HH:MM:SS)
hana_data_h80_sapprdhdb80_mnt00001_t020_xdp, Broken-Off - Contact Microsoft Operations immediately!, Idle, snapmirror.d4b34832-8922-11e9-9b18-00a098f706fa_2151036523.2020-03-12_052000, 2.47MB, 99h 99m 99s  WARNING: replication has not occurred for more than 30 minutes!
hana_log_backups_h80_sapprdhdb80_t020_xdp, Broken-Off - Contact Microsoft Operations immediately!, Idle, snapmirror.d4b34832-8922-11e9-9b18-00a098f706fa_2151036539.2020-03-12_052400, 196.7KB, 99h 99m 99s  WARNING: replication has not occurred for more than 30 minutes!
hana_shared_h80_sapprdhdb80_t020_xdp, Active, Idle, snapmirror.d4b34832-8922-11e9-9b18-00a098f706fa_2151036524.2020-04-02_020000, 2.94MB, 00h 04m 55s
```

Bu örnek birincil siteyle DR sitesi arasında başarılı bir çoğaltmaya sahiptir, bu nedenle bu sistemler bir DR senaryosunu desteklemeye hazırlanmıştır.

```bash
azacsnap -c details --details replication
```

```output
Getting replication details for HLI systems
Volume, Link status, Current Replication Activity, Latest Snapshot Replicated, Size of Latest Snapshot Replicated, Current Lag Time between snapshots (HH:MM:SS)
hana_data_h80_sapprdhdb80_mnt00001_t020_xdp, Active, Idle, snapmirror.21215d07-2653-11e8-8e4c-00a098af659c_2157387233.2019-04-09_055000, 106.8MB, 00h 09m 45s
hana_log_backups_h80_sapprdhdb80_t020_xdp, Active, Idle, snapmirror.21215d07-2653-11e8-8e4c-00a098af659c_2157387278.2019-04-09_055700, 75.57MB, 00h 02m 45s
hana_shared_h80_sapprdhdb80_t020_xdp, Active, Idle, snapmirror.d4b34832-8922-11e9-9b18-00a098f706fa_2151036524.2020-04-02_020000, 2.94MB, 00h 04m 55s
```

## <a name="next-steps"></a>Sonraki adımlar

- [Bir yedekleme yapın](azacsnap-cmd-ref-backup.md)
- [Anlık görüntüleri Sil](azacsnap-cmd-ref-delete.md)
