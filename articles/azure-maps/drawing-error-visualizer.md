---
title: Azure haritalar çizim hatası Görselleştiricisini Kullanma
description: Bu makalede, Oluşturucu (Önizleme) dönüştürme API 'SI tarafından döndürülen uyarıları ve hataları görselleştirmeyi öğreneceksiniz.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: db88e347e12783205ea8c31fed0bb374fccb4736
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96903590"
---
# <a name="using-the-azure-maps-drawing-error-visualizer-with-creator-preview"></a>Azure haritalar 'ı Oluşturucu (Önizleme) ile çizim hatası Görselleştiricisini Kullanma

> [!IMPORTANT]
> Azure haritalar Creator Hizmetleri şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Çizim hatası görselleştiricisi çizim [paketi uyarılarını ve](drawing-conversion-error-codes.md) dönüştürme işlemi sırasında algılanan hataları görüntüleyen tek başına bir Web uygulamasıdır. Hata görselleştiricisi Web uygulaması, internet 'e bağlanmadan kullanabileceğiniz bir statik sayfadan oluşur.  Hata Görselleştiriciyi, [Çizim paketi gereksinimlerine](drawing-requirements.md)uygun olarak hataları ve uyarıları onarmak için kullanabilirsiniz. [Azure haritalar dönüştürme API 'si](/rest/api/maps/conversion) yalnızca bir hata algılandığında yalnızca hata görselleştiricisi bağlantısı olan bir yanıt döndürür.

## <a name="prerequisites"></a>Önkoşullar

Çizim hatası Görselleştiricisini indirebilmeniz için önce şunları yapmanız gerekir:

1. [Azure Haritalar hesabı oluşturma](quick-demo-map-app.md#create-an-azure-maps-account)
2. Birincil anahtar veya abonelik anahtarı olarak da bilinen [birincil bir abonelik anahtarı alın](quick-demo-map-app.md#get-the-primary-key-for-your-account).
3. [Oluşturucu (Önizleme) kaynağı oluşturma](how-to-manage-creator.md)

Bu öğretici [Postman](https://www.postman.com/) uygulamasını kullanır, ancak farklı bir API geliştirme ortamı seçebilirsiniz.

## <a name="download"></a>İndir

1. Karşıya yüklenen paket için, çizim paketinizi Azure Maps Oluşturucu hizmeti 'ne (Önizleme) yükleyin `udid` . Bir paketi karşıya yükleme adımları için bkz. [bir çizim paketini karşıya yükleme](tutorial-creator-indoor-maps.md#upload-a-drawing-package).

2. Artık çizim paketi karşıya yüklendikten sonra, `udid` paketi harita verilerine dönüştürmek için karşıya yüklenen paket için kullanacağız. Bir paketi dönüştürme adımları için bkz. [bir çizim paketini dönüştürme](tutorial-creator-indoor-maps.md#convert-a-drawing-package).

    >[!NOTE]
    >Dönüştürme işleminiz başarılı olursa, hata görselleştiricisi aracına bir bağlantı almazsınız.

3. Postman uygulamasındaki yanıt **üstbilgileri** sekmesinde, `diagnosticPackageLocation` dönüştürme API 'si tarafından döndürülen özelliği arayın. Yanıt aşağıdaki JSON gibi görünmelidir:

    ```json
    {
        "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Failed",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {
            "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
        }
    }
    ```

4. URL 'de bir istek yaparak çizim paketi hata Görselleştiricisini indirin `HTTP-GET` `diagnosticPackageLocation` .

## <a name="setup"></a>Kurulum

Bağlantıdan indirilen sıkıştırılmış paketin içinde `diagnosticPackageLocation` iki dosya bulacaksınız.

* _VisualizationTool.zip_: çizim hatası görselleştiricisi için kaynak kodunu, medyayı ve Web sayfasını içerir.
* _ConversionWarningsAndErrors.js_:: çizim hatası görselleştiricisi tarafından kullanılan, uyarı, hata ve ek ayrıntıların bir listesini içerir.

_VisualizationTool.zip_ klasörünü açın. Aşağıdaki öğeleri içerir:

* _varlıklar_ klasörü: görüntüleri ve medya dosyalarını içerir
* _statik_ klasör: kaynak kodu
* _index.html_ dosyası: Web uygulaması.

_index.html_ dosyasını, ilgili sürüm numarasıyla aşağıdaki tarayıcılardan birini kullanarak açın. Sürüm, listelenen sürümde eşit olarak uyumlu davranışlar sunuyorsa farklı bir sürüm kullanabilirsiniz.

* Microsoft Edge 80
* Safari 13
* Chrome 80
* Firefox 74

## <a name="using-the-drawing-error-visualizer-tool"></a>Çizim hatası görselleştiricisi aracını kullanma

Çizim hatası görselleştiricisi aracını başlattıktan sonra karşıya yükleme sayfası görüntülenir. Karşıya yükleme sayfası bir sürükle ve bırak kutusu içerir. Sürükleme & bırakma kutusu ayrıca bir dosya Gezgini iletişim kutusu başlatan düğme olarak çalışır.

:::image type="content" source="./media/drawing-errors-visualizer/start-page.png" alt-text="Hata görselleştiricisi uygulaması çizme-başlangıç sayfası":::

Dosyadaki  _ConversionWarningsAndErrors.js_ indirilen dizinin köküne yerleştirildi. _ConversionWarningsAndErrors.js_ yüklemek için dosyayı sürükleyerek & sürükleyip kutuya bırakabilir veya kutuya tıklayabilir, dosya Gezgini iletişim kutusunda dosyayı bulabilir ve sonra dosyayı karşıya yükleyebilirsiniz.

:::image type="content" source="./media/drawing-errors-visualizer/loading-data.gif" alt-text="Hata görselleştiricisi uygulaması çizme-verileri yüklemek için sürükle ve bırak":::

Dosya _ConversionWarningsAndErrors.js_ yüklendikten sonra, çizim paketinizin hatalarının ve uyarılarının bir listesini görürsünüz. Her hata veya uyarı katman, düzey ve ayrıntılı bir ileti tarafından belirtilir. Bir hata veya uyarı hakkındaki ayrıntılı bilgileri görüntülemek için **Ayrıntılar** bağlantısına tıklayın. Daha sonra listenin altında bir ıntractable bölümü görüntülenir. Artık hatanın nasıl çözüleceği hakkında daha fazla bilgi edinmek için her bir hataya gidebilirsiniz.

:::image type="content" source="./media/drawing-errors-visualizer/errors.png" alt-text="Hata görselleştiricisi uygulaması çizme-hatalar ve uyarılar":::

## <a name="next-steps"></a>Sonraki adımlar

[Çizim paketiniz gereksinimleri karşıladıktan](drawing-requirements.md)sonra, çizim paketini bir veri kümesine dönüştürmek Için [Azure Maps veri kümesi hizmetini](/rest/api/maps/conversion) kullanabilirsiniz. Daha sonra, uygulamanızı geliştirmek için ınkapıharitaları Web modülünü kullanabilirsiniz. Aşağıdaki makaleleri okuyarak daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Çizim dönüştürme hata kodları](drawing-conversion-error-codes.md)

> [!div class="nextstepaction"]
> [Inkapı haritaları için Oluşturucu (Önizleme)](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Inkapıharitaları modülünü kullanma](how-to-use-indoor-module.md)

> [!div class="nextstepaction"]
> [Inkapısı eşlemesi dinamik stil uygulama](indoor-map-dynamic-styling.md)