---
title: Azure NetApp Files için Azure uygulaması tutarlı anlık görüntü aracı 'nı kullanarak geri yükleme | Microsoft Docs
description: Azure NetApp Files ile kullanabileceğiniz Azure uygulaması tutarlı anlık görüntü aracının geri yükleme komutunu çalıştırmaya yönelik bir kılavuz sağlar.
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
ms.openlocfilehash: 793b4da8fcf46ba4d5618f8ada86f9c3c8026ffd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865273"
---
# <a name="restore-using-azure-application-consistent-snapshot-tool-preview"></a>Azure uygulaması tutarlı anlık görüntü aracı 'nı (Önizleme) kullanarak geri yükleme

Bu makalede, Azure NetApp Files ile kullanabileceğiniz Azure uygulaması tutarlı anlık görüntü aracının geri yükleme komutunu çalıştırmaya yönelik bir kılavuz sağlanmaktadır.

## <a name="introduction"></a>Giriş

Bir anlık görüntüden toplu geri yükleme yapmak komutu kullanılarak yapılır `azacsnap -c restore` .

> [!IMPORTANT]
> Bu, bir veritabanı kurtarması gerçekleştirmez, aşağıdaki seçeneklerin her biri için açıklandığı şekilde yalnızca bir birim geri yükleme işlemi yapmaz.

## <a name="command-options"></a>Komut seçenekleri

`-c restore`Komut aşağıdaki seçeneklere sahiptir:

- `--restore snaptovol` Hedef birimdeki en son anlık görüntüye göre yeni bir birim oluşturur. Bu komut, yeni birimi oluşturmak için temel olarak en son birim anlık görüntüsünü kullanarak, yapılandırılan hedef birime dayalı yeni bir "kopyalanmış" birim oluşturur.  Bu komut, birincil sunucudan ikinciye depolama çoğaltmasını kesintiye uğratmaz. Bunun yerine, kullanılabilir en son anlık görüntünün kopyaları DR sitesinde oluşturulur ve kopyalanan birimlerin önerilen dosya sistemi bağlama noktaları sunulur. Bu komut, **Dr bölgesindeki** (yani, hedef sistemde başarısız olan) Azure büyük örnek sisteminde çalıştırılmalıdır.

- `--restore revertvolume` Hedef birimi en son anlık görüntüye göre önceki bir duruma geri döndürür.  Bu komutu, DR yük devretmesinin parçası olarak eşleştirilmiş DR bölgesine kullanmaktır. Bu komut, birincil siteden ikincil siteye depolama çoğaltmasını **sonlandırır** ve hedef Dr birimini, geri dönülebilecek Dr birimleri için önerilen dosya bağlama NOKTALARıYLA birlikte Dr birimlerindeki kullanılabilir en son anlık görüntüsüne geri döndürür. Bu komut, **Dr bölgesindeki** (yani, hedef sistemde başarısız olan) Azure büyük örnek sisteminde çalıştırılmalıdır.
    > [!NOTE]
    > Alt komut ( `--restore revertvolume` ) yalnızca Azure büyük örneği için kullanılabilir ve Azure NetApp Files için kullanılamaz.
- `--dbsid <SAP HANA SID>` , birim geri yükleme komutlarının ' ye uygulanması için yapılandırma dosyasından seçilmiş olan SAP HANA SID 'sidir.

- `[--configfile <config filename>]` , özel yapılandırma dosyası adlarına izin veren isteğe bağlı bir parametredir.

## <a name="perform-a-test-dr-failover-azacsnap--c-restore---restore-snaptovol"></a>Sınama DR yük devretmesi gerçekleştirme `azacsnap -c restore --restore snaptovol`

Bu komut, "tam" DR yük devretme komutuna ( `--restore restorevolume` ) benzer, ancak birincil siteyle olağanüstü durum kurtarma sitesi arasındaki çoğaltmayı bozmadan, olağanüstü durum kurtarma birimlerinden bir kopya birimi oluşturulur ve bu da Dr sitesinde en son anlık görüntünün geri yüklenmesine izin verir. Bu klonlanan birimler daha sonra müşteri tarafından, birincil siteyle olağanüstü durum kurtarma sitesi arasındaki çoğaltma anlaşmasını kesen HANA ortamının tamamen yük devretmesini yürütmek zorunda kalmadan, olağanüstü durum kurtarmayı test etmek için kullanılabilir.

- Birden çok farklı geri yükleme noktası bu şekilde, her biri kendi geri yükleme noktası ile test edilebilir.
- Kopya, komutun yürütüldüğü zaman damgası tarafından belirlenir ve çalıştırıldığında en son verileri ve diğer anlık görüntüyü temsil eder.

> [!IMPORTANT]
> Bu işlem yalnızca Azure büyük örneği için geçerlidir.
>
> - Bu komut yürütüldüğünde, 4 hafta sonra klonların silinmesinden önce ile işlemler için iletişim e-postası gerekir.
> - Bu komutun her yürütülmesi, test tamamlandığında Microsoft Işlemleri tarafından silinmesi gereken yeni bir kopya oluşturur.
> - Oluşturulan tüm kopya birimleri 4 hafta sonra otomatik olarak silinir.

