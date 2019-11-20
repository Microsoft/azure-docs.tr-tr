---
title: 'Hızlı Başlangıç: Azure DevOps Projeleri .NET için bir CI/CD işlem hattı oluşturun'
description: Azure DevOps projeleri, Azure'da kullanmaya başlamak kolaylaştırır. Birkaç hızlı adımda, tercih ettiğiniz bir Azure hizmetinde .NET uygulaması başlatmanıza yardımcı olur.
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
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 7d2ccdfa1fe553d0795a82856dd255f4a54138bf
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898034"
---
# <a name="create-a-cicd-pipeline-for-net-with-azure-devops-projects"></a>Azure DevOps Projeleri .NET için bir CI/CD işlem hattı oluşturun

DevOps Projeleri ile .NET Core veya ASP.NET uygulamanız için sürekli tümleştirme (CI) ve sürekli teslim (CD) yapılandırın. DevOps projeleri derleme ve yayın işlem hattı Azure işlem hatları, başlangıç yapılandırmasını basitleştirir.

Azure aboneliğiniz yoksa, [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) üzerinden ücretsiz edinebilirsiniz.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

DevOps projeleri, Azure DevOps bir CI/CD işlem hattı oluşturur. Yeni bir Azure DevOps kuruluş oluşturun veya mevcut bir kuruluşa kullanın. DevOps projeleri, Azure kaynaklarını da tercih ettiğiniz bir Azure aboneliği oluşturur.

