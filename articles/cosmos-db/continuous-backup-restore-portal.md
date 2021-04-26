---
title: Azure Cosmos DB Azure portal kullanarak sürekli yedeklemeyi ve zaman içindeki bir noktaya geri yüklemeyi yapılandırın.
description: Azure portal kullanarak geri yükleme noktasını belirlemeyi ve sürekli yedeklemeyi yapılandırmayı öğrenin. Canlı ve silinmiş bir hesabın nasıl geri yükleneceğini gösterir.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/05/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 707ef9f60891c1da7c13638e233ee74e78fc20dd
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106283946"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-portal"></a>Sürekli yedekleme ve zaman noktası geri yükleme (Önizleme) ile yapılandırma ve yönetme-Azure portal kullanma
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB için zaman noktası geri yükleme özelliği (sürekli yedekleme modu) Şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB zaman içinde geri yükleme özelliği (Önizleme), bir kapsayıcı içindeki yanlışlıkla yapılan bir değişikliği kurtarmanıza, silinen bir hesabı, veritabanını veya kapsayıcıyı geri yüklemeyi veya herhangi bir bölgeye geri yüklemeyi (yedeklemelerin varolduğu) sağlar. Sürekli yedekleme modu, son 30 gün içinde herhangi bir zaman noktasına geri yükleme yapmanıza olanak sağlar.

Bu makalede geri yükleme noktasının nasıl tanımlanması ve Azure portal kullanarak sürekli yedeklemenin nasıl yapılandırılacağı açıklanır.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>Sürekli yedekleme ile hesap sağlama

Yeni bir Azure Cosmos DB hesabı oluştururken, **yedekleme ilkesi** seçeneği için, yeni hesap için zaman içinde geri yükleme işlevini etkinleştirmek üzere **sürekli** mod ' u seçin. Bu özellik hesap için etkinleştirildikten sonra, tüm veritabanları ve kapsayıcılar sürekli yedekleme için kullanılabilir. Verilerin zaman içinde geri yüklenmesi sayesinde, veriler her zaman yeni bir hesaba geri yüklenir ve şu anda mevcut bir hesaba geri yükleme yapamazsınız.

:::image type="content" source="./media/continuous-backup-restore-portal/configure-continuous-backup-portal.png" alt-text="Sürekli yedekleme yapılandırması ile Azure Cosmos DB bir hesap sağlayın." border="true":::

## <a name="backup-storage-redundancy"></a>Yedekleme depolama yedekliliği

Azure Cosmos DB, varsayılan olarak, sürekli modda yedek verileri yerel olarak yedekli depolama Blobları 'nda depolar. Bölge artıklığı yapılandırılmış bölgelerde, yedekleme, bölgesel olarak yedekli depolama Blobları 'nda depolanır. Bu modda yedekleme depolama yedekliliği güncelleştiremezsiniz.

## <a name="restore-a-live-account-from-accidental-modification"></a><a id="restore-live-account"></a>Canlı bir hesabı yanlışlıkla değişiklikten geri yükleme

Canlı bir hesabı veya seçili veritabanlarını ve kapsayıcıları geri yüklemek için Azure portal kullanabilirsiniz. Verilerinizi geri yüklemek için aşağıdaki adımları kullanın:

