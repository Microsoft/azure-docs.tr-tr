---
title: Azure Application Insights Stream Analytics kullanarak dışarı aktarma | Microsoft Docs
description: Stream Analytics, Application Insights dışarı aktarma yaptığınız verileri sürekli olarak dönüştürebilir, filtreleyebilir ve yönlendirebilir.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 01/08/2019
ms.openlocfilehash: 3be1a643cbe942c0b740ae8ebcc2c7f2dda24854
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677945"
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Application Insights gelen verileri işlemek için Stream Analytics kullanma
[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) , [Application Insights aktarılmış](export-telemetry.md)verileri işlemeye yönelik ideal bir araçtır. Stream Analytics, çeşitli kaynaklardan veri çekebilir. Verileri dönüştürebilir ve filtreleyebilir ve sonra çeşitli alıcılara yönlendirebilir.

Bu örnekte, Application Insights veri alan bir bağdaştırıcı oluşturacağız, bazı alanları yeniden adlandırdığından ve işleyerek ve Power BI yönelttireceğiz.

> [!WARNING]
> [Power BI Application Insights verileri görüntülemenin](../../azure-monitor/app/export-power-bi.md )çok daha iyi ve kolay bir yolu vardır. Burada gösterilen yol, yalnızca bir örnek olarak, aktarılmış verilerin nasıl işlenmesi gerektiğini gösterir.
> 
> 

