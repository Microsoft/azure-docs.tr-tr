---
title: Bir runbook 'tan Azure Otomasyonu Değişiklik İzleme ve envanterini etkinleştirme
description: Bu makalede, bir runbook 'tan Değişiklik İzleme ve envanterin nasıl etkinleştirileceği açıklanır.
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: e5b42d6102737b778ea5d19cd7da3c2f64881b1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585920"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>Runbook’tan Değişiklik İzleme ve Stok özelliğini etkinleştirme

Bu makalede, ortamınızdaki VM 'Ler için [değişiklik izleme ve envanteri](overview.md) etkinleştirmek üzere bir runbook 'u nasıl kullanabileceğiniz açıklanır. Azure VM 'lerini ölçekli olarak etkinleştirmek için, Değişiklik İzleme ve envanterini kullanarak var olan bir VM 'yi etkinleştirmeniz gerekir.

> [!NOTE]
> Değişiklik İzleme ve envanteri etkinleştirirken, bir Log Analytics çalışma alanını ve bir Otomasyon hesabını bağlamak için yalnızca belirli bölgeler desteklenir. Desteklenen eşleme çiftlerinin bir listesi için bkz. [Otomasyon hesabı ve Log Analytics çalışma alanı Için bölge eşleme](../how-to/region-mappings.md).

Bu yöntem iki Runbook kullanır:

* **Enable-MultipleSolution** -yapılandırma bilgileri isteyen birincil Runbook, belirtilen VM 'yi sorgular ve diğer doğrulama denetimlerini gerçekleştirir ve ardından, belirtilen kaynak grubundaki her bir VM için değişiklik izleme ve envanteri yapılandırmak üzere **Enable-automationsolution** runbook 'u çağırır.
* **Enable-AutomationSolution** -hedef kaynak grubunda belirtilen bir veya daha fazla vm için değişiklik izleme ve envanteri etkinleştirir. Önkoşulların karşılandığını doğrular, Log Analytics VM uzantısının yüklendiğini ve bulunamadığını doğrular ve VM 'Leri Otomasyon hesabına bağlı belirtilen Log Analytics çalışma alanındaki kapsam yapılandırmasına ekler.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Henüz bir hesabınız yoksa [MSDN abone avantajlarınızı etkinleştirebilir](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) veya [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)için kaydolabilirsiniz.
* Makineleri yönetmek için [Otomasyon hesabı](../automation-security-overview.md).
* [Log Analytics çalışma alanı](../../azure-monitor/logs/design-logs-deployment.md)
* Bir [sanal makine](../../virtual-machines/windows/quick-create-portal.md).
* **Enable-AutomationSolution** runbook tarafından kullanılan iki Otomasyon varlığı. Bu runbook, Otomasyon hesabınızda zaten mevcut değilse, ilk çalıştırma sırasında **Enable-MultipleSolution** runbook tarafından otomatik olarak içeri aktarılır.
    * *Lasolutionsubscriptionıd*: Log Analytics çalışma alanının bulunduğu abonelik kimliği.
    * *Lasolutionworkspace ID*: Otomasyon hesabınıza bağlı Log Analytics çalışma alanının kimliği.

    Bu değişkenler, eklendi sanal makinesinin çalışma alanını yapılandırmak için kullanılır. Bunlar belirtilmemişse, komut dosyası öncelikle aboneliğindeki Değişiklik İzleme ve stoğa yönelik herhangi bir VM eklendi arar ve ardından Otomasyon hesabının bulunduğu aboneliğin ardından Kullanıcı hesabınızın erişebileceği diğer tüm abonelikler gelir. Doğru yapılandırılmamışsa bu durum, makinelerinizin bazı rasgele Log Analytics çalışma alanına eklendi alma sonucu verebilir.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="enable-change-tracking-and-inventory"></a>Değişiklik İzlemeyi ve Sayımı Etkinleştirme

1. Azure portal **Otomasyon hesapları**' na gidin. **Otomasyon hesapları** sayfasında, listeden hesabınızı seçin.

1. Otomasyon hesabınızda, **Envanter** veya **yapılandırma yönetimi** altında **değişiklik izleme** ' yi seçin.

1. Log Analytics çalışma alanını seçin ve **Etkinleştir**' e tıklayın. Envanter veya Değişiklik İzleme etkinleştirilirken bir başlık gösterilir.

    ![Değişiklik İzlemeyi ve Sayımı Etkinleştirme](media/enable-from-automation-account/enable-feature.png)

## <a name="install-and-update-modules"></a>Modülleri yükleme ve güncelleştirme

En son Azure modüllerine güncelleştirmek ve daha [az. Operationalınsights](/powershell/module/az.operationalinsights) modülünü içeri aktarmak için Runbook ile sanal makinelerinize yönelik güncelleştirme yönetimi başarıyla etkinleştirmeniz gerekir.

1. Otomasyon hesabınızda, **paylaşılan kaynaklar** altında **modüller** ' i seçin.

2. Azure modüllerini en son sürüme güncelleştirmek için **Azure Modüllerini Güncelleştir** seçeneğini belirleyin.

