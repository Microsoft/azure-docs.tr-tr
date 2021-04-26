---
title: 'Öğretici: Azure DevOps Starter kullanarak ASP.NET uygulamanızı ve Azure SQL veritabanı kodunuzu dağıtın'
description: DevOps Starter, Azure 'u kullanmaya başlamanızı kolaylaştırır. DevOps Starter ile, birkaç hızlı adımda ASP.NET uygulamanızı ve Azure SQL veritabanı kodunuzu dağıtabilirsiniz.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 7e85b698e1b3c3a1c3e8730ab85e8b72b7b0d9f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548487"
---
# <a name="tutorial-deploy-your-aspnet-app-and-azure-sql-database-code-by-using-azure-devops-starter"></a>Öğretici: Azure DevOps Starter kullanarak ASP.NET uygulamanızı ve Azure SQL veritabanı kodunuzu dağıtın

Azure DevOps Starter, Azure 'da sürekli tümleştirme (CI) ve sürekli teslim (CD) işlem hattı oluşturmak için mevcut kodunuzu ve git deponuzu getirebileceğiniz veya örnek bir uygulama seçebileceğiniz basitleştirilmiş bir deneyim sunar. 

DevOps Starter ayrıca:
* Azure SQL veritabanı 'nda bir veritabanı gibi Azure kaynaklarını otomatik olarak oluşturur.
* CI için derleme işlem hattı içeren Azure Pipelines serbest bırakma işlem hattı oluşturur ve yapılandırır.
* CD için bir yayın işlem hattı oluşturur. 
* İzleme için bir Azure Application Insights kaynağı oluşturur.

Bu öğreticide şunları yapacaksınız:

> [!div class="checklist"]
> * ASP.NET uygulamanızı ve Azure SQL veritabanı kodunuzu dağıtmak için Azure DevOps Starter 'ı kullanma
> * Azure DevOps ve bir Azure aboneliği yapılandırma 
> * CI işlem hattını inceleme
> * CD işlem hattını inceleme
> * Azure Repos değişiklikleri işleyin ve Azure 'a otomatik olarak dağıtın
> * Azure SQL Veritabanı'na bağlanma 
> * Kaynakları temizleme

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) aracılığıyla ücretsiz bir abonelik alabilirsiniz.

## <a name="create-a-project-in-devops-projects-for-an-aspnet-app-and-azure-sql-database"></a>ASP.NET uygulaması ve Azure SQL veritabanı için DevOps Projeleri bir proje oluşturma

DevOps Starter Azure Pipelines ' de bir CI/CD işlem hattı oluşturur. Yeni bir Azure DevOps kuruluşu oluşturabilir veya var olan bir kuruluşu kullanabilirsiniz. DevOps Starter Ayrıca Azure SQL veritabanı gibi tercih ettiğiniz Azure aboneliğinde Azure kaynakları da oluşturur.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Arama kutusuna **DevOps Starter** yazın ve ardından öğesini seçin. Yeni bir tane oluşturmak için **Ekle** ' ye tıklayın.

    ![DevOps başlangıç panosu](_img/azure-devops-starter-aks/search-devops-starter.png)

1. **.Net**' i seçin ve ardından **İleri**' yi seçin.

1. **Uygulama çerçevesi seçin** altında **ASP.net**' yi seçin.

1. **Veritabanı Ekle**' yi seçin ve ardından **İleri**' yi seçin. Önceki adımda seçtiğiniz uygulama çerçevesi, burada kullanılabilir olan Azure hizmet dağıtım hedefinin türünü belirler. 
    
1. **İleri**’yi seçin.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps ve bir Azure aboneliği yapılandırma

1. Yeni bir Azure DevOps organizasyonu oluşturun veya var olan bir kuruluşu seçin. 

1. Azure DevOps projeniz için bir ad girin. 

1. Azure abonelik hizmetlerinizi seçin. İsteğe bağlı olarak, ek Azure yapılandırma ayarlarını görüntülemek ve **veritabanı sunucusu oturum açma ayrıntıları** bölümünde Kullanıcı adını belirlemek için **Değiştir**' i seçebilirsiniz. Bu öğreticinin sonraki adımları için kullanıcı adınısaklayın. Bu isteğe bağlı adımı gerçekleştirirseniz, **bitti**' yi seçmeden önce Azure yapılandırma alanından çıkın.
 
