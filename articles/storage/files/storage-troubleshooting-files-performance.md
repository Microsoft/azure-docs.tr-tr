---
title: Azure dosyaları performans sorunlarını giderme kılavuzu
description: Azure dosya paylaşımları ve ilişkili geçici çözümler ile ilgili bilinen performans sorunları.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 0e11949804e0c3de52db315424f83905516b4da8
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996611"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Azure dosyaları performans sorunlarını giderme

Bu makalede, Azure dosya paylaşımlarıyla ilgili bazı yaygın sorunlar listelenmektedir. Bu sorunlarla karşılaşıldığında olası nedenleri ve geçici çözümleri sağlar.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Yüksek gecikme süresi, düşük aktarım hızı ve genel performans sorunları

### <a name="cause-1-share-experiencing-throttling"></a>Neden 1: Daraltma ile paylaşma

Premium paylaşımdaki varsayılan kota 100 GiB 'dir ve bu da 100 temel ıOPS (bir saat için en fazla ' 300 e kadar veri bloğu) sağlar. Sağlama ve ıOPS ile ilişkisi hakkında daha fazla bilgi için, planlama kılavuzunun [sağlanan paylaşımlar](storage-files-planning.md#provisioned-shares) bölümüne bakın.

Paylaşımınızın kısıtlandığından emin olmak için, portalda Azure ölçümlerinden yararlanabilirsiniz.

1. [Azure Portal](https://portal.azure.com) oturum açın.

1. **Tüm hizmetler** ' i seçin ve ardından **ölçümler**' i arayın.

1. **Ölçümler**’i seçin.

1. Kaynak olarak depolama hesabınızı seçin.

1. Ölçüm ad alanı olarak **Dosya** ' yı seçin.

1. Ölçüm olarak **işlemler** ' i seçin.

1. **ResponseType** için bir filtre ekleyin ve herhangi bir isteğin bir yanıt **kodu (SMB** için) veya **CLIENTKıSıTıNGERROR** (REST için) yanıt koduna sahip olup olmadığını denetleyin.

![Premium dosya paylaşımları için ölçüm seçenekleri](media/storage-troubleshooting-premium-fileshares/metrics.png)

### <a name="solution"></a>Çözüm

- Paylaşımınızda daha yüksek bir kota belirterek sağlanan kapasiteyi yükseltin.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Neden 2: Meta veri/ad alanı ağır iş yükü

İsteklerinizin çoğunluğu meta veri merkezli ise (CreateFile/OpenFile/CloseFile/QueryInfo/querydirectory gibi), okuma/yazma işlemlerine kıyasla gecikme daha kötüleşmeyecektir.

İsteklerinizin çoğunun meta veri merkezli olup olmadığını doğrulamak için yukarıdaki adımlardan aynı adımları kullanabilirsiniz. **ResponseType**için bir filtre eklemek yerine, **API adı**için bir filtre ekleyin.

![Ölçümlerinizin API adı için filtre](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Geçici Çözüm

- Meta veri işlemlerinin sayısını azaltmak için uygulamanın değiştirilip değiştirilemeyeceğini denetleyin.

### <a name="cause-3-single-threaded-application"></a>Neden 3: Tek iş parçacıklı uygulama

Müşteri tarafından kullanılan uygulama tek iş parçacıklı ise, bu, sağlanan paylaşma boyutunuzu temel alarak mümkün olan en yüksek sayıdan/aktarım hızına göre önemli ölçüde daha düşük bir değer oluşmasına neden olabilir.

### <a name="solution"></a>Çözüm

- İş parçacığı sayısını artırarak uygulama paralelliğini artırın.
- Paralellik mümkün olduğunda uygulamalara geçiş yapın. Örneğin, kopyalama işlemleri için müşteriler Windows istemcilerinden AzCopy veya RoboCopy veya Linux istemcilerindeki **Parallel** komutunu kullanabilir.

## <a name="very-high-latency-for-requests"></a>İstekler için çok yüksek gecikme süresi

### <a name="cause"></a>Nedeni

İstemci VM dosyası, dosya paylaşımından farklı bir bölgede bulunabilir.

### <a name="solution"></a>Çözüm

- Uygulamayı dosya paylaşımıyla aynı bölgede bulunan bir VM 'den çalıştırın.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>İstemci ağ tarafından desteklenen maksimum üretilen iş elde edemedi

Bunun olası nedenlerinden biri, SMB çoklu kanal desteğinin olmamasıdır. Şu anda Azure dosya paylaşımları yalnızca tek kanalı destekler, bu nedenle istemci sanal makineden sunucuya yalnızca bir bağlantı vardır. Bu tek bağlantı, istemci sanal makinesinde tek bir çekirdeğe gereğinden, bu nedenle bir VM 'den en fazla üretilen iş ulaşılabilir, tek bir çekirdekle bağlanır.

### <a name="workaround"></a>Geçici Çözüm

- Daha büyük bir çekirdekli VM 'nin alınması, aktarım hızını artırmaya yardımcı olabilir.
- İstemci uygulamasını birden çok VM 'den çalıştırmak, aktarım hızını artırır.

- Mümkün olduğunda REST API 'Leri kullanın.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>Linux istemcilerinde üretilen iş, Windows istemcileri ile karşılaştırıldığında önemli ölçüde düşüktür.

### <a name="cause"></a>Nedeni

Bu, Linux üzerinde SMB istemcisinin uygulanmasıyla ilgili bilinen bir sorundur.

### <a name="workaround"></a>Geçici Çözüm

- Yükü birden çok VM arasında yayın.
- Aynı VM 'de, **nosharesock** seçeneğiyle birden çok bağlama noktası kullanın ve yükü bu bağlama noktalarına yayın.
- Linux 'ta, her fsync çağrısında SMB temizlemeyi zormaktan kaçınmak için **nostrictsync** seçeneğiyle bağlamayı deneyin. Azure dosyaları için bu seçenek, veri ayrıntılarını etkilemez, ancak dizin listelemesi (**ls-l** komutu) üzerinde eski dosya meta verileri oluşmasına neden olabilir. Dosya meta verilerinin (**stat** komutu) doğrudan sorgulanması en güncel dosya meta verilerini döndürür.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Kapsamlı açık/kapalı işlemleri içeren meta veriler ağır iş yükleri için yüksek gecikme süreleri.

### <a name="cause"></a>Nedeni

Dizin kiraları için destek eksikliği yok.

### <a name="workaround"></a>Geçici Çözüm

- Mümkünse, kısa bir süre içinde aynı dizinde aşırı açma/kapatma tanıtıcısından kaçının.
- Linux VM 'ler için, bağlama seçeneği olarak **actimeo =\<sec >** belirterek Dizin girişi önbelleği zaman aşımını artırın. Bu, varsayılan olarak bir saniyedir ve üç ya da beş gibi daha büyük bir değer yardımcı olabilir.
- Linux sanal makineleri için, çekirdeği 4,20 veya daha yüksek bir sürüme yükseltin.

## <a name="low-iops-on-centosrhel"></a>CentOS/RHEL üzerinde düşük ıOPS

### <a name="cause"></a>Nedeni

Büyük GÇ derinliği, CentOS/RHEL üzerinde desteklenmez.

### <a name="workaround"></a>Geçici Çözüm

- CentOS 8/RHEL 8 ' e yükseltin.
- Ubuntu olarak değiştirin.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Linux 'ta Azure dosyalarından yavaş dosya kopyalama

Azure dosyaları 'na ve Azure dosyalarına yavaş dosya kopyalama ile karşılaşıyorsanız, Linux sorun giderme kılavuzu 'ndaki [Linux 'ta bulunan ve Azure dosyalarına yavaş dosya](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) kopyalama bölümüne göz atın.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>IOPS için jtery/testere-tooth deseninin

### <a name="cause"></a>Nedeni

İstemci uygulaması, temel ıOPS 'yi sürekli olarak aşıyor. Şu anda, istek yükünün hizmet tarafı yumuşatması yoktur, bu nedenle istemci temel ıOPS 'yi aşarsa hizmet tarafından kısıtlanacaktır. Bu kısıtlama, istemcinin bir jtery/testere-tooth ıOPS düzeniyle karşılaşmasına neden olabilir. Bu durumda, istemci tarafından elde edilen ortalama ıOPS, taban ıOPS değerinden daha düşük olabilir.

### <a name="workaround"></a>Geçici Çözüm

- Paylaşımın kısıtlanmaması için istemci uygulamasından gelen istek yükünü azaltın.
- Paylaşımın kısıtlanmaması için paylaşımın kotasını artırın.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Aşırı sayıda DirectoryOpen/DirectoryClose çağrısı

### <a name="cause"></a>Nedeni

DirectoryOpen/DirectoryClose çağrılarının sayısı en üst API çağrılarından ise ve istemcinin bu çok sayıda çağrı yapmasına gerek duymuyorsanız, Azure istemci VM 'de yüklü virüsten koruma sorunu olabilir.

### <a name="workaround"></a>Geçici Çözüm

- Bu sorunla ilgili bir düzelme, [Windows Için Nisan platformu güncelleştirmesinde](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)sunulmaktadır.

## <a name="file-creation-is-slower-than-expected"></a>Dosya oluşturma beklenenden yavaş

### <a name="cause"></a>Nedeni

Çok sayıda dosya oluşturmaya bağlı olan iş yükleri, Premium dosya paylaşımlarının ve standart dosya paylaşımlarının performansı arasında önemli bir farklılık görmeyecektir.

### <a name="workaround"></a>Geçici Çözüm

- Yok.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Windows 8.1 veya Server 2012 R2 'den yavaş performans

### <a name="cause"></a>Nedeni

GÇ yoğun iş yükleri için Azure dosyalarına erişirken beklenen gecikme süresinden daha yüksek.

### <a name="workaround"></a>Geçici Çözüm

- Kullanılabilir [düzeltmeyi](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)yükler.
