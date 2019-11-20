---
title: Takım için görevleri takım veri bilimi işlemi takıma sağlama
description: Ekip veri bilimi süreç ekibi üzerinde bir takım için görevlere ilişkin ayrıntılı bir anlatım
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/23/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9c6d0fcc20afc613094f10e9f3fb7c917ec6fa73
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327242"
---
# <a name="tasks-for-the-team-lead-on-a-team-data-science-process-team"></a>Takım veri bilimi süreç ekibi üzerinde ekip liderine yönelik görevler

Bu makalede, bir *ekibin* veri bilimi ekibi için tamamladığı görevler açıklanmaktadır. Ekip lideri hedefi, [ekip veri bilimi işlemini](overview.md) (TDSP) standartlaştırmaya yönelik işbirliği yapan bir ekip ortamı kurmaya yönelik. TDSP, işbirliğinin ve Team Learning 'in artırılmasına yardımcı olmak için tasarlanmıştır. 

TDSP, tahmine dayalı analiz çözümlerini ve akıllı uygulamaları verimli bir şekilde sunmak için çevik ve yinelemeli bir veri bilimi metodolojisidir. Süreç, Microsoft 'un ve sektördeki en iyi uygulama ve yapıların, şirketlerin analiz programlarının avantajlarından tamamen yararlanmalarına yardımcı olmak için veri bilimi girişimlerinin başarılı bir şekilde uygulanması için gerekli olan en iyi yöntem ve yapıların bir bölümünü açığa çıkarın. TDSP üzerinde standartlaştırarak bir veri bilimi ekibi için personel rollerinin ve ilişkili görevlerin bir özeti için bkz. [Team Data Science işlem rolleri ve görevleri](roles-tasks.md).

Bir ekip lideri, bir kuruluşun veri bilimi birimindeki çeşitli veri bilimlerinden oluşan bir ekibi yönetir. Veri bilimi biriminin boyutuna ve yapısına bağlı olarak, [Grup Yöneticisi](group-manager-tasks.md) ve ekip lideri aynı kişi olabilir ya da görevlerini yedeklerin kapılarına devredebilir. Ancak görevlerin kendileri değiştirmeyin. 

Aşağıdaki diyagramda ekip lideri bir takım ortamını ayarlamak için tamamlandıkça ekip lideri görevlere ilişkin iş akışı gösterilmektedir:

![Ekip lideri görev iş akışı](./media/team-lead-tasks/team-leads-1-creating-teams.png)

1. Azure DevOps 'da grubun kuruluşunda bir **Takım projesi** oluşturun. 
  
1. Varsayılan takım deposunu **Ekipçlikleri**olarak yeniden adlandırın.
  
1. Takım projesinde yeni bir **Teamtemplate** deposu oluşturun. 
  
1. Grubun **Grouputilities** ve **groupprojecttemplate** depolarındaki içerikleri **ekipte** , ekip **şablonu** depolarında içeri aktarın. 
  
1. Takım üyeleri ekleyerek ve izinlerini yapılandırarak **güvenlik denetimini** ayarlayın.
  
1. Gerekirse, takım verileri ve analiz kaynakları oluşturun:
   - **Ekipte izin deposuna ekibe** özel yardımcı programlar ekleyin. 
   - Tüm takım için yararlı olabilecek veri varlıklarını depolamak için **Azure dosya depolama alanı** oluşturun. 
   - Azure dosya depolama alanını ekip lideri **veri bilimi sanal makinesi** (dsvm) ile bağlayın ve bu verilere veri varlıkları ekleyin.

Aşağıdaki öğreticide, ayrıntılı adımlarda adım adım gösterilmektedir.

> [!NOTE] 
> Bu makalede, Microsoft 'ta TDSP 'nin uygulanması nedeniyle bir TDSP ekip ortamı ayarlamak için Azure DevOps ve DSVM kullanılmaktadır. Takımınız diğer kod barındırma veya geliştirme platformlarını kullanıyorsa, ekip sağlama görevleri aynıdır, ancak bunları tamamlamaya yönelik yol farklı olabilir.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide, aşağıdaki kaynakların ve izinlerin [Grup yöneticiniz](group-manager-tasks.md)tarafından ayarlandığı varsayılır:

- Veri biriminiz için Azure DevOps **organizasyonu**
- **Groupprojecttemplate** ve **Grouputilities** depoları, Microsoft TDSP ekibinin **ProjectTemplate** ve **yardımcı program** depoları içeriğiyle doldurulmuştur
- Takımınız için projeler ve depolar oluşturmanız için kuruluşunuzun hesabındaki izinler

Depoları kopyalayabilir ve yerel makinenizde veya DSVM 'de içeriklerini değiştirebilir veya Azure dosya depolama alanını ayarlayıp DSVM 'nize bağlayabilmek için şunlar gerekir:

- Azure aboneliği.
- Git makinenizde yüklü. DSVM kullanıyorsanız git önceden yüklüdür. Aksi takdirde bkz [platformları ve araçlarıyla ek](platforms-and-tools.md#appendix).
- DSVM 'yi, Azure 'da oluşturulan ve yapılandırılan Windows veya Linux DSVM 'yi kullanmak istiyorsanız. Daha fazla bilgi ve yönergeler için [veri bilimi sanal makinesi belgelerine](/azure/machine-learning/data-science-virtual-machine/)bakın.
- Bir Windows DSVM için, makinenizde yüklü [Git kimlik bilgileri Yöneticisi (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) . *README.MD* dosyasında, **indir ve yükle** bölümüne gidin ve **en son yükleyiciyi**seçin. Yükleyici sayfasından *. exe* yükleyicisini indirin ve çalıştırın. 
- Bir Linux DSVM için, DSVM 'niz üzerinde ayarlanan ve Azure DevOps 'a eklenen bir SSH ortak anahtarı. Daha fazla bilgi ve yönergeler için [platformlar ve araçlar EKINDE](platforms-and-tools.md#appendix) **SSH ortak anahtarı oluşturma** bölümüne bakın. 

## <a name="create-a-team-project-and-repositories"></a>Takım projesi ve depoları oluşturma

Bu bölümde, grubunuzun Azure DevOps kuruluşunda aşağıdaki kaynakları oluşturursunuz:

- Azure DevOps 'daki **myTeam** projesi
- **Teamtemplate** deposu
- **Ekipçlikleri** deposu

Bu öğreticideki depolar ve dizinler için belirtilen adlar, büyük veri bilimi kuruluşunuzda kendi ekibiniz için ayrı bir proje kurmak istediğinizi varsayar. Bununla birlikte, tüm grup, Grup Yöneticisi veya kuruluş yöneticisi tarafından oluşturulan tek bir proje altında çalışmayı tercih edebilir. Ardından, tüm veri bilimi takımları bu tek proje kapsamında depolar oluşturur. Bu senaryo için geçerli olabilir:
- Birden çok veri bilimi ekibi olmayan küçük bir veri bilimi grubu. 
- Aynı şekilde, grup düzeyi Sprint planlaması gibi etkinliklerle ekip içi işbirliğini iyileştirmek isteyen birden fazla veri bilimi ekibine sahip daha büyük bir veri bilimi grubu. 

Ekipler tek bir grup projesi altında, takımlarına özgü depolara sahip olmasını tercih ediyorsanız, ekip müşteri adayları  *\<TeamName > şablonu* ve  *\<TeamName > yardımcı programları*gibi adlara sahip depolar oluşturmamalıdır. Örneğin: *Teamatemplate* ve *teamautilities*. 

Herhangi bir durumda, takımın, takım üyelerinin hangi şablon ve yardımcı program depoları ayarlayabileceklerini ve kopyalayabileceklerini bilmesini sağlamak gerekir. Proje liderleri, ayrı projeler veya tek bir proje kapsamında olmak üzere, [bir veri bilimi ekibinin proje depoları oluşturması için proje sağlama görevlerini](project-lead-tasks.md) izlemelidir. 

### <a name="create-the-myteam-project"></a>MyTeam projesi oluşturma

Takımınız için ayrı bir proje oluşturmak için:

1. Web tarayıcınızda, URL *https:\//\<sunucu adı >/\<kuruluş adı >* konumundaki grubunuzun Azure DevOps kuruluş giriş sayfasına gidin ve **Yeni proje**' yi seçin. 
   
   ![Yeni proje seçin](./media/team-lead-tasks/team-leads-2-create-new-team.png)
   
1. **Proje oluştur** iletişim kutusunda, **Proje adı**' nın altındaki *myTeam*adını girin ve **Gelişmiş**' i seçin. 
   
1. **Sürüm denetimi**altında **Git**' i seçin ve **iş öğesi işlemi**altında **çevik**' i seçin. Ardından **Oluştur**’u seçin. 
   
   ![Proje oluşturma](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)
   
Takım projesi **Özet** sayfası açılarak sayfa URL 'si *https\/:/\<sunucu adı >/\<kuruluş adı >/\<takım adı >* .

### <a name="rename-the-myteam-default-repository-to-teamutilities"></a>MyTeam varsayılan deposunu Ekipçonelikler olarak yeniden adlandırın

1. **MyTeam** Project **Summary** sayfasında, **hangi hizmete başlamak istersiniz?** altında, **Depo**' yı seçin. 
   
   ![Depoları seçin](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)
   
1. **MyTeam** Deposu sayfasında, sayfanın üst kısmındaki **myTeam** deposunu seçin ve açılan listeden **depoları Yönet** ' i seçin. 
   
   ![Depoları Yönet ' i seçin](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)
1. **Proje ayarları** sayfasında, **myTeam** deposunun yanındaki **...** öğesini seçin ve ardından **Depoyu yeniden adlandır**' ı seçin. 
   
   ![Depoyu yeniden adlandır ' ı seçin](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)
   
1. **MyTeam Repository 'Yi yeniden adlandır** açılır penceresinde *ekipçlikleri*girin ve **Yeniden Adlandır**' ı seçin. 

### <a name="create-the-teamtemplate-repository"></a>TeamTemplate deposunu oluşturma

1. **Proje ayarları** sayfasında **yeni depo** ' yı seçin. 
   
   ![Yeni depo Seç](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)
   
   Ya da, **mytakım** projesi **Özeti** sayfasının sol gezinti menüsünden depo ' yı seçin, sayfanın üst kısmındaki bir **depoyu seçin ve** açılan menüden **yeni depo** ' ı seçin.
   
1. **Yeni depo oluştur** Iletişim kutusunda **tür**altında **Git** ' in seçildiğinden emin olun. **Depo adı**altında *teamtemplate* ' i girip **Oluştur**' u seçin.
   
   ![Depo oluştur](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)
   
1. Proje ayarları sayfanızda iki depo **Ekipçinin** ve **Takım şablonunun şablonunu** görebilecekleri onaylayın. 
   
   ![İki takım deposu](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)

### <a name="import-the-contents-of-the-group-common-repositories"></a>Grubun içeriğini içeri aktarma ortak depoları

Ekip depolarınızı grup yöneticiniz tarafından ayarlanan ortak depoların içeriğiyle doldurmak için:

1. **MyTeam** Project giriş sayfamınızda sol gezinti bölmesinde bulunan **Depo** ' yı seçin. **MyTeam** şablonunun bulunamadığını belirten bir ileti alırsanız, **Aksi halde bağlantıyı seçin, varsayılan teamtemplate deponuza gidin.** 
   
   Varsayılan **Teamtemplate** deposu açılır. 
   
1. **Teamtemplate boş** sayfasında **içeri aktar**' ı seçin. 
   
   ![Içeri aktar 'ı seçin](./media/team-lead-tasks/import-repo.png)
   
1. **Git deposunu Içeri aktar** iletişim kutusunda, **kaynak türü**olarak **Git** ' i seçin ve **kopya URL**'si altında grup ortak şablon deponuzun URL 'sini girin. URL, *https: \/ @ no__t-2 @ no__t-3sunucu adı >/\<kuruluş adı >/_git/\<repository adı >* . Örneğin: *https: \//dev. Azure. com/Databilimenceunit/GroupCommon/_Git/GroupProjectTemplate*. 
   
1. **Içeri aktar**' ı seçin. Grup şablonu deponuzun içeriği takım şablonu deponuza aktarılır. 
   
   ![Grup ortak şablon deposunu içeri aktar](./media/team-lead-tasks/import-repo-2.png)
   
1. Projenizin **depoları** sayfasının üst kısmında, açılır ve **ekip** oluşturma deposunu seçin.
   
1. Grup ortak yardımcı programları deponuzun (örneğin, *Grouputilities*) içeriğini **ekipte** izin deponuza aktarmak için içeri aktarma işlemini tekrarlayın. 
   
İki takım depolarınızdan her biri artık ilgili grup ortak deposundaki dosyaları içerir. 

### <a name="customize-the-contents-of-the-team-repositories"></a>Ekip depolarının içeriğini özelleştirme

Takımınızın belirli ihtiyaçlarını karşılamak üzere ekip depolarınızın içeriğini özelleştirmek istiyorsanız, bunu şimdi yapabilirsiniz. Dosyaları değiştirebilir, dizin yapısını değiştirebilir veya dosya ve klasör ekleyebilirsiniz.

Doğrudan Azure DevOps 'da dosya veya klasörleri değiştirmek, karşıya yüklemek veya oluşturmak için:

1. **MyTeam** Project **Summary** sayfasında, **Repos**' u seçin. 
   
1. Sayfanın üst kısmında, özelleştirmek istediğiniz depoyu seçin.

1. Depo dizin yapısında, değiştirmek istediğiniz klasöre veya dosyaya gidin. 
   
   - Yeni klasör veya dosya oluşturmak için **Yeni**' nin yanındaki oku seçin. 
     
     ![Yeni dosya oluştur](./media/team-lead-tasks/new-file.png)
     
   - Dosyaları karşıya yüklemek için, **karşıya dosya yükle**' yi seçin. 
     
     ![Dosyaları karşıya yükleme](./media/team-lead-tasks/upload-files.png)
     
   - Varolan dosyaları düzenlemek için dosyasına gidin ve ardından **Düzenle**' yi seçin. 
     
     ![Bir dosyayı düzenleme](./media/team-lead-tasks/edit-file.png)
     
1. Dosya ekledikten veya düzenledikten sonra, **Yürüt**' ü seçin.
   
   ![Değişiklikleri Kaydet](./media/team-lead-tasks/commit.png)

Yerel makinenizdeki veya DSVM 'nizin depolarıyla çalışmak için, önce depoları yerel makinenize kopyalar veya *klonlayın* , sonra değişikliklerinizi oluşturup paylaşılan ekip depolarına gönderirsiniz. 

Depoları kopyalamak için:

1. **MyTeam** Project **Summary** sayfasında, **Repos**' ı seçin ve sayfanın en üstünde, kopyalamak istediğiniz depoyu seçin.
   
1. Depo sayfasında sağ üstteki **kopya** ' ı seçin.
   
1. **Depoyu Kopyala** iletişim kutusunda, **komut satırı**altında, BIR SSH bağlantısı Için http bağlantısı veya **SSH** için **https** ' yi seçin ve kopya URL 'sini panonuza kopyalayın.
   
   ![Kopya URL 'sini Kopyala](./media/team-lead-tasks/clone.png)
   
1. Yerel makinenizde aşağıdaki dizinleri oluşturun:
   
   - Windows için: **C:\GitRepos\MyTeam**
   - Linux için **$Home/gitrepos/myTeam** 
   
1. Oluşturduğunuz dizine geçin.
   
1. Git Bash 'de, `git clone <clone URL>` \<kopya URL 'si > **kopyalama** iletişim kutusundan kopyaladığınız URL olduğunda komutunu çalıştırın.
   
   Örneğin, **Ekipçtilities** deposunu yerel makinenizde *myTeam* dizinine kopyalamak için aşağıdaki komutlardan birini kullanın. 
   
   **HTTPS bağlantısı:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH bağlantısı:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```

Deponuzun yerel kopyasına istediğiniz değişiklikleri yaptıktan sonra, değişiklikleri kaydedin ve paylaşılan ekip depolarına gönderin. 

Yerel **GitRepos\MyTeam\TeamTemplate** veya **GitRepos\MyTeam\TeamUtilities** dizininizden aşağıdaki git Bash komutlarını çalıştırın.

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Git deposuna ilk kez çalışıyorsanız, `git commit` komutu çalıştırmadan önce *User.Name* ve *User. email* genel parametrelerini yapılandırmanız gerekebilir. Aşağıdaki iki komutu çalıştırın:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Birden çok git deposuna çalışıyorsanız, hepsi için aynı adı ve e-posta adresini kullanın. Aynı ad ve e-posta adresinin kullanılması, git etkinliklerinizi birden çok depoda izlemek üzere Power BI panolar oluştururken kullanışlıdır.

## <a name="add-team-members-and-configure-permissions"></a>Takım üyeleri ekleme ve izinleri yapılandırma

Takıma üye eklemek için:

1. Azure DevOps 'da, **myTeam** Project giriş sayfasından sol gezinden **proje ayarları** ' nı seçin. 
   
1. **Proje ayarları** sol gezinti listesinden **takımlar**' ı seçin, ardından **takımlar** sayfasında, **myTeam ekibini**seçin. 
   
   ![Takımları yapılandırma](./media/team-lead-tasks/teams.png)
   
1. **Takım profili** sayfasında **Ekle**' yi seçin.
   
   ![MyTeam ekibine Ekle](./media/team-lead-tasks/add-to-team.png)
   
1. **Kullanıcı ve Grup Ekle** iletişim kutusunda, gruba eklenecek üyeleri arayıp seçin ve ardından **Değişiklikleri Kaydet**' i seçin. 
   
   ![Kullanıcı ve grup ekle](./media/team-lead-tasks/add-users.png)
   

Takım üyeleri için izinleri yapılandırmak için:

1. **Proje ayarları** sol gezinti listesinden **izinler**' i seçin. 
   
1. **İzinler** sayfasında, üye eklemek istediğiniz grubu seçin. 
   
1. Bu grubun sayfasında, **Üyeler**' i seçin ve ardından **Ekle**' yi seçin. 
   
1. **Üyeleri davet et** açılan penceresinde, gruba eklenecek üyeleri arayıp seçin ve ardından **Kaydet**' i seçin. 
   
   ![Üyelere izin verme](./media/team-lead-tasks/grant-permissions.png)

## <a name="create-team-data-and-analytics-resources"></a>Ekip verileri ve analiz kaynakları oluşturma

Bu adım isteğe bağlıdır, ancak takımınızın tamamında veri ve analiz kaynaklarının paylaşılması performansı ve maliyet avantajlarına sahiptir. Ekip üyeleri, projelerini paylaşılan kaynaklarda yürütebilir, bütçelerde kaydedebilir ve daha verimli işbirliği yapabilir. Azure dosya depolaması oluşturabilir ve bunu, ekip üyeleriyle paylaşmak için DSVM 'nize bağlayabilirsiniz. 

Azure HDInsight Spark kümeleri gibi ekibinizle diğer kaynakları paylaşma hakkında bilgi için bkz. [platformlar ve araçlar](platforms-and-tools.md). Bu konu, gereksinimlerinize uygun kaynakları seçerken bir veri bilimi perspektifinden yönergeler ve ürün sayfaları ile diğer ilgili ve yararlı öğreticilerin bağlantılarını sağlar.

>[!NOTE]
> Veri merkezlerinde verileri, yavaş ve maliyetli olabilecek veri iletmemek için Azure Kaynak grubunuzun, depolama hesabınızın ve DSVM 'nizin tümünün aynı Azure bölgesinde barındırıldığından emin olun. 

### <a name="create-azure-file-storage"></a>Azure dosya depolaması oluşturma

1. Tüm ekibiniz için yararlı olan veri varlıkları için Azure dosya depolaması oluşturmak üzere aşağıdaki betiği çalıştırın. Betik sizden Azure abonelik bilgilerinizi ister. bu nedenle girmeye hazırlanın. 

   - Bir Windows makinesinde, PowerShell komut isteminden betiği çalıştırın:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
     .\CreateFileShare.ps1
     ```
     
   - Linux makinesinde, Linux kabuğundan betiği çalıştırın:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
     bash CreateFileShare.sh
     ```
   
1. İstendiğinde Microsoft Azure hesabınızda oturum açın ve kullanmak istediğiniz aboneliği seçin.
   
1. Kullanılacak depolama hesabını seçin veya seçtiğiniz abonelik altında yeni bir tane oluşturun. Azure dosya depolama adı için küçük harf karakterler, sayılar ve kısa çizgiler kullanabilirsiniz.
   
1. Depolamayı bağlamayı ve paylaşmayı kolaylaştırmak için, ENTER tuşuna basın veya *Y* girin. Azure dosya depolama bilgilerini geçerli dizininizdeki bir metin dosyasına kaydedin. Bu metin dosyasını, **Docs\DataDictionaries**altında ideal olarak, **teamtemplate** deponuza iade edebilir, böylece ekibinizdeki tüm projeler erişebilir. Ayrıca, sonraki bölümde Azure dosya depolama alanınızı Azure DSVM 'nize bağlamak için dosya bilgilerine de ihtiyacınız vardır. 
   
### <a name="mount-azure-file-storage-on-your-local-machine-or-dsvm"></a>Azure dosya depolama alanını yerel makinenize veya DSVM 'ye bağlama

1. Azure dosya depolama alanınızı yerel makinenize veya DSVM 'nize bağlamak için aşağıdaki betiği kullanın.
   
   - Bir Windows makinesinde, PowerShell komut isteminden betiği çalıştırın:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
     .\AttachFileShare.ps1
     ```
     
   - Linux makinesinde, Linux kabuğundan betiği çalıştırın:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
     bash AttachFileShare.sh
     ```
   
1. Önceki adımda bir Azure dosya depolama bilgileri dosyası kaydettiyseniz, devam etmek için ENTER tuşuna basın veya *Y* girin. Oluşturduğunuz dosyanın tüm yolunu ve adını girin. 
   
   Azure dosya depolama bilgi dosyanız yoksa, *n*girin ve aboneliğinizi, Azure depolama hesabınızı ve Azure dosya depolama bilgilerini girmek için yönergeleri izleyin.
   
1. Dosya paylaşımının bağlanması için bir yerel veya TDSP sürücüsünün adını girin. Ekran, mevcut sürücü adlarının listesini görüntüler. Zaten mevcut olmayan bir sürücü adı sağlayın.
   
1. Yeni sürücünün ve depolamanın makinenize başarıyla bağlandığından emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Team Data Science Işlemi tarafından tanımlanan diğer roller ve görevler hakkında ayrıntılı açıklamaların bağlantıları aşağıda verilmiştir:

- [Bir veri bilimi takım için Grup yöneticisi görevleri](group-manager-tasks.md)
- [Proje için bir veri bilimi ekibi müşteri adayı görevleri](project-lead-tasks.md)
- [Bir veri bilimi ekibi için projeye bireysel katkıda bulunan görevleri](project-ic-tasks.md)
