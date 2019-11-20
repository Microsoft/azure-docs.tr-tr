---
title: Azure Automation 'da ilk PowerShell Iş akışı runbook 'um
description: PowerShell İş Akışı kullanarak basit bir runbook oluşturma, test etme ve yayımlama adımlarını anlatan öğretici.
keywords: powershell iş akışı, powershell iş akışı örnekleri, iş akışı powershell
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 09/24/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4973a5215d93a1ce9d0a80f83264dd8c1fbe0888
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73887215"
---
# <a name="my-first-powershell-workflow-runbook"></a>İlk PowerShell İş Akışı runbook uygulamam

> [!div class="op_single_selector"]
> * [Grafik](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell İş Akışı](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Bu öğretici, Azure Automation’da bir [PowerShell İş Akışı runbook](automation-runbook-types.md#powershell-workflow-runbooks) oluşturulmasını adım adım göstermektedir. Runbook işinin durumunun nasıl izleneceğini açıklayarak test ve yayımladığınız basit bir runbook 'u kullanmaya başlayabilirsiniz. Ardından, bir Azure sanal makinesini başlatmayı içeren bir örnekle, bu runbook’u gerçekten Azure kaynaklarını yönetmek üzere değiştirin. Son olarak, runbook parametreleri ekleyerek runbook 'u daha sağlam hale getirebilirsiniz.

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:

* Azure aboneliği. Henüz bir aboneliğiniz yoksa [MSDN abone avantajlarınızı etkinleştirebilir](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ya da [ücretsiz hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) için kaydolabilirsiniz.
* Runbook’u tutacak ve Azure kaynaklarında kimlik doğrulamasını yapacak bir [Automation hesabı](automation-offering-get-started.md).  Bu hesabın sanal makineyi başlatma ve durdurma izni olmalıdır.
* Azure sanal makinesi. Bu makineyi durdurup başlattığınızda bir üretim sanal makinesi olmaması gerekir.

## <a name="step-1---create-new-runbook"></a>1\. Adım - Yeni runbook oluşturma

*Merhaba Dünya*metin çıkışı veren basit bir runbook oluşturarak başlayın.

1. Azure portalında, Otomasyon hesabınızı açın.

   Automation hesabı sayfası size bu hesaptaki kaynakların hızlı bir görünümünü sağlar. Birkaç varlığınız zaten olmalıdır. Bu varlıkların çoğu, yeni bir Otomasyon hesabına otomatik olarak dahil edilen modüllerdir. Burada ayrıca [önkoşullarda](#prerequisites) belirtilen Kimlik Bilgileri varlığınız da bulunmalıdır.

1. Runbook listesini açmak için **Işlem Otomasyonu** altında **runbook 'lar** ' a tıklayın.
1. **+ Runbook Ekle** düğmesine tıklayarak yeni bir runbook oluşturun ve ardından **Yeni bir runbook oluşturun**.
1. Runbook’a *MyFirstRunbook-Workflow* adını verin.
1. Bu durumda, bir [PowerShell Iş akışı runbook](automation-runbook-types.md#powershell-workflow-runbooks) 'u oluşturacaksınız, bu nedenle **runbook türü**için **PowerShell iş akışı** ' nı seçin.
1. Runbook’u oluşturmak için **Oluştur**’a tıklayın ve metin düzenleyicisini açın.

## <a name="step-2---add-code-to-the-runbook"></a>2\. Adım - Runbook'a kod ekleme

Kodu doğrudan runbook’a yazabilir veya Kitaplık denetiminde cmdlet’leri, runbook’ları ve varlıkları seçebilir ve ilgili parametrelerle bunların runbook’a eklenmesini sağlayabilirsiniz. Bu izlenecek yol için doğrudan runbook 'a yazın.

1. Runbook 'larınız Şu anda yalnızca gerekli *iş akışı* anahtar sözcüğü, runbook 'larınızın adı ve tüm iş akışını sağlayan küme ayraçları ile boştur.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Ayraçlar arasına *Write-Output "Hello World."* yazın.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. **Kaydet**’e tıklayarak runbook’u kaydedin.

## <a name="step-3---test-the-runbook"></a>3\. Adım - Runbook'u test etme

Runbook’u üretimde kullanılabilir hale getirmek üzere yayımlamadan önce düzgün çalıştığından emin olmak için test etmeniz gerekir. Bir runbook'u test ettiğinizde, bunun **Taslak** sürümünü çalıştırır ve çıktısını etkileşimli olarak görüntülersiniz.

1. Test bölmesini açmak için **Test bölmesi**’ne tıklayın.
1. Testi başlatmak için **Başlat**’a tıklayın. Bu seçenek etkinleştirilmiş tek seçenek olmalıdır.
1. Bir [runbook işi](automation-runbook-execution.md) oluşturulur ve durumu görüntülenir.

   İş durumu, bulutta bir runbook worker 'ın kullanılabilir hale gelmesini bekdiğini belirten *sıraya* alınır. Bir çalışan işi talep ettiği zaman *başlayacak* ve ardından runbook gerçekten çalışmaya başladığında *çalışır* .

1. Runbook işi tamamlandığında çıktısı görüntülenir. Bu durumda *Merhaba Dünya*görmeniz gerekir.

   ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Tuvale geri dönmek için Test bölmesini kapatın.

## <a name="step-4---publish-and-start-the-runbook"></a>4\. Adım - Runbook’u yayımlama ve başlatma

Oluşturduğunuz runbook hala Taslak modundadır. Üretim ortamında çalıştırmadan önce onu yayımlamanız gerekir. Bir runbook yayımladığınızda, Taslak sürümü mevcut Yayımlanmış sürümün üzerine yazarsınız. Bu durumda, henüz runbook 'u oluşturduğunuz için yayınlanmış bir sürümdür.

1. Runbook’u yayımlamak için **Yayımla**’ya tıklayın ve sorulduğunda **Evet**’e tıklayın.
1. Şimdi runbook 'u runbook **'lar** bölmesinde görüntülemek için sola kaydırırsanız, **yayımlanmış**bir **yazma durumu** gösterir.
1. **MyFirstRunbook-Workflow** için bölmeyi görüntülemek üzere geri sağa kaydırın.
   Üst kısımdaki seçenekler runbook’u başlatmamıza, gelecekte bir zamanda başlatmak üzere zamanlamamıza ya da bir HTTP çağrısıyla başlatılabilmesi için [web kancası](automation-webhooks.md) oluşturmamıza olanak tanır.
1. Runbook 'u başlatmak istediğiniz zaman **Başlat** ' a tıklayın ve istendiğinde **Evet** ' e tıklayın.

   ![Runbook’u başlatma](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Oluşturduğunuz runbook işi için bir iş bölmesi açılır. Bu bölmeyi kapatabilirsiniz, ancak bu durumda, işin ilerlemesini izlemek için açık bırakmalısınız.
1. İş durumu **Iş özetinde** gösterilir ve Runbook 'u test ettiğinizde gördüğünüz durumları eşleştirir.

   ![İş Özeti](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Runbook durumu olarak *Tamamlandı* gösterilince **Çıktı**’ya tıklayın. Çıkış bölmesi açılır ve *Merhaba Dünya*bakabilirsiniz.

   ![İş Özeti](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. Çıktı bölmesini kapatın.
1. Runbook işine ait Akışlar bölmesini açmak için **Tüm Günlükler**’e tıklayın. yalnızca çıkış akışında *Merhaba Dünya* görmeniz gerekir, ancak bu görünüm, runbook 'a yazıyorsa ayrıntılı ve hata gibi bir runbook işi için diğer akışları gösterebilir.

   ![İş Özeti](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. MyFirstRunbook sayfasına dönmek için akışlar sayfasını ve Iş sayfasını kapatın.
1. Bu runbook 'un Işler sayfasını açmak için **işler** ' e tıklayın. Bu sayfada bu runbook tarafından oluşturulan tüm işler listelenir. işi yalnızca bir kez çalıştırdığınız için yalnızca bir iş listelendiğini görmeniz gerekir.

   ![İşler](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. Runbook 'u başlattığınızda görüntülediğiniz aynı Iş sayfasını açmak için bu işe tıklayabilirsiniz. Bu eylem, zaman içinde geri dönmenize ve belirli bir runbook için oluşturulan herhangi bir işin ayrıntılarını görüntülemenize olanak sağlar.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>5\. Adım- Azure kaynaklarını yönetmek için kimlik doğrulaması ekleme

Runbook uygulamanızı test ettiniz ve yayımladınız, ancak şu ana kadar faydalı bir şey yapmadı. Bu runbook’un Azure kaynaklarını yönetmesini istiyorsunuz. [Önkoşullarda, önkoşullarda](#prerequisites)başvurulan kimlik bilgilerini kullanarak kimlik doğrulaması yapmadığınız takdirde bunu yapamazsınız. Bunu **Connect-AzAccount** cmdlet 'i ile yapabilirsiniz.

1. MyFirstRunbook İş Akışı bölmesinde **Düzenle**’ye tıklayarak metin düzenleyicisini açın.
2. Artık **yazma çıkış** satırına ihtiyacınız yoktur, bu nedenle devam edin ve silin.
3. İmleci ayraçlar arasında boş bir satıra getirin.
4. Automation Farklı Çalıştır hesabınızla kimlik doğrulamasını işleyecek aşağıdaki kodu yazın veya kopyalayıp yapıştırın:

   ```powershell-interactive
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

   > [!IMPORTANT]
   > **Add-azaccount** ve **login-Azaccount** artık **Connect-azaccount**için diğer adlardır. **Connect-azaccount** cmdlet 'i yoksa, **Add-Azaccount** veya **login-Azaccount**komutunu kullanabilir veya Otomasyon hesabınızdaki [modüllerinizi](automation-update-azure-modules.md) en son sürümlere güncelleştirebilirsiniz.

> [!NOTE]
> Yeni bir Otomasyon hesabı oluşturmuş olsanız bile [modüllerinizi güncelleştirmeniz](automation-update-azure-modules.md) gerekebilir.

1. Runbook 'u test edebilmeniz için **Test bölmesi** ' ne tıklayın.
1. Testi başlatmak için **Başlat**’a tıklayın. Tamamlandığında, hesabınızdaki temel bilgileri görüntüleyen bir aşağıdakine benzer bir çıktı almalısınız. Bu eylem, kimlik bilgisinin geçerli olduğunu onaylar.

   ![Kimlik doğrulaması](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>6\. Adım - Sanal makineyi başlatmak için kod ekleme

Runbook 'larınızın Azure aboneliğinizde kimlik doğrulaması yapdığına göre, kaynakları yönetebilirsiniz. bir sanal makineyi başlatmak için bir komut ekleyin. Azure aboneliğinizdeki herhangi bir sanal makineyi seçebilirsiniz ve şu anda runbook 'ta bu adı kodlıyoruz. Kaynakları birden çok abonelikle yönetiyorsanız, **-azcontext** parametresini [Get-azcontext](/powershell/module/az.accounts/get-azcontext)ile birlikte kullanmanız gerekir.

1. *Connect-AzAccount*öğesinden sonra, başlatılacak sanal makinenin adını ve kaynak grubu adını sağlayan *Start-Azvm-Name ' VMname '-Resourcegroupname ' nameofresourcegroup '* yazın.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

1. Runbook 'u kaydedin ve test **bölmesi** ' ne tıklayarak test edebilirsiniz.
1. Testi başlatmak için **Başlat**’a tıklayın. Tamamlandığında, sanal makinenin başlatıldığını kontrol edin.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>7\. Adım - Runbook'a girdi parametresi ekleme

Runbook 'larınız Şu anda runbook 'a kodlandığı sanal makineyi başlatır, ancak runbook başlatıldığında sanal makineyi belirtebiliyorsanız daha yararlı olur. Bu işlevi sağlamak için Runbook 'a girdi parametreleri eklersiniz.

1. Runbook 'a *VMName* ve *resourcegroupname* parametreleri ekleyin ve aşağıdaki örnekte olduğu gibi **Start-azvm** cmdlet 'i ile bu değişkenleri kullanın.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Runbook'u kaydedin ve Test bölmesini açın. Artık testteki iki giriş değişkeni için değerler sağlayabilirsiniz.
3. Test bölmesini kapatın.
4. Runbook’un yeni sürümünü yayımlamak için **Yayımla**’ya tıklayın.
5. Önceki adımda başlattığınız sanal makineyi durdurun.
6. Runbook'u başlatmak için **Başlat**’a tıklayın. Başlatacağınız sanal makinenin **VMName** ve **ResourceGroupName** bilgilerini yazın.

   ![Runbook’u Başlatma](media/automation-first-runbook-textual/automation-pass-params.png)

7. Runbook tamamlandığında, sanal makinenin başladığından emin olun.

## <a name="next-steps"></a>Sonraki adımlar

* Dil başvurusu ve öğrenme modülleri de dahil olmak üzere PowerShell hakkında daha fazla bilgi için [PowerShell belgelerine](https://docs.microsoft.com/powershell/scripting/overview)bakın.
* Grafik runbook'ları kullanmaya başlamak için bkz. [İlk grafik runbook uygulamam](automation-first-runbook-graphical.md)
* PowerShell runbook'ları kullanmaya başlamak için bkz. [İlk PowerShell runbook uygulamam](automation-first-runbook-textual-powershell.md)
* Runbook türleri, avantajları ve sınırlamaları hakkında daha fazla bilgi için, bkz. [Azure Automation runbook türleri](automation-runbook-types.md)
* PowerShell betik desteği özelliği hakkında daha fazla bilgi için bkz. [Azure Automation’da Yerel PowerShell betik desteği](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
