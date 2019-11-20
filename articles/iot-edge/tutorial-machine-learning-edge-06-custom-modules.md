---
title: 'Öğretici: Azure IoT Edge üzerinde özel modüller oluşturma ve dağıtma-Machine Learning'
description: 'Öğretici: bir makine öğrenimi modeli aracılığıyla yaprak cihazlardan verileri işleyen IoT Edge modülleri oluşturun ve dağıtın ve ardından öngörüleri IoT Hub gönderin.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7bfe620510d5ff88a20c518be1f4dd1fb422daa2
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74106556"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>Öğretici: özel IoT Edge modüller oluşturma ve dağıtma

> [!NOTE]
> Bu makale, IoT Edge Azure Machine Learning kullanımı hakkında öğretici için bir serinin bir parçasıdır. Bu makaleye doğrudan ulaşdıysanız, en iyi sonuçlar için serideki [ilk makaleyle](tutorial-machine-learning-edge-01-intro.md) başlamanızı öneririz.

Bu makalede, yaprak cihazlardan ileti alan üç IoT Edge modülü oluşturacağız, verileri Machine Learning modelinize aracılığıyla çalıştırır ve ardından öngörüleri IoT Hub iletebilirsiniz.

IoT Edge hub, modülü modül iletişimine kolaylaştırır. IoT Edge hub 'ı bir ileti Aracısı olarak kullanmak, modülleri birbirinden bağımsız olarak tutar. Modüller yalnızca üzerinde iletileri ve bunlar iletileri yazma çıkışları kabul girişleri belirtmeniz gerekir.

IoT Edge cihazının bizimle ilgili dört şeyi yerine getirebilmemiz istiyoruz:

