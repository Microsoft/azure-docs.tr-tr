---
title: Özel etki alanı oluşturma ve kullanma
description: Özel bir etki alanını Azure 'da bir sanal makineye bağlama.
author: mimckitt
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/01/2021
ms.author: jamesser
ms.reviewer: cynthn
ms.openlocfilehash: c4797420904b6dc03550f658c2aa950a4de99c9c
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251029"
---
# <a name="add-custom-domain-to-azure-vm-or-resource"></a>Azure VM veya kaynağına özel etki alanı ekleme

Azure 'da, özel bir etki alanını sanal makinenize veya kaynağına bağlamak için birden çok yol vardır. Genel IP (sanal makine, Load Balancer Application Gateway) olan herhangi bir kaynak için en basit yol, karşılık gelen etki alanı kaydedicisinde bir kayıt kümesi oluşturmaktır. 

## <a name="prerequisites"></a>Önkoşullar 
- Çalıştıran bir Web sunucusuna sahip bir VM 'ye ihtiyacınız vardır. [Hızlı](./linux/quick-create-cli.md) başlangıcı kullanarak bir VM oluşturabilir ve NGINX ekleyebilirsiniz.

- VM Web 'in erişimine açık olmalıdır (bağlantı noktası 80 veya 443). Daha güvenli bir dağıtım için VM 'nizi bir yük dengeleyicinin arkasına yerleştirin veya önce Application Gateway. Daha fazla bilgi için bkz. [hızlı başlangıç: Load Balancer](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal?tabs=option-1-create-load-balancer-standard).

- Mevcut bir etki alanına sahip ve DNS ayarlarına erişim. Daha fazla bilgi için bkz. [Azure App Service için özel etki alanı satın alma](../app-service/manage-custom-dns-buy-domain.md).


## <a name="add-custom-domain-to-vm-public-ip-address"></a>VM genel IP adresine özel etki alanı Ekle

Azure portal bir sanal makine oluşturduğunuzda, sanal makine için genel bir IP kaynağı otomatik olarak oluşturulur. Genel IP adresiniz, VM 'ye Genel Bakış sayfasında gösterilir. 
 
:::image type="content" source="media/custom-domain/essentials.png" alt-text="VM Genel Bakış sayfasının temel bileşenler bölümünde genel IP adresini gösterir.":::

IP adresini seçerseniz, hakkında daha fazla bilgi görebilirsiniz. **IP atamalarınızın** **statik** olarak ayarlandığından emin olmak için denetleyin. VM veya kaynak yeniden başlatıldığında veya kapatılırsa statik bir IP adresi değişmez.

:::image type="content" source="media/custom-domain/ip-config.png" alt-text="IP adresinin statik olup olmadığını görebilmeniz için genel IP yapılandırmasını gösterir.":::

IP adresiniz statik değilse, bir FQDN oluşturmanız gerekecektir. 

1. Portalda VM 'nizi seçin. 
1. Sol taraftaki menüden **Özellikler** ' i seçin.
1. **Genel IP Adresi\ DNS adı etiketi** altında IP adresinizi seçin.
2. **DNS adı etiketi** altında kullanmak istediğiniz ön eki girin.
3. Sayfanın üst kısmındaki **Kaydet** ' i seçin.
4. Sol taraftaki menüden **genel bakış** ' ı seçerek VM 'ye genel bakış dikey penceresine dönün.
5. *DNS adının* doğru göründüğünü doğrulayın. 

Bir tarayıcı açın ve IP adresinizi veya FQDN 'sini girin ve sanal makinenizde çalışan Web içeriğini gösterir.
 
Statik IP veya FQDN 'nizi doğruladıktan sonra, etki alanı sağlayıcınıza gidin ve DNS ayarları ' na gidin.

Bir kez genel IP adresi veya FQDN 'sine işaret eden *bir kayıt* ekleyin. Örneğin, GoDaddy etki alanı kayıt şirketi için yordam şu şekildedir:
1. Oturum açın ve kullanmak istediğiniz özel etki alanını seçin.
2. **Etki alanları** bölümünde **Tümünü Yönet**' i seçin, sonra DNS ' yi seçin **| Bölgeleri yönetin**.
3. **Etki Alanı Adı** alanına özel etki alanınızı girin, ardından **Ara**’yı seçin.
4. DNS yönetimi sayfasında Ekle ' yi seçin, sonra tür listesinden bir seçin.
5. Bir girdinin alanlarını doldurun:
    - Yazın **: seçili bırakın** .
    - Ana bilgisayar: gir **@**
    - Şunu gösterir: VM 'nizin genel IP adresini veya FQDN 'sini girin. 
    - TTL: bir saat seçili olarak bırakın.
6. **Kaydet**’i seçin.

Kayıt girdisi, DNS kayıtları tablosuna eklenir.
 
Kayıt oluşturulduktan sonra, DNS yayma için genellikle bir saat sürer, ancak bazen 48 saate kadar sürebilir. 


 
## <a name="next-steps"></a>Sonraki adımlar
[Application Gateway Ile TLS sonlandırmasına ve uçtan uca TLS 'ye genel bakış](../application-gateway/ssl-overview.md).

 
