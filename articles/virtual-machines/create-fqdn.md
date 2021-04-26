---
title: Azure portal VM için FQDN oluşturma
description: Azure portal bir sanal makine için tam etki alanı adı (FQDN) oluşturmayı öğrenin.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/01/2021
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b010070b7a45c24037c6de4648574c01b017d759
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107406"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Linux VM için Azure portal tam etki alanı adı oluşturma

[Azure Portal](https://portal.azure.com)bir sanal makıne (VM) oluşturduğunuzda, sanal makine için genel bir IP kaynağı otomatik olarak oluşturulur. Bu IP adresini kullanarak VM 'ye uzaktan erişin. Portal [tam etki alanı adı](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)veya FQDN OLUŞTURMASA da VM oluşturulduktan sonra bir tane ekleyebilirsiniz. Bu makalede bir DNS adı veya FQDN oluşturma adımları gösterilmektedir. 

## <a name="create-a-fqdn"></a>FQDN oluşturma
Bu makalede, zaten bir VM oluşturmuş olduğunuz varsayılmaktadır. Gerekirse, portalda bir [Linux](./linux/quick-create-portal.md) veya [Windows](./windows/quick-create-portal.md) VM oluşturabilirsiniz. VM 'niz çalışır duruma getirildikten sonra aşağıdaki adımları izleyin:


1. Portalda VM 'nizi seçin. 
1. Sol taraftaki menüden **Özellikler** ' i seçin.
1. **Genel IP Adresi\ DNS adı etiketi** altında IP adresinizi seçin.
2. **DNS adı etiketi** altında kullanmak istediğiniz ön eki girin.
3. Sayfanın üst kısmındaki **Kaydet** ' i seçin.
4. Sol taraftaki menüden **genel bakış** ' ı seçerek VM 'ye genel bakış dikey penceresine dönün.
5. **DNS adının** doğru göründüğünü doğrulayın. 

## <a name="next-steps"></a>Sonraki adımlar

Ayrıca, [Azure DNS bölgelerini](../dns/dns-getstarted-portal.md)kullanarak DNS yönetebilirsiniz.