* Yaprak cihazlardan veri alma
* Verileri gönderen cihaz için kalan yararlı yaşam süresini (RUL) tahmin edin
* IoT Hub cihaz için yalnızca RUL ile bir ileti gönderin (Bu işlev yalnızca RUL 'nin bir düzey altına düşerse verileri gönderecek şekilde değiştirilebilir)
* Yaprak cihaz verilerini IoT Edge cihazdaki yerel bir dosyaya kaydedin. Bu veri dosyası, makine öğrenimi modelinin eğitimini iyileştirmek için dosya karşıya yükleme yoluyla düzenli aralıklarla IoT Hub yüklenir. Sabit ileti akışı yerine karşıya dosya yükleme kullanmak daha uygun maliyetli bir değerdir.

Bu görevleri gerçekleştirmek için üç özel modül kullanıyoruz:

* **Rul Sınıflandırıcısı:** [Azure Machine Learning modeli eğitme ve dağıtma](tutorial-machine-learning-edge-04-train-model.md) bölümünde oluşturduğumuz Turbofanrulsınıflandırıcı modülü, "amlInput" adlı bir girişi ve "amlOutput" adlı bir çıktıyı kullanıma sunan standart makine öğrenimi modülüdür. "AmlInput" girişinin, ACI tabanlı Web hizmetine gönderdiğimiz giriş gibi tam olarak görünmesini bekler. Benzer şekilde, "amlOutput", Web hizmetiyle aynı verileri döndürür.

* **Avro yazıcı:** Bu modül, "Avromoduleınput" girişinde iletileri alır ve daha sonra IoT Hub yüklemek için avro biçimindeki iletiyi diske devam ettirir.

* **Yönlendirici modülü:** Yönlendirici modülü, aşağı akış yaprak cihazlarından iletileri alır, ardından iletileri sınıflandırıcılara biçimlendirir ve gönderir. Modül daha sonra sınıflandırıcıdan iletileri alır ve iletiyi avro Writer modülüne iletir. Son olarak, modül yalnızca IoT Hub RUL tahminini gönderir.

  * Girişi
    * **Deviceınput**: yaprak cihazlardan iletileri alır
    * **Rulınput:** "amlOutput" öğesinden iletileri alır

  * Çıkışı
    * **sınıflandır:** Iletileri "amlInput" öğesine gönderir
    * **writeAvro:** "Avromoduleınput" iletilerini gönderir
    * **toIotHub:** iletileri bağlı IoT Hub ileten $upstream iletileri gönderir

Aşağıdaki diyagramda, tam çözüm için modüller, girişler, çıktılar ve IoT Edge hub yolları gösterilmektedir:

![IoT Edge üç modül mimarisi diyagramı](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

Bu makaledeki adımlar genellikle bir bulut geliştiricisi tarafından gerçekleştirilir.

## <a name="create-a-new-iot-edge-solution"></a>Yeni bir IoT Edge çözümü oluşturun

İki Azure Notebooks ikincisinin yürütülmesi sırasında, RUL modelimizi içeren bir kapsayıcı görüntüsü oluşturulup yayımladık. Azure Machine Learning, görüntü oluşturma sürecinin bir parçası olarak, görüntünün bir Azure IoT Edge modülü olarak dağıtılabilir olması için bu modelin paketlenmesi. Bu adımda, "Azure Machine Learning" modülünü kullanarak bir Azure IoT Edge çözümü oluşturacağız ve modülün Azure Notebooks kullanarak yayımladığımız görüntüye işaret eteceğiz.

1. Geliştirme makinenizde bir Uzak Masaüstü oturumu açın.

2. **C:\\Source klasörünü açın\\ıotedgeandmlsample** Visual Studio Code.

3. Gezgin paneline (boş alanda) sağ tıklayın ve **yeni IoT Edge çözüm**' ü seçin.

    ![Yeni IoT Edge çözümü oluştur](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

4. **EdgeSolution**varsayılan çözüm adını kabul edin.

5. Modül şablonu olarak **Azure Machine Learning** seçin.

6. Modülün **Turbofanrulite sınıflandırıcısını**adlandırın.

7. Machine Learning çalışma alanınızı seçin.

8. Azure Not defteri 'ni çalıştırırken oluşturduğunuz görüntüyü seçin.

9. Çözüme bakın ve oluşturulan dosyalara dikkat edin:

   * **Deployment. Template. JSON:** Bu dosya çözümdeki her bir modülün tanımını içerir. Bu dosyada dikkat etmeniz gereken üç bölüm vardır:

     * **Kayıt defteri kimlik bilgileri:** Çözümünüzde kullanmakta olduğunuz özel kapsayıcı kayıt defterleri kümesini tanımlar. Şimdi, Azure Machine Learning görüntünüzün depolandığı, Machine Learning çalışma alanınızdaki kayıt defterini içermesi gerekir. Herhangi bir sayıda kapsayıcı kayıt defterine sahip olabilirsiniz, ancak kolaylık sağlaması için bu kayıt defterini tüm modüller için kullanacağız

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io"
         }
       }
       ```

     * **Modüller:** Bu bölüm, bu çözümle birlikte gelen Kullanıcı tanımlı modüller kümesini içerir. Bu bölümün şu anda iki modül içerdiğini fark edeceksiniz: SimulatedTemperatureSensor ve Turbofanrulsınıflandırıcı. SimulatedTemperatureSensor Visual Studio Code şablonu tarafından yüklendi, ancak bu çözüm için bu çözüme ihtiyacım yok. Modüller bölümünden SimulatedTemperatureSensor Module tanımını silebilirsiniz. Turbofanrulsınıflandırıcı modül tanımının kapsayıcı kayıt defterinizde görüntüyü işaret ettiğini unutmayın. Çözüme daha fazla modül eklediğimiz için, bu bölümde görünür.

       ```json
       "modules": {
         "SimulatedTemperatureSensor": {
           "version": "1.0",
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
             "createOptions": {}
           }
         },
         "turbofanRulClassifier": {
           "version": "1.0",
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "<your registry>.azurecr.io/edgemlsample:1",
             "createOptions": {}
           }
         }
       }
       ```

     * **Rotalar:** Bu öğreticide yalnızca yollarla bir bit olacak şekilde çalışıyoruz. Rotalar modüllerin birbirleriyle nasıl iletişim kuracağını tanımlar. Şablon tarafından tanımlanan iki yol, gereksinimimiz yönlendirmeyle eşleşmiyor. İlk yol, sınıflandırıcının herhangi bir çıktısından tüm verileri IoT Hub ($upstream) gönderir. Diğer yol, az önce silintiğimiz SimulatedTemperatureSensor içindir. İki varsayılan yolu silin.

       ```json
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
             "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/\* INTO $upstream",
             "sensorToturbofanRulClassifier": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\\"/modules/turbofanRulClassifier/inputs/input1\\")"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       }
       ```

   * **Deployment. Debug. Template. JSON:** bu dosya Deployment. Template. JSON öğesinin hata ayıklama sürümüdür. Dağıtım. Template. JSON dosyasındaki tüm değişiklikleri bu dosyaya yansıtmalıdır.

   * **. env:** bu dosya, Kayıt defterinize erişmek için Kullanıcı adını ve parolayı sağlamanız gereken yerdir.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

10. Visual Studio Code Explorer 'da Deployment. Template. JSON dosyasına sağ tıklayın ve **Build IoT Edge Solution**' ı seçin.

11. Bu komutun bir Deployment. AMD64. JSON dosyası ile bir yapılandırma klasörü oluşturduğuna dikkat edin. Bu dosya, çözüm için somut dağıtım şablonudur.

## <a name="add-router-module"></a>Yönlendirici modülü Ekle

Ardından, çözümümüze yönlendirici modülünü ekliyoruz. Yönlendirici modülü, çözümümüzde çeşitli sorumlulukları işler:

* **Yaprak cihazlardan Iletiler al:** iletiler, aşağı akış aygıtlarından IoT Edge cihaza ulaştığında, yönlendirici modülü iletiyi alır ve iletiyi yönlendirmeyi düzenlemeye başlar.
* **Rul sınıflandırıcı modülüne Ileti gönderme:** bir aşağı akış aygıtından yeni bir ileti alındığında, yönlendirici modülü iletiyi rul sınıflandırıcının beklediği biçime dönüştürür. Yönlendirici, iletiyi RUL tahmini için RUL sınıflandırıcıya gönderir. Sınıflandırıcı bir tahmin yapıldıktan sonra iletiyi yönlendirici modülüne geri gönderir.
* **IoT Hub için RUL Iletileri gönderme:** yönlendirici sınıflandırıcıdan ileti aldığında, iletiyi yalnızca önemli bilgileri, cihaz kimliğini ve rul 'yi içerecek şekilde dönüştürür ve kısaltılmış iletiyi IoT Hub 'ına gönderir. Burada yapmadığımız daha ileri bir iyileştirme, yalnızca RUL tahmini bir eşiğin altına düştüğünde (örneğin, RUL 100 döngüden az olduğunda) iletileri IoT Hub gönderir. Bu şekilde filtrelendiğinde, ileti hacmi azalır ve IoT Hub 'ının maliyeti azalır.
* **Avro Writer modülüne Ileti gönderin:** aşağı akış cihazı tarafından gönderilen tüm verileri korumak Için, yönlendirici modülü sınıflandırıcıdan alınan Iletinin tamamını avro yazıcı modülüne gönderir ve bu, IoT Hub dosya yükleme kullanarak verileri devam ettirilecektir ve karşıya yükleyebilir.

> [!NOTE]
> Modül sorumluluklarına ilişkin açıklama işlem sıralı görünebilir, ancak akış ileti/olay tabanlıdır. Bu nedenle, yönlendirici modülümüzü beğendiniz bir düzenleme modülüne ihtiyacımız var.

### <a name="create-module-and-copy-files"></a>Modül oluşturma ve dosyaları kopyalama

1. Visual Studio Code ' deki modüller klasörüne sağ tıklayın ve **IoT Edge modülü Ekle**' yi seçin.

2. **C# Modül**seçin.

3. Modülün **Turbofanrouter**adını adlandırın.

4. Docker görüntü deponuz sorulduğunda, Machine Learning çalışma alanındaki kayıt defterini kullanın (kayıt defterini dağıtımınızın kayıt kimlik bilgileri düğümünde bulabilirsiniz *. Template. JSON* dosyası). Bu değer, kayıt **defterinizin\>. azurecr.io/turbofanrouter\<** gibi tam olarak nitelenmiş adresidir.

    > [!NOTE]
    > Bu makalede, sınıflandırımızda eğitim ve dağıtım yapmak için kullandığımız Azure Machine Learning çalışma alanı tarafından oluşturulan Azure Container Registry kullanırız. Bu yalnızca kolaylık sağlaması içindir. Yeni bir kapsayıcı kayıt defteri oluşturup modüllerimizi burada yayımladık.

5. Visual Studio Code ( > **terminalini** **görüntüle** ) ' de yeni bir Terminal penceresi açın ve dosyaları modüller dizininden kopyalayın.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

6. Program.cs üzerine yazmak isteyip istemediğiniz sorulduğunda `y` tuşuna basın ve ardından `Enter`.

### <a name="build-router-module"></a>Derleme yönlendirici modülü

1. Visual Studio Code, **Terminal** > **varsayılan derleme görevini Yapılandır**' ı seçin.

2. **Şablondan Tasks. JSON dosyası oluştur**' a tıklayın.

3. **.NET Core**'a tıklayın.

4. Tasks. JSON açıldığında içeriği şu şekilde değiştirin:

    ```json
    {
      // See https://go.microsoft.com/fwlink/?LinkId=733558
      // for the documentation about the tasks.json format
      "version": "2.0.0",
      "tasks": [
        {
          "label": "build",
          "command": "dotnet",
          "type": "shell",
          "group": {
            "kind": "build",
            "isDefault": true
          },
          "args": [
            "build",
            "${workspaceFolder}/modules/turbofanRouter"
          ],
          "presentation": {
            "reveal": "always"
          },
          "problemMatcher": "$msCompile"
        }
      ]
    }
    ```

5. Tasks. JSON öğesini kaydedin ve kapatın.

6. Derlemeyi `Ctrl + Shift + B` veya **terminalle** çalıştırın > **derlemeyi Çalıştır görevi**.

### <a name="set-up-module-routes"></a>Modül yollarını ayarlama

Yukarıda belirtildiği gibi, IoT Edge çalışma zamanı, gevşek olarak bağlanmış modüller arasındaki iletişimi yönetmek için *Deployment. Template. JSON* dosyasında yapılandırılan yolları kullanır. Bu bölümde, turbofanRouter modülü için yolların nasıl ayarlandığına detaylanıyoruz. Önce giriş yollarını ele alacak ve ardından çıkışlar üzerinde taşınacak.

#### <a name="inputs"></a>Girişler

1. Program.cs 'in Init () yönteminde, modül için iki geri çağırma kaydeder:

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. İlk geri çağırma, **Deviceınput** havuzuna gönderilen iletileri dinler. Yukarıdaki diyagramdan, herhangi bir yaprak cihazdan iletileri bu girişe yönlendirmek istediğdiğimiz görüyoruz. *Deployment. Template. JSON* dosyasında, Edge hub 'ına, bir IoT Edge modülü tarafından alınan IoT Edge cihaz tarafından alınan herhangi bir Iletiyi, turbofanRouter modülünde "deviceınput" adlı girişe yönlendirmeye yönlendiren bir yol ekleyin:

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. Ardından, Rulsınıflandırıcı modülünden turbofanRouter modülüne iletiler için bir yol ekleyin:

   ```json
   "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>Çıkışlar

