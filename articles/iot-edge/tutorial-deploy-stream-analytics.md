---
title: Öğretici-Azure IoT Edge kullanarak uç Stream Analytics
description: Bu öğreticide, Azure Stream Analytics bir IoT Edge cihazına modül olarak dağıtırsınız
author: kgremban
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 323973b7646acee07a0c4dbc59834e0aceca75ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462057"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>Öğretici: Azure Stream Analytics IoT Edge modülü olarak dağıtma

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Birçok IoT çözümü, IoT cihazlarından buluta ulaşan veriler hakkında bilgi edinmek için analiz hizmetlerini kullanır. Azure IoT Edge ile [Azure Stream Analytics](../stream-analytics/index.yml) mantığını alıp cihazın kendisine aktarabilirsiniz. Telemetri akışlarını uç cihazlarda işleyerek yüklenen veri miktarını ve eyleme dönüştürülebilir içgörülere tepki verme süresini azaltabilirsiniz.

Azure IoT Edge ve Azure Stream Analytics, iş yükü geliştirmeyi basitleştirmek için tümleşiktir. Azure portal bir Azure Stream Analytics işi oluşturabilir ve sonra ek kod olmadan bir IoT Edge modülü olarak dağıtabilirsiniz.  

Azure Stream Analytics, veri analizi için hem bulutta hem de IoT Edge cihazlarda zengin yapılandırılmış bir sorgu söz dizimi sağlar. Daha fazla bilgi için bkz. [Azure Stream Analytics belgeleri](../stream-analytics/stream-analytics-edge.md).

Bu öğreticideki Stream Analytics modülü, tekrar eden 30 saniyelik dönemler içindeki ortalama sıcaklığı hesaplar. Bu ortalama değer 70'e ulaştığında modül, eyleme geçmek için cihazla ilgili bir uyarı gönderir. Bu durumda eylem, sıcaklık sensörü simülasyonunu sıfırlamaktır. Bir üretim ortamında bu işlevi kullanarak sıcaklık tehlikeli düzeylere ulaştığında bir makineyi kapatabilir veya önleyici işlemler gerçekleştirebilirsiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
>
> * Verileri uç cihazlarda işlemek için bir Azure Stream Analytics işi oluşturma.
> * Yeni Azure Stream Analytics işini diğer IoT Edge modüllerine bağlama.
> * Azure Stream Analytics işini Azure portalından bir IoT Edge cihazına dağıtma.

<center>

![Diyagram-öğretici mimarisi: ASA işini hazırlama ve dağıtma](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bir Azure IoT Edge cihazı:

* [Linux](quickstart-linux.md) veya [Windows cihazları](quickstart.md)için Hızlı Başlangıç bölümündeki adımları izleyerek bir Azure sanal makinesini IoT Edge cihaz olarak kullanabilirsiniz.

Bulut kaynakları:

* Azure'da ücretsiz veya standart katman [IoT Hub'ı](../iot-hub/iot-hub-create-through-portal.md).

## <a name="create-an-azure-stream-analytics-job"></a>Azure Stream Analytics işi oluşturma

Bu bölümde, aşağıdaki adımları sağlayacak bir Azure Stream Analytics işi oluşturursunuz:

* IoT Edge cihazınızdan veri alın.
* Bir küme aralığı dışındaki değerler için telemetri verilerini sorgulayın.
* Sorgu sonuçlarına göre IoT Edge cihazda işlem yapın.

### <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

IoT Edge cihazında çalıştırmak üzere oluşturduğunuz Azure Stream Analytics işinin cihazdan çağrılabilecek bir şekilde depolanması gerekir. Mevcut bir Azure Depolama hesabını kullanabilir veya şimdi yeni bir tane oluşturabilirsiniz.

1. Azure Portal, **kaynak**  >  **depolama**  >  **depolama hesabı** oluştur ' a gidin.

1. Depolama hesabınızı oluşturmak için aşağıdaki değerleri girin:

   | Alan | Değer |
   | ----- | ----- |
   | Abonelik | IoT hub'ınızla aynı aboneliği seçin. |
   | Kaynak grubu | Hızlı başlangıç ve öğreticiler IoT Edge için tüm test kaynaklarınız için aynı kaynak grubunu kullanmanızı öneririz. Örneğin, **IoTEdgeResources**. |
   | Name | Depolama hesabınıza benzersiz bir ad verin. |
   | Konum | Size yakın bir konum seçin. |

