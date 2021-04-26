---
title: include dosyası
description: dosya dahil etme
services: backup
author: v-amallick
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 10/18/2018
ms.author: v-amallick
ms.custom: include file
ms.openlocfilehash: c126c349897016a00753b402b696c60fdd7b1b2e
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294148"
---
## <a name="create-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasası oluşturma

Kurtarma Hizmeti kasası, zaman içinde oluşturulan kurtarma noktalarını depolayan ve yedekleme ile ilgili işlemleri gerçekleştirmek için bir arabirim sağlayan yönetim varlığıdır. Bu işlemler arasında isteğe bağlı yedekleme, geri yükleme gerçekleştirme ve yedekleme ilkesi oluşturma sayılabilir.

Kurtarma Hizmetleri kasası oluşturmak için aşağıdaki adımları izleyin.

1. [Azure portalında](https://portal.azure.com/) aboneliğinizde oturum açın.

1. Sol taraftaki menüden **Tüm hizmetler**'i seçin.

    ![Tüm Hizmetler’i seçin](./media/backup-create-rs-vault/click-all-services.png)

1. **Tüm hizmetler** iletişim kutusuna *Kurtarma Hizmetleri* yazın. Kaynak listesi, yazdıklarınıza göre filtrelenir. Kaynak listesinde **Kurtarma Hizmetleri kasaları**'nı seçin.

    ![Kurtarma Hizmetleri kasaları yazın ve seçin](./media/backup-create-rs-vault/all-services.png)

    Abonelikteki Kurtarma Hizmetleri kasalarının listesi görünür.

1. **Kurtarma Hizmetleri kasası** panosunda **Ekle**'yi seçin.

    ![Kurtarma Hizmetleri kasası ekleme](./media/backup-create-rs-vault/add-button-create-vault.png)

    **Kurtarma Hizmetleri kasası** iletişim kutusu açılır. **Ad**, **Abonelik**, **Kaynak grubu** ve **Konum** için değer girin.

    ![Kurtarma Hizmetleri kasasını yapılandırma](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Ad**: Kasayı tanımlamak için kolay bir ad girin. Adın Azure aboneliği için benzersiz olması gerekir. En az 2, en fazla 50 karakter uzunluğunda bir ad belirtin. Ad bir harf ile başlamalıdır ve yalnızca harf, rakam ve kısa çizgi içerebilir.
   - **Abonelik**: Kullanılacak aboneliği seçin. Tek bir aboneliğiniz varsa yalnızca o seçenek görüntülenir. Hangi aboneliğin kullanılacağından emin değilseniz varsayılan (önerilen) aboneliği kullanın. Yalnızca iş veya okul hesabınızın birden çok Azure aboneliği ile ilişkili olması durumunda birden çok seçenek olur.
   - **Kaynak grubu**: Mevcut kaynak grubunu kullanın ya da yeni bir tane oluşturun. Aboneliğinizdeki kullanılabilir kaynak gruplarının listesini görmek için **Var olanı kullan**'ı seçip açılan listeden bir kaynak grubu seçin. Yeni bir kaynak grubu oluşturmak için **Yeni oluştur**'u seçip bir ad girin. Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure Resource Manager'a genel bakış](../articles/azure-resource-manager/management/overview.md).
   - **Konum**: Kasa için coğrafi bölgeyi seçin. Herhangi bir veri kaynağını korumak üzere bir kasa oluşturmak için kasa ile veri kaynağının aynı bölgede olması *şarttır*.

      > [!IMPORTANT]
      > Veri kaynağınızın konumundan emin değilseniz iletişim kutusunu kapatın. Portalda kaynaklarınızın bulunduğu listeye gidin. Birden çok bölgede veri kaynağınız varsa her bölgede bir Kurtarma Hizmetleri kasası oluşturun. Kasayı önce birinci konumda oluşturun, ardından diğer konumlara geçin. Yedekleme verilerinin depolanacağı depolama hesaplarını belirtmenize gerek yoktur. Kurtarma Hizmetleri kasası ve Azure Backup bunu otomatik olarak ayarlar.
      >
      >

1. Değerleri girdikten sonra **Gözden geçir ve oluştur**’u seçin.

    ![Kurtarma Hizmetleri Kasası oluşturma işleminde gözden geçir + Oluştur düğmesini gösteren ekran görüntüsü.](./media/backup-create-rs-vault/review-and-create.png)

1. Kurtarma Hizmetleri kasasını oluşturmaya hazırsanız **Oluştur**'u seçin.

    ![Kurtarma Hizmetleri kasasını oluşturma](./media/backup-create-rs-vault/click-create-button.png)

    Kurtarma Hizmetleri kasasının oluşturulması biraz zaman alabilir. Portalın sağ üst kısmındaki **Bildirimler** alanından durum bildirimlerini takip edebilirsiniz. Kasanız oluşturulduktan sonra Kurtarma Hizmetleri kasaları listesinde görünür. Kasanızı görmüyorsanız **Yenile**'yi seçin.

     ![Yedekleme kasası listesini yenileme](./media/backup-create-rs-vault/refresh-button.png)

>[!IMPORTANT]
> Kasada yedekleme yapılandırmadan önce **Depolama çoğaltma türü** ve **Güvenlik ayarları** için varsayılan değerleri mutlaka gözden geçirmeniz önerilir. Daha fazla bilgi için bkz. [Depolama yedekliliği](../articles/backup/backup-create-rs-vault.md#set-storage-redundancy).