---
title: Windows sanal masaüstü kiracı konak havuzu oluşturma-Azure
description: Windows sanal masaüstü kiracı ortamının kurulumu sırasında kiracı ve konak havuzu sorunlarını giderme ve çözme.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: helohr
ms.openlocfilehash: b53bf80774a0715c7a02d837975284e985958635
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607427"
---
# <a name="tenant-and-host-pool-creation"></a>Kiracı ve ana bilgisayar havuzu oluşturma

Bu makalede, Windows sanal masaüstü kiracısı ve ilgili oturum ana bilgisayar havuzu altyapısının ilk kurulumu sırasında sorunlar ele alınmaktadır.

## <a name="provide-feedback"></a>Geri bildirimde bulunma

Windows Sanal Masaüstü hizmetini ürün ekibi ve etkin topluluk üyeleriyle tartışmak için [Windows sanal masaüstü teknoloji Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) 'yi ziyaret edin.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Windows 10 Enterprise çoklu oturum görüntüsünü edinme

Windows 10 Enterprise çoklu oturum görüntüsünü kullanmak için Azure Marketi ' ne gidin, **Microsoft Windows 10** > ve [Windows 10 Enterprise for Virtual masaüstleri, sürüm 1809 Için](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice) > **kullanmaya başlayın** ' ı seçin.

