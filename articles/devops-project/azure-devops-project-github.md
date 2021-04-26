---
title: 'Öğretici: Azure DevOps Starter kullanarak mevcut kodunuz için bir CI/CD işlem hattı oluşturun'
description: Azure DevOps Starter, Azure 'u kullanmaya başlamanızı kolaylaştırır. Birkaç hızlı adımda, Azure hizmetinde bir uygulamayı başlatmak için kendi kodunuzu ve GitHub deponuzu kullanmanıza yardımcı olur DevOps Projeleri.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: mvc
ms.openlocfilehash: c03ba657ac264b72d035f28956354398421ed2f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566609"
---
# <a name="create-a-cicd-pipeline-for-github-repo-using-azure-devops-starter"></a>Azure DevOps Starter kullanarak GitHub deposu için bir CI/CD işlem hattı oluşturma

Azure DevOps Starter, Azure 'da sürekli tümleştirme (CI) ve sürekli teslim (CD) işlem hattı oluşturmak için basitleştirilmiş bir işlem sunar. Mevcut kodunuzu ve git deponuzu getirebilir veya örnek bir uygulama seçebilirsiniz.

Şunları yapacaksınız:

> [!div class="checklist"]
> * CI/CD işlem hattı oluşturmak için DevOps Starter 'ı kullanma
> * GitHub deponuza erişimi yapılandırma ve bir çerçeve seçme
> * Azure DevOps ve bir Azure aboneliği yapılandırma 
> * Değişiklikleri GitHub 'a işleyin ve Azure 'a otomatik olarak dağıtın
> * Azure Pipelines CI/CD işlem hattını inceleyin
> * Kaynakları temizleme

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) aracılığıyla ücretsiz bir abonelik alabilirsiniz.
* .NET, Java, PHP, Node.js, Python veya statik Web kodu içeren bir GitHub veya dış git deposuna erişim.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Azure DevOps Starter, Azure Pipelines içinde bir CI/CD işlem hattı oluşturur. Yeni bir Azure DevOps kuruluşu oluşturabilir veya var olan bir kuruluşu kullanabilirsiniz. Azure DevOps Starter Ayrıca seçtiğiniz Azure aboneliğindeki Azure kaynaklarını da oluşturur.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Arama kutusuna **DevOps Starter** yazın ve ardından öğesini seçin. Yeni bir tane oluşturmak için **Ekle** ' ye tıklayın.

    ![DevOps başlangıç panosu](_img/azure-devops-starter-aks/search-devops-starter.png)
    
1. **Kendi kodunuzu getir**' i seçin ve ardından **İleri**' yi seçin.

## <a name="configure-access-to-your-github-repo-and-select-a-framework"></a>GitHub deponuza erişimi yapılandırma ve bir çerçeve seçme

1. **GitHub** veya harici bir **Git** kod deposu seçin. Bu öğretici için **GitHub**' ı seçin. Azure 'un GitHub deponuza erişmesi için ilk kez GitHub ile kimlik doğrulamanız gerekebilir.

1. Bir **Depo** ve **dal** seçin ve ardından **İleri**' yi seçin.

1. Docker Kapsayıcıları kullanıyorsanız, değişiklik **uygulama Dockerto** **Evet** olarak ayarlanır. Bu öğretici için seçili **değil** ' i seçin ve ardından **İleri**' yi seçin. Docker kapsayıcılarını kullanma hakkında daha fazla bilgi için **ı** simgesinin üzerine gelin.

   ![Açılan menüde uygulama çerçevesi seçimi](_img/azure-devops-project-github/appframework.png)

1. Açılan menülerden bir **uygulama çalışma zamanı** ve bir **uygulama çerçevesi** seçin ve ardından **İleri**' yi seçin. Uygulama çerçevesi, kullanılabilir olan Azure hizmet dağıtım hedefinin türünü belirler.

1. Uygulamayı dağıtmak için bir **Azure hizmeti** seçin ve ardından **İleri**' yi seçin.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps ve bir Azure aboneliği yapılandırma