Yönlendirici modülündeki çıkışları işlemek için $edgeHub Route parametresine dört ek yol ekleyin.

1. Program.cs, yolu kullanarak RUL sınıflandırıcısına ileti göndermek için modül istemcisini kullanan Sendmessagetosınıflandırıcı () yöntemini tanımlar.

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")"
   ```

2. SendRulMessageToIotHub (), yönlendirme yoluyla IoT Hub cihaz için yalnızca RUL verisini göndermek üzere modül istemcisini kullanır:

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. SendMessageToAvroWriter (), iletisini avroFileWriter modülüne eklenen RUL verileriyle birlikte göndermek için modül istemcisini kullanır.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. HandleBadMessage () başarısız iletileri daha sonra yönlendirilebileceği IoT Hub yukarı akış gönderir.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

Tüm yolların birlikte ele alınması, "$edgeHub" düğümünüz aşağıdaki JSON gibi görünmelidir:

```json
"$edgeHub": {
  "properties.desired": {
    "schemaVersion": "1.0",
    "routes": {
      "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")",
      "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amlOutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")",
      "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")",
      "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream",
      "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")",
      "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
    },
    "storeAndForwardConfiguration": {
      "timeToLiveSecs": 7200
    }
  }
}
```

> [!NOTE]
> TurbofanRouter modülünü eklemek şu ek yol oluşturdu: `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream`. Yalnızca dağıtım. Template. JSON dosyanızda yukarıda listelenen yolları bırakarak bu rotayı kaldırın.

#### <a name="copy-routes-to-deploymentdebugtemplatejson"></a>Yolları dağıtıma Kopyala. Debug. Template. JSON

Son bir adım olarak, dosyalarınızı eşitlenmiş halde tutmak için, Deployment. Debug. JSON içinde Deployment. Template. json dosyasında yaptığınız değişiklikleri yansıtın.

## <a name="add-avro-writer-module"></a>Avro Writer modülü Ekle

Avro yazıcı modülünün,, iletileri depolamak ve dosyaları karşıya yüklemek için çözümümüzde iki sorumluluğu vardır.

* **Mağaza iletileri**: avro yazıcı modülü bir ileti aldığında, iletiyi avro biçimindeki yerel dosya sistemine yazar. Bir dizin (Bu durumda/Data/avrofiles) modülün kapsayıcısındaki bir yola bağlayan bir bağlama bağlama kullanıyoruz. Bu bağlama, modülün bir yerel yola (/avrofiles) yazmasını ve bu dosyaların doğrudan IoT Edge cihazdan erişilebilir olmasını sağlar.

* **Karşıya dosya yükleme**: avro Writer modülü, Azure depolama hesabına dosya yüklemek için Azure IoT Hub dosya karşıya yükleme özelliğini kullanır. Bir dosya başarıyla karşıya yüklendikten sonra Modül dosyayı diskten siler

### <a name="create-module-and-copy-files"></a>Modül oluşturma ve dosyaları kopyalama

1. Komut paletinde, öğesini arayın ve **Python: yorumlayıcı Seç**' i seçin.

1. C:\\Python37 içinde bulunan yorumlayıcı seçin.

1. Komut paletini tekrar açın ve sonra da **Terminal seçin: varsayılan kabuğu seçin**.

1. İstendiğinde, **komut istemi**' ni seçin.

1. Yeni bir Terminal kabuğu açın, **terminal** > **yeni Terminal**.

1. Visual Studio Code ' deki modüller klasörüne sağ tıklayın ve **IoT Edge modülü Ekle**' yi seçin.

1. **Python Modülü**'nü seçin.

1. "AvroFileWriter" modülünü adlandırın.

1. Docker görüntü deponuz sorulduğunda, yönlendirici modülünü eklerken kullandığınız kayıt defterini kullanın.

1. Dosyaları örnek modülünden çözüme kopyalayın.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. Main.py üzerine yazmak isteyip istemediğiniz sorulursa `y` yazın ve sonra `Enter`vurun.

1. Çözüme filemanager.py ve schema.py eklendiğini ve main.py güncelleştirildiğini unutmayın.

> [!NOTE]
> Bir Python dosyası açtığınızda, pylint ' i yüklemek isteyip istemediğiniz sorulabilir. Bu öğreticiyi tamamlayabilmeniz için nter yüklemeniz gerekmez.

### <a name="bind-mount-for-data-files"></a>Veri dosyaları için bağlama bağlama

Giriş bölümünde belirtildiği gibi, yazıcı modülü, avro dosyalarını cihazın dosya sistemine yazmak için bağlama bağlama varlığını kullanır.

#### <a name="add-directory-to-device"></a>Cihaza Dizin Ekle

1. SSH kullanarak IoT Edge cihaz sanal makinesine bağlanın.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Kayıtlı yaprak cihaz iletilerini tutacak dizini oluşturun.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

3. Kapsayıcı tarafından yazılabilir hale getirmek için dizin izinlerini güncelleştirin.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

4. Dizinin artık Kullanıcı, Grup ve sahip için yazma (w) iznine sahip olduğunu doğrulayın.

   ```bash
   ls -la /data
   ```

   ![Avrofiles için dizin izinleri](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>Modüle dizin ekleme

Dizini modülün kapsayıcısına eklemek için, avroFileWriter modülüyle ilişkili Dockerfiles 'ı değiştireeceğiz. Modülle ilişkili üç Dockerfiles vardır: Dockerfile. amd64, Dockerfile. AMD64. Debug ve Dockerfile. arm32v7. Bu dosyalar, hata ayıklamak veya bir ARM32 cihazına dağıtmak istediğimiz durumda eşitlenmiş durumda tutulmalıdır. Bu makalede yalnızca Dockerfile. amd64 üzerine odaklanırsınız.

1. Geliştirme makinenizde **Dockerfile. amd64** dosyasını açın.

2. Dosyayı aşağıdaki örnekte olduğu gibi görünecek şekilde değiştirin:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

   `mkdir` ve `chown` komutları Docker Build işlemine, görüntüde/avrofiles adlı bir üst düzey dizin oluşturulmasını ve ardından moduleuser öğesinin bu dizinin sahibi olmasını sağlar. Bu komutların, modül kullanıcısı `useradd` komutuyla görüntüye eklendikten ve bağlam moduleuser 'a (Kullanıcı moduleuser) geçmeden önce eklenmesi önemlidir.

3. Dockerfile. AMD64. Debug ve Dockerfile. arm32v7 üzerinde ilgili değişiklikleri yapın.

#### <a name="update-the-module-configuration"></a>Modül yapılandırmasını güncelleştirme

Bağlamayı oluşturmanın son adımı, Deployment. Template. JSON (ve Deployment. JSON) dosyalarını bağlama bilgileriyle güncelleştirmedir.

1. Deployment. Template. JSON öğesini açın.

2. /Avrofiles kapsayıcı dizinini uç cihazdaki yerel dizine gösteren `Binds` parametresini ekleyerek avroFileWriter için modül tanımını değiştirin. Modül tanımınız şu örnekle eşleşmelidir:

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles"
           ]
         }
       }
     }
   }
   ```

