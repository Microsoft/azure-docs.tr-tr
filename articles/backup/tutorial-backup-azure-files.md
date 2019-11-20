---
title: Öğretici-Azure dosyaları dosya paylaşımlarını yedekleme
description: Bu öğreticide, bir kurtarma hizmetleri Kasası yapılandırmak ve Azure dosya paylaşımlarını yedeklemek için Azure portal nasıl kullanacağınızı öğrenin.
ms.date: 06/10/2019
ms.topic: tutorial
ms.openlocfilehash: f3c1956a0344d300fff9a745a4733b7bef45a367
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171862"
---
# <a name="back-up-azure-file-shares-in-the-azure-portal"></a>Azure portal Azure dosya paylaşımlarını yedekleme

Bu öğreticide, [Azure dosya paylaşımlarını](../storage/files/storage-files-introduction.md)yedeklemek için Azure Portal nasıl kullanılacağı açıklanmaktadır.

Bu kılavuzda şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
>
> * Azure Dosyalarını yedeklemek için Kurtarma Hizmetleri kasasını yapılandırma
> * Geri yükleme noktası oluşturmak için isteğe bağlı yedekleme işini çalıştırma

## <a name="prerequisites"></a>Önkoşullar

Azure dosya paylaşımını yedekleyebilmeniz için önce [desteklenen Depolama Hesabı türlerinden](tutorial-backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview) birinde yer aldığından emin olmalısınız. Bunu doğruladıktan sonra dosya paylaşımlarınızı koruyabilirsiniz.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Önizleme sırasında Azure dosya paylaşma yedeklemesi için sınırlamalar

Azure dosya paylaşımları için yedekleme önizlemede sunulmaktadır. Hem genel amaçlı v1 hem de genel amaçlı v2 depolama hesaplarında Azure dosya paylaşımları desteklenir. Aşağıdaki yedekleme senaryoları, Azure dosya paylaşımları için desteklenmemektedir:

* Sanal Ağların veya Güvenlik Duvarının etkin olduğu depolama hesaplarında Azure dosya paylaşımlarını koruyamazsınız.
* Azure Backup kullanarak Azure dosyalarını korumak için kullanılabilir CLı yoktur.
* Günlük zamanlanan maksimum yedekleme sayısı birdir.
* Günlük zamanlanan maksimum istek üzerine yedekleme sayısı dörttür.
* Kurtarma Hizmetleri kasanızdaki yedeklemelerin yanlışlıkla silinmesini önlemek için depolama hesabındaki [kaynak kilitlerini](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) kullanın.
* Azure Backup tarafından oluşturulan anlık görüntülerin silmeyin. Anlık görüntülerin silinmesi, kurtarma noktalarının kaybolması ve/veya geri yükleme işlemlerinin başarısız olmasıyla sonuçlanabilir
* Azure Backup tarafından korunan dosya paylaşımlarını silmeyin. Geçerli çözüm, dosya paylaşımının silindiği ve bu nedenle tüm geri yükleme noktalarını kaybetmesine Azure Backup tarafından alınan tüm anlık görüntüleri siler

[Bölge yedekli depolama](../storage/common/storage-redundancy-zrs.md) (ZRS) çoğaltması Ile depolama hesaplarında Azure dosya paylaşımları için yedekleme şu anda yalnızca Orta ABD (cu düzeyinde kapsanır), Doğu ABD (EUS), Doğu ABD 2 (EUS2), Kuzey Avrupa (ne), Güneydoğu Asya (SEA), Batı Avrupa (we) ve Batı ABD 2 (WUS2) ' de kullanılabilir.

## <a name="configuring-backup-for-an-azure-file-share"></a>Azure dosya paylaşımı için yedeklemeyi yapılandırma

Bu öğreticide zaten yerleşik bir Azure dosya paylaşımınız olduğu varsayılır. Azure dosya paylaşımınızı yedeklemek için:

