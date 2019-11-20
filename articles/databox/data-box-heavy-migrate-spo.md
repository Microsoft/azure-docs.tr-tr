---
title: Dosya paylaşma içeriğini SharePoint Online 'a geçirmek için Azure Data Box Heavy kullanma | Microsoft Docs
description: Azure Data Box Heavy kullanarak dosya paylaşma içeriğini paylaşılan noktaya nasıl geçirebileceğinizi öğrenmek için bu öğreticiyi kullanın
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/18/2019
ms.author: alkohli
ms.openlocfilehash: 4955b28dff3193a95950912562cc3b6ec789479d
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325263"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>Dosya paylaşma içeriğinizi SharePoint Online 'a geçirmek için Azure Data Box Heavy kullanın

Dosya paylaşma içeriğinizi SharePoint Online ve OneDrive 'a kolayca geçirmek için Azure Data Box Heavy ve SharePoint Geçiş Aracı 'nı (SPMT) kullanın. Data Box Heavy kullanarak, verileri aktarmak için geniş alan ağı (WAN) bağlantılarınızın bağımlılığını kaldırabilirsiniz.

Microsoft Azure Data Box, bir cihazı Microsoft Azure portal sipariş etmenizi sağlayan bir hizmettir. Daha sonra, sunucularınızdan veri terabayta cihazdan cihaza kopyalayabilirsiniz. Microsoft 'a geri sevk ettikten sonra Verileriniz Azure 'a kopyalanır. Aktarmayı planladığınız verilerin boyutuna bağlı olarak şunları seçebilirsiniz:

- Küçük ve orta ölçekli veri kümelerine yönelik sipariş başına 35-TB kullanılabilir kapasiteye sahip [Data Box disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) .
- Orta-büyük veri kümeleri için cihaz başına 80-TB kullanılabilir kapasiteye sahip [Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) .
- Büyük veri kümeleri için cihaz başına 770-TB kullanılabilir kapasiteye sahip [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-overview) .

Bu makalede, dosya paylaşma içeriğinizi SharePoint Online 'a geçirmek için Data Box Heavy kullanma hakkında bilgi edinirsiniz.

## <a name="requirements-and-costs"></a>Gereksinimler ve maliyetler

### <a name="for-data-box-heavy"></a>Data Box Heavy için

