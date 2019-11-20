---
title: Sürekli tümleştirme ve sürekli dağıtım-Azure IoT Edge | Microsoft Docs
description: Azure DevOps ile sürekli tümleştirme ve sürekli dağıtım Azure IoT Edge ayarlayın Azure Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 1dc4065540256c8af0856b0e156053f7c2097c1f
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755841"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Azure IoT Edge için sürekli tümleştirme ve sürekli dağıtım

Azure Pipelines içindeki yerleşik Azure IoT Edge görevlerle DevOps 'u Azure IoT Edge uygulamalarınızla kolayca benimseyebilirsiniz. Bu makalede, Azure IoT Edge uygulamalarını hızla ve verimli bir şekilde oluşturmak, test etmek ve dağıtmak için Azure Pipelines sürekli tümleştirme ve sürekli dağıtım özelliklerini nasıl kullanabileceğiniz gösterilmektedir. 

![Geliştirme ve üretim için diyagram-CI ve CD dalları](./media/how-to-ci-cd/cd.png)

Bu makalede, IoT Edge çözümünüz için iki işlem hattı oluşturmak üzere Azure Pipelines için yerleşik Azure IoT Edge görevlerinin nasıl kullanılacağını öğrenirsiniz. Azure IoT Edge görevlerinde dört eylem kullanılabilir.
   - **Azure IoT Edge derleme modülü görüntüleri** , IoT Edge çözüm kodunuzu alır ve kapsayıcı görüntülerini oluşturur.
   - **Azure IoT Edge-gönderim modülü görüntüleri** , modül görüntülerini belirttiğiniz kapsayıcı kayıt defterine gönderir.
   - **Azure IoT Edge-dağıtım bildirimi oluşturma** bir Deployment. Template. json dosyasını ve değişkenlerini alır, ardından son IoT Edge dağıtım bildirim dosyasını oluşturur.
   - **Azure IoT Edge-IoT Edge cihazlara dağıtım** , tek/birden çok IoT Edge cihazlarına IoT Edge dağıtımları oluşturulmasına yardımcı olur.

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure Repos deposu. Bir tane yoksa, [projenizde yeni bir git deposu oluşturabilirsiniz](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav).
* IoT Edge bir çözüm kaydedilir ve deponuza gönderilir. Bu makaleyi test etmek için yeni bir örnek çözüm oluşturmak istiyorsanız, [Visual Studio Code 'de modül geliştirme ve hata ayıklama](how-to-vs-code-develop-module.md) veya [Visual Studio 'da modülleri geliştirme ve hata C# ](how-to-visual-studio-develop-csharp-module.md)ayıklama işlemleri bölümündeki adımları uygulayın.
   * Bu makalede, her türlü Visual Studio Code veya Visual Studio 'da IoT Edge şablonları tarafından oluşturulan çözüm klasörüdür. Devam etmeden önce bu kodu oluşturmanız, göndermeniz, dağıtmanız veya hata ayıklamanıza gerek yoktur. Bu süreçlerini Azure Pipelines göre ayarlarsınız. 
   * Yeni bir çözüm oluşturuyorsanız, deponuzu önce yerel olarak kopyalayın. Ardından, çözümü oluşturduğunuzda doğrudan depo klasöründe oluşturmayı seçebilirsiniz. Yeni dosyaları oradan kolayca kaydedebilir ve gönderebilirsiniz. 
* Modül görüntülerini ititebileceğiniz bir kapsayıcı kayıt defteri. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) veya bir üçüncü taraf kayıt defteri kullanabilirsiniz. 
* Ayrı test ve üretim dağıtım aşamalarını test etmek için en az IoT Edge cihazları olan etkin bir [IoT Hub 'ı](../iot-hub/iot-hub-create-through-portal.md) . [Linux](quickstart-linux.md) veya [Windows](quickstart.md) üzerinde IoT Edge bir cihaz oluşturmak için hızlı başlangıç makalelerini takip edebilirsiniz


Azure Repos kullanma hakkında daha fazla bilgi için bkz. [Visual Studio ile kodunuzu paylaşma ve Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)

## <a name="configure-continuous-integration"></a>Sürekli tümleştirmeyi yapılandırma
Bu bölümde, yeni bir yapı işlem hattı oluşturacaksınız. Örnek IoT Edge çözümünde yapılan değişiklikleri iade ettiğinizde ve Derleme günlüklerini yayımladığınızda, işlem hattını otomatik olarak çalışacak şekilde yapılandırın.

>[!NOTE]
>Bu makale, Azure DevOps görsel tasarımcısını kullanır. Bu bölümdeki adımları izlemeden önce, yeni YAML ardışık düzen oluşturma deneyiminin önizleme özelliğini devre dışı bırakın. 
>1. Azure DevOps 'da profil simgenizi seçip **Önizleme özellikleri**' ni seçin.
>2. **Yenı YAML ardışık düzen oluşturma deneyimini** devre dışı bırakın. 
>
>Daha fazla bilgi için bkz. [derleme işlem hattı oluşturma](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline).

1. Azure DevOps kuruluşunuzda oturum açın (**https: \//dev.Azure.com/{Your Organization}/** ) ve IoT Edge çözüm deponuzu içeren projeyi açın.

   Bu makalede, **ıotedgerepo**adlı bir depo oluşturduk. Bu depo, **filtermodule**adlı bir modülün koduna sahip **IoTEdgeSolution** içerir. 

   ![DevOps projenizi açın](./media/how-to-ci-cd/init-project.png)

2. Projenizdeki Azure Pipelines gidin. **Derlemeler** sekmesini açın ve yeni işlem **hattı**' nı seçin. Ya da zaten derleme işlem hatları varsa, **Yeni** düğmesini seçin. Ardından **Yeni derleme işlem hattı**' nı seçin.

    ![Yeni derleme işlem hattı oluşturma](./media/how-to-ci-cd/add-new-build.png)

3. İşlem hattınızı oluşturmak için istemleri izleyin. 

   1. Yeni derleme işlem hattının kaynak bilgilerini sağlayın. Kaynak olarak **Git Azure Repos** seçin, sonra IoT Edge çözüm kodunuzun bulunduğu projeyi, depoyu ve dalı seçin. Sonra **devam**' ı seçin. 

      ![İşlem hattı kaynağınızı seçin](./media/how-to-ci-cd/pipeline-source.png)

   2. Şablon yerine **boş işi** seçin. 

      ![Boş bir işlemle başlayın](./media/how-to-ci-cd/start-with-empty.png)

4. Ardışık düzen oluşturulduktan sonra işlem hattı düzenleyicisine yönlendirilirsiniz. İşlem hattı tanımınızda, hedef platformunuza bağlı olarak doğru aracı havuzunu seçin: 
    
   * Modüllerinizi Linux kapsayıcıları için platform AMD64 'de derlemek istiyorsanız **barındırılan Ubuntu 1604** ' ı seçin.

   * Modüllerinizi Windows 1809 kapsayıcıları için platform AMD64 'de derlemek isterseniz, [Windows üzerinde şirket içinde barındırılan aracıyı ayarlamanız](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts)gerekir.

   * Modüllerinizi Linux kapsayıcıları için platform arm32v7 veya arm64 ' de derlemek isterseniz, [Linux üzerinde şirket içinde barındırılan aracıyı ayarlamanız](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/)gerekir.
    
     ![Yapı Aracısı havuzunu yapılandırma](./media/how-to-ci-cd/configure-env.png)

5. İşlem hattınız, **Aracı işi 1**adlı bir iş ile önceden yapılandırılmış olarak gelir. İşe üç görev eklemek için artı işaretini ( **+** ) seçin: iki kez **Azure IoT Edge** , dosyaları bir kez **kopyalayın** ve **derleme yapılarını** bir kez yayımlayın. ( **Ekle** düğmesini görmek için her görevin adının üzerine gelin.)

   ![Azure IoT Edge görev ekle](./media/how-to-ci-cd/add-iot-edge-task.png)

   Dört görev eklendiğinde, aracı işiniz aşağıdaki örneğe benzer şekilde görünür:
    
   ![Derleme ardışık düzeninde üç görev](./media/how-to-ci-cd/add-tasks.png)

6. Düzenlemek için ilk **Azure IoT Edge** görevi seçin. Bu görev, Çözümdeki tüm modülleri belirttiğiniz hedef platforma göre oluşturur.

   * **Görünen ad**: varsayılan **Azure IoT Edge derleme modülü görüntülerini**kabul edin.
   * **Eylem**: varsayılan **derleme modülü görüntülerini**kabul edin. 
   * **. Template. JSON dosyası**: üç noktayı ( **...** ) seçin ve IoT Edge çözümünüzü içeren depodaki **Deployment. Template. JSON** dosyasına gidin. 
   * **Varsayılan platform**: hedef IoT Edge cihazınıza göre modülleriniz için uygun platformu seçin. 
   * **Çıkış değişkenleri**: çıkış değişkenleri, Deployment. JSON dosyanızın üretilebileceği dosya yolunu yapılandırmak için kullanabileceğiniz bir başvuru adı içerir. Başvuru adını **kenar**benzeri bir şeye ayarlayın. 

7. Düzenlemek için ikinci **Azure IoT Edge** görevi seçin. Bu görev, tüm modül görüntülerini seçtiğiniz kapsayıcı kayıt defterine iter.

   * **Görünen ad**: eylem alanı değiştiğinde görünen ad otomatik olarak güncelleştirilir. 
   * **Eylem**: **anında iletme modülü görüntülerini**seçmek için açılan listeyi kullanın. 
   * **Kapsayıcı kayıt defteri türü**: modül görüntülerinizi depolamak için kullandığınız kapsayıcı kayıt defteri türünü seçin. Seçtiğiniz kayıt defteri türüne bağlı olarak, form değişir. **Azure Container Registry**öğesini seçerseniz, Azure aboneliğini ve kapsayıcı kayıt defterinizin adını seçmek için açılan listeleri kullanın. **Genel Container Registry**seçerseniz, **Yeni** ' yi seçerek bir kayıt defteri hizmet bağlantısı oluşturun. 
   * **. Template. JSON dosyası**: üç noktayı ( **...** ) seçin ve IoT Edge çözümünüzü içeren depodaki **Deployment. Template. JSON** dosyasına gidin. 
   * **Varsayılan platform**: oluşturulan modül görüntüleriniz ile aynı platformu seçin.

   Modül görüntülerinizi barındırmak için birden çok kapsayıcı kayıt defterine sahipseniz, bu görevi çoğaltmanıza, farklı kapsayıcı kayıt defteri ' ne ve Gelişmiş ayarlar ' da **atlama modülünü** kullanarak bu belirli kayıt defteri için olmayan görüntüleri atlamanıza gerek duyarsınız.

8. Düzenlemek için **dosyaları Kopyala** görevini seçin. Dosyaları yapıt hazırlama dizinine kopyalamak için bu görevi kullanın.

   * **Görünen ad**: dosyaları kopyala: bırakma klasörü.
   * **İçerikler**: Bu bölüme iki satır koyun `deployment.template.json` ve `**/module.json`. Bu iki dosya türü IoT Edge dağıtım bildirimi oluşturmak için girişlerdir. Yapıt hazırlama klasörüne kopyalanması ve yayın işlem hattı için yayımlanmaları gerekir.
   * **Hedef klasör**: değişkeni `$(Build.ArtifactStagingDirectory)` koyun. Açıklama hakkında bilgi edinmek için bkz. [derleme değişkenleri](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) .

9. Düzenlemek için **derleme yapıtları Yayımla** görevini seçin. Yolun serbest bırakma işlem hattına yayınlanabilmesi için göreve yapıt hazırlama dizini yolu sağlayın.
   
   * **Görünen ad**: yayımlama yapıtı: bırak.
   * **Yayımlanacak yol**: değişkeni `$(Build.ArtifactStagingDirectory)` koyun. Açıklama hakkında bilgi edinmek için bkz. [derleme değişkenleri](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) .
   * **Yapıt adı**: Drop.
   * **Yapıt yayımlama konumu**: Azure Pipelines.


10. **Tetikleyiciler** sekmesini açın ve **sürekli tümleştirmeyi etkinleştirmek**için kutuyu işaretleyin. Kodunuzun bulunduğu dalın eklendiğinden emin olun.

    ![Sürekli tümleştirme tetikleyicisini aç](./media/how-to-ci-cd/configure-trigger.png)

11. Yeni derleme işlem hattını **Kaydet** düğmesi ile kaydedin.

Bu işlem hattı, artık yeni kodu depoya gönderdiğinizde otomatik olarak çalışacak şekilde yapılandırılmıştır. İşlem hattı yapıtlarını yayımlayan son görev, bir yayın işlem hattını tetikler. Yayın işlem hattını derlemek için sonraki bölüme geçin. 

## <a name="configure-continuous-deployment"></a>Sürekli dağıtımı yapılandırma
Bu bölümde, derleme işlem hatlarınız yapıtlar olduğunda otomatik olarak çalışacak şekilde yapılandırılmış bir yayın işlem hattı oluşturun ve Azure Pipelines dağıtım günlüklerini gösterir.

Yeni bir işlem hattı oluşturun ve yeni bir aşama ekleyin 

1. **Yayınlar** sekmesinde **+ Yeni işlem hattı**' nı seçin. Ya da yayın işlem hatlarınız zaten varsa **+ Yeni** düğmesini seçin ve **+ Yeni yayın işlem hattı**' nı seçin.  

    ![Yayın işlem hattı ekleme](./media/how-to-ci-cd/add-release-pipeline.png)

2. Bir şablon seçmek isteyip istemediğiniz sorulduğunda **boş bir işle**başlatmayı seçin.

    ![Boş bir iş ile başlayın](./media/how-to-ci-cd/start-with-empty-job.png)

3. Yeni yayın ardışık düzeni, **1. aşama**adlı bir aşama ile başlatılır. 1\. aşamayı **dev** olarak yeniden adlandırın ve test ortamı olarak değerlendirin. Genellikle, sürekli dağıtım işlem hatları **dev**, **hazırlık** ve **Üretim**dahil olmak üzere birden çok aşamaya sahiptir. DevOps uygulamanıza göre daha fazla bilgi oluşturabilirsiniz. Yeniden adlandırıldıktan sonra aşama ayrıntıları penceresini kapatın. 

4. Yayını derleme işlem hattı tarafından yayınlanan yapı yapıtlarına bağlayın. Yapıt alanında **Ekle** ' ye tıklayın.

   ![Yapıt Ekle](./media/how-to-ci-cd/add-artifacts.png)  
    
5. **Yapıt Ekle sayfasında**, kaynak türü **Oluştur**' u seçin. Ardından, oluşturduğunuz projeyi ve derleme işlem hattını seçin. Ardından **Ekle**'yi seçin.

   ![Derleme yapıtı ekleme](./media/how-to-ci-cd/add-an-artifact.png)

6. Yapıt tetikleyicilerini açın ve sürekli dağıtım tetikleyicisini etkinleştirmek için geçiş seçeneğini belirleyin. Şimdi yeni bir derleme kullanılabilir olduğunda yeni bir yayın oluşturulacaktır.

   ![Sürekli dağıtım tetikleyicisini yapılandırma](./media/how-to-ci-cd/add-a-trigger.png)

7. **Geliştirme** aşaması bir iş ve sıfır görev ile önceden yapılandırılmıştır. İşlem hattı menüsünde **Görevler** ' i ve ardından **geliştirme** aşamasını seçin.  Bu aşamadaki görevleri yapılandırmak için işi ve görev sayısını seçin.

    ![Geliştirme görevlerini yapılandırma](./media/how-to-ci-cd/view-stage-tasks.png)

8. **Geliştirme** aşamasında, varsayılan bir **Aracı işi**görmeniz gerekir. Aracı işiyle ilgili ayrıntıları yapılandırabilirsiniz, ancak dağıtım görevi platformun duyarsız olduğundan, **Aracı havuzunda** (veya kendi tarafından yönetilen diğer ARACıLARDAN **barındırılan VS2017** veya **barındırılan Ubuntu 1604** ) kullanabilirsiniz. 

9. İki görev eklemek için artı işaretini ( **+** ) seçin. **Azure IoT Edge** iki kez arayın ve ekleyin.

    ![Geliştirme için görev ekleme](./media/how-to-ci-cd/add-task-qa.png)

10. İlk **Azure IoT Edge** görevini seçin ve aşağıdaki değerlerle yapılandırın:

    * **Görünen ad**: eylem alanı değiştiğinde görünen ad otomatik olarak güncelleştirilir. 
    * **Eylem**: **dağıtım bildirimini oluştur**' u seçmek için açılan listeyi kullanın. Eylem değerini değiştirmek için görev görünen adının eşleşmesi de güncelleştirilir.
    * **. Template. JSON dosyası**: yolu `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json` yerleştirin. Yol, derleme ardışık düzeninde yayımlanır.
    * **Varsayılan platform**: modül görüntülerini oluştururken aynı değeri seçin.
    * **Çıkış yolu**: yolu `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` koyun. Bu yol, son IoT Edge dağıtım bildirimi dosyasıdır.

    Bu yapılandırma `deployment.template.json` dosyasındaki modül görüntüsü URL 'Lerinin değiştirilmesini sağlar. **Dağıtım oluşturma bildirimi** ayrıca değişkenlerin `deployment.template.json` dosyasında tanımladığınız tam değerle değiştirilmesini de sağlar. VS/VS Code 'de, gerçek değeri bir `.env` dosyasında belirtirsiniz. Azure Pipelines, serbest bırakma işlem hattı değişkenleri sekmesinde değeri ayarlarsınız. değişkenler sekmesine geçin ve adı ve değeri aşağıdaki şekilde yapılandırın.

    * **ACR_ADDRESS**: Azure Container Registry adresiniz. 
    * **ACR_PASSWORD**: Azure Container Registry parolanız.
    * **ACR_USER**: Azure Container Registry Kullanıcı adı.

    Projenizde başka değişkenlere sahipseniz, bu sekmede adı ve değeri belirtebilirsiniz. **Oluşturma dağıtım bildirimi** yalnızca değişkenleri `${VARIABLE}` Flavor olarak algılayabilir, bunu `*.template.json` dosyalarınızda kullandığınızdan emin olun.

    ![Yayın işlem hattı için değişkenleri yapılandırma](./media/how-to-ci-cd/configure-variables.png)

10. İkinci **Azure IoT Edge** görevi seçin ve aşağıdaki değerlerle yapılandırın:

    * **Görünen ad**: eylem alanı değiştiğinde görünen ad otomatik olarak güncelleştirilir. 
    * **Eylem**: **IoT Edge cihazlara dağıt**' ı seçmek için açılan listeyi kullanın. Eylem değerini değiştirmek için görev görünen adının eşleşmesi de güncelleştirilir.
    * **Azure aboneliği**: IoT Hub içeren aboneliği seçin.
    * **IoT Hub adı**: IoT Hub 'ınızı seçin. 
    * **Tek/birden çok cihaz seçin**: yayın işlem hattının bir cihaza veya birden çok cihaza dağıtılmasını isteyip istemediğinizi seçin. 
      * Tek bir cihaza dağıtıyorsanız **IoT Edge CIHAZ kimliğini**girin. 
      * Birden çok cihaza dağıtıyorsanız, cihaz **hedefi koşulunu**belirtin. Hedef koşul, IoT Hub bir IoT Edge cihazları kümesiyle eşleşecek bir filtredir. Cihaz etiketlerini koşul olarak kullanmak istiyorsanız, ilgili cihaz etiketlerinizi IoT Hub Device ikizi ile güncelleştirmeniz gerekir. Gelişmiş ayarlarda **IoT Edge DAĞıTıM kimliğini** ve **Dağıtım önceliğini IoT Edge** güncelleştirin. Birden çok cihaz için dağıtım oluşturma hakkında daha fazla bilgi için bkz. [IoT Edge otomatik dağıtımları anlama](module-deployment-monitoring.md).
    * Gelişmiş ayarlar ' ı genişletin, **IoT Edge DAĞıTıM kimliği**' ni seçin, değişkeni `$(System.TeamProject)-$(Release.EnvironmentName)` koyun. Bu, proje ve sürüm adını IoT Edge dağıtım KIMLIĞINIZLE eşler.

11. Yeni sürüm ardışık düzeninde yaptığınız değişiklikleri kaydetmek için **Kaydet** ' i seçin. Menüden işlem **hattı** ' nı seçerek işlem hattı görünümüne dönün. 
    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Derleme ve yayın işlem hatları ile IoT Edge CI/CD 'yi doğrulama

Bir yapı işini tetiklemek için, kaynak kodu deposuna bir kayıt gönderebilir veya el ile tetikleyebilirsiniz. Bu bölümde, onun çalıştığını test etmek için CI/CD işlem hattını el ile tetiklersiniz. Sonra dağıtımın başarılı olduğunu doğrulayın.

1. Bu makalenin başlangıcında oluşturduğunuz derleme ardışık düzenine gidin. 

2. Aşağıdaki ekran görüntüsünde olduğu gibi, **kuyruk** düğmesini seçerek yapı işlem hattınızda derleme işini tetikleyebilirsiniz.

    ![El ile tetikleyici](./media/how-to-ci-cd/manual-trigger.png)

3. İlerleme durumunu izlemek için derleme işini seçin. Derleme işlem hattı başarıyla tamamlanırsa, **geliştirme** aşamasına bir yayın tetikler. 

    ![Derleme günlükleri](./media/how-to-ci-cd/build-logs.png)

4. Başarılı **geliştirme** sürümü, IoT Edge cihazlarını hedeflemek için IoT Edge dağıtımı oluşturur.

    ![Geliştirme sürümü](./media/how-to-ci-cd/pending-approval.png)

5. Yayın günlüklerini görmek için **geliştirme** aşaması ' na tıklayın.

    ![Yayın günlükleri](./media/how-to-ci-cd/release-logs.png)



## <a name="next-steps"></a>Sonraki adımlar
* [IoT Edge Için Azure DevOps projesinde](how-to-devops-project.md) DevOps en iyi uygulamaları örneği IoT Edge
* IoT Edge dağıtımını, [tek cihazlarda veya ölçekteki IoT Edge dağıtımlarını anlayın](module-deployment-monitoring.md)
* Dağıtım [ve izleme IoT Edge modüllerindeki](how-to-deploy-monitor.md)bir dağıtımı oluşturma, güncelleştirme veya silme adımlarını gözden geçir.
