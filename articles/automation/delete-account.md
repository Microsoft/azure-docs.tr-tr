---
title: Azure Otomasyonu hesabınızı silme
description: Bu makalede, Otomasyon hesabınızı farklı yapılandırma senaryolarında nasıl silebilecekleri açıklanır.
services: automation
ms.service: automation
ms.subservice: process-automation
ms.date: 04/15/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fe2d99a610be3877b4a347e4bd0dd17df53ba326
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834165"
---
# <a name="how-to-delete-your-azure-automation-account"></a>Azure Otomasyonu hesabınızı silme

Bir Azure Otomasyonu hesabını, BT veya iş sürecini otomatik hale getirmeye yardımcı olmak üzere etkinleştirdikten sonra veya diğer özelliklerini Azure ve Güncelleştirme Yönetimi gibi Azure dışı makinelerinizin işlem yönetimini destekleyecek şekilde etkinleştirmek üzere etkinleştirdikten sonra Otomasyon hesabını kullanmayı durdurmayı seçebilirsiniz. Azure Izleyici Log Analytics çalışma alanıyla tümleştirmeye bağlı özellikleri etkinleştirdiyseniz, bu eylemi tamamlamaya yönelik daha fazla adım gerekir.

Otomasyon hesabınızı kaldırmak, desteklenen dağıtım modellerine bağlı olarak aşağıdaki yöntemlerden biri kullanılarak yapılabilir:

* Otomasyon hesabını içeren kaynak grubunu silin.
* Otomasyon hesabını ve bağlı Azure Izleyici Log Analytics çalışma alanını içeren kaynak grubunu silin:

    * Hesap ve çalışma alanı, Güncelleştirme Yönetimi, Değişiklik İzleme ve envanteri ve/veya VM'leri çalışma saatleri dışında başlat/durdur desteklemek için ayrılmıştır.
    * Hesap, runbook iş durumu ve iş akışları göndermek için bir çalışma alanıyla tümleştirilmiş ve işlem otomasyonu için ayrılmıştır.

* Log Analytics çalışma alanının Otomasyon hesabından bağlantısını kaldırın ve otomasyon hesabını silin.
* Özelliği bağlantılı çalışma alanınızdan silin, çalışma alanındaki hesabın bağlantısını kaldırın ve ardından Otomasyon hesabını silin.

Bu makalede, Azure PowerShell, Azure CLı veya REST API kullanarak Azure portal Otomasyon hesabınızı tamamen nasıl kaldıracakları açıklanır.

> [!NOTE]
> Devam etmeden önce, abonelik, kaynak grubu veya kaynak üzerinde, önemli kaynakların yanlışlıkla silinmesini veya değiştirilmesini önleyen [Kaynak Yöneticisi kilit](../azure-resource-manager/management/lock-resources.md) olmadığından emin olun. VM'leri çalışma saatleri dışında başlat/durdur çözümünü dağıttıysanız, Otomasyon hesabındaki birkaç bağımlı kaynağa (özellikle runbook 'lar ve değişkenler) karşı kilit düzeyini **Cannotdelete** olarak ayarlar. Otomasyon hesabını silebilmeniz için önce tüm kilitlerin kaldırılması gerekir.

## <a name="delete-the-dedicated-resource-group"></a>Adanmış kaynak grubunu silme

Otomasyon hesabınızı ve ayrıca hesap için ayrılmış aynı kaynak grubunda oluşturulan hesapla Log Analytics çalışma alanını silmek için, [Azure Resource Manager kaynak grubu ve kaynak silme](../azure-resource-manager/management/delete-resource-group.md) makalesinde belirtilen adımları izleyin.

## <a name="delete-a-standalone-automation-account"></a>Tek başına Otomasyon hesabını silme

Otomasyon hesabınız bir Log Analytics çalışma alanına bağlı değilse, silmek için aşağıdaki adımları gerçekleştirin.

