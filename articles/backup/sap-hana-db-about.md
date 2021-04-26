---
title: Azure VM 'lerinde SAP HANA veritabanı yedeklemesi hakkında
description: Bu makalede, Azure sanal makinelerinde çalışan SAP HANA veritabanlarını yedekleme hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: efb9c3f786e429df404e261f053a9c9a9b032e11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96296463"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Azure VM 'lerinde SAP HANA veritabanı yedeklemesi hakkında

SAP HANA veritabanları, düşük kurtarma noktası hedefi (RPO) ve hızlı kurtarma süresi hedefi (RTO) gerektiren görev açısından kritik iş yükleridir. Artık, [Azure Backup](./backup-overview.md)kullanarak [Azure vm 'lerinde çalışan SAP HANA veritabanlarını yedekleyebilirsiniz](./tutorial-backup-sap-hana-db.md) .

Azure Backup, SAP tarafından, SAP HANA 'ın yerel API 'Lerinden yararlanarak yerel yedekleme desteği sağlamak için [biriktirme tarafından sertifikalandırilmiştir](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) . Azure Backup bu teklif, **sıfır altyapı** yedeklemelerinin Azure Backup mantarafıyla hizalanır, yedekleme altyapısını dağıtma ve yönetme ihtiyacını ortadan kaldırır. Artık Azure VM 'lerinde çalışan SAP HANA veritabanlarını sorunsuz bir şekilde yedekleyebilir ve geri yükleyebilirsiniz (artık[d serisi VM 'ler](../virtual-machines/m-series.md) de desteklenir!) ve Azure Backup sağladığı kurumsal yönetim özelliklerinden yararlanabilirsiniz.

## <a name="added-value"></a>Değer eklendi

SAP HANA veritabanlarını yedeklemek ve geri yüklemek için Azure Backup kullanarak aşağıdaki avantajları sağlar:

* **15 dakikalık kurtarma noktası hedefi (RPO)**: en fazla 15 dakikalık kritik verilerin kurtarılması artık mümkündür.
* **Tek tıklamayla, bir noktadan sonra geri yüklemeler**: üretim VERILERININ alternatif Hana sunucularına geri yüklenmesi kolay hale getirilir. Yedeklemenin ve katalogların yedeklenme zincirlerinin yedeklenme işlemi, Azure tarafından arka planda yönetilir.
* **Uzun süreli saklama**: sıkı uyumluluk ve denetim ihtiyaçları için. Kurtarma noktalarının yerleşik yaşam döngüsü yönetimi özelliği tarafından otomatik olarak ayıklanacağı saklama süresine bağlı olarak, yedeklemelerinizi yıllarca koruyun.
* **Azure 'Dan yedekleme yönetimi**: gelişmiş yönetim deneyimi için Azure Backup yönetim ve izleme yeteneklerini kullanın. Azure CLı de desteklenir.

