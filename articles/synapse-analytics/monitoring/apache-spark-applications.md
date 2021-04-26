---
title: SYNAPSE Studio 'Yu kullanarak Apache Spark uygulamaları izleme
description: Apache Spark uygulamalarınızı izlemek için SYNAPSE Studio kullanın.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: ea08c82ed5772be7e3a6094f5477c4450975c1fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775902"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Apache Spark uygulamalarınızı izlemek için SYNAPSE Studio 'Yu kullanma

Azure SYNAPSE Analytics ile, çalışma alanınızdaki Apache Spark Havuzlarınızda not defterlerini, işleri ve diğer uygulama türlerini çalıştırmak için Apache Spark kullanabilirsiniz.

Bu makalede, Apache Spark uygulamalarınızın nasıl izleneceği açıklanmaktadır. Bu, en son durum, sorun ve ilerlemeyi göz önünde tutmanıza olanak sağlar.

Bu öğretici aşağıdaki görevleri kapsar:

* Apache Spark uygulamasını çalıştıran izleyici
* Tamamlanmış Apache Spark uygulamasını görüntüleme
* İptal edilen Apache Spark uygulamasını görüntüle
* Apache Spark uygulamada hata ayıklama başarısız oldu

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce aşağıdaki gereksinimlerin karşılandığından emin olun:

