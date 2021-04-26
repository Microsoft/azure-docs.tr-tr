---
title: Azure VM 'lerinde yedeklenen SAP HANA veritabanlarını yönetme
description: Bu makalede, Azure sanal makinelerinde çalışan SAP HANA veritabanlarını yönetmek ve izlemek için ortak görevler hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 54d3341a83873ad3cc50815f04a0b252bb44438e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101703775"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Yedeklenen SAP HANA veritabanlarını yönetme ve izleme

Bu makalede, bir Azure sanal makinesinde (VM) çalışan ve [Azure Backup](./backup-overview.md) hizmeti tarafından Azure Backup kurtarma hizmetleri kasasına yedeklenen SAP HANA veritabanlarını yönetmek ve izlemek için kullanılan ortak görevler açıklanmaktadır. İşlerin ve uyarıların nasıl izleneceğini, isteğe bağlı bir yedekleme tetiklemeyi, ilkeleri düzenlemenizi, veritabanı korumasını durdurup sürdürmeyi ve bir VM 'nin yedeklerden kaydını nasıl sileceğinizi öğreneceksiniz.

Yedeklemeleri henüz SAP HANA veritabanları için yapılandırmadıysanız, bkz. [Azure VM 'lerinde SAP HANA veritabanlarını yedekleme](./backup-azure-sap-hana-database.md).

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Portalda el ile yedekleme işlerini izleme

Azure Backup Azure portal tüm el ile tetiklenen işleri, **yedekleme işleri** bölümünde gösterir.

![Yedekleme işleri bölümü](./media/sap-hana-db-manage/backup-jobs.png)

Bu portalda gördüğünüz işler veritabanı bulma ve kaydetme, yedekleme ve geri yükleme işlemlerini içerir. Bu bölümde günlük yedeklemeleri dahil olmak üzere zamanlanmış işler gösterilmez. SAP HANA yerel istemcilerden el ile tetiklenen yedeklemeler (Studio/kokpit/DBA kokpiti) da burada gösterilmez.

![Yedekleme işleri listesi](./media/sap-hana-db-manage/backup-jobs-list.png)

İzleme hakkında daha fazla bilgi edinmek için [Azure izleyici kullanarak](./backup-azure-monitoring-use-azuremonitor.md)Azure Portal ve izleme ' [de izleme](./backup-azure-monitoring-built-in-monitor.md) ' ye gidin.

## <a name="view-backup-alerts"></a>Yedekleme uyarılarını görüntüle

Uyarılar SAP HANA veritabanlarının yedeklerini izlemenin kolay bir yöntemidir. Uyarılar, bir yedeklemenin oluşturduğu çok sayıda olayın kaybedilmesi gerekmeden en iyi şekilde ilgilendiğiniz olaylara odaklanmaya yardımcı olur. Azure Backup, uyarıları ayarlamanıza olanak sağlar ve aşağıdaki gibi izlenebilir:

* [Azure portalında](https://portal.azure.com/) oturum açın.
* Kasa panosunda **yedekleme uyarıları**' nı seçin.

  ![Kasa panosunda yedekleme uyarıları](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* Uyarıları görebileceksiniz:

  ![Yedekleme uyarıları listesi](./media/sap-hana-db-manage/backup-alerts-list.png)

* Daha fazla ayrıntı görmek için uyarıları seçin:

  ![Uyarı ayrıntıları](./media/sap-hana-db-manage/alert-details.png)

Bugün Azure Backup, e-posta ile uyarıların gönderilmesini sağlar. Bu uyarılar şunlardır:

* Tüm yedekleme hatalarıyla ilgili olarak tetiklendi.
* Veritabanı düzeyinde hata koduna göre birleştirildi.
* Yalnızca bir veritabanının ilk yedekleme hatası için gönderilir.

İzleme hakkında daha fazla bilgi edinmek için [Azure izleyici kullanarak](./backup-azure-monitoring-use-azuremonitor.md)Azure Portal ve izleme ' [de izleme](./backup-azure-monitoring-built-in-monitor.md) ' ye gidin.

## <a name="management-operations"></a>Yönetim İşlemleri

Azure Backup, yedeklenen bir SAP HANA veritabanının yönetimini desteklediği çok sayıda yönetim işlemi ile kolay hale getirir. Bu işlemler aşağıdaki bölümlerde daha ayrıntılı bir şekilde ele alınmıştır.

### <a name="run-an-on-demand-backup"></a>İsteğe bağlı yedekleme çalıştırma

Yedeklemeler, ilke zamanlamasına uygun olarak çalışır. İsteğe bağlı bir yedeklemeyi aşağıdaki gibi çalıştırabilirsiniz:

1. Kasa menüsünde **yedekleme öğeleri**' ni seçin.
2. **Yedekleme öğeleri**' nde, SAP HANA VERITABANıNı çalıştıran VM 'yi seçin ve ardından **Şimdi Yedekle**' yi seçin.
3. **Şimdi Yedekle**' de, gerçekleştirmek istediğiniz yedekleme türünü seçin. Ardından **Tamam**’ı seçin. Bu yedekleme, bu yedekleme öğesiyle ilişkili ilkeye göre saklanacaktır.
4. Portal bildirimlerini izleyin. İş ilerlemesini kasa panosunda izleyebilirsiniz > **yedekleme işleri**  >  **devam** ediyor. Veritabanınızın boyutuna bağlı olarak, ilk yedeklemenin oluşturulması biraz zaman alabilir.

Varsayılan olarak, isteğe bağlı yedeklemelerin saklanması 45 gündür.

### <a name="hana-native-client-integration"></a>HANA yerel istemci tümleştirmesi

#### <a name="backup"></a>Backup

HANA yerel istemcilerinden ( **Backint**'e) tetiklenen isteğe bağlı yedeklemeler, **yedekleme öğeleri** sayfasındaki yedekleme listesinde görünür.

![Son yedeklemeler çalışma](./media/sap-hana-db-manage/last-backups.png)

Ayrıca, [Bu yedeklemeleri](#monitor-manual-backup-jobs-in-the-portal) **yedekleme işleri** sayfasından da izleyebilirsiniz.

Bu isteğe bağlı yedeklemeler Ayrıca geri yükleme için geri yükleme noktaları listesinde görünür.

![Geri yükleme noktalarının listesi](./media/sap-hana-db-manage/list-restore-points.png)

#### <a name="restore"></a>Geri Yükleme

Aynı makineye geri yüklemek için HANA yerel istemcilerinden ( **Backint** kullanılarak) tetiklenen geri yüklemeler, **yedekleme işleri** sayfasından [izlenebilir](#monitor-manual-backup-jobs-in-the-portal) .

### <a name="run-sap-hana-native-client-backup-to-local-disk-on-a-database-with-azure-backup-enabled"></a>Azure Backup etkin olan bir veritabanında yerel diske SAP HANA yerel istemci yedeklemesi çalıştırma

Azure Backup yedeklenmekte olan bir veritabanının yerel yedeklemesini (HANA Studio/kokpit kullanarak) almak istiyorsanız aşağıdakileri yapın:

1. Veritabanının tamamlaması için herhangi bir tam veya günlük yedeklemesi bekleyin. SAP HANA Studio/Kokpit içindeki durumu denetleyin.
2. ilgili VERITABANı için
    1. Backınt parametrelerini yeniden ayarlamayın. Bunu yapmak için **SystemDB**  >  **yapılandırması**  >  **veritabanı**  >  **filtresi Seç (günlük)** öğesine çift tıklayın.
        * enable_auto_log_backup: Hayır
        * log_backup_using_backint: false
        * catalog_backup_using_backint: false
3. Veritabanına yönelik isteğe bağlı tam yedekleme yapın
4. Ardından adımları tersine çevirin. Yukarıda bahsedilen ilgili VERITABANı için,
    1. backınt parametrelerini yeniden etkinleştirin
        1. catalog_backup_using_backint: doğru
        1. log_backup_using_backint: doğru
        1. enable_auto_log_backup: Evet

### <a name="manage-or-clean-up-the-hana-catalog-for-a-database-with-azure-backup-enabled"></a>Azure Backup etkinleştirilmiş bir veritabanı için HANA kataloğunu yönetme veya temizleme

Yedekleme kataloğunu düzenlemek veya temizlemek istiyorsanız aşağıdakileri yapın:

1. Veritabanının tamamlaması için herhangi bir tam veya günlük yedeklemesi bekleyin. SAP HANA Studio/Kokpit içindeki durumu denetleyin.
2. ilgili VERITABANı için
    1. Backınt parametrelerini yeniden ayarlamayın. Bunu yapmak için **SystemDB**  >  **yapılandırması**  >  **veritabanı**  >  **filtresi Seç (günlük)** öğesine çift tıklayın.
        * enable_auto_log_backup: Hayır
        * log_backup_using_backint: false
        * catalog_backup_using_backint: false
3. Kataloğu Düzenle ve eski girdileri kaldır
4. Ardından adımları tersine çevirin. Yukarıda bahsedilen ilgili VERITABANı için,
    1. backınt parametrelerini yeniden etkinleştirin
        1. catalog_backup_using_backint: doğru
        1. log_backup_using_backint: doğru
        1. enable_auto_log_backup: Evet

### <a name="change-policy"></a>İlkeyi Değiştir

SAP HANA yedekleme öğesi için temel olan ilkeyi değiştirebilirsiniz.

* Kasa panosunda **yedekleme öğeleri**' ne gidin:

  ![Yedekleme öğelerini seçin](./media/sap-hana-db-manage/backup-items.png)

* **Azure VM 'de SAP HANA** seçin

  ![Azure VM 'de SAP HANA seçin](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* Temel ilkesini değiştirmek istediğiniz yedekleme öğesini seçin
* Mevcut yedekleme ilkesini seçin.

  ![Mevcut yedekleme ilkesini seçin](./media/sap-hana-db-manage/existing-backup-policy.png)

* Listeden seçim yaparak ilkeyi değiştirin. Gerekirse [Yeni bir yedekleme Ilkesi oluşturun](./backup-azure-sap-hana-database.md#create-a-backup-policy) .

  ![Açılan listeden ilke ' yi seçin](./media/sap-hana-db-manage/choose-backup-policy.png)

* Değişiklikleri kaydedin

  ![Değişiklikleri kaydedin](./media/sap-hana-db-manage/save-changes.png)

* İlke değişikliği, ilişkili tüm yedekleme öğelerini etkiler ve ilgili **yapılandırma koruma** işlerini tetikler.

>[!NOTE]
> Saklama döneminde yapılan herhangi bir değişiklik, yeni olanlar da içinde olmak üzere daha eski kurtarma noktalarına daha geriye dönük olarak uygulanır.

### <a name="modify-policy"></a>Ilkeyi Değiştir

Yedekleme türlerini, frekansları ve bekletme aralığını değiştirmek için ilkeyi değiştirin.

>[!NOTE]
>Saklama döneminde yapılan herhangi bir değişiklik, yeni olanlara ek olarak, daha eski kurtarma noktalarına daha etkin bir şekilde uygulanır.

1. Kasa panosunda **> yedekleme Ilkelerini Yönet** ' e gidin ve düzenlemek istediğiniz ilkeyi seçin.

   ![Düzenlenecek ilkeyi seçin](./media/sap-hana-db-manage/manage-backup-policies.png)

1. **Değiştir**'i seçin.

   ![Değiştir'i seçin](./media/sap-hana-db-manage/modify-policy.png)

1. Yedekleme türleri için sıklığı seçin.

   ![Yedekleme sıklığını seçin](./media/sap-hana-db-manage/choose-frequency.png)

İlke değişikliği, ilişkili tüm yedekleme öğelerini etkiler ve ilgili **yapılandırma koruma** işlerini tetikler.

### <a name="inconsistent-policy"></a>Tutarsız ilke

Bazen bir ilke değiştirme işlemi, bazı yedekleme öğeleri için **tutarsız** bir ilke sürümüne yol açabilir. Bu durum, bir ilkeyi değiştir işlemi tetiklendikten sonra ilgili **yapılandırma koruma** işi yedekleme öğesi için başarısız olduğunda gerçekleşir. Yedekleme öğesi görünümünde şu şekilde görünür:

![Tutarsız ilke](./media/sap-hana-db-manage/inconsistent-policy.png)

Etkilenen tüm öğelerin ilke sürümünü tek tıklamayla çözebilirsiniz:

![İlke sürümünü çözme](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>SAP HANA veritabanı korumasını durdurma

SAP HANA veritabanının korunmasını birkaç yolla durdurabilirsiniz:

* Gelecekteki tüm yedekleme işlerini durdurun ve tüm kurtarma noktalarını silin.
* Gelecekteki tüm yedekleme işlerini durdurun ve kurtarma noktalarını bozulmadan bırakın.

Kurtarma noktalarından ayrıldığınızda bu ayrıntıları göz önünde bulundurun:

* Tüm kurtarma noktaları süresiz olarak kalır ve tüm ayıklama, verileri koruyarak koruma durdurma sırasında durdurulur.
* Korumalı örnek ve tüketilen depolama alanı için ücretlendirilirsiniz. Daha fazla bilgi için bkz. [Azure Backup fiyatlandırması](https://azure.microsoft.com/pricing/details/backup/).
* Yedeklemeleri durdurmadan bir veri kaynağını silerseniz, yeni yedeklemeler başarısız olur.

Bir veritabanının korumasını durdurmak için:

* Kasa panosunda **yedekleme öğeleri**' ni seçin.
* **Yedekleme yönetimi türü** altında **Azure VM 'de SAP HANA** ' yi seçin.

  ![Azure VM 'de SAP HANA seçin](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Korumasını durdurmak istediğiniz veritabanını seçin:

  ![Korumayı durdurmak için veritabanını seçin](./media/sap-hana-db-manage/select-database.png)

* Veritabanı menüsünde **Yedeklemeyi Durdur**' u seçin.

  ![Yedeklemeyi Durdur ' u seçin](./media/sap-hana-db-manage/stop-backup.png)

* **Yedeklemeyi Durdur** menüsünde, verileri tutmayı veya silmeyi seçin. İsterseniz, bir neden ve açıklama belirtin.

  ![Veri tutmayı veya silmeyi seçin](./media/sap-hana-db-manage/retain-backup-data.png)

* **Yedeklemeyi Durdur**' u seçin.

### <a name="resume-protection-for-an-sap-hana-database"></a>SAP HANA veritabanı için korumayı sürdürür

SAP HANA veritabanı için korumayı durdurduğunuzda, **yedekleme verilerini sakla** seçeneğini belirlerseniz, daha sonra korumayı devam ettirebilirsiniz. Yedeklenen verileri korumazsanız korumaya devam edebilirsiniz.

Bir SAP HANA veritabanı korumasını sürdürmesini sağlamak için:

* Yedekleme öğesini açın ve yedeklemeyi yeniden **başlatma**' yı seçin.

   ![Yedeklemeyi sürdürür ' ı seçin](./media/sap-hana-db-manage/resume-backup.png)

* **Yedekleme ilkesi** menüsünde bir ilke seçin ve ardından **Kaydet**' i seçin.

### <a name="upgrading-from-sdc-to-mdc"></a>SDC 'den MDC 'ye yükseltme

[SDC 'den MDC 'ye yükselttikten sonra](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid)bir SAP HANA veritabanı için yedeklemeye nasıl devam edeceğinizi öğrenin.

### <a name="upgrading-from-sdc-to-mdc-without-a-sid-change"></a>SDC 'den MDC 'ye bir SID değişikliği olmadan yükseltme

[SDC 'den MDC 'ye yükselttikten sonra SID 'nin değişmediğinden](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-no-change-in-sid)SAP HANA veritabanının yedeklenmesi ile devam etmeyi öğrenin.

### <a name="upgrading-to-a-new-version-in-either-sdc-or-mdc"></a>SDC veya MDC 'de yeni bir sürüme yükseltme

[Sürümü yükseltilmekte](backup-azure-sap-hana-database-troubleshoot.md#sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm)olan SAP HANA veritabanının yedeğine devam etmeyi öğrenin.

### <a name="unregister-an-sap-hana-instance"></a>SAP HANA örneğinin kaydını silme

Korumayı devre dışı bıraktıktan sonra, kasayı silmeden önce bir SAP HANA örneğinin kaydını silin:

* Kasa panosunda, **Yönet** altında, **Yedekleme altyapısı**' nı seçin.

   ![Yedekleme altyapısını seçin](./media/sap-hana-db-manage/backup-infrastructure.png)

* **Azure VM 'de Iş yükü** olarak **yedekleme yönetimi türünü** seçin

   ![Azure VM 'de Iş yükü olarak yedekleme yönetimi türünü seçin](./media/sap-hana-db-manage/backup-management-type.png)

* **Korumalı sunucular**' da, silmek için örneği seçin. Kasayı silmek için tüm sunucuların/örneklerin kaydını silmeniz gerekir.

* Korunan örneğe sağ tıklayıp **kayıt kaldır**' ı seçin.

   ![Kayıt silmeyi Seç](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>SAP HANA sunucusu VM 'sinde uzantıyı yeniden Kaydet

Bazen VM 'deki iş yükü uzantısı bir neden veya başka bir nedenden dolayı etkilenebilir. Bu gibi durumlarda, VM 'de tetiklenen tüm işlemler başarısız olur. Ardından, uzantıyı VM 'de yeniden kaydetmeniz gerekebilir. Yeniden kaydetme işlemi devam etmek için VM 'de iş yükü yedekleme uzantısını yeniden yükler.

Bu seçeneği dikkatli bir şekilde kullanın: zaten sağlıklı bir uzantıya sahip bir VM 'de tetiklendiğinde, bu işlem uzantının yeniden başlatılmasına neden olur. Bu, tüm sürmekte olan işlerin başarısız olmasına neden olabilir. Yeniden kaydetme işlemini tetiklemeden önce bir veya daha fazla [belirtiye](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures) göz atın.

## <a name="next-steps"></a>Sonraki adımlar

* [SAP HANA veritabanlarını yedeklerken sık karşılaşılan sorunları gidermeye nasıl sorun](./backup-azure-sap-hana-database-troubleshoot.md) alabileceğinizi öğrenin.
