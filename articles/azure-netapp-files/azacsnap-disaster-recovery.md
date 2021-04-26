---
title: Azure NetApp Files için Azure uygulaması tutarlı anlık görüntü Aracı kullanılarak olağanüstü durum kurtarma | Microsoft Docs
description: Azure NetApp Files ile kullanabileceğiniz Azure uygulaması tutarlı anlık görüntü Aracı kullanılırken olağanüstü durum kurtarmanın nasıl gerçekleştirileceğini açıklar.
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
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 554fa394179e7cfc5b86a2b50eb754547d137a44
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870406"
---
# <a name="disaster-recovery-using-azure-application-consistent-snapshot-tool-preview"></a>Azure uygulaması tutarlı anlık görüntü aracı (Önizleme) kullanılarak olağanüstü durum kurtarma

Bu makalede, Azure NetApp Files ile kullanabileceğiniz Azure uygulaması tutarlı anlık görüntü Aracı kullanılırken olağanüstü durum kurtarmanın nasıl gerçekleştirileceği açıklanır.

> [!IMPORTANT]
> Bu işlem yalnızca **Azure büyük örnek** için geçerlidir.

## <a name="introduction"></a>Giriş

Azure büyük örnek platformu, depolama birimi anlık görüntülerinin çoğaltılabileceği, yapılandırılmış bir olağanüstü durum kurtarma sitesine de sahip olabilir.  Anlık görüntüler bu tür bir kurulumla doğru şekilde yapılandırıldıysa bu sitede bir olağanüstü durum kurtarma işlemi gerçekleştirilebilir.  Bu belgenin, bu kurulum için olağanüstü durum kurtarma gerçekleştirmeye yönelik bir kılavuz olması amaçlanmıştır.

## <a name="prerequisites-for-disaster-recovery-setup"></a>Olağanüstü durum kurtarma kurulumu önkoşulları

Olağanüstü durum kurtarma yük devretmesini planlayabilmeniz için aşağıdaki önkoşulların karşılanması gerekir.

