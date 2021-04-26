---
title: Azure Otomasyonu 'nda PowerShell runbook 'u oluşturma
description: Bu makalede basit bir PowerShell runbook 'u oluşturma, test etme ve yayımlama öğretilir.
keywords: azure powershell, powershell betik öğreticisi, powershell otomasyonu
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 958cbf8b7b03e2dd9e4b0e03ba178ffe180bfb48
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831267"
---
# <a name="tutorial-create-a-powershell-runbook"></a>Öğretici: PowerShell runbook 'u oluşturma

Bu öğretici, Azure Automation’da bir [PowerShell runbook](../automation-runbook-types.md#powershell-runbooks) oluşturulmasını adım adım göstermektedir. PowerShell runbook 'ları Windows PowerShell 'i temel alır. Azure portal metin düzenleyicisini kullanarak runbook 'un kodunu doğrudan düzenleyebilirsiniz.

> [!div class="checklist"]
> * Basit bir PowerShell runbook 'u oluşturma
> * Runbook 'u test etme ve yayımlama
> * Runbook işinin durumunu çalıştırma ve izleme
> * Runbook parametreleri ile bir Azure sanal makinesini başlatmak için Runbook 'u güncelleştirme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:

* Azure aboneliği. Henüz bir hesabınız yoksa [MSDN abone avantajlarınızı etkinleştirebilir](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) veya [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)için kaydolabilirsiniz.
* Runbook’u tutacak ve Azure kaynaklarında kimlik doğrulamasını yapacak bir [Automation hesabı](../automation-quickstart-create-account.md). Bu hesabın sanal makineyi başlatma ve durdurma izni olmalıdır.
* Azure sanal makinesi. Bu makineyi durdurup başlatabileceğinizden bu, bir üretim sanal makinesi olmamalıdır.
* Gerekirse, [Azure modüllerini içeri aktarın](../shared-resources/modules.md) veya kullandığınız cmdlet 'lere göre [modülleri güncelleştirin](../automation-update-azure-modules.md) .

## <a name="differences-from-powershell-workflow-runbooks"></a>PowerShell Iş akışı runbook 'larının farkları

PowerShell runbook 'ları, PowerShell Iş akışı runbook 'ları ile aynı yaşam döngüsü, yetenekler ve yönetime sahiptir. Ancak bazı farklar ve sınırlamalar vardır.

| Özellik  | PowerShell runbook 'Ları | PowerShell Iş akışı runbook 'Ları |
| ------ | ----- | ----- |
| Hız | Derleme adımı kullanmayan için hızlı bir şekilde çalıştırın. | Daha yavaş çalıştırın. |
| Kontrol noktaları | Kontrol noktalarını desteklemez. PowerShell runbook 'u yalnızca baştan başlayarak işlemi sürdürür. | Çalışma kitabının her noktadan işlemi sürdürmesini sağlayan denetim noktaları kullanın. |
| Komut yürütme | Yalnızca seri yürütmeyi destekler. | Hem seri hem de paralel yürütmeyi destekler.|
| Çalışma | Tek bir çalışma, bir betikteki her şeyi çalıştırır. | Bir etkinlik, komut veya betik bloğu için ayrı bir çalışma alanı kullanılabilir. |

Bu farklılıklara ek olarak PowerShell runbook 'ları, PowerShell Iş akışı runbook 'larında bazı [sözdizimsel farklılıklara](/previous-versions/technet-magazine/dn151046(v=msdn.10)) sahiptir.

## <a name="step-1---create-runbook"></a>1. Adım - Runbook oluşturma

Metni veren basit bir runbook oluşturarak başlayın `Hello World` .

1. Azure portalında, Otomasyon hesabınızı açın.

2. Runbook 'ların listesini açmak için **Işlem Otomasyonu** altında **runbook 'lar** ' ı seçin.

3. **Runbook oluştur**' a tıklayarak yeni bir runbook oluşturun.

4. Runbook’a **MyFirstRunbook-PowerShell** adını verin.

5. Bu durumda, bir [PowerShell runbook 'u](../automation-runbook-types.md#powershell-runbooks)oluşturacağız. **Runbook türü** için **PowerShell** ' i seçin.

6. Runbook’u oluşturmak için **Oluştur**’a tıklayın ve metin düzenleyicisini açın.

## <a name="step-2---add-code-to-the-runbook"></a>2. Adım - Runbook'a kod ekleme

Kodu doğrudan runbook’a yazabilir veya Kitaplık denetiminde cmdlet’leri, runbook’ları ve varlıkları seçebilir ve ilgili parametrelerle bunların runbook’a eklenmesini sağlayabilirsiniz. Bu öğreticide doğrudan runbook 'a kod yazacağım.

1. Runbook 'larınız Şu anda boş. `Write-Output "Hello World"`Betiğin gövdesini yazın.

   ![Hello World](../media/automation-tutorial-runbook-textual-powershell/automation-helloworld.png)

2. **Kaydet**’e tıklayarak runbook’u kaydedin.

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"></a> 3. adım-runbook 'u test etme

Runbook 'u üretimde kullanılabilir hale getirmek için yayımlamadan önce, düzgün çalıştığından emin olmak için test etmeniz gerekir. Bir runbook 'u test etmek taslak sürümünü çalıştırır ve çıktısını etkileşimli olarak görüntülemenizi sağlar.

1. Test bölmesini açmak için **Test bölmesi**’ne tıklayın.

2. Testi başlatmak için **Başlat**’a tıklayın. Etkinleştirilen tek seçenek bu olmalıdır.

3. [Runbook işinin](../automation-runbook-execution.md) oluşturulduğunu ve durumunun bölmesinde görüntülendiğini unutmayın.

   İşin durumu kuyruğa alındı olarak başlar ve işin buluttaki bir runbook worker 'ın kullanılabilir hale gelmesini beklediğini belirtir. Bir çalışan işi talep ettiği zaman, durum başlayacak şekilde değişir. Son olarak, runbook aslında çalışmaya başladığında durum çalışıyor olur.

4. Runbook işi tamamlandığında, test bölmesi çıktısını görüntüler. Bu durumda, görürsünüz `Hello World` .

   ![Test Bölmesi Çıktısı](../media/automation-tutorial-runbook-textual-powershell/automation-testpane-output.png)

5. Tuvale geri dönmek için Test bölmesini kapatın.

## <a name="step-4---publish-and-start-the-runbook"></a>4. Adım - Runbook’u yayımlama ve başlatma

Oluşturduğunuz runbook hala taslak modunda. Üretimde çalıştırılabilmesi için yayımlanması gerekir. Bir runbook yayımladığınızda, Taslak sürümü mevcut Yayımlanmış sürümün üzerine yazarsınız. Örneğimizde, runbook’u henüz oluşturduğunuzdan, Yayımlanmış sürümünüz yok.

1. Runbook’u yayımlamak için **Yayımla**’ya tıklayın ve sorulduğunda **Evet**’e tıklayın.

2. Runbook 'u runbook 'Lar sayfasında görüntülemek için sola kaydırın ve **yazma durum** değerinin **yayımlandı** olarak ayarlandığını unutmayın.

3. **Myfirstrunbook-PowerShell** sayfasını görüntülemek için sağa doğru kaydırın.
   
   Üstteki seçenekler runbook 'u şimdi başlatabilmenizi, gelecekteki bir başlangıç zamanı zamanlamanıza veya Runbook 'un bir HTTP çağrısıyla başlatılabilmesi için [Web kancası](../automation-webhooks.md) oluşturmanıza imkan tanır.

4. **Başlat** ' ı ve ardından runbook 'u başlatmak isteyip Istemediğiniz sorulduğunda **Evet** ' i seçin. 

5. Oluşturulan runbook işi için bir Iş bölmesi açılır. Bu bölmeyi kapatabilir olsanız da işin ilerlemesini izleyebilmeniz için hemen açık bırakın. İş durumu **Iş özetinde** gösterilir ve olası durumlar runbook 'u test etmek için açıklanmıştır.

   ![İş Özeti](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

6. Runbook durumunun Tamamlandı olarak gösterilmesi için **Çıkış** ' a tıklayarak, gösterilen çıkış sayfasını açın `Hello World` .

   ![İş Çıktısı](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

7. Çıkış sayfasını kapatın.

8. Runbook işine ait Akışlar bölmesini açmak için **Tüm Günlükler**’e tıklayın. Yalnızca `Hello World` Çıkış akışında görmeniz gerekir.

    Akışlar bölmesinin, runbook 'a yazıyorsa ayrıntılı ve hata akışları gibi bir runbook işi için diğer akışları gösterebileceğini unutmayın.

   ![Tüm Günlükler](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

9. MyFirstRunbook-PowerShell sayfasına dönmek için akışlar bölmesini ve Iş bölmesini kapatın.

10. **Ayrıntılar** altında, bu runbook 'un işler sayfasını açmak için **işler** ' e tıklayın. Bu sayfada runbook 'larınız tarafından oluşturulan tüm işler listelenir. İşi yalnızca bir kez çalıştırdığınız için, yalnızca bir iş listelendiğini görmeniz gerekir.

   ![İş Listesi](../media/automation-tutorial-runbook-textual-powershell/runbook-control-job-tile.png)

11. Runbook 'u başlattığınızda görüntülediğiniz aynı Iş bölmesini açmak için iş adına tıklayın. Runbook için oluşturulan herhangi bir işin ayrıntılarını görüntülemek için bu bölmeyi kullanın.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>5. Adım- Azure kaynaklarını yönetmek için kimlik doğrulaması ekleme

Runbook uygulamanızı test ettiniz ve yayımladınız, ancak şu ana kadar faydalı bir şey yapmadı. Bu runbook’un Azure kaynaklarını yönetmesini istiyorsunuz. Bunu yapmak için, runbook 'un Otomasyon hesabınızı oluştururken otomatik olarak oluşturulan farklı çalıştır hesabını kullanarak kimlik doğrulaması yapabilmesi gerekir.

Aşağıdaki örnekte gösterildiği gibi, farklı çalıştır bağlantısı [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'i ile yapılır. Kaynakları birden çok abonelik genelinde yönetiyorsanız, `AzContext` parametresini [Get-azcontext](/powershell/module/Az.Accounts/Get-AzContext)ile kullanmanız gerekir.

> [!NOTE]
> PowerShell runbook 'ları için `Add-AzAccount` ve `Add-AzureRMAccount` diğer adlardır `Connect-AzAccount` . Bu cmdlet 'leri kullanabilir veya Otomasyon hesabınızdaki [modüllerinizi](../automation-update-azure-modules.md) en son sürümlere güncelleştirebilirsiniz. Yeni bir Otomasyon hesabı oluşturmuş olsanız bile modüllerinizi güncelleştirmeniz gerekebilir.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $AzureContext
   ```

1. MyFirstRunbook-PowerShell sayfasında **Düzenle** ' ye tıklayarak metin düzenleyicisini açın.

2. Daha fazla satıra ihtiyacınız yoktur `Write-Output` . Hemen devam edip silmeniz yeterlidir.

3. Otomasyon farklı çalıştır hesabınızla kimlik doğrulamasını işleyen aşağıdaki kodu yazın veya kopyalayıp yapıştırın.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

4. Runbook 'u test edebilmeniz için **Test bölmesi** ' ne tıklayın.

5. Testi başlatmak için **Başlat**’a tıklayın. Tamamlandıktan sonra, hesabınızdaki temel bilgileri görüntüleyen aşağıdakine benzer bir çıktı görmeniz gerekir. Bu çıktı, farklı çalıştır hesabının geçerli olduğunu onaylar.

   ![Kimlik doğrulaması](../media/automation-tutorial-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>6. Adım - Sanal makineyi başlatmak için kod ekleme

Runbook 'larınızın Azure aboneliğinizde kimlik doğrulaması yapdığına göre, kaynakları yönetebilirsiniz. Bir sanal makineyi başlatmak için bir komut ekleyelim. Azure aboneliğinizdeki herhangi bir sanal makineyi seçebilir ve şu anda runbook 'ta bu adı sabit olarak kodlayın.

1. Runbook betiğe, sanal makineyi başlatmak için [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) cmdlet 'ini ekleyin. Aşağıda gösterildiği gibi cmdlet, adlı bir sanal makineyi `VMName` ve adlı bir kaynak grubu ile başlatır `ResourceGroupName` .

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

2. Runbook 'u kaydedin ve test **bölmesi** ' ne tıklayarak test edebilirsiniz.

3. Testi başlatmak için **Başlat** ' a tıklayın. İşlem tamamlandıktan sonra, sanal makinenin başlatıldığından emin olun.

## <a name="step-7---add-an-input-parameter"></a>7. adım-giriş parametresi ekleme

Runbook 'larınız Şu anda runbook 'ta sabit kodlanmış sanal makineyi başlatır. Runbook başlatıldığında sanal makineyi belirtirseniz runbook daha yararlıdır. Bu işlevi sağlamak için Runbook 'a giriş parametreleri ekleyelim.

1. Metinsel düzenleyicide `Start-AzVM` cmdlet 'ini parametreler ve parametreleri için değişkenler kullanacak şekilde değiştirin `VMName` `ResourceGroupName` . 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

2. Runbook'u kaydedin ve Test bölmesini açın. Şimdi testte kullanılacak olan iki girdi değişkeni için değerleri sağlayabilirsiniz.

3. Test bölmesini kapatın.

4. Runbook’un yeni sürümünü yayımlamak için **Yayımla**’ya tıklayın.

5. Daha önce başlattığınız sanal makineyi durdurun.

6. Runbook'u başlatmak için **Başlat**’a tıklayın. 

7. Başlayacağız sanal makineye yönelik **VMName** ve **resourcegroupname** değerlerini yazın ve ardından **Tamam**' a tıklayın.

    ![Parametre Geçir](../media/automation-tutorial-runbook-textual-powershell/automation-pass-params.png)

8. Runbook tamamlandığında, sanal makinenin başlatıldığından emin olun.

## <a name="next-steps"></a>Sonraki adımlar

* Dil başvurusu ve öğrenme modülleri dahil olmak üzere PowerShell hakkında daha fazla bilgi için bkz. [PowerShell belgeleri](/powershell/scripting/overview).
* PowerShell cmdlet başvurusu için bkz. [az. Automation](/powershell/module/az.automation).
* Grafik runbook 'ları kullanmaya başlamak için bkz. [grafik runbook 'U oluşturma](automation-tutorial-runbook-graphical.md).
* PowerShell Iş akışı runbook 'larını kullanmaya başlamak için bkz. [PowerShell Iş akışı runbook 'U oluşturma](automation-tutorial-runbook-textual.md).
* Runbook türleri ve bunların avantajları ve sınırlamaları hakkında daha fazla bilgi edinmek için bkz. [Azure Otomasyonu runbook türleri](../automation-runbook-types.md).
* PowerShell betiği desteği özelliği hakkında daha fazla bilgi için bkz. [Azure Otomasyonu 'Nda yerel PowerShell betiği desteği](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
