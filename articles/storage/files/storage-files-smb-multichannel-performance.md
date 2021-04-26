---
title: Çok kanallı SMB performansı-Azure dosyaları
description: Çok kanallı SMB performansı hakkında bilgi edinin.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: a9edd93aa265622732be4a7582cce9900959bf6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374992"
---
# <a name="smb-multichannel-performance"></a>Çok Kanallı SMB performansı

Azure dosyaları SMB çok kanallı (Önizleme), bir SMB 3. x istemcisinin bir dosya depolama hesabındaki Premium dosya paylaşımlarına birden fazla ağ bağlantısı kurmasını sağlar. SMB 3,0 protokolü, Windows Server 2012 ve Windows 8 istemcilerinde çok kanallı SMB özelliği sunmuştur. Bu nedenle, çok kanallı SMB 'yi destekleyen herhangi bir Azure dosyaları SMB 3. x istemcisi, Azure Premium dosya paylaşımlarının özelliğinden yararlanabilir. Depolama hesabında çok kanallı SMB 'yi etkinleştirmeye yönelik ek bir maliyet yoktur.

## <a name="benefits"></a>Avantajlar

Azure dosyaları SMB çok kanallı, istemcilerin, sahiplik maliyetini azaltarak daha yüksek performans sağlayan birden fazla ağ bağlantısı kullanmasına olanak sağlar. Yüksek performans, birden çok NIC üzerinden bant genişliği toplama yoluyla ve GÇ yükünü birden çok CPU arasında dağıtmak için NIC 'ler için Alma Tarafı Ölçeklendirmesi (RSS) desteğinin kullanılmasıyla elde edilir.

- **Artan verimlilik**: birden çok bağlantı, verilerin paralel olarak birden çok yol üzerinden aktarılmasına ve bu sayede büyük GÇ boyutlarıyla daha büyük dosya boyutları kullanan iş yüklerinin önemli ölçüde avantajları ve tek bir VM 'den ya da daha küçük bir VM kümesinden yüksek aktarım hızı gerektirmesini sağlar. Bu iş yüklerinden bazıları, içerik oluşturma veya kodlama, Genomiks ve finans hizmetleri risk analizi için medya ve eğlence içerir.
- **Daha yüksek IOPS**: NIC RSS özelliği birden çok bağlantıyla birden çok CPU arasında etkili yük dağıtımına izin verir. Bu, VM CPU 'ların daha yüksek ıOPS ölçeğinde ve etkili kullanımına yardımcı olur. Bu, veritabanı uygulamaları gibi küçük GÇ boyutlarına sahip iş yükleri için yararlıdır.
- **Ağ hata toleransı**: birden çok bağlantı, istemcilerin artık tek bir bağlantıya dayanmasından bu yana kesinti riskini azaltır.
- **Otomatik yapılandırma**: istemciler ve depolama hesaplarında çok kanallı SMB etkinleştirildiğinde, mevcut bağlantıların dinamik olarak bulunmasına izin verir ve gerektiğinde ek bağlantı yolları oluşturabilir.
- **Maliyet iyileştirmesi**: iş yükleri, Premium paylaşımlara bağlanırken tek bir VM 'den veya küçük bir VM 'den daha fazla ölçek elde edebilir. Bu, bir iş yükünü çalıştırmak ve yönetmek için gereken VM 'lerin sayısını azaltarak toplam sahiplik maliyetini azaltabilir.

Çok kanallı SMB hakkında daha fazla bilgi için [Windows belgelerine](/azure-stack/hci/manage/manage-smb-multichannel)bakın.

