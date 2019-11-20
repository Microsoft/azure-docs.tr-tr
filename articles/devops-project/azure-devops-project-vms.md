---
title: 'Öğretici: Azure DevOps Projeleri kullanarak ASP.NET uygulamanızı Azure sanal makinelerine dağıtma'
description: DevOps Projeleri, Azure 'da kullanmaya başlamanızı ve ASP.NET uygulamanızı birkaç hızlı adımda Azure sanal makinelerine dağıtmayı kolaylaştırır.
ms.author: mlearned
manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: b03fb27c46d4fd925b91b8927fdd50ff4912aaec
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71969552"
---
# <a name="tutorial-deploy-your-aspnet-app-to-azure-virtual-machines-by-using-azure-devops-projects"></a>Öğretici: Azure DevOps Projeleri kullanarak ASP.NET uygulamanızı Azure sanal makinelerine dağıtma

Azure DevOps Projeleri, mevcut kodunuzu ve git deponuzu getirebileceğiniz veya Azure 'a sürekli tümleştirme (CI) ve sürekli teslim (CD) işlem hattı oluşturmak için örnek bir uygulama seçebileceğiniz basitleştirilmiş bir deneyim sunar. 

Ayrıca DevOps Projeleri:
* , Yeni bir Azure sanal makinesi (VM) gibi Azure kaynaklarını otomatik olarak oluşturur.
* CI için derleme işlem hattı içeren Azure DevOps 'da bir yayın işlem hattı oluşturur ve yapılandırır.
* CD için bir yayın işlem hattı oluşturur. 
* İzleme için bir Azure Application Insights kaynağı oluşturur.

Bu öğreticide şunları yapacaksınız:

> [!div class="checklist"]
> * ASP.NET uygulamanızı dağıtmak için DevOps Projeleri kullanma
> * Azure DevOps ve bir Azure aboneliği yapılandırma 
> * CI işlem hattını inceleme
> * CD işlem hattını inceleme
> * Azure Repos değişiklikleri işleyin ve Azure 'a otomatik olarak dağıtın
> * Application Insights izlemeyi yapılandırma
> * Kaynakları temizleme

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) aracılığıyla ücretsiz bir abonelik alabilirsiniz.

## <a name="use-devops-projects-to-deploy-your-aspnet-app"></a>ASP.NET uygulamanızı dağıtmak için DevOps Projeleri kullanma

DevOps Projeleri, Azure Pipelines bir CI/CD işlem hattı oluşturur. Yeni bir Azure DevOps kuruluşu oluşturabilir veya var olan bir kuruluşu kullanabilirsiniz. DevOps Projeleri ayrıca, seçtiğiniz Azure aboneliğindeki sanal makineler gibi Azure kaynaklarını da oluşturur.

