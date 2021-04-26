---
title: Azure Otomasyonu 'nda grafik runbook 'u oluşturma
description: Bu makalede, Azure Otomasyonu 'nda basit bir grafik runbook 'u oluşturma, test etme ve yayımlama öğretilir.
keywords: runbook, runbook şablonu, runbook otomasyonu, azure runbook
services: automation
ms.subservice: process-automation
ms.date: 09/15/2020
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 668f661d2c8f349f4dac3f850800d83890d2f4d9
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831447"
---
# <a name="tutorial-create-a-graphical-runbook"></a>Öğretici: grafik runbook 'u oluşturma

Bu öğretici, Azure Automation’da bir [grafik runbook uygulaması](../automation-runbook-types.md#graphical-runbooks) oluşturulmasını adım adım göstermektedir. Azure portal grafik düzenleyicisini kullanarak grafik ve grafik PowerShell Iş akışı runbook 'ları oluşturup düzenleyebilirsiniz. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Basit bir grafik runbook 'u oluşturma
> * Runbook 'u test etme ve yayımlama
> * Runbook işinin durumunu çalıştırma ve izleme
> * Runbook parametreleri ve koşullu bağlantılar içeren bir Azure sanal makinesini başlatmak için Runbook 'u güncelleştirme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:

* Azure aboneliği. Henüz bir hesabınız yoksa [MSDN abone avantajlarınızı etkinleştirebilir](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) veya [ücretsiz bir hesap](https://azure.microsoft.com/free)için kaydolabilirsiniz.
* Runbook’u tutacak ve Azure kaynaklarında kimlik doğrulamasını yapacak bir [Automation hesabı](../index.yml). Bu hesabın sanal makineyi başlatma ve durdurma izni olmalıdır.
* Azure sanal makinesi. Bu makineyi durdurup başlatabileceğinizden bu, bir üretim sanal makinesi olmamalıdır.
* Gerekirse, [Azure modüllerini içeri aktarın](../shared-resources/modules.md) veya kullandığınız cmdlet 'lere göre [modülleri güncelleştirin](../automation-update-azure-modules.md) .

## <a name="step-1---create-runbook"></a>1. Adım - Runbook oluşturma

Metni veren basit bir runbook oluşturarak başlayın `Hello World` .

1. Azure portalında, Otomasyon hesabınızı açın.

    Automation hesabı sayfası size bu hesaptaki kaynakların hızlı bir görünümünü sağlar. Birkaç varlığınız zaten olmalıdır. Bu varlıkların çoğu, otomatik olarak yeni bir Otomasyon hesabına eklenen modüllerdir. Ayrıca, aboneliğinizle ilişkili kimlik bilgisi varlığını de bilmelisiniz.

2. Runbook 'ların listesini açmak için **Işlem Otomasyonu** altında **runbook 'lar** ' ı seçin.

3. **Runbook oluştur**' a tıklayarak yeni bir runbook oluşturun.

4. Runbook’a **MyFirstRunbook-Graphical** adını verin.

5. Bu durumda, bir [grafik runbook](../automation-graphical-authoring-intro.md)oluşturacağız. **Runbook türü** için **grafik** ' i seçin.

    ![Yeni runbook](../media/automation-tutorial-runbook-graphical/create-new-runbook.png)

6. Runbook’u oluşturmak için **Oluştur**’a tıklayın ve grafik düzenleyicisini açın.

## <a name="step-2---add-activities"></a>2. adım-etkinlikleri ekleme

Düzenleyicinin sol tarafındaki Kitaplık denetimi runbook uygulamanıza eklenecek etkinlikleri seçmenizi sağlar. `Write-Output`Runbook 'tan çıkış metnine bir cmdlet ekleyeceksiniz.

1. Kitaplık denetiminde, arama alanına tıklayın ve yazın `write-output` . Arama sonuçları aşağıdaki görüntüde gösterilmiştir.

    ![Microsoft.PowerShell.Utility](../media/automation-tutorial-runbook-graphical/search-powershell-cmdlet-writeoutput.png)

2. Listenin aşağısına kaydırın. **Write-output** ' a sağ tıklayın ve **tuvale Ekle**' yi seçin. Alternatif olarak, cmdlet adının yanındaki üç nokta (...) simgesine ve ardından **tuvale Ekle**' yi seçebilirsiniz.

3. Tuvalde **Write-Output** etkinliğine tıklayın. Bu eylem, etkinliği yapılandırmanızı sağlayan yapılandırma denetimi sayfasını açar.

4. **Etiket** alanı varsayılan olarak cmdlet adıdır, ancak bunu daha kolay bir şekilde değiştirebilirsiniz. Bunu `Write Hello World to output` olarak değiştirin.

5. Cmdlet parametreleri değerlerini sağlamak için **Parametreler**’e tıklayın. 

   Bazı cmdlet 'ler birden fazla parametre kümesine sahiptir ve hangisinin kullanılacağını seçmeniz gerekir. Bu durumda, `Write-Output` yalnızca bir parametre kümesi vardır.

6. Parametresini seçin `InputObject` . Bu, çıkış akışına göndermek üzere metin belirtmek için kullandığınız parametredir.

7. **Veri kaynağı** açılır menüsü bir parametre değerini doldurmak için kullanabileceğiniz kaynaklar sağlar. Bu menüde **PowerShell ifadesi**' ni seçin.

   Bu tür kaynaklardan gelen çıktıyı başka bir etkinlik, Otomasyon varlığı veya PowerShell ifadesi olarak kullanabilirsiniz. Bu durumda çıktı yalnızca `Hello World` . Bir PowerShell ifadesi kullanabilir ve dize belirtebilirsiniz.

8. **İfade** alanına, yazın `"Hello World"` ve ardından tuvale dönmek Için iki kez **Tamam** ' a tıklayın.

9. **Kaydet**’e tıklayarak runbook’u kaydedin.

## <a name="step-3---test-the-runbook"></a>3. Adım - Runbook'u test etme

Runbook 'u üretimde kullanılabilir hale getirmek için yayımlamadan önce, düzgün çalıştığından emin olmak için test etmeniz gerekir. Bir runbook 'u test etmek taslak sürümünü çalıştırır ve çıktısını etkileşimli olarak görüntülemenizi sağlar.

1. Test bölmesini açmak için **Test bölmesi** ' ni seçin.

2. Testi başlatmak için **Başlat**’a tıklayın. Etkinleştirilen tek seçenek bu olmalıdır.

3. [Runbook işinin](../automation-runbook-execution.md) oluşturulduğunu ve durumunun bölmesinde görüntülendiğini unutmayın.

   İşin durumu `Queued` , işin bulutta bir runbook worker 'ın kullanılabilir hale gelmesini beklediğini belirten olarak başlar. `Starting`Bir çalışan işi talep ettiği zaman durum değişir. Son olarak, `Running` runbook aslında çalışmaya başladığında durum olur.

4. Runbook işi tamamlandığında, test bölmesi çıktısını görüntüler. Bu durumda, görürsünüz `Hello World` .

    ![Merhaba Dünya Runbook çıkışı](../media/automation-tutorial-runbook-graphical/runbook-test-results.png)

5. Tuvale geri dönmek için Test bölmesini kapatın.

## <a name="step-4---publish-and-start-the-runbook"></a>4. Adım - Runbook’u yayımlama ve başlatma

Oluşturduğunuz runbook hala taslak modunda. Üretimde çalıştırılabilmesi için yayımlanması gerekir. Bir runbook yayımladığınızda, Taslak sürümü mevcut Yayımlanmış sürümün üzerine yazarsınız. Örneğimizde, runbook’u henüz oluşturduğunuzdan, Yayımlanmış sürümünüz yok.

1. Runbook 'u yayımlamak için **Yayımla** ' yı seçin ve istendiğinde **Evet** ' e tıklayın.

2. Runbook 'u runbook 'Lar sayfasında görüntülemek için sola kaydırın ve **yazma durum** değerinin **yayımlandı** olarak ayarlandığını unutmayın.

3. **Myfirstrunbook-grafik** sayfasını görüntülemek için sağ tarafa geri kaydırın.

   Üstteki seçenekler runbook 'u şimdi başlatabilmenizi, gelecekteki bir başlangıç zamanı zamanlamanıza veya Runbook 'un bir HTTP çağrısıyla başlatılabilmesi için [Web kancası](../automation-webhooks.md) oluşturmanıza imkan tanır.

4. **Başlat** ' ı ve ardından runbook 'u başlatmak isteyip Istemediğiniz sorulduğunda **Evet** ' i seçin.

5. Oluşturulan runbook işi için bir Iş bölmesi açılır. **İş durumu** alanının **tamamlandı** görüntülendiğini doğrulayın.

6. Görüntülenecek çıktı sayfasını açmak için **Çıkış** ' a tıklayın `Hello World` .

7. Çıkış sayfasını kapatın.

8. Runbook işine ait Akışlar bölmesini açmak için **Tüm Günlükler**’e tıklayın. Yalnızca `Hello World` Çıkış akışında görmeniz gerekir.

    Akışlar bölmesinin, runbook 'a yazıyorsa ayrıntılı ve hata akışları gibi bir runbook işi için diğer akışları gösterebileceğini unutmayın.

9. MyFirstRunbook-Graphical sayfasına dönmek için akışlar bölmesini ve Iş bölmesini kapatın.

10. Runbook 'un tüm işlerini görüntülemek için **kaynaklar** altında **işler** ' i seçin. Işler sayfasında, runbook 'larınız tarafından oluşturulan tüm işler listelenir. İşi yalnızca bir kez çalıştırdığınız için yalnızca bir iş listelendiğini görmeniz gerekir.

11. Runbook 'u başlattığınızda görüntülediğiniz aynı Iş bölmesini açmak için iş adına tıklayın. Runbook için oluşturulan herhangi bir işin ayrıntılarını görüntülemek için bu bölmeyi kullanın.

## <a name="step-5---create-variable-assets"></a>5. Adım - Değişken varlıkları oluşturma

Runbook 'unuzu test ve yayımladınız, ancak şu ana kadar Azure kaynaklarını yönetmek için faydalı bir şey yapmadınız. Runbook 'u kimlik doğrulaması yapacak şekilde yapılandırmadan önce, abonelik KIMLIĞINI tutmak için bir değişken oluşturmanız, kimlik doğrulaması için bir etkinlik ayarlamanız ve ardından değişkene başvurmanız gerekir. Abonelik bağlamına bir başvuru eklemek, birden çok abonelikle kolayca çalışmanıza olanak sağlar.

1. Gezinti bölmesindeki **abonelikler** SEÇENEĞINDEN abonelik kimliğinizi kopyalayın.

2. Otomasyon hesapları sayfasında, **paylaşılan kaynaklar** altında **değişkenler** ' i seçin.

3. **Değişken Ekle**' yi seçin.

4. Yeni değişken sayfasında, belirtilen alanlarda aşağıdaki ayarları yapın.

    * **Ad** --girin `AzureSubscriptionId` .
    * **Değer** --abonelik kimliğinizi girin.
    * **Tür** --dizeyi tut seçili.
    * **Şifreleme** --varsayılan değeri kullanın.

5. Değişkeni oluşturmak için **Oluştur**’a tıklayın.

## <a name="step-6---add-authentication"></a>6. Adım-kimlik doğrulaması ekleme

Abonelik KIMLIĞINI tutmak için bir değişkene sahip olduğunuza göre, runbook 'u aboneliğiniz için farklı çalıştır kimlik bilgileriyle kimlik doğrulaması yapacak şekilde yapılandırabilirsiniz. Bunu, Azure farklı çalıştır bağlantısını bir varlık olarak ekleyerek yapın. Ayrıca, tuvale [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet 'Ini ve [set-azcontext](/powershell/module/az.accounts/Set-AzContext) cmdlet 'ini de eklemeniz gerekir.

>[!NOTE]
>PowerShell runbook 'ları için `Add-AzAccount` ve `Add-AzureRMAccount` diğer adlardır `Connect-AzAccount` . Bu diğer adların grafik runbook 'larınız için kullanılamayacağını unutmayın. Grafik runbook 'u yalnızca kendisi kullanabilir `Connect-AzAccount` .

1. Runbook 'e gidin ve MyFirstRunbook-Graphical sayfasında **Düzenle** ' yi seçin.

2. `Write Hello World to output`Girişe daha fazla ihtiyacınız yoktur. Üç noktaya tıklayıp **Sil**' i seçmeniz yeterlidir.

3. Kitaplık denetiminde, **varlıklar**' ı ve ardından **Bağlantılar**' ı genişletin. `AzureRunAsConnection` **Tuvale Ekle ' ye** tıklayarak tuvale ekleyin.

4. Olarak yeniden adlandırın `AzureRunAsConnection` `Get Run As Connection` .

5. Kitaplık denetiminde, `Connect-AzAccount` Arama alanına yazın.

6. `Connect-AzAccount`Tuvale ekleyin.

7. `Get Run As Connection`Şeklin altında bir daire görünene kadar üzerine gelin. Bir bağlantı oluşturmak için daireye tıklayın ve oku sürükleyin `Connect-AzAccount` . Runbook ile başlar `Get Run As Connection` ve sonra çalışır `Connect-AzAccount` .

    ![Etkinlikler arasında bağlantı oluşturma](../media/automation-tutorial-runbook-graphical/runbook-link-auth-activities.png)

8. Tuvalde öğesini seçin `Connect-AzAccount` . Yapılandırma denetim bölmesinde **etiket** alanına Azure 'Da **oturum aç** yazın.

9. **Parametreler**' e tıklayın ve etkinlik parametresi yapılandırma sayfası görüntülenir.

10. `Connect-AzAccount`Cmdlet 'in birden çok parametre kümesi vardır ve parametre değerleri sağlamadan önce bir tane seçmeniz gerekir. **Parametre kümesi** ' ne tıklayın ve ardından **Serviceprincipalcertificatewithsubscriptionıd**' yi seçin.

11. Bu parametre kümesinin parametreleri etkinlik parametresi yapılandırma sayfasında görüntülenir. **APPLICATIONID**’ye tıklayın.

    ![Azure hesabı parametreleri ekleme](../media/automation-tutorial-runbook-graphical/Add-AzureRmAccount-params.png)

12. Parametre değeri sayfasında, aşağıdaki ayarları yapın ve ardından **Tamam**' a tıklayın.

   * **Veri kaynağı** -- **etkinlik çıkışı**' nı seçin.
   * Veri kaynağı listesi-- **Otomasyon bağlantısını al**' ı seçin.
   * **Alan yolu** --türü `ApplicationId` . Etkinlik birden fazla özelliğe sahip bir nesne çıkardığından, alan yolu için özelliğin adını belirtiyoruz.

13. **CERTIFICATETHUMBPRINT** öğesine tıklayın ve parametre değeri sayfasında, aşağıdaki ayarları yapın ve ardından **Tamam**' a tıklayın.

    * **Veri kaynağı** -- **etkinlik çıkışı**' nı seçin.
    * Veri kaynağı listesi-- **Otomasyon bağlantısını al**' ı seçin.
    * **Alan yolu** --türü `CertificateThumbprint` .

14. **ServicePrincipal** öğesine tıklayın ve parametre değeri sayfasında, **veri kaynağı** alanı için **ConstantValue** öğesini seçin. **Doğru** seçeneğine ve ardından **Tamam**' a tıklayın.

15. **Tenantıd**' ye tıklayın ve parametre değeri sayfasında aşağıdaki ayarları yapın. İşiniz bittiğinde, iki kez **Tamam** ' a tıklayın.

    * **Veri kaynağı** -- **etkinlik çıkışı**' nı seçin.
    * Veri kaynağı listesi-- **Otomasyon bağlantısını al**' ı seçin.
    * **Alan yolu** --türü `TenantId` .

16. Kitaplık denetiminde, `Set-AzContext` Arama alanına yazın.

17. `Set-AzContext`Tuvale ekleyin.

18. `Set-AzContext`Tuvalde seçin. Yapılandırma denetim bölmesinde `Specify Subscription Id` **etiket** alanına girin.

19. **Parametreler** ' e tıklayın ve etkinlik parametresi yapılandırma sayfası görüntülenir.

20. `Set-AzContext`Cmdlet 'in birden çok parametre kümesi vardır ve parametre değerleri sağlamadan önce bir tane seçmeniz gerekir. **Parametre kümesi** ' ne tıklayın ve ardından **SubscriptionID**' yi seçin.

21. Bu parametre kümesinin parametreleri etkinlik parametresi yapılandırma sayfasında görüntülenir. **SubscriptionID** öğesine tıklayın.

22. Parametre değeri sayfasında, **veri kaynağı** alanı Için **değişken varlık** ' ı seçin ve kaynak listesinden **Azuyeniden gönderme scriptionıd** ' yi seçin. İşiniz bittiğinde, iki kez **Tamam** ' a tıklayın.

23. `Login to Azure`Şeklin altında bir daire görünene kadar üzerine gelin. Daireye tıklayın ve oku sürükleyin `Specify Subscription Id` . Runbook 'larınızın bu noktada aşağıdaki gibi görünmesi gerekir.

    :::image type="content" source="../media/automation-tutorial-runbook-graphical/runbook-auth-config.png" alt-text="Ok, ' abonelik KIMLIĞINI belirt ' öğesine sürüklendikten sonra runbook 'un ekran görüntüsü.":::

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>7. Adım - Sanal makineyi başlatmak üzere etkinlik ekleme

Artık bir `Start-AzVM` sanal makineyi başlatmak için bir etkinlik eklemeniz gerekir. Azure aboneliğinizdeki herhangi bir VM 'yi seçebilirsiniz ve şu anda adını [Start-AzVM](/powershell/module/az.compute/start-azvm) cmdlet 'ine kodırsınız.

1. Kitaplık denetiminde, `Start-Az` Arama alanına yazın.

2. `Start-AzVM`Tuvale ekleyin ve ardından altına tıklayıp sürükleyin `Specify Subscription Id` .

3. `Specify Subscription Id`Şeklin altında bir daire görünene kadar üzerine gelin. Daireye tıklayın ve oku sürükleyin `Start-AzVM` .

4. `Start-AzVM` öğesini seçin. Etkinliğin kümelerini görüntülemek için **Parametreler** ' e ve ardından **parametre kümesi** ' ne tıklayın.

5. Parametre kümesi için **Resourcegroupnameparametersetname** ' ı seçin. **Resourcegroupname** ve **Name** alanları, bunların yanında, gerekli parametreler olduğunu göstermek için ünlem işaretleri vardır. Her iki alanın de dize değerleri beklediği unutulmamalıdır.

6. **Ad**’ı seçin. **Veri kaynağı** alanı için **PowerShell ifadesi** ' ni seçin. Bu runbook 'u başlatmak için kullandığınız VM için, çift tırnak işaretleriyle çevrelenen makine adını yazın. **Tamam**'a tıklayın.

7. **ResourceGroupName**’i seçin. **Veri kaynağı** alanı için **PowerShell ifadesi** değerini kullanın ve çift tırnaklarla çevrelenen kaynak grubunun adını yazın. **Tamam**'a tıklayın.

8. Runbook 'u test edebilmeniz için **Test bölmesi** ' ne tıklayın.

9. Testi başlatmak için **Başlat** ' a tıklayın. Tamamlandıktan sonra, VM 'nin başlatıldığından emin olun. Runbook 'larınızın bu noktada aşağıdaki gibi görünmesi gerekir.

    ![Runbook Start-AzVM çıkışı](../media/automation-tutorial-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>8. adım-ek giriş parametreleri ekleme

Runbook 'larınız Şu anda cmdlet 'i için belirttiğiniz kaynak grubundaki VM 'yi başlatır `Start-AzVM` . Runbook başlatıldığında hem ad hem de kaynak grubu belirtirseniz runbook daha yararlı olacaktır. Bu işlevi sağlamak için Runbook 'a giriş parametreleri ekleyelim.

1. MyFirstRunbook-Graphical sayfasında **Düzenle** ' ye tıklayarak grafik düzenleyiciyi açın.

2. **Giriş ve çıkış** ' ı seçin ve ardından giriş ' e **ekleyerek** runbook giriş parametresi bölmesini açın.

3. Aşağıdaki ayarları, belirtilen alanlarda yapın ve ardından **Tamam**' a tıklayın.
   * **Ad** --belirtin `VMName` .
   * **Tür** --dize ayarını saklayın.
   * **Zorunlu** --değeri **Evet** olarak değiştirin.

4. Adlı ikinci bir zorunlu giriş parametresi oluşturun `ResourceGroupName` ve ardından giriş ve çıkış bölmesini kapatmak Için **Tamam** ' ı tıklatın.

    ![Runbook Giriş Parametreleri](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-outputs.png)

5. Etkinliği seçin `Start-AzVM` ve ardından **Parametreler**' e tıklayın.

6. **Ad** için **veri kaynağı** alanını **runbook girişi** olarak değiştirin. Ardından **VMName**' i seçin.

7. **Resourcegroupname** için **veri kaynağı** alanını **runbook girişi** olarak değiştirin ve ardından **resourcegroupname** öğesini seçin.

    ![Start-AzVM parametreleri](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-runbookinput.png)

8. Runbook'u kaydedin ve Test bölmesini açın. Şimdi testte kullanılacak olan iki girdi değişkeni için değerleri sağlayabilirsiniz.

9. Test bölmesini kapatın.

10. Runbook’un yeni sürümünü yayımlamak için **Yayımla**’ya tıklayın.

11. Daha önce başlattığınız VM 'yi durdurun.

12. Runbook'u başlatmak için **Başlat**’a tıklayın. `VMName` `ResourceGroupName` Başlatmak istediğiniz VM için ve değerlerini yazın.

13. Runbook tamamlandığında, VM 'nin başlatıldığından emin olun.

## <a name="step-9---create-a-conditional-link"></a>9. Adım - Koşullu bağlantı oluşturma

Artık, runbook 'u yalnızca henüz başlatılmamışsa VM 'yi başlatmaya çalışır şekilde değiştirebilirsiniz. Bunu, sanal makinenin örnek düzeyi durumunu alan bir [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) cmdlet 'i ekleyerek yapın. Ardından, `Get Status` VM durumunun çalışıyor veya durduruldu olduğunu anlamak Için PowerShell kodu kod parçacığına sahip adlı bir PowerShell Iş akışı kodu modülü ekleyebilirsiniz. Modülden koşullu bağlantı `Get Status` yalnızca `Start-AzVM` geçerli çalışma durumu durdurulmuşsa çalışır. Bu yordamın sonunda, runbook 'un `Write-Output` başarılı bir şekilde başlatılıp başlatılamadığını bildirmek üzere bir ileti çıktısı almak için cmdlet 'ini kullanır.

1. Grafik düzenleyicide **Myfirstrunbook-grafik** ' i açın.

2. Ve arasında bağlantıyı, `Specify Subscription Id` `Start-AzVM` sonra da **Sil**' e tıklayarak kaldırın.

3. Kitaplık denetiminde, `Get-Az` Arama alanına yazın.

4. `Get-AzVM`Tuvale ekleyin.

5. `Get-AzVM`Cmdlet 'i için kümeleri görüntülemek üzere **parametre kümesi** ' ni seçin ve tıklayın.

6. **GetVirtualMachineInResourceGroupNameParamSet** parametre kümesini seçin. **Resourcegroupname** ve **ad** alanları, bunların yanında, gerekli parametreleri belirttikleri belirten ünlem işaretleri vardır. Her iki alanın de dize değerleri beklediği unutulmamalıdır.

7. **Ad** için **veri kaynağı** altında, **runbook girişi**' ni ve ardından **VMName**' i seçin. **Tamam**'a tıklayın.

8. **Resourcegroupname** için **veri kaynağı** altında, **runbook girişi**' ni ve ardından **resourcegroupname**' ı seçin. **Tamam**'a tıklayın.

9. **Durum** için **veri kaynağı** altında, **sabit değer**' i seçin ve ardından **doğru**. **Tamam**'a tıklayın.

10. ' Den ' e bir bağlantı oluşturun `Specify Subscription Id` `Get-AzVM` .

11. Kitaplık denetiminde, **runbook denetimi** ' ni genişletin ve tuvale **kod** ekleyin.  

12. ' Den ' e bir bağlantı oluşturun `Get-AzVM` `Code` .  

13. Yapılandırma bölmesinde, ve ' a tıklayın, `Code` **durumu almak** için etiketi değiştirin.

14. Seçim `Code` ve kod Düzenleyicisi sayfası görüntülenir.  

15. Aşağıdaki kod parçacığını düzenleyici sayfasına yapıştırın.

    ```powershell
    $Statuses = $ActivityOutput['Get-AzVM'].Statuses
    $StatusOut = ""
    foreach ($Status in $Statuses) {
      if($Status.Code -eq "Powerstate/running")
        {$StatusOut = "running"}
      elseif ($Status.Code -eq "Powerstate/deallocated")
        {$StatusOut = "stopped"}
    }
    $StatusOut
    ```

16. ' Den ' e bir bağlantı oluşturun `Get Status` `Start-AzVM` .

    ![Kod Modülü ile Runbook](../media/automation-tutorial-runbook-graphical/runbook-startvm-get-status.png)  

17. Bağlantıyı seçin ve Yapılandırma bölmesinde **koşul Uygula** ' yı **Evet** olarak değiştirin. Bağlantının, hedef etkinliğin yalnızca koşulun true olarak çözümlenirse çalışacağını belirten kesikli bir çizgi haline geldiğini unutmayın.  

18. **Koşul ifadesi** için, yazın `$ActivityOutput['Get Status'] -eq "Stopped"` . `Start-AzVM` Artık yalnızca VM durdurulmuşsa çalışır.

19. Kitaplık denetiminde, **Cmdlet'leri** ve ardından **Microsoft.PowerShell.Utility**’yi genişletin.

20. `Write-Output`Tuvale iki kez ekleyin.

21. İlk denetim için `Write-Output` , **Parametreler** ' e tıklayın ve **etiket** değerini **VM başladı olarak bildir** olarak değiştirin.

22. **InputObject** Için, **veri kaynağını** **PowerShell ifadesi** olarak değiştirin ve ifadeye yazın `$VMName successfully started.` .

23. İkinci `Write-Output` denetimde **Parametreler** ' e tıklayın ve **etiket** değerini **VM 'ye bildir hatasıyla** değiştirin.

24. **InputObject** Için, **veri kaynağını** **PowerShell ifadesi** olarak değiştirin ve ifadeye yazın `$VMName could not start` .

25. `Start-AzVM`Ve ' den ' e bağlantılar oluşturun `Notify VM Started` `Notify VM Start Failed` .

26. Bağlantısını seçin `Notify VM Started` ve **koşul Uygula** ' yı true olarak değiştirin.

27. **Koşul ifadesi** için, yazın `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` . Bu `Write-Output` Denetim artık yalnızca VM başarıyla başlarsa çalışır.

28. Bağlantısını seçin `Notify VM Start Failed` ve **koşul Uygula** ' yı true olarak değiştirin.

29. **Koşul ifadesi** alanı için, yazın `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` . Bu `Write-Output` Denetim artık yalnızca VM başarıyla başlatılmamışsa çalışır. Runbook 'larınızın aşağıdaki görüntü gibi görünmesi gerekir.

    ![Write-Output ile Runbook](../media/automation-tutorial-runbook-graphical/runbook-startazurermvm-complete.png)

30. Runbook'u kaydedin ve Test bölmesini açın.

31. VM 'yi durdurulan runbook 'u başlatın ve makinenin başlaması gerekir.

## <a name="next-steps"></a>Sonraki adımlar

* Grafik yazma hakkında daha fazla bilgi edinmek için bkz. [Azure Otomasyonu 'nda grafik runbook 'U yazma](../automation-graphical-authoring-intro.md).
* PowerShell runbook 'larını kullanmaya başlamak için bkz. [PowerShell runbook 'U oluşturma](automation-tutorial-runbook-textual-powershell.md).
* PowerShell Iş akışı runbook 'larını kullanmaya başlamak için bkz. [PowerShell iş akışı runbook 'U oluşturma](automation-tutorial-runbook-textual.md).
* PowerShell cmdlet başvurusu için bkz. [az. Automation](/powershell/module/az.automation).
