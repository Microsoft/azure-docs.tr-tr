---
title: 'Öğretici: Azure DevOps Starter ile Azure Işlevlerine ASP.NET uygulamalarını dağıtma'
description: Azure DevOps Starter, Azure 'u kullanmaya başlamanızı kolaylaştırır. DevOps Starter ile, birkaç hızlı adımda ASP.NET uygulamanızı Azure Işlevlerine dağıtabilirsiniz.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: devx-track-csharp
ms.openlocfilehash: 3277fefb5615fd492b2859b741ef8e6f991eaf23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91355483"
---
# <a name="deploy-to-azure-functions-with-devops-starter"></a>DevOps Starter ile Azure Işlevlerine dağıtma

Azure DevOps Starter, Azure 'da sürekli tümleştirme (CI) ve sürekli teslim (CD) işlem hattı oluşturmak için mevcut kodunuzu ve git deponuzu getirebileceğiniz veya örnek bir uygulama seçebileceğiniz basitleştirilmiş bir deneyim sunar.

DevOps Starter ayrıca:

* Azure Işlevleri gibi Azure kaynaklarını otomatik olarak oluşturur

* CI/CD için Azure DevOps 'da bir yayın işlem hattı oluşturur ve yapılandırır

Bu öğreticide şunları yapacaksınız:

> [!div class="checklist"]
>* ASP.NET uygulamasını Azure Işlevine dağıtmak için DevOps Starter 'ı kullanma
>* Azure DevOps ve bir Azure aboneliği yapılandırma
>* Azure Işlevini inceleyin
>* CI işlem hattını inceleme
>* CD işlem hattını inceleme
>* Git 'e değişiklikleri işleyin ve Azure 'a otomatik olarak dağıtın
>* Kaynakları temizleme

Şu anda işlevler için desteklenen çalışma zamanları **.net** ve **Node.js**. Kullanıyoruz. Bu öğreticinin Azure Işlevlerine dağıtılması için NET çalışma zamanı. 

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) aracılığıyla bir ücretsiz edinebilirsiniz

## <a name="use-devops-starter-to-deploy-an-aspnet-app-to-azure-functions"></a>Azure Işlevlerine bir ASP.NET uygulaması dağıtmak için DevOps Starter 'ı kullanma

DevOps Starter Azure Pipelines ' de bir CI/CD işlem hattı oluşturur. Yeni bir Azure DevOps kuruluşu oluşturabilir veya var olan bir kuruluşu kullanabilirsiniz. DevOps Projeleri ayrıca, tercih ettiğiniz Azure aboneliğinde bir ıothub gibi Azure kaynakları da oluşturur.

