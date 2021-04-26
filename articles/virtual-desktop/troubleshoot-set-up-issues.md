---
title: Windows sanal masaüstü ortamı konak havuzu oluşturma-Azure
description: Windows sanal masaüstü ortamının kurulumu sırasında kiracı ve konak havuzu sorunlarını giderme ve çözme.
author: Heidilohr
ms.topic: troubleshooting
ms.custom: references_regions
ms.date: 02/17/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 4d515e75aba9b3912507da07267c3dcb31f1ee34
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445508"
---
# <a name="host-pool-creation"></a>Konak havuzu oluşturma

>[!IMPORTANT]
>Bu içerik Azure Resource Manager Windows sanal masaüstü nesneleri ile Windows sanal masaüstü için geçerlidir. Azure Resource Manager nesneleri olmadan Windows sanal masaüstü (klasik) kullanıyorsanız, [Bu makaleye](./virtual-desktop-fall-2019/troubleshoot-set-up-issues-2019.md)bakın.

Bu makalede, Windows sanal masaüstü kiracısı ve ilgili oturum ana bilgisayar havuzu altyapısının ilk kurulumu sırasında sorunlar ele alınmaktadır.

## <a name="provide-feedback"></a>Geribildirim gönderme

Windows Sanal Masaüstü hizmetini ürün ekibi ve etkin topluluk üyeleriyle tartışmak için [Windows sanal masaüstü teknoloji Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) 'yi ziyaret edin.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Windows 10 Enterprise çoklu oturum görüntüsünü edinme

Windows 10 Enterprise çoklu oturum görüntüsünü kullanmak için Azure Marketi ' ne gidin, **kullanmaya başlama**  >  **Microsoft Windows 10** > ve [Windows 10 Enterprise multi-session, sürüm 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice)' i seçin.

## <a name="issues-with-using-the-azure-portal-to-create-host-pools"></a>Konak havuzları oluşturmak için Azure portal kullanma sorunları

### <a name="error-create-a-free-account-appears-when-accessing-the-service"></a>Hata: hizmete erişirken "ücretsiz hesap oluşturma" görüntülenir

> [!div class="mx-imgBorder"]
> !["Ücretsiz hesap oluşturma" iletisinin gösterildiği Azure portal gösteren resim](media/create-new-account.png)

**Neden**: Azure 'da oturum açtığınız hesapta etkin abonelikler yok veya hesabın abonelikleri görüntüleme izni yok.

