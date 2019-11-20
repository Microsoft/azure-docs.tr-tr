---
title: Azure Backup Sunucusu kullanarak SQL Server yedekleme
description: Bu makalede, Microsoft Azure Backup sunucusu (MABS) kullanarak SQL Server veritabanlarının yedeklenmesi için yapılandırma adımlarını öğrenin.
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: d5e0c49e2cad77e42960fafec9311b31d4cf2601
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172673"
---
# <a name="back-up-sql-server-to-azure-with-azure-backup-server"></a>Azure Backup Sunucusu Ile Azure 'a yedekleme SQL Server

Bu makale, Microsoft Azure Backup sunucusu (MABS) kullanarak SQL Server veritabanlarının yedeklenmesi için yapılandırma adımlarında size yol gösterir.

Azure 'da SQL Server veritabanı yedeklemenin yönetimi ve Azure 'da kurtarma işlemleri üç adımdan oluşur:

1. SQL Server veritabanlarını Azure 'da korumak için bir yedekleme ilkesi oluşturun.
2. Azure 'da isteğe bağlı yedekleme kopyaları oluşturun.
3. Veritabanını Azure 'dan kurtarın.

## <a name="before-you-start"></a>Başlamadan önce

Başlamadan önce [Azure Backup sunucusu yüklediğinizden ve hazırlandığınızdan](backup-azure-microsoft-azure-backup.md)emin olun.

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>SQL Server veritabanlarını Azure 'da korumak için bir yedekleme ilkesi oluşturma

1. Azure Backup Sunucusu Kullanıcı arabiriminde, **koruma** çalışma alanına tıklayın.
2. Araç şeridinde **Yeni ' ye tıklayarak yeni bir** koruma grubu oluşturun.

    ![Koruma grubu oluştur](./media/backup-azure-backup-sql/protection-group.png)
3. MABS, bir **koruma grubu**oluşturma konusunda rehberlik ile başlangıç ekranını gösterir. **İleri**’ye tıklayın.
4. **Sunucular**' ı seçin.

    ![Koruma grubu türünü seçin-' sunucular '](./media/backup-azure-backup-sql/pg-servers.png)
