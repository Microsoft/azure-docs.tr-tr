---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: 8009d98ddbfa778cf5f357248ecd943b810e06e3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803229"
---
## <a name="create-a-release-pipeline-for-continuous-deployment"></a>Sürekli dağıtım için bir yayın işlem hattı oluşturma

Bu bölümde, derleme işlem hatlarınız yapıtlar olduğunda otomatik olarak çalışacak şekilde yapılandırılmış bir yayın işlem hattı oluşturun ve Azure Pipelines dağıtım günlüklerini gösterir.

Yeni bir işlem hattı oluşturun ve yeni bir aşama ekleyin:

1. İşlem **hatları** altındaki **yayınlar** sekmesinde **+ Yeni işlem hattı**' nı seçin. Ya da yayın işlem hatlarınız zaten varsa **+ Yeni** düğmesini seçin ve **+ Yeni yayın işlem hattı**' nı seçin.  

    ![+ Yeni işlem hattı düğmesini kullanarak yayın işlem hattı ekleme](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-release-pipeline.png)

2. Bir şablon seçmek isteyip istemediğiniz sorulduğunda **boş bir işle** başlatmayı seçin.

    ![Yayın işlem hattınızı boş bir iş ile başlatın](./media/iot-edge-create-release-pipeline-for-continuous-deployment/start-with-empty-release-job.png)

3. Yeni yayın ardışık düzeni, **1. aşama** adlı bir aşama ile başlatılır. 1. aşamayı **dev** olarak yeniden adlandırın ve geliştirme ortamınız için bir sürekli dağıtım işlem hattı olarak değerlendirin. Genellikle, sürekli dağıtım işlem hatları **dev**, **hazırlık** ve **Üretim** dahil olmak üzere birden çok aşamaya sahiptir. Farklı adlar kullanabilir ve DevOps uygulamanıza göre daha fazla bilgi oluşturabilirsiniz. Yeniden adlandırıldıktan sonra aşama ayrıntıları penceresini kapatın.

   Ayrıca, en üstteki "yeni yayın işlem hattı" metnini seçerek yayın işlem hattınızı yeniden adlandırabilirsiniz.

