---
title: Azure Kubernetes için dağıtım merkezi
description: Azure DevOps 'daki dağıtım merkezi, uygulamanız için sağlam bir Azure DevOps işlem hattı ayarlamayı basitleştirir
ms.author: puagarw
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
monikerRange: vsts
ms.openlocfilehash: 5384180720d391c6b4ae830f9316a70e80003063
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972970"
---
# <a name="deployment-center-for-azure-kubernetes"></a>Azure Kubernetes için dağıtım merkezi

Azure DevOps 'daki dağıtım merkezi, uygulamanız için güçlü bir Azure DevOps işlem hattı ayarlamayı basitleştirir. Dağıtım Merkezi, varsayılan olarak, uygulama güncelleştirmelerinizi Kubernetes kümesine dağıtmak için bir Azure DevOps işlem hattı yapılandırır. Varsayılan yapılandırılmış Azure DevOps işlem hattını genişletebilir ve ayrıca daha zengin yetenekler ekleyebilirsiniz: dağıtımdan önce onay kazanma, ek Azure kaynakları sağlama, betikleri çalıştırma, uygulamanızı yükseltme, hatta daha fazla doğrulama testi çalıştırma olanağı.

Bu öğreticide şunları yapmanız gerekir:

> [!div class="checklist"]
> * Uygulama güncelleştirmelerinizi Kubernetes kümesine dağıtmak için bir Azure DevOps işlem hattı yapılandırın.
> * Sürekli tümleştirme (CI) işlem hattını inceleyin.
> * Sürekli teslim (CD) ardışık düzenini inceleyin.
> * Kaynakları temizleyin.

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure aboneliği. [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)aracılığıyla bir ücretsiz edinebilirsiniz.

* Bir Azure Kubernetes hizmeti (AKS) kümesi.

## <a name="create-an-aks-cluster"></a>AKS kümesi oluşturma