# <a name="azure-portal"></a>[Azure portalı](#tab/azure-portal)

1. Adresinden Azure 'da oturum açın [https://portal.azure.com](https://portal.azure.com) .

2. Azure portal **Otomasyon hesapları**' na gidin.

3. Otomasyon hesabınızı açın ve menüden **Sil** ' i seçin.

Bilgiler doğrulanırken ve hesap silinirken, menüden seçili olan **Bildirimler** altında ilerlemeyi izleyebilirsiniz.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Bu komut, doğrulama istenmeden Otomasyon hesabını kaldırır.

```powershell
Remove-AzAutomationAccount -Name "automationAccountName" -Force -ResourceGroupName "resourceGroupName"
```

---

## <a name="delete-a-standalone-automation-account-linked-to-workspace"></a>Çalışma alanına bağlı tek başına Otomasyon hesabını silme

Otomasyon hesabınız, iş akışlarını ve iş günlüklerini toplamak için bir Log Analytics çalışma alanına bağlanmışsa, hesabı silmek için aşağıdaki adımları gerçekleştirin.

Otomasyon hesabınızdan Log Analytics çalışma alanının bağlantısını kesmek için iki seçenek vardır. Bu işlemi Otomasyon hesabından veya bağlı çalışma alanından gerçekleştirebilirsiniz.

Otomasyon Hesabınızın bağlantısını kaldırmak için aşağıdaki adımları gerçekleştirin.

1. Azure portal **Otomasyon hesapları**' na gidin.

2. Otomasyon hesabınızı açın ve sol taraftaki **Ilgili kaynaklar** altında **bağlantılı çalışma alanı** ' nı seçin.

