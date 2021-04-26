---
title: Yedekleme Merkezi'ni kullanarak yedeklemeleri izleme ve çalıştırma
description: Bu makalede yedekleme merkezi kullanılarak yedeklemelerin nasıl izleneceği ve çalıştırılacağı açıklanmaktadır
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 86b81110d6abeb1425e18ee45dfe65a96f69687d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102506172"
---
# <a name="monitor-and-operate-backups-using-backup-center"></a>Yedekleme merkezi 'ni kullanarak yedeklemeleri izleme ve çalıştırma

Yedekleme Yöneticisi olarak, iş ve yedekleme envanterinizi gün temelinde izlemek için yedekleme merkezini tek bir cam bölmesi olarak kullanabilirsiniz. Ayrıca, isteğe bağlı yedekleme isteklerine yanıt verme, yedeklemeleri geri yükleme, yedekleme ilkeleri oluşturma gibi düzenli işlemleri gerçekleştirmek için yedekleme merkezi 'ni de kullanabilirsiniz.

## <a name="supported-scenarios"></a>Desteklenen senaryolar

* Yedekleme merkezi şu anda Azure VM yedeklemesi, Azure SAP HANA 'da SQL VM yedeklemesi, Azure VM yedeklemesi, Azure dosya yedekleme, Azure Blobları yedekleme, Azure yönetilen diskler Yedekleme ve PostgreSQL için Azure veritabanı sunucu yedeklemesi için desteklenmektedir.
* Desteklenen ve desteklenmeyen senaryoların ayrıntılı bir listesi için [destek matrisine](backup-center-support-matrix.md) bakın.

## <a name="backup-instances"></a>Yedekleme örnekleri

Yedekleme merkezi, yedeklemeniz genelinde yedekleme örneklerinin kolay bir şekilde aranmasını ve keşfedilmesini sağlar.

Yedekleme merkezi 'nde **yedekleme örnekleri** sekmesinin seçilmesi, erişiminiz olan tüm yedekleme örneklerinin ayrıntılarını görüntülemenize olanak sağlar.

 Yedekleme örneklerinizin her biri hakkında aşağıdaki bilgileri görebilirsiniz:

* Veri kaynağı aboneliği
* Veri kaynağı kaynak grubu
* En son kurtarma noktası
* Yedekleme örneğiyle ilişkili kasa
* Koruma durumu

 Ayrıca, aşağıdaki parametrelerde yedekleme örnekleri listesini de filtreleyebilirsiniz:

* Veri kaynağı aboneliği
* Veri kaynağı kaynak grubu
* Veri kaynağı konumu
* Veri kaynağı türü
* Kasa
* Koruma durumu
* Veri kaynağı etiketleri

Kılavuzdaki öğelerin herhangi birine sağ tıklanması, kaynak üzerinde gezinme, isteğe bağlı yedeklemeleri tetikleme ve geri yükleme ya da yedeklemeyi durdurma gibi, verilen yedekleme örneğinde eylemler gerçekleştirmenizi sağlar.

![Yedekleme merkezi-örnekler](./media/backup-center-monitor-operate/backup-center-instances.png)

## <a name="backup-jobs"></a>Yedekleme işleri

Yedekleme merkezi, yedeklemelerinizde oluşturulan tüm işlerle ilgili ayrıntılı bilgileri görüntülemenize ve başarısız işler için uygun işlemleri yapmanıza olanak tanır.

Yedekleme merkezi 'nde **yedekleme işleri** menü öğesini seçmek, tüm işlerinizin bir görünümünü sağlar. Her iş aşağıdaki bilgileri içerir:

* İşle ilişkili yedekleme örneği
* Veri kaynağı aboneliği
* Veri kaynağı kaynak grubu
* Veri kaynağı konumu
* İş işlemi
* İş durumu
* İşin başlangıç saati
* İş süresi

Kılavuzdaki bir öğenin seçilmesi, belirtilen iş hakkında daha fazla ayrıntı görüntülemenizi sağlar. Bir öğeye sağ tıklandığında gerekli işlemleri gerçekleştirmek için kaynağa gitmeniz yardımcı olur.

![Yedekleme merkezi-Işler](./media/backup-center-monitor-operate/backup-center-jobs.png)

**Yedekleme işleri** sekmesini kullanarak en son yedi güne kadar iş görüntüleyebilirsiniz. Eski işleri görüntülemek için [yedekleme raporları](backup-center-obtain-insights.md)' nı kullanın.

## <a name="vaults"></a>Kasalar

Yedekleme merkezi 'nde **kasa** menü öğesini seçmek, erişiminiz olan tüm [Kurtarma Hizmetleri kasalarının ve yedek kasalarının](backup-azure-recovery-services-vault-overview.md) bir listesini görmenizi [](backup-vault-overview.md) sağlar. Aşağıdaki parametrelerle listeye filtre uygulayabilirsiniz:

* Kasa aboneliği
* Kasa kaynak grubu
* Kasa adı
* İlkeyle ilişkili veri kaynağı türü

Listedeki herhangi bir öğeyi seçmek, belirli bir kasaya gitmenizi sağlar.

![Yedekleme merkezi-kasa](./media/backup-center-monitor-operate/backup-center-vaults.png)

## <a name="backup-policies"></a>Yedekleme ilkeleri

Yedekleme merkezi, yedekleme ilkelerinizin herhangi birine ilişkin önemli bilgileri görüntülemenize ve düzenlemenize olanak tanır.

**Yedekleme ilkeleri** menü öğesinin seçilmesi, yedeklemeniz genelinde oluşturduğunuz tüm ilkeleri görüntülemenize olanak sağlar. Listeyi kasa aboneliğine, kaynak grubuna, veri kaynağı türüne ve kasaya göre filtreleyebilirsiniz. Kılavuzdaki bir öğeye sağ tıklamak, bu ilke için ilişkili öğeleri görüntülemenize, ilkeyi düzenlemenize ve hatta gerekirse silmenize olanak sağlar.

![Yedekleme merkezi-Ilkeler](./media/backup-center-monitor-operate/backup-center-policies.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Yedeklemenizi yönetin](backup-center-govern-environment.md)
* [Yedekleme merkezi 'ni kullanarak eylem gerçekleştirme](backup-center-actions.md)
* [Yedeklemeleriniz hakkında Öngörüler edinin](backup-center-obtain-insights.md)
