---
title: Azure IoT Hub ile bağlantıyı Tanıla ve sorunlarını giderme
description: Azure IoT Hub cihaz bağlantısıyla sık karşılaşılan hataları tanılamayı ve sorun gidermeyi öğrenin
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: jlian
ms.openlocfilehash: 3904c6390cfe8de197bae470c4ae32d22605ae6a
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801418"
---
# <a name="detect-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Azure IoT Hub ile kesilen sorunları algılayın ve giderin

Birçok olası hata noktası olduğundan, IoT cihazlarına yönelik bağlantı sorunlarının giderilmesi zor olabilir. Cihaz tarafı uygulama mantığı, fiziksel ağlar, protokoller, donanım ve Azure IoT Hub her türlü soruna neden olabilir. Bu makalede, bulut tarafında (cihaz tarafı aksine) cihaz bağlantısı sorunlarını algılamaya ve gidermeye yönelik öneriler sağlanır.

## <a name="get-alerts-and-error-logs"></a>Uyarıları ve hata günlüklerini al

Cihaz bağlantıları başlatıldığında uyarıları almak ve günlükleri yazmak için Azure Izleyici 'yi kullanın.

### <a name="turn-on-diagnostic-logs"></a>Tanılama günlüklerini açın

Cihaz bağlantısı olaylarını ve hatalarını günlüğe kaydetmek için IoT Hub tanılamayı açın.

