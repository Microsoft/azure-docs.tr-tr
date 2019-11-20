---
title: Hızlı Başlangıç, Linux üzerinde Azure IOT Edge cihazı oluşturma | Microsoft Docs
description: Bu hızlı başlangıçta bir IOT Edge cihazı oluşturma ve ardından önceden oluşturulmuş kod Azure Portalı'ndan uzaktan dağıtma öğrenin.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/09/2019
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 70d0f5b28f769617b16b2ae8c71bc5b3e90d5dfe
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877260"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>Hızlı Başlangıç: İlk IoT Edge modülünüzü bir sanal Linux cihazına dağıtma

Kapsayıcılı kodu bir sanal IoT Edge cihazına dağıtarak bu hızlı başlangıçta Azure IoT Edge test edin. IoT Edge, iş yüklerinizi kenarda daha fazla gönderebilmeniz için cihazlarınızda kodu uzaktan yönetmenizi sağlar. Bu hızlı başlangıçta, IoT Edge cihazınız için bir Azure sanal makinesi kullanmanızı öneririz. Bu, tüm önkoşulların yüklü olduğu bir test makinesini hızlıca oluşturmanızı ve sonra işiniz bittiğinde silmenizi sağlar. 

Bu hızlı başlangıçta şunları yapmayı öğrenirsiniz:

1. Bir IoT Hub oluşturma.
2. Bir IoT Edge cihazını IoT hub'ınıza kaydetme.
3. IoT Edge çalışma zamanını sanal cihazınıza yükleyip başlatın.
4. Bir IoT Edge cihazına uzaktan modül dağıtma.

![Diyagram - cihaz ve buluta yönelik hızlı başlangıç mimarisi](./media/quickstart-linux/install-edge-full.png)

Bu hızlı başlangıçta, IoT Edge cihaz olarak yapılandırılmış bir Linux sanal makinesi oluşturma işlemi adım adım açıklanmaktadır. Bu işlemin ardından Azure portalından cihazınıza bir modül dağıtabilirsiniz. Bu hızlı başlangıçta oluşturduğunuz modül; sıcaklık, nem ve basınç verileri üreten bir sensör simülasyonudur. Diğer Azure IoT Edge öğreticileri, burada iş içgörüsü için simülasyon verilerini analiz eden modüller dağıtarak yaptığınız çalışmayı temel alır.

Etkin bir Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Bu hızlı başlangıçtaki birçok adımı tamamlamak için Azure CLI kullanacaksınız. Azure IoT de ek işlevleri etkinleştirmek için bir uzantıya sahiptir.

Azure IoT uzantısını cloud shell örneğine ekleyin.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prerequisites"></a>Önkoşullar

Bulut kaynakları:

* Bu hızlı başlangıçta kullandığınız tüm kaynakları yönetmek için kullanacağınız bir kaynak grubu.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

IoT Edge cihazı:

* IoT Edge cihazınız olacak bir Linux cihazı veya sanal makinesi. [Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu) sanal makinesinde Microsoft tarafından sağlanmış Azure IoT Edge kullanmanız gerekir ve bu, bir cihazda IoT Edge çalıştırmak için ihtiyaç duyduğunuz her şeyi önceden kurar. Kullanım koşullarını kabul edin ve aşağıdaki komutları kullanarak bu sanal makineyi oluşturun:

   ```azurecli-interactive
   az vm image accept-terms --urn microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest
   az vm create --resource-group IoTEdgeResources --name EdgeVM --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys
   ```

   Yeni sanal makinenin oluşturulması ve başlatılması birkaç dakika sürebilir.

   Yeni bir sanal makine oluşturduğunuzda, Not **Publicıpaddress**, oluşturma komut çıktısı bir parçası olarak sağlanır. Bu hızlı başlangıcın sonraki bölümlerinde sanal makineye bağlanmak için bu genel IP adresi kullanır.