- Data Box Heavy yalnızca Kurumsal Anlaşma (EA), bulut çözümü sağlayıcısı (CSP) veya Azure Sponsorluk teklifleri için kullanılabilir. Aboneliğiniz yukarıdaki türlerden birine sahip değilse, aboneliğinizi yükseltmek veya [Azure abonelik fiyatlandırması](https://azure.microsoft.com/pricing/)' nı görmek için Microsoft desteği başvurun.
- Data Box Heavy kullanmak için ücret ödersiniz. [Data Box Heavy fiyatlandırmasını](https://azure.microsoft.com/pricing/details/databox/heavy/)gözden geçirdiğinizden emin olun.


### <a name="for-sharepoint-online"></a>SharePoint Online için

- [SharePoint Geçiş Aracı (SPMT) Için en düşük gereksinimleri](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool)gözden geçirin.

## <a name="workflow-overview"></a>İş akışına genel bakış

Bu iş akışı, Azure Data Box Heavy ve SharePoint Online üzerindeki adımları gerçekleştirmenizi gerektirir.
Aşağıdaki adımlar Azure Data Box Heavy ilgilidir.

1. Order Azure Data Box Heavy.
2. Cihazınızı alın ve ayarlayın.
3. Şirket içi dosya paylaşımınızdan, cihazınızdaki Azure dosyaları için klasöre veri kopyalayın.
4. Kopyalama işlemi tamamlandıktan sonra, yönergelere göre cihazı geri gönderin.
5. Verilerin Azure 'a tamamen yüklenmesini bekleyin.

Aşağıdaki adımlar SharePoint Online ile ilgilidir.

6. Azure portal bir VM oluşturun ve Azure dosya paylaşımının üzerine bağlayın.
7. Azure VM 'ye SPMT aracını yüklemeyin.
8. *Kaynak*olarak Azure dosya paylaşımından yararlanarak SPMT aracını çalıştırın.
9. Aracın son adımlarını doldurun.
10. Verilerinizi doğrulayın ve onaylayın.

## <a name="use-data-box-heavy-to-copy-data"></a>Verileri kopyalamak için Data Box Heavy kullanma

Data Box Heavy verileri kopyalamak için aşağıdaki adımları uygulayın.

1. [Data Box Heavy sıralayın](data-box-heavy-deploy-ordered.md).
2. Data Box Heavy aldıktan sonra, [Data Box Heavy ayarlayın](data-box-heavy-deploy-set-up.md). Cihazlarınızın her ikisini de kablosunu ve yapılandıracaksınız.
3. [Azure Data Box Heavy verileri kopyalayın](data-box-heavy-deploy-copy-data.md). Kopyalama sırasında aşağıdakileri yaptığınızdan emin olun:

    - Verileri kopyalamak için yalnızca Data Box Heavy *StorageAccountName_AzFile* klasörünü kullanın. Bunun nedeni, verilerin blok Blobları veya sayfa Blobları değil bir Azure dosya paylaşımında bitmesini istemezsiniz.
    - Dosyaları *StorageAccountName_AzFile* klasörü içindeki bir klasöre kopyalayın. *StorageAccountName_AzFile* klasörünün içindeki bir alt klasör bir dosya paylaşma oluşturur. Doğrudan *StorageAccountName_AzFile* klasörüne kopyalanan dosyalar başarısız olur ve blok Blobları olarak karşıya yüklenir. Bu, bir sonraki adımda sanal makinenize bağlayacaksınız dosya paylaşımıdır.
    - Verileri Data Box Heavy her iki düğümüne da kopyalayın.
3. Cihazınızda [göndermeye hazırlama](data-box-heavy-deploy-picked-up.md#prepare-to-ship) çalıştırın. Başarılı bir hazırlanması, dosyaların Azure 'a başarılı bir şekilde yüklenmesini sağlar.
4. [Cihazı döndürün](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back).
5. [Azure 'a veri yüklemeyi doğrulayın](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure).

## <a name="use-spmt-to-migrate-data"></a>Verileri geçirmek için SPMT kullanma

Azure veri ekibinin onay aldıktan sonra veri kopyanızın tamamlandığını, verilerinizi SharePoint Online 'a geçirmeye devam edin.

En iyi performans ve bağlantı için bir Azure sanal makinesi (VM) oluşturmanızı öneririz.

1. Azure portal oturum açın ve sonra [bir sanal makine oluşturun](../virtual-machines/windows/quick-create-portal.md).
2. [Azure dosya PAYLAŞıMıNDAN sanal makineye bağlayın](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer).
3. [SharePoint geçiş aracını indirin](https://spmtreleasescus.blob.core.windows.net/install/default.htm) ve Azure VM 'nize yükleyin.
4. SharePoint geçiş aracını başlatın. **Oturum aç** ' a tıklayın ve Office 365 kullanıcı adınızı ve parolanızı girin.
5. **Verileriniz nerede**olduğu sorulduğunda **dosya paylaşma**' yı seçin. Verilerinizin bulunduğu Azure dosya paylaşımınızın yolunu girin.
6. Hedef konumunuz dahil olmak üzere, kalan istemleri normal olarak izleyin. Daha fazla bilgi için, [SharePoint Geçiş Aracı 'nı kullanma](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool)sayfasına gidin.

> [!IMPORTANT]
> - Verilerin SharePoint Online 'a alındığı hız, verilerinizin zaten Azure 'da olmasına bakılmaksızın çeşitli faktörlerden etkilenir. Bu faktörleri anlamak, geçişinizin verimliliğini planlayıp en üst düzeye çıkarmanıza yardımcı olur.  Daha fazla bilgi için [SharePoint Online ve OneDrive geçiş hızına](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed)gidin.
> - Verileri SharePoint Online 'a geçirirken dosyalardaki mevcut izinleri kaybetme riski vardır. Tarafından *oluşturulan* ve tarafından *değiştirilen tarih*gibi belirli meta verileri de kaybedebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Data Box Heavy sıralama](./data-box-heavy-deploy-ordered.md)