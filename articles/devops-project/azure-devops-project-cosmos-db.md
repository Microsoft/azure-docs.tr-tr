---
title: 'Öğretici: Azure DevOps Projeleri ile Azure Cosmos DB tarafından desteklenen Node. js uygulamalarını dağıtma'
description: Azure DevOps Projeleri Azure 'u kullanmaya başlamanızı kolaylaştırır. DevOps Projeleri ile, birkaç hızlı adımda Windows Web App 'e Azure Cosmos DB desteklenen Node. js uygulamanızı dağıtabilirsiniz.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/11/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 229b4b9f53ea3866dce1169645f6d6da20827271
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888899"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-projects"></a>DevOps Projeleri ile Azure Cosmos DB tarafından desteklenen Node. js uygulamalarını dağıtın

Azure DevOps Projeleri, Azure 'da sürekli tümleştirme (CI) ve sürekli dağıtım (CD) işlem hattı oluşturabileceğiniz kolaylaştırılmış bir deneyim sunar. Bunu, mevcut kodunuzu ve git deponuzu (Depoyu) kullanarak veya örnek bir uygulama seçerek yapabilirsiniz.

Ayrıca DevOps Projeleri:

* Azure Cosmos DB, Azure Application Insights, Azure App Service ve App Service planları gibi Azure kaynaklarını otomatik olarak oluşturur

* Azure DevOps 'da bir CI/CD yayın işlem hattı oluşturur ve yapılandırır

Bu öğreticide şunları yapacaksınız:

> [!div class="checklist"]
> * Azure Cosmos DB tarafından desteklenen bir Node. js uygulamasını dağıtmak için DevOps Projeleri kullanın
> * Azure DevOps ve bir Azure aboneliği yapılandırma
> * Azure Cosmos DB inceleyin
> * CI işlem hattını inceleme
> * CD işlem hattını inceleme
> * Değişiklikleri git 'e işleyin ve Azure 'a otomatik olarak dağıtın
> * Kaynakları Temizleme

## <a name="prerequisites"></a>Ön koşullar

[Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) ücretsiz olarak alabileceğiniz bir Azure aboneliğine ihtiyacınız vardır.

## <a name="use-devops-projects-to-deploy-nodejs-app"></a>Node. js uygulamasını dağıtmak için DevOps Projeleri kullanma

DevOps Projeleri, Azure Pipelines bir CI/CD işlem hattı oluşturur. Yeni bir Azure DevOps kuruluşu oluşturabilir veya var olan bir kuruluşu kullanabilirsiniz. DevOps Projeleri ayrıca, tercih ettiğiniz Azure aboneliğinde Azure Cosmos DB, Application Insights, App Service ve App Service planları gibi Azure kaynakları da oluşturur.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Sol bölmede **kaynak oluştur**' u seçin.

1. Arama kutusuna **DevOps Projeleri**girin ve ardından **Ekle**' yi seçin.

   ![DevOps Projeleri bölmesi](_img/azure-devops-project-cosmos-db/devops-project.png)

1. Çalışma zamanı olarak **Node. js** ' yi seçin ve ardından **İleri**' yi seçin. **Uygulama çerçevesi seçin**altında **Express. js**' yi seçin.

1. **Cosmos DB**Için **veritabanı ekleme** bölümünü etkinleştirin ve sonra **İleri**' yi seçin.

    ![Veritabanı Ekle](_img/azure-devops-project-cosmos-db/add-database.png)

    Azure DevOps Projeleri, **Express. js**, **örnek Node. js uygulaması**ve **SAIL. js**gibi çeşitli uygulama çerçevelerini destekler. Bu öğreticide **Express. js**kullanıyoruz.

1. Uygulamayı dağıtmak için bir Azure hizmeti seçin ve ardından **İleri**' yi seçin. Seçenekleriniz Windows Web uygulaması, Azure Kubernetes hizmeti ve Azure Kapsayıcılar için Web App içerir. Bu öğreticide, **Windows Web App**kullanıyoruz.

## <a name="configure-azure-devops-and-azure-subscription"></a>Azure DevOps ve Azure aboneliğini yapılandırma

1. Azure DevOps projeniz için bir ad girin.

1. Yeni bir Azure DevOps organizasyonu oluşturun veya var olan bir kuruluşu seçin.

1. Azure aboneliğinizi seçin.

1. Ek Azure yapılandırma ayarlarını görüntülemek veya fiyatlandırma katmanını ve konumunu tanımlamak için **ek ayarlar**' ı seçin. Bu bölmede, fiyatlandırma katmanını ve Azure hizmetlerinin konumunu yapılandırmaya yönelik çeşitli seçenekler gösterilir.

