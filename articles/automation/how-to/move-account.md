---
title: Azure Otomasyonu hesabınızı başka bir aboneliğe taşıma
description: Bu makalede Otomasyon hesabınızı başka bir aboneliğe nasıl taşıyacağınız açıklanır.
services: automation
ms.subservice: process-automation
ms.date: 01/07/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1fd9350baf1805c0e6278b6210ad9818fa18c8d8
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831501"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Azure Otomasyonu hesabınızı başka bir aboneliğe taşıma

Azure Otomasyonu, bazı kaynakları yeni bir kaynak grubuna veya aboneliğe taşımanızı sağlar. Kaynakları Azure portal, PowerShell, Azure CLı veya REST API üzerinden taşıyabilirsiniz. İşlem hakkında daha fazla bilgi edinmek için bkz. [kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Otomasyon hesabı, taşıyacağınız kaynaklardan biridir. Bu makalede, Otomasyon hesaplarını başka bir kaynağa veya aboneliğe taşımayı öğreneceksiniz. Otomasyon hesabınızı taşımaya yönelik üst düzey adımlar şunlardır:

1. Özelliklerinizi devre dışı bırakın.
2. Çalışma alanınızın bağlantısını kaldırın.
3. Otomasyon hesabını taşıyın.
4. Farklı Çalıştır hesaplarını silin ve yeniden oluşturun.
5. Özelliklerinizi yeniden etkinleştirin.

## <a name="remove-features"></a>Özellikleri Kaldır

Çalışma alanınızın Otomasyon hesabınızla bağlantısını kaldırmak için, çalışma alanınızdaki Özellik kaynaklarını kaldırmanız gerekir:

- Değişiklik İzleme ve Stok
- Güncelleştirme Yönetimi
- Hizmetin kapalı olduğu saatlerde Sanal Makineleri Başlatma/Durdurma

1. Azure portalda kaynak grubunuzu bulun.
2. Her bir özelliği bulun ve **kaynakları Sil** sayfasında **Sil** ' i seçin.

    ![Azure portal Özellik kaynaklarını silme ekran görüntüsü](../media/move-account/delete-solutions.png)

İsterseniz, [Remove-AzResource](/powershell/module/Az.Resources/Remove-AzResource) cmdlet 'ini kullanarak kaynakları silebilirsiniz:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="remove-alert-rules-for-startstop-vms-during-off-hours"></a>VM'leri çalışma saatleri dışında başlat/durdur için uyarı kurallarını kaldır

VM'leri çalışma saatleri dışında başlat/durdur için, özelliği tarafından oluşturulan uyarı kurallarını da kaldırmanız gerekir.

1. Azure Portal, kaynak grubunuza gidip **izleme**  >  **uyarıları**  >  **Uyarı kurallarını yönet**' i seçin.

   ![Uyarı kurallarını yönet seçimini gösteren uyarılar sayfasının ekran görüntüsü](../media/move-account/alert-rules.png)

2. Kurallar sayfasında, bu kaynak grubunda yapılandırılmış uyarıların bir listesini görmeniz gerekir. Özelliği bu kuralları oluşturur:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. Kuralları tek seferde seçin ve **Sil** ' i seçerek kaldırın.

    ![Kurallar sayfasının ekran görüntüsü, seçili kurallar için silme onayı isteniyor](../media/move-account/delete-rules.png)

    > [!NOTE]
    > Kurallar sayfasında herhangi bir uyarı kuralı görmüyorsanız, devre dışı bırakılmış uyarıları göstermek için **durum** alanını **devre dışı** olarak değiştirin. 

4. Uyarı kurallarını kaldırdığınızda, VM'leri çalışma saatleri dışında başlat/durdur bildirimleri için oluşturulan eylem grubunu kaldırmanız gerekir. Azure Portal, **izleme**  >  **uyarıları**  >  **işlem gruplarını yönet**' i seçin.

5. **StartStop_VM_Notification** seçin. 

6. Eylem grubu sayfasında **Sil**' i seçin.

    ![Eylem Grup sayfasının ekran görüntüsü](../media/move-account/delete-action-group.png)

İsterseniz, [Remove-AzActionGroup](/powershell/module/az.monitor/remove-azactiongroup) cmdlet 'ini kullanarak eylem grubunuzu silebilirsiniz:

```azurepowershell-interactive
Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Çalışma alanınızın bağlantısını kaldırma

Artık çalışma alanınızın bağlantısını kaldırabilirsiniz:

1. Azure Portal **Otomasyon hesabı**  >  **ilişkili kaynaklar**  >  **bağlantılı çalışma alanı**' nı seçin. 

2. Çalışma alanının Otomasyon hesabından bağlantısını kaldırmak için **çalışma alanının bağlantısını kaldır** ' ı seçin.

    ![Bir Otomasyon hesabından çalışma alanının bağlantısını kaldırma ekran görüntüsü](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Otomasyon hesabınızı taşıyın

Artık Otomasyon hesabınızı ve Runbook 'larını taşıyabilirsiniz. 

1. Azure portal, Otomasyon hesabınızın kaynak grubuna gidin. **Taşımayı**  >  **başka bir aboneliğe** taşıma ' yı seçin.

    ![Kaynak grubu sayfasının ekran görüntüsü, başka bir aboneliğe taşıma](../media/move-account/move-resources.png)

2. Kaynak grubunuzda, taşımak istediğiniz kaynakları seçin. Otomasyon hesabınızı, runbook 'larını ve Log Analytics çalışma alanı kaynaklarınızı dahil edin.

## <a name="re-create-run-as-accounts"></a>Farklı Çalıştır hesaplarını yeniden oluşturma

[Farklı Çalıştır hesapları](../automation-security-overview.md#run-as-accounts) Azure kaynaklarıyla kimlik doğrulaması yapmak için Azure Active Directory bir hizmet sorumlusu oluşturur. Abonelikleri değiştirdiğinizde, Otomasyon hesabı artık mevcut farklı çalıştır hesabını kullanmaz. Farklı Çalıştır hesaplarını yeniden oluşturmak için:

1. Yeni abonelikte Otomasyon hesabınıza gidin ve **Hesap ayarları** altında **Farklı Çalıştır hesapları** ' nı seçin. Farklı Çalıştır hesaplarının şimdi tamamlanmamış olarak gösterileceğini görürsünüz.

    ![Farklı Çalıştır hesaplarının, tamamlanmamış olduğunu gösteren ekran görüntüsü](../media/move-account/run-as-accounts.png)

2. Farklı Çalıştır hesaplarını, **Özellikler** sayfasında **Sil** ' i seçerek tek seferde silin. 

    > [!NOTE]
    > Farklı Çalıştır hesaplarını oluşturma veya görüntüleme izniniz yoksa şu iletiyi görürsünüz: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` daha fazla bilgi için, [Farklı Çalıştır hesaplarını yapılandırmak Için gereken izinler](../automation-security-overview.md#permissions)bölümüne bakın.

3. Farklı Çalıştır hesaplarını sildikten sonra **Azure farklı çalıştır hesabı** altında **Oluştur** ' u seçin. 

4. Farklı Çalıştır hesabı ve hizmet sorumlusu oluşturmak için Azure farklı çalıştır hesabı Ekle sayfasında **Oluştur** ' u seçin. 

5. Azure klasik farklı çalıştır hesabı ile yukarıdaki adımları tekrarlayın.

## <a name="enable-features"></a>Özellikleri etkinleştir

Farklı Çalıştır hesaplarını yeniden oluşturduktan sonra, taşımadan önce kaldırdığınız özellikleri yeniden etkinleştirmeniz gerekir:

1. Değişiklik İzleme ve stoku açmak için Otomasyon hesabınızda **değişiklik izleme ve envanter** ' ı seçin. Üzerine taşıdığınız Log Analytics çalışma alanını seçin ve **Etkinleştir**' i seçin.

2. Güncelleştirme Yönetimi için 1. adımı yineleyin.

    ![Taşınan Otomasyon hesabınızdaki özelliklerin yeniden etkinleştirilmesi için ekran görüntüsü](../media/move-account/reenable-solutions.png)

3. Özelliklerinizi etkinleştirilmiş makineler, mevcut Log Analytics çalışma alanını bağladığınızda görülebilir. VM'leri çalışma saatleri dışında başlat/durdur özelliğini etkinleştirmek için yeniden etkinleştirmeniz gerekir. **İlgili kaynaklar**' ın altında, **VM 'leri Başlat/Durdur**' u seçin  >  **ve**  >  dağıtımı başlatmak için çözüm **oluşturma** ' yı etkinleştirin.

4. Çözüm Ekle sayfasında, Log Analytics çalışma alanınızı ve otomasyon hesabınızı seçin.

    ![Çözüm Ekle menüsünün ekran görüntüsü](../media/move-account/add-solution-vm.png)

5. Özelliği [VM'leri çalışma saatleri dışında Başlat/Durdur genel bakış](../automation-solution-vm-management.md)bölümünde açıklanan şekilde yapılandırın.

## <a name="verify-the-move"></a>Taşımayı doğrulama

Taşıma işlemi tamamlandığında, aşağıda listelenen yeteneklerin etkinleştirildiğini doğrulayın. 

|Özellik|Testler|Sorun giderme|
|---|---|---|
|Runbook'lar|Runbook, Azure kaynaklarına başarıyla çalıştırıp bağlanabilir.|[Runbook sorunlarını giderme](../troubleshoot/runbooks.md)
|Kaynak denetimi|Kaynak denetimi deponuzda el ile eşitleme gerçekleştirebilirsiniz.|[Kaynak denetimi tümleştirmesi](../source-control-integration.md)|
|Değişiklik izleme ve envanter|Makinelerinizden güncel envanter verilerini gördiğinizi doğrulayın.|[Değişiklik izleme sorunlarını giderme](../troubleshoot/change-tracking.md)|
|Güncelleştirme yönetimi|Makinelerinizi görmediğinizi ve sağlıklı olduğunu doğrulayın.</br>Bir test yazılım güncelleştirme dağıtımı çalıştırın.|[Güncelleştirme yönetimi sorunlarını giderme](../troubleshoot/update-management.md)|
|Paylaşılan kaynaklar|[Kimlik bilgileri](../shared-resources/credentials.md) ve [değişkenler](../shared-resources/variables.md)gibi tüm paylaşılan kaynaklarınızı gördiğinizi doğrulayın.|

## <a name="next-steps"></a>Sonraki adımlar

Azure 'da kaynakları taşıma hakkında bilgi edinmek için bkz. [Azure 'da kaynakları taşıma](../../azure-resource-manager/management/move-support-resources.md).
