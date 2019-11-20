---
title: 'Hızlı Başlangıç: Azure DevOps Projeleri ile PHP için CI/CD işlem hattı oluşturma'
description: DevOps Projeleri Azure 'u kullanmaya başlamanızı kolaylaştırır. Birkaç hızlı adımda, tercih ettiğiniz bir Azure hizmetinde uygulama başlatmanıza yardımcı olur.
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
ms.openlocfilehash: 7c097c2a7247c9cad6cdce8a89059ee8d90d159e
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899585"
---
# <a name="create-a-cicd-pipeline-for-php-with-azure-devops-projects"></a>Azure DevOps Projeleri ile PHP için CI/CD işlem hattı oluşturma

Azure DevOps Projeleri, Azure kaynakları oluşturan ve Azure Pipelines PHP uygulamanız için sürekli tümleştirme (CI) ve sürekli teslim (CD) işlem hattı ayarlayan basitleştirilmiş bir deneyim sunar.  

Bir Azure aboneliğiniz yoksa, [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)aracılığıyla ücretsiz olarak edinebilirsiniz.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

 DevOps Projeleri, Azure Pipelines bir CI/CD işlem hattı oluşturur. Ücretsiz yeni bir Azure DevOps organizasyonu oluşturabilir veya var olan bir kuruluşu kullanabilirsiniz. DevOps projeleri, Azure kaynaklarını da tercih ettiğiniz bir Azure aboneliği oluşturur.

