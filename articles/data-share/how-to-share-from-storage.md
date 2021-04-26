---
title: Azure Blob Depolama ve Azure Data Lake Storage'dan verileri paylaşma ve alma
description: Azure Blob depolama ve Azure Data Lake Storage verileri paylaşmayı ve almayı öğrenin.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 04/20/2021
ms.openlocfilehash: 59c1ca67c9e93b62890512cda647ffcdf7712f9a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819277"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>Azure Blob Depolama ve Azure Data Lake Storage'dan verileri paylaşma ve alma

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

Azure veri paylaşımı, bir depolama hesabından anlık görüntü tabanlı paylaşımı destekler. Bu makalede, Azure Blob depolama, Azure Data Lake Storage 1. ve Azure Data Lake Storage 2. nasıl paylaşılacağını ve verilerin alınacağını açıklamaktadır.

Azure veri paylaşımı, Azure Data Lake gen1 ve Azure Data Lake Gen2 ' den dosya, klasör ve dosya sistemlerinin paylaşılmasını destekler. Ayrıca, Azure Blob depolamadan blob, klasör ve kapsayıcı paylaşımını da destekler. Blok, ekleme veya sayfa bloblarını paylaşabilir ve blok Blobları olarak alınır. Bu kaynaklardan paylaşılan veriler Azure Data Lake Gen2 veya Azure Blob depolama alanı tarafından alınabilir.

Dosya sistemleri, kapsayıcılar veya klasörler anlık görüntü tabanlı paylaşımda paylaşıldığında, veri tüketicileri, paylaşım verilerinin tam bir kopyasını yapmayı seçebilirler. Ya da yalnızca yeni veya güncelleştirilmiş dosyaları kopyalamak için Artımlı anlık görüntü özelliğini kullanabilirler. Artımlı anlık görüntü özelliği, dosyaların son değiştirilme zamanına göre belirlenir. 

Bir anlık görüntü sırasında aynı ada sahip varolan dosyaların üzerine yazılır. Kaynaktan silinen bir dosya hedefte silinmez. Kaynaktaki boş alt klasörler hedefe kopyalanmaz. 

## <a name="share-data"></a>Veri paylaşımı

