---
title: Azure Dosya Eşitleme giriş | Microsoft Docs
description: Endüstri standardı SMB protokolünü kullanarak bulutta ağ dosya paylaşımları oluşturmanızı ve bunları kullanmanızı sağlayan bir hizmet olan Azure Dosya Eşitleme genel bakış.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 04/19/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: da40783e3d299b0edf27c6d045dbc6dcfc5cf964
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797670"
---
# <a name="what-is-azure-file-sync"></a>Azure Dosya Eşitleme nedir?
Azure Dosya Eşitleme, Azure dosyalarında kuruluşunuzun dosya paylaşımlarını merkezileştirerek bir Windows dosya sunucusunun esnekliğini, performansını ve uyumluluğunu koruyarak sağlar. Bazı kullanıcılar verilerinin tam bir kopyasını yerel olarak saklamayı tercih edebilirken, ayrıca Azure Dosya Eşitleme, Windows Server 'ı Azure dosya paylaşımınızın hızlı bir önbelleğine dönüştürme özelliğine sahiptir. Verilere yerel olarak erişmek için Windows Server üzerinde kullanılabilen tüm protokolleri (SMB, NFS ve FTPS gibi) kullanabilirsiniz. Dünyanın dört bir yanında ihtiyacınız olan sayıda önbellekler olabilir.   

## <a name="videos"></a>Videolar
| Azure Dosya Eşitleme tanıtımı | Eşitlenmiş Azure dosyaları (Ignite 2019)  |
|-|-|
| [![Giriş Azure Dosya Eşitleme ekran görüntüsü-oynatmak için tıklayın!](../files/media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Eşitleme sunumuna sahip Azure dosyalarının ekran kaydı-oynatmak için tıklayın!](../files/media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/embed/6E2p28XwovU) |

## <a name="benefits-of-azure-file-sync"></a>Azure Dosya Eşitleme avantajları

### <a name="cloud-tiering"></a>Bulut katmanlaması
Bulut katmanlama etkinken, en sık erişilen dosyalarınız yerel sunucunuzda önbelleğe alınır ve en az erişilen dosyalarınız buluta göre katmanlanalınır. Önbelleğe alma için ne kadar yerel disk alanı kullanıldığını kontrol edebilirsiniz. Katmanlı dosyalar hızlı bir şekilde isteğe bağlı olarak çağrılabileceği için, yalnızca şirket içi verilerinizin bir kısmını depolarken ihtiyaç duyurken maliyetten tasarruf etmeniz mümkün olduğunca sorunsuz bir şekilde geri alınabilir. Bulut katmanlama hakkında daha fazla bilgi için bkz. [bulut katmanlaması genel bakış](file-sync-cloud-tiering-overview.md). 

### <a name="multi-site-access-and-sync"></a>Çoklu site erişimi ve eşitleme
Azure Dosya Eşitleme, dağıtılmış erişim senaryoları için idealdir. Ofislerinizin her biri için, Azure Dosya Eşitleme dağıtımınızın bir parçası olarak yerel bir Windows Server sağlayabilirsiniz. Bir ofisteki sunucuda yapılan değişiklikler diğer tüm ofislerdeki sunucularla otomatik olarak eşitlenir.

### <a name="business-continuity-and-disaster-recovery"></a>İş sürekliliği ve olağanüstü durum kurtarma
Azure Dosya Eşitleme, yüksek oranda kullanılabilir depolama için birkaç artıklık seçeneği sunan Azure dosyaları tarafından desteklenir. Azure, verilerinizin dayanıklı kopyalarını içerdiğinden, Yerel sunucunuz bir atılabilir önbelleğe alma cihazı haline gelir ve Azure Dosya Eşitleme dağıtımınıza yeni bir sunucu eklenerek başarısız olan bir sunucudan kurtarılması gerçekleştirilebilir. Yerel bir yedeklemeden geri yüklemek yerine, başka bir Windows Server sağlar, Azure Dosya Eşitleme aracısını yükleyin ve ardından Azure Dosya Eşitleme dağıtımınıza ekleyin. Azure Dosya Eşitleme, sunucunuzun mümkün olan en kısa sürede çalışmaya ve çalışmaya devam edebilmesi için, verileri indirmeden önce dosya ad alanınızı indirir. Daha hızlı kurtarma için, dağıtımınızın bir parçası olarak sunucuya göre çalışan bir veya Windows Kümeleme ile Azure Dosya Eşitleme de kullanabilirsiniz.

## <a name="cloud-side-backup"></a>Bulut tarafı yedekleme
Azure Backup kullanarak bulutta merkezi yedeklemeler gerçekleştirerek şirket içi yedekleme harcamalarınızı azaltın. Azure dosyaları SMB paylaşımlarında yerel anlık görüntü becerileri vardır ve bu işlem, yedeklemelerinizi zamanlamak ve bekletme işlemlerini yönetmek için Azure Backup kullanılarak otomatik hale getirilebilir. Azure Backup Ayrıca, şirket içi sunucularınız ile tümleşir, bu nedenle buluta geri yüklediğinizde bu değişiklikler otomatik olarak Windows sunucularınızda indirilir.  

## <a name="next-steps"></a>Sonraki Adımlar
* [Azure Dosya Eşitleme dağıtımı planlama](file-sync-planning.md)
* [Bulut katmanlaması genel bakış](file-sync-cloud-tiering-overview.md)
* [Azure Dosya Eşitleme’yi izleme](file-sync-monitoring.md)