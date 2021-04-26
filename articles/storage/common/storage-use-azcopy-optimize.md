---
title: Azure Storage ile AzCopy ile v10 arasındaki performansını iyileştirin | Microsoft Docs
description: Bu makale, Azure depolama ile AzCopy ile v10 arasındaki performansını iyileştirmenize yardımcı olur.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: b60fc4b1fc20c455c2c409f544a8af16f1dbf8d1
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509168"
---
# <a name="optimize-the-performance-of-azcopy-with-azure-storage"></a>Azure depolama ile AzCopy performansını iyileştirin

AzCopy, bir depolama hesabına iki yönlü blob veya dosya kopyalama işlemi gerçekleştirmenizi sağlayan bir komut satırı yardımcı programıdır. Bu makale, performansı iyileştirmenize yardımcı olur.

> [!NOTE]
> AzCopy kullanmaya başlamanıza yardımcı olacak içerik arıyorsanız bkz. [AzCopy ile çalışmaya başlama](storage-use-azcopy-v10.md)

Performans ve kaynak tüketimine ilişkin en iyi zorunluluğunu getirir bulmak için, performansı ve ardından komut ve ortam değişkenlerini kullanabilirsiniz.

## <a name="run-benchmark-tests"></a>Kıyaslama testlerini Çalıştır

Genel performans istatistiklerini görüntülemek ve performans sorunlarını belirlemek için, belirli blob kapsayıcıları veya dosya paylaşımlarında performans kıyaslama testini çalıştırabilirsiniz. Oluşturulan test verilerini karşıya yükleyerek veya indirerek testi çalıştırabilirsiniz. 

Bir performans kıyaslama testi çalıştırmak için aşağıdaki komutu kullanın.

**Syntax**

`azcopy benchmark 'https://<storage-account-name>.blob.core.windows.net/<container-name>'`

**Örnek**

