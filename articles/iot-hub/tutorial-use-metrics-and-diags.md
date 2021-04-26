---
title: Öğretici-Azure IoT Hub ile ölçümleri ve günlükleri ayarlama ve kullanma
description: Öğretici-Azure IoT Hub ile ölçümleri ve günlükleri ayarlamayı ve kullanmayı öğrenin. Bu, hub 'ınızın sahip olabileceği sorunları tanılamanıza yardımcı olması için analiz edilecek verileri sağlar.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 10/29/2020
ms.author: robinsh
ms.custom:
- mvc
- mqtt
- devx-track-azurecli
- devx-track-csharp
ms.openlocfilehash: 96a9f7c50f3e30d86497c7a612ddda248db3f703
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865702"
---
# <a name="tutorial-set-up-and-use-metrics-and-logs-with-an-iot-hub"></a>Öğretici: IoT Hub ile ölçümleri ve günlükleri ayarlama ve kullanma

Azure Izleyici 'yi kullanarak IoT Hub 'ınız için, çözümünüzün işlemlerini izlemenize ve sorunları ortaya çıkabilecek sorunları gidermenize yardımcı olan ölçümler ve Günlükler toplayabilirsiniz. Bu makalede ölçümleri temel alarak grafikler oluşturmayı, ölçümler üzerinde tetiklenen uyarılar oluşturmayı, Azure Izleyici günlüklerine IoT Hub işlemler ve hataların nasıl gönderileceğini ve günlüklerin hatalara karşı nasıl kontrol leneceğini göreceksiniz.

Bu öğreticide, IoT Hub 'ına ileti göndermek için [.net telemetri gönderme hızlı](quickstart-send-telemetry-dotnet.md) başlangıcı ' ndan Azure örneği kullanılmaktadır. İletileri göndermek için her zaman bir cihaz ya da başka bir örnek kullanabilirsiniz, ancak bazı adımları uygun şekilde değiştirmeniz gerekebilir.

Azure Izleyici kavramlarıyla ilgili bazı benzerlik, Bu öğreticiye başlamadan önce yararlı olabilir. Daha fazla bilgi için bkz. [izleyici IoT Hub](monitor-iot-hub.md). IoT Hub tarafından yayılan ölçümler ve kaynak günlükleri hakkında daha fazla bilgi edinmek için bkz. [izleme veri başvurusu](monitor-iot-hub-reference.md).

Bu öğreticide, aşağıdaki görevleri gerçekleştireceksiniz:

> [!div class="checklist"]
>
> * Azure CLı kullanarak bir IoT Hub 'ı oluşturun, sanal cihazı kaydedin ve bir Log Analytics çalışma alanı oluşturun.  
> * Log Analytics çalışma alanındaki Azure Izleyici günlüklerine IoT Hub bağlantıları ve cihaz telemetri kaynak günlükleri gönderin.
> * Ölçüm Gezgini 'ni kullanarak seçili ölçümleri temel alan bir grafik oluşturun ve panonuza sabitleyin.
> * Önemli koşullar gerçekleştiğinde e-postayla bildirim alabilmeniz için ölçüm uyarıları oluşturun.
> * IoT Hub 'a ileti gönderen bir IoT cihazının benzetimini yapan bir uygulamayı indirip çalıştırın.
> * Koşullarınız gerçekleştiğinde uyarıları görüntüleyin.
> * Panonuzda ölçüm grafiğini görüntüleyin.
> * Azure Izleyici günlüklerinde IoT Hub hataları ve işlemleri görüntüleyin.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