1. [Azure Portal](https://portal.azure.com)’ında oturum açın.

1. Sol bölmede **Yeni**' yi seçin.

1. Arama kutusuna **DevOps Projeleri**yazın ve ardından **Oluştur**' u seçin.

    ![DevOps Projeleri panosu](_img/azure-devops-project-github/fullbrowser.png)

1. **.Net**' i seçin ve ardından **İleri**' yi seçin.

1. **Uygulama çerçevesi seçin**altında **ASP.net**' ı seçin ve ardından **İleri**' yi seçin.  
    Önceki adımda seçtiğiniz uygulama çerçevesi, burada kullanılabilir olan Azure hizmet dağıtım hedefinin türünü belirler. 

1. Sanal makineyi seçin ve ardından **İleri**' yi seçin.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps ve bir Azure aboneliği yapılandırma

1. Yeni bir Azure DevOps organizasyonu oluşturun veya var olan bir kuruluşu seçin. 

1. Azure DevOps projeniz için bir ad girin. 

1. Azure abonelik hizmetlerinizi seçin.  
    İsteğe bağlı olarak **Değiştir** ' i seçip Azure kaynaklarının konumu gibi daha fazla yapılandırma ayrıntıları girebilirsiniz.
 
1. Yeni Azure sanal makine kaynağınız için bir sanal makine adı, Kullanıcı adı ve parola girin ve **bitti**' yi seçin.  
    Birkaç dakika sonra Azure sanal makinesi hazırlanacaktır. Azure DevOps kuruluşunuzda bir depoda bir örnek ASP.NET uygulaması ayarlanır, derleme ve yayın yürütülür ve uygulamanız yeni oluşturulan Azure VM 'ye dağıtılır. 

    Tamamlandıktan sonra, DevOps Projeleri panosu Azure portal görüntülenir. Ayrıca, panoda doğrudan Azure portal **tüm kaynaklardan** da gidebilirsiniz. 

    Pano, Azure DevOps kod deponuzu, CI/CD işlem hattınızı ve çalışan uygulamanızı Azure 'da görünürlük sağlar.   

    ![Pano görünümü](_img/azure-devops-project-vms/dashboardnopreview.png)

DevOps Projeleri, depoya kod değişiklikleri dağıtan bir CI derlemesini ve yayın tetikleyicisini otomatik olarak yapılandırır. Azure DevOps’ta başka seçenekleri de yapılandırabilirsiniz. Çalışan uygulamanızı görüntülemek için, **Araştır**' ı seçin.
    
## <a name="examine-the-ci-pipeline"></a>CI işlem hattını inceleme
 
DevOps Projeleri, Azure Pipelines otomatik olarak bir CI/CD işlem hattı yapılandırdı. İşlem hattını inceleyebilir ve özelleştirebilirsiniz. Derleme işlem hattını öğrenmek için aşağıdakileri yapın:

1. DevOps Projeleri panosunun en üstünde **derleme Işlem hatları**' nı seçin.  
    Bir tarayıcı sekmesi, yeni projeniz için derleme işlem hattını görüntüler.

1. **Durum** alanını işaret edin ve ardından üç nokta (...) simgesini seçin.  
    Bir menü, yeni bir derlemeyi sıraya alma, bir derlemeyi duraklatma ve derleme işlem hattını düzenlemeyle çeşitli seçenekleri görüntüler.

1. **Düzenle**’yi seçin.

1. Bu bölmede, derleme işlem hattınızla ilgili çeşitli görevleri inceleyebilirsiniz.  
    Derleme git deposundan kaynak getirme, bağımlılıkları geri yükleme ve dağıtımlar için kullanılan yayınlama çıkışları gibi çeşitli görevleri gerçekleştirir.

1. Yapı işlem hattının en üstünde derleme işlem hattı adını seçin.

1. Derleme işlem hattınızı daha açıklayıcı bir şekilde değiştirin, **& kuyruğu kaydet**' i seçin ve ardından **Kaydet**' i seçin.

1. Derleme işlem hattı adınızın altında **Geçmiş**’i seçin.  
    Bu bölme, derleme için son değişikliklerinizin denetim izini görüntüler. Azure DevOps, derleme ardışık düzeninde yapılan tüm değişiklikleri izler ve sürümleri karşılaştırmanızı sağlar.

1. **Tetikleyiciler**’i seçin.  
    DevOps Projeleri otomatik olarak bir CI tetikleyicisi oluşturur ve depoya yapılan her bir işleme yeni bir derleme başlatır. İsteğe bağlı olarak, CI işlemindeki dalları dahil etmek veya hariç tutmak seçebilirsiniz.

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

1. **Bırakma** simgesinin yanındaki **sürekli dağıtım tetikleyicisi**' ni seçin.  
    Bu sürüm ardışık düzeninde, her yeni derleme yapıtı kullanılabilir olduğunda bir dağıtımı yürüten etkinleştirilmiş bir CD tetikleyicisi vardır. İsteğe bağlı olarak, dağıtımlarınızın el ile yürütme gerektirdiğinden tetikleyiciyi devre dışı bırakabilirsiniz. 

1. Solda, **Görevler**' i seçin ve ardından ortamınızı seçin.  
    Görevler, dağıtım işleminizin yürütüldüğü etkinliklerdir ve aşamalar halinde gruplandırılır. Bu yayın işlem hattı iki aşamada gerçekleşir:
    * İlk aşamada iki şey yapan bir Azure Kaynak grubu dağıtım görevi bulunur:
      * VM 'yi dağıtım için yapılandırır
      * Yeni VM 'yi bir Azure DevOps dağıtım grubuna ekler. Azure DevOps 'daki VM dağıtım grubu, dağıtım hedef makinelerinin mantıksal gruplarını yönetir.
    * İkinci aşamada, bir IIS Web uygulaması Yönet görevi VM 'de bir IIS Web sitesi oluşturur. Siteyi dağıtmak için ikinci bir IIS Web uygulaması dağıtım görevi oluşturulur.

1. Sağ tarafta, sürümlerin geçmişini görüntülemek için **yayınları görüntüle** ' yi seçin.

1. Bir yayının yanındaki üç nokta (...) simgesini seçin ve sonra **Aç**' ı seçin.  
    Yayın Özeti, ilişkili iş öğeleri ve testler gibi çeşitli menüleri inceleyebilirsiniz.

1. **İşlemeler**'i seçin.  
    Bu görünüm, bu dağıtımla ilişkili kod işlemelerini gösterir. Dağıtımlar arasındaki işleme farklılıklarını görmek için yayınları karşılaştırın.

1. **Günlükler**’i seçin.  
    Günlüklerde, dağıtım işlemiyle ilgili yararlı bilgiler bulunur. Bunları, dağıtımları sırasında ve sonrasında görüntüleyebilirsiniz.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Azure Repos değişiklikleri işleyin ve Azure 'a otomatik olarak dağıtın 

Şimdi, en son çalışmalarınızı otomatik olarak Web sitenize dağıtan bir CI/CD işlemi kullanarak uygulamanızdaki bir takımla işbirliği yapmaya hazırsınız. Git deposunda yapılan her değişiklik Azure DevOps 'da bir derleme başlatır ve bir CD işlem hattı Azure 'a bir dağıtım yürütür. Bu bölümdeki yordamı izleyin veya değişiklikleri deponuzda uygulamak için başka bir teknik kullanın. Kod değişiklikleri CI/CD işlemini başlatır ve değişikliklerinizi Azure VM 'de IIS Web sitesinde otomatik olarak dağıtır.

1. Sol bölmede **kod**' ı seçin ve ardından depoya gidin.

1. *Views\home* dizinine gidin, *Index. cshtml* dosyasının yanındaki üç nokta (...) simgesini seçin ve ardından **Düzenle**' yi seçin.

1. Dosyada, div etiketlerinden birine metin ekleme gibi bir değişiklik yapın. 

1. Sağ üst köşedeki **Kaydet**' i seçin ve sonra değişikliklerinizi göndermek Için yeniden **Yürüt** ' ü seçin.  
    Birkaç dakika sonra, bir derleme Azure DevOps 'da başlar ve değişiklikleri dağıtmak için bir yayın yürütülür. DevOps Projeleri panosunda veya Azure DevOps kuruluşunuzla tarayıcıda derleme durumunu izleyin.

1. Yayın tamamlandıktan sonra, değişikliklerinizi doğrulamak için uygulamanızı yenileyin.

## <a name="configure-azure-application-insights-monitoring"></a>Application Insights izlemeyi yapılandırma

Azure Application Insights ile, uygulamanızın performansını ve kullanımını kolayca izleyebilirsiniz. DevOps Projeleri, uygulamanız için Application Insights kaynağını otomatik olarak yapılandırır. Gerekirse başka uyarılar ve izleme özellikleri de yapılandırabilirsiniz.

1. Azure portal, DevOps Projeleri panosuna gidin. 

1. Sağ alt köşedeki uygulamanızın **Application Insights** bağlantısını seçin.  
    **Application Insights** bölmesi açılır. Bu görünüm uygulamanızın kullanım, performans ve kullanılabilirlik izleme bilgilerini içerir.

    ![Application Insights bölmesi](_img/azure-devops-project-github/appinsights.png) 

1. **Zaman aralığı**' nı seçin ve ardından **son saat**' i seçin. Sonuçları filtrelemek için **Güncelleştir**' i seçin.  
    Artık son 60 dakikadan tüm etkinlikleri görüntüleyebilirsiniz. 
    
1. Zaman aralığından çıkmak için **x**' i seçin.

1. **Uyarılar**' ı seçin ve ardından **ölçüm uyarısı Ekle**' yi seçin. 

1. Uyarı için bir ad girin.

1. **Ölçüm** açılan listesinde çeşitli uyarı ölçümlerini inceleyin.  
    Varsayılan uyarı, **1 saniyeden uzun sunucu yanıt süresi** içindir. Çeşitli uyarıları kolayca yapılandırıp uygulamanızın izleme özelliklerini geliştirebilirsiniz.

1. **E-posta sahipleri, katkıda bulunanlar ve okuyucular aracılığıyla bildir** onay kutusunu seçin.  
    İsteğe bağlı olarak, bir Azure mantıksal uygulaması yürüterek bir uyarı görüntülendiğinde ek eylemler gerçekleştirebilirsiniz.

1. Uyarıyı oluşturmak için **Tamam ' ı** seçin.  
    Birkaç dakika sonra, uyarı panoda etkin olarak görünür. 

1. **Uyarılar** alanından çıkıp **Application Insights** bölmesine geri dönün.

1. **Kullanılabilirlik**' i seçin ve ardından **Test Ekle**' yi seçin. 

1. Bir test adı girin ve ardından **Oluştur**' u seçin.  
    Uygulamanızın kullanılabilirliğini doğrulamak için basit bir ping testi oluşturulur. Birkaç dakika sonra, test sonuçları sağlanır ve Application Insights panosu bir kullanılabilirlik durumu görüntüler.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Test ediyorsanız, kaynaklarınızı temizleyerek faturalandırma ücretlerinden kaçınabilirsiniz. Artık gerekli olmadığında, bu öğreticide oluşturduğunuz Azure sanal makinesini ve ilgili kaynakları silebilirsiniz. Bunu yapmak için DevOps Proje panosundaki **Delete** işlevini kullanın. 

> [!IMPORTANT]
> Aşağıdaki yordam kaynakları kalıcı olarak siler. *Silme* işlevselliği, proje tarafından oluşturulan verileri hem Azure hem de Azure devops içinde DevOps Projeleri yok eder ve bu işlemi geri alamazsınız. Bu yordamı yalnızca istemleri dikkatle okuduktan sonra kullanın.

1. Azure portal, DevOps Projeleri panosuna gidin.
1. Sağ üst köşedeki **Sil**' i seçin. 
1. İstem sırasında, kaynakları *kalıcı olarak silmek* için **Evet** ' i seçin.

Ekibinizin gereksinimlerine uygun olarak bu derleme ve yayın işlem hatlarını istediğiniz gibi değiştirebilirsiniz. Ayrıca bu CI/CD desenini diğer işlem hatlarınızda şablon olarak kullanabilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * ASP.NET uygulamanızı dağıtmak için DevOps Projeleri kullanma
> * Azure DevOps ve bir Azure aboneliği yapılandırma 
> * CI işlem hattını inceleme
> * CD işlem hattını inceleme
> * Azure Repos değişiklikleri işleyin ve Azure 'a otomatik olarak dağıtın
> * Application Insights izlemeyi yapılandırma
> * Kaynakları temizleme

CI/CD işlem hattı hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Çoklu aşamalı sürekli dağıtım (CD) işlem hattınızı tanımlama](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