3. Var olan tüm Azure modüllerini en son sürüme güncelleştirmek için **Evet** ' e tıklayın.

    ![Modülleri güncelleştirme](media/enable-from-runbook/update-modules.png)

4. **Paylaşılan kaynaklar** altındaki **modüllere** geri dönün.

5. Modül galerisini açmak için **tarayıcı galerisine gidin** ' i seçin.

6. `Az.OperationalInsights`Bu modülü arayıp Otomasyon hesabınızda içeri aktarın.

    ![OperationalInsights modülünü içeri aktarma](media/enable-from-runbook/import-operational-insights-module.png)

## <a name="select-azure-vm-to-manage"></a>Yönetmek için Azure VM 'yi seçin

Değişiklik İzleme ve envanter etkinleştirildiğinde, özelliği tarafından yönetim için bir Azure VM ekleyebilirsiniz.

1. Otomasyon hesabınızdan, **yapılandırma yönetimi** altında **değişiklik izleme** veya **Stok** ' ı seçin.

2. VM 'nizi eklemek için **Azure VM 'Leri Ekle** ' ye tıklayın.

3. Listeden VM 'nizi seçin ve **Etkinleştir**' e tıklayın. Bu eylem VM 'nin Değişiklik İzleme ve envanterini sunar.

   ![VM Değişiklik İzleme ve envanterini etkinleştirme](media/enable-from-runbook/enable-azure-vm.png)

    > [!NOTE]
    > Değişiklik İzleme ve envanter kurulumu tamamlanmadan önce başka bir özelliği etkinleştirmeye çalışırsanız şu iletiyi alırsınız: `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>Değişiklik İzleme ve envanteri etkinleştirmek için Runbook 'u içeri aktarma

1. Otomasyon hesabınızda, **Işlem Otomasyonu** altında **runbook 'lar** ' ı seçin.

2. **Galeriye gözat** seçeneğini belirleyin.

3. **Güncelleştirme ve değişiklik izleme** için arama yapın.

4. Runbook 'u seçin ve **kaynak görüntüleme** sayfasında **içeri aktar** ' a tıklayın.

5. Runbook 'u Otomasyon hesabına aktarmak için **Tamam** ' ı tıklatın.

   ![Kurulum için Runbook 'u içeri aktar](media/enable-from-runbook/import-from-gallery.png)

6. **Runbook** sayfasında, **Enable-multiplesolution** runbook ' u seçin ve ardından **Düzenle**' ye tıklayın. Metinsel düzenleyicide  **Yayımla**' yı seçin.

7. Onaylamanız istendiğinde, runbook 'u yayımlamak için **Evet** ' e tıklayın.

## <a name="start-the-runbook"></a>Runbook’u başlatma

Bu runbook 'u başlatmak için bir Azure VM için Değişiklik İzleme ve envanteri etkinleştirmiş olmanız gerekir. Hedef kaynak grubunda bir veya daha fazla sanal makine yapılandırmak için özelliği etkinleştirilmiş mevcut bir VM ve kaynak grubu gerektirir.

1. **Enable-MultipleSolution** runbook 'unu açın.

   ![Birden çok çözüm runbook 'u](media/enable-from-runbook/runbook-overview.png)

2. Başlat düğmesine tıklayın ve parametre değerlerini aşağıdaki alanlara girin:

   * **VMName** -değişiklik izleme ve stoğa eklenecek mevcut bir sanal makinenin adı. Kaynak grubundaki tüm VM 'Leri eklemek için bu alanı boş bırakın.
   * **Vmresourcegroup** -etkinleştirilecek VM 'ler için kaynak grubunun adı.
   * **SubscriptionID** -etkinleştirilecek yeni sanal makınenın abonelik kimliği. Çalışma alanının aboneliğini kullanmak için bu alanı boş bırakın. Farklı bir abonelik KIMLIĞI kullandığınızda, Otomasyon hesabınızın farklı çalıştır hesabını abonelik için katkıda bulunan olarak ekleyin.
   * **Alreadyonboardedvd** -güncelleştirmeler için zaten etkinleştirilmiş olan sanal makinenin adı.
   * **Alreadyonboardedvmresourcegroup** -VM 'nin ait olduğu kaynak grubunun adı.
   * **Solutiontype** - **ChangeTracking** girin.

   ![Enable-MultipleSolution runbook parametreleri](media/enable-from-runbook/runbook-parameters.png)

3. Runbook işini başlatmak için **Tamam**’ı seçin.

4. Runbook işinin ilerlemesini ve **işler** sayfasındaki hataları izleyin.

## <a name="next-steps"></a>Sonraki adımlar

* Bir runbook 'u zamanlamak için bkz. [Azure Otomasyonu 'nda zamanlamaları yönetme](../shared-resources/schedules.md).

* Özelliği ile çalışma hakkında ayrıntılı bilgi için bkz. [değişiklik izleme yönetme](manage-change-tracking.md) ve [envanteri yönetme](manage-inventory-vms.md).

* Özellikle ilgili sorunları gidermek için bkz. [değişiklik izleme ve envanter sorunlarını giderme](../troubleshoot/change-tracking.md).


