---
title: 'Hızlı Başlangıç: Azure DevOps Projeleri kullanarak Ruby on rayları için bir CI/CD işlem hattı oluşturun'
description: Azure DevOps projeleri, Azure'da kullanmaya başlamak kolaylaştırır. Birkaç hızlı adımda Azure hizmetinde bir Ruby Web uygulaması başlatabilirsiniz.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 5cb47cdd76d1de284c4dc6dbdfbfedd0095653ab
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899576"
---
# <a name="create-a-cicd-pipeline-for-ruby-on-rails-by-using-azure-devops-projects"></a>Azure DevOps Projeleri kullanarak Ruby on rayları için bir CI/CD işlem hattı oluşturun

Azure DevOps Projeleri kullanarak Ruby on raylarınız uygulamanız için sürekli tümleştirme (CI) ve sürekli teslim (CD) yapılandırın. DevOps Projeleri, bir Azure DevOps derleme ve yayın işlem hattının ilk yapılandırmasını basitleştirir.

Azure aboneliğiniz yoksa, [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) üzerinden ücretsiz edinebilirsiniz.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Azure DevOps Projeleri, Azure Repos bir CI/CD işlem hattı oluşturur. Yeni bir Azure DevOps kuruluşu oluşturabilir veya var olan bir kuruluşu kullanabilirsiniz. DevOps projeleri, Azure kaynaklarını da tercih ettiğiniz bir Azure aboneliği oluşturur.

