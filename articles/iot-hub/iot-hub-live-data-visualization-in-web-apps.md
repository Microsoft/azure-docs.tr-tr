---
title: Web uygulamasındaki IoT Hub verilerinizin gerçek zamanlı veri görselleştirmesi
description: Bir sensörden toplanan ve IoT Hub 'ınıza gönderilen sıcaklık ve nem verilerini görselleştirmek için bir Web uygulaması kullanın.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 073a766662b2ead4b816276fa7fda6dc5e6caca7
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954642"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Azure IoT Hub 'ınızdaki gerçek zamanlı algılayıcı verilerini bir Web uygulamasında görselleştirin

![Uçtan uca diyagram](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Öğrenecekleriniz

Bu öğreticide, IoT Hub 'ınızın yerel bilgisayarınızda çalışan bir Node. js web uygulamasıyla aldığı gerçek zamanlı algılayıcı verilerini görselleştirmeyi öğreneceksiniz. Web uygulamasını yerel olarak çalıştırdıktan sonra, isteğe bağlı olarak, Azure App Service Web uygulamasını barındırmak için adımları izleyebilirsiniz. Power BI kullanarak IoT Hub 'ınızdaki verileri görselleştirmeye denemek istiyorsanız bkz. [Azure IoT Hub 'deki gerçek zamanlı algılayıcı verilerini görselleştirmek için Power BI kullanma](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Yapabilecekleriniz

* Web uygulamasının algılayıcı verilerini okumak için kullanacağı IoT Hub 'ınıza bir tüketici grubu ekleyin
* Web uygulaması kodunu GitHub 'dan indirin
* Web uygulaması kodunu inceleyin
* Ortam değişkenlerini, Web uygulamanız için gereken IoT Hub yapıtları tutacak şekilde yapılandırma
* Geliştirme makinenizde Web uygulamasını çalıştırma
* IoT Hub 'ınızdan gerçek zamanlı sıcaklık ve nem verilerini görmek için bir Web sayfası açın
* Seçim Azure CLı kullanarak Web uygulamanızı barındırma Azure App Service

## <a name="what-you-need"></a>Ne gerekiyor

* [Raspberry PI Çevrimiçi simülatör](iot-hub-raspberry-pi-web-simulator-get-started.md) öğreticisini veya cihaz öğreticilerinin birini doldurun; Örneğin, [Node. js Ile Raspberry Pi](iot-hub-raspberry-pi-kit-node-get-started.md). Bu, aşağıdaki gereksinimleri kapsar:

  * Etkin bir Azure aboneliği
  * Aboneliğiniz kapsamındaki bir IoT Hub 'ı
  * IoT Hub 'ınıza ileti gönderen bir istemci uygulaması

* [Git 'i indir](https://www.git-scm.com/downloads)

* Bu makaledeki adımlarda bir Windows geliştirme makinesi varsayılır; Ancak, bu adımları tercih ettiğiniz kabukta bir Linux sisteminde kolayca yapabilirsiniz.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure CLı için Microsoft Azure IoT uzantısını Cloud Shell örneğinize eklemek için aşağıdaki komutu çalıştırın. IOT uzantısı, Azure CLı 'ye IoT Hub, IoT Edge ve IoT cihaz sağlama hizmeti 'ne (DPS) özel komutlar ekler.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>IoT Hub 'ınıza bir tüketici grubu ekleme

[Tüketici grupları](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) , uygulama ve Azure hizmetlerinin aynı Olay Hub 'ı uç noktasından bağımsız olarak verileri kullanmasını sağlayan olay akışına bağımsız görünümler sağlar. Bu bölümde, IoT Hub 'ınızın yerleşik uç noktasına, Web uygulamasının verileri okumak için kullanacağı bir tüketici grubu eklersiniz.

IoT Hub 'ınızın yerleşik uç noktasına bir tüketici grubu eklemek için aşağıdaki komutu çalıştırın:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Seçtiğiniz adı aklınızda, bu öğreticide daha sonra ihtiyacınız olacaktır.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>IoT Hub 'ınız için bir hizmet bağlantı dizesi alın

IoT Hub 'ları, çeşitli varsayılan erişim ilkeleriyle oluşturulur. Bu tür bir ilke, bir hizmetin IoT Hub 'ın uç noktalarını okuması ve yazması için yeterli izinleri sağlayan **hizmet** ilkesidir. IoT Hub 'ınız için hizmet ilkesine uyan bir bağlantı dizesi almak için aşağıdaki komutu çalıştırın:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

Bağlantı dizesi şuna benzer olmalıdır:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Hizmet bağlantı dizesi ' ni aklınızda, bu öğreticide daha sonra ihtiyacınız olacaktır.

## <a name="download-the-web-app-from-github"></a>Web uygulamasını GitHub 'dan indirin

Bir komut penceresi açın ve örneği GitHub 'dan indirmek ve örnek dizine değiştirmek için aşağıdaki komutları girin:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Web uygulaması kodunu inceleyin

Web Apps-node-IoT-Hub-veri görselleştirme dizininden Web uygulamasını en sevdiğiniz düzenleyicide açın. Aşağıda VS Code görüntülenen dosya yapısı gösterilmektedir:

![Web uygulaması dosya yapısı](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Aşağıdaki dosyaları incelemek için bir dakikanızı ayırın:

* **Server. js** , Web yuvasını ve Olay Hub sarmalayıcı sınıfını başlatan hizmet tarafı bir betiktir. Bu, sınıfın, gelen iletileri Web yuvasına yayımlamak için kullandığı olay hub sarmalayıcı sınıfına bir geri çağırma sağlar.

* **Event-hub-Reader. js** , belirtilen bağlantı dizesini ve tüketici grubunu kullanarak IoT Hub 'ın yerleşik uç noktasına bağlanan bir hizmet tarafı betiğidir. Gelen iletilerde DeviceID ve EnqueuedTimeUtc 'yi ayıklar ve ardından Server. js tarafından kaydedilen geri arama yöntemini kullanarak iletiyi geçirir.

* **Chart-Device-Data. js** , Web yuvasını dinleyen, her bir DeviceID 'nin izlenmesini tutan ve her bir cihaz için gelen verilerin son 50 noktasını depolayan bir istemci tarafı komut dosyası. Daha sonra seçili cihaz verilerini grafik nesnesine bağlar.

* **Index. html** , Web sayfası için Kullanıcı arabirimi yerleşimini işler ve istemci tarafı mantığı için gerekli betiklerine başvurur.

## <a name="configure-environment-variables-for-the-web-app"></a>Web uygulaması için ortam değişkenlerini yapılandırma

IoT Hub 'ınızdaki verileri okumak için, Web uygulamasının IoT Hub 'ının bağlantı dizesine ve okuması gereken tüketici grubunun adına ihtiyacı vardır. Bu dizeleri, Server. js ' de aşağıdaki satırlardaki işlem ortamından alır:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Aşağıdaki komutlarla, komut pencerenizde ortam değişkenlerini ayarlayın. Yer tutucu değerlerini IoT Hub 'ınız için hizmet bağlantı dizesiyle ve daha önce oluşturduğunuz tüketici grubunun adına değiştirin. Dizeleri tırnak içine mayın.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Web uygulamasını çalıştırma

1. Cihazınızın çalıştığından ve veri göndermekle emin olun.

2. Komut penceresinde, başvurulan paketleri indirip yüklemek ve Web sitesini başlatmak için aşağıdaki satırları çalıştırın:

   ```cmd
   npm install
   npm start
   ```

3. Web uygulamasının IoT Hub 'ınıza başarıyla bağlandığını ve 3000 numaralı bağlantı noktasını dinlediğini belirten konsolda çıktı görmeniz gerekir:

   ![Konsol üzerinde Web uygulaması başlatıldı](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>IoT Hub 'ınızdaki verileri görmek için bir Web sayfası açın

`http://localhost:3000`için bir tarayıcı açın.

Cihaz **seçin** listesinde, cihaz tarafından IoT Hub 'ınıza gönderilen son 50 sıcaklık ve nem veri noktalarının çalışan bir çizim listesini görmek için cihazınızı seçin.

![Gerçek zamanlı sıcaklık ve nem gösteren Web uygulaması sayfası](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

Ayrıca, konsolunda Web uygulamanızın tarayıcı istemcisine yayınlamasını belirten iletileri gösteren çıktıyı görmeniz gerekir:  

![Konsol üzerinde Web uygulaması yayın çıkışı](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>Web uygulamasını App Service barındırın

[Azure App Service Web Apps özelliği](https://docs.microsoft.com/azure/app-service/overview) , Web uygulamalarını barındırmak için hizmet olarak platform (PaaS) sağlar. Azure App Service barındırılan Web uygulamaları, ek güvenlik, yük dengeleme ve ölçeklenebilirlik gibi güçlü Azure özelliklerinden ve sürekli dağıtım, paket yönetimi gibi Azure ve iş ortağı DevOps çözümlerinin avantajlarından yararlanabilir. Azure App Service birçok popüler dilde geliştirilen ve Windows veya Linux altyapısına dağıtılan Web uygulamalarını destekler.

Bu bölümde, Azure CLı komutlarını kullanarak App Service bir Web uygulaması hazırlarsınız ve kodunuzu buna dağıtırsınız. [Az WebApp](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest) belgelerinde kullanılan komutların ayrıntılarını bulabilirsiniz. Başlamadan önce, [IoT Hub 'ınıza bir kaynak grubu eklemek](#add-a-consumer-group-to-your-iot-hub), [IoT Hub 'ınız için bir hizmet bağlantı dizesi almak](#get-a-service-connection-string-for-your-iot-hub)ve [Web uygulamasını GitHub 'dan indirmek](#download-the-web-app-from-github)için adımları tamamladığınızdan emin olun.

1. [App Service planı](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) , App Service çalıştırılmak üzere barındırılan bir uygulama için bir işlem kaynakları kümesi tanımlar. Bu öğreticide, Web uygulamasını barındırmak için geliştirici/ücretsiz katmanını kullanırız. Ücretsiz katman sayesinde, Web uygulamanız diğer müşterilerin uygulamaları dahil olmak üzere diğer App Service uygulamalarla paylaşılan Windows kaynakları üzerinde çalışır. Azure Ayrıca, Linux işlem kaynaklarında Web Apps dağıtmak için App Service planlar sunmaktadır. Kullanmak istediğiniz bir App Service planınız zaten varsa, bu adımı atlayabilirsiniz.

   Windows ücretsiz katmanını kullanarak bir App Service planı oluşturmak için aşağıdaki komutu çalıştırın. IoT Hub 'ınızın bulunduğu kaynak grubunu kullanın. Hizmet planı adınız büyük ve küçük harf, rakam ve kısa çizgi içerebilir.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Şimdi App Service planınızda bir Web uygulaması sağlayın. `--deployment-local-git` parametresi, Web uygulaması kodunun yerel makinenize git deposundan yüklenmesini ve dağıtılmasını sağlar. Web uygulamanızın adı genel olarak benzersiz olmalıdır ve büyük ve küçük harf, sayı ve kısa çizgi karakterlerini içerebilir.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --deployment-local-git
   ```

3. Şimdi IoT Hub bağlantı dizesini ve Olay Hub 'ı tüketici grubunu belirten ortam değişkenlerine yönelik uygulama ayarlarını ekleyin. Tek tek ayarlar `-settings` parametresinde ayrılmış alandır. IoT Hub 'ınız ve bu öğreticide daha önce oluşturduğunuz tüketici grubu için hizmet bağlantı dizesini kullanın. Değerleri tırnak içine mayın.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. Web uygulaması için Web soketi protokolünü etkinleştirin ve Web uygulamasını yalnızca HTTPS isteklerini alacak şekilde ayarlayın (HTTP istekleri HTTPS 'ye yönlendirilir).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Kodu App Service dağıtmak için [Kullanıcı düzeyinde dağıtım kimlik bilgilerinizi](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials)kullanacaksınız. Kullanıcı düzeyi dağıtım kimlik bilgileriniz Azure kimlik bilgilerinizle farklıdır ve bir Web uygulamasına git yerel ve FTP dağıtımları için kullanılır. Bu ayarlar bir kez ayarlandıktan sonra Azure hesabınızdaki tüm aboneliklerdeki tüm App Service uygulamalarınız arasında geçerlidir. Daha önce Kullanıcı düzeyinde dağıtım kimlik bilgilerini ayarladıysanız, bunları kullanabilirsiniz.

   Daha önce Kullanıcı düzeyinde dağıtım kimlik bilgilerini ayarlamadıysanız veya parolanızı hatırlayamıyorsanız, aşağıdaki komutu çalıştırın. Dağıtım Kullanıcı adınız Azure içinde benzersiz olmalıdır ve yerel git gönderimleri için ' @ ' sembolünü içermemelidir. İstendiğinde, yeni parolanızı girip onaylayın. Parola en az sekiz karakter uzunluğunda olmalıdır ve şu üç öğeden ikisi vardır: harfler, rakamlar ve semboller.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Kodunuzu App Service 'ye göndermek için kullanılacak git URL 'sini alın.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. App Service içindeki Web uygulaması için git deposuna başvuran bir uzak kopya ekleyin. Git kopyası URL 'SI\>\<için, önceki adımda döndürülen URL 'YI kullanın. Komut pencerenizde aşağıdaki komutu çalıştırın.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Kodu App Service dağıtmak için komut pencerenizde aşağıdaki komutu girin. Kimlik bilgileri istenirse, 5. adımda oluşturduğunuz Kullanıcı düzeyinde dağıtım kimlik bilgilerini girin. App Service uzak ana dalına gönderdiğinizden emin olun.

    ```cmd
    git push webapp master:master
    ```

9. Dağıtım ilerleme durumu, komut pencerenizde güncelleştirilecek. Başarılı bir dağıtım, aşağıdaki çıktıya benzer satırlar ile sona acaktır:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. Web uygulamanızın durumunu sorgulamak ve çalıştığından emin olmak için aşağıdaki komutu çalıştırın:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. Bir tarayıcıda `https://<your web app name>.azurewebsites.net` sayfasına gidin. Web uygulamasını yerel olarak çalıştırdığınızda gördüğünüzle benzer bir Web sayfası. Cihazınızın çalıştığını ve veri gönderdiğini varsayarak, cihaz tarafından gönderilen 50 en son sıcaklık ve nem okumalardan oluşan, çalışan bir çizim görmeniz gerekir.

## <a name="troubleshooting"></a>Sorun giderme

Bu örnekle ilgili herhangi bir sorun yaşıyorsanız, aşağıdaki bölümlerdeki adımları deneyin. Hala sorun yaşıyorsanız, bu konunun en altında bize geri bildirim gönderin.

### <a name="client-issues"></a>İstemci sorunları

* Bir cihaz listede görünmezse veya hiçbir grafik çizilmezse cihaz kodunun cihazınızda çalıştığından emin olun.

* Tarayıcıda Geliştirici Araçları ' nı açın (birçok tarayıcıda F12 tuşu açılır ve konsolu bulunur). Burada yazdırılan uyarıları veya hataları arayın.

* /JS/chat-Device-Data.js. içinde istemci tarafı betikte hata ayıklaması yapabilirsiniz

### <a name="local-website-issues"></a>Yerel Web sitesi sorunları

* Konsol çıktısı için düğümü başlattığınız penceredeki çıktıyı izleyin.

* Sunucu kodunda özel olarak Server. js ve/Scripts/Event-hub-Reader.exe hatalarını ayıklayın.

### <a name="azure-app-service-issues"></a>Azure App Service sorunları

* Azure portal, Web uygulamanıza gidin. Sol bölmedeki **izleme** altında **App Service Günlükler**' i seçin. **Uygulama günlüğünü (dosya sistemi)** açın, **düzeyi** hata olarak ayarlayın ve ardından **Kaydet**' i seçin. Ardından **günlük akışı** ' nı açın ( **izleme**altında).

* Web uygulamanızdan Azure portal, **geliştirme araçları** altında **konsol** ' ı seçin ve `node -v` ve `npm -v`ile NPM sürümlerini doğrulayın.

* Bir paket bulmayla ilgili bir hata görürseniz, adımları sıra dışı olarak çalıştırmanız gerekebilir. Site dağıtıldığında (`git push`ile) App Service, yapılandırılan düğümün geçerli sürümüne göre çalışan `npm install`çalıştırır. Bu yapılandırmada daha sonra değiştirilirse kodda daha az bir değişiklik yapmanız ve yeniden göndermeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

IoT Hub 'ınızdaki gerçek zamanlı algılayıcı verilerini görselleştirmek için Web uygulamanızı başarıyla kullandınız.

Azure IoT Hub 'den verileri görselleştirmenin başka bir yolu için bkz. [IoT Hub 'ınızdaki gerçek zamanlı algılayıcı verilerini görselleştirmek için Power BI kullanma](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
