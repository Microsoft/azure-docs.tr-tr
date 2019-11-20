---
title: 'Hızlı Başlangıç: Azure DevOps Projeleri ile Python için bir CI/CD işlem hattı oluşturma'
description: DevOps Projeleri Azure 'ı kullanmaya başlamanızı kolaylaştırır. Birkaç hızlı adımda, tercih ettiğiniz bir Azure hizmetinde uygulama başlatmanıza yardımcı olur.
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
ms.openlocfilehash: cb4e27aeada4f1b5f9e06279382327dcc51cc7dc
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899598"
---
# <a name="create-a-cicd-pipeline-for-python-with-azure-devops-projects"></a>Azure DevOps Projeleri ile Python için bir CI/CD işlem hattı oluşturma

Azure DevOps Projeleri, Azure kaynakları oluşturan ve Python uygulamanız için sürekli tümleştirme (CI) ve sürekli teslim (CD) işlem hattı ayarlayan basitleştirilmiş bir deneyim sunar.  

Bir Azure aboneliğiniz yoksa, [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)aracılığıyla ücretsiz olarak edinebilirsiniz.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

 DevOps Projeleri, Azure Pipelines bir CI/CD işlem hattı oluşturur.  Ücretsiz yeni bir Azure DevOps organizasyonu oluşturabilir veya var olan bir kuruluşu kullanabilirsiniz. DevOps projeleri, Azure kaynaklarını da tercih ettiğiniz bir Azure aboneliği oluşturur.