1. **Bitti** seçeneğini belirleyin. Birkaç dakika sonra işlem tamamlanır ve DevOps başlangıç panosu Azure portal açılır. Ayrıca, panoda doğrudan Azure portal **tüm kaynaklardan** da gidebilirsiniz. Sağ tarafta, çalışan uygulamanızı görüntülemek için **Araştır** ' ı seçin.
    
## <a name="examine-the-ci-pipeline"></a>CI işlem hattını inceleme

DevOps Starter, Azure Repos içinde tam bir CI/CD işlem hattını otomatik olarak yapılandırır. İşlem hattını inceleyebilir ve özelleştirebilirsiniz. Azure DevOps derleme işlem hattını öğrenmek için aşağıdakileri yapın:

1. DevOps başlangıç panosunun en üstünde **derleme işlem hatları**' nı seçin. Bir tarayıcı sekmesi, yeni projeniz için derleme işlem hattını görüntüler.

1. **Durum** alanını işaret edin ve ardından üç nokta (...) simgesini seçin. Bir menü, yeni bir derlemeyi sıraya alma, bir derlemeyi duraklatma ve derleme işlem hattını düzenlemeyle çeşitli seçenekleri görüntüler.

1. **Düzenle**'yi seçin.

1. Bu bölmede, derleme işlem hattınızla ilgili çeşitli görevleri inceleyebilirsiniz. Derleme, git deposundan kaynak getirme, bağımlılıkları geri yükleme ve dağıtımlar için kullanılan yayınlama çıkışları gibi çeşitli görevleri gerçekleştirir.

1. Derleme işlem hattının üst kısmında derleme işlem hattı adı’nı seçin.

1. Derleme işlem hattınızı daha açıklayıcı bir şekilde değiştirin, **& kuyruğu kaydet**' i seçin ve ardından **Kaydet**' i seçin.

1. Derleme işlem hattı adınızın altında **Geçmiş**’i seçin. Bu bölme, derleme için son değişikliklerinizin denetim izini görüntüler. Azure Pipelines, derleme ardışık düzeninde yapılan tüm değişiklikleri izler ve sürümleri karşılaştırmanızı sağlar.

1. **Tetikleyiciler**’i seçin. DevOps Starter otomatik olarak bir CI tetikleyicisi oluşturur ve depoya yapılan her işlem yeni bir derleme başlatır. İsteğe bağlı olarak, CI işlemindeki dalları dahil etmek veya hariç tutmak seçebilirsiniz.

1. **Saklama**’yı seçin. Senaryonuza bağlı olarak, belirli sayıda derlemeyi tutmanın veya kaldırabilmeniz için ilkeler belirtebilirsiniz.

## <a name="examine-the-cd-pipeline"></a>CD işlem hattını inceleme

DevOps Starter, Azure DevOps kuruluşunuzdan Azure aboneliğinize dağıtım yapmak için gerekli adımları otomatik olarak oluşturur ve yapılandırır. Bu adımlar, Azure aboneliğinizdeki Azure DevOps kimlik doğrulaması için bir Azure hizmet bağlantısı yapılandırmayı içerir. Otomasyon Ayrıca CD 'yi Azure sanal makinesine sağlayan bir CD işlem hattı oluşturur. Azure DevOps CD işlem hattı hakkında daha fazla bilgi edinmek için aşağıdakileri yapın:

1. **Build ve Release**' i seçin ve ardından **yayınlar**' ı seçin. DevOps Starter, Azure dağıtımlarını yönetmek için bir yayın işlem hattı oluşturur.

1. Yayın işlem hattının yanındaki üç nokta (...) simgesini seçin ve ardından **Düzenle**' yi seçin. Yayın işlem hattı, yayın işlemini tanımlayan bir *işlem hattı* içerir.

1. **Yapıtlar**’ın altında **Bırak**’ı seçin. Önceki adımlarda inceettiğiniz derleme işlem hattı, yapıt için kullanılan çıktıyı üretir. 

