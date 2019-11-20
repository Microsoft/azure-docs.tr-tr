---
title: Azure 'dan SQL 'e aktarma Application Insights | Microsoft Docs
description: Application Insights verileri sürekli olarak SQL 'e Stream Analytics kullanarak dışarı aktarın.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/11/2017
ms.openlocfilehash: 41efcbc7b70395302858638a9f44f3cbba27bf9a
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678262"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>İzlenecek yol: Application Insights Stream Analytics kullanarak SQL 'e aktarma
Bu makalede, [sürekli dışa aktarma][export] ve [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)kullanarak telemetri VERILERINIZI [Azure Application Insights][start] Azure SQL veritabanına nasıl taşıyacağınız gösterilmektedir. 

Sürekli dışarı aktarma, telemetri verilerinizi JSON biçiminde Azure depolama 'ya taşılar. Azure Stream Analytics kullanarak JSON nesnelerini ayrıştıracağız ve bir veritabanı tablosunda satır oluşturacak.

(Daha genel, sürekli dışarı aktarma, uygulamalarınızın Application Insights gönderdikleri Telemetriyi kendi analizinizi yapmanın yoludur. Bu kod örneğini, veri toplama gibi, içe aktarılmış telemetri ile başka şeyler yapmak üzere uyarlayabilirsiniz.)

İzlemek istediğiniz uygulamayı zaten sahip olduğunuz varsayımıyla başlayacağız.

Bu örnekte, sayfa görünümü verilerini kullanacağız, ancak aynı model özel olaylar ve özel durumlar gibi diğer veri türlerine kolayca genişletilebilir. 

## <a name="add-application-insights-to-your-application"></a>Uygulamanıza Application Insights ekleyin
Kullanmaya başlamak için:

1. [Web sayfalarınız için Application Insights ayarlayın](../../azure-monitor/app/javascript.md). 
   
    (Bu örnekte, istemci tarayıcılarından sayfa görünümü verilerini işlemeye odaklanacağız, ancak [Java](../../azure-monitor/app/java-get-started.md) veya [ASP.net](../../azure-monitor/app/asp-net.md) uygulamanızın sunucu tarafı ve işlem isteğiniz, bağımlılığı ve diğer sunucu telemetrisi için Application Insights de ayarlayabilirsiniz.)
2. Uygulamanızı yayımlayın ve Application Insights kaynağınız içinde görüntülenen telemetri verilerini izleyin.

## <a name="create-storage-in-azure"></a>Azure 'da depolama oluşturma
Sürekli dışarı aktarma, verileri her zaman bir Azure depolama hesabına çıkarır, bu nedenle önce depolama alanını oluşturmanız gerekir.