3. Dağıtım. Debug. Template. JSON öğesine karşılık gelen değişiklikleri yapın.

### <a name="bind-mount-for-access-to-configyaml"></a>Config. YAML erişimi için bağlama bağlama

Yazıcı modülü için bir bağlama daha eklememiz gerekiyor. Bu bağ, IoT Edge cihazında/etc/iotedge/config.exe dosyasından bağlantı dizesini okumak için modüle erişim sağlar. Dosyaları IoT Hub 'a yüklemek için\_blob\_zaman uyumsuz yöntemini çağırabilmemiz için bir ıothubclient oluşturmak üzere bağlantı dizesine ihtiyacımız var. Bu bağlamayı ekleme adımları, önceki bölümde yer aldığı olanlarla benzerdir.

#### <a name="update-directory-permission"></a>Dizin izinlerini Güncelleştir

1. SSH kullanarak IoT Edge cihazınıza bağlanın.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

2. Config. YAML dosyasına okuma izni ekleyin.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

3. İzinlerin doğru şekilde ayarlandığını doğrulayın.

   ```bash
   ls -la /etc/iotedge/
   ```

4. Config. YAML izinlerinin **-r--r--r--** olduğundan emin olun.

#### <a name="add-directory-to-module"></a>Modüle Dizin Ekle

