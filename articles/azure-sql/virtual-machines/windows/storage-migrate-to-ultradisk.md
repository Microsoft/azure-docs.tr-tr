---
title: Günlük diskini Ultra diske geçir
description: Azure sanal makinesi (VM) günlük diskine yönelik SQL Server, yüksek performans ve düşük gecikme süresinden faydalanmak için Azure Ultradisk 'a nasıl geçirebileceğinizi öğrenin.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e8410b4e0997798eba5ee91f361c3a5f1ce47ef1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104586310"
---
# <a name="migrate-log-disk-to-ultra-disk"></a>Günlük diskini Ultra diske geçir
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure Ultra diskler, Azure sanal makinesi (VM) üzerinde SQL Server için yüksek aktarım hızı, yüksek ıOPS ve sürekli düşük gecikmeli disk depolama alanı sunar. 

Bu makalede, Ultra diskler tarafından sunulan performans avantajlarından yararlanmak için günlük diskinizi bir ultra SSD 'ye geçirmenize öğretilir. 

## <a name="back-up-database"></a>Veritabanını yedekle

Veritabanınızın [tam yedeklemesini](backup-restore.md) doldurun. 

## <a name="attach-disk"></a>Disk Ekle

VM 'de ultradisk uyumluluğunu etkinleştirdikten sonra Ultra SSD sanal makinenize ekleyin. 

Ultra disk, VM boyutlarının ve bölgelerinin bir alt kümesinde desteklenir. Devam etmeden önce, sanal makinenizin Ultra diski destekleyen bir bölgede, bölgede ve boyutta olduğunu doğrulayın. Azure CLı veya PowerShell kullanarak [VM boyutunu ve bölgesini tespit edebilir ve doğrulayabilirsiniz](../../../virtual-machines/disks-enable-ultra-ssd.md#determine-vm-size-and-region-availability) . 

### <a name="enable-compatibility"></a>Uyumluluğu etkinleştir

Uyumluluğu etkinleştirmek için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/)sanal makinenize gidin. 
1. Sanal makineyi durdurun/serbest bırakın. 
1. **Ayarlar** altında **diskler** ' i seçin ve ardından **ek ayarlar**' ı seçin. 

   :::image type="content" source="media/storage-migrate-to-ultradisk/additional-disks-settings-azure-portal.png" alt-text="Azure portal ayarlar altında diskler için ek ayarları seçin":::

1. **Ultra disk uyumluluğunu etkinleştirmek** için **Evet** ' i seçin. 

   :::image type="content" source="../../../virtual-machines/media/virtual-machines-disks-getting-started-ultra-ssd/enable-ultra-disks-existing-vm.png" alt-text="Evet seçeneğini gösteren ekran görüntüsü.":::

1. **Kaydet**’i seçin. 



### <a name="attach-disk"></a>Disk Ekle

