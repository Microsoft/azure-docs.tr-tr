---
title: Azure Storage 'da veri artıklığı | Microsoft Docs
description: Microsoft Azure Depolama hesabınızdaki veriler, dayanıklılık ve yüksek kullanılabilirlik için çoğaltılır. Artıklık seçenekleri yerel olarak yedekli depolama (LRS), bölgesel olarak yedekli depolama (ZRS), coğrafi olarak yedekli depolama (GRS), Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS), coğrafi bölge yedekli depolama (GZRS) (Önizleme) ve Okuma Erişimli Coğrafi bölge-yedekli depolama (RA-GZRS) (Önizleme).
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 8025228275afeb3f23268db759eb7659b9887132
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71670796"
---
# <a name="azure-storage-redundancy"></a>Azure depolama artıklığı

Microsoft Azure Depolama hesabınızdaki veriler, dayanıklılık ve yüksek kullanılabilirlik sağlamak için her zaman çoğaltılır. Azure depolama, verilerinizi geçici donanım arızaları, ağ veya güç kesintileri ve çok büyük doğal olağanüstü durumlar dahil olmak üzere planlı ve planlanmamış olaylardan korunabilecek şekilde kopyalar. Verilerinizi aynı veri merkezinde, aynı bölgedeki ZGen veri merkezlerinde veya coğrafi olarak ayrılmış bölgelerde çoğaltmayı tercih edebilirsiniz.

