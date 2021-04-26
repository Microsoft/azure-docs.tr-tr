---
title: Azure Otomasyonu 'nda kaynak denetimi tümleştirmesini kullanma-eski
description: Bu makalede, kaynak denetimi tümleştirmesini nasıl kullanacağınız açıklanır.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2019
ms.topic: conceptual
ms.openlocfilehash: 4dedbcf58e76b8c969f8607db6922e87a85f08e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97591882"
---
# <a name="use-source-control-integration-in-azure-automation---legacy"></a>Azure Otomasyonu 'nda kaynak denetimi tümleştirmesini kullanma-eski

> [!NOTE]
> Kaynak denetimi için yeni bir deneyim vardır. Yeni deneyim hakkında daha fazla bilgi edinmek için bkz. [kaynak denetimi (Önizleme)](source-control-integration.md).

Kaynak denetimi tümleştirmesi Otomasyon hesabınızdaki runbook 'ları GitHub Kaynak denetimi deposu ile ilişkilendirmenize olanak tanır. Kaynak denetimi, takımınızla kolayca işbirliği yapmanızı, değişiklikleri izlemenizi ve Runbook 'larınızın önceki sürümlerine geri dönmeyi sağlar. Örneğin, kaynak denetimi, kaynak denetimindeki farklı dalları geliştirme, test veya üretim Otomasyon hesaplarınızla eşitlemenize olanak tanır; böylece geliştirme ortamınızda test edilmiş kodu Üretim otomasyon hesabınıza yükseltebilirsiniz.

Kaynak denetimi, Azure Automation 'dan kaynak denetimine kod göndermenize veya Runbook 'larınızı kaynak denetiminden Azure Otomasyonu 'na çekbırakmanıza olanak tanır. Bu makalede, Azure Otomasyonu ortamınızda kaynak denetiminin nasıl ayarlanacağı açıklanır. Azure Otomasyonu 'Nu GitHub deponuza erişmek ve kaynak denetimi tümleştirmesi kullanılarak yapılabilecek farklı işlemlere yol gösterecek şekilde yapılandırarak başlayacağız. 

