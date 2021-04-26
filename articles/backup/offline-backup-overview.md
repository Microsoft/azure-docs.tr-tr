---
title: Çevrimdışı yedeklemeye genel bakış
description: Çevrimdışı yedeklemenin bileşenleri hakkında bilgi edinin. Azure Içeri/dışarı aktarma hizmeti temel alınarak Azure Data Box ve çevrimdışı yedekleme tabanlı çevrimdışı yedekleme içerirler.
ms.topic: conceptual
ms.date: 1/28/2020
ms.custom: references_regions
ms.openlocfilehash: 7c65cf6b36af3057fb06c6a6584fa458b1030c72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98704144"
---
# <a name="overview-of-offline-backup"></a>Çevrimdışı yedeklemeye genel bakış

Bu makalede, çevrimdışı yedeklemeye genel bakış sunulmaktadır.

Azure 'a ilk tam yedeklemeler genellikle büyük miktarda veriyi çevrimiçi olarak aktarır ve yalnızca artımlı değişiklikleri aktarmaya yönelik sonraki yedeklemelere kıyasla daha fazla ağ bant genişliği gerektirir. Belirli coğrafi bölgelerde bulunan uzak ofisler veya veri merkezleri her zaman yeterli ağ bant genişliğine sahip değildir. Bu nedenle, bu ilk yedeklemeler birkaç gün sürer. Bu süre boyunca yedeklemeler, şirket içi veri merkezinde çalışan uygulamalar için sağlanan ağı sürekli olarak kullanır.

Azure Backup, ağ bant genişliği kullanılmadan ilk yedekleme verilerini çevrimdışı olarak aktaran çevrimdışı yedeklemeyi destekler. Yedekleme verilerini fiziksel depolama cihazlarına kopyalamak için bir mekanizma sağlar. Cihazlar daha sonra yakın bir Azure veri merkezine gönderilir ve bir kurtarma hizmetleri Kasası üzerine yüklenir. Bu işlem, herhangi bir ağ bant genişliği kullanmadan yedekleme verilerinin sağlam aktarılmasını sağlar.

## <a name="offline-backup-options"></a>Çevrimdışı yedekleme seçenekleri

Çevrimdışı yedekleme, depolama cihazlarının sahipliğini temel alan iki modda sunulur:

- Azure Data Box göre çevrimdışı yedekleme (Önizleme)
- Azure Içeri/dışarı aktarma hizmeti 'ni temel alan çevrimdışı yedekleme

## <a name="offline-backup-based-on-azure-data-box-preview"></a>Azure Data Box göre çevrimdışı yedekleme (Önizleme)

Bu mod şu anda Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı ile desteklenir, önizleme aşamasındadır. Bu seçenek, Microsoft 'a özel, güvenli ve yetkisiz aktarım gereçlerini, veri merkezinize veya uzak ofisinize USB bağlayıcılarına teslim etmek için [Azure Data Box](https://azure.microsoft.com/services/databox/) avantajlarından yararlanır. Yedekleme verileri doğrudan bu cihazlara yazılır. Bu seçenek, kendi Azure uyumlu disklerini ve bağlayıcılarınızı temin etmek ya da bir hazırlama konumu olarak geçici depolama sağlamak için gereken çabayı kaydeder. Microsoft ayrıca, Azure portal aracılığıyla izleyebileceğiniz uçtan uca aktarım lojistik 'yı da işler.

Bu seçenekle yedekleme verilerinin hareketini açıklayan bir mimari burada gösterilmiştir.

