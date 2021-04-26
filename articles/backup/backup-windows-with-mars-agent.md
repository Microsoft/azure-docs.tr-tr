---
title: MARS Aracısı 'nı kullanarak Windows makinelerini yedekleme
description: Windows makinelerini yedeklemek için Microsoft Azure Kurtarma Hizmetleri (MARS) aracısını kullanın.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 54628c15ffb51c7157132c9a91f41c16873df340
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519179"
---
# <a name="back-up-windows-server-files-and-folders-to-azure"></a>Windows Server dosyalarını ve klasörlerini Azure 'a yedekleme

Bu makalede, Windows makinelerini [Azure Backup](backup-overview.md) hizmeti ve MICROSOFT Azure kurtarma HIZMETLERI (mars) Aracısı kullanılarak nasıl yedekleyeceğiniz açıklanmaktadır. MARS, Azure Backup Aracısı olarak da bilinir.

Bu makalede şunları yapmayı öğreneceksiniz:

> [!div class="checklist"]
>
> * Ön koşulları doğrulayın
> * Bir yedekleme ilkesi ve zamanlama oluşturun.
> * İsteğe bağlı yedekleme gerçekleştirin.

## <a name="before-you-start"></a>Başlamadan önce

* [Azure Backup Windows makinelerini yedeklemek IÇIN Mars aracısını nasıl kullandığını](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)öğrenin.
* Bir ikincil MABS veya Data Protection Manager sunucusu üzerinde MARS aracısını çalıştıran [yedekleme mimarisi](backup-architecture.md#architecture-back-up-to-dpmmabs) hakkında bilgi edinin.
* [Desteklenen öğeleri ve Mars Aracısı tarafından neleri yedekleyebileceğinize](backup-support-matrix-mars-agent.md) bakın.
* Yedeklemek istediğiniz makinelerde [İnternet erişimini doğrulayın](install-mars-agent.md#verify-internet-access) .
* MARS Aracısı yüklü değilse, [buradan](install-mars-agent.md)yüklemeyi öğrenin.

## <a name="create-a-backup-policy"></a>Yedekleme ilkesi oluşturma

Yedekleme ilkesi, kurtarma noktaları oluşturmak için verilerin ne zaman anlık görüntülerini ele almak istediğinizi belirtir. Ayrıca, kurtarma noktalarının ne kadar süreyle saklanacağını de belirtir. Bir yedekleme ilkesi yapılandırmak için MARS aracısını kullanın.

Azure Backup otomatik olarak bir hesaba gün ışığından yararlanma saati (DST) almaz. Bu varsayılan değer gerçek zamanlı ve zamanlanan yedekleme zamanı arasında bir uyuşmazlık oluşmasına neden olabilir.

Yedekleme ilkesi oluşturmak için:

1. MARS aracısını indirip kaydettikten sonra, aracı konsolunu açın. Bunu, makinenizde **Microsoft Azure Backup** aramasını yaparak bulabilirsiniz.  

1. **Eylemler**' in altında, **yedeklemeyi zamanla**' yı seçin.

    ![Windows Server yedeklemesini zamanlama](./media/backup-configure-vault/schedule-first-backup.png)
1. Yedekleme zamanlaması sihirbazında, sonraki **Başlangıç**' ı seçin  >  .
1. **Yedeklenecek öğeleri seçin** altında **öğe Ekle**' yi seçin.

    ![Yedeklenecek öğeleri ekle](./media/backup-azure-manage-mars/select-item-to-backup.png)

1. **Öğeleri seçin** kutusunda, yedeklenecek öğeleri seçin ve ardından **Tamam**' ı seçin.

    ![Yedeklenecek öğeleri seçin](./media/backup-azure-manage-mars/selected-items-to-backup.png)

1. **Yedeklenecek öğeleri seçin** sayfasında **İleri**' yi seçin.
1. **Yedekleme zamanlamasını belirtin** sayfasında, günlük veya haftalık yedeklemelerin ne zaman ele geçirmesine belirtin. Sonra **İleri**’yi seçin.

    * Bir yedekleme çekilirken bir kurtarma noktası oluşturulur.
    * Ortamınızda oluşturulan kurtarma noktası sayısı yedekleme zamanlamanıza bağlıdır.
    * Günde en fazla üç günlük yedekleme zamanlayabilirsiniz. Aşağıdaki örnekte, biri gece yarısı ve diğeri 6:00 PM olmak üzere iki günlük yedek oluşur.

        ![Günlük yedekleme zamanlaması ayarlama](./media/backup-configure-vault/day-schedule.png)

    * Haftalık yedeklemeleri de çalıştırabilirsiniz. Aşağıdaki örnekte yedeklemeler, her alternatif Pazar ve Çarşamba 9:30, 1:00 ' de alınır.

        ![Haftalık yedekleme zamanlaması ayarlama](./media/backup-configure-vault/week-schedule.png)

1. **Bekletme Ilkesi Seç** sayfasında, verilerinizin geçmiş kopyalarının nasıl depolanacağını belirtin. Sonra **İleri**’yi seçin.

    * Bekletme ayarları hangi kurtarma noktalarının depolanacağını ve ne kadar süreyle depolanacağını belirtir.
    * Günlük bekletme ayarı için, günlük bekletme için belirtilen zamanda, en son kurtarma noktasının belirtilen gün sayısı boyunca tutulacaksınız. Ya da her ayın 30. gününde oluşturulan kurtarma noktasının 12 ay boyunca depolanması gerektiğini göstermek için bir aylık bekletme ilkesi belirtebilirsiniz.
    * Günlük ve haftalık kurtarma noktaları için bekletme genellikle yedekleme zamanlaması ile saatle çakışan. Bu nedenle, zamanlama bir yedekleme tetiklerse, yedeklemenin oluşturduğu kurtarma noktası günlük veya haftalık bekletme ilkesinin belirttiği süre için depolanır.
    * Aşağıdaki örnekte:

        * Gece yarısı ve 6:00 PM 'de günlük yedeklemeler yedi gün boyunca tutulur.
        * Gece yarısı ve 6:00 PM 'de Cumartesi günü gerçekleştirilen yedeklemeler dört hafta boyunca tutulur.
        * Ayın gece yarısı ve 6:00 PM 'nin son Cumartesi günü gerçekleştirilen yedeklemeler 12 ay boyunca tutulur.
        * Mart 'taki son Cumartesi üzerinde gerçekleştirilen yedeklemeler 10 yıl boyunca tutulur.

        ![Bekletme ilkesi örneği](./media/backup-configure-vault/retention-example.png)

1. **Ilk yedekleme türünü seçin** sayfasında, ilk yedeklemeyi ağ üzerinden mi yoksa çevrimdışı yedekleme mi kullanacağınızı belirleyin. İlk yedeklemeyi ağ üzerinden almak için, sonraki **ağ üzerinden otomatik olarak**' ı seçin  >  .

    Çevrimdışı yedekleme hakkında daha fazla bilgi için bkz. [çevrimdışı yedekleme için Azure Data Box kullanma](offline-backup-azure-data-box.md).

    ![İlk yedekleme türünü seçin](./media/backup-azure-manage-mars/choose-initial-backup-type.png)

1. **Onay** sayfasında, bilgileri gözden geçirin ve ardından **son**' u seçin.

    ![Yedekleme türünü onaylayın](./media/backup-azure-manage-mars/confirm-backup-type.png)

1. Sihirbaz Yedekleme zamanlamasını oluşturmayı tamamladıktan sonra **Kapat**' ı seçin.

    ![Yedekleme zamanlaması ilerlemesini görüntüleme](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Aracının yüklü olduğu her makinede bir ilke oluşturun.

### <a name="do-the-initial-backup-offline"></a>İlk yedeklemeyi çevrimdışı yap

Bir ilk yedeklemeyi ağ üzerinden otomatik olarak çalıştırabilir veya çevrimdışı yedekleyebilirsiniz. Bir ilk yedekleme için çevrimdışı dengeli dağıtım, aktarım için çok fazla ağ bant genişliği gerektiren büyük miktarda veriniz olduğunda faydalıdır.

Çevrimdışı aktarım yapmak için:

1. Yedekleme verilerini bir hazırlama konumuna yazın.
1. Hazırlama konumundaki verileri bir veya daha fazla SATA diskine kopyalamak için AzureOfflineBackupDiskPrep aracını kullanın.

    Araç bir Azure Içeri aktarma işi oluşturur. Daha fazla bilgi için bkz. [Azure içeri/dışarı aktarma hizmeti nedir?](../import-export/storage-import-export-service.md)
1. SATA disklerini bir Azure veri merkezine gönderin.

    Veri merkezinde, disk verileri bir Azure depolama hesabına kopyalanır. Azure Backup, verileri depolama hesabından kasaya kopyalar ve artımlı yedeklemeler zamanlanır.

Çevrimdışı dağıtım hakkında daha fazla bilgi için bkz. [çevrimdışı yedekleme için Azure Data Box kullanma](offline-backup-azure-data-box.md).

### <a name="enable-network-throttling"></a>Ağ azaltmayı etkinleştir

Ağ azaltmayı etkinleştirerek MARS aracısının ağ bant genişliğini nasıl kullandığını kontrol edebilirsiniz. Çalışma saatleri sırasında verileri yedeklemeniz gerekiyorsa ancak yedekleme ve geri yükleme etkinliğinin kullandığı bant genişliğini denetlemek istediğinizde kısıtlama yararlı olur.

Azure Backup ağ azaltma, yerel işletim sisteminde [hizmet kalitesi (QoS)](/windows-server/networking/technologies/qos/qos-policy-top) kullanır.

Yedeklemeler için ağ azaltma, Windows Server 2012 ve üzeri sürümlerde ve Windows 8 ve üzeri sürümlerde kullanılabilir. İşletim sistemleri en son hizmet paketlerini çalıştırıyor olmalıdır.

Ağ azaltmayı etkinleştirmek için:

1. MARS aracısında **Özellikleri Değiştir**' i seçin.
1. **Daraltma** sekmesinde, **yedekleme işlemleri için internet bant genişliği kullanımını azaltmayı etkinleştir**' i seçin.

    ![Yedekleme işlemleri için ağ daraltma ayarlama](./media/backup-configure-vault/throttling-dialog.png)
1. Çalışma saatleri ve çalışma dışı saatler sırasında izin verilen bant genişliğini belirtin. Bant genişliği değerleri 512 kbps ile başlar ve 1.023 Mbps 'e kadar gider. Ardından **Tamam**’ı seçin.

## <a name="run-an-on-demand-backup"></a>İsteğe bağlı yedekleme çalıştırma

1. MARS aracısında **Şimdi Yedekle**' yi seçin.

    ![Windows Server 'da Şimdi Yedekle](./media/backup-configure-vault/backup-now.png)

1. MARS Aracısı sürümü 2.0.9169.0 veya daha yeniyse, özel bir bekletme tarihi belirleyebilirsiniz. Yedeklemeyi aynı şekilde **sakla** bölümünde takvimden bir tarih seçin.

   ![Bekletme tarihini özelleştirmek için takvimi kullanın](./media/backup-configure-vault/mars-ondemand.png)

1. **Onay** sayfasında, ayarları gözden geçirin ve **Yedekle**' yi seçin.
1. Sihirbazı kapatmak için **Kapat** ' ı seçin. Yedekleme tamamlanmadan önce Sihirbazı kapatırsanız, sihirbaz arka planda çalışmaya devam eder.

İlk yedekleme tamamlandıktan sonra, yedekleme konsolunda **iş tamamlandı** durumu görüntülenir.

## <a name="set-up-on-demand-backup-policy-retention-behavior"></a>İsteğe bağlı yedekleme ilkesi saklama davranışını ayarlama

> [!NOTE]
> Bu bilgiler yalnızca 2.0.9169.0 ' den eski olan MARS Aracısı sürümleri için geçerlidir.
>

| Yedekleme-zamanlama seçeneği | Veri saklama süresi
| -- | --
| Gün | **Varsayılan saklama**: "günlük yedeklemeler için gün cinsinden bekletme" ile eşdeğerdir. <br/><br/> **Özel durum**: uzun süreli saklama (hafta, ay veya yıl) için ayarlanmış günlük zamanlanmış bir yedekleme başarısız olursa, uzun süreli saklama için hata olarak değerlendirildikten hemen sonra tetiklenen bir isteğe bağlı yedekleme yapılır. Aksi halde, bir sonraki zamanlanmış yedekleme uzun süreli saklama için kabul edilir.<br/><br/> **Örnek senaryo**: 8:00 tarihinde Salı günü zamanlanan yedekleme başarısız oldu. Bu yedekleme, haftalık, aylık veya yıllık bekletme için değerlendirilmelidir. Bu nedenle, 8:00 ' de saat ' de bir sonraki zamanlanmış yedeklemeden önce tetiklenen ilk isteğe bağlı yedekleme, haftalık, aylık veya yıllık bekletme için otomatik olarak etiketlenir. Bu yedekleme, 8:00. yedekleme için alternatifler kullanır.
| Hafta | **Varsayılan bekletme**: bir gün. Haftalık yedekleme ilkesi olan bir veri kaynağı için alınan isteğe bağlı yedeklemeler bir sonraki gün silinir. Bunlar, veri kaynağı için en son yedeklemeler olsalar bile silinir. <br/><br/> **Özel durum**: uzun süreli saklama (hafta, ay veya yıl) için ayarlanmış haftalık zamanlanmış bir yedekleme başarısız olursa, uzun süreli saklama için hata olarak değerlendirildikten hemen sonra tetiklenen bir isteğe bağlı yedekleme yapılır. Aksi halde, bir sonraki zamanlanmış yedekleme uzun süreli saklama için kabul edilir. <br/><br/> **Örnek senaryo**: 8:00 tarihinde Salı günü zamanlanan yedekleme başarısız oldu. Bu yedekleme, aylık veya yıllık bekletme için değerlendirilmelidir. Bu nedenle, 8:00 ' de Salı günü saat veya yıllık bekletme için bir sonraki zamanlanmış yedeklemeden önce tetiklenen ilk talep üzerine yedekleme otomatik olarak etiketlenir. Bu yedekleme, 8:00. yedekleme için alternatifler kullanır.

Daha fazla bilgi için bkz. [yedekleme Ilkesi oluşturma](#create-a-backup-policy).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure 'da dosyaları geri yüklemeyi](backup-azure-restore-windows-server.md)öğrenin.
* [Dosya ve klasörleri yedekleme hakkında sık sorulan soruları](backup-azure-file-folder-backup-faq.yml) bulun
