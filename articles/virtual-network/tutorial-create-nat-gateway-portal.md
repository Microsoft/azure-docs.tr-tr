---
title: 'Öğretici: NAT Gateway oluşturma-Azure portal'
titlesuffix: Azure Virtual Network NAT
description: Bu öğreticide, Azure portal kullanarak bir NAT ağ geçidi oluşturmayı ve doğrulamayı öğrenin.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: 82b5892b027627871e5492e3c6cd3776a923632b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102553451"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal"></a>Öğretici: Azure portal kullanarak NAT ağ geçidi oluşturma

Bu öğreticide, Azure sanal ağ NAT hizmetini nasıl kullanacağınız gösterilmektedir. Azure 'da bir sanal makine için giden bağlantı sağlamak üzere bir NAT ağ geçidi oluşturacaksınız. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Sanal ağ oluşturun.
> * Sanal makine oluşturur.
> * Bir NAT ağ geçidi oluşturun ve sanal ağla ilişkilendirin.
> * Sanal makineye bağlanın ve NAT IP adresini doğrulayın.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="virtual-network"></a>Sanal ağ

Bir VM 'yi dağıtmadan ve NAT ağ geçidinizi kullanabilmeniz için, kaynak grubunu ve sanal ağı oluşturmanız gerekir.

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. Ekranın sol üst kısmındaki **Kaynak oluştur > Ağ > Sanal ağ** seçeneğini belirleyin veya arama kutusuna **Sanal ağ** yazarak arama yapın.

3. **Oluştur**’u seçin.

4. **Sanal ağ oluştur**' da, **temel** bilgiler sekmesinde bu bilgileri girin veya seçin:

    | **Ayar**          | **Değer**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Proje ayrıntıları**  |                                                                 |
    | Abonelik     | Azure aboneliğinizi seçin                                  |
    | Kaynak Grubu   | **Yeni oluştur**’u seçin. </br> **Myresourcegroupnat** girin. </br> **Tamam**’ı seçin. |
    | **Örnek ayrıntıları** |                                                                 |
    | Name             | **Myvnet** girin                                    |
    | Region           | Seç **(Avrupa) Batı Avrupa** |

5. **IP adresleri** sekmesini seçin veya sayfanın altındaki **Sonraki: IP adresleri** düğmesini seçin.

6. **IP adresleri** sekmesinde şu bilgileri girin:

    | Ayar            | Değer                      |
    |--------------------|----------------------------|
    | IPv4 adres alanı | **10.1.0.0/16** girin |

7. **+ Alt ağ ekle** seçeneğini belirleyin. 

8. **Alt ağı Düzenle**' de şu bilgileri girin:

    | Ayar            | Değer                      |
    |--------------------|----------------------------|
    | Alt ağ adı | **Mysubnet** girin |
    | Alt ağ adres aralığı | **10.1.0.0/24** girin |

9. **Add (Ekle)** seçeneğini belirleyin.

10. **Güvenlik** sekmesini seçin.

11. **Bastionhost** altında **Etkinleştir**' i seçin. Bu bilgileri girin:

    | Ayar            | Değer                      |
    |--------------------|----------------------------|
    | Savunma adı | **Mybastionhost** girin |
    | AzureBastionSubnet adres alanı | **10.1.1.0/24** girin |
    | Genel IP Adresi | **Yeni oluştur**’u seçin. </br> **Ad** Için **Mybastionıp** girin. </br> **Tamam**’ı seçin. |

12. **Gözden geçir + oluştur** sekmesini seçin ya da **gözden geçir + oluştur** düğmesini seçin.

13. **Oluştur**’u seçin.

## <a name="nat-gateway"></a>NAT Gateway

Bir veya daha fazla genel IP adresi kaynağı, genel IP ön ekleri veya her ikisini birden kullanabilirsiniz. Genel IP kaynağı ve NAT ağ geçidi kaynağı ekleyeceğiz.

1. Ekranın sol üst kısmında, **bir kaynak oluştur > ağ > NAT ağ geçidi** ' ni veya arama kutusunda **NAT ağ geçidini** ara ' yı seçin.

2. **Oluştur**’u seçin. 

3. **Ağ adresi çevirisi (NAT) ağ geçidi oluştur**' da, **temel** bilgiler sekmesinde bu bilgileri girin veya seçin:

    | **Ayar**          | **Değer**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Proje ayrıntıları**  |                                                                 |
    | Abonelik     | Azure aboneliğinizi seçin.                                  |
    | Kaynak Grubu   | **Myresourcegroupnat** öğesini seçin. |
    | **Örnek ayrıntıları** |                                                                 |
    | Name             | **Mynatgateway** girin                                    |
    | Region           | Seç **(Avrupa) Batı Avrupa**  |
    | Kullanılabilirlik Alanı | **Hiçbiri** seçeneğini belirtin. |
    | Boşta kalma zaman aşımı (dakika) | **10** girin. |

4. **Gıden IP** sekmesini seçin veya sayfanın altındaki **sonrakı: giden IP** düğmesini seçin.

