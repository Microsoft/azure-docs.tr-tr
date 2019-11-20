---
title: 'Öğretici: Azure IoT Edge Machine Learning ortamı ayarlama'
description: 'Öğretici: ortamınızı, uçtan makine öğrenimi için geliştirme ve modüllerin dağıtımı için hazırlayın.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2ea4248ebaedd318e4112e41169f72bc80b1120f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114062"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Öğretici: IoT Edge makine öğrenimi için bir ortam ayarlama

> [!NOTE]
> Bu makale, IoT Edge Azure Machine Learning kullanımı hakkında öğretici için bir serinin bir parçasıdır. Bu makaleye doğrudan ulaşdıysanız, en iyi sonuçlar için serideki [ilk makaleyle](tutorial-machine-learning-edge-01-intro.md) başlamanızı öneririz.

IoT Edge öğreticideki uçtan uca Azure Machine Learning Bu makalede, ortamınız geliştirme ve dağıtım için hazırlanmanıza yardımcı olur. İlk olarak, ihtiyacınız olan tüm araçlarla bir geliştirme makinesi ayarlayın. Daha sonra, Azure 'da gerekli bulut kaynaklarını oluşturun.

## <a name="set-up-a-development-machine"></a>Geliştirme makinesi ayarlama

Bu adım genellikle bir bulut geliştiricisi tarafından gerçekleştirilir. Bazı yazılımlar da bir veri bilimcı için yararlı olabilir.

