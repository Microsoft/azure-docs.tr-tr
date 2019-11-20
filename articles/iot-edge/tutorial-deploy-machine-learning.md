---
title: 'Öğretici: bir cihaza Azure Machine Learning dağıtma-Azure IoT Edge'
description: Bu öğreticide bir Azure Machine Learning modeli oluşturup bir uç cihaza modül olarak dağıtırsınız
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 2d56d434fbdaf8c05c4d4db75bffb65468a5bc42
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114040"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Öğretici: Azure Machine Learning'i bir IoT Edge modülü olarak dağıtma (önizleme)

Machine Learning modülünü geliştirmek ve Azure IoT Edge çalıştıran bir Linux cihazına dağıtmak için Azure Notebooks kullanın. 

İş mantığınızı uygulayan kodu doğrudan IoT Edge cihazlarınıza dağıtmak için IoT Edge modüllerini kullanabilirsiniz. Bu öğreticide simülasyon makinesi sıcaklık verilerini temel alarak bir cihazın arızalanacağı zamanı tahmin eden bir Azure Machine Learning modülünü dağıtma adımları açıklanmaktadır. IoT Edge Azure Machine Learning hakkında daha fazla bilgi için bkz. [Azure Machine Learning belgeleri](../machine-learning/service/how-to-deploy-to-iot.md).

Bu öğreticide oluşturduğunuz Azure Machine Learning modülü, cihazınızın ürettiği ortam verilerini okur ve iletileri normal veya anormal olarak etiketler.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bir Azure Machine Learning modülü oluşturma
> * Azure kapsayıcı kayıt defterine bir modülü kapsayıcısı gönderme
> * IoT Edge cihazınıza bir Azure Machine Learning modülü dağıtma
> * Oluşturulan verileri görüntüleme

>[!NOTE]
>Azure IoT Edge üzerindeki Azure Machine Learning modülleri genel önizleme sürümündedir.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Önkoşullar

Bir Azure IoT Edge cihazı:

* [Linux](quickstart-linux.md)için Hızlı Başlangıç bölümündeki adımları Izleyerek bir Azure sanal makinesini IoT Edge cihaz olarak kullanabilirsiniz.
* Azure Machine Learning modülü Windows kapsayıcılarını desteklemez.
* Azure Machine Learning modülü ARM İşlemcileri desteklemez.

Bulut kaynakları:

* Azure'da ücretsiz veya standart katman [IoT Hub'ı](../iot-hub/iot-hub-create-through-portal.md).
* Azure Machine Learning çalışma alanı. Bir tane oluşturmak için Azure Machine Learning kullanmaya başlamak ve nasıl kullanılacağını öğrenmek için [Azure Portal kullanma](../machine-learning/service/quickstart-get-started.md) bölümündeki yönergeleri izleyin.
   * Çalışma alanı adı, kaynak grubu ve abonelik KIMLIĞI ' ni bir yere getirin. Bu değerler, Azure portal çalışma alanına genel bakış üzerinden kullanılabilir. Bu değerleri öğreticide daha sonra çalışma alanı kaynaklarınıza bir Azure Not defteri bağlamak için kullanacaksınız. 


## <a name="create-and-deploy-azure-machine-learning-module"></a>Azure Machine Learning modülünü oluşturun ve dağıtın

Bu bölümde, eğitilen makine öğrenme modeli dosyalarını ve bir Azure Machine Learning kapsayıcısına dönüştürürsünüz. Docker görüntüsü için gerekli tüm bileşenler [Azure IoT Edge için AI Toolkit deposunda](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial) mevcuttur. Kapsayıcıyı oluşturmak ve Azure Container Registry göndermek için bu depoyu Microsoft Azure Notebooks 'e yüklemek için bu adımları izleyin.


