---
title: Team Data Science Işlem Grubu Yöneticisi görevleri
description: Bir grup yöneticisi 'nin bir veri bilimi takım projesinde tamamladığı görevlere ilişkin ayrıntılı izlenecek yolu izleyin.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/24/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a3d23950f5cbfaac00b03b25e3c19078c76ad0a5
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053318"
---
# <a name="team-data-science-process-group-manager-tasks"></a>Team Data Science Işlem Grubu Yöneticisi görevleri

Bu makalede bir veri bilimi organizasyonu için bir *grup yöneticisinin* tamamladığı görevler açıklanmaktadır. Grup Yöneticisi, bir kuruluştaki tüm veri bilimi birimini yönetir. Bir veri bilimi birimi, her biri ayrı iş verticinde birçok veri bilimi projesi üzerinde çalışan çeşitli takımlara sahip olabilir. Grup yöneticisinin hedefi, [ekip veri bilimi işlemini](overview.md) (TDSP) standartlaştırın bir işbirliğine dayalı grup ortamı kurmak. TDSP üzerinde standartlaştırarak bir veri bilimi ekibi tarafından işlenen tüm personel rollerinin ve ilişkili görevlerin bir özeti için bkz. [Team Data Science işlem rolleri ve görevleri](roles-tasks.md).

Aşağıdaki diyagramda altı ana grup yöneticisi kurulum görevi gösterilmektedir. Grup yöneticileri kendi görevlerini yedeklerin temsilcileriyle devredebilir, ancak rolle ilişkili görevler değişmez.

![Grup Yöneticisi görevleri](./media/group-manager-tasks/tdsp-group-manager.png)

1. Grup için bir **Azure DevOps organizasyonu** ayarlayın.
2. Azure DevOps kuruluşunda varsayılan **Groupcommon projesini** oluşturun.
3. Azure Repos ' de **Groupprojecttemplate** deposu oluşturun.
4. Azure Repos ' de **Grouputilities** deposu oluşturun.
5. Microsoft TDSP ekibinin **ProjectTemplate** ve **yardımcı program** depolarının içeriğini grup ortak depolarında içeri aktarın.
6. Gruba erişmek için takım üyelerinin **üyeliğini** ve **izinlerini** ayarlayın.

Aşağıdaki öğreticide, ayrıntılı adımlarda adım adım gösterilmektedir. 

> [!NOTE] 
> Bu makalede, Microsoft 'ta TDSP 'nin uygulanması nedeniyle bir TDSP grup ortamı ayarlamak için Azure DevOps kullanılır. Grubunuz diğer kod barındırma veya geliştirme platformlarını kullanıyorsa, Grup Yöneticisi 'nin görevleri aynıdır, ancak bunları tamamlamaya yönelik yol farklı olabilir.

## <a name="create-an-organization-and-project-in-azure-devops"></a>Azure DevOps 'da bir kuruluş ve proje oluşturma