3. **Çalışma alanının bağlantısını kaldır** sayfasında, **çalışma alanının bağlantısını kaldır**' ı seçin ve istemlere yanıtlayın.

   ![Çalışma alanının bağlantısını Kaldır sayfası](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Log Analytics çalışma alanının bağlantısını kesmeyi denediğinde, menüdeki **Bildirimler** altında ilerlemeyi izleyebilirsiniz.

Çalışma alanından bağlantısını kaldırmak için aşağıdaki adımları gerçekleştirin.

1. Azure portal, **Log Analytics çalışma alanları**' na gidin.

2. Çalışma alanından **Ilgili kaynaklar** altında **Otomasyon hesabı** ' nı seçin.

3. Otomasyon hesabı sayfasında **Hesap bağlantısını kaldır**' ı seçin ve istemlere yanıtlayın.

Otomasyon hesabının bağlantısını kaldırmayı denediğinde, ilerleme durumunu menüdeki **Bildirimler** bölümünden izleyebilirsiniz.

Otomasyon hesabının çalışma alanından başarıyla bağlantısı kaldırıldıktan sonra, hesabı silmek için [tek başına Otomasyon hesabı](#delete-a-standalone-automation-account) bölümündeki adımları gerçekleştirin.

## <a name="delete-a-shared-capability-automation-account"></a>Paylaşılan bir yetenek Otomasyonu hesabını silme

Güncelleştirme Yönetimi, Değişiklik İzleme ve envanteri ve/veya VM'leri çalışma saatleri dışında başlat/durdur desteğiyle Log Analytics çalışma alanına bağlı Otomasyon hesabınızı silmek için aşağıdaki adımları gerçekleştirin.

### <a name="step-1-delete-the-solution-from-the-linked-workspace"></a>Adım 1. Çözümü bağlantılı çalışma alanından Sil

# <a name="azure-portal"></a>[Azure portalı](#tab/azure-portal)

1. Adresinden Azure 'da oturum açın [https://portal.azure.com](https://portal.azure.com) .

2. Otomasyon hesabınıza gidin ve **ilgili kaynaklar** altında **bağlantılı çalışma alanı** ' nı seçin.

3. **Çalışma alanına git**' i seçin.

4. **Genel** altında **çözümler** ' e tıklayın.

5. Çözümler sayfasında, hesapta dağıtılan özelliklere göre aşağıdakilerden birini seçin:

    * VM'leri çalışma saatleri dışında başlat/durdur için **Başlat-Durdur-VM [çalışma alanı adı]** öğesini seçin.
    * Güncelleştirme Yönetimi için **Güncelleştirmeler (çalışma alanı adı)** öğesini seçin.
    * Değişiklik İzleme ve envanter için, **ChangeTracking (çalışma alanı adı)** öğesini seçin.

6. **Çözüm** sayfasında, menüden **Sil** ' i seçin. Yukarıda listelenen özelliklerden daha fazlası Otomasyon hesabına ve bağlantılı çalışma alanına dağıtılmışsa, devam etmeden önce her birini seçmeniz ve silmeniz gerekir.

7. Bilgiler doğrulanırken ve özellik silinirken, menüden seçili olan **Bildirimler** altında ilerlemeyi izleyebilirsiniz. Kaldırma işleminden sonra çözümler sayfasına döndürülürsünüz.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell kullanarak yüklü bir çözümü kaldırmak için [Remove-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution) cmdlet 'ini kullanın.

```powershell
Remove-AzMonitorLogAnalyticsSolution -ResourceGroupName "resourceGroupName" -Name "solutionName"
```

---

### <a name="step-2-unlink-workspace-from-automation-account"></a>Adım 2. Çalışma alanının Otomasyon hesabından bağlantısını kaldırma

Otomasyon hesabınızdan Log Analytics çalışma alanının bağlantısını kesmek için iki seçenek vardır. Bu işlemi Otomasyon hesabından veya bağlı çalışma alanından gerçekleştirebilirsiniz.

Otomasyon Hesabınızın bağlantısını kaldırmak için aşağıdaki adımları gerçekleştirin.

1. Azure portal **Otomasyon hesapları**' na gidin.

2. Otomasyon hesabınızı açın ve sol taraftaki **Ilgili kaynaklar** altında **bağlantılı çalışma alanı** ' nı seçin.

3. **Çalışma alanının bağlantısını kaldır** sayfasında, **çalışma alanının bağlantısını kaldır**' ı seçin ve istemlere yanıtlayın.

   ![Çalışma alanının bağlantısını Kaldır sayfası](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Log Analytics çalışma alanının bağlantısını kesmeyi denediğinde, menüdeki **Bildirimler** altında ilerlemeyi izleyebilirsiniz.

Çalışma alanından bağlantısını kaldırmak için aşağıdaki adımları gerçekleştirin.

1. Azure portal, **Log Analytics çalışma alanları**' na gidin.

2. Çalışma alanından **Ilgili kaynaklar** altında **Otomasyon hesabı** ' nı seçin.

3. Otomasyon hesabı sayfasında **Hesap bağlantısını kaldır**' ı seçin ve istemlere yanıtlayın.

Otomasyon hesabının bağlantısını kaldırmayı denediğinde, ilerleme durumunu menüdeki **Bildirimler** bölümünden izleyebilirsiniz.

### <a name="step-3-delete-automation-account"></a>3. Adım Otomasyon hesabını Sil

Otomasyon hesabının çalışma alanından başarıyla bağlantısı kaldırıldıktan sonra, hesabı silmek için [tek başına Otomasyon hesabı](#delete-a-standalone-automation-account) bölümündeki adımları gerçekleştirin.

## <a name="next-steps"></a>Sonraki adımlar

Azure portal bir Otomasyon hesabı oluşturmak için bkz. [tek başına Azure Otomasyonu hesabı oluşturma](automation-create-standalone-account.md). Hesabınızı bir şablon kullanarak oluşturmayı tercih ediyorsanız, bkz. [Azure Resource Manager şablonu kullanarak Otomasyon hesabı oluşturma](quickstart-create-automation-account-template.md).