1. [Azure Portal][portal]aboneliğinizde bir depolama hesabı oluşturun.
   
    ![Azure portal yeni, veri, depolama ' yı seçin. Klasik ' i seçin, Oluştur ' u seçin. Depolama adı belirtin.](./media/code-sample-export-sql-stream-analytics/040-store.png)
2. Bir kapsayıcı oluşturma
   
    ![Yeni depolama alanında kapsayıcılar ' ı seçin, kapsayıcılar kutucuğuna tıklayın ve ardından Ekle](./media/code-sample-export-sql-stream-analytics/050-container.png)
3. Depolama erişim anahtarını kopyalama
   
    Girişi Stream Analytics hizmetine ayarlamanız yakında gerekecektir.
   
    ![Depolama alanında ayarlar, anahtarlar ' ı açın ve birincil erişim anahtarının bir kopyasını alın](./media/code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Azure depolama 'ya sürekli dışarı aktarmayı Başlat
1. Azure portal, uygulamanız için oluşturduğunuz Application Insights kaynağına gidin.
   
    ![Uygulamanıza gözatıp Application Insights seçin](./media/code-sample-export-sql-stream-analytics/060-browse.png)
2. Sürekli dışarı aktarma oluşturun.
   
    ![Ayarları seçin, sürekli dışarı aktarma, Ekle](./media/code-sample-export-sql-stream-analytics/070-export.png)

    Daha önce oluşturduğunuz depolama hesabını seçin:

    ![Dışarı aktarma hedefini ayarla](./media/code-sample-export-sql-stream-analytics/080-add.png)

    Görmek istediğiniz olay türlerini ayarlayın:

    ![Olay türlerini seçin](./media/code-sample-export-sql-stream-analytics/085-types.png)


1. Bazı verilerin birikmesini sağlar. Geri gelin ve başkalarının uygulamanızı bir süre içinde kullanmasına izin verin. Telemetri ' de gelir ve [Ölçüm Gezgini](../../azure-monitor/app/metrics-explorer.md) 'nde istatistiksel grafikler ve [Tanılama aramasında](../../azure-monitor/app/diagnostic-search.md)tek tek olaylar görürsünüz. 
   
    Ayrıca, veriler depolama verilerinize dışarı aktaracaktır. 
2. , Portalda, bu verileri inceleyin; **tarayıcı**' yı seçin, depolama hesabınızı seçin ve ardından veya Visual Studio 'da **kapsayıcılar** ' ı seçin. Visual Studio 'da **Görünüm/bulut Gezgini**' ni seçin ve Azure/Storage ' ı açın. (Bu menü seçeneğine sahip değilseniz, Azure SDK 'sını yüklemeniz gerekir: yeni proje iletişim kutusunu açın ve Visual C# /Cloud/Get .NET için Microsoft Azure SDK açın.)
   
    ![Visual Studio 'da sunucu tarayıcısı, Azure, depolama 'yı açın](./media/code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Yol adının, uygulama adı ve izleme anahtarından türetilen ortak bölümünü bir yere göz önünde bir şekilde oluşturun. 

Olaylar JSON biçimindeki blob dosyalarına yazılır. Her dosya bir veya daha fazla olay içerebilir. Bu nedenle, olay verilerini okumak ve istediğimiz alanları filtrelemek istiyoruz. Verilerle yaptığımız her türlü şey vardır, ancak bugün planımız, verileri bir SQL veritabanına taşımak için Stream Analytics kullanmaktır. Bu, çok sayıda ilginç sorgunun çalıştırılmasını kolaylaştırır.

## <a name="create-an-azure-sql-database"></a>Azure SQL veritabanı oluşturma
[Azure Portal][portal]' de aboneliğinizden başladıktan sonra, verileri yazacağınız veritabanını (ve yeni bir sunucuyu) oluşturun.

![Yeni, veri, SQL](./media/code-sample-export-sql-stream-analytics/090-sql.png)

Veritabanı sunucusunun Azure hizmetlerine erişime izin verdiğinden emin olun:

![Azure 'a, sunuculara, sunucunuza, ayarlarınıza, güvenlik duvarına erişime Izin verin](./media/code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-db"></a>Azure SQL DB 'de tablo oluşturma
Önceki bölümde oluşturulan ve tercih ettiğiniz yönetim araclarınızın veritabanına bağlanın. Bu izlenecek yolda [SQL Server Yönetim Araçları](https://msdn.microsoft.com/ms174173.aspx) (SSMS) kullanacağız.

![](./media/code-sample-export-sql-stream-analytics/31-sql-table.png)

Yeni bir sorgu oluşturun ve aşağıdaki T-SQL ' i yürütün:

```SQL

CREATE TABLE [dbo].[PageViewsTable](
    [pageName] [nvarchar](max) NOT NULL,
    [viewCount] [int] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlDataPort] [int] NULL,
    [urlDataprotocol] [nvarchar](50) NULL,
    [urlDataHost] [nvarchar](50) NULL,
    [urlDataBase] [nvarchar](50) NULL,
    [urlDataHashTag] [nvarchar](max) NULL,
    [eventTime] [datetime] NOT NULL,
    [isSynthetic] [nvarchar](50) NULL,
    [deviceId] [nvarchar](50) NULL,
    [deviceType] [nvarchar](50) NULL,
    [os] [nvarchar](50) NULL,
    [osVersion] [nvarchar](50) NULL,
    [locale] [nvarchar](50) NULL,
    [userAgent] [nvarchar](max) NULL,
    [browser] [nvarchar](50) NULL,
    [browserVersion] [nvarchar](50) NULL,
    [screenResolution] [nvarchar](50) NULL,
    [sessionId] [nvarchar](max) NULL,
    [sessionIsFirst] [nvarchar](50) NULL,
    [clientIp] [nvarchar](50) NULL,
    [continent] [nvarchar](50) NULL,
    [country] [nvarchar](50) NULL,
    [province] [nvarchar](50) NULL,
    [city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
    [eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![](./media/code-sample-export-sql-stream-analytics/34-create-table.png)

Bu örnekte, sayfa görünümlerindeki verileri kullanıyoruz. Mevcut diğer verileri görmek için JSON çıktlarınızı inceleyin ve [dışarı aktarma veri modelini](../../azure-monitor/app/export-data-model.md)görüntüleyin.

## <a name="create-an-azure-stream-analytics-instance"></a>Azure Stream Analytics örneği oluşturma
[Azure Portal](https://portal.azure.com/), Azure Stream Analytics hizmetini seçin ve yeni bir Stream Analytics işi oluşturun:

![Stream Analytics Ayarları](./media/code-sample-export-sql-stream-analytics/SA001.png)

![](./media/code-sample-export-sql-stream-analytics/SA002.png)

Yeni iş oluşturulduğunda **Kaynağa Git**' i seçin.

![Stream Analytics Ayarları](./media/code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>Yeni giriş Ekle

![Stream Analytics Ayarları](./media/code-sample-export-sql-stream-analytics/SA004.png)

Sürekli dışarı aktarma blobundan giriş alacak şekilde ayarlayın:

![Stream Analytics Ayarları](./media/code-sample-export-sql-stream-analytics/SA0005.png)

Artık, daha önce not ettiğiniz depolama hesabınızdan birincil erişim anahtarı gerekir. Bunu depolama hesabı anahtarı olarak ayarlayın.

#### <a name="set-path-prefix-pattern"></a>Yol ön eki modelini ayarla

**Tarih biçimini YYYY-AA-GG (tireler ile) olarak ayarladığınızdan emin olun.**

Yol ön eki, Stream Analytics depolama alanındaki giriş dosyalarını nasıl bulduğunu belirtir. Sürekli dışarı aktarmanın verileri nasıl depoladığını karşılayacak şekilde ayarlamanız gerekir. Bunu şöyle ayarlayın:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

Bu örnekte:

* `webapplication27`, Application Insights kaynağın adı, **hepsi küçük bir durumdur**. 
* `1234...`, Application Insights kaynağın ana **çizgiler kaldırılmış olan**izleme anahtarıdır. 
* `PageViews` çözümlemek istediğimiz veri türüdür. Kullanılabilir türler, sürekli dışarı aktarma sırasında ayarladığınız filtreye bağlıdır. Dışarı aktarılmış verileri inceleyerek diğer kullanılabilir türleri görüntüleyin ve [veri aktarma modelini](../../azure-monitor/app/export-data-model.md)görüntüleyin.
* `/{date}/{time}`, harfine yazılmış bir modeldir.

Application Insights kaynağınızın adını ve Ikey değerini almak için, Genel Bakış sayfasında temel bilgiler ' i açın veya ayarları açın.

> [!TIP]
> Giriş yolunu doğru olarak ayarlayıp ayarlayamadığını denetlemek için Sample işlevini kullanın. Başarısız olursa: seçtiğiniz örnek zaman aralığı için depolamada veri olup olmadığını denetleyin. Giriş tanımını düzenleyin ve depolama hesabı, yol öneki ve tarih biçimini doğru olarak ayarlayın.

 
## <a name="set-query"></a>Sorgu ayarla
Sorgu bölümünü açın:

Varsayılan sorguyu ile değiştirin:

```SQL

    SELECT flat.ArrayValue.name as pageName
    , flat.ArrayValue.count as viewCount
    , flat.ArrayValue.url as url
    , flat.ArrayValue.urlData.port as urlDataPort
    , flat.ArrayValue.urlData.protocol as urlDataprotocol
    , flat.ArrayValue.urlData.host as urlDataHost
    , flat.ArrayValue.urlData.base as urlDataBase
    , flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

İlk birkaç özellik sayfa görüntüleme verilerine özgü olduğuna dikkat edin. Diğer telemetri türlerinin dışarı aktarmaları farklı özelliklere sahip olur. [Özellik türleri ve değerleri için ayrıntılı veri modeli başvurusuna bakın.](../../azure-monitor/app/export-data-model.md)

## <a name="set-up-output-to-database"></a>Çıktıyı veritabanına ayarla
Çıkış olarak SQL ' i seçin.

![Stream Analytics 'te çıktılar ' i seçin.](./media/code-sample-export-sql-stream-analytics/SA006.png)

SQL veritabanını belirtin.

![Veritabanınızın ayrıntılarını girin](./media/code-sample-export-sql-stream-analytics/SA007.png)

Sihirbazı kapatın ve çıktının ayarlandığını belirten bir bildirim bekleyin.

## <a name="start-processing"></a>İşlemeye başla
Eylem çubuğundan işi başlatın:

![Stream Analytics ' te Başlat ' a tıklayın.](./media/code-sample-export-sql-stream-analytics/SA008.png)

Verilerin şimdi işlenmesine veya daha önceki verilerle başlatılmasına nasıl başlayameyeceğinizi seçebilirsiniz. İkinci bir süre içinde zaten çalışan sürekli dışa aktarma yaptıysanız yararlıdır.

Birkaç dakika sonra, SQL Server Yönetim Araçları sayfasına dönün ve içindeki verileri izleyin. Örneğin, şöyle bir sorgu kullanın:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## <a name="related-articles"></a>İlgili makaleler
* [Stream Analytics kullanarak PowerBI 'a aktarma](../../azure-monitor/app/export-power-bi.md )
* [Özellik türleri ve değerleri için ayrıntılı veri modeli başvurusu.](../../azure-monitor/app/export-data-model.md)
* [Application Insights 'da sürekli dışarı aktarma](../../azure-monitor/app/export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[export]: ../../azure-monitor/app/export-telemetry.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md

