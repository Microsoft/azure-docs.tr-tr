---
title: Azure VM 'de SQL Server veritabanlarını yönetme ve izleme
description: Bu makalede, bir Azure VM üzerinde çalışan SQL Server veritabanlarının nasıl yönetileceği ve izleneceği açıklanır.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 4daf068e97a08d1a611ef64cb64569cacd5d7420
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172151"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Yedeklenen SQL Server veritabanlarını yönetme ve izleme

Bu makalede, bir Azure sanal makinesinde (VM) çalışan ve [Azure Backup](backup-overview.md) hizmeti tarafından Azure Backup kurtarma hizmetleri kasasına yedeklenen SQL Server veritabanlarını yönetmek ve izlemek için kullanılan ortak görevler açıklanmaktadır. İşlerin ve uyarıların nasıl izleneceğini, veritabanı korumasını durdurmayı ve sürdürmeyi, yedekleme işlerini çalıştırmayı ve bir VM 'nin yedeklerden kaydını nasıl sileceğinizi öğreneceksiniz.

SQL Server veritabanınız için henüz yedeklemeleri yapılandırmadıysanız bkz. [Azure VM 'lerinde SQL Server veritabanlarını yedekleme](backup-azure-sql-database.md)

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Portalda el ile yedekleme işlerini izleme

Azure Backup, **yedekleme işleri** portalındaki tüm el ile tetiklenen işleri gösterir. Bu portalda gördüğünüz işler veritabanı bulma ve kaydetme, yedekleme ve geri yükleme işlemlerini içerir.

![Yedekleme işleri portalı](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> **Yedekleme işleri** portalında zamanlanmış yedekleme işleri gösterilmez. Sonraki bölümde açıklandığı gibi zamanlanmış yedekleme işlerini izlemek için SQL Server Management Studio kullanın.
>

Izleme senaryoları hakkında daha fazla bilgi için [Azure izleyici kullanarak](backup-azure-monitoring-use-azuremonitor.md)Azure Portal ve izleme ' [de izleme](backup-azure-monitoring-built-in-monitor.md) ' ye gidin.  

## <a name="view-backup-alerts"></a>Yedekleme uyarılarını görüntüle

Günlük yedeklemeleri her 15 dakikada bir gerçekleştiğinden, izleme yedekleme işleri sıkıcı olabilir. Azure Backup, e-posta uyarıları göndererek izlemeyi kolaylaştırır. E-posta uyarıları şunlardır:

- Tüm yedekleme hatalarıyla ilgili olarak tetiklendi.
- Veritabanı düzeyinde hata koduna göre birleştirildi.
- Yalnızca bir veritabanının ilk yedekleme hatası için gönderilir.

Veritabanı yedekleme uyarılarını izlemek için:

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. Kasa panosunda **Uyarılar ve olaylar**' ı seçin.

   ![Uyarıları ve olayları seçin](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. **Uyarılar ve olaylar**' da **yedekleme uyarıları**' nı seçin.

   ![Yedekleme uyarılarını seçin](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>SQL Server veritabanı korumasını durdurma

SQL Server veritabanını yedeklemeyi birkaç yolla durdurabilirsiniz:

- Gelecekteki tüm yedekleme işlerini durdurun ve tüm kurtarma noktalarını silin.
- Gelecekteki tüm yedekleme işlerini durdurun ve kurtarma noktalarını dokunulmadan bırakın.

Kurtarma noktalarından ayrıldığınızda bu ayrıntıları göz önünde bulundurun:

- Tüm kurtarma noktaları süresiz olarak kalır, tüm ayıklama, verileri sakla ile korumayı durdurma sırasında durdurulur.
- Korumalı örnek ve tüketilen depolama alanı için ücretlendirilirsiniz. Daha fazla bilgi için bkz. [Azure Backup fiyatlandırması](https://azure.microsoft.com/pricing/details/backup/).
- Yedeklemeleri durdurmadan bir veri kaynağını silerseniz, yeni yedeklemeler başarısız olur.

Bir veritabanının korumasını durdurmak için:

1. Kasa panosunda **yedekleme öğeleri**' ni seçin.

2. **Yedekleme yönetimi türü**altında **Azure VM 'de SQL**' i seçin.

    ![Azure VM 'de SQL seçin](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Korumasını durdurmak istediğiniz veritabanını seçin.

    ![Korumayı durdurulacak veritabanını seçin](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. Veritabanı menüsünde **Yedeklemeyi Durdur**' u seçin.

    ![Yedeklemeyi Durdur ' u seçin](./media/backup-azure-sql-database/stop-db-button.png)

5. **Yedeklemeyi Durdur** menüsünde, verileri tutmayı veya silmeyi seçin. İsterseniz, bir neden ve açıklama belirtin.

    ![Yedeklemeyi Durdur menüsünde verileri tutma veya silme](./media/backup-azure-sql-database/stop-backup-button.png)

6. **Yedeklemeyi Durdur**' u seçin.

> [!NOTE]
>
>Verileri Sil seçeneği hakkında daha fazla bilgi için aşağıdaki SSS bölümüne bakın:
>
>- [Bir veritabanını bir yeniden korunan örnekten silersem, yedeklemelere ne olur?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
>- [Otomatik korumalı bir veritabanının yedekleme işlemini durdurdum, davranışı ne olur?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>

## <a name="resume-protection-for-a-sql-database"></a>Bir SQL veritabanı için korumayı sürdürür

SQL veritabanı için korumayı durdurduğunuzda, **yedekleme verilerini sakla** seçeneğini belirlerseniz, daha sonra korumayı devam ettirebilirsiniz. Yedekleme verilerini korumazsanız korumaya devam edebilirsiniz.

Bir SQL veritabanı için korumayı sürdürmesini sağlamak için:

1. Yedekleme öğesini açın ve yedeklemeyi yeniden **başlatma**' yı seçin.

    ![Veritabanı korumasını sürdürmesini sağlamak için yedeklemeyi sürdürür ' ı seçin](./media/backup-azure-sql-database/resume-backup-button.png)

2. **Yedekleme ilkesi** menüsünde bir ilke seçin ve ardından **Kaydet**' i seçin.

## <a name="run-an-on-demand-backup"></a>İsteğe bağlı yedekleme çalıştırma

İsteğe bağlı yedeklemelerin farklı türlerini çalıştırabilirsiniz:

- Tam yedekleme
- Salt kopya tam yedekleme
- Değişiklik yedeği
- Günlük yedekleme

Yalnızca kopya tam yedekleme için saklama süresini belirtmeniz gerektiğinde, isteğe bağlı tam yedekleme için bekletme aralığı otomatik olarak geçerli zamandan 45 gün olarak ayarlanır.

Daha fazla bilgi için bkz. [SQL Server yedekleme türleri](backup-architecture.md#sql-server-backup-types).

## <a name="unregister-a-sql-server-instance"></a>SQL Server örneğinin kaydını silme

Korumayı devre dışı bıraktıktan sonra ancak kasayı silmeden önce bir SQL Server örneğinin kaydını kaldırın:

1. Kasa panosunda, **Yönet**altında, **Yedekleme altyapısı**' nı seçin.  

   ![Yedekleme altyapısını seçin](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. **Yönetim sunucuları**altında **korumalı sunucular**' ı seçin.

   ![Korumalı sunucuları seçin](./media/backup-azure-sql-database/protected-servers.png)

3. **Korumalı sunucular**' da, kaydı kaldırmak istediğiniz sunucuyu seçin. Kasayı silmek için tüm sunucuların kaydını kaldırmanız gerekir.

4. Korumalı sunucuya sağ tıklayın ve **kayıt kaldır**' ı seçin.

   ![Sil ' i seçin](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="modify-policy"></a>İlkeyi Değiştir

Yedekleme sıklığını veya bekletme aralığını değiştirmek için ilkeyi değiştirin.

> [!NOTE]
> Saklama döneminde yapılan herhangi bir değişiklik, yeni olanlar da içinde olmak üzere daha eski kurtarma noktalarına daha geriye dönük olarak uygulanır.

Kasa panosunda > **yedekleme Ilkelerini** **Yönet** ' e gidin ve düzenlemek istediğiniz ilkeyi seçin.

  ![Yedekleme ilkesini Yönet](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![Yedekleme ilkesini değiştirme](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

İlke değişikliği, ilişkili tüm yedekleme öğelerini etkiler ve ilgili **yapılandırma koruma** işlerini tetikler.

### <a name="inconsistent-policy"></a>Tutarsız ilke

Bazen bir ilke değiştirme işlemi bazı yedekleme öğeleri için **tutarsız** bir ilke sürümüne yol açabilir. Bu durum, bir ilkeyi değiştir işlemi tetiklendikten sonra ilgili **yapılandırma koruma** işi yedekleme öğesi için başarısız olduğunda gerçekleşir. Yedekleme öğesi görünümünde şu şekilde görünür:

  ![Tutarsız ilke](./media/backup-azure-sql-database/inconsistent-policy.png)

Etkilenen tüm öğelerin ilke sürümünü tek tıklamayla çözebilirsiniz:

  ![Tutarsız ilkeyi çözme](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="re-register-extension-on-the-sql-server-vm"></a>SQL Server VM uzantıyı yeniden Kaydet

Bazen, VM 'deki iş yükü uzantısı bir neden veya diğer bir nedenden dolayı etkilenebilir. Bu gibi durumlarda, VM 'de tetiklenen tüm işlemler başarısız olur. Ardından, uzantıyı VM 'de yeniden kaydetmeniz gerekebilir. **Yeniden kaydetme** işlemi devam etmek için VM 'de iş yükü yedekleme uzantısını yeniden yükler.

Bu seçeneği dikkatli bir şekilde kullanın; zaten sağlıklı bir uzantıya sahip bir VM 'de tetiklendiğinde, bu işlem uzantının yeniden başlatılmasına neden olur. Bu, tüm sürmekte olan işlerin başarısız olmasına neden olabilir. Yeniden kaydetme işlemini tetiklemeden önce bir veya daha fazla [belirtiyle](backup-sql-server-azure-troubleshoot.md#re-registration-failures) ilgili bir veya daha fazla belirti olup olmadığını denetleyin.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [SQL Server veritabanı üzerindeki yedeklemelerin sorunlarını giderme](backup-sql-server-azure-troubleshoot.md).
