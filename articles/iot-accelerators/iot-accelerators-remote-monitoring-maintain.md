---
title: Uzaktan Izleme çözümünde uyarıları kullanma-Azure | Microsoft Docs
description: Bu öğreticide Uzaktan İzleme çözümünde uyarıların nasıl kullanılacağı ve bu çözüme bağlı cihazlarla ilgili sorunların nasıl yönetileceği gösterilmektedir.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 853fa2b80e04dd8d9225d023db8030fed044ed7f
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890916"
---
# <a name="tutorial-troubleshoot-and-fix-device-issues"></a>Öğretici: Cihaz sorunlarını giderme ve düzeltme

Bu öğreticide bağlı IoT cihazlarınızla ilgili sorunları tanımlamak ve gidermek için Uzaktan İzleme çözümü hızlandırıcısını kullanacaksınız. Çözüm hızlandırıcısı panosundaki uyarıları kullanarak sorunları tanımlayacak ve bu sorunları gidermek için uzaktan iş çalıştıracaksınız.

Contoso, yeni bir **Prototype** cihazını sahada test etmektedir. Contoso'da çalışan bir operatör olarak test sırasında **Prototype** cihazının panoda beklenmedik bir şekilde sıcaklık uyarısı tetiklediğini fark ediyorsunuz. Bu arızalı **Prototype** cihazının davranışını incelemeniz ve sorunu çözmeniz gerekiyor.

Bu öğreticide şunları yaptınız:

>[!div class="checklist"]
> * Bir cihazdan gelen uyarıyı araştırma
> * Cihazla ilgili sorunu çözme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="investigate-an-alert"></a>Sorunu araştırma

**Dashboard** (Pano) sayfasında **Prototype** cihazlarıyla ilişkilendirilmiş kuraldan beklenmeyen sıcaklık uyarıları geldiğini fark ediyorsunuz:

[![Panodaki uyarılar](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-expanded.png#lightbox)

Sorunu araştırmak için uyarının yanındaki **Explore Alert** (Uyarıyı Keşfet) seçeneğini belirleyin:

[![Uyarıyı panodan keşfetme](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-expanded.png#lightbox)

Uyarının ayrıntılı görünümünde şu bilgiler yer alır:

* Uyarının tetiklendiği zaman
* Uyarıyla ilişkilendirilmiş cihazlar hakkında durum bilgileri
* Uyarıyla ilişkilendirilmiş cihazların telemetri verileri

[![Uyarı ayrıntıları](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-expanded.png#lightbox)

Uyarıyı onaylamak için **Alert occurrences** (Uyarı tekrarları) girişlerinin tümünü seçip **Acknowledge** (Onayla) öğesini seçin. Bu eylem, diğer operatörlere uyarıyı gördüğünüzü ve üzerinde çalıştığınızı bildirir:

[![Uyarıları onaylama](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-expanded.png#lightbox)

Uyarıyı onayladığınızda durumu **Acknowledged** (Onaylandı) olarak değişir.

Uyarı bulunan cihaz listesinde sıcaklık uyarısının oluşturulmasından sorumlu olan **Prototype** cihazını görebilirsiniz:

[![Uyarıya neden olan cihazları listeleme](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-expanded.png#lightbox)

## <a name="resolve-the-issue"></a>Sorunu çözme

**Prototype** cihazıyla ilgili sorunu çözmek için cihazda **DecreaseTemperature** metodunu çağırmanız gerekir.

Üzerinde işlem yapmak istediğiniz cihazı uyarı veren cihaz listesinden seçin ve **Jobs** (İşler) öğesini seçin. **Prototype** cihaz modeli altı metodu destekler:

[![Cihazın desteklediği metotları görüntüleme](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-expanded.png#lightbox)

**DecreaseTemperature** girişini seçin ve işe **DecreaseTemperature** adını verin. Ardından **Apply** (Uygula) öğesine tıklayın:

[![Sıcaklığı düşürme işini oluşturma](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-expanded.png#lightbox)

İşin durumunu izlemek için **View job status** (İş durumunu görüntüle) öğesine tıklayın. **Jobs** (İşler) görünümünü kullanarak çözümdeki tüm işleri ve metot çağrılarını izleyebilirsiniz:

[![Sıcaklığı düşürme işini izleme](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-expanded.png#lightbox)

**Dashboard** (Pano) sayfasında telemetri verilerini görüntüleyerek cihaz sıcaklığının düşüp düşmediğini kontrol edebilirsiniz:

[![Sıcaklık düşüşünü görüntüleme](./media/iot-accelerators-remote-monitoring-maintain/jobresult-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/jobresult-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide cihazlarınızla ilgili sorunları tanımlamak için uyarıları kullanmayı ve sorunları çözmek için cihazlar üzerinde işlem gerçekleştirmeyi öğrendiniz. Çözüm hızlandırıcısına gerçek bir cihaz bağlamayı öğrenmek için nasıl yapılır makalelerine devam edin.

Cihaz sorunlarını yönetmeyi öğrendiniz, önerilen bir sonraki adımda [Cihazınızı Uzaktan İzleme çözümü hızlandırıcısına bağlamayı](iot-accelerators-connecting-devices.md) öğrenmeniz önerilir.