1. Geliştirme makinenizde **Dockerfile. amd64** dosyasını açın.

2. Aşağıdaki gibi görünmesi için dosyaya ek bir `mkdir` ve `chown` komut kümesi ekleyin:

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig

   USER moduleuser

   CMD "python3", "-u", "./main.py"]
   ```

3. Dockerfile. AMD64. Debug ve Dockerfile. arm32v7 üzerinde ilgili değişiklikleri yapın.

#### <a name="update-the-module-configuration"></a>Modül yapılandırmasını güncelleştirme

1. **Deployment. Template. JSON** dosyasını açın.

2. Kapsayıcı dizinini (/App/iotconfig) cihazdaki yerel dizine (/etc/iotedge) işaret eden `Binds` parametresine ikinci bir satır ekleyerek avroFileWriter için modül tanımını değiştirin.

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles",
             "/etc/iotedge:/app/iotconfig"
           ]
         }
       }
     }
   }
   ```

3. Dağıtım. Debug. Template. JSON öğesine karşılık gelen değişiklikleri yapın.

## <a name="install-dependencies"></a>Bağımlılıkları yükleme

Yazıcı modülü, fastavro ve PyYAML olmak üzere iki Python kitaplığı bağımlılığı alır. Geliştirme makinemizdeki bağımlılıkları yüklememiz ve Docker Build işlemini modülün görüntüsüne yüklemesi gerekir.

### <a name="pyyaml"></a>PyYAML

1. Geliştirme makinenizde, **requirements. txt** dosyasını açın ve pyyaml ekleyin.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

2. Setuptools 'ı yükseltmek için **Dockerfile. amd64** dosyasını açın ve bir `pip install` komutu ekleyin.

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && \
       apt-get install -y --no-install-recommends libcurl4-openssl-dev python3-pip libboost-python1.58-dev libpython3-dev && \
       rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   RUN pip install -U pip setuptools
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

3. Dockerfile. AMD64. Debug üzerinde ilgili değişiklikleri yapın. <!--may not be necessary. Add 'if needed'?-->

4. Visual Studio Code ' de bir Terminal açıp ve yazarak pyyaml 'yi yerel olarak yükleyip

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro dili

1. Requirements. txt dosyasında pyyaml sonrasında fastavro ekleyin.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

2. Visual Studio Code terminalini kullanarak fastavro 'yi geliştirme makinenize yüklemeyin.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>IoT Hub yeniden yapılandırın