Bu özellik, çok iş parçacıklı uygulamalara daha fazla performans avantajları sağlar, ancak genellikle tek iş parçacıklı uygulamalara yardımcı değildir. Daha fazla ayrıntı için [performans karşılaştırması](#performance-comparison) bölümüne bakın.

## <a name="limitations"></a>Sınırlamalar

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Bölgesel kullanılabilirlik

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="configuration"></a>Yapılandırma

SMB çok kanallı yalnızca özellik istemci tarafında (istemciniz) ve hizmet tarafında (Azure depolama hesabınız) etkin olduğunda işe yarar.

Windows istemcilerinde çok kanallı SMB varsayılan olarak etkindir. Aşağıdaki PowerShell komutunu çalıştırarak yapılandırmanızı doğrulayabilirsiniz: 

`Get-smbClientConfiguration | select EnableMultichannel`.
 
Azure depolama hesabınızda çok kanallı SMB 'yi etkinleştirmeniz gerekir. Bkz. çok [KANALLı SMB 'Yi etkinleştirme (Önizleme)](storage-files-enable-smb-multichannel.md).

### <a name="disable-smb-multichannel"></a>SMB çok kanallı devre dışı bırak
Çoğu senaryoda, özellikle çok iş parçacıklı iş yükleri, istemciler çok kanallı SMB ile iyileştirilmiş performansı görmelidir. Ancak, tek iş parçacıklı iş yükleri veya test amaçları gibi bazı özel senaryolar, çok kanallı SMB 'yi devre dışı bırakmak isteyebilirsiniz. Daha ayrıntılı bilgi için bkz. [performans karşılaştırması](#performance-comparison) .

## <a name="verify-smb-multichannel-is-configured-correctly"></a>Çok kanallı SMB 'nin doğru yapılandırıldığını doğrulama

1. Premium dosya paylaşma oluşturun veya var olan bir dosyayı kullanın.
1. İstemcinizin çok kanallı SMB 'yi desteklediğinden emin olun (bir veya daha fazla ağ bağdaştırıcısında Alma Tarafı Ölçeklendirmesi etkin). Daha fazla bilgi için [Windows belgelerine](/azure-stack/hci/manage/manage-smb-multichannel) bakın.
1. İstemcinizle bir dosya paylaşma bağlayın.
1. Uygulamanızla birlikte yük oluşturun.
    Robocopy/MT gibi bir kopyalama aracı ya da DiskSpd gibi dosyaları okumak/yazmak için herhangi bir performans Aracı Yük üretebilir.
1. PowerShell 'i yönetici olarak açın ve şu komutu kullanın: `Get-SmbMultichannelConnection |fl`
1. **Maxchannels** ve **currentchannels** özelliklerini arayın

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG" alt-text="Get-SMBMultichannelConnection sonuçlarının ekran görüntüsü." lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG":::

## <a name="performance-comparison"></a>Performans karşılaştırması

Tek iş parçacıklı ve çok iş parçacıklı, okuma/yazma iş yükü desenlerinin iki kategorisi vardır. Çoğu iş yükü birden çok dosya kullanır, ancak iş yükünün paylaşımdaki tek bir dosya ile çalıştığı belirli bir kullanım durumu olabilir. Bu bölümde, farklı kullanım durumları ve bunların her biri için performans etkisi ele alınmaktadır. Genel olarak, çoğu iş yükü çok iş parçacıklıdır ve birden çok dosya üzerinde iş yükünü dağıtarak çok kanallı SMB ile önemli performans iyileştirmeleri gözlemlemelidir.