1. Azure Notebooks projelerinize gidin. [Azure portal](https://portal.azure.com) Azure Machine Learning çalışma alanınızdan veya Azure hesabınızla [Microsoft Azure Notebooks](https://notebooks.azure.com/home/projects) oturum açarak buradan edinebilirsiniz.

2. **GitHub deposunu karşıya yükle**' yi seçin.

3. Şu GitHub deposu adını sağlayın: `Azure/ai-toolkit-iot-edge`. Projenizin özel kalmasını istiyorsanız **ortak** kutunun işaretini kaldırın. **Içeri aktar**' ı seçin. 

4. İçeri aktarma işlemi tamamlandıktan sonra, yeni **AI-araç seti-IoT-Edge** projesine gidin ve **IoT Edge anomali algılama öğreticisi** klasörünü açın. 

5. Projenizin çalıştığını doğrulayın. Aksi takdirde, **ücretsiz işlem üzerinde Çalıştır**' ı seçin.

   ![Ücretsiz işlem üzerinde Çalıştır](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. **Aml_config/config.exe JSON** dosyasını açın.

7. Yapılandırma dosyasını, Azure abonelik KIMLIĞINIZ, aboneliğinizdeki bir kaynak grubu ve Azure Machine Learning çalışma alanı adı değerlerini içerecek şekilde düzenleyin. Azure 'daki çalışma alanınızın **genel bakış** bölümünden tüm bu değerleri alabilirsiniz. 

8. Yapılandırma dosyasını kaydedin.

9. **00-anomali-algılama-öğretici. ipynb** dosyasını açın.

10. İstendiğinde, **Python 3,6** çekirdeğini seçin ve ardından **çekirdeği ayarla**' yı seçin.

11. Not defterindeki ilk hücreyi, açıklamalarındaki yönergelere göre düzenleyin. Yapılandırma dosyasına eklediğiniz aynı kaynak grubunu, abonelik KIMLIĞINI ve çalışma alanı adını kullanın.

12. Not defterindeki hücreleri seçerek ve **Çalıştır** ' a tıklayarak veya `Shift + Enter`' ya basarak, öğeleri çalıştırın.

    >[!TIP]
    >Anomali algılama öğreticisi not defterindeki hücrelerden bazıları, bazı kullanıcıların henüz bir IoT Hub gibi bir veya daha fazla sahip olmadığı kaynaklar oluşturduğundan, isteğe bağlıdır. Mevcut kaynak bilgilerinizi ilk hücreye yerleştirirseniz, Azure yinelenen kaynaklar oluşturmadığından yeni kaynak oluşturan hücreleri çalıştırırsanız hata alırsınız. Bu sorun iyidir; hataları yoksayabilirsiniz veya bu isteğe bağlı bölümleri tamamen atlayabilirsiniz. 

Not defterindeki tüm adımları tamamlayarak, bir anomali algılama modeli eğitilmiş ve onu Docker kapsayıcı görüntüsü olarak derlediniz ve bu görüntüyü Azure Container Registry gönderdiniz. Ardından, modeli test edersiniz ve son olarak IoT Edge cihazınıza dağıttınız. 

## <a name="view-container-repository"></a>Kapsayıcı deposunu görüntüle

Kapsayıcı resminizin, makine öğrenimi ortamınızla ilişkili Azure Container Registry 'de başarıyla oluşturulup depolanmadığını denetleyin. Önceki bölümde kullandığınız Not defteri, kapsayıcı görüntüsünü ve kayıt defteri kimlik bilgilerini IoT Edge cihazınıza otomatik olarak sağladı, ancak bilgileri daha sonra bulabilmeniz için nerede depolandığını bilmeniz gerekir. 

1. [Azure Portal](https://portal.azure.com), Machine Learning hizmeti çalışma alanınıza gidin. 

2. **Genel bakış** bölümü, çalışma alanı ayrıntılarının yanı sıra ilişkili kaynakları listeler. Çalışma alanınızın adı ve ardından rastgele sayılar olması gereken **kayıt defteri** değerini seçin. 

3. Kapsayıcı kayıt defterinde **depolar**' ı seçin. Önceki bölümde çalıştırdığınız Not defteri tarafından oluşturulan **tempanoydetection** adlı bir depo görmeniz gerekir. 

4. **Tempanoi algılama**' yı seçin. Deponun bir etiket olduğunu görmeniz gerekir: **1**. 

   Artık kayıt defteri adını, depo adını ve etiketini öğrendikmiş olduğunuza göre, kapsayıcının tam görüntü yolunu bilirsiniz. Görüntü yolları **\<registry_name\>. azurecr.io/tempanomalydetection:1**gibi görünür. Bu kapsayıcıyı IoT Edge cihazlara dağıtmak için görüntü yolunu kullanabilirsiniz. 

5. Kapsayıcı kayıt defterinde **erişim tuşları**' nı seçin. **Oturum açma sunucusu** ve **Kullanıcı adı**da dahil olmak üzere bir dizi erişim kimlik bilgisi ve Yönetici Kullanıcı **parolası** görmeniz gerekir.

   Bu kimlik bilgileri, IoT Edge cihazınızı kayıt defterinden çekme kapsayıcı görüntülerine erişim sağlamak için dağıtım bildirimine dahil edilebilir. 

Artık Machine Learning kapsayıcı resminin nerede depolandığını bilirsiniz. Sonraki bölümde, IoT Edge cihazınızda modül olarak çalışan kapsayıcıyı görüntüleme adımları gösterilmektedir. 

## <a name="view-generated-data"></a>Oluşturulan verileri görüntüleme

Her bir IoT Edge modülü tarafından oluşturulan ve IoT hub'ınıza gönderilen iletileri görüntüleyebilirsiniz.

### <a name="view-data-on-your-iot-edge-device"></a>IoT Edge cihazınızdaki verileri görüntüleme

IoT Edge cihazınızda her bir modülden gönderilen iletileri görüntüleyebilirsiniz.

`iotedge` komutlarını çalıştırmak için yükseltilmiş izinler için `sudo` kullanmanız gerekebilir. Oturumunuzu kapatıp cihazınızda yeniden oturum açmak, izinlerinizi otomatik olarak güncelleştirir.

1. IoT Edge cihazınızda tüm modülleri görüntüleyin.

   ```cmd/sh
   iotedge list
   ```

2. Belirli bir cihazdan gönderilen iletileri görüntüleyin. Önceki komutun çıktısında yer alan modül adını kullanın.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>IoT hub'ınıza ulaşan verileri görüntüleme

[Visual Studio Code Için azure IoT Hub araç seti uzantısını](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (eski adıyla Azure IoT araç seti uzantısı) kullanarak IoT Hub 'ınızın aldığı cihazdan buluta iletileri görüntüleyebilirsiniz.

Aşağıdaki adımlar, IoT hub'ınıza ulaşan cihazdan buluta iletileri izlemek için yapmanız gereken Visual Studio Code ayarlarını göstermektedir.

1. Visual Studio Code'da **IoT Hub Cihazları**'nı seçin.

2. Menüden **...** öğesini, sonra **IoT Hub Bağlantı Dizesini Ayarla**'yı seçin.

   ![IOT Hub bağlantı dizesine ayarlayın](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Sayfanın üstünde açılan metin kutusuna IoT Hub'ınız için iothubowner bağlantı dizesini girin. IoT Edge cihazınız IoT Hub Cihazları listesinde görünmelidir.

4. Yeniden seçin **.** .. sonra **, yerleşik olay uç noktasını izlemeyi Başlat '** ı seçin.

5. tempSensor her beş saniyede bir gelen iletileri gözlemleyin. İleti gövdesi, machinelearningmodule 'un doğru veya yanlış bir değer ile sağladığı **anomali**adlı bir özellik içerir. Model başarıyla çalıştıysa, **AzureMLResponse** özelliği "OK" değerini içerir.

   ![İleti gövdesinde yanıt Azure Machine Learning](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki önerilen makaleye geçmeyi planlıyorsanız, oluşturduğunuz kaynaklarla yapılandırmaları tutabilir ve yeniden kullanabilirsiniz. Aynı IoT Edge cihazını test cihazı olarak kullanmaya devam edebilirsiniz.

Geçmeyecekseniz ücret kesilmesini önlemek için yerel yapılandırmalarınızı ve bu makalede oluşturulan Azure kaynaklarını silebilirsiniz.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure Machine Learning ile çalışan bir IoT Edge modülü dağıttınız. Azure IoT Edge'in verileri iş içgörüsüne çevirmenize yardımcı olabilecek diğer yollar hakkında bilgi edinmek için diğer öğreticilere geçebilirsiniz.

> [!div class="nextstepaction"]
> [Özel Görüntü İşleme hizmetiyle görüntüleri sınıflandırma](tutorial-deploy-custom-vision.md)