IoT Edge cihaz ve modüllerle sisteme bakarak, hub 'a hangi verilerin gönderileceği hakkında beklentilerimizi ve hangi amaçla değiştirildiğini değiştirdik. Yeni gerçeklik ile başa çıkmak için hub 'daki yönlendirmeyi yeniden yapılandırmamız gerekiyor.

> [!NOTE]
> Yönetim ayarları, özellikle dosya karşıya yükleme, avroFileWriter modülünün doğru şekilde çalışması için doğru şekilde ayarlanması gerektiğinden, modüller dağıtılmadan önce hub 'ı yeniden yapılandıracağız

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>IoT Hub RUL iletileri için rota ayarlama

Yönlendirici ve sınıflandırıcıda, yalnızca cihaz KIMLIĞI ve cihaz için RUL tahmini içeren normal iletiler almayı bekledik. RUL verilerini, cihazların durumunu izleyebilmemiz, raporlar derleyebilir ve gerektiğinde uyarı tetikliyoruz. Aynı zamanda, geçerli depolama konumuna yönlendirmeye devam etmek için IoT Edge cihazımızı henüz eklenmemiş bir yaprak cihaz tarafından doğrudan gönderilen cihaz verilerinin olmasını istiyoruz.

#### <a name="create-a-rul-message-route"></a>RUL ileti yolu oluşturma

1. Azure portal IoT Hub gidin.

2. Sol gezinmede **ileti yönlendirme**' yi seçin.

3. **Add (Ekle)** seçeneğini belirleyin.

4. Yol **Rulmessageroute**adını adlandırın.

5. **Uç nokta** seçicisinin yanındaki **Ekle** ' yi seçin ve **BLOB depolama**' yı seçin.

6. **Depolama uç noktası Ekle** formunda, uç nokta **rulı verilerini**adlandırın.

7. **Kapsayıcı Seç**' i seçin.

8. Bu öğreticide kullanılan, **ıotedgeandml\<benzersiz sonek\>** gibi adlandırılan depolama hesabını seçin.

9. **Ruldata** kapsayıcısını seçin ve **Seç**' e tıklayın.

10. Depolama uç noktası oluşturmak için **Oluştur** ' a tıklayın.

11. **Yönlendirme sorgusu**için aşağıdaki sorguyu girin:

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

12. **Test** bölümünü ve ardından **ileti gövdesi** bölümünü genişletin. İletiyi beklenen iletilerimizin bu örneği ile değiştirin:

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

13. **Test rotası**seçin. Sınama başarılı olursa "ileti sorguyla eşleşti." iletisini görürsünüz.

14. **Kaydet**’e tıklayın.

#### <a name="update-turbofandevicetostorage-route"></a>TurbofanDeviceToStorage yolunu Güncelleştir

Yeni tahmin verilerini eski depolama konumumuza yönlendirmek istemiyorum, bu nedenle yolu engellemek için yeniden güncelleştirin.

1. **İleti yönlendirme** IoT Hub sayfasında, **rotalar** sekmesini seçin.

2. **Turbofandevicedatatostorage**' ı veya ilk cihaz veri yönlendirmenize verdiğiniz adı seçin.

3. Yönlendirme sorgusunu güncelleştirme

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

4. **Test** bölümünü ve ardından **ileti gövdesi** bölümünü genişletin. İletiyi beklenen iletilerimizin bu örneği ile değiştirin:

   ```json
   {
     "Sensor13": 2387.96,
     "OperationalSetting1": -0.0008,
     "Sensor6": 21.61,
     "Sensor11": 47.2,
     "Sensor9": 9061.45,
     "Sensor4": 1397.86,
     "Sensor14": 8140.39,
     "Sensor18": 2388.0,
     "Sensor12": 522.87,
     "Sensor2": 642.42,
     "Sensor17": 391.0,
     "OperationalSetting3": 100.0,
     "Sensor1": 518.67,
     "OperationalSetting2": 0.0002,
     "Sensor20": 39.03,
     "DeviceId": 19.0,
     "Sensor5": 14.62,
     "PredictedRul": 212.00132402791962,
     "Sensor8": 2388.01,
     "Sensor16": 0.03,
     "CycleTime": 42.0,
     "Sensor21": 23.3188,
     "Sensor15": 8.3773,
     "Sensor3": 1580.09,
     "Sensor10": 1.3,
     "Sensor7": 554.57,
     "Sensor19": 100.0
   }
   ```

5. **Test rotası**seçin. Sınama başarılı olursa "ileti sorguyla eşleşti." iletisini görürsünüz.

6. **Kaydet**’i seçin.

### <a name="configure-file-upload"></a>Karşıya dosya yüklemeyi yapılandırma

Dosya yazıcı modülünün depolama alanına dosya yüklemesine izin vermek için IoT Hub dosya yükleme özelliğini yapılandırın.

1. IoT Hub sol gezgin 'den **karşıya dosya yükleme**' yi seçin.

2. **Azure depolama kapsayıcısı**' nı seçin.

3. Listeden depolama hesabınızı seçin.

4. **Uploadturbofanfiles** kapsayıcısını seçip **Seç**' e tıklayın.

5. **Kaydet**’i seçin. Portal, Kaydetme tamamlandığında size bildirir.