1. [Azure portalda](https://portal.azure.com) oturum açma

1. Arama kutusuna **DevOps Starter** yazın ve ardından öğesini seçin. Yeni bir tane oluşturmak için **Ekle** ' ye tıklayın.

    ![DevOps başlangıç panosu](_img/azure-devops-starter-aks/search-devops-starter.png)

1. **.Net**' i seçin ve ardından **İleri**' yi seçin. **Uygulama çerçevesi seçin** altında **ASP.net** ' ı seçin ve **İleri**' ye tıklayın.

1. **İşlev uygulaması** seçip **İleri**' yi seçin.

## <a name="configure-azure-devops-and-azure-subscription"></a>Azure DevOps ve Azure aboneliğini yapılandırma

1. Azure DevOps projeniz için bir ad girin.

1. Yeni bir Azure DevOps organizasyonu oluşturun veya var olan bir kuruluşu seçin.

1. Azure aboneliğinizi seçin.

1. Ek Azure yapılandırma ayarlarını görüntülemek ve fiyatlandırma katmanını ve konumunu tanımlamak için **ek ayarlar**' a tıklayın. Bu bölmede, fiyatlandırma katmanını ve Azure hizmetlerinin konumunu yapılandırmaya yönelik çeşitli seçenekler görüntülenir.

1. Azure yapılandırma alanından çıkıp **bitti**' yi seçin.

1. Birkaç dakika sonra işlem tamamlanır. Azure DevOps kuruluşunuzda bir git deposunda bir örnek ASP.NET uygulaması ayarlanır, bir İşlev Uygulaması ve Application Insights oluşturulur, bir CI/CD işlem hattı yürütülür ve uygulamanız Azure 'a dağıtılır.

   Bu işlem tamamlandıktan sonra Azure DevOps başlangıç panosu Azure portal görüntülenir. Ayrıca, doğrudan Azure portal DevOps **Başlangıç panosuna** da gidebilirsiniz.

   Bu Pano, Azure DevOps kod deponuza, CI/CD işlem hattınızla ve Azure Işlevinizde görünürlük sağlar. Azure DevOps işlem hattınızda ek CI/CD seçenekleri yapılandırabilirsiniz. Sağ tarafta, görüntülemek için **işlev uygulaması** seçin.

## <a name="examine-the-function-app"></a>İşlev Uygulaması inceleyin

DevOps Starter, keşfedebileceğiniz ve özelleştirebileceğiniz işlev uygulamasını otomatik olarak yapılandırır. İşlev uygulamasını bildirmek için aşağıdakileri yapın:

1. DevOps başlangıç panosuna gidin.

    ![DevOps Projeleri panosu](_img/azure-devops-project-functions/fapp-dashboard.png)

1. Sağ tarafta, işlev uygulamasını seçin. İşlev uygulaması için bir bölme açılır. Bu görünümden, işlem izleme, günlükleri arama gibi çeşitli eylemler gerçekleştirebilirsiniz.

    ![İşlev Uygulaması](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>CI işlem hattını inceleme

DevOps Starter, Azure DevOps kuruluşunuzda bir CI/CD işlem hattını otomatik olarak yapılandırır. İşlem hattını inceleyebilir ve özelleştirebilirsiniz. Bunu öğrenmek için aşağıdakileri yapın:

1. DevOps başlangıç panosuna gidin.

1. **Build** altındaki köprüye tıklayın. Bir tarayıcı sekmesi, yeni projeniz için derleme işlem hattını görüntüler.

    ![Ekran görüntüsünde, C ı/C D işlem hattındaki Build altındaki bir bağlantıya işaret eden bir ok ile Azure portal DevOps başlangıç panosu gösterilmektedir.](_img/azure-devops-project-functions/build.png)

1. **Düzenle**'yi seçin. Bu bölmede, derleme işlem hattınızla ilgili çeşitli görevleri inceleyebilirsiniz. Derleme, git deposundan kaynak kodu getirme, uygulamayı oluşturma, birim testlerini çalıştırma ve dağıtımlar için kullanılan yayınlama çıkışları gibi çeşitli görevleri gerçekleştirir.

1. **Tetikleyiciler**’i seçin. DevOps Starter otomatik olarak bir CI tetikleyicisi oluşturur ve depoya yapılan her bir işleme yeni bir derleme başlatır. İsteğe bağlı olarak, CI işlemindeki dalları dahil etmek veya hariç tutmak seçebilirsiniz.

1. **Saklama**’yı seçin. Senaryonuza bağlı olarak, belirli sayıda derlemeyi tutmanın veya kaldırabilmeniz için ilkeler belirtebilirsiniz.

1. Derleme işlem hattının üst kısmında derleme işlem hattı adı’nı seçin.

1. Derleme işlem hattınızı daha açıklayıcı bir şekilde değiştirin ve ardından **& kuyruğu kaydet** açılan listesinden **Kaydet** ' i seçin.

1. Derleme işlem hattı adınızın altında **Geçmiş**’i seçin. Bu bölme, derleme için son değişikliklerinizin denetim izini görüntüler. Azure DevOps, derleme ardışık düzeninde yapılan tüm değişiklikleri izler ve sürümleri karşılaştırmanızı sağlar.

## <a name="examine-the-cd-release-pipeline"></a>CD sürümü ardışık düzenini inceleyin

DevOps Starter, Azure DevOps kuruluşunuzdan Azure aboneliğinize dağıtım yapmak için gerekli adımları otomatik olarak oluşturur ve yapılandırır. Bu adımlar, Azure aboneliğinizdeki Azure DevOps kimlik doğrulaması için bir Azure hizmet bağlantısı yapılandırmayı içerir. Otomasyon Ayrıca CD 'yi Azure 'a sağlayan bir yayın işlem hattı oluşturur. Yayın işlem hattı hakkında daha fazla bilgi edinmek için aşağıdakileri yapın:

1. İşlem hatlarına gidin **| Yayınlar**.

1. **Düzenle**' ye tıklayın.

1. **Yapıtlar**’ın altında **Bırak**’ı seçin. Önceki adımlarda inceettiğiniz derleme işlem hattı, yapıt için kullanılan çıktıyı üretir.

1. **Bırakma** simgesinin sağ tarafında, **sürekli dağıtım tetikleyicisi**' ni seçin. Bu sürüm ardışık düzeninde, her yeni derleme yapıtı kullanılabilir olduğunda bir dağıtımı yürüten etkinleştirilmiş bir CD tetikleyicisi vardır. İsteğe bağlı olarak, dağıtımlarınızın el ile yürütme gerektirdiğinden tetikleyiciyi devre dışı bırakabilirsiniz.

1. Sağ tarafta, sürümlerin geçmişini görüntülemek için **yayınları görüntüle** ' yi seçin.

1. İşlem hattını görüntüleyen sürüme tıklayın. Sürüm **özetini, Işlemeleri**, Ilişkili **iş öğelerini** denetlemek için herhangi bir ortama tıklayın.

1. **İşlemeler**'i seçin. Bu görünüm, bu dağıtımla ilişkili kod işlemelerini gösterir. Dağıtımlar arasındaki işleme farklılıklarını görmek için yayınları karşılaştırın.

1. **Günlükleri görüntüle**' yi seçin. Günlüklerde, dağıtım işlemiyle ilgili yararlı bilgiler bulunur. Bunları, dağıtımları sırasında ve sonrasında görüntüleyebilirsiniz.

## <a name="commit-code-changes-and-execute-cicd"></a>Kod değişikliklerini işleme ve CI/CD’yi yürütme

> [!NOTE]
> Aşağıdaki yordam, bir basit metin değişikliği yaparak CI/CD işlem hattını sınar.

Artık en son çalışmalarınızı Azure Işlevinizle otomatik olarak dağıtan bir CI/CD işlemi kullanarak uygulamanızdaki bir takımla işbirliği yapmaya hazırsınız. Git deposunda yapılan her değişiklik Azure DevOps 'da bir derleme başlatır ve bir CD işlem hattı Azure 'a bir dağıtım yürütür. Bu bölümdeki yordamı izleyin veya değişiklikleri deponuzda uygulamak için başka bir teknik kullanın. Örneğin, git deposunu en sevdiğiniz araçta veya IDE 'de kopyalayabilir ve sonra değişiklikleri bu depoya gönderebilirsiniz.

1. Azure DevOps menüsünde, Repos ' u seçin. **** Ve sonra depoya gidin.

1. Depo zaten, oluşturma işleminde seçtiğiniz uygulama diline bağlı olarak **Samplefunctionapp** adlı bir kod içeriyor. **Application/SampleFunctionApp/işlev1. cs** dosyasını açın.

1. **Düzenle**' yi seçin ve ardından **31 numaralı satırda** bir değişiklik yapın. Örneğin, bunu Merhaba olacak şekilde güncelleştirebilirsiniz **! DevOps Starter kullanarak Azure Işlevlerine hoş geldiniz**

1. Sağ üst köşedeki **Kaydet**' i seçin ve sonra değişikliklerinizi göndermek Için yeniden **Yürüt** ' ü seçin.

1. **Application/SampleFunctionApp. test/Function1TestRunner. cs** dosyasını açın. 

1. **Düzenle**' yi seçin ve **satır numarası 21**' de bir değişiklik yapın. Örneğin, bunu Merhaba olacak şekilde güncelleştirebilirsiniz **! Azure DevOps Starter kullanarak Azure Işlevlerine hoş geldiniz**.

     Birkaç dakika sonra, bir derleme Azure DevOps 'da başlar ve değişiklikleri dağıtmak için bir yayın yürütülür. Yapı durumunu DevOps başlangıç panosunda veya tarayıcıda Azure DevOps kuruluşunuzla izleyin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık ihtiyaç duymadığınızda oluşturduğunuz ilgili kaynakları silebilirsiniz. DevOps başlangıç panosundaki **silme** işlevini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Ekibinizin gereksinimlerine uygun olarak bu derleme ve yayın işlem hatlarını istediğiniz gibi değiştirebilirsiniz. Ayrıca bu CI/CD desenini diğer işlem hatlarınızda şablon olarak kullanabilirsiniz. Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Azure Işlevine ASP.NET Core uygulaması dağıtmak için DevOps Starter 'ı kullanma
> * Azure DevOps ve bir Azure aboneliği yapılandırma 
> * Azure Işlevini inceleyin
> * CI işlem hattını inceleme
> * CD işlem hattını inceleme
> * Git 'e değişiklikleri işleyin ve Azure 'a otomatik olarak dağıtın
> * Kaynakları temizleme

