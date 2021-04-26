---
title: 'Öğretici: Azure IoT Edge bir model Machine Learning eğitme ve dağıtma'
description: Bu öğreticide, Azure Machine Learning kullanarak bir makine öğrenimi modeli eğtireceksiniz ve sonra modeli bir Azure IoT Edge modülü olarak dağıtılabilecek bir kapsayıcı görüntüsü olarak paketleyebilirsiniz.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 16d5fe90be71f39d448e4c1ce10c0373f6bfc86c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463111"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Öğretici: Azure Machine Learning modeli eğitme ve dağıtma

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Bu makalede, aşağıdaki görevleri yaptık:

* Machine Learning modelini eğitmek için Azure Machine Learning Studio kullanın.
* Eğitilen modeli kapsayıcı görüntüsü olarak paketleyin.
* Kapsayıcı görüntüsünü Azure IoT Edge modülü olarak dağıtın.

Machine Learning Studio, makine öğrenimi modellerini denemek, eğmek ve dağıtmak için kullanılan temel bir bloğudur.

Bu makaledeki adımlar genellikle veri bilimcileri tarafından gerçekleştirilmiş olabilir.

Öğreticinin bu bölümünde şunları yapmayı öğreneceksiniz:

> [!div class="checklist"]
> * Makine öğrenimi modelini eğitmek için bir Azure Machine Learning çalışma alanında jupi Not Defterleri oluşturun.
> * Eğitilen makine öğrenme modelini kapsayıcıya geçirin.
> * Kapsayıcılı makine öğrenimi modelinden bir IoT Edge modülü oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu makale, IoT Edge Machine Learning kullanımı hakkında öğretici için bir serinin bir parçasıdır. Serideki her makale, önceki makaledeki iş üzerinde oluşturulur. Bu makaleye doğrudan ulaşdıysanız, serideki [ilk makaleye](tutorial-machine-learning-edge-01-intro.md) bakın.

## <a name="set-up-azure-machine-learning"></a>Azure Machine Learning ayarlama

İki jupi Not defterini ve destekleyici dosyaları barındırmak için Machine Learning Studio kullanırız. Burada bir Machine Learning projesi oluşturup yapılandırdık. Jupyter veya Machine Learning Studio kullanmadıysanız, iki giriş belgesi aşağıda verilmiştir:

