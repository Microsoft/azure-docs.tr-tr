---
title: 'Öğretici: Azure DevOps Projeleri kullanarak ASP.NET uygulamanızı ve Azure SQL veritabanı kodunuzu dağıtın'
description: DevOps Projeleri Azure 'u kullanmaya başlamanızı kolaylaştırır. DevOps Projeleri ile, birkaç hızlı adımda ASP.NET uygulamanızı ve Azure SQL veritabanı kodunuzu dağıtabilirsiniz.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 560b16a40d73d07df6c9f446c7196cd930e2274e
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71971495"
---
# <a name="tutorial-deploy-your-aspnet-app-and-azure-sql-database-code-by-using-azure-devops-projects"></a>Öğretici: Azure DevOps Projeleri kullanarak ASP.NET uygulamanızı ve Azure SQL veritabanı kodunuzu dağıtın

Azure DevOps Projeleri, mevcut kodunuzu ve git deponuzu getirebileceğiniz veya Azure 'a sürekli tümleştirme (CI) ve sürekli teslim (CD) işlem hattı oluşturmak için örnek bir uygulama seçebileceğiniz basitleştirilmiş bir deneyim sunar. 

Ayrıca DevOps Projeleri:
* Azure SQL veritabanı gibi Azure kaynaklarını otomatik olarak oluşturur.
* CI için derleme işlem hattı içeren Azure Pipelines serbest bırakma işlem hattı oluşturur ve yapılandırır.
* CD için bir yayın işlem hattı oluşturur. 
* İzleme için bir Azure Application Insights kaynağı oluşturur.

Bu öğreticide şunları yapacaksınız:

> [!div class="checklist"]
> * ASP.NET uygulamanızı ve Azure SQL veritabanı kodunuzu dağıtmak için Azure DevOps Projeleri kullanma
> * Azure DevOps ve bir Azure aboneliği yapılandırma 
> * CI işlem hattını inceleme
> * CD işlem hattını inceleme
> * Azure Repos değişiklikleri işleyin ve Azure 'a otomatik olarak dağıtın
> * Azure SQL veritabanına bağlanma 
> * Kaynakları temizleme

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) aracılığıyla ücretsiz bir abonelik alabilirsiniz.

## <a name="create-a-project-in-devops-projects-for-an-aspnet-app-and-an-azure-sql-database"></a>Bir ASP.NET uygulaması ve Azure SQL veritabanı için DevOps Projeleri bir proje oluşturma

DevOps Projeleri, Azure Pipelines bir CI/CD işlem hattı oluşturur. Yeni bir Azure DevOps kuruluşu oluşturabilir veya var olan bir kuruluşu kullanabilirsiniz. DevOps Projeleri ayrıca Azure SQL veritabanı gibi tercih ettiğiniz Azure aboneliğindeki Azure kaynaklarını da oluşturur.

