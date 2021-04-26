---
title: Azure VM 'de SQL Server veritabanlarını geri yükleme
description: Bu makalede, bir Azure VM üzerinde çalışan ve Azure Backup yedeklenen SQL Server veritabanlarının nasıl geri yükleneceği açıklanmaktadır. Veritabanlarınızı ikincil bir bölgeye geri yüklemek için çapraz bölge geri yükleme özelliğini de kullanabilirsiniz.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 7dd8d8d54fa7d33bb4a0935357597d19dd2368c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97734411"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Azure VM’lerinde SQL Server veritabanlarını geri yükleme

Bu makalede, [Azure Backup](backup-overview.md) hizmetinin bir Azure Backup kurtarma hizmetleri kasasına yedeklemiş olduğu bir Azure sanal MAKINESINDE (VM) çalışan SQL Server bir veritabanının nasıl geri yükleneceği açıklanmaktadır.

Bu makalede SQL Server veritabanlarının nasıl geri yükleneceği açıklanmaktadır. Daha fazla bilgi için bkz. [Azure VM 'lerde SQL Server veritabanlarını yedekleme](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Bir saate veya kurtarma noktasına geri yükleme

Azure Backup, Azure VM 'lerinde çalışan SQL Server veritabanlarını şu şekilde geri yükleyebilir:

- İşlem günlüğü yedeklerini kullanarak belirli bir tarih veya saate (ikinci olarak) geri yükleyin. Azure Backup, uygun tam değişiklik yedeklemesini ve seçilen saate göre geri yüklemek için gereken günlük yedeklemeleri zincirini otomatik olarak belirler.
- Belirli bir kurtarma noktasına geri yüklemek için belirli bir tam veya değişiklik yedeklemesini geri yükleyin.

## <a name="restore-prerequisites"></a>Önkoşulları geri yükle

Bir veritabanını geri yüklemeden önce aşağıdakileri göz önünde bulabilirsiniz:

- Veritabanını aynı Azure bölgesindeki bir SQL Server örneğine geri yükleyebilirsiniz.
- Hedef sunucu, kaynakla aynı kasada kayıtlı olmalıdır.
- Sunucuda çalışan birden fazla örnek varsa, tüm örneklerin çalışıyor olması gerekir. Aksi takdirde sunucu, hedef sunucular listesinde görünmez ve veritabanını ' ye geri yüklemeniz gerekir. Daha fazla bilgi için [sorun giderme adımlarına](backup-sql-server-azure-troubleshoot.md#faulty-instance-in-a-vm-with-multiple-sql-server-instances)bakın.
- Bir TDE şifreli veritabanını başka bir SQL Server geri yüklemek için öncelikle [sertifikayı hedef sunucuya geri yüklemeniz](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server)gerekir.
- [CDC](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server) etkin veritabanlarının [dosya olarak geri yükle](#restore-as-files) seçeneği kullanılarak geri yüklenmesi gerekir.
- "Ana" veritabanını geri yüklemeden önce, **-ı AzureWorkloadBackup** başlangıç seçeneğini kullanarak SQL Server örneğini tek kullanıcılı modda başlatın.
  - **-D** değeri, istemcinin adıdır.
  - Bağlantıyı yalnızca belirtilen istemci adı açabilir.
- Tüm sistem veritabanları için (model, ana, msdb) geri yüklemeyi tetikleyebilmeniz için önce SQL Server Agent hizmetini durdurun.
- Bu veritabanlarından herhangi birine bir bağlantı almaya çalışabilir olabilecek tüm uygulamaları kapatın.

## <a name="restore-a-database"></a>Veritabanını geri yükleme

Geri yüklemek için aşağıdaki izinlere sahip olmanız gerekir:

- Geri yüklemeyi yaptığınız kasada **yedekleme işletmeni** izinleri.
- Yedeklenen kaynak VM 'ye **katkıda bulunan (yazma)** erişimi.
- Hedef VM 'ye **katkıda bulunan (yazma)** erişimi:
  - Aynı VM 'ye geri yüklüyorsanız, kaynak VM 'dir.
  - Alternatif bir konuma geri yüklüyorsanız, bu yeni hedef VM 'dir.

Aşağıdaki şekilde geri yükleyin:

1. SQL Server VM kaydedildiği kasayı açın.
2. Kasa panosunda, **kullanım** altında **yedekleme öğeleri**' ni seçin.
3. **Yedekleme öğeleri**' nde, **yedekleme yönetimi türü** altında **Azure VM 'de SQL**' i seçin.

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
     ![Yapılandırma menüsünü geri yükle](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>Alternatif bir konuma geri yükleme

1. **Geri yükleme yapılandırma** menüsünde, **geri yükleme yeri**' nin altında, **alternatif konum**' u seçin.
1. Veritabanını geri yüklemek istediğiniz SQL Server adı ve örneği seçin.
1. **Geri yüklenen veritabanı adı** kutusuna hedef veritabanının adını girin.
1. Geçerliyse, **SEÇILI SQL örneğinde aynı ada sahip BIR veritabanı zaten varsa üzerine yaz**' ı seçin.
1. **Geri yükleme noktası**' nı seçin ve belirli bir [noktaya](#restore-to-a-specific-point-in-time) geri yükleme veya [belirli bir kurtarma noktasına geri yükleme](#restore-to-a-specific-restore-point)yapılıp yapılmayacağını seçin.

    ![Geri yükleme noktası seç](./media/backup-azure-sql-database/select-restore-point.png)

    ![Zaman noktasına geri yükleme](./media/backup-azure-sql-database/restore-to-point-in-time.png)

1. **Gelişmiş yapılandırma** menüsünde:

    - Geri yüklemeden sonra veritabanını işlemsel olmayan halde tutmak istiyorsanız, **norecovery Ile geri yüklemeyi** etkinleştirin.
    - Hedef sunucudaki geri yükleme konumunu değiştirmek istiyorsanız, yeni hedef yollar girin.

        ![Hedef yolları girin](./media/backup-azure-sql-database/target-paths.png)

1. Geri yüklemeyi tetiklemek için **Tamam ' ı** seçin. Geri yükleme ilerlemesini **Bildirimler** alanında izleyin veya kasadaki **yedekleme işleri** görünümü altında izleyin.

    > [!NOTE]
    > Zaman içinde geri yükleme yalnızca tam ve toplu günlük kurtarma modundaki veritabanlarına yönelik günlük yedeklemeleri için kullanılabilir.

### <a name="restore-and-overwrite"></a>Geri yükleme ve üzerine yazma

1. **Geri yükleme yapılandırma** menüsünde, **geri yükleme yeri**' nin altında, **DB Tamam üzerine yaz**' ı seçin  >  .

    ![DB üzerine yaz seçeneğini belirleyin](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. [Belirli bir noktaya geri yüklemek](#restore-to-a-specific-point-in-time)için **geri yükleme noktası seç** bölümünde Günlükler ' i **(zaman içindeki nokta)** seçin. Veya [belirli bir kurtarma noktasına](#restore-to-a-specific-restore-point)geri yüklemek için **tam & türev** öğesini seçin.

    > [!NOTE]
    > Zaman içinde geri yükleme yalnızca tam ve toplu günlük kurtarma modundaki veritabanlarına yönelik günlük yedeklemeleri için kullanılabilir.

### <a name="restore-as-files"></a>Dosya olarak geri yükleme

Yedekleme verilerini bir veritabanı yerine. bak dosyaları olarak geri yüklemek için **dosya olarak geri yükle**' yi seçin. Dosyalar belirtilen bir yola alındıktan sonra, bu dosyaları bir veritabanı olarak geri yüklemek istediğiniz herhangi bir makineye alabilirsiniz. Bu dosyaları herhangi bir makineye taşıyabileceğinizden, artık verileri abonelikler ve bölgeler arasında geri yükleyebilirsiniz.

1. **Nerede ve nasıl geri yükleyeceğiniz** altında **dosya olarak geri yükle**' yi seçin.
1. Yedekleme dosyalarını geri yüklemek istediğiniz SQL Server adını seçin.
1. **Sunucu üzerindeki hedef yolda** , adım 2 ' de seçilen sunucudaki klasör yolunu girin. Bu, hizmetin tüm gerekli yedekleme dosyalarının dökümünü alacak konumudur. Genellikle, bir ağ paylaşımının yolu veya hedef yolu olarak belirtildiğinde bağlı bir Azure dosya paylaşımının yolu, bu dosyalara aynı ağdaki diğer makineler veya bunlara bağlı aynı Azure dosya paylaşımıyla daha kolay erişim sağlar.<BR>

    >Veritabanı yedekleme dosyalarını, hedef kayıtlı VM 'ye bağlanmış bir Azure dosya paylaşımında geri yüklemek için NT AUTHORITY\SYSTEM 'ın dosya paylaşımında erişime sahip olduğundan emin olun. VM 'de takılı olan AFS 'ye okuma/yazma izinleri vermek için aşağıda verilen adımları gerçekleştirebilirsiniz:
    >
    >- `PsExec -s cmd`NT AUTHORITY\SYSTEM kabuğu 'na girmek için çalıştırın
    >   - `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>` yürütme
    >   - Erişimi doğrulama `dir \\<storageacct>.file.core.windows.net\<filesharename>`
    >- Yedekleme kasasından dosya olarak geri yüklemeyi başlatma `\\<storageacct>.file.core.windows.net\<filesharename>`<BR>
    PsExec ' i [Sysinternals](/sysinternals/downloads/psexec) sayfasından indirebilirsiniz.

1. **Tamam**’ı seçin.

    ![Dosya olarak geri yükle ' yi seçin](./media/backup-azure-sql-database/restore-as-files.png)

1. **Geri yükleme noktası**' nı seçin ve belirli bir [noktaya](#restore-to-a-specific-point-in-time) geri yükleme veya [belirli bir kurtarma noktasına geri yükleme](#restore-to-a-specific-restore-point)yapılıp yapılmayacağını seçin.

1. Seçilen kurtarma noktasıyla ilişkili tüm yedekleme dosyaları, hedef yoluna dökülür. Dosyaları, SQL Server Management Studio kullanarak mevcut oldukları herhangi bir makinede bir veritabanı olarak geri yükleyebilirsiniz.

    ![Hedef yolundaki yedekleme dosyaları geri yüklendi](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>Belirli bir zaman noktasına geri yükleme

Geri yükleme türü olarak **Günlükler (zaman içinde)** seçtiyseniz şunları yapın:

1. **Geri yükleme tarihi/saati** altında takvimi açın. Takvimde, kurtarma noktaları olan tarihler kalın türde görüntülenir ve geçerli tarih vurgulanır.
1. Kurtarma noktaları olan bir tarih seçin. Kurtarma noktası olmayan tarihleri seçemezsiniz.

    ![Takvimi açın](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. Bir tarih seçtikten sonra, zaman çizelgesi grafiğinde kullanılabilir kurtarma noktaları sürekli bir aralıkta görüntülenir.
1. Zaman çizelgesi grafiğinde kurtarma için bir saat belirtin veya bir saat seçin. Ardından **Tamam**’ı seçin.

### <a name="restore-to-a-specific-restore-point"></a>Belirli bir geri yükleme noktasına geri yükleme

Geri yükleme türü olarak **tam & türev** seçtiyseniz şunları yapın:

1. Listeden bir kurtarma noktası seçin ve geri yükleme noktası yordamını gerçekleştirmek için **Tamam** ' ı seçin.

    ![Tam kurtarma noktası seçin](./media/backup-azure-sql-database/choose-full-recovery-point.png)

    >[!NOTE]
    > Varsayılan olarak, son 30 günün kurtarma noktaları görüntülenir. **Filtre** ' i seçerek ve özel bir Aralık seçerek 30 günden daha eski kurtarma noktalarını görüntüleyebilirsiniz.

### <a name="restore-databases-with-large-number-of-files"></a>Çok sayıda dosya içeren veritabanlarını geri yükleme

Bir veritabanındaki dosyaların toplam dize boyutu [belirli bir sınırdan](backup-sql-server-azure-troubleshoot.md#size-limit-for-files)fazlaysa, Azure Backup veritabanı dosyalarının listesini farklı bir pit bileşeninde depolar, böylece geri yükleme işlemi sırasında hedef geri yükleme yolunu ayarlayamazsınız. Bu dosyalar yerine SQL varsayılan yoluna geri yüklenir.

  ![Veritabanını büyük dosyayla geri yükleme](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="cross-region-restore"></a>Çapraz bölge geri yükleme

Geri yükleme seçeneklerinden biri olan çapraz bölge geri yükleme (CRR), Azure sanal makinelerinde barındırılan SQL veritabanlarını bir Azure eşlenmiş bölgesi olan ikincil bir bölgede geri yüklemenize olanak tanır.

Önizleme sırasında özelliğe eklemek için [başlamadan önce bölümünü](./backup-create-rs-vault.md#set-cross-region-restore)okuyun.

CRR 'nin etkin olup olmadığını görmek için [çapraz bölge geri yükleme 'Yi yapılandırma](backup-create-rs-vault.md#configure-cross-region-restore) bölümündeki yönergeleri izleyin.

### <a name="view-backup-items-in-secondary-region"></a>İkincil bölgedeki yedekleme öğelerini görüntüle

CRR etkinse, yedekleme öğelerini ikincil bölgede görüntüleyebilirsiniz.

1. Portaldan **Kurtarma Hizmetleri Kasası**  >  **yedekleme öğeleri**' ne gidin.
1. İkincil bölgedeki öğeleri görüntülemek için **Ikincil bölge** ' yi seçin.

>[!NOTE]
>Yalnızca CRR özelliğini destekleyen yedekleme yönetim türleri listede gösterilir. Şu anda yalnızca ikincil bölge verilerinin bir ikincil bölgeye geri yüklenmesi için desteğe izin verilir.

![İkincil bölgedeki yedekleme öğeleri](./media/backup-azure-sql-database/backup-items-secondary-region.png)

![İkincil bölgedeki veritabanları](./media/backup-azure-sql-database/databases-secondary-region.png)

### <a name="restore-in-secondary-region"></a>İkincil bölgede geri yükleme

İkinci bölge geri yükleme kullanıcı deneyimi, birincil bölge geri yükleme kullanıcı deneyimiyle benzerdir. Geri yüklemeyi yapılandırmak için geri yükleme yapılandırma bölmesindeki Ayrıntılar yapılandırılırken yalnızca ikincil bölge parametreleri sağlamanız istenir.

![Nereye ve nasıl geri yükleneceği](./media/backup-azure-sql-database/restore-secondary-region.png)

>[!NOTE]
>İkincil bölgedeki sanal ağın benzersiz olarak atanması gerekir ve bu kaynak grubundaki diğer VM 'Ler için kullanılamaz.

![Geri yükleme tetikleme devam ediyor bildirimi](./media/backup-azure-arm-restore-vms/restorenotifications.png)

>[!NOTE]
>
>- Geri yükleme tetiklendikten ve veri aktarımı aşamasında geri yükleme işi iptal edilemez.
>- İkincil bölgeye geri yüklemek için gereken Azure rolleri, birincil bölgeyle aynı olanlardır.

### <a name="monitoring-secondary-region-restore-jobs"></a>İkincil bölge geri yükleme işlerini izleme

1. Portaldan **Kurtarma Hizmetleri Kasası**  >  **yedekleme işleri** ' ne gidin
1. İkincil bölgedeki öğeleri görüntülemek için **Ikincil bölge** ' yi seçin.

    ![Filtrelenen yedekleme işleri](./media/backup-azure-sql-database/backup-jobs-secondary-region.png)

## <a name="next-steps"></a>Sonraki adımlar

[Yönetme ve izleme](manage-monitor-sql-database-backup.md) Azure Backup tarafından yedeklenen veritabanlarını SQL Server.
