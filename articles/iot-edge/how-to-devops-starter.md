---
title: Azure DevOps Starter ile CI/CD işlem hattı-Azure IoT Edge | Microsoft Docs
description: Azure DevOps Starter, Azure 'u kullanmaya başlamanızı kolaylaştırır. Birkaç hızlı adımda tercih ettiğiniz bir Azure IoT Edge uygulamasını başlatmanıza yardımcı olur.
author: kgremban
ms.author: kgremban
ms.date: 08/25/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8a23041a714089c447ab35ee05aae0de0c9c33d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201165"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-starter"></a>Azure DevOps Starter ile IoT Edge için bir CI/CD işlem hattı oluşturma

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

DevOps Projeleri IoT Edge uygulamanız için sürekli tümleştirme (CI) ve sürekli teslim (CD) yapılandırın. DevOps Starter, Azure Pipelines bir derleme ve serbest bırakma işlem hattının başlangıç yapılandırmasını basitleştirir.

Etkin bir Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

DevOps Starter, Azure DevOps 'da bir CI/CD işlem hattı oluşturur. Yeni bir Azure DevOps kuruluşu oluşturabilir veya var olan bir kuruluşu kullanabilirsiniz. DevOps Starter Ayrıca seçtiğiniz Azure aboneliğindeki Azure kaynaklarını da oluşturur.