![Azure Backup Data Box mimarisi](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Mimarinin özeti aşağıdadır:

1. Azure Backup, yedekleme verilerini doğrudan bu önceden yapılandırılmış cihazlara kopyalar.
2. Daha sonra bu cihazları bir Azure veri merkezine gönderebilirsiniz.
3. Azure Data Box, verileri müşterinin sahip olduğu bir depolama hesabına kopyalar.
4. Azure Backup, yedekleme verilerini depolama hesabından belirlenen kurtarma hizmetleri kasasına otomatik olarak kopyalar. Artımlı çevrimiçi yedeklemeler zamanlanır.

Azure Data Box göre çevrimdışı yedekleme 'yi kullanmak için [Azure Data Box kullanarak çevrimdışı yedekleme](offline-backup-azure-data-box.md)konusuna bakın.

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>Azure Içeri/dışarı aktarma hizmeti 'ni temel alan çevrimdışı yedekleme

Bu seçenek Microsoft Azure Backup Server (MABS), System Center Data Protection Manager (DPM) DPM-A ve MARS Aracısı tarafından desteklenir. [Azure içeri/dışarı aktarma hizmetini](../import-export/storage-import-export-service.md)kullanır. Azure ile uyumlu disklerinizi ve bağlayıcılarınızı kullanarak ilk yedekleme verilerini Azure 'a aktarabilirsiniz. Bu yaklaşım, hazırlama konumu olarak bilinen geçici depolamayı sağlamanızı ve yedekleme verilerini biçimlendirmek ve müşterinin sahip olduğu disklere kopyalamak için önceden oluşturulmuş yardımcı programları kullanmayı gerektirir.

Bu seçenekle yedekleme verilerinin hareketini açıklayan bir mimari burada gösterilmiştir.

![Azure Backup Içeri/dışarı aktarma hizmeti mimarisi](./media/offline-backup-overview/azure-backup-import-export.png)

Mimarinin özeti aşağıdadır:

1. Yedekleme verilerini ağ üzerinden göndermek yerine, Azure Backup yedekleme verilerini bir hazırlama konumuna yazar.
2. Hazırlama konumundaki veriler özel bir yardımcı program kullanılarak bir veya daha fazla SATA diskine yazılır.
3. Hazırlık işinin bir parçası olarak yardımcı program bir Azure içeri aktarma işi oluşturur. SATA sürücüler en yakın Azure veri merkezine gönderilir ve etkinlikleri bağlamak için içeri aktarma işine başvurur.
4. Azure veri merkezinde, disklerdeki veriler bir Azure depolama hesabına kopyalanır.
5. Azure Backup, yedekleme verilerini depolama hesabından kurtarma hizmetleri kasasına kopyalar. Artımlı yedeklemeler zamanlandı.

Azure Içeri/dışarı aktarma hizmeti 'ni MARS aracısına göre çevrimdışı yedeklemeyi kullanmak için, bkz. [Azure Backup çevrimdışı yedekleme iş akışı](./backup-azure-backup-import-export.md).

MABS veya DPM-A ile aynı şekilde kullanmak için bkz. [DPM Için çevrimdışı yedekleme iş akışı ve Azure Backup sunucusu](./backup-azure-backup-server-import-export.md).

## <a name="offline-backup-support-summary"></a>Çevrimdışı yedekleme desteği Özeti

Aşağıdaki tabloda, senaryonuza göre uygun seçimleri yapabilmek için kullanılabilecek iki seçenek karşılaştırılmaktadır.

| **Değerlendirme**                                            | **Azure Data Box göre çevrimdışı yedekleme**                     | **Azure Içeri/dışarı aktarma hizmeti 'ni temel alan çevrimdışı yedekleme**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure Backup dağıtım modelleri                              | MARS Aracısı (Önizleme)                                              | MARS Aracısı, MABS, DPM-A                                           |
| Sunucu başına (MARS) veya koruma grubu başına en fazla yedekleme verisi (MABS, DPM-A) | [Azure Data Box disk](../databox/data-box-disk-overview.md) -7,2 TB <br> [Azure Data Box](../databox/data-box-overview.md) -80 TB       | 80 TB (her biri 8 TB 'a kadar)                          |
| Güvenlik (veri, cihaz ve hizmet)                           | [Data](../databox/data-box-security.md#data-box-data-protection) -AES 256 bit şifreli <br> Veri kopyalamak için [cihaz](../databox/data-box-security.md#data-box-device-protection) -Rugged Case, tescilli, kimlik bilgisi tabanlı arabirim <br> [Hizmet](../databox/data-box-security.md#data-box-service-protection) tarafından korunan Azure Güvenlik özellikleri | Veri-BitLocker şifreli                                 |
| Geçici hazırlama konumu sağlama                     | Gerekli değil                                                | Tahmini yedekleme verileri boyutundan daha fazla veya eşit        |
| Desteklenen bölgeler                                           | [Azure Data Box disk bölgeleri](../databox/data-box-disk-overview.md#region-availability) <br> [Azure Data Box bölgeler](../databox/data-box-disk-overview.md#region-availability) | [Azure Içeri/dışarı aktarma hizmeti bölgeleri](../import-export/storage-import-export-service.md#region-availability) |
| Çapraz ülke gönderimi                                     | Desteklenmez  <br>    Kaynak adresi ve hedef Azure veri merkezi aynı ülkede/bölgede olmalıdır * | Desteklenir                                                    |
| Lojistik (teslim, taşıma, toplama)           | Tamamen Microsoft tarafından yönetilen                                     | Müşteri tarafından yönetilen                                            |
| Fiyatlandırma                                                      | [Azure Data Box fiyatlandırması](https://azure.microsoft.com/pricing/details/databox/) <br> [Azure Data Box disk fiyatlandırması](https://azure.microsoft.com/pricing/details/databox/disk/) | [Azure Içeri/dışarı aktarma hizmeti fiyatlandırması](https://azure.microsoft.com/pricing/details/storage-import-export/) |

* Ülkeniz/bölgeniz bir Azure veri merkezine sahip değilse, disklerinizi başka bir ülkede/bölgede bir Azure veri merkezine teslim etmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Data Box kullanarak çevrimdışı yedekleme Azure Backup](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
- [Azure Backup çevrimdışı yedekleme iş akışı](backup-azure-backup-import-export.md)
- [DPM ve Azure Backup Sunucusu için çevrimdışı yedekleme iş akışı](backup-azure-backup-server-import-export.md)
