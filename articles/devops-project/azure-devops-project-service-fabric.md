---
title: 'Öğretici: Azure DevOps Projeleri kullanarak ASP.NET Core uygulamanızı Azure Service Fabric dağıtma'
description: Azure DevOps Projeleri Azure 'u kullanmaya başlamanızı kolaylaştırır. DevOps Projeleri, ASP.NET Core uygulamanızı birkaç hızlı adımda Azure Service Fabric dağıtabilirsiniz.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 5f14164da5cd89cc7d0578e6b64c39d227734d75
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71969469"
---
# <a name="tutorial-deploy-your-aspnet-core-app-to-azure-service-fabric-by-using-azure-devops-projects"></a>Öğretici: Azure DevOps Projeleri kullanarak ASP.NET Core uygulamanızı Azure Service Fabric dağıtma

Azure DevOps Projeleri, mevcut kodunuzu ve git deponuzu getirebileceğiniz veya Azure 'a sürekli tümleştirme (CI) ve sürekli teslim (CD) işlem hattı oluşturmak için örnek bir uygulama seçebileceğiniz basitleştirilmiş bir deneyim sunar. 

Ayrıca DevOps Projeleri:
* Azure Service Fabric gibi Azure kaynaklarını otomatik olarak oluşturur.
* Azure DevOps 'da bir CI/CD işlem hattı ayarlayan bir yayın işlem hattı oluşturur ve yapılandırır.
* İzleme için bir Azure Application Insights kaynağı oluşturur.

Bu öğreticide şunları yapacaksınız:

> [!div class="checklist"]
> * Bir ASP.NET Core uygulaması oluşturmak ve Service Fabric dağıtmak için DevOps Projeleri kullanın
> * Azure DevOps ve bir Azure aboneliği yapılandırma 
> * CI işlem hattını inceleme
> * CD işlem hattını inceleme
> * Git’e değişiklikleri işleme ve Azure’a otomatik olarak dağıtma
> * Kaynakları temizleme

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) aracılığıyla ücretsiz bir abonelik alabilirsiniz.

## <a name="use-devops-projects-to-create-an-aspnet-core-app-and-deploy-it-to-service-fabric"></a>Bir ASP.NET Core uygulaması oluşturmak ve Service Fabric dağıtmak için DevOps Projeleri kullanın

DevOps Projeleri, Azure Pipelines bir CI/CD işlem hattı oluşturur. Yeni bir Azure DevOps kuruluşu oluşturabilir veya var olan bir kuruluşu kullanabilirsiniz. DevOps Projeleri ayrıca, tercih ettiğiniz Azure aboneliğinde bir Service Fabric kümesi gibi Azure kaynakları da oluşturur.

