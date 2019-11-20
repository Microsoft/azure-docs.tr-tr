---
title: Verilerin gerçek zamanlı veri görselleştirmesi frm Azure IoT Hub – Power BI
description: Sensörden toplanan ve Azure IoT Hub 'ınıza gönderilen sıcaklık ve nem verilerini görselleştirmek için Power BI kullanın.
author: robinsh
keywords: gerçek zamanlı veri görselleştirme, canlı veri görselleştirme, algılayıcı veri görselleştirme
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/06/2019
ms.author: robinsh
ms.openlocfilehash: f0b909d10790511408e090546fd3359889ea5aca
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954638"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Azure IoT Hub Power BI kullanarak gerçek zamanlı algılayıcı verilerini görselleştirin

![Uçtan uca diyagram](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Öğrenecekleriniz

Power BI kullanarak Azure IoT Hub 'ınızın aldığı gerçek zamanlı algılayıcı verilerini görselleştirmeyi öğreneceksiniz. IoT Hub 'ınızdaki verileri bir Web uygulamasıyla görselleştirmeyi denemek istiyorsanız bkz. [Azure IoT Hub gerçek zamanlı algılayıcı verilerini görselleştirmek için Web uygulaması kullanma](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>Yapabilecekleriniz

* Bir tüketici grubu ekleyerek IoT Hub 'ınızı veri erişimi için hazırlayın.

* IoT Hub 'ınızdan Power BI hesabınıza veri aktarımı için Stream Analytics işi oluşturun, yapılandırın ve çalıştırın.

* Verileri görselleştirmek için bir Power BI raporu oluşturun ve yayımlayın.

## <a name="what-you-need"></a>Ne gerekiyor

* [Raspberry PI Çevrimiçi simülatör](iot-hub-raspberry-pi-web-simulator-get-started.md) öğreticisini veya cihaz öğreticilerinin birini doldurun; Örneğin, [Node. js Ile Raspberry Pi](iot-hub-raspberry-pi-kit-node-get-started.md). Bu makaleler aşağıdaki gereksinimleri kapsar:
  
  * Etkin bir Azure aboneliği.
  * Aboneliğiniz kapsamındaki bir Azure IoT Hub 'ı.
  * Azure IoT Hub 'ınıza ileti gönderen bir istemci uygulaması.

* Power BI hesabı. ([Power BI ücretsiz deneyin](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Stream Analytics işi oluşturma, yapılandırma ve çalıştırma

Stream Analytics işi oluşturarak başlayalım. İşi oluşturduktan sonra, verileri almak için kullanılan girdileri, çıkışları ve sorguyu tanımlarsınız.

### <a name="create-a-stream-analytics-job"></a>Akış Analizi işi oluşturma

1. [Azure Portal](https://portal.azure.com) **bir kaynak oluştur** > **nesnelerin interneti** > **Stream Analytics işi**seçin.

2. İş için aşağıdaki bilgileri girin.

   **İş adı**: İşin adı. Adın genel olarak benzersiz olması gerekir.

   **Kaynak grubu**: IoT Hub 'ınızın kullandığı kaynak grubunu kullanın.

   **Konum**: kaynak grubunuzda aynı konumu kullanın.

   ![Azure 'da Stream Analytics işi oluşturma](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job-azure.png)

3. **Oluştur**'u seçin.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Stream Analytics işine giriş ekleme

1. Stream Analytics işini açın.

2. **Iş topolojisi**altında **girişler**' i seçin.

3. **Giriş** bölmesinde, **akış girişi Ekle**' yi seçin ve ardından açılır listeden **IoT Hub** ' ı seçin. Yeni giriş bölmesinde aşağıdaki bilgileri girin:

   **Giriş diğer adı**: giriş için benzersiz bir diğer ad girin.

   **Aboneliğinizden IoT Hub sağlayın**: Bu radyo düğmesini seçin.

   **Abonelik**: Bu öğretici için kullanmakta olduğunuz Azure aboneliğini seçin.

   **IoT Hub**: Bu öğretici için kullanmakta olduğunuz IoT Hub seçin.

   **Uç nokta**: **Mesajlaşma**'yı seçin.

   **Paylaşılan erişim ilkesi adı**: Stream Analytics işinin IoT Hub 'ınız için kullanmasını istediğiniz paylaşılan erişim ilkesinin adını seçin. Bu öğretici için *hizmet*' i seçebilirsiniz. *Hizmet* ilkesi, yeni IoT Hub 'larında varsayılan olarak oluşturulur ve IoT Hub tarafından sunulan bulut tarafı uç noktalarında gönderme ve alma izni verir. Daha fazla bilgi edinmek için bkz. [erişim denetimi ve izinleri](iot-hub-devguide-security.md#access-control-and-permissions).

   **Paylaşılan erişim ilkesi anahtarı**: Bu alan, paylaşılan erişim ilkesi adı için seçiminize göre otomatik olarak doldurulur.

   **Tüketici grubu**: daha önce oluşturduğunuz tüketici grubunu seçin.

   Tüm diğer alanları varsayılan olarak bırakın.

   ![Azure 'da bir Stream Analytics işine giriş ekleme](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job-azure.png)

4. **Kaydet**’i seçin.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Stream Analytics işine çıkış ekleme

1. **Iş topolojisi**altında, **çıktılar**' i seçin.

2. **Çıktılar** bölmesinde **Ekle** ve **Power BI**' yi seçin.

3. **Power BI-yeni çıkış** bölmesinde **Yetkilendir** ' i seçin ve Power BI hesabınızda oturum açmak için istemleri izleyin.

4. Power BI oturumu açtıktan sonra, aşağıdaki bilgileri girin:

   **Çıkış diğer adı**: çıkış için benzersiz bir diğer ad.

   **Grup çalışma alanı**: hedef grup çalışma alanınızı seçin.

   **Veri kümesi adı**: bir veri kümesi adı girin.

   **Tablo adı**: bir tablo adı girin.

   ![Azure 'da bir Stream Analytics işine çıkış ekleme](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job-azure.png)

5. **Kaydet**’i seçin.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Stream Analytics işinin sorgusunu yapılandırma

1. **İş Topolojisi**'nin altında **Sorgu**'yu seçin.

2. `[YourInputAlias]` değerini işin giriş diğer adıyla değiştirin.

3. `[YourOutputAlias]` değerini işin çıkış diğer adıyla değiştirin.

   ![Azure 'da bir Stream Analytics işine sorgu ekleme](./media/iot-hub-live-data-visualization-in-power-bi/add-query-stream-analytics-job-azure.png)

4. **Kaydet**’i seçin.

### <a name="run-the-stream-analytics-job"></a>Stream Analytics işini çalıştırma

Stream Analytics işinde **genel bakış**' ı seçin ve ardından Başlat ' a > şimdi **başlat '** ı ** > seçin.** İş düzgün bir şekilde başlatıldıktan sonra, **Durduruldu** olan iş durumu **Çalışıyor** olarak değiştirilir.

![Azure 'da bir Stream Analytics işi çalıştırma](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Verileri görselleştirmek için Power BI raporu oluşturma ve yayımlama

1. Örnek uygulamanın cihazınızda çalıştığından emin olun. Aksi takdirde, [cihazınızı kurulum](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started)bölümündeki öğreticilere başvurabilirsiniz.

2. [Power BI](https://powerbi.microsoft.com/en-us/) hesabınızda oturum açın.

3. Kullandığınız çalışma alanını, **çalışma alanım**' ı seçin.

4. **Veri kümelerini**seçin.

   Stream Analytics işi için çıktıyı oluştururken belirttiğiniz veri kümesini görmeniz gerekir.

5. Oluşturduğunuz veri kümesi için **rapor Ekle** ' yi (veri kümesi adının sağındaki ilk simge) seçin.

   ![Microsoft Power BI raporu oluşturma](./media/iot-hub-live-data-visualization-in-power-bi/start-power-bi.png)

6. Zamanla değişen gerçek zamanlı sıcaklığı göstermek için bir çizgi grafik oluşturun.

   1. Rapor oluşturma sayfasının **görselleştirmeler** bölmesinde çizgi grafik simgesini seçerek çizgi grafik ekleyin.

   2. **Alanlar** bölmesinde, Stream Analytics işi için çıkış oluştururken belirttiğiniz tabloyu genişletin.

   3. **Görselleştirmeler** bölmesinde **EventEnqueuedUtcTime** öğesini **Eksen** üzerine sürükleyin.

   4. **temperature** öğesini **Değerler** üzerine sürükleyin.

      Bir çizgi grafik oluşturulur. Grafiğin x ekseninde UTC saat dilimine göre tarih ve saat görüntülenir. Grafiğin y ekseninde algılayıcıdan alınan sıcaklık görüntülenir.

      ![Microsoft Power BI raporuna sıcaklık için çizgi grafik ekleme](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temp.png)

7. Zamanla değişen gerçek zamanlı nem oranını göstermek için bir çizgi grafik daha oluşturun. Bunu yapmak için yukarıdaki adımları izleyin ve x ekseninde **Eventenqueuedutctime** ve y ekseni hakkında **nem** koyun.

   ![Microsoft Power BI raporuna nem için çizgi grafik ekleme](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. Raporu kaydetmek için **Kaydet** ' i seçin.

9. Sol bölmedeki **raporlar** ' ı seçin ve ardından yeni oluşturduğunuz raporu seçin.

10. **Web 'de yayımla** > **Dosya** ' yı seçin.

11. **Ekleme kodu oluştur**' u seçin ve ardından **Yayımla**' yı seçin.

Rapor erişimi ve raporu blogunuzla veya Web siteniz ile tümleştirmede kullanabileceğiniz bir kod parçacığı ile paylaşabileceğiniz bir rapor bağlantısı sağlamış olursunuz.

![Microsoft Power BI raporu yayımlama](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-publish.png)

Microsoft, mobil cihazınızda Power BI panoları ve raporlarınızı görüntülemek ve bunlarla etkileşim kurmak için [Power BI mobil uygulamalar](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) da sunmaktadır.

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Hub 'ınızdan gerçek zamanlı algılayıcı verilerini görselleştirmek için Power BI başarıyla kullandınız.

Azure IoT Hub 'den verileri görselleştirmenin başka bir yolu için bkz. [azure IoT Hub gerçek zamanlı algılayıcı verilerini görselleştirmek için bir Web uygulaması kullanma](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
