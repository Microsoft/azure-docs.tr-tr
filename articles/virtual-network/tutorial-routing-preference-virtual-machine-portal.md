---
title: VM için yönlendirme tercihini Yapılandırma-Azure portal
description: Azure portal kullanarak yönlendirme tercihi seçimine sahip bir genel IP adresi ile VM oluşturmayı öğrenin.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: mnayak
ms.openlocfilehash: 0559d02ec603d12578fa46d9790d0711fde5e38b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670902"
---
# <a name="configure-routing-preference-for-a-vm-using-the-azure-portal"></a>Azure portal kullanarak bir VM için yönlendirme tercihini yapılandırma

Bu makalede, bir sanal makine için yönlendirme tercihini yapılandırma gösterilmektedir. Internet 'e ait trafiği yönlendirme tercih seçeneği olarak **Internet** ' i SEÇTIĞINIZDE, ISS ağı aracılığıyla yönlendirilecektir. Varsayılan yönlendirme, Microsoft Global ağı aracılığıyla yapılır.

Bu makalede, Azure portal kullanarak trafiği genel İnternet üzerinden yönlendirmek üzere ayarlanmış bir genel IP ile sanal makine oluşturma işlemi gösterilmektedir.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

1. Azure portalının sol üst köşesinde bulunan **+ Kaynak oluştur** seçeneğini belirleyin.
2. **İşlem**' ı seçin ve ardından **WINDOWS Server 2016 VM** veya seçtiğiniz başka bir işletim sistemini seçin.
3. Aşağıdaki bilgileri girin veya seçin, kalan ayarlar için varsayılan değerleri kabul edin ve sonra **Tamam**’ı seçin:

    |Ayar|Değer|
    |---|---|
    |Ad|myVM|
    |Kullanıcı adı| Seçtiğiniz bir kullanıcı adını girin.|
    |Parola| Seçtiğiniz bir parolayı girin. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)karşılamalıdır.|
    |Abonelik| Aboneliğinizi seçin.|
    |Kaynak grubu| **Mevcut olanı kullan**’ı seçin ve **myResourceGroup** seçeneğini belirleyin.|
    |Konum| **Doğu ABD** seçin|

4. Sanal makine için bir boyut seçin ve **Seç** seçeneğini belirleyin.
5. **Ağ** sekmesinde, **genel IP adresi** için **Yeni oluştur** ' a tıklayın.
6. *Mypublicıpaddress* yazın, SKU 'yu **Standart** olarak seçin ve ardından yönlendirme tercihi **Internet** ' i seçin ve ardından aşağıdaki resimde gösterildiği gibi **Tamam 'a** basın:

   ![Statik seçin](./media/tutorial-routing-preference-virtual-machine-portal/routing-preference-internet-new.png)

6. Bir bağlantı noktası seçin veya **Genel gelen bağlantı noktalarını seçin** altında bağlantı noktası yok. Portal 3389, Windows Server sanal makinesine internet 'ten uzaktan erişimi etkinleştirmek için seçilidir. 3389 numaralı bağlantı noktasını Internet 'ten açmak, üretim iş yükleri için önerilmez.

   ![Bir bağlantı noktası seçin](./media/tutorial-routing-preference-virtual-machine-portal/pip-ports-new.png)

7. Kalan varsayılan ayarları kabul edin ve **Tamam**' ı seçin.
8. **Özet** sayfasında **Oluştur**'u seçin. Sanal makinenin dağıtılması birkaç dakika sürer.
9. Sanal makine dağıtıldıktan sonra portalın üst kısmındaki arama kutusuna *Mypublicıpaddress* yazın. Arama sonuçlarında **Mypublicıpaddress** göründüğünde seçin.
10. Aşağıdaki resimde gösterildiği gibi, atanan genel IP adresini ve adresin **Myvm** sanal makinesine atandığını görebilirsiniz:

    ![Ekran görüntüsü, NIC 'in ağ arabirimi için genel g/ç 'sini gösterir MYNIC.](./media/tutorial-routing-preference-virtual-machine-portal/pip-properties-new.png)

11. **Ağ**' ı seçin, ardından NIC **MYNIC** ' e tıklayın ve ardından genel IP adresini seçerek yönlendirme tercihinin **Internet** olarak atandığını onaylayın.

    ![Ekran görüntüsünde, bir genel ı adresi için ı P adresi ve yönlendirme tercihi gösterilmektedir.](./media/tutorial-routing-preference-virtual-machine-portal/pip-routing-internet-new.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu ve içerdiği tüm kaynakları silin:

1. Portalın üst kısmındaki **Ara** kutusuna *myResourceGroup* değerini girin. Arama sonuçlarında **myResourceGroup** seçeneğini gördüğünüzde bunu seçin.
2. **Kaynak grubunu sil**'i seçin.
3. **KAYNAK GRUBU ADINI YAZIN:** için *myResourceGroup* girin ve **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar
- [Yönlendirme tercihi ile genel IP](routing-preference-overview.md)hakkında daha fazla bilgi edinin.
- Azure 'da [genel IP adresleri](./public-ip-addresses.md#public-ip-addresses) hakkında daha fazla bilgi edinin.
- Tüm [genel IP adresi ayarları](virtual-network-public-ip-address.md#create-a-public-ip-address)hakkında daha fazla bilgi edinin.