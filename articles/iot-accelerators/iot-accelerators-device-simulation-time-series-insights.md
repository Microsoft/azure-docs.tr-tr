---
title: Time Series Insights-Azure ile sanal Telemetriyi görselleştirin | Microsoft Docs
description: Cihaz benzetimi Çözüm Hızlandırıcısı tarafından oluşturulan Telemetriyi araştırmak ve analiz etmek için Time Series Insights ortamınızı nasıl yapılandıracağınızı öğrenin.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 2bbd7911a40d6a256d478e2533ad2469b8fd6973
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889338"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Cihaz simülasyonu çözüm hızlandırıcısında gönderilen Telemetriyi görselleştirmek için Time Series Insights kullanın

Cihaz benzetimi Çözüm Hızlandırıcısı, IoT çözümlerinizi test etmek için sanal cihazlardan telemetri oluşturmanıza imkan tanır. Bu nasıl yapılır Kılavuzu, bir Time Series Insights ortamı kullanarak sanal Telemetriyi görselleştirmeyi ve çözümlemeyi gösterir.

## <a name="prerequisites"></a>Ön koşullar

Bu nasıl yapılır kılavuzundaki adımları izleyerek etkin bir Azure aboneliğine ihtiyacınız vardır. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

Bu nasıl yapılır kılavuzundaki adımlarda, cihaz benzetimi çözüm Hızlandırıcısını Azure aboneliğinize dağıttığınız varsayılmaktadır. Çözüm hızlandırıcıyı dağıtmadıysanız, [bulut tabanlı cihaz benzetimi çözüm](quickstart-device-simulation-deploy.md) hızlı başlangıcı ' nda bulunan adımları izleyin ve çalıştırın.

Bu makalede, çözüm hızlandırıcının adının **contoso simülasyonu**olduğu varsayılır. Aşağıdaki adımları tamamladıktan sonra **contoso simülasyonu** çözüm hızlandırıcısının adıyla değiştirin.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Tüketici grubu oluşturma

Time Series Insights telemetri akışını sağlamak için IoT Hub 'ınızda adanmış bir tüketici grubu oluşturmanız gerekir. Time Series Insights bir olay kaynağı bir IoT Hub tüketici grubunun dışlamalı kullanımına sahip olmalıdır.

Aşağıdaki adımlar, tüketici grubunu oluşturmak için Azure Cloud Shell Azure CLı 'yi kullanır:

1. IoT Hub 'ı, cihaz benzetimi çözüm Hızlandırıcısını dağıtırken oluşturulan çeşitli kaynaklardan biridir. Aşağıdaki komutu yürütün IoT Hub 'ınızın adını bulun-çözüm hızlandırıcısının adını kullanmayı unutmayın:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    IoT Hub, **Microsoft. Devices/IotHubs**türünde kaynaktır.

1. **Devicesimulationtsi** adlı bir tüketici grubunu hub 'a ekleyin. Aşağıdaki komutta, hub 'ınızın adını ve çözüm hızlandırıcıyı kullanın:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Artık Azure Cloud Shell kapatabilirsiniz.

## <a name="create-a-new-time-series-insights-environment"></a>Yeni bir Time Series Insights ortamı oluştur

[Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md) , bulutta IoT ölçekli zaman serisi verilerinin yönetilmesi için tam olarak yönetilen bir analiz, depolama ve görselleştirme hizmetidir. Yeni bir Time Series Insights ortamı oluşturmak için:

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. **Time Series Insights** > **nesnelerin interneti** > **kaynak oluştur** ' u seçin:

    ![Yeni Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. Time Series Insights ortamınızı çözüm hızlandırıcılarınız ile aynı kaynak grubunda oluşturmak için aşağıdaki tablodaki değerleri kullanın:

    | Ayar | Değer |
    | ------- | ----- |
    | Ortam adı | Aşağıdaki ekran görüntüsünde **contoso-TSI**adı kullanılmaktadır. Bu adımı tamamladığınızda kendi benzersiz adınızı seçin. |
    | Abonelik | Açılan listeden Azure aboneliğinizi seçin. |
    | Kaynak grubu | **contoso-simülasyon**. Çözüm hızlandırıcısının adını kullanın. |
    | Konum | Bu örnek **Doğu ABD**kullanır. Ortamınızı cihazınızın benzetim hızlandırıcısında aynı bölgede oluşturun. |
    | Sku |**S1** |
    | Kapasite | **1** |

    ![Time Series Insights oluştur](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Time Series Insights ortamının çözüm hızlandırıcısı ile aynı kaynak grubuna eklenmesi, çözüm hızlandırıcıyı sildiğinizde silindiği anlamına gelir.

1. **Oluştur**'a tıklayın. Ortamın oluşturulması birkaç dakika sürebilir.

## <a name="create-event-source"></a>Olay kaynağı oluşturma

IoT Hub 'ınıza bağlanmak için yeni bir olay kaynağı oluşturun. Önceki adımlarda oluşturduğunuz tüketici grubunu kullanın. Time Series Insights olay kaynağı, başka bir hizmet tarafından kullanımda olan adanmış bir tüketici grubu gerektirir.

1. Azure portal yeni zaman serisi ortamınıza gidin.

1. Sol tarafta **olay kaynakları**' na tıklayın:

    ![Olay kaynaklarını görüntüle](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. **Ekle**' ye tıklayın:

    ![Olay kaynağı Ekle](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. IoT Hub 'ınızı yeni bir olay kaynağı olarak yapılandırmak için aşağıdaki tablodaki değerleri kullanın:

    | Ayar | Değer |
    | ------- | ----- |
    | Olay kaynağı adı | Aşağıdaki ekran görüntüsünde **contoso-IoT-Hub**adı kullanılmaktadır. Bu adımı tamamladığınızda kendi benzersiz adınızı kullanın. |
    | Kaynak | **IoT Hub’ı** |
    | İçeri aktarma seçeneği | **Kullanılabilir aboneliklerden IoT Hub kullanma** |
    | Abonelik Kimliği | Açılan listeden Azure aboneliğinizi seçin. |
    | IoT Hub adı | **contoso-simulation7d894**. Cihaz simülasyonu çözüm hızlandırıcısında IoT Hub 'ınızın adını kullanın. |
    | IoT Hub ilkesi adı | **iothubowner** |
    | IoT Hub ilke anahtarı | Bu alan otomatik olarak doldurulur. |
    | IoT Hub 'ı Tüketici grubu | **devicesimulationtsi** |
    | Olay serileştirme biçimi | **JSON** |
    | Zaman damgası Özellik adı | Boş bırakın |

    ![Olay kaynağı oluştur](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. **Oluştur**'a tıklayın.

> [!NOTE]
> Time Series Insights gezgin 'e daha [fazla kullanıcı erişimi](../../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access) verebilirsiniz.

## <a name="start-a-simulation"></a>Simülasyon başlatma

Time Series Insights Gezginini kullanmadan önce, cihaz benzetimi çözüm Hızlandırıcısını, bazı telemetri oluşturacak şekilde yapılandırın. Aşağıdaki ekran görüntüsünde, 10 Chiller cihazlarıyla çalışan bir simülasyonu gösterilmektedir:

![Çalışan cihaz benzetimi](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Time Series Insights gezgini

Time Series Insights Gezgini, telemetrinizi görselleştirmek için kullanabileceğiniz bir Web uygulamasıdır.

1. Azure portal, Time Series Insights **genel bakış** sekmesini seçin.

1. Time Series Insights Explorer Web uygulamasını açmak için **ortama git**' e tıklayın:

    ![Time Series Insights gezgini](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. Zaman seçimi panelinde, hızlı saatler menüsünde **son 30 dakika** ' yı seçin ve **Ara**' ya tıklayın:

    ![Gezgin aramasını Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. Sol taraftaki terimler panelinde, **Ölçü** olarak **sıcaklık** ve **ıothub-bağlantı-cihaz-kimliği** değerlerini **bölünmüş değere göre** seçin:

    ![Gezgin sorgusunu Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Grafiğe sağ tıklayın ve **olayları keşfet**' i seçin:

    ![Gezgin olaylarını Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. Olay verileri bir kılavuzda gösterilir:

    ![Gezgin tablosu Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Perspektif görünümü düğmesine tıklayın:

    ![Gezgin perspektifini Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Perspektife yeni bir sorgu eklemek için **+** ' ye tıklayın:

    ![Time Series Insights Gezgini sorgu Ekle](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Zaman aralığı olarak **son 30 dakika** , **Ölçü**olarak **nem** ve **ıothub-Connection-Device-ID** değerini değere **göre bölme** olarak seçin:

    ![Gezgin sorgusunu Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Cihaz telemetri panonuzu görüntülemek için perspektif görünümü düğmesine tıklayın:

    ![Gezgin panosunu Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Daha fazla araştırma yapmak istiyorsanız çözüm Hızlandırıcısını dağıtılmış bırakın.

Artık çözüm hızlandırıcısına ihtiyacınız yoksa, bunu seçip **çözümü Sil**' e tıklayarak [sağlanan çözümler](https://www.azureiotsolutions.com/Accelerators#dashboard) sayfasından silin.

Time Series Insights ortamını çözüm hızlandırıcısının kaynak grubuna eklediyseniz, çözüm hızlandırıcıyı sildiğinizde otomatik olarak silinir. Aksi takdirde, Azure portal Time Series Insights ortamını el ile kaldırmanız gerekir.

## <a name="next-steps"></a>Sonraki Adımlar

Time Series Insights Explorer 'da verileri araştırıp sorgulama hakkında daha fazla bilgi edinmek için, bkz. [Azure Time Series Insights Explorer](../time-series-insights/time-series-insights-explorer.md).