1. Azure yapılandırma alanından çıkıp **bitti**' yi seçin.

1. İşlem birkaç dakika sonra tamamlanır. Azure DevOps kuruluşunuzda bir git deposunda örnek Node. js uygulaması ayarlanır. Daha sonra, Azure Cosmos DB, App Service, App Service planı ve Application Insights kaynakların yanı sıra CI/CD işlem hattı da oluşturulur. Uygulamanız daha sonra Azure 'a dağıtılır.

   Tüm bu işlemler bittikten sonra, Azure DevOps proje panosu Azure portal görüntülenir. Ayrıca, artık Azure portal **tüm kaynaklardan** DevOps Projeleri panosuna gidebilirsiniz.

   Bu Pano, Azure DevOps kod deponuza, CI/CD işlem hattınızla ve Azure Cosmos DB veritabanınıza yönelik görünürlük sağlar. Azure DevOps işlem hattınızda ek CI/CD seçenekleri yapılandırabilirsiniz. Panonun sağ tarafında, bu seçenekleri görüntülemek için **Azure Cosmos DB** ' yi seçin.

## <a name="examine-azure-cosmos-db"></a>Azure Cosmos DB inceleyin

DevOps Projeleri Azure Cosmos DB otomatik olarak yapılandırır, bu, keşfedebileceğiniz ve özelleştirebileceğiniz. Azure Cosmos DB hakkında bilgi edinmek için aşağıdakileri yapın:

1. DevOps Projeleri panosuna gidin.

    ![DevOps Projeleri panosu](_img/azure-devops-project-cosmos-db/devops-project-dashboard.png)

