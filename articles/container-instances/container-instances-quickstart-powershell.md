---
title: Hızlı başlangıç-Azure Container Instances-PowerShell 'e Docker kapsayıcısı dağıtma
description: Bu hızlı başlangıçta, yalıtılmış bir Azure Container örneğinde çalışan kapsayıcılı bir Web uygulamasını hızlıca dağıtmak için Azure PowerShell kullanacaksınız
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 7fe199d2ac228ddb0ccfd1e5bc980e680e160acf
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325839"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-azure-powershell"></a>Hızlı Başlangıç: Azure PowerShell kullanarak Azure 'da kapsayıcı örneği dağıtma

Azure 'da sunucusuz Docker kapsayıcılarını basitlik ve hızla çalıştırmak için Azure Container Instances kullanın. Azure Kubernetes hizmeti gibi tam kapsayıcı düzenleme platformu gerekmiyorsa, bir uygulamayı isteğe bağlı olarak bir kapsayıcı örneğine dağıtın.

Bu hızlı başlangıçta, yalıtılmış bir Windows kapsayıcısını dağıtmak ve uygulamayı tam etki alanı adı (FQDN) ile kullanılabilir hale getirmek için Azure PowerShell kullanırsınız. Tek bir dağıtım komutunu yürütmeden birkaç saniye sonra, kapsayıcıda çalışan uygulamaya gidebilirsiniz:

![Azure Container Instances hizmetine dağıtılmış uygulamanın tarayıcıdaki görüntüsü][qs-powershell-01]

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici Azure PowerShell modülünü gerektirir. Sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-Az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Tüm Azure kaynakları gibi Azure kapsayıcı örneklerinin de bir kaynak grubuna dağıtılması gerekir. Kaynak grupları, ilgili Azure kaynaklarını düzenlemenizi ve yönetmenizi sağlar.

İlk olarak, aşağıdaki [New-AzResourceGroup][New-AzResourceGroup] komutuyla *Eastus* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturun:

 ```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Bir kapsayıcı oluşturma

Artık bir kaynak grubuna sahip olduğunuza göre Azure'da kapsayıcı çalıştırabilirsiniz. Azure PowerShell bir kapsayıcı örneği oluşturmak için, [New-AzContainerGroup][New-AzContainerGroup] cmdlet 'ine bir kaynak grubu adı, kapsayıcı örneği adı ve Docker kapsayıcı görüntüsü sağlayın. Bu hızlı başlangıçta, ortak `mcr.microsoft.com/windows/servercore/iis:nanoserver` görüntüyü kullanırsınız. Bu görüntü, nano sunucu 'da çalıştırılacak Microsoft Internet Information Services (IIS) paketleri.

Açılacak bir veya daha fazla bağlantı noktası, DNS ad etiketi ya da ikisini birden belirterek kapsayıcılarınızı internete açabilirsiniz. Bu hızlı başlangıçta, IIS 'nin genel olarak erişilebilir olması için DNS adı etiketiyle bir kapsayıcı dağıtırsınız.

Bir kapsayıcı örneği başlatmak için aşağıdakine benzer bir komut yürütün. Örneği oluşturduğunuz `-DnsNameLabel` Azure bölgesi içinde benzersiz bir değer ayarlayın. "DNS ad etiketi kullanılamıyor" hata iletisiyle karşılaşırsanız farklı bir DNS ad etiketi deneyin.

 ```azurepowershell-interactive
New-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image mcr.microsoft.com/windows/servercore/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

Birkaç saniye içinde Azure’dan bir yanıt almanız gerekir. Kapsayıcının `ProvisioningState` değeri başlangıçta **Oluşturuluyor** şeklindedir ancak birkaç dakika içinde **Başarılı** olarak değişmesi gerekir. [Get-AzContainerGroup][Get-AzContainerGroup] cmdlet 'ini kullanarak dağıtım durumunu denetleyin:

 ```azurepowershell-interactive
Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

Kapsayıcının sağlama durumu, tam etki alanı adı (FQDN) ve IP adresi, cmdlet çıkışında görüntülenir:

```console
PS Azure:\> Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            : Always
IpAddress                : 52.226.19.87
DnsNameLabel             : aci-demo-win
Fqdn                     : aci-demo-win.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Windows
Volumes                  :
State                    : Pending
Events                   : {}
```

Kapsayıcının `ProvisioningState` bilgisi **Başarılı** olduğunda tarayıcınızdan `Fqdn` adresine gidin. Aşağıdakine benzer bir web sayfası görüyorsanız kendinizi tebrik edebilirsiniz! Docker kapsayıcısında çalışan bir uygulamayı başarıyla Azure'a dağıttınız.

![Azure Container Instances kullanılarak dağıtılmış IIS tarayıcıda görüntüleniyor][qs-powershell-01]

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kapsayıcı ile işiniz bittiğinde [Remove-AzContainerGroup][Remove-AzContainerGroup] cmdlet 'ini kullanarak kaldırın:

 ```azurepowershell-interactive
Remove-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, genel bir Docker Hub deposundaki bir görüntüden bir Azure kapsayıcı örneği oluşturdunuz. Kapsayıcı görüntüsünü oluşturup özel bir Azure kapsayıcı kayıt defterinden dağıtmak istiyorsanız Azure Container Instances öğreticisine geçin.

> [!div class="nextstepaction"]
> [Azure Container Instances öğreticisi](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzResourceGroup]: /powershell/module/az.resources/new-Azresourcegroup
[New-AzContainerGroup]: /powershell/module/az.containerinstance/new-Azcontainergroup
[Get-AzContainerGroup]: /powershell/module/az.containerinstance/get-Azcontainergroup
[Remove-AzContainerGroup]: /powershell/module/az.containerinstance/remove-Azcontainergroup