1. [Microsoft Azure portalında](https://portal.azure.com) oturum açın.

2. Sol bölmedeki **kaynak oluştur** simgesini seçin ve ardından **DevOps Projeleri**arayın.  

3. **Oluştur**’u seçin.

    ![Sürekli Teslim yapılandırmasını başlatma](_img/azure-devops-project-python/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Örnek uygulama ve Azure hizmeti seçme

1. Python örnek uygulamasını seçin. Python örnekleri birkaç uygulama çerçevesi seçeneği içerir.

1. Varsayılan örnek Framework Docgo ' dır. Varsayılan ayarı bırakın ve ardından **İleri**' yi seçin.    
Kapsayıcılar Için Web App varsayılan dağıtım hedefidir. Daha önce seçtiğiniz uygulama çerçevesi, burada kullanılabilir olan Azure hizmet dağıtım hedefinin türünü belirler. 

3. Varsayılan hizmeti bırakın ve ardından **İleri**' yi seçin.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps ve Azure aboneliğinin yapılandırın 

1. Yeni bir Azure DevOps organizasyonu oluşturun veya var olan bir kuruluşu seçin. 

    a. Azure DevOps 'da projeniz için bir ad girin.  

    b. Azure aboneliğinizi ve konumunuzu seçin, uygulamanız için bir ad girin ve **bitti**' yi seçin.  
     Birkaç dakika sonra proje panosu Azure portal görüntülenir. Örnek uygulama, Azure DevOps kuruluşunuzda bir depoda ayarlanır, bir derleme yürütülür ve uygulamanız Azure 'a dağıtılır. Bu Pano, kod deponuza, CI/CD işlem hattınızla ve Azure 'daki uygulamanıza ilişkin görünürlük sağlar.  
    
2. Çalışan uygulamanızı görüntülemek için **Araştır** ' ı seçin.

    ![Pano görünümü](_img/azure-devops-project-python/dashboardnopreview.png) 
    
   DevOps Projeleri otomatik olarak bir CI derleme ve yayın tetikleyicisi yapılandırır. Artık en son çalışmalarınızı otomatik olarak Web sitenize dağıtan bir CI/CD işlemi kullanarak bir Python uygulamasında bir ekip ile işbirliği yapmaya hazırsınız.

## <a name="commit-code-changes-and-execute-cicd"></a>Kod değişikliklerini işleme ve CI/CD’yi yürütme

 DevOps Projeleri, Azure Repos veya GitHub 'da bir git deposu oluşturur. Depoyu görüntülemek ve uygulamanızda kod değişikliği yapmak için aşağıdakileri yapın: 

1. DevOps Projeleri panosunun sol tarafında, ana dalınızın bağlantısını seçin.  
        Bu bağlantı yeni oluşturulan Git deposuna bir görünüm açar.

1. Depo kopya URL'sini görüntülemek için tarayıcının sağ üst kısmından **Kopya**’yı seçin.   
Git deponuzu en sevdiğiniz IDE’de kopyalayabilirsiniz.  Sonraki birkaç adımda, kod değişiklikleri yapıp doğrudan ana dala işlemek için web tarayıcısını kullanabilirsiniz.

1. Sol tarafta **App/Templates/App/index.html** dosyasına gidin.

1. **Düzenle**’yi seçin ve metnin bir kısmında değişiklik yapın. Örneğin, div etiketlerinden biri için metnin bir kısmını değiştirin.

1. Seçin **işleme**ve ardından değişikliklerinizi kaydedin.

1. Tarayıcınızda DevOps Projeleri panosuna gidin.   
    Artık sürmekte olan bir derleme görmeniz gerekir. Yeni yaptığınız değişiklikler otomatik olarak bir CI/CD işlem hattı aracılığıyla oluşturulup dağıtılır.

## <a name="examine-the-cicd-pipeline"></a>CI/CD işlem hattı inceleyin

Önceki adımda, DevOps Projeleri otomatik olarak tam CI/CD işlem hattı yapılandırdı. İşlem hattını gerektiği şekilde keşfedin ve özelleştirin. Derleme ve yayın işlem hatları hakkında bilgi edinmek için aşağıdakileri yapın:

1. DevOps projeleri panonun üst kısmında seçin **derleme işlem hatlarını**.  
Bir tarayıcı sekmesi, yeni projeniz için derleme işlem hattını görüntüler.

1. **Durum** alanını işaret edin ve ardından **üç nokta** (...) simgesini seçin.  
        Bir menü, yeni bir derlemeyi sıraya alma, bir derlemeyi duraklatma ve derleme işlem hattını düzenlemeyle çeşitli seçenekleri görüntüler.

1. **Düzenle**’yi seçin.

1. Bu bölmede, derleme işlem hattı için çeşitli görevleri inceleyebilirsiniz.  
        Derleme git deposundan kaynak getirme, bağımlılıkları geri yükleme ve dağıtımlar için yayımlama çıkışları gibi çeşitli görevleri gerçekleştirir.

1. Derleme işlem hattı üstünde derleme işlem hattı adı seçin.

1. Bir şeyler daha açıklayıcı, select, derleme işlem hattı adını değiştirmek **Kaydet ve kuyruğa**ve ardından **Kaydet**.

1. Derleme işlem hattı adınızın altında **Geçmiş**’i seçin.  
        Derleme için yaptığınız son değişikliklere ait denetim kaydını görürsünüz.  Azure DevOps, derleme ardışık düzeninde yapılan tüm değişiklikleri izler ve sürümleri karşılaştırmanızı sağlar.

1. **Tetikleyiciler**’i seçin.  
         DevOps Projeleri otomatik olarak bir CI tetikleyicisi oluşturur ve depoya yapılan her bir kayıt yeni bir derleme başlatır.  İsteğe bağlı olarak dalları CI işlemine dahil etmeyi veya işlemden hariç tutmayı seçebilirsiniz.

1. **Saklama**’yı seçin.  
        Senaryonuza bağlı olarak, saklamak veya belirli bir sayıda derlemeleri kaldırmak için ilkeleri belirtebilirsiniz.

1. **Oluştur ve Yayınla**' yı seçin ve ardından **yayınlar**' ı seçin.   
 DevOps Projeleri, Azure dağıtımlarını yönetmek için bir yayın işlem hattı oluşturur.

1. Yayın işlem hattının yanındaki üç noktayı seçin ve ardından **Düzenle**' yi seçin.  
Yayın işlem hattı yayın işlemini tanımlar.  
        
12. **Yapıtlar**’ın altında **Bırak**’ı seçin.   
Önceki adımlarda inceettiğiniz derleme işlem hattı, yapıt için kullanılan çıktıyı üretir. 

1. Yanındaki **bırak** simgesini seçme **sürekli dağıtım tetikleyicisi**.  
        Yayın ardışık düzeni, kullanılabilir yeni bir yapı yapıtı her seferinde bir dağıtım çalıştıran etkinleştirilmiş bir CD tetikleyicisine sahiptir. İsteğe bağlı olarak, el ile yürütme dağıtımlarınızı gerektirir böylece tetikleyiciyi devre dışı bırakabilirsiniz. 

1. Sol tarafta, seçin **görevleri**.   
Dağıtım işleminizin gerçekleştiren etkinlikler görevlerdir. Bu örnekte, Azure App Service dağıtmak üzere bir görev oluşturulmuştur.

1. Sağ tarafta, sürümlerin geçmişini görüntülemek için **sürümleri görüntüle** ' yi seçin.  
        
1. Sürümlerinizin birini yanındaki üç nokta (...) seçin ve ardından **açık**.  
        Bu görünümden, bir yayın Özeti, ilişkili iş öğeleri ve testler gibi çeşitli menüler araştırılacak.

1. **İşlemeler**'i seçin. 
        Bu görünüm, belirli bir dağıtımla ilişkili kod işlemelerini gösterir. 

1. **Günlükler**’i seçin.   
Günlüklerde, dağıtım işlemiyle ilgili yararlı bilgiler bulunur. Bunları, dağıtımları sırasında ve sonrasında görüntüleyebilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık ihtiyaç duymadığınızda Azure App Service ve ilgili kaynakları silebilirsiniz. Kullanım **Sil** DevOps projeleri Pano işlevselliği.

## <a name="next-steps"></a>Sonraki adımlar

CI/CD işleminizi yapılandırdığınızda, derleme ve yayın işlem hatları otomatik olarak oluşturulmuştur. Ekibinizin ihtiyaçlarını karşılamak için bu derleme ve yayın işlem hatlarını değiştirebilirsiniz. CI/CD işlem hattı hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [CD işlemini özelleştirme](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
