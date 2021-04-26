---
title: Azure Otomasyonu 'nda PowerShell Iş akışı runbook 'u oluşturma
description: Bu makalede basit bir PowerShell Iş akışı runbook 'u oluşturma, test etme ve yayımlama öğretilir.
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 45c7bb19e3f48fedf93fbb147a3014e09914a475
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831285"
---
# <a name="tutorial-create-a-powershell-workflow-runbook"></a>Öğretici: PowerShell Iş akışı runbook 'u oluşturma

Bu öğretici, Azure Automation’da bir [PowerShell İş Akışı runbook](../automation-runbook-types.md#powershell-workflow-runbooks) oluşturulmasını adım adım göstermektedir. PowerShell Iş akışı runbook 'ları Windows PowerShell Iş akışını temel alan metin runbook 'larıdır. Azure portal metin düzenleyicisini kullanarak runbook kodunu oluşturabilir ve düzenleyebilirsiniz. 

> [!div class="checklist"]
> * Basit PowerShell Iş akışı runbook 'u oluşturma
> * Runbook 'u test etme ve yayımlama
> * Runbook işinin durumunu çalıştırma ve izleme
> * Runbook parametreleri ile bir Azure sanal makinesini başlatmak için Runbook 'u güncelleştirme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:

* Azure aboneliği. Henüz bir hesabınız yoksa [MSDN abone avantajlarınızı etkinleştirebilir](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) veya [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)için kaydolabilirsiniz.
* Runbook’u tutacak ve Azure kaynaklarında kimlik doğrulamasını yapacak bir [Automation hesabı](../index.yml). Bu hesabın sanal makineyi başlatma ve durdurma izni olmalıdır.
* Azure sanal makinesi. Bu makineyi durdurup başlatabileceğinizden bu, bir üretim sanal makinesi olmamalıdır.

## <a name="step-1---create-new-runbook"></a>1. Adım - Yeni runbook oluşturma

Metni veren basit bir runbook oluşturarak başlayın `Hello World` .

1. Azure portalında, Otomasyon hesabınızı açın.

   Automation hesabı sayfası size bu hesaptaki kaynakların hızlı bir görünümünü sağlar. Birkaç varlığınız zaten olmalıdır. Bu varlıkların çoğu, otomatik olarak yeni bir Otomasyon hesabına eklenen modüllerdir. Ayrıca, aboneliğinizle ilişkili kimlik bilgisi varlığını de bilmelisiniz.
 
2. Runbook 'ların listesini açmak için **Işlem Otomasyonu** altında **runbook 'lar** ' ı seçin.

3. **Runbook oluştur**' a tıklayarak yeni bir runbook oluşturun.

4. Runbook’a **MyFirstRunbook-Workflow** adını verin.

5. Bu durumda, bir [PowerShell Iş akışı runbook 'u](../automation-runbook-types.md#powershell-workflow-runbooks)oluşturacağız. **Runbook türü** Için **PowerShell iş akışını** seçin.

6. Runbook’u oluşturmak için **Oluştur**’a tıklayın ve metin düzenleyicisini açın.

## <a name="step-2---add-code-to-the-runbook"></a>2. Adım - Runbook'a kod ekleme

Kodu doğrudan runbook 'a yazabilir ya da kitaplık denetiminden cmdlet 'leri, runbook 'ları ve varlıkları seçebilir ve ilgili parametrelerle runbook 'lara ekleyebilirsiniz. Bu öğreticide doğrudan runbook 'a kod yazın.

1. Runbook 'larınız Şu anda yalnızca gerekli `Workflow` anahtar sözcüğü, runbook 'un adı ve tüm iş akışını gösteren küme ayraçları ile boştur.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

2. `Write-Output "Hello World"`Küme ayraçları arasına yazın.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

3. **Kaydet**’e tıklayarak runbook’u kaydedin.

## <a name="step-3---test-the-runbook"></a>3. Adım - Runbook'u test etme

Runbook 'u üretimde kullanılabilir hale getirmek için yayımlamadan önce, düzgün çalıştığından emin olmak için test etmeniz gerekir. Bir runbook 'u test etmek taslak sürümünü çalıştırır ve çıktısını etkileşimli olarak görüntülemenizi sağlar.

1. Test bölmesini açmak için **Test bölmesi** ' ni seçin.

2. Sınamayı başlatmak için **Başlat** ' a tıklayın ve yalnızca etkin seçeneği test edin.

3. [Runbook işinin](../automation-runbook-execution.md) oluşturulduğunu ve durumunun bölmesinde görüntülendiğini unutmayın.

   İşin durumu kuyruğa alındı olarak başlar ve işin buluttaki bir runbook worker 'ın kullanılabilir hale gelmesini beklediğini belirtir. Bir çalışan işi talep ettiği zaman, durum başlayacak şekilde değişir. Son olarak, runbook aslında çalışmaya başladığında durum çalışıyor olur.

4. Runbook işi tamamlandığında, test bölmesi çıktısını görüntüler. Bu durumda, görürsünüz `Hello World` .

   ![Hello World](../media/automation-tutorial-runbook-textual/test-output-hello-world.png)

5. Tuvale geri dönmek için Test bölmesini kapatın.

## <a name="step-4---publish-and-start-the-runbook"></a>4. Adım - Runbook’u yayımlama ve başlatma

Oluşturduğunuz runbook hala taslak modunda. Üretim ortamında çalıştırmadan önce onu yayımlamanız gerekir. Bir runbook yayımladığınızda, Taslak sürümü mevcut Yayımlanmış sürümün üzerine yazarsınız. Örneğimizde, runbook’u henüz oluşturduğunuzdan, Yayımlanmış sürümünüz yok.

1. Runbook’u yayımlamak için **Yayımla**’ya tıklayın ve sorulduğunda **Evet**’e tıklayın.

2. Runbook 'u runbook **'lar** sayfasında görüntülemek için sola kaydırın ve **yazma durumu** alanının **yayımlandı** olarak ayarlandığını unutmayın.

3. **Myfirstrunbook-Workflow** için sayfayı görüntülemek üzere sağa doğru kaydırın.

   Üstteki seçenekler runbook 'u şimdi başlatabilmenizi, gelecekteki bir başlangıç zamanı zamanlamanıza veya Runbook 'un bir HTTP çağrısıyla başlatılabilmesi için [Web kancası](../automation-webhooks.md) oluşturmanıza imkan tanır.

4. **Başlat** ' ı ve ardından runbook 'u başlatmak isteyip Istemediğiniz sorulduğunda **Evet** ' i seçin.

   ![Runbook’u başlatma](../media/automation-tutorial-runbook-textual/automation-runbook-controls-start.png)

5. Oluşturulan runbook işi için bir Iş bölmesi açılır. Bu durumda, işin ilerlemesini izlemek için bölmeyi açık bırakın.

6. İş durumunun **Iş özetinde** gösterildiğini unutmayın. Bu durum, runbook 'u test ederken gördüğünüz durumlardan eşleşir.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-jobsummary.png" alt-text="Iş Özeti bölümünün vurgulandığını gösteren runbook Iş bölmesinin ekran görüntüsü.":::

7. Runbook durumu tamamlandı olarak gösterildikten sonra **Çıkış**' a tıklayın. İletinizi görebileceğiniz çıkış sayfası açılır `Hello World` .

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-outputtile.png" alt-text="Çıkış düğmesini vurgulanmış olan runbook Işi bölmesinin ekran görüntüsü.":::

8. Çıkış sayfasını kapatın.

9. Runbook işine ait Akışlar bölmesini açmak için **Tüm Günlükler**’e tıklayın. Yalnızca `Hello World` Çıkış akışında görmeniz gerekir. Akışlar bölmesinin, runbook 'a yazıyorsa ayrıntılı ve hata akışları gibi bir runbook işi için diğer akışları gösterebileceğini unutmayın.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-alllogstile.png" alt-text="Tüm Günlükler düğmesinin vurgulandığını gösteren runbook Iş bölmesinin ekran görüntüsü.":::

10. MyFirstRunbook sayfasına dönmek için akışlar bölmesini ve Iş bölmesini kapatın.

11. Bu runbook 'un Işler sayfasını açmak için **kaynaklar** altında **işler** ' e tıklayın. Bu sayfada runbook 'larınız tarafından oluşturulan tüm işler listelenir. İşi yalnızca bir kez çalıştırdığınız için yalnızca bir iş listelendiğini görmeniz gerekir.

   ![İşler](../media/automation-tutorial-runbook-textual/runbook-control-job-tile.png)

12. Runbook 'u başlattığınızda görüntülediğiniz aynı Iş bölmesini açmak için iş adına tıklayın. Runbook için oluşturulan herhangi bir işin ayrıntılarını görüntülemek için bu bölmeyi kullanın.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>5. Adım- Azure kaynaklarını yönetmek için kimlik doğrulaması ekleme

Runbook uygulamanızı test ettiniz ve yayımladınız, ancak şu ana kadar faydalı bir şey yapmadı. Bu runbook’un Azure kaynaklarını yönetmesini istiyorsunuz. Bu, aboneliğin kimlik bilgilerini kullanarak kimlik doğrulaması yapamadığı takdirde bunu yapamıyor. Kimlik doğrulaması [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'ini kullanır.

>[!NOTE]
>PowerShell runbook 'ları için `Add-AzAccount` ve `Add-AzureRMAccount` diğer adlardır `Connect-AzAccount` . Bu cmdlet 'leri kullanabilir veya Otomasyon hesabınızdaki [modüllerinizi](../automation-update-azure-modules.md) en son sürümlere güncelleştirebilirsiniz. Yeni bir Otomasyon hesabı oluşturmuş olsanız bile modüllerinizi güncelleştirmeniz gerekebilir.

1. MyFirstRunbook-Workflow sayfasına gidin ve **Düzenle**' ye tıklayarak metin düzenleyicisini açın.

2. Satırı silin `Write-Output` .

3. İmleci ayraçlar arasında boş bir satıra getirin.

4. Otomasyon farklı çalıştır hesabınızla kimlik doğrulamasını işleyen aşağıdaki kodu yazın veya kopyalayıp yapıştırın.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

5. Runbook 'u test edebilmeniz için **Test bölmesi** ' ne tıklayın.

6. Testi başlatmak için **Başlat**’a tıklayın. Tamamlandıktan sonra, hesabınızdaki temel bilgileri görüntüleyen aşağıdakine benzer bir çıktı görmeniz gerekir. Bu eylem, kimlik bilgisinin geçerli olduğunu onaylar.

   ![Kimlik doğrulaması](../media/automation-tutorial-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>6. Adım - Sanal makineyi başlatmak için kod ekleme

Runbook 'larınızın Azure aboneliğine kimlik doğrulaması yapdığına göre, kaynakları yönetebilirsiniz. Bir sanal makineyi başlatmak için bir komut ekleyelim. Azure aboneliğinizdeki herhangi bir VM 'yi seçebilirsiniz ve şu anda runbook 'ta bu adı kodlıyoruz. Kaynakları birden çok abonelik genelinde yönetiyorsanız, `AzContext` parametresini [Get-azcontext](/powershell/module/az.accounts/get-azcontext) cmdlet 'i ile kullanmanız gerekir.

1. Aşağıda gösterildiği gibi [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) cmdlet 'ine bir çağrı girerek başlatılacak sanal makinenin adını ve kaynak grubu adını belirtin. 

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures that you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Get-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

2. Runbook 'u kaydedin ve test **bölmesi** ' ne tıklayarak test edebilirsiniz.

3. Testi başlatmak için **Başlat**’a tıklayın. Tamamlandıktan sonra VM 'nin başlatılmış olduğunu denetleyin.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>7. Adım - Runbook'a girdi parametresi ekleme

Runbook 'larınız Şu anda runbook 'ta sabit kodlanmış sanal makineyi başlatır. Runbook başlatıldığında VM 'yi belirtebileceğiniz zaman daha yararlı olacaktır. Bu işlevi sağlamak için Runbook 'a giriş parametreleri ekleyelim.

1. Ve parametreleri için, `VMName` `ResourceGroupName` runbook 'a değişkenler ekleyin ve `Start-AzVM` aşağıdaki gibi cmdlet ile değişkenleri kullanın.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Runbook'u kaydedin ve Test bölmesini açın. Artık testteki iki giriş değişkeni için değerler sağlayabilirsiniz.

3. Test bölmesini kapatın.

4. Runbook’un yeni sürümünü yayımlamak için **Yayımla**’ya tıklayın.

5. Başlattığınız VM 'yi durdurun.

6. Runbook'u başlatmak için **Başlat**’a tıklayın. 

7. Başlayacaksanız kullanılacak VM için **VMName** ve **resourcegroupname** değerlerini yazın.

   ![Runbook’u Başlatma](../media/automation-tutorial-runbook-textual/automation-pass-params.png)

8. Runbook tamamlandığında, VM 'nin başlatıldığını doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

* Dil başvurusu ve öğrenme modülleri de dahil olmak üzere PowerShell hakkında daha fazla bilgi için [PowerShell belgelerine](/powershell/scripting/overview)bakın.
* PowerShell cmdlet başvurusu için bkz. [az. Automation](/powershell/module/az.automation).
* Grafik runbook 'ları kullanmaya başlamak için bkz. [grafik runbook 'U oluşturma](automation-tutorial-runbook-graphical.md).
* PowerShell runbook 'larını kullanmaya başlamak için bkz. [PowerShell runbook 'U oluşturma](automation-tutorial-runbook-textual-powershell.md).
* Runbook türleri ve bunların avantajları ve sınırlamaları hakkında daha fazla bilgi edinmek için bkz. [Azure Otomasyonu runbook türleri](../automation-runbook-types.md).
* PowerShell betiği destek özellikleri hakkında daha fazla bilgi için bkz. [Azure Otomasyonu 'Nda yerel PowerShell betiği desteği](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
