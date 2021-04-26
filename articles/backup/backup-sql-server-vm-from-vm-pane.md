---
title: VM bölmesinden bir SQL Server VM yedekleme
description: Bu makalede, VM bölmesinden Azure sanal makinelerinde SQL Server veritabanlarını nasıl yedekleyeceğinizi öğrenin.
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: 4f4ea202ee96e93a621c8dd0025c9ebc8b8d445d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88891666"
---
# <a name="back-up-a-sql-server-from-the-vm-pane"></a>VM bölmesinden bir SQL Server yedekleme

Bu makalede, [Azure Backup](backup-overview.md) hizmeti Ile Azure VM 'lerinde çalışan SQL Server nasıl yedekleyeceğiniz açıklanmaktadır. SQL Server VM 'Leri, iki yöntemi kullanarak yedekleyebilirsiniz:

- Tek SQL Server Azure VM: Bu makaledeki yönergeler, bir SQL Server VM doğrudan VM görünümünden nasıl yedekleyeceğiniz açıklanır.
- Birden çok SQL Server Azure VM: bir kurtarma hizmetleri Kasası ayarlayabilir ve birden çok VM için yedeklemeyi yapılandırabilirsiniz. Bu senaryo için [Bu makaledeki](backup-sql-server-database-azure-vms.md) yönergeleri izleyin.

## <a name="before-you-start"></a>Başlamadan önce

1. [Destek matrisi](sql-support-matrix.md)ile ortamınızı doğrulayın.
2. SQL Server VM için Azure Backup bir [genel bakış](backup-azure-sql-database.md) alın.
3. VM 'nin [ağ bağlantısı](backup-sql-server-database-azure-vms.md#establish-network-connectivity)olduğunu doğrulayın.

## <a name="configure-backup-on-the-sql-server"></a>SQL Server yedeklemeyi yapılandırma

SQL Server VM yedeklemeyi VM 'deki **yedekleme** bölmesinden etkinleştirebilirsiniz. Bu yöntem iki şeyi yapar:

- SQL VM 'yi Azure Backup hizmetine kaydederek erişim izni verin.
- , Sanal makine içinde çalışan tüm SQL Server örneklerini oto korur. Bu, yedekleme ilkesinin tüm mevcut veritabanlarına ve gelecekte bu örneklere eklenecek veritabanlarına uygulandığı anlamına gelir.

1. SQL Server yedekleme görünümünü açmak için sayfanın üst kısmındaki başlık ' ı seçin.

    ![SQL Server yedekleme görünümü](./media/backup-sql-server-vm-from-vm-pane/sql-server-backup-view.png)

    >[!NOTE]
    >Başlığı göremiyor musunuz? Başlık yalnızca Azure Market görüntüleri kullanılarak oluşturulan SQL Server VM 'Ler için görüntülenir. Ayrıca, Azure VM yedeklemesi ile korunan VM 'Ler için de görüntülenir. Diğer görüntüler için, [burada](backup-sql-server-database-azure-vms.md)açıklandığı gibi yedeklemeyi yapılandırabilirsiniz.

2. Kurtarma Hizmetleri Kasası adını girin. Kasa, tüm yedeklemelerinizi depolamak ve yönetmek için bir mantıksal varlıktır. Yeni bir kasa oluşturuyorsanız:

    - Bu işlem, koruduğunuz SQL Server VM aynı abonelikte ve bölgede oluşturulur.
    - Tüm yedeklemeler için coğrafi olarak yedekli depolama (GRS) ayarıyla oluşturulacaktır. Artıklık türünü değiştirmek isterseniz, VM 'yi korumadan önce bunu yapmanız gerekir. Daha fazla bilgi için [bu makaleye](backup-create-rs-vault.md#set-storage-redundancy) bakın.

3. Bir **yedekleme ilkesi** seçin. Varsayılan ilkeden veya kasada oluşturduğunuz diğer mevcut ilkelerden birini seçebilirsiniz. Yeni bir ilke oluşturmak istiyorsanız, adım adım kılavuz için [Bu makaleye](backup-sql-server-database-azure-vms.md#create-a-backup-policy) başvurabilirsiniz.

    ![Yedekleme ilkesi seçme](./media/backup-sql-server-vm-from-vm-pane/backup-policy.png)

4. **Yedeklemeyi Etkinleştir**’i seçin. İşlemin tamamlanması birkaç dakika sürebilir.

    ![Yedeklemeyi Etkinleştir ' i seçin](./media/backup-sql-server-vm-from-vm-pane/enable-backup.png)

5. İşlem tamamlandıktan sonra başlık **adını** başlıkta görürsünüz.

    ![Kasa adı başlıkta görünür](./media/backup-sql-server-vm-from-vm-pane/vault-name.png)

6. Tüm kayıtlı VM 'Leri ve bunların koruma durumlarını görebileceğiniz kasa görünümüne gitmek için başlık ' ı seçin.

    ![Kayıtlı VM 'Ler içeren kasa görünümü](./media/backup-sql-server-vm-from-vm-pane/vault-view.png)

7. Market olmayan görüntüler için kayıt başarılı olabilir, ancak Azure Backup uzantıya SQL Server izin verilene kadar **yedeklemeyi yapılandırma** tetiklenemez. Bu gibi durumlarda, **yedek hazırlık** sütunu okuma Için **hazır değildir**. Yedekleme 'nin tetiklenmesi için [uygun izinleri](backup-azure-sql-database.md#set-vm-permissions) Market olmayan görüntüler için el ile atamanız gerekir.

    ![Yedekleme hazırlığı hazır değil](./media/backup-sql-server-vm-from-vm-pane/backup-readiness-not-ready.png)

8. Yedeklenmiş SQL Server VM yapmanız gereken daha fazla işlem veya izleme için, ilgili kurtarma hizmetleri kasasına gidin. Bu kasada yedeklenen tüm veritabanlarını görmek için **yedekleme öğeleri** ' ne gidin ve isteğe bağlı yedekleme ve geri yükleme gibi işlemleri tetikleyin. Benzer şekilde, koruma, yedekleme ve geri yükleme gibi işlemlere karşılık gelen işleri [izlemek](manage-monitor-sql-database-backup.md) Için **yedekleme işleri** ' ne gidin.

    ![Yedekleme öğelerinde yedeklenen veritabanlarına bakın](./media/backup-sql-server-vm-from-vm-pane/backup-items.png)

>[!NOTE]
>Yedekleme, daha sonra korunan sanal makineye eklenebilecek yeni SQL Server örneklerinden hiçbirinde otomatik olarak yapılandırılmaz. Yeni eklenen örneklerde yedeklemeyi yapılandırmak için, VM 'nin kayıtlı olduğu kasaya gitmeniz ve [burada](backup-sql-server-database-azure-vms.md)listelenen adımları izlemeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Şunları nasıl yapacağınızı öğrenin:

- [Yedeklenen SQL Server veritabanlarını geri yükleme](restore-sql-database-azure-vm.md)
- [Yedeklenen SQL Server veritabanlarını yönetme](manage-monitor-sql-database-backup.md)
