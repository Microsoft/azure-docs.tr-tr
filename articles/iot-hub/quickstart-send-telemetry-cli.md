---
title: Azure IoT Hub (CLı) hızlı başlangıç 'a telemetri gönderme
description: Bu hızlı başlangıçta, bir IoT Hub 'ı oluşturmak, Telemetriyi göndermek ve bir cihaz ile hub arasındaki iletileri görüntülemek için Azure CLı kullanılarak çalışmaya başlamak IoT Hub yeni geliştiriciler gösterilmektedir.
ms.service: iot-hub
ms.topic: quickstart
ms.custom:
- iot-send-telemetry-cli
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/06/2019
ms.openlocfilehash: 948dfd25881a6a90dd441ad640091d88812cc298
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931825"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-monitor-it-with-the-azure-cli"></a>Hızlı başlangıç: bir cihazdan IoT Hub 'ına telemetri gönderme ve Azure CLı ile izleme

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub, IoT cihazlarınızdan buluta depolama veya işleme amacıyla yüksek hacimlerde telemetri almanızı sağlayan bir Azure hizmetidir. Bu hızlı başlangıçta, bir IoT Hub ve sanal cihaz oluşturmak, hub 'a cihaz telemetrisi göndermek ve buluttan cihaza ileti göndermek için Azure CLı 'yi kullanırsınız. Cihaz ölçümlerini görselleştirmek için Azure portal de kullanabilirsiniz. Bu, IoT Hub bir uygulamayla etkileşim kurmak için CLı kullanan geliştiriciler için temel bir iş akışıdır.

## <a name="prerequisites"></a>Önkoşullar
- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
- Azure CLı. Bu hızlı başlangıçta, tarayıcınızda çalışan etkileşimli bir CLı kabuğu olan Azure Cloud Shell kullanarak tüm komutları çalıştırabilirsiniz. Cloud Shell kullanıyorsanız, herhangi bir şey yüklemeniz gerekmez. CLı 'yi yerel olarak kullanmayı tercih ediyorsanız bu hızlı başlangıç, Azure CLı sürüm 2.0.76 veya üstünü gerektirir. Sürümü bulmak için az --version komutunu çalıştırın. Yüklemek veya yükseltmek için bkz. [Azure CLI 'Yı yüklemek]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın
https://portal.azure.com adresinden Azure portalında oturum açın.

CLı 'yi yerel olarak veya Cloud Shell ' de çalıştırdığınız bağımsız olarak, portalı tarayıcınızda açık tutun.  Daha sonra bu hızlı başlangıçta kullanacaksınız.

## <a name="launch-the-cloud-shell"></a>Cloud Shell başlatın
Bu bölümde Azure Cloud Shell bir örneğini başlatın. CLı 'yi yerel olarak kullanırsanız, [ıkı CLI oturumu hazırlama](#prepare-two-cli-sessions)bölümüne atlayın.

Cloud Shell başlatmak için:
1. Azure portal sağ üstteki menü çubuğunda **Cloud Shell** düğmesini seçin. 

    ![Azure portal Cloud Shell düğmesi](media/quickstart-send-telemetry-cli/cloud-shell-button.png)

    > [!NOTE]
    > Cloud Shell ilk kez kullandıysanız, Cloud Shell kullanmak için gerekli olan depolama alanı oluşturmanızı ister.  Depolama hesabı ve Microsoft Azure dosyaları paylaşma oluşturmak için bir abonelik seçin. 

1. **Ortam Seç** açılan menüsünde tercıh ettiğiniz CLI ortamınızı seçin. Bu hızlı başlangıç, **Bash** ortamını kullanır. Aşağıdaki CLı komutlarının hepsi PowerShell ortamında da çalışır. 

    ![CLı ortamı seçin](media/quickstart-send-telemetry-cli/cloud-shell-environment.png)

## <a name="prepare-two-cli-sessions"></a>İki CLı oturumu hazırlama
Bu bölümde, iki Azure CLı oturumu hazırlarsınız. Cloud Shell kullanıyorsanız, iki oturumu ayrı tarayıcı sekmelerinde çalıştıracaksınız. Yerel bir CLı istemcisi kullanıyorsanız, iki ayrı CLı örneği çalıştıracaksınız. İlk oturumu bir sanal cihaz olarak, ikinci oturum ise iletileri izlemek ve göndermek için kullanacaksınız. Bir komut çalıştırmak için, bu hızlı başlangıçta bir kod bloğunu kopyalamak için **Kopyala** ' yı seçin, kabuk oturumunuza yapıştırın ve çalıştırın.

Azure CLı, Azure hesabınızda oturum açmanızı gerektirir. Azure CLı kabuğu oturumunuz ile IoT Hub 'ınız arasındaki tüm iletişimin kimliği doğrulanır ve şifrelenir. Sonuç olarak, bu hızlı başlangıç, bağlantı dizesi gibi gerçek bir cihazla kullanacağınız ek kimlik doğrulamasına gerek kalmaz.

