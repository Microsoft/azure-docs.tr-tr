---
title: Yedekleme merkezi 'ni kullanarak eylem gerçekleştirme
description: Bu makalede, yedekleme merkezi kullanılarak eylemlerin nasıl gerçekleştirileceği açıklanır.
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 8c21475e5a52cdce7e38bbeb9d00df3c3ac3a752
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102506499"
---
# <a name="perform-actions-using-backup-center"></a>Yedekleme merkezi 'ni kullanarak eylem gerçekleştirme

Yedekleme merkezi, tek bir kasaya gitmek zorunda kalmadan merkezi bir arabirimden ilgili anahtar yedeklemeyle ilgili eylemleri gerçekleştirmenize olanak tanır. Yedekleme merkezi 'nden gerçekleştirebileceğiniz bazı eylemler şunlardır:

* Veri kaynaklarınız için yedeklemeyi yapılandırma
* Bir yedekleme örneğini geri yükleme
* Yeni kasa oluştur
* Yeni bir yedekleme İlkesi Oluştur
* Bir yedekleme örneği için isteğe bağlı yedekleme tetikleyin
* Yedekleme örneği için Yedeklemeyi Durdur

## <a name="supported-scenarios"></a>Desteklenen senaryolar

* Yedekleme merkezi şu anda Azure VM yedeklemesi, Azure SAP HANA 'da SQL VM yedeklemesi, Azure VM yedeklemesi, Azure dosyaları yedeklemesi ve PostgreSQL için Azure veritabanı sunucu yedeklemesi için desteklenmektedir.
* Desteklenen ve desteklenmeyen senaryoların ayrıntılı bir listesi için [destek matrisine](backup-center-support-matrix.md) bakın.

## <a name="configure-backup"></a>Yedeklemeyi yapılandırma

Azure VM 'leri, Azure VM 'leri, Azure VM 'lerde veya Azure dosyalarında SAP HANA yedekliyorsanız, bir kurtarma hizmetleri Kasası kullanmanız gerekir. PostgreSQL sunucusu için Azure veritabanlarını yedekliyorsanız, bir yedekleme Kasası kullanmanız gerekir. 

Yedeklemek istediğiniz veri kaynağı türüne bağlı olarak, aşağıda açıklandığı gibi uygun yönergeleri izleyin.

### <a name="configure-backup-to-a-recovery-services-vault"></a>Yedeklemeyi bir kurtarma hizmetleri kasasına yapılandırma