1. [VisualStudio.Microsoft.com](https://visualstudio.microsoft.com)adresine gidin, sağ üst köşedeki **oturum aç** ' ı seçin ve Microsoft hesabı oturum açın. 
   
   ![Microsoft hesabı oturum açın](./media/group-manager-tasks/signinvs.png)
   
   Microsoft hesabı yoksa, **Şimdi kaydol**' u seçin, bir Microsoft hesabı oluşturun ve bu hesabı kullanarak oturum açın. Kuruluşunuzun bir Visual Studio aboneliği varsa, bu aboneliğin kimlik bilgileriyle oturum açın.
   
1. Oturum açtıktan sonra, Azure DevOps sayfasında sağ üst köşedeki **yeni kuruluş oluştur**' u seçin.
   
   ![Yeni kuruluş oluştur](./media/group-manager-tasks/create-organization.png)
   
1. Hizmet koşulları, gizlilik bildirimi ve kullanım kuralları 'nı kabul etmeniz istenirse **devam**' ı seçin.
   
1. Kaydolma iletişim kutusunda Azure DevOps kuruluşunuzu adlandırın ve konak bölgesi atamasını kabul edin veya aşağı açılır ve farklı bir bölge seçin. Daha sonra **Devam** seçeneğini belirleyin. 

1. Başlamak **için bir proje oluşturun**altında *groupcommon*' i girin ve ardından **proje oluştur**' u seçin. 
   
   ![Proje oluşturma](./media/group-manager-tasks/create-project.png)

**Groupcommon** Project **Özet** sayfası açılır. Sayfa URL 'SI *https:\//\<servername >/\<kuruluş-adı >/GroupCommon*.

![Proje özet sayfası](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>Grup ortak depoları ayarlama

Azure Repos grubunuz için aşağıdaki depo türlerini barındırır:

- **Ortak depoları gruplandırma**: genel amaçlı depolar, bir veri bilimi birimi içindeki birden çok ekibin birçok veri bilimi projesi için benimseyebileceği depolardır. 
- **Ekip depoları**: bir veri bilimi birimi içindeki belirli takımlar için depolar. Bu depolar bir ekibin ihtiyaçlarına özgüdür ve bu takım dahilinde birden fazla proje için kullanılabilir, ancak bir veri bilimi birimi içindeki birden çok takım genelinde kullanılmak üzere yeterince genel değildir.
- **Proje depoları**: belirli projeler için depolar. Bu depolar, bir takım içindeki birden fazla proje için veya bir veri bilimi birimindeki diğer takımlar için yeterince genel olmayabilir.

Projenizde grup ortak depoları ayarlamak için şunları yapın: 
- Varsayılan **Groupcommon** deposunu **groupprojecttemplate** olarak yeniden adlandırın
- Yeni bir **Grouputilities** deposu oluşturma

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>Varsayılan proje deposunu GroupProjectTemplate olarak yeniden adlandır

Varsayılan **Groupcommon** proje deposunu **groupprojecttemplate**olarak yeniden adlandırmak için:

1. **Groupcommon** proje **Özeti** sayfasında, **Depo**' yı seçin. Bu eylem, şu anda boş olan GroupCommon projesinin varsayılan **groupcommon** deposuna götürür.
   
1. Sayfanın üst kısmında, **Groupcommon** öğesinin yanındaki oku aşağı kaydırın ve **depoları Yönet**' i seçin.
   
   ![Depoları yönetme](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. **Proje ayarları** sayfasında, **groupcommon**' ın yanındaki **...** öğesini seçin ve ardından **Depoyu yeniden adlandır**' ı seçin. 
   
   ![Seç... sonra depoyu yeniden adlandır ' ı seçin.](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. **GroupCommon deposunu yeniden adlandır** açılan penceresinde *groupprojecttemplate*girin ve **Yeniden Adlandır**' ı seçin. 
   
   ![Depoyu yeniden adlandır](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>GroupUtilities deposunu oluşturma

**Grouputilities** deposunu oluşturmak için:

1. **Groupcommon** proje **Özeti** sayfasında, **Depo**' yı seçin. 
   
1. Sayfanın üst kısmında **Groupprojecttemplate** ' in yanındaki oku ve **yeni depo**' ı seçin.
   
   ![Yeni depo Seç](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. **Yeni depo oluştur** Iletişim kutusunda **tür**olarak **Git** ' i seçin, **Depo adı**olarak *grouputilities* ' i girin ve ardından **Oluştur**' u seçin.
   
   ![GroupUtilities deposu oluşturma](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. **Proje ayarları** sayfasında sol gezinmede yer alan depolar **' ı** seçerek Iki grup deposunu görüntüleyin: **groupprojecttemplate** ve **grouputilities**.
   
   ![İki grup deposu](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>Microsoft TDSP ekip depolarını içeri aktarma

Öğreticinin bu bölümünde, Microsoft TDSP ekibi tarafından yönetilen **ProjectTemplate** ve **yardımcı** program depoları içeriğini **Groupprojecttemplate** ve **grouputilities** Depolarınıza içeri aktarırsınız. 

TDSP ekip depolarını içeri aktarmak için:

1. **Grouportak** proje giriş sayfasından sol **Gezinti bölmesinde yer ' i** seçin. Varsayılan **Groupprojecttemplate** deposu açılır. 
   
1. **Groupprojecttemplate boş** sayfasında **içeri aktar**' ı seçin. 
   
   ![Içeri aktar 'ı seçin](./media/group-manager-tasks/import-repo.png)
   
1. **Git deposunu Içeri aktar** iletişim kutusunda, **kaynak türü**olarak **Git** ' i seçin ve **kopya URL 'si**için *https:\//GitHub.com/Azure/Azure-TDSP-ProjectTemplate.git* girin. Ardından **Içeri aktar**' ı seçin. Microsoft TDSP Team ProjectTemplate deposunun içeriği GroupProjectTemplate deponuza aktarılır. 
   
   ![Microsoft TDSP ekip deposunu içeri aktar](./media/group-manager-tasks/import-repo-2.png)
   
1. **Depo** sayfasının en üstünde aşağı açılır ve **grouputilities** deposunu seçin.
   
1. Microsoft TDSP Team **Utilities** deposunun ( *https:\//GitHub.com/Azure/Azure-TDSP-Utilities.git*) içeriğini **grouputilities** deponuza aktarmak için içeri aktarma işlemini tekrarlayın. 
   
Her iki grup deponuz artık, Microsoft TDSP ekibinin karşılık gelen deposundan *. git* diziniyle hariç tüm dosyaları içerir. 

## <a name="customize-the-contents-of-the-group-repositories"></a>Grup depolarının içeriğini özelleştirme

Grubunuzun belirli ihtiyaçlarını karşılamak üzere Grup depolarınızın içeriğini özelleştirmek istiyorsanız bunu şimdi yapabilirsiniz. Dosyaları değiştirebilir, dizin yapısını değiştirebilir veya grubunuzun geliştirdiği veya grubunuz için yararlı olan dosyaları ekleyebilirsiniz.

### <a name="make-changes-in-azure-repos"></a>Azure Repos değişiklik yap

Depo içeriğini özelleştirmek için:

1. **Groupcommon** proje **Özeti** sayfasında, **Depo**' yı seçin. 
   
1. Sayfanın üst kısmında, özelleştirmek istediğiniz depoyu seçin.

1. Depo dizin yapısında, değiştirmek istediğiniz klasöre veya dosyaya gidin. 
   
   - Yeni klasör veya dosya oluşturmak için **Yeni**' nin yanındaki oku seçin. 
     
     ![Yeni dosya oluştur](./media/group-manager-tasks/new-file.png)
     
   - Dosyaları karşıya yüklemek için, **karşıya dosya yükle**' yi seçin. 
     
     ![Dosyaları karşıya yükleme](./media/group-manager-tasks/upload-files.png)
     
   - Varolan dosyaları düzenlemek için dosyasına gidin ve ardından **Düzenle**' yi seçin. 
     
     ![Bir dosyayı düzenleme](./media/group-manager-tasks/edit-file.png)
     
1. Dosya ekledikten veya düzenledikten sonra, **Yürüt**' ü seçin.
   
   ![Değişiklikleri Kaydet](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>Yerel makinenizi veya DSVM 'nizi kullanarak değişiklik yapma

Yerel makinenizi veya DSVM 'yi kullanarak değişiklik yapmak ve değişiklikleri grup depolarına iletmek istiyorsanız git ve DSVMs ile çalışmaya yönelik önkoşullara sahip olduğunuzdan emin olun:

- DSVM oluşturmak istiyorsanız, bir Azure aboneliği.
- Git makinenizde yüklü. DSVM kullanıyorsanız git önceden yüklüdür. Aksi takdirde, [platformlar ve araçlar ek](platforms-and-tools.md#appendix)bölümüne bakın.
- DSVM 'yi, Azure 'da oluşturulan ve yapılandırılan Windows veya Linux DSVM 'yi kullanmak istiyorsanız. Daha fazla bilgi ve yönergeler için [veri bilimi sanal makinesi belgelerine](/azure/machine-learning/data-science-virtual-machine/)bakın.
- Bir Windows DSVM için, makinenizde yüklü [Git kimlik bilgileri Yöneticisi (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) . *README.MD* dosyasında, **indir ve yükle** bölümüne gidin ve **en son yükleyiciyi**seçin. Yükleyici sayfasından *. exe* yükleyicisini indirin ve çalıştırın. 
- Bir Linux DSVM için, DSVM 'niz üzerinde ayarlanan ve Azure DevOps 'a eklenen bir SSH ortak anahtarı. Daha fazla bilgi ve yönergeler için [platformlar ve araçlar EKINDE](platforms-and-tools.md#appendix) **SSH ortak anahtarı oluşturma** bölümüne bakın. 

İlk olarak, depoyu yerel makinenize *kopyalayın veya kopyalayın* . 
   
1. **Groupcommon** proje **Özeti** sayfasında, **Depo**' yı seçin ve sayfanın en üstünde, kopyalamak istediğiniz depoyu seçin.
   
1. Depo sayfasında sağ üstteki **kopya** ' ı seçin.
   
1. **Depoyu Kopyala** iletişim kutusunda, http bağlantısı için **https** ' yi veya bir SSH bağlantısı için **SSH** ' yi SEÇIN ve **komut satırı** altına kopya URL 'sini panonuza kopyalayın.
   
   ![Depoyu Kopyala](./media/group-manager-tasks/clone.png)
   
1. Yerel makinenizde aşağıdaki dizinleri oluşturun:
   
   - Windows için: **C:\GitRepos\GroupCommon**
   - Linux için, giriş dizininizde **$/GitRepos/GroupCommon** 
   
1. Oluşturduğunuz dizine geçin.
   
1. Git Bash 'de komutunu çalıştırın `git clone <clone URL>.`
   
   Örneğin, aşağıdaki komutlardan biri **Grouputilities** deposunu yerel makinenizde *groupcommon* dizinine kopyalar. 
   
   **HTTPS bağlantısı:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **SSH bağlantısı:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

Deponuzun yerel kopyasına istediğiniz değişiklikleri yaptıktan sonra, değişiklikleri paylaşılan grup ortak depolarında gönderebilirsiniz. 

Yerel **Groupprojecttemplate** veya **grouputilities** dizininden aşağıdaki git Bash komutlarını çalıştırın.

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Git deposuna ilk kez çalışıyorsanız, `git commit` komutunu çalıştırmadan önce *User.Name* ve *User. email* genel parametrelerini yapılandırmanız gerekebilir. Aşağıdaki iki komutu çalıştırın:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Birden çok git deposuna çalışıyorsanız, hepsi için aynı adı ve e-posta adresini kullanın. Aynı ad ve e-posta adresinin kullanılması, git etkinliklerinizi birden çok depoda izlemek üzere Power BI panolar oluştururken kullanışlıdır.

## <a name="add-group-members-and-configure-permissions"></a>Grup üyeleri ekleme ve izinleri yapılandırma

Gruba üye eklemek için:

1. Azure DevOps 'da, **Groupcommon** Project giriş sayfasından sol gezinti ' den **proje ayarları** ' nı seçin. 
   
1. **Proje ayarları** sol gezinti listesinden **takımlar**' ı seçin, ardından **takımlar** sayfasında, **groupcommon ekibini**seçin. 
   
   ![Takımları yapılandırma](./media/group-manager-tasks/teams.png)
   
1. **Takım profili** sayfasında **Ekle**' yi seçin.
   
   ![GroupCommon ekibine Ekle](./media/group-manager-tasks/add-to-team.png)
   
1. **Kullanıcı ve Grup Ekle** iletişim kutusunda, gruba eklenecek üyeleri arayıp seçin ve ardından **Değişiklikleri Kaydet**' i seçin. 
   
   ![Kullanıcı ve Grup Ekle](./media/group-manager-tasks/add-users.png)
   

Üyeler için izinleri yapılandırmak için:

1. **Proje ayarları** sol gezinti listesinden **izinler**' i seçin. 
   
1. **İzinler** sayfasında, üye eklemek istediğiniz grubu seçin. 
   
1. Bu grubun sayfasında, **Üyeler**' i seçin ve ardından **Ekle**' yi seçin. 
   
1. **Üyeleri davet et** açılan penceresinde, gruba eklenecek üyeleri arayıp seçin ve ardından **Kaydet**' i seçin. 
   
   ![Üyelere izin verme](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>Sonraki adımlar

Team Data Science Işlemindeki diğer roller ve görevler hakkında ayrıntılı açıklamaların bağlantıları aşağıda verilmiştir:

- [Bir veri bilimi ekibi için ekip sağlama görevleri](team-lead-tasks.md)
- [Veri bilimi ekibi için proje lideri görevleri](project-lead-tasks.md)
- [Bir veri bilimi ekibi için projeye bireysel katkıda bulunan görevleri](project-ic-tasks.md)
