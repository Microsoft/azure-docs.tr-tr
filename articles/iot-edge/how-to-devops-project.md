---
title: Azure DevOps Projeleri-Azure IoT Edge ile CI/CD işlem hattı | Microsoft Docs
description: Azure DevOps Projeleri Azure 'u kullanmaya başlamanızı kolaylaştırır. Birkaç hızlı adımda tercih ettiğiniz bir Azure IoT Edge uygulamasını başlatmanıza yardımcı olur.
author: shizn
manager: ''
ms.author: xshi
ms.date: 10/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: daa4bc7b1584dc2159d4128fa4b44056df347ecb
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72253039"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects"></a>Azure DevOps Projeleri ile IoT Edge için bir CI/CD işlem hattı oluşturma

DevOps Projeleri IoT Edge uygulamanız için sürekli tümleştirme (CI) ve sürekli teslim (CD) yapılandırın. DevOps Projeleri, derleme ve yayın işlem hattının Azure Pipelines içinde ilk yapılandırmasını basitleştirir.

Etkin bir Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

DevOps Projeleri, Azure DevOps 'da bir CI/CD işlem hattı oluşturur. Yeni bir Azure DevOps kuruluşu oluşturabilir veya var olan bir kuruluşu kullanabilirsiniz. DevOps Projeleri ayrıca Azure kaynaklarını tercih ettiğiniz Azure aboneliğinde de oluşturur.

