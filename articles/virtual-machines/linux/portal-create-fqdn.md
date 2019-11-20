---
title: Azure portal bir Linux sanal makinesi için FQDN oluşturma
description: Azure portal Kaynak Yöneticisi tabanlı bir sanal makine için tam etki alanı adı veya FQDN oluşturma hakkında bilgi edinin.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3d30f5a60bf19e9185d992b973414f58942f9954
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035303"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Linux VM için Azure portal tam etki alanı adı oluşturma

[Azure Portal](https://portal.azure.com)bir sanal makıne (VM) oluşturduğunuzda, sanal makine için genel bir IP kaynağı otomatik olarak oluşturulur. Bu IP adresini kullanarak VM 'ye uzaktan erişin. Portal [tam etki alanı adı](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)veya FQDN OLUŞTURMASA da VM oluşturulduktan sonra bir tane ekleyebilirsiniz. Bu makalede bir DNS adı veya FQDN oluşturma adımları gösterilmektedir.

## <a name="create-a-fqdn"></a>FQDN oluşturma
Bu makalede, zaten bir VM oluşturmuş olduğunuz varsayılmaktadır. Gerekirse, portalda veya [Azure CLI ile](quick-create-cli.md) [bir VM oluşturabilirsiniz](quick-create-portal.md) . VM 'niz çalışır duruma getirildikten sonra aşağıdaki adımları izleyin:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Artık `ssh azureuser@mydns.westus.cloudapp.azure.com`gibi bu DNS adını kullanarak VM 'ye uzaktan bağlanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
Artık VM 'nizin genel IP ve DNS adı olduğuna göre, yaygın uygulama çerçevelerini veya NGINX, MongoDB, Docker gibi Hizmetleri dağıtabilirsiniz.

Ayrıca, Azure dağıtımlarınızı oluşturmaya yönelik ipuçları için [Kaynak Yöneticisi kullanma](../../azure-resource-manager/resource-group-overview.md) hakkında daha fazla bilgi edinebilirsiniz.

