---
title: 'Öğretici: Azure IoT Edge üzerinde sanal cihaz verileri oluşturma-Machine Learning'
description: Öğretici-daha sonra bir makine öğrenimi modelini eğitebilmek için kullanılabilen sanal cihazlar oluşturun.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/20/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d5fdd762834d351119116c5e4854dd4233671c29
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463138"
---
# <a name="tutorial-generate-simulated-device-data"></a>Öğretici: sanal cihaz verileri oluşturma

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Bu makalede, Azure IoT Hub telemetri gönderen bir cihazın benzetimini yapmak için makine öğrenimi eğitim verilerini kullanırız. Bu öğretici, giriş bölümünde belirtildiği gibi, eğitim ve test için bir uçak motoru kümesinden verilerin benzetimini yapmak için [turbofan motor düşme simülasyonu veri kümesini](https://c3.nasa.gov/dashlink/resources/139/) kullanır.

Deneysel senaryolarımızda şunları biliyoruz:

* Veriler birden fazla çok değişkenli zaman serisinden oluşur.
* Her veri kümesi, eğitimlere ve test alt kümelerine bölünmüştür.
* Her bir serinin farklı bir altyapıdan olduğu her zaman.
* Her motor farklı derecenin ilk giyme ve üretim çeşitlemesi ile başlar.

Bu öğreticide, tek bir veri kümesinin eğitim verisi alt kümesini kullanıyoruz (FD003).

Gerçekte, her altyapının bağımsız bir IoT cihazı olması gerekir. Internet 'e bağlı bir türbofa altyapısı koleksiyonunuz yoksa, bu cihazlar için bir yazılım için tek bir yazılım oluşturacağız.

Simülatör, IoT Hub ile sanal cihazları programlı bir şekilde kaydetmek için IoT Hub API 'Leri kullanan bir C# programıdır. Ardından, her bir cihazın verilerini NASA tarafından sunulan veri alt kümesinden okur ve sanal bir IoT cihazı kullanarak IoT Hub 'ınıza gönderir. Öğreticinin bu kısmına ait tüm kod, deponun Devicebir dizininde bulunabilir.

Devicebir projesi, dört sınıftan oluşan C# dilinde yazılmış bir .NET Core Projýdýr:

* **Program:** Kullanıcı girişini ve genel koordinasyonu işlemekten sorumlu yürütme için giriş noktası.
* **Traıningfilemanager:** Seçili veri dosyasını okumaktan ve ayrıştırmaktan sorumludur.
* **Verisi cycledata:** İleti biçimine dönüştürülen bir dosyadaki tek bir veri satırını temsil eder.
* **Turbofandevice:** Tek bir cihaza (zaman serisi) karşılık gelen ve verileri IoT Hub iletmek için bir IoT cihazı oluşturmaktan sorumludur.

Bu makalede açıklanan görevlerin tamamlanması yaklaşık 20 dakika sürer.

Bu adımdaki işe yönelik gerçek hayatta büyük olasılıkla cihaz geliştiricileri ve bulut geliştiricileri tarafından gerçekleştirilmesi olasıdır.

Öğreticinin bu bölümünde şunları yapmayı öğreneceksiniz:

> [!div class="checklist"]
>
> * Geliştirme ortamınıza dış bir proje ekleyin.
> * Sanal IoT cihaz verileri oluşturmak için örnek Devicebir Project kullanın.
> * IoT Hub oluşturulan verileri görüntüleyin.

## <a name="prerequisites"></a>Önkoşullar

Bu makale, IoT Edge Azure Machine Learning kullanımı hakkında öğretici için bir serinin bir parçasıdır. Serideki her makale, önceki makaledeki iş üzerinde oluşturulur. Bu makaleye doğrudan ulaşdıysanız, serideki [ilk makaleyi](tutorial-machine-learning-edge-01-intro.md) ziyaret edin.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Visual Studio Code yapılandırma ve Devicebir proje oluşturma

1. Geliştirme sanal makinenize Uzak Masaüstü oturumu açın.

1. Visual Studio Code 'da `C:\source\IoTEdgeAndMlSample\DeviceHarness` klasörünü açın.

1. Bu makinede uzantıları ilk kez kullandığınızdan, bazı uzantılar bağımlılıklarını güncelleştirecek ve yükleyecek. Uzantıyı güncelleştirmeniz istenebilir. Öyleyse, **pencereyi yeniden yükle**' yi seçin.

   Çıkış penceresinde OmniSharp hataları görünürse C# uzantısını kaldırmanız gerekir.

1. Device, için gerekli varlıkları eklemeniz istenecektir. Eklemek için **Evet** ' i seçin.

   * Bildirimin görünmesi birkaç saniye sürebilir.
   * Bu bildirimi kaçırdıysanız sağ alt köşedeki zil simgesine göz atın.

   ![VS Code uzantısı açılan kutusu](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Paket bağımlılıklarını geri yüklemek için **geri yükle** ' yi seçin.

   ![VS Code geri yükleme istemi](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

   Bu bildirimleri alamazsanız Visual Studio Code kapatın, içindeki bin ve obj dizinlerini silin `C:\source\IoTEdgeAndMlSample\DeviceHarness` , Visual Studio Code açın ve Devicebandı klasörünü yeniden açın.

1. Bir derleme, **CTRL**  +  **vardiyası**  +  **B** veya **Terminal**  >  **çalıştırma oluşturma görevi** tetikleyerek ortamınızın düzgün şekilde ayarlandığını doğrulayın.

1. Çalıştırılacak derleme görevini seçmeniz istenir. **Oluştur**' u seçin.

1. Derleme çalışır ve başarılı bir ileti verir.

   ![Derleme başarılı çıkış iletisi](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Bu derlemeyi varsayılan derleme görevi yap   >  **varsayılan derleme görevini Yapılandır...** seçeneğini belirleyerek ve istemden **Oluştur** seçeneğini belirleyerek yapabilirsiniz.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>IoT Hub Bağlan ve Devicebir şekilde Çalıştır

Proje binamız olduğuna göre, bağlantı dizesine erişmek ve veri oluşturma işleminin ilerlemesini izlemek için IoT Hub 'ınıza bağlanın.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Visual Studio Code’da Azure oturumu açma

1. Komut paletini açarak `Ctrl + Shift + P` veya   >  **komut paletini** görüntüleyerek Visual Studio Code Azure aboneliğinizde oturum açın.

1. **Azure: oturum aç** komutunu arayın.

   Bir tarayıcı penceresi açılır ve sizden kimlik bilgilerinizi ister. Bir başarı sayfasına yeniden yönlendirildiğinde, tarayıcıyı kapatabilirsiniz.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>IoT Hub 'ınıza bağlanın ve hub bağlantı dizesini alın

1. Visual Studio Code Gezgini ' nin alt bölümünde, genişletmek için **Azure IoT Hub** çerçevesini seçin.

1. Genişletilmiş çerçevede **IoT Hub Seç**' e tıklayın.

1. İstendiğinde, Azure aboneliğinizi ve ardından IoT Hub 'ınızı seçin.

1. Daha fazla eylem için **Azure IoT Hub** sağındaki **...** seçeneğine tıklayın. **IoT Hub bağlantı dizesini Kopyala**' yı seçin.

   ![IoT Hub bağlantı dizesini Kopyala](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Devicebir projeyi Çalıştır

1.   >  Visual Studio Code terminalini açmak için **terminali** görüntüle ' yi seçin.

   Bir istem görmüyorsanız, ENTER tuşuna basın.

1. Terminale `dotnet run` girin.

1. IoT Hub bağlantı dizesi istendiğinde, önceki bölümde kopyaladığınız bağlantı dizesini yapıştırın.

1. **Azure IoT Hub cihazları** çerçevesinde Yenile düğmesine tıklayın.

   ![IoT Hub cihaz listesini Yenile](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Cihazların IoT Hub eklendiğini ve verilerin bu cihaz aracılığıyla gönderildiğini göstermek için cihazların yeşil renkte görünür olduğunu unutmayın. Cihazlar IoT Hub 'ına ileti gönderdikten sonra bağlantı kesilir ve mavi görünür.

1. Herhangi bir cihaza sağ tıklayıp **Izlemeyi Başlat yerleşik olay uç noktasını** seçerek hub 'a gönderilen iletileri görüntüleyebilirsiniz. İletiler, Visual Studio Code ' deki çıkış bölmesinde görünür.

1. **Azure IoT Hub** çıktı bölmesine tıklayarak izlemeyi durdurun ve **yerleşik olay uç noktasını İzlemeyi Durdur**' u seçin.

1. Uygulamanın tamamlanmasını, birkaç dakika sürer.

## <a name="check-iot-hub-for-activity"></a>Etkinlik IoT Hub denetle

Devicebandı tarafından gönderilen veriler IoT Hub 'ınıza gitti, burada Azure portal doğrulayabilirsiniz.

1. [Azure Portal](https://portal.azure.com/) açın ve bu öğretici Için oluşturulan IoT Hub 'ına gidin.

1. Sol bölme menüsünde, **izleme** altında **ölçümler**' i seçin.

1. Grafik tanımı sayfasında, **ölçüm** açılan listesine tıklayın, listeyi aşağı kaydırın ve **Yönlendirme: depolamaya teslim edilen veriler**' i seçin. Grafik, verilerin depoya yönlendirilme durumunu göstermelidir.

   ![Veri depolamaya teslim edildiğinde grafik ani artış gösterir](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Azure depolama 'daki verileri doğrulama

IoT Hub 'ınıza yeni gönderdiğimiz veriler, önceki makalede oluşturduğumuz depolama kapsayıcısına yönlendirildi. Depolama hesabımızda bulunan verilere göz atalım.

1. Azure portalında depolama hesabınıza gidin.

1. Depolama hesabı Gezgininde **Depolama Gezgini (Önizleme)** öğesini seçin.

1. Depolama Gezgini 'nde **BLOB kapsayıcıları** ' nı seçin `devicedata` .

1. İçerik bölmesinde, IoT Hub 'ın adının ardından yıl, ay, gün ve saat temelinde klasöre tıklayın. Verilerin yazıldığı süreyi temsil eden birkaç klasör görürsünüz.

   ![Blob depolamada klasörleri görüntüleme](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. **00** ve **01** etiketli veri dosyalarını bölüm ile ilgili olarak bulmak için bu klasörlerden birine tıklayın.

1. Dosyalar [avro](https://avro.apache.org/) biçiminde yazılır. Başka bir tarayıcı sekmesi açmak ve verileri kısmen işlemek için bu dosyalardan birine çift tıklayın. Dosyayı bir programda açmanız istenirse VS Code seçebilirsiniz ve doğru şekilde işlenir.

1. Şu anda verileri okumaya veya yorumlamaya denemeniz gerekmez; Bunu bir sonraki makalede yapacağız.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğretici, her bir makalenin bir önceki bölümde gerçekleştirilen iş üzerinde oluşturulduğu bir küme parçasıdır. Lütfen son öğreticiyi tamamlayana kadar tüm kaynakları temizlemeyi bekleyin.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir .NET Core projesi kullanarak bir sanal IoT cihazı kümesi oluşturup bunları IoT Hub 'larımıza ve bir Azure depolama kapsayıcısından veri gönderecek şekilde kullandık. Bu proje, fiziksel IoT cihazlarının bir IoT Hub veri göndereceği ve seçkin bir depolama alanına yönelik olarak gerçek dünyada bir senaryoya benzetir. Bu veriler, algılayıcı ayarlarını, işlemsel ayarları, hata sinyallerini ve modlarını ve bu şekilde devam eder. Yeterli veri toplandıktan sonra, cihaz için kalan kullanım ömrünü (RUL) tahmin eden modelleri eğitmek üzere bu uygulamayı kullanırız. Bu makine öğrenimi bir sonraki makalede gösterilmektedir.

Bir makine öğrenimi modelini verilerle eğitmek için sonraki makaleye geçin.

> [!div class="nextstepaction"]
> [Bir Azure Machine Learning modelini eğitme ve dağıtma](tutorial-machine-learning-edge-04-train-model.md)