1. **Bırakma** simgesinin sağ tarafında, **sürekli dağıtım tetikleyicisi**' ni seçin. Bu sürüm ardışık düzeninde, her yeni derleme yapıtı kullanılabilir olduğunda bir dağıtımı yürüten etkinleştirilmiş bir CD tetikleyicisi vardır. İsteğe bağlı olarak, dağıtımlarınızın el ile yürütme gerektirdiğinden tetikleyiciyi devre dışı bırakabilirsiniz. 

    DevOps Starter, rastgele bir SQL parolası ayarlar ve bunu sürüm ardışık düzeni için kullanır.
    
1. Sol tarafta **değişkenler**' i seçin. 

   > [!NOTE]
   > Yalnızca SQL Server parolasını değiştirdiyseniz aşağıdaki adımı gerçekleştirin. Tek bir parola değişkeni vardır.
  
1. **Değer** kutusunun yanındaki asma kilit simgesini seçin, yeni parolayı girin ve ardından **Kaydet**' i seçin.

1. Solda, **Görevler**' i seçin ve ardından ortamınızı seçin. Görevler, dağıtım işleminizin yürütüldüğü etkinliklerdir ve aşamalar halinde gruplandırılır. Bu yayın işlem hattının bir *Azure App Service dağıtımı* ve *Azure SQL veritabanı dağıtım* görevi içeren tek bir aşaması vardır.

1. *Azure SQL Yürüt* görevini SEÇIN ve SQL dağıtımı için kullanılan çeşitli özellikleri inceleyin. **Dağıtım paketi** altında, görev BIR *SQL dacpac* dosyası kullanır.

1. Sağ tarafta, sürümlerin geçmişini görüntülemek için **yayınları görüntüle** ' yi seçin.

1. Bir yayının yanındaki üç nokta (...) simgesini seçin ve sonra **Aç**' ı seçin. Yayın Özeti, ilişkili iş öğeleri ve testler gibi çeşitli menüleri inceleyebilirsiniz.

1. **İşlemeler**'i seçin. Bu görünüm, bu dağıtımla ilişkili kod işlemelerini gösterir. Dağıtımlar arasındaki işleme farklılıklarını görmek için yayınları karşılaştırın.

1. **Günlükleri** seçin. Günlüklerde, dağıtım işlemiyle ilgili yararlı bilgiler bulunur. Bunları, dağıtımları sırasında ve sonrasında görüntüleyebilirsiniz.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Azure Repos değişiklikleri işleyin ve Azure 'a otomatik olarak dağıtın 

 > [!NOTE]
 > Aşağıdaki yordam, CI/CD işlem hattını basit bir metin değişikliğine karşı sınar. SQL dağıtım sürecini test etmek için isteğe bağlı olarak tabloda bir SQL Server şeması değişikliği yapabilirsiniz.

Şimdi, en son çalışmalarınızı otomatik olarak Web sitenize dağıtan bir CI/CD işlemi kullanarak uygulamanızdaki bir takımla işbirliği yapmaya hazırsınız. Git deposunda yapılan her değişiklik Azure DevOps 'da bir derleme başlatır ve bir CD işlem hattı Azure 'a bir dağıtım yürütür. Bu bölümdeki yordamı izleyin veya değişiklikleri deponuza uygulamak için başka bir teknik kullanın. Kod değişiklikleri CI/CD işlemini başlatır ve değişikliklerinizi otomatik olarak Azure 'a dağıtır.

1. Sol bölmede **kod**' ı seçin ve ardından deponuza gidin.

1. *Samplewebapplication\views\home* dizinine gidin, *Index. cshtml* dosyasının yanındaki üç nokta (...) simgesini seçin ve ardından **Düzenle**' yi seçin. 

1. Dosyada, div etiketlerinden birine metin ekleme gibi bir değişiklik yapın. 

1. Sağ üst köşedeki **Kaydet**' i seçin ve sonra değişikliklerinizi göndermek Için yeniden **Yürüt** ' ü seçin. Birkaç dakika sonra, bir derleme Azure DevOps 'da başlar ve değişiklikleri dağıtmak için bir yayın yürütülür. Yapı durumunu DevOps başlangıç panosunda veya tarayıcıda Azure DevOps kuruluşunuzla izleyin.