Yapılandırma dosyası (örneğin, `DR.json` ) üretim birimleri değil yalnızca Dr birimlerini içermelidir, aksi takdirde üretim birimlerinde klonlar oluşturulabilir.

### <a name="output-of-the-azacsnap--c-restore---restore-snaptovol-command-for-single-node-scenario"></a>`azacsnap -c restore --restore snaptovol`Komutun çıkışı (Single-Node senaryosu için)

```output
> azacsnap --configfile DR.json -c restore --restore snaptovol --dbsid H80
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to simulate a Disaster Recovery
  failover without actually requiring a failover and subsequent failback.
* Any other restore points must be handled by Microsoft Operations.
* As part of the process, a clone is created of the each of the 'data' and 'other'
  volumes per the configuration file.

Do you wish to continue? (y/n) [n]: y

About to create clones of volumes based on the latest snapshot, these will be
kept for 4 weeks before being automatically deleted by Microsoft Operations.
Enter an email address to contact when deleting clones: <b>person@nowhere.com</b>
Checking state of HLI volumes for SID 'PEW'
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_sapprdhdb80_mnt00001_t020_xdp_rwclone_20200916_0256  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_sapprdhdb80_t020_xdp_rwclone_20200916_0256  /hana/log_backups/H80/01 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*******************  HANA Test DR Restore Steps  ******************************
* Complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
*  These snapshot copies (clones) are kept for 4 weeks before                  *
*  being automatically removed.                                                *
*  Please contact Microsoft Operations to delete them earlier.                 *
********************************************************************************
```

> [!IMPORTANT]
> "Birime göre bağlama noktaları görüntüleme" çıkışı, çeşitli senaryolar için farklıdır.

## <a name="perform-full-dr-failover-azacsnap--c-restore---restore-revertvolume"></a>Tam DR yük devretmesi gerçekleştirme `azacsnap -c restore --restore revertvolume`

Bu komut, birincil siteden ikincil siteye depolama çoğaltmasını **sonlandırır** , Dr birimlerindeki en son anlık görüntüyü geri yükler ve Dr birimleri için bağlama noktaları sağlar.

Bu komut, `DR.json` yalnızca Dr birimlerine sahip bir yapılandırma dosyası (örneğin,) KULLANıLARAK Dr sunucusunda yürütülmelidir!

Komutunu yürüterek DR sitesine yük devretme işlemi gerçekleştirin `azacsnap -c restore --restore revertvolume` .  Bu komut, bir SID 'nin parametre olarak eklenmesini gerektirir. Bu, DR sitesinde kurtarılması gereken HANA örneğinin SID 'sidir.

> [!IMPORTANT]
> Yalnızca DR alıştırmasını veya bir testi gerçekleştirmeyi planlıyorsanız bu komutu çalıştırın. Bu komut çoğaltmayı keser. Çoğaltmayı yeniden etkinleştirmek için Microsoft Işlemlerine başvurmanız gerekir.

Yüksek düzeyde, DR yük devretmesini çalıştırmaya yönelik adımlar aşağıda verilmiştir:

- **Birincil** sitede Hana örneğini kapatmanız gerekir. Bu eylem, yalnızca veri Tutarsızlıklardan kaçınmak için DR sitesine yük devretmek için gereklidir.
- Üretim SID 'SI için DR düğümündeki HANA örneğini kapatın.
- `azacsnap -c restore --restore revertvolume`KURTARıLACAK SID Ile Dr düğümünde komutunu yürütün.
  - Komut, birincil sunucudan DR sitesine depolama çoğaltma bağlantısını keser
  - Komut "veri" ve "diğer" birimleri yapılandırılmış olarak geri yükler.  Genellikle, bu işlem `/hana/data` ve dosya sistemlerine yönelik birimler için olacaktır `/hana/logbackups` .  `/hana/shared`Dosya sistemi kurtarılmaz, ancak bunun yerıne Dr konumunda var olan `/hana/shared` SID 'yi kullanır.
  - `/hana/data`Ve birimlerini bağlama `/hana/logbackups` – dosyaya eklendiğinden emin olun `/etc/fstab`
- HANA SYSTEMDB anlık görüntüsünü geri yükleyin. HANA Studio yalnızca anlık görüntü komutu yürütmesinin bir parçası olarak geri yüklenen depolama anlık görüntüsünün altında bulunan en son HANA anlık görüntüsünü gösterir `azacsnap -c restore --restore revertvolume` .
- Kiracı veritabanını kurtarın.
- Üretim SID 'SI için DR sitesinde HANA örneğini başlatın (örnek: Bu örnekte H80).
- Herhangi bir veritabanı testini gerçekleştirin.

## <a name="next-steps"></a>Sonraki adımlar

- [Bir yedekleme yapın](azacsnap-cmd-ref-backup.md)
- [Anlık görüntü ayrıntılarını al](azacsnap-cmd-ref-details.md)