1. [Microsoft Azure portalda](https://portal.azure.com) oturum açın.

1. Sol bölmede, **kaynak oluştur**' u seçin ve ardından **DevOps Starter** için arama yapın.  

1. **Oluştur**’u seçin.

1. Varsayılan olarak, DevOps Starter, GitHub ile ayarlanır. Bu nasıl yapılır içindeki özelliklerden yararlanmak için DevOps Starter 'ı Azure DevOps kullanarak ayarlanacak şekilde değiştirin. **Ayarları burada Değiştir** bağlantısını izleyin.

   ![GitHub 'dan Azure DevOps 'a geçmek için ayarları değiştir ' i seçin](./media/how-to-devops-starter/create-with-github-change-settings.png)

1. Sağ bölmede **Azure DevOps** kutucuğunu seçin ve **bitti**' yi seçin.

   ![DevOps başlangıç kodunuzu ayarlamak için Azure DevOps ' ı seçin](./media/how-to-devops-starter/select-azure-devops.png)

   Şimdi DevOps Starter 'ın Azure DevOps ile ayarlanmış olduğunu görmeniz gerekir.

## <a name="create-a-new-application-pipeline"></a>Yeni bir uygulama işlem hattı oluşturun

1. Azure IoT Edge modüllerinizi [C#](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) ve [Java](tutorial-java-module.md)'da yazılabilir. Yeni bir uygulamayı başlatmak için tercih ettiğiniz dili seçin: **.net**, **Node.js**, **Python**, **C** veya **Java**. Devam etmek için **İleri** seçeneğini belirleyin.

   ![Yeni bir uygulama oluşturmak için dil seçin](./media/how-to-devops-starter/select-language.png)

2. Uygulama çatısı olarak **basit IoT** ' yi seçin ve ardından **İleri**' yi seçin.

   ![Basit IoT çerçevesini seçin](media/how-to-devops-starter/select-iot.png)

3. Uygulamanızı dağıtan Azure hizmeti olarak **IoT Edge** seçin ve ardından **İleri**' yi seçin.

   ![IoT Edge hizmeti seçin](media/how-to-devops-starter/select-iot-edge.png)

4. Yeni bir ücretsiz Azure DevOps organizasyonu oluşturun veya var olan bir kuruluşu seçin.

   1. Projeniz için bir ad sağlayın.

   2. Azure DevOps kuruluşunuzu seçin. Mevcut bir kuruluşunuz yoksa yeni bir tane oluşturmak için **ek ayarlar** ' ı seçin.

   3. Azure aboneliğinizi seçin.

   4. Projenizin adı tarafından oluşturulan IoT Hub adını kullanın veya kendi kendinize sağlayın.

   5. Varsayılan konumu kabul edin veya bir kapanış seçin.

   6. DevOps Starter tarafından sizin adınıza oluşturulan Azure kaynaklarını yapılandırmak için **ek ayarlar** ' ı seçin.

   7. Projenizi oluşturmayı bitirdiğinizde **bitti** ' yi seçin.

   ![Projeyi adlandırın ve oluşturun](media/how-to-devops-starter/create-project.png)

Birkaç dakika sonra DevOps başlangıç panosu Azure portal görüntülenir. İlerleme durumunu görmek için projenizin adını seçin. Sayfayı yenilemeniz gerekebilir. Örnek bir IoT Edge uygulaması Azure DevOps kuruluşunuzda bir depoda ayarlanır, bir derleme yürütülür ve uygulamanız IoT Edge cihazına dağıtılır. Bu Pano, kod deponuza, CI/CD işlem hattına ve Azure 'daki uygulamanıza ilişkin görünürlük sağlar.

   ![Projeyi Azure portal görüntüle](./media/how-to-devops-starter/portal.png)

## <a name="commit-code-changes-and-execute-cicd"></a>Kod değişikliklerini işleme ve CI/CD’yi yürütme

DevOps Starter, Azure Repos projeniz için bir git deposu oluşturdu. Bu bölümde, depoyu görüntüler ve uygulamanızda kod değişikliği yaparsınız.

1. Projeniz için oluşturulan depoya gitmek için, proje panonuzun menüsünde **depolar** ' ı seçin. Bu bağlantı, yeni projeniz için bir tarayıcı sekmesi ve Azure DevOps deposu açar.

   ![Azure Repos oluşturulan depoyu görüntüle](./media/how-to-devops-starter/view-repositories.png)

   > [!NOTE]
   > Aşağıdaki adımlarda, kod değişikliği yapmak için Web tarayıcısının kullanımı gösterilmektedir. Bunun yerine deponuzu yerel olarak kopyalamak istiyorsanız pencerenin sağ üst kısmından **Kopyala** ' yı seçin. Visual Studio Code veya tercih ettiğiniz geliştirme aracında git deponuzu kopyalamak için belirtilen URL 'YI kullanın.

2. Depo, oluşturma işleminde seçtiğiniz uygulama diline bağlı olarak **filtermodule** adlı bir modülün kodunu zaten içeriyor. Dosya **üzerinde modüller/FilterModule/module.js** açın.

   ![Azure Repos dosyasında module.jsaçın](./media/how-to-devops-starter/open-module-json.png)

3. Bu dosyanın **Sürüm** parametresinde [Azure DevOps derleme değişkenlerini](/azure/devops/pipelines/build/variables#build-variables) kullandığından emin olun. Bu yapılandırma, her yeni derleme çalıştırıldığında modülün yeni bir sürümünün oluşturulmasını sağlar.

## <a name="examine-the-cicd-pipeline"></a>CI/CD işlem hattını inceleyin

Önceki bölümlerde Azure DevOps Starter, IoT Edge uygulamanız için bir tam CI/CD işlem hattı otomatik olarak yapılandırdı. Şimdi, işlem hattını gereken şekilde araştırıp özelleştirin. Azure DevOps derleme ve yayın işlem hatları hakkında bilgi edinmek için aşağıdaki adımları kullanın.

1. DevOps projenizde derleme işlem hatlarını görüntülemek için, proje panonuzun menüsünde işlem **hatlarını oluştur** ' u seçin. Bu bağlantı, yeni projeniz için bir tarayıcı sekmesi ve Azure DevOps derleme işlem hattı açar.

   ![Azure Pipelines yapı işlem hatlarını görüntüleme](./media/how-to-devops-starter/view-build-pipelines.png)

2. Otomatik olarak oluşturulan derleme ardışık düzenini açın ve sağ üst köşedeki **Düzenle** ' yi seçin.

    ![Derleme ardışık düzenini Düzenle](media/how-to-devops-starter/click-edit-button.png)

3. Açılan panelde, derleme işlem hatlarınız çalıştırıldığında oluşan görevleri inceleyebilirsiniz. Yapı işlem hattı, git deposundan kaynak getirme, IoT Edge modül görüntüleri oluşturma, IoT Edge modülleri bir kapsayıcı kayıt defterine iletme ve dağıtımlar için kullanılan çıkışları yayımlama gibi çeşitli görevleri gerçekleştirir. Azure DevOps 'da Azure IoT Edge görevler hakkında daha fazla bilgi edinmek için bkz. [Azure Pipelines yapılandırma, sürekli tümleştirme için](how-to-continuous-integration-continuous-deployment-classic.md#create-a-build-pipeline-for-continuous-integration).

4. İşlem hattı ayrıntılarını açmak için derleme işlem hattının üst kısmındaki işlem **hattı** başlığını seçin. Derleme işlem hattınızın adını daha açıklayıcı bir şeyle değiştirin.

   ![İşlem hattı ayrıntılarını düzenleme](./media/how-to-devops-starter/edit-build-pipeline.png)

5. **& kuyruğu kaydet**' i seçin ve ardından **Kaydet**' i seçin. Açıklama için isteğe bağlıdır.

6. Derleme işlem hattı menüsünden **Tetikleyiciler** ' i seçin. DevOps Starter otomatik olarak bir CI tetikleyicisi oluşturdu ve depoya yapılan her işlem yeni bir derleme başlatır.  İsteğe bağlı olarak dalları CI işlemine dahil etmeyi veya işlemden hariç tutmayı seçebilirsiniz.

7. **Saklama**’yı seçin. Sizi bekletme ilkelerinin bulunduğu proje ayarlarına yönlendirmek için bağlantıyı izleyin. Senaryonuza bağlı olarak, belirli sayıda derlemeyi tutmanın veya kaldırabilmeniz için ilkeler belirtebilirsiniz.

8. **Geçmişi** seçin. Geçmiş paneli, derlemede yapılan son değişikliklerin bir denetim izini içerir. Azure Pipelines, derleme ardışık düzeninde yapılan tüm değişiklikleri izler ve sürümleri karşılaştırmanızı sağlar.

9. Derleme işlem hattını araştırdığınızda, ilgili sürüm ardışık düzenine gidin. İşlem **hatları** altında **yayınlar** ' ı seçin ve işlem hattı ayrıntılarını görüntülemek için **Düzenle** ' yi seçin.

    ![Yayın işlem hattını görüntüle](media/how-to-devops-starter/release-pipeline.png)

10. **Yapıtlar**’ın altında **Bırak**’ı seçin. Bu yapıtın izleyen kaynak, önceki adımlarda inceettiğiniz derleme işlem hattının çıktıdır.

11. **Bırakma** simgesinin yanında, bir şimşek sürgüsü gibi görünen **sürekli dağıtım tetikleyicisini** seçin. Bu yayın işlem hattı, her yeni derleme yapıtı olduğunda bir dağıtımı çalıştıran tetikleyiciyi etkinleştirdi. İsteğe bağlı olarak, dağıtımlarınızın el ile yürütme gerektirdiğinden tetikleyiciyi devre dışı bırakabilirsiniz.  

12. Yayın işlem hatlarınızın menüsünde **Görevler** ' i seçin ve açılan listeden **dev** aşamasını seçin. DevOps Projeleri, IoT Hub 'ı oluşturan, bu hub 'da bir IoT Edge cihaz oluşturan, örnek modülünü derleme ardışık düzeninde dağıtan ve bir sanal makinenin IoT Edge cihazınız olarak çalışmasını sağlayan bir yayın aşaması oluşturdu. CD için Azure IoT Edge görevler hakkında daha fazla bilgi için bkz. [sürekli dağıtım için Azure Pipelines yapılandırma](how-to-continuous-integration-continuous-deployment-classic.md#create-a-release-pipeline-for-continuous-deployment).

    ![Sürekli dağıtım görevlerini görüntüle](media/how-to-devops-starter/choose-release.png)

13. Sağ tarafta **sürümleri görüntüle**' yi seçin. Bu görünümde yayın geçmişi gösterilir.

14. Hakkında daha fazla bilgi görüntülemek için bir sürümün adını seçin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık ihtiyaç duymadığınızda oluşturduğunuz Azure App Service ve diğer ilgili kaynakları silebilirsiniz. DevOps başlangıç panosundaki **silme** işlevini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* [Sürekli tümleştirme ve sürekli dağıtımda](how-to-continuous-integration-continuous-deployment.md) Azure DevOps üzerinde Azure IoT Edge görevleri hakkında bilgi edinin Azure IoT Edge
* IoT Edge dağıtımını, [tek cihazlarda veya ölçekteki IoT Edge dağıtımlarını anlayın](module-deployment-monitoring.md)
* Dağıtım [ve izleme IoT Edge modüllerindeki](how-to-deploy-at-scale.md)bir dağıtımı oluşturma, güncelleştirme veya silme adımlarını gözden geçir.