![Sanal masaüstleri için Windows 10 Enterprise, sürüm 1809 ' ı seçme ekran görüntüsü.](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Windows sanal masaüstü kiracısı oluşturuluyor

Bu bölümde, Windows sanal masaüstü kiracısı oluşturulurken olası sorunlar ele alınmaktadır.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Hata: Kullanıcı, Yönetim hizmetini sorgulama yetkisine sahip değil

![Kullanıcının yönetim hizmetini sorgulama yetkisine sahip olmadığı PowerShell penceresinin ekran görüntüsü.](media/UserNotAuthorizedNewTenant.png)

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

**Çözüm:** [Azure Active Directory kiracınızdaki bir kullanıcıya TenantCreator uygulama rolünü atama](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory#assign-the-tenantcreator-application-role)bölümündeki yönergeleri izleyin. Yönergeleri tamamladıktan sonra, TenantCreator rolüne atanan bir Kullanıcı olacaktır.

![Atanan TenantCreator rolü ekran görüntüsü.](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Windows sanal masaüstü oturumu ana bilgisayarı VM 'Leri oluşturma

Oturum Ana bilgisayar VM 'Leri çeşitli yollarla oluşturulabilir, ancak Uzak Masaüstü Hizmetleri/Windows sanal masaüstü ekipleri yalnızca Azure Resource Manager şablonuyla ilgili VM sağlama sorunlarını destekler. Azure Resource Manager şablonu [Azure Marketi](https://azuremarketplace.microsoft.com/) ve [GitHub](https://github.com/)' da kullanılabilir.

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Windows sanal masaüstü ile ilgili sorunlar – bir konak havuzu sağlama Azure Marketi teklifi

Windows sanal masaüstü – Azure Marketi 'nden bir konak havuzu şablonu sağlayın.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Hata: GitHub 'dan bağlantı kullanılırken, "ücretsiz hesap oluştur" iletisi görüntülenir

![Ücretsiz bir hesap oluşturmak için ekran görüntüsü.](media/be615904ace9832754f0669de28abd94.png)

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

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Şablon ve PowerShell Istenen durum yapılandırması (DSC) hatalarını Azure Resource Manager

Azure Resource Manager şablonlarının ve PowerShell DSC 'nin başarısız dağıtımlarıyla ilgili sorunları gidermek için bu yönergeleri izleyin.

1. [Azure Resource Manager ile dağıtım Işlemlerini görüntüleme](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)kullanarak dağıtımdaki hataları gözden geçirin.
2. Dağıtımda hata yoksa, [kaynaklardaki eylemleri denetlemek için etkinlik günlüklerini görüntüle](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)' yi kullanarak etkinlik günlüğündeki hataları gözden geçirin.
3. Hata tanımlandıktan sonra, sorunu gidermek için [Azure Resource Manager ile yaygın Azure dağıtım hatalarını giderme](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors) bölümündeki hata iletisini ve kaynakları kullanın.
4. Önceki dağıtım sırasında oluşturulan tüm kaynakları silin ve şablonu yeniden dağıtma işlemini yeniden deneyin.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Hata: dağıtımınız başarısız oldu....\<ana bilgisayar adı >/JoinDomain

![Dağıtımınız ekran görüntüsünde başarısız oldu.](media/e72df4d5c05d390620e07f0d7328d50f.png)

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

**2. Çözüm:** VM 'Ler için "etki alanı adı çözümlenmiyor" hatası, [Oturum Ana BILGISAYAR VM yapılandırmasındaki](troubleshoot-vm-configuration.md)etki alanına katılmadığını görün.


### <a name="error-your-deployment-failedunauthorized"></a>Hata: dağıtımınız başarısız oldu. ..\Yetkilendirilmemiş

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Neden:** Kullanmakta olduğunuz abonelik, müşterinin dağıtmaya çalıştığı bölgedeki gerekli özelliklere erişemiyoruz. Örneğin, MSDN, ücretsiz veya eğitim abonelikleri bu hatayı gösterebilir.

**Çözüm:** Abonelik türü veya bölgenizi, gerekli özelliklere erişebilecek bir şekilde değiştirin.

### <a name="error-vmextensionprovisioningerror"></a>Hata: VMExtensionProvisioningError

![Dağıtımınızın ekran görüntüsü, Terminal sağlama durumuyla başarısız oldu.](media/7aaf15615309c18a984673be73ac969a.png)

**Neden 1:** Windows sanal masaüstü ortamında geçici bir hata oluştu.

**Neden 2:** Bağlantıyla geçici hata.

**Çözüm:** PowerShell kullanarak oturum açarak Windows sanal masaüstü ortamının sağlıklı olduğunu onaylayın. [PowerShell ile bir konak havuzu oluşturma](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)bölümünde VM kaydını el ile tamamlama.

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Hata: belirtilen yönetici kullanıcı adına izin verilmiyor

![Belirtilen bir yöneticiye izin verilmediğinden dağıtımınızın ekran görüntüsü başarısız oldu.](media/f2b3d3700e9517463ef88fa41875bac9.png)

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

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Hata: VM, uzantıyı işlerken bir hata bildirdi

![Dağıtımınızda Terminal sağlama durumu ile tamamlanan kaynak işleminin ekran görüntüsü başarısız oldu.](media/49c4a1836a55d91cd65125cf227f411f.png)

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

![' FirstSessionHost ' PowerShell DSC yapılandırması başarısız olan dağıtım ekran görüntüsü hata (ler) ile tamamlandı.](media/64870370bcbe1286906f34cf0a8646ab.png)

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

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Hata: Kullanıcı, Yönetim hizmetini sorgulama yetkisine sahip değil

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

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Hata: Kullanıcı Azure Multi-Factor Authentication gerektiriyor (MFA)

![Multi-Factor Authentication olmaması nedeniyle dağıtımınızın ekran görüntüsü başarısız oldu (MFA)](media/MFARequiredError.png)

Ham hata örneği:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Neden:** Belirtilen Windows sanal masaüstü kiracı yöneticisi, oturum açmak için Azure Multi-Factor Authentication (MFA) gerektirir.

**Çözüm:** [Öğretici: PowerShell ile hizmet sorumluları ve rol atamaları oluşturma](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell)' daki adımları izleyerek bir hizmet sorumlusu oluşturun ve Windows sanal masaüstü kiracınız için bir rol atayın. Hizmet sorumlusu ile Windows sanal masaüstü 'nde oturum açadığınızı doğruladıktan sonra, kullandığınız yönteme bağlı olarak Azure Marketi teklifini veya GitHub Azure Resource Manager şablonunu yeniden çalıştırın. Yönteminizin doğru parametrelerini girmek için aşağıdaki yönergeleri izleyin.

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

## <a name="next-steps"></a>Sonraki adımlar

- Windows sanal masaüstü ve yükseltme izlemelerinin sorunlarını giderme hakkında genel bilgi için bkz. [sorun giderme genel bakış, geri bildirim ve destek](troubleshoot-set-up-overview.md).
- Windows sanal masaüstündeki bir sanal makineyi (VM) yapılandırırken oluşan sorunları gidermek için bkz. [oturum ana bilgisayarı sanal makine yapılandırması](troubleshoot-vm-configuration.md).
- Windows sanal masaüstü istemci bağlantılarıyla ilgili sorunları gidermek için bkz. [Uzak Masaüstü istemci bağlantıları](troubleshoot-client-connection.md).
- Windows sanal masaüstü ile PowerShell kullanırken karşılaşılan sorunları gidermek için bkz. [Windows sanal masaüstü PowerShell](troubleshoot-powershell.md).
- Hizmet hakkında daha fazla bilgi edinmek için bkz. [Windows sanal masaüstü ortamı](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Sorun giderme öğreticisini öğrenmek için bkz. [öğretici: Kaynak Yöneticisi şablonu dağıtımlarının sorunlarını giderme](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Denetim eylemleri hakkında bilgi edinmek için bkz. [Kaynak Yöneticisi Ile denetim işlemleri](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Dağıtım sırasında hataları belirleme eylemleri hakkında bilgi edinmek için bkz. [dağıtım Işlemlerini görüntüleme](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
