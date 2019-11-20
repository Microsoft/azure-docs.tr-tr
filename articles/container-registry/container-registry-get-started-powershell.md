---
title: Hızlı başlangıç-kayıt defteri oluşturma-PowerShell-Azure Container Registry
description: PowerShell ile Azure Container Registry 'de özel bir Docker kayıt defteri oluşturmayı hızlı bir şekilde öğrenin
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: b337c28efc3db7d4bec4408b5da1f8a5e1d87094
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931620"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell kullanarak özel kapsayıcı kayıt defteri oluşturma

Azure Container Registry, Docker kapsayıcı görüntülerini oluşturmak, depolamak ve sunmak için kullanılan özel bir yönetilen Docker kapsayıcı kayıt defteridir. Bu hızlı başlangıçta PowerShell kullanarak Azure Container Registry oluşturmayı öğreneceksiniz. Ardından, Docker komutlarını kullanarak kayıt defterine bir kapsayıcı görüntüsü gönderin ve son olarak görüntüyü Kayıt defterinizden çekin ve çalıştırın.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu hızlı başlangıç Azure PowerShell modülünü gerektiriyor. Yüklü sürümünüzü belirlemek için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure PowerShell Modülü yükleme](/powershell/azure/install-az-ps).

Ayrıca sisteminizde yerel olarak Docker yüklü olması gerekir. Docker, [MacOS][docker-mac], [Windows][docker-windows]ve [Linux][docker-linux] sistemleri için paketler sağlar.

Azure Cloud Shell gerekli tüm Docker bileşenlerini (`dockerd` daemon) içermediğinden, bu hızlı başlangıçta Cloud Shell’i kullanamazsınız.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Connect-AzAccount][Connect-AzAccount] komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin.

```powershell
Connect-AzAccount
```

## <a name="create-resource-group"></a>Kaynak grubu oluşturma

Azure ile kimlik doğrulaması yaptıktan sonra [New-AzResourceGroup][New-AzResourceGroup]ile bir kaynak grubu oluşturun. Kaynak grubu, Azure kaynaklarınızın dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Kapsayıcı kayıt defteri oluşturma

Ardından, yeni [-AzContainerRegistry][New-AzContainerRegistry] komutuyla yeni kaynak grubunuzda bir kapsayıcı kayıt defteri oluşturun.

Kaynak defteri adı Azure’da benzersiz olmalı ve 5-50 arası alfasayısal karakter içermelidir. Aşağıdaki örnek, "myContainerRegistry007" adlı bir kayıt defteri oluşturur. Aşağıdaki komutta *myContainerRegistry007* değerini değiştirin ve kayıt defterini oluşturmak için komutu çalıştırın:

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

Bu hızlı başlangıçta, Azure Container Registry hakkında bilgi edinmek için uygun maliyetli bir seçenek olan *temel* bir kayıt defteri oluşturursunuz. Kullanılabilir hizmet katmanları hakkında daha fazla bilgi için bkz. [kapsayıcı kayıt defteri SKU 'ları][container-registry-skus].

## <a name="log-in-to-registry"></a>Kayıt defterinde oturum açma

Kapsayıcı görüntülerini gönderip çekmeden önce kayıt defterinizde oturum açmalısınız. Üretim senaryolarında, kapsayıcı kayıt defteri erişimi için tek bir kimlik veya hizmet sorumlusu kullanmanız gerekir, ancak bu hızlı başlangıcı kısa tutmak için, [Get-AzContainerRegistryCredential][Get-AzContainerRegistryCredential] komutuyla kayıt defterinizde yönetici kullanıcıyı etkinleştirin:

```powershell
$creds = Get-AzContainerRegistryCredential -Registry $registry
```

Ardından, oturum açmak için [Docker oturum açma][docker-login] çalıştırın:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

Bu komut tamamlandığında `Login Succeeded` döndürülür.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıçta oluşturduğunuz kaynaklarla çalışmayı tamamladıktan sonra, burada depolanan kaynak grubunu, kapsayıcı kayıt defterini ve kapsayıcı görüntülerini kaldırmak için [Remove-AzResourceGroup][Remove-AzResourceGroup] komutunu kullanın:

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure PowerShell bir Azure Container Registry oluşturdunuz, kapsayıcı görüntüsünü gönderdi ve görüntüyü kayıt defterinden çekmiş ve çalıştırdınız. ACR 'ye daha ayrıntılı bir bakış için Azure Container Registry öğreticilerine geçin.

> [!div class="nextstepaction"]
> [Azure Container Registry öğreticileri][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Get-AzContainerRegistryCredential]: /powershell/module/az.containerregistry/get-azcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzContainerRegistry]: /powershell/module/az.containerregistry/New-AzContainerRegistry
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[Remove-AzResourceGroup]: /powershell/module/az.resources/remove-azresourcegroup
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
