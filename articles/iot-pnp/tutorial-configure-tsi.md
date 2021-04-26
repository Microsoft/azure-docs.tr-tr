---
title: Öğretici-Azure IoT Tak ve Kullan cihaz telemetrinizi depolamak ve analiz etmek için Azure Time Series Insights kullanma
description: Öğretici-Time Series Insights ortamı ayarlama ve IoT Hub 'ınızı, IoT Tak ve Kullan cihazlarınızdan Telemetriyi görüntülemek ve analiz etmek için bağlayın.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 28cda9fb6997500f6cd7c4c4349635e7b7a36398
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504286"
---
# <a name="tutorial-create-and-configure-a-time-series-insights-gen2-environment"></a>Öğretici: Time Series Insights Gen2 ortamı oluşturma ve yapılandırma

Bu öğreticide, IoT Tak ve Kullan çözümünüz ile tümleştirilecek bir [Azure Time Series Insights Gen2](../time-series-insights/overview-what-is-tsi.md) ortamı oluşturma ve yapılandırma hakkında bilgi edineceksiniz. Nesnelerin İnterneti (IoT) ölçeğinde zaman serisi verilerini toplamak, işlemek, depolamak, sorgulamak ve görselleştirmek için Time Series Insights kullanın.

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Time Series Insights ortamı sağlayın ve IoT Hub 'ınızı akış olay kaynağı olarak bağlayın.
> * [Zaman serisi modelinizi](../time-series-insights/concepts-model-overview.md)yazmak için model eşitleme aracılığıyla çalışın.
> * Sıcaklık denetleyicisi ve termostat cihazları için kullandığınız [dijital TWINS tanım dili (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) örnek model dosyalarını kullanın.

> [!NOTE]
> Time Series Insights ile IoT Tak ve Kullan arasındaki bu tümleştirme önizlemededir. DTDL cihaz modellerinin Time Series Insights zaman serisi modeliyle ne şekilde eşleşbir şekilde değiştirileceği.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Bu noktada şunları yapabilirsiniz:

* Azure IoT Hub 'ı.
* IoT Hub 'ınıza bağlı bir cihaz sağlama hizmeti (DPS) örneği. DPS örneği, IoT Tak ve Kullan cihazınız için ayrı bir cihaz kaydına sahip olmalıdır.
* Tek bileşenden oluşan bir cihazdan veya sanal verileri barındıran bir çoklu bileşen cihazınızla IoT Hub 'ınıza bir bağlantı.

Azure CLı 'yı yerel olarak kurma gereksinimini önlemek için Azure Cloud Shell kullanarak bulut hizmetlerini ayarlayabilirsiniz.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-event-source"></a>Olay kaynağınızı hazırlama

Daha önce oluşturduğunuz IoT Hub 'ı Time Series Insights ortamınızın [olay kaynağıdır](../time-series-insights/concepts-streaming-ingestion-event-sources.md).

> [!IMPORTANT]
> Varolan tüm IoT Hub yollarını devre dışı bırakın. [Yönlendirme](../iot-hub/iot-hub-devguide-messages-d2c.md#routing-endpoints) yapılandırılmış bir IoT Hub 'ı kullanmayla ilgili bilinen bir sorun var. Tüm yönlendirme uç noktalarını geçici olarak devre dışı bırakın. IoT Hub 'ınız Time Series Insights bağlandığında, yönlendirme uç noktalarını yeniden etkinleştirebilirsiniz.

IoT Hub 'ınızda Time Series Insights için benzersiz bir tüketici grubu oluşturun. Aşağıdaki örnekte, öğesini `my-pnp-hub` daha önce kullandığınız IoT Hub 'ının adıyla değiştirin.

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group
```

## <a name="choose-a-time-series-id"></a>Zaman Serisi Kimliği

Time Series Insights ortamınızı sağladığınızda bir *zaman SERISI kimliği* seçmeniz gerekir. Uygun zaman serisi KIMLIĞINI seçmeniz önemlidir. Bu özellik sabittir ve ayarlandıktan sonra değiştirilemez. Bir zaman serisi KIMLIĞI, bir veritabanı bölüm anahtarı gibidir. Zaman serisi KIMLIĞI, zaman serisi modelinize yönelik birincil anahtar olarak davranır. Daha fazla bilgi için bkz. [bir zaman SERISI kimliği seçmek Için en iyi uygulamalar](../time-series-insights/how-to-select-tsid.md).

IoT Tak ve Kullan bir kullanıcı olarak, zaman serisi KIMLIĞINIZ için ve ' den oluşan bir _bileşik anahtar_ belirtin `iothub-connection-device-id` `dt-subject` . IoT Hub 'ı, IoT Tak ve Kullan cihaz KIMLIĞINIZI ve cihaz bileşen adlarını sırasıyla içeren bu sistem özelliklerini ekler.

IoT Tak ve Kullan cihaz modelleriniz Şu anda bileşenleri kullanmasa bile, `dt-subject` gelecekte bileşenleri kullanabilmeniz için bileşik anahtarın bir parçası olarak dahil etmelisiniz. Zaman serisi KIMLIĞINIZ sabit olduğundan, Microsoft bu seçeneğin gelecekte gerekli olması durumunda bu seçeneği etkinleştirmeyi önerir.

> [!NOTE]
> Bu makaledeki örnekler, Çoklu bileşen `TemperatureController` cihazına yöneliktir. Ancak kavramlar, bileşen olmayan cihaz için aynıdır `Thermostat` .

## <a name="provision-your-time-series-insights-environment"></a>Time Series Insights ortamınızı sağlama

Bu bölümde, Azure Time Series Insights Gen2 ortamınızın nasıl sağlanacağı açıklanmaktadır.

Şu komutu çalıştırın:

* Ortamınızın [soğuk deposu](../time-series-insights/concepts-storage.md#cold-store)Için bir Azure depolama hesabı oluşturun. Bu hesap, geçmiş veriler için uzun süreli saklama ve analiz için tasarlanmıştır.
  * Kodunuzda, `mytsicoldstore` soğuk depolama hesabınız için benzersiz bir adla değiştirin.
* Azure Time Series Insights Gen2 ortamı oluşturun. Ortam, süresi yedi gün olan bir Bekletme süresine sahip olan yarı depolama ile oluşturulur. Sınırsız saklama için soğuk depolama hesabı eklenecektir.
  * Kodunuzda, `my-tsi-env` Time Series Insights ortamınız için benzersiz bir adla değiştirin.
  * Kodunuzda, `my-pnp-resourcegroup` Kurulum sırasında kullandığınız kaynak grubunun adıyla değiştirin.
  * Zaman serisi KIMLIĞI özelliği `iothub-connection-device-id, dt-subject` .

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=iothub-connection-device-id type=String --time-series-id-properties name=dt-subject type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

IoT Hub olay kaynağınızı bağlayın. `my-pnp-resourcegroup`, `my-pnp-hub` Ve `my-tsi-env` değerlerini seçtiğiniz değerlerle değiştirin. Aşağıdaki komut, daha önce oluşturduğunuz Time Series Insights için tüketici grubuna başvurur:

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az tsi event-source iothub create --event-source-name iot-hub-event-source --environment-name $env --resource-group $rg --location eastus2 --consumer-group-name tsi-consumer-group --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id --iot-hub-name $iothub
```

[Azure Portal](https://portal.azure.com), kaynak grubunuza gidin ve yeni Time Series Insights ortamınızı seçin. Örneğe genel bakış bölümünde gösterilen **Time Series Insights gezgın URL** 'sine gidin:

![Portala Genel Bakış sayfasının ekran görüntüsü.](./media/tutorial-configure-tsi/view-environment.png)

Gezgin 'de üç örnek görürsünüz:

* &lt;PNP cihaz KIMLIĞINIZ &gt; , thermostat1
* &lt;PNP cihaz KIMLIĞINIZ &gt; , thermostat2
* &lt;PNP cihaz KIMLIĞINIZ &gt; , `null`

> [!NOTE]
> Üçüncü etiket, `TemperatureController` bir cihaz belleği çalışma kümesi gibi kendi kendisinden gelen Telemetriyi temsil eder. Bu bir üst düzey özellik olduğundan, bileşen adı değeri null olur. Daha sonraki bir adımda, bu adı Kullanıcı dostu hale getirebilirsiniz.

![Gezgin 'deki üç örneği gösteren ekran görüntüsü.](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="configure-model-translation"></a>Model çevirisini yapılandırma

Daha sonra, DTDL cihaz modelinizi Azure Time Series Insights varlık modeline çeviolursunuz. Time Series Insights, zaman serisi modeli veri bağlamı için bir anlamsal modelleme aracıdır. Modelde üç çekirdek bileşeni vardır:

* [Zaman serisi model örnekleri](../time-series-insights/concepts-model-overview.md#time-series-model-instances) , zaman serisinin kendi sanal temsilleridir. Örnekler, zaman serisi KIMLIĞINIZ tarafından benzersiz şekilde tanımlanır.
* [Zaman serisi model hiyerarşileri](../time-series-insights/concepts-model-overview.md#time-series-model-hierarchies) , özellik adlarını ve bunların ilişkilerini belirterek örnekleri düzenler.
* [Zaman serisi model türleri](../time-series-insights/concepts-model-overview.md#time-series-model-types) hesaplamalar için [değişkenler](../time-series-insights/concepts-variables.md) veya formüller tanımlamanıza yardımcı olur. Türler belirli bir örnekle ilişkilendirilir.

### <a name="define-your-types"></a>Türlerinizi tanımlama

Önceden tanımlanmış bir modele sahip olmadan Azure Time Series Insights Gen2 'e veri almaya başlayabilirsiniz. Telemetri geldiğinde, zaman serisi KIMLIĞI Özellik değerlerinizin temelinde zaman serisi örneklerini otomatik olarak çözümlemeye çalışır Time Series Insights. Tüm örneklere *Varsayılan tür* atanır. Örneklerinizi doğru şekilde kategorilere ayırmak için el ile yeni bir tür oluşturmanız gerekir.

Aşağıdaki ayrıntılar, cihaz DTDL modellerinizi zaman serisi model türleriniz ile eşitlemeye yönelik en basit yöntemi özetler:

* Dijital ikizi modeli tanımlarınız tür KIMLIĞINIZ olur.
* Tür adı, model adı ya da görünen ad olabilir.
* Model açıklaması türün açıklaması olur.
* Sayısal bir şemaya sahip her telemetri için en az bir tür değişkeni oluşturulur.
  * Değişkenler için yalnızca sayısal veri türleri kullanılabilir, ancak bir değer dönüştürülebileceği başka bir tür olarak gönderilmişse, örneğin gibi bir `"0"` [dönüştürme](/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) işlevi kullanabilirsiniz `toDouble` .
* Değişken adı telemetri adı ya da görünen ad olabilir.
* Zaman serisi Ifade değişkenini tanımladığınızda, tel ve telemetrideki veri türü için telemetri adına bakın.

| DTDL JSON | Zaman serisi model türü JSON | Örnek değer |
|-----------|------------------|-------------|
| `@id` | `id` | `dtmi:com:example:TemperatureController;1` |
| `displayName`    | `name`   |   `Temperature Controller`  |
| `description`  |  `description`  |  `Device with two thermostats and remote reboot.` |
|`contents` dizide| `variables` nesne  | Aşağıdaki örneğe bakın.

![D T D L ile zaman serisi model türünü gösteren ekran görüntüsü.](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

> [!NOTE]
> Bu örnekte üç değişken gösterilmektedir, ancak her tür en fazla 100 değişken içerebilir. Farklı değişkenler gerektiğinde farklı hesaplamalar yapmak için aynı telemetri değerine başvurabilir. Filtrelerin, toplamların ve skaler işlevlerin tam listesi için, bkz. [Time Series Insights Gen2 zaman serisi ifade sözdizimi](/rest/api/time-series-insights/reference-time-series-expression-syntax).

Bir metin Düzenleyicisi açın ve aşağıdaki JSON 'ı yerel sürücünüze kaydedin.

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:TemperatureController;1",
      "name": "Temperature Controller",
      "description": "Device with two thermostats and remote reboot.",
      "variables": {
        "workingSet": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.workingSet.Long, toLong($event.workingSet.Double))"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.temperature.Long, toLong($event.temperature.Double))"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "eventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        }
      }
    }
  ]
}
```

Time Series Insights Gezgini ' nde, sol taraftaki model simgesini seçerek **model** sekmesini açın. **Türler** ' i seçin ve ardından **JSON yükle**' yi seçin:

![JSON karşıya yüklemeyi gösteren ekran görüntüsü.](./media/tutorial-configure-tsi/upload-type.png)

**Dosya Seç**' i seçin, daha önce kaydettiğiniz JSON ' ı seçin ve ardından **karşıya yükle**' yi seçin.

Yeni tanımlanan **sıcaklık denetleyicisi** türünü görürsünüz.

### <a name="create-a-hierarchy"></a>Hiyerarşi oluşturma

Üst öğesi altındaki etiketleri düzenlemek için bir hiyerarşi oluşturun `TemperatureController` . Aşağıdaki basit örnekte tek bir düzey vardır, ancak IoT çözümlerinin genellikle bir kuruluştaki fiziksel ve anlam konumlarına göre etiketleri bağlamak için birçok iç içe geçme düzeyi vardır.

**Hiyerarşiler** ' ı seçin ve ardından **hiyerarşi Ekle**' yi seçin. Ad için, *Device Fleet*' i girin. *Cihaz adı* adlı bir düzey oluşturun. Sonra **Kaydet**'i seçin.

![Hiyerarşinin nasıl ekleneceğini gösteren ekran görüntüsü.](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>Örneklerinizi doğru türe atayın

Ardından, örneklerinizin türünü değiştirirsiniz ve bunları hiyerarşiye yerleştirebilirsiniz.

**Örnekler** sekmesini seçin. Cihazın çalışma kümesini temsil eden örneği bulun ve ardından en sağdaki **Düzenle** simgesini seçin.

![Bir örneği nasıl düzenleyileceğini gösteren ekran görüntüsü.](./media/tutorial-configure-tsi/edit-instance.png)

**Tür** açılır menüsünü açın ve ardından **sıcaklık denetleyicisi**' ni seçin. Aygıtınızla ilişkili tüm üst düzey etiketleri temsil eden örneğin adını güncelleştirmek için *Defaultcomponent <your device name>* girin.

![Örnek türünün nasıl değiştirileceğini gösteren ekran görüntüsü.](./media/tutorial-configure-tsi/change-type.png)

**Kaydet**'i seçmeden önce, önce **örnek alanları** sekmesini seçin ve ardından **Device Fleet**' i seçin. Telemetrisini birlikte gruplandırmak için *\<your device name> -Temp Controller* yazın. Sonra **Kaydet**'i seçin.

![Bir hiyerarşiye örnek atamayı gösteren ekran görüntüsü](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Termostat etiketlerinizi doğru tür ve hiyerarşiye atamak için önceki adımları tekrarlayın.

## <a name="view-your-data"></a>Verilerinizi görüntüleme

Grafik bölmesine dönün ve cihazınızı > **cihaz Fleet** ' i genişletin. **Thermostat1** öğesini seçin, **sıcaklık** değişkenini seçin ve ardından değeri grafiğe **Ekle** ' yi seçin. **Thermostat2** ve **defaultcomponent** **WorkingSet** değeri için aynısını yapın.

![Thermostat2 için örnek türünün nasıl değiştirileceğini gösteren ekran görüntüsü.](./media/tutorial-configure-tsi/charting-values.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> Aralık boyutlandırma ve y ekseni denetimleri de dahil olmak üzere çeşitli grafik seçenekleri hakkında daha fazla bilgi edinmek için bkz. [Azure Time Series Insights Explorer](../time-series-insights/concepts-ux-panels.md).