5. Yedeklenecek veritabanlarının bulunduğu SQL Server makinesini genişletin. MABS 'ler, bu sunucudan yedeklenebilir çeşitli veri kaynaklarını gösterir. **Tüm SQL paylaşımlarını** genişletin ve veritabanlarını seçin (Bu durumda REPORTSERVER $ MSDPM2012 ve ReportServer $ MSDPM2012TempDB ' yi seçtik). **İleri**’ye tıklayın.

    ![SQL DB 'yi seçin](./media/backup-azure-backup-sql/pg-databases.png)
6. Koruma grubu için bir ad girin ve **çevrimiçi koruma** istiyorum onay kutusunu seçin.

    ![Veri koruma yöntemi-kısa süreli disk çevrimiçi & Azure](./media/backup-azure-backup-sql/pg-name.png)
7. **Kısa dönem hedeflerini belirtin** ekranında, diske yedekleme noktaları oluşturmak için gerekli girişleri ekleyin.

    Burada, **bekletme aralığının** *5 gün*olarak ayarlandığını, **eşitleme sıklığının** her *15 dakikada*bir olarak ayarlandığını ve yedeklemenin ne sıklıkta yapılacağını görüyoruz. **Hızlı tam yedekleme** *8:00 P. M*olarak ayarlanır.

    ![Kısa vadeli hedefler](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > 8:00 PM 'de (ekran girişine göre), önceki günün 8:00 PM Yedekleme noktasından değiştirilen veriler aktarılarak her gün bir yedekleme noktası oluşturulur. Bu işlem **Hızlı tam yedekleme**olarak adlandırılır. İşlem günlükleri 15 dakikada bir eşitlenirken, veritabanını 9:00 PM 'de kurtarmak için bir gereksinim varsa, bu nokta, günlükleri son hızlı tam yedekleme noktasından (Bu örnekte 8pm) yeniden kaydederek oluşturulur.
   >
   >

8. **İleri**’ye tıklayın

    MABS, kullanılabilir genel depolama alanını ve olası disk alanı kullanımını gösterir.

    ![Disk ayırma](./media/backup-azure-backup-sql/pg-storage.png)

    Varsayılan olarak, MABS, ilk yedekleme kopyası için kullanılan veri kaynağı başına bir birim (SQL Server veritabanı) oluşturur. Bu yaklaşımı kullanarak, mantıksal disk Yöneticisi (LDM) MABS korumasını 300 veri kaynağına (SQL Server veritabanları) sınırlandırır. Bu kısıtlamayı geçici olarak çözmek için **DPM depolama havuzu 'ndaki verileri birlikte bulundur**seçeneğini belirleyin. Bu seçeneği kullanırsanız, MABS birden fazla veri kaynağı için tek bir birim kullanır ve bu da MABS 'nin 2000 SQL veritabanını korumasına olanak tanır.

    **Birimleri otomatik olarak Büyüt** seçeneği işaretliyse, mabs, üretim verileri büyüdükçe artan yedekleme birimi için hesap oluşturabilir. **Birimleri otomatik olarak Büyüt** seçeneği seçili DEĞILSE, mabs, koruma grubundaki veri kaynakları için kullanılan yedekleme depolama alanını sınırlandırır.
9. Yöneticiler, bant genişliği yığmasını veya ağ üzerinden kaçınmak için bu ilk yedeklemenin el ile (ağ dışı) aktarılmasını tercih eder. Bunlar, ilk aktarımın gerçekleşebileceği süreyi de yapılandırabilir. **İleri**’ye tıklayın.

    ![İlk çoğaltma yöntemi](./media/backup-azure-backup-sql/pg-manual.png)

    İlk yedekleme kopyası, tüm veri kaynağının (SQL Server veritabanı) üretim sunucusundan (SQL Server makine) MABS 'e aktarılmasını gerektirir. Bu veriler büyük olabilir ve verilerin ağ üzerinden aktarılması bant genişliğini aşabilir. Bu nedenle, Yöneticiler ilk yedeklemeyi aktarmayı seçebilir: bant genişliği tıkanıklığını önlemek için **el ile** (çıkarılabilir medya kullanarak) veya **ağ üzerinden otomatik olarak** (belirli bir zamanda).

    İlk yedekleme tamamlandıktan sonra, yedeklemelerin geri kalanı ilk yedekleme kopyasında artımlı yedeklemelerdir. Artımlı yedeklemeler küçük olma eğilimindedir ve ağ üzerinden kolayca aktarılır.
10. Tutarlılık denetiminin ne zaman çalıştırılmasını istediğinizi seçin ve **İleri**' ye tıklayın.

    ![Tutarlılık denetimi](./media/backup-azure-backup-sql/pg-consistent.png)

    MABS, yedekleme noktasının bütünlüğünü denetlemek için bir tutarlılık denetimi gerçekleştirebilir. Üretim sunucusundaki (Bu senaryodaki SQL Server makine) yedekleme dosyasının sağlama toplamını ve bu dosya için MABS üzerinde yedeklenmiş verileri hesaplar. Çakışma durumunda, MABS 'teki yedeklenen dosyanın bozuk olduğu varsayılır. MABS, sağlama toplamı uyuşmazlığına karşılık gelen blokları göndererek yedeklenen verileri yeniden toplayın. Tutarlılık denetimi performansı yoğun bir işlem olduğu için, Yöneticiler tutarlılık denetimini zamanlama veya otomatik olarak çalıştırma seçeneğine sahiptir.
11. Veri kaynaklarının çevrimiçi korumasını belirtmek için, Azure 'da korunacak veritabanlarını seçin ve **İleri**' ye tıklayın.

    ![Veri kaynaklarını seçin](./media/backup-azure-backup-sql/pg-sqldatabases.png)
12. Yöneticiler, kuruluş ilkelerine uygun olan yedekleme zamanlamalarını ve bekletme ilkelerini seçebilir.

    ![Zamanlama ve bekletme](./media/backup-azure-backup-sql/pg-schedule.png)

    Bu örnekte, yedeklemeler günde 12:00 PM ve 8 PM (ekranın alt kısmı) tarihinde bir kez alınır

    > [!NOTE]
    > Hızlı kurtarma için diskte birkaç kısa süreli kurtarma noktasına sahip olmak iyi bir uygulamadır. Bu kurtarma noktaları "işletimsel kurtarma" için kullanılır. Azure, daha yüksek SLA 'Lar ve garantili kullanılabilirliğiyle iyi bir site dışı konum işlevi görür.
    >
    >

    **En Iyi Yöntem**: DPM kullanarak yerel disk yedeklemelerinin tamamlanmasından sonra Azure yedeklerinin zamanlandığından emin olun. Bu, en son disk yedeklemesinin Azure 'a kopyalanmasını sağlar.

13. Bekletme ilkesi zamanlaması ' nı seçin. Bekletme ilkesinin nasıl çalıştığı hakkındaki ayrıntılar, [bant altyapısı makalenizi değiştirmek için Azure Backup kullanılıyor](backup-azure-backup-cloud-as-tape.md)olarak sunulmaktadır.

    ![Bekletme İlkesi](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Bu örnekte:

    * Yedeklemeler günde bir kez 12:00 PM ve 8 PM (ekranın alt kısmı) ile gerçekleştirilir ve 180 gün boyunca korunur.
    * 12:00 P.M. ' de Cumartesi Cumartesi günü 104 hafta boyunca korunur
    * Son Cumartesi günü 12:00 P.M. ' de yedekleme 60 ay boyunca tutulur
    * Mart 'ın son Cumartesi günü 12:00 P.M. 10 yıl boyunca korunur
14. **İleri** ' ye tıklayın ve ilk yedekleme kopyasının Azure 'a aktarılması için uygun seçeneği belirleyin. Ağ veya **çevrimdışı yedekleme** **üzerinde otomatik olarak** seçim yapabilirsiniz.

    * **Ağ üzerinden otomatik olarak,** yedekleme için seçilen zamanlamaya göre yedekleme verilerini Azure 'a aktarır.
    * **Çevrimdışı yedekleme** 'nin çalışma [iş akışında Azure Backup bölümünde](backup-azure-backup-import-export.md)açıklanacaktır.

    İlk yedekleme kopyasını Azure 'a göndermek için ilgili Aktarım mekanizmasını seçin ve **İleri**' ye tıklayın.
15. **Özet** ekranında ilke ayrıntılarını gözden geçirdikten sonra, iş akışını gerçekleştirmek Için **Grup Oluştur** düğmesine tıklayın. **Kapat** düğmesine tıklayıp izleme çalışma alanında işin ilerlemesini izleyebilirsiniz.

    ![Koruma grubu oluşturma devam ediyor](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>SQL Server veritabanının isteğe bağlı yedeklemesi

Önceki adımlar bir yedekleme ilkesi oluştururken, yalnızca ilk yedekleme gerçekleştiğinde bir "kurtarma noktası" oluşturulur. Aşağıdaki adımlar, Scheduler 'ın başlamasını beklemek yerine, bir kurtarma noktasının el ile oluşturulmasını tetikler.

1. Kurtarma noktasını oluşturmadan önce koruma grubu durumunun veritabanı için **Tamam 'ı** gösterip gösterene kadar bekleyin.

    ![Koruma grubu üyeleri](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Veritabanına sağ tıklayın ve **Kurtarma noktası oluştur**' u seçin.

    ![Çevrimiçi kurtarma noktası oluştur](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Açılan menüden **çevrimiçi koruma** ' yı seçin ve **Tamam**' ı tıklatın. Bu, Azure 'da bir kurtarma noktası oluşturmayı başlatır.

    ![Kurtarma noktası oluştur](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. İş ilerlemesini, sonraki şekilde gösterildiği gibi, devam eden bir işin bulunduğu **izleme** çalışma alanında görüntüleyebilirsiniz.

    ![İzleme konsolu](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Azure 'dan SQL Server veritabanını kurtarma

Korunan bir varlığı (SQL Server veritabanı) Azure 'dan kurtarmak için aşağıdaki adımlar gereklidir.

1. DPM sunucusu yönetim konsolunu açın. DPM tarafından yedeklenen sunucuları görebileceğiniz **Kurtarma** çalışma alanı ' na gidin. Gerekli veritabanına (Bu durumda ReportServer $ MSDPM2012) gözatamazsınız. **Çevrimiçi**ile biten zamandan **Kurtarma** seçin.

    ![Kurtarma noktası seçin](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Veritabanı adına sağ tıklayın ve **kurtar**' a tıklayın.

    ![Azure 'dan kurtarma](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. DPM, kurtarma noktasının ayrıntılarını gösterir. **İleri**’ye tıklayın. Veritabanının üzerine yazmak için, **SQL Server özgün örneğine kurtar**kurtarma türünü seçin. **İleri**’ye tıklayın.

    ![Özgün konuma kurtar](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Bu örnekte DPM, veritabanının başka bir SQL Server örneğine veya tek başına bir ağ klasörüne kurtarılmasını sağlar.
4. **Kurtarma seçeneklerini belirtin** ekranında, kurtarma tarafından kullanılan bant genişliğini azaltmak için ağ bant genişliği kullanımı azaltma gibi kurtarma seçeneklerini belirleyebilirsiniz. **İleri**’ye tıklayın.
5. **Özet** ekranında, şu ana kadar sunulan tüm kurtarma yapılandırmasını görürsünüz. **Kurtar**' ı tıklatın.

    Kurtarma durumu kurtarılan veritabanını gösterir. **Kapat** ' a tıklayarak Sihirbazı kapatabilir ve ilerlemeyi **izleme** çalışma alanında görüntüleyebilirsiniz.

    ![Kurtarma işlemini Başlat](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Kurtarma işlemi tamamlandıktan sonra, geri yüklenen veritabanı uygulamayla tutarlı olur.

### <a name="next-steps"></a>Sonraki adımlar

* [Azure Backup SSS](backup-azure-backup-faq.md)
