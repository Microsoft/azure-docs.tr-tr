---
title: Windows sanal masaüstü (klasik) kiracı konak havuzu oluşturma-Azure
description: Windows sanal masaüstü (klasik) kiracı ortamının kurulumu sırasında kiracı ve konak havuzu sorunlarını giderme ve çözme.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 12be4611f0583c6da630b9e5a0e4b5a82ebfa650
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444284"
---
# <a name="tenant-and-host-pool-creation-in-windows-virtual-desktop-classic"></a>Windows sanal masaüstünde kiracı ve konak havuzu oluşturma (klasik)

>[!IMPORTANT]
>Bu içerik, Windows sanal masaüstü nesneleri Azure Resource Manager desteklemeyen Windows sanal masaüstü (klasik) için geçerlidir. Azure Resource Manager Windows sanal masaüstü nesnelerini yönetmeye çalışıyorsanız, [Bu makaleye](../troubleshoot-set-up-issues.md)bakın.

Bu makalede, Windows sanal masaüstü kiracısı ve ilgili oturum ana bilgisayar havuzu altyapısının ilk kurulumu sırasında sorunlar ele alınmaktadır.

## <a name="provide-feedback"></a>Geribildirim gönderme

Windows Sanal Masaüstü hizmetini ürün ekibi ve etkin topluluk üyeleriyle tartışmak için [Windows sanal masaüstü teknoloji Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) 'yi ziyaret edin.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Windows 10 Enterprise çoklu oturum görüntüsünü edinme

Windows 10 Enterprise çoklu oturum görüntüsünü kullanmak için Azure Marketi ' ne gidin,   >  [sanal masaüstleri, sürüm 1809 için](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice)başlangıç **Microsoft Windows 10** > ve Windows 10 Enterprise ' ı seçin.