> [!Note]
> Bu öğretici için karşıya yükleme bildirimini kapatmıyoruz, ancak karşıya dosya yükleme bildirimini işleme hakkında ayrıntılar için karşıya [dosya yükleme bildirimi alma](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification) bölümüne bakın.

## <a name="build-publish-and-deploy-modules"></a>Modüller oluşturma, yayımlama ve dağıtma

Artık yapılandırma değişikliklerini yaptığımız için, görüntüleri oluşturmaya ve bunları Azure Container Registry 'imizde yayımlamaya hazırız. Yapı işlemi, hangi modüllerin oluşturulması gerektiğini belirleyen Deployment. Template. json dosyasını kullanır. Sürümü de dahil olmak üzere her modülün ayarları modül klasöründeki Module. json dosyasında bulunur. Yapı işlemi önce, bir görüntü oluşturmak için Module. json dosyasında bulunan geçerli yapılandırmayla eşleşen Dockerfiles üzerinde bir Docker derlemesini çalıştırır. Daha sonra, Module. JSON dosyasındaki modül. JSON dosyası ile eşleşen bir sürüm etiketiyle görüntüyü kayıt defterine yayınlar. Son olarak, IoT Edge cihaza dağıtacağınız yapılandırmaya özgü bir dağıtım bildirimi (örneğin, Deployment. AMD64. JSON) oluşturur. IoT Edge cihaz, dağıtım bildiriminden bilgileri okur ve yönergeleri temel alarak modülleri indirir, yolları yapılandırır ve istediğiniz özellikleri ayarlar. Bu dağıtım yönteminde dikkat etmeniz gereken iki yan etkisi vardır:

* **Dağıtım gecikmesi:** IoT Edge çalışma zamanının, yeniden yapılandırma işlemine başlamadan önce istenen özelliklerine yapılan değişikliği tanıması gerektiğinden, çalışma zamanı onları bulana ve IoT Edge güncelleştirilmeye başladıktan sonra modüllerinizi dağıttıktan sonra zaman alabilir aygıtların.

* **Modül sürümleri:** önceki modülle aynı sürüm etiketlerini kullanarak bir modülün kapsayıcısının kapsayıcı Kayıt defterinize yeni bir sürümünü yayımlarsanız, çalışma zamanı modülün yeni sürümünü indirmeyecektir. Yerel görüntünün sürüm etiketinin ve dağıtım bildiriminden istenen görüntünün bir karşılaştırmasını yapar. Bu sürümler eşleşiyorsa, çalışma zamanı hiçbir eylemde bulunmaz. Bu nedenle, yeni değişiklikler dağıtmak istediğiniz her seferinde modülünüzün sürümünü artırmak önemlidir. Değiştirdiğiniz modülün Module. JSON dosyasındaki **Tag** özelliği altındaki **Version** özelliğini değiştirerek sürümü artırın. Ardından modülü derleyin ve yayımlayın.

    ```json
    {
      "$schema-version": "0.0.1",
      "description": "",
      "image": {
        "repository": "<your registry>.azurecr.io/avrofilewriter",
        "tag": {
          "version": "0.0.1",
          "platforms": {
            "amd64": "./Dockerfile.amd64",
            "amd64.debug": "./Dockerfile.amd64.debug",
            "arm32v7": "./Dockerfile.arm32v7"
          }
        },
        "buildOptions": []
      },
      "language": "python"
    }
    ```

### <a name="build-and-publish"></a>Derleme ve yayımlama

1. Geliştirme sanal makinenizde Visual Studio Code, bir Visual Studio Code Terminal penceresi açın ve kapsayıcı kayıt defterinizde oturum açın.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. Visual Studio Code, Deployment. Template. json ' a sağ tıklayın ve **IoT Edge çözümü oluştur ve Gönder**' i seçin.

### <a name="view-modules-in-the-registry"></a>Kayıt defterindeki modülleri görüntüleme

Oluşturma başarıyla tamamlandıktan sonra, yayımlanan modüllerimizi gözden geçirmek için Azure portal kullanacağız.

1. Azure portal, Azure Machine Learning çalışma alanınıza gidin ve **kayıt defteri**köprüsüne tıklayın.

    ![Machine Learning hizmeti çalışma alanından kayıt defterine git](media/tutorial-machine-learning-edge-06-custom-modules/follow-registry-link.png)

2. Kayıt defteri tarafı Gezgini ' nden **depolar**' ı seçin.

3. Her iki modülün da, **avrofilewriter** ve **turbofanrouter**, depo olarak göründüğünü unutmayın.

