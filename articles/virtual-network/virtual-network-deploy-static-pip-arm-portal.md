---
title: Statik bir genel IP adresi ile VM oluşturma-Azure portal
description: Azure portal kullanarak statik bir genel IP adresi ile VM oluşturmayı öğrenin.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/12/2020
ms.author: allensu
ms.openlocfilehash: d416af3d3a8eb8ab8057f13cc0d9a133adcb849a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98221165"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Azure portal kullanarak statik genel IP adresi içeren bir sanal makine oluşturun

Genel bir IP adresi, internet 'ten bir sanal makineyle iletişim kurmanızı sağlar. 

Adresin hiçbir şekilde değişmeyeceğinden emin olmak için dinamik bir adres yerine statik bir genel IP adresi atayın.   

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

1. Portalın sol üst tarafında, **kaynak oluştur**  >  **işlem**  >  **sanal makinesi** ' ni seçin veya arama kutusunda **sanal makine** ara ' yı seçin.
   
2. **Sanal makine oluştur**' da **temel bilgiler** sekmesinde değerleri yazın veya seçin:

    | Ayar | Değer                                          |
    |-----------------------|----------------------------------|
    | **Proje ayrıntıları** |  |
    | Abonelik | Azure aboneliğinizi seçin |
    | Kaynak Grubu | **Yeni oluştur**’u seçin. </br> **Ad** alanına **myresourcegroup** yazın. </br> **Tamam**’ı seçin. |
    | **Örnek ayrıntıları** |  |
    | Sanal makine adı | **Myvm 'yi** girin |
    | Region | **Doğu ABD** seçin |
    | Kullanılabilirlik seçenekleri | **Altyapı yedekliliği gerekli değil** ' i seçin |
    | Görüntü | **Windows Server 2019 Datacenter-Gen1** seçin |
    | Azure Spot örneği | **Hayır** seçin |
    | Boyut | VM boyutunu seçin veya varsayılan ayarı yapın |
    | **Yönetici hesabı** |  |
    | Kullanıcı adı | Kullanıcı adı girin |
    | Parola | Parola girin |
    | Parolayı onayla | Parolayı yeniden girin |

3. **Ağ** sekmesini seçin veya **Sonraki: diskler**' i ve sonra **İleri: ağ**' ı seçin.
  
4. Ağ sekmesinde, şunu seçin veya girin:

    | Ayar | Değer |
    |-|-|
    | **Ağ arabirimi** |  |
    | Sanal ağ | Varsayılan ağ adını kabul edin. |
    | Alt ağ | Varsayılan alt ağ yapılandırmasını kabul edin. |
    | Genel IP | **Yeni oluştur**’u seçin. </br> **Genel IP adresi oluştur** bölümünde adı **Mypublicıp** yazın. </br> **SKU** için **Standart**' ı seçin. </br> **Atama**, **statik** seçeneğini belirleyin. </br> **Tamam**’ı seçin.  |
    | NIC ağ güvenlik grubu | **Temel** seçin|
    | Genel gelen bağlantı noktaları | **Seçili bağlantı noktalarına Izin ver**' i seçin. |
    | Gelen bağlantı noktalarını seçin | RDP 'yi seçin **(3389)** |

    > [!WARNING]
    > Portal 3389, Windows Server sanal makinesine internet 'ten uzaktan erişimi etkinleştirmek için seçilidir. 3389 numaralı bağlantı noktasını Internet 'e açmak, üretim iş yüklerini yönetmek için önerilmez. </br> Azure sanal makinelerine güvenli erişim için bkz. **[Azure nasıl yapılır?](../bastion/bastion-overview.md)**
   
5. **Gözden geçir ve oluştur**’u seçin. 
  
6. Ayarları gözden geçirin ve ardından **Oluştur**' u seçin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu ve içerdiği tüm kaynakları silin:

1. Portalın üst kısmındaki **Ara** kutusuna **myResourceGroup** değerini girin. Arama sonuçlarında **myResourceGroup** seçeneğini gördüğünüzde bunu seçin.
2. **Kaynak grubunu sil**'i seçin.
3. **KAYNAK GRUBU ADINI YAZIN:** için **myResourceGroup** girin ve **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bkz. [IP adreslerini ekleme, değiştirme veya kaldırma](virtual-network-network-interface-addresses.md):

* Dinamik olan bir genel IP adresini statik olarak değiştirmek için.
* Özel IP adresleriyle çalışın.

Genel IP adreslerinin nominal bir [ücreti](https://azure.microsoft.com/pricing/details/ip-addresses)vardır. Abonelik başına kullanabileceğiniz genel IP adresi sayısı için bir [sınır](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) vardır.

Sanal makinenin genel IP adresi SKU 'SU, bir arka uç havuzuna eklendiğinde Azure Load Balancer ortak IP SKU 'SU ile eşleşmelidir. Ayrıntılar için bkz. [Azure Load Balancer](../load-balancer/skus.md).

Azure [Genel](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [Çin](https://www.microsoft.com/download/details.aspx?id=57062) ve [Almanya](https://www.microsoft.com/download/details.aspx?id=57064) bulutları için bu aralıkların (ön ekler) listesini indirebilirsiniz.

- [Statik genel IP adresleri](./public-ip-addresses.md#allocation-method)hakkında daha fazla bilgi edinin.
- Azure 'da [genel IP adresleri](./public-ip-addresses.md#public-ip-addresses) hakkında daha fazla bilgi edinin.
- Tüm [genel IP adresi ayarları](virtual-network-public-ip-address.md#create-a-public-ip-address)hakkında daha fazla bilgi edinin.
- [Özel IP adresleri](./private-ip-addresses.md) hakkında daha fazla bilgi edinin ve bir Azure sanal makinesine [STATIK bir özel IP adresi](virtual-network-network-interface-addresses.md#add-ip-addresses) atama.