1. [Azure Portal](https://portal.azure.com/)oturum açın.

1. Azure portal menü çubuğunun sağ tarafındaki [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) seçeneğini belirleyin.

1. AKS kümesini oluşturmak için aşağıdaki komutları çalıştırın:

    ```cmd
    # Create a resource group in the South India location:

    az group create --name azooaks --location southindia

    # Create a cluster named azookubectl with one node.

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-a-kubernetes-cluster"></a>Uygulama güncelleştirmelerini bir Kubernetes kümesine dağıtma

1. Önceki bölümde oluşturduğunuz kaynak grubuna gidin.

1. AKS kümesini seçin ve ardından sol dikey pencerede **Dağıtım Merkezi (Önizleme)** öğesini seçin. **Kullanmaya**başlayın ' ı seçin.

   ![Ayarlar](media/deployment-center-launcher/settings.png)

1. Kodun konumunu seçin ve **İleri ' yi**seçin. Daha sonra, şu anda desteklenen depolardan birini seçin: **[Azure Repos](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)** veya **GitHub**.

    Azure Repos, kodunuzu yönetmenize yardımcı olan bir sürüm denetimi araçları kümesidir. Yazılım projenizin büyük veya küçük olup olmadığı, sürüm denetiminin olabildiğince erken kullanılması iyi bir fikirdir.

    - **Azure Repos**: mevcut projenizden ve kuruluşunuzdan bir depoyu seçin.

        ![Azure Repos](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**: Github hesabınız için depoyu yetkilendirme ve seçme.

        ![GitHub](media/deployment-center-launcher/github.gif)


1. Dağıtım merkezi depoyu analiz eder ve Dockerfile dosyanızı algılar. Dockerfile dosyasını güncelleştirmek istiyorsanız, tanımlanan bağlantı noktası numarasını düzenleyebilirsiniz.

    ![Uygulama ayarları](media/deployment-center-launcher/application-settings.png)

    Depo Dockerfile içermiyorsa, sistem bir tane kaydetmek için bir ileti görüntüler.

    ![Dockerfile](media/deployment-center-launcher/dockerfile.png)

1. Mevcut bir kapsayıcı kayıt defteri seçin ya da bir tane oluşturun ve ardından **son**' u seçin. İşlem hattı otomatik olarak oluşturulur ve [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=azure-devops)bir derlemeyi kuyruğa alır.

    Azure Pipelines, kod projenizi otomatik olarak derlemek ve test etmek ve diğer kullanıcıların kullanımına sunmak için kullanabileceğiniz bir bulut hizmetidir. Azure Pipelines sürekli tümleştirme ve sürekli teslimi sürekli ve tutarlı bir şekilde bir araya getirerek kodunuzu oluşturup her hedefe sunamaz.

    ![Container Registry](media/deployment-center-launcher/container-registry.png)

1. Devam eden ardışık düzeni görmek için bağlantıyı seçin.

1. Dağıtım tamamlandıktan sonra başarılı günlükleri görürsünüz.

    ![Logs](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>CI işlem hattını İnceleme

Dağıtım Merkezi, Azure DevOps kuruluşunuzun CI/CD ardışık düzenini otomatik olarak yapılandırır. İşlem hattı araştırılabilir ve özelleştirilebilir.

1. Dağıtım Merkezi panosuna gidin.  

1. Projenizin derleme ardışık düzenini görüntülemek için başarılı Günlükler listesinden yapı numarasını seçin.

1. Sağ üst köşedeki üç nokta (...) simgesini seçin. Bir menü, yeni bir derlemeyi sıraya alma, bir derlemeyi tutma ve derleme işlem hattını düzenlemeyle çeşitli seçenekleri gösterir. İşlem hattını **Düzenle**' yi seçin. 

1. Yapı ardışık düzeni için farklı görevleri bu bölmeden inceleyebilirsiniz. Derleme, git deposundan kaynakları toplama, görüntü oluşturma, bir görüntüyü kapsayıcı kayıt defterine gönderme ve dağıtımlar için kullanılan çıkışları yayımlama gibi çeşitli görevleri gerçekleştirir.

1. İşlem hattının üst kısmında derleme işlem hattının adını seçin.

1. Derleme işlem hattı adınızı daha açıklayıcı bir değere değiştirin, **& kuyruğu kaydet**' i seçin ve ardından **Kaydet**' i seçin.

1. Yapı ardışık düzeni altında **Geçmiş**' i seçin. Bu bölmede, son yapı değişikliklerinizin bir denetim izi gösterilmektedir. Azure DevOps, derleme ardışık düzeninde yapılan tüm değişiklikleri izler ve sürümleri karşılaştırmanızı sağlar.

1. **Tetikleyiciler**' i seçin. CI işleminden dalları dahil edebilir veya dışlayabilirsiniz.

1. **Bekletme**' yi seçin. Senaryonuza bağlı olarak bir dizi derlemeyi tutan veya kaldırabilmeniz için ilkeler belirtebilirsiniz.

## <a name="examine-the-cd-pipeline"></a>CD işlem hattını inceleyin

Dağıtım Merkezi, Azure DevOps kuruluşunuz ile Azure aboneliğiniz arasındaki ilişkiyi otomatik olarak oluşturur ve yapılandırır. Söz konusu adımlarda, Azure DevOps ile Azure aboneliğiniz için kimlik doğrulaması yapmak üzere bir Azure hizmet bağlantısı kurulması dahildir. Otomatikleştirilmiş süreç ayrıca Azure 'a sürekli teslim sağlayan bir yayın işlem hattı oluşturur.

1. İşlem **hatları**' nı seçin ve ardından **yayınlar**' ı seçin.

1. Yayın ardışık düzenini düzenlemek için **Düzenle**' yi seçin.

1. **Yapıtlar** listesinden **bırak** ' ı seçin. Önceki adımlarda, inceettiğiniz oluşturma işlem hattı, yapıt için kullanılan çıktıyı üretir. 

1. **Bırakma** seçeneğinin sağ tarafındaki **sürekli dağıtım** tetikleyicisini seçin. Bu sürüm ardışık düzeninde, her yeni derleme yapıtı kullanılabilir olduğunda bir dağıtımı çalıştıran etkinleştirilmiş bir CD tetikleyicisi bulunur. Ayrıca, dağıtımlarınız için el ile yürütme gerektirmek üzere tetikleyiciyi devre dışı bırakabilirsiniz.

1. İşlem hattınızla ilgili tüm görevleri incelemek için **Görevler**' i seçin. Yayın, Tiller ortamını ayarlar, `imagePullSecrets` parametresini yapılandırır, helk araçlarını kurar ve Held grafiklerini Kubernetes kümesine dağıtır.

1. Yayın geçmişini görüntülemek için **sürümleri görüntüle**' yi seçin.

1. Özeti görmek için **yayın**' ı seçin. Yayın Özeti, ilişkili iş öğeleri ve testler gibi birden çok menü araştırmak için aşamaların herhangi birini seçin. 

1. **İşlemeler**' i seçin. Bu görünüm, bu dağıtımla ilgili kod yürütmelerini gösterir. Dağıtımlar arasındaki tamamlama farklarını görmek için yayınları karşılaştırın.

1. **Günlükleri**seçin. Günlükler, dağıtımları sırasında ve sonrasında görüntüleyebileceğiniz yararlı dağıtım bilgilerini içerir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık ihtiyaç duymadığınızda oluşturduğunuz ilgili kaynakları silebilirsiniz. DevOps Projeleri panosundaki silme işlevini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Bu derleme ve yayın işlem hatlarını takımınızın ihtiyaçlarını karşılayacak şekilde değiştirebilirsiniz. Ya da bu CI/CD modelini diğer işlem hatlarınız için şablon olarak kullanabilirsiniz.
