---
title: Azure portal Azure dosya paylaşımlarını yedekleme
description: Kurtarma Hizmetleri kasasındaki Azure dosya paylaşımlarını yedeklemek için Azure portal nasıl kullanacağınızı öğrenin
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: e7f44a71388468be432bdfcb0eb2bf67c0fcc8ef
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519944"
---
# <a name="back-up-azure-file-shares"></a>Azure dosya paylaşımlarını yedekleme

Bu makalede, [Azure dosya paylaşımlarının](../storage/files/storage-files-introduction.md) Azure Portal nasıl yedekleneceği açıklanır.

Bu makalede şunları yapmayı öğreneceksiniz:

* Kurtarma Hizmetleri kasası oluşturun.
* Kurtarma Hizmetleri kasasından yedeklemeyi yapılandırma
* Dosya paylaşma bölmesinden yedeklemeyi yapılandırma
* Geri yükleme noktası oluşturmak için isteğe bağlı yedekleme işini çalıştırma

## <a name="prerequisites"></a>Önkoşullar

* Azure dosya paylaşma anlık görüntü tabanlı yedekleme çözümü hakkında [bilgi edinin](azure-file-share-backup-overview.md) .
* Dosya paylaşımının [Desteklenen depolama hesabı türlerinden](azure-file-share-support-matrix.md)birinde bulunduğundan emin olun.
* Dosya paylaşımının barındırılmasına yönelik depolama hesabıyla aynı bölgede bir [Kurtarma Hizmetleri Kasası](#create-a-recovery-services-vault) oluşturun veya oluşturun.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="configure-backup-from-the-recovery-services-vault"></a>Kurtarma Hizmetleri kasasından yedeklemeyi yapılandırma

Aşağıdaki adımlarda, kurtarma hizmetleri Kasası bölmesinden birden çok dosya paylaşımı için yedeklemeyi nasıl yapılandırabileceğiniz açıklanmaktadır:

1. [Azure Portal](https://portal.azure.com/), dosya paylaşımının yedeklemesini yapılandırmak için kullanmak Istediğiniz kurtarma hizmetleri kasasını açın.

1. **Kurtarma Hizmetleri Kasası** bölmesinde, üstteki menüden **+ Backup** ' ı seçin.

   ![Kurtarma Hizmetleri kasası](./media/backup-afs/recovery-services-vault.png)

    1. **Yedekleme hedefi** bölmesinde, **Iş yükünüzün nerede çalıştığını** , açılan listeden **Azure** seçeneğini belirleyerek **Azure** 'a ayarlayın.

          ![İş yükü olarak Azure 'u seçin](./media/backup-afs/backup-goal.png)

    2. **Neleri yedeklemek istiyorsunuz?** bölümünde, açılan listeden **Azure dosya paylaşma** ' yı seçin.

          ![Azure FileShare seçin](./media/backup-afs/select-azure-file-share.png)

    3. Azure dosya paylaşımının uzantısını kasaya kaydetmek için **Yedekle** ' yi seçin.

          ![Azure dosya paylaşımının kasasıyla ilişkilendirilmesi için yedekleme 'yi seçin](./media/backup-afs/register-extension.png)

1. **Yedekle**' yi seçtikten sonra **yedekleme** bölmesi açılır. Korumak istediğiniz dosya paylaşımının barındırıldığı depolama hesabını seçmek için, **depolama hesabı** metin kutusunun altındaki bağlantıyı **seçin** metin kutusunu seçin.

   ![Seçme bağlantısını seçin](./media/backup-afs/choose-select-link.png)

1. **Depolama hesabı Seç bölmesi** sağ tarafta açılarak, bulunan desteklenen depolama hesaplarının bir kümesini listeler. Bu kasayla ilişkilendirilir veya kasada aynı bölgede bulunur, ancak henüz herhangi bir kurtarma hizmetleri kasasıyla ilişkilendirilmemiştir.

1. Bulunan depolama hesapları listesinden bir hesap seçin ve **Tamam**' ı seçin.

   ![Bulunan depolama hesaplarından seçin](./media/backup-afs/select-discovered-storage-account.png)

1. Sonraki adım, yedeklemek istediğiniz dosya paylaşımlarını seçdir. **Yedeklenecek dosya paylaşımları** bölümündeki **Ekle** düğmesini seçin.

   ![Yedeklenecek dosya paylaşımlarını seçin](./media/backup-afs/select-file-shares-to-back-up.png)

1. Sağ tarafta **dosya paylaşımları seçin** bağlam bölmesi açılır. Azure, yedeklenebilir dosya paylaşımları için depolama hesabını arar. Dosya paylaşımlarınızı son zamanlarda eklediyseniz ve listede görmüyorsanız, dosya paylaşımlarının görünmesi için bir süre bekleyin.

1. **Dosya paylaşımları Seç** listesinden yedeklemek istediğiniz bir veya daha fazla dosya paylaşımını seçin. **Tamam**’ı seçin.

   ![Dosya paylaşımlarını seçin](./media/backup-afs/select-file-shares.png)

1. Dosya paylaşımınız için bir yedekleme ilkesi seçmek üzere üç seçeneğiniz vardır:

   * Varsayılan ilkeyi seçin.<br>
   Bu seçenek, 30 gün boyunca tutulacak günlük yedeklemeyi etkinleştirmenizi sağlar. Kasada mevcut bir yedekleme ilkeniz yoksa, yedekleme bölmesi varsayılan ilke ayarlarıyla açılır. Varsayılan ayarları seçmek istiyorsanız, **yedeklemeyi etkinleştir**' i doğrudan seçebilirsiniz.

   * Yeni ilke oluşturma <br>

      1. Dosya paylaşımınız için yeni bir yedekleme ilkesi oluşturmak üzere **yedekleme ilkesi** bölümündeki aşağı açılan listenin altındaki bağlantı metnini seçin.<br>

         ![Yeni ilke oluştur](./media/backup-afs/create-new-policy.png)

      1. **Yedekleme ilkesi** bağlam bölmesi sağ tarafta açılır. Metin kutusunda bir ilke adı belirtin ve gereksinimlerinize göre bekletme dönemini seçin. Yalnızca günlük bekletme seçeneği varsayılan olarak etkindir. Haftalık, aylık veya yıllık saklama kullanmak istiyorsanız, ilgili onay kutusunu seçin ve istenen saklama değerini sağlayın.

      1. Saklama değerlerini ve geçerli bir ilke adını belirttikten sonra **Tamam**' ı seçin.<br>

         ![İlke adı ve bekletme değerleri verme](./media/backup-afs/policy-name.png)

   * Mevcut yedekleme ilkelerinden birini seçin <br>

      Korumayı yapılandırmak için mevcut yedekleme ilkelerinden birini seçmek için **yedekleme ilkesi** açılan listesinden istenen ilkeyi seçin.<br>

      ![Mevcut ilkeyi seçin](./media/backup-afs/choose-existing-policy.png)

1. Dosya paylaşımının korunmasını başlatmak için **yedeklemeyi etkinleştir** ' i seçin.

   ![Yedeklemeyi Etkinleştir ' i seçin](./media/backup-afs/enable-backup.png)

Bir yedekleme İlkesi ayarladıktan sonra, dosya paylaşımlarının bir anlık görüntüsü zamanlanan saatte alınır. Kurtarma noktası, seçilen dönem için de korunur.

>[!NOTE]
>Azure Backup artık Azure dosya paylaşma yedeklemesi için günlük/haftalık/aylık/yıllık saklama ilkelerini destekler.

## <a name="configure-backup-from-the-file-share-pane"></a>Dosya paylaşma bölmesinden yedeklemeyi yapılandırma

Aşağıdaki adımlarda, ilgili dosya paylaşımı bölmesinden ayrı dosya paylaşımları için yedeklemeyi nasıl yapılandırabileceğiniz açıklanmaktadır:

1. [Azure Portal](https://portal.azure.com/), yedeklemek istediğiniz dosya paylaşımının barındırıldığı depolama hesabını açın.

1. Depolama hesabında bir kez, **dosya paylaşımları** etiketli kutucuğu seçin. Ayrıca, depolama hesabı için içindekiler tablosu aracılığıyla **dosya paylaşımlarına** gidebilirsiniz.

   ![Depolama hesabı](./media/backup-afs/storage-account.png)

1. Dosya paylaşımı listesinde, depolama hesabında bulunan tüm dosya paylaşımlarını görmeniz gerekir. Yedeklemek istediğiniz dosya payını seçin.

   ![Dosya paylaşımları listesi](./media/backup-afs/file-shares-list.png)

1. Dosya paylaşma bölmesinin **işlemler** bölümünde **Yedekle** ' yi seçin. **Yedeklemeyi Yapılandır** bölmesi sağ tarafta yüklenir.

   ![Yedekleme bölmesini yapılandırma](./media/backup-afs/configure-backup.png)

1. Kurtarma Hizmetleri Kasası seçimi için aşağıdakilerden birini yapın:

    * Zaten bir kasanız varsa, **var olan** kurtarma hizmetleri Kasası Seç radyo düğmesini seçin ve kasa adı açılan menüsünde mevcut **kasalardan** birini seçin.

       ![Mevcut kasayı seçin](./media/backup-afs/select-existing-vault.png)

    * Kasanız yoksa, yeni kurtarma hizmetleri Kasası **Oluştur** radyo düğmesini seçin. Kasa için bir ad belirtin. Dosya paylaşımıyla aynı bölgede oluşturulur. Varsayılan olarak, kasa dosya paylaşımıyla aynı kaynak grubunda oluşturulur. Farklı bir kaynak grubu seçmek istiyorsanız, **kaynak türü** açılan listesini aşağıdan **Yeni bağlantı oluştur** ' u seçin ve kaynak grubu için bir ad belirtin. Devam etmek için **Tamam**'ı seçin.

       ![Yeni kasa oluştur](./media/backup-afs/create-new-vault.png)

      >[!IMPORTANT]
      >Depolama hesabı bir kasayla kayıtlıysa veya korumaya çalıştığınız dosya paylaşımını barındıran depolama hesabı içinde birkaç korumalı paylaşım varsa, kurtarma hizmetleri kasasının adı önceden doldurulur ve [daha fazla bilgi edinin](backup-azure-files-faq.yml#why-can-t-i-change-the-vault-to-configure-backup-for-the-file-share-).

1. **Yedekleme ilkesi** seçimi için aşağıdakilerden birini yapın:

    * Varsayılan ilkeyi bırakın. Günlük yedeklemeler, 30 günlük bekletme ile zamanlanır.

    * **Yedekleme ilkesi** açılır menüsünden, varsa, var olan bir yedekleme ilkesi seçin.

       ![Yedekleme ilkesi seçin](./media/backup-afs/choose-backup-policy.png)

    * Gereksinime göre günlük/haftalık/aylık/yıllık bekletme ile yeni bir ilke oluşturun.  

         1. **Yeni Ilke oluştur** bağlantı metnini seçin.

         2. **Yedekleme ilkesi** bağlam bölmesi sağ tarafta açılır. Metin kutusunda bir ilke adı belirtin ve gereksinimlerinize göre bekletme dönemini seçin. Yalnızca günlük bekletme seçeneği varsayılan olarak etkindir. Haftalık, aylık veya yıllık saklama kullanmak istiyorsanız, ilgili onay kutusunu seçin ve istenen saklama değerini sağlayın.

         3. Saklama değerlerini ve geçerli bir ilke adını belirttikten sonra **Tamam**' ı seçin.

            ![Yeni yedekleme İlkesi Oluştur](./media/backup-afs/create-new-backup-policy.png)

1. Dosya paylaşımının korunmasını başlatmak için **yedeklemeyi etkinleştir** ' i seçin.

   ![Yedeklemeyi Etkinleştir ' i seçin](./media/backup-afs/select-enable-backup.png)

1. Yapılandırma ilerlemesini Portal bildirimlerinde izleyebilir veya dosya paylaşımının korunması için kullandığınız kasadaki yedekleme işlerini izleyerek izleyebilirsiniz.

   ![Portal bildirimleri](./media/backup-afs/portal-notifications.png)

1. Yedekleme yapılandırma işlemini tamamladıktan sonra dosya paylaşma bölmesinin **işlemler** bölümünde **Yedekle** ' yi seçin. **Kasa Essentials** 'ın listelendiği bağlam bölmesi sağ tarafta yüklenir. Buradan, isteğe bağlı yedekleme ve geri yükleme işlemlerini tetikleyebilirsiniz.

   ![Kasa temel bileşenleri](./media/backup-afs/vault-essentials.png)

## <a name="run-an-on-demand-backup-job"></a>İsteğe bağlı yedekleme işi çalıştırma

Bazen yedekleme ilkesinde zamanlanan saatlerin dışında bir yedekleme anlık görüntüsü veya kurtarma noktası oluşturmak isteyebilirsiniz. Yedekleme ilkesini yapılandırdıktan sonra, isteğe bağlı yedekleme oluşturmak için sık karşılaşılan bir neden vardır. Yedekleme ilkesindeki zamanlamaya göre, bir anlık görüntü alınana kadar saat veya gün olabilir. Yedekleme ilkesi devreye girene kadar verilerinizi korumak için, bir isteğe bağlı yedekleme başlatın. Dosya paylaşımlarınız üzerinde planlı değişiklikler yapmadan önce isteğe bağlı yedekleme oluşturmak genellikle gereklidir.

### <a name="from-the-recovery-services-vault"></a>Kurtarma Hizmetleri kasasından

1. Dosya paylaşımınızı yedeklemek için kullandığınız kurtarma hizmetleri kasasını açın. **Genel bakış** bölmesinde, **korumalı öğeler** bölümünde **yedekleme öğeleri** ' ni seçin.

   ![Yedekleme öğelerini seçin](./media/backup-afs/backup-items.png)

1. **Yedekleme öğeleri**' ni seçtikten sonra, **genel bakış** bölmesinin yanında tüm **yedekleme yönetimi türlerini** listeleyen yeni bir bölmesi görüntülenir.

   ![Yedekleme yönetim türlerinin listesi](./media/backup-afs/backup-management-types.png)

1. **Yedekleme yönetimi türü** listesinden **Azure depolama (Azure dosyaları)** öğesini seçin. Tüm dosya paylaşımlarının ve bu kasa kullanılarak yedeklenen karşılık gelen depolama hesaplarının bir listesini görürsünüz.

   ![Azure depolama (Azure dosyaları) yedekleme öğeleri](./media/backup-afs/azure-files-backup-items.png)

1. Azure dosya paylaşımları listesinden istediğiniz dosya paylaşımını seçin. **Yedekleme öğesi** ayrıntıları görüntülenir. **Yedekleme öğesi** menüsünde **Şimdi Yedekle**' yi seçin. Bu yedekleme işi isteğe bağlı olduğundan, kurtarma noktasıyla ilişkili bir bekletme ilkesi yok.

   ![Şimdi Yedekle 'yi seçin](./media/backup-afs/backup-now.png)

1. **Şimdi Yedekle** bölmesi açılır. Kurtarma noktasını bekletmek istediğiniz son günü belirtin. İsteğe bağlı yedekleme için en fazla 10 yıl bekletmeye sahip olabilirsiniz.

   ![Bekletme tarihi seçin](./media/backup-afs/retention-date.png)

1. Çalışan isteğe bağlı yedekleme işini onaylamak için **Tamam ' ı** seçin.

1. Yedekleme işi çalıştırma işleminin tamamlanmasını izlemek için Portal bildirimlerini izleyin. İş ilerlemesini kasa panosunda izleyebilirsiniz.   >  **Devam eden** yedekleme işleri seçin.

### <a name="from-the-file-share-pane"></a>Dosya paylaşma bölmesinden

1. İsteğe bağlı yedekleme gerçekleştirmek istediğiniz dosya paylaşımının **genel bakış** bölmesini açın.

1. **İşlem** bölümünün altında **Yedekle** ' yi seçin. **Kasa Essentials** 'ın listelendiği bağlam bölmesi sağ tarafta yüklenir. İsteğe bağlı yedekleme gerçekleştirmek için **Şimdi Yedekle** ' yi seçin.

   ![Şimdi Yedekle 'yi seçin](./media/backup-afs/select-backup-now.png)

1. **Şimdi Yedekle** bölmesi açılır. Kurtarma noktası için saklama süresini belirtin. İsteğe bağlı yedekleme için en fazla 10 yıl bekletmeye sahip olabilirsiniz.

   ![Yedekleme tarihini sakla](./media/backup-afs/retain-backup-date.png)

1. Onaylamak için **Tamam**’ı seçin.

>[!NOTE]
>Azure Backup, karşılık gelen hesaptaki herhangi bir dosya paylaşımının korumasını yapılandırırken depolama hesabını kilitler. Bu, yedeklenen dosya paylaşımlarına sahip bir depolama hesabının yanlışlıkla silinmesine karşı koruma sağlar.

## <a name="best-practices"></a>En iyi uygulamalar

* Azure Backup tarafından oluşturulan anlık görüntüleri silmeyin. Anlık görüntülerin silinmesi, kurtarma noktalarının kaybolması ve/veya geri yükleme işlemlerinin başarısız olmasıyla sonuçlanabilir

* Azure Backup tarafından depolama hesabında gerçekleştirilen kilidi kaldırmayın. Kilidi silerseniz, depolama hesabınız yanlışlıkla silinmeye açıktır ve siliniyorsa, anlık görüntülerinizi veya yedeklerinizi kaybedersiniz.

## <a name="next-steps"></a>Sonraki adımlar

Şunları nasıl yapacağınızı öğrenin:

* [Azure dosya paylaşımlarını geri yükleme](restore-afs.md)
* [Azure dosya paylaşımı yedeklemelerini yönetme](manage-afs-backup.md)
