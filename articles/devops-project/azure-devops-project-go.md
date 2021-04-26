---
title: 'Hızlı başlangıç: Azure DevOps Starter kullanarak go programlama dili için bir CI/CD işlem hattı oluşturma'
description: DevOps Starter, Azure 'u kullanmaya başlamanızı kolaylaştırır. Birkaç hızlı adımda Azure hizmetinde bir go programlama dili Web uygulaması başlatmanıza yardımcı olur.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 6d6181686eaeb90d4fcdae0231430623b84e2c1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548521"
---
# <a name="create-a-cicd-pipeline-for-go-using-azure-devops-starter"></a>Azure DevOps Starter kullanarak Go için bir CI/CD işlem hattı oluşturun

Azure DevOps Starter kullanarak go uygulamanız için sürekli tümleştirme (CI) ve sürekli teslim (CD) yapılandırın. DevOps Starter, bir Azure DevOps derleme ve yayın işlem hattının ilk yapılandırmasını basitleştirir.

Azure aboneliğiniz yoksa, [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) üzerinden ücretsiz edinebilirsiniz.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

DevOps Starter Azure Pipelines ' de bir CI/CD işlem hattı oluşturur. Yeni bir Azure DevOps kuruluşu oluşturabilir veya var olan bir kuruluşu kullanabilirsiniz. DevOps Starter Ayrıca seçtiğiniz Azure aboneliğindeki Azure kaynaklarını da oluşturur.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Arama kutusuna **DevOps Starter** yazın ve ardından öğesini seçin. Yeni bir tane oluşturmak için **Ekle** ' ye tıklayın.

    ![DevOps başlangıç panosu](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-app-and-azure-service"></a>Örnek uygulama ve Azure hizmeti seçin

1. **Go** örnek uygulamasını seçin ve ardından **İleri**' yi seçin.  
    
1. **Basit Go uygulaması** varsayılan çerçevedir. **İleri**’yi seçin.  Daha önce seçtiğiniz uygulama çerçevesi, dağıtım için kullanılabilen Azure hizmet dağıtım hedeflerinin türünü belirler. 
    
1. Varsayılan Azure hizmetini bırakın ve Ileri ' **yi** seçin.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps ve bir Azure aboneliği yapılandırma 

1. Yeni bir ücretsiz Azure DevOps organizasyonu oluşturun veya var olan bir kuruluşu seçin. 

1. Azure DevOps projeniz için bir ad girin. 

1. Azure aboneliğinizi ve konumunuzu seçin, uygulamanız için bir ad girin ve **bitti**' yi seçin. Birkaç dakika sonra DevOps başlangıç panosu Azure portal görüntülenir. Azure DevOps kuruluşunuzda bir depoda bir örnek uygulama ayarlanır, bir derleme yürütülür ve uygulamanız Azure 'a dağıtılır. 

    Pano, kod deponuzda, CI/CD işlem hattınızla ve Azure 'daki uygulamanız için görünürlük sağlar. Sağ tarafta, çalışan uygulamanızı görüntülemek için **Araştır** ' ı seçin.

    ![Pano görünümü](_img/azure-devops-project-go/dashboardnopreview.png) 

## <a name="commit-your-code-changes-and-execute-the-cicd"></a>Kod değişikliklerinizi işleyin ve CI/CD 'yi yürütün

DevOps Starter, Azure Repos veya GitHub 'da bir git deposu oluşturur. Depoyu görüntülemek ve uygulamanızda kod değişikliği yapmak için aşağıdakileri yapın:

1. DevOps Starter 'da, sol taraftaki ana dalınızın bağlantısını seçin. Bağlantı, yeni oluşturulan git deposuna bir görünüm açar.

1. Depo kopyası URL 'sini görüntülemek için sağ üst köşedeki **Kopyala** ' yı seçin. Git deponuzu en sevdiğiniz IDE 'de kopyalayabilirsiniz. Sonraki birkaç adımda, kod değişikliklerini doğrudan ana dala getirmek ve yürütmek için Web tarayıcısını kullanabilirsiniz.

1. Solda, *Görünümler/index.html* dosyasına gidin ve ardından **Düzenle**' yi seçin.

1. Dosyada değişiklik yapın. Örneğin, div etiketlerinin birindeki bazı metinleri değiştirin.

1. **Yürüt**' ü seçin ve ardından değişikliklerinizi kaydedin.

1. Tarayıcınızda DevOps Projeleri panosuna gidin. Bir yapı devam ediyor olmalıdır. Yaptığınız değişiklikler otomatik olarak bir CI/CD işlem hattı aracılığıyla oluşturulup dağıtılır.

## <a name="examine-the-cicd-pipeline"></a>CI/CD işlem hattını inceleyin

DevOps Starter, Azure Repos içinde tam bir CI/CD işlem hattını otomatik olarak yapılandırır. İşlem hattını gerektiği şekilde keşfedin ve özelleştirin. Azure DevOps derleme ve yayın işlem hatları hakkında bilgi edinmek için aşağıdakileri yapın:

1. DevOps başlangıç panosuna gidin.

1. Üstte **derleme işlem hatları**' nı seçin. Bir tarayıcı sekmesi, yeni projeniz için derleme işlem hattını görüntüler.

1. **Durum** alanını işaret edin ve ardından üç nokta (...) simgesini seçin. Bir menü, yeni bir derlemeyi sıraya alma, bir derlemeyi duraklatma ve derleme işlem hattını düzenlemeyle çeşitli seçenekleri görüntüler.

1. **Düzenle**'yi seçin.

1. Bu bölmede, derleme işlem hattınızla ilgili çeşitli görevleri inceleyebilirsiniz. Derleme git deposundan kaynak getirme, bağımlılıkları geri yükleme ve dağıtımlar için kullanılan yayınlama çıkışları gibi çeşitli görevleri gerçekleştirir.

1. Derleme işlem hattının üst kısmında derleme işlem hattı adı’nı seçin.

1. Derleme işlem hattınızı daha açıklayıcı bir şekilde değiştirin, **& kuyruğu kaydet**' i seçin ve ardından **Kaydet**' i seçin.

1. Derleme işlem hattı adınızın altında **Geçmiş**’i seçin. Bu bölme, derleme için son değişikliklerinizin denetim izini görüntüler. Azure DevOps, derleme ardışık düzeninde yapılan tüm değişiklikleri izler ve sürümleri karşılaştırmanızı sağlar.

1. **Tetikleyiciler**’i seçin. DevOps Starter otomatik olarak bir CI tetikleyicisi oluşturur ve depoya yapılan her bir işleme yeni bir derleme başlatır. İsteğe bağlı olarak, CI işlemindeki dalları dahil etmek veya hariç tutmak seçebilirsiniz.

1. **Saklama**’yı seçin. Senaryonuza bağlı olarak, belirli sayıda derlemeyi tutmanın veya kaldırabilmeniz için ilkeler belirtebilirsiniz.

1. **Build ve Release**' i seçin ve ardından **yayınlar**' ı seçin.  DevOps Starter, Azure dağıtımlarını yönetmek için bir yayın işlem hattı oluşturur.

1. Yayın işlem hattının yanındaki üç nokta (...) simgesini seçin ve ardından **Düzenle**' yi seçin. Yayın işlem hattı, yayın işlemini tanımlayan bir *işlem hattı* içerir.

1. **Yapıtlar**’ın altında **Bırak**’ı seçin. Daha önce incelenen derleme işlem hattı, yapıt için kullanılan çıktıyı üretir. 

1. **Bırakma** simgesinin sağ tarafında, **sürekli dağıtım tetikleyicisi**' ni seçin. Bu sürüm ardışık düzeninde, her yeni derleme yapıtı kullanılabilir olduğunda bir dağıtımı yürüten etkinleştirilmiş bir CD tetikleyicisi vardır. İsteğe bağlı olarak, dağıtımlarınızın el ile yürütme gerektirdiğinden tetikleyiciyi devre dışı bırakabilirsiniz. 

1. Solda, **Görevler**' i seçin. Görevler, dağıtım işleminizin gerçekleştirdiği etkinliklerdir. Bu örnekte, Azure App Service dağıtmak üzere bir görev oluşturulmuştur.

1. Sağ tarafta, sürümlerin geçmişini görüntülemek için **yayınları görüntüle** ' yi seçin.

1. Bir yayının yanındaki üç nokta (...) simgesini seçin ve sonra **Aç**' ı seçin. Yayın Özeti, ilişkili iş öğeleri ve testler gibi çeşitli menüleri inceleyebilirsiniz.

1. **İşlemeler**'i seçin. Bu görünüm, bu dağıtımla ilişkili kod işlemelerini gösterir. 

1. **Günlükleri** seçin. Günlüklerde, dağıtım işlemiyle ilgili yararlı bilgiler bulunur. Bunları, dağıtımları sırasında ve sonrasında görüntüleyebilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında, Azure App Service örneğini ve bu hızlı başlangıçta oluşturduğunuz ilgili kaynakları silebilirsiniz. Bunu yapmak için DevOps başlangıç panosundaki **silme** işlevini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Takımınızın ihtiyaçlarını karşılamak için derleme ve yayın işlem hatlarını değiştirme hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Çoklu aşamalı sürekli dağıtım (CD) işlem hattınızı tanımlama](/azure/devops/pipelines/release/define-multistage-release-process)