---
title: Azure portal Azure IoT Edge için modülleri uygun ölçekte dağıtın
description: IoT Edge cihaz grupları için otomatik dağıtımlar oluşturmak üzere Azure portal kullanın
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: db27a466ca5f1370e8b43ceb472f5deeaba509f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200327"
---
# <a name="deploy-iot-edge-modules-at-scale-using-the-azure-portal"></a>Azure portal kullanarak ölçekli IoT Edge modüllerini dağıtın

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Aynı anda birçok cihaz için devam eden dağıtımları yönetmek üzere Azure portal **Otomatik dağıtım IoT Edge** oluşturun. IoT Edge için otomatik dağıtımlar IoT Hub [otomatik cihaz yönetimi](../iot-hub/iot-hub-automatic-device-management.md) özelliğinin bir parçasıdır. Dağıtımlar birden çok modülü birden çok cihaza dağıtmanızı, modüllerin durumunu ve durumunu izlemenizi ve gerektiğinde değişiklik yapmayı sağlayan dinamik işlemlerdir.

Daha fazla bilgi için bkz. [tek cihazlarda veya ölçekte IoT Edge otomatik dağıtımları anlama](module-deployment-monitoring.md).

## <a name="identify-devices-using-tags"></a>Etiketleri kullanarak cihazları tanımla

Bir dağıtım oluşturabilmeniz için önce hangi cihazların etkilenmesini istediğinizi belirleyebilmelisiniz. Azure IoT Edge cihaz ikizi **etiketleri** kullanarak cihazları tanımlar. Her cihazda, çözümünüz için anlamlı olacak şekilde tanımladığınız birden fazla etiket olabilir.

Örneğin, akıllı binaları bir kampüs yönetiyorsanız, bir cihaza konum, oda türü ve ortam etiketleri ekleyebilirsiniz:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Cihaz ikgörüti ve etiketleri hakkında daha fazla bilgi için bkz. [IoT Hub 'da cihaz TWINS 'ı anlama ve kullanma](../iot-hub/iot-hub-devguide-device-twins.md).

## <a name="create-a-deployment"></a>Dağıtım oluşturma

IoT Edge, senaryonuzu özelleştirmek için kullanabileceğiniz iki farklı türde otomatik dağıtım sağlar. Sistem çalışma zamanı modüllerini ve ek modülleri ve yolları içeren standart bir *dağıtım* oluşturabilirsiniz. Her cihaz yalnızca bir dağıtım uygulayabilir. Ya da sistem çalışma zamanını değil yalnızca özel modüller ve rotalar içeren *katmanlı bir dağıtım* oluşturabilirsiniz. Birçok katmanlı dağıtım, bir cihazda standart dağıtımın üzerine birleştirilebilir. İki otomatik dağıtım türünün birlikte nasıl çalıştığı hakkında daha fazla bilgi için bkz. [tek cihazlarda veya ölçekte IoT Edge otomatik dağıtımları anlama](module-deployment-monitoring.md).

Dağıtım ve katmanlı bir dağıtım oluşturma adımları çok benzerdir. Herhangi bir farklılık aşağıdaki adımlarda çağrılır.

