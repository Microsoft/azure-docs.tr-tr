---
title: 'Hızlı başlangıç: Azure DevOps Starter ile Python için bir CI/CD işlem hattı oluşturma'
description: DevOps Starter, Azure 'u kullanmaya başlamanızı kolaylaştırır. Birkaç hızlı adımda, tercih ettiğiniz bir Azure hizmetinde uygulama başlatmanıza yardımcı olur.
services: vsts
documentationcenter: vs-devops-build
author: mlearned
ms.author: mlearned
manager: gwallace
ms.date: 03/24/2020
ms.prod: devops
ms.technology: devops-cicd
ms.topic: quickstart
ms.workload: web
ms.tgt_pltfrm: na
ms.custom:
- mvc
- devx-track-python
- mode-api
ms.openlocfilehash: d236eadd4c1c3bbda5a781d00de43e4ed3dc88b9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533269"
---
# <a name="create-a-cicd-pipeline-for-python-with-azure-devops-starter"></a>Azure DevOps Starter ile Python için bir CI/CD işlem hattı oluşturma

Bu hızlı başlangıçta, Azure Pipelines ' de Python uygulamanız için sürekli tümleştirme (CI) ve sürekli teslim (CD) işlem hattı ayarlamak üzere Basitleştirilmiş Azure DevOps Starter deneyimini kullanırsınız. Uygulamanızı geliştirmek, dağıtmak ve izlemek için ihtiyacınız olan her şeyi ayarlamak için Azure DevOps Starter 'ı kullanabilirsiniz. 

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Bir [Azure DevOps](https://azure.microsoft.com/services/devops/) hesabı ve kuruluşu.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

DevOps Starter Azure Pipelines ' de bir CI/CD işlem hattı oluşturur. Yeni bir Azure DevOps kuruluşu oluşturabilir veya var olan bir kuruluşu kullanabilirsiniz. DevOps Starter Ayrıca seçtiğiniz Azure aboneliğindeki Azure kaynaklarını da oluşturur.

1. [Azure portalında](https://portal.azure.com) oturum açın. 

1. Arama kutusuna **DevOps Starter** yazın ve ardından öğesini seçin. Yeni bir tane oluşturmak için **Ekle** ' ye tıklayın.

    ![DevOps başlangıç panosu](_img/azure-devops-starter-aks/search-devops-starter.png) 

## <a name="select-a-sample-application-and-azure-service"></a>Örnek uygulama ve Azure hizmeti seçme

1. Python örnek uygulamasını seçin. Python örnekleri birkaç uygulama çerçevesi seçeneği içerir.

1. Varsayılan örnek çerçeve Django’dur. Varsayılan ayarı bırakın ve ardından **İleri**' yi seçin. Kapsayıcılar için Web App varsayılan dağıtım hedefidir. Daha önce seçtiğiniz uygulama çerçevesi, burada kullanılabilir olan Azure hizmet dağıtım hedefinin türünü belirler. 

3. Varsayılan hizmeti bırakın ve ardından **İleri**' yi seçin.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps ve bir Azure aboneliği yapılandırma 

1. Yeni bir Azure DevOps kuruluşu oluşturun veya var olan bir kuruluşu seçin. 

    1. Azure DevOps 'da projeniz için bir ad girin.  

    1. Azure aboneliğinizi ve konumunuzu seçin, uygulamanız için bir ad girin ve **bitti**' yi seçin.  
    
     Birkaç dakika sonra, başlangıç panosu Azure portal görüntülenir. Örnek uygulama, Azure DevOps kuruluşunuzda bir depoda ayarlanır, bir derleme yürütülür ve uygulamanız Azure 'a dağıtılır. Bu Pano, kod deponuza, CI/CD işlem hattınızla ve Azure 'daki uygulamanıza ilişkin görünürlük sağlar.  
    
2. Çalışan uygulamanızı görüntülemek için **Araştır** ' ı seçin.

    ![Pano görünümü](_img/azure-devops-project-python/dashboardnopreview.png) 
    
   DevOps Projeleri otomatik olarak bir CI derleme ve yayın tetikleyicisi yapılandırır. Artık en son çalışmalarınızı otomatik olarak Web sitenize dağıtan bir CI/CD işlemi kullanarak bir Python uygulamasında bir ekip ile işbirliği yapmaya hazırsınız.

## <a name="commit-code-changes-and-execute-cicd"></a>Kod değişikliklerini işleme ve CI/CD’yi yürütme

DevOps Starter, Azure Repos veya GitHub 'da bir git deposu oluşturur. Depoyu görüntülemek ve uygulamanızda kod değişikliği yapmak için aşağıdakileri yapın: 

1. DevOps başlangıç panosunun sol tarafında, ana dalınızın bağlantısını seçin. Bu bağlantı yeni oluşturulan Git deposuna bir görünüm açar.

1. Depo kopya URL'sini görüntülemek için tarayıcının sağ üst kısmından **Kopya**’yı seçin. Git deponuzu en sevdiğiniz IDE’de kopyalayabilirsiniz. Sonraki birkaç adımda, kod değişikliklerini doğrudan ana dala getirmek ve yürütmek için Web tarayıcısını kullanabilirsiniz.

1. Sol tarafta **App/Templates/App/index.html** dosyasına gidin.

1. **Düzenle**’yi seçin ve metnin bir kısmında değişiklik yapın. Örneğin, div etiketlerinden biri için metnin bir kısmını değiştirin.

1. **Yürüt**' ü seçin ve ardından değişikliklerinizi kaydedin.

1. Tarayıcınızda DevOps başlangıç panosuna gidin. Artık sürmekte olan bir derleme görmeniz gerekir. Yeni yaptığınız değişiklikler otomatik olarak bir CI/CD işlem hattı aracılığıyla oluşturulup dağıtılır.

## <a name="examine-the-cicd-pipeline"></a>CI/CD işlem hattını inceleyin

Önceki adımda, DevOps Starter otomatik olarak tam bir CI/CD işlem hattı yapılandırdı. İşlem hattını gerektiği şekilde keşfedin ve özelleştirin. Derleme ve yayın işlem hatları hakkında bilgi edinmek için aşağıdakileri yapın:

1. DevOps başlangıç panosunun en üstünde **derleme Işlem hatları**' nı seçin. Bir tarayıcı sekmesi, yeni projeniz için derleme işlem hattını görüntüler.

1. **Durum** alanını işaret edin ve ardından **üç nokta** (...) simgesini seçin. Bir menü, yeni bir derlemeyi sıraya alma, bir derlemeyi duraklatma ve derleme işlem hattını düzenlemeyle çeşitli seçenekleri görüntüler.

1. **Düzenle**'yi seçin.

1. Bu bölmede, derleme işlem hattınızla ilgili çeşitli görevleri inceleyebilirsiniz. Derleme git deposundan kaynak getirme, bağımlılıkları geri yükleme ve dağıtımlar için yayımlama çıkışları gibi çeşitli görevleri gerçekleştirir.

1. Derleme işlem hattının üst kısmında derleme işlem hattı adı’nı seçin.

1. Derleme işlem hattınızı daha açıklayıcı bir şekilde değiştirin, **& kuyruğu kaydet**' i seçin ve ardından **Kaydet**' i seçin.

1. Derleme işlem hattı adınızın altında **Geçmiş**’i seçin. Derleme için yaptığınız son değişikliklere ait denetim kaydını görürsünüz. Azure DevOps, derleme ardışık düzeninde yapılan tüm değişiklikleri izler ve sürümleri karşılaştırmanızı sağlar.

1. **Tetikleyiciler**’i seçin. DevOps Starter otomatik olarak bir CI tetikleyicisi oluşturur ve depoya yapılan her işlem yeni bir derleme başlatır. İsteğe bağlı olarak dalları CI işlemine dahil etmeyi veya işlemden hariç tutmayı seçebilirsiniz.

1. **Saklama**’yı seçin. Senaryonuza bağlı olarak, belirli sayıda derlemeyi tutmanın veya kaldırabilmeniz için ilkeler belirtebilirsiniz.

1. **Oluştur ve Yayınla**' yı seçin ve ardından **yayınlar**' ı seçin.   
 DevOps Projeleri, Azure dağıtımlarını yönetmek için bir yayın işlem hattı oluşturur.

1. Yayın işlem hattının yanındaki üç noktayı seçin ve ardından **Düzenle**' yi seçin. Yayın işlem hattı yayın işlemini tanımlar.  
        
12. **Yapıtlar**’ın altında **Bırak**’ı seçin. Önceki adımlarda inceettiğiniz derleme işlem hattı, yapıt için kullanılan çıktıyı üretir. 

1. **Bırakma** simgesinin yanındaki **sürekli dağıtım tetikleyicisi**' ni seçin. Yayın ardışık düzeni, kullanılabilir yeni bir yapı yapıtı her seferinde bir dağıtım çalıştıran etkinleştirilmiş bir CD tetikleyicisine sahiptir. İsteğe bağlı olarak, dağıtımlarınızın el ile yürütme gerektirdiğinden tetikleyiciyi devre dışı bırakabilirsiniz. 

1. Sol tarafta **Görevler**' i seçin. Görevler, dağıtım işleminizin gerçekleştirdiği etkinliklerdir. Bu örnekte, Azure App Service dağıtmak üzere bir görev oluşturulmuştur.

1. Sağ tarafta, sürümlerin geçmişini görüntülemek için **sürümleri görüntüle** ' yi seçin.  
        
1. Sürümlerden birinin yanındaki üç nokta (...) simgesini seçin ve sonra **Aç**' ı seçin. Bu görünümden, bir yayın Özeti, ilişkili iş öğeleri ve testler gibi çeşitli menüler araştırılacak.

1. **İşlemeler**'i seçin. Bu görünüm, belirli bir dağıtımla ilişkili kod işlemelerini gösterir. 

1. **Günlükleri** seçin. Günlüklerde, dağıtım işlemiyle ilgili yararlı bilgiler bulunur. Bunları, dağıtımları sırasında ve sonrasında görüntüleyebilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık ihtiyaç duymadığınızda Azure App Service ve ilgili kaynakları silebilirsiniz. DevOps başlangıç panosundaki **silme** işlevini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

CI/CD işleminizi yapılandırdığınızda, derleme ve yayın işlem hatları otomatik olarak oluşturulmuştur. Ekibinizin ihtiyaçlarını karşılamak için bu derleme ve yayın işlem hatlarını değiştirebilirsiniz. CI/CD işlem hattı hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [CD işlemini özelleştirme](/azure/devops/pipelines/release/define-multistage-release-process)