1. [Azure Portal](https://portal.azure.com)’ında oturum açın.

1. Sol bölmede **kaynak oluştur**' u seçin.

1. Arama kutusuna **DevOps Projeleri**yazın ve ardından **Oluştur**' u seçin.

    ![DevOps Projeleri panosu](_img/azure-devops-project-github/fullbrowser.png)

1. **.Net**' i seçin ve ardından **İleri**' yi seçin.

1. **Uygulama çerçevesi seçin**altında **ASP.NET Core**' yi seçin ve ardından **İleri**' yi seçin.

1. **Service Fabric kümesi**' ni seçin ve ardından **İleri**' yi seçin. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps ve bir Azure aboneliği yapılandırma

1. Yeni bir Azure DevOps organizasyonu oluşturun veya var olan bir kuruluşu seçin. 

1. Azure DevOps projeniz için bir ad girin. 

1. Azure aboneliğinizi seçin.

1. Ek Azure yapılandırma ayarlarını görüntülemek ve Service Fabric kümesi için düğüm sanal makine boyutunu ve işletim sistemini tanımlamak üzere **Değiştir**' i seçin.  
    Bu bölmede, Azure hizmetlerinin türünü ve konumunu yapılandırmaya yönelik çeşitli seçenekler görüntülenir.
 
1. Azure yapılandırma alanından çıkıp **bitti**' yi seçin.  
    Birkaç dakika sonra işlem tamamlanır. Azure DevOps kuruluşunuzda bir git deposunda bir örnek ASP.NET Core uygulaması ayarlanır, bir Service Fabric kümesi oluşturulur, bir CI/CD işlem hattı yürütülür ve uygulamanız Azure 'a dağıtılır. 

    Bu tamamlandıktan sonra, DevOps Projeleri panosu Azure portal görüntülenir. Ayrıca, artık Azure portal **tüm kaynaklardan** DevOps Projeleri panosuna gidebilirsiniz. 

    Bu Pano, Azure DevOps kod deponuzu, CI/CD işlem hattınızı ve Service Fabric kümenizi görünürlük sağlar. Azure Repos ' de CI/CD işlem hatlarınız için ek seçenekler yapılandırabilirsiniz. Sağ tarafta, çalışan uygulamanızı görüntülemek için **Araştır** ' ı seçin.

## <a name="examine-the-ci-pipeline"></a>CI işlem hattını inceleme

DevOps Projeleri, Azure Pipelines bir CI/CD işlem hattını otomatik olarak yapılandırır. İşlem hattını inceleyebilir ve özelleştirebilirsiniz. Bunu öğrenmek için aşağıdakileri yapın:

1. DevOps Proje panosuna gidin.

1. DevOps Projeleri panosunun en üstünde **derleme işlem hatları**' nı seçin.  
    Bir tarayıcı sekmesi, yeni projeniz için derleme işlem hattını görüntüler.

1. **Durum** alanını işaret edin ve ardından üç nokta (...) simgesini seçin.  
    Bir menü, yeni bir derlemeyi sıraya alma, bir derlemeyi duraklatma ve derleme işlem hattını düzenlemeyle çeşitli seçenekleri görüntüler.

1. **Düzenle**’yi seçin.

1. Bu bölmede, derleme işlem hattınızla ilgili çeşitli görevleri inceleyebilirsiniz.  
    Derleme git deposundan kaynak getirme, bağımlılıkları geri yükleme ve dağıtımlar için kullanılan yayınlama çıkışları gibi çeşitli görevleri gerçekleştirir.

1. Yapı işlem hattının en üstünde derleme işlem hattı adını seçin. 

1. Derleme işlem hattı adınızın altında **Geçmiş**’i seçin.  
    Bu bölme, derleme için son değişikliklerinizin denetim izini görüntüler. Azure DevOps, derleme ardışık düzeninde yapılan tüm değişiklikleri izler ve sürümleri karşılaştırmanızı sağlar.

1. **Tetikleyiciler**’i seçin.  
    DevOps Projeleri otomatik olarak bir CI tetikleyicisi oluşturur ve depoya yapılan her bir işleme yeni bir derleme başlatır. İsteğe bağlı olarak, CI işlemindeki dalları dahil etmek veya hariç tutmak seçebilirsiniz.

1. **Saklama**’yı seçin.  
    Senaryonuza bağlı olarak, belirli sayıda derlemeyi tutmanın veya kaldırabilmeniz için ilkeler belirtebilirsiniz.

## <a name="examine-the-cd-pipeline"></a>CD işlem hattını inceleme

DevOps Projeleri, Azure DevOps kuruluşunuzdan Azure aboneliğinize dağıtmak için gerekli adımları otomatik olarak oluşturur ve yapılandırır. Bu adımlar, Azure aboneliğinizdeki Azure DevOps kimlik doğrulaması için bir Azure hizmet bağlantısı yapılandırmayı içerir. Otomasyon Ayrıca CD 'yi Azure 'a sağlayan bir yayın işlem hattı oluşturur. Yayın işlem hattı hakkında daha fazla bilgi edinmek için aşağıdakileri yapın:

1. **Build ve Release**' i seçin ve ardından **yayınlar**' ı seçin.  
    DevOps Projeleri, Azure dağıtımlarını yönetmek için bir yayın işlem hattı oluşturur.

1. Yayın işlem hattının yanındaki üç nokta (...) simgesini seçin ve ardından **Düzenle**' yi seçin.  
    Yayın işlem hattı, yayın işlemini tanımlayan bir *işlem hattı* içerir.

1. **Yapıtlar**’ın altında **Bırak**’ı seçin.  
    Daha önce incelenen derleme işlem hattı, yapıt için kullanılan çıktıyı üretir. 

1. **Bırakma** simgesinin sağ tarafında, **sürekli dağıtım tetikleyicisi**' ni seçin.  
    Bu sürüm ardışık düzeninde, her yeni derleme yapıtı kullanılabilir olduğunda bir dağıtımı yürüten etkinleştirilmiş bir CD tetikleyicisi vardır. İsteğe bağlı olarak, dağıtımlarınızın el ile yürütme gerektirdiğinden tetikleyiciyi devre dışı bırakabilirsiniz. 

1. Sağ tarafta, sürümlerin geçmişini görüntülemek için **yayınları görüntüle** ' yi seçin.

1. Bir yayının yanındaki üç nokta (...) simgesini seçin ve sonra **Aç**' ı seçin.  
    Yayın Özeti, ilişkili iş öğeleri ve testler gibi çeşitli menüleri inceleyebilirsiniz.

1. **İşlemeler**'i seçin.  
    Bu görünüm, bu dağıtımla ilişkili kod işlemelerini gösterir. Dağıtımlar arasındaki işleme farklılıklarını görmek için yayınları karşılaştırın.

1. **Günlükler**’i seçin.  
    Günlüklerde, dağıtım işlemiyle ilgili yararlı bilgiler bulunur. Bunları, dağıtımları sırasında ve sonrasında görüntüleyebilirsiniz.

## <a name="commit-changes-to-git-and-automatically-deploy-them-to-azure"></a>Git 'e değişiklikleri işleyin ve Azure 'a otomatik olarak dağıtın 

 > [!NOTE]
 > Aşağıdaki yordam, bir basit metin değişikliği yaparak CI/CD işlem hattını sınar.

Şimdi, en son çalışmalarınızı otomatik olarak Web sitenize dağıtan bir CI/CD işlemi kullanarak uygulamanızdaki bir takımla işbirliği yapmaya hazırsınız. Git deposunda yapılan her değişiklik bir derleme başlatır ve bir sürüm yaptığınız değişiklikleri Azure 'a dağıtır. Bu bölümdeki yordamı izleyin veya değişiklikleri deponuzda uygulamak için başka bir teknik kullanın. Örneğin, git deposunu en sevdiğiniz araçta veya IDE 'de kopyalayabilir ve sonra değişiklikleri bu depoya gönderebilirsiniz.

1. Azure DevOps menüsünde **kod** > **dosyaları**' nı seçin ve ardından deponuza gidin.

1. *Views\home* dizinine gidin, *Index. cshtml* dosyasının yanındaki üç nokta (...) simgesini seçin ve ardından **Düzenle**' yi seçin.

1. Dosyada, div etiketlerinden birine metin ekleme gibi bir değişiklik yapın. 

1. Sağ üst köşedeki **Kaydet**' i seçin ve sonra değişikliklerinizi göndermek Için yeniden **Yürüt** ' ü seçin.  
    Birkaç dakika sonra, bir derleme başlar ve sonra değişiklikleri dağıtmak için bir yayın yürütülür. Yapı durumunu DevOps Projeleri panosunda veya tarayıcıda Azure DevOps gerçek zamanlı günlük kaydı ile izleyebilirsiniz.

1. Yayın tamamlandıktan sonra değişikliklerinizi doğrulamak için uygulamanızı yenileyin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Test ediyorsanız, kaynaklarınızı temizleyerek faturalandırma ücretlerinden kaçınabilirsiniz. Artık gerekli olmadığında Azure Service Fabric kümesini ve bu öğreticide oluşturduğunuz ilgili kaynakları silebilirsiniz. Bunu yapmak için DevOps Projeleri panosundaki **silme** işlevini kullanın.

> [!IMPORTANT]
> Aşağıdaki yordam kaynakları kalıcı olarak siler. *Silme* işlevselliği, proje tarafından oluşturulan verileri hem Azure hem de Azure devops içinde DevOps Projeleri yok eder ve bu işlemi geri alamazsınız. Bu yordamı yalnızca istemleri dikkatle okuduktan sonra kullanın.

1. Azure portal, DevOps Projeleri panosuna gidin.
1. Sağ üst köşedeki **Sil**' i seçin. 
1. İstem sırasında, kaynakları *kalıcı olarak silmek* için **Evet** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Ekibinizin gereksinimlerine uygun olarak bu Azure CI/CD işlem hatlarını istediğiniz gibi değiştirebilirsiniz. Ayrıca bu CI/CD desenini diğer işlem hatlarınızda şablon olarak kullanabilirsiniz. Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Bir ASP.NET Core uygulaması oluşturmak ve Service Fabric dağıtmak için DevOps Projeleri kullanın
> * Azure DevOps ve bir Azure aboneliği yapılandırma 
> * CI işlem hattını inceleme
> * CD işlem hattını inceleme
> * Git 'e değişiklikleri işleyin ve Azure 'a otomatik olarak dağıtın
> * Kaynakları temizleme

Service Fabric ve mikro hizmetler hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Uygulamaları oluşturmak için bir mikro hizmetler yaklaşımı kullanma](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
