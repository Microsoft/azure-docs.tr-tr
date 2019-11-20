---
title: DPM ile bir SharePoint grubunu Azure 'a yedekleme
description: Bu makalede, Azure 'da bir SharePoint grubunun DPM/Azure Backup sunucu korumasına genel bakış sunulmaktadır
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 1750270b3383e815b9255273923b50d2879fdba6
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173331"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>DPM ile bir SharePoint grubunu Azure 'a yedekleme

System Center Data Protection Manager (DPM) kullanarak Microsoft Azure için bir SharePoint grubunu diğer veri kaynaklarını yedeklemekten çok benzer şekilde yedekleyerek. Azure Backup günlük, haftalık, aylık veya yıllık yedekleme noktaları oluşturmak için yedekleme zamanlamasında esneklik sağlar ve çeşitli yedekleme noktaları için bekletme ilkesi seçenekleri sunar. DPM, hızlı kurtarma zamanı hedeflerine (RTO) yönelik yerel disk kopyalarını depolamanıza ve ekonomik, uzun süreli saklama için Azure 'a kopya depolamanıza olanak sağlar.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint desteklenen sürümleri ve ilgili koruma senaryoları

DPM için Azure Backup aşağıdaki senaryoları destekler:

| İş yükü | Sürüm | SharePoint dağıtımı | DPM Dağıtım türü | DPM-System Center 2012 R2 | Koruma ve kurtarma |
| --- | --- | --- | --- | --- | --- |
| SharePoint |SharePoint 2013, SharePoint 2010, SharePoint 2007, SharePoint 3,0 |Fiziksel sunucu veya Hyper-V/VMware sanal makinesi olarak dağıtılan SharePoint <br> -------------- <br> SQL AlwaysOn |Fiziksel sunucu veya şirket içi Hyper-V sanal makinesi |Güncelleştirme paketi 5 ' ten Azure 'a yedeklemeyi destekler |SharePoint grubu kurtarma seçeneklerini koruyun: kurtarma grubu, veritabanı ve disk kurtarma noktalarından dosya veya liste öğesi.  Azure kurtarma noktalarından grup ve veritabanı kurtarma. |

## <a name="before-you-start"></a>Başlamadan önce

Bir SharePoint grubunu Azure 'a yedeklemebilmeniz için öncelikle onaylamanız gereken birkaç nokta vardır.

### <a name="prerequisites"></a>Önkoşullar