1. CLı kabuğunuzun Azure CLı için Microsoft Azure IoT uzantısını eklemek için [az Extension Add](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az-extension-add) komutunu çalıştırın. IOT uzantısı, Azure CLı 'ye IoT Hub, IoT Edge ve IoT cihaz sağlama hizmeti 'ne (DPS) özel komutlar ekler.

   ```azurecli
   az extension add --name azure-cli-iot-ext
   ```
    Azure ıOT uzantısını yükledikten sonra, Cloud Shell oturumunda yeniden yüklemeniz gerekmez. 

1. İkinci bir CLı oturumu açın.  Cloud Shell kullanıyorsanız, **yeni oturum aç**' ı seçin. CLı 'yi yerel olarak kullanıyorsanız ikinci bir örnek açın. 

    ![Yeni Cloud Shell oturumu aç](media/quickstart-send-telemetry-cli/cloud-shell-new-session.png)

## <a name="create-an-iot-hub"></a>IoT Hub'ı oluşturma
Bu bölümde, bir kaynak grubu ve bir IoT Hub oluşturmak için Azure CLı 'yi kullanırsınız.  Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. IoT Hub, IoT uygulamanız ve cihazlar arasında çift yönlü iletişim için merkezi bir ileti hub 'ı olarak davranır. 

> [!TIP]
> İsteğe bağlı olarak, [Azure Portal](iot-hub-create-through-portal.md), [Visual Studio Code](iot-hub-create-use-iot-toolkit.md)veya diğer programlı yöntemleri kullanarak bir Azure Kaynak grubu, IoT Hub ve diğer kaynakları oluşturabilirsiniz.  

1. Bir kaynak grubu oluşturmak için [az Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) komutunu çalıştırın. Aşağıdaki komut *eastus* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur. 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. IoT Hub 'ı oluşturmak için [az IoT Hub Create](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-create) komutunu çalıştırın. IoT Hub 'ı oluşturmak birkaç dakika sürebilir. 

    *Youriothubname*. Aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin. IoT Hub 'ı adı Azure 'da genel olarak benzersiz olmalıdır. Bu yer tutucu, IoT Hub 'ınızın adını göstermek için bu hızlı başlangıçtaki geri kalanında kullanılır.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-and-monitor-a-device"></a>Cihaz oluşturma ve izleme
Bu bölümde, ilk CLı oturumunda bir sanal cihaz oluşturacaksınız. Sanal cihaz, IoT Hub 'ınıza cihaz telemetri gönderir. İkinci CLı oturumunda, olayları ve Telemetriyi izler ve sanal cihaza buluttan cihaza bir ileti gönderirsiniz.

Sanal cihaz oluşturmak ve başlatmak için:
1. İlk CLı oturumunda [az IoT Hub Device-Identity Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) komutunu çalıştırın. Bu, sanal cihaz kimliğini oluşturur. 

    *Youriothubname*. Aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin. 

    *Simdevice*. Bu adı doğrudan bu hızlı başlangıçta sanal cihaz için kullanabilirsiniz. İsteğe bağlı olarak, farklı bir ad kullanın. 

    ```azurecli
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName} 
    ```

1. İlk CLı oturumunda [az IoT Device simülasyonu](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/device?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-device-simulate) komutunu çalıştırın.  Bu, sanal cihazı başlatır. Cihaz, IoT Hub 'ınıza telemetri gönderir ve bundan gelen iletileri alır.  

    *Youriothubname*. Aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

Bir cihazı izlemek için:
1. İkinci CLı oturumunda, [az IoT Hub Monitor-Events](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-monitor-events) komutunu çalıştırın. Bu, sanal cihazı izlemeye başlar. Çıkış, sanal cihazın IoT Hub 'ına gönderdiği Telemetriyi gösterir.

    *Youriothubname*. Aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin. 

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

    ![İzleme olaylarını Cloud Shell](media/quickstart-send-telemetry-cli/cloud-shell-monitor.png)

1. Sanal cihazı ikinci CLı oturumunda izledikten sonra, izlemeyi durdurmak için CTRL + C tuşlarına basın. 

## <a name="use-the-cli-to-send-a-message"></a>CLı kullanarak bir ileti gönderin
Bu bölümde, sanal cihaza bir ileti göndermek için ikinci CLı oturumunu kullanırsınız.

1. İlk CLı oturumunda, sanal cihazın çalıştığını onaylayın. Cihaz durdurulmuşsa, başlatmak için aşağıdaki komutu çalıştırın:

    *Youriothubname*. Aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. İkinci CLı oturumunda, [az IoT Device C2D-Message Send](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/device/c2d-message?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-device-c2d-message-send) komutunu çalıştırın. Bu, IoT Hub 'ınızdan sanal cihaza buluttan cihaza bir ileti gönderir. İleti bir dize ve iki anahtar-değer çifti içerir.  

    *Youriothubname*. Aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin. 

    ```azurecli
    az iot device c2d-message send -d simDevice --data "Hello World" --props "key0=value0;key1=value1" -n {YourIoTHubName}
    ```
    İsteğe bağlı olarak, Azure portal kullanarak buluttan cihaza iletiler gönderebilirsiniz. Bunu yapmak için IoT Hub genel bakış sayfasına göz atın, **IoT cihazları**' nı seçin, sanal cihazı seçin ve **cihaza mesaj**' ı seçin. 