1. [Azure Portal](https://portal.azure.com) oturum açın.

2. IoT Hub 'ınıza gidin.

3. **Tanılama ayarları**' nı seçin.

4. Seçin **tanılamayı Aç**.

5. Toplanabilecek **bağlantı** günlüklerini etkinleştirin.

6. Daha kolay analiz için **Log Analytics gönder** ' i açın ([bkz. fiyatlandırma](https://azure.microsoft.com/pricing/details/log-analytics/)). [Bağlantı hatalarını çözme](#resolve-connectivity-errors)bölümündeki örneğe bakın.

   ![Önerilen ayarlar](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Daha fazla bilgi edinmek için bkz. [Azure IoT Hub sistem durumunu izleme ve sorunları hızlı bir şekilde tanılama](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-the-_connected-devices_-count-metric"></a>_Bağlı cihazlar_ sayım ölçümü için uyarıları ayarlama

Cihazların bağlantısı kesildiğinde uyarı almak için, **bağlı cihazlar (Önizleme)** ölçümünde uyarıları yapılandırın.

1. [Azure Portal](https://portal.azure.com) oturum açın.

2. IoT Hub 'ınıza gidin.

3. **Uyarıları**seçin.

4. **Yeni uyarı kuralı**' nı seçin.

5. **Koşul Ekle**' yi seçin ve ardından "bağlı cihazlar (Önizleme)" öğesini seçin.

6. İstediğiniz eşiklerinizi ve uyarı seçeneklerini aşağıdaki istemlerle ayarlamayı tamamlayın.

Daha fazla bilgi edinmek için bkz. [Microsoft Azure klasik uyarılar nelerdir?](../azure-monitor/platform/alerts-overview.md).

## <a name="resolve-connectivity-errors"></a>Bağlantı hatalarını çözme

Bağlı cihazlar için tanılama günlüklerini ve uyarılarını açtığınızda, hata oluştuğunda uyarılar alırsınız. Bu bölümde, bir uyarı aldığınızda yaygın sorunların nasıl giderileceği açıklanmaktadır. Aşağıdaki adımlarda, tanılama günlüklerinizin Azure Izleyici günlüklerini ayarlamış olduğunuz varsayılmaktadır.

1. Azure portal **Log Analytics** için çalışma alanınıza gidin.

2. **Günlük araması**' nı seçin.

3. IoT Hub için bağlantı hata günlüklerini yalıtmak için aşağıdaki sorguyu girin ve **Çalıştır**' ı seçin:

    ```kusto
    search *
    | where ( Type == "AzureDiagnostics" and ResourceType == "IOTHUBS")
    | where ( Category == "Connections" and Level == "Error")
    ```

1. Sonuçlar varsa, hata hakkında daha fazla `OperationName`ayrıntı `ResultType` almak için, (hata kodu `ResultDescription` ) ve (hata iletisi) bölümüne bakın.

   ![Hata günlüğü örneği](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

2. Sık karşılaşılan hataları anlamak ve çözmek için bu tabloyu kullanın.

    | Hata | Kök neden | Çözüm |
    |-------|------------|------------|
    | 404104 Deviceconnectioncloseduzaktan | Bağlantı cihaz tarafından kapatıldı, ancak IoT Hub nedenini bilmez. Genel nedenler MQTT/AMQP zaman aşımı ve internet bağlantı kaybını içerir. | Cihazın [bağlantıyı test](tutorial-connectivity.md)ederek IoT Hub bağlanabildiğinizden emin olun. Bağlantı iyi ise, ancak cihaz zaman zaman bağlantıyı kesse, protokol seçiminiz (MQTT/AMPQ) için uygun etkin tut cihaz mantığını uyguladığınızdan emin olun. |
    | 401003 Iothubyetkilendirilmemiş | IoT Hub bağlantının kimliğini doğrulayamadı. | Kullandığınız SAS veya diğer güvenlik belirtecinin kullanım dışında olmadığından emin olun. [Azure IoT SDK 'ları](iot-hub-devguide-sdks.md) özel yapılandırma gerektirmeden belirteçleri otomatik olarak oluşturur. |
    | 409002 LinkCreationConflict | Cihazda birden fazla bağlantı vardır. Bir cihaz için yeni bir bağlantı isteği geldiğinde, IoT Hub önceki hatayı bu hatayla kapatır. | En yaygın durumda, bir cihaz bir bağlantı kesmeyi algılar ve bağlantıyı yeniden kurmaya çalışır, ancak IoT Hub cihazın bağlı olduğunu kabul eder. IoT Hub önceki bağlantıyı kapatır ve bu hatayı günlüğe kaydeder. Bu hata genellikle farklı, geçici bir sorunun yan etkisi olarak görünür, bu nedenle daha fazla sorun gidermek için günlüklerdeki diğer hatalara bakın. Aksi takdirde, yalnızca bağlantı düşerse yeni bir bağlantı isteği yayınlediğinizden emin olun. |
    | 500001 ServerError | IoT Hub, sunucu tarafı bir sorunla karşılaştı. Büyük olasılıkla sorun geçicidir. IoT Hub takım [SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/)'yı sürdürmek için zor çalışırken, IoT Hub düğümlerin küçük alt kümeleri zaman zaman geçici hatalara karşılaşabilir. Cihazınız sorun yaşayan bir düğüme bağlanmaya çalıştığında, bu hatayı alırsınız. | Geçici hatayı azaltmak için cihazdan yeniden deneme yapın. [Yeniden denemeleri otomatik olarak yönetmek](iot-hub-reliability-features-in-sdks.md#connection-and-retry)Için [Azure IoT SDK](iot-hub-devguide-sdks.md)'larının en son sürümünü kullandığınızdan emin olun.<br><br>Geçici hata işleme ve yeniden denemeler için en iyi yöntem için bkz. [geçici hata işleme](/azure/architecture/best-practices/transient-faults).  <br><br>Sorun yeniden denemeden sonra devam ederse, IoT Hub bilinen bir sorun olup olmadığını görmek için [kaynak durumu](iot-hub-monitor-resource-health.md#use-azure-resource-health) ve [Azure durumunu](https://azure.microsoft.com/status/history/) kontrol edin. Bilinen bir sorun yoksa ve sorun devam ederse, daha fazla araştırma için [desteğe başvurun](https://azure.microsoft.com/support/options/) . |
    | 500008 GenericTimeout | IoT Hub, zaman aşımından önce bağlantı isteğini tamamlayamadı. 500001 sunucubir hata gibi, bu hata muhtemelen geçicidir. | 500001 sunucubir hata için sorun giderme adımlarını temel neden izleyin ve bu hatayı çözün.|

## <a name="other-steps-to-try"></a>Denemek için diğer adımlar

Önceki adımlarda yardım olmadıysa, şunları deneyebilirsiniz:

* Fiziksel veya uzaktan (SSH gibi) sorunlu cihazlara erişiminiz varsa, sorun gidermeye devam etmek için [cihaz tarafı sorun giderme kılavuzunu](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) izleyin.

* IoT Hub 'ınızın > IoT cihazlarınızın Azure portal >, cihazlarınızın **etkinleştirildiğini** doğrulayın.

* [Azure IoT Hub Forumu](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)veya [Azure desteğiyle](https://azure.microsoft.com/support/options/)yardım alın.

Herkesin belgelerinin artırılmasına yardımcı olmak için, bu kılavuz size yardımcı olmadıysa aşağıdaki geri bildirim bölümünde bir yorum bırakın.

## <a name="next-steps"></a>Sonraki adımlar

* Geçici sorunları çözme hakkında daha fazla bilgi için bkz. [geçici hata işleme](/azure/architecture/best-practices/transient-faults).

* Azure IoT SDK ve yeniden denemeleri yönetme hakkında daha fazla bilgi edinmek için bkz. [azure IoT Hub cihaz SDK 'larını kullanarak bağlantı ve güvenilir mesajlaşma yönetimi](iot-hub-reliability-features-in-sdks.md#connection-and-retry).
