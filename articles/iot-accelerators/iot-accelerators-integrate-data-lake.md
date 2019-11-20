---
title: Uzaktan Izlemenin Data Lake Store-Azure 'a veri akışı Microsoft Docs
description: Azure Stream Analytics işini kullanarak uzaktan Izleme çözümünü Azure Data Lake Store ile tümleştirmeyi öğrenin.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 0a684151e01b298c60ff17ef1470e0648a425850
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889247"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>Uzaktan Izleme çözümünü Azure Data Lake Store ile tümleştirme

Uzaktan Izleme çözümünde sunulmadan daha fazla gelişmiş analiz gereksinimlerine sahip olabilirsiniz. Azure Data Lake Store, büyük ve farklı veri kümelerinden veri depolayabildiğinden ve isteğe bağlı analiz sağlamak için Azure Data Lake Analytics ile tümleştirabileceğinden bu uygulama için idealdir.

Bu nasıl yapılır bölümünde, uzaktan Izleme çözümünüzdeki IoT Hub 'ından bir Azure Data Lake Store veri akışı için bir Azure Stream Analytics işi kullanacaksınız.

## <a name="prerequisites"></a>Ön koşullar

Bu nasıl yapılır hakkında daha fazla işlem için şunlar gerekir:

* [Uzaktan izleme çözüm Hızlandırıcısını dağıtın](quickstart-remote-monitoring-deploy.md).
  * Uzaktan Izleme çözümü, bu makalede kullanılan IoT Hub 'ı ve Azure Stream Analytics işini Azure aboneliğinize dağıtacaktır.
* [Azure Data Lake Store dağıtma](../data-lake-store/data-lake-store-get-started-portal.md)
  * Data Lake Store, uzaktan Izleme çözümünüz ile aynı bölgeye dağıtılmalıdır.
  * Hesabınızda "streaming" adlı [bir klasör oluşturun](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) .

## <a name="create-a-consumer-group"></a>Tüketici grubu oluşturma

Uzaktan Izleme çözümünüzün IoT Hub 'ında adanmış bir tüketici grubu oluşturun. Bu, Data Lake Store veri akışı için Stream Analytics işi tarafından kullanılır.

> [!NOTE]
> Tüketici grupları, uygulamalar tarafından Azure IoT Hub veri çekmek için kullanılır. Her beş çıkış tüketicisi için yeni bir tüketici grubu oluşturmanız gerekir. En çok 32 Tüketici grubu oluşturabilirsiniz.

1. Azure Portal’da oturum açın.