1. Dosya paylaşımınızla aynı bölgede bir Kurtarma Hizmetleri kasası oluşturun. Zaten bir kasanız varsa, kasanızın Genel Bakış sayfasını açın ve **Yedekle**'ye tıklayın.

    ![Kasadaki Genel Bakış sayfasında yedekle ' ye tıklayın](./media/backup-file-shares/overview-backup-page.png)

2. **Yedekleme hedefi** menüsünde, **ne yedeklemek Istiyorsunuz?** listesinden Azure FileShare ' i seçin.

    ![Yedekleme hedefi olarak Azure Dosya Paylaşımı'nı seçin](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. Azure dosya paylaşımınızı Kurtarma Hizmetleri kasasına yapılandırmak için **Yedekle**'ye tıklayın.

   ![Azure dosya paylaşımını kasayla ilişkilendirmek için Yedekle'ye tıklayın](./media/backup-file-shares/set-backup-goal.png)

    Kasa Azure dosya paylaşımıyla ilişkilendirildikten sonra, yedekleme menüsü açılır ve bir depolama hesabı seçmenizi ister. Menü, kasalarınızın bir kurtarma hizmetleri kasası ile henüz ilişkilendirilmemiş bulunduğu bölgedeki tüm desteklenen depolama hesaplarını görüntüler.

   ![Depolama hesabınızı seçin](./media/backup-file-shares/list-of-storage-accounts.png)

4. Depolama hesapları listesinde bir hesap seçin ve **Tamam**'a tıklayın. Azure, depolama hesabında yedeklenebilecek dosya paylaşımlarını arar. Dosya paylaşımlarınızı kısa süre önce eklediyseniz ve listede görmüyorsanız, dosya paylaşımlarının gösterilmesi için biraz zaman tanıyın.

   ![Dosya paylaşımları keşfediliyor](./media/backup-file-shares/discover-file-shares.png)

5. **Dosya paylaşımları** listesinde, yedeklemek istediğiniz bir veya daha fazla dosya paylaşımını seçin ve **Tamam**' a tıklayın.

6. Dosya Paylaşımlarınızı seçtikten sonra, Yedekle menüsü **Yedekleme ilkesi**'ne dönüşür. Bu menüde mevcut yedekleme ilkelerinden birini seçin veya yeni ilke oluşturun ve ardından **Yedeklemeyi Etkinleştir**'e tıklayın.

   ![Bir yedekleme ilkesi seçin veya yeni bir tane oluşturun](./media/backup-file-shares/apply-backup-policy.png)

    Yedekleme ilkesi oluşturulduktan sonra, planlanan zamanda Dosya Paylaşımlarının anlık görüntüsü alınır ve seçilen süre için kurtarma noktası korunur.

## <a name="create-an-on-demand-backup"></a>İsteğe bağlı yedekleme oluşturma

Yedekleme ilkesini yapılandırdıktan sonra, verilerinizin bir sonraki zamanlanmış yedeklemeye kadar korunmasını sağlamak için isteğe bağlı bir yedekleme oluşturmak isteyeceksiniz.

### <a name="to-create-an-on-demand-backup"></a>İsteğe bağlı yedekleme oluşturmak için

1. Dosya paylaşımı kurtarma noktalarını içeren Kurtarma Hizmetleri kasasını açın ve **Yedekleme Öğeleri**'ne tıklayın. Yedekleme Öğesi türlerinin listesi gösterilir.

   ![Yedekleme öğelerinin listesi](./media/backup-file-shares/list-of-backup-items.png)

2. Listeden **Azure Depolama (Azure Dosyaları)** öğesini seçin. Azure dosya paylaşımlarının listesi görüntülenir.

   ![Azure dosya paylaşımlarının listesi](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. Azure dosya paylaşımları listesinden istediğiniz dosya paylaşımını seçin. Seçili dosya paylaşımının Yedekleme Öğesi menüsü açılır.

   ![Seçili dosya paylaşımının yedekleme öğesi menüsü](./media/backup-file-shares/backup-item-menu.png)

4. Yedekleme Öğesi menüsünde **Şimdi Yedekle**'ye tıklayın. Bu isteğe bağlı bir yedekleme işi olduğundan, kurtarma noktasıyla ilişkilendirilmiş bir bekletme ilkesi yoktur. **Şimdi Yedekle** iletişim kutusu açılır. Kurtarma noktasını bekletmek istediğiniz son günü belirtin.

   ![Kurtarma noktası bekletme için tarih seçin](./media/backup-file-shares/backup-now-menu.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure portalı kullanarak şu işlemleri gerçekleştirdiniz:

> [!div class="checklist"]
>
> * Azure Dosyalarını yedeklemek için Kurtarma Hizmetleri kasasını yapılandırma
> * Geri yükleme noktası oluşturmak için isteğe bağlı yedekleme işini çalıştırma

Bir Azure dosya paylaşımının yedeğinden geri yüklemek için sonraki makaleye devam edin.

> [!div class="nextstepaction"]
> [Azure dosya paylaşımlarının yedeğinden geri yükleme](./backup-azure-files.md#restore-from-backup-of-azure-file-share)