- DR sitesinde sağlanmış bir DR düğümünüz var. DR için iki seçenek vardır. Biri normal DR ve diğeri çok amaçlı DR 'dir.
- Depolama çoğaltması çalışıyor. Microsoft operasyon ekibi, otomatik olarak DR sağlama sırasında depolama çoğaltma kurulumunu gerçekleştirir. DR sitesindeki komutunu kullanarak depolama çoğaltmasını izleyebilirsiniz `azacsnap -c details --details replication` .
- Birincil konumda depolama anlık görüntülerini ayarlamış ve yapılandırmış olmanız gerekir.
- Birincil örnekle aynı SID 'ye sahip olan DR sitesinde birincil için bir HANA örneği yüklü.
- [Azure 'da yüksek kullanılabilirlik ve olağanüstü durum kurtarma SAP HANA büyük örnekleri](../virtual-machines/workloads/sap/hana-failover-procedure.md) açıklanan Dr yük devretme yordamını okur ve anlamış olursunuz
- DR konumunda depolama anlık görüntülerini ayarlamış ve yapılandırmış olmanız gerekir.
- Dr `DR.json` Storage birimleri ve Dr sunucusu 'ndaki ilişkili bilgilerle bir yapılandırma dosyası (örneğin,) oluşturulmuştur.
- DR sitesindeki adımları şu şekilde tamamladınız:
  - Depolama ile iletişimi etkinleştirin.
  - SAP HANA ile iletişimi etkinleştirin.

## <a name="set-up-disaster-recovery"></a>Olağanüstü durum kurtarma işlemini ayarlama

Microsoft, DR kurtarması için depolama düzeyinde çoğaltmayı destekler. DR 'yi kurmanın iki yolu vardır.

Biri **normaldir** ve diğeri **çok** önemlidir. **Normal** Dr 'de, yük devretme için Dr konumunda adanmış bir örneğiniz vardır. **Çok amaçlı** Dr SENARYOSUNDA, Dr sitesindeki Hana büyük örnek biriminde çalışan başka bir qa veya geliştirme Hana örneğiniz vardır. Ayrıca, devre dışı bırakılmış ve bu HANA büyük örnek birimine yük devretmek istediğiniz HANA örneğiyle aynı SID 'ye sahip önceden yüklenmiş bir HANA örneği de yüklediniz. Microsoft işlemleri, hazırlama sırasında hizmet Isteği formunda (SRF) belirtilen girişe bağlı olarak depolama çoğaltmasını dahil etmek için ortamı ayarlar.

> [!IMPORTANT]
> DR kurulumu için tüm önkoşulların karşılandığından emin olun.

## <a name="monitor-data-replication-from-primary-to-dr-site"></a>Birincil sunucudan DR sitesine veri çoğaltmasını izleme

Microsoft operasyon ekibi, birincil siteden DR sitesine DR bağlantısını zaten yönettiğinden ve izlersiniz.
Anlık görüntü komutunu kullanarak birincil sunucunuzdaki veri çoğaltmasını DR sunucusuna izleyebilirsiniz `azacsnap -c details --details replication` .

## <a name="perform-a-failover-to-dr-site"></a>DR sitesine yük devretme gerçekleştirme

DR sitesinde () yük devretme komutunu çalıştırın `azacsnap -c restore --restore revertvolume` .

> [!IMPORTANT]
> `azacsnap -c restore --restore revertvolume`Komut, üretim SITESINDEN Dr sitesine depolama çoğaltmasını keser. Çoğaltmayı yeniden kurmak için Microsoft Işlemlerine ulaşmalısınız. Çoğaltma yeniden etkinleştirildikten sonra, bu SID için DR depolamadaki tüm veriler başlatılır. Yük devretmeyi gerçekleştiren komut, en son çoğaltılan depolama anlık görüntüsünü kullanılabilir hale getirir. Daha eski bir anlık görüntüye geri yüklemeniz gerekiyorsa, işlemler DR sitesinde geri yüklenmiş daha önceki bir anlık görüntü sağlamaya yardımcı olabilecek bir destek isteği açın.

Yüksek düzeyde, DR yük devretmesi için izlemeniz gereken adımlar şunlardır:

- **Birincil** sitede Hana örneğini kapatmanız gerekir. Bu eylem yalnızca, veri tutarsızlıklardan yalnızca DR sitesine yük devretmeniz durumunda gereklidir.
- Üretim SID 'SI için DR düğümündeki HANA örneğini kapatın.
- `azacsnap -c restore --restore revertvolume`KURTARıLACAK SID Ile Dr düğümünde komutunu yürütün
  - Komut, birincil sunucudan DR sitesine depolama çoğaltma bağlantısını keser
  - Komut/Data ve/logbackups birimini geri yükler,/Shared Volume kurtarılamaz, ancak bunun yerine DR konumundaki mevcut/Shared for SID 'yi kullanır.
  - /Data ve/logbackups birimini bağlama – fstab dosyasına eklendiğinden emin olun
- HANA SYSTEMDB anlık görüntüsünü geri yükleyin. HANA Studio yalnızca, komut yürütmenin bir parçası olarak geri yüklenen depolama anlık görüntüsü altında bulunan en son HANA anlık görüntüsünü gösterir `azacsnap -c restore --restore revertvolume` .
- Kiracı veritabanını kurtarın.
- Üretim SID 'SI için DR sitesinde HANA örneğini başlatın (örnek: Bu örnekte H80).
- Test gerçekleştirin.

### <a name="example-performing-disaster-recovery"></a>Olağanüstü durum kurtarma gerçekleştirme örneği

Bu alt bölümde, olağanüstü durum kurtarma sitesine yük devretme için ayrıntılı adımlar açıklanmaktadır.

#### <a name="step-1-get-the-volume-details-of-the-dr-node"></a>1. Adım: DR düğümünün birim ayrıntılarını alın

`df –h`Yük devretmeden sonra başvurmak üzere dosya sistemlerini ve ilişkili birimleri listelemek için komutunu yürütün.

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0%
/dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0%
/sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-2-shut-down-hana-on-the-primary-site"></a>2. Adım: birincil sitede HANA 'yı kapatma

Üretim iş yüklerinin tamamen yük devretmesini gerçekleştiriyorsa ve birincil üretim sitesine bağlanmak mümkünse, yük devremekte olan SAP HANA örneklerini DR 'ye kapatın.

Örneğin, kök olarak oturum açarsa aşağıdaki örnekte SAP HANA nasıl kapatılabilen gösterilmektedir.  <sid>SAP HANA SID 'iyle değiştirin.

```bash
su - <sid>adm
HDB stop
```

#### <a name="step-3-shut-down-hana-on-dr-site"></a>3. Adım: DR sitesinde HANA 'yı kapatma

Birimleri geri yüklemeden önce DR sitesindeki SAP HANA kapatmak önemlidir.

Örneğin, kök olarak oturum açarsa aşağıdaki örnekte SAP HANA nasıl kapatılabilen gösterilmektedir.  <sid>SAP HANA SID 'iyle değiştirin.

```bash
su - <sid>adm
HDB stop
```

> [!IMPORTANT]
> Herhangi bir birimi geri yüklemeden önce DR sitesindeki HANA örneklerinin çevrimiçi olduğundan emin olun.

#### <a name="step-4-restore-the-volumes"></a>4. Adım: birimleri geri yükleme

```bash
azacsnap -c restore --restore revertvolume --dbsid H80
```

**_Dr yük devretme komutunun çıktısı_**.

```bash
azacsnap --configfile DR.json -c restore --restore revertvolume --dbsid H80
```

```output
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to complete a Disaster Recovery
  failover.
* Any other restore points must be handled by Microsoft Operations.
* All volumes ('data' and 'other') are reverted to their most recent snapshot.
* The SnapMirror replication relationship between Prod and DR will be broken.

  CAUTION: a failback will be required after running this command and failback
   might not be a quick process and will require multiple steps in coordination
   with Microsoft Operations.

Do you wish to continue? (y/n) [n]: y
Checking state of HLI volumes for SID 'H80'
Configured volumes (Data and Other) are not quiesced for revert, will retry in 00:00:10 seconds
Volumes All Ok to Revert = True
Reverting volume 'hana_data_h80_mnt00001_t020_xdp' to snapshot 'H80_HANA_DATA_30MIN.2020-09-16_0330.0'
DR.json Data Volume #1 'hana_data_h80_mnt00001_t020_xdp' assigning to mountpoint 'mnt00001'
Reverting volume 'hana_log_backups_h80_t020_xdp01' to snapshot 'H80_HANA_LOGS_3MIN_X9.2020-09-16_0339.recent'
DR.json Other Volume #1 'hana_log_backups_h80_t020_xdp01' assigning to mountpoint '01'
HLI Volume revert completed for SID 'H80'
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*********************  HANA DR Restore Steps  **********************************
* Please complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
```

> [!NOTE]
> Bir DR yük devretmesinin depolama hazırlığını tamamlaması için konsolun sonundaki adımların alınması gerekir.

#### <a name="step-5-unmount-unnecessary-filesystems"></a>5. Adım: gereksiz dosya sistemlerini çıkarma

`umount`Gerekli olmayan dosya sistemlerini/birimleri çıkarmak için komutunu yürütün.

```bash
umount <Mount point>
```

Veri ve günlük yedeklemesi bağlama noktalarını çıkarın. Genişleme senaryosunda birden çok veri bağlama noktanız olabilir.

#### <a name="step-6-configure-the-mount-points"></a>6. Adım: bağlama noktalarını yapılandırma

`/etc/fstab`BIRINCIL SID (Bu örnekte SID = H80) için verileri ve günlük yedeklemeleri girdilerini açıklama olarak değiştirin ve birincil SITE Dr birimlerinden oluşturulan yeni bağlama noktası girdilerini ekleyin. Yeni bağlama noktası girdileri komut çıkışında verilmiştir.

- DR sitesinde çalışan mevcut bağlama noktalarını şu `#` karakterle Açıklama:

  ```output
  #172.18.20.241:/hana_data_h80_mnt00001_t020_vol /hana/data/H80/mnt00001 nfs     rw,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  #172.18.20.241:/hana_log_backups_h80_t020 /hana/logbackups/H80 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

- Aşağıdaki satırları öğesine ekleyin `/etc/fstab`
  > Bu, komuttan aynı çıkış olmalıdır

  ```output
  10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

#### <a name="step-7-mount-the-recovery-volumes"></a>7. Adım: kurtarma birimlerini bağlama

`mount –a`Tüm bağlama noktalarını bağlamak için komutunu yürütün.

```bash
mount -a
```

Şimdi `df –h` çalıştırırsanız, `*_dp` bağlı birimleri görmeniz gerekir.

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0% /dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0% /sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-8-recover-the-systemdb"></a>8. Adım: SYSTEMDB 'yi kurtarma

HANA Studio 'dan SYSTEMDB örneğine sağ tıklayın ve "yedekleme ve kurtarma" seçeneğini belirleyip "sistem veritabanını kurtar" ı seçin

Bir veritabanını bir anlık görüntüden, özellikle de SYSTEMDB 'den kurtarmaya yönelik kılavuza bakın.

#### <a name="step-9-recover-the-tenant-database"></a>9. Adım: kiracı veritabanını kurtarma

HANA Studio 'dan SYSTEMDB örneğine sağ tıklayın ve "yedekleme ve kurtarma" seçeneğini belirleyip "Kiracı veritabanını kurtar" seçeneğini seçin.

Veritabanını, özellikle de KIRACı veritabanlarını bir anlık görüntüden kurtarmaya yönelik kılavuza bakın.

### <a name="run-azacsnap--c-backup-at-the-dr-site"></a>`azacsnap -c backup`Dr sitesinde Çalıştır

DR sitesinde anlık görüntü tabanlı yedeklemeler çalıştırıyorsanız, DR sitesindeki yapılandırma dosyasında yapılandırılan HANA sunucu adı, `azacsnap` üretim sunucusu adı ile aynı olmalıdır.

> [!IMPORTANT]
> Çalıştırmak, `azacsnap -c backup` Dr sitesinde depolama anlık görüntüleri oluşturabilir, bunlar otomatik olarak başka bir siteye çoğaltılmaz.  Tüm dosyaları veya verileri özgün üretim sitesine geri döndürmeyi daha iyi anlamak için Microsoft Işlemleriyle birlikte çalışın.

## <a name="next-steps"></a>Sonraki adımlar

- [Anlık görüntü ayrıntılarını al](azacsnap-cmd-ref-details.md)
- [Bir yedekleme yapın](azacsnap-cmd-ref-backup.md)