1. [Microsoft Azure portalında](https://portal.azure.com) oturum açın.

1. Sol bölmede seçin **kaynak Oluştur** sol gezinti çubuğunda ve ardından arama simgesine **DevOps projeleri**.  

3.  **Oluştur**’u seçin.

    ![Sürekli Teslim Başlatılıyor](_img/azure-devops-project-aspnet-core/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Örnek uygulama ve Azure hizmeti seçme

1. **.NET** örnek uygulamasını seçin. .NET örnekleri açık kaynaklı ASP.NET çerçevesi ya da çoklu platform .NET Core çerçevesi seçeneklerinden birini içerir.

   ![.NET Framework](_img/azure-devops-project-aspnet-core/chooselanguagedotnet.png)

2. Bu örnek bir ASP.NET Core MVC uygulamasıdır. **.NET Core** uygulama çerçevesini seçip **İleri**' yi seçin.    
    
3. Dağıtım hedefi olarak **Windows Web uygulaması** ' nı seçin ve ardından **İleri**' yi seçin. İsteğe bağlı olarak, dağıtımınız için diğer Azure hizmetlerini seçebilirsiniz. Daha önce seçtiğiniz uygulama çerçevesi, burada bulunan Azure hizmet dağıtım hedefinin türünü belirler.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps ve Azure aboneliğinin yapılandırın 

1. Bir **Proje adı**girin.

2. Yeni bir ücretsiz **Azure DevOps organizasyonu** oluşturun veya açılan listeden mevcut bir kuruluştan birini seçin.

3. **Azure aboneliğinizi**seçin, **Web uygulamanız** için bir ad girin veya varsayılan işlemi yapın ve **bitti**' yi seçin. Birkaç dakika sonra, Azure portal DevOps Projeleri dağıtımına genel bakış görüntülenir. 

4. DevOps Proje panosunu görüntülemek için **Kaynağa Git** ' i seçin. Hızlı erişim için, sağ üst köşede **projeyi** panonuza sabitleyin. Örnek bir uygulama, **Azure DevOps kuruluşunuzda**bir depoda ayarlanır. Bir yapı yürütülür ve uygulamanız Azure 'a dağıtılır.

5. Pano, kod deponuzda, CI/CD işlem hattınızla ve Azure 'daki uygulamanız için görünürlük sağlar. Çalışan uygulamanızı görüntülemek için Azure kaynakları ' nın sağ tarafında bulunan **Araştır** ' ı seçin.

   ![Pano görünümü](_img/azure-devops-project-aspnet-core/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>Kod değişikliklerini işleme ve CI/CD’yi yürütme

DevOps projeleri, Azure depoları veya GitHub Git deposu oluşturuldu. Depoyu görüntülemek ve uygulamanızda kod değişikliği yapmak için aşağıdakileri yapın:

1. DevOps projeleri panosunun sol tarafta, bağlantısını seçin, **ana** dal. Bu bağlantı yeni oluşturulan Git deposuna bir görünüm açar.

2. Sonraki birkaç adımda, kod değişikliklerini doğrudan **ana** dala getirmek ve yürütmek için Web tarayıcısını kullanabilirsiniz. Ayrıca, depo sayfasının sağ üst kısmından **Kopyala** ' yı seçerek git deponuzu sık kullandığınız IDE 'nize da kopyalayabilirsiniz. 

3. Sol tarafta uygulama dosyası yapısına **Application/ASPNET-Core-DotNet-Core/Pages/Index. cshtml**sayfasına gidin.

4. **Düzenle**' yi seçin ve ardından H2 başlığında bir değişiklik yapın. Örneğin, **Azure DevOps Projeleri hemen kullanmaya** başlayın yazın veya başka bir değişiklik yapın.

      ![Kod düzenlemeleri](_img/azure-devops-project-aspnet-core/codechange.png)

5. **Yürüt**' ü seçin, bir açıklama bırakın ve yeniden **Yürüt** ' ü seçin.

6. Tarayıcınızda Azure DevOps projesi panoya gidin.  Bir derlemenin sürdüğünü görüyor olmanız gerekir. Yaptığınız değişiklikleri otomatik olarak oluşturulur ve bir CI/CD işlem hattı dağıtılır.

## <a name="examine-the-cicd-pipeline"></a>CI/CD işlem hattı inceleyin

Önceki adımda, Azure DevOps Projeleri otomatik olarak tam CI/CD işlem hattı yapılandırdı. İşlem hattını gerektiği şekilde keşfedin ve özelleştirin. Azure DevOps yapıyla hakkında bilgilenmeli ve yayın işlem hatları için aşağıdaki adımları uygulayın.

1. DevOps projeleri panonun üst kısmında seçin **derleme işlem hatlarını**.  
Bu bağlantı, bir tarayıcı sekmesi açar ve Azure DevOps yeni projeniz için işlem hattı oluşturun.

1. Üç nokta (...) simgesini seçin.  Bu eylem, yeni bir derlemeyi sıraya alma, bir derlemeyi duraklatma ve derleme işlem hattını düzenlemeyle çeşitli etkinlikleri başlatabileceğiniz bir menü açar.

1. **Düzenle**’yi seçin.

    ![Derleme işlem hattı](_img/azure-devops-project-aspnet-core/builddef.png)

1. Bu bölmede, derleme işlem hattı için çeşitli görevleri inceleyebilirsiniz.  
 Derleme, git deposundan kaynak getirme, bağımlılıkları geri yükleme ve dağıtımlar için kullanılan yayımlama çıkışları gibi çeşitli görevleri gerçekleştirir.

1. Derleme işlem hattı üstünde derleme işlem hattı adı seçin.

1. Bir şeyler daha açıklayıcı, select, derleme işlem hattı adını değiştirmek **Kaydet ve kuyruğa**ve ardından **Kaydet**.

1. Derleme işlem hattı adınızın altında **Geçmiş**’i seçin.   
İçinde **geçmişi** bölmesinde, bir denetim kaydı yaptığınız son değişiklikler derleme için bkz.  Azure işlem hatları için derleme işlem hattı yapılan değişiklikleri izler ve sürümleri karşılaştırmanızı sağlar.

1. **Tetikleyiciler**’i seçin.  
DevOps projeleri CI tetikleyicisini otomatik olarak oluşturulan ve depoya her işleme, yeni bir yapı başlatır.  İsteğe bağlı olarak dalları CI işlemine dahil etmeyi veya işlemden hariç tutmayı seçebilirsiniz.

1. **Saklama**’yı seçin.  
Senaryonuza bağlı olarak, saklamak veya belirli bir sayıda derlemeleri kaldırmak için ilkeleri belirtebilirsiniz.

1. **Oluştur ve Yayınla**' yı seçin ve ardından **yayınlar**' ı seçin.  
DevOps Projeleri, Azure dağıtımlarını yönetmek için bir yayın işlem hattı oluşturur.

1.  Sol tarafta, yayın işlem hattının yanındaki üç nokta (...) simgesini seçin ve ardından **Düzenle**' yi seçin.  
Sürüm ardışık yayın işlemini tanımlar. bir işlem hattı içerir.  

1. **Yapıtlar**’ın altında **Bırak**’ı seçin.  Önceki adımlarda incelediğiniz derleme işlem hattı, yapıt için kullanılan çıkışı üretir. 

1. Yanındaki **bırak** simgesini seçme **sürekli dağıtım tetikleyicisi**.  
Bu yayın işlem hattı yok her seferinde yeni bir derleme yapıtının kullanılabilir bir dağıtım çalıştığı etkin bir CD tetikleyicisine sahiptir. İsteğe bağlı olarak, el ile yürütme dağıtımlarınızı gerektirir böylece tetikleyiciyi devre dışı bırakabilirsiniz.  

1. Sol tarafta, seçin **görevleri**.   
Dağıtım işleminizin gerçekleştiren etkinlikler görevlerdir. Bu örnekte, Azure App Service dağıtmak üzere bir görev oluşturulmuştur.

1. Sağ tarafta seçin **yayınları görüntüleyebilir**. Bu görünümde yayın geçmişi gösterilir.

1. Sürümlerinizin birini yanındaki üç nokta (...) seçin ve ardından **açık**.  
Yayın özeti, ilişkili iş öğeleri ve test gibi keşfetmek için birkaç menüleri vardır.


1. **İşlemeler**'i seçin.   
Bu görünümde, belirli bir dağıtım ile ilişkili kod tamamlama gösterilir. 

1. **Günlükler**’i seçin.  
Günlüklerde, dağıtım işlemiyle ilgili yararlı bilgiler bulunur. Bunlar hem dağıtım sırasında hem de sonrasında görüntülenebilir.


## <a name="clean-up-resources"></a>Kaynakları temizleme

Azure App Service ve artık gerekmediğinde, oluşturduğunuz ilgili diğer kaynakları silebilirsiniz. Kullanım **Sil** DevOps projeleri Pano işlevselliği.

## <a name="next-steps"></a>Sonraki adımlar

Derleme ve yayın işlem hattınızı ekibinizin ihtiyaçlarını karşılayacak şekilde değiştirmeyle ilgili daha fazla bilgi edinmek için bu öğreticiye bakın:

> [!div class="nextstepaction"]
> [CD işlemini özelleştirme](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Videolar

> [!VIDEO https://www.youtube.com/embed/itwqMf9aR0w]