**Çözüm**: en az katkıda bulunan düzeyinde erişimi olan bir hesapla oturum ana bilgisayarı sanal makinelerini (VM 'ler) dağıtacağınız abonelikte oturum açın.

### <a name="error-exceeding-quota-limit"></a>Hata: "kota sınırını aşılıyor"

İşlem, kota sınırının üzerine gittiğinde, aşağıdakilerden birini yapabilirsiniz:

- Aynı parametrelerle, ancak daha az VM ve VM çekirdeğine sahip yeni bir konak havuzu oluşturun.

- Belirtilen VM SKU 'SU için Azure aboneliğinizin kotasını artırma isteği göndermek üzere bir tarayıcıda statusMessage alanında gördüğünüz bağlantıyı açın.

### <a name="error-cant-see-user-assignments-in-app-groups"></a>Hata: uygulama gruplarında Kullanıcı atamaları göremez.

**Neden**: Bu hata genellikle aboneliği 1 Azure ACTIVE DIRECTORY (ad) kiracısından diğerine taşıdıktan sonra oluşur. Eski atamalarınız hala eski Azure AD kiracısına bağlıysa, Azure portal bunların izini kaybeder.

**Çözüm**: kullanıcıları uygulama gruplarına yeniden atamanız gerekir.

### <a name="i-only-see-us-when-setting-the-location-for-my-service-objects"></a>Yalnızca hizmet nesnelerim için konum ayarlarken bızı görüyorum

**Neden**: Azure Şu anda Windows sanal masaüstü hizmeti için bu bölgeyi desteklemiyor. Hangi geographilerin destekleyeceğinizi öğrenmek için [veri konumlarına](data-locations.md)göz atın. Windows sanal masaüstü konumu destekliyorsa, ancak bir konum seçerken hala görünmezse, bu, kaynak sağlayıcınız henüz güncelleştirilmemiş anlamına gelir.

**Çözüm**: bölgelerin en son listesini almak için kaynak sağlayıcısını yeniden kaydedin:

1. **Abonelikler** ' e gidin ve ilgili aboneliği seçin.
2. **Kaynak sağlayıcısı**' nı seçin.
3. **Microsoft. DesktopVirtualization**' ı seçin ve ardından Eylem menüsünden **yeniden kaydet** ' i seçin.

Kaynak sağlayıcısını yeniden kaydettiğinizde, belirli bir UI geri bildirimi veya güncelleştirme durumu görmezsiniz. Yeniden kayıt işlemi de mevcut ortamlarınızla karışmaz.

## <a name="azure-resource-manager-template-errors"></a>Azure Resource Manager şablonu hataları

Azure Resource Manager şablonlarının ve PowerShell DSC 'nin başarısız dağıtımlarıyla ilgili sorunları gidermek için bu yönergeleri izleyin.

1. [Azure Resource Manager ile dağıtım Işlemlerini görüntüleme](../azure-resource-manager/templates/deployment-history.md)kullanarak dağıtımdaki hataları gözden geçirin.
2. Dağıtımda hata yoksa, [kaynaklardaki eylemleri denetlemek için etkinlik günlüklerini görüntüle](../azure-resource-manager/management/view-activity-logs.md)' yi kullanarak etkinlik günlüğündeki hataları gözden geçirin.
3. Hata tanımlandıktan sonra, sorunu gidermek için [Azure Resource Manager ile yaygın Azure dağıtım hatalarını giderme](../azure-resource-manager/templates/common-deployment-errors.md) bölümündeki hata iletisini ve kaynakları kullanın.
4. Önceki dağıtım sırasında oluşturulan tüm kaynakları silin ve şablonu yeniden dağıtma işlemini yeniden deneyin.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Hata: dağıtımınız başarısız oldu.... \<hostname> /JoinDomain

> [!div class="mx-imgBorder"]
> ![Dağıtımınız ekran görüntüsünde başarısız oldu.](media/failure-joindomain.png)

Ham hata örneği:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Neden 1:** VM 'Lerin etki alanına katılması için girilen kimlik bilgileri yanlış.

**1. Çözüm:** VM 'Ler için "yanlış kimlik bilgileri" hatası, [Oturum Ana BILGISAYAR VM yapılandırmasındaki](troubleshoot-vm-configuration.md)etki alanına katılmaz.

**Neden 2:** Etki alanı adı çözümlenmiyor.

**2. Çözüm:** Bkz. [hata: etki alanı adı](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve) , [oturum ana makinesi yapılandırmasında](troubleshoot-vm-configuration.md)çözümlenmiyor.

**Neden 3:** Sanal ağ (VNET) DNS yapılandırmanız **varsayılan** olarak ayarlanır.

Bunu yapmak için aşağıdaki işlemleri yapın:

1. Azure portal açın ve **sanal ağlar** sekmesine gidin.
2. VNET 'nizi bulun ve **DNS sunucuları**' nı seçin.
3. Ekranınızın sağ tarafında DNS sunucuları menüsü görünmelidir. Bu menüde **özel**' i seçin.
4. Özel altında listelenen DNS sunucularının etki alanı denetleyicinize veya Active Directory etki alanına göre eşleştiğinden emin olun. DNS sunucunuzu görmüyorsanız, değerini **DNS sunucusu Ekle** alanına girerek ekleyebilirsiniz.

### <a name="error-your-deployment-failedunauthorized"></a>Hata: Dağıtımınız başarısız oldu...\Unauthorized

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Neden:** Kullanmakta olduğunuz abonelik, müşterinin dağıtmaya çalıştığı bölgedeki gerekli özelliklere erişemiyoruz. Örneğin, MSDN, ücretsiz veya eğitim abonelikleri bu hatayı gösterebilir.

**Çözüm:** Abonelik türü veya bölgenizi, gerekli özelliklere erişebilecek bir şekilde değiştirin.

### <a name="error-vmextensionprovisioningerror"></a>Hata: VMExtensionProvisioningError

> [!div class="mx-imgBorder"]
> ![Dağıtımınızın ekran görüntüsü, Terminal sağlama durumuyla başarısız oldu.](media/failure-vmextensionprovisioning.png)

**Neden 1:** Windows sanal masaüstü ortamında geçici bir hata oluştu.

**Neden 2:** Bağlantıyla geçici hata.

**Çözüm:** PowerShell kullanarak oturum açarak Windows sanal masaüstü ortamının sağlıklı olduğunu onaylayın. [PowerShell ile bir konak havuzu oluşturma](create-host-pools-powershell.md)bölümünde VM kaydını el ile tamamlama.

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Hata: Belirtilen Yönetici Kullanıcı Adına izin verilmiyor

> [!div class="mx-imgBorder"]
> ![Belirtilen bir yöneticiye izin verilmediğinden dağıtımınızın ekran görüntüsü başarısız oldu.](media/failure-username.png)

Ham hata örneği:

```Error
 { …{ "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } … }
```

**Neden:** Girilen parola yasak alt dizeler (yönetici, yönetici, kök) içeriyor.

**Çözüm:** Kullanıcı adını güncelleştirin veya farklı kullanıcılar kullanın.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Hata: VM uzantıyı işlerken bir hata bildirdi

> [!div class="mx-imgBorder"]
> ![Dağıtımınızda Terminal sağlama durumu ile tamamlanan kaynak işleminin ekran görüntüsü başarısız oldu.](media/failure-processing.png)

Ham hata örneği:

```Error
{ … "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'.
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message:
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] … }
```

**Neden:** PowerShell DSC Uzantısı VM 'de yönetici erişimini alamadı.

**Çözüm:** Kullanıcı adının ve parolanın sanal makinede yönetici erişimine sahip olduğunu onaylayın ve Azure Resource Manager şablonunu yeniden çalıştırın.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Hata: DeploymentFailed – PowerShell DSC yapılandırması ' FirstSessionHost ' hatalarla tamamlandı

> [!div class="mx-imgBorder"]
> ![' FirstSessionHost ' PowerShell DSC yapılandırması başarısız olan dağıtım ekran görüntüsü hata (ler) ile tamamlandı.](media/failure-dsc.png)

Ham hata örneği:

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**Neden:** PowerShell DSC Uzantısı VM 'de yönetici erişimini alamadı.

**Çözüm:** Belirtilen Kullanıcı adı ve parolanın sanal makinede yönetim erişimi olduğundan emin olun ve Azure Resource Manager şablonunu yeniden çalıştırın.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Hata: DeploymentFailed – InvalidResourceReference

Ham hata örneği:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Neden:** Kaynak grubu adının bir kısmı, şablon tarafından oluşturulan belirli kaynaklar için kullanılır. Mevcut kaynaklarla eşleşen ad nedeniyle, şablon farklı bir gruptan var olan bir kaynağı seçebilir.

**Çözüm:** Oturum Ana bilgisayar sanal makinelerini dağıtmak için Azure Resource Manager şablonunu çalıştırırken, abonelik kaynak grubu adınız için ilk iki karakteri benzersiz yapın.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Hata: DeploymentFailed – InvalidResourceReference

Ham hata örneği:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**Neden:** Bu hata, Azure Resource Manager şablonuyla oluşturulan NIC 'in zaten VNET 'te bulunan başka bir NIC ile aynı ada sahip olmasından kaynaklanır.

**Çözüm:** Farklı bir ana bilgisayar öneki kullanın.

### <a name="error-deploymentfailed--error-downloading"></a>Hata: DeploymentFailed – indirme hatası

Ham hata örneği:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-provision-host-pool-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-prod
/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Neden:** Bu hata, bir statik yol, güvenlik duvarı kuralı veya NSG 'nin Azure Resource Manager şablonuna bağlı ZIP dosyasının indirilmesini engellediğini nedeniyle oluşur.

**Çözüm:** Statik yolu, güvenlik duvarı kuralını veya NSG 'yi engellemeyi kaldırın. İsteğe bağlı olarak, Azure Resource Manager şablonu json dosyasını bir metin düzenleyicisinde açın, ZIP dosyasına bağlantıyı alın ve kaynağı izin verilen bir konuma indirin.

### <a name="error-cant-delete-a-session-host-from-the-host-pool-after-deleting-the-vm"></a>Hata: VM silindikten sonra konak havuzundan bir oturum ana bilgisayarı silinemiyor

**Neden:** VM 'yi silmeden önce oturum konağını silmeniz gerekir.

**Çözüm:** Oturum konağını boşaltma moduna alın, oturum ana bilgisayarındaki tüm kullanıcıların oturumunu kapatın ve ardından Konağı silin.

## <a name="next-steps"></a>Sonraki adımlar

- Windows sanal masaüstü ve yükseltme izlemelerinin sorunlarını giderme hakkında genel bilgi için bkz. [sorun giderme genel bakış, geri bildirim ve destek](troubleshoot-set-up-overview.md).
- Windows sanal masaüstündeki bir sanal makineyi (VM) yapılandırırken oluşan sorunları gidermek için bkz. [oturum ana bilgisayarı sanal makine yapılandırması](troubleshoot-vm-configuration.md).
- Windows sanal masaüstü Aracısı veya oturum bağlantısıyla ilgili sorunları gidermek için bkz. [Genel Windows sanal masaüstü Aracısı sorunlarını giderme](troubleshoot-agent.md).
- Windows sanal masaüstü istemci bağlantılarıyla ilgili sorunları gidermek için bkz. [Windows sanal masaüstü hizmeti bağlantıları](troubleshoot-service-connection.md).
- Uzak Masaüstü istemcileriyle ilgili sorunları gidermek için bkz [. uzak masaüstü Istemcisinde sorun giderme](troubleshoot-client.md)
- Windows sanal masaüstü ile PowerShell kullanırken karşılaşılan sorunları gidermek için bkz. [Windows sanal masaüstü PowerShell](troubleshoot-powershell.md).
- Hizmet hakkında daha fazla bilgi edinmek için bkz. [Windows sanal masaüstü ortamı](environment-setup.md).
- Sorun giderme öğreticisini öğrenmek için bkz. [öğretici: Kaynak Yöneticisi şablonu dağıtımlarının sorunlarını giderme](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Denetim eylemleri hakkında bilgi edinmek için bkz. [Kaynak Yöneticisi Ile denetim işlemleri](../azure-resource-manager/management/view-activity-logs.md).
- Dağıtım sırasında hataları belirleme eylemleri hakkında bilgi edinmek için bkz. [dağıtım Işlemlerini görüntüleme](../azure-resource-manager/templates/deployment-history.md).
