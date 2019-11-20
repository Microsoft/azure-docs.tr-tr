---
title: Azure Data Lake Storage 1. performans ayarlama-PowerShell
description: Azure Data Lake Storage 1. Azure PowerShell kullanırken performansı geliştirme hakkında ipuçları.
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: stewu
ms.openlocfilehash: c975af1799d427651b76bb9fde5ff765afed3f86
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904571"
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-storage-gen1"></a>PowerShell 'i Azure Data Lake Storage 1. kullanmaya yönelik performans ayarlama Kılavuzu

Bu makalede, PowerShell kullanırken Data Lake Storage 1. çalışmak için daha iyi performans sağlamak üzere ayarlayacağınız özellikler açıklanmaktadır.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="performance-related-properties"></a>Performansla ilgili özellikler

| Özellik            | Varsayılan | Açıklama |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Bu parametre, her bir dosya karşıya yüklenirken veya indirilirken kaç paralel iş parçacığı kullanılacağını seçmenize olanak tanır. Bu sayı, dosya başına ayrılabilecek en fazla iş parçacığını temsil eder, ancak senaryonuza bağlı olarak daha az iş parçacığı alabilirsiniz (örneğin, 1 KB 'lık bir dosyayı karşıya yüklüyorsanız, 20 iş parçacığı sorsanız bile bir iş parçacığı alırsınız).  |
| ConcurrentFileCount | 10      | Bu parametre özellikle klasörlerin karşıya yüklenmesi ve indirilmesi içindir. Bu parametre, karşıya yüklenebilecek veya indirilebilecek eş zamanlı dosya sayısını belirler. Bu sayı, tek seferde karşıya yüklenebilen veya indirilebilecek en fazla eş zamanlı dosya sayısını temsil eder, ancak senaryonuza bağlı olarak daha az eşzamanlılık alabilirsiniz (örneğin, iki dosya yüklüyorsanız, iki dosyayı karşıya yüklüyorsanız, sorun 15). |

**Örnek:**

Bu komut, dosya başına 20 iş parçacığı ve 100 eşzamanlı dosya kullanarak Data Lake Storage 1. dosyalarını kullanıcının yerel sürücüsüne indirir.

```PowerShell
Export-AzDataLakeStoreItem -AccountName "Data Lake Storage Gen1 account name" `
    -PerFileThreadCount 20 `
    -ConcurrentFileCount 100 `
    -Path /Powershell/100GB `
    -Destination C:\Performance\ `
    -Force `
    -Recurse
```

## <a name="how-to-determine-property-values"></a>Özellik değerlerini belirleme

Bir sonraki soru, performansla ilgili özellikler için hangi değerin sağlanması gerektiğine göre belirlenir. Aşağıda kullanabileceğiniz bazı yönergeler verilmiştir.

* **1. Adım: toplam iş parçacığı sayısını belirleme** -kullanılacak toplam iş parçacığı sayısı hesaplanarak başlayın. Genel bir kılavuz olarak, her fiziksel çekirdek için altı iş parçacığı kullanmanız gerekir.

    `Total thread count = total physical cores * 6`

    **Örnek:**

    PowerShell komutlarını 16 çekirdekli bir D14 VM’den çalıştırdığınız varsayılmıştır

    `Total thread count = 16 cores * 6 = 96 threads`

* **2. Adım: PerFileThreadCount değerini hesaplayın** -dosya boyutuna göre PerFileThreadCount değerini hesapladık. 2,5 GB 'tan küçük dosyalar için, varsayılan 10 ' un yeterli olması nedeniyle bu parametreyi değiştirmeniz gerekmez. 2,5 GB 'tan büyük dosyalar için ilk 2,5 GB için temel olarak 10 iş parçacığı kullanmalı ve dosya boyutundaki her ek 256-MB artışı için 1 iş parçacığı eklemeniz gerekir. Birçok farklı boyutta dosya içeren bir klasörü kopyalıyorsanız dosyaları benzer dosya boyutları halinde gruplandırmayı göz önünde bulundurun. Dosya boyutlarının benzer olmaması performansın düşmesine neden olabilir. Benzer boyutlu dosyaları gruplandırmanız mümkün değilse PerFileThreadCount değerini en büyük dosya boyutuna göre ayarlamanız gerekir.

    `PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size`

    **Örnek:**

    1 GB ile 10 GB arasında 100 dosya olduğunu varsayarsak, eşitlik için en büyük dosya boyutu olan 10 GB 'ı kullanıyoruz, bu, aşağıdaki gibi okuyacağız.

    `PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads`

* **3. Adım: ConcurrentFilecount değerini hesaplayın** -aşağıdaki denklemi temel alarak ConcurrentFileCount değerini hesaplamak için toplam iş parçacığı sayısı ve PerFileThreadCount değerini kullanın:

    `Total thread count = PerFileThreadCount * ConcurrentFileCount`

    **Örnek:**

    Kullandığımız örnek değerler temel alınmıştır

    `96 = 40 * ConcurrentFileCount`

    Sonuç olarak **ConcurrentFileCount** değeri **2,4** ve bunu **2**’ye yuvarlayabiliriz.

## <a name="further-tuning"></a>Daha fazla ayar

Üzerinde çalışılan dosyaların boyutu çeşitlilik gösterdiğinden daha fazla ayar yapmanız gerekebilir. Önceki hesaplama, dosyaların tümü veya çoğu 10 GB aralığa daha büyükse ve bundan daha fazlaysa iyi bir şekilde çalışacaktır. Bunun yerine çoğu dosya küçük olacak şekilde birçok farklı dosya boyutu varsa PerFileThreadCount değerini azaltabilirsiniz. PerFileThreadCount değerini azaltarak ConcurrentFileCount değerini artırabiliriz. Bu nedenle, çok sayıda dosyanın 5 GB 'lik aralıkta daha küçük olduğunu varsaydığımızda, hesaplamamızı yinelenebiliriz:

`PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20`

Bu nedenle, **Concurrentfilecount** 96/20 olur, bu 4,8, **4**' e yuvarlanır.

Dosya boyutlarınızın dağılımına göre **PerFileThreadCount** değerini artırıp azaltarak bu ayarları değiştirmeye devam edebilirsiniz.

### <a name="limitation"></a>Sınırlama

* **Dosya sayısı ConcurrentFileCount değerinden az**: Karşıya yüklemekte olduğunuz dosyaların sayısı hesapladığınız **ConcurrentFileCount** değerinden azsa **ConcurrentFileCount** değerini dosya sayısına eşit olacak şekilde azaltmanız gerekir. **PerFileThreadCount** değerini artırmak için kalan iş parçacıklarından dilediğinizi kullanabilirsiniz.

* **Çok fazla iş parçacığı**: Kümenizin boyutunu artırmadan iş parçacığı sayısını çok fazla artırırsanız düşük performans riskiyle karşılaşabilirsiniz. CPU’da bağlam değişimi sırasında çekişme sorunları olabilir.

* **Yetersiz eşzamanlılık**: Eşzamanlılık yeterli değilse kümeniz çok küçük olabilir. Kümenizde daha fazla eşzamanlılık sağlayan düğümlerin sayısını artırabilirsiniz.

* **Azaltma hataları**: Eşzamanlılığınız çok yüksekse azaltma hataları görebilirsiniz. Azaltma hataları görüyorsanız eşzamanlılığı azaltmanız veya bize ulaşmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

* [Büyük veri gereksinimleri için Azure Data Lake Storage 1. kullanma](data-lake-store-data-scenarios.md) 
* [Data Lake Storage Gen1'de verilerin güvenliğini sağlama](data-lake-store-secure-data.md)
* [Data Lake Storage 1. ile Azure Data Lake Analytics kullanma](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight 'ı Data Lake Storage 1. ile kullanma](data-lake-store-hdinsight-hadoop-use-portal.md)