1. [Azure Portal](https://portal.azure.com) oturum açın.

1. Sol bölmede **kaynak oluştur**' u seçin.

1. Arama kutusuna **DevOps Projeleri**yazın ve ardından **Oluştur**' u seçin.

    ![DevOps Projeleri panosu](_img/azure-devops-project-github/fullbrowser.png)

## <a name="select-a-sample-app-and-azure-service"></a>Örnek uygulama ve Azure hizmeti seçin

1. **Ruby** örnek uygulamasını seçin.

1. **Ruby on Rails** uygulama çerçevesini seçin. İşiniz bittiğinde **İleri**' yi seçin.

1. **Linux üzerinde Web App** varsayılan dağıtım hedefidir.  
    İsteğe bağlı olarak **kapsayıcılar için Web App**seçebilirsiniz. Daha önce seçtiğiniz uygulama çerçevesi, burada kullanılabilir olan Azure hizmet dağıtım hedefinin türünü belirler. 
    
1. Seçtiğiniz hedef hizmeti seçin ve ardından **İleri**' yi seçin.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps ve Azure aboneliğinin yapılandırın 

1. Yeni bir ücretsiz Azure DevOps kuruluş oluşturun veya mevcut bir kuruluşa seçin. 

1. Azure DevOps projeniz için bir ad girin. 

1. Azure aboneliğinizi ve konumunuzu seçin, uygulamanız için bir ad girin ve **bitti**' yi seçin.  
    DevOps projeleri Pano, birkaç dakika sonra Azure portalında görüntülenir. Azure DevOps kuruluşunuzda bir depoda bir örnek uygulama ayarlanır, bir derleme yürütülür ve uygulamanız Azure 'a dağıtılır. 
    
    Pano, kod deponuzda, CI/CD işlem hattınızla ve Azure 'daki uygulamanız için görünürlük sağlar. Sağ tarafta, çalışan uygulamanızı görüntülemek için **Araştır** ' ı seçin.

    ![Pano görünümü](_img/azure-devops-project-go/dashboardnopreview.png) 

## <a name="commit-your-code-changes-and-execute-the-cicd"></a>Kod değişikliklerinizi işleyin ve CI/CD 'yi yürütün

Azure DevOps Projeleri, Azure Pipelines veya GitHub 'da bir git deposu oluşturur. Depoyu görüntülemek ve uygulamanızda kod değişikliği yapmak için aşağıdakileri yapın:

1. DevOps Projeleri panosunda, sol taraftaki ana dalınızın bağlantısını seçin.  
    Bağlantı, yeni oluşturulan git deposuna bir görünüm açar.

1. Depo kopyası URL 'sini görüntülemek için sağ üst köşedeki **Kopyala** ' yı seçin.  
    Git deponuzu en sevdiğiniz IDE 'de kopyalayabilirsiniz. Sonraki birkaç adımda, kod değişiklikleri yapıp doğrudan ana dala işlemek için web tarayıcısını kullanabilirsiniz.

1. Solda *uygulama/görünümler/sayfalar/Home. html. erb* dosyasına gidin ve ardından **Düzenle**' yi seçin.

1. Dosyada değişiklik yapın. Örneğin, div etiketlerinin birindeki bazı metinleri değiştirin.

1. Seçin **işleme**ve ardından değişikliklerinizi kaydedin.

1. Tarayıcınızda DevOps Projeleri panosuna gidin.  
    Bir yapı devam ediyor olmalıdır. Yaptığınız değişiklikleri otomatik olarak oluşturulur ve bir CI/CD işlem hattı dağıtılır.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Azure Pipelines CI/CD işlem hattını inceleyin

Azure DevOps Projeleri, Azure DevOps kuruluşunuzda tam bir CI/CD işlem hattını otomatik olarak yapılandırır. İşlem hattını gerektiği şekilde keşfedin ve özelleştirin. Azure DevOps derleme ve yayın işlem hatları hakkında bilgi edinmek için aşağıdakileri yapın:

1. DevOps Projeleri panosuna gidin.

1. Üstte **derleme işlem hatları**' nı seçin.  
    Bir tarayıcı sekmesi, yeni projeniz için derleme işlem hattını görüntüler.

1. **Durum** alanını işaret edin ve ardından üç nokta (...) simgesini seçin.  
    Bir menü, yeni bir derlemeyi sıraya alma, bir derlemeyi duraklatma ve derleme işlem hattını düzenlemeyle çeşitli seçenekleri görüntüler.

1. **Düzenle**’yi seçin.

1. Bu bölmede, derleme işlem hattı için çeşitli görevleri inceleyebilirsiniz.  
    Derleme git deposundan kaynak getirme, bağımlılıkları geri yükleme ve dağıtımlar için kullanılan yayınlama çıkışları gibi çeşitli görevleri gerçekleştirir.

1. Derleme işlem hattı üstünde derleme işlem hattı adı seçin.

1. Bir şeyler daha açıklayıcı, select, derleme işlem hattı adını değiştirmek **Kaydet ve kuyruğa**ve ardından **Kaydet**.

1. Derleme işlem hattı adınızın altında **Geçmiş**’i seçin.  
    Bu bölme, derleme için son değişikliklerinizin denetim izini görüntüler. Azure DevOps, derleme ardışık düzeninde yapılan tüm değişiklikleri izler ve sürümleri karşılaştırmanızı sağlar.

1. **Tetikleyiciler**’i seçin.  
    DevOps Projeleri otomatik olarak bir CI tetikleyicisi oluşturur ve depoya yapılan her bir işleme yeni bir derleme başlatır. İsteğe bağlı olarak, CI işlemindeki dalları dahil etmek veya hariç tutmak seçebilirsiniz.

1. **Saklama**’yı seçin.  
    Senaryonuza bağlı olarak, saklamak veya belirli bir sayıda derlemeleri kaldırmak için ilkeleri belirtebilirsiniz.

1. **Build ve Release**' i seçin ve ardından **yayınlar**' ı seçin.  
    DevOps Projeleri, Azure dağıtımlarını yönetmek için bir yayın işlem hattı oluşturur.

1. Yayın işlem hattının yanındaki üç nokta (...) simgesini seçin ve ardından **Düzenle**' yi seçin.  
    Yayın işlem hattı, yayın işlemini tanımlayan bir *işlem hattı* içerir.

1. **Yapıtlar**’ın altında **Bırak**’ı seçin.  
    Daha önce incelenen derleme işlem hattı, yapıt için kullanılan çıktıyı üretir. 

1. **Bırakma** simgesinin sağ tarafında, **sürekli dağıtım tetikleyicisi**' ni seçin.  
    Bu sürüm ardışık düzeninde, her yeni derleme yapıtı kullanılabilir olduğunda bir dağıtımı yürüten etkinleştirilmiş bir CD tetikleyicisi vardır. İsteğe bağlı olarak, el ile yürütme dağıtımlarınızı gerektirir böylece tetikleyiciyi devre dışı bırakabilirsiniz. 

1. Solda, **Görevler**' i seçin.  
    Görevler, dağıtım işleminizin gerçekleştirdiği etkinliklerdir. Bu örnekte, Azure App Service dağıtmak üzere bir görev oluşturulmuştur.

1. Sağ tarafta, sürümlerin geçmişini görüntülemek için **yayınları görüntüle** ' yi seçin.

1. Bir yayının yanındaki üç nokta (...) simgesini seçin ve sonra **Aç**' ı seçin.  
    Yayın Özeti, ilişkili iş öğeleri ve testler gibi çeşitli menüleri inceleyebilirsiniz.

1. **İşlemeler**'i seçin.  
    Bu görünüm, bu dağıtımla ilişkili kod işlemelerini gösterir. 

1. **Günlükler**’i seçin.  
    Günlüklerde, dağıtım işlemiyle ilgili yararlı bilgiler bulunur. Bunları, dağıtımları sırasında ve sonrasında görüntüleyebilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında, Azure App Service örneğini ve bu hızlı başlangıçta oluşturduğunuz ilgili kaynakları silebilirsiniz. Bunu yapmak için DevOps Projeleri panosundaki **silme** işlevini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Takımınızın ihtiyaçlarını karşılamak için derleme ve yayın işlem hatlarını değiştirme hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Çoklu aşamalı sürekli dağıtım (CD) işlem hattınızı tanımlama](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