1. [Microsoft Azure portalında](https://portal.azure.com) oturum açın.

1. Sol bölmede, **kaynak oluştur**' u seçin ve ardından **DevOps Projeleri**arayın.  

1.  **Oluştur**'u seçin.

## <a name="create-a-new-application-pipeline"></a>Yeni bir uygulama işlem hattı oluşturun 

1. Azure IoT Edge modüllerinizin, [Node. js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C#](tutorial-csharp-module.md) [C](tutorial-c-module.md) ve [Java](tutorial-java-module.md)dilinde yazılabilir. Yeni bir uygulamayı başlatmak için tercih ettiğiniz dili seçin: **.net**, **Node. js**, **Python**, **C**veya **Java**. Devam etmek için **İleri**’yi seçin.

   ![Yeni bir uygulama oluşturmak için dil seçin](./media/how-to-devops-project/select-language.png)

2. Uygulama çatısı olarak **basit IoT** ' yi seçin ve ardından **İleri**' yi seçin.

   ![Basit IoT çerçevesini seçin](media/how-to-devops-project/select-iot.png)

3. Uygulamanızı dağıtan Azure hizmeti olarak **IoT Edge** seçin ve ardından **İleri**' yi seçin.

   ![IoT Edge hizmeti seçin](media/how-to-devops-project/select-iot-edge.png)

4. Yeni bir ücretsiz Azure DevOps organizasyonu oluşturun veya var olan bir kuruluşu seçin.

   1. Projeniz için bir ad sağlayın. 

   2. Azure DevOps kuruluşunuzu seçin. Mevcut bir kuruluşunuz yoksa yeni bir tane oluşturmak için **ek ayarlar** ' ı seçin. 

   3. Azure aboneliğinizi seçin.

   4. Projenizin adı tarafından oluşturulan IoT Hub adını kullanın veya kendi kendinize sağlayın.

   5. Varsayılan konumu kabul edin veya bir kapanış seçin. 

   5. DevOps Projeleri oluşturduğunuz Azure kaynaklarını sizin adınıza yapılandırmak için **ek ayarlar** ' ı seçin.

   6. Projenizi oluşturmayı bitirdiğinizde **bitti** ' yi seçin. 

   ![Ad ve uygulama oluştur](media/how-to-devops-project/select-devops.png)

Birkaç dakika sonra, DevOps Projeleri panosu Azure portal görüntülenir. İlerleme durumunu görmek için projenizin adını seçin. Sayfayı yenilemeniz gerekebilir. Örnek bir IoT Edge uygulaması Azure DevOps kuruluşunuzda bir depoda ayarlanır, bir derleme yürütülür ve uygulamanız IoT Edge cihazına dağıtılır. Bu Pano, kod deponuza, CI/CD işlem hattına ve Azure 'daki uygulamanıza ilişkin görünürlük sağlar.

   ![Uygulamayı Azure portal görüntüle](./media/how-to-devops-project/devops-portal.png)


## <a name="commit-code-changes-and-execute-cicd"></a>Kod değişikliklerini işleme ve CI/CD’yi yürütme

DevOps Projeleri, Azure Repos ' de projeniz için bir git deposu oluşturdu. Bu bölümde, depoyu görüntüler ve uygulamanızda kod değişikliği yaparsınız.

1. Projeniz için oluşturulan depoya gitmek için, proje panonuzun menüsünde **depolar** ' ı seçin.  

   ![Azure Repos oluşturulan depoyu görüntüle](./media/how-to-devops-project/view-repositories.png)

2. Aşağıdaki adımlarda, kod değişikliği yapmak için Web tarayıcısının kullanımı gösterilmektedir. Bunun yerine deponuzu yerel olarak kopyalamak istiyorsanız pencerenin sağ üst kısmından **Kopyala** ' yı seçin. Visual Studio Code veya tercih ettiğiniz geliştirme aracında git deponuzu kopyalamak için belirtilen URL 'YI kullanın. 

3. Depo, oluşturma işleminde seçtiğiniz uygulama diline bağlı olarak **filtermodule** adlı bir modülün kodunu zaten içeriyor. **Modüller/FilterModule/Module. JSON** dosyasını açın.

   ![Modül. json dosyasını Azure Repos açın](./media/how-to-devops-project/open-module-json.png)

4. Bu dosyanın **Sürüm** parametresinde [Azure DevOps derleme değişkenlerini](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) kullandığından emin olun. Bu yapılandırma, her yeni derleme çalıştırıldığında modülün yeni bir sürümünün oluşturulmasını sağlar. 


## <a name="examine-the-cicd-pipeline"></a>CI/CD işlem hattını inceleyin

Önceki bölümlerde, Azure DevOps Projeleri IoT Edge uygulamanız için otomatik olarak tam CI/CD işlem hattı yapılandırdı. Şimdi, işlem hattını gereken şekilde araştırıp özelleştirin. Azure DevOps derleme ve yayın işlem hatları hakkında bilgi edinmek için aşağıdaki adımları kullanın.

1. DevOps projenizde derleme işlem hatlarını görüntülemek için, proje panonuzun menüsünde işlem **hatlarını oluştur** ' u seçin. Bu bağlantı, yeni projeniz için bir tarayıcı sekmesi ve Azure DevOps derleme işlem hattı açar.

   ![Azure Pipelines yapı işlem hatlarını görüntüleme](./media/how-to-devops-project/view-build-pipelines.png)

2. **Düzenle**’yi seçin.

    ![Derleme ardışık düzenini Düzenle](media/how-to-devops-project/click-edit-button.png)

3. Açılan panelde, derleme işlem hatlarınız çalıştırıldığında oluşan görevleri inceleyebilirsiniz. Yapı işlem hattı, git deposundan kaynak getirme, IoT Edge modül görüntüleri oluşturma, IoT Edge modülleri bir kapsayıcı kayıt defterine iletme ve dağıtımlar için kullanılan çıkışları yayımlama gibi çeşitli görevleri gerçekleştirir. Azure DevOps 'da Azure IoT Edge görevler hakkında daha fazla bilgi edinmek için bkz. [Azure Pipelines yapılandırma, sürekli tümleştirme için](how-to-ci-cd.md#configure-continuous-integration).

4. İşlem hattı ayrıntılarını açmak için derleme işlem hattının üst kısmındaki işlem **hattı** başlığını seçin. Derleme işlem hattının adını daha açıklayıcı bir şekilde değiştirin.

   ![İşlem hattı ayrıntılarını düzenleme](./media/how-to-devops-project/edit-build-pipeline.png)

5. **& Kuyruğu kaydet**' i seçin ve ardından **Kaydet**' i seçin.

6. Derleme işlem hattı menüsünden **Tetikleyiciler** ' i seçin. DevOps Projeleri otomatik olarak bir CI tetikleyicisi oluşturdu ve depoya yapılan her bir kayıt yeni bir derleme başlatır.  İsteğe bağlı olarak dalları CI işlemine dahil etmeyi veya işlemden hariç tutmayı seçebilirsiniz.

7. **Saklama**’yı seçin. Senaryonuza bağlı olarak, belirli sayıda derlemeyi tutmanın veya kaldırabilmeniz için ilkeler belirtebilirsiniz.

8. **Geçmişi**seçin. Geçmiş paneli, derlemede yapılan son değişikliklerin bir denetim izini içerir. Azure Pipelines, derleme ardışık düzeninde yapılan tüm değişiklikleri izler ve sürümleri karşılaştırmanızı sağlar.

9. Derleme işlem hattını araştırdığınızda, ilgili sürüm ardışık düzenine gidin. İşlem **hatları**altında **yayınlar** ' ı seçin ve işlem hattı ayrıntılarını görüntülemek için **Düzenle** ' yi seçin.

    ![Yayın işlem hattını görüntüle](media/how-to-devops-project/release-pipeline.png)

10. **Yapıtlar**’ın altında **Bırak**’ı seçin. Bu yapıtın izleyen kaynak, önceki adımlarda inceettiğiniz derleme işlem hattının çıktıdır. 

11. **Bırakma** simgesinin yanında, bir şimşek sürgüsü gibi görünen **sürekli dağıtım tetikleyicisini** seçin. Bu yayın işlem hattı, her yeni derleme yapıtı olduğunda bir dağıtımı çalıştıran tetikleyiciyi etkinleştirdi. İsteğe bağlı olarak, dağıtımlarınızın el ile yürütme gerektirdiğinden tetikleyiciyi devre dışı bırakabilirsiniz.  

12. Yayın işlem hatlarınızın menüsünde **Görevler** ' i seçin ve açılan listeden **dev** aşamasını seçin. DevOps Projeleri, IoT Hub 'ı oluşturan, bu hub 'da bir IoT Edge cihaz oluşturan, örnek modülünü derleme ardışık düzeninde dağıtan ve bir sanal makinenin IoT Edge cihazınız olarak çalışmasını sağlayan bir yayın aşaması oluşturdu. CD için Azure IoT Edge görevler hakkında daha fazla bilgi için bkz. [sürekli dağıtım için Azure Pipelines yapılandırma](how-to-ci-cd.md#configure-continuous-deployment).

    ![Sürekli dağıtım görevlerini görüntüle](media/how-to-devops-project/dev-release.png)

13. Sağ tarafta **sürümleri görüntüle**' yi seçin. Bu görünümde yayın geçmişi gösterilir.

14. Hakkında daha fazla bilgi görüntülemek için bir sürümün adını seçin.


## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık ihtiyaç duymadığınızda oluşturduğunuz Azure App Service ve diğer ilgili kaynakları silebilirsiniz. DevOps Projeleri panosundaki **silme** işlevini kullanın.

## <a name="next-steps"></a>Sonraki adımlar
* [Sürekli tümleştirme ve sürekli dağıtımda](how-to-ci-cd.md) Azure DevOps üzerinde Azure IoT Edge görevleri hakkında bilgi edinin Azure IoT Edge
* IoT Edge dağıtımını, [tek cihazlarda veya ölçekteki IoT Edge dağıtımlarını anlayın](module-deployment-monitoring.md)
* Dağıtım [ve izleme IoT Edge modüllerindeki](how-to-deploy-monitor.md)bir dağıtımı oluşturma, güncelleştirme veya silme adımlarını gözden geçir.
