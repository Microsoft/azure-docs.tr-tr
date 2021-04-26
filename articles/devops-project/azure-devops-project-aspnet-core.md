---
title: 'Hızlı başlangıç: Azure DevOps Starter ile .NET için bir CI/CD işlem hattı oluşturma'
description: Azure DevOps Starter, Azure 'u kullanmaya başlamanızı kolaylaştırır. Birkaç hızlı adımda, tercih ettiğiniz bir Azure hizmetinde .NET uygulaması başlatmanıza yardımcı olur.
ms.prod: devops
ms.technology: devops-cicd
services: azure-devops-project
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 02/23/2021
ms.author: mlearned
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 75531b9a75c2d28ad665748a9be4e37e2056b451
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102547977"
---
# <a name="create-a-cicd-pipeline-for-net-with-azure-devops-starter"></a>Azure DevOps Starter ile .NET için CI/CD işlem hattı oluşturma

DevOps Starter ile .NET Core veya ASP.NET uygulamanız için sürekli tümleştirme (CI) ve sürekli teslim (CD) yapılandırın. DevOps Starter, Azure Pipelines bir derleme ve serbest bırakma işlem hattının başlangıç yapılandırmasını basitleştirir.

Azure aboneliğiniz yoksa, [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) üzerinden ücretsiz edinebilirsiniz.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

DevOps Starter, Azure DevOps 'da bir CI/CD işlem hattı oluşturur. Yeni bir Azure DevOps kuruluşu oluşturabilir veya var olan bir kuruluşu kullanabilirsiniz. DevOps Starter Ayrıca seçtiğiniz Azure aboneliğindeki Azure kaynaklarını da oluşturur.

