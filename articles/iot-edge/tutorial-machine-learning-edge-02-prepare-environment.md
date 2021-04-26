---
title: 'Öğretici: Azure IoT Edge Machine Learning ortamı ayarlama'
description: 'Öğretici: ortamınızı, uçtan makine öğrenimi için geliştirme ve modüllerin dağıtımı için hazırlayın.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/12/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 723d7b6ae5b96cf6dc622be1b1bb98065151787f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463162"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Öğretici: IoT Edge makine öğrenimi için bir ortam ayarlama

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Bu makalede, ortamınız geliştirme ve dağıtım için hazırlanmanıza yardımcı olur. İlk olarak, ihtiyacınız olan tüm araçlarla bir geliştirme makinesi ayarlayın. Daha sonra, Azure 'da gerekli bulut kaynaklarını oluşturun.

Öğreticinin bu bölümünde şunları yapmayı öğreneceksiniz:

> [!div class="checklist"]
>
> * Geliştirme için bir sanal makine ayarlayın.
> * Geliştirme ortamınız için kullanmak üzere bir IoT Hub ve bulut depolaması ayarlayın.

## <a name="prerequisites"></a>Önkoşullar

Bu makale, IoT Edge Azure Machine Learning kullanımı hakkında öğretici için bir serinin bir parçasıdır. Serideki her makale, önceki makaledeki iş üzerinde oluşturulur. Bu makaleye doğrudan ulaşdıysanız, serideki [ilk makaleyi](tutorial-machine-learning-edge-01-intro.md) ziyaret edin.

## <a name="set-up-the-development-vm"></a>Geliştirme sanal makinesini ayarlama

Bu adım genellikle bir bulut geliştiricisi tarafından gerçekleştirilir. Bazı yazılımlar da bir veri bilimcı için yararlı olabilir.

Zaten yapılandırılmış bir çok önkoşul içeren bir Azure sanal makinesi oluşturan bir PowerShell betiği oluşturduk. Oluşturduğumuz sanal makinenin [iç içe sanallaştırmayı](../virtual-machines/windows/nested-virtualization.md)işleyebilmesi gerekir, bu nedenle [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md) makine boyutu seçtik.

Geliştirme sanal makinesi şu şekilde ayarlanır:

* Windows 10
* [Chocolatey](https://chocolatey.org/)
* [Windows için Docker Desktop](https://www.docker.com/products/docker-desktop)
* [Windows için Git](https://gitforwindows.org/)
* [Windows için git kimlik bilgileri Yöneticisi](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [.NET Core SDK](https://dotnet.microsoft.com/)
* [Python 3 karşılaştırması](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](/powershell/azure/)
* [VS Code uzantıları](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Azure IoT Araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

Geliştirici VM 'si kesinlikle gerekli değildir – tüm geliştirme araçları yerel bir makinede çalıştırılabilir. Bununla birlikte, bir düzey yürütülüyor alanı sağlamak için VM 'nin kullanılması önemle önerilir.

Sanal makineyi oluşturmak ve yapılandırmak yaklaşık 30 dakika sürer.

1. [Machine Learning ve IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) örnek deposunu yerel bilgisayarınıza kopyalayın veya indirin.

1. PowerShell 'i yönetici olarak açın ve kodu indirdiğiniz kök dizin altında bulunan **\ıotedgeandmlsample\devvm** dizinine gidin. Kaynağınız için kök dizine başvuracağız `srcdir` .

    ```powershell
    cd c:\srcdir\IoTEdgeAndMlSample\DevVM
    ```

   DevVM dizini, bu öğreticiyi tamamlamak için uygun bir Azure sanal makinesi oluşturmak için gereken dosyaları içerir.

1. Betiklerin yürütülmesine izin vermek için aşağıdaki komutu çalıştırın. İstendiğinde **Evet** ' i seçin.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Create-AzureDevVM.ps1 çalıştırın.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    İstendiğinde aşağıdaki bilgileri girin:

    * **Azure ABONELIK kimliği**: portalda [Azure aboneliklerinde](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) bulunan abonelik Kimliğiniz.
    * **Kaynak grubu adı**: Azure 'da yeni veya var olan bir kaynak grubunun adı.
    * **Konum**: sanal makinenin oluşturulacağı Azure konumunu seçin. Örneğin, ' Batı ABD 2 ' veya ' Kuzey Avrupa '. Daha fazla bilgi için bkz. [Azure konumları](https://azure.microsoft.com/global-infrastructure/locations/).
    * **Kullanıcı adı**: VM için yönetici hesabı için hatırlayabileceğiniz bir ad sağlayın.
    * **Parola**: VM için yönetici hesabı için bir parola ayarlayın.

   Komut dosyası aşağıdaki adımları yürüttüğünde birkaç dakika çalışır:

    1. [Azure PowerShell az Module 'ü](/powershell/azure/new-azureps-module-az)yüklüyor.
    1. Azure 'da oturum açmanızı ister.
    1. VM 'nizin oluşturulmasıyla ilgili bilgileri onaylar. Devam etmek için **y** veya **ENTER** tuşuna basın.
    1. Mevcut değilse kaynak grubunu oluşturur.
    1. Sanal makineyi dağıtır.
    1. VM üzerinde Hyper-V ' d i etkinleştirilir.
    1. Geliştirme için yazılım gereksinimini ve örnek depoyu klonlayın.
    1. VM 'yi yeniden başlatır.
    1. SANAL makineye bağlanmak için masaüstünüzde bir RDP dosyası oluşturur.

   Yeniden başlatmak için VM 'nin adı istenirse, adını betik çıktısından kopyalayabilirsiniz. Çıktı Ayrıca, sanal makineye bağlanmak için RDP dosyasının yolunu gösterir.

### <a name="set-auto-shutdown-schedule"></a>Otomatik kapatılma zamanlamasını ayarla

Maliyeti azaltmanıza yardımcı olmak için, geliştirme sanal makinesi, 1900 PST olarak ayarlanan bir otomatik kapatılma zamanlaması ile oluşturulmuştur. Konumunuza ve zamanlamanıza bağlı olarak bu ayarı güncelleştirmeniz gerekebilir. Kapalı zamanlamayı güncelleştirmek için:

1. Azure portal, betiğin oluşturulduğu VM 'ye gidin.

1. Sol bölme menüsünde, **işlemler** altında **otomatik olarak kapalı**' yı seçin.

1. **Zamanlanan kapalı** ve **saat dilimini** Istediğiniz şekilde ayarlayın ve **Kaydet**' i seçin.

## <a name="connect-to-the-development-vm"></a>Geliştirme sanal makinesine bağlanma

Bir VM oluşturduğumuz artık öğreticiyi tamamlamaya yönelik gereken yazılımı yüklemeyi tamamlamamız gerekir.

1. Komut dosyasının masaüstünüzde oluşturduğu RDP dosyasına çift tıklayın.

1. Uzak bağlantı yayımcısının bilinmediğini belirten bir iletişim kutusu görüntülenir. Bu kabul edilebilir, bu nedenle **Bağlan**' ı seçin.

1. VM oluşturmak için sağladığınız yönetici parolasını girip **Tamam**' a tıklayın.

1. VM için sertifikayı kabul etmeniz istenir. **Evet**’i seçin.

## <a name="install-visual-studio-code-extensions"></a>Visual Studio Code uzantılarını yükleme

Geliştirme makinesine bağlandığınıza göre, geliştirme deneyimini kolaylaştırmak için Visual Studio Code bazı yararlı uzantılar ekleyin.

1. Geliştirme VM 'sine bağlanın, bir PowerShell penceresi açın ve **C:\source\ıotedgeandmlsample\devvm** dizinine gidin. Bu dizin, VM 'yi oluşturan komut dosyası tarafından oluşturuldu.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\DevVM
    ```

1. Betiklerin yürütülmesine izin vermek için aşağıdaki komutu çalıştırın. İstendiğinde **Evet** ' i seçin.

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Visual Studio Code uzantıları betiğini çalıştırın.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

1. Betik, VS Code uzantılarını yüklerken birkaç dakika çalışır:

    * Azure IoT Araçları
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>IoT Hub ve depolamayı ayarlama

Bu adımlar genellikle bir bulut geliştiricisi tarafından gerçekleştirilir.

Azure IoT Hub, IoT cihazları ve bulutu arasındaki güvenli iletişimi işlediği için tüm IoT uygulamalarının kalbidir. IoT Edge Machine Learning çözümünün çalışması için ana düzenleme noktasıdır.

* IoT Hub, IoT cihazlarından gelen verileri diğer aşağı akış hizmetlerine yönlendirmek için yollar kullanır. Azure depolama 'ya cihaz verileri göndermek için IoT Hub yolların avantajlarından yararlanacağız. Azure depolama 'da cihaz verileri, kalan kullanım ömrü (RUL) sınıflandırıcımızı eğitmek için Azure Machine Learning tarafından kullanılır.

* Öğreticide daha sonra, Azure IoT Edge cihazımızı yapılandırmak ve yönetmek için IoT Hub kullanacağız.

Bu bölümde, bir Azure IoT Hub 'ı ve bir Azure depolama hesabı oluşturmak için bir komut dosyası kullanırsınız. Azure portal, hub tarafından alınan verileri bir Azure depolama kapsayıcısına ileten bir rota yapılandırırsınız. Bu adımların tamamlanması yaklaşık 10 dakika sürer.

1. Geliştirme VM 'sine bağlanın, bir PowerShell penceresi açın ve **ıothub** dizinine gidin.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Oluşturma betiğini çalıştırın. Geliştirme sanal makinesini oluştururken yaptığınız gibi abonelik KIMLIĞI, konum ve kaynak grubu için aynı değerleri kullanın.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location <location> -ResourceGroupName <resource group>
    ```

    * Azure'da oturum açmanız istenir.
    * Betik, hub ve depolama hesabınızın oluşturulmasına ilişkin bilgileri onaylar. Devam etmek için **y** veya **ENTER** tuşuna basın.

Betiğin çalıştırılması yaklaşık iki dakika sürer. Komut dosyası tamamlandıktan sonra IoT Hub 'ın ve depolama hesabının adını verir.

## <a name="review-route-to-storage-in-iot-hub"></a>IoT Hub depolama alanını gözden geçirme

IoT Hub 'ı oluşturmanın bir parçası olarak, önceki bölümde çalıştık bir komut dosyası özel bir uç nokta ve bir yol de oluşturulmuştur. IoT Hub yollar bir sorgu ifadesinden ve bir uç noktadan oluşur. Bir ileti ifadesiyle eşleşiyorsa, veriler ilişkili uç noktaya yol üzerinden gönderilir. Uç noktalar Event Hubs, Service Bus kuyrukları ve konuları olabilir. Bu durumda, uç nokta depolama hesabındaki bir blob kapsayıcısıdır. Betiğimizden oluşturulan yolu gözden geçirmek için Azure portal kullanalım.

1. [Azure Portal](https://portal.azure.com) açın ve bu öğretici için kullanmakta olduğunuz kaynak grubuna gidin.

1. Kaynak listesinde, betiğin oluşturduğu IoT Hub seçin. Bu, gibi rastgele karakterlerle biten bir ada sahip olacaktır `IotEdgeAndMlHub-jrujej6de6i7w` .

1. Sol bölme menüsünde, **mesajlaşma** altında **ileti yönlendirme**' yi seçin.

1. **İleti yönlendirme** sayfasında **Özel uç noktalar** sekmesini seçin.

1. **Depolama** bölümünü genişletin:

   ![Türbofandevicesstorage 'ın özel uç noktalar listesinde olduğunu doğrulama](media/tutorial-machine-learning-edge-02-prepare-environment/custom-endpoints.png)

   **Turbofandevicesstorage** özel uç noktalar listesinde görüyoruz. Bu uç nokta hakkında aşağıdaki özelliklere göz önünde edin:

   * `devicedata` **Kapsayıcı adına** göre gösterildiği gibi, oluşturduğunuz BLOB depolama kapsayıcısını işaret eder.
   * **Dosya adı biçimi** , adı içindeki son öğe olarak bölümlenir. Bu biçimi, öğreticide daha sonra Azure Notebooks yapacağız dosya işlemleri için daha uygun olduğunu bulduk.
   * **Durumu** sağlıklı olmalıdır.

1. **Rotalar** sekmesini seçin.

1. **Turbofandevicedatatostorage** adlı yolu seçin.

1. **Rotalar ayrıntıları** sayfasında, yolun uç noktasının **Turbofandevicesstorage** uç noktası olduğunu unutmayın.

   ![TurbofanDeviceDataToStorage rotası hakkındaki ayrıntıları gözden geçirin](media/tutorial-machine-learning-edge-02-prepare-environment/route-details.png)

1. **Doğru** olarak ayarlanan **yönlendirme sorgusuna** bakın. Bu ayar, tüm cihaz telemetri iletilerinin bu rota ile eşleştiği anlamına gelir; Bu nedenle, tüm iletiler **Turbofandevicesstorage** uç noktasına gönderilir.

1. Hiçbir düzenleme yapılmazdan bu yana yalnızca bu sayfayı kapatmanız yeterlidir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğretici, her bir makalenin bir önceki bölümde gerçekleştirilen iş üzerinde oluşturulduğu bir küme parçasıdır. Lütfen son öğreticiyi tamamlayana kadar tüm kaynakları temizlemeyi bekleyin.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede bir IoT Hub oluşturdunuz ve bir Azure depolama hesabına yol yapılandırdınız. Daha sonra, depolama hesabına IoT Hub aracılığıyla bir sanal cihaz kümesinden veri gönderecağız. Öğreticide daha sonra, IoT Edge cihazımuzu ve modüllerimizi yapılandırdıktan sonra yönlendirmeleri geri ziyaret edeceğiz ve yönlendirme sorgusunda biraz daha fazla görüneceğiz.

İzlenecek sanal bir cihaz oluşturmak için sonraki makaleye devam edin.

> [!div class="nextstepaction"]
> [Cihaz verileri oluştur](tutorial-machine-learning-edge-03-generate-data.md)
