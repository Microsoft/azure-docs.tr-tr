---
title: 'Öğretici: Izleme ve ayarlama-PostgreSQL için Azure veritabanı-tek sunucu'
description: Bu öğreticide PostgreSQL için Azure veritabanı-tek sunucu izleme ve ayarlama işlemleri gösterilmektedir.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: tutorial
ms.date: 5/6/2019
ms.openlocfilehash: a12068259d82e833826bcac5e6c58059fb51c56c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336988"
---
# <a name="tutorial-monitor-and-tune-azure-database-for-postgresql---single-server"></a>Öğretici: PostgreSQL için Azure veritabanı 'nı Izleme ve ayarlama-tek sunucu

PostgreSQL için Azure Veritabanı, sunucu performansınızı anlamanıza ve geliştirmenize yardımcı olan özelliklere sahiptir. Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> * Sorguyu etkinleştirme ve bekleme istatistiklerini toplama
> * Toplanan verilere erişme ve bu verileri kullanma
> * Sorgu performansını ve zamanla oluşan bekleme istatistiklerini görüntüleme
> * Performans önerileri almak için bir veritabanını analiz etme
> * Performans önerilerini uygulama

## <a name="prerequisites"></a>Önkoşullar
PostgreSQL sürüm 9.6 veya 10’un yüklü olduğu bir PostgreSQL için Azure Veritabanı sunucusuna ihtiyacınız vardır. Bir sunucu oluşturmak için [Öğretici oluştur](tutorial-design-database-using-azure-portal.md)’daki adımları izleyebilirsiniz.

> [!IMPORTANT]
> **Query Store**, **Sorgu Performansı İçgörüleri** ve **Performans Önerisi**, Genel Önizleme aşamasındadır.

## <a name="enabling-data-collection"></a>Veri koleksiyonunu etkinleştirme
[Query Store](concepts-query-store.md), sorguların geçmişini toplar ve sunucunuzdaki istatistikleri bekler ve sunucunuzdaki **azure_sys** veritabanında saklar. Bu tercihli bir özelliktir. Etkinleştirmek için:

1. Azure portalını açın.

2. PostgreSQL için Azure Veritabanı sunucunuzu seçin.

3. Soldaki menüde yer alan **Ayarlar** bölümündeki **Sunucu Parametreleri**’ni seçin.

4. Sorgu performansı verilerini toplamaya başlamak için, **pg_qs.query_capture_mode** komutunu **TOP** olarak ayarlayın. Bekleme istatistiklerini toplamak için, **pgms_wait_sampling.query_capture_mode** komutunu **ALL** olarak ayarlayın. Kaydedin.
   
   :::image type="content" source="./media/tutorial-performance-intelligence/query-store-parameters.png" alt-text="Query Store sunucusu parametreleri":::

5. İlk toplu iş verilerinin **azure_sys** veritabanında kalıcı olması için 20 dakikaya izin verin.


## <a name="performance-insights"></a>Performans içgörüleri
Azure portaldaki [Sorgu Performansı İçgörüleri](concepts-query-performance-insight.md) görünümü, Query Store’dan alınan önemli bilgilerdeki görselleştirmeleri kullanıma açar. 

1. Azure Veritabanı için PostgreSQL sunucunuzun portal sayfasında, soldaki menünün **Destek + sorun giderme** bölümündeki **Sorgu performansı içgörüleri**’ni seçin.

2. **Uzun süren sorgular** sekmesi, yürütme başına ortalama 15 dakikalık aralıklarla toplanan en iyi 5 sorguyu gösterir. 
   
   :::image type="content" source="./media/tutorial-performance-intelligence/query-performance-insight-landing-page.png" alt-text="Sorgu Performansı İçgörüleri giriş sayfası":::

   **Sorgu Sayısı** açılır menüsünden seçerek, daha fazla sorgu görüntüleyebilirsiniz. Bunu yaptığınızda, grafik renkleri belirli bir Sorgu Kimliği için değişebilir.

3. Belirli bir zaman penceresine daraltmak için grafikte tıklayıp sürükleyebilirsiniz.

4. Sırasıyla daha kısa veya daha uzun bir süreyi görüntülemek için yakınlaştırma ve uzaklaştırma simgelerini kullanın.

5. O zaman penceresindeki uzun süren sorgular hakkında daha fazla ayrıntı öğrenmek için grafiğin altındaki tabloyu görüntüleyin.

6. Sunucudaki beklemelerle ilgili görselleştirmeleri görüntülemek için **Bekleme İstatistikleri** sekmesini seçin.
   
   :::image type="content" source="./media/tutorial-performance-intelligence/query-performance-insight-wait-statistics.png" alt-text="Sorgu Performansı İçgörüleri bekleme istatistikleri":::

### <a name="permissions"></a>İzinler
Sorgu Performansı İçgörüleri’ndeki metni görünüm için **Sahip** veya **Katkıda bulunan** izinleri gereklidir. **Okuyucu**, grafikleri ve tabloları görüntüleyebilir ancak metni sorgulayamaz.


## <a name="performance-recommendations"></a>Performans önerileri
[Performans Önerileri](concepts-performance-recommendations.md) özelliği, performansı iyileştirme potansiyeli olan dizinleri tanımlamak için sunucunuzdaki iş yüklerini analiz eder.

1. PostgreSQL sunucunuzun Azure portalı sayfasındaki menü çubuğunun **Destek + sorun giderme** bölümünden **Performans Önerileri**’ni açın.
   
   :::image type="content" source="./media/tutorial-performance-intelligence/performance-recommendations-landing-page.png" alt-text="Performans Önerileri giriş sayfası":::

2. **Analiz**’i seçin ve bir veritabanı belirtin. Bu işlem analizi başlatır.

3. Bu işlemin tamamlanması iş yükünüze bağlı olarak birkaç dakika sürebilir. Analiz tamamlanınca portalda bir bildirim olur.

4. Hiçbir öneri bulunamamışsa, **Performans Önerileri** penceresi bir öneri listesi gösterir. 

5. Bir öneri, ilgili **Veritabanı**, **Tablo**, **Sütun** ve **Dizin Boyutu** ile ilgili bilgileri gösterir.

   :::image type="content" source="./media/tutorial-performance-intelligence/performance-recommendations-result.png" alt-text="Performans Önerileri sonucu":::

6. Öneriyi uygulamak için, sorgu metnini kopyalayın ve seçtiğiniz istemciden çalıştırın.

### <a name="permissions"></a>İzinler
Performans Önerileri özelliğini kullanarak analiz çalıştırmak için **Sahip** veya **Katkıda bulunan** izinleri gereklidir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Yukarıdaki adımlarda, bir sunucu grubunda Azure kaynakları oluşturdunuz. Gelecekte bu kaynaklara ihtiyaç duymazsanız, sunucu grubunu silin. Sunucu grubunuzun *genel bakış* sayfasında *Sil* düğmesine basın. Bir açılır sayfada istendiğinde, sunucu grubunun adını onaylayın ve son *Sil* düğmesine tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> PostgreSQL için Azure Veritabanı’nda [izleme ve ayarlama](concepts-monitoring.md) hakkında daha fazla bilgi edinin.