1. **Proje adı** için bir ad girin.

1. **Azure DevOps kuruluşunda** yeni bir ücretsiz kuruluş oluşturun veya açılan menüden var olan bir kuruluşu seçin.

1. **Azure aboneliğindeki** aboneliğinizi seçin ve **Web** uygulamasında bir ad girin veya varsayılanı kullanın. Bir **konum** seçin ve **bitti**' yi seçin. Birkaç dakika sonra DevOps Başlatıcı dağıtımına genel bakış Azure portal görüntülenir.

1. DevOps başlangıç panosunu görüntülemek için **Kaynağa Git** ' i seçin. Hızlı erişim için, sağ üst köşede **projeyi** panonuza sabitleyin. Azure DevOps Starter, otomatik olarak bir CI derleme ve yayın tetikleyicisi yapılandırır. Kodunuz, GitHub deponuzda veya başka bir dış depoda kalır ve **Azure DevOps kuruluşundaki** bir depoda bir örnek uygulama ayarlanır. Azure DevOps Starter derlemeyi çalıştırır ve uygulamayı Azure 'a dağıtır.

   ![Azure DevOps Projeleri pano görünümü](_img/azure-devops-project-github/projectsdashboard.png)

1. Pano, kod deponuzu, CI/CD işlem hattınızı ve Azure 'daki uygulamanızı gösterir. Çalışan uygulamanızı görüntülemek için sağdaki Azure kaynakları altında, **Araştır** ' ı seçin.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Değişiklikleri GitHub 'a işleyin ve Azure 'a otomatik olarak dağıtın

Artık bir ekiple uygulamanızda işbirliği yapmaya hazırsınız. CI/CD işlemi, en son çalışmalarınızı otomatik olarak Web sitenize dağıtır. GitHub deposunda yapılan her değişiklik Azure DevOps 'da bir derleme başlatır ve bir CD işlem hattı Azure 'a bir dağıtım çalıştırır.

1. DevOps başlangıç panonuzdan **depolar**' ı seçin. GitHub deponuz yeni bir tarayıcı sekmesinde açılır. Uygulamanızda bir değişiklik yapın ve ardından **Değişiklikleri Kaydet**' i seçin.

1. Birkaç dakika sonra, bir derleme Azure Pipelines başlar. Yapı durumunu DevOps başlangıç panosunda izleyebilirsiniz. Ayrıca, DevOps başlangıç panosundan **derleme işlem hatları** sekmesini seçerek Azure DevOps kuruluşunuzda da izleme yapabilirsiniz.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Azure Pipelines CI/CD işlem hattını inceleyin

Azure DevOps Starter, Azure Pipelines bir CI/CD işlem hattını otomatik olarak yapılandırır. İşlem hattını gerektiği şekilde keşfedin ve özelleştirin. Derleme ve yayın işlem hatları hakkında bilgi edinmek için aşağıdakileri yapın:

1. DevOps başlangıç panosundan, **derleme işlem hatları**' nı seçin.

1. **Azure Pipelines** sayfanız açıldıktan sonra, en son derlemelerin ve her bir yapının durumunun geçmişini görürsünüz.

   ![Azure Pipelines yapılar sayfası](_img/azure-devops-project-github/pipelinesbuildpage.png)

1. **Yapılar** sayfasının sağ üst köşesinde, geçerli derlemeyi değiştirmek için **Düzenle** ' yi, yeni bir derleme eklemek için **kuyruğu** veya dikey üç nokta düğmesini (**&#8942;**) seçerek daha fazla seçenek içeren bir menüyü açabilirsiniz. **Düzenle**'yi seçin.

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
> [Çoklu aşamalı sürekli dağıtım (CD) işlem hattınızı tanımlama](/azure/devops/pipelines/release/define-multistage-release-process)

Uygulama izleme hakkında daha fazla bilgi için bkz.
  
 > [!div class="nextstepaction"]
 > [Azure izleyici nedir?](../azure-monitor/overview.md)