```azcopy
azcopy benchmark 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

> [!TIP]
> Bu örnek tek tırnak (' ') ile yol bağımsız değişkenlerini barındırır. Windows komut kabuğu (cmd.exe) hariç tüm komut kabukta tek tırnak işaretleri kullanın. Bir Windows komut kabuğu (cmd.exe) kullanıyorsanız, yol bağımsız değişkenlerini tek tırnak (' ') yerine çift tırnak işareti ("") içine alın.

Bu komut test verilerini belirtilen bir hedefe yükleyerek bir performans kıyaslaması çalıştırır. Test verileri bellekte oluşturulur, hedefe yüklenir ve ardından test tamamlandıktan sonra hedefteki silinir. İsteğe bağlı komut parametrelerini kullanarak oluşturulacak dosya sayısını ve bunların kaç boyutunu istediğinizi belirtebilirsiniz.

Bu testi verileri indirerek çalıştırmayı tercih ederseniz `mode` parametresini olarak ayarlayın `download` . Ayrıntılı başvuru belgeleri için bkz. [AzCopy kıyaslaması](storage-ref-azcopy-bench.md). 

## <a name="optimize-for-large-numbers-of-small-files"></a>Çok sayıda küçük dosya için iyileştirin

Küçük dosyalar aktarılırken aktarım hızı, özellikle de büyük sayılar aktarılırken azalabilir. Performansı en üst düzeye çıkarmak için, her bir işin boyutunu küçültün. İndirme ve karşıya yükleme işlemleri için eşzamanlılık artırma, günlük etkinliğini azaltma ve yüksek performans maliyetlerine neden olan özellikleri kapatma.

#### <a name="reduce-the-size-of-each-job"></a>Her bir işin boyutunu küçültün

En iyi performansı elde etmek için, her işlerin 10.000.000 'den daha az dosya aktarmasına emin olun. AzCopy iş izleme mekanizması önemli miktarda ek yük oluşturduğundan, 50.000.000 'den fazla dosya aktarımı yapan işler kötü bir şekilde çalışabilir. Ek yükü azaltmak için büyük işleri daha küçük olanlara bölüyi göz önünde bulundurun. 

Bir işin boyutunu azaltmanın bir yolu, bir işten etkilenen dosya sayısını sınırlandırmaktır. Bunu yapmak için komut parametrelerini kullanabilirsiniz. Örneğin, bir iş, `include path` [AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunun bir parçası olarak parametresini kullanarak dizinlerin yalnızca bir alt kümesini kopyalayabilir. 

`include-pattern`Belirli bir uzantıya sahip dosyaları kopyalamak için parametresini kullanın (örneğin: `*.pdf` ). Ayrı bir işte, `exclude-pattern` uzantısı olmayan tüm dosyaları kopyalamak için parametresini kullanın `*.pdf` . Örnekler için bkz. [belirli dosyaları karşıya yükleme](storage-use-azcopy-blobs-upload.md#upload-specific-files) ve [belirli blob 'ları indirme](storage-use-azcopy-blobs-download.md#download-specific-blobs) .

Büyük işleri daha küçük olanlara nasıl böleceğinize karar verdikten sonra, birden fazla sanal makinede (VM) işleri çalıştırmayı göz önünde bulundurun.

#### <a name="increase-concurrency"></a>Eşzamanlılık artırma

Dosya yüklüyorsanız veya indiriyorsanız, `AZCOPY_CONCURRENCY_VALUE` makinenizde oluşabilecek eşzamanlı isteklerin sayısını artırmak için ortam değişkenini kullanın. Makinenizin performansından ödün vermeden bu değişkeni mümkün olduğunca yüksek olarak ayarlayın. Bu değişken hakkında daha fazla bilgi edinmek için bu makalenin [eşzamanlı istek sayısını artırma](#increase-the-number-of-concurrent-requests) bölümüne bakın.

Blob 'ları depolama hesapları arasında kopyalıyorsanız, `AZCOPY_CONCURRENCY_VALUE` ortam değişkeninin değerini değerinden büyük bir değere ayarlamayı göz önünde bulundurun `1000` . AzCopy sunucudan sunucuya API 'Ler kullandığından, veriler doğrudan depolama sunucuları arasında kopyalandığından ve makinenizin işlem gücünü kullanmadığı için bu değişkeni yüksek bir şekilde ayarlayabilirsiniz.  

#### <a name="decrease-the-number-of-logs-generated"></a>Oluşturulan günlüklerin sayısını azaltın

AzCopy 'in bir işlemi tamamladığı için oluşturduğu günlük girişlerinin sayısını azaltarak performansı artırabilirsiniz. Varsayılan olarak, AzCopy bir işlemle ilgili tüm etkinlikleri günlüğe kaydeder. En iyi performansı elde etmek için `log-level` kopyalama, eşitleme veya kaldırma komutunun parametresini olarak ayarlamayı düşünün `ERROR` . Bu şekilde, AzCopy yalnızca hataları günlüğe kaydeder. Varsayılan olarak, değer günlük düzeyi olarak ayarlanır `INFO` . 

#### <a name="turn-off-length-checking"></a>Uzunluk denetimini kapat 

Dosyaları karşıya yüklüyor veya indiriyorsanız `--check-length` kopyalama ve eşitleme komutlarınızın öğesini olarak ayarlamayı göz önünde bulundurun `false` . Bu, AzCopy 'in bir aktarımdan sonra dosyanın uzunluğunu doğrulamasını önler. Varsayılan olarak, AzCopy, kaynak ve hedef dosyaların bir aktarım tamamlandıktan sonra eşleştiğinden emin olmak için uzunluğu denetler. AzCopy, her dosya aktarımından sonra bu denetimi gerçekleştirir. Bu denetim, işler çok sayıda küçük dosyayı aktarırken performansı düşürebilir. 

#### <a name="turn-on-concurrent-local-scanning-linux"></a>Eşzamanlı yerel taramayı aç (Linux)

Bazı Linux sistemlerindeki dosya taramaları, tüm paralel ağ bağlantılarının doygunluğu için yeterince hızlı yürütülmez. Bu gibi durumlarda, öğesini `AZCOPY_CONCURRENT_SCAN` olarak ayarlayabilirsiniz `true` . 

## <a name="increase-the-number-of-concurrent-requests"></a>Eşzamanlı isteklerin sayısını artırma

Ortam değişkenini ayarlayarak aktarım hızını artırabilirsiniz `AZCOPY_CONCURRENCY_VALUE` . Bu değişken gerçekleşebilecek eşzamanlı istek sayısını belirtir.  

Bilgisayarınızda 5 ' ten az CPU varsa, bu değişkenin değeri olarak ayarlanır `32` . CPU dana fazlaysa, varsayılan değer CPU sayısının 16 katına eşittir. Bu değişkenin en büyük varsayılan değeri `3000` , ancak bu değeri el ile veya daha düşük bir şekilde ayarlayabilirsiniz. 

| İşletim sistemi | Komut  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **macOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

`azcopy env`Bu değişkenin geçerli değerini denetlemek için öğesini kullanın. Değer boşsa, herhangi bir AzCopy günlük dosyasının başlangıcına bakarak hangi değerin kullanıldığını okuyabilirsiniz. Seçili değer ve seçildiği neden burada raporlanır.

Bu değişkeni ayarlamadan önce, bir kıyaslama testi çalıştırmanızı öneririz. Kıyaslama test süreci, önerilen eşzamanlılık değerini rapor eder. Alternatif olarak, ağ koşullarınız ve yüklerinizin farklılık göstermesi durumunda bu değişkeni `AUTO` belirli bir sayı yerine kelimeyle ayarlayın. Bu, AzCopy 'in kıyaslama testlerinde kullandığı otomatik ayarlama işlemini her zaman çalıştırmasına neden olur.

## <a name="limit-the-throughput-data-rate"></a>Verimlilik veri hızını sınırlayın

`cap-mbps`İş hızı veri hızına bir tavan koymak için komutlarınızın bayrağını kullanabilirsiniz. Örneğin, aşağıdaki komut bir işi sürdürür ve `10` saniye başına megabit (MB) cinsinden bir işleme devam eder. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

## <a name="optimize-memory-use"></a>Bellek kullanımını iyileştirme

`AZCOPY_BUFFER_GB`Dosya indirme ve karşıya yükleme sırasında AzCopy 'in arabelleğe alma için kullanmasını istediğiniz en fazla sistem belleği miktarını belirtmek için ortam değişkenini ayarlayın. Bu değeri gigabayt (GB) cinsinden ifade edin.

| İşletim sistemi | Komut  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **macOS** | `export AZCOPY_BUFFER_GB=<value>` |

> [!NOTE]
> İş izleme, bellek kullanımında her zaman ek yük getirir. Miktar, bir işteki aktarımların sayısına göre değişir. Arabellekler, bellek kullanımının en büyük bileşenidir. Gereksinimlerinizi yaklaşık olarak karşılamak için kullanarak ek yükün denetimine yardımcı olabilirsiniz `AZCOPY_BUFFER_GB` , ancak genel bellek kullanımının tamamen üst sınırı için kullanılabilir bayrak yoktur.

## <a name="optimize-file-synchronization"></a>Dosya eşitlemesini iyileştirme

[Eşitleme](storage-ref-azcopy-sync.md) komutu hedefteki tüm dosyaları tanımlar ve ardından eşitleme işlemine başlamadan önce dosya adlarını ve son değiştirilme zaman damgalarını karşılaştırır. Çok sayıda dosya varsa, bu ön işlemden yararlanarak performansı artırabilirsiniz. 

Bunu gerçekleştirmek için, bunun yerine [AzCopy kopyalama](storage-ref-azcopy-copy.md) komutunu kullanın ve `--overwrite` bayrağını olarak ayarlayın `ifSourceNewer` . AzCopy, dosyaları, ön tarama ve karşılaştırmalar yapılmadan kopyalandıkları gibi karşılaştıracaktır. Bu, Karşılaştırılacak çok sayıda dosya olduğu durumlarda bir performans kenarı sağlar.

[AzCopy kopyalama](storage-ref-azcopy-copy.md) komutu, hedefteki dosyaları silmez, bu nedenle hedefteki dosyaları kaynak üzerinde olmayan bir şekilde silmek isterseniz [AzCopy Sync](storage-ref-azcopy-sync.md) komutunu `--delete-destination` bayrağıyla veya değerine ayarlanmış şekilde kullanın `true` `prompt` .

## <a name="see-also"></a>Ayrıca bkz.

- [AzCopy’yi kullanmaya başlama](storage-use-azcopy-v10.md)