Azure veri paylaşımından verileri paylaşmak için aşağıdaki bölümlerde bulunan bilgileri kullanın. 
### <a name="prerequisites-to-share-data"></a>Veri paylaşma önkoşulları

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
* Alıcının Azure oturum açma e-posta adresini bulun. Alıcının e-posta diğer adı, amacınız için çalışmaz.
* Kaynak Azure veri deposu, veri paylaşma kaynağını oluşturacağınız olandan farklı bir Azure aboneliğinde yer alıyorsa, [Microsoft. DataShare kaynak sağlayıcısını](concepts-roles-permissions.md#resource-provider-registration) Azure veri deposunun bulunduğu abonelikte kaydedin. 

### <a name="prerequisites-for-the-source-storage-account"></a>Kaynak depolama hesabı önkoşulları

* Azure Depolama hesabı. Henüz bir hesabınız yoksa, [bir hesap oluşturun](../storage/common/storage-account-create.md).
* Depolama hesabına yazma izni. Yazma izni *Microsoft. Storage/storageAccounts/Write*' da bulunur. Katkıda bulunan rolünün bir parçasıdır.
* Depolama hesabına rol ataması ekleme izni. Bu izin, *Microsoft. Authorization/role atamaları/yazma*' dır. Sahip rolünün bir parçasıdır. 

### <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

### <a name="create-a-data-share-account"></a>Veri paylaşma hesabı oluşturma

Azure Kaynak grubunda bir Azure veri paylaşma kaynağı oluşturun.

1. Portalın sol üst köşesinde, menüyü açın ve **kaynak oluştur** ' u (+) seçin.

1. *Veri paylaşımında* arama yapın.

1. **Veri paylaşma** ve **oluşturma** seçeneğini belirleyin.

1. Azure veri paylaşma kaynağınızın temel ayrıntılarını sağlayın: 

     **Ayar** | **Önerilen değer** | **Alan açıklaması**
    |---|---|---|
    | Abonelik | Aboneliğiniz | Veri paylaşma hesabınız için bir Azure aboneliği seçin.|
    | Kaynak grubu | *test-resource-group* | Mevcut bir kaynak grubunu kullanın veya bir kaynak grubu oluşturun. |
    | Konum | *Doğu ABD 2* | Veri paylaşma hesabınız için bir bölge seçin.
    | Name | *datashareaccount* | Veri paylaşma hesabınızı adlandırın. |
    | | |

1. Veri paylaşma hesabınızı sağlamak için **gözden geçir +**  >  **Oluştur** oluştur ' u seçin. Yeni bir veri paylaşma hesabının sağlanması genellikle yaklaşık 2 dakika sürer. 

1. Dağıtım tamamlandığında **Kaynağa git** seçeneğini belirleyin.

### <a name="create-a-share"></a>Bir paylaşma oluşturun

1. Veri paylaşımınıza **genel bakış** sayfasına gidin.

   :::image type="content" source="./media/share-receive-data.png" alt-text="Veri paylaşımının genel bakışını gösteren ekran görüntüsü.":::

1. **Verilerinizi paylaşmayı Başlat**' ı seçin.

1. **Oluştur**’u seçin.   

1. Paylaşımınızla ilgili ayrıntıları sağlayın. Bir ad, paylaşma türü, içerik paylaşma açıklaması ve kullanım koşulları (isteğe bağlı) belirtin. 

    ![Veri paylaşımının ayrıntılarını gösteren ekran görüntüsü.](./media/enter-share-details.png "Veri paylaşımının ayrıntılarını girin.") 

1. **Devam**’ı seçin.

1. Paylaşımınıza veri kümeleri eklemek için **veri kümesi Ekle**' yi seçin. 

    ![Paylaşımınıza veri kümelerinin nasıl ekleneceğini gösteren ekran görüntüsü.](./media/datasets.png "Kümelerinin.")

1. Eklemek için bir veri kümesi türü seçin. Veri kümesi türlerinin listesi, önceki adımda anlık görüntü tabanlı paylaşım veya yerinde paylaşım ' ın seçili olmasına bağlı olarak değişir. 

    ![Veri kümesi türünün nerede seçileceğini gösteren ekran görüntüsü.](./media/add-datasets.png "Veri kümeleri ekleyin.")    

1. Paylaşmak istediğiniz nesneye gidin. Sonra **veri kümesi Ekle**' yi seçin. 

    ![Paylaşılacak bir nesnenin nasıl seçileceğini gösteren ekran görüntüsü.](./media/select-datasets.png "Veri kümelerini seçin.")    

1. **Alıcılar** sekmesinde, **Alıcı Ekle**' yi seçerek veri tüketicinizin e-posta adresini ekleyin. 

    ![Alıcı e-posta adreslerinin nasıl ekleneceğini gösteren ekran görüntüsü.](./media/add-recipient.png "Alıcı ekleyin.") 

1. **Devam**’ı seçin.

1. Bir anlık görüntü paylaşma türü seçtiyseniz, veri tüketicisi için verilerinizi güncelleştirmek üzere anlık görüntü zamanlaması ayarlayabilirsiniz. 

    ![Anlık görüntü zamanlama ayarlarını gösteren ekran görüntüsü.](./media/enable-snapshots.png "Anlık görüntüleri etkinleştirin.") 

1. Bir başlangıç saati ve yinelenme aralığı seçin. 

1. **Devam**’ı seçin.

1. **Gözden geçir + oluştur** sekmesinde paket içeriklerinizi, ayarlarınızı, alıcılarını ve eşitleme ayarlarınızı gözden geçirin. Ardından **Oluştur**’u seçin.

Artık Azure veri paylaşımınızı oluşturdunuz. Veri paylaşımınızın alıcısı davetinizi kabul edebilir. 

## <a name="receive-data"></a>Veri Al

Aşağıdaki bölümlerde, paylaşılan verilerin nasıl alınacağı açıklanır.
### <a name="prerequisites-to-receive-data"></a>Veri alma önkoşulları
Bir veri paylaşma davetini kabul etmeden önce, aşağıdaki önkoşullara sahip olduğunuzdan emin olun: 

* Azure aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)oluşturun.
* Azure 'dan gelen bir davet. E-postanın konusu "Azure Data Share daveti" olmalıdır *\<yourdataprovider\@domain.com>* .
* İçinde kayıtlı bir [Microsoft. DataShare kaynak sağlayıcısı](concepts-roles-permissions.md#resource-provider-registration) :
    * Veri paylaşma kaynağı oluşturacağınız Azure aboneliği.
    * Hedef Azure veri mağazalarınızın bulunduğu Azure aboneliği.

### <a name="prerequisites-for-a-target-storage-account"></a>Hedef depolama hesabı önkoşulları

* Azure Depolama hesabı. Henüz bir [hesabınız yoksa hesap oluşturun](../storage/common/storage-account-create.md). 
* Depolama hesabına yazma izni. Bu izin *Microsoft. Storage/storageAccounts/Write*' da bulunur. Katkıda bulunan rolünün bir parçasıdır. 
* Depolama hesabına rol ataması ekleme izni. Bu atama, *Microsoft. Authorization/role atamaları/yazma*' dır. Sahip rolünün bir parçasıdır.  

### <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

### <a name="open-an-invitation"></a>Bir davet açın

E-posta veya doğrudan Azure portal bir davet açabilirsiniz.

1. E-postadaki bir daveti açmak için, veri sağlayıcınızdan bir davet için gelen kutunuza bakın. Microsoft Azure davetinde, "Azure veri paylaşma daveti" başlığı altında bulunur *\<yourdataprovider\@domain.com>* . Azure 'da davetinizi görmek için **daveti görüntüle** ' yi seçin. 

   Azure portal bir davet açmak için, *veri paylaşma davetlerini* arayın. Veri paylaşma davetlerinin listesini görürsünüz.

   ![Azure portal davetlerin listesini gösteren ekran görüntüsü.](./media/invitations.png "Davetlerin listesi.") 

1. Görüntülemek istediğiniz paylaşmayı seçin. 

### <a name="accept-an-invitation"></a>Daveti kabul et
1. **Kullanım koşulları** dahil olmak üzere tüm alanları gözden geçirin. Koşulları kabul ediyorsanız onay kutusunu seçin. 

   ![Kullanım koşulları alanını gösteren ekran görüntüsü.](./media/terms-of-use.png "Kullanım koşulları.") 

1. **Hedef veri paylaşma hesabı** altında, veri paylaşımınızı dağıtacağınız aboneliği ve kaynak grubunu seçin. Ardından aşağıdaki alanları girin:

   * **Veri paylaşma hesabı alanında,** veri paylaşma hesabınız yoksa **Yeni oluştur** ' u seçin. Aksi takdirde, veri paylaşımınızı kabul edecek mevcut bir veri paylaşma hesabı seçin. 

   * **Alınan paylaşma adı** alanında, varsayılan olarak belirtilen veri sağlayıcısını veya alınan paylaşımın yeni bir adını belirtin. 

1. **Kabul et ve Yapılandır ' ı** seçin. Bir paylaşma aboneliği oluşturulur. 

   ![Yapılandırma seçeneklerinin kabul edileceği yeri gösteren ekran görüntüsü.](./media/accept-options.png "Kabul etme seçenekleri") 

    Alınan paylaşma, veri paylaşma hesabınızda görünür. 

    Daveti kabul etmek istemiyorsanız **Reddet**' i seçin. 

### <a name="configure-a-received-share"></a>Alınan bir paylaşma yapılandırma
Veri alacak bir konum yapılandırmak için bu bölümdeki adımları izleyin.

1. **Veri kümeleri** sekmesinde, hedef atamak istediğiniz veri kümesinin yanındaki onay kutusunu işaretleyin. Hedef veri deposu seçmek için **hedef eşleme** ' yi seçin. 

   ![Hedefe nasıl eşleneceğini gösteren ekran görüntüsü.](./media/dataset-map-target.png "Hedefe eşleyin.") 

1. Veriler için bir hedef veri deposu seçin. Alınan verilerdeki dosyalarla aynı yola ve ada sahip hedef veri deposundaki dosyaların üzerine yazılacak. 

   ![Hedef depolama hesabının nerede seçileceğini gösteren ekran görüntüsü.](./media/map-target.png "Hedef depolama alanı.") 

1. Anlık görüntü tabanlı paylaşım için, veri sağlayıcısı verileri düzenli olarak güncelleştirmek üzere bir anlık görüntü zamanlaması kullanıyorsa, zamanlamayı **anlık görüntü zamanlaması** sekmesinden etkinleştirebilirsiniz. Anlık görüntü zamanlamasının yanındaki kutuyu seçin. Ardından **Etkinleştir**' i seçin. Zamanlanan ilk anlık görüntünün, zamanlama zamanının bir dakikası içinde ve sonraki anlık görüntülerin zamanlanan sürenin saniye içinde başlatılacağını unutmayın.

   ![Anlık görüntü zamanlamasının nasıl etkinleştirileceğini gösteren ekran görüntüsü.](./media/enable-snapshot-schedule.png "Anlık görüntü zamanlamasını etkinleştirin.")

### <a name="trigger-a-snapshot"></a>Anlık görüntü tetikleyin
Bu bölümdeki adımlar yalnızca anlık görüntü tabanlı paylaşım için geçerlidir.

1. **Ayrıntılar** sekmesinden bir anlık görüntü tetikleyebilirsiniz. Sekmesinde **tetikleyici anlık görüntüsünü** seçin. Verilerinizin tam anlık görüntüsünü veya artımlı anlık görüntüsünü tetiklemeyi seçebilirsiniz. Veri sağlayıcınızdan ilk kez veri alıyorsanız **Tam kopya**' ı seçin. Bir anlık görüntü çalışırken, sonraki anlık görüntüler bir önceki işlem tamamlanana kadar başlatılmaz.

   ![Tetikleyici anlık görüntü seçimini gösteren ekran görüntüsü.](./media/trigger-snapshot.png "Görüntüyü tetikle.") 

1. Son çalıştırma durumu *başarılı* olduğunda, alınan verileri görüntülemek için hedef veri deposuna gidin. **Veri kümeleri**' ni seçin ve hedef yolu bağlantısını seçin. 

   ![Tüketici veri kümesi eşlemesini gösteren ekran görüntüsü.](./media/consumer-datasets.png "Tüketici veri kümesi eşleme.") 

### <a name="view-history"></a>Geçmişi görüntüleme
Anlık görüntülerinizin geçmişini yalnızca anlık görüntü tabanlı paylaşımda görüntüleyebilirsiniz. Geçmişi görüntülemek için **Geçmiş** sekmesini açın. Burada, son 30 gün içinde oluşturulan tüm anlık görüntülerin geçmişini görürsünüz. 

## <a name="storage-snapshot-performance"></a>Depolama anlık görüntüsü performansı
Depolama anlık görüntüsü performansı, dosya sayısına ve paylaşılan verilerin boyutuna ek olarak bir dizi faktörden etkilenir. Her zaman kendi performans testinizi uygulamanız önerilir. Aşağıda, performansı etkileyen bazı örnek faktörler verilmiştir.

* Kaynak ve hedef veri depolarına eşzamanlı erişim.  
* Kaynak ve hedef veri depolarının konumu. 
* Artımlı anlık görüntü için, paylaşılan veri kümesindeki dosya sayısı, son başarılı anlık görüntüden sonraki son değiştirilme zamanına sahip dosyaların listesini bulmak için gereken süreyi etkileyebilir. 


## <a name="next-steps"></a>Sonraki adımlar
Azure veri paylaşma hizmetini kullanarak bir depolama hesabından nasıl veri alacağınızı ve alacağınız hakkında daha fazla öğrendiniz. Diğer veri kaynaklarından paylaşma hakkında bilgi edinmek için bkz. [desteklenen veri depoları](supported-data-stores.md).
