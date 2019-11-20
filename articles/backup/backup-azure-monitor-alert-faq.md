---
title: Uyarı ve rapor izleme hakkında SSS
description: Bu makalede, Azure Backup Izleme uyarısı ve rapor Azure Backup hakkında sık sorulan soruların yanıtlarını bulun.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 9cf7bf49d29b5faa9811a591b45179fe83c1d483
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172921"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Azure Backup Izleme uyarısı-SSS

Bu makalede, Azure izleme uyarısıyla ilgili sık sorulan sorular yanıtlanmaktadır.

## <a name="configure-azure-backup-reports"></a>Azure Backup raporlarını yapılandırma

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-storage-account"></a>Nasıl yaparım? raporlama verilerinin bir depolama hesabında akışa başlayıp başlamadığına bakın.

Yapılandırdığınız depolama hesabına gidin ve kapsayıcılar ' ı seçin. Kapsayıcıda Öngörüler-logs-azurebackupreport girişi varsa, raporlama verilerinin ' de akışa başlatıldığını belirtir.

### <a name="what-is-the-frequency-of-data-push-to-a-storage-account-and-the-azure-backup-content-pack-in-power-bi"></a>Bir depolama hesabına veri gönderme sıklığı ve Power BI Azure Backup içerik paketi nedir?

  Gün 0 kullanıcıları için, bir depolama hesabına veri göndermek yaklaşık 24 saat sürer. Bu ilk gönderme işlemi tamamlandıktan sonra, veriler aşağıdaki şekilde gösterilen sıklığa göre yenilenir.

* **İşlerle**, **uyarılarla**, **yedekleme öğeleriyle** **, kasaların,** **korumalı sunucularla**ve **ilkelerden** ilgili veriler, bir müşteri depolama hesabına ve ne zaman günlüğe kaydedilir.

* **Depolama** ile ilgili veriler her 24 saatte bir müşteri depolama hesabına gönderilir.

    ![Azure Backup rapor verileri gönderim sıklığı](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

* Power BI [günde bir kez zamanlanmış yenileme](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed)içerir. İçerik paketi için Power BI verileri el ile yenilemeyi gerçekleştirebilirsiniz.

### <a name="how-long-can-i-retain-reports"></a>Raporları ne kadar süreyle tutabilir?

Bir depolama hesabı yapılandırdığınızda, depolama hesabındaki rapor verileri için bir saklama süresi seçebilirsiniz. [Raporlar için depolama hesabını yapılandırma](backup-azure-configure-reports.md#configure-storage-account-for-reports) bölümünde 6. adımı izleyin. Ayrıca, [raporları Excel 'de çözümleyebilir](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) ve gereksinimlerinize göre daha uzun bir saklama süresi için kaydedebilirsiniz.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-storage-account"></a>Depolama hesabını yapılandırdıktan sonra raporlardaki tüm verilerimi görüyorum mi?

 Bir depolama hesabını yapılandırdıktan sonra oluşturulan tüm veriler depolama hesabına gönderilir ve raporlarda kullanılabilir. Devam eden işler raporlama için itilmedi. İş bittikten veya başarısız olduktan sonra raporlara gönderilir.

### <a name="if-i-already-configured-the-storage-account-to-view-reports-can-i-change-the-configuration-to-use-another-storage-account"></a>Raporları görüntülemek için depolama hesabını zaten yapılandırdım, yapılandırmayı başka bir depolama hesabı kullanacak şekilde değiştirebilir miyim?

Evet, yapılandırmayı farklı bir depolama hesabına işaret etmek üzere değiştirebilirsiniz. Azure Backup içerik paketine bağlanırken yeni yapılandırılan depolama hesabını kullanın. Ayrıca, farklı bir depolama hesabı yapılandırıldıktan sonra, bu depolama hesabındaki yeni veriler akar. Eski veriler (yapılandırmayı değiştirmeden önce) hala eski depolama hesabında kalmaya devam eder.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Raporları kasaların ve aboneliklerde görüntüleyebilir miyim?

Evet, farklı kasaların genelinde aynı depolama hesabını yapılandırarak çapraz kasa raporlarını görüntüleyebilirsiniz. Ayrıca, abonelikler genelinde aynı depolama hesabını da yapılandırabilirsiniz. Daha sonra, raporları görüntülemek için Power BI Azure Backup içerik paketine bağlanırken bu depolama hesabını kullanabilirsiniz. Seçilen depolama hesabı, kurtarma hizmetleri kasasıyla aynı bölgede olmalıdır.

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Azure Backup Aracısı iş durumunun portalda yansıtılması ne kadar sürer?

Azure portal, Azure Backup Aracısı iş durumunun yansıtılması 15 dakika kadar sürebilir.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Bir yedekleme işi başarısız olduğunda, bir uyarının ne kadar süreceğine?

Azure Backup hatasının 20 dakika içinde bir uyarı oluşturulur.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Bildirimler yapılandırılırsa bir e-postanın gönderilmeyeceği bir durum var mı?

Evet. Aşağıdaki durumlarda bildirimler gönderilmez.

* Bildirimler saatlik olarak yapılandırılırsa ve saat içinde bir uyarı oluşturulur ve çözülür
* Bir iş iptal edildiğinde
* İlk yedekleme işi devam ettiğinden ikinci bir yedekleme işi başarısız olursa

## <a name="recovery-services-vault"></a>Kurtarma Hizmetleri Kasası

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Azure Backup Aracısı iş durumunun portalda yansıtılması ne kadar sürer?

Azure portal, Azure Backup Aracısı iş durumunun yansıtılması 15 dakika kadar sürebilir.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Bir yedekleme işi başarısız olduğunda, bir uyarının ne kadar süreceğine?

Azure Backup hatasının 20 dakika içinde bir uyarı oluşturulur.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Bildirimler yapılandırılırsa bir e-postanın gönderilmeyeceği bir durum var mı?

Evet. Aşağıdaki durumlarda bildirimler gönderilmez:

* Bildirimler saatlik olarak yapılandırılırsa ve saat içinde bir uyarı oluşturulur ve çözülür
* Bir iş iptal edildiğinde
* İlk yedekleme işi devam ettiğinden ikinci bir yedekleme işi başarısız olursa

## <a name="next-steps"></a>Sonraki adımlar

Diğer SSS 'leri okuyun:

* Azure VM yedeklemeleri hakkında [sık sorulan sorular](backup-azure-vm-backup-faq.md) .
* Azure Backup aracısıyla ilgili [sık sorulan sorular](backup-azure-file-folder-backup-faq.md)
