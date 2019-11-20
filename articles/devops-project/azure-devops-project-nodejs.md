---
title: Azure DevOps Projeleri ile Node. js için bir CI/CD işlem hattı oluşturma
description: DevOps Projeleri Azure 'u kullanmaya başlamanızı kolaylaştırır. Birkaç hızlı adımda seçtiğiniz bir Azure hizmetinde bir uygulamayı başlatmanıza yardımcı olur.
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
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
monikerRange: vsts
ms.openlocfilehash: 11edeb35119e2c598fd83fd89c65ba4dc4679650
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256119"
---
#  <a name="quickstart-create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-projects"></a>Hızlı başlangıç: Azure DevOps Projeleri ile Node. js için Azure Pipelines bir CI/CD işlem hattı oluşturma

Azure DevOps Projeleri, Azure kaynakları oluşturan ve Azure Pipelines ' de Node. js uygulamanız için sürekli tümleştirme (CI) ve sürekli teslim (CD) işlem hattı ayarlayan basitleştirilmiş bir deneyim sunar.  

Azure aboneliğiniz yoksa [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)aracılığıyla ücretsiz bir ücretsiz edinebilirsiniz.

## <a name="sign-in-to-the-azure-portal"></a>Azure portal oturum açın

DevOps Projeleri, Azure Pipelines bir CI/CD işlem hattı oluşturur. Yeni bir Azure DevOps kuruluşu oluşturabilir veya var olan bir kuruluşu kullanabilirsiniz. DevOps Projeleri ayrıca Azure kaynaklarını tercih ettiğiniz Azure aboneliğinde de oluşturur.

