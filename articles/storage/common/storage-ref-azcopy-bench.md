---
title: AzCopy tezgahtır | Microsoft Docs
description: Bu makale, AzCopy tezgahtır komutu için başvuru bilgileri sağlar.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 1e49e787854069c2fcea30df7a43c3aacdd21b9e
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502037"
---
# <a name="azcopy-benchmark"></a>AzCopy kıyaslaması

, Belirtilen bir hedefe veya test verilerinin test verilerini karşıya yükleyerek ya da indirerek bir performans kıyaslaması çalıştırır. Karşıya yüklemeler için, test verileri otomatik olarak oluşturulur.

Kıyaslama komutu dışında ' Copy ' ile aynı işlemi çalıştırır: 

  - Hem kaynak hem de hedef parametreleri gerektirmek yerine, kıyaslama yalnızca bir tane alır. Bu, karşıya yüklemek veya indirmek istediğiniz blob kapsayıcısı, Azure dosya paylaşımıdır veya Azure Data Lake Storage 2. dosya sistemidir.

  - ' Mode ' parametresi, AzCopy 'ın verilen hedefe yükleme veya indirme işlemlerini test etmesi gerekip gerekmediğini açıklar. Geçerli değerler ' upload ' ve ' download '. Varsayılan değer ' upload '.

  - Yükleme kıyaslamaları için yük, kaç dosyanın otomatik olarak oluşturulduğunu ve dosyaların ne kadar önemli olduğunu denetleyen komut satırı parametreleriyle açıklanmıştır. Oluşturma işlemi tamamen bellekte gerçekleşir. Disk kullanılmıyor.

  - İndirilenler için yük, kaynakta zaten var olan dosyalardan oluşur. (Gerekirse test dosyaları oluşturma hakkında aşağıdaki örneğe bakın).
  
  - Kopyalama komutu için kullanılabilen isteğe bağlı parametrelerin yalnızca birkaçı desteklenir.
  
  - Ek Tanılamalar ölçülür ve raporlanır.
  
  - Karşıya yüklemeler için varsayılan davranış, Test çalıştırmasının sonunda aktarılan verilerin silinmesine yöneliktir.  İndirmeler için, veriler hiçbir şekilde yerel olarak kaydedilmez.

Kıyaslama modu, en yüksek aktarım hızını sağlayan paralel TCP bağlantısı sayısına otomatik olarak ayarlanır. Bu numara sonda görüntülenir. Oto ayarlamayı engellemek için AZCOPY_CONCURRENCY_VALUE ortam değişkenini belirli sayıda bağlantı olarak ayarlayın. 

Tüm olağan kimlik doğrulama türleri desteklenir. Ancak, yük boşaltma için en kullanışlı yaklaşım genellikle SAS belirtecine sahip boş bir kapsayıcı oluşturmak ve SAS kimlik doğrulamasını kullanmaktır. (İndirme modu, hedef kapsayıcıda mevcut olacak bir dizi test verisi gerektirir.)

## <a name="related-conceptual-articles"></a>İlgili kavramsal makaleler

- [AzCopy’yi kullanmaya başlama](storage-use-azcopy-v10.md)
- [Azure depolama ile AzCopy ile v10 arasındaki performansını iyileştirin](storage-use-azcopy-optimize.md)


## <a name="examples"></a>Örnekler

```azcopy
azcopy benchmark [destination] [flags]
```