1. İlk CLı oturumunda, sanal cihazın iletiyi aldığını onaylayın. 

    ![Buluttan cihaza ileti Cloud Shell](media/quickstart-send-telemetry-cli/cloud-shell-receive-message.png)

1. İletiyi görüntüleyip, ikinci CLı oturumunu kapatın. İlk CLı oturumunu açık tutun. Daha sonraki bir adımda kaynakları temizlemek için bunu kullanırsınız.

## <a name="view-messaging-metrics-in-the-portal"></a>Portalda mesajlaşma ölçümlerini görüntüleme
Azure portal, IoT Hub ve cihazlarınızın tüm yönlerini yönetmenizi sağlar. Cihazlardan Telemetriyi gösteren tipik bir IoT Hub uygulamasında cihazları izlemek veya cihaz telemetrisi üzerinde ölçümleri görüntülemek isteyebilirsiniz. 

Azure portal ileti ölçümlerini görselleştirmek için:
1. Portalın sol gezinti menüsünde **tüm kaynaklar**' ı seçin. Bu, oluşturduğunuz IoT Hub 'ı dahil olmak üzere aboneliğinizdeki tüm kaynakları listeler. 

1. Oluşturduğunuz IoT Hub 'ında bağlantıyı seçin. Portal, merkez için genel bakış sayfasını görüntüler.

1. IoT Hub sol bölmede **ölçümler** ' i seçin. 

    ![IoT Hub mesajlaşma ölçümleri](media/quickstart-send-telemetry-cli/iot-hub-portal-metrics.png)

1. **Kapsamınızdan**IoT Hub 'ınızın adını girin.

2. **Ölçüm ad**alanında *IoT Hub standart ölçümleri* ' ni seçin.

3. **Ölçümde** *kullanılan toplam ileti sayısını* seçin. 

4. Fare işaretçinizi, cihazınızın ileti gönderdiği zaman çizelgesi alanının üzerine getirin. Zaman çizelgesinin sol alt köşesinde bulunan bir noktadaki toplam ileti sayısı görüntülenir.

    ![Azure IoT Hub ölçümlerini görüntüleme](media/quickstart-send-telemetry-cli/iot-hub-portal-view-metrics.png)

5. İsteğe bağlı olarak, sanal cihazınızdaki diğer ölçümleri göstermek için **ölçüm** açılan listesini kullanın. Örneğin, *C2D ileti teslimleri tamamlandı* veya *Toplam cihaz (Önizleme)* . 

## <a name="clean-up-resources"></a>Kaynakları temizleme
Bu hızlı başlangıçta oluşturulan Azure kaynaklarına artık ihtiyacınız yoksa, Azure CLı 'yi kullanarak bunları silebilirsiniz.

Önerilen bir sonraki makaleye devam ederseniz, önceden oluşturduğunuz kaynakları tutabilir ve yeniden kullanabilirsiniz. 

> [!IMPORTANT]
> Silinen kaynak grupları geri alınamaz. Kaynak grubu ve içindeki tüm kaynaklar kalıcı olarak silinir. Yanlış kaynak grubunu veya kaynakları yanlışlıkla silmediğinizden emin olun. 

Bir kaynak grubunu adıyla silmek için:
1. [Az Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) komutunu çalıştırın. Bu, kaynak grubunu, IoT Hub ve oluşturduğunuz cihaz kaydını kaldırır.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Kaynak grubunun silindiğini onaylamak için [az Group List](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-list) komutunu çalıştırın.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Sonraki adımlar
Bu hızlı başlangıçta IoT Hub 'ı oluşturmak, sanal cihaz oluşturmak, telemetri göndermek, telemetri izlemek, bir buluttan cihaza ileti göndermek ve kaynakları temizlemek için Azure CLı 'yi kullandınız. Cihazınızda mesajlaşma ölçümlerini görselleştirmek için Azure portal kullandınız.

Bir cihaz geliştiricisiyseniz, önerilen sonraki adım C için Azure IoT cihaz SDK 'sını kullanan telemetri hızlı başlangıcını görmenize yöneliktir. Isteğe bağlı olarak, tercih ettiğiniz dil veya SDK 'daki kullanılabilir Azure IoT Hub telemetri hızlı başlangıç makalelerinden birine bakın.

> [!div class="nextstepaction"]
> [Hızlı başlangıç: bir cihazdan IoT Hub 'ına telemetri gönderme (C)](quickstart-send-telemetry-c.md)
