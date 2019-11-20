---
title: 'Öğretici: Azure IoT Edge bir model Machine Learning eğitme ve dağıtma'
description: 'Öğretici: Azure Machine Learning kullanarak bir makine öğrenimi modeli eğitme ve modeli bir Azure IoT Edge modülü olarak dağıtılabilecek kapsayıcı görüntüsü olarak paketleme.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8cb3d0329c41256823a66fc3276711c43da61cdd
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112476"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Öğretici: Azure Machine Learning modeli eğitme ve dağıtma

> [!NOTE]
> Bu makale, IoT Edge Azure Machine Learning kullanımı hakkında öğretici için bir serinin bir parçasıdır. Bu makaleye doğrudan ulaşdıysanız, en iyi sonuçlar için serideki [ilk makaleyle](tutorial-machine-learning-edge-01-intro.md) başlamanızı öneririz.

Bu makalede, Azure Machine Learning kullanarak bir makine öğrenimi modelini eğitme ve sonra bu modeli bir Azure IoT Edge modülü olarak dağıtılabilecek bir kapsayıcı görüntüsü olarak paketleme için Azure Notebooks kullanırız. Azure Notebooks, makine öğrenimi modellerini denemek, eğmek ve dağıtmak için kullanılan temel bir blok olan Azure Machine Learning çalışma alanından faydalanır.

Öğreticinin bu bölümündeki etkinlikler iki not defteri arasında bölünmüştür.

* **01-turbofan\_gerileme. ipynb:** Bu not defteri Azure Machine Learning kullanarak bir modeli eğitme ve yayımlama adımlarında size yol gösterir. Genel olarak, ilgili adımlar şunlardır:

  1. Eğitim verilerini indirin, hazırlayın ve araştırın
  2. Makine öğrenimi denemesi oluşturmak ve çalıştırmak için hizmet çalışma alanını kullanma
  3. Deneyden model sonuçlarını değerlendirin
  4. En iyi modeli hizmet çalışma alanına yayımlayın

* **02-turbofan\_\_modeli dağıt. ipynb:** Bu not defteri, önceki bir not defterinde oluşturulan modeli alır ve bir Azure IoT Edge cihazına dağıtılmaya yönelik bir kapsayıcı görüntüsü oluşturmak için kullanır.

  1. Model için bir Puanlama betiği oluşturun
  2. Görüntü oluşturma ve yayımlama
  3. Görüntüyü Azure Container Instance üzerinde Web hizmeti olarak dağıtma
  4. Modeli doğrulamak için Web hizmetini kullanma ve görüntünün beklendiği gibi çalışması

Bu makaledeki adımlar genellikle veri bilimcileri tarafından gerçekleştirilmiş olabilir.

## <a name="set-up-azure-notebooks"></a>Azure Notebooks ayarlama

İki jupi Not defterini ve destekleyici dosyaları barındırmak için Azure Notebooks kullanırız. Burada bir Azure Notebooks projesi oluşturacağız ve yapılandıracağız. Jupyter ve/veya Azure Notebooks kullanmadıysanız, bir dizi giriş belgesi aşağıda verilmiştir:

* **Hızlı başlangıç:** [Not defteri oluşturma ve paylaşma](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Öğretici:** [Python Ile Jupyter Not defteri oluşturma ve çalıştırma](../notebooks/tutorial-create-run-jupyter-notebook.md)

Geliştirici sanal makinesinde daha önce olduğu gibi Azure Not defterleri kullanmak, alıştırma için tutarlı bir ortam sağlar.

> [!NOTE]
> Ayarladıktan sonra, Azure Notebooks hizmetine herhangi bir makineden erişilebilir. Kurulum sırasında, ihtiyacınız olacak tüm dosyaları içeren geliştirme sanal makinesini kullanmanız gerekir.

### <a name="create-an-azure-notebooks-account"></a>Azure Notebooks hesabı oluşturma

Azure Not defteri hesapları Azure aboneliklerinden bağımsızdır. Azure Notebooks kullanmak için bir hesap oluşturmanız gerekir.

1. [Azure Not defterleri](https://notebooks.azure.com)' ne gidin.

2. Sayfanın sağ üst köşesinde **bulunan oturum aç** ' a tıklayın.

3. İş veya okul hesabınızla (Azure Active Directory) ya da kişisel hesabınızla (Microsoft hesabı) oturum açın.

4. Daha önce Azure Notebooks kullanmadıysanız, Azure Notebooks uygulamasına erişim vermeniz istenir.

5. Azure Notebooks için bir kullanıcı KIMLIĞI oluşturun.

### <a name="upload-jupyter-notebooks-files"></a>Jupyıter Not defteri dosyalarını karşıya yükle

Bu adımda, yeni bir Azure Notebooks projesi oluşturur ve bu dosyayı karşıya yükleyin. Karşıya yüklemediğimiz dosyalar özellikle:

* **01-turbofan\_gerileme. ipynb**: Azure Storage hesabından cihaz bandı tarafından oluşturulan verileri indirme işlemini adım adım gösteren Jupyter Not defteri dosyası; sınıflandırıcının eğitimi için verileri keşfetme ve hazırlama; modeli eğitme; Test\_FD003. txt dosyasında bulunan test veri kümesini kullanarak verileri test etme; ve son olarak, Machine Learning hizmet çalışma alanına sınıflandırıcı modelini kaydetme.

* **02-turbofan\_\_modeli dağıt. ipynb:** Bir kapsayıcı görüntüsü oluşturmak için Machine Learning hizmeti çalışma alanına kaydedilen sınıflandırıcı modelini kullanma sürecinde size kılavuzluk eden Jupyter Not defteri. Görüntü oluşturulduktan sonra Not defteri, görüntüyü bir Web hizmeti olarak dağıtma sürecinde size kılavuzluk eder; böylece, beklendiği gibi çalıştığını doğrulayabilirsiniz. Doğrulanan görüntü, Bu öğreticinin [özel IoT Edge modülleri oluştur ve dağıt](tutorial-machine-learning-edge-06-custom-modules.md) bölümünde IoT Edge cihazımız cihaza dağıtılır.

* **Test\_FD003. txt:** Bu dosya, eğitilen sınıflandırıcımız doğrulanırken test kümesi olarak kullanacağız verileri içerir. Örnek basitliği için test kümesi olarak özgün yarışma göre belirtilen test verilerini kullanmayı seçtik.

* **Rul\_FD003. txt:** Bu dosya, test\_FD003. txt dosyasındaki her bir cihazın son döngüsüyle ilgili RUL 'yi içerir. Verilerin ayrıntılı bir açıklaması için bkz. C:\\Source\\ıotedgeandmlsample\\\\Data içindeki **README. txt** ve **hasar yayma modellemesi. PDF** dosyaları.

* **Utils.py:** Verilerle çalışmak için bir Python yardımcı program işlevleri kümesi içerir. İlk not defteri, işlevlerin ayrıntılı bir açıklamasını içerir.

* **README.MD:** Not defterlerinin kullanımını açıklayan Benioku dosyası.

Yeni bir proje oluşturun ve dosyaları Not defterinize yükleyin.

1. Üst menü çubuğundan **Projelerim** ' nı seçin.

1. **+ Yeni proje**' yi seçin. Bir ad ve KIMLIK girin. Projenin genel olması veya bir Benioku dosyası olması gerekmez.

1. **Karşıya yükle** ' yi seçin ve **bilgisayar**' ı seçin.

1. **Dosya Seç**' i seçin.

1. **C:\source\ıotedgeandmlsample\azurenotebooks**dizinine gidin. Tüm dosyalar ' ı seçin ve **Aç**' a tıklayın.

1. Karşıya yüklemeye başlamak için **karşıya yükle** ' yi seçin ve sonra işlem tamamlandıktan sonra **bitti** ' yi seçin.

## <a name="run-azure-notebooks"></a>Azure Notebooks Çalıştır

Proje oluşturuldığına göre, **01-turbofan\_gerileme. ipynb** Not defterini çalıştırın.

1. Turbofan proje sayfasında, **01-turbofa\_regresyon. ipynb**' yi seçin.

    ![Çalıştırılacak ilk not defterini seçin](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

2. İstenirse, iletişim kutusunda Python 3,6 çekirdeğini seçin ve **çekirdeği ayarla**' yı seçin.

3. Not defteri **güvenilir değil**olarak listeleniyorsa, Not defterinin sağ üst köşesindeki **güvenilir değil** pencere öğesine tıklayın. İletişim kutusu geldiğinde **güven**' i seçin.

4. Not defterindeki yönergeleri izleyin.

    * `Ctrl + Enter` bir hücre çalıştırır.
    * `Shift + Enter` bir hücre çalıştırır ve sonraki hücreye gider.
    * Bir hücre çalışırken, köşeli ayraçlar arasında **[\*]** gibi bir yıldız işareti vardır. İşlem tamamlandığında, yıldız işareti bir sayıyla değişir ve aşağıda ilgili bir çıktı aşağıda bulunabilir. Hücreler genellikle önceki çalışmaların çalışmasına göre oluşturulduğundan, tek seferde yalnızca bir hücre çalıştırılabilir.

5. **01-turbofan\_gerileme. ipynb** Not defterini çalıştırmayı bitirdiğinizde proje sayfasına dönün.

6. **02-turbofa\_deploy\_model. ipynb** ' nı açın ve ikinci not defterini çalıştırmak için bu bölümdeki adımları tekrarlayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, geriye kalan bir kullanım ömrü (RUL) sınıflandırıcısını eğitmek, bir kapsayıcı görüntüsü oluşturmak ve görüntüyü bir Web örneği olarak dağıtmak ve test etmek için turbofa cihazlarındaki verileri kullanmak üzere Azure Notebooks çalıştıran iki JUPITER Not defteri kullandık. rvice.

IoT Edge bir cihaz oluşturmak için sonraki makaleye devam edin.

> [!div class="nextstepaction"]
> [IoT Edge cihaz yapılandırma](tutorial-machine-learning-edge-05-configure-edge-device.md)
