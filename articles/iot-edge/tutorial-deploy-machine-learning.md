---
title: Öğretici-Azure IoT Edge kullanarak bir cihaza Azure Machine Learning dağıtma
description: Bu öğreticide bir Azure Machine Learning modeli oluşturup bir uç cihaza modül olarak dağıtırsınız
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: b3192c3ee8458623f3724da5f875cc09032a42bf
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219425"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Öğretici: Azure Machine Learning'i bir IoT Edge modülü olarak dağıtma (önizleme)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Machine Learning modülünü geliştirmek ve Linux kapsayıcılarıyla Azure IoT Edge çalıştıran bir cihaza dağıtmak için Azure Notebooks kullanın.
İş mantığınızı uygulayan kodu doğrudan IoT Edge cihazlarınıza dağıtmak için IoT Edge modüllerini kullanabilirsiniz. Bu öğreticide simülasyon makinesi sıcaklık verilerini temel alarak bir cihazın arızalanacağı zamanı tahmin eden bir Azure Machine Learning modülünü dağıtma adımları açıklanmaktadır. IoT Edge Azure Machine Learning hakkında daha fazla bilgi için bkz. [Azure Machine Learning belgeleri](../machine-learning/how-to-deploy-and-where.md).

>[!NOTE]
>Azure IoT Edge üzerindeki Azure Machine Learning modülleri genel önizleme sürümündedir.

Bu öğreticide oluşturduğunuz Azure Machine Learning modülü, cihazınızın ürettiği ortam verilerini okur ve iletileri normal veya anormal olarak etiketler.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Azure Machine Learning modülü oluşturun.
> * Bir modül kapsayıcısını Azure Container Registry 'ye gönderin.
> * IoT Edge cihazınıza Azure Machine Learning modülünü dağıtın.
> * Oluşturulan verileri görüntüleme.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bir Azure IoT Edge cihazı:

* Hızlı başlangıçlarını bir [Linux cihazı](quickstart-linux.md) veya [Windows cihazı](quickstart.md)ayarlamak için kullanabilirsiniz.
* Azure Machine Learning modülü Windows kapsayıcılarını desteklemez.
* Azure Machine Learning modülü ARM İşlemcileri desteklemez.

Bulut kaynakları:

* Azure'da ücretsiz veya standart katman [IoT Hub'ı](../iot-hub/iot-hub-create-through-portal.md).
* Azure Machine Learning çalışma alanı. Bir tane oluşturmak için Azure Machine Learning kullanmaya başlamak ve nasıl kullanılacağını öğrenmek için [Azure Portal kullanma](../machine-learning/tutorial-1st-experiment-sdk-setup.md) bölümündeki yönergeleri izleyin.
  * Çalışma alanı adı, kaynak grubu ve abonelik KIMLIĞI ' ni bir yere getirin. Bu değerler, Azure portal çalışma alanına genel bakış üzerinden kullanılabilir. Azure Notebooks bir dosyayı çalışma alanı kaynaklarınıza bağlamak için öğreticide daha sonra bu değerleri kullanacaksınız.

## <a name="create-and-deploy-azure-machine-learning-module"></a>Azure Machine Learning modülünü oluşturun ve dağıtın

Bu bölümde, eğitilen makine öğrenme modeli dosyalarını ve bir Azure Machine Learning kapsayıcısına dönüştürürsünüz. Docker görüntüsü için gerekli tüm bileşenler [Azure IoT Edge için AI Toolkit deposunda](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial) mevcuttur. Kapsayıcıyı oluşturmak ve Azure Container Registry göndermek için bu depoyu Microsoft Azure Notebooks 'e yüklemek için bu adımları izleyin.