- Bir Synapse Studio çalışma alanı. Yönergeler için bkz. [SYNAPSE Studio çalışma alanı oluşturma](../../machine-learning/how-to-manage-workspace.md#create-a-workspace).

- Bir Apache Spark Havuzu.

## <a name="view-apache-spark-applications"></a>Apache Spark uygulamalarını görüntüleme 
Tüm Apache Spark uygulamalarını **izleyici**  ->  **Apache Spark uygulamalarından** görüntüleyebilirsiniz.
   ![Apache Spark uygulamaları](./media/how-to-monitor-spark-applications/apache-spark-applications.png)

## <a name="view-completed-apache-spark-application"></a>Tamamlanmış Apache Spark uygulamasını görüntüleme

**İzleyiciyi** açın ve **Apache Spark uygulamalar**' ı seçin. Tamamlanmış Apache Spark uygulamalarla ilgili ayrıntıları görüntülemek için Apache Spark uygulamasını seçin ve ayrıntıları görüntüleyin.

  ![tamamlanan işi seçin](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. **Tamamlanan görevleri**, **durumu** ve **toplam süreyi** denetleyin.

2. Günlük sorgusunu yenileyin.

3. **Spark geçmiş sunucusu**' na tıklayarak Apache Spark geçmiş sunucu bağlantısını açın.

4. **Özet** bilgisini denetleyin.

5. **Günlükleri** denetleyin. Açılan listeden farklı türde Günlükler seçebilirsiniz ve **günlükleri indir**' e tıklayarak günlük bilgilerini indirebilir ve gereken hataları ve uyarıları filtrelemek için **hataları ve uyarıları filtrele** onay kutusunu işaretleyebilirsiniz.

6. Oluşturulan iş grafiğinde işinize bir genel bakış görebilirsiniz. Varsayılan olarak, grafik tüm işleri gösterir. Bu görünümü **Iş kimliğine** göre filtreleyebilirsiniz.

7. Varsayılan olarak, **Ilerleme durumu** ekranı seçilidir.  /  /  / **Görüntüleme** açılır listesinde ilerleme okuma yazma **süresini** seçerek veri akışını kontrol edebilirsiniz.

8. İşi kayıttan yürütmek için, **kayıttan yürütme** düğmesine tıklayın. **Durdur düğmesine tıklayarak** istediğiniz zaman durdurabilirsiniz.

9. Fare kaydırma veya kaydırma çubuğunu kullanarak iş grafiğini yakınlaştırın ve uzaklaştırın, ekrana sığacak şekilde **yakınlaştırmak Için Yakınlaştır** ' ı da seçebilirsiniz.

10. İş grafiği düğümü her aşama için aşağıdaki bilgileri görüntüler:

    * Numarasını.

    * Ad veya açıklama.

    * Toplam görev numarası.

    * Okunan veriler: giriş boyutu ve karışık okuma boyutu toplamı.

    * Veri yazma: çıkış boyutunun ve karışık yazma boyutunun toplamı.

    * Yürütme Süresi: son denemede ilk deneme ve tamamlanma zamanının başlangıç saati arasındaki süre.

    * Satır sayısı: giriş kayıtlarının toplamı, çıkış kayıtları, okuma kayıtlarını karıştırma ve yazma kayıtlarını karıştırma.

    * Lemesine.

     ![tamamlanan işi görüntüle](./media/how-to-monitor-spark-applications/view-completed-job.png)
    
11. Grafikteki **Ayrıntıları görüntüle** ' ye tıklayın, sonra da aşama için ayrıntılar gösterilir.

    ![aşama ayrıntıları](./media/how-to-monitor-spark-applications/details-for-stage.png)


## <a name="monitor-running-apache-spark-application"></a>Apache Spark uygulamasını çalıştıran izleyici

**İzleyiciyi** açın ve **Apache Spark uygulamalar**' ı seçin. Çalıştıran Apache Spark uygulamalarla ilgili ayrıntıları görüntülemek için Apache Spark uygulamayı gönderme ve ayrıntıları görüntüleme seçeneğini belirleyin. Apache Spark uygulama hala çalışıyorsa, ilerlemeyi izleyebilirsiniz.

   ![çalışan işi seçin](./media/how-to-monitor-spark-applications/select-running-job.png)

1. **Tamamlanan görevleri**, **durumu** ve **toplam süreyi** denetleyin.

2. Apache Spark uygulamasını **Iptal edin** .

3. **Yenile** Günlük sorgusu.

4. Spark Iş sayfasına gitmek için **Spark Kullanıcı arabirimi** düğmesine tıklayın.

5. Grafiği görüntüleyin. Oluşturulan iş grafiğinde işinize bir genel bakış görebilirsiniz. Adım 6, 7, 8, 9, [tamamlanmış Apache Spark uygulamasının](#view-completed-apache-spark-application)10. adımına bakın.

6. **Özet** bilgisini denetleyin.

7. **Tanılama** sekmesinde tanılamayı denetleyin.

8. Bu sekmedeki **günlükleri** kontrol edin. Açılan listeden farklı türde Günlükler seçebilirsiniz ve **günlükleri indir**' e tıklayarak günlük bilgilerini indirebilir ve gereken hataları ve uyarıları filtrelemek için **hataları ve uyarıları filtrele** onay kutusunu işaretleyebilirsiniz.

    ![çalışan işi görüntüle](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-canceled-apache-spark-application"></a>İptal edilen Apache Spark uygulamasını görüntüle

**İzleyiciyi** açın ve **Apache Spark uygulamalar**' ı seçin. İptal edilen Apache Spark uygulamalarla ilgili ayrıntıları görüntülemek için Apache Spark uygulamayı seçin ve ayrıntıları görüntüleyin.

 ![iptal edilen işi seçin](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. **Tamamlanan görevleri**, **durumu** ve **toplam süreyi** denetleyin.

2. Günlük sorgusunu yenileyin.

3. **Spark geçmiş sunucusu**' na tıklayarak Apache geçmiş sunucusu bağlantısını açın.

4. Grafiği görüntüleyin. Oluşturulan iş grafiğinde işinize bir genel bakış görebilirsiniz. Adım 6, 7, 8, 9, [tamamlanmış Apache Spark uygulamasının](#view-completed-apache-spark-application)10. adımına bakın.

5. **Özet** bilgisini denetleyin.

6. **Günlükleri** denetleyin. Açılan listeden farklı türde Günlükler seçebilirsiniz ve günlükleri **İndir** ' e tıklayarak günlük bilgilerini indirebilir ve gereken hataları ve uyarıları filtrelemek için **hataları ve uyarıları filtrele** onay kutusunu işaretleyebilirsiniz.

7. Grafikteki **Ayrıntıları görüntüle** ' ye tıklayın, sonra da aşama için ayrıntılar gösterilir.

   ![iptal edilen işi görüntüle](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>Apache Spark uygulamada hata ayıklama başarısız oldu

**İzleyiciyi** açın ve **Apache Spark uygulamalar**' ı seçin. Başarısız Apache Spark uygulamalarla ilgili ayrıntıları görüntülemek için Apache Spark uygulamayı seçip ayrıntıları görüntüleyin.

![başarısız iş seçin](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. **Tamamlanan görevleri**, **durumu** ve **toplam süreyi** denetleyin.

2. Günlük sorgusunu yenileyin.

3. **Spark geçmiş sunucusu**' na tıklayarak Apache Spark geçmiş sunucu bağlantısını açın.

4. Grafiği görüntüleyin. Oluşturulan iş grafiğinde işinize bir genel bakış görebilirsiniz. Adım 6, 7, 8, 9, [tamamlanan Apache Spark uygulamasının](#view-completed-apache-spark-application) 10. adımına bakın

5. **Özet** bilgisini denetleyin.

6. Hata bilgilerini denetleyin.

   ![başarısız iş bilgileri](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="compare-apache-spark-applications"></a>Apache Spark uygulamalarını karşılaştırın

Uygulamaları karşılaştırmak için iki yol vardır. Bir **karşılaştırma uygulaması** seçerek karşılaştırabilir veya Not defteri 'nde görüntülemek için **Not defterine Karşılaştır** düğmesini tıklayabilirsiniz.

### <a name="compare-by-choose-an-application"></a>Uygulama seçerek karşılaştırın

**Uygulamaları Karşılaştır** düğmesine tıklayın ve performansı karşılaştırmak için bir uygulama seçin, iki uygulama arasındaki farkı görebilirsiniz.

![uygulamaları karşılaştırın](./media/how-to-monitor-spark-applications/compare-applications.png)

![Ayrıntılar uygulamaları Karşılaştır](./media/how-to-monitor-spark-applications/details-compare-applications.png)

1. Fareyi kullanarak bir uygulamaya gelin ve ardından **uygulamaları Karşılaştır** simgesi görüntülenir.

2. **Uygulamaları Karşılaştır** simgesine tıklayın ve uygulamaları Karşılaştır sayfası açılır.

3. **Karşılaştırma uygulaması Seç** sayfasını açmak Için **Uygulama Seç** düğmesine tıklayın.

4. Karşılaştırma uygulamasını seçerken, uygulama URL 'sini girmeniz veya yinelenen listeden seçim yapmanız gerekir. Ardından **Tamam** düğmesine tıklayın. 

   ![karşılaştırma uygulaması seçin](./media/how-to-monitor-spark-applications/choose-comparison-application.png)

5. Karşılaştırma sonucu, uygulamaları Karşılaştır sayfasında görüntülenir.

   ![karşılaştırma sonucu](./media/how-to-monitor-spark-applications/comparison-result.png)

### <a name="compare-by-compare-in-notebook"></a>Not defterindeki karşılaştırmaya göre karşılaştır

**Uygulamayı Karşılaştır** sayfasındaki not defterinde **Karşılaştır** düğmesine tıklayarak Not defterini açın. . İpynb dosyasının varsayılan adı **yinelenen uygulama analizinden** oluşur.

![Not defterinde Karşılaştır](./media/how-to-monitor-spark-applications/compare-in-notebook.png)

Not Defteri: yinelenen uygulama analizi dosyasında, Spark havuzunu ve dilini ayarladıktan sonra doğrudan çalıştırabilirsiniz.

![Yinelenen uygulama analizi](./media/how-to-monitor-spark-applications/recurrent-application-analytics.png)

## <a name="next-steps"></a>Sonraki adımlar

İşlem hattı çalıştırmalarını izleme hakkında daha fazla bilgi için bkz. [SYNAPSE Studio kullanarak işlem hattı çalıştırmalarını izleme](how-to-monitor-pipeline-runs.md) .
