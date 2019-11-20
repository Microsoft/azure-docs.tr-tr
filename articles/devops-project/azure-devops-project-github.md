---
title: 'Öğretici: Azure DevOps Projeleri kullanarak mevcut kodunuz için bir CI/CD işlem hattı oluşturun'
description: Azure DevOps Projeleri Azure 'u kullanmaya başlamanızı kolaylaştırır. Birkaç hızlı adımda, Azure hizmetinde bir uygulamayı başlatmak için kendi kodunuzu ve GitHub deponuzu kullanmanıza yardımcı olur DevOps Projeleri.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/27/2019
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: a0496999a9a5dc3e9bfd57df0ec035e6db77d620
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615119"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Öğretici: Azure DevOps Projeleri kullanarak mevcut kodunuz için bir CI/CD işlem hattı oluşturun

Azure DevOps Projeleri, Azure 'da sürekli tümleştirme (CI) ve sürekli teslim (CD) işlem hattı oluşturmak için basitleştirilmiş bir işlem sunar. Mevcut kodunuzu ve git deponuzu getirebilir veya örnek bir uygulama seçebilirsiniz.

Yapacaklarınız:

> [!div class="checklist"]
> * CI/CD işlem hattı oluşturmak için DevOps Projeleri kullanma
> * GitHub deponuza erişimi yapılandırma ve bir çerçeve seçme
> * Azure DevOps ve bir Azure aboneliği yapılandırma 
> * Değişiklikleri GitHub 'a işleyin ve Azure 'a otomatik olarak dağıtın
> * Azure Pipelines CI/CD işlem hattını inceleyin
> * Kaynakları temizleme

## <a name="prerequisites"></a>Ön koşullar

* Azure aboneliği. [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) aracılığıyla ücretsiz bir abonelik alabilirsiniz.
* .NET, Java, PHP, Node. js, Python veya statik Web kodu içeren bir GitHub veya dış git deposuna erişim.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Azure DevOps Projeleri, Azure Pipelines bir CI/CD işlem hattı oluşturur. Yeni bir Azure DevOps kuruluşu oluşturabilir veya var olan bir kuruluşu kullanabilirsiniz. Azure DevOps Projeleri ayrıca Azure kaynaklarını tercih ettiğiniz Azure aboneliğinde de oluşturur.

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. Azure portal menüsünde **kaynak oluştur**' u seçin.

   ![Azure portal menüsü-kaynak oluşturma](_img/azure-devops-project-github/createaresource.png)

3. **Devops** > **DevOps projesi**' ni seçin.

   ![DevOps Projeleri panosu](_img/azure-devops-project-github/azuredashboard.png)

1. **Kendi kodunuzu getir**' i seçin ve ardından **İleri**' yi seçin.

## <a name="configure-access-to-your-github-repo-and-select-a-framework"></a>GitHub deponuza erişimi yapılandırma ve bir çerçeve seçme

1. **GitHub** veya harici bir **Git** kod deposu seçin. Bu öğretici için **GitHub**' ı seçin. Azure 'un GitHub deponuza erişmesi için ilk kez GitHub ile kimlik doğrulamanız gerekebilir.

1. Bir **Depo** ve **dal**seçin ve ardından **İleri**' yi seçin.

1. Docker Kapsayıcıları kullanıyorsanız, değişiklik **uygulama Dockerto** **Evet**olarak ayarlanır. Bu öğretici için seçili **değil** ' i seçin ve ardından **İleri**' yi seçin. Docker kapsayıcılarını kullanma hakkında daha fazla bilgi için **ı** simgesinin üzerine gelin.

   ![Açılan menüde uygulama çerçevesi seçimi](_img/azure-devops-project-github/appframework.png)

1. Açılan menülerden bir **uygulama çalışma zamanı** ve bir **uygulama çerçevesi**seçin ve ardından **İleri**' yi seçin. Uygulama çerçevesi, kullanılabilir olan Azure hizmet dağıtım hedefinin türünü belirler.

1. Uygulamayı dağıtmak için bir **Azure hizmeti** seçin ve ardından **İleri**' yi seçin.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps ve bir Azure aboneliği yapılandırma

1. **Proje adı**için bir ad girin.

1. **Azure DevOps kuruluşunda** yeni bir ücretsiz kuruluş oluşturun veya açılan menüden var olan bir kuruluşu seçin.

1. **Azure aboneliğindeki**aboneliğinizi seçin ve **Web** uygulamasında bir ad girin veya varsayılanı kullanın. Bir **konum**seçin ve **bitti**' yi seçin. Birkaç dakika sonra, Azure portal DevOps Projeleri dağıtımına genel bakış görüntülenir.

