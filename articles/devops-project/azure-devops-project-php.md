---
title: 'Hızlı başlangıç: Azure DevOps Starter ile PHP için bir CI/CD işlem hattı oluşturma'
description: DevOps Starter, Azure 'u kullanmaya başlamanızı kolaylaştırır. Birkaç hızlı adımda, tercih ettiğiniz bir Azure hizmetinde uygulama başlatmanıza yardımcı olur.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 06ac110da35a5a77157bd4e31caaa5aa5fd015cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566575"
---
# <a name="create-a-cicd-pipeline-for-php-with-azure-devops-starter"></a>Azure DevOps Starter ile PHP için bir CI/CD işlem hattı oluşturma

Azure DevOps Starter, Azure kaynaklarını oluşturan ve Azure Pipelines ' de PHP uygulamanız için sürekli tümleştirme (CI) ve sürekli teslim (CD) işlem hattı ayarlayan basitleştirilmiş bir deneyim sunar.  

Bir Azure aboneliğiniz yoksa, [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)aracılığıyla ücretsiz olarak edinebilirsiniz.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

 DevOps Starter Azure Pipelines ' de bir CI/CD işlem hattı oluşturur. Ücretsiz yeni bir Azure DevOps organizasyonu oluşturabilir veya var olan bir kuruluşu kullanabilirsiniz. DevOps Projeleri ayrıca Azure kaynaklarını tercih ettiğiniz Azure aboneliğinde de oluşturur.

