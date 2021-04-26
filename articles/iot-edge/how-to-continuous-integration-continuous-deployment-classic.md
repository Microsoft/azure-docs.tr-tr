---
title: Azure IoT Edge cihazlara sürekli tümleştirme ve sürekli dağıtım (klasik düzenleyici)-Azure IoT Edge
description: Azure DevOps ile klasik düzenleyici-Azure IoT Edge kullanarak sürekli tümleştirme ve sürekli dağıtım ayarlayın Azure Pipelines
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f7c28ecbaa58731c528a9ecb5f869eba2bc0c99f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484430"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices-classic-editor"></a>Azure IoT Edge cihazlara sürekli tümleştirme ve sürekli dağıtım (klasik düzenleyici)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure Pipelines içindeki yerleşik Azure IoT Edge görevlerle DevOps 'u Azure IoT Edge uygulamalarınızla kolayca benimseyebilirsiniz. Bu makalede, klasik düzenleyiciyi kullanarak Azure IoT Edge uygulamaları hızla ve verimli bir şekilde oluşturmak, test etmek ve dağıtmak için Azure Pipelines sürekli tümleştirme ve sürekli dağıtım özelliklerini nasıl kullanabileceğiniz gösterilmektedir. Alternatif olarak, [YAML](how-to-continuous-integration-continuous-deployment.md)'yi de kullanabilirsiniz.

![Geliştirme ve üretim için diyagram-CI ve CD dalları](./media/how-to-continuous-integration-continuous-deployment-classic/model.png)

Bu makalede, IoT Edge çözümünüz için derleme ve yayın işlem hatları oluşturmak üzere Azure Pipelines için yerleşik [Azure IoT Edge görevlerinin](/azure/devops/pipelines/tasks/build/azure-iot-edge) nasıl kullanılacağını öğrenirsiniz. İşlem hattınızı eklenen her bir Azure IoT Edge görevi aşağıdaki dört eylemden birini uygular:

 | Eylem | Açıklama |
 | --- | --- |
 | Modül görüntülerini oluşturma | IoT Edge çözüm kodunuzu alır ve kapsayıcı görüntülerini oluşturur.|
 | Modül görüntülerini gönder | Modül görüntülerini belirttiğiniz kapsayıcı kayıt defterine iter. |
 | Dağıtım bildirimi oluştur | Dosya ve değişkenlerde bir deployment.template.jsalır, ardından son IoT Edge dağıtım bildirim dosyasını oluşturur. |
 | IoT Edge'e cihazlarına dağıtma | Bir veya daha fazla IoT Edge cihazda IoT Edge dağıtımları oluşturur. |

Aksi belirtilmediği takdirde, bu makaledeki yordamlar görev parametreleri aracılığıyla kullanılabilen tüm işlevleri araştırmaz. Daha fazla bilgi için, aşağıdakilere bakın:

* [Görev sürümü](/azure/devops/pipelines/process/tasks?tabs=classic#task-versions)
* **Gelişmiş** -uygunsa, inşa etmek istemediğiniz modülleri belirtin.
* [Denetim seçenekleri](/azure/devops/pipelines/process/tasks?tabs=classic#task-control-options)
* [Ortam değişkenleri](/azure/devops/pipelines/process/variables?tabs=classic#environment-variables)
* [Çıkış değişkenleri](/azure/devops/pipelines/process/variables?tabs=classic#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure Repos deposu. Bir tane yoksa, [projenizde yeni bir git deposu oluşturabilirsiniz](/azure/devops/repos/git/create-new-repo). Bu makalede, **ıotedgerepo** adlı bir depo oluşturduk.
* IoT Edge bir çözüm kaydedilir ve deponuza gönderilir. Bu makaleyi test etmek için yeni bir örnek çözüm oluşturmak istiyorsanız, [Visual Studio Code 'de modül geliştirme ve hata ayıklama](how-to-vs-code-develop-module.md) veya [Visual Studio 'Da C# modüllerini geliştirme ve hata](./how-to-visual-studio-develop-module.md)ayıklama işlemleri bölümündeki adımları uygulayın. Bu makalede, depomızda **filtermodule** adlı bir modülün kodunu içeren **IoTEdgeSolution** adlı bir çözüm oluşturduk.

   Bu makalede, her türlü Visual Studio Code veya Visual Studio 'da IoT Edge şablonları tarafından oluşturulan çözüm klasörüdür. Devam etmeden önce bu kodu oluşturmanız, göndermeniz, dağıtmanız veya hata ayıklamanıza gerek yoktur. Azure Pipelines bu süreçlerini ayarlayacaksınız.

   Yeni bir çözüm oluşturuyorsanız, deponuzu önce yerel olarak kopyalayın. Ardından, çözümü oluşturduğunuzda doğrudan depo klasöründe oluşturmayı seçebilirsiniz. Yeni dosyaları oradan kolayca kaydedebilir ve gönderebilirsiniz.

* Modül görüntülerini ititebileceğiniz bir kapsayıcı kayıt defteri. [Azure Container Registry](../container-registry/index.yml) veya bir üçüncü taraf kayıt defteri kullanabilirsiniz.
* Ayrı test ve üretim dağıtımı aşamalarını test etmek için en az iki IoT Edge cihazı olan etkin bir Azure [IoT Hub 'ı](../iot-hub/iot-hub-create-through-portal.md) . [Linux](quickstart-linux.md) veya [Windows](quickstart.md) üzerinde IoT Edge bir cihaz oluşturmak için hızlı başlangıç makalelerini takip edebilirsiniz

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Sürekli tümleştirme için derleme işlem hattı oluşturma

Bu bölümde, yeni bir yapı işlem hattı oluşturacaksınız. İşlem hattını, örnek IoT Edge çözümünde yapılan herhangi bir değişikliği iade ettiğinizde ve Derleme günlüklerini yayımlamak için otomatik olarak çalışacak şekilde yapılandırırsınız.

1. Azure DevOps kuruluşunuzda () oturum açın `https://dev.azure.com/{your organization}` ve IoT Edge çözüm deponuzu içeren projeyi açın.

    ![DevOps projenizi açın](./media/how-to-continuous-integration-continuous-deployment-classic/initial-project.png)

2. Projenizdeki sol bölme menüsünde işlem **hatları**' nı seçin. Sayfanın merkezinde işlem **hattı oluştur** ' u seçin. Ya da zaten derleme işlem hatlarınız varsa sağ üst köşedeki **Yeni işlem hattı** düğmesini seçin.

    ![Yeni derleme işlem hattı oluşturma](./media/how-to-continuous-integration-continuous-deployment-classic/add-new-pipeline.png)

3. **Kodunuz nerede?** sayfasında, **Klasik düzenleyiciyi kullan**' ı seçin. Projenizin derleme işlem hatlarını oluşturmak için YAML 'yi kullanmak istiyorsanız, [YAML kılavuzuna](how-to-continuous-integration-continuous-deployment.md)bakın.

    ![Klasik düzenleyiciyi kullan seçeneğini belirleyin](./media/how-to-continuous-integration-continuous-deployment-classic/create-without-yaml.png)

4. İşlem hattınızı oluşturmak için istemleri izleyin.

   1. Yeni derleme işlem hattının kaynak bilgilerini sağlayın. Kaynak olarak **Git Azure Repos** seçin, sonra IoT Edge çözüm kodunuzun bulunduğu projeyi, depoyu ve dalı seçin. Sonra **devam**' ı seçin.

      ![İşlem hattı kaynağınızı seçin](./media/how-to-continuous-integration-continuous-deployment-classic/pipeline-source.png)

   2. Şablon yerine **boş işi** seçin.

      ![Derleme işlem hattınızda boş bir iş ile başlayın](./media/how-to-continuous-integration-continuous-deployment-classic/start-with-empty-build-job.png)

5. Ardışık düzen oluşturulduktan sonra işlem hattı düzenleyicisine yönlendirilirsiniz. Burada, işlem hattının adını, aracı havuzunu ve aracı belirtimini değiştirebilirsiniz.

   Microsoft tarafından barındırılan bir havuz veya yönettiğiniz şirket içinde barındırılan bir havuz seçebilirsiniz.

   İşlem hattı tanımınızda, hedef platformunuza bağlı olarak doğru aracı belirtimini seçin:

   * Modüllerinizi Linux kapsayıcıları için platform AMD64 'de derlemek istiyorsanız **Ubuntu-16,04** ' ı seçin.

   * Modüllerinizi Windows 1809 kapsayıcıları için platform AMD64 'de derlemek isterseniz, [Windows üzerinde şirket içinde barındırılan aracıyı ayarlamanız](/azure/devops/pipelines/agents/v2-windows)gerekir.

   * Modüllerinizi Linux kapsayıcıları için platform arm32v7 veya arm64 ' de derlemek isterseniz, [Linux üzerinde şirket içinde barındırılan aracıyı ayarlamanız](https://devblogs.microsoft.com/iotdev/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent)gerekir.

    ![Yapı Aracısı belirtimini yapılandırma](./media/how-to-continuous-integration-continuous-deployment-classic/configure-env.png)

6. İşlem hattınız, **Aracı işi 1** adlı bir iş ile önceden yapılandırılmış olarak gelir. **+** İşe dört görev eklemek için artı işaretini () seçin: iki kez **Azure IoT Edge** , **dosyaları** bir kez kopyalayın ve **derleme yapıtlarını** bir kez yayımlayın. Her bir görevi arayın ve **Ekle** düğmesini görmek için görevin adının üzerine gelin.

   ![Azure IoT Edge görev ekle](./media/how-to-continuous-integration-continuous-deployment-classic/add-iot-edge-task.png)

   Dört görev eklendiğinde, aracı işiniz aşağıdaki örneğe benzer şekilde görünür:

   ![Derleme ardışık düzeninde dört görev](./media/how-to-continuous-integration-continuous-deployment-classic/add-tasks.png)

7. Düzenlemek için ilk **Azure IoT Edge** görevi seçin. Bu görev, Çözümdeki tüm modülleri belirttiğiniz hedef platforma göre oluşturur. Görevi aşağıdaki değerlerle düzenleyin:

    | Parametre | Açıklama |
    | --- | --- |
    | Görünen ad | Görünen ad, eylem alanı değiştiğinde otomatik olarak güncelleştirilir. |
    | Eylem | **Derleme modülü görüntülerini** seçin. |
    | Dosya üzerinde .template.js | Üç nokta (**...**) simgesini seçin ve IoT Edge çözümünüzü içeren depodaki dosyadaki **deployment.template.js** gidin. |
    | Varsayılan platform | Modülleriniz için hedeflenen IoT Edge cihazınıza göre uygun işletim sistemini seçin. |
    | Çıkış değişkenleri | Dosya yolu ile ilişkilendirilecek, **kenar** gibi deployment.jsbir başvuru adı girin. |

   Bu yapılandırmalarda, `module.json` Modül görüntüsünü adlandırmak ve etiketlemek için dosyada tanımlanan görüntü deposu ve etiketi kullanılır. **Derleme modülü görüntüleri** , değişkenlerin dosyada tanımladığınız tam değerle değiştirilmesini de sağlar `module.json` . Visual Studio veya Visual Studio Code içinde, gerçek değeri bir dosyada belirtmektir `.env` . Azure Pipelines, işlem **hattı değişkenleri** sekmesinde değeri ayarlarsınız. İşlem hattı Düzenleyicisi menüsünde **değişkenler** sekmesini seçin ve ad ve değeri aşağıdaki şekilde yapılandırın:

    * **ACR_ADDRESS**: Azure Container Registry **oturum açma sunucunuzun** değeri. Oturum açma sunucusunu Azure portal kapsayıcı kayıt defterinizin genel bakış sayfasından alabilirsiniz.

    Projenizde başka değişkenlere sahipseniz, bu sekmede adı ve değeri belirtebilirsiniz. **derleme modülü görüntüleri** yalnızca biçimdeki değişkenleri tanır. `${VARIABLE}` Dosyalarınızda bu biçimi kullandığınızdan emin olun `**/module.json` .

8. Düzenlemek için ikinci **Azure IoT Edge** görevi seçin. Bu görev, tüm modül görüntülerini seçtiğiniz kapsayıcı kayıt defterine iter.

    | Parametre | Açıklama |
    | --- | --- |
    | Görünen ad | Görünen ad, eylem alanı değiştiğinde otomatik olarak güncelleştirilir. |
    | Eylem | **Gönderim modülü görüntülerini** seçin. |
    | Kapsayıcı kayıt defteri türü | Varsayılan türü kullanın: `Azure Container Registry` . |
    | Azure aboneliği | Aboneliğinizi seçin. |
    | Azure Container Registry | Modül görüntülerinizi depolamak için kullandığınız kapsayıcı kayıt defteri türünü seçin. Seçtiğiniz kayıt defteri türüne bağlı olarak, form değişir. **Azure Container Registry** öğesini seçerseniz, Azure aboneliğini ve kapsayıcı kayıt defterinizin adını seçmek için açılan listeleri kullanın. **Genel Container Registry** seçerseniz, **Yeni** ' yi seçerek bir kayıt defteri hizmet bağlantısı oluşturun. |
    | Dosya üzerinde .template.js | Üç nokta (**...**) simgesini seçin ve IoT Edge çözümünüzü içeren depodaki dosyadaki **deployment.template.js** gidin. |
    | Varsayılan platform | Modülleriniz için hedeflenen IoT Edge cihazınıza göre uygun işletim sistemini seçin. |
    | Dağıtım bildirimine kayıt defteri kimlik bilgilerini ekle | Docker görüntülerini dağıtım bildirimine dağıtmaya yönelik kayıt defteri kimlik bilgilerini eklemek için true değerini belirtin. |

   Modül görüntülerinizi barındırmak için birden çok kapsayıcı kayıt defterine sahipseniz, bu görevi çoğaltmanıza, farklı kapsayıcı kayıt defteri ' ne ve **Gelişmiş** ayarlar ' da **atlama modülünü** kullanarak bu belirli kayıt defteri için olmayan görüntüleri atlamanıza gerek duyarsınız.

9. Düzenlemek için **dosyaları Kopyala** görevini seçin. Dosyaları yapıt hazırlama dizinine kopyalamak için bu görevi kullanın.

    | Parametre | Açıklama |
    | --- | --- |
    | Görünen ad | Varsayılan adı kullan veya Özelleştir |
    | Kaynak klasör | Kopyalanacak dosyaları içeren klasör. |
    | İçindekiler | İki satır ekleyin: `deployment.template.json` ve `**/module.json` . Bu iki dosya IoT Edge dağıtım bildirimini oluşturmak için giriş olarak hizmet verir. |
    | Hedef klasör | Değişkeni belirtin `$(Build.ArtifactStagingDirectory)` . Açıklama hakkında bilgi edinmek için bkz. [derleme değişkenleri](/azure/devops/pipelines/build/variables#build-variables) . |

10. Düzenlemek için **derleme yapıtları Yayımla** görevini seçin. Yolun serbest bırakma işlem hattına yayınlanabilmesi için göreve yapıt hazırlama dizini yolu sağlayın.

    | Parametre | Açıklama |
    | --- | --- |
    | Görünen ad | Varsayılan adı kullanın veya özelleştirin. |
    | Yayımlanacak yol | Değişkeni belirtin `$(Build.ArtifactStagingDirectory)` . Daha fazla bilgi için bkz. [derleme değişkenleri](/azure/devops/pipelines/build/variables#build-variables) . |
    | Yapıt adı | Varsayılan adı kullan: **bırak** |
    | Yapıt yayımlama konumu | Varsayılan konumu kullanın: **Azure Pipelines** |

11. **Tetikleyiciler** sekmesini açın ve **sürekli tümleştirmeyi etkinleştirmek** için kutuyu işaretleyin. Kodunuzun bulunduğu dalın eklendiğinden emin olun.

    ![Sürekli tümleştirme tetikleyicisini aç](./media/how-to-continuous-integration-continuous-deployment-classic/configure-trigger.png)

12. **& sırayı kaydet** açılır listesinden **Kaydet** ' i seçin.

Bu işlem hattı, artık yeni kodu depoya gönderdiğinizde otomatik olarak çalışacak şekilde yapılandırılmıştır. İşlem hattı yapıtlarını yayımlayan son görev, bir yayın işlem hattını tetikler. Yayın işlem hattını derlemek için sonraki bölüme geçin.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

>[!NOTE]
>İşlem hattınızda **katmanlı dağıtımlar** kullanmak istiyorsanız, katmanlı dağıtımlar henüz Azure devops 'daki Azure IoT Edge görevlerinde desteklenmez.
>
>Ancak, [Azure DevOps 'da bir Azure CLI görevi](/azure/devops/pipelines/tasks/deploy/azure-cli) kullanarak dağıtımınızı katmanlı bir dağıtım olarak oluşturabilirsiniz. **Satır Içi betik** değeri için [az IoT Edge Deployment Create komutunu](/cli/azure/iot/edge/deployment)kullanabilirsiniz:
>
>   ```azurecli-interactive
>   az iot edge deployment create -d {deployment_name} -n {hub_name} --content modules_content.json --layered true
>   ```

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Sonraki adımlar

* [IoT Edge Için Azure DevOps Starter](how-to-devops-starter.md) 'Da DevOps en iyi yöntemler örneği IoT Edge
* IoT Edge dağıtımını, [tek cihazlarda veya ölçekteki IoT Edge dağıtımlarını anlayın](module-deployment-monitoring.md)
* Dağıtım [ve izleme IoT Edge modüllerindeki](how-to-deploy-at-scale.md)bir dağıtımı oluşturma, güncelleştirme veya silme adımlarını gözden geçir.