1. [Microsoft Azure Portal](https://portal.azure.com)oturum açın.

1. Sol bölmede, **kaynak oluştur**' u seçin ve ardından **DevOps Projeleri**arayın. 

    ![Sürekli teslim yapılandırma kaynağı oluştur](_img/azure-devops-project-nodejs/create-azure-resource.png)

## <a name="select-a-sample-application-and-azure-service"></a>Örnek uygulama ve Azure hizmeti seçin

1. Node. js örnek uygulamasını seçin.  
    Node. js örnekleri, çeşitli uygulama çerçevelerinin bir seçimini içerir.

1. Varsayılan örnek Framework Express. js ' dir. Varsayılan ayarı bırakın ve ardından **İleri**' yi seçin.  
    **Windows üzerinde Web App** varsayılan dağıtım hedefidir.  Daha önce seçtiğiniz uygulama çerçevesi, burada kullanılabilir olan Azure hizmet dağıtım hedefinin türünü belirler.  

2. Varsayılan hizmeti bırakın ve ardından **İleri**' yi seçin.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps ve bir Azure aboneliği yapılandırma 

1. Yeni bir Azure DevOps organizasyonu oluşturun veya var olan bir kuruluşu seçin. 

    a. Projeniz için bir ad girin.

    b. Azure aboneliğinizi ve konumunuzu seçin, uygulamanız için bir ad girin ve **bitti**' yi seçin.  
    Birkaç dakika sonra, DevOps Projeleri panosu Azure portal görüntülenir. Örnek uygulama, Azure DevOps kuruluşunuzda bir depoda ayarlanır, bir derleme yürütülür ve uygulamanız Azure 'a dağıtılır. Bu Pano, kod deponuza, CI/CD işlem hattına ve Azure 'daki uygulamanıza ilişkin görünürlük sağlar.
     
3. Çalışan uygulamanızı görüntülemek için **Araştır** ' ı seçin.

    ![CI/CD işlem hattının pano görünümü](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 
    
DevOps Projeleri bir CI derleme ve yayın tetikleyicisi otomatik olarak yapılandırıldı.  Artık en son çalışmalarınızı Web sitenize dağıtan bir CI/CD işlemiyle bir Node. js uygulamasında bir takımla işbirliği yapmaya hazırsınız.

## <a name="commit-code-changes-and-execute-cicd"></a>Kod değişikliklerini işleyin ve CI/CD yürütün

DevOps Projeleri, Azure Repos veya GitHub 'da bir git deposu oluşturur. Depoyu görüntülemek ve uygulamanızda kod değişikliği yapmak için aşağıdaki adımları uygulayın.

1. DevOps Projeleri panosunun sol tarafında, ana dalınızın bağlantısını seçin.  
Bu bağlantı, yeni oluşturulan git deposunun bir görünümünü açar.

1. Depo kopyası URL 'sini görüntülemek için tarayıcının sağ üst köşesindeki **Kopyala** ' yı seçin.   
    Git deponuzu en sevdiğiniz IDE 'niz içinde kopyalayabilirsiniz. Sonraki birkaç adımda, kod değişikliklerini doğrudan ana dala getirmek ve yürütmek için Web tarayıcısını kullanabilirsiniz.

1. Tarayıcının sol tarafında **Görünümler/index. Pug** dosyasına gidin.

1. **Düzenle**' yi seçin ve ardından H2 başlığında bir değişiklik yapın.  
    Örneğin, **Azure DevOps Projeleri ile hemen kullanmaya** başlayın yazın veya başka bir değişiklik yapın.

1. **Yürüt**' ü seçin ve ardından değişikliklerinizi kaydedin.

1. Tarayıcınızda DevOps Projeleri panosuna gidin.   
Artık sürmekte olan bir derleme görmeniz gerekir. Yeni yaptığınız değişiklikler otomatik olarak bir CI/CD işlem hattı aracılığıyla oluşturulup dağıtılır.

## <a name="examine-the-azure-cicd-pipeline"></a>Azure CI/CD işlem hattını inceleyin

Önceki adımda, DevOps Projeleri otomatik olarak tam CI/CD işlem hattı yapılandırdı. İşlem hattını gereken şekilde araştırıp özelleştirin. Derleme ve yayın işlem hatları hakkında bilgi edinmek için aşağıdaki adımları uygulayın.

1. DevOps Projeleri panosunun en üstünde **derleme Işlem hatları**' nı seçin.  
Bu bağlantı, yeni projeniz için bir tarayıcı sekmesi ve derleme işlem hattı açar.

1. **Durum** alanını işaret edin ve ardından üç nokta (...) simgesini seçin.  
    Bu eylem, yeni bir derlemeyi sıraya alma, bir derlemeyi duraklatma ve derleme işlem hattını düzenlemeyle çeşitli etkinlikleri başlatabileceğiniz bir menü açar.

1. **Düzenle**' yi seçin.

1. Bu bölmede, derleme işlem hattınızla ilgili çeşitli görevleri inceleyebilirsiniz.  
Derleme, git deposundan kaynak getirme, bağımlılıkları geri yükleme ve dağıtımlar için kullanılan çıkışları yayımlama gibi çeşitli görevler gerçekleştirir.

1. Yapı işlem hattının en üstünde derleme işlem hattı adını seçin.

1. Derleme işlem hattınızı daha açıklayıcı bir şekilde değiştirin, **& kuyruğu kaydet**' i seçin ve ardından **Kaydet**' i seçin.

1. Yapı işlem hattı adınızın altında **Geçmiş**' i seçin.   
**Geçmiş** bölmesinde, derleme için son değişikliklerinizin bir denetim izini görürsünüz.  Azure Pipelines, derleme ardışık düzeninde yapılan tüm değişiklikleri izler ve sürümleri karşılaştırmanızı sağlar.

1. **Tetikleyiciler**' i seçin.   
 DevOps Projeleri otomatik olarak bir CI tetikleyicisi oluşturdu ve depoya yapılan her bir kayıt yeni bir derleme başlatır.  İsteğe bağlı olarak, CI işlemine dal eklemeyi veya dışlamayı seçebilirsiniz.

1. **Bekletme**' yi seçin.   
Senaryonuza bağlı olarak, belirli sayıda derlemeyi tutmanın veya kaldırabilmeniz için ilkeler belirtebilirsiniz.

1. **Build ve Release**' i seçin ve ardından **yayınlar**' ı seçin.  
 DevOps Projeleri, Azure dağıtımlarını yönetmek için bir yayın işlem hattı oluşturur.

1. Sol tarafta, yayın işlem hattının yanındaki üç nokta (...) simgesini seçin ve ardından **Düzenle**' yi seçin.  
Yayın işlem hattı yayın işlemini tanımlar.

12. **Yapıtlar**altında **bırak**' ı seçin.  
    Önceki adımlarda inceettiğiniz derleme işlem hattı, yapıt için kullanılan çıktıyı üretir. 

1. **Bırakma** simgesinin yanındaki **sürekli dağıtım tetikleyicisi**' ni seçin.  
Bu sürüm ardışık düzeninde, kullanılabilir yeni bir yapı yapıtı olduğunda bir dağıtımı çalıştıran etkinleştirilmiş bir CD tetikleyicisi vardır. İsteğe bağlı olarak, dağıtımlarınızın el ile yürütme gerektirdiğinden tetikleyiciyi devre dışı bırakabilirsiniz. 


1. Sol tarafta **Görevler**' i seçin.   
Görevler, dağıtım işleminizin gerçekleştirdiği etkinliklerdir. Bu örnekte, Azure App Service dağıtmak üzere bir görev oluşturulmuştur.


1. Sağ tarafta **sürümleri görüntüle**' yi seçin.  
Bu görünüm, sürümlerin geçmişini gösterir.

1. Sürümlerden birinin yanındaki üç nokta (...) simgesini seçin ve sonra **Aç**' ı seçin.  
Bir yayın Özeti, ilişkili iş öğeleri ve testler gibi çeşitli birçok menü vardır.

1. **İşlemeler**' i seçin.   
Bu görünüm, belirli bir dağıtımla ilişkili kod işlemelerini gösterir.

1. **Günlükleri**seçin.  
Günlükler, dağıtım işlemiyle ilgili yararlı bilgiler içerir. Dağıtım sırasında ve sonrasında görüntülenebilirler.

## <a name="clean-up-resources"></a>Kaynakları Temizleme

Artık ihtiyaç duymadığınızda oluşturduğunuz Azure App Service ve diğer ilgili kaynakları silebilirsiniz. DevOps Projeleri panosundaki **silme** işlevini kullanın.


## <a name="next-steps"></a>Sonraki adımlar

CI/CD işleminizi yapılandırdığınızda, derleme ve yayın işlem hatları otomatik olarak oluşturulmuştur. Bu derleme ve yayın işlem hatlarını takımınızın ihtiyaçlarını karşılayacak şekilde değiştirebilirsiniz. CI/CD işlem hattı hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [CD işlemini özelleştirme](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Videolar

> [!VIDEO https://www.youtube.com/embed/3etwjubReJs]
