---
title: Azure Stream Analytics sorguları sorunlarını giderme
description: Bu makalede, sorgularınızı Azure Stream Analytics işlerinde sorun giderme teknikleri açıklar.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 586ddb237144daddf0cbfd19785fcba7658469a0
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621468"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Azure Stream Analytics sorguları sorunlarını giderme

Bu makalede, Stream Analytics sorgularını ve bunları nasıl giderebileceğinizden geliştirme ile yaygın sorunları açıklar.

## <a name="query-is-not-producing-expected-output"></a>Sorgu beklenen çıkış üretmez 
1.  Hataları yerel olarak test ederek inceleyin:
    - Üzerinde **sorgu** sekmesinde **Test**. İçin indirilen örnek verileri kullanarak [sorguyu test](stream-analytics-test-query.md). Hataları inceleyin ve bunları düzeltmeye çalışın.   
    - Ayrıca [sorgunuzda doğrudan Canlı giriş test](stream-analytics-live-data-local-testing.md) Visual Studio için Stream Analytics araçları kullanarak.

2.  Kullanırsanız [ **TIMESTAMP By**](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics), olayları zaman damgaları büyüktür sahip olduğunuzu doğrulayın [iş başlangıç zamanı](stream-analytics-out-of-order-and-late-events.md).

3.  Yaygın görülen tehlikeleri gibi kaldırın:
    - A [ **burada** ](https://docs.microsoft.com/stream-analytics-query/where-azure-stream-analytics) yan tümcesinin sorgudaki tüm olayları, herhangi bir çıktı oluşturulmasını önleme filtrelendi.
    - A [ **ATAMA** ](https://docs.microsoft.com/stream-analytics-query/cast-azure-stream-analytics) işlevi başarısız, işinin başarısız olmasına neden olur. Tür atama hataları önlemek için [ **TRY_CAST** ](https://docs.microsoft.com/stream-analytics-query/try-cast-azure-stream-analytics) yerine.
    - Pencere işlevleri kullandığınızda, tüm pencere süresi sorgudan bir sonuç görmek bekleyin.
    - Zaman damgası olayları için iş başlangıç zamanından önce gelir ve bu nedenle, olayların bırakılma.

4.  Olay sıralama ilkeleri beklendiği şekilde yapılandırıldığından emin olun. Git **ayarları** seçip [ **olay sıralama**](stream-analytics-out-of-order-and-late-events.md). İlke *değil* kullandığınızda uygulanan **Test** düğmesi sorguyu test edin. Test işi üretimde çalışan ve tarayıcı arasındaki tek fark sonucudur. 

5. Denetim ve tanılama günlüklerini kullanarak hata ayıklama:
    - Kullanım [denetim günlüklerini](../azure-resource-manager/resource-group-audit.md), hatalarını ayıklamanıza ve tanımlamak için filtreleyin.
    - Kullanım [iş tanılama günlükleri](stream-analytics-job-diagnostic-logs.md) tanımlamak ve hata ayıklama hataları için.

## <a name="job-is-consuming-too-many-streaming-units"></a>İş, çok fazla akış birimi tüketiyor
Azure Stream Analytics'te paralelleştirme yararlanmak emin olun. Bilgi edinebilirsiniz [sorgunun paralelleştirme ile ölçek](stream-analytics-parallelization.md) giriş bölümler yapılandırarak ve ayarlama Stream Analytics işlerinin tanımı analytics sorgu.

## <a name="debug-queries-progressively"></a>Sorguları aşamalı olarak hata ayıklama

Gerçek zamanlı veri işleme verileri sorgu ortasında nasıl göründüğünü bilmek yararlı olabilir. Giriş veya bir Azure Stream Analytics işi adımları birden çok kez okunduğu için ek SELECT INTO deyimleri yazabilirsiniz. Bunun yapılması ve Ara verileri depolama alanına, veri doğruluğunu gibi incelemenize olanak tanır *değişkenleri izleyin* yapmak için hata ayıklaması yaptığınızda bir program.

Azure Stream Analytics işi aşağıdaki örnek sorguda bir akış girişi, iki başvuru verisi girişleri ve Azure tablo depolama için bir çıktı sahiptir. Sorgu adı ve kategori bilgilerini almak için iki başvuru BLOB'ları ve olay hub'ı verileri birleştirir:

![Stream Analytics SELECT INTO örnek sorgu](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

İş çalışıyor, ancak çıkış olay üretilen unutmayın. Üzerinde **izleme** kutucuk, giriş, veri üretir, ancak hangi adımında tanımadığınız görürsünüz, burada gösterilen **katılın** tüm olayları bırakılmasına neden oldu.

![Stream Analytics izleme kutucuğu](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)
 
Bu durumda, "Ara sonuçları birleştirme ve girdiden okunan verileri günlüğe kaydetmek için" birkaç ek SELECT INTO deyimleri ekleyebilirsiniz.

Bu örnekte, iki yeni "geçici çıktı." ekledik İstediğiniz herhangi bir havuza olabilirler. Burada Azure depolama örnek olarak kullanırız:

![Ek SELECT INTO deyimleri için Stream Analytics sorgusu ekleme](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Ardından, böyle bir sorgu yazabilirsiniz:

![Yeniden seçin içine Stream Analytics sorgusu](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Artık işi yeniden başlatın ve birkaç dakikalığına çalışmasına olanak tanır. Ardından sorgu temp1 ve temp2 Gezgini'yle Visual Studio bulut aşağıdaki tablolar oluşturmak için:

**temp1 tablo**
![temp1 tabloda Stream Analytics sorgu SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**temp2 tablo**
![temp2 tabloda Stream Analytics sorgu SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Gördüğünüz gibi temp1 ve temp2 veriniz varsa ve Ad sütununda doğru temp2 doldurulur. Ancak, olduğundan hala veri çıkışında, bir şey yanlış:

![SELECT INTO output1 tabloyla veri Stream Analytics sorgu yok](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Verileri yeniden örnekleyerek, sorunu ikinci birleşim ile neredeyse emin olabilirsiniz. Başvuru verileri blob indirmek ve bir göz atalım:

![Başvuru tablo Stream Analytics sorgu SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Gördüğünüz gibi bu başvuru verilerinde GUID'sinin biçimi biçiminden farklıdır [sütundan] temp2. İşte bu verileri içinde output1 beklendiği gibi geldiğinde kaydetmedi.

Veri biçimi düzeltin, bunu blob başvurusu ve yeniden deneyin:

![SELECT INTO geçici tablo Stream Analytics sorgusu](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Bu kez, çıktı verileri biçimlendirilmiş ve beklendiği şekilde doldurulur.

![Tablonun son halini Stream Analytics sorgu SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Yardım alın

Daha fazla yardım için deneyin bizim [Azure Stream Analytics forumumuzu](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Stream analytics'e giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Akış Analizi Sorgu Dili Başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](https://msdn.microsoft.com/library/azure/dn835031.aspx)