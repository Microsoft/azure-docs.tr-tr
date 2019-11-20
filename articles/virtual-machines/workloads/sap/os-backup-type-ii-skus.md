---
title: Azure 'da SAP HANA işletim sistemi yedeklemesi ve geri yüklemesi (büyük örnekler) tür II SKU 'Ları | Microsoft Docs
description: Azure 'da SAP HANA için Işletim sistemi yedeklemesi ve geri yükleme gerçekleştirin (büyük örnekler) tür II SKU 'Ları
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 046daed4f548d24010c3d3bef177cee8cf24a55e
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098731"
---
# <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>Düzeltme 3 damgalarının tür II SKU 'Ları için işletim sistemi yedekleme ve geri yükleme

Bu belgede, bir işletim sistemi dosya düzeyinde yedekleme gerçekleştirme ve düzeltme 3 ' ün HANA büyük örneklerinin **tür II SKU 'ları** için geri yükleme adımları açıklanır. 

>[!Important]
> **Bu makale, düzeltme 4 HANA büyük örnek damgalarındaki tür II SKU dağıtımları için geçerlidir.** Düzeltme 4 HANA büyük örnek damgalarına dağıtılmış olan II HANA büyük örnek birimlerinin önyükleme LUN 'leri, düzeltme 3 damgalarının tür ı SKU 'su olan bu durum olduğu için depolama anlık görüntüleriyle yedeklenebilir.


>[!NOTE]
>İşletim sistemi yedekleme betikleri, sunucuda önceden yüklenmiş olan arka yazılımı kullanır.  

Sağlama, Microsoft `Service Management` ekibi tarafından tamamlandıktan sonra varsayılan olarak, işletim sisteminin dosya sistemi düzeyini yedeklemek için iki yedekleme zamanlamalarıyla yapılandırılır. Aşağıdaki komutu kullanarak yedekleme işlerinin zamanlamalarını kontrol edebilirsiniz:
```
#crontab –l
```
Aşağıdaki komutu kullanarak yedekleme zamanlamasını dilediğiniz zaman değiştirebilirsiniz:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>El ile yedekleme nasıl yapılır?

İşletim sistemi dosya sistemi yedeklemesi, zaten bir **cron işi** kullanılarak zamanlandı. Ancak, işletim sistemi dosya düzeyi yedeklemesini de el ile gerçekleştirebilirsiniz. El ile yedekleme gerçekleştirmek için aşağıdaki komutu çalıştırın:

```
#rear -v mkbackup
```
Aşağıdaki ekran gösteri, örnek el ile yedeklemeyi gösterir:

![oluşturulacağı](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Yedekleme nasıl geri yüklenir?

Bir tam yedeklemeyi veya tek bir dosyayı yedekten geri yükleyebilirsiniz. Geri yüklemek için şu komutu kullanın:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Geri yükleme işleminden sonra dosya geçerli çalışma dizininde kurtarılır.

Aşağıdaki komut, yedekleme dosyasından bir */etc/fstabfile* yedeğinden geri yüklemeyi gösterir *. tar. gz*
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>Yedeklemeden geri yüklendikten sonra dosyayı istenen konuma kopyalamanız gerekir.

Aşağıdaki ekran görüntüsünde, tüm yedeklemenin geri yüklenmesi gösterilmektedir:

![HowtoRestoreaBackup. PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Arka aracı nasıl yüklenir ve yapılandırma nasıl değiştirilir? 

Rahat ve-kurtar (arka) paketleri, HANA büyük örneklerinin **tür II SKU 'larına** **önceden yüklenir** ve sizin için hiçbir işlem yapmanız gerekmez. İşletim sistemi yedeklemesi için arka öğesini kullanmaya doğrudan başlayabilirsiniz.
Ancak, paketleri kendi kendinize yüklemeniz gereken durumlarda, arka aracı yüklemek ve yapılandırmak için listelenen adımları izleyebilirsiniz.

**Arka** yedekleme paketlerini yüklemek için aşağıdaki komutları kullanın:

**SLES** işletim sistemi için aşağıdaki komutu kullanın:
```
#zypper install <rear rpm package>
```
**RHEL** işletim sistemi için aşağıdaki komutu kullanın: 
```
#yum install rear -y
```
Arka Aracı yapılandırmak için, */etc/rear/Local.exe dosyasında* **OUTPUT_URL** ve **BACKUP_URL** parametrelerini güncelleştirmeniz gerekir.
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

Aşağıdaki ekran görüntüsünde, tüm yedeklemenin geri yüklenmesi gösterilmektedir: ![RearToolConfiguration. PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
