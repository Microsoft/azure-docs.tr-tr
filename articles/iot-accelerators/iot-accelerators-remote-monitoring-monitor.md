---
title: Uzaktan Izleme çözümünde cihazları izleme-Azure | Microsoft Docs
description: Bu öğreticide Uzaktan İzleme çözümü hızlandırıcısını kullanarak IoT cihazlarınızı izlemeyi öğreneceksiniz.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 400a71b11fde210b889d938041e88c5ebe73c1dc
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890863"
---
# <a name="tutorial-monitor-your-iot-devices"></a>Öğretici: IoT cihazlarınızı izleme

Bu öğreticide bağlı IoT cihazlarınızı izlemek için Uzaktan İzleme çözümü hızlandırıcısını kullanacaksınız. Çözüm panosunu kullanarak telemetri verilerini, cihaz bilgilerini, uyarıları ve KPI'leri görüntüleyeceksiniz.

Öğreticide konum, hız ve yük sıcaklığı telemetrisini gönderen iki sanal tır cihazı kullanılmaktadır. Tırlar, Contoso adlı bir kuruluş tarafından yönetilmektedir ve Uzaktan İzleme çözümü hızlandırıcısına bağlıdır. Contoso'da çalışan bir operatör olarak sahadaki tırlarınızdan birinin (truck-02) konumunu ve davranışını izlemeniz gerekmektedir.

Bu öğreticide şunları yaptınız:

>[!div class="checklist"]
> * Panodaki cihazları filtreleme
> * Gerçek zamanlı telemetri verilerini görüntüleme
> * Cihaz ayrıntılarını görüntüleme
> * Cihazlarınızdan gelen uyarıları görüntüleme
> * Sistem KPI'lerini görüntüleme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>Görüntülenecek cihazları seçme

**Dashboard** (Pano) sayfasında görüntülenecek bağlı cihazları seçmek için filtreleri kullanın. Yalnızca **Truck** (Tır) cihazlarını görüntülemek için filtre açılan menüsünden yerleşik **Trucks** (Tırlar) filtresini seçin:

[![Panoda trucks (tır) filtresi](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-expanded.png#lightbox)

Bir filtre uyguladığınızda haritada ve telemetri panelinde yalnızca filtre koşullarıyla eşleşen cihazlar görüntülenir. Çözüm hızlandırıcısına iki tırın bağlı olduğunu ve birinin truck-02 olduğunu görebilirsiniz:

[![Haritada yalnızca tırlar görüntülenir](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-expanded.png#lightbox)

Filtre oluşturmak, düzenlemek ve silmek için **Manage device groups** (Cihaz gruplarını yönet) öğesine tıklayın.

## <a name="view-real-time-telemetry"></a>Gerçek zamanlı telemetri verilerini görüntüleme

Çözüm hızlandırıcısı, **Dashboard** (Pano) sayfasındaki grafiğe gerçek zamanlı telemetri verilerini çizer. Telemetri grafiğinin en üstünde truck-02 dahil olmak üzere geçerli filtre ile seçilen cihazlar için kullanılabilen telemetri türleri gösterilir. Varsayılan olarak, grafik kamyonların enlem değerini gösterir ve buna göre truck-02 sabit görünmektedir:

[![Tır telemetri türleri](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-expanded.png#lightbox)

Tırların sıcaklık telemetrisini görüntülemek için **Temperature** (Sıcaklık) öğesine tıklayın. truck-02 için sıcaklık değerinin son bir saat içindeki değişimini görebilirsiniz:

[![Tır sıcaklık telemetrisi çizimi](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-expanded.png#lightbox)

## <a name="view-the-map"></a>Haritayı görüntüleme

Haritada geçerli filtre ile seçilmiş olan sanal tırlar hakkındaki bilgiler görüntülenir. Konum ayrıntılarını azaltmak veya artırmak için haritada yakınlaştırma ve kaydırma yapabilirsiniz. Harita üzerindeki cihaz simgesinin rengi **Alerts** (Uyarılar) (koyu mavi) veya **Warnings** (Uyarılar) (kırmızı) olduğunu gösterecek şekilde değişir. Haritanın sol tarafında **Alerts** (Uyarılar) ve **Warnings** (Uyarılar) için özet sayı görüntülenir.

truck-02 ayrıntılarını görüntülemek için haritada yakınlaştırma veya kaydırma yaparak tırı bulun ve haritadan tırı seçin. Ardından cihaz etiketini seçerek **Device details** (Cihaz ayrıntıları) panelini açın. Cihaz ayrıntıları panelinde şu veriler bulunur:

* Son telemetri değerleri
* Cihazın desteklediği metotlar
* Cihaz özellikleri

[![Panoda cihaz ayrıntılarını görüntüleme](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-expanded.png#lightbox)

## <a name="view-alerts"></a>Uyarıları görüntüleme

**Alerts** (Uyarılar) paneli, cihazlarınızdan gelen son uyarılarla ilgili ayrıntılı bilgileri görüntüler. truck-02 cihazından gelen uyarılar, yük sıcaklığının normalin üzerinde olduğunu göstermektedir:

[![Panoda cihaz uyarılarını görüntüleme](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-expanded.png#lightbox)

Son uyarıların süresini ayarlamak için bir filtre kullanabilirsiniz. Panel varsayılan olarak son bir saat içindeki uyarıları görüntüler.

## <a name="view-the-system-kpis"></a>Sistem KPI'lerini görüntüleme

**Dashboard** (Pano) sayfasında **Analytics** (Analiz) panelindeki çözüm hızlandırıcısı tarafından hesaplanan sistem KPI'leri görüntülenir:

[![Pano KPI'leri](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-expanded.png#lightbox)

Panoda seçilen uyarılar için geçerli cihaza ve zaman filtrelerine göre belirlenmiş üç KPI gösterilir:

* En fazla uyarıyı tetikleyen kurallar için etkin uyarı sayısı.
* Uyarıların cihaz türüne oranı.
* Kritik uyarıların yüzdesi.

truck-02 için tüm uyarılar, yük sıcaklığının normalin üzerinde olduğunu göstermektedir.

KPI'lerin nasıl toplanacağını uyarılar için süreyi ayarlayan ve görüntülenen cihazları denetleyen filtreler belirler. Panelde varsayılan olarak son bir saat için toplu KPI'ler görüntülenir.

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide sanal tırları filtrelemek ve izlemek amacıyla Uzaktan İzleme çözümü hızlandırıcısındaki **Dashboard** (Pano) sayfasını nasıl kullanacağınız gösterilmiştir. Çözüm hızlandırıcısını bağlı cihazlarınızdaki sorunları algılama amacıyla kullanmayı öğrenmek için bir sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [İzleme çözümünüze bağlı cihazlarla sorunları algılama](iot-accelerators-remote-monitoring-automate.md)