1. Azure portal **Cloud Shell** düğmesine tıklayın.

    ![Portal başlatma simgesi](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. Yeni bir tüketici grubu oluşturmak için bu komutu yürütün:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Uzaktan Izleme çözümünüzdeki kaynak grubunu ve IoT Hub adlarını kullanın.

## <a name="create-stream-analytics-job"></a>Stream Analytics Işi oluştur

IoT Hub 'ınızdaki verileri Azure Data Lake deponuza akışa almak için bir Azure Stream Analytics işi oluşturun.

1. **Kaynak oluştur ' a**tıklayın, marketten nesnelerin interneti seçin ve **Stream Analytics iş**' e tıklayın.

    ![Yeni Stream Analytics Işi](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Bir iş adı girin ve uygun aboneliği ve kaynak grubunu seçin.

1. Data Lake Store, yakın yerde veya aynı bölgede yer alan bir konum seçin. Burada Doğu ABD kullandık.

1. Barındırma ortamının varsayılan **bulut**olarak ayrılmadığınızdan emin olun.

1. **Oluştur**'a tıklayın.

    ![Stream Analytics Işi oluştur](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>Stream Analytics işini yapılandırma

1. Uzaktan Izleme çözümü kaynak grubunuzda **Stream Analytics işe** gidin.

1. Genel Bakış sayfasında **girişler**' e tıklayın.

    ![Genel Bakış sayfası](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. **Akış girişi Ekle** ' ye tıklayın ve açılan listeden **IoT Hub** seçin.

    ![Giriş Ekle](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. Yeni Giriş sekmesinde, **ıothub**Için bir giriş diğer adı girin.

1. Tüketici grubu açılır listesinden daha önce oluşturduğunuz tüketici grubunu seçin. Burada **streamanalyticsjob**kullandık.

    ![Giriş seçin](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. **Kaydet** düğmesine tıklayın.

1. Genel Bakış sayfasında, **çıktılar**' e tıklayın.

    ![Data Lake Store Ekle](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. **Ekle** ' ye tıklayın ve açılan listeden **Data Lake Store** ' yi seçin.

    ![Çıkış Ekle](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. Yeni çıkış sekmesinde, **DataLakeStore**Için bir çıkış diğer adı girin.

1. Önceki adımlarda oluşturduğunuz Data Lake Store hesabını seçin ve verileri depoya akışına yönelik klasör yapısını sağlayın.

1. Tarih biçimi alanına **/streaming/{Date}/{Time}** yazın. Varsayılan tarih biçimini YYYY/AA/GG ve saat biçimi SS olarak bırakın.

    ![Klasör yapısı sağla](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. **Yetkilendir**'e tıklayın.

    Stream Analytics işi için dosya sistemine yazma erişimi sağlamak üzere Data Lake Store ile yetkilendirmeniz gerekir.

    ![Stream Analytics Data Lake Store yetkilendirme](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    Açılır bir pencere görürsünüz ve açılan menü, yetkilendirmeyi kapatır onay kutusu, yetkilendirme tamamlandıktan sonra gri kalır.

    > [!NOTE]
    > Açılan pencerede bir hata görürseniz, ınbilito modunda yeni bir tarayıcı penceresi açın ve yeniden deneyin.

1. **Kaydet** düğmesine tıklayın.

## <a name="edit-the-stream-analytics-query"></a>Stream Analytics sorgusunu düzenle

Azure Stream Analytics, verileri akımış bir giriş kaynağı belirtmek, verileri istendiği gibi dönüştürmek ve çeşitli depolama veya işleme hedeflerine çıkış yapmak için SQL benzeri bir sorgu dili kullanır.

1. Genel Bakış sekmesinde **Sorguyu Düzenle**' ye tıklayın.

    ![Sorguyu Düzenle](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. Sorgu Düzenleyicisi 'nde, [YourOutputAlias] ve [Yourınputalias] yer tutucuları daha önce tanımladığınız değerlerle değiştirin.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Stream Analytics sorgu](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. **Kaydet** düğmesine tıklayın.
1. Değişiklikleri kabul etmek için **Evet** ' e tıklayın.

## <a name="start-the-stream-analytics-job"></a>Stream Analytics işini Başlat

1. Genel Bakış sekmesinde **Başlat**' a tıklayın.

    ![Stream Analytics Işi Başlat](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. İşi Başlat sekmesinde, **Özel ' e**tıklayın.

1. Cihazınızın akışa başlatıldığı zaman verilerin çekilmesi için birkaç saat geri gitmesi için özel süre ayarlayın.

1. **Başlat**'a tıklayın.

    ![Özel Tarih Seç](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    İş çalışır duruma gelene kadar bekleyin, sorgınızdan olabilecek hatalar görürseniz, sözdiziminin doğru olduğundan emin olun.

    ![İş çalışıyor](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    Akış işi IoT Hub verileri okumaya başlar ve verileri Data Lake Store depolar. Verilerin Data Lake Store görünmeye başlaması birkaç dakika sürebilir.

## <a name="explore-the-streaming-data"></a>Akış verilerini keşfet

1. Data Lake Store gidin.

1. Genel Bakış sekmesinde, **Veri Gezgini**' ne tıklayın.

1. Veri Gezgini 'nde **/streaming** klasörüne gidin. YYYY/AA/GG/HH biçimiyle oluşturulan klasörleri görürsünüz.

    ![Akış verilerini keşfet](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    JSON dosyalarını saat başına bir dosya olarak görürsünüz.

    ![Akış verilerini keşfet](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Sonraki Adımlar

Azure Data Lake Analytics, Data Lake Store veri kümelerinde büyük veri analizi gerçekleştirmek için kullanılabilir. [Data Lake Analytics belgeleri](https://docs.microsoft.com/azure/data-lake-analytics)hakkında daha fazla bilgi edinin.
