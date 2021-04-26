---
title: Azure yönetilen disklerini geri yükleme
description: Azure portal Azure yönetilen disklerini nasıl geri yükleyeceğinizi öğrenin.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: c57d60047a5bcef58c721ee25bd8a0b3ed523aa4
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517207"
---
# <a name="restore-azure-managed-disks"></a>Azure yönetilen disklerini geri yükleme

Bu makalede, [Azure yönetilen disklerinin](../virtual-machines/managed-disks-overview.md) Azure Backup tarafından oluşturulan bir geri yükleme noktasından nasıl geri yükleneceği açıklanmaktadır.

Şu anda, yedeklemelerin alındığı kaynak diski değiştirerek geri yükleme Original-Location kurtarma (OLR) seçeneği desteklenmez. Aynı kaynak grubunda yedeklerin alındığı kaynak diskle veya başka bir kaynak grubunda yeni bir disk oluşturmak için bir kurtarma noktasından geri yükleme yapabilirsiniz. Bu, Alternate-Location kurtarma (ALR) olarak bilinir ve bu, hem kaynak diski hem de geri yüklenen (yeni) diski tutmaya yardımcı olur.

Bu makalede şunları yapmayı öğreneceksiniz:

- Yeni bir disk oluşturmak için geri yükleme

- Geri yükleme işlemi durumunu izleme

## <a name="restore-to-create-a-new-disk"></a>Yeni bir disk oluşturmak için geri yükleme

Yedekleme Kasası, diğer Azure kaynaklarına erişmek için yönetilen kimlik kullanır. Yedekten geri yüklemek için, yedekleme kasasının yönetilen kimliği, diskin geri yükleneceği kaynak grubunda bir izin kümesi gerektirir.

Yedekleme Kasası, kaynak başına bir tane ile kısıtlanan ve bu kaynağın yaşam döngüsüne bağlı olan sistem tarafından atanmış bir yönetilen kimlik kullanır. Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak yönetilen kimliğe izin verebilirsiniz. Yönetilen kimlik, yalnızca Azure kaynaklarıyla kullanılabilecek özel bir türün hizmet sorumlusundan oluşur. [Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md)hakkında daha fazla bilgi edinin.

Geri yükleme işlemi gerçekleştirmek için aşağıdaki önkoşulların olması gerekir:

1. Diskin Azure Backup hizmeti tarafından geri yükleneceği kaynak grubundaki yedekleme kasasının yönetilen kimliğine **disk geri yükleme işletmeni** rolünü atayın.

    >[!NOTE]
    > Yedeklemelerin alındığı kaynak diskle aynı kaynak grubunu veya aynı ya da farklı bir abonelik içindeki başka bir kaynak grubunu seçebilirsiniz.

    1. Diskin geri yükleneceği kaynak grubuna gidin. Örneğin, kaynak grubu *Targetrg* olur.

    1. **Access Control (IAM)** sayfasına gidin ve **rol atamaları Ekle** ' yi seçin.

    1. Sağ bağlam bölmesinde, **rol** açılan listesinden **disk geri yükleme işleci** ' ni seçin. Yedekleme kasasının yönetilen kimliğini seçin ve **kaydedin**.

        >[!TIP]
        >Kasasının yönetilen kimliğini seçmek için yedekleme kasasının adını yazın.

        ![Disk geri yükleme işletmeni rolünü seçin](./media/restore-managed-disks/disk-restore-operator-role.png)

