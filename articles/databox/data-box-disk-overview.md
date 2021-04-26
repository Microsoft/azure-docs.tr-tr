---
title: Microsoft Azure Data Box Disk'e genel bakış | Verilerde Microsoft Docs
description: Büyük miktarlarda verinin Azure’a aktarılmasını sağlayan bir bulut çözümü olan Azure Data Box Disk’i açıklar
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: ca46ce3355edf2b77400011d023cd6af330a3b58
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770443"
---
# <a name="what-is-azure-data-box-disk"></a>Azure Data Box Disk nedir?

Microsoft Azure Data Box Disk çözümü, terabaytlarca şirket içi veriyi Azure'a hızlı, uygun maliyetli ve güvenilir bir şekilde göndermenizi sağlar. Güvenli veri aktarımının daha hızlı gerçekleştirilmesi için 1 ila 5 katı hal sürücüsü (SSD) gönderilir. Bu 8 TB şifreli diskler, veri merkezinize bölgesel bir taşıyıcı aracılığıyla gönderilir.

Azure portal Data Box hizmeti aracılığıyla diskleri hızlı bir şekilde yapılandırabilir, bağlanabilir ve açabilirsiniz. Verilerinizi disklere kopyalayın ve diskleri Azure'a geri gönderin. Azure veri merkezinde verileriniz sürücülerden hızlı, özel bir ağ yükleme bağlantısı kullanılarak buluta yüklenir.

## <a name="use-cases"></a>Uygulama alanları

Data Box Disk'i kullanarak sıfır veya sınırlı ağ bağlantısı olan senaryolarda terabaytlarca veriyi aktarabilirsiniz. Veri taşıma işlemi tek seferlik, düzenli veya başta toplu veri aktarımı ve ardından düzenli aktarımlardan oluşabilir.

- **Tek seferlik geçiş**: Büyük miktardaki şirket içi verilerinin Azure'a taşınmasıdır. Çevrimdışı bantlardaki verilerin Azure seyrek erişimli depolama katmanında arşivlenmesi örnek olarak verilebilir.
- **Artımlı aktarım**: Data Box Disk (seed) ile toplu veri aktarımı yapıldıktan sonra ağ üzerinden artımlı aktarım işlemlerinin gerçekleştirilmesidir. Örneğin Commvault ve Data Box Disk ile yedek kopyalar Azure'a taşınabilir. Bu geçişin ardından ağ kullanarak artımlı verileri Azure depolama 'ya kopyalamadır.
- **Düzenli yüklemeler**: Düzenli olarak oluşturulan büyük miktarlardaki verilerin Azure'a taşınması gerektiği durumlardır. Enerji keşfinde petrol kuyularında ve rüzgar enerjisini santrallerinde oluşturulan videolar örnek olarak verilebilir.

### <a name="ingestion-of-data-from-data-box"></a>Data Box verilerin alımı

Azure sağlayıcıları ve Azure dışı sağlayıcılar Azure Data Box verileri alabilir. Azure Data Box veri alımı sağlayan Azure Hizmetleri şunları içerir:

- **SharePoint Online** -dosya paylaşma Içeriğinizi SharePoint Online 'a geçirmek için Azure Data Box ve SharePoint Geçiş Aracı 'Nı (SPMT) kullanın. Data Box kullanarak, WAN bağlantılarınızın bağımlılığını kaldırarak verileri aktarabilirsiniz. Daha fazla bilgi için, bkz. [Azure Data Box Heavy kullanarak dosya paylaşma Içeriğinizi SharePoint Online 'a geçirin](data-box-heavy-migrate-spo.md).

- **Azure dosya eşitleme** -Data Box dosyaları bir Azure dosya paylaşımıyla çoğaltarak verilerinize yerel erişimi sürdürirken dosya hizmetlerinizi Azure 'da merkezileştirmenizi sağlar. Daha fazla bilgi için bkz. [dağıtma Azure dosya eşitleme](../storage/file-sync/file-sync-deployment-guide.md).

-  , Data Box kullanarak Hadoop kümenizin şirket içi Hadoop Dağıtılmış dosya sistemi (bir) deposundan verileri Azure depolama alanına geçirin. Daha fazla bilgi için, [Azure Data Box ile şirket içi bir Azure depolama 'Ya geçiş](../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md)konusuna bakın.

- **Azure Backup** -kritik kurumsal verilerin büyük yedeklemelerini Azure kurtarma hizmetleri kasasına çevrimdışı mekanizmalar aracılığıyla taşımanızı sağlar. Daha fazla bilgi için bkz. [Azure Backup genel bakış](../backup/backup-overview.md).

Data Box verilerinizi Azure olmayan birçok hizmet sağlayıcısı ile kullanabilirsiniz. Örneğin:

- **[Commkasa](http://documentation.commvault.com/commvault/v11/article?p=97276.htm)** -Azure Data Box kullanarak büyük hacimlerinizi Microsoft Azure geçirmenize olanak sağlar.
- **[Veeab](https://helpcenter.veeam.com/docs/backup/hyperv/osr_adding_data_box.html?ver=100)** -Hyper-V makinenizden Data Box büyük miktarlarda veriyi yedeklemenize ve çoğaltyüklemenize olanak sağlar.

Data Box ile tümleştirilen diğer Azure dışı hizmet sağlayıcılarının listesi için bkz. [Azure Data Box Iş ortakları](https://cloudchampions.blob.core.windows.net/db-partners/PartnersTable.pdf).

## <a name="the-workflow"></a>İş akışı

Tipik iş akışı aşağıdaki adımlardan oluşur:

1. **Sipariş** -Azure Portal bir sipariş oluşturun, gönderim bilgilerini ve verileriniz Için hedef Azure Depolama hesabını belirtin. Diskler mevcutsa Azure şifreler, hazırlar ve gönderip bir gönderi takip numarası iletir.

2. **Alma**: Diskler teslim edildikten sonra paketini açın ve kopyalanacak verilerin bulunduğu bilgisayara bağlayın. Disklerin kilidini açın.

3. **Verileri kopyalama**: Verileri sürükleyip disklere kopyalayın.

4. **İade**: Diskleri hazırlayın ve Azure veri merkezine geri gönderin.

5. **Yükleme**: Veriler otomatik olarak disklerden Azure'a kopyalanır. Diskler Ulusal Standart ve Teknoloji Kurumu (NIST) yönergelerine uygun ve güvenli bir şekilde silinir.

Bu süreç boyunca tüm durum değişikliklerinde e-postayla bildirim alırsınız. Ayrıntılı akış hakkında daha fazla bilgi için bkz. [Azure portalda Data Box Disk dağıtma](data-box-disk-quickstart-portal.md).

## <a name="benefits"></a>Avantajlar

Data Box Disk, büyük miktarlarda veriyi ağ bağlantısını etkilemeden Azure'a taşımak için tasarlanmıştır. Çözümün şöyle avantajları vardır:

- **Hız**: Data Box Disk, USB 3.0 bağlantısını kullanarak 35 TB boyutunda veriyi bir haftadan daha kısa bir sürede Azure'a aktarır.

- **Kullanımı kolay**: Data Box, kullanımı kolay bir çözümdür.

  - Diskler USB bağlantısı kullanır ve kuruluma neredeyse hiç ihtiyaç duymaz.
  - Disklerin fiziksel boyutu küçük olduğundan kullanımı ve gönderimi kolaydır.
  - Diskler harici güç kaynağına ihtiyaç duymaz.
  - Diskler veri merkezi sunucusu, masaüstü veya dizüstü bilgisayarlar ile kullanılabilir.
  - Çözüm, Azure portal kullanarak uçtan uca izleme sağlar.

- **Güvenli**: Data Box Disk diskler, veriler ve hizmet için yerleşik güvenlik önlemlerine sahiptir.
  - Diskler kurcalamaya karşı korumalıdır ve güvenli güncelleştirmeyi destekler.
  - Disklerde bulunan veriler her zaman AES 128 bit ile şifrelenir.
  - Disklerin kilidi yalnızca Azure portaldan alınan anahtarla açılabilir.
  - Hizmet, Azure güvenlik özelliklerinin koruması altındadır.
  - Verileriniz Azure'a yüklendikten sonra diskler NIST 800-88r1 standartlarına göre silinir.  

Daha fazla bilgi için bkz. [Azure Data Box Disk güvenliği ve veri koruması](data-box-disk-security.md).

## <a name="features-and-specifications"></a>Özellikler ve belirtimler

| Belirtimler                                          | Description              |
|---------------------------------------------------------|--------------------------|
| Ağırlık                                                  | < 2 lb. kutu başına. Kutuda en fazla 5 disk                |
| Boyutlar                                              | Disk - 2,5" SSD |
| Kablolar                                                  | Disk başına 1 USB 3.1 kablosu|
| Sipariş başına depolama kapasitesi                              | 40 TB (kullanılabilir: ~ 35 TB)|
| Disk depolama kapasitesi                                   | 8 TB (kullanılabilir: ~ 7 TB)|
| Veri arabirimi                                          | USB   |
| Güvenlik                                                | BitLocker ile önceden şifreleme ve güvenli güncelleştirme <br> Destek anahtarı korumalı diskler <br> Veriler her zaman şifrelenir  |
| Veri aktarımı hızı                                      | dosya boyutuna bağlı olarak en fazla 430 MB/sn      |
|Yönetim                                               | Azure portalı |

## <a name="region-availability"></a>Bölge kullanılabilirliği

Bölge kullanılabilirliği hakkında daha fazla bilgi için [bölgeye göre kullanılabilir Azure ürünlerine](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)gidin. Data Box Disk, Azure Kamu Bulutu 'nda da dağıtılabilir. Daha fazla bilgi için bkz. [Azure Kamu nedir?](../azure-government/documentation-government-welcome.md).

## <a name="pricing"></a>Fiyatlandırma

Fiyatlandırma hakkında daha fazla bilgi için [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/databox/disk/)gidin.

## <a name="next-steps"></a>Sonraki adımlar

- [Data Box Disk gereksinimlerini](data-box-disk-system-requirements.md) gözden geçirin.
- [Data Box Disk sınırlarını](data-box-disk-limits.md) anlayın.
- Azure portalında [Azure Data Box Disk](data-box-disk-quickstart-portal.md)’i hızlı dağıtın.