- **Çok iş parçacıklı/birden çok dosya**: iş yükü düzenine bağlı olarak, birden çok kanal üzerinde IOS okuma ve yazma konusunda önemli performans geliştirmesini görmeniz gerekir. Performans kazançları, ıOPS, üretilen iş ve gecikme bakımından 2x ile 4X arasında farklılık gösterir. Bu kategori için çok kanallı SMB, en iyi performans için etkinleştirilmelidir.
- **Çok iş parçacıklı/tek dosya**: Bu kategorideki çoğu kullanım örneği için, iş yüklerinin çok kanallı olması, özellikle de iş yükünün ortalama bir gç boyutu > ~ 16k olması avantajına sahip olur. Çok kanallı SMB 'den faydalanabilecek birkaç örnek senaryo, tek bir büyük dosyanın yedeklenmesi veya kurtarılması. İş yükünüz küçük IOs ağır ise çok kanallı SMB 'yi devre dışı bırakmak isteyebileceğiniz bir özel durum. Bu durumda, %10 oranında küçük bir performans kaybı gözlemleyebilirsiniz. Kullanım örneğine bağlı olarak, birden çok Dosya arasında yük yayılmasını düşünün veya özelliği devre dışı bırakabilirsiniz. Ayrıntılar için [yapılandırma](#configuration) bölümüne bakın.
- **Tek iş parçacıklı/birden çok dosya veya tek dosya**: çoğu tek iş parçacıklı iş yükleri için, paralellik olmaması nedeniyle en düşük performans avantajları vardır, genellikle çok kanallı SMB etkinse yaklaşık %10 oranında performans düşüşü vardır. Bu durumda, çok kanallı SMB 'yi tek bir özel durumla devre dışı bırakmak idealdir. Tek iş parçacıklı iş yükü birden çok Dosya arasında yük dağıtabilir ve ortalama büyük GÇ boyutu (> ~ 16k) üzerinde kullanıyorsa, çok kanallı SMB 'den hafif performans avantajları olmalıdır.

### <a name="performance-test-configuration"></a>Performans testi yapılandırması

Bu makaledeki grafiklerde, şu yapılandırma kullanıldı: dört kanallı tek bir RSS özellikli NIC ile tek bir standart D32s v3 VM. Yük, GÇ derinliği 10 olan diskspd.exe, çoklu iş parçacıklı ve çeşitli GÇ boyutlarıyla rastgele IOs kullanılarak oluşturulmuştur.

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | Maksimum önbelleğe alınmış ve geçici depolama aktarım hızı: ıOPS/MBps (GiB 'de önbellek boyutu) | Önbelleğe alınmamış maksimum disk aktarım hızı: ıOPS/MBps | En fazla NIC|Beklenen ağ bant genişliği (MB/sn) |
|---|---|---|---|---|---|---|---|---|
| [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8|16000 |

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG" alt-text="Performans testi yapılandırmasını gösteren ekran görüntüsü." lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG":::

### <a name="mutli-threadedmultiple-files-with-smb-multichannel"></a>Çok kanallı SMB ile çoklu iş parçacıklı/birden çok dosya

Yük, çeşitli GÇ boyutlarına sahip 10 dosyada oluşturulmuştur. Ölçek artırma test sonuçları, her iki ıOPS 'de önemli iyileştirmeler ve çok kanallı SMB 'nin etkin olduğu performans ile test sonuçlarını gösterdi. Aşağıdaki diyagramlar sonuçları betimdir:

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png" alt-text="Performans diyagramı" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png" alt-text="Verimlilik performansı diyagramı." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png":::

- Tek bir NIC 'de, okuma için 2x-3x performans artışı gözlemlenmiştir ve yazma işlemleri için, her iki ıOPS ve aktarım hızı bakımından 3x-4X kazanımı gözlemlenmiştir.
- SMB çok kanallı izin verilen ıOPS ve aktarım hızı, tek bir NIC ve dört kanallı sınırın yanı sıra VM sınırlarına ulaşacak.
- Çıkış (veya depolama 'ya okuma) tarifeli olmadığından, okuma aktarım hızı 16.000 MB/sn 'lik (2 GiB/sn) VM Yayımlanma limitini aşabilir. Test >2,7 GiB/sn 'ye ulaşıldı. Giriş (veya depolamaya yazma) hala VM sınırlarına tabidir.
- Çok sayıda dosyanın üzerinde yük yayılması önemli geliştirmeler için izin verilir.

Bu testte kullanılan örnek bir komut şunlardır: 

`diskspd.exe -W300 -C5 -r -w100 -b4k -t8 -o8 -Sh -d60 -L -c2G -Z1G z:\write0.dat z:\write1.dat z:\write2.dat z:\write3.dat z:\write4.dat z:\write5.dat z:\write6.dat z:\write7.dat z:\write8.dat z:\write9.dat `.

### <a name="multi-threadedsingle-file-workloads-with-smb-multichannel"></a>Çok kanallı SMB ile çoklu iş parçacıklı/tek dosya iş yükleri

Yük, tek bir 128 GiB dosyasına göre oluşturulmuştur. Çok iş parçacıklı çok kanallı/tek dosyalarla ölçek artırma testi, çoğu durumda geliştirmeler gösterdi. Aşağıdaki diyagramlar sonuçları betimdir:

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png" alt-text="IOPS performansı diyagramı." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png" alt-text="Tek dosya işleme performansının diyagramı." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png":::

- Daha büyük ortalama GÇ boyutuna sahip tek bir NIC 'de (> ~ 16k), hem okuma hem de yazma işlemlerinde önemli iyileştirmeler yapılmıştır.
- Daha küçük GÇ boyutları için, çok kanallı SMB etkinken performans üzerinde yaklaşık %10 ' un küçük bir etkisi vardı. Bu, yükü birden çok dosya üzerinden yayarak veya özelliği devre dışı bırakarak azaltılabilir.
- Performans, [tek dosya sınırlarına](storage-files-scale-targets.md#file-scale-targets)göre hala bağlanmıştır.

## <a name="optimizing-performance"></a>Performansı iyileştirme

Aşağıdaki ipuçları, performansınızı iyileştirmenize yardımcı olabilir:

- Ağ gecikmesini azaltmak için depolama hesabınızın ve istemcinizin aynı Azure bölgesinde birlikte bulunduğundan emin olun.
- Çoklu iş parçacıklı uygulamalar kullanın ve birden çok dosya genelinde yayılmış yük yükleyin.
- Yükün dağıtılması sırasında SMB çok kanallı artışın performans avantajları.
- Premium paylaşımın performansı, sağlanan paylaşma boyutu (ıOPS/çıkış/giriş) ve tek dosya limitleri ile bağlıdır. Ayrıntılar için bkz. [Premium dosya paylaşımları için sağlamayı anlama](understanding-billing.md#provisioned-model).
- Tek bir VM istemcisinin maksimum performansı hala VM sınırlarına bağlanır. Örneğin, [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) maksimum bant genişliğini 16.000 Mbps (veya 2Gbps) DESTEKLEYEBILIR, VM 'den çıkış (depolamaya yazma) tarifeli, giriş (depolamadan okuma) desteklenmez. Dosya paylaşımının performansı makine ağ sınırları, CPU 'Lar, iç depolama kullanılabilir ağ bant genişliği, GÇ boyutları, paralellik ve diğer faktörlere tabidir.
- İlk test genellikle bir sıcak olur, sonuçlarını atar ve testi yineler.
- Performans tek bir istemciyle sınırlı ise ve iş yükü hala sağlanan paylaşma sınırlarına göre sınırlıysa, yük birden fazla istemcinin üzerinden yayılırken daha yüksek performans elde edilebilir.

### <a name="the-relationship-between-iops-throughput-and-io-sizes"></a>IOPS, verimlilik ve GÇ boyutları arasındaki ilişki

**Aktarım hızı = GÇ boyutu * ıOPS**

Yüksek GÇ boyutları daha yüksek aktarım hızını azaltır ve daha yüksek gecikme sürelerine sahip olur ve bu da daha az sayıda net ıOPS elde eder. Daha küçük GÇ boyutları daha yüksek ıOPS 'yi barındıracak, ancak net verimlilik ve gecikme süreleriyle sonuçlanır.

## <a name="next-steps"></a>Sonraki adımlar

- [Dosya depolama hesabında çok kanallı SMB 'yi etkinleştirme (Önizleme)](storage-files-enable-smb-multichannel.md)
- Çok kanallı SMB hakkında daha fazla bilgi için [Windows belgelerine](/azure-stack/hci/manage/manage-smb-multichannel) bakın.
