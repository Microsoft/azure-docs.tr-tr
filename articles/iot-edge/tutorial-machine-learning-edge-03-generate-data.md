---
title: 'Öğretici: Azure IoT Edge üzerinde sanal cihaz verileri oluşturma-Machine Learning'
description: 'Öğretici: bir makine öğrenimi modelini eğitebilmek için daha sonra kullanılabilecek sanal cihazlar oluşturun.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 51d93e5b83d203f3fa99b69cc5f2877bbfdb6fb1
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112862"
---
# <a name="tutorial-generate-simulated-device-data"></a>Öğretici: sanal cihaz verileri oluşturma

> [!NOTE]
> Bu makale, IoT Edge Azure Machine Learning kullanımı hakkında öğretici için bir serinin bir parçasıdır. Bu makaleye doğrudan ulaşdıysanız, en iyi sonuçlar için serideki [ilk makaleyle](tutorial-machine-learning-edge-01-intro.md) başlamanızı öneririz.

Bu makalede, IoT Hub telemetri gönderen bir cihazın benzetimini yapmak için makine öğrenimi eğitim verilerini kullanırız. Giriş bölümünde belirtildiği gibi, bu uçtan uca öğreticide, eğitim ve test için bir uçak altyapısından veri benzetimi yapmak üzere [Türbofan motor düşme simülasyonu veri kümesi](https://c3.nasa.gov/dashlink/resources/139/) kullanılmaktadır.

Eşlik eden Readme. txt ' den sonra şunları biliyoruz:

* Veriler birden fazla çok değişkenli zaman serisinden oluşur
* Her veri kümesi, eğitimlere ve test alt kümelerine bölünmüştür
* Her bir serinin farklı bir altyapısından olması
* Her motor farklı derecenin ilk aşınlarını ve üretim çeşitlemesiyle başlar

Bu öğreticide, tek bir veri kümesinin eğitim verisi alt kümesini kullanıyoruz (FD003).

Gerçekte, her altyapının bağımsız bir IoT cihazı olması gerekir. Internet 'e bağlı bir türbofa altyapısı koleksiyonunuz yoksa, bu cihazlar için bir yazılım için tek bir yazılım oluşturacağız.

Simülatör, IoT Hub C# ile sanal cihazları programlı bir şekilde kaydetmek Için IoT Hub API 'leri kullanan bir programdır. Ardından, her bir cihazın verilerini NASA tarafından sunulan veri alt kümesinden okur ve sanal bir IoT cihazı kullanarak IoT Hub 'ınıza gönderir. Öğreticinin bu kısmına ait tüm kod, deponun Devicebir dizininde bulunabilir.

Devicebir projesi, dört sınıftan C# oluşan bir .NET Core projýdýr:

* **Program:** Kullanıcı girişini ve genel koordinasyonu işlemekten sorumlu yürütme için giriş noktası.
* **Traıningfilemanager:** seçili veri dosyasını okumaktan ve ayrıştırmaktan sorumludur.
* **Verisi cycledata:** ileti biçimine dönüştürülen bir dosyadaki tek bir veri satırını temsil eder.
* **Turbofandevice:** verilerdeki tek bir cihaza (zaman serisi) karşılık gelen ve verileri IoT cihazı aracılığıyla IoT Hub Ileten bir IoT cihazı oluşturmaktan sorumludur.

Bu makalede açıklanan görevlerin tamamlanması yaklaşık 20 dakika sürer.

Bu adımdaki işe yönelik gerçek hayatta büyük olasılıkla cihaz geliştiricileri ve bulut geliştiricileri tarafından gerçekleştirilmesi olasıdır.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Visual Studio Code yapılandırma ve Devicebir proje oluşturma

1. Önceki makalede gösterildiği gibi, sanal makinenize bir Uzak Masaüstü oturumu açın.

1. Visual Studio Code'u açın.

1. Visual Studio Code **dosya** > **klasörü aç...** ' ı seçin.

1. **Klasör** metin kutusuna `C:\source\IoTEdgeAndMlSample\DeviceHarness` girin ve **Klasör Seç** düğmesine tıklayın.

   Çıkış penceresinde OmniSharp hataları görünürse, C# uzantıyı kaldırmanız, vs Code kapatıp yeniden açmanız, C# uzantıyı yüklemeniz ve ardından pencereyi yeniden yüklemeniz gerekir.

1. Bu makinede uzantıları ilk kez kullandığınızdan, bazı uzantılar bağımlılıklarını güncelleştirecek ve yükleyecek. Uzantıyı güncelleştirmeniz istenebilir. Öyleyse, **pencereyi yeniden yükle**' yi seçin.

1. Device, için gerekli varlıkları eklemeniz istenecektir. Eklemek için **Evet** ' i seçin.

   * Bildirimin görünmesi birkaç saniye sürebilir.
   * Bu bildirimi kaçırdıysanız sağ alt köşedeki "zil" simgesine bakın.

   ![VS Code uzantısı açılan kutusu](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Paket bağımlılıklarını geri yüklemek için **geri yükle** ' yi seçin.

   ![VS Code geri yükleme istemi](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

1. Yapı, `Ctrl + Shift + B` veya **Terminal** > **Derleme görevi çalıştıran**bir derlemeyi tetikleyerek ortamınızın düzgün şekilde ayarlandığını doğrulayın.

1. Çalıştırılacak derleme görevini seçmeniz istenir. **Oluştur**' u seçin.

1. Derleme çalışır ve başarılı bir ileti verir.

   ![Derleme başarılı çıkış iletisi](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Bu derlemeyi varsayılan derleme görevi yap > **varsayılan derleme görevini Yapılandır...** ' yı seçerek ve istemden **Build** ' **i seçerek yapabilirsiniz** .

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>IoT Hub Bağlan ve Devicebir şekilde Çalıştır

Proje binamız olduğuna göre, bağlantı dizesine erişmek ve veri oluşturma işleminin ilerlemesini izlemek için IoT Hub 'ınıza bağlanın.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Visual Studio Code 'de Azure 'da oturum açın

1. Komut paletini Visual Studio Code **`Ctrl + Shift + P` veya > ** komut paleti ' ni açarak Azure aboneliğinizde oturum açın **...** .

1. İsteminde arama yapın ve **Azure: oturum aç '** ı seçin.

1. Bir tarayıcı penceresi açılır ve sizden kimlik bilgilerinizi ister. Bir başarı sayfasına yönlendirildiğinde, tarayıcıyı kapatabilirsiniz.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>IoT Hub 'ınıza bağlanın ve hub bağlantı dizesini alın

1. Visual Studio Code Gezgini ' nin alt bölümünde, **Azure IoT Hub cihazlar** çerçevesini seçerek genişletin.

1. Genişletilmiş çerçevede **IoT Hub Seç**' e tıklayın.

1. İstendiğinde, Azure aboneliğinizi ve ardından IoT Hub 'ınızı seçin.

1. **Azure IoT Hub cihazları** çerçevesine tıklayın ve daha fazla eylem için **...** seçeneğine tıklayın. **IoT Hub bağlantı dizesini Kopyala**' yı seçin.

   ![IoT Hub bağlantı dizesini Kopyala](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Devicebir projeyi Çalıştır

1. Visual Studio Code terminalini açmak için > **terminali** **görüntüle** ' yi seçin.

   Bir istem görmüyorsanız, ENTER ' u seçin.

1. Terminalde `dotnet run` girin.

1. IoT Hub bağlantı dizesi istendiğinde, önceki bölümde kopyaladığınız bağlantı dizesini yapıştırın.

1. **Azure IoT Hub cihazları** çerçevesinde Yenile düğmesine tıklayın.

   ![IoT Hub cihaz listesini Yenile](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Cihazların IoT Hub eklendiğini ve verilerin bu cihaz aracılığıyla gönderildiğini göstermek için cihazların yeşil renkte görünür olduğunu unutmayın.

1. Herhangi bir cihaza sağ tıklayıp **Izlemeyi Başlat yerleşik olay uç noktası**' nı seçerek hub 'a gönderilen iletileri görebilirsiniz. İletiler, Visual Studio Code ' deki çıkış bölmesinde görünür.

1. **Azure IoT Hub araç seti** çıkış bölmesine tıklayarak izlemeyi durdurun ve **yerleşik olay uç noktasını İzlemeyi Durdur**' u seçin.

1. Uygulamanın tamamlanmasını, birkaç dakika sürer.

## <a name="check-iot-hub-for-activity"></a>Etkinlik IoT Hub denetle

Devicebandı tarafından gönderilen veriler IoT Hub 'ınıza gitti. Verilerin Azure portal kullanarak hub 'ınıza ulaştığından emin olmak kolaydır.

1. [Azure Portal](https://portal.azure.com/) açın ve IoT Hub 'ınıza gidin.

1. Genel Bakış sayfasında, verilerin hub 'a gönderildiğini görmeniz gerekir:  

   ![IoT Hub 'de cihazı bulut iletilerine görüntüleme](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Azure depolama 'daki verileri doğrulama

IoT Hub 'ınıza yeni gönderdiğimiz veriler, önceki makalede oluşturduğumuz depolama kapsayıcısına yönlendirildi. Depolama hesabımızda bulunan verilere göz atalım.

1. Azure portalında depolama hesabınıza gidin.

1. Depolama hesabı Gezgininde **Depolama Gezgini (Önizleme)** öğesini seçin.

1. Depolama Gezgini 'nde **BLOB kapsayıcıları** ' nı ve **devicedata**' ı seçin.

1. İçerik bölmesinde, IoT Hub adı, yıl, ay, gün ve saat için klasörü tıklatın. Verilerin yazıldığı süreyi temsil eden birkaç klasör görürsünüz.

   ![Blob depolamada klasörleri görüntüleme](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. **00** ve **01** etiketli veri dosyalarını bölüm ile ilgili olarak bulmak için bu klasörlerden birine tıklayın.

1. Dosyalar [avro](https://avro.apache.org/) biçiminde yazılır, ancak bu dosyalardan birine çift tıklandığında başka bir tarayıcı sekmesi açılır ve veriler kısmen işlenir. Bunun yerine dosyayı bir programda açmanız istenirse VS Code seçebilirsiniz ve doğru şekilde işlenir.

1. Şu anda verileri okumaya veya yorumlamaya denemeniz gerekmez; Bunu bir sonraki makalede yapacağız.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir sanal cihaz kümesi oluşturmak ve bu cihazlar aracılığıyla IoT Hub ve bir Azure depolama kapsayıcısına veri göndermek için bir .NET Core projesi kullandık. Bu proje, fiziksel cihazların algılayıcı readler, işlem ayarları, hata sinyalleri ve modlar gibi verileri, bir IoT Hub ve bir depolama alanı üzerinde bir şekilde bir şekilde gönderdiğini, gerçek dünyada bir senaryoya benzetir. Yeterli veri toplandıktan sonra, bir sonraki makalede gösterdiğimiz cihaz için kalan yararlı yaşam süresini (RUL) tahmin eden modelleri eğitmek üzere bu işlemi kullanırız.

Bir makine öğrenimi modelini verilerle eğitmek için sonraki makaleye geçin.

> [!div class="nextstepaction"]
> [Azure Machine Learning modeli eğitme ve dağıtma](tutorial-machine-learning-edge-04-train-model.md)
