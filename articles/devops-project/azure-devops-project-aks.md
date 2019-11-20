---
title: 'Öğretici: Azure DevOps Projeleri ile Azure Kubernetes hizmetine ASP.NET Core uygulamaları dağıtın'
description: Azure DevOps Projeleri Azure 'u kullanmaya başlamanızı kolaylaştırır. DevOps Projeleri, ASP.NET Core uygulamanızı birkaç hızlı adımda Azure Kubernetes hizmeti (AKS) ile dağıtabilirsiniz.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: b27d56d78296dc5500f97802f811a8923c4e87a8
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71969681"
---
# <a name="tutorial-deploy-aspnet-core-apps-to-azure-kubernetes-service-with-azure-devops-projects"></a>Öğretici: Azure DevOps Projeleri ile Azure Kubernetes hizmetine ASP.NET Core uygulamaları dağıtın

Azure DevOps Projeleri, mevcut kodunuzu ve git deponuzu getirebileceğiniz veya Azure 'a sürekli tümleştirme (CI) ve sürekli teslim (CD) işlem hattı oluşturmak için örnek bir uygulama seçebileceğiniz basitleştirilmiş bir deneyim sunar. 

Ayrıca DevOps Projeleri:
* Azure Kubernetes hizmeti (AKS) gibi Azure kaynaklarını otomatik olarak oluşturur.
* CI/CD için derleme ve yayın işlem hattı ayarlayan Azure DevOps 'da bir yayın işlem hattı oluşturur ve yapılandırır.
* İzleme için bir Azure Application Insights kaynağı oluşturur.
* AKS kümesindeki kapsayıcı iş yüklerinin performansını izlemek için [kapsayıcılar Için Azure izleyicisini](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) sağlar

Bu öğreticide şunları yapacaksınız:

> [!div class="checklist"]
> * Bir ASP.NET Core uygulamasını AKS 'e dağıtmak için DevOps Projeleri kullanma
> * Azure DevOps ve bir Azure aboneliği yapılandırma 
> * AKS kümesini inceleme
> * CI işlem hattını inceleme
> * CD işlem hattını inceleme
> * Git 'e değişiklikleri işleyin ve Azure 'a otomatik olarak dağıtın
> * Kaynakları temizleme

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) aracılığıyla ücretsiz bir abonelik alabilirsiniz.

## <a name="use-devops-projects-to-deploy-an-aspnet-core-app-to-aks"></a>Bir ASP.NET Core uygulamasını AKS 'e dağıtmak için DevOps Projeleri kullanma

DevOps Projeleri, Azure Pipelines bir CI/CD işlem hattı oluşturur. Yeni bir Azure DevOps kuruluşu oluşturabilir veya var olan bir kuruluşu kullanabilirsiniz. DevOps Projeleri ayrıca, tercih ettiğiniz Azure aboneliğindeki AKS kümesi gibi Azure kaynaklarını da oluşturur.

