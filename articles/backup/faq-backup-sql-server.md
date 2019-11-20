---
title: SSS-Azure VM 'lerinde SQL Server veritabanlarını yedekleme
description: Azure Backup ile Azure VM 'lerinde SQL Server veritabanlarının yedeklenmesi hakkında sık sorulan soruların yanıtlarını bulun.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 73224164286e35f8c9447dd24cd81d7242fbb7b6
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172025"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Azure VM yedeklemesi üzerinde çalışan SQL Server veritabanları hakkında SSS

Bu makalede, Azure sanal makinelerinde (VM 'Ler) çalışan SQL Server veritabanlarının yedeklenmesi ve [Azure Backup](backup-overview.md) hizmetinin kullanılması hakkında sık sorulan sorular yanıtlanmaktadır.

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>IaaS sanal makinesi için Azure Backup 'ın yanı sıra aynı makinede SQL Server kullanabilir miyim?

Evet, VM yedeklemesi ve SQL Backup 'ın aynı VM 'de olmasını sağlayabilirsiniz. Bu durumda, günlükleri kesmeyin şekilde sanal makinede salt kopya tam yedeklemeyi tetikliyoruz.

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>Çözüm yeniden denensin veya yedeklemeleri otomatik olarak al mi?

Bazı durumlarda Azure Backup hizmeti düzeltme yedeklemelerini tetikler. Otomatik Heal aşağıda bahsedilen altı koşuldan herhangi biri için gerçekleşebilir:

- Günlük veya değişiklik yedeklemesi LSN doğrulama hatası nedeniyle başarısız olursa, sonraki günlük veya değişiklik yedeklemesi bunun yerine tam yedeklemeye dönüştürülür.
- Bir günlük veya değişiklik yedeklemesinden önce tam yedekleme gerçekleştiyse, bu günlük veya değişiklik yedeklemesi bunun yerine tam yedeklemeye dönüştürülür.
- En son tam yedeklemenin zaman aşımı 15 günden eskiyse, sonraki günlük veya değişiklik yedeklemesi bunun yerine tam yedeklemeye dönüştürülür.
- Uzantı yükseltmesi nedeniyle iptal edilen tüm yedekleme işleri, yükseltme tamamlandıktan ve uzantı başlatıldıktan sonra yeniden tetiklenir.
- Geri yükleme sırasında veritabanının üzerine yazmayı seçerseniz, bir sonraki günlük/değişiklik yedeklemesi başarısız olur ve bunun yerine tam yedekleme tetiklenir.
- Veritabanı kurtarma modelindeki değişiklik nedeniyle günlük zincirlerinin sıfırlanması için tam yedeklemenin gerekli olduğu durumlarda, bir sonraki zamanlamaya göre bir tam otomatik olarak tetiklenir.

Otomatik olarak bir özellik olarak tüm kullanıcılar için varsayılan olarak etkindir; Ancak, bunu devre dışı bırakmak isterseniz aşağıdaki işlemleri gerçekleştirin:

- SQL Server örneğinde, *C:\Program Files\Azure Iş yükü Backup\bin* klasöründe **ExtensionSettingsOverrides. JSON** dosyasını oluşturun veya düzenleyin.
- **ExtensionSettingsOverrides. JSON**dosyasında *{"EnableAutoHealer": false}* ayarlayın.
- Değişikliklerinizi kaydedin ve dosyayı kapatın.
- SQL Server örneğinde, **Görevi Yönet** ' i açın ve sonra **AzureWLBackupCoordinatorSvc** hizmetini yeniden başlatın.

## <a name="can-i-control-as-to-how-many-concurrent-backups-run-on-the-sql-server"></a>SQL Server 'da kaç eşzamanlı yedeklemenin çalıştırıldığını denetleyebilir miyim?

Evet. Bir SQL Server örneğindeki etkiyi en aza indirmek için yedekleme ilkesinin çalışma hızını azallendirebilirsiniz. Ayarı değiştirmek için:

1. SQL Server örneğinde, *C:\Program Files\Azure Iş yükü Backup\bin* klasöründe *ExtensionSettingsOverrides. JSON* dosyasını oluşturun.
2. *ExtensionSettingsOverrides. JSON* dosyasında **Defaultbackuptasksthreshold** ayarını daha düşük bir değere (örneğin, 5) değiştirin. <br>
  `{"DefaultBackupTasksThreshold": 5}`

3. Değişikliklerinizi kaydedin ve dosyayı kapatın.
4. SQL Server örneğinde, **Görev Yöneticisi**'ni açın. **AzureWLBackupCoordinatorSvc** hizmetini yeniden başlatın.<br/> <br/>
 Bu yöntem, yedek uygulamanın büyük miktarda kaynak tükettiği durumlarda, SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor?view=sql-server-2017) , gelen uygulama ISTEKLERININ kullanabileceği CPU, fiziksel GÇ ve bellek miktarına yönelik sınırları belirtmenin daha genel bir yoludur.

> [!NOTE]
> UX 'de, herhangi bir zamanda daha fazla yedekleme yapabilirsiniz, ancak yukarıdaki örneğe göre, 5 ' in bir kayan penceresinde işlenir.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>İkincil bir çoğaltmadan tam yedekleme çalıştırabilir miyim?

SQL kısıtlamalarına göre, Ikincil çoğaltmada yalnızca tam yedeklemeyi Kopyala ' yı çalıştırabilirsiniz; Ancak tam yedeklemeye izin verilmez.

## <a name="can-i-protect-availability-groups-on-premises"></a>Şirket içi kullanılabilirlik gruplarını koruyabilir miyim?