1. [Azure Portal](https://portal.azure.com)’ında oturum açın.

1. Sol bölmede **kaynak oluştur**' u seçin.

1. Arama kutusuna **DevOps Projeleri**yazın ve ardından **Oluştur**' u seçin.

    ![DevOps Projeleri panosu](_img/azure-devops-project-github/fullbrowser.png)

1. **.Net**' i seçin ve ardından **İleri**' yi seçin.

1. **Uygulama çerçevesi seçin**altında **ASP.net**' yi seçin.

1. **Veritabanı Ekle**' yi seçin ve ardından **İleri**' yi seçin.  
    Önceki adımda seçtiğiniz uygulama çerçevesi, burada kullanılabilir olan Azure hizmet dağıtım hedefinin türünü belirler. 
    
1. **İleri**’yi seçin.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps ve bir Azure aboneliği yapılandırma

1. Yeni bir Azure DevOps organizasyonu oluşturun veya var olan bir kuruluşu seçin. 

1. Azure DevOps projeniz için bir ad girin. 

1. Azure abonelik hizmetlerinizi seçin.  
    İsteğe bağlı olarak, ek Azure yapılandırma ayarlarını görüntülemek ve **veritabanı sunucusu oturum açma ayrıntıları** bölümünde Kullanıcı adını belirlemek için **Değiştir**' i seçebilirsiniz. Bu öğreticideki sonraki adımlar için Kullanıcı adını depolayın. Bu isteğe bağlı adımı gerçekleştirirseniz, **bitti**' yi seçmeden önce Azure yapılandırma alanından çıkın.
 
1. **Done** (Bitti) öğesini seçin.  
    Birkaç dakika sonra işlem tamamlanır ve DevOps Projeleri panosu Azure portal açılır. Ayrıca, panoda doğrudan Azure portal **tüm kaynaklardan** da gidebilirsiniz. Sağ tarafta, çalışan uygulamanızı görüntülemek için **Araştır** ' ı seçin.
    
## <a name="examine-the-ci-pipeline"></a>CI işlem hattını inceleme

DevOps Projeleri, Azure Repos bir tam CI/CD işlem hattını otomatik olarak yapılandırır. İşlem hattını inceleyebilir ve özelleştirebilirsiniz. Azure DevOps derleme işlem hattını öğrenmek için aşağıdakileri yapın:

1. DevOps Projeleri panosunun en üstünde **derleme işlem hatları**' nı seçin.  
    Bir tarayıcı sekmesi, yeni projeniz için derleme işlem hattını görüntüler.

1. **Durum** alanını işaret edin ve ardından üç nokta (...) simgesini seçin.  
    Bir menü, yeni bir derlemeyi sıraya alma, bir derlemeyi duraklatma ve derleme işlem hattını düzenlemeyle çeşitli seçenekleri görüntüler.

1. **Düzenle**’yi seçin.

1. Bu bölmede, derleme işlem hattınızla ilgili çeşitli görevleri inceleyebilirsiniz.  
    Derleme, git deposundan kaynak getirme, bağımlılıkları geri yükleme ve dağıtımlar için kullanılan yayınlama çıkışları gibi çeşitli görevleri gerçekleştirir.

1. Yapı işlem hattının en üstünde derleme işlem hattı adını seçin.

1. Derleme işlem hattınızı daha açıklayıcı bir şekilde değiştirin, **& kuyruğu kaydet**' i seçin ve ardından **Kaydet**' i seçin.

1. Derleme işlem hattı adınızın altında **Geçmiş**’i seçin.  
    Bu bölme, derleme için son değişikliklerinizin denetim izini görüntüler. Azure Pipelines, derleme ardışık düzeninde yapılan tüm değişiklikleri izler ve sürümleri karşılaştırmanızı sağlar.

1. **Tetikleyiciler**’i seçin.  
    DevOps Projeleri otomatik olarak bir CI tetikleyicisi oluşturur ve depoya yapılan her bir kayıt yeni bir derleme başlatır. İsteğe bağlı olarak, CI işlemindeki dalları dahil etmek veya hariç tutmak seçebilirsiniz.

1. **Saklama**’yı seçin.  
    Senaryonuza bağlı olarak, belirli sayıda derlemeyi tutmanın veya kaldırabilmeniz için ilkeler belirtebilirsiniz.

## <a name="examine-the-cd-pipeline"></a>CD işlem hattını inceleme

DevOps Projeleri, Azure DevOps kuruluşunuzdan Azure aboneliğinize dağıtmak için gerekli adımları otomatik olarak oluşturur ve yapılandırır. Bu adımlar, Azure aboneliğinizdeki Azure DevOps kimlik doğrulaması için bir Azure hizmet bağlantısı yapılandırmayı içerir. Otomasyon Ayrıca CD 'yi Azure sanal makinesine sağlayan bir CD işlem hattı oluşturur. Azure DevOps CD işlem hattı hakkında daha fazla bilgi edinmek için aşağıdakileri yapın:

1. **Build ve Release**' i seçin ve ardından **yayınlar**' ı seçin.  
    DevOps Projeleri, Azure dağıtımlarını yönetmek için bir yayın işlem hattı oluşturur.

1. Yayın işlem hattının yanındaki üç nokta (...) simgesini seçin ve ardından **Düzenle**' yi seçin.  
    Yayın işlem hattı, yayın işlemini tanımlayan bir *işlem hattı* içerir.

1. **Yapıtlar**’ın altında **Bırak**’ı seçin.  
    Önceki adımlarda inceettiğiniz derleme işlem hattı, yapıt için kullanılan çıktıyı üretir. 

1. **Bırakma** simgesinin sağ tarafında, **sürekli dağıtım tetikleyicisi**' ni seçin.  
    Bu sürüm ardışık düzeninde, her yeni derleme yapıtı kullanılabilir olduğunda bir dağıtımı yürüten etkinleştirilmiş bir CD tetikleyicisi vardır. İsteğe bağlı olarak, dağıtımlarınızın el ile yürütme gerektirdiğinden tetikleyiciyi devre dışı bırakabilirsiniz. 

    DevOps Projeleri rastgele bir SQL parolası ayarlar ve bunu sürüm ardışık düzeni için kullanır.
    
1. Sol tarafta **değişkenler**' i seçin. 

   > [!NOTE]
   > Yalnızca SQL Server parolasını değiştirdiyseniz aşağıdaki adımı gerçekleştirin. Tek bir parola değişkeni vardır.
  
1. **Değer** kutusunun yanındaki asma kilit simgesini seçin, yeni parolayı girin ve ardından **Kaydet**' i seçin.

1. Solda, **Görevler**' i seçin ve ardından ortamınızı seçin.  
    Görevler, dağıtım işleminizin yürütüldüğü etkinliklerdir ve aşamalar halinde gruplandırılır. Bu yayın işlem hattının bir *Azure App Service dağıtımı* ve *Azure SQL veritabanı dağıtım* görevi içeren tek bir aşaması vardır.

1. *Azure SQL Yürüt* görevini SEÇIN ve SQL dağıtımı için kullanılan çeşitli özellikleri inceleyin.  
    **Dağıtım paketi**altında, görev BIR *SQL dacpac* dosyası kullanır.

1. Sağ tarafta, sürümlerin geçmişini görüntülemek için **yayınları görüntüle** ' yi seçin.

1. Bir yayının yanındaki üç nokta (...) simgesini seçin ve sonra **Aç**' ı seçin.  
     Yayın Özeti, ilişkili iş öğeleri ve testler gibi çeşitli menüleri inceleyebilirsiniz.

1. **İşlemeler**'i seçin.  
     Bu görünüm, bu dağıtımla ilişkili kod işlemelerini gösterir. Dağıtımlar arasındaki işleme farklılıklarını görmek için yayınları karşılaştırın.

1. **Günlükler**’i seçin.  
     Günlüklerde, dağıtım işlemiyle ilgili yararlı bilgiler bulunur. Bunları, dağıtımları sırasında ve sonrasında görüntüleyebilirsiniz.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Azure Repos değişiklikleri işleyin ve Azure 'a otomatik olarak dağıtın 

 > [!NOTE]
 > Aşağıdaki yordam, CI/CD işlem hattını basit bir metin değişikliğine karşı sınar. SQL dağıtım sürecini test etmek için isteğe bağlı olarak tabloda bir SQL Server şeması değişikliği yapabilirsiniz.

Şimdi, en son çalışmalarınızı otomatik olarak Web sitenize dağıtan bir CI/CD işlemi kullanarak uygulamanızdaki bir takımla işbirliği yapmaya hazırsınız. Git deposunda yapılan her değişiklik Azure DevOps 'da bir derleme başlatır ve bir CD işlem hattı Azure 'a bir dağıtım yürütür. Bu bölümdeki yordamı izleyin veya değişiklikleri deponuza uygulamak için başka bir teknik kullanın. Kod değişiklikleri CI/CD işlemini başlatır ve değişikliklerinizi otomatik olarak Azure 'a dağıtır.

1. Sol bölmede **kod**' ı seçin ve ardından deponuza gidin.

1. *Samplewebapplication\views\home* dizinine gidin, *Index. cshtml* dosyasının yanındaki üç nokta (...) simgesini seçin ve ardından **Düzenle**' yi seçin. 

1. Dosyada, div etiketlerinden birine metin ekleme gibi bir değişiklik yapın. 

1. Sağ üst köşedeki **Kaydet**' i seçin ve sonra değişikliklerinizi göndermek Için yeniden **Yürüt** ' ü seçin.  
    Birkaç dakika sonra, bir derleme Azure DevOps 'da başlar ve değişiklikleri dağıtmak için bir yayın yürütülür. DevOps Projeleri panosunda veya Azure DevOps kuruluşunuzla tarayıcıda derleme durumunu izleyin.

1. Yayın tamamlandıktan sonra, değişikliklerinizi doğrulamak için uygulamanızı yenileyin.

## <a name="connect-to-the-azure-sql-database"></a>Azure SQL veritabanına bağlanma

Azure SQL veritabanına bağlanmak için uygun izinlere sahip olmanız gerekir.

1. DevOps Projeleri **panosunda SQL veritabanı ' nı seçerek SQL** veritabanının yönetim sayfasına gidin.
   
1. **Sunucu güvenlik duvarını ayarla**' yı seçin ve **Istemci IP 'si Ekle**' yi seçin. 

1. **Kaydet**’i seçin.  
    İstemci IP 'niz artık SQL Server Azure kaynağına erişimi vardır.

1. **SQL veritabanı** bölmesine geri dönün. 

1. Sağ tarafta, **SQL Server**için yapılandırma sayfasına gitmek üzere sunucu adını seçin.

1. **Parolayı Sıfırla**' yı seçin, SQL Server yönetici oturumu için bir parola girin ve ardından **Kaydet**' i seçin.  
    Bu öğreticide daha sonra kullanmak üzere bu parolayı kaydettiğinizden emin olun.

    İsteğe bağlı olarak, SQL Server ve Azure SQL veritabanı 'na bağlanmak için SQL Server Management Studio veya Visual Studio gibi istemci araçlarını kullanabilirsiniz. Bağlanmak için **Sunucu adı** özelliğini kullanın.

    DevOps Projeleri içinde projeyi başlangıçta yapılandırdığınızda veritabanı kullanıcı adını değiştirmediyseniz, Kullanıcı adınız e-posta adresinizin yerel kısmıdır. Örneğin, e-posta adresiniz *johndoe\@microsoft.com*ise, Kullanıcı adınız *johntikan*olur.

   > [!NOTE]
   > SQL oturum açma parolasını değiştirirseniz, "CD işlem hattını Inceleme" bölümünde açıklandığı gibi sürüm ardışık düzen değişkeninde parolayı değiştirmeniz gerekir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Test ediyorsanız, kaynaklarınızı temizleyerek faturalandırma ücretlerinden kaçınabilirsiniz. Artık gerekli olmadığında, bu öğreticide oluşturduğunuz Azure SQL veritabanını ve ilgili kaynakları silebilirsiniz. Bunu yapmak için DevOps Projeleri panosundaki **silme** işlevini kullanın.

> [!IMPORTANT]
> Aşağıdaki yordam kaynakları kalıcı olarak siler. *Silme* işlevselliği, proje tarafından oluşturulan verileri hem Azure hem de Azure devops içinde DevOps Projeleri yok eder ve bu işlemi geri alamazsınız. Bu yordamı yalnızca istemleri dikkatle okuduktan sonra kullanın.

1. Azure portal, DevOps Projeleri panosuna gidin.
2. Sağ üst köşedeki **Sil**' i seçin. 
3. İstem sırasında, kaynakları *kalıcı olarak silmek* için **Evet** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Ekibinizin gereksinimlerine uygun olarak bu derleme ve yayın işlem hatlarını istediğiniz gibi değiştirebilirsiniz. Ayrıca bu CI/CD desenini diğer işlem hatlarınızda şablon olarak kullanabilirsiniz. Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * ASP.NET uygulamanızı ve Azure SQL veritabanı kodunuzu dağıtmak için Azure DevOps Projeleri kullanma
> * Azure DevOps ve bir Azure aboneliği yapılandırma 
> * CI işlem hattını inceleme
> * CD işlem hattını inceleme
> * Azure Repos değişiklikleri işleyin ve Azure 'a otomatik olarak dağıtın
> * Azure SQL veritabanına bağlanma 
> * Kaynakları temizleme

CI/CD işlem hattı hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Çoklu aşamalı sürekli dağıtım (CD) işlem hattınızı tanımlama](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Videolar

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]