Varsayılan parametrelerle bir kıyaslama testi çalıştırın (ağları 1 GB/sn 'ye kadar sınama için uygun): '

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"
```
100 dosyalarını karşıya yükleyen bir kıyaslama testi çalıştırın, her 2 GiB boyutu: (hızlı bir ağda (örneğin, 10 Gbps) bir değerlendirme için uygundur): '

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"--file-count 100 --size-per-file 2G
```
Bir kıyaslama testi çalıştırın ancak 50.000 dosya kullanın, her 8 MIB bir boyut kullanır ve MD5 karmalarına göre işlem `--put-md5` yapar (Bu, bayrağın Copy komutunda bunu yaparken olduğu şekilde). `--put-md5`Değerlendirme zaman amacı, MD5 hesaplamasının seçili dosya sayısı ve boyutu için üretilen işi etkileyip etkilemediğini test etsağlamaktır:

```azcopy
azcopy bench --mode='Upload' "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 50000 --size-per-file 8M --put-md5
```

Bir hedeften var olan dosyaları indiren bir kıyaslama testi çalıştırın

```azcopy
azcopy bench --mode='Download' "https://[account].blob.core.windows.net/[container]?<SAS?"
```

Aktarılan dosyaları silolmayan bir karşıya yükleme çalıştırın. (Bu dosyalar daha sonra bir indirme testi için yük olarak işlev görebilir)

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 100 --delete-test-data=false
```

## <a name="options"></a>Seçenekler

**--BLOB türü** dize, hedefteki blob türünü tanımlar. Farklı blob türlerini benchişaretlemek için kullanılır. Copy komutunda aynı adlandırılmış parametre ile özdeş (varsayılan "Algıla").

**--blok-boyut-MB** float bu blok boyutunu kullanın (MIB 'de belirtilir). Varsayılan değer, dosya boyutuna göre otomatik olarak hesaplanır. Ondalık kesirlere izin verilir; Örneğin, 0,25. Copy komutunda aynı adlandırılmış parametre ile özdeş.

**--Çek uzunluğu**  Aktarımdan sonra hedefteki bir dosyanın uzunluğunu denetleyin. Kaynak ve hedef arasında uyuşmazlık varsa, aktarım başarısız olarak işaretlenir. (varsayılan doğru)

**--Delete-test-Data**  Doğru ise, kıyaslama verileri kıyaslama çalıştırmasının sonunda silinir.  Verileri hedefteki tutmak istiyorsanız bu değeri false olarak ayarlayın; Örneğin, kıyaslama modu dışındaki el ile testler için bunu kullanın (varsayılan doğru).

**--File-Count** uint.  Kullanılacak otomatik oluşturulan veri dosyası sayısı (varsayılan 100).

**--Yardım**  Tezgahtır için yardım

**--günlük düzeyi** dize günlük dosyası, kullanılabilir düzeyler: bilgi (tüm istekler/yanıtlar), uyarı (yavaş yanıtlar), hata (yalnızca başarısız istekler) ve hiçbiri (çıktı günlüğü yok) için günlük ayrıntı düzeylerini tanımlar. (varsayılan "BILGI")

**--Mode** dize, AzCopy 'in bu hedeften karşıya yüklemeleri veya İndirmeleri test etmesi gerekip gerekmediğini tanımlar. Geçerli değerler ' upload ' ve ' download '. Varsayılan olarak ' upload ' seçeneği. (varsayılan ' karşıya yükle ')

**--** 0 ' dan büyükse klasörlerin sayısı uint, verileri bölmek için klasörler oluşturun.

**--PUT-MD5**  Her bir dosyanın MD5 karmasını oluşturun ve karmayı hedef blob/dosyanın Content-MD5 özelliği olarak kaydedin. (Varsayılan olarak, karma oluşturulmaz.) Copy komutunda aynı adlandırılmış parametre ile özdeş.

**--** her bir otomatik olarak oluşturulan veri dosyasının dosya başına dize boyutu. Hemen arkasından K, M veya G gelen bir sayı olmalıdır. örn. 12k veya 200G (varsayılan "250M").

## <a name="options-inherited-from-parent-commands"></a>Üst komutlardan devralınan seçenekler

**--Cap-Mbps float**  Saniye başına megabit cinsinden aktarım hızının üst sınırı. Kısa süre içinde işlem hacmi büyük bir farklılık gösterebilir. Bu seçenek sıfır olarak ayarlandıysa veya atlanırsa, üretilen iş işleme alınır.

**--komut çıktısının çıkış türü** dize biçimi. Seçenekler şunlardır: Text, JSON. Varsayılan değer ' text ' değeridir. (varsayılan "metin").

**--Güvenilen-Microsoft-Suffixes** dizesi Azure Active Directory oturum açma belirteçlerinin gönderilebileceği ek etki alanı soneklerini belirtir.  Varsayılan değer '*. Core.Windows.net;*' dir. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Burada listelenenler varsayılan olarak eklenir. Güvenlik için yalnızca Microsoft Azure etki alanlarını Buraya yerleştirmeniz gerekir. Birden çok girişi noktalı virgülle ayırın.


## <a name="see-also"></a>Ayrıca bkz.

- [AzCopy](storage-ref-azcopy.md)