1. Diğer alanlar için varsayılan değerleri tutun ve **gözden geçir + oluştur**' u seçin.

1. Ayarlarınızı gözden geçirin ve **Oluştur**' u seçin.

### <a name="create-a-new-job"></a>Yeni bir iş oluşturma

1. Azure Portal **kaynak oluşturma**  >  **nesnelerin interneti**  >  **Stream Analytics işi**' ne gidin.

1. İşinizi oluşturmak için aşağıdaki değerleri girin:

   | Alan | Değer |
   | ----- | ----- |
   | İş adı | İşinize bir ad verin. Örneğin, **IoTEdgeJob** |
   | Abonelik | IoT hub'ınızla aynı aboneliği seçin. |
   | Kaynak grubu | IoT Edge hızlı başlangıçlarında ve öğreticilerinde oluşturduğunuz tüm test kaynakları için aynı kaynak grubunu kullanmanızı öneririz. Örneğin, **IoTEdgeResources**. |
   | Konum | Size yakın bir konum seçin. |
   | Barındırma ortamı | **Kenar**'ı seçin. |

1. **Oluştur**’u seçin.

### <a name="configure-your-job"></a>İşinizi yapılandırma

Azure portalda Stream Analytics işiniz oluşturulduktan sonra iletilen verilerle çalışması için giriş, çıkış ve sorgu ile yapılandırabilirsiniz.

Bu bölümde giriş, çıkış ve sorgu öğelerini kullanarak IoT Edge cihazından sıcaklık verilerini alan bir iş oluşturabilirsiniz. Bu iş 30 saniyelik aralıklarla verileri analiz eder. Herhangi bir aralıktaki ortalama sıcaklık değeri 70 derecenin üzerine çıkarsa IoT Edge cihazına bir uyarı gönderilir. Bir sonraki bölümde işi dağıtırken verilerin geldiği ve gittiği yerleri belirteceksiniz.  

1. Azure portalda Stream Analytics işinize gidin.

1. **İş Topolojisi** bölümünde **Girişler**'i ve **Akış girişi ekle**'yi seçin.

   ![Azure Stream Analytics-giriş ekleme](./media/tutorial-deploy-stream-analytics/asa-input.png)

1. Açılan listeden **Edge Hub**'ını seçin.

1. **Yeni giriş** bölmesinde giriş diğer adı olarak **temperature** yazın.

1. Diğer alanların varsayılan değerlerini tutun ve **Kaydet**’i seçin.

1. **İş Topolojisi**'nin altında **Çıkışlar**'ı açın ve **Ekle**'yi seçin.

   ![Azure Stream Analytics-çıkış Ekle](./media/tutorial-deploy-stream-analytics/asa-output.png)

1. Açılan listeden **Edge Hub**'ını seçin.

1. **Yeni çıkış** bölmesinde çıkış diğer adı olarak **alert** yazın.

1. Diğer alanların varsayılan değerlerini tutun ve **Kaydet**’i seçin.

1. **Iş topolojisi** altında **sorgu**' yı seçin.

1. Varsayılan metni aşağıdaki sorguyla değiştirin. 30 saniyelik süre içindeki ortalama makine sıcaklığı 70 dereceye ulaşırsa SQL kodu uyarı çıkışına bir sıfırlama komutu gönderir. Sıfırlama komutu, gerçekleştirilebilecek bir eylem olarak önceden sensöre programlanmıştır.

    ```sql
    SELECT  
        'reset' AS command
    INTO
       alert
    FROM
       temperature TIMESTAMP BY timeCreated
    GROUP BY TumblingWindow(second,30)
    HAVING Avg(machine.temperature) > 70
    ```

1. **Sorguyu Kaydet**' i seçin.

### <a name="configure-iot-edge-settings"></a>IoT Edge ayarlarını yapılandırma

IoT Edge cihazınıza dağıtılacak Stream Analytics işinizi hazırlamak için işi depolama hesabındaki bir kapsayıcıyla ilişkilendirmeniz gerekir. İşinizi dağıttığınızda iş tanımı depolama kapsayıcısına aktarılır.

1. **Yapılandır** altında **depolama hesabı ayarları** ' nı seçin ve **depolama hesabı ekle**' yi seçin.

   ![Azure Stream Analytics-depolama hesabı ekleme](./media/tutorial-deploy-stream-analytics/add-storage-account.png)