1. [Azure Portal](https://portal.azure.com/) oturum açın
1. Azure Cosmos DB hesabınıza gidin ve **zaman Içinde nokta geri yükleme** bölmesini açın.

   > [!NOTE]
   > Azure portal içindeki geri yükleme bölmesi yalnızca izniniz varsa doldurulur `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` . Bu iznin nasıl ayarlanacağı hakkında daha fazla bilgi edinmek için [yedekleme ve geri yükleme izinleri](continuous-backup-restore-permissions.md) makalesine bakın.

1. Geri yüklemek için aşağıdaki ayrıntıları girin:

   * **Geri yükleme noktası (UTC)** : son 30 gün içinde zaman damgası. Hesabın bu zaman damgasında mevcut olması gerekir. Geri yükleme noktasını UTC olarak belirtebilirsiniz. Geri yüklemek istediğiniz zaman ikincisine yakın olabilir. [Geri yükleme noktasını tanımlama](#event-feed)hakkında yardım almak Için **burayı tıklatın** bağlantısını seçin.

   * **Konum** : hesabın geri yüklendiği hedef bölge. Hesabın belirtilen zaman damgasında bu bölgede mevcut olması gerekir (örneğin, Batı ABD veya Doğu ABD). Bir hesap yalnızca kaynak hesabın varolduğu bölgelere geri yüklenebilir.

   * **Kaynağı geri yükle** – geri yüklemek için **Tüm hesap** veya **Seçili bir veritabanı/kapsayıcı** seçebilirsiniz. Veritabanları ve kapsayıcılar verilen zaman damgasında bulunmalıdır. Seçilen geri yükleme noktası ve konuma göre, geri yükleme kaynakları doldurulur, bu da kullanıcının geri yüklenmesi gereken belirli veritabanlarını veya kapsayıcıları seçmesine olanak sağlar.

   * **Kaynak grubu** -hedef hesabın oluşturulacağı ve geri yükleneceği kaynak grubu. Kaynak grubu zaten var olmalıdır.

   * **Hedef hesabı geri yükle** : hedef hesap adı. Yeni bir hesap oluştururken hedef hesap adının aynı yönergeleri izlemesi gerekir. Bu hesap, geri yükleme işlemi tarafından, kaynak hesabınızın bulunduğu bölgedeki aynı bölgede oluşturulacaktır.
 
   :::image type="content" source="./media/continuous-backup-restore-portal/restore-live-account-portal.png" alt-text="Canlı bir hesabı yanlışlıkla değişiklik Azure portal geri yükleyin." border="true":::

1. Yukarıdaki parametreleri seçtikten sonra, bir geri yüklemeyi kapatmak için **Gönder** düğmesini seçin. Geri yükleme maliyeti, belirtilen bölgedeki depolama alanı için veri miktarına ve ücretlere dayalı olarak bir zaman ücretlidir. Daha fazla bilgi edinmek için [fiyatlandırma](continuous-backup-restore-introduction.md#continuous-backup-pricing) bölümüne bakın.

## <a name="use-event-feed-to-identify-the-restore-time"></a><a id="event-feed"></a>Geri yükleme süresini tanımlamak için olay akışını kullanın

Geri yükleme noktası süresi Azure portal doldurma sırasında geri yükleme noktası belirlemek için yardıma ihtiyacınız varsa, **buraya tıklayın** bağlantısını seçin ve bunu olay akışı dikey penceresine götürür. Olay akışı, kaynak hesabın veritabanları ve kapsayıcılarındaki oluşturma, değiştirme, silme olaylarının tam bir listesini sağlar. 

Örneğin, belirli bir kapsayıcının silinmeden veya Güncellenme öncesindeki noktaya geri yüklemek istiyorsanız bu olay akışını kontrol edin. Olaylar, oluşma sırasında kronolojik olarak azalan zaman içinde görüntülenir ve en son olaylar en üstte bulunur. Sonuçlara göz atabilir ve zamandan daha önce veya sonra zamandan daha dar süreyi seçebilirsiniz.

:::image type="content" source="./media/continuous-backup-restore-portal/event-feed-portal.png" alt-text="Geri yükleme noktası süresini tanımlamak için olay akışını kullanın." border="true":::

> [!NOTE]
> Olay akışı, öğe kaynaklarında yapılan değişiklikleri görüntülemez. Geri yükleme için her zaman zaman damgasını son 30 gün içinde el ile belirtebilirsiniz (o anda hesap var olduğu sürece).

## <a name="restore-a-deleted-account"></a><a id="restore-deleted-account"></a>Silinen bir hesabı geri yükleme

Silinen bir hesabı silme işlemini 30 gün içinde tamamen geri yüklemek için Azure portal kullanabilirsiniz. Silinen bir hesabı geri yüklemek için aşağıdaki adımları kullanın:

1. [Azure Portal](https://portal.azure.com/) oturum açın
1. Genel arama çubuğunda *Azure Cosmos DB* kaynak arayın. Tüm mevcut hesaplarınızı listeler.
1. Sonra **geri yükle** düğmesini seçin. Geri yükleme bölmesi, silme zamanından itibaren 30 gün olan saklama süresi içinde geri yüklenebilen silinmiş hesapların bir listesini görüntüler.
1. Geri yüklemek istediğiniz hesabı seçin.

   :::image type="content" source="./media/continuous-backup-restore-portal/restore-deleted-account-portal.png" alt-text="Silinen bir hesabı Azure portal geri yükleyin." border="true":::

   > [!NOTE]
   > Note: Azure portal içindeki geri yükleme bölmesi yalnızca izniniz varsa doldurulur `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` . Bu iznin nasıl ayarlanacağı hakkında daha fazla bilgi edinmek için [yedekleme ve geri yükleme izinleri](continuous-backup-restore-permissions.md) makalesine bakın.

1. Geri yüklenecek bir hesap seçin ve silinen bir hesabı geri yüklemek için aşağıdaki ayrıntıları girin:

   * **Geri yükleme noktası (UTC)** : son 30 gün içinde zaman damgası. Bu zaman damgasında hesap var olmalıdır. Geri yükleme noktasını UTC olarak belirtin. Geri yüklemek istediğiniz zaman ikincisine yakın olabilir.

   * **Konum** : hesabın geri yüklenmesi gereken hedef bölge. Kaynak hesap, belirtilen zaman damgasında bu bölgede bulunmalıdır. Örnek Batı ABD veya Doğu ABD.  

   * **Kaynak grubu** -hedef hesabın oluşturulacağı ve geri yükleneceği kaynak grubu. Kaynak grubu zaten var olmalıdır.

   * **Hedef hesabı geri yükle** – hedef hesap adının, yeni bir hesap oluştururken kullandığınız yönergeleri izlemesi gerekir. Bu hesap, geri yükleme işlemi tarafından, kaynak hesabınızın bulunduğu bölgedeki aynı bölgede oluşturulacaktır.

## <a name="track-the-status-of-restore-operation"></a><a id="track-restore-status"></a>Geri yükleme işleminin durumunu izleme

Geri yükleme işlemini başlattıktan sonra, portalın sağ üst köşesindeki **bildirim** zil simgesini seçin. Geri yüklenmekte olan hesabın durumunu görüntüleyen bir bağlantı sağlar. Geri yükleme devam *ederken, hesabın durumu oluşturulacak, geri* yükleme işlemi tamamlandıktan sonra hesap durumu *çevrimiçi* olarak değişir.

:::image type="content" source="./media/continuous-backup-restore-portal/track-restore-operation-status.png" alt-text="İşlem tamamlandığında, geri yüklenen hesabın durumu, oluşturma işleminden çevrimiçi olarak değişir." border="true":::

## <a name="next-steps"></a>Sonraki adımlar

* [Azure CLI](continuous-backup-restore-command-line.md), [PowerShell](continuous-backup-restore-powershell.md)veya [Azure Resource Manager](continuous-backup-restore-template.md)kullanarak sürekli yedeklemeyi yapılandırın ve yönetin.
* [Sürekli yedekleme modunun kaynak modeli](continuous-backup-restore-resource-model.md)
* Sürekli yedekleme moduyla verileri geri yüklemek için gereken [Izinleri yönetin](continuous-backup-restore-permissions.md) .