1. [Azure portal](https://portal.azure.com)IoT Hub gidin.
1. Sol bölmedeki menüde **otomatik cihaz yönetimi** altında **IoT Edge** ' yi seçin.
1. Üstteki çubukta **dağıtım oluştur** veya **katmanlı dağıtım oluştur**' u seçin.

Dağıtım oluşturmak için beş adım vardır. Aşağıdaki bölümler her bir adım adım yol gösterir.

>[!NOTE]
>Bu makaledeki adımlarda IoT Edge aracısının ve hub 'ın en son şema sürümü yansıtılacaktır. Şema sürümü 1,1, IoT Edge Version 1.0.10 ile birlikte yayımlanmıştır ve modül başlangıç sırası ve yol önceliği belirleme özelliklerini sunar.
>
>Sürüm 1.0.9 veya daha önceki bir sürümü çalıştıran bir cihaza dağıtıyorsanız, sihirbazın **modüller** adımında, şema sürümü 1,0 ' i kullanmak Için **çalışma zamanı ayarlarını** düzenleyin.

### <a name="step-1-name-and-label"></a>1. Adım: adı ve etiketi

1. Dağıtımınıza 128 küçük harflerden oluşan benzersiz bir ad verin. Boşluklardan ve aşağıdaki geçersiz karakterlerden kaçının: `& ^ [ ] { } \ | " < > /` .
1. Dağıtımlarınızın izlenmesine yardımcı olmak için anahtar-değer çiftleri olarak Etiketler ekleyebilirsiniz. Örneğin, **Hostplatform** ve **Linux** veya **Version** ve **3.0.1**.
1. Sonraki adıma geçmek için **modüller** ' i seçin.

### <a name="step-2-modules"></a>2. Adım: modüller

Bir dağıtıma en çok 50 modül ekleyebilirsiniz. Modül olmadan bir dağıtım oluşturursanız, hedef cihazlardan tüm geçerli modüller kaldırılır.

Dağıtımlarda, IoT Edge Aracısı ve IoT Edge hub modülleri için ayarları yönetebilirsiniz. İki çalışma zamanı modülünü yapılandırmak için **çalışma zamanı ayarları** ' nı seçin. Katmanlı dağıtımda, çalışma zamanı modülleri dahil edilmez, bu nedenle yapılandırılamaz.

Özel kodu bir modül olarak eklemek veya el ile bir Azure hizmet modülünü eklemek için aşağıdaki adımları izleyin:

1. Sayfanın **Container Registry ayarları** bölümünde, modül görüntülerinizi içeren özel kapsayıcı kayıt defterlerine erişim için kimlik bilgilerini sağlayın.
1. Sayfanın **IoT Edge modüller** bölümünde **Ekle**' yi seçin.
1. Açılan menüden üç tür modülden birini seçin:

   * **IoT Edge modülü** -modül adı ve KAPSAYıCı görüntüsü URI 'sini sağlarsınız. Örneğin, örnek SimulatedTemperatureSensor modülünün görüntü URI 'SI `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0` . Modül görüntüsü bir özel kapsayıcı kayıt defterinde depolanıyorsa, görüntüye erişmek için bu sayfadaki kimlik bilgilerini ekleyin.
   * **Market modülü** -Azure Marketi 'nde barındırılan modüller. Bazı Market modülleri ek yapılandırma gerektirir, bu nedenle [Azure marketi IoT Edge modüller](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) listesindeki modül ayrıntılarını gözden geçirin.
   * **Azure Stream Analytics modülü** -bir Azure Stream Analytics iş yüküyle oluşturulan modüller.

1. Gerekirse, dağıtımınıza ek modüller eklemek için adım 2 ve 3 ' ü tekrarlayın.

Bir dağıtıma modül ekledikten sonra, bu adı seçerek **güncelleştirme IoT Edge modülü** sayfasını açabilirsiniz. Bu sayfada modül ayarlarını, ortam değişkenlerini, oluşturma seçeneklerini, başlangıç sırasını ve ikizi modülünü düzenleyebilirsiniz. Market 'ten bir modül eklediyseniz, bu parametrelerin bazıları zaten doldurulmuş olabilir. Kullanılabilir modül ayarları hakkında daha fazla bilgi için bkz. [Modül yapılandırması ve yönetimi](module-composition.md#module-configuration-and-management).

Katmanlı bir dağıtım oluşturuyorsanız, aynı cihazları hedefleyen diğer dağıtımlarda var olan bir modül yapılandırıyorsanız. Modül ikizi diğer sürümlerin üzerine yazmadan güncelleştirmek için, **module Ikizi ayarları** sekmesini açın. Modül ikizi 'ın istenen özellikleri içindeki bir alt bölüm için benzersiz bir adla yeni bir **module Ikizi özelliği** oluşturun (örneğin,) `properties.desired.settings` . Yalnızca alanın içinde Özellikler tanımlarsanız `properties.desired` , daha düşük öncelikli dağıtımlarda tanımlanan modül için istenen özelliklerin üzerine yazılır.

![Katmanlı dağıtım için Module ikizi özelliğini ayarla](./media/how-to-deploy-monitor/module-twin-property.png)

Katmanlı dağıtımlarda modül ikizi yapılandırması hakkında daha fazla bilgi için bkz. [katmanlı dağıtım](module-deployment-monitoring.md#layered-deployment).

Yapılandırılmış bir dağıtım için tüm modülleriniz olduktan sonra Ileri ' yi seçin. üçüncü adıma geçmek için **yollar** .

### <a name="step-3-routes"></a>3. Adım: rotalar

**Rotalar** sekmesinde, iletilerin modüller arasında nasıl geçtiğini tanımlar ve IoT Hub. İletiler ad/değer çiftleri kullanılarak oluşturulur.

Örneğin, bir ad **rotası** ve **/Ileti/ \* $upstream Into** değeri olan bir yol, herhangi bir modülle giden iletileri alır ve bunları IoT Hub 'ınıza gönderir.  

**Öncelik** ve **yaşam süresi** parametreleri, bir yol tanımına dahil ettiğiniz isteğe bağlı parametrelerdir. Priority parametresi, ilk olarak hangi yolların işlenmesi gerektiğini veya hangi yolların son işlenmesi gerektiğini seçmenizi sağlar. Öncelik, 0-9 sayısı ayarlanarak belirlenir, burada 0 üst önceliktir. Yaşam süresi parametresi, bu rotadaki iletilerin işlenmeyi veya kuyruktan kaldırılıncaya kadar ne kadar süreyle tutulması gerektiğini bildirmenize olanak tanır.

Yolların nasıl oluşturulacağı hakkında daha fazla bilgi için bkz. [yolları bildirme](module-composition.md#declare-routes).

**İleri ' yi seçin: ölçümler**.

### <a name="step-4-metrics"></a>4. Adım: ölçümler

Ölçümler, bir cihazın yapılandırma içeriği uygulama sonucu olarak rapor sağlayabileceği çeşitli durumların Özet sayısını sağlar.

1. **Ölçüm adı** için bir ad girin.

1. **Ölçüm ölçütü** için bir sorgu girin. Sorgu IoT Edge hub modülünün ikizi [bildirilen özelliklerini](module-edgeagent-edgehub.md#edgehub-reported-properties)temel alır. Ölçüm, sorgu tarafından döndürülen satır sayısını temsil eder.

   Örnek:

   ```sql
   SELECT deviceId FROM devices
     WHERE properties.reported.lastDesiredStatus.code = 200
   ```

**İleri ' yi seçin: hedef cihazlar**.

### <a name="step-5-target-devices"></a>5. Adım: hedef cihazlar

Bu dağıtımı alması gereken belirli cihazları hedeflemek için cihazlarınızdaki Etiketler özelliğini kullanın.

Birden çok dağıtım aynı cihazı hedefleyebilir, bu da her dağıtıma bir öncelik numarası vermeniz gerekir. Herhangi bir çakışma varsa, en yüksek önceliğe sahip dağıtım (daha büyük değerler daha yüksek öncelik gösterir) kazanır. İki dağıtımda aynı öncelik numarası varsa, en son WINS 'nin oluşturulduğu bir değer vardır.

Birden çok dağıtım aynı cihazı hedefliyorsanız, yalnızca yüksek önceliğe sahip olan bir işlem uygulanır. Birden çok katmanlı dağıtım aynı cihazı hedeflerse, hepsi uygulanır. Ancak, herhangi bir özellik yinelendiğinden, aynı ada sahip iki yol varsa, daha yüksek öncelikli katmanlı dağıtımdan biri geri kalanı geçersiz kılar.

Bir cihazın hedeflediği katmanlı dağıtımlar, uygulanması için temel dağıtımdan daha yüksek önceliğe sahip olmalıdır.

1. Dağıtım **önceliği** için pozitif bir tamsayı girin.
1. Bu dağıtıma hangi cihazların hedeflenceğini belirleyen bir **hedef koşul** girin. Bu koşul, Device ikizi etiketlerine veya Device ikizi bildirilen özelliklerine dayalıdır ve ifade biçimiyle eşleşmelidir. Örneğin `tags.environment='test'` veya `properties.reported.devicemodel='4000x'` olabilir.

Ileri ' yi seçin: son adımla gezinmek için **+ Oluştur** ' u seçin.

### <a name="step-6-review-and-create"></a>6. Adım: Inceleme ve oluşturma

Dağıtım bilgilerinizi gözden geçirin ve ardından **Oluştur**' u seçin.

Dağıtımınızı izlemek için bkz. [IoT Edge dağıtımlarını izleme](how-to-monitor-iot-edge-deployments.md).

## <a name="modify-a-deployment"></a>Bir dağıtımı değiştirme

Bir dağıtımı değiştirdiğinizde, değişiklikler hedeflenen tüm cihazlara hemen çoğaltılır. Mevcut bir dağıtım için aşağıdaki ayarları ve özellikleri değiştirebilirsiniz:

* Hedef koşullar
* Özel ölçümler
* Etiketler
* Etiketler
* İstenen özellikler

### <a name="modify-target-conditions-custom-metrics-and-labels"></a>Hedef koşulları, özel ölçümleri ve etiketleri değiştirme

1. IoT Hub 'ınızda sol bölme menüsünden **IoT Edge** ' yi seçin.
1. **IoT Edge dağıtımları** sekmesini seçin ve ardından yapılandırmak istediğiniz dağıtımı seçin.
1. **Hedef koşul** sekmesini seçin. **Hedef koşulu** hedeflenen cihazları hedefleyecek şekilde değiştirin. **Önceliği** de ayarlayabilirsiniz.  **Kaydet**’i seçin.

    Hedef koşulu güncelleştirirseniz, aşağıdaki güncelleştirmeler gerçekleşir:

    * Bir cihaz eski hedef koşulunu karşılamıyorsa, ancak yeni hedef koşulunu karşılıyorsa ve bu dağıtım bu cihaz için en yüksek önceliktir, bu dağıtım cihaza uygulanır.
    * Şu anda bu dağıtımı çalıştıran bir cihaz artık hedef koşulu karşılamıyorsa, bu dağıtımı kaldırır ve sonraki en yüksek öncelikli dağıtımı alır.
    * Şu anda bu dağıtımı çalıştıran bir cihaz artık hedef koşulunu karşılamıyorsa ve diğer dağıtımların hedef koşulunu karşılamıyorsa, cihazda hiçbir değişiklik gerçekleşmez. Cihaz geçerli durumunda mevcut modüllerini çalıştırmaya devam eder, ancak artık bu dağıtımın bir parçası olarak yönetilmez. Başka bir dağıtımın hedef koşulunu karşılıyorsa, bu dağıtımı kaldırır ve yenisini alır.

1. **Ölçümler** sekmesini seçin ve **ölçümleri Düzenle** düğmesine tıklayın. Örnek sözdizimini kılavuz olarak kullanarak özel ölçümleri ekleyin veya değiştirin. **Kaydet**’i seçin.

    ![Bir dağıtımda özel ölçümleri düzenleme](./media/how-to-deploy-monitor/metric-list.png)

1. **Etiketler** sekmesini seçin ve istediğiniz değişiklikleri yapın ve **Kaydet**' i seçin.

## <a name="delete-a-deployment"></a>Bir dağıtımı silme

Bir dağıtımı sildiğinizde, dağıtılan tüm cihazlar bir sonraki en yüksek öncelikli dağıtımı alır. Cihazlarınız başka bir dağıtımın hedef koşulunu karşılamıyorsa, dağıtım silindiğinde modüller kaldırılmaz.

1. [Azure Portal](https://portal.azure.com) oturum açın ve IoT Hub gidin.
1. **IoT Edge** seçin.
1. **IoT Edge dağıtımları** sekmesini seçin.

   ![IoT Edge dağıtımlarını görüntüle](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. Silmek istediğiniz dağıtımı seçmek için onay kutusunu kullanın.
1. **Sil**’i seçin.
1. Bir istem, bu işlemin bu dağıtımı silineceğini ve tüm cihazlar için önceki duruma geri dönmeyeceğini size bildirir. Düşük önceliğe sahip bir dağıtım uygulanacaktır. Başka bir dağıtım hedeflendiyse hiçbir modül kaldırılmaz. Cihazınızdan tüm modülleri kaldırmak istiyorsanız, sıfır modüllerle bir dağıtım oluşturun ve aynı cihazlara dağıtın. Devam etmek için **Evet**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

[IoT Edge cihazlara modül dağıtma](module-deployment-monitoring.md)hakkında daha fazla bilgi edinin.