---
title: Azure Automation 'da ilk PowerShell runbook 'um
description: Basit bir PowerShell runbook oluşturma, test etme ve yayımlama adımlarını anlatan öğretici.
keywords: azure powershell, powershell betik öğreticisi, powershell otomasyonu
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 11/27/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ae9daaf797d3d82200ee094b63bad1f5c1ff68cc
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743810"
---
# <a name="my-first-powershell-runbook"></a>İlk PowerShell runbook’um

> [!div class="op_single_selector"]
> * [Grafik](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell İş Akışı](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Bu öğretici, Azure Automation’da bir [PowerShell runbook](automation-runbook-types.md#powershell-runbooks) oluşturulmasını adım adım göstermektedir. Runbook işinin durumunu izlemeyi öğrenirken test ve yayımladığınız basit bir runbook 'u kullanmaya başlayabilirsiniz. Ardından, bir Azure sanal makinesini başlatmayı içeren bir örnekle, bu runbook’u gerçekten Azure kaynaklarını yönetmek üzere değiştirin. Son olarak, runbook parametreleri ekleyerek runbook 'u daha sağlam hale getirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdaki önkoşulları karşılamanız gerekir:

* Azure aboneliği. Henüz bir aboneliğiniz yoksa [MSDN abone avantajlarınızı etkinleştirebilir](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ya da [ücretsiz hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) için kaydolabilirsiniz.
* Runbook’u tutacak ve Azure kaynaklarında kimlik doğrulamasını yapacak bir [Automation hesabı](automation-quickstart-create-account.md). Bu hesabın sanal makineyi başlatma ve durdurma izni olmalıdır.
* Azure sanal makinesi. Bu makineyi durdurup başlattığınızda bir üretim sanal makinesi olmaması gerekir.
* Kullandığınız cmdlet 'lere göre [Azure modüllerinizi güncelleştirmeniz](automation-update-azure-modules.md) gerekebilir.

## <a name="create-new-runbook"></a>Yeni runbook oluştur

*Merhaba Dünya*metin çıkışı veren basit bir runbook oluşturarak başlayın.

1. Azure portalında, Otomasyon hesabınızı açın.
2. Runbook listesini açmak için **Işlem Otomasyonu** altında **runbook 'lar** ' a tıklayın.
3. **+ Runbook Ekle** düğmesine tıklayarak yeni bir runbook oluşturun ve ardından **Yeni bir runbook oluşturun**.
4. Runbook’a *MyFirstRunbook-PowerShell* adını verin.
5. Bu durumda, bir [PowerShell runbook](automation-runbook-types.md#powershell-runbooks) 'u oluşturacaksınız, bu nedenle **runbook türü**için **PowerShell** ' i seçin.
6. Runbook’u oluşturmak için **Oluştur**’a tıklayın ve metin düzenleyicisini açın.

## <a name="add-code-to-the-runbook"></a>Runbook 'a kod ekleme

Kodu doğrudan runbook’a yazabilir veya Kitaplık denetiminde cmdlet’leri, runbook’ları ve varlıkları seçebilir ve ilgili parametrelerle bunların runbook’a eklenmesini sağlayabilirsiniz. Bu izlenecek yol için Runbook 'a doğrudan yazın.

1. Runbook 'larınız Şu anda boş, *Write-output "Merhaba Dünya" yazın.* yazın.

   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. **Kaydet**’e tıklayarak runbook’u kaydedin.

## <a name="step-3---test-the-runbook"></a> Runbook 'u test etme

Runbook’u üretimde kullanılabilir hale getirmek üzere yayımlamadan önce düzgün çalıştığından emin olmak için test etmeniz gerekir. Bir runbook'u test ettiğinizde, bunun **Taslak** sürümünü çalıştırır ve çıktısını etkileşimli olarak görüntülersiniz.

1. Test bölmesini açmak için **Test bölmesi**’ne tıklayın.
2. Testi başlatmak için **Başlat**’a tıklayın. Etkinleştirilen tek seçenek bu olmalıdır.
3. Bir [runbook işi](automation-runbook-execution.md) oluşturulur ve durumu görüntülenir.

   İş durumu, bulutta bir runbook worker 'ın kullanılabilir duruma gelmesi beklendiğini belirten *sıraya alınmış* olarak başlar. Bir çalışan işi talep ettiği zaman *başlayacak* ve ardından runbook gerçekten çalışmaya başladığında *çalışır* .

4. Runbook işi tamamlandığında çıktısı görüntülenir. Bu durumda *Merhaba Dünya*görmeniz gerekir.

   ![Test Bölmesi Çıktısı](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. Tuvale geri dönmek için Test bölmesini kapatın.

## <a name="publish-and-start-the-runbook"></a>Runbook 'u yayımlama ve başlatma

Oluşturduğunuz runbook hala Taslak modundadır. Üretim ortamında çalıştırılabilmesi için önce yayımlanmalıdır.  Bir runbook yayımladığınızda, Taslak sürümü mevcut Yayımlanmış sürümün üzerine yazarsınız. Bu durumda, henüz runbook 'u oluşturduğunuz için yayınlanmış bir sürümdür.

1. Runbook’u yayımlamak için **Yayımla**’ya tıklayın ve sorulduğunda **Evet**’e tıklayın.
1. Şimdi runbook 'u runbook **'lar** bölmesinde görüntülemek için sola kaydırırsanız, **yayımlanmış**bir **yazma durumu** gösterir.
1. **MyFirstRunbook-PowerShell**’e ait bölmeyi görüntülemek üzere geri sağa kaydırın.
   Üst kısımdaki seçenekler runbook’u başlatmamıza, görüntülememize, gelecek bir zamanda başlatılmak üzere zamanlamamıza ya da bir HTTP çağrısıyla başlatılabilmesi için [web kancası](automation-webhooks.md) oluşturmamıza olanak tanır.
1. Runbook 'u başlatmak istiyorsunuz, bu nedenle **Başlat** ' a tıklayın ve Runbook 'u Başlat sayfası açıldığında **Tamam** ' a tıklayın.
1. Oluşturduğunuz runbook işi için bir iş sayfası açılır. Bu bölmeyi kapatabilirsiniz, ancak bu durumda, işin ilerlemesini izlemek için açık bırakmalısınız.
1. İş durumu **Iş özetinde** gösterilir ve Runbook 'u test ettiğinizde gördüğünüz durumları eşleştirir.

   ![İş Özeti](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Runbook durumu *tamamlandı olarak tamamlandığında*, **genel bakış** ' ın altında **Çıkış**' a tıklayın. Çıkış bölmesi açılır ve *Merhaba Dünya*bakabilirsiniz.

   ![İş Çıkışı](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Çıkış sayfasını kapatın.
1. Runbook işine ait Akışlar bölmesini açmak için **Tüm Günlükler**’e tıklayın. Yalnızca çıkış akışında *Merhaba Dünya* görmeniz gerekir, ancak bu çıktı, runbook 'a yazıyorsa ayrıntılı ve hata gibi bir runbook işi için diğer akışları gösterebilir.

   ![Tüm Günlükler](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. MyFirstRunbook-PowerShell sayfasına dönmek için akışlar sayfasını ve Iş sayfasını kapatın.
1. **Ayrıntılar**altında, bu runbook 'un işler bölmesini açmak için **işler** ' e tıklayın. Bu sayfada bu runbook tarafından oluşturulan tüm işler listelenir. İşi yalnızca bir kez çalıştırdığınız için sadece bir işin listelendiğini görmeniz gerekir.

   ![İş Listesi](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. Runbook’u başlattığınızda, görüntülediğiniz iş bölmesini açmak için bu işe tıklayabilirsiniz. Bu eylem, zaman içinde geri dönmenize ve belirli bir runbook için oluşturulan herhangi bir işin ayrıntılarını görüntülemenize olanak sağlar.

## <a name="add-authentication-to-manage-azure-resources"></a>Azure kaynaklarını yönetmek için kimlik doğrulaması ekleme

Runbook uygulamanızı test ettiniz ve yayımladınız, ancak şu ana kadar faydalı bir şey yapmadı. Bu runbook’un Azure kaynaklarını yönetmesini istiyorsunuz. Otomasyon hesabınızı oluştururken otomatik olarak oluşturulan farklı çalıştır bağlantısını kullanarak kimlik doğrulaması yapamadığınız sürece bunu yapamazsınız. Farklı Çalıştır bağlantısını **Connect-AzureRmAccount** cmdlet 'i ile birlikte kullanırsınız. Kaynakları birden çok abonelik genelinde yönetiyorsanız, [Get-azurermcontext](/powershell/module/azurerm.profile/get-azurermcontext)ile birlikte **-azurermcontext** parametresini kullanmanız gerekir.

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzureRmAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationID $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $AzureContext
   ```

1. MyFirstRunbook-PowerShell sayfasında **Düzenle** ' ye tıklayarak metin düzenleyicisini açın.
1. Artık **yazma çıkış** satırına ihtiyacınız yoktur, bu nedenle devam edin ve silin.
1. Otomasyon Farklı Çalıştır hesabınızla kimlik doğrulamasını işleyen aşağıdaki kodu yazın veya kopyalayıp yapıştırın:

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzureRmAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationID $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

   > [!IMPORTANT]
   > **Add-azurermaccount** ve **login-azurermaccount** artık **Connect-azurermaccount**için diğer adlardır. **Connect-azurermaccount** cmdlet 'i yoksa, **Add-azurermaccount** veya **login-Azurermaccount**kullanabilirsiniz veya Otomasyon hesabınızdaki modüllerinizi en son sürümlere güncelleştirebilirsiniz.

1. Runbook 'u test edebilmeniz için **Test bölmesi** ' ne tıklayın.
1. Testi başlatmak için **Başlat**’a tıklayın. Tamamlandığında, hesabınızdaki temel bilgileri görüntüleyen bir aşağıdakine benzer bir çıktı almalısınız. Bu çıktı, farklı çalıştır hesabının geçerli olduğunu onaylar.

   ![Kimlik Doğrula](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="add-code-to-start-a-virtual-machine"></a>Sanal makineyi başlatmak için kod ekleme

Runbook 'larınızın Azure aboneliğinizde kimlik doğrulaması yapdığına göre, kaynakları yönetebilirsiniz. Bir sanal makineyi başlatmak için bir komut ekleyin. Azure aboneliğinizdeki herhangi bir sanal makineyi seçebilirsiniz ve şu anda runbook 'ta bu adı kodlarınız.

1. *Connect-AzureRmAccount*öğesinden sonra, başlatılacak sanal makinenin adını ve kaynak grubu adını sağlayan *Start-AzureRmVM-adı ' VMname '-Resourcegroupname ' nameofresourcegroup '* yazın.

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzureRmAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationID $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Runbook 'u kaydedin ve test **bölmesi** ' ne tıklayarak test edebilirsiniz.
1. Testi başlatmak için **Başlat**’a tıklayın. Tamamlandığında, sanal makinenin başlatıldığını kontrol edin.

## <a name="add-an-input-parameter"></a>Giriş parametresi ekleme

Runbook 'larınız Şu anda runbook 'a kodlandığı sanal makineyi başlatır, ancak runbook başlatıldığında sanal makineyi belirtirseniz daha yararlı olur. Bu işlevi sağlamak için Runbook 'a girdi parametreleri eklersiniz.

1. Runbook 'a *VMName* ve *resourcegroupname* parametreleri ekleyin ve aşağıdaki örnekte gösterildiği gibi **Start-azurermvm** cmdlet 'i ile bu değişkenleri kullanın.

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzureRmAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationID $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. Runbook'u kaydedin ve Test bölmesini açın. Şimdi testte kullanılan iki girdi değişkeni için değerleri sağlayabilirsiniz.
1. Test bölmesini kapatın.
1. Runbook’un yeni sürümünü yayımlamak için **Yayımla**’ya tıklayın.
1. Önceki adımda başlattığınız sanal makineyi durdurun.
1. Runbook 'u başlatmak için **Tamam** ' ı tıklatın. Başlatacağınız sanal makinenin **VMName** ve **ResourceGroupName** bilgilerini yazın.<br><br> ![Parametre Geçirme](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. Runbook tamamlandığında, sanal makinenin başladığından emin olun.

## <a name="differences-from-powershell-workflow"></a>PowerShell İş Akışı ile arasındaki farklar

PowerShell runbook'ları, PowerShell İş Akışı runbook'ları ile aynı yaşam döngüsü, yetenekler ve yönetim özelliklerine sahiptir, ancak arada bazı farklar ve sınırlamalar vardır:

1. PowerShell runbook'ları, bir derleme adımı içermediklerinden PowerShell İş Akışı runbook'larına göre daha hızlı çalışır.
2. PowerShell Iş akışı runbook 'ları destek denetim noktaları, PowerShell Iş akışı runbook 'ları runbook 'taki herhangi bir noktadan sürdürülemez. PowerShell runbook 'ları yalnızca baştan sürdürülecek.
3. PowerShell Iş akışı runbook 'ları paralel ve seri yürütmeyi destekler. PowerShell runbook 'ları komutları yalnızca hizmet temelli olarak yürütebilir.
4. PowerShell Iş akışı runbook 'unda, etkinlik, komut veya betik bloğu kendi çalışma alanına sahip olabilir. Bir PowerShell runbook 'unda, bir betikteki her şey tek bir çalışma alanında çalışır. Ayrıca yerel bir PowerShell runbook'u ile bir PowerShell İş Akışı runbook'u arasında bazı [söz dizimi farklılıkları](https://technet.microsoft.com/magazine/dn151046.aspx) vardır.

## <a name="next-steps"></a>Sonraki adımlar

* Dil başvurusu ve öğrenme modülleri de dahil olmak üzere PowerShell hakkında daha fazla bilgi için [PowerShell belgelerine](/powershell/scripting/overview)bakın.
* Grafik runbook'ları kullanmaya başlamak için bkz. [İlk grafik runbook uygulamam](automation-first-runbook-graphical.md)
* PowerShell iş akışı runbook'larını kullanmaya başlamak için bkz. [İlk PowerShell iş akışı runbook uygulamam](automation-first-runbook-textual.md)
* Runbook türleri, avantajları ve sınırlamaları hakkında daha fazla bilgi için bkz. [Azure Automation runbook türleri](automation-runbook-types.md)
* PowerShell betik desteği özelliği hakkında daha fazla bilgi için bkz. [Azure Automation’da Yerel PowerShell betik desteği](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