4. **Turbofanrouter** ' ı seçin ve 0.0.1-amd64 olarak etiketlenmiş bir görüntü yayımladığınızdan emin olabilirsiniz.

   ![Turbofanrouter 'un ilk etiketli sürümünü görüntüle](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>Modülleri IoT Edge cihaza dağıt

Çözümünüzde modülleri oluşturup yapılandırdık, şimdi de modülleri IoT Edge cihaza dağıtacağız.

1. Visual Studio Code ' de, yapılandırma klasöründe **Deployment. AMD64. JSON** dosyasına sağ tıklayın.

2. **Tek cihaz Için dağıtım oluştur**seçeneğini belirleyin.

3. IoT Edge cihazınızı, **Aaturbofanedgedevice**' ı seçin.

4. Visual Studio Code Explorer 'da Azure IoT Hub cihazları panelini yenileyin. Üç yeni modülün dağıtıldığını ancak henüz çalışmadığını görmeniz gerekir.

5. Birkaç dakika sonra yeniden yenileyin ve çalıştıran modülleri görürsünüz.

   ![Visual Studio Code çalışan modülleri görüntüleme](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> Modüllerin sürekli çalışan bir duruma başlaması ve yeniden başlatılması birkaç dakika sürebilir. Bu süre boyunca, IoT Edge hub modülüyle bir bağlantı kurmaya çalışırken modüllerin başlayıp durdurulabiliyor olabilirsiniz.

## <a name="diagnosing-failures"></a>Sorunları tanılama

Bu bölümde, bir modül veya modüllerle neyin yanlış olduğunu anlamak için birkaç teknik paylaşıyoruz. Genellikle bir hata, Visual Studio Code durumundan önce bir hata olabilir.

### <a name="identify-failed-modules"></a>Başarısız modülleri tanımla

* **Visual Studio Code:** Azure IoT Hub cihazlar paneline bakın. Çoğu modül çalışır durumda, ancak bir durdurulmuşsa, bu durdurulan modülün daha fazla araştırılması gerekir. Tüm modüller uzun bir süre boyunca durdurulmuş durumdaysa, bu da hata olduğunu gösteriyor olabilir.

* **Azure Portal:** Portalda IoT Hub 'ınıza gidip cihaz ayrıntıları sayfasını bularak (IoT Edge altında, cihazınızla detaya gitme), bir modülün bir hata raporladığını veya IoT Hub 'ına hiçbir şey bildirmemiş olduğunu fark edebilirsiniz.

### <a name="diagnosing-from-the-device"></a>Cihazdan tanılama

IoT Edge cihazda oturum açarak modüllerinizin durumu hakkında iyi bir bilgiye erişim elde edebilirsiniz. Kullandığımız ana mekanizma, cihazdaki kapsayıcıları ve görüntüleri incelemenize olanak tanıyan Docker komutlarıdır.

1. Tüm çalışan kapsayıcıları listeleyin. Her modül için modüle karşılık gelen bir ada sahip bir kapsayıcı görmemiz bekleniyor. Ayrıca, bu komut, bir sürüm dahil olmak üzere kapsayıcının tam görüntüsünü listeler, böylece beklentilerinizle eşleştirebilirsiniz. Ayrıca, komut içinde "kapsayıcı" için "görüntü" koyarak görüntüleri listeleyebilirsiniz.

   ```bash
   sudo docker container ls
   ```

2. Bir kapsayıcı için günlükleri alın. Bu komut, kapsayıcıda StdErr ve StdOut 'a yazılan her şeyi çıktı olarak verir. Bu komut, başlatılan ve bir nedenden dolayı daha sonra gelen kapsayıcılar için geçerlidir. Ayrıca, edgeAgent veya edgeHub kapsayıcılarıyla neler olduğunu anlamak için de kullanışlıdır.

   ```bash
   sudo docker container logs <container name>
   ```

3. Bir kapsayıcıyı inceleyin. Bu komut, görüntü hakkında bilgi verir. Veriler, neyi aradığınızı bağlı olarak filtrelenebilir. Örnek olarak, avroFileWriter üzerindeki bağlamalar doğru olup olmadığını görmek isterseniz komutunu kullanabilirsiniz:

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

4. Çalışan bir kapsayıcıya bağlanın. Bu komut, çalışırken kapsayıcıyı incelemek istiyorsanız yararlı olabilir:

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, üç modülle, sınıflandırıcı, yönlendirici ve dosya yazarı/Uploader ile Visual Studio Code IoT Edge bir çözüm oluşturduk. Modüllerin Edge cihazında birbirleriyle iletişim kurmasına izin vermek için yollar ayarladık, sınır cihazının yapılandırmasını değiştirmiş ve bu, bağımlılıkları yükleyecek ve modüllerin kapsayıcılarına BIND bağlama ekleyecek olan Dockerfiles 'ı güncelleştirdik. Daha sonra, IoT Hub yapılandırmasını, bu iletileri türüne göre yönlendirmekte ve dosya karşıya yüklemelerini işleyecek şekilde güncelleştirdik. Her şey ile, modülleri IoT Edge cihaza dağıttık ve modüller doğru şekilde çalışıyor.

Aşağıdaki sayfalarda daha fazla bilgi bulabilirsiniz:

* [IoT Edge'de modülleri dağıtmayı ve yolları oluşturmayı öğrenin](module-composition.md)
* [IoT Hub ileti yönlendirme sorgusu sözdizimi](../iot-hub/iot-hub-devguide-routing-query-syntax.md)
* [IoT Hub ileti yönlendirme: Şu anda ileti gövdesinde yönlendirme ile](https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/)
* [IoT Hub ile dosyaları karşıya yükleme](../iot-hub/iot-hub-devguide-file-upload.md)
* [IoT Hub ile cihazınızdan buluta dosya yükleme](../iot-hub/iot-hub-python-python-file-upload.md)

Veri göndermeye başlamak ve çözümünüzü eylemde görmek için sonraki makaleye geçin.

> [!div class="nextstepaction"]
> [Saydam ağ geçidi aracılığıyla veri Gönder](tutorial-machine-learning-edge-07-send-data-to-hub.md)
