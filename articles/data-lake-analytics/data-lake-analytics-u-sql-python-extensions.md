---
title: Azure Data Lake Analytics 'de Python ile U-SQL betiklerini genişletme
description: Azure Data Lake Analytics kullanarak U-SQL betiklerinizde Python kodu çalıştırmayı öğrenin
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/20/2017
ms.custom: devx-track-python
ms.openlocfilehash: d81e529d7073e8200c5a1d1fa8f51504b1dc9259
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92218451"
---
# <a name="extend-u-sql-scripts-with-python-code-in-azure-data-lake-analytics"></a>U-SQL betiklerini Azure Data Lake Analytics Python kodu ile genişletme

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, Python uzantılarının Azure Data Lake Analytics hesabınızda yüklü olduğundan emin olun.

* Azure portal Data Lake Analytics hesabınıza gidin
* Sol taraftaki menüde, **Başlarken** bölümünde **örnek betiklerine** tıklayın
* **U-SQL uzantılarını yükleyip** **Tamam 'a** tıklayın

## <a name="overview"></a>Genel Bakış

U-SQL için Python uzantıları, geliştiricilerin Python kodu üzerinde yüksek düzeyde paralel yürütme gerçekleştirmesini sağlar. Aşağıdaki örnek, temel adımları göstermektedir:

* `REFERENCE ASSEMBLY`U-SQL betiği Için Python uzantılarını etkinleştirmek üzere ifadesini kullanın
* `REDUCE`Anahtar üzerinde giriş verilerini bölümlemek için işlemi kullanma
* U-SQL için Python uzantıları, `Extension.Python.Reducer` Reducer atanan her bir köşede Python kodu çalıştıran yerleşik bir Reducer () içerir.
* U-SQL betiği, `usqlml_main` giriş olarak bir Pandas dataframe kabul eden adlı ve çıkış olarak bir Pandas dataframe döndüren bir işlevi olan gömülü Python kodunu içerir.

```usql
REFERENCE ASSEMBLY [ExtPython];
DECLARE @myScript = @"
def get_mentions(tweet):
    return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )
def usqlml_main(df):
    del df['time']
    del df['author']
    df['mentions'] = df.tweet.apply(get_mentions)
    del df['tweet']
    return df
";
@t  =
    SELECT * FROM
       (VALUES
           ("D1","T1","A1","@foo Hello World @bar"),
           ("D2","T2","A2","@baz Hello World @beer")
       ) AS date, time, author, tweet );
@m  =
    REDUCE @t ON date
    PRODUCE date string, mentions string
    USING new Extension.Python.Reducer(pyScript:@myScript);
OUTPUT @m
    TO "/tweetmentions.csv"
    USING Outputters.Csv();
```

## <a name="how-python-integrates-with-u-sql"></a>Python U-SQL ile nasıl tümleştirilir?

### <a name="datatypes"></a>Türleriyle

* U-SQL ' den gelen dize ve sayısal sütunlar, Pandas ve U-SQL arasında olduğu gibi dönüştürülür
* U-SQL null değerleri Pandas değerlerine ve öğesinden dönüştürülür `NA`

### <a name="schemas"></a>Şemalar

* Pandas 'teki Dizin vektörleri U-SQL ' de desteklenmez. Python işlevindeki tüm giriş verisi çerçeveleri her zaman 0 ' dan 1 ' den fazla satır sayısı kadar olan 64 bitlik bir sayısal dizine sahiptir.
* U-SQL veri kümelerinde yinelenen sütun adları olamaz
* U-SQL veri kümeleri dize olmayan sütun adları.

### <a name="python-versions"></a>Python sürümleri

Yalnızca Python 3.5.1 (Windows için derlenen) desteklenir.

### <a name="standard-python-modules"></a>Standart Python modülleri

Tüm standart Python modülleri dahildir.

### <a name="additional-python-modules"></a>Ek Python modülleri

Standart Python kitaplıklarının yanı sıra, yaygın olarak kullanılan birkaç Python kitaplığı dahil edilmiştir:

* pandas
* numpy
* numexpr

### <a name="exception-messages"></a>Özel durum Iletileri

Şu anda Python kodundaki bir özel durum genel köşe hatası olarak gösteriliyor. Gelecekte, U-SQL Işi hata iletilerinde Python özel durum iletisi görüntülenir.

### <a name="input-and-output-size-limitations"></a>Giriş ve çıkış boyutu sınırlamaları

Her köşenin kendisine atanan sınırlı miktarda belleği vardır. Şu anda bu sınır, AU için 6 GB 'dir. Giriş ve çıkış veri çerçevelerinin Python kodundaki bellekte bulunması gerektiğinden, giriş ve çıkış için Toplam Boyut 6 GB 'ı aşamaz.

## <a name="next-steps"></a>Sonraki adımlar

* [Microsoft Azure Data Lake Analytics'e genel bakış](data-lake-analytics-overview.md)
* [Visual Studio için Data Lake Araçları'nı kullanarak U-SQL betikleri geliştirme](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure Data Lake Analytics işleri için U-SQL pencere işlevlerini kullanma](./data-lake-analytics-u-sql-get-started.md)
* [Visual Studio Code için Azure Data Lake Araçları’nı kullanma](data-lake-analytics-data-lake-tools-for-vscode.md)