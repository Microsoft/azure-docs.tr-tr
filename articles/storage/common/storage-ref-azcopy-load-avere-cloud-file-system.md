---
title: AzCopy Load clfs | Microsoft Docs
titleSuffix: Azure Storage
description: Bu makale, AzCopy Load clfs komutu için başvuru bilgileri sağlar.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ebf04531f29e18f9d120ca2efa17244c4282084c
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503278"
---
# <a name="azcopy-load-clfs"></a>azcopy load clfs

Yerel verileri bir kapsayıcıya aktarır ve Microsoft 'un avere Cloud FileSystem (CLFS) biçiminde depolar.

## <a name="synopsis"></a>Özeti

Load komutu, verileri Azure Blob depolama kapsayıcılarına kopyalar ve bu verileri Microsoft 'un avere Cloud FileSystem (CLFS) biçiminde depolar. Özel CLFS biçimi, Azure için Azure HPC Cache ve avere vFXT tarafından kullanılır.

Bu komuttan yararlanmak için, gerekli uzantıyı aracılığıyla yükleyin: pip3 install clfsload ~ = 1.0.23. CLFSLoad.py yolunda olduğundan emin olun. Bu adımla ilgili daha fazla bilgi için, adresini ziyaret edin [https://aka.ms/azcopy/clfs](https://aka.ms/azcopy/clfs) .

Bu komut, belirli Microsoft yüksek performanslı bilgi işlem önbelleği ürünleriyle kullanılmak üzere mevcut verileri bulut depolama alanına taşımaya yönelik basit bir seçenektir. 

Bu ürünler verileri yönetmek için özel bir bulut dosya biçimi kullandığından, bu veriler yerel kopyalama komutu aracılığıyla yüklenemez. 

Bunun yerine, verilerin önbellek ürününün kendisi aracılığıyla ya da doğru mülkiyet biçimini kullanan bu Load komutu aracılığıyla yüklenmesi gerekir.
Bu komut, önbelleği kullanmadan verileri aktarmanıza olanak tanır. Örneğin, depolamayı önceden doldurmak veya önbellek yükünü arttırmadan çalışan bir kümesine dosya eklemek için.

Hedef boş bir Azure depolama kapsayıcısıdır. Aktarım tamamlandığında, hedef kapsayıcı Azure için bir Azure HPC Cache örneği veya avere vFXT ile kullanılabilir.

> [!NOTE] 
> Bu, Load komutunun önizleme sürümüdür. Lütfen AzCopy GitHub deposunda bulunan tüm sorunları bildirin.

```
azcopy load clfs [local dir] [container URL] [flags]
```

## <a name="related-conceptual-articles"></a>İlgili kavramsal makaleler

- [AzCopy’yi kullanmaya başlama](storage-use-azcopy-v10.md)
- [AzCopy ve BLOB Storage ile veri aktarma](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy ve dosya depolama ile verileri aktarma](storage-use-azcopy-files.md)

## <a name="examples"></a>Örnekler

CLFS biçiminde SAS içeren bir kapsayıcıya tam bir dizin yükleyin:

```azcopy
azcopy load clfs "/path/to/dir" "https://[account].blob.core.windows.net/[container]?[SAS]" --state-path="/path/to/state/path"
```

## <a name="options"></a>Seçenekler

**--Compression-Type** dize, aktarımlar için kullanılacak sıkıştırma türünü belirtir. Kullanılabilir değerler: `DISABLED` , `LZ4` . (varsayılan `LZ4` )

**--**    komut yardımı Yardımı `azcopy load clfs` .

**--günlük düzeyi** dize, kullanılabilir düzeyler:,,, ve günlük dosyası için günlük ayrıntı düzeylerini tanımlar `DEBUG` `INFO` `WARNING` `ERROR` . (varsayılan `INFO` )

**--en fazla-hata** uint32 değeri, tolerans için en fazla aktarım hatası sayısını belirtir. Yeterli hata oluşursa, işi hemen durdurun.

**--yeni-oturum**   İzleme bilgileri konumunda tutulan mevcut bir işleme devam etmek yerine yeni bir iş başlatın `--state-path` . (varsayılan doğru)

**--Preserve-hardlinks**    Sabit bağlantı ilişkilerini koru.

**--State-Path** dize, iş durumu izleme için yerel bir dizinin yolunu gerektirdi. Yolun bir işi sürdürmesini sağlamak için var olan bir dizine işaret etmesi gerekir. Yeni bir iş için boş olması gerekir.

## <a name="options-inherited-from-parent-commands"></a>Üst komutlardan devralınan seçenekler

|Seçenek|Açıklama|
|---|---|
|--Cap-Mbps float|Saniye başına megabit cinsinden aktarım hızının üst sınırı. Kısa süre içinde işlem hacmi büyük bir farklılık gösterebilir. Bu seçenek sıfır olarak ayarlandıysa veya atlanırsa, üretilen iş işleme alınır.|
|--çıkış türü dize|Komutun çıktısının biçimi. Seçenekler şunlardır: Text, JSON. Varsayılan değer "Text" değeridir.|
|--Güvenilen-Microsoft-sonekler dizesi   | Azure Active Directory oturum açma belirteçlerinin gönderilebileceği ek etki alanı soneklerini belirtir.  Varsayılan değer '*. Core.Windows.net;*' dir. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Burada listelenenler varsayılan olarak eklenir. Güvenlik için yalnızca Microsoft Azure etki alanlarını Buraya yerleştirmeniz gerekir. Birden çok girişi noktalı virgülle ayırın.|

## <a name="see-also"></a>Ayrıca bkz.

- [AzCopy](storage-ref-azcopy.md)