5. **Gıden IP** sekmesinde aşağıdaki bilgileri girin veya seçin:

    | **Ayar** | **Değer** |
    | ----------- | --------- |
    | Genel IP adresleri | **Yeni bir genel IP adresi oluştur**' u seçin. </br> **Ad** alanına **Mypublicıp** yazın. </br> **Tamam**’ı seçin. |

6. **Alt ağ** sekmesini seçin veya sayfanın altındaki **Sonraki: alt ağ** düğmesini seçin.

7. **Alt ağ** sekmesinde, **sanal ağ** çekme kutusunda **myvnet** ' i seçin.

8. **Mysubnet öğesinin** yanındaki kutuyu işaretleyin.

9. **Gözden geçir + oluştur** sekmesini seçin veya sayfanın altındaki mavi **gözden geçir + oluştur** düğmesini seçin.

10. **Oluştur**’u seçin.
    
## <a name="virtual-machine"></a>Sanal makine

Bu bölümde, NAT ağ geçidini test etmek ve giden bağlantının genel IP adresini doğrulamak için bir sanal makine oluşturacaksınız.

1. Portalın sol üst kısmında **kaynak oluştur**  >  **işlem**  >  **sanal makinesi**' ni seçin. 

2. **Temel bilgiler** sekmesindeki **sanal makine oluştur** sayfasında, aşağıdaki bilgileri girin veya seçin:

    | **Ayar** | **Değer** |
    | ----------- | --------- |
    | **Proje ayrıntıları** |   |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **Myresourcegroupnat** öğesini seçin. |
    | **Örnek ayrıntıları** |   |
    | Sanal makine adı | **Myvm**' i girin. |
    | Region | **(Avrupa) Batı Avrupa** seçin. |
    | Kullanılabilirlik seçenekleri | Varsayılan bir artıklık olmaması gerekmez. |
    | Görüntü | **Windows Server 2019 Datacenter-Gen1** öğesini seçin. |
    | Boyut | **Standard_DS1_v2** seçin. |
    | **Yönetici hesabı** |   |
    | Kullanıcı adı | Sanal makine için bir Kullanıcı adı girin. |
    | Parola | Bir parola girin. |
    | Parolayı onayla | Parolayı onaylayın. |
    | **Gelen bağlantı noktası kuralları** |    |
    | Genel gelen bağlantı noktaları | **Hiçbiri** seçeneğini belirtin. |

3. **Diskler** sekmesini seçin veya sayfanın altındaki **Sonraki: diskler** düğmesini seçin.

4. Varsayılan ' i **diskler** sekmesinde bırakın.

5. **Ağ** sekmesini seçin veya sayfanın altındaki **İleri: ağ** düğmesini seçin.

6. **Ağ** sekmesinde, aşağıdaki bilgileri girin veya seçin:

    | **Ayar** | **Değer** |
    | ----------- | --------- |
    | **Ağ arabirimi** |   |
    | Sanal ağ | **Myvnet**' i seçin. |
    | Alt ağ | **Mysubnet** öğesini seçin. |
    | Genel IP | **Hiçbiri** seçeneğini belirtin. |
    | NIC ağ güvenlik grubu | **Temel**'i seçin. |
    | Genel gelen bağlantı noktaları | **Hiçbiri** seçeneğini belirtin. |

7. **Gözden geçir + oluştur** sekmesini seçin veya sayfanın altındaki mavi **gözden geçir + oluştur** düğmesini seçin.

8. **Oluştur**’u seçin.

## <a name="test-nat-gateway"></a>Test NAT ağ geçidi

Bu bölümde, NAT ağ geçidini test edeceğiz. Önce NAT ağ geçidinin genel IP 'sini keşfedeceğiz. Ardından, test sanal makinesine bağlanır ve NAT ağ geçidi üzerinden giden bağlantıyı doğrulayacağız.
    
1. **Genel bakış** ekranında NAT ağ GEÇIDININ genel IP adresini bulun. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından **mypublicıp**' yi seçin.

2. Genel IP adresini bir yere getirin:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="NAT ağ geçidinin genel IP adresini bulma" border="true":::

3. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **myresourcegroupnat** kaynak grubunda bulunan **myvm** ' yi seçin.

4. **Genel bakış** sayfasında **Bağlan** **' ı ve** sonra da ' yi seçin.

5. Mavi **kullanımı** savunma düğmesini seçin.

6. VM oluşturma sırasında girilen kullanıcı adını ve parolayı girin.

7. **Mytestvm**'de **Internet Explorer** 'ı açın.

8. **https://whatsmyip.com** Adres çubuğuna yazın.

9. Görünen IP adresinin, önceki adımda not ettiğiniz NAT ağ geçidi adresiyle eşleştiğini doğrulayın:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Dış giden IP 'yi gösteren Internet Explorer" border="true":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam edemeyecekiz, sanal ağ, sanal makine ve NAT ağ geçidini aşağıdaki adımlarla silin:

1. Sol taraftaki menüden **kaynak grupları**' nı seçin.

2. **Myresourcegroupnat** kaynak grubunu seçin.

3. **Kaynak grubunu sil**'i seçin.

4. **Myresourcegroupnat** girin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Azure sanal ağ NAT hakkında daha fazla bilgi için bkz.:
> [!div class="nextstepaction"]
> [Sanal ağ NAT genel bakış](nat-overview.md)