Sanal makinenize bir ultra disk iliştirmek için Azure portal kullanın. Ayrıntılar için bkz. [bir ultra disk iliştirme](../../../virtual-machines/disks-enable-ultra-ssd.md#attach-an-ultra-disk).

Disk eklendikten sonra, Azure portal kullanarak sanal makineyi bir kez daha başlatın. 



## <a name="format-disk"></a>Diski Biçimlendir

Sanal makinenize bağlanın ve ultra diskinizi biçimlendirin.  

Ultra diskinizi biçimlendirmek için aşağıdaki adımları izleyin:

1. Uzak Masaüstü Protokolü (RDP) kullanarak sanal makinenize bağlanın.
1. Yeni bağlı Ultra diskinizi biçimlendirmek ve bölümlemek için [disk yönetimi](/windows-server/storage/disk-management/overview-of-disk-management) 'ni kullanın. 


## <a name="use-disk-for-log"></a>Günlük için disk kullan

Yeni günlük sürücüsünü kullanmak için SQL Server yapılandırın. Bunu Transact-SQL (T-SQL) veya SQL Server Management Studio (SSMS) kullanarak yapabilirsiniz. SQL Server hizmet hesabı için kullanılan hesabın yeni günlük dosyası konumu üzerinde tam denetimi olmalıdır. 

### <a name="configure-permissions"></a>İzinleri yapılandırma

1. SQL Server tarafından kullanılan hizmet hesabını doğrulayın. SQL Server Yapılandırma Yöneticisi veya Services. msc kullanarak bunu yapabilirsiniz.
1. Yeni diskinize gidin. 
1. Günlük dosyanız için kullanılacak bir klasör (veya birden çok klasör) oluşturun. 
1. Klasöre sağ tıklayın ve **Özellikler**' i seçin.
1. **Güvenlik** sekmesinde, SQL Server hizmet hesabına tam denetim erişimi verin. 
1. Ayarlarınızı kaydetmek için **Tamam ' ı**  seçin. 
1. SQL verilerinin olmasını planladığınız her kök düzeyi klasör için bunu tekrarlayın. 

### <a name="use-new-log-drive"></a>Yeni günlük sürücüsü kullan 

İzin verildikten sonra, veritabanını ayırmak ve var olan günlük dosyalarını yeni konuma taşımak için Transact-SQL (T-SQL) veya SQL Server Management Studio (SSMS) kullanın.

   > [!CAUTION]
   > Veritabanını ayırmak, çevrimdışı duruma gelir, bağlantıları kapatıyor ve uçuşdaki tüm işlemleri geri alacak. Dikkatli ve bir süre sonra bakım penceresi sırasında devam edin. 



# <a name="transact-sql-t-sql"></a>[Transact-SQL (T-SQL)](#tab/tsql)

T-SQL ' y i kullanarak mevcut dosyaları yeni bir konuma taşıyın:

1. SQL Server Management Studio veritabanınıza bağlanın ve **Yeni bir sorgu** penceresi açın. 
1. Mevcut dosyaları ve konumları al:

   ```sql
   USE AdventureWorks
   GO

   sp_helpfile
   GO
   ```

1. Veritabanını ayır: 

   ```sql
   USE master
   GO

   sp_detach_db 'AdventureWorks'
   GO
   ```

1. Günlük dosyasını Ultra diskteki yeni konuma taşımak için dosya Gezgini 'ni kullanın. 

1. Yeni dosya konumlarını belirterek veritabanını ekleyin: 

   ```sql
    sp_attach_db 'AdventureWorks'
   'E:\Fixed_FG\AdventureWorks.mdf',
   'E:\Fixed_FG\AdventureWorks_2.ndf',
   'F:\New_Log\AdventureWorks_log.ldf'
   GO
   ```

Bu noktada, veritabanı yeni konumdaki günlük ile çevrimiçi olarak gelir. 



# <a name="sql-server-management-studio-ssms"></a>[SQL Server Management Studio (SSMS)](#tab/ssms)

SSMS kullanarak var olan dosyaları yeni bir konuma taşıyın:

1. SQL Server Management Studio (SSMS) ' de veritabanınıza bağlanın. 
1. Veritabanına sağ tıklayın, **Özellikler** ' i seçin ve ardından **dosyalar**' ı seçin. 
1. Mevcut dosyaların yolunu aklınızda edin. 
1. İletişim kutusunu kapatmak için **Tamam ' ı** seçin. 
1. Veritabanına sağ tıklayın, **Görevler**  >  **Ayır**' ı seçin. 
1. Veritabanını ayırmak için Sihirbazı izleyin. 
1. Günlük dosyasını el ile yeni konuma taşımak için dosya Gezgini 'ni kullanın.
1. Veritabanını SQL Server Management Studio iliştirme
   1. **Nesne Gezgini** **veritabanları** ' na sağ tıklayın ve **veritabanı Ekle**' yi seçin. 
   1. İletişim kutusunu kullanarak, günlük dosyası dahil olmak üzere her dosyayı yeni konumuna ekleyin. 
   1. Veritabanını iliştirmek için **Tamam ' ı** seçin. 

Bu noktada, veritabanı yeni konumdaki günlük ile çevrimiçi olarak gelir.

---


## <a name="next-steps"></a>Sonraki adımlar

Performansı artırmak için ek ayarlar için [en iyi performans uygulamalarını](performance-guidelines-best-practices.md) gözden geçirin. 

Azure sanal makinelerinde SQL Server genel bir bakış için aşağıdaki makalelere bakın:

- [Windows VM 'lerinde SQL Server genel bakış](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Linux üzerinde SQL Server VM 'lerine genel bakış](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