1. [Microsoft Azure portalda](https://portal.azure.com) oturum açın.

1. Arama kutusuna **DevOps Starter** yazın ve ardından öğesini seçin. Yeni bir tane oluşturmak için **Ekle** ' ye tıklayın. 

    ![DevOps başlangıç panosu](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Örnek uygulama ve Azure hizmeti seçme

1. **.NET** örnek uygulamasını seçin. .NET örnekleri açık kaynaklı ASP.NET çerçevesi ya da çoklu platform .NET Core çerçevesi seçeneklerinden birini içerir.

   ![.NET Framework](_img/azure-devops-project-aspnet-core/select-dotnet.png)
   
   > [!NOTE]
   > DevOps Starter 'ı ayarlamaya yönelik varsayılan seçenek **GitHub** ile yapılır, ancak bu ayar sihirbazdan değiştirilebilir.
2. Bu örnek bir ASP.NET Core MVC uygulamasıdır. **.NET Core** uygulama çerçevesini seçip **İleri**' yi seçin.    
    
3. Dağıtım hedefi olarak **Windows Web uygulaması** ' nı seçin ve ardından **İleri**' yi seçin. İsteğe bağlı olarak, dağıtımınız için diğer Azure hizmetlerini seçebilirsiniz. Daha önce seçtiğiniz uygulama çerçevesi, burada bulunan Azure hizmet dağıtım hedefinin türünü belirler.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps ve bir Azure aboneliği yapılandırma 

1. Bir **Proje adı** girin.

2. Yeni bir ücretsiz **Azure DevOps organizasyonu** oluşturun veya açılan listeden mevcut bir kuruluştan birini seçin.

3. **Azure aboneliğinizi** seçin, **Web uygulamanız** için bir ad girin veya varsayılan işlemi yapın ve **bitti**' yi seçin. Birkaç dakika sonra DevOps Başlatıcı dağıtımına genel bakış Azure portal görüntülenir. 

4. DevOps başlangıç panosunu görüntülemek için **Kaynağa Git** ' i seçin. Hızlı erişim için, sağ üst köşede **projeyi** panonuza sabitleyin. Örnek bir uygulama, **Azure DevOps kuruluşunuzda** bir depoda ayarlanır. Bir yapı yürütülür ve uygulamanız Azure 'a dağıtılır.

5. Pano, kod deponuzda, CI/CD işlem hattınızla ve Azure 'daki uygulamanız için görünürlük sağlar. Çalışan uygulamanızı görüntülemek için Azure kaynakları ' nın sağ tarafında bulunan **Araştır** ' ı seçin.

   ![Pano görünümü](_img/azure-devops-project-aspnet-core/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>Kod değişikliklerini işleme ve CI/CD’yi yürütme

DevOps Starter, Azure Repos veya GitHub 'da bir git deposu oluşturdu. Depoyu görüntülemek ve uygulamanızda kod değişikliği yapmak için aşağıdakileri yapın:

1. DevOps başlangıç panosunun sol tarafında, **ana** dalınızın bağlantısını seçin. Bu bağlantı yeni oluşturulan Git deposuna bir görünüm açar.

2. Sonraki birkaç adımda, kod değişikliklerini doğrudan **ana** dala getirmek ve yürütmek için Web tarayıcısını kullanabilirsiniz. Ayrıca, depo sayfasının sağ üst kısmından **Kopyala** ' yı seçerek git deponuzu sık kullandığınız IDE 'nize da kopyalayabilirsiniz. 

3. Sol tarafta uygulama dosyası yapısına **Application/ASPNET-Core-DotNet-Core/Pages/Index. cshtml** sayfasına gidin.

4. **Düzenle**' yi seçin ve ardından H2 başlığında bir değişiklik yapın. Örneğin, **Azure DevOps Starter ile** hemen başlayın yazın veya başka bir değişiklik yapın.

      ![Kod düzenlemeleri](_img/azure-devops-project-aspnet-core/codechange.png)

5. **Yürüt**' ü seçin, bir açıklama bırakın ve yeniden **Yürüt** ' ü seçin.

6. Tarayıcınızda Azure DevOps başlangıç panosu ' na gidin.  Bir derlemenin sürdüğünü görüyor olmanız gerekir. Yaptığınız değişiklikler otomatik olarak bir CI/CD işlem hattı aracılığıyla oluşturulup dağıtılır.

## <a name="examine-the-cicd-pipeline"></a>CI/CD işlem hattını inceleyin

Önceki adımda, Azure DevOps Starter otomatik olarak tam bir CI/CD işlem hattı yapılandırdı. İşlem hattını gerektiği şekilde keşfedin ve özelleştirin. Azure DevOps derleme ve yayın işlem hatları hakkında bilgi edinmek için aşağıdaki adımları uygulayın.

1. DevOps başlangıç panosunun en üstünde **derleme Işlem hatları**' nı seçin. Bu bağlantı, yeni projeniz için bir tarayıcı sekmesi ve Azure DevOps derleme işlem hattı açar.

1. Üç nokta (...) simgesini seçin.  Bu eylem, yeni bir derlemeyi sıraya alma, bir derlemeyi duraklatma ve derleme işlem hattını düzenlemeyle çeşitli etkinlikleri başlatabileceğiniz bir menü açar.

1. **Düzenle**'yi seçin.

    ![Derleme işlem hattı](_img/azure-devops-project-aspnet-core/builddef.png)

1. Bu bölmede, derleme işlem hattınızla ilgili çeşitli görevleri inceleyebilirsiniz. Derleme, git deposundan kaynak getirme, bağımlılıkları geri yükleme ve dağıtımlar için kullanılan yayımlama çıkışları gibi çeşitli görevleri gerçekleştirir.

1. Derleme işlem hattının üst kısmında derleme işlem hattı adı’nı seçin.

1. Derleme işlem hattınızı daha açıklayıcı bir şekilde değiştirin, **& kuyruğu kaydet**' i seçin ve ardından **Kaydet**' i seçin.

1. Derleme işlem hattı adınızın altında **Geçmiş**’i seçin.   
**Geçmiş** bölmesinde, derleme için son değişikliklerinizin bir denetim izini görürsünüz.  Azure Pipelines, derleme ardışık düzeninde yapılan tüm değişiklikleri izler ve sürümleri karşılaştırmanızı sağlar.

1. **Tetikleyiciler**’i seçin. DevOps Starter otomatik olarak bir CI tetikleyicisi oluşturdu ve depoya yapılan her işlem yeni bir derleme başlatır. İsteğe bağlı olarak dalları CI işlemine dahil etmeyi veya işlemden hariç tutmayı seçebilirsiniz.

1. **Saklama**’yı seçin. Senaryonuza bağlı olarak, belirli sayıda derlemeyi tutmanın veya kaldırabilmeniz için ilkeler belirtebilirsiniz.

1. **Oluştur ve Yayınla**' yı seçin ve ardından **yayınlar**' ı seçin.  
DevOps Starter, Azure dağıtımlarını yönetmek için bir yayın işlem hattı oluşturur.

1.  Sol tarafta, yayın işlem hattının yanındaki üç nokta (...) simgesini seçin ve ardından **Düzenle**' yi seçin. Yayın işlem hattı, yayın işlemini tanımlayan bir işlem hattı içerir.  

1. **Yapıtlar**’ın altında **Bırak**’ı seçin. Önceki adımlarda incelediğiniz derleme işlem hattı, yapıt için kullanılan çıkışı üretir. 

1. **Bırakma** simgesinin yanındaki **sürekli dağıtım tetikleyicisi**' ni seçin. Bu sürüm ardışık düzeninde, kullanılabilir yeni bir yapı yapıtı olduğunda bir dağıtımı çalıştıran etkinleştirilmiş bir CD tetikleyicisi vardır. İsteğe bağlı olarak, dağıtımlarınızın el ile yürütme gerektirdiğinden tetikleyiciyi devre dışı bırakabilirsiniz.  

1. Sol tarafta **Görevler**' i seçin.  Görevler, dağıtım işleminizin gerçekleştirdiği etkinliklerdir. Bu örnekte, Azure App Service dağıtmak üzere bir görev oluşturulmuştur.

1. Sağ tarafta **sürümleri görüntüle**' yi seçin. Bu görünümde yayın geçmişi gösterilir.

1. Sürümlerden birinin yanındaki üç nokta (...) simgesini seçin ve sonra **Aç**' ı seçin. Bir yayın Özeti, ilişkili iş öğeleri ve testler gibi çeşitli birçok menü vardır.

1. **İşlemeler**'i seçin. Bu görünüm, belirli bir dağıtımla ilişkili kod işlemelerini gösterir. 

1. **Günlükleri** seçin. Günlüklerde, dağıtım işlemiyle ilgili yararlı bilgiler bulunur. Bunlar hem dağıtım sırasında hem de sonrasında görüntülenebilir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık ihtiyaç duymadığınızda oluşturduğunuz Azure App Service ve diğer ilgili kaynakları silebilirsiniz. DevOps başlangıç panosundaki **silme** işlevini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Derleme ve yayın işlem hattınızı ekibinizin ihtiyaçlarını karşılayacak şekilde değiştirmeyle ilgili daha fazla bilgi edinmek için bu öğreticiye bakın:

> [!div class="nextstepaction"]
> [CD işlemini özelleştirme](/azure/devops/pipelines/release/define-multistage-release-process)

## <a name="videos"></a>Videolar

> [!VIDEO https://www.youtube.com/embed/itwqMf9aR0w]