1. Yayın tamamlandıktan sonra, değişikliklerinizi doğrulamak için uygulamanızı yenileyin.

## <a name="connect-to-azure-sql-database"></a>Azure SQL Veritabanı'na bağlanma

Azure SQL veritabanı 'na bağlanmak için uygun izinlere sahip olmanız gerekir.

1. DevOps başlangıç **panosunda SQL veritabanı ' nı seçerek SQL** veritabanı yönetim sayfasına gidin.
   
1. **Sunucu güvenlik duvarını ayarla**' yı seçin ve **Istemci IP 'si Ekle**' yi seçin. 

1. **Kaydet**’i seçin. İstemci IP 'niz artık SQL Server Azure kaynağına erişimi vardır.

1. **SQL veritabanı** bölmesine geri dönün. 

1. Sağ tarafta, **SQL Server** için yapılandırma sayfasına gitmek üzere sunucu adını seçin.

1. **Parolayı Sıfırla**' yı seçin, SQL Server yönetici oturumu için bir parola girin ve ardından **Kaydet**' i seçin. Bu öğreticide daha sonra kullanmak üzere bu parolayı kaydettiğinizden emin olun.

    İsteğe bağlı olarak, SQL Server ve Azure SQL veritabanı 'na bağlanmak için SQL Server Management Studio veya Visual Studio gibi istemci araçlarını kullanabilirsiniz. Bağlanmak için **Sunucu adı** özelliğini kullanın.

    DevOps Projeleri içinde projeyi başlangıçta yapılandırdığınızda veritabanı kullanıcı adını değiştirmediyseniz, Kullanıcı adınız e-posta adresinizin yerel kısmıdır. Örneğin, e-posta adresiniz *cetikan \@ Microsoft.com* ise, Kullanıcı adınız *johntikan* olur.

   > [!NOTE]
   > SQL oturum açma parolasını değiştirirseniz, sürüm ardışık düzen değişkeninde bulunan parolayı, CD işlem hattını [İnceleme](#examine-the-cd-pipeline) bölümünde açıklandığı gibi değiştirmeniz gerekir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Test ediyorsanız, kaynaklarınızı temizleyerek faturalandırma ücretlerinden kaçınabilirsiniz. Artık gerekli olmadığında, bu öğreticide oluşturduğunuz Azure SQL veritabanını ve ilgili kaynakları silebilirsiniz. Bunu yapmak için DevOps başlangıç panosundaki **silme** işlevini kullanın.

> [!IMPORTANT]
> Aşağıdaki yordam kaynakları kalıcı olarak siler. *Silme* işlevselliği, hem Azure hem de Azure DevOps 'Daki, DevOps Starter 'daki proje tarafından oluşturulan verileri yok eder ve bu işlemi geri alamazsınız. Bu yordamı yalnızca istemleri dikkatle okuduktan sonra kullanın.

1. Azure portal DevOps başlangıç panosuna gidin.
2. Sağ üst köşedeki **Sil**' i seçin. 
3. İstem sırasında, kaynakları *kalıcı olarak silmek* için **Evet** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Ekibinizin gereksinimlerine uygun olarak bu derleme ve yayın işlem hatlarını istediğiniz gibi değiştirebilirsiniz. Ayrıca bu CI/CD desenini diğer işlem hatlarınızda şablon olarak kullanabilirsiniz. Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * ASP.NET uygulamanızı ve Azure SQL veritabanı kodunuzu dağıtmak için Azure DevOps Starter 'ı kullanma
> * Azure DevOps ve bir Azure aboneliği yapılandırma 
> * CI işlem hattını inceleme
> * CD işlem hattını inceleme
> * Azure Repos değişiklikleri işleyin ve Azure 'a otomatik olarak dağıtın
> * Azure SQL Veritabanı'na bağlanma 
> * Kaynakları temizleme

CI/CD işlem hattı hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Çoklu aşamalı sürekli dağıtım (CD) işlem hattınızı tanımlama](/azure/devops/pipelines/release/define-multistage-release-process)

## <a name="videos"></a>Videolar

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]