1. DevOps Projeleri panosunu görüntülemek için **Kaynağa Git** ' i seçin. Hızlı erişim için, sağ üst köşede **projeyi** panonuza sabitleyin. Azure DevOps Projeleri otomatik olarak bir CI derleme ve yayın tetikleyicisi yapılandırır. Kodunuz, GitHub deponuzda veya başka bir dış depoda kalır ve **Azure DevOps kuruluşundaki**bir depoda bir örnek uygulama ayarlanır. Azure DevOps Projeleri derlemeyi çalıştırır ve uygulamayı Azure 'a dağıtır.

   ![Azure DevOps Projeleri pano görünümü](_img/azure-devops-project-github/projectsdashboard.png)

1. Pano, kod deponuzu, CI/CD işlem hattınızı ve Azure 'daki uygulamanızı gösterir. Çalışan uygulamanızı görüntülemek için sağdaki Azure kaynakları altında, **Araştır** ' ı seçin.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Değişiklikleri GitHub 'a işleyin ve Azure 'a otomatik olarak dağıtın

Artık bir ekiple uygulamanızda işbirliği yapmaya hazırsınız. CI/CD işlemi, en son çalışmalarınızı otomatik olarak Web sitenize dağıtır. GitHub deposunda yapılan her değişiklik Azure DevOps 'da bir derleme başlatır ve bir CD işlem hattı Azure 'a bir dağıtım çalıştırır.

1. DevOps Projeleri panonuzdan **depolar**' ı seçin. GitHub deponuz yeni bir tarayıcı sekmesinde açılır. uygulamanızda bir değişiklik yapın ve ardından **Değişiklikleri Kaydet**' i seçin.

1. Birkaç dakika sonra, bir derleme Azure Pipelines başlar. Yapı durumunu DevOps Projeleri panosunda izleyebilirsiniz. Ayrıca, DevOps Projeleri panosundan **derleme işlem hatları** sekmesini seçerek Azure DevOps kuruluşunuzda da izleme yapabilirsiniz.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Azure Pipelines CI/CD işlem hattını inceleyin

Azure DevOps Projeleri, Azure Pipelines bir CI/CD işlem hattını otomatik olarak yapılandırır. İşlem hattını gerektiği şekilde keşfedin ve özelleştirin. Derleme ve yayın işlem hatları hakkında bilgi edinmek için aşağıdakileri yapın:

1. DevOps Projeleri panosunda **derleme işlem hatları**' nı seçin.

1. **Azure Pipelines** sayfanız açıldıktan sonra, en son derlemelerin ve her bir yapının durumunun geçmişini görürsünüz.

   ![Azure Pipelines yapılar sayfası](_img/azure-devops-project-github/pipelinesbuildpage.png)

1. **Yapılar** sayfasının sağ üst köşesinde, geçerli derlemeyi değiştirmek için **Düzenle** ' yi, yeni bir derleme eklemek için **kuyruğu** veya dikey üç nokta düğmesini ( **&#8942;** ) seçerek daha fazla seçenek içeren bir menüyü açabilirsiniz. **Düzenle**’yi seçin.

1. Derleme, depodan kaynak getirme, bağımlılıkları geri yükleme ve dağıtımlar için yayımlama çıkışları gibi çeşitli görevleri yapar. Sağ tarafta, **ad**' ın altında, derleme işlem hattı adını daha açıklayıcı bir şekilde değiştirin. **& kuyruğu kaydet**' i seçin ve ardından **Kaydet**' i seçin. Bir açıklama girin ve sonra yeniden **Kaydet** ' i seçin.

   ![Azure DevOps derlemeleri sayfası](_img/azure-devops-project-github/buildpage.png)

1. Derleme için son değişikliklerinizin bir denetim izini görmek için **Geçmiş** sekmesini seçin.  Azure DevOps, derleme ardışık düzeninde yapılan tüm değişiklikleri izler ve sürümleri karşılaştırmanızı sağlar.

1. **Tetikleyiciler** sekmesini seçin. Azure DevOps projeleri otomatik olarak, bazı varsayılan AYARLARLA bir CI tetikleyicisi oluşturur. Her kod değişikliğini her seferinde derlemeyi çalıştırmak için **sürekli tümleştirmeyi etkinleştir** gibi Tetikleyiciler ayarlayabilirsiniz. Tetikleyicileri, yapıları belirli zamanlarda çalışacak şekilde zamanlamak için de ayarlayabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık Azure App Service ve bu öğreticide oluşturduğunuz ilgili kaynaklara ihtiyaç duymadığınızda, bunları silebilirsiniz. DevOps Projeleri panosundaki **silme** işlevini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide CI/CD işleminizi yapılandırdığınızda, Azure DevOps Projeleri ' de otomatik olarak bir derleme ve serbest bırakma işlem hattı oluşturdunuz. Ekibinizin ihtiyaçlarını karşılamak için bu derleme ve yayın işlem hatlarını değiştirebilirsiniz.

CI/CD işlem hattı hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Çoklu aşamalı sürekli dağıtım (CD) işlem hattınızı tanımlama](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

Uygulama izleme hakkında daha fazla bilgi için bkz.
  
 > [!div class="nextstepaction"]
 > [Azure izleyici nedir?](https://docs.microsoft.com/azure/azure-monitor/overview)