1. Açılır menüden Bu öğreticinin başlangıcında oluşturduğunuz **Depolama hesabını** seçin.

1. **Kapsayıcı** alanında **Yeni oluştur**'u seçip depolama kapsayıcısı için bir ad girin.

1. **Kaydet**’i seçin.

## <a name="deploy-the-job"></a>İşi dağıtma

Artık Azure Stream Analytics işinizi IoT Edge cihazınıza dağıtmaya hazırsınız.

Bu bölümde Azure portaldaki **Modülleri Ayarlama** sihirbazını kullanarak bir *dağıtım bildirimi* oluşturacaksınız. Dağıtım bildirimi bir cihaza dağıtılacak tüm modülleri, modül görüntülerinin depolandığı kapsayıcı kayıt defterlerini, modüllerin yönetilme şeklini ve modüllerin birbirleriyle iletişim kurma şeklini belirten bir JSON dosyasıdır. IoT Edge cihazınız, dağıtım bildirimini IoT Hub'dan aldıktan sonra içindeki bilgileri kullanarak tüm atanmış modülleri dağıtır ve yapılandırır.

Bu öğreticide iki modül dağıtacaksınız. Birincisi, sıcaklık ve nem sensöri taklit eden bir modül olan **SimulatedTemperatureSensor**. İkincisi ise Stream Analytics işinizdir. Sensör modülü, iş sorgunuzun analiz edeceği veri akışını sağlar.

1. Azure portalında IoT Hub'ınıza gidin.

1. **IoT Edge**' e gidin ve IoT Edge cihazınızın Ayrıntılar sayfasını açın.

1. **Modül ayarla**' yı seçin.  

1. SimulatedTemperatureSensor modülünü daha önce bu cihaza dağıttıysanız, bu cihaz için yeniden doldurma olabilir. Aksi takdirde aşağıdaki adımlarla modülü ekleyin:

   1. **Ekle**'ye tıklayıp **IoT Edge Modülü**'nü seçin.
   1. Ad için **SimulatedTemperatureSensor** yazın.
   1. Görüntü URI'si alanına **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0** yazın.
   1. Diğer ayarları değiştirmeden bırakın ve **Ekle**' yi seçin.

1. Aşağıdaki adımları uygulayarak Azure Stream Analytics Edge işinizi ekleyin:

   1. **Ekle**'ye tıklayıp **Azure Stream Analytics Modülü**'nü seçin.
   1. Aboneliğinizi ve oluşturduğunuz Azure Stream Analytics Edge işini seçin.
   1. **Kaydet**’i seçin.

   Değişikliklerinizi kaydettikten sonra, Stream Analytics işinizin ayrıntıları oluşturduğunuz depolama kapsayıcısına yayımlanır.

1. Stream Analytics modülü Modüller listesine eklendiğinde, nasıl yapılandırıldığını görmek ve **güncelleştirme IoT Edge modülü** sayfasında ayarlarını güncelleştirmek için adını seçin.

   **Modül ayarları** sekmesi, standart bir Azure Stream Analytics görüntüsünü işaret eden **görüntü URI 'sine** sahiptir. Bu tek görüntü, bir IoT Edge cihazına dağıtılan her Stream Analytics modülü için kullanılır.

   **Module Ikizi Settings** sekmesi, **asajobınfo** adlı Azure Stream Analytics (ASA) özelliğini tanımlayan JSON 'ı gösterir. Bu özelliğin değeri, depolama kapsayıcınızdaki iş tanımını gösterir. Bu özellik Stream Analytics görüntüsünün belirli iş ayrıntılarınız ile nasıl yapılandırıldığı.

   Varsayılan olarak Stream Analytics modülü, temel aldığı işle aynı adı alır. İsterseniz bu sayfada modül adını değiştirebilirsiniz, ancak gerekli değildir.

1. **Güncelleştir** veya **iptal**' i seçin.

1. Bir sonraki adımda ihtiyaç duyacağınız için Stream Analytics modülünüzün adını unutmayın. Sonra, Ileri ' yi seçin: devam etmek için **yollar** .