Devam etmeden önce, iş yüklerini korumak için [Microsoft Azure Backup kullanmaya yönelik tüm önkoşulları](backup-azure-dpm-introduction.md#prerequisites-and-limitations) karşıladığınızdan emin olun. Önkoşullar için bazı görevler şunlardır: bir yedekleme Kasası oluşturma, kasa kimlik bilgilerini indirme, Azure Backup Aracısı yükleme ve DPM/Azure Backup Sunucusu 'i kasaya kaydetme.

### <a name="dpm-agent"></a>DPM Aracısı

DPM aracısının, SharePoint çalıştıran sunucuda, SQL Server çalıştıran sunucular ve SharePoint grubunun parçası olan diğer tüm sunucular üzerinde yüklü olması gerekir. Koruma aracısının nasıl ayarlanacağı hakkında daha fazla bilgi için bkz. [Kurulum koruma Aracısı](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx).  Tek istisna, aracıyı yalnızca tek bir Web ön ucu (WFE) sunucusuna yüklemektir. DPM 'nin bir WFE sunucusunda aracısına yalnızca koruma için giriş noktası olarak sahip olması gerekir.

### <a name="sharepoint-farm"></a>SharePoint grubu

Gruptaki her 10.000.000 öğe için DPM klasörünün bulunduğu birimde en az 2 GB alan olması gerekir. Bu alan Katalog oluşturma için gereklidir. DPM 'nin belirli öğeleri (site koleksiyonları, siteler, listeler, belge kitaplıkları, klasörler, tek belgeler ve liste öğeleri) kurtarması için, Katalog oluşturma her bir içerik veritabanının içindeki URL 'lerin bir listesini oluşturur. URL 'lerin listesini, DPM Yönetici Konsolu **Kurtarma** görev alanındaki kurtarılabilir öğe bölmesinde görüntüleyebilirsiniz.

### <a name="sql-server"></a>SQL Server

DPM, LocalSystem hesabı olarak çalışır. SQL Server veritabanlarını yedeklemek için DPM, SQL Server çalıştıran sunucu için bu hesapta sysadmin ayrıcalıklarına sahip olmalıdır. Yedekleme yapmadan önce SQL Server çalıştıran sunucuda NT AUTHORITY\SYSTEM ' i *sysadmin* olarak ayarlayın.

SharePoint grubu SQL Server diğer adlarla yapılandırılmış SQL Server veritabanlarına sahipse, SQL Server istemci bileşenlerini DPM 'nin koruduğu ön uç Web sunucusuna yükler.

### <a name="sharepoint-server"></a>SharePoint Server

Performans, tek bir DPM sunucusunun 25 TB 'lık bir SharePoint grubunu koruyabildiği genel yönergeler olarak, SharePoint grubunun boyutu gibi birçok etkene bağlıdır.

### <a name="dpm-update-rollup-5"></a>DPM güncelleştirme paketi 5

Bir SharePoint grubunu Azure 'da korumaya başlamak için DPM güncelleştirme paketi 5 veya sonraki bir sürümünü yüklemeniz gerekir. Güncelleştirme paketi 5, Grup SQL AlwaysOn kullanılarak yapılandırıldıysa bir SharePoint grubunu Azure 'a koruma özelliği sağlar.
Daha fazla bilgi için [DPM güncelleştirme paketi 5](https://blogs.technet.com/b/dpm/archive/2015/02/11/update-rollup-5-for-system-center-2012-r2-data-protection-manager-is-now-available.aspx) ' i tanıtan blog gönderisine bakın.

### <a name="whats-not-supported"></a>Desteklenmeyen durumlar

* SharePoint grubunu koruyan DPM, arama dizinlerini veya uygulama hizmeti veritabanlarını korumaz. Bu veritabanlarının korumasını ayrı olarak yapılandırmanız gerekecektir.
* DPM, genişleme dosya sunucusu (SOFS) paylaşımlarında barındırılan SharePoint SQL Server veritabanlarının yedeklemesini sağlamaz.

## <a name="configure-sharepoint-protection"></a>SharePoint korumasını yapılandırma

SharePoint 'i korumak için DPM 'yi kullanabilmeniz için, **ConfigureSharePoint. exe**' yi kullanarak SharePoint VSS Yazıcı HIZMETINI (WSS Yazıcı hizmeti) yapılandırmanız gerekir.

**ConfigureSharePoint. exe** ' yi ön uç Web SUNUCUSUNDAKI [DPM yükleme yolu] \Bin klasöründe bulabilirsiniz. Bu araç, koruma aracısına SharePoint grubu için kimlik bilgilerini sağlar. Tek bir WFE sunucusunda çalıştırırsınız. Birden çok WFE sunucunuz varsa, bir koruma grubu yapılandırdığınızda yalnızca bir tane seçin.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>SharePoint VSS Yazıcı hizmetini yapılandırmak için

1. WFE sunucusunda, bir komut isteminde [DPM yükleme konumu] \Bin\ adresine gidin.
2. ConfigureSharePoint-EnableSharePointProtection girin.
3. Grup Yöneticisi kimlik bilgilerini girin. Bu hesap, WFE sunucusunda yerel yönetici grubunun bir üyesi olmalıdır. Grup Yöneticisi yerel bir yönetici değilse WFE sunucusunda aşağıdaki izinleri verin:
   * DPM klasörüne (% Program Files%\Microsoft Data Protection Manager\DPM) WSS_Admin_WPG grubuna Tam Denetim izni verin.
   * DPM kayıt defteri anahtarına WSS_Admin_WPG grubuna Okuma erişimi verin (HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> SharePoint grubu yönetici kimlik bilgilerinde bir değişiklik olduğunda ConfigureSharePoint. exe ' yi yeniden çalıştırmanız gerekir.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-dpm"></a>DPM kullanarak bir SharePoint grubunu yedekleme

Daha önce açıklandığı gibi DPM 'yi ve SharePoint grubunu yapılandırdıktan sonra, SharePoint DPM tarafından korunabilir.

### <a name="to-protect-a-sharepoint-farm"></a>SharePoint grubunu korumak için

1. DPM Yönetici Konsolu **koruma** sekmesinde **Yeni**' ye tıklayın.
    Yeni Koruma sekmesini ![](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. **Yeni koruma grubu oluşturma** Sihirbazı ' nın **koruma grubu türünü seçin** sayfasında **sunucular**' ı seçin ve ardından **İleri**' ye tıklayın.

    ![Koruma grubu türünü seçin](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. **Grup üyelerini seçin** ekranında, korumak istediğiniz SharePoint sunucusunun onay kutusunu seçin ve **İleri**' ye tıklayın.

    ![Grup üyelerini seçin](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > DPM Aracısı yüklü olduğunda, sihirbazda sunucuyu görebilirsiniz. DPM, yapısını da gösterir. ConfigureSharePoint. exe ' yi çalıştırdığınız için DPM, SharePoint VSS Yazıcı hizmeti ve buna karşılık gelen SQL Server veritabanları ile iletişim kurar ve SharePoint grup yapısını, ilişkili içerik veritabanlarını ve ilgili tüm öğeleri tanır.
   >
   >
4. **Veri koruma yöntemini seçin** sayfasında, **koruma grubunun**adını girin ve tercih ettiğiniz *koruma yöntemlerinizi*seçin. **İleri**’ye tıklayın.

    ![Veri koruma yöntemini seçin](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > Disk koruma yöntemi, kısa kurtarma süresi hedeflerini karşılamasına yardımcı olur. Azure, bantlarla karşılaştırıldığında ekonomik ve uzun süreli bir koruma hedefidir. Daha fazla bilgi için bkz. [bant altyapınızı değiştirmek için Azure Backup kullanma](https://azure.microsoft.com/documentation/articles/backup-azure-backup-cloud-as-tape/)
   >
   >
5. **Kısa dönem hedeflerini belirtin** sayfasında, tercih ettiğiniz **bekletme aralığını** seçin ve yedeklemelerin ne zaman gerçekleşmesini istediğinizi belirleyin.

    ![Kısa vadeli hedefleri belirtin](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Kurtarma en sık beş günden eski olan veriler için gerektiğinden, diskte beş günlük bir bekletme aralığı seçtik ve bu örnek için yedeklemenin üretim dışı saatlerde gerçekleştiğinden emin olun.
   >
   >
6. Koruma grubu için ayrılmış depolama havuzu disk alanını gözden geçirin ve ardından **İleri**' ye tıklayın.
7. Her koruma grubu için DPM, çoğaltmaları depolamak ve yönetmek üzere disk alanı ayırır. Bu noktada DPM seçili verilerin bir kopyasını oluşturmalıdır. Çoğaltmanın nasıl ve ne zaman oluşturulacağını seçin ve ardından **İleri**' ye tıklayın.

    ![Çoğaltma oluşturma yöntemini seçin](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Ağ trafiğinin etkili olmadığından emin olmak için, üretim saatleri dışında bir zaman seçin.
   >
   >
8. DPM, çoğaltmada tutarlılık denetimleri gerçekleştirerek veri bütünlüğünü sağlar. Kullanılabilecek iki seçenek vardır. Tutarlılık denetimleri çalıştırmak için bir zamanlama tanımlayabilir veya DPM, tutarsız hale geldiğinde çoğaltma denetimlerini otomatik olarak çoğaltma üzerinde çalıştırabilir. Tercih ettiğiniz seçeneği belirleyin ve ardından **İleri**' ye tıklayın.

    ![Tutarlılık denetimi](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. **Çevrimiçi koruma verilerini belirtin** sayfasında, korumak istediğiniz SharePoint grubunu seçin ve ardından **İleri**' ye tıklayın.

    ![DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. **Çevrimiçi yedekleme zamanlamasını belirtin** sayfasında, tercih ettiğiniz zamanlamayı seçin ve ardından **İleri**' ye tıklayın.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > DPM, Azure 'a farklı zamanlarda en fazla iki günlük yedekleme sağlar. Azure Backup Ayrıca, [ağ azaltma Azure Backup](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling)kullanarak en yoğun ve yoğun saatlerde yedeklemeler IÇIN kullanılabilen WAN bant genişliği miktarını denetleyebilir.
    >
    >
11. Seçtiğiniz yedekleme zamanlamalarına bağlı olarak, **çevrimiçi saklama Ilkesini belirtin** sayfasında günlük, haftalık, aylık ve yıllık yedekleme noktaları için bekletme ilkesini seçin.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > DPM, farklı yedekleme noktaları için farklı bir bekletme ilkesi seçilebilir olan, bir babalar ve son bekletme şeması kullanır.
    >
    >
12. Diske benzer şekilde, Azure 'da bir başlangıç başvuru noktası çoğaltmasının oluşturulması gerekir. Azure 'da ilk yedekleme kopyası oluşturmak için tercih ettiğiniz seçeneği belirleyin ve ardından **İleri**' ye tıklayın.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. **Özet** sayfasında seçtiğiniz ayarları gözden geçirin ve ardından **Grup Oluştur**' a tıklayın. Koruma grubu oluşturulduktan sonra bir başarı iletisi görürsünüz.

    ![Özet](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-dpm"></a>DPM kullanarak bir SharePoint öğesini diskten geri yükleme

Aşağıdaki örnekte, *Kurtarma SharePoint öğesi* yanlışlıkla silinmiş ve kurtarılması gerekiyor.
![DPM SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. **DPM Yönetici Konsolu**açın. DPM tarafından korunan tüm SharePoint grupları, **koruma** sekmesinde gösterilir.

    ![DPM SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Öğeyi kurtarmaya başlamak için **Kurtarma** sekmesini seçin.

    ![DPM SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Bir kurtarma noktası aralığı içinde joker karakter tabanlı bir arama kullanarak *SharePoint öğesini kurtarmak* için SharePoint 'te arama yapabilirsiniz.

    ![DPM SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Arama sonuçlarından uygun kurtarma noktasını seçin, öğeye sağ tıklayın ve ardından **kurtar**' ı seçin.
5. Ayrıca, çeşitli kurtarma noktalarına göz atabilir ve kurtarılacak bir veritabanı veya öğe seçebilirsiniz. **Tarih > kurtarma zamanı**' nı seçin ve ardından **> SharePoint grubu > kurtarma noktası > öğesi doğru veritabanını**seçin.

    ![DPM SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Öğeye sağ tıklayın ve ardından **Kurtarma Sihirbazı**'nı açmak için **kurtar** ' ı seçin. **İleri**’ye tıklayın.

    ![Kurtarma seçimini İncele](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Gerçekleştirmek istediğiniz kurtarma türünü seçin ve ardından **İleri**' ye tıklayın.

    ![Kurtarma türü](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > Örnekteki **orijinale kurtar** seçimi, öğeyi özgün SharePoint sitesine kurtarır.
   >
   >
8. Kullanmak istediğiniz **kurtarma işlemini** seçin.

   * SharePoint grubu değiştirilmediğinde ve geri yüklenmekte olan kurtarma noktasıyla aynı ise, **Kurtarma grubu kullanmadan kurtar** ' ı seçin.
   * Kurtarma noktası oluşturulduktan sonra SharePoint grubu değiştiyse **Kurtarma grubu kullanarak kurtar** ' ı seçin.

     ![Kurtarma Işlemi](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Veritabanını geçici olarak kurtarmak için bir hazırlama SQL Server örnek konumu sağlayın ve DPM sunucusunda ve SharePoint çalıştıran sunucuda öğeyi kurtarmak için bir hazırlama dosyası paylaşımının sağlanması gerekir.

    ![Hazırlama location1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    DPM, SharePoint öğesini barındıran içerik veritabanını geçici SQL Server örneğine iliştirir. İçerik veritabanından DPM sunucusu öğeyi kurtarır ve DPM sunucusundaki hazırlama dosyası konumuna koyar. DPM sunucusunun hazırlama konumunda bulunan kurtarılan öğenin artık SharePoint grubundaki hazırlama konumuna aktarılması gerekir.

    ![Hazırlama Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. **Kurtarma seçeneklerini belirtin**' i seçin ve güvenlik ayarlarını SharePoint grubuna uygulayın veya kurtarma noktasının güvenlik ayarlarını uygulayın. **İleri**’ye tıklayın.

    ![Kurtarma seçenekleri](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Ağ bant genişliği kullanımını azaltmayı seçebilirsiniz. Bu, üretim saatleri sırasında üretim sunucusuna etkisini en aza indirir.
    >
    >
11. Özet bilgilerini gözden geçirin ve ardından dosyayı kurtarmaya başlamak için **kurtar** ' ı tıklatın.

    ![Kurtarma Özeti](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Şimdi kurtarmanın **durumunu** görüntülemek Için **DPM Yönetici Konsolu** **izleme** sekmesini seçin.

    ![Kurtarma durumu](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Dosya şimdi geri yüklendi. Geri yüklenen dosyayı denetlemek için SharePoint sitesini yenileyebilirsiniz.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>DPM kullanarak bir SharePoint veritabanını Azure 'dan geri yükleme

1. Bir SharePoint içerik veritabanını kurtarmak için, çeşitli kurtarma noktalarına göz atarak (daha önce gösterildiği gibi) ve geri yüklemek istediğiniz kurtarma noktasını seçin.

    ![DPM SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. SharePoint kurtarma noktasına çift tıklayarak kullanılabilir SharePoint kataloğu bilgilerini görüntüleyin.

   > [!NOTE]
   > SharePoint grubu Azure 'da uzun süreli saklama için korunduğundan, DPM sunucusunda hiçbir katalog bilgisi (meta veri) yok. Sonuç olarak, her zaman bir noktadan SharePoint içerik veritabanının kurtarılması gerektiğinde SharePoint grubunu yeniden kataloglanmalıdır.
   >
   >
3. **Yeniden katalog**' a tıklayın.

    ![DPM SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    **Bulut yeniden Kataloglandırma** durumu penceresi açılır.

    ![DPM SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Kataloglama tamamlandıktan sonra durum *başarılı*olarak değişir. **Kapat**’a tıklayın.

    ![DPM SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. İçerik veritabanı yapısını almak için DPM **Kurtarma** sekmesinde gösterilen SharePoint nesnesine tıklayın. Öğeye sağ tıklayın ve ardından **kurtar**' a tıklayın.

    ![DPM SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Bu noktada, bir SharePoint içerik veritabanını diskten kurtarmak için bu makalenin önceki kısımlarında yer alarak kurtarma adımlarını izleyin.

## <a name="next-steps"></a>Sonraki adımlar

* SharePoint 'in DPM koruması hakkında daha fazla bilgi edinin-bkz. [video serisi-SharePoint 'ın DPM koruması](https://channel9.msdn.com/Series/Azure-Backup/Microsoft-SCDPM-Protection-of-SharePoint-1-of-2-How-to-create-a-SharePoint-Protection-Group)
* [System Center 2012-Data Protection Manager Için sürüm notlarını](https://technet.microsoft.com/library/jj860415.aspx) gözden geçirin
* [System Center 2012 SP1 'de Data Protection Manager Için sürüm notlarını](https://technet.microsoft.com/library/jj860394.aspx) gözden geçirin