- Geliştirme makinenizde .NET Core SDK 2,1 veya üzeri bir sürümü gerekir. [.NET](https://dotnet.microsoft.com/download)’ten birden fazla platform için .NET Core SDK’sını indirebilirsiniz.

  Aşağıdaki komutu kullanarak geliştirme makinenizde geçerli C# sürümünü doğrulayabilirsiniz:

  ```cmd/sh
  dotnet --version
  ```

- Posta alan bir e-posta hesabı.

- Güvenlik duvarınızdaki 8883 numaralı bağlantı noktasını açık olduğundan emin olun. Bu öğreticideki cihaz örneği, 8883 numaralı bağlantı noktası üzerinden iletişim kuran MQTT protokolünü kullanır. Bu bağlantı noktası, bazı kurumsal ve eğitim ağ ortamlarında engellenebilir. Bu sorunu geçici olarak çözmek için daha fazla bilgi ve IoT Hub bkz. [bağlanma (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="set-up-resources"></a>Kaynakları ayarlama

Bu öğreticide, bir IoT Hub, Log Analytics çalışma alanı ve sanal bir IoT cihazı gerekir. Bu kaynaklar Azure CLI veya Azure PowerShell kullanılarak oluşturulabilir. Tüm kaynaklar için aynı kaynak grubunu ve konumunu kullanın. Öğreticiyi tamamladığınızda, kaynak grubunu silerek her şeyi tek bir adımda kaldırabilirsiniz.

Gerekli adımlar aşağıda verilmiştir.

1. Bir [kaynak grubu](../azure-resource-manager/management/overview.md)oluşturun.

2. IoT Hub 'ı oluşturun.

3. Log Analytics çalışma alanı oluşturma.

4. IoT Hub 'ınıza ileti gönderen sanal cihaz için bir cihaz kimliği kaydedin. Sanal cihazı yapılandırmak için kullanılacak cihaz bağlantı dizesini kaydedin.

### <a name="set-up-resources-using-azure-cli"></a>Azure CLı kullanarak kaynakları ayarlama

Bu betiği kopyalayıp Cloud Shell'e yapıştırın. Zaten oturum açmış olduğunuzu varsayarak, betiği bir kerede bir satır olmak üzere çalıştırır. Komutların bazılarının yürütülmesi biraz zaman alabilir. Yeni kaynaklar *contosoresources* kaynak grubunda oluşturulur.

Bazı kaynakların adı Azure genelinde benzersiz olmalıdır. Betik, işleviyle birlikte rastgele bir değer oluşturur `$RANDOM` ve bunu bir değişkende depolar. Bu kaynaklar için, komut dosyası bu rastgele değeri kaynağın temel adına ekler ve kaynak adı benzersiz hale getirir.

Abonelik başına yalnızca bir ücretsiz IoT Hub 'ına izin verilir. Aboneliğinizde zaten ücretsiz bir IoT Hub 'ınız varsa, betiği çalıştırmadan önce onu silin veya ücretsiz IoT Hub 'ınızı veya standart ya da temel katmanı kullanan bir IoT Hub kullanarak betiği değiştirin.

Betik, IoT Hub 'ın adını, Log Analytics çalışma alanının adını ve kaydoldığı cihaz için bağlantı dizesini yazdırır. Bu makalede daha sonra ihtiyacınız olacak şekilde bunları aklınızda olduğunuzdan emin olun.

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-iot

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotDeviceName=Contoso-Test-Device
randomValue=$RANDOM

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier. Partition count must be 2.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --partition-count 2 \
    --sku F1 --location $location

# The Log Analytics workspace name must be globally unique, so add a random number to the end.
workspaceName=contoso-la-workspace$randomValue
echo "Log Analytics workspace name = " $workspaceName


# Create the Log Analytics workspace
az monitor log-analytics workspace create --resource-group $resourceGroup \
    --workspace-name $workspaceName --location $location

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the primary connection string for the device identity, then copy it to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show-connection-string --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>Cihaz kimliğini oluştururken şu hatayı alabilirsiniz: *IoT Hub ContosoTestHub ilke ıothubowner için anahtar bulunamadı*. Bu hatayı onarmak için Azure CLı IoT uzantısını güncelleştirin ve ardından komut dosyasında son iki komutu yeniden çalıştırın. 
>
>Uzantıyı güncelleştirme komutu aşağıda verilmiştir. Cloud Shell örneğiniz içinde bu komutu çalıştırın.
>
>```cli
>az extension update --name azure-iot
>```

## <a name="collect-logs-for-connections-and-device-telemetry"></a>Bağlantılar ve cihaz telemetrisi için günlükleri toplayın

IoT Hub birkaç işlem kategorisi için kaynak günlüklerini yayar; Ancak, bu günlükleri görüntülemeniz için bir hedefe göndermek üzere bir tanılama ayarı oluşturmanız gerekir. Bu tür bir hedef, Log Analytics çalışma alanında toplanan Azure Izleyici günlüklerdir. IoT Hub kaynak günlükleri farklı kategorilerde gruplandırılır. Tanılama ayarında Azure Izleyici günlüklerine hangi kategorilerin gönderilmesini istediğinizi seçebilirsiniz. Bu makalede, bağlantılar ve cihaz telemetrisi ile ilgili olarak oluşan işlemler ve hatalar için günlük toplayacağız. IoT Hub için desteklenen kategorilerin tam listesi için bkz. [IoT Hub kaynak günlükleri](monitor-iot-hub-reference.md#resource-logs).

Azure Izleyici günlüklerine IoT Hub kaynak günlükleri göndermek üzere bir tanılama ayarı oluşturmak için aşağıdaki adımları izleyin:

1. İlk olarak, portalda hub 'ınız yoksa, **kaynak grupları** ' nı seçin ve contosoresources kaynak grubunu seçin. Görünen kaynak listesinden IoT Hub 'ınızı seçin.

1. IoT Hub dikey penceresindeki **izleme** bölümünü arayın. **Tanılama ayarları**' nı seçin. Ardından **Tanılama ayarı Ekle**' yi seçin.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/open-diagnostic-settings.png" alt-text="Izleme bölümündeki tanılama ayarlarını vurgulayan ekran görüntüsü.":::

1. **Tanılama ayarı** bölmesinde, "bağlantıları ve telemetri günlüklere gönder" gibi açıklayıcı bir ad verin.

1. **Kategori ayrıntıları**' nın altında **Bağlantılar** ve **cihaz telemetrisi**' ni seçin.

1. **Hedef ayrıntıları** altında **Log Analytics gönder**' i seçin ve ardından Log Analytics çalışma alanı seçicisini kullanarak daha önce not ettiğiniz çalışma alanını seçin. İşiniz bittiğinde, tanılama ayarı aşağıdaki ekran görüntüsüne benzer görünmelidir:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/add-diagnostic-setting.png" alt-text="Son tanılama günlüğü ayarlarını gösteren ekran görüntüsü.":::

1. Ayarları kaydetmek için **Kaydet**’i seçin. **Tanılama ayarı** bölmesini kapatın. Yeni ayarınızı Tanılama ayarları listesinde görebilirsiniz.

## <a name="set-up-metrics"></a>Ölçümleri ayarlama

Şimdi, izlemek istediğiniz ölçümleri görüntüleyen bir grafik oluşturmak için ölçüm Gezgini 'ni kullanacağız. Bu grafiği Azure portal varsayılan panonuza sabitleyebilirsiniz.

1. IoT Hub 'ınızın sol bölmesinde, **izleme** bölümünde **ölçümler** ' i seçin.

1. Ekranın üst kısmında **son 24 saat (otomatik)** seçeneğini belirleyin. Görüntülenen açılır menüde **zaman aralığı** için **son 4 saat** ' i seçin, **zaman parçalı yapısını** **1 dakika** olarak ayarlayın ve **saat gösterme** için **Yerel** ' i seçin. Bu ayarları kaydetmek için **Uygula** ' yı seçin. Bu ayar şu anda **Yerel Saat deyin: son 4 saat (1 dakika)**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-select-time-range.png" alt-text="Ölçüm zaman ayarlarını gösteren ekran görüntüsü.":::

1. Grafik üzerinde IoT Hub 'ınız için kapsamlı olarak görüntülenmiş bir kısmi ölçüm ayarı vardır. **Kapsam** ve **ölçüm ad alanı** değerlerini varsayılan değerlerinde bırakın. **Ölçüm** ayarını seçin ve "Telemetri" yazın ve açılan listeden **gönderilen telemetri iletilerini** seçin. **Toplama** , otomatik olarak **Sum** olarak ayarlanacak. Grafiğinizin başlığının de değişdiğine dikkat edin.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-telemetry-messages-sent.png" alt-text="Grafiğe ölçüm gönderilen telemetri iletileri eklemeyi gösteren ekran görüntüsü.":::

1. Şimdi grafiğe başka bir ölçüm eklemek için **ölçüm Ekle** ' yi seçin. **Ölçüm** altında, **kullanılan toplam ileti sayısını** seçin. **Toplama** , otomatik olarak **AVG** olarak ayarlanacak. Grafik başlığının bu ölçümü içerecek şekilde değiştiğini yeniden unutmayın.

   Artık ekranınızda, *gönderilen telemetri iletileri* için küçültülmüş olan ölçüm ve ayrıca *kullanılan toplam ileti sayısı* için yeni ölçüm gösterilmektedir.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used.png" alt-text="Grafik için kullanılan ölçüm toplam ileti sayısını gösteren ekran görüntüsü.":::

1. Grafiğin sağ üst kısmında **panoya sabitle**' yi seçin.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used-pin.png" alt-text="Panoya sabitle düğmesini vurgulayan ekran görüntüsü.":::

1. **Panoya sabitle** bölmesinde **var olan** sekmeyi seçin. **Özel** ' i seçin ve ardından Pano açılır listesinden **Pano** ' yı seçin. Son olarak, Azure portal grafiği varsayılan panonuza sabitlemek için **sabitle** ' yi seçin. Grafiğinizi bir panoya sabitleyemez, Ölçüm Gezgini 'nden çıktığınızda ayarlarınız korunmaz.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/pin-to-dashboard.png" alt-text="Panoya sabitle ayarlarını gösteren ekran görüntüsü.":::

## <a name="set-up-metric-alerts"></a>Ölçüm uyarılarını ayarlama

Şimdi, gönderilen iki ölçüm *telemetri iletisi* ve *kullanılan toplam ileti sayısı* için tetiklenecek uyarılar ayarlayacağız.

*Gönderilen telemetri iletileri* , ileti aktarım hızını izlemek ve daralmamak üzere izlemek için iyi bir ölçümdür. Ücretsiz katmanda bir IoT Hub için azaltma sınırı 100 ileti/sn 'dir. Tek bir cihazla, bu tür bir işleme ulaşamayacak, bunun yerine, ileti sayısı 5 dakikalık bir dönemde 1000 ' i aşarsa tetiklenecek uyarıyı ayarlayacağız. Üretimde, sinyali katman, sürüm ve IoT Hub 'ınızın birim sayısına göre daha önemli bir değere ayarlayabilirsiniz.

*Kullanılan toplam* ileti sayısı, kullanılan günlük ileti sayısını izler. Bu ölçüm her gün 00:00 UTC 'yi sıfırlar. Günlük kotayı belirli bir eşiğin ötesinde aşarsanız IoT Hub artık iletileri kabul etmez. Ücretsiz katmanda bir IoT Hub için günlük ileti kotası 8000 olur. Toplam ileti sayısı, kotanın 4000, %50 ' i aşarsa tetiklenecek uyarıyı ayarlayacağız. Uygulamada, büyük olasılıkla bu yüzdeyi daha yüksek bir değere ayarlamanız gerekir. Günlük kota değeri, IoT Hub 'ınızın katmanına, sürümüne ve birim sayısına bağlıdır.

IoT Hub ile kota ve azaltma limitleri hakkında daha fazla bilgi için bkz. [Kotalar ve azaltma](iot-hub-devguide-quotas-throttling.md).

Ölçüm uyarılarını ayarlamak için:

1. Azure portal ' de IoT Hub 'ınıza gidin.

1. **İzleme** altında **Uyarılar**' ı seçin. Ardından **Yeni uyarı kuralı**' nı seçin.  **Uyarı kuralı oluştur** bölmesi açılır.

    :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-pane.png" alt-text="Uyarı kuralı oluştur bölmesini gösteren ekran görüntüsü.":::

    **Uyarı kuralı oluştur** bölmesinde dört bölüm vardır:

    * **Kapsam** zaten IoT Hub 'ınıza ayarlanmış, bu nedenle bu bölümü tek başına bırakıyoruz.
    * **Koşul** , uyarının tetikleyeceği sinyali ve koşulları ayarlar.
    * **Eylemler** , uyarı tetiklendiğinde ne olacağını yapılandırır.
    * **Uyarı kuralı ayrıntıları** , uyarı için bir ad ve açıklama ayarlamanıza olanak sağlar.

1. Önce uyarının tetikleyeceği koşulu yapılandırın.

    1. **Koşul** bölümünde **Koşul Ekle**' yi seçin. **Sinyal mantığını Yapılandır** bölmesinde, arama kutusuna "Telemetri" yazın ve **gönderilen telemetri iletileri**' ni seçin.

       :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-telemetry-messages-sent.png" alt-text="Ölçüm seçmeyi gösteren ekran görüntüsü.":::

    1. **Sinyal mantığını Yapılandır** bölmesinde, **Uyarı mantığı** altında aşağıdaki alanları ayarlayın veya onaylayın (grafiği yoksayabilirsiniz):

       **Eşik**:  *statik*.

       **İşleç**: *büyüktür*.

       **Toplama türü**: *Toplam*.

       **Eşik değeri**: 1000.

       **Toplama ayrıntı düzeyi (nokta)**: *5 dakika*.

       **Değerlendirme sıklığı**: *1 dakikada* bir

        :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-set-conditions.png" alt-text="Uyarı koşulları ayarlarını gösteren ekran görüntüsü.":::

       Bu ayarlar, sinyali 5 dakikalık bir dönemdeki ileti sayısını toplam olarak ayarlar. Bu toplam dakikada değerlendirilir ve önceki 5 dakikalık toplam 1000 mesajı aşarsa, uyarı tetiklenir.

       Sinyal mantığını kaydetmek için **bitti** ' yi seçin.

1. Uyarı için eylemi şimdi yapılandırın.

    1. **Uyarı kuralı oluştur** bölmesine geri dönün, **Eylemler** altında **eylem grupları Ekle**' yi seçin. **Bu uyarı kuralına iliştirilecek bir eylem grubu seçin** sayfasında, **eylem grubu oluştur**' u seçin.

    1. **Eylem grubu oluştur** bölmesindeki **temel bilgiler** sekmesinde, eylem grubunuza bir ad ve görünen ad verin.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-basics.png" alt-text="Eylem Oluştur Grup bölmesinin temelleri sekmesini gösteren ekran görüntüsü.":::

    1. **Bildirimler** sekmesini seçin. **Bildirim türü** için, açılan listeden **e-posta/SMS iletisi/gönderim/ses** ' i seçin. **E-posta/SMS ileti/gönderim/ses** bölmesi açılır.

    1. **E-posta/SMS ileti/gönderim/ses** bölmesinde e-posta ' yı seçin ve e-posta adresinizi girip **Tamam**' ı seçin.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/set-email-address.png" alt-text="E-posta adresi ayarını gösteren ekran görüntüsü.":::

    1. **Bildirimler** bölmesine geri dönüp bildirim için bir ad girin.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-notification-complete.png" alt-text="Tamamlandı bildirimleri bölmesini gösteren ekran görüntüsü.":::

    1. Seçim **Eylemler** sekmesini ve ardından **eylem türü** açılan listesini seçerseniz, bir uyarıyla tetikleyebileceğiniz eylem türlerini görebilirsiniz. Bu makalede yalnızca bildirimleri kullanacağız, bu sayede ayarları yoksayabilirsiniz.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/action-types.png" alt-text="Eylemler bölmesinde bulunan eylem türlerini gösteren ekran görüntüsü.":::

    1. **İnceleme ve oluşturma** sekmesini seçin, ayarlarınızı doğrulayın ve **Oluştur**' u seçin.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-review-and-create.png" alt-text="Inceleme ve oluşturma bölmesini gösteren ekran görüntüsü.":::

    1. **Uyarı kuralı oluştur** bölmesine geri döndüğünüzde, uyarı için eylemlere yeni eylem grubunuzun eklendiğine dikkat edin.  

1. Son olarak, uyarı kuralı ayrıntılarını yapılandırın ve uyarı kuralını kaydedin.

    1. Uyarı kuralı **Oluştur** bölmesindeki uyarı kuralı ayrıntıları ' nın altında, uyarılarınız için bir ad ve açıklama girin; Örneğin, "5 dakikadan fazla 1000 ileti varsa uyarır". **Oluşturma sonrasında uyarı kuralını etkinleştir** ' in işaretli olduğundan emin olun. Tamamlanan uyarı kuralınız bu ekran görüntüsüne benzer şekilde görünür.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-final.png" alt-text="Tamamlandı uyarı kuralı oluştur bölmesini gösteren ekran görüntüsü.":::

    1. Yeni kuralınızı kaydetmek için **Uyarı kuralı oluştur** ' u seçin.

1. Şimdi, *kullanılan toplam ileti sayısı* için başka bir uyarı ayarlayın. Bu ölçüm, kullanılan ileti sayısı IoT Hub 'ının günlük kotasına yaklaştığı zaman, IoT Hub 'ın iletileri reddetbaşlayacağı bir uyarı göndermek istiyorsanız yararlı olur. Aşağıdaki farklılıklarla, daha önce yaptığınız adımları izleyin.

    * **Sinyal mantığını Yapılandır** bölmesindeki sinyal Için **kullanılan toplam ileti sayısını** seçin.

    * **Sinyal mantığını Yapılandır** bölmesinde, aşağıdaki alanları ayarlayın veya onaylayın (grafiği yoksayabilirsiniz):

       **Eşik**:  *statik*.

       **İşleç**: *büyüktür*.

       **Toplama türü**: *en fazla*.

       **Eşik değeri**: 4000.

       **Toplama ayrıntı düzeyi (nokta)**: *1 dakika*.

       **Değerlendirme sıklığı**: *1 dakikada* bir

       Bu ayarlar, ileti sayısı 4000 ' e ulaştığında, sinyali tetikme olarak ayarlar. Ölçüm her dakikada değerlendirilir.

    * Uyarı kuralınız için eylemi belirttiğinizde, daha önce oluşturduğunuz eylem grubunu seçmeniz yeterlidir.

    * Uyarı ayrıntıları için daha önce yaptığından farklı bir ad ve açıklama seçin.

1. IoT Hub 'ınızın sol bölmesinde **izleme** altında **Uyarılar**' ı seçin. Şimdi **Uyarılar** bölmesinin en üstündeki menüdeki **Uyarı kurallarını yönet** ' i seçin. **Kurallar** bölmesi açılır. Şimdi iki uyarı görmeniz gerekir:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/rules-management.png" alt-text="Yeni uyarı kurallarıyla kurallar bölmesini gösteren ekran görüntüsü.":::

1. **Kurallar** bölmesini kapatın.

Bu ayarlarla bir uyarı tetiklenir ve 5 dakikalık bir zaman dilimi içinde 1000 ' den fazla ileti gönderildiğinde ve ayrıca kullanılan toplam ileti sayısı 4000 ' i aşarsa (ücretsiz katmanda bir IoT Hub 'ı için günlük kotanın %50% ' si) bir e-posta bildirimi alırsınız.

## <a name="run-the-simulated-device-app"></a>Sanal cihaz uygulamasını çalıştırma

[Kaynakları ayarla](#set-up-resources) bölümünde, IoT cihazını kullanarak benzetim yapmak için kullanılacak bir cihaz kimliği kaydettiniz. Bu bölümde, bir IoT Hub cihazdan buluta iletiler gönderen bir cihaza benzetim yapan bir .NET konsol uygulaması indirirler, bu iletileri IoT Hub 'ınıza gönderecek şekilde yapılandırabilir ve sonra çalıştırmalısınız.

> [!IMPORTANT]
>
> Uyarıların, IoT Hub tarafından tam olarak yapılandırılması ve etkinleştirilmesi 10 dakika kadar sürebilir. Son uyarınızı yapılandırdığınız ve sanal cihaz uygulamasının çalıştırıldığı zaman arasında en az 10 dakika bekleyin.

[IoT Cihaz Simülasyonu](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) çözümünü indirin. Bu bağlantı, içindeki çeşitli uygulamalarla bir depoyu indirir; Aradığınız biri IoT-Hub/Quickbaşlangıçların/benzetimli-Device/' da bulunur.

1. Yerel bir Terminal penceresinde, çözümün kök klasörüne gidin. Daha sonra **iot-hub\Quickstarts\simulated-device** klasörüne gidin.

1. **SimulatedDevice.cs** dosyasını, istediğiniz bir metin düzenleyicide açın.

    1. Değişkenin değerini, `s_connectionString` kaynak ayarlamak için betiği çalıştırdığınızda not ettiğiniz cihaz bağlantı dizesiyle değiştirin.

    1. Yönteminde, `SendDeviceToCloudMessagesAsync` `Task.Delay` 1 saniyeden 0,001 saniyeye ileti gönderme arasındaki süreyi azaltan 1000 ' dan 1 ' e değiştirin. Bu gecikmeyi kısaltaştırma, gönderilen ileti sayısını artırır. (Saniye başına 100 ileti ücreti göndermeyecektir.)

        ```csharp
        await Task.Delay(1);
        ```

    1. **SimulatedDevice. cs**' ye yaptığınız değişiklikleri kaydedin.

1. Yerel Terminal penceresinde, sanal cihaz uygulaması için gerekli paketleri yüklemek üzere aşağıdaki komutu çalıştırın:

    ```cmd/sh
    dotnet restore
    ```

1. Yerel terminal penceresinde, aşağıdaki komutu çalıştırarak simülasyon cihazı uygulamasını derleyip çalıştırın:

    ```cmd/sh
    dotnet run
    ```

    Aşağıdaki ekran görüntüsünde, simülasyon cihazı uygulaması, IoT hub’ınıza telemetri gönderdiğinde oluşan çıktı gösterilmektedir:

    :::image type="content" source="media/tutorial-use-metrics-and-diags/simulated-device-output.png" alt-text="Sanal cihaz çıkışını gösteren ekran görüntüsü.":::

Uygulamanın en az 10-15 dakika boyunca çalışmasına izin verin. İdeal olarak, ileti göndermeyi durdurmadan (yaklaşık 20-30 dakika) çalışmasına izin verin. Bu durum, IoT Hub 'ınız için günlük ileti kotasını aştığınız ve daha fazla ileti kabul etmediği zaman meydana gelir.

> [!NOTE]
> Cihaz uygulamasını, ileti göndermeyi başlattıktan sonra genişletilmiş bir süre boyunca çalışır durumda bırakırsanız, bir özel durum alabilirsiniz. Bu özel durumu güvenle yoksayabilir ve uygulama penceresini kapatabilirsiniz.

## <a name="view-metrics-chart-on-your-dashboard"></a>Panonuzda ölçüm grafiğini görüntüleme

1. Azure portal sol üst köşesinde Portal menüsünü açın ve ardından **Pano**' yı seçin.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/select-dashboard.png" alt-text="Panonuzun nasıl seçdiğinin ekran görüntüsü.":::

1. Daha önce sabitlediğiniz grafiği bulun ve genişletmek için Grafik verilerinin dışındaki kutucukta herhangi bir yere tıklayın. Bu, gönderilen telemetri iletilerini ve grafikte kullanılan toplam ileti sayısını gösterir. En son sayılar grafiğin en altında görünür. Belirli saatlerin ölçüm değerlerini görmek için imleci grafiğe taşıyabilirsiniz. Ayrıca, grafiğin en üstündeki zaman değerini ve ayrıntı düzeyini değiştirerek verilerin ölçeğini daraltın veya bir zaman dilimine göre verileri genişletebilirsiniz.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-on-dashboard-last-hour.png" alt-text="Ölçüm grafiğini gösteren ekran görüntüsü.":::

   Bu senaryoda, sanal cihazın ileti işleme, IoT Hub iletilerini kısıtlayacak kadar büyük değildir. Gerçekten azaltmayı içeren bir senaryoda, gönderilen telemetri iletilerinin sınırlı bir süre için IoT Hub 'ınız için kısıtlama sınırını aşmadığını görebilirsiniz. Bu, veri bloğu trafiğine uyum sağlar. Ayrıntılar için bkz. [trafik şekillendirme](iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="view-the-alerts"></a>Uyarıları görüntüleme

Gönderilen ileti sayısı, uyarı kurallarında ayarladığınız limitleri aştığında, e-posta uyarıları almaya başlayabilirsiniz.

Etkin bir uyarı olup olmadığını görmek için IoT Hub 'ınızın sol bölmesinde **izleme** altında **Uyarılar** ' ı seçin. **Uyarılar** bölmesi, belirtilen zaman aralığı için önem derecesine göre sıralanmış olarak tetiklenen uyarı sayısını gösterir.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-alerts.png" alt-text="Uyarı özetini gösteren ekran görüntüsü.":::

Önem derecesi sev 3 için satırı seçin. **Tüm uyarılar** bölmesi açılır ve tetiklenen sev 3 uyarılarını listeler.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-all-alerts.png" alt-text="Tüm uyarılar bölmesini gösteren ekran görüntüsü.":::

Uyarı ayrıntılarını görmek için uyarılardan birini seçin.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-individual-alert.png" alt-text="Uyarı ayrıntılarını gösteren ekran görüntüsü.":::

Microsoft Azure 'den e-postalar için gelen kutunuzu denetleyin. Konu satırında tetiklenen uyarı tanımlanacaktır. Örneğin, *Azure: 5 dakikadan fazla 1000 ' den fazla ileti olursa, etkin önem derecesi: 3 uyarı*. Gövde aşağıdaki resme benzer şekilde görünür:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/alert-mail.png" alt-text="Uyarıları gösteren e-postanın ekran görüntüsü.":::

## <a name="view-azure-monitor-logs"></a>Azure Izleyici günlüklerini görüntüleme

[Bağlantılar ve cihaz telemetri için günlükleri topla](#collect-logs-for-connections-and-device-telemetry) bölümünde, IoT Hub 'ınız tarafından Azure izleyici günlüklerine bağlantı ve cihaz telemetri işlemleri için oluşturulan kaynak günlüklerini göndermek üzere bir tanılama ayarı oluşturdunuz. Bu bölümde, oluşan hataları gözlemlemek için Azure Izleyici günlüklerinde bir kusto sorgusu çalıştıracaksınız.

1. Azure portal ' de IoT Hub 'ınızın sol bölmesindeki **izleme** ' nin altında **Günlükler**' i seçin. Açılırsa ilk **sorgular** penceresini kapatın.

1. Yeni sorgu bölmesinde **sorgular** sekmesini seçin ve ardından varsayılan sorguların listesini görmek için **IoT Hub** ' ı genişletin.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/new-query-pane.png" alt-text="IoT Hub varsayılan sorguların ekran görüntüsü.":::

1. *Hata Özeti* sorgusunu seçin. Sorgu, sorgu Düzenleyicisi bölmesinde görünür. Düzenleyici bölmesinde **Çalıştır** ' ı seçin ve sorgu sonuçlarını gözlemleyin. Ayrıntıları görmek için satırlardan birini genişletin.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/logs-errors.png" alt-text="Hata Özeti sorgusunun döndürdüğü günlüklerin ekran görüntüsü.":::

   > [!NOTE]
   > Herhangi bir hata görmüyorsanız, *son bağlı cihazlar* sorgusunu çalıştırmayı deneyin. Bu, sanal cihaz için bir satır döndürmelidir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticide oluşturduğunuz tüm kaynakları kaldırmak için kaynak grubunu silin. Bu eylem grubun içerdiği tüm kaynakları siler. Bu durumda, IoT Hub 'ı, Log Analytics çalışma alanını ve kaynak grubunun kendisini kaldırır. Panoya sabitlenmiş ölçüm grafikleri varsa, her grafiğin sağ üst köşesindeki üç noktaya tıklayarak ve **Kaldır**' ı seçerek bunları el ile kaldırmanız gerekir. Grafikleri sildikten sonra değişikliklerinizi kaydettiğinizden emin olun.

Kaynak grubunu kaldırmak için [az group delete](/cli/azure/group#az_group_delete) komutunu kullanın.

```azurecli-interactive
az group delete --name ContosoResources
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, aşağıdaki görevleri gerçekleştirerek IoT Hub ölçümleri ve günlükleri nasıl kullanacağınızı öğrendiniz:

> [!div class="checklist"]
>
> * Azure CLı kullanarak bir IoT Hub 'ı oluşturun, sanal cihazı kaydedin ve bir Log Analytics çalışma alanı oluşturun.  
> * Log Analytics çalışma alanındaki Azure Izleyici günlüklerine IoT Hub bağlantıları ve cihaz telemetri kaynak günlükleri gönderin.
> * Ölçüm Gezgini 'ni kullanarak seçili ölçümleri temel alan bir grafik oluşturun ve panonuza sabitleyin.
> * Önemli koşullar gerçekleştiğinde e-postayla bildirim alabilmeniz için ölçüm uyarıları oluşturun.
> * IoT Hub 'a ileti gönderen bir IoT cihazının benzetimini yapan bir uygulamayı indirip çalıştırın.
> * Koşullarınız gerçekleştiğinde uyarıları görüntüleyin.
> * Panonuzda ölçüm grafiğini görüntüleyin.
> * Azure Izleyici günlüklerinde IoT Hub hataları ve işlemleri görüntüleyin.

IoT cihazı durumunun nasıl yönetileceğini öğrenmek için sonraki öğreticiye geçin. 

> [!div class="nextstepaction"]
> [Cihazlarınızı bir arka uç hizmetinden yapılandırma](tutorial-device-twins.md)