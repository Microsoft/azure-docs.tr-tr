---
title: PowerShell Galerisi 'de Azure Otomasyonu runbook 'larını ve modüllerini kullanma
description: Bu makalede, Microsoft GitHub depoları ve PowerShell Galerisi runbook 'ları ve modülleri nasıl kullanacağınız açıklanır.
services: automation
ms.subservice: process-automation
ms.date: 04/07/2021
ms.topic: conceptual
ms.openlocfilehash: 2df019888d293cd8a25a34e6f0f4e7dd215c6a41
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030688"
---
# <a name="use-existing-runbooks-and-modules"></a>Mevcut runbook 'ları ve modülleri kullanma

Azure Otomasyonu 'nda kendi runbook 'larınızı ve modüllerinizi oluşturmak yerine, Microsoft ve Community tarafından önceden oluşturulmuş senaryolara erişebilirsiniz. Azure portal, Azure ile ilgili PowerShell ve Python runbook 'larını, PowerShell Galerisi runbook galerisinden ve [modüller](#modules-in-the-powershell-gallery) ve [runbook](#runbooks-in-the-powershell-gallery) 'lardan (Azure 'a özgü olabilir veya buna özgü olmayabilir), bu sayede edinebilirsiniz. Ayrıca, [geliştirdiğiniz senaryoları](#contribute-to-the-community)paylaşarak topluluğa katkıda bulunabilirsiniz.

> [!NOTE]
> TechNet Betik Merkezi devre dışı bırakılıyor. Runbook galerisindeki betik merkezi 'ndeki tüm runbook 'lar [Otomasyon GitHub kuruluşumuza](https://github.com/azureautomation) taşındı daha fazla bilgi için bkz. [Azure Otomasyonu runbook 'ları GitHub 'a taşınıyor](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337).

## <a name="import-runbooks-from-github-with-the-azure-portal"></a>Runbook 'ları Azure portal ile GitHub 'dan içeri aktarın

1. Azure portalında, Otomasyon hesabınızı açın.
2. **Işlem Otomasyonu** altında **runbook 'lar Galerisi** ' ni seçin.
3. **Kaynak: GitHub**' ı seçin.
4. Görünümü yayımcıya, türüne ve sıralamaya göre daraltmak için listenin üzerindeki filtreleri kullanabilirsiniz. İstediğiniz Galeri öğesini bulun ve ayrıntılarını görüntülemek için seçin.

   :::image type="content" source="./media/automation-runbook-gallery/browse-gallery-github.png" alt-text="Runbook galerisine göz atma." lightbox="./media/automation-runbook-gallery/browse-gallery-github-expanded.png":::

5. Bir öğeyi içeri aktarmak için Ayrıntılar sayfasında **Içeri aktar** ' a tıklayın.

   :::image type="content" source="./media/automation-runbook-gallery/gallery-item-import.png" alt-text="Galeri öğesi içeri aktarma.":::

6. İsteğe bağlı olarak, içeri aktarma dikey penceresinde runbook 'un adını değiştirin ve ardından runbook 'u içeri aktarmak için **Tamam** ' ı tıklatın.

   :::image type="content" source="./media/automation-runbook-gallery/gallery-item-import-blade.png" alt-text="Galeri öğesi içeri aktarma dikey penceresi.":::

7. Runbook, Otomasyon hesabı için **runbook 'lar** sekmesinde görünür.
 
## <a name="runbooks-in-the-powershell-gallery"></a>PowerShell Galerisi runbook 'lar

> [!IMPORTANT]
> PowerShell Galerisi aldığınız tüm runbook 'ların içeriğini doğrulamanız gerekir. Bunları bir üretim ortamına yüklemek ve çalıştırmak için çok dikkatli olun.

[PowerShell Galerisi](https://www.powershellgallery.com/packages) , Microsoft 'tan ve topluluk tarafından Azure Otomasyonu 'na aktarabileceğiniz çeşitli runbook 'lar sağlar. Bir tane kullanmak için, Galeriden bir runbook indirin veya Runbook 'tan veya Azure portal Otomasyon hesabınızdan doğrudan runbook 'ları içeri aktarabilirsiniz.

> [!NOTE]
> Grafik runbook 'ları PowerShell Galerisi desteklenmez.

Azure portal kullanarak yalnızca PowerShell Galerisi doğrudan içeri aktarabilirsiniz. PowerShell kullanarak bu işlevi gerçekleştiremezsiniz. Bu yordam, **kaynak** **PowerShell Galerisi** olması dışında, [runbook 'Ları GitHub 'dan Azure Portal içeri aktarma](#import-runbooks-from-github-with-the-azure-portal)işleminde gösterilenle aynıdır.

:::image type="content" source="./media/automation-runbook-gallery/source-runbook-gallery-small.png" alt-text="Runbook Galerisi kaynak seçimi gösteriliyor." lightbox="./media/automation-runbook-gallery/source-runbook-gallery-large.png":::

## <a name="modules-in-the-powershell-gallery"></a>PowerShell Galerisi modüller

PowerShell modülleri, runbook 'larınızda kullanabileceğiniz cmdlet 'leri içerir. Azure Otomasyonu 'nda yükleyebileceğiniz mevcut modüller [PowerShell Galerisi](https://www.powershellgallery.com)kullanılabilir. Bu galeriyi Azure portal başlatabilir ve modülleri doğrudan Azure Otomasyonu 'na yükleyebilir veya el ile indirip yükleyebilirsiniz.

Ayrıca, Azure portal içeri aktarılacak modülleri de bulabilirsiniz. Bunlar, **paylaşılan kaynaklar** altındaki **modüller galerisinde** Otomasyon hesabınız için listelenir.

## <a name="common-scenarios-available-in-the-powershell-gallery"></a>PowerShell Galerisi kullanılabilen yaygın senaryolar

Aşağıdaki liste, yaygın senaryoları destekleyen birkaç runbook içerir. Azure Otomasyonu ekibi tarafından oluşturulan runbook 'ların tam listesi için bkz. [Azureautomationteam profile](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) -PowerShell Galerisi bir Otomasyon hesabındaki tüm modüllerin en son sürümünü içeri aktarır.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) -iş durumu ve iş akışları Içeren Azure Otomasyonu günlüklerini almak için Azure Tanılama ve Log Analytics yapılandırır.
   * [Copy-ıtemfrolaurevm](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) -bir Windows Azure sanal makinesinden uzak bir dosyayı kopyalar.
   * [Copy-ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) -bir Azure sanal makinesine yerel bir dosya kopyalar.

## <a name="contribute-to-the-community"></a>Topluluğa katkıda bulunma

Azure Otomasyonu topluluğu 'na katkıda bulunmak ve bunları büyütmeniz önemle önerilir. Toplulukla oluşturduğunuz harika runbook 'ları paylaşabilirsiniz. Katkılarınız, teşekkürler!

### <a name="add-a-runbook-to-the-github-runbook-gallery"></a>GitHub runbook galerisine runbook ekleme

Bu GitHub iş akışıyla runbook galerisine yeni PowerShell veya Python runbook 'ları ekleyebilirsiniz.

1. GitHub 'da ortak bir depo oluşturun ve Runbook 'u ve diğer gerekli dosyaları (readme.md, açıklama vb. gibi) ekleyin.
1. `azureautomationrunbookgallery`Deponun hizmetimizin olduğundan emin olmak için konuyu ekleyin, bu nedenle Otomasyon Runbook galerisinde görüntülenebilirler.
1. Oluşturduğunuz Runbook bir PowerShell iş akışıdır, konusunu ekleyin `PowerShellWorkflow` . Bu bir Python 3 runbook ise ekleyin `Python3` . Azure runbook 'lar için başka belirli konular gerekmez, ancak runbook galerisinde kategori ve arama için kullanılabilecek başka konular eklemenizi öneririz.

   >[!NOTE]
   >Görüntülenen biçimlendirme, üst bilgiler ve mevcut Etiketler (örneğin, veya) gibi işlemler için galerideki mevcut runbook 'lara göz atın `Azure Automation` `Linux Azure Virtual Machines` .

Var olan bir runbook 'ta değişiklik önermek için, buna karşı bir çekme isteği yapın. 

Var olan bir runbook 'u kopyalamaya ve düzenlemeye karar verirseniz, en iyi yöntem buna farklı bir ad vermektir. Eski adı yeniden kullanırsanız, Runbook Galerisi listesinde iki kez görünür.

>[!NOTE]
>Lütfen hem güncelleştirilmiş hem de yeni runbook 'lar için GitHub ve Automation Runbook Galerisi arasında eşitleme için en az 12 saate izin verin.

### <a name="add-a-powershell-runbook-to-the-powershell-gallery"></a>PowerShell galerisine PowerShell runbook 'u ekleme

Microsoft, diğer müşteriler için yararlı olacağını düşündüğünüz PowerShell Galerisi runbook eklemenizi önerir. PowerShell Galerisi PowerShell modüllerini ve PowerShell betiklerini kabul eder. Bir runbook 'u [PowerShell Galerisi yükleyerek](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package)ekleyebilirsiniz.

## <a name="import-a-module-from-the-modules-gallery-in-the-azure-portal"></a>Azure portal modüller galerisinden bir modül içeri aktarın

1. Azure portalında, Otomasyon hesabınızı açın.
1. **Paylaşılan kaynaklar** altında, modüller listesini açmak için **modüller Galerisi** ' ni seçin.

      :::image type="content" source="media/automation-runbook-gallery/modules-blade-sm.png" alt-text="Modül galerisinin görünümü." lightbox="media/automation-runbook-gallery/modules-blade-lg.png":::

1. Galeriye gözatamıyorum sayfasında, aşağıdaki alanlara göre arama yapabilirsiniz:

   * Modül Adı
   * Etiketler
   * Yazma
   * Cmdlet/DSC kaynak adı

1. İlgilendiğiniz bir modül bulun ve ayrıntılarını görüntülemek için seçin.

   Belirli bir modülün detayına göre daha fazla bilgi görüntüleyebilirsiniz. Bu bilgiler PowerShell Galerisi, tüm gerekli bağımlılıkları ve modülün içerdiği tüm cmdlet 'leri ya da DSC kaynaklarını geri bir bağlantı içerir.

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-details-blade-sm.png" alt-text="Galeriden bir modülün ayrıntılı görünümü." lightbox="media/automation-runbook-gallery/gallery-item-details-blade-lg.png":::

1. Modülü doğrudan Azure Otomasyonu 'na yüklemek için **Içeri aktar**' a tıklayın.
1. Içeri aktarma bölmesinde, içeri aktarılacak modülün adını görebilirsiniz. Tüm bağımlılıklar yüklüyse, **Tamam** düğmesi etkinleştirilir. Bağımlılıklarınız eksikse, bu modülü içeri aktarmadan önce bu bağımlılıkları içeri aktarmanız gerekir.
1. Içeri aktarma bölmesinde, modülü içeri aktarmak için **Tamam** ' ı tıklatın. Azure Otomasyonu hesabınıza bir modül içeri aktarırken, modül ve cmdlet 'ler hakkında meta verileri ayıklar. Bu eylem, her etkinliğin ayıklanması gerektiğinden birkaç dakika sürebilir.
1. Modülün dağıtılmakta olduğu bir başlangıç bildirimi ve tamamlandığında başka bir bildirim alırsınız.
1. Modül içeri aktarıldıktan sonra, kullanılabilir etkinlikleri görebilirsiniz. Runbook 'larınızda ve DSC kaynaklarınızın modül kaynaklarını kullanabilirsiniz.

> [!NOTE]
> Yalnızca PowerShell Core 'u destekleyen modüller Azure Otomasyonu 'nda desteklenmez ve Azure portal içeri aktarılamaz ya da doğrudan PowerShell Galerisi dağıtılır.

## <a name="request-a-runbook-or-module"></a>Runbook veya modül isteme

İstekleri [Kullanıcı seslerine](https://feedback.azure.com/forums/246290-azure-automation/)gönderebilirsiniz.  Bir Runbook yazma veya PowerShell hakkında sorularınız ile ilgili yardıma ihtiyacınız varsa [Microsoft Q&soru sayfasına](/answers/topics/azure-automation.html)bir soru gönderin.

## <a name="next-steps"></a>Sonraki adımlar

* PowerShell runbook 'larını kullanmaya başlamak için bkz. [öğretici: PowerShell runbook 'U oluşturma](learn/automation-tutorial-runbook-textual-powershell.md).
* Runbook 'larla çalışmak için bkz. [Azure Otomasyonu 'nda runbook 'Ları yönetme](manage-runbooks.md).
* PowerShell betiği hakkında daha fazla bilgi için bkz. [PowerShell docs](/powershell/scripting/overview).
* PowerShell cmdlet başvurusu için bkz. [az. Automation](/powershell/module/az.automation).
