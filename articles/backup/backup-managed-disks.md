---
title: Azure yönetilen disklerini yedekleme
description: Azure portal Azure yönetilen disklerini nasıl yedekleyeceğinizi öğrenin.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: e234495eb483d6d0cc6ca556ca418138c61a99f5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110636"
---
# <a name="back-up-azure-managed-disks"></a>Azure yönetilen disklerini yedekleme

Bu makalede, [Azure yönetilen diskin](../virtual-machines/managed-disks-overview.md) Azure Portal nasıl yedekleneceği açıklanır.

Bu makalede şunları yapmayı öğreneceksiniz:

- Yedekleme Kasası oluşturma

- Yedekleme ilkesi oluşturma

- Azure diskinin yedeğini yapılandırma

- İsteğe bağlı yedekleme işi çalıştırma

Azure disk yedekleme bölgesi kullanılabilirliği, desteklenen senaryolar ve sınırlamalar hakkında bilgi için bkz. [destek matrisi](disk-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Yedekleme Kasası oluşturma

Yedekleme Kasası, Azure 'da PostgreSQL sunucuları ve Azure diskleri için Azure veritabanı gibi Azure Backup desteklediği çeşitli yeni iş yükleri için yedekleme verileri tutan bir depolama varlıktır. Yedekleme kasaları, yedekleme verilerinizi düzenlemenizi kolaylaştırır, ancak yönetim yükünü en aza indirir. Yedekleme kasaları, yedekleme verilerini güvenli hale getirmeye yardımcı olmak için gelişmiş yetenekler sağlayan Azure Azure Resource Manager modelini temel alır.

1. [https://portal.azure.com](https://portal.azure.com/) adresinden Azure portalında oturum açın.
1. Arama kutusuna **yedekleme merkezi** yazın.
1. **Hizmetler** altında **yedekleme merkezi**' ni seçin.
1. **Yedekleme merkezi** sayfasında **kasa**' yı seçin.

   ![Yedekleme merkezi 'nde kasa seçin](./media/backup-managed-disks/backup-center.png)

1. **Başlat: kasa oluştur** ekranında, **Yedekleme Kasası**' nı seçin ve **devam edin**.

   ![Başlat: kasa oluşturma](./media/backup-managed-disks/initiate-create-vault.png)

1. **Temel bilgiler** sekmesinde abonelik, kaynak grubu, Yedekleme Kasası adı, bölge ve yedek depolama yedekliliği sağlayın. **Gözden geçir + oluştur** seçeneğini belirleyerek devam edin. [Yedekleme Kasası oluşturma](./backup-vault-overview.md#create-a-backup-vault)hakkında daha fazla bilgi edinin.

   ![Kasayı gözden geçirin ve oluşturun](./media/backup-managed-disks/review-and-create.png)

## <a name="create-backup-policy"></a>Yedekleme İlkesi Oluştur

1. Önceki adımda oluşturulan *Demokasa* **yedekleme kasasında** **yedekleme Ilkeleri** ' ne gidin ve **Ekle**' yi seçin.

   ![Yedekleme İlkesi Ekle](./media/backup-managed-disks/backup-policies.png)

1. **Temel bilgiler** sekmesinde, ilke adı ' nı belirtin, **veri kaynağı türü** ' nü **Azure disk** olarak seçin. Kasa zaten önceden doldurulmuştur ve seçili kasa özellikleri sunulmuştur.

   >[!NOTE]
   > Seçili kasadaki küresel artıklık ayarı olabilir, ancak şu anda Azure disk yedekleme yalnızca anlık görüntü veri deposunu destekler. Tüm yedeklemeler aboneliğinizdeki bir kaynak grubunda depolanır ve Yedekleme Kasası depolamasına kopyalanmaz.

   ![Veri kaynağı türünü seçin](./media/backup-managed-disks/datasource-type.png)

1. **Yedekleme ilkesi** sekmesinde yedekleme zamanlaması sıklığını seçin.

   ![Yedekleme zamanlama sıklığını seçin](./media/backup-managed-disks/backup-schedule-frequency.png)

   Azure disk yedekleme günde birden çok yedekleme sunar. Daha sık yedeklemeler gerekiyorsa, **saatlik** yedekleme sıklığını her 4, 6, 8 veya 12 saat aralıklarıyla yedeklemeler alma özelliğiyle birlikte seçin. Yedeklemeler, seçilen **zaman** aralığı temelinde zamanlanır. Örneğin, **her 4 saatte** bir seçeneğini belirlerseniz yedeklemeler günde eşit olarak dağıtılırsa yedeklemeler her 4 saatte bir zaman aralığında alınır. Günde bir kez yedekleme yeterliyse **günlük** yedekleme sıklığını seçin. Günlük yedekleme sıklığında, yedeklemelerinizin alındığı günün saatini belirtebilirsiniz. Gün zamanının yedeklemenin tamamlandığı zaman değil, yedekleme başlangıç saatini gösterdiğine dikkat edin. Yedekleme işleminin tamamlanması için gereken süre, diskin boyutu ve ardışık yedeklemeler arasındaki dalgalanma oranı dahil olmak üzere çeşitli faktörlere bağımlıdır. Ancak, Azure disk yedekleme, üretim uygulaması performansını etkilemeyen [Artımlı anlık görüntüleri](../virtual-machines/disks-incremental-snapshots.md)kullanan aracısız bir yedeğiniz.

1. **Yedekleme ilkesi** sekmesinde, kurtarma noktası hedefı (RPO) gereksinimini karşılayan bekletme ayarları ' nı seçin.

   Başka bir bekletme kuralı belirtilmemişse varsayılan saklama kuralı uygulanır. Varsayılan saklama kuralı, saklama süresini değiştirecek şekilde değiştirilebilir, ancak silinemez. **Bekletme kuralı ekle**' ye tıklayarak yeni bir bekletme kuralı ekleyebilirsiniz.

   ![Bekletme kuralı ekle](./media/backup-managed-disks/add-retention-rule.png)

   Günlük veya haftalık alınan **ilk başarılı yedeklemeyi** seçebilir ve belirli yedeklemelerin silinmeden önce bekletileceği saklama süresini sağlayabilirsiniz. Bu seçenek, daha uzun bir süre boyunca günün veya haftanın belirli yedeklemelerini sürdürmek için yararlıdır. Tüm diğer sık yedeklemeler daha kısa bir süre boyunca korunabilir.

   ![Bekletme ayarları](./media/backup-managed-disks/retention-settings.png)

   >[!NOTE]
   >Yönetilen diskler için Azure Backup, disk başına 200 anlık görüntüsü ile sınırlı olan Artımlı anlık görüntüleri kullanır. Yedekleme ilkesi, zamanlanan yedeklemelerin dışında isteğe bağlı yedeklemeler yapmanıza olanak tanımak için toplam yedeklemeleri 180 olarak sınırlandırır. Yönetilen disk için [Artımlı anlık görüntüler](../virtual-machines/disks-incremental-snapshots.md#restrictions) hakkında daha fazla bilgi edinin.

1. Yedekleme ilkesi oluşturmayı **gözden geçir + oluştur** seçeneğini belirleyerek doldurun.

## <a name="configure-backup"></a>Yedeklemeyi yapılandırma

Yedekleme Kasası, diğer Azure kaynaklarına erişmek için yönetilen kimlik kullanır. Yönetilen disklerin yedeklemesini yapılandırmak için, yedekleme kasasının yönetilen kimliği, anlık görüntülerin oluşturulduğu ve yönetildiği kaynak disklerde ve kaynak gruplarında bir izin kümesi gerektirir.

Sistem tarafından atanan yönetilen kimlik, kaynak başına bir kaynakla kısıtlıdır ve bu kaynağın yaşam döngüsüne bağlıdır. Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak yönetilen kimliğe izin verebilirsiniz. Yönetilen kimlik, yalnızca Azure kaynaklarıyla kullanılabilecek özel bir türün hizmet sorumlusundan oluşur. [Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md)hakkında daha fazla bilgi edinin.

Yönetilen disklerin yedeklemesini yapılandırmak için aşağıdaki Önkoşullar gereklidir:

1. Yedeklenecek kaynak diskte yedekleme kasasının yönetilen kimliğine **disk yedekleme okuyucusu** rolünü atayın.

   1. Yedeklenmesi gereken diske gidin.

   1. **Access Control (IAM)** sayfasına gidin ve **rol atamaları Ekle** ' yi seçin.

   1. Sağ bağlam bölmesinde, **rol** açılan listesinden **disk yedekleme okuyucusu** ' nu seçin. Yedekleme kasasının yönetilen kimliğini seçin ve **kaydedin**.

   >[!TIP]
   >Kasasının yönetilen kimliğini seçmek için Yedekleme Kasası adını yazın.

   ![Disk yedekleme okuyucu rolü Ekle](./media/backup-managed-disks/disk-backup-reader-role.png)

1. **Disk anlık görüntüsü katkıda** bulunan rolünü, yedeklemelerin Azure Backup hizmeti tarafından oluşturulup yönetildiği kaynak grubundaki yedekleme kasasının yönetilen kimliğine atayın. Disk anlık görüntüleri, aboneliğinizde içindeki bir kaynak grubunda depolanır. Azure Backup hizmetinin anlık görüntü oluşturmasına, depolamasına ve yönetmesine izin vermek için yedekleme kasasının izinlerini sağlamanız gerekir.

   **Anlık görüntüleri depolamak ve yönetmek için kaynak grubu seçme:**

   - Kaynak diskle aynı kaynak grubunu seçmeyin.

   - Bir kılavuz olarak, Azure Backup hizmeti tarafından kullanılacak anlık görüntü veri deposu olarak adanmış bir kaynak grubu oluşturmanız önerilir. Ayrılmış bir kaynak grubuna sahip olmak, kaynak grubunda erişim izinlerinin kısıtlanması, böylece yedekleme verilerinin güvenliği ve yönetimi kolaylığı sağlar.

   - Bu kaynak grubunu, yedeklerinin yedeklendiği birden çok diskte anlık görüntüleri depolamak için kullanabilirsiniz.  

   - Bu diskin aboneliği dışındaki belirli bir disk için artımlı bir anlık görüntü oluşturamazsınız. Bu nedenle, yedeklenecek diskle aynı abonelik içindeki kaynak grubunu seçin. Yönetilen diskler için [Artımlı anlık görüntü](../virtual-machines/disks-incremental-snapshots.md#restrictions) hakkında daha fazla bilgi edinin.

   Rolü atamak için aşağıdaki adımları izleyin:

   1. Kaynak grubuna gidin. Örneğin, kaynak grubu, yedeklenecek diskle aynı abonelikte olan *anlık görüntüyle* gelir.

   1. **Access Control (IAM)** sayfasına gidin ve **rol atamaları Ekle**' yi seçin.

   1. Sağ bağlam bölmesinde, **rol** açılan listesinden **disk anlık görüntüsü katılımcısı** ' nı seçin. Yedekleme kasasının yönetilen kimliğini seçin ve **kaydedin**.

   >[!TIP]
   >Kasasının yönetilen kimliğini seçmek için Yedekleme Kasası adını yazın.

   ![Disk anlık görüntüsü katılımcısı rolü Ekle](./media/backup-managed-disks/disk-snapshot-contributor-role.png)

1. Yedekleme kasasının yönetilen kimliğinin, anlık görüntü deposu görevi gören kaynak diskte ve kaynak grubunda doğru rol atamaları kümesine sahip olduğunu doğrulayın.

   1. **Yedekleme Kasası-> kimliği** ' ne gidin ve **Azure rolü atamaları**' nı seçin.

      ![Azure rol atamalarını seçin](./media/backup-managed-disks/azure-role-assignments.png)

   1. Rolün, kaynak adının ve kaynak türünün doğru yansıtıldığını doğrulayın.

      ![Rol, kaynak adı ve kaynak türünü doğrulama](./media/backup-managed-disks/verify-role.png)

   >[!NOTE]
   >Rol atamaları portalda doğru şekilde yansıtıldığından, izinlerin yedekleme kasasının yönetilen kimliğine uygulanması yaklaşık 15 dakika sürebilir.

1. Önkoşullar karşılandığında, **Yedekleme Kasası 'na > genel bakış ' a** gidin ve disklerin yedeklenmesini yapılandırmaya başlamak için **Yedekle** ' yi seçin.

   ![Yedekleme seçin](./media/backup-managed-disks/select-backup.png)

1. **Temel bilgiler** sekmesinde, veri kaynağı türü olarak **Azure disk** ' i seçin.

   ![Azure diski seçin](./media/backup-managed-disks/select-azure-disk.png)

   >[!NOTE]
   >Azure Backup, yönetilen disklerin [Artımlı anlık görüntülerini](../virtual-machines/disks-incremental-snapshots.md#restrictions) kullanır ve bu, üst diskin depolama türünden bağımsız olarak, standart HDD depolama alanının son anlık görüntüsünden bu yana yalnızca diskte Delta değişikliklerini depolar. Daha fazla güvenilirlik için, ZRS 'yi destekleyen bölgelerde Artımlı anlık görüntüler bölge yedekli depolama (ZRS) üzerinde varsayılan olarak depolanır. Şu anda Azure disk yedekleme, yedeklemeleri Yedekleme Kasası deposuna kopyalamamakta olan yönetilen disklerin işletimsel yedeklemesini destekler. Bu nedenle yedekleme kasasının yedek depolama yedeklilik ayarı kurtarma noktaları için uygulanmaz.

1. **Yedekleme ilkesi** sekmesinde, bir yedekleme ilkesi seçin.

   ![Yedekleme ilkesi seçin](./media/backup-managed-disks/choose-backup-policy.png)

1. **Kaynaklar** sekmesinde **Azure Disk Seç**' i seçin. Sağ bağlam bölmesinde yedeklenecek diskleri seçin.

   ![Yedeklenecek diskleri seçin](./media/backup-managed-disks/select-disks-to-backup.png)

   >[!NOTE]
   >Portal birden çok disk seçmenize ve yedeklemeyi yapılandırmanıza izin verdiğinden, her disk ayrı bir yedekleme örneğidir. Şu anda Azure disk yedeklemesi yalnızca ayrı disklerin yedeklenmesini desteklemektedir. Bir sanal diske bağlı birden çok diskin zaman içinde yedeklenmesi desteklenmez.
   >
   >Portalı kullanırken, aynı abonelik içinde diskler seçmeye sınırlı olursunuz. Yedeklenecek birkaç diskiniz varsa veya diskler farklı aboneliğe yayıldıysanız, otomatikleştirebilmek için betikleri kullanabilirsiniz.
   >
   >Azure disk yedekleme bölgesi kullanılabilirliği, desteklenen senaryolar ve sınırlamalar hakkında daha fazla bilgi için bkz. [destek matrisi](disk-backup-support-matrix.md).

1. Bir **anlık görüntü kaynak grubu** seçip **Doğrula**' yı seçin. Bu, Azure Backup hizmetinin oluşturduğu kaynak grubudur ve yedekleme kasasının Artımlı anlık görüntülerini yönetir. Yönetilen kimlik, önkoşulların bir parçası olarak gerekli rol izinleriyle atanır.

   ![Anlık görüntü kaynak grubunu seçin](./media/backup-managed-disks/select-snapshot-resource-group.png)

   >[!NOTE]
   >Yedekleme iş akışını yapılandırmayı tamamlamadan önce doğrulamanın tamamlanması birkaç dakika sürebilir. Doğrulama başarısız olabilir:
   >
   > - bir disk desteklenmiyor. Desteklenmeyen senaryolar için [destek matrisine](disk-backup-support-matrix.md) bakın.
   > - Yedekleme Kasası yönetilen kimliği, bir **diskte** yedeklenmek üzere veya artımlı anlık görüntülerin depolandığı **anlık görüntü kaynak grubunda** geçerli rol atamaları yok.

1. Başarılı bir doğrulamadan sonra, seçili disklerin yedeklemesini yapılandırmak için **gözden geçir ve Yapılandır** ' ı seçin.

## <a name="run-an-on-demand-backup"></a>İsteğe bağlı yedekleme çalıştırma

1. Önceki adımda oluşturulan *Demokasa* **yedekleme kasasında** , **yedekleme örnekleri** ' ne gidin ve bir yedekleme örneği seçin.

   ![Yedekleme örneği Seç](./media/backup-managed-disks/select-backup-instance.png)

1. **Yedekleme örnekleri** ekranında şunları bulacaksınız:

   - kaynak disk adı, artımlı anlık görüntülerin depolandığı anlık görüntü kaynak grubu, yedekleme kasası ve yedekleme İlkesi gibi **önemli** bilgiler.
   - Yedekleme ve geri yükleme işlemlerinin özetini gösteren **iş durumu** ve son yedi gün içindeki durumları.
   - Seçilen zaman aralığı için **geri yükleme noktalarının** listesi.

1. İsteğe bağlı yedekleme başlatmak için **Yedekle** ' yi seçin.

   ![Şimdi Yedekle 'yi seçin](./media/backup-managed-disks/backup-now.png)

1. Yedekleme ilkesiyle ilişkili bekletme kurallarından birini seçin. Bu bekletme kuralı, bu isteğe bağlı yedeklemenin saklama süresini belirlecek. Yedeklemeyi başlatmak için **Şimdi Yedekle** ' yi seçin.

   ![Yedeklemeyi Başlat](./media/backup-managed-disks/initiate-backup.png)

## <a name="track-a-backup-operation"></a>Yedekleme işlemini izleme

Azure Backup hizmeti, zamanlanmış yedeklemeler için bir iş oluşturur veya izleme için isteğe bağlı yedekleme işlemi tetiklersiniz. Yedekleme işinin durumunu görüntülemek için:

1. **Yedekleme örneği** ekranına gidin. Son yedi günün işlem ve durumu ile birlikte iş panosunu gösterir.

   ![İşler Panosu](./media/backup-managed-disks/jobs-dashboard.png)

1. Yedekleme işleminin durumunu görüntülemek için, bu yedekleme örneğinin devam eden ve geçmiş işlerini göstermek için **Tümünü görüntüle** ' yi seçin.

   ![Tümünü görüntüle seçeneğini belirleyin](./media/backup-managed-disks/view-all.png)

1. Yedekleme ve geri yükleme işlerinin ve bunların durumlarının listesini gözden geçirin. İş ayrıntılarını görüntülemek için iş listesinden bir iş seçin.

   ![Ayrıntıları görmek için iş öğesini seçin](./media/backup-managed-disks/select-job.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure yönetilen disklerini geri yükleme](restore-managed-disks.md)