Hayır. Azure 'da çalışan SQL Server veritabanlarını Azure Backup korur. Bir kullanılabilirlik grubu (AG) Azure ile şirket içi makineler arasında yayılaysa, ağ yalnızca birincil çoğaltma Azure 'da çalışıyorsa korunabilir. Ayrıca, Azure Backup yalnızca kurtarma hizmetleri kasasıyla aynı Azure bölgesinde çalışan düğümleri korur.

## <a name="can-i-protect-availability-groups-across-regions"></a>Bölge genelindeki kullanılabilirlik gruplarını koruyabilir miyim?

Azure Backup kurtarma hizmetleri Kasası, kasala aynı bölgedeki tüm düğümleri algılayabilir ve koruyabilir. SQL Server Always on kullanılabilirlik grubunuz birden çok Azure bölgesine yayılmışsa, birincil düğümü olan bölgeden Yedeklemeyi ayarlayın. Azure Backup, yedekleme tercihlerinize göre kullanılabilirlik grubundaki tüm veritabanlarını algılayabilir ve koruyabilir. Yedekleme tercihiniz karşılanmazsa yedeklemeler başarısız olur ve hata uyarısını alırsınız.

## <a name="do-successful-backup-jobs-create-alerts"></a>Başarılı yedekleme işleri uyarı oluştursın mı?

Hayır. Başarılı yedekleme işleri uyarı oluşturmaz. Uyarılar yalnızca başarısız olan yedekleme işleri için gönderilir. Portal uyarıları için ayrıntılı davranış [burada](backup-azure-monitoring-built-in-monitor.md)belgelenmiştir. Ancak, bu işlemleri başarılı işler için bile izleyebilirsiniz, [Azure izleyici 'yi kullanarak izlemeyi](backup-azure-monitoring-use-azuremonitor.md)kullanabilirsiniz.

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Yedekleme Işleri menüsünde zamanlanmış yedekleme işlerini görebilir miyim?

**Yedekleme işi** menüsü yalnızca isteğe bağlı yedekleme işlerini gösterir. Zamanlanan iş için [Azure izleyici 'yi kullanarak izlemeyi](backup-azure-monitoring-use-azuremonitor.md)kullanın.

## <a name="are-future-databases-automatically-added-for-backup"></a>Gelecekteki veritabanları yedekleme için otomatik olarak eklendi mi?

Evet, bu özelliği [otomatik koruma](backup-sql-server-database-azure-vms.md#enable-auto-protection)ile elde edebilirsiniz.  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Bir veritabanını bir yeniden korunan örnekten silersem, yedeklemelere ne olur?

Bir veritabanı, bir yeniden korunan örnekten bırakılırsa, veritabanı yedeklemeleri hala denenir. Bu, silinen veritabanının **yedekleme öğeleri** altında sağlıksız olarak gösterilmeye başladığı ve hala korunduğu anlamına gelir.

Bu veritabanını korumayı durdurmak için doğru yol, bu veritabanındaki **silme verileriyle** **yedeklemeyi durdurmaktır** .  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Otomatik korumalı bir veritabanının yedekleme işlemini durdurdum, davranışı ne olur?

**Verileri koruma ile yedeklemeyi durdurursanız**, gelecekteki yedeklemeler gerçekleşmez ve var olan kurtarma noktaları değişmeden kalır. Veritabanı hala korumalı olarak kabul edilir ve **yedekleme öğeleri**altında gösterilir.

**Silme verileriyle yedeklemeyi durdurursanız**, gelecekteki yedeklemeler gerçekleşmeyecektir ve var olan kurtarma noktaları da silinir. Veritabanı korunmuyor olarak değerlendirilir ve yapılandırma Yedeklemedeki örnek altında gösterilir. Bununla birlikte, el ile seçilemeyen veya bu veritabanı tarafından korunabilen diğer, korunan veritabanlarının aksine, bu veritabanı gri görünür ve seçilemez. Bu veritabanını yeniden korumanın tek yolu, örnekte otomatik korumayı devre dışı bırakmanız. Artık bu veritabanını seçebilir ve korumayı yapılandırabilir veya örnekte otomatik korumayı yeniden etkinleştirebilirsiniz.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Korunduktan sonra veritabanının adını değiştirdiğimde, davranış ne olur?

Yeniden adlandırılmış bir veritabanı yeni bir veritabanı olarak değerlendirilir. Bu nedenle, hizmet bu durumu veritabanı bulunmazsa ve yedeklemelerle başarısız olarak değerlendirir.

Şimdi yeniden adlandırılan veritabanını seçebilirsiniz ve üzerinde koruma yapılandırabilirsiniz. Örnekte otomatik korumanın etkin olması durumunda, yeniden adlandırılmış veritabanı otomatik olarak algılanır ve korunur.

## <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Neden yeniden korunan bir örnek için eklenen bir veritabanını göremiyorum?

[Bir oto korumalı örneğe eklediğiniz](backup-sql-server-database-azure-vms.md#enable-auto-protection) bir veritabanı, korunan öğeler altında hemen görünmeyebilir. Bunun nedeni, bulmanın genellikle her 8 saatte bir çalışır. Ancak, aşağıdaki görüntüde gösterildiği gibi, veritabanlarını **yeniden keşfet**' i seçerek bir bulmayı el ile çalıştırırsanız yeni veritabanlarını hemen bulabilir ve koruyabilirsiniz:

  ![Yeni eklenen bir veritabanını el ile bulma](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>Sonraki adımlar

Azure VM 'de çalışan [SQL Server bir veritabanını nasıl yedekleyeceğinizi](backup-azure-sql-database.md) öğrenin.