* **Jupyter Notebook**: [Visual Studio Code Jupyter Not defterleri ile çalışma](https://code.visualstudio.com/docs/python/jupyter-support)
* **Azure Machine Learning**: [Jupyter not defterlerinde Azure Machine Learning kullanmaya başlama](../machine-learning/tutorial-1st-experiment-sdk-setup.md)

> [!NOTE]
> Hizmet kurulduktan sonra, Machine Learning herhangi bir makineden erişilebilir. Kurulum sırasında, ihtiyacınız olan tüm dosyaları içeren geliştirme sanal makinesini kullanmanız gerekir.

### <a name="install-azure-machine-learning-visual-studio-code-extension"></a>Azure Machine Learning Visual Studio Code uzantısını yükler

Geliştirme sanal makinesinde Visual Studio Code Bu uzantının yüklü olması gerekir. Farklı bir örnek üzerinde çalıştırıyorsanız, uzantıyı [Visual Studio Code uzantısını ayarlama](../machine-learning/tutorial-setup-vscode-extension.md)bölümünde açıklandığı gibi yeniden yükleyin.

### <a name="create-an-azure-machine-learning-account"></a>Azure Machine Learning hesabı oluşturma

Azure 'da kaynakları sağlamak ve iş yüklerini çalıştırmak için Azure hesabı kimlik bilgilerinizle oturum açın.

1. Visual Studio Code menüsünde, menü çubuğundan komut paleti **görüntüle**' yi seçerek komut paleti ' ni açın  >   .

1. `Azure: Sign In`Oturum açma işlemini başlatmak için komut paletinde komutunu girin. Oturum açmayı tamamlamaya yönelik yönergeleri izleyin.

1. İş yükünüzü çalıştırmak için bir Machine Learning işlem örneği oluşturun. Komut paletinde komutunu girin `Azure ML: Create Compute` .
1. Azure aboneliğinizi seçin.
1. **+ Yeni Azure ML çalışma alanı oluştur**' u seçin ve **turbofandemo** adını girin.
1. Bu demo için kullandığınız kaynak grubunu seçin.
1. Visual Studio Code pencerenizin sağ alt köşesinde çalışma alanı oluşturma işleminin ilerlemesini görmeniz gerekir: **çalışma alanı oluşturuluyor: turobofandemo**. Bu adım bir veya iki dakika sürebilir.
1. Çalışma alanının başarıyla oluşturulmasını bekleyin. **Azure ML çalışma alanı turbofandemo 'nin oluşturulmasını** söylemelidir.

### <a name="upload-jupyter-notebook-files"></a>Jupyter Notebook dosyalarını karşıya yükle

Örnek Not defteri dosyalarını yeni bir Machine Learning çalışma alanına yükleyeceğiz.

1. Ml.azure.com adresine gidin ve oturum açın.
1. Microsoft dizininizi, Azure aboneliğinizi ve yeni oluşturulan Machine Learning çalışma alanını seçin.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-studio-workspace.png" alt-text="Azure Machine Learning çalışma alanınızı seçmeyi gösteren ekran görüntüsü." :::

1. Machine Learning çalışma alanınızda oturum açtıktan sonra, sol taraftaki menüyü kullanarak **Not defterleri** bölümüne gidin.
1. **My Files** sekmesini seçin.

1. **Karşıya yükle** (yukarı ok simgesi) seçeneğini belirleyin.

1. **C:\source\ıotedgeandmlsample\azurenotebooks** dizinine gidin. Listedeki tüm dosyaları seçin ve **Aç**' ı seçin.

1. **Bu dosyaların içeriğine güvendim** onay kutusunu seçin.

1. Karşıya yüklemeye başlamak için **karşıya yükle** ' yi seçin. Sonra işlem tamamlandıktan sonra **bitti** ' yi seçin.

### <a name="jupyter-notebook-files"></a>Jupyter Notebook dosyaları

Machine Learning çalışma alanınıza yüklediğiniz dosyaları gözden geçirelim. Öğreticinin bu bölümündeki etkinlikler, birkaç destekleyici dosya kullanan iki Not Defteri dosyasında yayılamaz.

* **01-turbofan \_ regresyon. ipynb**: Bu not defteri, Machine Learning denemesi oluşturmak ve çalıştırmak için Machine Learning çalışma alanını kullanır. Genel olarak, Not defteri aşağıdaki adımları yapar:

  1. Cihaz bandı tarafından oluşturulan Azure Storage hesabından verileri indirir.
  1. Verileri araştırır ve hazırlar ve ardından sınıflandırıcı modelini eğitmek için verileri kullanır.
  1. Test veri kümesi (testFD003.txt) kullanarak deneyden modeli değerlendirir \_ .
  1. En iyi sınıflandırıcı modelini Machine Learning çalışma alanına yayınlar.

* **02-turbofan \_ dağıtım \_ modeli. ipynb**: Bu not defteri, önceki not defterinde oluşturulan modeli alır ve bir IoT Edge cihazına dağıtılmaya yönelik bir kapsayıcı görüntüsü oluşturmak için kullanır. Not defteri aşağıdaki adımları gerçekleştirir:

  1. Model için bir Puanlama betiği oluşturur.
  1. Machine Learning çalışma alanında kaydedilen sınıflandırıcı modelini kullanarak bir kapsayıcı görüntüsü üretir.
  1. Görüntüyü Azure Container Instances Web hizmeti olarak dağıtır.
  1. , Modeli doğrulamak için Web hizmetini kullanır ve görüntü beklendiği gibi çalışır. Doğrulanan görüntü, Bu öğreticinin [özel IoT Edge modülleri oluştur ve dağıt](tutorial-machine-learning-edge-06-custom-modules.md) bölümünde IoT Edge cihazımız cihaza dağıtılır.

* **Test \_FD003.txt**: Bu dosya, eğitilen sınıflandırıcımızın doğrulanması sırasında test kümesi olarak kullanacağımız verileri içerir. Kendi basitliği için test kümesi olarak, özgün yarışma göre belirtilen test verilerini kullanmayı seçtik.
* **Rul \_FD003.txt**: Bu dosya, testFD003.txt dosyasındaki her bir cihazın son döngüsü için kalan yararlı yaşam süresini (rul) içerir \_ . \\ \\ \\ \\ Verilerin ayrıntılı bir açıklaması için C: Source ıotedgeandmlsample Data ' deki readme.txt ve hasar yayma Modeling.pdf dosyalarına bakın.
* **Utils.py**: Bu dosya, verilerle çalışmaya yönelik bir Python yardımcı programı işlevleri içerir. İlk not defteri, işlevlerin ayrıntılı bir açıklamasını içerir.
* **README.MD**: bu Benioku dosyası, Not defterlerinin kullanımını açıklar.

## <a name="run-the-jupyter-notebooks"></a>Jupyıter not defterlerini çalıştırma

Çalışma alanı oluşturuldığına göre, artık not defterlerini çalıştırabilirsiniz.

1. **My Files** sayfasında, **01-turbofan \_ regresyon. ipynb**' yi seçin.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-turbofan-notebook.png" alt-text="Çalıştırılacak ilk not defterini seçen ekran görüntüsü.":::

1. Not defteri **güvenilir değil** olarak listeleniyorsa, Not defteri 'nin sağ üst köşesindeki **güvenilir değil** pencere öğesini seçin. İletişim kutusu göründüğünde **güven**' i seçin.

1. En iyi sonuçlar için, her bir hücrenin belgelerini okuyun ve tek tek çalıştırın. Araç çubuğunda **Çalıştır** ' ı seçin. Daha sonra, birden çok hücre çalıştırmak için bir bilgi edinebilirsiniz. Yükseltme ve kullanımdan kaldırma uyarılarını yoksayabilirsiniz.

    Bir hücre çalışırken, köşeli ayraçlar ([]) arasında bir yıldız işareti görüntüler \* . Hücrenin işlemi tamamlandığında, yıldız işareti bir sayıyla değişir ve ilgili çıktı görünebilir. Bir not defterindeki hücreler sıralı olarak ve tek seferde yalnızca bir hücre çalışabilir.

    Ayrıca, **hücre** menüsündeki Çalıştır seçeneklerini kullanabilirsiniz. Bir hücre çalıştırmak için **CTRL + ENTER** ' ı seçin ve bir hücre çalıştırmak ve sonraki hücreye Ilerlemek için **SHIFT + enter** ' u seçin.

    > [!TIP]
    > Tutarlı hücre işlemleri için tarayıcınızda birden çok sekmeden aynı not defterini çalıştırmaktan kaçının.

1. **Set global Properties** yönergelerini izleyen hücrede, Azure aboneliğinizin, ayarlarınızın ve kaynaklarınızın değerlerini girin. Sonra hücreyi çalıştırın.

    ![Not defterindeki genel özellikleri ayarlamayı gösteren ekran görüntüsü.](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. **Çalışma alanı ayrıntılarının** önceki hücresinde, çalıştıktan sonra, kimlik doğrulaması için oturum açmanızı sağlayan bağlantıyı arayın.

    ![Cihaz kimlik doğrulaması için oturum açma isteğini gösteren ekran görüntüsü.](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Bağlantıyı açın ve belirtilen kodu girin. Bu oturum açma yordamı, Microsoft Azure platformlar arası komut satırı arabirimini kullanarak Azure kaynaklarına erişmek için Jupyter Not defterinin kimliğini doğrular.

    ![Cihaz onayındaki uygulamanın kimliğini doğrulama işlemini gösteren ekran görüntüsü.](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. **Sonuçları keşfetmeye** önce gelen hücrede, çalışma kimliği ' nden değeri kopyalayın ve **bir çalıştırma edilmeyen** izleyen hücrede çalıştırma kimliği için yapıştırın.

   ![Çalışma KIMLIĞINI hücreler arasında kopyalamayı gösteren ekran görüntüsü.](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. Not defterinde kalan hücreleri çalıştırın.

1. Not defterini kaydedin ve proje sayfanıza geri dönün.

1. **02-turbofan \_ Deploy \_ model. ipynb**' nı açın ve her hücreyi çalıştırın. **Çalışma alanını Yapılandır**' ın bulunduğu hücrede kimlik doğrulaması yapmak için oturum açmanız gerekir.

1. Not defterini kaydedin ve proje sayfanıza geri dönün.

### <a name="verify-success"></a>Başarıyı doğrula

Not defterlerinin başarıyla tamamlandığını doğrulamak için, birkaç öğenin oluşturulduğunu doğrulayın.

1. Machine Learning Not defteri **My Files** sekmesinde **Yenile**' yi seçin.

1. Aşağıdaki dosyaların oluşturulduğunu doğrulayın.

    | Dosya | Description |
    | --- | --- |
    | ./aml_config/.azureml/config.js | Machine Learning çalışma alanını oluşturmak için kullanılan yapılandırma dosyası. |
    | ./aml_config/model_config.jsaçık | Modeli Azure 'daki **Turbofandemo** Machine Learning çalışma alanına dağıtmak için gereken yapılandırma dosyası. |
    | MyENV. yıml| Dağıtılan Machine Learning modeli için bağımlılıklar hakkında bilgi sağlar.|

1. Aşağıdaki Azure kaynaklarının oluşturulduğunu doğrulayın. Bazı kaynak adlarına rastgele karakterler eklenir.

    | Azure kaynağı | Name |
    | --- | --- |
    | Azure Machine Learning çalışma alanı | turborfanDemo |
    | Azure Container Registry | turbofandemoxxxxxxxx |
    | Application Insights | turbofanınsilerxxxxxxxx |
    | Azure Key Vault | turbofankeyvaultbxxxxxxxx |
    | Azure Depolama | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>Hata Ayıklama

Hata ayıklama için not defterine Python deyimleri ekleyebilirsiniz, örneğin, `print()` değerleri gösterme komutu. Tanımlı olmayan değişkenler veya nesneler görürseniz, ilk olarak bildirildiği veya örneklendiği hücreleri çalıştırın.

Not defterlerini yeniden yapmanız gerekiyorsa, önceden oluşturulmuş dosyaları ve Azure kaynaklarını silmeniz gerekebilir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğretici, her bir makalenin bir önceki bölümde gerçekleştirilen iş üzerinde oluşturulduğu bir küme parçasıdır. Son öğreticiyi tamamlamadan tüm kaynakları temizlemeyi bekleyin.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, turbofa cihazlarındaki verileri kullanmak üzere Machine Learning Studio çalıştıran iki Jupyter Not defterini kullandık:

- RUL sınıflandırıcısını eğitme.
- Sınıflandırıcısını model olarak kaydedin.
- Kapsayıcı görüntüsü oluşturun.
- Görüntüyü bir Web hizmeti olarak dağıtın ve test edin.

IoT Edge bir cihaz oluşturmak için sonraki makaleye devam edin.

> [!div class="nextstepaction"]
> [IoT Edge cihazını yapılandırma](tutorial-machine-learning-edge-05-configure-edge-device.md)