Bu makalenin kursunda IoT Edge modülleri ve IoT cihazlarını kodlama, derleme, yapılandırma ve dağıtma gibi çeşitli geliştirici görevlerini gerçekleştirdik. Kullanım kolaylığı için, zaten yapılandırılmış bir çok önkoşul içeren bir Azure sanal makinesi oluşturan bir PowerShell betiği oluşturduk. Oluşturduğumuz sanal makinenin [iç içe sanallaştırmayı](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)işleyebilmesi gerekir, bu nedenle [Standard_D8s_v3](../virtual-machines/windows/sizes-general.md#dsv3-series-1) makine boyutu seçtik.

Geliştirme sanal makinesi şu şekilde ayarlanır:

* Windows 10
* [Chocolatey](https://chocolatey.org/)
* [Windows için Docker Desktop](https://www.docker.com/products/docker-desktop)
* [Windows için Git](https://gitforwindows.org/)
* [Windows için git kimlik bilgileri Yöneticisi](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [.Net Core SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.1.0)
* [VS Code uzantıları](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

Geliştirici VM 'si kesinlikle gerekli değildir – tüm geliştirme araçları yerel bir makinede çalıştırılabilir. Bununla birlikte, bir düzey yürütülüyor alanı sağlamak için VM 'nin kullanılması önemle önerilir.

Sanal makineyi oluşturmak ve yapılandırmak yaklaşık 30 dakika sürer.

### <a name="get-the-script"></a>Betiği al

[Machine Learning ve IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) örnek deposundan PowerShell betiğini kopyalayın veya indirin.

### <a name="create-an-azure-virtual-machine"></a>Azure sanal makinesi oluşturma

DevVM dizini, bu öğreticiyi tamamlamak için uygun bir Azure sanal makinesi oluşturmak için gereken dosyaları içerir.

1. PowerShell 'i yönetici olarak açın ve kodu indirdiğiniz dizine gidin. `<srcdir>`olarak kaynağınız için kök dizine başvuracağız.

    ```powershell
    cd <srcdir>\IoTEdgeAndMlSample\DevVM
    ```

2. Betiklerin yürütülmesine izin vermek için aşağıdaki komutu çalıştırın. İstendiğinde **Evet** ' i seçin.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

3. Bu dizinden Create-AzureDevVM. ps1 ' i çalıştırın.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    * İstendiğinde, aşağıdaki bilgileri sağlayın:
      * **Azure ABONELIK kimliği**: Azure Portal bulunan abonelik kimliğiniz
      * **Kaynak grubu adı**: Azure 'da yeni veya var olan bir kaynak grubunun adı
      * **Konum**: sanal makinenin oluşturulacağı Azure konumunu seçin. Örneğin, westus2 veya northeurope. Daha fazla bilgi için bkz. [Azure konumları](https://azure.microsoft.com/global-infrastructure/locations/).
      * **AdminUserName**: sanal makinede oluşturmak ve kullanmak istediğiniz yönetici hesabı için hatırlayabileceğiniz bir ad sağlayın.
      * **AdminPassword**: sanal makinede yönetici hesabı için bir parola ayarlayın.

    * Azure PowerShell yüklü değilse, betik [Azure PowerShell az Module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.1.0) yükler

    * Azure'da oturum açmanız istenir.

    * Betik, sanal makinenizin oluşturulmasına ilişkin bilgileri onaylar. Devam etmek için `y` veya `Enter` tuşuna basın.

Komut dosyası aşağıdaki adımları yürüttüğünde birkaç dakika çalışır:

* Mevcut değilse kaynak grubunu oluşturun
* Sanal makineyi dağıtma
* VM 'de Hyper-V ' d i etkinleştirme
* Geliştirme için yazılım gereksinimini yükler ve örnek depoyu kopyalayın
* VM 'yi yeniden başlatma
* SANAL makineye bağlanmak için masaüstünüzde bir RDP dosyası oluşturma

### <a name="set-auto-shutdown-schedule"></a>Otomatik kapatılma zamanlamasını ayarla

Maliyeti azaltmaya yardımcı olmak için VM, 1900 PST olarak ayarlanan bir otomatik kapatılma zamanlaması ile oluşturulmuştur. Konumunuza ve zamanlamanıza bağlı olarak bu zamanlamayı güncelleştirmeniz gerekebilir. Kapalı zamanlamayı güncelleştirmek için:

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. Önceki bölümde belirttiğiniz kaynak grubundaki sanal makinenize gidin.

3. Yan gezgin üzerinde **otomatik olarak kapatmaları** ' i seçin.

4. **Zamanlanan kapanmaya** yeni bir kapatılma süresi girin veya **saat dilimini** değiştirip **Kaydet**' e tıklayın.

### <a name="connect-and-configure-development-machine"></a>Geliştirme makinesini bağlama ve yapılandırma

Bir VM oluşturduğumuz artık öğreticiyi tamamlamaya yönelik gereken yazılımı yüklemeyi tamamlamamız gerekir.

#### <a name="start-a-remote-desktop-session"></a>Uzak Masaüstü oturumu başlatma

1. VM oluşturma betiği masaüstünüzde bir RDP dosyası oluşturdu.

2. **\<Azure VM adı\>. rdp**adlı dosyaya çift tıklayın.

3. Uzak bağlantı yayımcısının bilinmediğini belirten bir iletişim kutusu görüntülenir. **Bu bilgisayara bağlantı için bana bir daha sorma** onay kutusuna tıklayın ve ardından **Bağlan**' ı seçin.

4. İstendiğinde, VM 'yi ayarlamak için betiği çalıştırırken kullandığınız AdminPassword 'yi sağlayın ve **Tamam**' a tıklayın.

5. VM için sertifikayı kabul etmeniz istenir. **Bu bilgisayara bağlantı için bir daha sorma** ' yı seçin ve **Evet**' i seçin.

#### <a name="install-visual-studio-code-extensions"></a>Visual Studio Code uzantıları 'nı yükler

Geliştirme makinesine bağlandığınıza göre, geliştirme deneyimini kolaylaştırmak için Visual Studio Code bazı yararlı uzantılar ekleyin.

1. Bir PowerShell penceresinde, **C:\\source\\ıotedgeandmlsample\\DevVM dizinine**gidin.

2. Yazarak sanal makinede betiklerin yürütülmesine izin verin.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope CurrentUser -Force
    ```

3. Betiği çalıştırın.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

4. Betik, VS Code uzantılarını yüklerken birkaç dakika çalışır:

    * Azure IoT Araçları
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>IoT Hub ve depolamayı ayarlama

Bu adımlar genellikle bir bulut geliştiricisi tarafından gerçekleştirilir.

Azure IoT Hub, tüm IoT uygulamalarının kalbidir. IoT cihazları ve bulutu arasındaki güvenli iletişimi işler. IoT Edge Machine Learning çözümünün çalışması için ana düzenleme noktasıdır.

* IoT Hub, IoT cihazlarından gelen verileri diğer aşağı akış hizmetlerine yönlendirmek için yollar kullanır. Kalan kullanım ömrü (RUL) sınıflandırıcımızı eğitebilmemiz için Azure Machine Learning tarafından tüketilen Azure depolama 'ya cihaz verileri göndermek için IoT Hub yolların avantajlarından yararlanacağız.

* Öğreticide daha sonra, Azure IoT Edge cihazımızı yapılandırmak ve yönetmek için IoT Hub kullanacağız.

Bu bölümde, bir Azure IoT Hub 'ı ve bir Azure depolama hesabı oluşturmak için bir komut dosyası kullanırsınız. Ardından, hub tarafından alınan verileri Azure portal kullanarak bir Azure Depolama Blobu kapsayıcısına ileten bir yol yapılandırırsınız. Bu adımların tamamlanması yaklaşık 10 dakika sürer.

### <a name="create-cloud-resources"></a>Bulut kaynakları oluşturma

1. Geliştirme makinenizde bir PowerShell penceresi açın.

1. Iothub dizinine geçin.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Oluşturma betiğini çalıştırın. Geliştirme sanal makinesini oluştururken yaptığınız gibi abonelik KIMLIĞI, konum ve kaynak grubu için aynı değerleri kullanın.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location
    <location> -ResourceGroupName <resource group>
    ```

    * Azure'da oturum açmanız istenir.
    * Betik, hub ve depolama hesabınızın oluşturulmasına ilişkin bilgileri onaylar. Devam etmek için `y` veya `Enter` tuşuna basın.

Betiğin çalıştırılması iki dakika sürer. Komut dosyası tamamlandıktan sonra hub ve depolama hesabı adını verir.

### <a name="review-route-to-storage-in-iot-hub"></a>IoT Hub depolama alanını gözden geçirme

IoT Hub 'ı oluşturmanın bir parçası olarak, önceki bölümde çalıştık bir komut dosyası özel bir uç nokta ve bir yol de oluşturulmuştur. IoT Hub yollar bir sorgu ifadesinden ve bir uç noktadan oluşur. Bir ileti ifadesiyle eşleşiyorsa, veriler ilişkili uç noktaya yol üzerinden gönderilir. Uç noktalar Event Hubs, Service Bus kuyrukları ve konuları olabilir. Bu durumda, uç nokta depolama hesabındaki bir blob kapsayıcısıdır. Betiğimizden oluşturulan yolu gözden geçirmek için Azure portal kullanalım.

1. [Azure portalı](https://portal.azure.com) açın.

1. Sol gezgin 'den tüm hizmetler ' i seçin, arama kutusuna IoT yazın ve **IoT Hub**' yi seçin.

1. Önceki adımda oluşturulan IoT Hub seçin.

1. IoT Hub tarafı Gezgini ' nde **ileti yönlendirme**' yi seçin.

1. İleti yönlendirme sayfasında iki sekme, **yol** ve **Özel uç nokta**vardır. **Özel uç noktalar** sekmesini seçin.

1. **BLOB depolama**altında, **Turbofandevicesstorage**' ı seçin.

1. Bu uç noktanın, **ıotedgeandml\<benzersiz sonek\>** olarak adlandırılan son adımda oluşturulan depolama hesabındaki **devicedata** adlı bir blob kapsayıcısını işaret ettiğini unutmayın.

1. Ayrıca, **BLOB dosya adı biçiminin** varsayılan biçimden olarak değiştirildiğini, bunun yerine bölümü ada göre son öğe olarak yerleştirmeniz gerektiğini de aklınızda bulabilirsiniz. Bu biçimi, öğreticide daha sonra Azure Notebooks yapacağız dosya işlemleri için daha uygun olduğunu bulduk.

1. **İleti yönlendirme** sayfasına dönmek için uç nokta ayrıntıları dikey penceresini kapatın.

1. **Rotalar** sekmesini seçin.

1. **Turbofandevicedatatostorage**adlı yolu seçin.

1. Yolun uç noktasının **Turbofandevicesstorage** özel uç noktası olduğunu unutmayın.

1. **Doğru**olarak ayarlanan yönlendirme sorgusuna bakın. Bu, tüm cihaz telemetri iletilerinin bu rotayla eşleştiği anlamına gelir ve bu nedenle tüm iletiler **Turbofandevicesstorage** uç noktasına gönderilir.

1. Yol ayrıntılarını kapatın.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede bir IoT Hub oluşturdunuz ve bir Azure depolama hesabına yol yapılandırdınız. Bir sonraki makalede, depolama hesabına IoT Hub aracılığıyla bir sanal cihaz kümesinden veri gönderecağız. Öğreticide daha sonra, IoT Edge cihazımuzu ve modüllerimizi yapılandırdıktan sonra yönlendirmeleri geri ziyaret edeceğiz ve yönlendirme sorgusunda biraz daha fazla görüneceğiz.

IoT Edge öğreticisindeki Machine Learning bu bölümünde ele alınan adımlar hakkında daha fazla bilgi için bkz.:

* [Azure IoT Temel Konuları](https://docs.microsoft.com/azure/iot-fundamentals/)
* [IoT Hub ile ileti yönlendirmeyi yapılandırma](../iot-hub/tutorial-routing.md)
* [Azure portal kullanarak IoT Hub 'ı oluşturma](../iot-hub/iot-hub-create-through-portal.md)

İzlenecek sanal bir cihaz oluşturmak için sonraki makaleye devam edin.

> [!div class="nextstepaction"]
> [Cihaz verileri oluştur](tutorial-machine-learning-edge-03-generate-data.md)