1. Azure Notebooks projelerinize gidin. [Azure portal](https://portal.azure.com) Azure Machine Learning çalışma alanınızdan veya Azure hesabınızla [Microsoft Azure Notebooks](https://notebooks.azure.com/home/projects) oturum açarak buradan edinebilirsiniz.

2. **GitHub deposunu karşıya yükle**' yi seçin.

3. Aşağıdaki GitHub deposu adını sağlayın: `Azure/ai-toolkit-iot-edge` . Projenizin özel kalmasını istiyorsanız **ortak** kutunun işaretini kaldırın. **İçeri aktar**'ı seçin.

4. İçeri aktarma işlemi tamamlandıktan sonra, yeni **AI-araç seti-IoT-Edge** projesine gidin ve **IoT Edge anomali algılama öğreticisi** klasörünü açın.

5. Projenizin çalıştığını doğrulayın. Aksi takdirde, **ücretsiz işlem üzerinde Çalıştır**' ı seçin.

   ![Ücretsiz işlem üzerinde Çalıştır](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Dosyada **aml_config/config.js** açın.

7. Yapılandırma dosyasını, Azure abonelik KIMLIĞINIZ, aboneliğinizdeki bir kaynak grubu ve Azure Machine Learning çalışma alanı adı değerlerini içerecek şekilde düzenleyin. Azure 'daki çalışma alanınızın **genel bakış** bölümünden tüm bu değerleri alabilirsiniz.

8. Yapılandırma dosyasını kaydedin.

9. **00-anomali-algılama-öğretici. ipynb** dosyasını açın.

10. İstendiğinde, **Python 3,6** çekirdeğini seçin ve ardından **çekirdeği ayarla**' yı seçin.

11. Not defterindeki ilk hücreyi, açıklamalarındaki yönergelere göre düzenleyin. Yapılandırma dosyasına eklediğiniz aynı kaynak grubunu, abonelik KIMLIĞINI ve çalışma alanı adını kullanın.

12. Not defteri ' ni seçip **Çalıştır** ' a tıklayarak ve ardından da ' i seçerek hücreleri çalıştırın `Shift + Enter` .

    >[!TIP]
    >Anomali algılama öğreticisi not defterindeki hücrelerden bazıları, bazı kullanıcıların henüz bir IoT Hub gibi bir veya daha fazla sahip olmadığı kaynaklar oluşturduğundan, isteğe bağlıdır. Mevcut kaynak bilgilerinizi ilk hücreye yerleştirirseniz, Azure yinelenen kaynaklar oluşturmadığından yeni kaynak oluşturan hücreleri çalıştırırsanız hata alırsınız. Bu sorun iyidir; hataları yoksayabilirsiniz veya bu isteğe bağlı bölümleri tamamen atlayabilirsiniz.

Not defterindeki tüm adımları tamamlayarak, bir anomali algılama modeli eğitilmiş ve onu Docker kapsayıcı görüntüsü olarak derlediniz ve bu görüntüyü Azure Container Registry gönderdiniz. Ardından, modeli test edersiniz ve son olarak IoT Edge cihazınıza dağıttınız.

## <a name="view-container-repository"></a>Kapsayıcı deposunu görüntüle

Kapsayıcı resminizin, makine öğrenimi ortamınızla ilişkili Azure Container Registry 'de başarıyla oluşturulup depolanmadığını denetleyin. Önceki bölümde kullandığınız Not defteri, kapsayıcı görüntüsünü ve kayıt defteri kimlik bilgilerini IoT Edge cihazınıza otomatik olarak sağladı, ancak bilgileri daha sonra bulabilmeniz için nerede depolandığını bilmeniz gerekir.

1. [Azure Portal](https://portal.azure.com), Machine Learning hizmeti çalışma alanınıza gidin.

2. **Genel bakış** bölümü, çalışma alanı ayrıntılarının yanı sıra ilişkili kaynakları listeler. Çalışma alanınızın adı ve ardından rastgele sayılar olması gereken **kayıt defteri** değerini seçin.

3. Kapsayıcı kayıt defterinde, **Hizmetler** altında **depolar**' ı seçin. Önceki bölümde çalıştırdığınız Not defteri tarafından oluşturulan **tempanoydetection** adlı bir depo görmeniz gerekir.

4. **Tempanoi algılama**' yı seçin. Deponun bir etiket olduğunu görmeniz gerekir: **1**.

   Artık kayıt defteri adını, depo adını ve etiketini öğrendikmiş olduğunuza göre, kapsayıcının tam görüntü yolunu bilirsiniz. Görüntü yolları **\<registry_name\> . azurecr.io/tempanomalydetection:1** gibi görünür. Bu görüntü yolunu kullanarak bu kapsayıcıyı IoT Edge cihazlarına dağıtabilirsiniz.

5. Kapsayıcı kayıt defterinde, **Ayarlar** altında **erişim anahtarları**' nı seçin. **Oturum açma sunucusu** ve **Kullanıcı adı** da dahil olmak üzere bir dizi erişim kimlik bilgisi ve Yönetici Kullanıcı **parolası** görmeniz gerekir.

   Bu kimlik bilgilerini dağıtım bildirimine dahil ederek IoT Edge cihazınızın kayıt defterindeki kapsayıcı görüntülerini çekmesini sağlayabilirsiniz.

Artık Machine Learning kapsayıcı resminin nerede depolandığını bilirsiniz. Sonraki bölümde, IoT Edge cihazınızda modül olarak çalışan kapsayıcıyı görüntüleme adımları gösterilmektedir.

## <a name="view-the-generated-data"></a>Oluşturulan verileri görüntüleme

Her bir IoT Edge modülü tarafından oluşturulan ve IoT hub'ınıza gönderilen iletileri görüntüleyebilirsiniz.

### <a name="view-data-on-your-iot-edge-device"></a>IoT Edge cihazınızdaki verileri görüntüleme

IoT Edge cihazınızda her bir modülden gönderilen iletileri görüntüleyebilirsiniz.

`sudo`Komutları çalıştırmak için yükseltilmiş izinler için kullanmanız gerekebilir `iotedge` . Oturumunuzu kapatıp cihazınızda yeniden oturum açmak, izinlerinizi otomatik olarak güncelleştirir.

1. IoT Edge cihazınızda tüm modülleri görüntüleyin.

   ```cmd/sh
   iotedge list
   ```

2. Belirli bir cihazdan gönderilen iletileri görüntüleyin. Önceki komutun çıktısında yer alan modül adını kullanın.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>IoT hub'ınıza ulaşan verileri görüntüleme

[Visual Studio Code Için Azure IoT Hub uzantısını](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)kullanarak, IoT Hub 'ınızın aldığı cihazdan buluta iletileri görüntüleyebilirsiniz.

Aşağıdaki adımlar, IoT hub'ınıza ulaşan cihazdan buluta iletileri izlemek için yapmanız gereken Visual Studio Code ayarlarını göstermektedir.

1. Visual Studio Code Gezgini ' nde, **Azure IoT Hub** bölümünde **aygıtlar** ' ı genişleterek IoT cihazları listesini görüntüleyin.

2. IoT Edge cihazınızın adına sağ tıklayın ve **Izlemeyi Başlat yerleşik olay uç noktası**' nı seçin.

3. tempSensor kaynağından beş saniyede bir gelen iletileri gözlemleyin. İleti gövdesinde, machinelearningmodule'un true veya false değeri verdiği **anomaly** adlı bir özellik bulunur. Model başarıyla çalıştıysa, **AzureMLResponse** özelliği "OK" değerini içerir.

   ![İleti gövdesinde yanıt Azure Machine Learning](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki önerilen makaleye geçmeyi planlıyorsanız, oluşturduğunuz kaynaklarla yapılandırmaları tutabilir ve yeniden kullanabilirsiniz. Aynı IoT Edge cihazını test cihazı olarak kullanmaya devam edebilirsiniz.

Geçmeyecekseniz ücret kesilmesini önlemek için yerel yapılandırmalarınızı ve bu makalede oluşturulan Azure kaynaklarını silebilirsiniz.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure Machine Learning ile çalışan bir IoT Edge modülü dağıttınız. Azure IoT Edge'in verileri iş içgörüsüne çevirmenize yardımcı olabilecek diğer yollar hakkında bilgi edinmek için diğer öğreticilere geçebilirsiniz.

> [!div class="nextstepaction"]
> [Özel Görüntü İşleme hizmetiyle görüntüleri sınıflandırma](tutorial-deploy-custom-vision.md)