* Azure IoT Edge çalışma zamanını kendi cihazınızda çalıştırmayı tercih ediyorsanız, [Linux üzerinde Azure IoT Edge çalışma zamanını yüklemek](how-to-install-iot-edge-linux.md)için yönergeleri izleyin.

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

Azure CLı ile IoT Hub 'ı oluşturarak hızlı başlangıcı başlatın.

![Diyagram - bulutta IOT hub'ı oluşturma](./media/quickstart-linux/create-iot-hub.png)

IoT Hub’ın ücretsiz düzeyi bu hızlı başlangıç için kullanılabilir. IoT Hub'ı daha önce kullandıysanız ve oluşturulmuş ücretsiz hub'ınız varsa bu IoT hub'ını kullanabilirsiniz. Her aboneliğin yalnızca bir ücretsiz IoT hub’ı olabilir.

Aşağıdaki kod, **IoTEdgeResources** kaynak grubunda ücretsiz bir **F1** hub’ı oluşturur. *{hub_name}* değerini IoT hub'ınız için benzersiz bir adla değiştirin.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1
   ```

   Aboneliğinizde zaten bir ücretsiz hub olduğu için hata alırsanız, SKU değerini **S1** olarak değiştirin. IOT hub'ı adı kullanılamıyor, bir hata alırsanız, başka birisi zaten bu ada sahip bir hub'ı olduğu anlamına gelir. Yeni bir ad deneyin.

## <a name="register-an-iot-edge-device"></a>IoT Edge cihazı kaydetme

Yeni oluşturulan IoT hub'ına bir IoT Edge cihazı kaydedin.

![Diyagram-IoT Hub kimliği olan bir cihazı kaydetme](./media/quickstart-linux/register-device.png)

IoT Edge cihazınız için, IoT Hub 'ınız ile iletişim kurabilmesi için bir cihaz kimliği oluşturun. Cihaz kimliği bulutta kalır ve fiziksel cihazla cihaz kimliği arasında bağlantı kurmak için benzersiz bir bağlantı dizesi kullanılır.

IOT Edge cihazları sınıflardır ve tipik bir IOT cihazlarında farklı yönetilebilir olduğundan, bu kimlik ile IOT Edge cihazı için bildirmek `--edge-enabled` bayrağı.

1. Azure Cloud Shell'de aşağıdaki komutu girerek hub'ınızda **myEdgeDevice** adlı bir cihaz oluşturun.

   ```azurecli-interactive
   az iot hub device-identity create --hub-name {hub_name} --device-id myEdgeDevice --edge-enabled
   ```

   İlke anahtarları iothubowner hakkında bir hata alırsanız, cloud shell azure CLI IOT ext uzantısı'nın en son sürümünü çalıştırdığından emin olun.

2. Fiziksel cihazınızla IoT Hub'daki kimliği arasında bağlantı oluşturan cihaz bağlantı dizesini alın.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. JSON çıktısından `connectionString` anahtarın değerini kopyalayın ve kaydedin. Bu değer, cihaz bağlantı dizesidir. Sonraki bölümde IoT Edge çalışma zamanını yapılandırmak için bu bağlantı dizesini kullanacaksınız.

   ![CLı çıktısından bağlantı dizesini al](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>IoT Edge cihazınızı yapılandırma

IoT Edge cihazınızda Azure IoT Edge çalışma zamanını başlatın.

![Diyagram-çalışma zamanını cihazda Başlat](./media/quickstart-linux/start-runtime.png)

IoT Edge çalışma zamanı tüm IoT Edge cihazlarına dağıtılır. Üç bileşeni vardır. **IoT Edge güvenlik arka plan programı** , IoT Edge aracısını başlatarak IoT Edge bir cihaz her önyüklendiğinde ve cihazı önyükleme başlatılır. **IoT Edge aracısı**, IoT Edge hub'ı dahil olmak üzere IoT Edge cihazındaki modüllerin dağıtımını ve izlenmesini kolaylaştırır. **IoT Edge hub'ı** IoT Edge cihazındaki modüller ve cihaz ile IoT Hub'ı arasındaki iletişimi yönetir.

Çalışma zamanı yapılandırması sırasında cihaz bağlantı dizesi sağlamanız gerekir. Azure CLI'den aldığınız dizeyi kullanın. Bu dize, fiziksel cihazınızı Azure'daki IoT Edge cihaz kimliğiyle ilişkilendirir.

### <a name="set-the-connection-string-on-the-iot-edge-device"></a>IOT Edge cihazında bağlantı dizesini Ayarla

Önkoşul bölümünde açıklandığı gibi Ubuntu sanal makinesinde Azure IoT Edge kullanıyorsanız, cihazınızda IoT Edge çalışma zamanı zaten yüklüdür. Cihazınızı, önceki bölümde aldığınız cihaz bağlantı dizesi ile yapılandırmanız yeterlidir. Bunu, sanal makineye bağlanmak zorunda kalmadan uzaktan yapabilirsiniz. **{Device_connection_string}** öğesini kendi dizenizle değiştirerek aşağıdaki komutu çalıştırın.

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Yerel makinenizde veya bir ARM32 ya da ARM64 cihazında IoT Edge çalıştırıyorsanız, IoT Edge çalışma zamanını ve önkoşul olan önkoşulları cihazınıza yüklemeniz gerekir. [Linux 'ta Azure IoT Edge çalışma zamanını yüklemek](how-to-install-iot-edge-linux.md)ve ardından bu hızlı başlangıca geri dönmek için yönergeleri izleyin.

### <a name="view-the-iot-edge-runtime-status"></a>IoT Edge çalışma zamanı durumunu görüntüleme

Bu hızlı başlangıçtaki komutların geri kalanı IoT Edge cihazınızın kendisinde gerçekleşirken cihazda neler olduğunu görmeniz için. Bir sanal makine kullanıyorsanız, oluşturma komutu tarafından çıktı olan genel IP adresini kullanarak o makineye şimdi bağlanın. Genel IP adresini sanal makinenizin genel bakış sayfasında Azure Portalı'nda da bulabilirsiniz. Sanal makinenize bağlanmak için aşağıdaki komutu kullanın. Önkoşullarda önerilenden farklı bir Kullanıcı adı kullandıysanız **{azureuser}** öğesini değiştirin. Değiştirin **{Publicıpaddress}** makinenizin adresine sahip.

   ```azurecli-interactive
   ssh azureuser@{publicIpAddress}
   ```

Çalışma zamanının IoT Edge cihazınıza başarıyla yüklenip yapılandırıldığını doğrulayın.

>[!TIP]
>`iotedge` komutlarını çalıştırmak için yükseltilmiş ayrıcalıklara ihtiyacınız olacaktır. Makinenizdeki oturumu kapattıktan sonra IoT Edge çalışma zamanını yükleyip oturum açtığınızda izinleriniz otomatik olarak güncelleştirilir. O zamana kadar komutların önüne **sudo** eklemenize gerekir.

1. IoT Edge güvenlik arka plan programının sistem hizmeti olarak çalışıp çalışmadığını denetleyin.

   ```bash
   sudo systemctl status iotedge
   ```

   ![Sistem hizmeti olarak çalışan IoT Edge Daemon 'a bakın](./media/quickstart-linux/iotedged-running.png)

2. Hizmetle ilgili sorunları gidermeniz gerekirse hizmet günlüklerini alın.

   ```bash
   journalctl -u iotedge
   ```

3. Cihazınızda çalışan modülleri görüntüleyin.

   ```bash
   sudo iotedge list
   ```

   ![Cihazınızda bir modülü görüntüleme](./media/quickstart-linux/iotedge-list-1.png)

IoT Edge cihazınız yapılandırıldı. Bulutta dağıtılan modülleri çalıştırmak için hazır.

## <a name="deploy-a-module"></a>Modül dağıtma

Azure IoT Edge cihazınızı, IoT Hub'ına telemetri verileri gönderecek bir modül dağıtmak için buluttan yönetin.
![Diyagram - modülü buluttan cihaza dağıtın](./media/quickstart-linux/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Oluşturulan verileri görüntüleme

Bu hızlı başlangıçta, yeni bir IoT Edge cihazı oluşturdunuz ve üzerine IoT Edge çalışma zamanını yüklediniz. Ardından, cihazın kendisinde değişiklik yapmak zorunda kalmadan cihazda çalıştırmak üzere bir IoT Edge modülünü dağıtmak için Azure portal kullandınız.

Bu durumda, gönderdiğiniz modül test için kullanabileceğiniz örnek veriler oluşturur. Sanal sıcaklık algılayıcı modülü, daha sonra test için kullanabileceğiniz ortam verileri oluşturur. Sanal algılayıcı hem makinenin hem de makinenin bulunduğu ortamın izlenmesini izler. Örneğin, bu algılayıcı bir sunucu odasında, bir fabrika katında veya bir rüzgar Turbin üzerinde olabilir. İleti çevresel sıcaklık ve nem, makine sıcaklığı ve basınç ve bir zaman damgası içerir. IoT Edge öğreticileri, bu modülle oluşturulan verileri analiz için test verileri olarak kullanır.

IOT Edge Cihazınızda bir komut istemi yeniden açın veya Azure CLI SSH bağlantısından kullanın. Buluttan dağıtılan modülün IoT Edge cihazınızda çalıştığından emin olun:

   ```bash
   sudo iotedge list
   ```

   ![Cihazınızda üç modül görüntüleme](./media/quickstart-linux/iotedge-list-2.png)

Sıcaklık Algılayıcı modülünden gönderilen iletileri görüntüleyin:

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >IoT Edge komutlar modül adlarına başvururken büyük/küçük harfe duyarlıdır.

   ![Verileri modülünüzden görüntüleme](./media/quickstart-linux/iotedge-logs.png)

Ayrıca, [Visual Studio Code Için azure IoT Hub araç seti uzantısını](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (eskiden Azure IoT araç seti uzantısı) kullanarak IoT Hub 'ınıza gelen iletileri izleyebilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

IoT Edge öğreticilerine devam etmek istiyorsanız bu hızlı başlangıçta kaydettiğiniz ve ayarladığınız cihazı kullanabilirsiniz. Aksi takdirde, ücretlendirmemek için oluşturduğunuz Azure kaynaklarını silebilirsiniz.

Sanal makinenizi ve IoT hub’ınızı yeni bir kaynak grubunda oluşturduysanız, bu grubu ve ilişkili tüm kaynaklarını silebilirsiniz. Çifte denetim var. emin olmak için kaynak grubunun içeriğini kullanıcının tutmak istediğiniz bir şey. Tüm bir grubu silmek istemiyorsanız, bunun yerine bu kaynakları tek tek silebilirsiniz.

**IoTEdgeResources** grubunu kaldırın.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

## <a name="next-steps"></a>Sonraki adımlar


Bu hızlı başlangıçta bir IoT Edge cihaz oluşturdunuz ve cihaza kod dağıtmak için Azure IoT Edge bulut arabirimini kullandınız. Artık ortamı hakkında ham veri üreten bir test cihazınız var.

Bir sonraki adım, iş mantığınızı çalıştıran IoT Edge modüller oluşturmaya başlayabilmeniz için yerel geliştirme ortamınızı ayarlamanıza olanak sağlar. 

> [!div class="nextstepaction"]
> [Linux cihazları için IoT Edge modülleri geliştirmeye başlama](tutorial-develop-for-linux.md)