> [!div class="mx-imgBorder"]
> ![Sanal masaüstleri için Windows 10 Enterprise, sürüm 1809 ' ı seçme ekran görüntüsü.](../media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant&quot;></a>Windows sanal masaüstü kiracısı oluşturuluyor

Bu bölümde, Windows sanal masaüstü kiracısı oluşturulurken olası sorunlar ele alınmaktadır.

### <a name=&quot;error-aadsts650052-the-app-needs-access-to-a-service&quot;></a>Hata: AADSTS650052 uygulamanın bir hizmete erişmesi gerekiyor.

Ham hata örneği:

```Error
AADSTS650052 Message The app needs access to a service(\"{name}\") that your organization
\"{organization}\" has not subscribed to or enabled. Contact your IT Admin to review the
configuration of your service subscriptions.650052 Message The app needs access to a service
(\"{name}\") that your organization \"{organization}\" has not subscribed to or enabled.
Contact your IT Admin to review the configuration of your service subscriptions.
```

**Neden:** Azure Active Directory örneğinde Windows sanal masaüstüne izin verilmedi.

**Çözüm:** izin vermek için [Bu kılavuzu izleyin](./tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) .

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Hata: Kullanıcının yönetim hizmetini sorgulama yetkisi yok

> [!div class="mx-imgBorder"]
> ![Kullanıcının yönetim hizmetini sorgulama yetkisine sahip olmadığı PowerShell penceresinin ekran görüntüsü.](../media/UserNotAuthorizedNewTenant.png)

Ham hata örneği:

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

**Neden:** Oturum açmış olan kullanıcıya Azure Active Directory, TenantCreator rolü atanmaz.

**Çözüm:** [Azure Active Directory kiracınızdaki bir kullanıcıya TenantCreator uygulama rolünü atama](tenant-setup-azure-active-directory.md#assign-the-tenantcreator-application-role)bölümündeki yönergeleri izleyin. Yönergeleri tamamladıktan sonra, TenantCreator rolüne atanan bir Kullanıcı olacaktır.

> [!div class="mx-imgBorder"]
> ![Atanan TenantCreator rolü ekran görüntüsü.](../media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Windows sanal masaüstü oturumu ana bilgisayarı VM 'Leri oluşturma

Oturum Ana bilgisayar VM 'Leri çeşitli yollarla oluşturulabilir, ancak Windows sanal masaüstü ekibi yalnızca [Azure Marketi](https://azuremarketplace.microsoft.com/) TEKLIFIYLE ilgili VM sağlama sorunlarını destekler. Daha fazla bilgi için bkz. [Windows sanal masaüstü 'nü kullanan sorunlar-bir konak havuzu sağlama Azure Marketi teklifi](#issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering).

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Windows sanal masaüstü ile ilgili sorunlar – bir konak havuzu sağlama Azure Marketi teklifi

Windows sanal masaüstü – Azure Marketi 'nden bir konak havuzu şablonu sağlayın.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Hata: GitHub 'dan bağlantı kullanılırken, "ücretsiz hesap oluştur" iletisi görüntülenir

> [!div class="mx-imgBorder"]
> ![Ücretsiz bir hesap oluşturmak için ekran görüntüsü.](../media/be615904ace9832754f0669de28abd94.png)

**Neden 1:** Hesapta Azure 'da oturum açmak için kullanılan etkin abonelikler yok veya kullanılan hesabın abonelikleri görüntüleme izni yok.

**1. Çözüm:** Katılımcı erişimi olan bir hesapla (en azından) oturum ana bilgisayar VM 'lerinin dağıtılacağı aboneliğe oturum açın.

**Neden 2:** Kullanılan abonelik Microsoft Bulut hizmet sağlayıcısı (CSP) kiracısının bir parçasıdır.

**2. Çözüm:** **Yeni Windows sanal masaüstü konak havuzu oluşturma ve sağlama** için GitHub konumuna gidin ve şu yönergeleri izleyin:

1. **Azure 'A dağıt** ' a sağ tıklayın ve **bağlantı adresini kopyala**' yı seçin.
2. **Not defteri 'ni** açın ve bağlantıyı yapıştırın.
3. # Karakterinden önce, CSP son müşteri kiracı adını ekleyin.
4. Yeni bağlantıyı bir tarayıcıda açın ve Azure portal şablonu yükler.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

### <a name="error-you-receive-template-deployment-is-not-valid-error"></a>Hata: "şablon dağıtımı geçerli değil" hatası alıyorsunuz

> [!div class="mx-imgBorder"]
> !["Şablon dağıtımı... ekran görüntüsü geçerli değil "hatası](../media/troubleshooting-marketplace-validation-error-generic.png)

Belirli bir eylem yapmadan önce, başarısız dağıtım doğrulamasına ilişkin ayrıntılı hatayı görmek için etkinlik günlüğünü denetlemeniz gerekir.

Etkinlik günlüğündeki hatayı görüntülemek için:

1. Geçerli Azure Marketi dağıtım sunumndan çıkın.
2. Üst arama çubuğunda **etkinlik günlüğü**' nü arayıp seçin.
3. Durumu **başarısız** olan **dağıtımı doğrula** adlı bir etkinlik bulun ve etkinliği seçin.

   > [!div class="mx-imgBorder"]
   > ![* * Başarısız * * durumundaki tek * * dağıtımı doğrula * * etkinliğinin ekran görüntüsü](../media/troubleshooting-marketplace-validation-error-activity-summary.png)

4. JSON ' ı seçin ve ardından "statusMessage" alanını görene kadar ekranın alt kısmına gidin.

   > [!div class="mx-imgBorder"]
   > ![JSON metninin statusMessage özelliği etrafında kırmızı bir kutu ile başarısız etkinliğin ekran görüntüsü.](../media/troubleshooting-marketplace-validation-error-json-boxed.png)

İşlem şablonunuz kota sınırının üzerine gittiğinde, bunu onarmak için aşağıdaki işlemlerden birini yapabilirsiniz:

 - Azure Marketi 'ni ilk kez kullandığınız parametrelerle çalıştırın, ancak bu kez daha az VM ve VM çekirdeği kullanır.
 - Belirtilen VM SKU 'SU için Azure aboneliğinizin kotasını artırma isteği göndermek üzere bir tarayıcıda **StatusMessage** alanında gördüğünüz bağlantıyı açın.

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Şablon ve PowerShell Istenen durum yapılandırması (DSC) hatalarını Azure Resource Manager

Azure Resource Manager şablonlarının ve PowerShell DSC 'nin başarısız dağıtımlarıyla ilgili sorunları gidermek için bu yönergeleri izleyin.

1. [Azure Resource Manager ile dağıtım Işlemlerini görüntüleme](../../azure-resource-manager/templates/deployment-history.md)kullanarak dağıtımdaki hataları gözden geçirin.
2. Dağıtımda hata yoksa, [kaynaklardaki eylemleri denetlemek için etkinlik günlüklerini görüntüle](../../azure-resource-manager/management/view-activity-logs.md)' yi kullanarak etkinlik günlüğündeki hataları gözden geçirin.
3. Hata tanımlandıktan sonra, sorunu gidermek için [Azure Resource Manager ile yaygın Azure dağıtım hatalarını giderme](../../azure-resource-manager/templates/common-deployment-errors.md) bölümündeki hata iletisini ve kaynakları kullanın.
4. Önceki dağıtım sırasında oluşturulan tüm kaynakları silin ve şablonu yeniden dağıtma işlemini yeniden deneyin.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Hata: dağıtımınız başarısız oldu.... \<hostname> /JoinDomain

> [!div class="mx-imgBorder"]
> ![Dağıtımınız ekran görüntüsünde başarısız oldu.](../media/e72df4d5c05d390620e07f0d7328d50f.png)

Ham hata örneği:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Neden 1:** VM 'Lerin etki alanına katılması için girilen kimlik bilgileri yanlış.

**1. Çözüm:** VM 'Ler için "yanlış kimlik bilgileri" hatası, [Oturum Ana BILGISAYAR VM yapılandırmasındaki](troubleshoot-vm-configuration-2019.md)etki alanına katılmaz.

**Neden 2:** Etki alanı adı çözümlenmiyor.

**2. Çözüm:** Bkz. [hata: etki alanı adı](troubleshoot-vm-configuration-2019.md#error-domain-name-doesnt-resolve) , [oturum ana makinesi yapılandırmasında](troubleshoot-vm-configuration-2019.md)çözümlenmiyor.

**Neden 3:** Sanal ağ (VNET) DNS yapılandırmanız **varsayılan** olarak ayarlanır.

Bunu yapmak için aşağıdaki işlemleri yapın:

1. Azure portalını açın ve **sanal ağlar** sekmesine gidin.
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
> ![Dağıtımınızın ekran görüntüsü, Terminal sağlama durumuyla başarısız oldu.](../media/7aaf15615309c18a984673be73ac969a.png)

**Neden 1:** Windows sanal masaüstü ortamında geçici bir hata oluştu.

**Neden 2:** Bağlantıyla geçici hata.

**Çözüm:** PowerShell kullanarak oturum açarak Windows sanal masaüstü ortamının sağlıklı olduğunu onaylayın. [PowerShell ile bir konak havuzu oluşturma](create-host-pools-powershell-2019.md)bölümünde VM kaydını el ile tamamlama.

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Hata: Belirtilen Yönetici Kullanıcı Adına izin verilmiyor

> [!div class="mx-imgBorder"]
> ![Belirtilen bir yöneticiye izin verilmediğinden dağıtımınızın ekran görüntüsü başarısız oldu.](../media/f2b3d3700e9517463ef88fa41875bac9.png)

Ham hata örneği:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Neden:** Girilen parola yasak alt dizeler (yönetici, yönetici, kök) içeriyor.

**Çözüm:** Kullanıcı adını güncelleştirin veya farklı kullanıcılar kullanın.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Hata: VM uzantıyı işlerken bir hata bildirdi

> [!div class="mx-imgBorder"]
> ![Dağıtımınızda Terminal sağlama durumu ile tamamlanan kaynak işleminin ekran görüntüsü başarısız oldu.](../media/49c4a1836a55d91cd65125cf227f411f.png)

Ham hata örneği:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-provision-host-pool-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'.
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message:
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource":
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

**Neden:** PowerShell DSC Uzantısı VM 'de yönetici erişimini alamadı.

**Çözüm:** Kullanıcı adının ve parolanın sanal makinede yönetici erişimine sahip olduğunu onaylayın ve Azure Resource Manager şablonunu yeniden çalıştırın.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Hata: DeploymentFailed – PowerShell DSC yapılandırması ' FirstSessionHost ' hatalarla tamamlandı

> [!div class="mx-imgBorder"]
> ![' FirstSessionHost ' PowerShell DSC yapılandırması başarısız olan dağıtım ekran görüntüsü hata (ler) ile tamamlandı.](../media/64870370bcbe1286906f34cf0a8646ab.png)

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

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Hata: Kullanıcının yönetim hizmetini sorgulama yetkisi yok

Ham hata örneği:

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00",
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s).
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

**Neden:** Belirtilen Windows sanal masaüstü kiracı yöneticisinin geçerli bir rol ataması yok.

**Çözüm:** Windows sanal masaüstü kiracısını oluşturan kullanıcının Windows sanal masaüstü PowerShell 'de oturum açması ve denenen kullanıcıya bir rol ataması atamasını sağlaması gerekir. GitHub Azure Resource Manager şablonu parametrelerini çalıştırıyorsanız, PowerShell komutlarını kullanarak şu yönergeleri izleyin:

```PowerShell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName "RDS Contributor" -SignInName <UPN>
```

### <a name="error-user-requires-azure-ad-multi-factor-authentication-mfa"></a>Hata: Kullanıcı Azure AD Multi-Factor Authentication gerektiriyor (MFA)

> [!div class="mx-imgBorder"]
> ![Multi-Factor Authentication olmaması nedeniyle dağıtımınızın ekran görüntüsü başarısız oldu (MFA)](../media/MFARequiredError.png)

Ham hata örneği:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Neden:** Belirtilen Windows sanal masaüstü kiracı yöneticisi, oturum açmak için Azure AD Multi-Factor Authentication (MFA) gerektirir.

**Çözüm:** [Öğretici: PowerShell ile hizmet sorumluları ve rol atamaları oluşturma](create-service-principal-role-powershell.md)' daki adımları izleyerek bir hizmet sorumlusu oluşturun ve Windows sanal masaüstü kiracınız için bir rol atayın. Hizmet sorumlusu ile Windows sanal masaüstü 'nde oturum açadığınızı doğruladıktan sonra, kullandığınız yönteme bağlı olarak Azure Marketi teklifini veya GitHub Azure Resource Manager şablonunu yeniden çalıştırın. Yönteminizin doğru parametrelerini girmek için aşağıdaki yönergeleri izleyin.

Azure Marketi teklifini çalıştırıyorsanız, Windows sanal masaüstü 'nde düzgün şekilde kimlik doğrulaması yapmak için aşağıdaki parametrelerin değerlerini sağlayın:

- Windows sanal masaüstü kiracı RDS sahibi: hizmet sorumlusu
- Uygulama KIMLIĞI: oluşturduğunuz yeni hizmet sorumlusunun uygulama kimliği
- Parola/parolayı onayla: hizmet sorumlusu için oluşturduğunuz parola parolası
- Azure AD kiracı KIMLIĞI: oluşturduğunuz hizmet sorumlusunun Azure AD kiracı KIMLIĞI

GitHub Azure Resource Manager şablonu çalıştırıyorsanız, Windows sanal masaüstü 'nde düzgün şekilde kimlik doğrulaması yapmak için aşağıdaki parametrelerin değerlerini sağlayın:

- Kiracı Yöneticisi Kullanıcı asıl adı (UPN) veya uygulama KIMLIĞI: oluşturduğunuz yeni hizmet sorumlusunun uygulama kimliği
- Kiracı Yönetici parolası: hizmet sorumlusu için oluşturduğunuz parola parolası
- Isserviceprincipal: **true**
- Aadtenantıd: oluşturduğunuz hizmet sorumlusunun Azure AD kiracı KIMLIĞI

### <a name="error-vmsubnet-not-available-when-configuring-virtual-networks"></a>Hata: sanal ağları yapılandırırken vmSubnet kullanılamaz

**Neden:** Windows sanal masaüstü marketi şablonunda, Kullanıcı arabirimi yalnızca şablonda belirtilen toplam VM sayısı kadar en az sayıda IP adresi bulunan alt ağları görüntüler. Alt ağdaki kullanılabilir IP adreslerinin gerçek sayısının yalnızca dağıtılan yeni VM sayısına eşit olması gerekir, ancak bu geçerli kullanıcı arabirimi tarafından hesaplanamaz.

**Çözüm:** Market Kullanıcı arabirimi kullanılarak eklenmekte olan VM sayısı kadar en az sayıda IP adresi bulunan bir alt ağ belirtebilirsiniz; Bu, [mevcut bir dağıtımı](expand-existing-host-pool-2019.md#redeploy-from-azure) yeniden [dağıtırken veya GitHub 'DAN temel alınan ARM şablonunu kullanarak dağıttığınızda](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool), "**existingsubnetname**" parametresindeki alt ağ adı belirtilerek yapılabilir.

## <a name="next-steps"></a>Sonraki adımlar

- Windows sanal masaüstü ve yükseltme izlemelerinin sorunlarını giderme hakkında genel bilgi için bkz. [sorun giderme genel bakış, geri bildirim ve destek](troubleshoot-set-up-overview-2019.md).
- Windows sanal masaüstündeki bir sanal makineyi (VM) yapılandırırken oluşan sorunları gidermek için bkz. [oturum ana bilgisayarı sanal makine yapılandırması](troubleshoot-vm-configuration-2019.md).
- Windows sanal masaüstü istemci bağlantılarıyla ilgili sorunları gidermek için bkz. [Windows sanal masaüstü hizmeti bağlantıları](troubleshoot-service-connection-2019.md).
- Uzak Masaüstü istemcileriyle ilgili sorunları gidermek için bkz [. uzak masaüstü Istemcisinde sorun giderme](../troubleshoot-client.md)
- Windows sanal masaüstü ile PowerShell kullanırken karşılaşılan sorunları gidermek için bkz. [Windows sanal masaüstü PowerShell](troubleshoot-powershell-2019.md).
- Hizmet hakkında daha fazla bilgi edinmek için bkz. [Windows sanal masaüstü ortamı](environment-setup-2019.md).
- Sorun giderme öğreticisini öğrenmek için bkz. [öğretici: Kaynak Yöneticisi şablonu dağıtımlarının sorunlarını giderme](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Denetim eylemleri hakkında bilgi edinmek için bkz. [Kaynak Yöneticisi Ile denetim işlemleri](../../azure-resource-manager/management/view-activity-logs.md).
- Dağıtım sırasında hataları belirleme eylemleri hakkında bilgi edinmek için bkz. [dağıtım Işlemlerini görüntüleme](../../azure-resource-manager/templates/deployment-history.md).