1. Yedekleme kasasının yönetilen kimliğinin, diskin geri yükleneceği kaynak grubunda doğru rol atamaları kümesine sahip olduğunu doğrulayın.

    1. **Yedekleme Kasası-> kimliği** ' ne gidin ve **Azure rolü atamaları** ' nı seçin

        ![Azure rol atamalarını seçin](./media/restore-managed-disks/azure-role-assignments.png)

    1. Rolün, kaynak adının ve kaynak türünün doğru göründüğünü doğrulayın.

        ![Rol, kaynak adı ve kaynak türünü doğrulama](./media/restore-managed-disks/verify-role.png)

    >[!NOTE]
    >Rol atamaları portalda doğru şekilde yansıtıldığından, izinlerin yedekleme kasasının yönetilen kimliğine uygulanması yaklaşık 15 dakika sürebilir.
    >
    >Zamanlanmış yedeklemeler veya isteğe bağlı yedekleme işlemi sırasında, Azure Backup disk yedeklemesini yapılandırma sırasında belirtilen anlık görüntü kaynak grubundaki disk Artımlı anlık görüntülerini depolar. Azure Backup, geri yükleme işlemi sırasında bu artımlı anlık görüntüleri kullanır. Anlık görüntüler, anlık görüntü kaynak grubundan silinir veya taşınabilir veya anlık görüntü kaynak grubundaki Yedekleme Kasası rolü atamaları iptal edildiğinde, geri yükleme işlemi başarısız olur.

1. Geri yüklenecek disk, [müşteri tarafından yönetilen anahtarlarla (CMK)](../virtual-machines/disks-enable-customer-managed-keys-portal.md) şifrelendiyse veya [platform tarafından yönetilen anahtarlar ve müşteri tarafından yönetilen anahtarlar kullanılarak Çift şifreleme](../virtual-machines/disks-enable-double-encryption-at-rest-portal.md)kullanıyorsa, bu durumda, **disk şifreleme kümesi** kaynağındaki yedekleme kasasının yönetilen kimliğine **okuyucu** rolü iznini atayın.

Önkoşullar karşılandığında, geri yükleme işlemini gerçekleştirmek için aşağıdaki adımları izleyin.