1. [Microsoft Azure portalında](https://portal.azure.com) oturum açın.

1. Sol bölmede **kaynak oluştur** simgesini seçin ve ardından **DevOps Projeleri**arayın.  

3. **Oluştur**’u seçin.

    ![Sürekli teslim yapılandırması başlatılıyor](_img/azure-devops-project-php/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Örnek uygulama ve Azure hizmeti seçme

1. PHP örnek uygulamasını seçin.  
        PHP örnekleri, çeşitli uygulama çerçevelerinin bir seçimini içerir. Varsayılan örnek Framework, Laralevel ' dır. 
        
2. Varsayılan ayarı bırakın ve ardından **İleri**' yi seçin.  

1. Kapsayıcılar Için Web App varsayılan dağıtım hedefidir.  
    Daha önce seçtiğiniz uygulama çerçevesi, burada kullanılabilir olan Azure hizmet dağıtım hedefinin türünü belirler.  Varsayılan hizmeti bırakın ve ardından **İleri**' yi seçin.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps ve Azure aboneliğinin yapılandırın 

1. Yeni bir Azure DevOps organizasyonu oluşturun veya var olan bir kuruluşu seçin. 

    a. Azure DevOps 'da projeniz için bir ad seçin. 
    
    b. Azure aboneliğinizi ve konumunuzu seçin, uygulamanız için bir ad girin ve **bitti**' yi seçin.   
        Birkaç dakika sonra, DevOps Projeleri panosu Azure portal görüntülenir. Örnek bir uygulama, Azure DevOps kuruluşunuzda bir depoda ayarlanır, bir derleme çalışır ve uygulamanız Azure 'a dağıtılır. Bu Pano, kod deponuza, CI/CD işlem hattınızla ve Azure 'daki uygulamanıza ilişkin görünürlük sağlar.  
        
2. Çalışan uygulamanızı görüntülemek için **Araştır** ' ı seçin.

    ![Pano görünümü](_img/azure-devops-project-php/dashboardnopreview.png) 
    
   DevOps Projeleri bir CI derleme ve yayın tetikleyicisi otomatik olarak yapılandırıldı.  Artık en son çalışmanızı otomatik olarak web sitenize dağıtan bir CI/CD işlemiyle PHP uygulaması üzerinde bir ekiple birlikte çalışmaya hazırsınız.

## <a name="commit-code-changes-and-execute-cicd"></a>Kod değişikliklerini işleme ve CI/CD’yi yürütme

 DevOps Projeleri, Azure Repos veya GitHub 'da bir git deposu oluşturur. Depoyu görüntülemek ve uygulamanızda kod değişikliği yapmak için aşağıdaki adımları uygulayın:

1. DevOps Projeleri panosunun sol tarafında, ana dalınızın bağlantısını seçin.   
    Bu bağlantı yeni oluşturulan Git deposuna bir görünüm açar.

1. Depo kopya URL'sini görüntülemek için tarayıcının sağ üst kısmından **Kopya**’yı seçin.   
    Git deponuzu en sevdiğiniz IDE’de kopyalayabilirsiniz. Sonraki birkaç adımda, kod değişikliklerini doğrudan ana dala eklemek ve yürütmek için Web tarayıcısını kullanın.

1. Sol tarafta **kaynaklar/görünümler/hoş geldiniz. dikey pencere. php** dosyasına gidin.

1. **Düzenle**' yi seçin ve sonra bazı metinden değişiklik yapın.  Örneğin, div etiketlerinden biri için metnin bir kısmını değiştirin.

1. Seçin **işleme**ve ardından değişikliklerinizi kaydedin.

1. Tarayıcınızda DevOps Projeleri panosuna gidin.  
Artık sürmekte olan bir derleme görmeniz gerekir. Yeni yaptığınız değişiklikler otomatik olarak bir CI/CD işlem hattı aracılığıyla oluşturulup dağıtılır.

## <a name="examine-the-cicd-pipeline"></a>CI/CD işlem hattı inceleyin

 DevOps Projeleri, Azure Pipelines bir tam CI/CD işlem hattını otomatik olarak yapılandırır. İşlem hattını gerektiği şekilde keşfedin ve özelleştirin. Derleme ve yayın işlem hatları hakkında bilgi edinmek için aşağıdakileri yapın:

1. DevOps projeleri panonun üst kısmında seçin **derleme işlem hatlarını**.  
    Bu bağlantı, yeni projeniz için bir tarayıcı sekmesi ve derleme işlem hattı açar.

1. **Durum** alanını işaret edin ve ardından **üç nokta** (...) simgesini seçin.  
    Bir menü, yeni bir derlemeyi sıraya alma, bir derlemeyi duraklatma ve derleme işlem hattını düzenlemeyle çeşitli seçenekleri görüntüler.

1. **Düzenle**’yi seçin.

1. Bu bölmede, derleme işlem hattı için çeşitli görevleri inceleyebilirsiniz.  
    Derleme, git deposundan kaynak getirme, bağımlılıkları geri yükleme ve dağıtımlar için kullanılan çıkışları yayımlama gibi çeşitli görevleri çalıştırır.

1. Derleme işlem hattı üstünde derleme işlem hattı adı seçin.

1. Derleme işlem hattınızı daha açıklayıcı bir şekilde değiştirin, seçin, **& kuyruğu kaydedin**ve ardından **Kaydet**' i seçin.

1. Derleme işlem hattı adınızın altında **Geçmiş**’i seçin.   
    **Geçmiş** bölmesi, derleme için son değişikliklerinizin denetim izini görüntüler. Azure işlem hatları için derleme işlem hattı yapılan değişiklikleri izler ve sürümleri karşılaştırmanızı sağlar.

1. **Tetikleyiciler**’i seçin.  
      DevOps projeleri CI tetikleyicisini otomatik olarak oluşturulan ve depoya her işleme, yeni bir yapı başlatır. İsteğe bağlı olarak dalları CI işlemine dahil etmeyi veya işlemden hariç tutmayı seçebilirsiniz.

1. **Saklama**’yı seçin.   
    Senaryonuza bağlı olarak, saklamak veya belirli bir sayıda derlemeleri kaldırmak için ilkeleri belirtebilirsiniz.

1. **Build ve Release**' i seçin ve ardından **yayınlar**' ı seçin.  
     DevOps Projeleri, Azure dağıtımlarını yönetmek için bir yayın işlem hattı oluşturur.

1. Yayın işlem hattının yanındaki üç nokta (...) simgesini seçin ve ardından **Düzenle**' yi seçin.  
    Sürüm ardışık yayın işlemini tanımlar. bir işlem hattı içerir. 

12. **Yapıtlar**’ın altında **Bırak**’ı seçin.  
    Önceki adımlarda inceettiğiniz derleme işlem hattı, yapıt için kullanılan çıktıyı üretir. 

1. Yanındaki **bırak** simgesini seçme **sürekli dağıtım tetikleyicisi**.   
    Bu sürüm ardışık düzeninde, kullanılabilir yeni bir yapı yapıtı her seferinde bir dağıtım çalıştıran etkinleştirilmiş bir CD tetikleyicisi vardır.  İsteğe bağlı olarak, el ile yürütme dağıtımlarınızı gerektirir böylece tetikleyiciyi devre dışı bırakabilirsiniz. 

1. Sol tarafta, seçin **görevleri**.  
        Dağıtım işleminizin gerçekleştiren etkinlikler görevlerdir.  Bu örnekte, Azure App Service dağıtmak üzere bir görev oluşturulmuştur.

1. Sağ tarafta, sürümlerin geçmişini görüntülemek için **sürümleri görüntüle** ' yi seçin.

1. Sürümlerden birinin yanındaki üç nokta (...) simgesini seçin ve sonra **Aç**' ı seçin.  
        Bu görünümde keşfedilebilecek yayın özeti, ilişkili iş öğeleri ve testler gibi çeşitli menüler vardır.

1. **İşlemeler**'i seçin.  
        Bu görünümde, belirli bir dağıtım ile ilişkili kod tamamlama gösterilir. 

1. **Günlükler**’i seçin.  
        Günlüklerde, dağıtım işlemiyle ilgili yararlı bilgiler bulunur. Bunlar hem dağıtım sırasında hem de sonrasında görüntülenebilir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık ihtiyaç duymadığınızda, Azure App Service ve diğer ilgili kaynakları silebilirsiniz. Kullanım **Sil** DevOps projeleri Pano işlevselliği.

## <a name="next-steps"></a>Sonraki adımlar

CI/CD işleminizi yapılandırdığınızda, derleme ve yayın işlem hatları otomatik olarak oluşturulmuştur. Ekibinizin ihtiyaçlarını karşılamak için bu derleme ve yayın işlem hatlarını değiştirebilirsiniz. CI/CD işlem hattı hakkında daha fazla bilgi edinmek için şu öğreticiye bakın:

> [!div class="nextstepaction"]
> [CD işlemini özelleştirme](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