1. Sağ tarafta Azure Cosmos DB ' yi seçin. Azure Cosmos DB için bir bölme açılır. Bu görünümden, işlemleri izleme ve günlükleri arama gibi çeşitli eylemler gerçekleştirebilirsiniz.

    ![Azure Cosmos DB bölmesi](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>CI işlem hattını inceleme

DevOps Projeleri, Azure DevOps kuruluşunuzda bir CI/CD işlem hattını otomatik olarak yapılandırır. İşlem hattını inceleyebilir ve özelleştirebilirsiniz. Bunu öğrenmek için aşağıdakileri yapın:

1. DevOps Projeleri panosuna gidin.

1. **Build**altındaki köprüyü seçin. Bir tarayıcı sekmesi, yeni projeniz için derleme işlem hattını görüntüler.

    ![Derleme bölmesi](_img/azure-devops-project-cosmos-db/build.png)

1. **Düzenle**’yi seçin. Bu bölmede, derleme işlem hattınızla ilgili çeşitli görevleri inceleyebilirsiniz. Derleme, git deposundan kaynak kodu getirme, uygulamayı oluşturma, birim testlerini çalıştırma ve dağıtımlar için kullanılan yayınlama çıkışları gibi çeşitli görevleri gerçekleştirir.

1. **Tetikleyiciler**’i seçin. DevOps Projeleri otomatik olarak bir CI tetikleyicisi oluşturur ve depoya yapılan her bir işleme yeni bir derleme başlatır. CI işlemindeki dalları dahil etmek veya hariç tutmak seçebilirsiniz.

1. **Saklama**’yı seçin. Senaryonuza bağlı olarak, belirli sayıda derlemeyi tutmanın veya kaldırabilmeniz için ilkeler belirtebilirsiniz.

1. Yapı işlem hattının en üstünde derleme işlem hattı adını seçin.

1. Derleme işlem hattınızı daha açıklayıcı bir şekilde değiştirin ve ardından **& kuyruğu kaydet** açılan listesinden **Kaydet** ' i seçin.

1. Derleme işlem hattı adınızın altında **Geçmiş**’i seçin. Bu bölme, derleme için son değişikliklerinizin denetim izini görüntüler. Azure DevOps, derleme ardışık düzeninde yapılan tüm değişiklikleri izler ve sürümleri karşılaştırmanızı sağlar.

## <a name="examine-the-cd-release-pipeline"></a>CD sürümü ardışık düzenini inceleyin

DevOps Projeleri, Azure DevOps kuruluşunuzdan Azure aboneliğinize dağıtmak için gerekli adımları otomatik olarak oluşturur ve yapılandırır. Bu adımlar, Azure aboneliğinizdeki Azure DevOps kimlik doğrulaması için bir Azure hizmet bağlantısı yapılandırmayı içerir. Otomasyon Ayrıca CD 'yi Azure 'a sağlayan bir yayın işlem hattı oluşturur. Yayın işlem hattı hakkında daha fazla bilgi edinmek için aşağıdakileri yapın:

1. İşlem **hatları** ' na gidin ve **yayınlar**' ı seçin.

1. **Düzenle**’yi seçin.

1. **Yapıtlar**’ın altında **Bırak**’ı seçin. Önceki adımlarda inceettiğiniz derleme işlem hattı, yapıt için kullanılan çıktıyı üretir.

1. **Bırakma** simgesinin sağında **sürekli dağıtım tetikleyicisi**' ni seçin. Bu yayın işlem hattı, her yeni derleme yapısı kullanılabilir olduğunda bir dağıtımı yürüten sürekli dağıtım tetikleyicisini etkinleştirdi. Dağıtımlarınızın el ile yürütülmesi için tetikleyiciyi devre dışı bırakabilirsiniz.

1. Sağ tarafta, sürümlerin geçmişini görüntülemek için bölüm **görünümü** ' nü seçin.

1. İşlem hattını görüntüleyen yayını seçin. Sürüm özetini, yürütmeleri veya ilişkili iş öğelerini denetlemek için herhangi bir ortam seçin.

1. **İşlemeler**'i seçin. Bu görünüm, bu dağıtımla ilişkili kod işlemelerini gösterir. Dağıtımlar arasındaki işleme farklılıklarını görmek için yayınları karşılaştırın.

1. **Günlükleri görüntüle**' yi seçin. Günlüklerde, dağıtım işlemiyle ilgili yararlı bilgiler bulunur. Bunları, dağıtımları sırasında ve sonrasında görüntüleyebilirsiniz.

## <a name="commit-code-changes-and-execute-the-cicd-pipeline"></a>Kod değişikliklerini işleme ve CI/CD işlem hattını yürütme

> [!NOTE]
> Aşağıdaki yordam, bir basit metin değişikliği yaparak CI/CD işlem hattını sınar.

Artık App Service en son çalışmalarınızı dağıtan bir CI/CD işlemi kullanarak uygulamanızdaki bir takımla işbirliği yapmaya hazırsınız. Git deposunda yapılan her değişiklik Azure DevOps 'da bir derleme başlatır ve bir CD işlem hattı Azure 'a bir dağıtım yürütür. Bu bölümdeki yordamı izleyin veya değişiklikleri deponuzda uygulamak için başka bir teknik kullanın. Örneğin, git deposunu en sevdiğiniz araçta veya IDE 'de kopyalayabilir ve sonra değişiklikleri bu depoya gönderebilirsiniz.

1. Azure DevOps menüsünde, **Repos** ve ardından **dosyalar**' ı seçin. Sonra depoya gidin.

1. Depo, oluşturma işleminde seçtiğiniz uygulama diline bağlı olarak zaten kod içeriyor. **Uygulama/görünümler/index. Pug** dosyasını açın.

1. **Düzenle**' yi seçin ve ardından **15 numaralı satırda**bir değişiklik yapın. Örneğin, bunu "Ilk dağıtımma Azure App Service Azure Cosmos DB tarafından desteklenen olarak değiştirebilirsiniz.

1. Sağ üst köşede, **Kaydet**' i seçin ve sonra da değişiklerinizi göndermek Için yeniden **Yürüt** ' ü seçin.

     Birkaç saniye sonra, bir derleme Azure DevOps 'da başlar ve değişiklikleri dağıtmak için bir yayın yürütülür. DevOps Projeleri panosunda veya Azure DevOps kuruluşunuzla tarayıcıda derleme durumunu izleyin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık ihtiyaç duymadığınızda oluşturduğunuz ilgili kaynakları silin. DevOps Projeleri panosundaki **silme** işlevini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Ekibinizin ihtiyaçlarını karşılamak için bu derleme ve yayın işlem hatlarını değiştirebilirsiniz. Ayrıca bu CI/CD desenini diğer işlem hatlarınızda şablon olarak kullanabilirsiniz. Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Azure Cosmos DB tarafından desteklenen bir Node. js uygulamasını dağıtmak için DevOps Projeleri kullanın
> * Azure DevOps ve bir Azure aboneliği yapılandırma 
> * Azure Cosmos DB inceleyin
> * CI işlem hattını inceleme
> * CD işlem hattını inceleme
> * Git 'e değişiklikleri işleyin ve Azure 'a otomatik olarak dağıtın
> * Kaynakları temizleme

Daha fazla bilgi ve sonraki adımlar için bkz. [Multi-Stage sürekli dağıtımı (CD) ardışık düzenini tanımlama](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=azure-devops&viewFallbackFrom=vsts) .


