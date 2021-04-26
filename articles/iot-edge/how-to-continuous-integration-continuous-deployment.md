---
title: Azure IoT Edge cihazlara sürekli tümleştirme ve sürekli dağıtım-Azure IoT Edge
description: Azure DevOps ile YAML-Azure IoT Edge kullanarak sürekli tümleştirme ve sürekli dağıtım ayarlayın Azure Pipelines
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d9db9997af20fee226214eb12ad32729cab55caa
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199246"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices"></a>Azure IoT Edge cihazlara sürekli tümleştirme ve sürekli dağıtım

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure Pipelines içindeki yerleşik Azure IoT Edge görevlerle DevOps 'u Azure IoT Edge uygulamalarınızla kolayca benimseyebilirsiniz. Bu makalede, YAML kullanarak Azure IoT Edge uygulamaları hızla ve verimli bir şekilde oluşturmak, test etmek ve dağıtmak için Azure Pipelines sürekli tümleştirme ve sürekli dağıtım özelliklerini nasıl kullanabileceğiniz gösterilmektedir. Alternatif olarak, [Klasik düzenleyiciyi de kullanabilirsiniz](how-to-continuous-integration-continuous-deployment-classic.md).

![Geliştirme ve üretim için diyagram-CI ve CD dalları](./media/how-to-continuous-integration-continuous-deployment/model.png)

Bu makalede, IoT Edge çözümünüz için derleme ve yayın işlem hatları oluşturmak üzere Azure Pipelines için yerleşik [Azure IoT Edge görevlerinin](/azure/devops/pipelines/tasks/build/azure-iot-edge) nasıl kullanılacağını öğrenirsiniz. İşlem hattınızı eklenen her bir Azure IoT Edge görevi aşağıdaki dört eylemden birini uygular:

 | Eylem | Açıklama |
 | --- | --- |
 | Modül görüntülerini oluşturma | IoT Edge çözüm kodunuzu alır ve kapsayıcı görüntülerini oluşturur.|
 | Modül görüntülerini gönder | Modül görüntülerini belirttiğiniz kapsayıcı kayıt defterine iter. |
 | Dağıtım bildirimi oluştur | Dosya ve değişkenlerde bir deployment.template.jsalır, ardından son IoT Edge dağıtım bildirim dosyasını oluşturur. |
 | IoT Edge'e cihazlarına dağıtma | Bir veya daha fazla IoT Edge cihazda IoT Edge dağıtımları oluşturur. |

Aksi belirtilmediği takdirde, bu makaledeki yordamlar görev parametreleri aracılığıyla kullanılabilen tüm işlevleri araştırmaz. Daha fazla bilgi için, aşağıdakilere bakın:

* [Görev sürümü](/azure/devops/pipelines/process/tasks?tabs=yaml#task-versions)
* **Gelişmiş** -uygunsa, inşa etmek istemediğiniz modülleri belirtin.
* [Denetim seçenekleri](/azure/devops/pipelines/process/tasks?tabs=yaml#task-control-options)
* [Ortam değişkenleri](/azure/devops/pipelines/process/variables?tabs=yaml#environment-variables)
* [Çıkış değişkenleri](/azure/devops/pipelines/process/variables?tabs=yaml#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure Repos deposu. Bir tane yoksa, [projenizde yeni bir git deposu oluşturabilirsiniz](/azure/devops/repos/git/create-new-repo). Bu makalede, **ıotedgerepo** adlı bir depo oluşturduk.
* IoT Edge bir çözüm kaydedilir ve deponuza gönderilir. Bu makaleyi test etmek için yeni bir örnek çözüm oluşturmak istiyorsanız, [Visual Studio Code 'de modül geliştirme ve hata ayıklama](how-to-vs-code-develop-module.md) veya [Visual Studio 'Da C# modüllerini geliştirme ve hata](./how-to-visual-studio-develop-module.md)ayıklama işlemleri bölümündeki adımları uygulayın. Bu makalede, depomızda **filtermodule** adlı bir modülün kodunu içeren **IoTEdgeSolution** adlı bir çözüm oluşturduk.

   Bu makalede, her türlü Visual Studio Code veya Visual Studio 'da IoT Edge şablonları tarafından oluşturulan çözüm klasörüdür. Devam etmeden önce bu kodu oluşturmanız, göndermeniz, dağıtmanız veya hata ayıklamanıza gerek yoktur. Azure Pipelines bu süreçlerini ayarlayacaksınız.

   Yeni bir çözüm oluşturuyorsanız, deponuzu önce yerel olarak kopyalayın. Ardından, çözümü oluşturduğunuzda doğrudan depo klasöründe oluşturmayı seçebilirsiniz. Yeni dosyaları oradan kolayca kaydedebilir ve gönderebilirsiniz.

* Modül görüntülerini ititebileceğiniz bir kapsayıcı kayıt defteri. [Azure Container Registry](../container-registry/index.yml) veya bir üçüncü taraf kayıt defteri kullanabilirsiniz.
* Ayrı test ve üretim dağıtımı aşamalarını test etmek için en az iki IoT Edge cihazı olan etkin bir Azure [IoT Hub 'ı](../iot-hub/iot-hub-create-through-portal.md) . [Linux](quickstart-linux.md) veya [Windows](quickstart.md) üzerinde IoT Edge bir cihaz oluşturmak için hızlı başlangıç makalelerini takip edebilirsiniz

Azure Repos kullanma hakkında daha fazla bilgi için bkz. [Visual Studio ile kodunuzu paylaşma ve Azure Repos](/azure/devops/repos/git/share-your-code-in-git-vs)

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Sürekli tümleştirme için derleme işlem hattı oluşturma

Bu bölümde, yeni bir yapı işlem hattı oluşturacaksınız. İşlem hattını, örnek IoT Edge çözümünde yapılan herhangi bir değişikliği iade ettiğinizde ve Derleme günlüklerini yayımlamak için otomatik olarak çalışacak şekilde yapılandırırsınız.

1. Azure DevOps kuruluşunuzda () oturum açın `https://dev.azure.com/{your organization}` ve IoT Edge çözüm deponuzu içeren projeyi açın.

   ![DevOps projenizi açın](./media/how-to-continuous-integration-continuous-deployment/initial-project.png)

2. Projenizdeki sol bölme menüsünde işlem **hatları**' nı seçin. Sayfanın merkezinde işlem **hattı oluştur** ' u seçin. Ya da zaten derleme işlem hatlarınız varsa sağ üst köşedeki **Yeni işlem hattı** düğmesini seçin.

    ![Yeni ardışık düzen düğmesini kullanarak yeni bir derleme işlem hattı oluşturun](./media/how-to-continuous-integration-continuous-deployment/add-new-pipeline.png)

3. **Kodunuz nerede?** sayfasında **`YAML` Git Azure Repos**' yi seçin. Projenizin derleme işlem hatlarını oluşturmak için klasik düzenleyiciyi kullanmak istiyorsanız [Klasik düzenleyici kılavuzuna](how-to-continuous-integration-continuous-deployment-classic.md)bakın.

4. İşlem hattı oluşturduğunuz depoyu seçin.

    ![Derleme işlem hattınızla ilgili depoyu seçin](./media/how-to-continuous-integration-continuous-deployment/select-repository.png)

5. **Ardışık düzen yapılandırma** sayfasında **Başlatıcı işlem hattı**' nı seçin. Bu işlem hattını oluşturmak için kullanmak istediğiniz önceden var olan bir Azure Pipelines YAML dosyanız varsa, **mevcut Azure Pipelines YAML dosyasını** seçebilir ve depoya dosya için dala ve yola girebilirsiniz.

    ![Derleme ardışık düzenine başlamak için Başlatıcı işlem hattı veya mevcut Azure Pipelines YAML dosyasını seçin](./media/how-to-continuous-integration-continuous-deployment/configure-pipeline.png)

6. İşlem **hattınızı gözden geçirin YAML** sayfasında, işlem `azure-pipelines.yml` hattının yapılandırma dosyasını yeniden adlandırmak için varsayılan ada tıklayabilirsiniz.

   **Görev** paletini açmak Için **yardımcıyı göster** ' i seçin.

    ![Görevler paletini açmak için yardımcıyı göster ' i seçin](./media/how-to-continuous-integration-continuous-deployment/show-assistant.png)

7. Bir görev eklemek için imlecinizi YAML 'nin sonuna veya göreviniz için yönergelerin eklenmesini istediğiniz yere yerleştirin. Arama yapın ve **Azure IoT Edge** seçin. Görevin parametrelerini aşağıdaki şekilde doldurun. Ardından **Ekle**' yi seçin.

   | Parametre | Açıklama |
   | --- | --- |
   | Eylem | **Derleme modülü görüntülerini** seçin. |
   | Dosya üzerinde .template.js | IoT Edge çözümünüzü içeren depodaki **deployment.template.js** dosyanın yolunu belirtin. |
   | Varsayılan platform | Modülleriniz için hedeflenen IoT Edge cihazınıza göre uygun işletim sistemini seçin. |

    ![İşlem hattınızı görev eklemek için görev paletini kullanma](./media/how-to-continuous-integration-continuous-deployment/add-build-task.png)

   >[!TIP]
   > Her görev eklendikten sonra, düzenleyici eklenen satırları otomatik olarak vurgulayacaktır. Yanlışlıkla yazılmasını engellemek için satırların seçimini kaldırın ve ek görevler eklemeden önce bir sonraki göreviniz için yeni bir alan sağlayın.

8. Aşağıdaki parametrelere sahip üç görev daha eklemek için bu işlemi tekrarlayın:

   * Görev: **Azure IoT Edge**

       | Parametre | Açıklama |
       | --- | --- |
       | Eylem | **Gönderim modülü görüntülerini** seçin. |
       | Kapsayıcı kayıt defteri türü | Varsayılan türü kullanın: **Azure Container Registry**. |
       | Azure aboneliği | Aboneliğinizi seçin. |
       | Azure Container Registry | İşlem hattı için kullanmak istediğiniz kayıt defterini seçin. |
       | Dosya üzerinde .template.js | IoT Edge çözümünüzü içeren depodaki **deployment.template.js** dosyanın yolunu belirtin. |
       | Varsayılan platform | Modülleriniz için hedeflenen IoT Edge cihazınıza göre uygun işletim sistemini seçin. |

   * Görev: **dosyaları Kopyala**

       | Parametre | Açıklama |
       | --- | --- |
       | Kaynak klasör | Kopyalanacak kaynak klasör. Empty, deponun köküdür. Dosyalar depoda değilse değişkenleri kullanın. Örnek: `$(agent.builddirectory)`.
       | İçindekiler | İki satır ekleyin: `deployment.template.json` ve `**/module.json` . |
       | Hedef klasör | Değişkeni belirtin `$(Build.ArtifactStagingDirectory)` . Açıklama hakkında bilgi edinmek için bkz. [derleme değişkenleri](/azure/devops/pipelines/build/variables?tabs=yaml#build-variables) . |

   * Görev: **derleme yapıtları Yayımla**

       | Parametre | Açıklama |
       | --- | --- |
       | Yayımlanacak yol | Değişkeni belirtin `$(Build.ArtifactStagingDirectory)` . Açıklama hakkında bilgi edinmek için bkz. [derleme değişkenleri](/azure/devops/pipelines/build/variables?tabs=yaml#build-variables) . |
       | Yapıt adı | Varsayılan adı belirtin: `drop` |
       | Yapıt yayımlama konumu | Varsayılan konumu kullanın: `Azure Pipelines` |

9. Sağ üst köşedeki **Kaydet ve Çalıştır** açılır listesinden **Kaydet** ' i seçin.

10. Sürekli Tümleştirme tetikleyicisi, YAML işlem hattınızda varsayılan olarak etkinleştirilmiştir. Bu ayarları düzenlemek isterseniz, işlem hattınızı seçin ve sağ üst köşedeki **Düzenle** ' ye tıklayın. Sağ üstteki **Çalıştır** düğmesinin yanındaki **diğer eylemler** ' i seçin ve **Tetikleyiciler**' e gidin. **Sürekli tümleştirme** , ardışık düzenin adı altında etkinleştirilmiş olarak gösterilir. Tetikleyicinin ayrıntılarını görmek isterseniz, **YAML sürekli tümleştirme tetikleyicisi ' ni buradan geçersiz kıl** kutusunu işaretleyin.

    ![İşlem hattının tetikleyici ayarlarını gözden geçirmek için, daha fazla eylem altındaki tetikleyicilere bakın](./media/how-to-continuous-integration-continuous-deployment/check-trigger-settings.png)

Yayın işlem hattını derlemek için sonraki bölüme geçin.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Sonraki adımlar

* [IoT Edge Için Azure DevOps Starter](how-to-devops-starter.md) 'Da DevOps en iyi yöntemler örneği IoT Edge
* IoT Edge dağıtımını, [tek cihazlarda veya ölçekteki IoT Edge dağıtımlarını anlayın](module-deployment-monitoring.md)
* Dağıtım [ve izleme IoT Edge modüllerindeki](how-to-deploy-at-scale.md)bir dağıtımı oluşturma, güncelleştirme veya silme adımlarını gözden geçir.