> [!NOTE]
> Kaynak denetimi PowerShell [Iş akışı runbook](automation-runbook-types.md#powershell-workflow-runbooks) 'larının yanı sıra [PowerShell runbook](automation-runbook-types.md#powershell-runbooks)'larının de çekmesini ve kullanılmasını destekler. [Grafik runbook 'ları](automation-runbook-types.md#graphical-runbooks) henüz desteklenmiyor.

## <a name="configure-source-control"></a>Kaynak denetimini yapılandırma

Otomasyon hesabınız için kaynak denetimi yapılandırmak için gereken iki basit adım vardır ve yalnızca bir GitHub hesabınız varsa. 

### <a name="create-a-github-repository"></a>GitHub deposu oluşturma

Azure Otomasyonu 'na bağlamak istediğiniz bir GitHub hesabınız ve bir deponuz varsa, mevcut hesabınızda oturum açın ve aşağıdaki adım 2 ' den başlayın. Aksi takdirde, [GitHub](https://github.com/)' a gidin, yeni bir hesap için kaydolun ve [Yeni bir depo oluşturun](https://help.github.com/articles/create-a-repo/).

### <a name="set-up-source-control"></a>Kaynak denetimini ayarlama

1. Azure portal Otomasyon hesabı sayfasından, **Hesap ayarları** altında, **kaynak denetimi** ' ne tıklayın.

2. GitHub hesabı ayrıntılarınızı yapılandırabileceğiniz kaynak denetimi sayfası açılır. Yapılandırılacak parametrelerin listesi aşağıdadır:  

   | **Parametre** | **Açıklama** |
   |:--- |:--- |
   | Kaynak seçin |Kaynağı seçin. Şu anda yalnızca **GitHub** desteklenir. |
   | Yetkilendirme |GitHub deponuza Azure Otomasyonu erişimi sağlamak için **Yetkilendir** düğmesine tıklayın. Zaten GitHub hesabınızda farklı bir pencerede oturum açtıysanız, bu hesabın kimlik bilgileri kullanılır. Yetkilendirme başarılı olduktan sonra sayfa, **yetkilendirme özelliği** altında GitHub Kullanıcı adınızı gösterir. |
   | Depo seçin |Kullanılabilir depolar listesinden bir GitHub deposu seçin. |
   | Dal seçin |Kullanılabilir dallar listesinden bir dal seçin. Yalnızca bir dal oluşturmadıysanız **ana** dal gösterilir. |
   | Runbook klasörü yolu |Runbook klasörü yolu, kodunuzu göndermek veya çekmek istediğiniz GitHub deposundaki yolu belirtir. **/FolderName/subklasöradı** biçiminde girilmesi gerekir. Yalnızca runbook klasörü yolundaki runbook 'lar Otomasyon hesabınızla eşitlenir. Runbook klasör yolunun alt klasörlerindeki runbook **'lar eşitlenmez.** **/** Depodaki tüm runbook 'ları eşitlemek için kullanın. |
3. Örneğin, **RootFolder** adlı ve **alt** klasörü adlı bir klasör içeren **powershellscripts** adlı bir havuzunuz varsa. Her klasör düzeyini eşitlemek için aşağıdaki dizeleri kullanabilirsiniz:

   1. Runbook 'ları **depodan** eşitlemek için Runbook klasörü yolu ' dur **/** .
   2. Runbook 'ları **RootFolder**'a eşitlemek için Runbook klasörü yolu **/RootFolder** olur.
   3. Runbook 'ları **alt** klasörden eşitlemek için, runbook klasörü yolu **/RootFolder/alt klasörüdür**.
4. Parametreleri yapılandırdıktan sonra, kaynak denetimi ayarlama sayfasında görüntülenir.  

    ![Ayarları gösteren kaynak denetimi sayfası](media/source-control-integration-legacy/automation-SourceControlConfigure.png)
5. **Tamam**' a tıkladığınızda, kaynak denetimi tümleştirmesi artık Otomasyon hesabınız için yapılandırılmıştır ve GitHub bilgileriniz ile güncelleştirilmeleri gerekir. Artık tüm kaynak denetimi eşitleme işi geçmişinizi görüntülemek için bu bölüme tıklayabilirsiniz.  

    ![Geçerli yapılandırılmış kaynak denetimi yapılandırması için değerler](media/source-control-integration-legacy/automation-RepoValues.png)
6. Kaynak denetimini ayarladıktan sonra, Otomasyon hesabınızda Iki [değişken varlık](./shared-resources/variables.md) oluşturulur. Ayrıca, bir yetkili uygulama GitHub hesabınıza eklenir.

   * **Microsoft. Azure. Automation. SourceControl. Connection** değişkeni, aşağıda gösterildiği gibi bağlantı dizesinin değerlerini içerir.  

     | **Parametre** | **Değer** |
     |:--- |:--- |
     | `Name`  |Microsoft. Azure. Automation. SourceControl. Connection |
     | `Type`  |Dize |
     | `Value` |{"Branch": \<*Your branch name*> , "runbookfolderpath": \<*Runbook folder path*> , "ProviderType": \<*has a value 1 for GitHub*> , "depo": \<*Name of your repository*> , "kullanıcıadı": \<*Your GitHub user name*> } |

   * **Microsoft. Azure. Automation. SourceControl. OAuthToken** değişkeni, OAuthToken 'inizin güvenli şifreli değerini içerir.  

     |**Parametre**            |**Değer** |
     |:---|:---|
     | `Name`  | `Microsoft.Azure.Automation.SourceControl.OAuthToken` |
     | `Type`  | `Unknown(Encrypted)` |
     | `Value` | <`Encrypted OAuthToken`> |  

     ![Kaynak denetim değişkenlerini gösteren bir pencere](media/source-control-integration-legacy/automation-Variables.png)  

   * **Otomasyon kaynak denetimi** , GitHub hesabınıza yetkili bir uygulama olarak eklenir. Uygulamayı görüntülemek için GitHub giriş sayfanızda **profil**  >  **ayarları**  >  **uygulamalar**' a gidin. Bu uygulama, Azure Otomasyonu 'nun GitHub deponuzu bir Otomasyon hesabıyla eşitlemesini sağlar.  

     ![GitHub 'daki uygulama ayarları](media/source-control-integration-legacy/automation-GitApplication.png)

## <a name="use-source-control-in-automation"></a>Otomasyon 'da kaynak denetimini kullanma

Runbook denetimi, Azure Otomasyonu 'ndaki bir runbook 'ta yaptığınız değişiklikleri kaynak denetimi deponuza göndermenize olanak tanır. Aşağıda runbook 'u iade etme adımları verilmiştir:

1. Otomasyon hesabınızdan [Yeni bir metinsel runbook oluşturun](./learn/automation-tutorial-runbook-textual.md)veya [var olan bir metin runbook 'unu düzenleyin](automation-edit-textual-runbook.md). Bu Runbook bir PowerShell Iş akışı ya da bir PowerShell betiği runbook 'u olabilir.  
2. Runbook 'unuzu düzenledikten sonra kaydedin ve düzenleme sayfasında **Iade et** ' e tıklayın.  

    ![GitHub düğmesine iade etmeyi gösteren pencere](media/source-control-integration-legacy/automation-CheckinButton.png)

     > [!NOTE] 
     > Azure Otomasyonu 'nda iade etme, kaynak denetiinizdeki mevcut olan kodun üzerine yazar. Git, git **Add + git COMMIT + git Push** için git eşdeğeri komut satırı yönergesi  

3. **İade et**' e tıkladığınızda, bir onay iletisiyle istenir ve devam etmek için **Evet** ' e tıklayın.  

    ![Kaynak denetimine iade etmeyi onaylayan bir iletişim kutusu](media/source-control-integration-legacy/automation-CheckinMessage.png)
4. İade etme kaynak denetim runbook 'unu başlatır: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Bu runbook GitHub 'a bağlanır ve değişiklikleri Azure Automation 'tan deponuza gönderir. İade edilen iş geçmişini görüntülemek için **kaynak denetimi tümleştirme** sekmesine dönün ve depo eşitleme sayfasını açmak için tıklayın. Bu sayfada tüm kaynak denetim işleriniz gösterilmektedir. Görüntülemek istediğiniz işi seçin ve ayrıntılarını görüntülemek için tıklayın.  

    ![Eşitleme işinin sonuçlarını gösteren pencere](media/source-control-integration-legacy/automation-CheckinRunbook.png)

   > [!NOTE]
   > Kaynak denetimi runbook 'ları, görüntüleyemezsiniz veya düzenleyemezsiniz. Runbook listenizde gösterilmediğinden, işler listenizde gösterilen eşitleme işleri görürsünüz.

5. Değiştirilen runbook 'un adı, iade edilen runbook için bir giriş parametresi olarak gönderilir. Depo eşitleme sayfasında runbook 'u genişleterek [iş ayrıntılarını görüntüleyebilirsiniz](automation-runbook-execution.md#job-statuses) .  

    ![Bir eşitleme işi için girişi gösteren pencere](media/source-control-integration-legacy/automation-CheckinInput.png)
6. Değişiklikleri görüntülemek için, iş tamamlandığında GitHub deponuzu yenileyin.  Bir COMMIT iletisi ile deponuzda bir kayıt olmalıdır: **Azure Otomasyonu 'Nda *Runbook adı* güncelleştirildi.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Runbook 'ları kaynak denetiminden Azure Otomasyonu 'na eşitleme

Depo eşitleme sayfasındaki Eşitle düğmesi, deponuzdaki runbook klasörü yolundaki tüm runbook 'ları Otomasyon hesabınıza çekmesini sağlar. Aynı depo birden fazla Otomasyon hesabıyla eşitlenebilir. Aşağıda runbook eşitleme adımları verilmiştir:

1. Kaynak denetimini ayarladığınız Otomasyon hesabından kaynak denetimi tümleştirmesi/depo eşitleme sayfasını açın ve **Eşitle**' ye tıklayın.  Bir onay iletisiyle karşılaşdıysanız, devam etmek için **Evet** ' e tıklayın.  

    ![Tüm runbook 'ların eşitleneceğini onaylayan ileti ile Eşitle düğmesi](media/source-control-integration-legacy/automation-SyncButtonwithMessage.png)

2. Eşitleme, GitHub 'a bağlanan ve değişiklikleri deponuzdan Azure Otomasyonu 'na çeken  **Sync-MicrosoftAzureAutomationAccountFromGitHubV1** runbook 'unu başlatır. Bu eylem için depo eşitleme sayfasında yeni bir iş görmeniz gerekir. Eşitleme işi hakkındaki ayrıntıları görüntülemek için, iş ayrıntıları sayfasını açmak üzere tıklayın.  

    ![GitHub deposunda eşitleme işinin Eşitleme sonuçlarını gösteren bir pencere](media/source-control-integration-legacy/automation-SyncRunbook.png)

    > [!NOTE]
    > Kaynak denetiminden eşitleme, şu anda kaynak denetimindeki **Tüm** runbook 'Lar için Automation hesabınızda mevcut olan runbook 'ların taslak sürümünün üzerine yazar. Git 'in eşitleme için git eşdeğeri komut satırı yönergesi **git pull**

![Askıya alınmış bir kaynak denetimi eşitleme işinden tüm günlükleri gösteren bir pencere](media/source-control-integration-legacy/automation-AllLogs.png)

## <a name="disconnect-source-control"></a>Kaynak denetimi bağlantısını kes

GitHub hesabınızla bağlantıyı kesmek için depo eşitleme sayfasını açın ve **bağlantıyı kes**' e tıklayın. Kaynak denetimini kestikten sonra, daha önce eşitlenmiş olan runbook 'lar Otomasyon hesabınızda kalır, ancak depo eşitleme sayfası etkinleştirilmeyecektir.  

  ![Kaynak denetiminin bağlantısını kesmek için bağlantıyı kes düğmesini gösteren bir pencere](media/source-control-integration-legacy/automation-Disconnect.png)

## <a name="next-steps"></a>Sonraki adımlar

* Azure Automation 'da kaynak denetimini tümleştirmek için bkz. Azure Otomasyonu [: Azure Otomasyonu 'Nda kaynak denetimi tümleştirmesi](https://azure.microsoft.com/blog/azure-automation-source-control-13/).  
* Visual Studio Online ile runbook kaynak denetimini tümleştirmek için bkz. [Azure Otomasyonu: Visual Studio Online kullanarak runbook kaynak denetimini tümleştirme](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/).  
