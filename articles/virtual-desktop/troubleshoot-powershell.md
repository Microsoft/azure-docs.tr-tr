---
title: Windows sanal masaüstü PowerShell-Azure
description: Windows sanal masaüstü ortamı ayarlarken PowerShell ile ilgili sorunları giderme.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 06/05/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 868aacc986e66d4f7513851855b69ed84c4a3e88
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445474"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows Sanal Masaüstü PowerShell

>[!IMPORTANT]
>Bu içerik Azure Resource Manager Windows sanal masaüstü nesneleri ile Windows sanal masaüstü için geçerlidir. Azure Resource Manager nesneleri olmadan Windows sanal masaüstü (klasik) kullanıyorsanız, [Bu makaleye](./virtual-desktop-fall-2019/troubleshoot-powershell-2019.md)bakın.

PowerShell 'i Windows sanal masaüstü ile kullanırken oluşan hataları ve sorunları gidermek için bu makaleyi kullanın. PowerShell Uzak Masaüstü Hizmetleri hakkında daha fazla bilgi için bkz. [Windows sanal masaüstü PowerShell](/powershell/windows-virtual-desktop/overview).

## <a name="provide-feedback"></a>Geribildirim gönderme

Windows Sanal Masaüstü hizmetini ürün ekibi ve etkin topluluk üyeleriyle tartışmak için [Windows sanal masaüstü teknoloji Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) 'yi ziyaret edin.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Windows sanal masaüstü kurulumu sırasında kullanılan PowerShell komutları

Bu bölümde, Windows sanal masaüstü ayarlanırken genellikle kullanılan PowerShell komutları listelenir ve bunları kullanırken oluşabilecek sorunları çözmeye yönelik yollar sağlanır.

### <a name="error-new-azroleassignment-the-provided-information-does-not-map-to-an-ad-object-id"></a>Hata: New-Azroleatama: belirtilen bilgiler bir AD nesne KIMLIĞIYLE eşlenmiyor

```powershell
New-AzRoleAssignment -SignInName "admins@contoso.com" -RoleDefinitionName "Desktop Virtualization User" -ResourceName "0301HP-DAG" -ResourceGroupName 0301RG -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

**Neden:** *-Signınname* parametresi tarafından belirtilen kullanıcı, Windows sanal masaüstü ortamına bağlı Azure Active Directory bulunamıyor.

**Çözüm:** Aşağıdaki işlemlerden emin olun.

- Azure Active Directory için kullanıcının eşitlenmesi gerekir.
- Kullanıcı, işletmeden müşteriye (B2C) veya işletmeden işletmeye (B2B) ticareti 'ne bağlı olmamalıdır.
- Windows sanal masaüstü ortamı, doğru Azure Active Directory bağlı olmalıdır.

### <a name="error-new-azroleassignment-the-client-with-object-id-does-not-have-authorization-to-perform-action-over-scope-code-authorizationfailed"></a>Hata: New-Azroleatama: "nesne kimliği olan istemcinin kapsam üzerinde eylem gerçekleştirme yetkilendirmesi yok (kod: AuthorizationFailed)"

**Neden 1:** Kullanılan hesabın abonelik üzerinde sahip izinleri yok.

**1. Çözüm:** Sahip izinlerine sahip bir kullanıcının rol atamasını yürütmesi gerekir. Alternatif olarak, bir Kullanıcı bir uygulama grubuna atamak için kullanıcının Kullanıcı erişimi Yöneticisi rolüne atanması gerekir.

**Neden 2:** Kullanılan hesabın sahip izinleri var, ancak ortamın Azure Active Directory parçası değil veya kullanıcının bulunduğu Azure Active Directory sorgulamak için izinlere sahip değil.

**2. Çözüm:** Active Directory izinlere sahip bir kullanıcının rol atamasını yürütmesi gerekir.

### <a name="error-new-azwvdhostpool----the-location-is-not-available-for-resource-type"></a>Hata: New-AzWvdHostPool--konum, kaynak türü için kullanılamaz

```powershell
New-AzWvdHostPool_CreateExpanded: The provided location 'southeastasia' is not available for resource type 'Microsoft.DesktopVirtualization/hostpools'. List of available regions for the resource type is 'eastus,eastus2,westus,westus2,northcentralus,southcentralus,westcentralus,centralus'.
```

Neden: Windows sanal masaüstü, belirli konumlarda hizmet meta verilerini depolamak için konak havuzlarının, uygulama gruplarının ve çalışma alanlarının konumunu seçmeyi destekler. Seçenekleriniz bu özelliğin kullanılabildiği yerle kısıtlıdır. Bu hata, özelliğin seçtiğiniz konumda kullanılamadığı anlamına gelir.

Çözüm: hata iletisinde, desteklenen bölgelerin bir listesi yayımlanacak. Bunun yerine desteklenen bölgelerden birini kullanın.

### <a name="error-new-azwvdapplicationgroup-must-be-in-same-location-as-host-pool"></a>Hata: New-AzWvdApplicationGroup konak havuzuyla aynı konumda olmalıdır

```powershell
New-AzWvdApplicationGroup_CreateExpanded: ActivityId: e5fe6c1d-5f2c-4db9-817d-e423b8b7d168 Error: ApplicationGroup must be in same location as associated HostPool
```

**Neden:** Bir konum uyumsuzluğu var. Tüm konak havuzları, uygulama grupları ve çalışma alanları, hizmet meta verilerini depolamak için bir konuma sahiptir. Oluşturduğunuz tüm nesneler aynı konumda olmalıdır. Örneğin, bir konak havuzu içinde ise `eastus` , içinde uygulama grupları da oluşturmanız gerekir `eastus` . Bu uygulama gruplarının kaydedileceği bir çalışma alanı oluşturursanız, bu çalışma alanının da olması gerekir `eastus` .

**Çözüm:** Konak havuzunun oluşturulduğu konumu alın, ardından oluşturmakta olduğunuz uygulama grubunu aynı konuma atayın.

## <a name="next-steps"></a>Sonraki adımlar

- Windows sanal masaüstü ve yükseltme izlemelerinin sorunlarını giderme hakkında genel bilgi için bkz. [sorun giderme genel bakış, geri bildirim ve destek](troubleshoot-set-up-overview.md).
- Windows sanal masaüstü ortamınızı ve konak havuzlarınızı ayarlarken oluşan sorunları gidermek için bkz. [ortam ve konak havuzu oluşturma](troubleshoot-set-up-issues.md).
- Windows sanal masaüstündeki bir sanal makineyi (VM) yapılandırırken oluşan sorunları gidermek için bkz. [oturum ana bilgisayarı sanal makine yapılandırması](troubleshoot-vm-configuration.md).
- Windows sanal masaüstü istemci bağlantılarıyla ilgili sorunları gidermek için bkz. [Windows sanal masaüstü hizmeti bağlantıları](troubleshoot-service-connection.md).
- Uzak Masaüstü istemcileriyle ilgili sorunları gidermek için bkz [. uzak masaüstü Istemcisinde sorun giderme](troubleshoot-client.md)
- Hizmet hakkında daha fazla bilgi edinmek için bkz. [Windows sanal masaüstü ortamı](environment-setup.md).
- Denetim eylemleri hakkında bilgi edinmek için bkz. [Kaynak Yöneticisi Ile denetim işlemleri](../azure-resource-manager/management/view-activity-logs.md).
- Dağıtım sırasında hataları belirleme eylemleri hakkında bilgi edinmek için bkz. [dağıtım Işlemlerini görüntüleme](../azure-resource-manager/templates/deployment-history.md).
