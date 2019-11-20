---
title: Verileri Azure Veri Gezgini almak için akış alımı kullanın
description: Akış alma kullanarak verileri Azure Veri Gezgini 'a alma (yükleme) hakkında bilgi edinin.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 712273ddfb8b6f781627e2cc7915a1f538f57b4d
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090623"
---
# <a name="streaming-ingestion-preview"></a>Akış alma (Önizleme)

Akış alımı, değişen birim verileri için 10 saniyeden daha az bir alım süresiyle düşük gecikme süresi gerektiren senaryolara yöneliktir. Bir veya daha fazla veritabanında, her tabloya veri akışının görece küçük (saniyede birkaç kayıt) olduğu, ancak genel veri alma birimi yüksek (saniyede binlerce kayıt) olduğu bir veya daha fazla veritabanında işlemsel işleme işleminin en iyi duruma getirilmesi için kullanılır.

Veri miktarı her tablo için saniyede 1 MB 'tan fazla büyüdüğünde akış alımı yerine klasik (toplu) alımı kullanın. Çeşitli alma yöntemleri hakkında daha fazla bilgi edinmek için [veri alımı genel bakış](/azure/data-explorer/ingest-data-overview) makalesini okuyun.

> [!NOTE]
> Akış alımı aşağıdaki özellikleri desteklemez:
> * [Veritabanı imleçleri](/azure/kusto/management/databasecursor).
> * [Veri eşleme](/azure/kusto/management/mappings). Yalnızca [önceden oluşturulmuş](/azure/kusto/management/tables#create-ingestion-mapping) veri eşleme destekleniyor. 

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.
* [Web Kullanıcı arabiriminde](https://dataexplorer.azure.com/)oturum açın.
* [Azure Veri Gezgini kümesi ve veritabanı](create-cluster-database-portal.md) oluşturma

## <a name="enable-streaming-ingestion-on-your-cluster"></a>Kümenizde akış alımını etkinleştirme

1. Azure portal Azure Veri Gezgini kümenize gidin. **Ayarlar**' da, **Konfigürasyonlar**' ı seçin. 
1. **Yapılandırma** bölmesinde, **akış**alımı 'nı etkinleştirmek için **Açık** ' ı seçin.
1. **Kaydet**’i seçin.
 
    ![akış alımı](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. [Web Kullanıcı arabiriminde](https://dataexplorer.azure.com/), veri akışı verileri alacak tablo (ler) veya veritabanları üzerinde [akış alma ilkesi](/azure/kusto/concepts/streamingingestionpolicy) tanımlayın. 

    > [!NOTE]
    > * İlke veritabanı düzeyinde tanımlanmışsa, veritabanındaki tüm tablolar akış alımı için etkinleştirilir.
    > * Uygulanan ilke, veritabanındaki diğer tablolara değil, yalnızca yeni alınan verilere başvurabilir.

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>Verileri kümenize almak için akış alımı kullanın

Desteklenen iki akış alma türü vardır:

* Veri kaynağı olarak kullanılan [Olay Hub 'ı](/azure/data-explorer/ingest-data-event-hub)
* Özel Alım, Azure Veri Gezgini istemci kitaplıklarından birini kullanan bir uygulama yazmanızı gerektirir. Örnek bir uygulama için bkz. [akış alma örneği](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) .

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>Uygun akış alma türünü seçin

|   |Olay Hub'ı  |Özel Alım  |
|---------|---------|---------|
|Sorgu başlatma ve sorgu için kullanılabilen veriler arasındaki veri gecikmesi   |    daha uzun gecikme     |   daha kısa gecikme      |
|Geliştirme ek yükü    |   hızlı ve kolay kurulum, geliştirme yükü yok    |   uygulamanın hataları işlemesi ve veri tutarlılığı sağlamak için yüksek geliştirme yükü     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>Kümenizde akış alımını devre dışı bırakma

> [!WARNING]
> Akış alımını devre dışı bırakmak birkaç saat sürebilir.

1. Tüm ilgili tablo ve veritabanlarından akış alma giriş [ilkesini](/azure/kusto/concepts/streamingingestionpolicy) bırakın. Akış alma ilkesi kaldırma işlemi, ilk depolamadan, sütun deposundaki (kapsamlar veya parçalar) kalıcı depolamaya giriş verilerini taşımayı tetikler. İlk depolama alanındaki veri miktarına ve kümenin CPU ve bellek kullanımına bağlı olarak, veri taşıma en son birkaç saniye ile birkaç saat arasında sürebilir.
1. Azure portal Azure Veri Gezgini kümenize gidin. **Ayarlar**' da, **Konfigürasyonlar**' ı seçin. 
1. **Yapılandırma** bölmesinde, **akış alımını**devre dışı bırakmak için **kapalı** ' yı seçin.
1. **Kaydet**’i seçin.

    ![Akış alma kapalı](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>Sınırlamalar

* Artan VM ve küme boyutları ile akış alma performansı ve kapasitesi ölçeklenir. Tek bir D14 düğümü için, önerilen yük saniyede 150 ' e kadar istek olur.
* Şu anda destek yalnızca 8 ve 16 çekirdek SKU 'Larına yöneliktir (D13, D14, L8 ve L16).
* Alma isteği başına veri boyutu sınırlaması 4 MB 'tır.
* Tablo ve giriş eşlemelerinin oluşturulması ve değiştirilmesi gibi şema güncelleştirmeleri, akış alma hizmeti için 5 dakikaya kadar sürebilir.
* Veri akışı aracılığıyla yapılmasa bile, küme üzerinde akış alımı etkinleştirme, verileri akışa alma için küme makinelerinin yerel SSD disk bir kısmını kullanır ve etkin önbellek için kullanılabilir depolama alanını azaltır.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Veri Gezgini 'de verileri sorgulama](web-query-data.md)