1. [Azure Portal](https://portal.azure.com)’ında oturum açın.

1. Sol bölmede **kaynak oluştur**' u seçin.

1. Arama kutusuna **DevOps Projeleri**yazın ve ardından **Oluştur**' u seçin.

    ![DevOps Projeleri panosu](_img/azure-devops-project-github/fullbrowser.png)

1. **.Net**' i seçin ve ardından **İleri**' yi seçin.

1. **Uygulama çerçevesi seçin**altında **ASP.NET Core**' yi seçin.

1. **Kubernetes hizmeti**' ni seçin ve ardından **İleri**' yi seçin. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps ve bir Azure aboneliği yapılandırma

1. Yeni bir Azure DevOps organizasyonu oluşturun veya var olan bir kuruluşu seçin. 

1. Azure DevOps projeniz için bir ad girin. 

1. Azure aboneliğinizi seçin.

1. Ek Azure yapılandırma ayarlarını görüntülemek ve AKS kümesi için düğüm sayısını belirlemek üzere **Değiştir**' i seçin.  
    Bu bölmede, Azure hizmetlerinin türünü ve konumunu yapılandırmaya yönelik çeşitli seçenekler görüntülenir.
 
1. Azure yapılandırma alanından çıkıp **bitti**' yi seçin.  
    Birkaç dakika sonra işlem tamamlanır. Azure DevOps kuruluşunuzda bir git deposunda bir örnek ASP.NET Core uygulaması ayarlanır, bir AKS kümesi oluşturulur, bir CI/CD işlem hattı yürütülür ve uygulamanız Azure 'a dağıtılır. 

    Bu işlem tamamlandıktan sonra Azure DevOps proje panosu Azure portal görüntülenir. Ayrıca, artık Azure portal **tüm kaynaklardan** DevOps Projeleri panosuna gidebilirsiniz. 

    Bu Pano, Azure DevOps kod deponuza, CI/CD işlem hattınızla ve AKS kümenize ilişkin görünürlük sağlar. Azure DevOps işlem hattınızda ek CI/CD seçenekleri yapılandırabilirsiniz. Sağ tarafta, çalışan uygulamanızı görüntülemek için **Araştır** ' ı seçin.

## <a name="examine-the-aks-cluster"></a>AKS kümesini inceleme

DevOps Projeleri, keşfedebileceğiniz ve özelleştirebileceğiniz bir AKS kümesini otomatik olarak yapılandırır. AKS kümesi hakkında bilgi edinmek için aşağıdakileri yapın:

1. DevOps Projeleri panosuna gidin.

1. Sağ tarafta AKS hizmetini seçin.  
    AKS kümesi için bir bölme açılır. Bu görünümden kapsayıcı durumunu izleme, günlükleri arama ve Kubernetes panosunu açma gibi çeşitli eylemler gerçekleştirebilirsiniz.

1. Sağ tarafta **Kubernetes panosunu görüntüle**' yi seçin.  
    İsteğe bağlı olarak, Kubernetes panosunu açmak için adımları izleyin.

## <a name="examine-the-ci-pipeline"></a>CI işlem hattını inceleme

DevOps Projeleri, Azure DevOps kuruluşunuzda bir CI/CD işlem hattını otomatik olarak yapılandırır. İşlem hattını inceleyebilir ve özelleştirebilirsiniz. Bunu öğrenmek için aşağıdakileri yapın:

1. DevOps Projeleri panosuna gidin.

1. DevOps Projeleri panosunun en üstünde **derleme Işlem hatları**' nı seçin.  
    Bir tarayıcı sekmesi, yeni projeniz için derleme işlem hattını görüntüler.

1. **Durum** alanını işaret edin ve ardından üç nokta (...) simgesini seçin.  
    Bir menü, yeni bir derlemeyi sıraya alma, bir derlemeyi duraklatma ve derleme işlem hattını düzenlemeyle çeşitli seçenekleri görüntüler.

1. **Düzenle**’yi seçin.

1. Bu bölmede, derleme işlem hattınızla ilgili çeşitli görevleri inceleyebilirsiniz.  
    Derleme git deposundan kaynak getirme, bağımlılıkları geri yükleme ve dağıtımlar için kullanılan yayınlama çıkışları gibi çeşitli görevleri gerçekleştirir.

1. Yapı işlem hattının en üstünde derleme işlem hattı adını seçin.

1. Derleme işlem hattınızı daha açıklayıcı bir şekilde değiştirin, **& kuyruğu kaydet**' i seçin ve ardından **Kaydet**' i seçin.

1. Derleme işlem hattı adınızın altında **Geçmiş**’i seçin.  
    Bu bölme, derleme için son değişikliklerinizin denetim izini görüntüler. Azure DevOps, derleme ardışık düzeninde yapılan tüm değişiklikleri izler ve sürümleri karşılaştırmanızı sağlar.

1. **Tetikleyiciler**’i seçin.  
    DevOps Projeleri otomatik olarak bir CI tetikleyicisi oluşturur ve depoya yapılan her bir işleme yeni bir derleme başlatır. İsteğe bağlı olarak, CI işlemindeki dalları dahil etmek veya hariç tutmak seçebilirsiniz.

1. **Saklama**’yı seçin.  
    Senaryonuza bağlı olarak, belirli sayıda derlemeyi tutmanın veya kaldırabilmeniz için ilkeler belirtebilirsiniz.

## <a name="examine-the-cd-release-pipeline"></a>CD sürümü ardışık düzenini inceleyin

DevOps Projeleri, Azure DevOps kuruluşunuzdan Azure aboneliğinize dağıtmak için gerekli adımları otomatik olarak oluşturur ve yapılandırır. Bu adımlar, Azure aboneliğinizdeki Azure DevOps kimlik doğrulaması için bir Azure hizmet bağlantısı yapılandırmayı içerir. Otomasyon Ayrıca CD 'yi Azure 'a sağlayan bir yayın işlem hattı oluşturur. Yayın işlem hattı hakkında daha fazla bilgi edinmek için aşağıdakileri yapın:

1. **Build ve Release**' i seçin ve ardından **yayınlar**' ı seçin.  
    DevOps Projeleri, Azure dağıtımlarını yönetmek için bir yayın işlem hattı oluşturur.

1. Yayın işlem hattının yanındaki üç nokta (...) simgesini seçin ve ardından **Düzenle**' yi seçin.  
    Yayın işlem hattı, yayın işlemini tanımlayan bir *işlem hattı* içerir.

1. **Yapıtlar**’ın altında **Bırak**’ı seçin.  
    Önceki adımlarda inceettiğiniz derleme işlem hattı, yapıt için kullanılan çıktıyı üretir. 

1. **Bırakma** simgesinin sağ tarafında, **sürekli dağıtım tetikleyicisi**' ni seçin.  
    Bu sürüm ardışık düzeninde, her yeni derleme yapıtı kullanılabilir olduğunda bir dağıtımı yürüten etkinleştirilmiş bir CD tetikleyicisi vardır. İsteğe bağlı olarak, dağıtımlarınızın el ile yürütme gerektirdiğinden tetikleyiciyi devre dışı bırakabilirsiniz. 

1. Sağ tarafta, sürümlerin geçmişini görüntülemek için **yayınları görüntüle** ' yi seçin.

1. Bir yayının yanındaki üç nokta (...) simgesini seçin ve sonra **Aç**' ı seçin.  
    Yayın Özeti, ilişkili iş öğeleri ve testler gibi çeşitli menüleri inceleyebilirsiniz.

1. **İşlemeler**'i seçin.  
    Bu görünüm, bu dağıtımla ilişkili kod işlemelerini gösterir. Dağıtımlar arasındaki işleme farklılıklarını görmek için yayınları karşılaştırın.

1. **Günlükler**’i seçin.  
    Günlüklerde, dağıtım işlemiyle ilgili yararlı bilgiler bulunur. Bunları, dağıtımları sırasında ve sonrasında görüntüleyebilirsiniz.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Azure Repos değişiklikleri işleyin ve Azure 'a otomatik olarak dağıtın 

 > [!NOTE]
 > Aşağıdaki yordam, bir basit metin değişikliği yaparak CI/CD işlem hattını sınar.

Şimdi, en son çalışmalarınızı otomatik olarak Web sitenize dağıtan bir CI/CD işlemi kullanarak uygulamanızdaki bir takımla işbirliği yapmaya hazırsınız. Git deposunda yapılan her değişiklik Azure DevOps 'da bir derleme başlatır ve bir CD işlem hattı Azure 'a bir dağıtım yürütür. Bu bölümdeki yordamı izleyin veya değişiklikleri deponuzda uygulamak için başka bir teknik kullanın. Örneğin, git deposunu en sevdiğiniz araçta veya IDE 'de kopyalayabilir ve sonra değişiklikleri bu depoya gönderebilirsiniz.

1. Azure DevOps menüsünde **kod** > **dosyaları**' nı seçin ve ardından deponuza gidin.

1. *Views\home* dizinine gidin, *Index. cshtml* dosyasının yanındaki üç nokta (...) simgesini seçin ve ardından **Düzenle**' yi seçin.

1. Dosyada, div etiketlerinden birine metin ekleme gibi bir değişiklik yapın. 

1. Sağ üst köşedeki **Kaydet**' i seçin ve sonra değişikliklerinizi göndermek Için yeniden **Yürüt** ' ü seçin.  
    Birkaç dakika sonra, bir derleme Azure DevOps 'da başlar ve değişiklikleri dağıtmak için bir yayın yürütülür. DevOps Projeleri panosunda veya Azure DevOps kuruluşunuzla tarayıcıda derleme durumunu izleyin.

1. Yayın tamamlandıktan sonra değişikliklerinizi doğrulamak için uygulamanızı yenileyin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Test ediyorsanız, kaynaklarınızı temizleyerek faturalandırma ücretlerinden kaçınabilirsiniz. Artık gerekli olmadığında, bu öğreticide oluşturduğunuz AKS kümesini ve ilgili kaynakları silebilirsiniz. Bunu yapmak için DevOps Projeleri panosundaki **silme** işlevini kullanın.

> [!IMPORTANT]
> Aşağıdaki yordam kaynakları kalıcı olarak siler. *Silme* işlevselliği, proje tarafından oluşturulan verileri hem Azure hem de Azure devops içinde DevOps Projeleri yok eder ve bu işlemi geri alamazsınız. Bu yordamı yalnızca istemleri dikkatle okuduktan sonra kullanın.

1. Azure portal, DevOps Projeleri panosuna gidin.
2. Sağ üst köşedeki **Sil**' i seçin. 
3. İstem sırasında, kaynakları *kalıcı olarak silmek* için **Evet** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Ekibinizin gereksinimlerine uygun olarak bu derleme ve yayın işlem hatlarını istediğiniz gibi değiştirebilirsiniz. Ayrıca bu CI/CD desenini diğer işlem hatlarınızda şablon olarak kullanabilirsiniz. Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Bir ASP.NET Core uygulamasını AKS 'e dağıtmak için DevOps Projeleri kullanma
> * Azure DevOps ve bir Azure aboneliği yapılandırma 
> * AKS kümesini inceleme
> * CI işlem hattını inceleme
> * CD işlem hattını inceleme
> * Git 'e değişiklikleri işleyin ve Azure 'a otomatik olarak dağıtın
> * Kaynakları temizleme

Kubernetes panosunu kullanma hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Kubernetes panosunu kullanma](https://docs.microsoft.com/azure/aks/kubernetes-dashboard)
