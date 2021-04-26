---
title: Hızlı başlangıç-PowerShell kullanarak Azure API Management örneği oluşturma | Microsoft Docs
description: Azure PowerShell kullanarak yeni bir Azure API Management örneği oluşturun.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: quickstart
ms.custom: mvc, devx-track-azurepowershell
ms.date: 09/14/2020
ms.author: apimpm
ms.openlocfilehash: 641f262cf95753bd4c364fa889051a2b8f9d111a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814271"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-powershell"></a>Hızlı başlangıç: PowerShell kullanarak yeni bir Azure API Management hizmet örneği oluşturma

Azure API Management (APIM), kuruluşların kendi veri ve hizmet potansiyellerini ortaya çıkarmak üzere API’leri dış, iş ortağı ve iç geliştiricilere yayımlamalarına yardımcı olur. API Management; geliştirici katılımı, iş öngörüleri, analizler, güvenlik ve koruma aracılığıyla başarılı bir API programı yürütmeye ilişkin temel uzmanlıklar sağlar. APIM, herhangi bir yerde barındırılan mevcut arka uç hizmetleri için modern API ağ geçitleri oluşturmanıza ve yönetmenize olanak sağlar. Daha fazla bilgi için bkz. [Genel Bakış](api-management-key-concepts.md).

Bu hızlı başlangıçta, Azure PowerShell cmdlet 'lerini kullanarak yeni bir API Management örneği oluşturma adımları açıklanmaktadır.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici, Azure PowerShell modülü 1,0 veya sonraki bir sürümü gerektirir. Sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-Az-ps). PowerShell 'i yerel olarak çalıştırıyorsanız `Connect-AzAccount` Azure ile bir bağlantı oluşturmak için öğesini de çalıştırmanız gerekir.

## <a name="create-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)Ile bir Azure Kaynak grubu oluşturun. Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. 

Aşağıdaki komut Batı ABD konumunda *Myresourcegroup* adlı bir kaynak grubu oluşturur:

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location WestUS
```

## <a name="create-an-api-management-service"></a>API Management hizmeti oluşturma

Artık bir kaynak grubunuz olduğuna göre, bir API Management hizmet örneği oluşturabilirsiniz. [New-Azapimana,](/powershell/module/az.apimanagement/new-azapimanagement) ' i kullanarak bir tane oluşturun ve bir hizmet adı ve Yayımcı Ayrıntıları sağlayın. Hizmet adı Azure içinde benzersiz olmalıdır.

Aşağıdaki örnekte, hizmet adı için *uygulamaım* kullanılır. Adı benzersiz bir değer olarak güncelleştirin. Ayrıca API yayımcısının kuruluş adını ve bildirim almak için yönetici e-posta adresini güncelleştirin.

Varsayılan olarak, komut, Azure API Management değerlendirmek için ekonomik bir seçenek olan geliştirici katmanında örneği oluşturur. Bu katman üretim kullanımı için değildir. API Management katmanlarını ölçeklendirme hakkında daha fazla bilgi için bkz. [yükseltme ve ölçeklendirme](upgrade-and-scale.md).

> [!NOTE]
> Bu uzun süredir çalışan bir işlemdir. Bu katmanda bir API Management hizmeti oluşturmak ve etkinleştirmek için 30 ila 40 dakika sürebilir.

```azurepowershell-interactive
New-AzApiManagement -Name "myapim" -ResourceGroupName "myResourceGroup" `
  -Location "West US" -Organization "Contoso" -AdminEmail "admin@contoso.com" 
```

Komut döndüğünde, Azure API Management hizmetinin özelliklerini görüntülemek için [Get-Azapimanaya](/powershell/module/az.apimanagement/get-azapimanagement) komutunu çalıştırın. Etkinleştirmeden sonra, sağlama durumu başarılı olur ve hizmet örneğinde birkaç ilişkili URL bulunur. Örnek:

```azurepowershell-interactive
Get-AzApiManagement -Name "myapim" -ResourceGroupName "myResourceGroup" 
```

Örnek çıktı:

```console
PublicIPAddresses                     : {203.0.113.1}
PrivateIPAddresses                    :
Id                                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.ApiManagement/service/myapim
Name                                  : myapim
Location                              : West US
Sku                                   : Developer
Capacity                              : 1
CreatedTimeUtc                        : 9/9/2020 9:07:43 PM
ProvisioningState                     : Succeeded
RuntimeUrl                            : https://myapim.azure-api.net
RuntimeRegionalUrl                    : https://myapi-westus-01.regional.azure-api.net
PortalUrl                             : https://myapim.portal.azure-api.net
DeveloperPortalUrl                    : https://myapim.developer.azure-api.net
ManagementApiUrl                      : https://myapim.management.azure-api.net
ScmUrl                                : https://myapim.scm.azure-api.net
PublisherEmail                        : admin@contoso.com
OrganizationName                      : Contoso
NotificationSenderEmail               : apimgmt-noreply@mail.windowsazure.com
VirtualNetwork                        :
VpnType                               : None
PortalCustomHostnameConfiguration     :
ProxyCustomHostnameConfiguration      : {myapim.azure-api.net}
ManagementCustomHostnameConfiguration :
ScmCustomHostnameConfiguration        :
DeveloperPortalHostnameConfiguration  :
SystemCertificates                    :
Tags                                  : {}
AdditionalRegions                     : {}
SslSetting                            : Microsoft.Azure.Commands.ApiManagement.Models.PsApiManagementSslSetting
Identity                              :
EnableClientCertificate               :
ResourceGroupName                     : myResourceGroup

```

API Management hizmet örneğiniz dağıtıldıktan sonra, bunu kullanmaya hazırsınız demektir. [Ilk API 'nizi içeri aktarma ve yayımlama](import-and-publish.md)öğreticisiyle başlayın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu ve tüm ilgili kaynakları kaldırabilirsiniz.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [İlk API’nizi içeri aktarma ve yayımlama](import-and-publish.md)