1. [Microsoft Azure portalda](https://portal.azure.com) oturum açın.

1. Arama kutusuna **DevOps Starter** yazın ve ardından öğesini seçin. Yeni bir tane oluşturmak için **Ekle** ' ye tıklayın.

    ![DevOps başlangıç panosu](_img/azure-devops-starter-aks/search-devops-starter.png) 

## <a name="select-a-sample-application-and-azure-service"></a>Örnek uygulama ve Azure hizmeti seçme

1. PHP örnek uygulamasını seçin. PHP örnekleri birkaç uygulama çerçevesi seçeneği içerir. Varsayılan örnek çerçeve Laravel’dir.
        
1. Varsayılan ayarı bırakın ve ardından **İleri**' yi seçin.  

1. Kapsayıcılar için Web App varsayılan dağıtım hedefidir. Daha önce seçtiğiniz uygulama çerçevesi, burada kullanılabilir olan Azure hizmet dağıtım hedefinin türünü belirler.  Varsayılan hizmeti bırakın ve ardından **İleri**' yi seçin.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps ve bir Azure aboneliği yapılandırma 

1. Yeni bir Azure DevOps organizasyonu oluşturun veya var olan bir kuruluşu seçin. 

    1. Azure DevOps 'da projeniz için bir ad seçin. 
    
    1. Azure aboneliğinizi ve konumunuzu seçin, uygulamanız için bir ad girin ve **bitti**' yi seçin.  
    
    Birkaç dakika sonra DevOps başlangıç panosu Azure portal görüntülenir. Örnek bir uygulama, Azure DevOps kuruluşunuzda bir depoda ayarlanır, bir derleme çalışır ve uygulamanız Azure 'a dağıtılır. Bu Pano, kod deponuza, CI/CD işlem hattınızla ve Azure 'daki uygulamanıza ilişkin görünürlük sağlar.  
        
2. Çalışan uygulamanızı görüntülemek için **Araştır** ' ı seçin.

    ![Pano görünümü](_img/azure-devops-project-php/dashboardnopreview.png) 
    
   DevOps Starter, otomatik olarak bir CI derleme ve yayın tetikleyicisi yapılandırdı.  Artık en son çalışmanızı otomatik olarak web sitenize dağıtan bir CI/CD işlemiyle PHP uygulaması üzerinde bir ekiple birlikte çalışmaya hazırsınız.

## <a name="commit-code-changes-and-execute-cicd"></a>Kod değişikliklerini işleme ve CI/CD’yi yürütme

 DevOps Starter, Azure Repos veya GitHub 'da bir git deposu oluşturur. Depoyu görüntülemek ve uygulamanızda kod değişikliği yapmak için aşağıdaki adımları uygulayın:

1. DevOps başlangıç panosunun sol tarafında, ana dalınızın bağlantısını seçin. Bu bağlantı yeni oluşturulan Git deposuna bir görünüm açar.

1. Depo kopya URL'sini görüntülemek için tarayıcının sağ üst kısmından **Kopya**’yı seçin. Git deponuzu en sevdiğiniz IDE’de kopyalayabilirsiniz. Sonraki birkaç adımda, kod değişikliklerini doğrudan ana dala eklemek ve yürütmek için Web tarayıcısını kullanın.

1. Sol tarafta **kaynaklar/görünümler/hoş geldiniz. dikey pencere. php** dosyasına gidin.

1. **Düzenle**' yi seçin ve sonra bazı metinden değişiklik yapın.  Örneğin, div etiketlerinden biri için metnin bir kısmını değiştirin.

1. **Yürüt**' ü seçin ve ardından değişikliklerinizi kaydedin.

1. Tarayıcınızda DevOps başlangıç panosuna gidin. Artık sürmekte olan bir derleme görmeniz gerekir. Yeni yaptığınız değişiklikler otomatik olarak bir CI/CD işlem hattı aracılığıyla oluşturulup dağıtılır.

## <a name="examine-the-cicd-pipeline"></a>CI/CD işlem hattını inceleyin

 DevOps Starter, Azure Pipelines içinde tam bir CI/CD işlem hattını otomatik olarak yapılandırır. İşlem hattını gerektiği şekilde keşfedin ve özelleştirin. Derleme ve yayın işlem hatları hakkında bilgi edinmek için aşağıdakileri yapın:

1. DevOps başlangıç panosunun en üstünde **derleme Işlem hatları**' nı seçin. Bu bağlantı, yeni projeniz için bir tarayıcı sekmesi ve derleme işlem hattı açar.

1. **Durum** alanını işaret edin ve ardından **üç nokta** (...) simgesini seçin. Bir menü, yeni bir derlemeyi sıraya alma, bir derlemeyi duraklatma ve derleme işlem hattını düzenlemeyle çeşitli seçenekleri görüntüler.

1. **Düzenle**'yi seçin.

1. Bu bölmede, derleme işlem hattınızla ilgili çeşitli görevleri inceleyebilirsiniz. Derleme, git deposundan kaynak getirme, bağımlılıkları geri yükleme ve dağıtımlar için kullanılan çıkışları yayımlama gibi çeşitli görevleri çalıştırır.

1. Derleme işlem hattının üst kısmında derleme işlem hattı adı’nı seçin.

1. Derleme işlem hattınızı daha açıklayıcı bir şekilde değiştirin, seçin, **& kuyruğu kaydedin** ve ardından **Kaydet**' i seçin.

1. Derleme işlem hattı adınızın altında **Geçmiş**’i seçin.  **Geçmiş** bölmesi, derleme için son değişikliklerinizin denetim izini görüntüler. Azure Pipelines, derleme ardışık düzeninde yapılan tüm değişiklikleri izler ve sürümleri karşılaştırmanızı sağlar.

1. **Tetikleyiciler**’i seçin. DevOps Starter otomatik olarak bir CI tetikleyicisi oluşturdu ve depoya yapılan her işlem yeni bir derleme başlatır. İsteğe bağlı olarak dalları CI işlemine dahil etmeyi veya işlemden hariç tutmayı seçebilirsiniz.

1. **Saklama**’yı seçin. Senaryonuza bağlı olarak, belirli sayıda derlemeyi tutmanın veya kaldırabilmeniz için ilkeler belirtebilirsiniz.

1. **Build ve Release**' i seçin ve ardından **yayınlar**' ı seçin.  DevOps Starter, Azure dağıtımlarını yönetmek için bir yayın işlem hattı oluşturur.

1. Yayın işlem hattının yanındaki üç nokta (...) simgesini seçin ve ardından **Düzenle**' yi seçin. Yayın işlem hattı, yayın işlemini tanımlayan bir işlem hattı içerir. 

12. **Yapıtlar**’ın altında **Bırak**’ı seçin. Önceki adımlarda inceettiğiniz derleme işlem hattı, yapıt için kullanılan çıktıyı üretir. 

1. **Bırakma** simgesinin yanındaki **sürekli dağıtım tetikleyicisi**' ni seçin. Bu sürüm ardışık düzeninde, kullanılabilir yeni bir yapı yapıtı her seferinde bir dağıtım çalıştıran etkinleştirilmiş bir CD tetikleyicisi vardır. İsteğe bağlı olarak, dağıtımlarınızın el ile yürütme gerektirdiğinden tetikleyiciyi devre dışı bırakabilirsiniz. 

1. Sol tarafta **Görevler**' i seçin. Görevler, dağıtım işleminizin gerçekleştirdiği etkinliklerdir. Bu örnekte, Azure App Service dağıtmak üzere bir görev oluşturulmuştur.

1. Sağ tarafta, sürümlerin geçmişini görüntülemek için **sürümleri görüntüle** ' yi seçin.

1. Sürümlerden birinin yanındaki üç nokta (...) simgesini seçin ve sonra  **Aç**' ı seçin. Bu görünümde keşfedilebilecek yayın özeti, ilişkili iş öğeleri ve testler gibi çeşitli menüler vardır.

1. **İşlemeler**'i seçin. Bu görünüm, belirli bir dağıtımla ilişkili kod işlemelerini gösterir. 

1. **Günlükleri** seçin. Günlüklerde, dağıtım işlemiyle ilgili yararlı bilgiler bulunur. Bunlar hem dağıtım sırasında hem de sonrasında görüntülenebilir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık ihtiyaç duymadığınızda, Azure App Service ve diğer ilgili kaynakları silebilirsiniz. DevOps başlangıç panosundaki **silme** işlevini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

CI/CD işleminizi yapılandırdığınızda, derleme ve yayın işlem hatları otomatik olarak oluşturulmuştur. Ekibinizin ihtiyaçlarını karşılamak için bu derleme ve yayın işlem hatlarını değiştirebilirsiniz. CI/CD işlem hattı hakkında daha fazla bilgi edinmek için şu öğreticiye bakın:

> [!div class="nextstepaction"]
> [CD işlemini özelleştirme](/azure/devops/pipelines/release/define-multistage-release-process)