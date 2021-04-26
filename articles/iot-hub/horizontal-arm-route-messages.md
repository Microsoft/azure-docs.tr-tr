---
title: ARM şablonunu kullanarak Azure IoT Hub, depolama hesabı, rota iletileri yayımlama
description: ARM şablonunu kullanarak Azure IoT Hub, depolama hesabı, rota iletileri yayımlama
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: robinsh
ms.custom: mvc, subject-armqs
ms.openlocfilehash: fc8ddba2ec9b7bc9f1c2db8673ab805810afe17e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99981298"
---
# <a name="quickstart-deploy-an-azure-iot-hub-and-a-storage-account-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonu kullanarak Azure IoT Hub ve depolama hesabı dağıtma

Bu hızlı başlangıçta, Azure depolama 'ya iletileri yönlendiren bir IoT Hub oluşturmak için bir Azure Resource Manager şablonu (ARM şablonu) ve iletileri tutacak bir depolama hesabı kullanırsınız. İletileri göndermek için hub 'a bir sanal IoT cihazı el ile ekledikten sonra, cihazdan hub 'a ileti göndermek için  *ARM-okuma-yazma* adlı bir uygulamadaki Bu bağlantı bilgilerini yapılandırırsınız. Hub, hub 'a gönderilen iletilerin otomatik olarak depolama hesabına yönlendirilmesi için yapılandırılır. Bu hızlı başlangıç sonunda, depolama hesabını açıp gönderilen iletileri görebilirsiniz.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure'a Dağıt](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon `101-iothub-auto-route-messages` [Azure hızlı başlangıç şablonlarından](https://azure.microsoft.com/resources/templates/101-iothub-auto-route-messages)çağırılır.

:::code language="json" source="~/quickstart-templates/101-iothub-auto-route-messages/azuredeploy.json":::

Şablonda iki Azure kaynağı tanımlanmıştır:

- [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft. Devices/IotHubs](/azure/templates/microsoft.devices/iothubs)

## <a name="deploy-the-template-and-run-the-sample-app"></a>Şablonu dağıtma ve örnek uygulamayı çalıştırma

Bu bölümde, iletileri göndermek için şablonu dağıtma, sanal cihaz oluşturma ve ARM okuma yazma uygulaması çalıştırma adımları sağlanmaktadır.