1. **Rotalar** sekmesinde, iletilerin modüller arasında nasıl geçtiğini tanımlar ve IoT Hub. İletiler ad/değer çiftleri kullanılarak oluşturulur. Default `route` ve `upstream` Name ve Values değerlerini aşağıdaki tabloda gösterilen çiftler ile değiştirin, aşağıdaki ad/değer çiftleri, _{moduleName}_ örneklerini Azure Stream Analytics modülünüzün adıyla değiştirir.

    | Name | Değer |
    | --- | --- |
    | `telemetryToCloud` | `FROM /messages/modules/SimulatedTemperatureSensor/* INTO $upstream` |
    | `alertsToCloud` | `FROM /messages/modules/{moduleName}/* INTO $upstream` |
    | `alertsToReset` | `FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint("/modules/SimulatedTemperatureSensor/inputs/control")` |
    | `telemetryToAsa` | `FROM /messages/modules/SimulatedTemperatureSensor/* INTO BrokeredEndpoint("/modules/{moduleName}/inputs/temperature")`|

    Burada belirttiğiniz rotalar, IoT Edge cihazından veri akışını belirler. SimulatedTemperatureSensor ' deki telemetri verileri IoT Hub ve Stream Analytics işinde yapılandırılan **sıcaklık** girişine gönderilir. **Uyarı** çıkış iletileri, sıfırlama komutunun tetiklenmesi için IoT Hub ve SimulatedTemperatureSensor modülüne gönderilir.

1. **İleri ' yi seçin: gözden geçir + oluştur**.

1. **Gözden geçir + oluştur** sekmesinde, sihirbazda verdiğiniz bIlgIlerIn bir JSON dağıtım bildirimine nasıl dönüştürüleceğini görebilirsiniz. Bildirimi incelemeyi tamamladıktan sonra **Oluştur**' u seçin.

1. Cihaz ayrıntıları sayfasına döndürülürsünüz. **Yenile**' yi seçin.  

    Çalışan yeni Stream Analytics modülünü, IoT Edge Aracısı ve IoT Edge hub modülleriyle birlikte görmeniz gerekir. Bilgilerin IoT Edge cihazınıza ulaşması ve ardından yeni modüllerin başlaması birkaç dakika sürebilir. Sağ tarafta çalışan modülleri görmüyorsanız, sayfayı yenilemeye devam edin.

    ![Cihaz tarafından bildirilen SimulatedTemperatureSensor ve ASA modülü](./media/tutorial-deploy-stream-analytics/module-output2.png)

## <a name="view-data"></a>Verileri görüntüleme

Artık Azure Stream Analytics modülü ve SimulatedTemperatureSensor modülü arasındaki etkileşimi denetlemek için IoT Edge cihazınıza gidebilirsiniz.

1. Docker'daki tüm modüllerin çalıştığından emin olun:

   ```cmd/sh
   iotedge list  
   ```

1. Tüm sistem günlüklerini ve ölçüm verilerini görüntüleyin. Stream Analytics modülünün adını kullanın:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

1. Reset komutunu görüntüle algılayıcı günlüklerini görüntüleyerek SimulatedTemperatureSensor 'i etkiler:

   ```cmd/sh
   iotedge logs SimulatedTemperatureSensor
   ```

   Makinenin sıcaklığını 30 saniye boyunca 70 derece ulaşana kadar yavaş bir şekilde izleyebilirsiniz. Bu noktada Stream Analytics modülü bir sıfırlama işlemini tetikler ve makine sıcaklığı 21'e düşer.

   ![Komut çıkışını modül günlüklerine Sıfırla](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki önerilen makaleye geçmeyi planlıyorsanız, oluşturduğunuz kaynaklarla yapılandırmaları tutabilir ve yeniden kullanabilirsiniz. Aynı IoT Edge cihazını test cihazı olarak kullanmaya devam edebilirsiniz.

Aksi takdirde, ücretlerden kaçınmak için bu makalede kullandığınız yerel konfigürasyonları ve Azure kaynaklarını silebilirsiniz.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide IoT Edge cihazınızdan gelen verileri analiz etmek için bir Azure Streaming Analytics işi yapılandırdınız. Ardından bu Azure Stream Analytics modülünü IoT Edge cihazınıza yükleyerek yerel ortamdaki sıcaklık artışını izleme ve buna tepki göstermeye ek olarak toplanan veri akışının buluta gönderilmesini sağladınız. Azure IoT Edge sisteminin işletmeniz için oluşturabileceği ek çözümleri görmek için diğer öğreticilere geçin.

> [!div class="nextstepaction"]
> [Bir Azure Machine Learning modelini modül olarak dağıtma](tutorial-deploy-machine-learning.md)