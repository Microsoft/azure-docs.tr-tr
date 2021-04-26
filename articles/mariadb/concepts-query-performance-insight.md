---
title: Sorgu Performansı İçgörüleri-MariaDB için Azure veritabanı
description: Bu makalede, MariaDB için Azure veritabanı 'nda Sorgu Performansı İçgörüleri özelliği açıklanmaktadır
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: b643ba3305736480e06d7c10d594b2271839038f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664240"
---
# <a name="query-performance-insight-in-azure-database-for-mariadb"></a>MariaDB için Azure Veritabanı'nda Sorgu Performansı İçgörüleri

**Uygulama hedefi:** MariaDB için Azure veritabanı 10,2

Sorgu Performansı İçgörüleri, en uzun çalışan sorguların ne olduğunu, zaman içinde nasıl değişdiklerinizi ve neleri etkilemeyi beklediğini hızlı bir şekilde tanımanıza yardımcı olur.

## <a name="common-scenarios"></a>Genel senaryolar

### <a name="long-running-queries"></a>Uzun süre çalışan sorgular

- Son X saat içinde en uzun çalışan sorguları tanımlama
- Kaynaklarda bekleyen ilk N sorguyu tanımlama
 
### <a name="wait-statistics"></a>Bekleme istatistikleri

- Sorgu için bekleme yapısını anlama
- Kaynak beklemeleri ve kaynak çekişmelerinin nerede olduğunu anlama

## <a name="permissions"></a>İzinler

Sorgu Performansı İçgörüleri’ndeki metni görünüm için **Sahip** veya **Katkıda bulunan** izinleri gereklidir. **Okuyucu**, grafikleri ve tabloları görüntüleyebilir ancak metni sorgulayamaz.

## <a name="prerequisites"></a>Önkoşullar

Sorgu Performansı İçgörüleri çalışması için, verilerin [sorgu deposunda](concepts-query-store.md)bulunması gerekir.

## <a name="viewing-performance-insights"></a>Performans öngörülerini görüntüleme

Azure portaldaki [Sorgu Performansı İçgörüleri](concepts-query-performance-insight.md) görünümü, Query Store’dan alınan önemli bilgilerdeki görselleştirmeleri kullanıma açar.

MariaDB sunucusu için Azure veritabanı 'nın portal sayfasında, menü çubuğunun **akıllı performans** bölümü altında **sorgu performansı içgörüleri** ' yi seçin.

### <a name="long-running-queries"></a>Uzun süre çalışan sorgular

**Uzun süre çalışan sorgular** sekmesi, 15 dakikalık aralıklarda toplanan, yürütme başına ortalama süreye göre ilk 5 sorguyu gösterir. **Sorgu sayısı** açılan listesinden seçerek daha fazla sorgu görüntüleyebilirsiniz. Bunu yaptığınızda, grafik renkleri belirli bir Sorgu Kimliği için değişebilir.

Belirli bir zaman penceresine daraltmak için grafikte tıklayıp sürükleyebilirsiniz. Alternatif olarak, daha küçük veya daha büyük bir zaman aralığı görüntülemek için Yakınlaştır ve Kapat simgelerini kullanın.

![Uzun süre çalışan sorguları Sorgu Performansı İçgörüleri](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

### <a name="wait-statistics"></a>Bekleme istatistikleri 

> [!NOTE]
> Bekleme istatistikleri sorgu performans sorunlarını gidermeye yöneliktir. Yalnızca sorun giderme amacıyla açık olması önerilir. <br>Azure portal "hata iletisini alırsanız,"*Microsoft. Dbformarıdb "için karşılaşılan sorun; istek yerine getirilemiyor. Bu sorun devam ederse veya beklenmiyorsa lütfen bu bilgilerle desteğe başvurun.*" Bekleme istatistiklerini görüntülerken, daha kısa bir zaman aralığı kullanın.

Bekleme istatistikleri, belirli bir sorgunun yürütülmesi sırasında oluşan bekleme olaylarının bir görünümünü sağlar. [MySQL Engine belgelerindeki](https://go.microsoft.com/fwlink/?linkid=2098206)bekleme olayı türleri hakkında daha fazla bilgi edinin.

Sunucudaki beklemelerle ilgili görselleştirmeleri görüntülemek için **Bekleme İstatistikleri** sekmesini seçin.

Bekleme istatistikleri görünümünde görüntülenen sorgular, belirtilen zaman aralığı boyunca en büyük değeri gösteren sorgulara göre gruplandırılır.

![Sorgu Performansı İçgörüleri bekleyen istatistikler](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Sonraki adımlar

- MariaDB için Azure veritabanı 'nda [izleme ve ayarlama](concepts-monitoring.md) hakkında daha fazla bilgi edinin.