![SA 'dan PBı 'a dışarı aktarma için blok diyagramı](./media/export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Azure 'da depolama oluşturma
Sürekli dışarı aktarma, verileri her zaman bir Azure depolama hesabına çıkarır, bu nedenle önce depolama alanını oluşturmanız gerekir.

1. [Azure Portal](https://portal.azure.com)aboneliğinizde bir "klasik" depolama hesabı oluşturun.
   
   ![Azure portal ' de yeni, veri, depolama ' yı seçin.](./media/export-stream-analytics/030.png)
2. Bir kapsayıcı oluşturma
   
    ![Yeni depolama alanında kapsayıcılar ' ı seçin, kapsayıcılar kutucuğuna tıklayın ve ardından Ekle](./media/export-stream-analytics/040.png)
3. Depolama erişim anahtarını kopyalama
   
    Girişi Stream Analytics hizmetine ayarlamanız yakında gerekecektir.
   
    ![Depolama alanında ayarlar, anahtarlar ' ı açın ve birincil erişim anahtarının bir kopyasını alın](./media/export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Azure depolama 'ya sürekli dışarı aktarmayı Başlat
[Sürekli dışarı aktarma](export-telemetry.md) , verileri Application Insights 'den Azure Storage 'a taşılar.

1. Azure portal, uygulamanız için oluşturduğunuz Application Insights kaynağına gidin.
   
    ![Uygulamanıza gözatıp Application Insights seçin](./media/export-stream-analytics/050.png)
2. Sürekli dışarı aktarma oluşturun.
   
    ![Ayarları seçin, sürekli dışarı aktarma, Ekle](./media/export-stream-analytics/060.png)

    Daha önce oluşturduğunuz depolama hesabını seçin:

    ![Dışarı aktarma hedefini ayarla](./media/export-stream-analytics/070.png)

    Görmek istediğiniz olay türlerini ayarlayın:

    ![Olay türlerini seçin](./media/export-stream-analytics/080.png)

1. Bazı verilerin birikmesini sağlar. Geri gelin ve başkalarının uygulamanızı bir süre içinde kullanmasına izin verin. Telemetri ' de gelir ve [Ölçüm Gezgini](../../azure-monitor/app/metrics-explorer.md) 'nde istatistiksel grafikler ve [Tanılama aramasında](../../azure-monitor/app/diagnostic-search.md)tek tek olaylar görürsünüz. 
   
    Ayrıca, veriler depolama verilerinize dışarı aktaracaktır. 
2. Aktarılmış verileri inceleyin. Visual Studio 'da **Görünüm/bulut Gezgini**' ni seçin ve Azure/Storage ' ı açın. (Bu menü seçeneğine sahip değilseniz, Azure SDK 'sını yüklemeniz gerekir: yeni proje iletişim kutusunu açın ve Visual C# /Cloud/Get .NET için Microsoft Azure SDK açın.)
   
    ![](./media/export-stream-analytics/04-data.png)
   
    Yol adının, uygulama adı ve izleme anahtarından türetilen ortak bölümünü bir yere göz önünde bir şekilde oluşturun. 

Olaylar JSON biçimindeki blob dosyalarına yazılır. Her dosya bir veya daha fazla olay içerebilir. Bu nedenle, olay verilerini okumak ve istediğimiz alanları filtrelemek istiyoruz. Verilerle yaptığımız her türlü şey vardır, ancak şu anda planımız, verileri Power BI için boru yapmak üzere Stream Analytics kullanmaktır.

## <a name="create-an-azure-stream-analytics-instance"></a>Azure Stream Analytics örneği oluşturma
[Azure Portal](https://portal.azure.com/), Azure Stream Analytics hizmetini seçin ve yeni bir Stream Analytics işi oluşturun:

![](./media/export-stream-analytics/SA001.png)

![](./media/export-stream-analytics/SA002.png)

Yeni iş oluşturulduğunda **Kaynağa Git**' i seçin.

![](./media/export-stream-analytics/SA003.png)

### <a name="add-a-new-input"></a>Yeni giriş Ekle

![](./media/export-stream-analytics/SA004.png)

Sürekli dışarı aktarma blobundan giriş alacak şekilde ayarlayın:

![](./media/export-stream-analytics/SA0005.png)

Artık, daha önce not ettiğiniz depolama hesabınızdan birincil erişim anahtarı gerekir. Bunu depolama hesabı anahtarı olarak ayarlayın.

### <a name="set-path-prefix-pattern"></a>Yol ön eki modelini ayarla

**Tarih biçimini YYYY-AA-GG (tireler ile) olarak ayarladığınızdan emin olun.**

Yol ön eki deseninin, Stream Analytics depolama alanındaki giriş dosyalarını nerede bulacağını belirtir. Sürekli dışarı aktarmanın verileri nasıl depoladığını karşılayacak şekilde ayarlamanız gerekir. Bunu şöyle ayarlayın:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

Bu örnekte:

* `webapplication27`, Application Insights kaynağın adı **tüm küçük bir durumdur**.
* `1234...`, Application Insights kaynağın izleme anahtarıdır ve **tireler**yok edilir. 
* `PageViews` çözümlemek istediğiniz veri türüdür. Kullanılabilir türler, sürekli dışarı aktarma sırasında ayarladığınız filtreye bağlıdır. Dışarı aktarılmış verileri inceleyerek diğer kullanılabilir türleri görüntüleyin ve [veri aktarma modelini](export-data-model.md)görüntüleyin.
* `/{date}/{time}`, harfine yazılmış bir modeldir.

> [!NOTE]
> Yolu doğru aldığınızdan emin olmak için depolamayı inceleyin.
> 

## <a name="add-new-output"></a>Yeni çıkış Ekle
Şimdi iş > **çıktıları**  > **Ekle**' yi seçin.

![](./media/export-stream-analytics/SA006.png)


![Yeni kanalı seçin, çıkış ' a tıklayın, Power BI ekleyin](./media/export-stream-analytics/SA010.png)

Power BI kaynağına erişmek üzere Stream Analytics yetkilendirmek için **iş veya okul hesabınızı** girin. Ardından, çıkış için bir ad ve hedef Power BI veri kümesi ve tablo için bir ad koyun.

## <a name="set-the-query"></a>Sorguyu ayarlama
Sorgu, girişten çıkışa çeviriyi yönetir.

Doğru çıktıyı almanızı denetlemek için test işlevini kullanın. Giriş sayfasından aldığınız örnek verileri verin. 

### <a name="query-to-display-counts-of-events"></a>Olay sayılarını görüntüleme sorgusu
Bu sorguyu Yapıştır:

```SQL

    SELECT
      flat.ArrayValue.name,
      count(*)
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.[event]) as flat
    GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* Export-Input, akış girişine verdiğimiz diğer addır
* PBI-Output, tanımladığımız çıkış diğer adıdır
* Olay adı, iç içe geçmiş bir JSON dizisinde olduğundan, [dış uygulama, GetElements](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) kullanıyoruz. Ardından Select, zaman döneminde bu adı taşıyan örneklerin sayısı ile birlikte olay adını seçer. [Group By](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) yan tümcesi, öğeleri bir dakikalık zaman dilimlerine göre gruplandırır.

### <a name="query-to-display-metric-values"></a>Ölçüm değerlerini görüntüleme sorgusu
```SQL

    SELECT
      A.context.data.eventtime,
      avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.context.custom.metrics) as flat
    GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime

``` 

* Bu sorgu, olay zamanını ve ölçüm değerini almak için ölçüm telemetrisine gider. Ölçüm değerleri bir dizi içindedir, bu nedenle, satırları ayıklamak için DıŞTAKI al Gements düzenlerini kullanırız. "myMetric", bu durumda ölçüm adıdır. 

### <a name="query-to-include-values-of-dimension-properties"></a>Boyut özelliklerinin değerlerini dahil etmek için sorgu
```SQL

    WITH flat AS (
    SELECT
      MySource.context.data.eventTime as eventTime,
      InstanceId = MyDimension.ArrayValue.InstanceId.value,
      BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
    FROM MySource
    OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
    )
    SELECT
     eventTime,
     InstanceId,
     BusinessUnitId
    INTO AIOutput
    FROM flat

```

* Bu sorgu, boyut dizisindeki sabit bir dizinde bulunan belirli bir boyuta bağlı kalmadan boyut özelliklerinin değerlerini içerir.

## <a name="run-the-job"></a>İşi çalıştırma
İşten başlamak için geçmişte bir tarih seçebilirsiniz. 

![İşi seçin ve sorgu ' ya tıklayın. Aşağıdaki örneği yapıştırın.](./media/export-stream-analytics/SA008.png)

İş çalışmaya başlamadan bekleyin.

## <a name="see-results-in-power-bi"></a>Sonuçlara bakın Power BI
> [!WARNING]
> [Power BI Application Insights verileri görüntülemenin](../../azure-monitor/app/export-power-bi.md )çok daha iyi ve kolay bir yolu vardır. Burada gösterilen yol, yalnızca bir örnek olarak, aktarılmış verilerin nasıl işlenmesi gerektiğini gösterir.
> 
> 

İş veya okul hesabınızla Power BI açın ve Stream Analytics işinin çıktısı olarak tanımladığınız veri kümesini ve tabloyu seçin.

![Power BI, veri kümenizi ve alanlarınızı seçin.](./media/export-stream-analytics/200.png)

Artık bu veri kümesini, [Power BI](https://powerbi.microsoft.com)raporlarında ve panolarda kullanabilirsiniz.

![Power BI, veri kümenizi ve alanlarınızı seçin.](./media/export-stream-analytics/210.png)

## <a name="no-data"></a>Veri yok mu?
* Tarih biçimini YYYY-AA-GG olarak (tireler ile) doğru olarak [ayarlayıp ayarlayamadığını](#set-path-prefix-pattern) kontrol edin.

## <a name="video"></a>Video
Nohar ben Zeev, Stream Analytics kullanarak dışarıya aktarılmış verilerin nasıl işlenmesi gerektiğini gösterir.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## <a name="next-steps"></a>Sonraki adımlar
* [Sürekli dışarı aktarma](export-telemetry.md)
* [Özellik türleri ve değerleri için ayrıntılı veri modeli başvurusu.](export-data-model.md)
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)

