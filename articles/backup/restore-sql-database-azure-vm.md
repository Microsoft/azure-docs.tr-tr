---
title: Azure VM 'de SQL Server veritabanlarını geri yükleme
description: Bu makalede, bir Azure VM üzerinde çalışan ve Azure Backup yedeklenen SQL Server veritabanlarının nasıl geri yükleneceği açıklanmaktadır.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 0dbf5c48884dc665355d2806ff343facfbeffc29
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171897"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Azure VM 'lerinde SQL Server veritabanlarını geri yükleme

Bu makalede, [Azure Backup](backup-overview.md) hizmetinin bir Azure Backup kurtarma hizmetleri kasasına yedeklemiş olduğu bir Azure sanal MAKINESINDE (VM) çalışan SQL Server bir veritabanının nasıl geri yükleneceği açıklanmaktadır.

Bu makalede SQL Server veritabanlarının nasıl geri yükleneceği açıklanmaktadır. Daha fazla bilgi için bkz. [Azure VM 'lerde SQL Server veritabanlarını yedekleme](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Bir saate veya kurtarma noktasına geri yükleme

Azure Backup, Azure VM 'lerinde çalışan SQL Server veritabanlarını şu şekilde geri yükleyebilir:

- İşlem günlüğü yedeklerini kullanarak belirli bir tarih veya saate (ikinci olarak) geri yükleyin. Azure Backup, uygun tam değişiklik yedeklemesini ve seçilen saate göre geri yüklemek için gereken günlük yedeklemeleri zincirini otomatik olarak belirler.
- Belirli bir kurtarma noktasına geri yüklemek için belirli bir tam veya değişiklik yedeklemesini geri yükleyin.

## <a name="prerequisites"></a>Önkoşullar

Bir veritabanını geri yüklemeden önce aşağıdakileri göz önünde bulabilirsiniz:

- Veritabanını aynı Azure bölgesindeki bir SQL Server örneğine geri yükleyebilirsiniz.
- Hedef sunucu, kaynakla aynı kasada kayıtlı olmalıdır.
- Bir TDE şifreli veritabanını başka bir SQL Server geri yüklemek için öncelikle [sertifikayı hedef sunucuya geri yüklemeniz](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017)gerekir.
- "Ana" veritabanını geri yüklemeden önce, **-ı AzureWorkloadBackup**başlangıç seçeneğini kullanarak SQL Server örneğini tek kullanıcılı modda başlatın.
  - **-D** değeri, istemcinin adıdır.
  - Bağlantıyı yalnızca belirtilen istemci adı açabilir.
- Tüm sistem veritabanları için (model, ana, msdb) geri yüklemeyi tetikleyebilmeniz için önce SQL Server Agent hizmetini durdurun.
- Bu veritabanlarından herhangi birine bir bağlantı almaya çalışabilir olabilecek tüm uygulamaları kapatın.
- Sunucuda çalışan birden fazla örneize sahipseniz, tüm örnekler çalışır durumda olmalıdır, aksi takdirde sunucu, veritabanını geri yüklemeniz için hedef sunucular listesinde görünmez.

## <a name="restore-a-database"></a>Bir veritabanını geri yükleme

Geri yüklemek için aşağıdaki izinlere sahip olmanız gerekir:

- Geri yüklemeyi yaptığınız kasada **yedekleme işletmeni** izinleri.
- Yedeklenen kaynak VM 'ye **katkıda bulunan (yazma)** erişimi.
- Hedef VM 'ye **katkıda bulunan (yazma)** erişimi:
  - Aynı VM 'ye geri yüklüyorsanız, kaynak VM 'dir.
  - Alternatif bir konuma geri yüklüyorsanız, bu yeni hedef VM 'dir.

Aşağıdaki şekilde geri yükleyin:

1. SQL Server VM kaydedildiği kasayı açın.
2. Kasa panosunda, **kullanım**altında **yedekleme öğeleri**' ni seçin.
3. **Yedekleme öğeleri**' nde, **yedekleme yönetimi türü**altında **Azure VM 'de SQL**' i seçin.

    ![Azure VM 'de SQL seçin](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Geri yüklenecek veritabanını seçin.

    ![Geri yüklenecek veritabanını seçin](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Veritabanı menüsünü gözden geçirin. Aşağıdakiler dahil olmak üzere veritabanı yedeklemesi hakkında bilgi sağlar:

    - En eski ve en son geri yükleme noktaları.
    - Tam ve toplu günlük kurtarma modundaki ve işlem günlüğü yedeklemeleri için yapılandırılmış olan veritabanları için son 24 saat için günlük yedekleme durumu.

6. **Geri yükle**' yi seçin.

    ![Geri yükle 'Yi seçin](./media/backup-azure-sql-database/restore-db.png)

7. **Yapılandırma geri yükleme**' de, verilerin nereye (veya nasıl) geri yükleneceğini belirtin:
   - **Alternatif konum**: veritabanını alternatif bir konuma geri yükleyin ve özgün kaynak veritabanını koruyun.
   - **DB üzerine yaz**: verileri özgün kaynakla aynı SQL Server örneğine geri yükleyin. Bu seçenek özgün veritabanının üzerine yazar.

    > [!IMPORTANT]
    > Seçilen veritabanı her zaman açık kullanılabilirlik grubuna aitse, SQL Server veritabanının üzerine yazılmasına izin vermez. Yalnızca **alternatif konum** kullanılabilir.
    >
   - **Dosya olarak geri yükle**: veritabanı olarak geri yüklemek yerine, daha sonra SQL Server Management Studio kullanarak dosyaların bulunduğu herhangi bir makinede veritabanı olarak kurtarılabilen yedekleme dosyalarını geri yükleyin.
     Yapılandırma menüsünü geri ![](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>Alternatif bir konuma geri yükleme

1. **Geri yükleme yapılandırma** menüsünde, **geri yükleme yeri**' nin altında, **alternatif konum**' u seçin.
2. Veritabanını geri yüklemek istediğiniz SQL Server adı ve örneği seçin.
3. **Geri yüklenen veritabanı adı** kutusuna hedef veritabanının adını girin.
4. Geçerliyse, **SEÇILI SQL örneğinde aynı ada sahip BIR veritabanı zaten varsa üzerine yaz**' ı seçin.
5. **Tamam**’ı seçin.

    ![Geri yükleme yapılandırma menüsü için değerler sağlayın](./media/backup-azure-sql-database/restore-configuration.png)

6. **Geri yükleme noktası seç**bölümünde [belirli bir noktaya geri yükleme](#restore-to-a-specific-point-in-time) veya [belirli bir kurtarma noktasına geri yükleme](#restore-to-a-specific-restore-point)yapılıp yapılmayacağını seçin.

    > [!NOTE]
    > Zaman içinde geri yükleme yalnızca tam ve toplu günlük kurtarma modundaki veritabanlarına yönelik günlük yedeklemeleri için kullanılabilir.

### <a name="restore-and-overwrite"></a>Geri yükleme ve üzerine yazma

1. **Geri yükleme yapılandırma** menüsünde, **geri yükleme yeri**' nin altında, **DB > üzerine yaz** **' ı seçin**.

    ![DB üzerine yaz seçeneğini belirleyin](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. [Belirli bir noktaya geri yüklemek](#restore-to-a-specific-point-in-time)için **geri yükleme noktası seç**bölümünde Günlükler ' i **(zaman içindeki nokta)** seçin. Veya [belirli bir kurtarma noktasına](#restore-to-a-specific-restore-point)geri yüklemek için **tam & Türev** öğesini seçin.

    > [!NOTE]
    > Zaman içinde geri yükleme yalnızca tam ve toplu günlük kurtarma modundaki veritabanlarına yönelik günlük yedeklemeleri için kullanılabilir.

### <a name="restore-as-files"></a>Dosya olarak geri yükleme

Yedekleme verilerini bir veritabanı yerine. bak dosyaları olarak geri yüklemek için **dosya olarak geri yükle**' yi seçin. Dosyalar belirtilen bir yola alındıktan sonra, bu dosyaları bir veritabanı olarak geri yüklemek istediğiniz herhangi bir makineye alabilirsiniz. Bu dosyaları herhangi bir makineye taşıyabilmekte olan virtuale tarafından artık verileri abonelikler ve bölgeler arasında geri yükleyebilirsiniz.

1. Geri yükleme **yapılandırma** menüsünde, geri yükleme **yeri**' nin altında, **dosya olarak geri yükle**' yi seçin.
2. Yedekleme dosyalarını geri yüklemek istediğiniz SQL Server adını seçin.
3. **Sunucu üzerindeki hedef yolda** , adım 2 ' de seçilen sunucudaki klasör yolunu girin. Bu, hizmetin tüm gerekli yedekleme dosyalarının dökümünü alacak konumudur. Genellikle, bir ağ paylaşımının yolu veya hedef yolu olarak belirtildiğinde bağlı bir Azure dosya paylaşımının yolu, bu dosyalara aynı ağdaki diğer makineler veya bunlara bağlı aynı Azure dosya paylaşımıyla daha kolay erişim sağlar.
4. **Tamam**’ı seçin.

![Dosya olarak geri yükle ' yi seçin](./media/backup-azure-sql-database/restore-as-files.png)

5. Tüm kullanılabilir. bak dosyalarının geri yükleneceği **geri yükleme noktasını** seçin.

![Geri yükleme noktası seçin](./media/backup-azure-sql-database/restore-point.png)

6. Seçilen kurtarma noktasıyla ilişkili tüm yedekleme dosyaları, hedef yoluna dökülür. Dosyaları, SQL Server Management Studio kullanarak mevcut oldukları herhangi bir makinede bir veritabanı olarak geri yükleyebilirsiniz.

![Hedef yolundaki yedekleme dosyaları geri yüklendi](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>Belirli bir zaman noktasına geri yükleme

Geri yükleme türü olarak **Günlükler (zaman içinde)** seçtiyseniz şunları yapın:

1. **Geri yükleme tarihi/saati**altında takvimi açın. Takvimde, kurtarma noktaları olan tarihler kalın türde görüntülenir ve geçerli tarih vurgulanır.
1. Kurtarma noktaları olan bir tarih seçin. Kurtarma noktası olmayan tarihleri seçemezsiniz.

    ![Takvimi açın](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Bir tarih seçtikten sonra, zaman çizelgesi grafiğinde kullanılabilir kurtarma noktaları sürekli bir aralıkta görüntülenir.
1. Zaman çizelgesi grafiğinde kurtarma için bir saat belirtin veya bir saat seçin. Sonra **Tamam**’ı seçin.

    ![Geri yükleme saati seçin](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

1. **Gelişmiş yapılandırma** menüsünde, geri yüklemeden sonra veritabanını işlemsel halde tutmak ISTIYORSANıZ, **norecovery ile geri yüklemeyi**etkinleştirin.
1. Hedef sunucudaki geri yükleme konumunu değiştirmek istiyorsanız, yeni bir hedef yolu girin.
1. **Tamam**’ı seçin.

    ![Gelişmiş yapılandırma menüsü](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. **Geri yükleme menüsünde geri** **Yükle ' yi seçerek restore** işini başlatın.
1. Geri yükleme ilerlemesini **Bildirimler** alanında izleyin veya veritabanı menüsünde **işleri geri yükle** ' yi seçerek izleyin.

    ![İş ilerlemesini geri yükleme](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>Belirli bir geri yükleme noktasına geri yükleme

Geri yükleme türü olarak **tam & türev** seçtiyseniz şunları yapın:

1. Listeden bir kurtarma noktası seçin ve geri yükleme noktası yordamını gerçekleştirmek için **Tamam** ' ı seçin.

    ![Tam kurtarma noktası seçin](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

1. **Gelişmiş yapılandırma** menüsünde, geri yüklemeden sonra veritabanını işlemsel halde tutmak ISTIYORSANıZ, **norecovery ile geri yüklemeyi**etkinleştirin.
1. Hedef sunucudaki geri yükleme konumunu değiştirmek istiyorsanız, yeni bir hedef yolu girin.
1. **Tamam**’ı seçin.

    ![Gelişmiş yapılandırma menüsü](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. **Geri yükleme menüsünde geri** **Yükle ' yi seçerek restore** işini başlatın.
1. Geri yükleme ilerlemesini **Bildirimler** alanında izleyin veya veritabanı menüsünde **işleri geri yükle** ' yi seçerek izleyin.

    ![İş ilerlemesini geri yükleme](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>Çok sayıda dosya içeren veritabanlarını geri yükleme

Bir veritabanındaki dosyaların toplam dize boyutu [belirli bir sınırdan](backup-sql-server-azure-troubleshoot.md#size-limit-for-files)fazlaysa, Azure Backup veritabanı dosyalarının listesini, geri yükleme işlemi sırasında hedef geri yükleme yolunu ayarlayamayacak şekilde farklı bir pit bileşeninde depolar. Bu dosyalar yerine SQL varsayılan yoluna geri yüklenir.

  ![Veritabanını büyük dosyayla geri yükleme](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="next-steps"></a>Sonraki adımlar

[Yönetme ve izleme](manage-monitor-sql-database-backup.md) Azure Backup tarafından yedeklenen veritabanlarını SQL Server.