Çoğaltma işlemi, hata durumunda bile depolama hesabınızın [Depolama için Hizmet Düzeyi Sözleşmesi'ne (SLA)](https://azure.microsoft.com/support/legal/sla/storage/) uymasını sağlar. Azure Depolama'nın dayanıklılık ve kullanılabilirlikle ilgili sağladığı garantiler hakkında bilgi edinmek için SLA'ya göz atın.

Azure depolama, Döngüsel artıklık denetimleri (CRCs) kullanılarak depolanan verilerin bütünlüğünü düzenli olarak doğrular. Veri bozulması algılanırsa, bu, gereksiz veriler kullanılarak onarılır. Azure Storage Ayrıca veri depolarken veya alırken veri paketlerinin bozulmasını algılamak için tüm ağ trafiğinde sağlama toplamlarını hesaplar.

## <a name="choosing-a-redundancy-option"></a>Artıklık seçeneği seçme

Bir depolama hesabı oluşturduğunuzda, aşağıdaki artıklık seçeneklerinden birini belirleyebilirsiniz:

[!INCLUDE [azure-storage-redundancy](../../../includes/azure-storage-redundancy.md)]

Aşağıdaki tabloda, her bir çoğaltma stratejisinin belirli bir olay türü (veya benzer etkinin olayı) için size sağladığı dayanıklılık ve kullanılabilirlik kapsamına hızlı bir genel bakış sunulmaktadır.

| Senaryo                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS (Önizleme)                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Bir veri merkezi içinde düğüm kullanılamaz                                                                 | Evet                             | Evet                              | Evet                                  | Evet                                  |
| Tüm veri merkezi (zonal veya ZGen olmayan) kullanılamaz hale gelir                                           | Hayır                              | Evet                              | Evet                                  | Evet                                  |
| Bölge genelinde kesinti                                                                                     | Hayır                              | Hayır                               | Evet                                  | Evet                                  |
| Bölge genelinde kullanım dışı kalması durumunda verilerinize (uzak, coğrafi olarak çoğaltılan bir bölgede) okuma erişimi | Hayır                              | Hayır                               | Evet (RA-GRS ile)                                   | Evet (RA-GZRS ile)                                 |
| Belirli bir yıl \_ boyunca nesnelerin dayanıklılığını sağlamak \_ için tasarlandı                                          | en az% 99,999999999 (11 9) | en az% 99,9999999999 (12 9) | en az% 99.99999999999999 (16 9) | en az% 99.99999999999999 (16 9) |
| Desteklenen depolama hesabı türleri                                                                   | GPv2, GPv1, blob                | GPv2                             | GPv2, GPv1, blob                     | GPv2                     |
| Okuma istekleri için kullanılabilirlik SLA 'Sı | En az% 99,9 (Seyrek Erişimli Katman için% 99) | En az% 99,9 (Seyrek Erişimli Katman için% 99) | GRS için en az% 99,9 (Seyrek Erişimli Katman için% 99)<br /><br />RA-GRS için en az% 99,99 (Seyrek Erişimli Katman için% 99,9) | GZRS için en az% 99,9 (Seyrek Erişimli Katman için% 99)<br /><br />RA-GZRS için en az% 99,99 (Seyrek Erişimli Katman için% 99,9) |
| Yazma istekleri için kullanılabilirlik SLA 'Sı | En az% 99,9 (Seyrek Erişimli Katman için% 99) | En az% 99,9 (Seyrek Erişimli Katman için% 99) | En az% 99,9 (Seyrek Erişimli Katman için% 99) | En az% 99,9 (Seyrek Erişimli Katman için% 99) |

Depolama hesabınızdaki tüm veriler, blok Blobları ve ekleme Blobları, sayfa Blobları, kuyruklar, tablolar ve dosyalar dahil olmak üzere çoğaltılır. Tüm depolama hesabı türleri çoğaltılır, ancak ZRS genel amaçlı v2 depolama hesabı gerektirir.

Her artıklık seçeneği için fiyatlandırma bilgileri için bkz. [Azure Storage fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/). 

Dayanıklılık ve kullanılabilirlik için Azure depolama garantisi hakkında daha fazla bilgi için bkz. [Azure Storage SLA](https://azure.microsoft.com/support/legal/sla/storage/).

> [!NOTE]
> Azure Premium Depolama şu anda yalnızca yerel olarak yedekli depolamayı (LRS) desteklemektedir.

## <a name="changing-replication-strategy"></a>Çoğaltma stratejisini değiştirme

Depolama hesabınızın çoğaltma stratejinizi [Azure Portal](https://portal.azure.com/), [Azure POWERSHELL](storage-powershell-guide-full.md), [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)veya [Azure Storage istemci kitaplıklarından](https://docs.microsoft.com/azure/index#pivot=sdkstools)birini kullanarak değiştirebilirsiniz. Depolama hesabınızın çoğaltma türünü değiştirmek, zaman kaybına neden olmaz.

> [!NOTE]
> Şu anda, hesabınızı ZRS, GZRS veya RA-GZRS ' e dönüştürmek için Azure portal veya Azure Storage istemci kitaplıklarını kullanamazsınız. Hesabınızı ZRS 'ye geçirmek için bkz. Ayrıntılar için [yüksek düzeyde kullanılabilir Azure depolama uygulamaları oluşturmak için bölgesel olarak yedekli depolama (ZRS)](storage-redundancy-zrs.md) . GZRS veya RA-GZRS geçirmek için, Ayrıntılar için [yüksek kullanılabilirlik ve en yüksek dayanıklılık (Önizleme) Için coğrafi bölge yedekli depolama](storage-redundancy-zrs.md) bölümüne bakın.

### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Hesabımın çoğaltma stratejisini değiştirmek için herhangi bir maliyet var mı?

Dönüştürme yolunuza bağlıdır. En az pahalı, Azure Storage artıklık teklifleri LRS, ZRS, GRS, RA-GRS, GZRS ve RA-GZRS arasında sıralama. Örneğin, LRS *'den* başka bir çoğaltma türüne geçmek, daha ayrıntılı bir artıklık düzeyine taşıdığınız için ek ücretler doğurur. GRS veya RA-GRS ' *e* geçiş, verileriniz (birincil bölgenizde) uzak ikincil bölgenize çoğaltıldığı için çıkış bant genişliği ücretine neden olur. Bu ücret, ilk kurulumda tek seferlik bir maliyettir. Veriler kopyalandıktan sonra başka geçiş ücretleri yoktur. Yalnızca var olan verilere yönelik yeni veya güncelleştirmelerin çoğaltılması için ücretlendirilirsiniz. Bant genişliği ücretleri hakkında daha fazla bilgi için bkz. [Azure Depolama fiyatlandırması sayfası](https://azure.microsoft.com/pricing/details/storage/blobs/).

GRS 'den LRS 'ye depolama hesabınızı geçirirseniz, ek bir maliyet yoktur, ancak çoğaltılan verileriniz ikincil konumdan silinir.

Depolama hesabınızı RA-GRS ' d e veya LRS 'ye geçirirseniz, bu hesap, dönüştürülen tarihin ötesinde 30 gün boyunca RA-GRS olarak faturalandırılır.

## <a name="see-also"></a>Ayrıca bkz.

- [Yerel olarak yedekli depolama (LRS): Azure depolama için düşük maliyetli veri artıklığı](storage-redundancy-lrs.md)
- [Bölgesel olarak yedekli depolama (ZRS): Yüksek oranda kullanılabilir Azure depolama uygulamaları](storage-redundancy-zrs.md)
- [Coğrafi olarak yedekli depolama (GRS): Azure depolama için çapraz bölgesel çoğaltma](storage-redundancy-grs.md)
- [Yüksek kullanılabilirlik ve maksimum dayanıklılık (Önizleme) için coğrafi bölge yedekli depolama (GZRS)](storage-redundancy-gzrs.md)
- [Azure depolama ölçeklenebilirlik ve performans hedefleri](storage-scalability-targets.md)
- [RA-GRS depolama kullanarak yüksek oranda kullanılabilir uygulamalar tasarlama](../storage-designing-ha-apps-with-ragrs.md)
- [Microsoft Azure Depolama yedeklilik seçenekleri ve Okuma Erişimli Coğrafi olarak yedekli depolama](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [SOSP kağıdı-Azure depolama: Güçlü tutarlılığı olan yüksek oranda kullanılabilir bir bulut depolama hizmeti](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
