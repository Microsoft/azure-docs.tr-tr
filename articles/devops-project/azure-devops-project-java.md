---
title: 'Hızlı başlangıç: Java için bir CI/CD işlem hattı oluşturma-Azure DevOps Starter'
description: Azure Pipelines ' de Java uygulamanız için sürekli tümleştirme (CI) ve sürekli teslim (CD) işlem hattı ayarlamak üzere Basitleştirilmiş Azure DevOps Starter deneyimini kullanmayı öğrenin.
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
- seo-java-july2019
- seo-java-august2019
- seo-java-september2019
- devx-track-java
- mode-api
ms.openlocfilehash: 30ee97e0928f7b2995bd7bf02bec15bb8a46f7e2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535427"
---
# <a name="set-up-a-cicd-pipeline-for-a-java-app-with-azure-devops-starter"></a>Azure DevOps Starter ile Java uygulaması için bir CI/CD işlem hattı ayarlama

Bu hızlı başlangıçta, Azure Pipelines ' de Java uygulamanız için sürekli tümleştirme (CI) ve sürekli teslim (CD) işlem hattı ayarlamak üzere Basitleştirilmiş Azure DevOps Starter deneyimini kullanırsınız. Uygulamanızı geliştirmek, dağıtmak ve izlemek için ihtiyacınız olan her şeyi ayarlamak için Azure DevOps Starter 'ı kullanabilirsiniz. 

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Bir [Azure DevOps](https://azure.microsoft.com/services/devops/) hesabı ve kuruluşu.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

DevOps Starter Azure Pipelines ' de bir CI/CD işlem hattı oluşturur. Yeni bir Azure DevOps kuruluşu oluşturabilir veya var olan bir kuruluşu kullanabilirsiniz. DevOps Starter Ayrıca seçtiğiniz Azure aboneliğindeki Azure kaynaklarını da oluşturur.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Arama kutusuna **DevOps Starter** yazın ve ardından öğesini seçin. Yeni bir tane oluşturmak için **Ekle** ' ye tıklayın.

    ![DevOps başlangıç panosu](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Örnek uygulama ve Azure hizmeti seçme

1. Java örnek uygulamasını seçin. Java örnekleri birkaç uygulama çerçevesi seçeneği içerir.

1. Varsayılan örnek çerçeve Spring’dir. Varsayılan ayarı bırakın ve ardından **İleri**' yi seçin.  Kapsayıcılar için Web App varsayılan dağıtım hedefidir. Daha önce seçtiğiniz uygulama çerçevesi, burada kullanılabilir olan Azure hizmet dağıtım hedefinin türünü belirler. 

2. Varsayılan hizmeti bırakın ve ardından **İleri**' yi seçin.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps ve bir Azure aboneliği yapılandırma 

1. Yeni bir Azure DevOps kuruluşu oluşturun veya var olan bir kuruluşu seçin. 
   
   1. Projeniz için bir ad seçin. 
   
   1. Azure aboneliğinizi ve konumunuzu seçin, uygulamanız için bir ad seçin ve **bitti**' yi seçin.  
   Birkaç dakika sonra DevOps başlangıç panosu Azure portal görüntülenir. Örnek uygulama, Azure DevOps kuruluşunuzda bir depoda ayarlanır, bir derleme yürütülür ve uygulamanız Azure 'a dağıtılır. Bu Pano, kod deponuza, CI/CD işlem hattına ve Azure 'daki uygulamanıza ilişkin görünürlük sağlar.
   
2. Çalışan uygulamanızı görüntülemek için **Araştır** ' ı seçin.
   
   ![Uygulama panosunu Azure portal görüntüle](_img/azure-devops-project-java/azure-devops-application-dashboard.png) 

DevOps Starter, otomatik olarak bir CI derleme ve yayın tetikleyicisi yapılandırdı.  Artık en son çalışmanızı otomatik olarak web sitenize dağıtan bir CI/CD işlemiyle Java uygulaması üzerinde bir ekiple birlikte çalışmaya hazırsınız.

## <a name="commit-code-changes-and-execute-cicd"></a>Kod değişikliklerini işleme ve CI/CD’yi yürütme

DevOps Starter, Azure Repos veya GitHub 'da bir git deposu oluşturur. Depoyu görüntülemek ve uygulamanızda kod değişikliği yapmak için aşağıdakileri yapın:

1. DevOps başlangıç panosunun sol tarafında, ana dalınızın bağlantısını seçin. Bu bağlantı yeni oluşturulan Git deposuna bir görünüm açar.

1. Depo kopyası URL 'sini görüntülemek için tarayıcının sağ üst köşesindeki **Kopyala** ' yı seçin. Git deponuzu en sevdiğiniz IDE’de kopyalayabilirsiniz. Sonraki birkaç adımda, kod değişikliklerini doğrudan ana dala getirmek ve yürütmek için Web tarayıcısını kullanabilirsiniz.

1. Tarayıcının sol tarafında **src/Main/WebApp/index.html** dosyasına gidin.

1. **Düzenle**' yi seçin ve sonra bazı metinden değişiklik yapın.
    Örneğin, div etiketlerinden biri için metnin bir kısmını değiştirin.

1. **Yürüt**' ü seçin ve ardından değişikliklerinizi kaydedin.

1. Tarayıcınızda DevOps başlangıç panosuna gidin.   
Artık sürmekte olan bir derleme görmeniz gerekir. Yeni yaptığınız değişiklikler otomatik olarak bir CI/CD işlem hattı aracılığıyla oluşturulup dağıtılır.

## <a name="examine-the-cicd-pipeline"></a>CI/CD işlem hattını inceleyin

 Önceki adımda, DevOps Starter otomatik olarak tam bir CI/CD işlem hattı yapılandırdı. İşlem hattını gerektiği şekilde keşfedin ve özelleştirin. Derleme ve yayın işlem hatları hakkında bilgi edinmek için aşağıdaki adımları uygulayın.

1. DevOps başlangıç panosunun en üstünde **derleme Işlem hatları**' nı seçin. Bu bağlantı, yeni projeniz için bir tarayıcı sekmesi ve derleme işlem hattı açar.

1. **Durum** alanını işaret edin ve ardından üç nokta (...) simgesini seçin. Bu eylem, yeni bir derlemeyi sıraya alma, bir derlemeyi duraklatma ve derleme işlem hattını düzenlemeyle çeşitli etkinlikleri başlatabileceğiniz bir menü açar.

1. **Düzenle**'yi seçin.

1. Bu bölmede, derleme işlem hattınızla ilgili çeşitli görevleri inceleyebilirsiniz. Derleme, git deposundan kaynak getirme, bağımlılıkları geri yükleme ve dağıtımlar için kullanılan çıkışları yayımlama gibi çeşitli görevler gerçekleştirir.

1. Derleme işlem hattının üst kısmında derleme işlem hattı adı’nı seçin.

1. Derleme işlem hattınızı daha açıklayıcı bir şekilde değiştirin, **& kuyruğu kaydet**' i seçin ve ardından **Kaydet**' i seçin.

1. Derleme işlem hattı adınızın altında **Geçmiş**’i seçin.   
**Geçmiş** bölmesinde, derleme için son değişikliklerinizin bir denetim izini görürsünüz.  Azure Pipelines, derleme ardışık düzeninde yapılan tüm değişiklikleri izler ve sürümleri karşılaştırmanızı sağlar.

1. **Tetikleyiciler**’i seçin.  DevOps Starter otomatik olarak bir CI tetikleyicisi oluşturdu ve depoya yapılan her işlem yeni bir derleme başlatır.  İsteğe bağlı olarak dalları CI işlemine dahil etmeyi veya işlemden hariç tutmayı seçebilirsiniz.

1. **Saklama**’yı seçin. Senaryonuza bağlı olarak, belirli sayıda derlemeyi tutmanın veya kaldırabilmeniz için ilkeler belirtebilirsiniz.

1. **Build ve Release**' i seçin ve ardından **yayınlar**' ı seçin.  
 DevOps Starter, Azure dağıtımlarını yönetmek için bir yayın işlem hattı oluşturur.

1. Sol tarafta, yayın işlem hattının yanındaki üç nokta (...) simgesini seçin ve ardından **Düzenle**' yi seçin. Yayın işlem hattı, yayın işlemini tanımlayan bir işlem hattı içerir.  
    
12. **Yapıtlar**’ın altında **Bırak**’ı seçin. Önceki adımlarda inceettiğiniz derleme işlem hattı, yapıt için kullanılan çıktıyı üretir. 

1. **Bırakma** simgesinin yanındaki **sürekli dağıtım tetikleyicisi**' ni seçin. Bu sürüm ardışık düzeninde, kullanılabilir yeni bir yapı yapıtı olduğunda bir dağıtımı çalıştıran etkinleştirilmiş bir CD tetikleyicisi vardır. İsteğe bağlı olarak, dağıtımlarınızın el ile yürütme gerektirdiğinden tetikleyiciyi devre dışı bırakabilirsiniz. 

1. Sol tarafta **Görevler**' i seçin. Görevler, dağıtım işleminizin gerçekleştirdiği etkinliklerdir. Bu örnekte, Azure App Service dağıtmak üzere bir görev oluşturulmuştur.

1. Sağ tarafta **sürümleri görüntüle**' yi seçin. Bu görünümde yayın geçmişi gösterilir.

1. Sürümlerden birinin yanındaki üç nokta (...) simgesini seçin ve sonra **Aç**' ı seçin. Bir yayın Özeti, ilişkili iş öğeleri ve testler gibi çeşitli birçok menü vardır.

1. **İşlemeler**'i seçin. Bu görünüm, belirli bir dağıtımla ilişkili kod işlemelerini gösterir. 

1. **Günlükleri** seçin. Günlüklerde, dağıtım işlemiyle ilgili yararlı bilgiler bulunur. Bunlar hem dağıtım sırasında hem de sonrasında görüntülenebilir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık ihtiyaç duymadığınızda, Azure App Service ve diğer ilgili kaynakları silebilirsiniz. DevOps başlangıç panosundaki **silme** işlevini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

CI/CD işleminizi yapılandırdığınızda, derleme ve yayın işlem hatları otomatik olarak oluşturulmuştur. Ekibinizin ihtiyaçlarını karşılamak için bu derleme ve yayın işlem hatlarını değiştirebilirsiniz. CI/CD işlem hattı hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [CD işlemini özelleştirme](/azure/devops/pipelines/release/define-multistage-release-process)