Günümüzde destekdiğimiz yedekleme ve geri yükleme senaryolarını görüntülemek için [SAP HANA senaryo desteği matrisine](./sap-hana-backup-support-matrix.md#scenario-support)bakın.

## <a name="backup-architecture"></a>Backup mimarisi

![Yedekleme mimarisi diyagramı](./media/sap-hana-db-about/backup-architecture.png)

* Yedekleme işlemi, Azure 'da [bir kurtarma hizmetleri Kasası oluşturarak](./tutorial-backup-sap-hana-db.md#create-a-recovery-services-vault) başlar. Bu kasa, zaman içinde oluşturulan yedeklemeleri ve kurtarma noktalarını depolamak için kullanılacaktır.
* SAP HANA Server çalıştıran Azure VM kasayla kaydedilir ve yedeklenecek veritabanları [bulunur.](./tutorial-backup-sap-hana-db.md#discover-the-databases) Azure Backup hizmetinin veritabanlarını bulmasına olanak tanımak için, bir [preregistration betiğinin](https://aka.ms/scriptforpermsonhana) , bir kök kullanıcı olarak Hana sunucusunda çalıştırılması gerekir.
* Bu betik, **AZUREWLBACKUPHANAUSER** DB kullanıcısı ve **hdbuserstore**'da aynı ada sahip karşılık gelen bir anahtar oluşturur. Betiğin ne yaptığını hakkında daha fazla bilgi edinmek için  [ön kayıt betiğinin ne olduğunu](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) öğrenin bölümüne bakın.
* Azure Backup hizmeti artık, kayıtlı SAP HANA sunucusuna **Hana için Azure Backup eklentisini** yüklüyor.
* Preregistration betiği tarafından oluşturulan **AZUREWLBACKUPHANAUSER** DB kullanıcısı, tüm yedekleme ve geri yükleme işlemlerini GERÇEKLEŞTIRMEK üzere **Hana için Azure Backup eklentisi** tarafından kullanılır. Bu betiği çalıştırmadan SAP HANA DBs için yedeklemeyi yapılandırmaya çalışırsanız, şu hatayı alabilirsiniz: **Usererrorhanascriptnotrun**.
* Bulunan veritabanlarında [yedeklemeyi yapılandırmak](./tutorial-backup-sap-hana-db.md#configure-backup) için gerekli yedekleme ilkesini seçin ve yedeklemeleri etkinleştirin.

* Yedekleme yapılandırıldıktan sonra Azure Backup hizmeti, korunan SAP HANA sunucusundaki VERITABANı düzeyinde aşağıdaki Backınt parametrelerini ayarlar:
  * [catalog_backup_using_backint: true]
  * [enable_accumulated_catalog_backup: false]
  * [parallel_data_backup_backint_channels: 1]
  * [log_backup_timeout_s: 900)]
  * [backint_response_timeout: 7200]

>[!NOTE]
>Bu parametrelerin konak düzeyinde mevcut *olmadığından* emin olun. Ana bilgisayar düzeyindeki Parametreler bu parametreleri geçersiz kılacak ve beklenmeyen davranışlara neden olabilir.
>

* **Hana için Azure Backup eklentisi** tüm yedekleme zamanlamalarını ve ilke ayrıntılarını saklar. Zamanlanan yedeklemeleri tetikler ve Backınt API 'Leri aracılığıyla **Hana yedekleme altyapısıyla** iletişim kurar.
* **Hana yedekleme altyapısı** , yedeklenecek verilerle birlikte bir backınt akışı döndürür.
* Tam veya fark olan tüm zamanlanmış yedeklemeler ve isteğe bağlı yedeklemeler (Azure portal tetiklenir), **Hana için Azure Backup eklentisi** tarafından başlatılır. Ancak, günlük yedeklemeleri **Hana yedekleme altyapısının** kendisi tarafından yönetilir ve tetiklenir.
* SAP HANA için Azure Backup, bir BackInt sertifikalı çözüm olması, temel diske veya VM türlerine bağlı değildir. Yedekleme, HANA tarafından oluşturulan akışlar tarafından gerçekleştirilir.

## <a name="using-azure-vm-backup-with-azure-sap-hana-backup"></a>Azure SAP HANA yedekleme ile Azure VM yedeklemesi kullanma

Azure 'da veritabanı düzeyinde yedekleme ve kurtarma sağlayan SAP HANA yedeklemesini kullanmanın yanı sıra, işletim sistemi ve veritabanı olmayan diskleri yedeklemek için Azure VM yedekleme çözümünü kullanabilirsiniz.

[Backınt sertifikalı Azure SAP HANA yedekleme çözümü](#backup-architecture) veritabanı yedekleme ve kurtarma için kullanılabilir.

[Azure VM yedeklemesi](backup-azure-vms-introduction.md) , işletim sistemini ve diğer veritabanı olmayan diskleri yedeklemek için kullanılabilir. VM yedeklemesi her gün bir kez alınır ve tüm diskleri ( **yazma hızlandırıcısı (WA) işletim sistemi diskleri** ve **Ultra diskler** dışında) yedekler. Veritabanı Azure SAP HANA yedekleme çözümü kullanılarak yedeklendiğinden, [Seçmeli disk yedekleme ve Azure VM 'leri için geri yükleme](selective-disk-backup-restore.md) özelliğini kullanarak yalnızca işletim sistemi ve veritabanı olmayan disklerin dosya ile tutarlı bir yedeklemesini gerçekleştirebilirsiniz.

SAP HANA çalıştıran bir VM 'yi geri yüklemek için şu adımları izleyin:

* [Azure VM yedeğinden yeni BIR VM](backup-azure-arm-restore-vms.md) 'yi en son kurtarma noktasından geri yükleyin. Veya yeni bir boş VM oluşturun ve diskleri en son kurtarma noktasından bağlayın.
* WA diskleri dışlanmazsa, geri yüklenmez. Bu durumda boş WA diskleri ve günlük alanı oluşturun.
* Diğer tüm yapılandırmaların (IP, sistem adı, vb.) ayarlanabilmesi için VM, Azure Backup 'ten DB verisi alacak şekilde ayarlanır.
* Şimdi VERITABANıNı [Azure SAP HANA DB YEDEĞINDEN](sap-hana-db-restore.md#restore-to-a-point-in-time-or-to-a-recovery-point) sanal makineye istediğiniz zaman noktasına geri yükleyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure VM 'de çalışan bir SAP HANA veritabanını nasıl geri](./sap-hana-db-restore.md) yükleyeceğinizi öğrenin
* [Kullanarak yedeklenen SAP HANA veritabanlarını yönetmeyi öğrenin Azure Backup](./sap-hana-db-manage.md)