1. ARM şablonunu dağıtarak kaynakları oluşturun.

    > [!TIP]
    > Şablonun dağıtımını başlatmak için aşağıdaki düğmeyi seçin. Çalışırken, ARM okuma yazma uygulamasını çalıştırmak için ayarlayın.

    [![Azure'a Dağıt](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

1. [IoT C# örneklerini](/samples/azure-samples/azure-iot-samples-csharp/azure-iot-samples-for-csharp-net/)indirip sıkıştırmasını açın.

1. Bir komut penceresi açın ve IoT C# örneklerinin sıkıştırunı geri yüklediğiniz klasöre gidin. ARM-Read-Write. csproj dosyasını içeren klasörü bulun. Bu komut penceresinde ortam değişkenlerini oluşturursunuz. Anahtarları almak için [Azure Portal](https://portal.azure.com) oturum açın. **Kaynak grupları** ' nı seçin ve ardından bu hızlı başlangıç için kullanılan kaynak grubunu seçin.

   ![Kaynak grubunu seçin](./media/horizontal-arm-route-messages/01-select-resource-group.png)

1. ARM şablonunu dağıtırken oluşturulan IoT Hub ve depolama hesabı görüntülenir. Devam etmeden önce şablon tam olarak dağıtılana kadar bekleyin. Sonra kaynaklarınızı görmek için kaynak grubunuzu seçin.

   ![Kaynak grubundaki kaynakları görüntüle](./media/horizontal-arm-route-messages/02-view-resources-in-group.png)

1. **Hub adına** ihtiyacınız vardır. Kaynak listesinden hub 'ı seçin. Hub 'ın adını IoT Hub bölümünün en üstünden Windows panosuna kopyalayın.

   ![Hub adını kopyalama](./media/horizontal-arm-route-messages/03-copy-hub-name.png)

    Bu komutta hub adını belirtilen yerde değiştirin ve komut penceresinde bu komutu yürütün:

    ```cmd
    SET IOT_HUB_URI=<hub name goes here>.azure-devices-net;
    ```

   Bu örnekte bu örnek şöyle görünür:

   ```cmd
   SET IOT_HUB_URI=ContosoTestHubdlxlud5h.azure-devices-net;
   ```

1. Sonraki ortam değişkeni IoT cihaz anahtarıdır. Hub 'ın IoT Hub menüsünden **IoT cihazları** ' nı seçerek hub 'a yeni bir cihaz ekleyin.

   ![IoT cihazlarını seçin](./media/horizontal-arm-route-messages/04-select-iot-devices.png)

1. Ekranın sağ tarafında yeni bir cihaz eklemek için **+ Yeni** ' yi seçin.

   Yeni cihaz adını girin. Bu hızlı başlangıç, **contoso-test-cihazından** başlayarak bir ad kullanır. Cihazı kaydedin ve sonra cihaz anahtarını almak için bu ekranı yeniden açın. (Bölmeyi kapattığınızda anahtar sizin için oluşturulur.) Birincil veya ikincil anahtarı seçin ve Windows panosuna kopyalayın. Komut penceresinde, komutunu Yürüt ' e ayarlayın ve ardından **ENTER** tuşuna basın. Komut, aşağıdaki gibi görünmelidir:

   ```cmd
   SET IOT_DEVICE_KEY=<device-key-goes-here>
   ```

1. Son ortam değişkeni, **CIHAZ kimliğidir**. Komut penceresinde, komutunu ayarlayıp yürütün.

   ```cmd
   SET IOT_DEVICE_ID=<device-id-goes-here>
   ```

   Bu örnek şöyle görünür:

   ```cmd
   SET IOT_DEVICE_ID=Contoso-Test-Device
   ```

1. Tanımladığınız ortam değişkenlerini görmek için, komut satırında SET yazın ve **ENTER** tuşuna basın, ardından **IoT** ile başlayan olanları arayın.

   ![Bkz. ortam değişkenleri](./media/horizontal-arm-route-messages/06-environment-variables.png)

    Artık ortam değişkenleri ayarlanır, uygulamayı aynı komut penceresinden çalıştırın. Aynı pencereyi kullandığınız için, uygulamayı çalıştırdığınızda değişkenler bellekte erişilebilir olacaktır.

1. Uygulamayı çalıştırmak için, komut penceresine aşağıdaki komutu yazın ve **ENTER** tuşuna basın.

    `dotnet run arm-read-write`

   Uygulama, her iletiyi IoT Hub 'ına gönderirken konsolda iletileri oluşturur ve görüntüler. Hub, ARM şablonunda otomatik yönlendirmeye sahip olacak şekilde yapılandırılmıştır. Metni içeren iletiler `level = storage` otomatik olarak depolama hesabına yönlendirilir. Uygulamanın 10 ila 15 dakika boyunca çalışmasına izin verin, ardından çalışmayı durduruncaya kadar bir kez veya iki **kez ENTER tuşuna** basın.

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

1. [Azure Portal](https://portal.azure.com) oturum açın ve kaynak grubunu seçip depolama hesabını seçin.

1. Dosyaları bulana kadar depolama hesabının detayına gidin.

   ![Depolama hesabı dosyalarına bakın](./media/horizontal-arm-route-messages/07-see-storage.png)

1. Dosyalardan birini seçip **İndir** ' i seçin ve dosyayı daha sonra bulabileceğiniz bir konuma indirin. Bu, 47 gibi sayısal bir ada sahip olacaktır. Sonuna _. txt_ ekleyin ve sonra açmak için dosyaya çift tıklayın.

1. Dosyayı açtığınızda, her satır farklı bir ileti için olur; her iletinin gövdesi de şifrelenir. İleti gövdesine karşı sorgu gerçekleştirmeniz için olması gerekir.

   ![Gönderilen iletileri görüntüleme](./media/horizontal-arm-route-messages/08-messages.png)

   > [!NOTE]
   > Bu iletiler UTF-32 ve Base64 olarak kodlanır. İletiyi geri okuduğunuzda, bunu ASCII olarak okumak için Base64 ve UTF-32 kodunu çözmelisiniz. İlgileniyorsanız, yönlendirme öğreticisinde ReadOneRowFromFile yöntemini kullanarak bu ileti dosyalarından birinden bir tane okuyabilir ve bunu ASCII 'ye ekleyebilirsiniz. ReadOneRowFromFile, bu hızlı başlangıç için sıkıştırılmış IoT C# örnek deposundadır. Bu klasörün en üstündeki yol şöyledir: *./iot-hub/Tutorials/Routing/SimulatedDevice/program.cs.* Boolean `readTheFile` değerini true olarak ayarlayın ve diskteki dosyanın yolunu basılı olarak kodlayın ve dosyadaki ilk satırı açar ve çevirir.

Bir IoT Hub ve depolama hesabı oluşturmak için ARM şablonu dağıttı ve hub 'a ileti göndermek için bir program çalıştırın. İletiler daha sonra, görüntülenebilecekleri depolama hesabında otomatik olarak depolanır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıç sırasında eklenen kaynakları kaldırmak için [Azure Portal](https://portal.azure.com)oturum açın. **Kaynak grupları**' nı seçin ve ardından bu hızlı başlangıç için kullandığınız kaynak grubunu bulun. Kaynak grubunu seçin ve *Sil*' i seçin. Gruptaki tüm kaynakları silecek.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: ilk ARM şablonunuzu oluşturma ve dağıtma](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