1. Yedekleme merkezi ' ne gidin ve **genel bakış** sekmesinin en üstündeki **+ Backup** ' ı seçin.

    ![Yedekleme merkezi 'ne genel bakış](./media/backup-center-actions/backup-center-overview-configure-backup.png)

2. Yedeklemek istediğiniz veri kaynağı türünü seçin.

    ![VM yedeklemesini yapılandırmak için veri kaynağını seçin](./media/backup-center-actions/backup-select-datasource-vm.png)

3. Bir kurtarma hizmetleri Kasası seçin ve **devam**' ı seçin. Bu, kurtarma hizmetleri kasasından erişilebilen bir yedekleme yapılandırma deneyimine yol açar. [Kurtarma Hizmetleri kasasıyla Azure sanal makineleri için yedeklemeyi yapılandırma hakkında daha fazla bilgi edinin](tutorial-backup-vm-at-scale.md).

### <a name="configure-backup-to-a-backup-vault"></a>Yedeklemeyi bir yedekleme kasasına yapılandırma

1. Yedekleme merkezi ' ne gidin ve **genel bakış** sekmesinin en üstündeki **+ Backup** ' ı seçin.
2. Yedeklemek istediğiniz veri kaynağı türünü seçin (Bu durumda PostgreSQL için Azure veritabanı sunucusu).

    ![PostgreSQL için Azure veritabanı sunucu yedeklemesini yapılandırmak için veri kaynağını seçin](./media/backup-center-actions/backup-select-datasource-type-postgresql.png)

3. **Devam**' ı seçin. Bu, bir yedekleme kasasından erişilebilen bir yedekleme yapılandırma deneyimine yol açar. [Yedekleme Kasası Ile PostgreSQL Için Azure veritabanı sunucusu için yedeklemeyi yapılandırma hakkında daha fazla bilgi edinin](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases).

## <a name="restore-a-backup-instance"></a>Bir yedekleme örneğini geri yükleme

Geri yüklemek istediğiniz veri kaynağı türüne bağlı olarak, aşağıda açıklandığı gibi uygun yönergeleri izleyin.

### <a name="if-youre-restoring-from-a-recovery-services-vault"></a>Bir kurtarma hizmetleri kasasından geri yükleme yapıyorsanız

1. Yedekleme merkezi ' ne gidin ve **genel bakış** sekmesinin en üstünde **geri yükle** ' yi seçin.

    ![VM 'yi geri yüklemek için yedekleme merkezi 'Ne genel bakış](./media/backup-center-actions/backup-center-overview-restore.png)

2. Geri yüklemek istediğiniz veri kaynağı türünü seçin.

    ![VM geri yükleme için veri kaynağını seçin](./media/backup-center-actions/restore-select-datasource-vm.png)

3. Bir yedekleme örneği seçin ve **devam**' ı seçin. Bu, kurtarma hizmetleri kasasından erişilebilen bir ile aynı olan geri yükleme ayarları deneyimine yol açar. Bir [Azure sanal makinesini kurtarma hizmetleri kasasıyla geri yükleme hakkında daha fazla bilgi edinin](backup-azure-arm-restore-vms.md#before-you-start).

### <a name="if-youre-restoring-from-a-backup-vault"></a>Bir yedekleme kasasından geri yükleme yapıyorsanız

1. Yedekleme merkezi ' ne gidin ve **genel bakış** sekmesinin en üstünde **geri yükle** ' yi seçin.
2. Geri yüklemek istediğiniz veri kaynağı türünü seçin (Bu durumda PostgreSQL için Azure veritabanı sunucusu).

    ![PostgreSQL için Azure veritabanı sunucusunun geri yüklenmesi için veri kaynağı seçin](./media/backup-center-actions/restore-select-datasource-postgresql.png)

3. Bir yedekleme örneği seçin ve **devam**' ı seçin. Bu, kurtarma hizmetleri kasasından erişilebilen bir ile aynı olan geri yükleme ayarları deneyimine yol açar. [Bir PostgreSQL Için Azure veritabanı sunucusunu yedekleme kasasıyla geri yükleme hakkında daha fazla bilgi edinin](backup-azure-database-postgresql.md#restore).

## <a name="create-a-new-vault"></a>Yeni kasa oluştur

Yedekleme merkezi ' ne giderek ve **genel bakış** sekmesinin en üstünde **+ kasa** ' yı seçerek yeni bir kasa oluşturabilirsiniz.

![Kasa oluştur](./media/backup-center-actions/backup-center-create-vault.png)

* [Kurtarma Hizmetleri Kasası oluşturma hakkında daha fazla bilgi edinin](backup-create-rs-vault.md)
* [Yedekleme Kasası oluşturma hakkında daha fazla bilgi edinin](backup-vault-overview.md)

## <a name="create-a-new-backup-policy"></a>Yeni bir yedekleme İlkesi Oluştur

Yedeklemek istediğiniz veri kaynağı türüne bağlı olarak, aşağıda açıklanan ilgili yönergeleri izleyin.

### <a name="if-youre-backing-up-to-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasasına yedekleme yapıyorsanız

1. Yedekleme merkezi ' ne gidin ve **genel bakış** sekmesinin en üstündeki **+ ilke** ' yi seçin.

    ![Yedekleme ilkesi için yedekleme merkezi 'Ne genel bakış](./media/backup-center-actions/backup-center-overview-policy.png)

2. Yedeklemek istediğiniz veri kaynağı türünü seçin.

    ![VM yedeklemesi için ilke için veri kaynağı seçin](./media/backup-center-actions/policy-select-datasource-vm.png)

3. Bir kurtarma hizmetleri Kasası seçin ve **devam**' ı seçin. Bu, kurtarma hizmetleri kasasından erişilebilen bir ilke oluşturma deneyimine yol açar. [Kurtarma Hizmetleri kasasıyla Azure sanal makinesi için yeni bir yedekleme ilkesi oluşturma hakkında daha fazla bilgi edinin](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

### <a name="if-youre-backing-up-to-a-backup-vault"></a>Bir yedekleme kasasına yedekleme yapıyorsanız

1. Yedekleme merkezi ' ne gidin ve **genel bakış** sekmesinin en üstündeki **+ ilke** ' yi seçin.
2. Yedeklemek istediğiniz veri kaynağı türünü seçin (Bu durumda PostgreSQL için Azure veritabanı sunucusu).

    ![PostgreSQL için Azure veritabanı sunucu yedeklemesi ilkesi için veri kaynağı seçin](./media/backup-center-actions/policy-select-datasource-postgresql.png)

3. **Devam**' ı seçin. Bu, bir yedekleme kasasından erişilebilen bir ilke oluşturma deneyimine yol açar. [Yedekleme Kasası ile yeni bir yedekleme ilkesi oluşturma hakkında daha fazla bilgi edinin](backup-azure-database-postgresql.md#create-backup-policy).

## <a name="execute-an-on-demand-backup-for-a-backup-instance"></a>Yedekleme örneği için isteğe bağlı yedekleme yürütme

Yedekleme merkezi, yedeklemeniz genelinde yedekleme örnekleri aramanızı ve isteğe bağlı yedekleme işlemleri yürütmelerine olanak sağlar.

İsteğe bağlı bir yedeklemeyi tetiklemek için yedekleme merkezi ' ne gidin ve **yedekleme örnekleri** menü öğesini seçin. Bunun belirlenmesi, erişiminiz olan tüm yedekleme örneklerinin ayrıntılarını görüntülemenizi sağlar. Yedeklemek istediğiniz yedekleme örneği için arama yapabilirsiniz. Kılavuzdaki bir öğeye sağ tıklandığında, kullanılabilir eylemlerin bir listesi açılır. İsteğe bağlı yedeklemeyi yürütmek için **Şimdi Yedekle** seçeneğini belirleyin.

![İsteğe bağlı yedekleme](./media/backup-center-actions/backup-center-on-demand-backup.png)

[Azure sanal makineleri için isteğe bağlı yedeklemeler gerçekleştirme hakkında daha fazla bilgi edinin](backup-azure-manage-vms.md#run-an-on-demand-backup).

[PostgreSQL Için Azure veritabanı sunucusu için isteğe bağlı yedeklemeleri gerçekleştirme hakkında daha fazla bilgi edinin](backup-azure-database-postgresql.md#on-demand-backup).

## <a name="stop-backup-for-a-backup-instance"></a>Yedekleme örneği için Yedeklemeyi Durdur

Yedeklenmekte olan temel kaynağın artık mevcut olmadığı gibi, bir yedekleme örneği için yedeklemeyi durdurmak isteyebileceğiniz senaryolar vardır.

İsteğe bağlı bir yedeklemeyi tetiklemek için yedekleme merkezi ' ne gidin ve **yedekleme örnekleri** menü öğesini seçin. Bu seçeneği, erişiminiz olan tüm yedekleme örneklerinin ayrıntılarını görüntülemenizi sağlar. Yedeklemek istediğiniz yedekleme örneği için arama yapabilirsiniz. Kılavuzdaki bir öğeye sağ tıklandığında, kullanılabilir eylemlerin bir listesi açılır. Yedekleme örneği için yedeklemeyi durdurmak üzere **Yedeklemeyi Durdur** seçeneğini belirleyin.

![Korumayı durdurma](./media/backup-center-actions/backup-center-stop-protection.png)

[Azure sanal makineleri için yedeklemeyi durdurma hakkında daha fazla bilgi edinin](backup-azure-manage-vms.md#stop-protecting-a-vm).

[PostgreSQL için Azure veritabanı sunucusuna yedeklemeyi durdurma hakkında daha fazla bilgi edinin](backup-azure-database-postgresql.md#stop-protection)

## <a name="next-steps"></a>Sonraki adımlar

* [Yedeklemeleri izleme ve çalıştırma](backup-center-monitor-operate.md)
* [Yedeklemenizi yönetin](backup-center-govern-environment.md)
* [Yedeklemeleriniz hakkında Öngörüler edinin](backup-center-obtain-insights.md)