1. [Azure Portal](https://portal.azure.com/) **yedekleme merkezi**' ne gidin. **Yönet** bölümünün altında **yedekleme örnekleri** ' ni seçin. Yedekleme örnekleri listesinden, geri yükleme işlemini gerçekleştirmek istediğiniz disk yedekleme örneğini seçin.

    ![Yedekleme örneklerinin listesi](./media/restore-managed-disks/backup-instances.png)

    Alternatif olarak, diskin yedeklemesini yapılandırmak için kullandığınız yedekleme kasasından bu işlemi gerçekleştirebilirsiniz.

1. **Yedekleme örneği** ekranında, geri yükleme işlemini gerçekleştirmek için kullanmak istediğiniz geri yükleme noktasını seçin ve **geri yükle**' yi seçin.

    ![Geri yükleme noktası seç](./media/restore-managed-disks/select-restore-point.png)

1. **Geri yükleme** iş akışında, **temelleri** gözden geçirin ve **Kurtarma noktası** sekmesi bilgileri ' ni seçin ve Ileri ' yi seçin **: parametreleri geri yükle**.

    ![Temelleri gözden geçirin ve kurtarma noktası bilgilerini seçin](./media/restore-managed-disks/review-information.png)

1. **Parametreleri geri yükle** sekmesinde, yedeği geri yüklemek istediğiniz **hedef aboneliği** ve **hedef kaynak grubunu** seçin. Geri yüklenecek diskin adını belirtin. **İleri ' yi seçin: gözden geçir ve geri yükle**.

    ![Parametreleri geri yükle](./media/restore-managed-disks/restore-parameters.png)

    >[!TIP]
    >Disk yedekleme çözümü kullanılarak Azure Backup yedeklenen diskler, kurtarma hizmetleri kasası ile Azure VM yedekleme çözümü kullanılarak Azure Backup tarafından da yedeklenebilir. Bu diskin eklendiği Azure VM korumasını yapılandırdıysanız, Azure VM geri yükleme işlemini de kullanabilirsiniz. VM 'yi veya disk dosyalarını veya klasörleri karşılık gelen Azure VM yedekleme örneğinin kurtarma noktasından geri yüklemeyi seçebilirsiniz. Daha fazla bilgi için bkz. [Azure VM yedeklemesi](./about-azure-vm-restore.md).

1. Doğrulama başarılı olduktan sonra geri yükleme işlemini **başlatmak için geri yükle '** yi seçin.

    ![Geri yükleme işlemini Başlat](./media/restore-managed-disks/initiate-restore.png)

    >[!NOTE]
    > Geri yükleme işlemini tetikleyebilmeniz için doğrulamanın tamamlanması birkaç dakika sürebilir. Doğrulama başarısız olabilir:
    >
    > - **geri yüklenen disk adında** aynı ada sahip bir disk, **hedef kaynak grubunda** zaten var
    > - Yedekleme kasasının yönetilen kimliği, **hedef kaynak grubunda** geçerli rol atamalarına sahip değil
    > - Yedekleme kasasının yönetilen kimlik rolü atamaları, artımlı anlık görüntülerin depolandığı **anlık görüntü kaynak grubunda** iptal edilir
    > - Artımlı anlık görüntüler silinir veya anlık görüntü kaynak grubundan taşınırsa

Geri yükleme, geri yükleme işlemi sırasında sağlanmış olan hedef kaynak grubundaki seçili kurtarma noktasından yeni bir disk oluşturacak. Geri yüklenen diski mevcut bir sanal makinede kullanmak için daha fazla adım gerçekleştirmeniz gerekir:

- Geri yüklenen disk bir veri diskise, var olan bir diski bir sanal makineye ekleyebilirsiniz. Geri yüklenen disk işletim sistemi diskindeyken, bir sanal makinenin işletim sistemi diskini, **Ayarlar** bölümündeki **sanal makine** bölmesi-> **diskler** menüsünün altındaki Azure Portal takas edebilirsiniz.

    ![İşletim sistemi disklerini değiştirme](./media/restore-managed-disks/swap-os-disks.png)

- Windows sanal makineler için, geri yüklenen disk bir veri diskise, [özgün veri diskini](../virtual-machines/windows/detach-disk.md#detach-a-data-disk-using-the-portal) sanal makineden ayırmak için yönergeleri izleyin. Ardından [geri yüklenen diski](../virtual-machines/windows/attach-managed-disk-portal.md) sanal makineye ekleyin. Sanal makinenin [işletim sistemi diskini](../virtual-machines/windows/os-disk-swap.md) geri yüklenen diskle değiştirmek için yönergeleri izleyin.

- Linux sanal makineleri için, geri yüklenen disk bir veri diskise, [özgün veri diskini](../virtual-machines/linux/detach-disk.md#detach-a-data-disk-using-the-portal) sanal makineden ayırmak için yönergeleri izleyin. Ardından [geri yüklenen diski](../virtual-machines/linux/attach-disk-portal.md#attach-an-existing-disk) sanal makineye ekleyin. Sanal makinenin [işletim sistemi diskini](../virtual-machines/linux/os-disk-swap.md) geri yüklenen diskle değiştirmek için yönergeleri izleyin.

Geri yükleme işlemi başarıyla tamamlandıktan sonra, **hedef kaynak grubundaki** yedekleme kasasının yönetilen kimliğinden **disk geri yükleme işletmeni** rol atamasını iptal etmeniz önerilir.

## <a name="track-a-restore-operation"></a>Geri yükleme işlemini izleme

Geri yükleme işlemini tetikledikten sonra, yedekleme hizmeti izleme için bir iş oluşturur. Azure Backup portalda iş hakkında bildirimleri görüntüler. Geri yükleme işinin ilerlemesini görüntülemek için:

1. **Yedekleme örneği** ekranına gidin. Son yedi günün işlem ve durumu ile birlikte iş panosunu gösterir.

    ![İşler Panosu](./media/restore-managed-disks/jobs-dashboard.png)

1. Geri yükleme işleminin durumunu görüntülemek için, bu yedekleme örneğinin devam eden ve geçmiş işlerini göstermek için **Tümünü görüntüle** ' yi seçin.

    ![Tümünü görüntüle seçeneğini belirleyin](./media/restore-managed-disks/view-all.png)

1. Yedekleme ve geri yükleme işlerinin ve bunların durumlarının listesini gözden geçirin. İş ayrıntılarını görüntülemek için iş listesinden bir iş seçin.

    ![İşlerin listesi](./media/restore-managed-disks/list-of-jobs.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure disk yedekleme hakkında SSS](disk-backup-faq.yml)