4. Yayını derleme işlem hattı tarafından yayınlanan yapı yapıtlarına bağlayın. Yapıt alanında **Ekle** ' ye tıklayın.

   ![Arabirimin yapıt alanında Ekle ' ye tıklayın.](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifacts.png)

5. **Yapıt Ekle sayfasında**, **kaynak türü** olarak **Oluştur** ' u seçin. Oluşturduğunuz projeyi ve derleme işlem hattını seçin. İsterseniz, **kaynak diğer adı** daha açıklayıcı bir şekilde değiştirebilirsiniz. Ardından **Ekle**' yi seçin.

   ![Yapıt Ekle sayfasında, yapıtı oluşturmak için Ekle ' yi seçin](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifact.png)

6. Yapıt tetikleyicilerini açın ve sürekli dağıtım tetikleyicisini etkinleştirmek için geçiş seçeneğini belirleyin. Şimdi yeni bir derleme kullanılabilir olduğunda yeni bir yayın oluşturulacaktır.

   ![Yapıt tetikleyicilerini açın ve sürekli dağıtım tetikleyicisini etkinleştirmek için geçiş yapın](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-trigger.png)

7. **Geliştirme** aşaması bir iş ve sıfır görev ile önceden yapılandırılmıştır. İşlem hattı menüsünde **Görevler** ' i ve ardından **geliştirme** aşamasını seçin. **Aracı işini** seçin ve **görünen adını** **qa** olarak değiştirin. Aracı işiyle ilgili ayrıntıları yapılandırabilirsiniz, ancak seçilen **Aracı havuzunda** herhangi bir **Aracı belirtimini** kullanabilmeniz için Dağıtım görevi platforma duyarlıdır.

   ![Görevler sekmesinin altındaki geliştirme aşamalarınız için görevleri görüntüleyin](./media/iot-edge-create-release-pipeline-for-continuous-deployment/view-stage-tasks.png)

8. QA işinde, **+** iki görev eklemek için artı işaretini () seçin. **Azure IoT Edge** iki kez arayın ve ekleyin.

9. İlk **Azure IoT Edge** görevini seçin ve aşağıdaki değerlerle yapılandırın:

    | Parametre | Açıklama |
    | --- | --- |
    | Görünen ad | Görünen ad, eylem alanı değiştiğinde otomatik olarak güncelleştirilir. |
    | Eylem | `Generate deployment manifest` öğesini seçin. |
    | Dosya üzerinde .template.js | Yolu belirtin: `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json` . Yol, derleme ardışık düzeninde yayımlanır. |
    | Varsayılan platform | Modülleriniz için hedeflenen IoT Edge cihazınıza göre uygun işletim sistemini seçin. |
    | Çıkış yolu| Yolu yerleştirin `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` . Bu yol, son IoT Edge dağıtım bildirimi dosyasıdır. |

    Bu yapılandırma, dosyadaki modül görüntüsü URL 'Lerinin değiştirilmesini sağlar `deployment.template.json` . **Dağıtım oluşturma bildirimi** Ayrıca değişkenlerin dosyada tanımladığınız tam değerle değiştirilmesini de sağlar `deployment.template.json` . VS/VS Code 'de gerçek değeri bir dosyada belirtmektir `.env` . Azure Pipelines, **serbest bırakma Işlem hattı değişkenleri** sekmesinde değeri ayarlarsınız. **Değişkenler** sekmesine geçin ve adı ve değeri aşağıdaki şekilde yapılandırın:

    * **ACR_ADDRESS**: Azure Container Registry **oturum açma sunucunuzun** değeri. Oturum açma sunucusunu Azure portal kapsayıcı kayıt defterinizin genel bakış sayfasından alabilirsiniz.
    * **ACR_PASSWORD**: Azure Container Registry parolanız.
    * **ACR_USER**: Azure Container Registry Kullanıcı adı.

    Projenizde başka değişkenlere sahipseniz, bu sekmede adı ve değeri belirtebilirsiniz. **Dağıtım oluşturma bildirimi** yalnızca Flavor içindeki değişkenleri tanıyabilir `${VARIABLE}` . Bu türü dosyalarınızda kullandığınızdan emin olun `*.template.json` .
    
    ```json-interactive
    "registryCredentials": {
      "<ACR name>": { // Your Azure Container Registry **Registry name** value
        "username": "${ACR_USER}",
        "password": "${ACR_PASSWORD}",
        "address": "${ACR_ADDRESS}"
      }
    }
    ```
    
    ![Değişkenler sekmesinde yayın işlem hattınızla ilgili değişkenleri yapılandırın](./media/iot-edge-create-release-pipeline-for-continuous-deployment/configure-variables.png)

10. İkinci **Azure IoT Edge** görevi seçin ve aşağıdaki değerlerle yapılandırın:

    | Parametre | Açıklama |
    | --- | --- |
    | Görünen ad | Görünen ad, eylem alanı değiştiğinde otomatik olarak güncelleştirilir. |
    | Eylem | `Deploy to IoT Edge devices` öğesini seçin. |
    | Dağıtım dosyası | Yolu yerleştirin `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` . Bu yol, dosya IoT Edge dağıtım bildirimi dosyasıdır. |
    | Azure aboneliği | IoT Hub içeren aboneliği seçin.|
    | IoT Hub adı | IoT Hub 'ınızı seçin.|
    | Tek/birden çok cihaz seçin | Yayın işlem hattının bir veya birden çok cihaza dağıtılmasını isteyip istemediğinizi seçin. Tek bir cihaza dağıtıyorsanız **IoT Edge CIHAZ kimliğini** girin. Birden çok cihaza dağıtıyorsanız, cihaz **hedefi koşulunu** belirtin. Hedef koşul, IoT Hub bir IoT Edge cihazları kümesiyle eşleşecek bir filtredir. Cihaz etiketlerini koşul olarak kullanmak istiyorsanız, ilgili cihaz etiketlerinizi IoT Hub Device ikizi ile güncelleştirmeniz gerekir. Gelişmiş ayarlarda **IoT Edge DAĞıTıM kimliğini** ve **Dağıtım önceliğini IoT Edge** güncelleştirin. Birden çok cihaz için dağıtım oluşturma hakkında daha fazla bilgi için bkz. [IoT Edge otomatik dağıtımları anlama](../articles/iot-edge/module-deployment-monitoring.md). |
    | Cihaz KIMLIĞI veya hedef koşulu | Önceki seçime bağlı olarak, birden çok cihaza dağıtılacak bir cihaz KIMLIĞI veya [hedef koşulu](../articles/iot-edge/module-deployment-monitoring.md#target-condition) belirtin. |
    | Gelişmiş | IoT Edge dağıtım KIMLIĞI için, belirtin `$(System.TeamProject)-$(Release.EnvironmentName)` . Bu değişken, proje ve sürüm adını IoT Edge dağıtım KIMLIĞINIZLE eşler. |
    

    Göreviniz, genel buluta görünmeyen özel bir Docker güvenilen kayıt defterinde bulunan bir görüntünün kullanımını içeriyorsa, **SKIP_MODULE_IMAGE_VALIDATION** ortam değişkenini `true` görüntü doğrulamayı atlayacak şekilde ayarlayabilirsiniz. 

    ![Geliştirme aşamalarınız için Azure IoT Edge görevleri ekleyin](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-quality-assurance-task.png)

11. Yeni sürüm ardışık düzeninde yaptığınız değişiklikleri kaydetmek için **Kaydet** ' i seçin. Menüden işlem **hattı** sekmesini seçerek işlem hattı görünümüne dönün.
