---
title: Ağ trafiğini filtrele-öğretici-Azure portal
titlesuffix: Azure Virtual Network
description: Bu öğreticide, Azure portal kullanarak bir ağ güvenlik grubu ile ağ trafiğini bir alt ağa nasıl filtreleyeceğinizi öğreneceksiniz.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: tutorial
ms.date: 03/06/2021
ms.author: kumud
ms.openlocfilehash: cfbb499c79761e1f2014c834e65dac35fe09ef90
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057295"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>Öğretici: Azure portal kullanarak ağ trafiğini ağ güvenlik grubuyla filtreleme

Bir sanal ağ alt ağından gelen ve giden ağ trafiğini filtrelemek için bir ağ güvenlik grubu kullanabilirsiniz.

Ağ güvenlik grupları, ağ trafiğini IP adresi, bağlantı noktası ve protokole göre filtreleyen güvenlik kuralları içerir. Güvenlik kuralları bir alt ağda dağıtılmış kaynaklara uygulanır. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Ağ güvenlik grubu ve güvenlik kuralları oluşturma
> * Bir sanal ağ oluşturma ve ağ güvenlik grubunu alt ağ ile ilişkilendirme
> * Sanal makineleri (VM) bir alt ağa dağıtma
> * Trafik filtrelerini test etme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

1. Portalın sol üst köşesinde bulunan **kaynak oluştur** ' u seçin.

2. Arama kutusuna **sanal ağ**' ı girin. Arama sonuçlarında **sanal ağ** ' ı seçin.

3. **Sanal ağ** sayfasında **Oluştur**' u seçin.

4. **Sanal ağ oluştur**' da, **temel** bilgiler sekmesinde bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Proje ayrıntıları** |   |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **Yeni oluştur**’u seçin.  </br> **Myresourcegroup** girin. </br> **Tamam**’ı seçin. |
    | **Örnek ayrıntıları** |   |
    | Name | **myVNet** yazın. |
    | Region | **Doğu ABD (ABD)** seçin. |

5. **Gözden geçir + oluştur** sekmesini seçin veya sayfanın altındaki mavi **gözden geçir + oluştur** düğmesini seçin.

6. **Oluştur**’u seçin.

## <a name="create-application-security-groups"></a>Uygulama güvenlik grupları oluşturma

Uygulama güvenlik grubu, web sunucuları gibi benzer işlevlere sahip sunucuları birlikte gruplandırmanızı sağlar.

1. Portalın sol üst köşesinde bulunan **kaynak oluştur** ' u seçin.

2. Arama kutusuna **uygulama güvenlik grubu**' nu girin. Arama sonuçlarında **uygulama güvenlik grubu** ' nu seçin.

3. **Uygulama güvenlik grubu** sayfasında **Oluştur**' u seçin.

4. **Uygulama güvenlik grubu oluştur**' da, **temel** bilgiler sekmesinde bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    |**Proje ayrıntıları** |  |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **myResourceGroup** öğesini seçin. |
    | **Örnek ayrıntıları** |  |
    | Name | **Myasgwebservers** girin. |
    | Region | **Doğu ABD (ABD)** seçin. | 

5. **Gözden geçir + oluştur** sekmesini seçin veya sayfanın altındaki mavi **gözden geçir + oluştur** düğmesini seçin.

6. **Oluştur**’u seçin.

7. Aşağıdaki değerleri belirterek 4. adımı tekrar tekrarlayın:

    | Ayar | Değer |
    | ------- | ----- |
    |**Proje ayrıntıları** |  |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **myResourceGroup** öğesini seçin. |
    | **Örnek ayrıntıları** |  |
    | Name | **Myasgmgmtservers** girin. |
    | Region | **Doğu ABD (ABD)** seçin. |

8. **Gözden geçir + oluştur** sekmesini seçin veya sayfanın altındaki mavi **gözden geçir + oluştur** düğmesini seçin.

9. **Oluştur**’u seçin.

## <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

Bir ağ güvenlik grubu, sanal ağınızdaki ağ trafiğinin güvenliğini sağlar.

1. Portalın sol üst köşesinde bulunan **kaynak oluştur** ' u seçin.

2. Arama kutusuna **ağ güvenlik grubu**' nu girin. Arama sonuçlarında **ağ güvenlik grubu** ' nu seçin.

3. **Ağ güvenlik grubu** sayfasında **Oluştur**' u seçin.

4. **Ağ güvenlik grubu oluştur**' da, **temel** bilgiler sekmesinde bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Proje ayrıntıları** |   |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **myResourceGroup** öğesini seçin. |
    | **Örnek ayrıntıları** |   |
    | Name | **Mynsg** girin. |
    | Konum | **Doğu ABD (ABD)** seçin. | 

5. **Gözden geçir + oluştur** sekmesini seçin veya sayfanın altındaki mavi **gözden geçir + oluştur** düğmesini seçin.

6. **Oluştur**’u seçin.

## <a name="associate-network-security-group-to-subnet"></a>Ağ güvenlik grubunu alt ağ ile ilişkilendirme

Bu bölümde, ağ güvenlik grubunu daha önce oluşturduğumuz sanal ağın alt ağıyla ilişkilendireceğiz.

1. Portalın üst kısmındaki **Kaynak, hizmet ve belgeleri arayın** kutusuna **myNsg** yazmaya başlayın. Arama sonuçlarında **myNsg** görüntülendiğinde bunu seçin.

2. **Mynsg** Genel Bakış sayfasında, **Ayarlar**' da **alt ağlar** ' ı seçin.

3. **Ayarlar** sayfasında, **ilişkilendir**' i seçin:

    :::image type="content" source="./media/tutorial-filter-network-traffic/associate-nsg-subnet.png" alt-text="NSG 'yi alt ağ ile ilişkilendirin." border="true":::

3. **Alt ağı ilişkilendir** altında **sanal ağ** ' ı seçin ve ardından **myvnet**' i seçin. 

4. **Alt ağ**, **varsayılan**' i seçin ve ardından **Tamam**' ı seçin.

## <a name="create-security-rules"></a>Güvenlik kuralları oluşturma

1. **Mynsg** **ayarlarında** **gelen güvenlik kuralları**' nı seçin.

2. **Gelen güvenlik kuralları**' nda **+ Ekle**' yi seçin:

    :::image type="content" source="./media/tutorial-filter-network-traffic/add-inbound-rule.png" alt-text="Gelen güvenlik kuralı ekle." border="true":::

3. 80 ve 443 numaralı bağlantı noktalarına **myAsgWebServers** uygulama güvenlik grubu için izin veren bir güvenlik kuralı oluşturun. **Gelen güvenlik kuralı ekle**' de, aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Kaynak | **Bunlardan** birini varsayılan bırakın. |
    | Kaynak bağlantı noktası aralıkları | Varsayılan **(*)** dışında bırakın |
    | Hedef | **Uygulama güvenlik grubu**' nu seçin. |
    | Hedef uygulama güvenlik grubu | **Myasgwebservers**' ı seçin. |
    | Hizmet | Varsayılan değer olan **özel**' i bırakın. |
    | Hedef bağlantı noktası aralıkları | **80.443** girin. |
    | Protokol | **TCP**’yi seçin. |
    | Eylem | Varsayılan **Izin ver**' i bırakın. |
    | Öncelik | Varsayılan değer olan **100**' i bırakın. |
    | Name | **Allow-Web-All** girin. |

    :::image type="content" source="./media/tutorial-filter-network-traffic/inbound-security-rule.png" alt-text="Gelen güvenlik kuralı." border="true":::

3. Aşağıdaki değerleri kullanarak 2. adımı yeniden tamamlayın:

    | Ayar | Değer |
    | ------- | ----- |
    | Kaynak | **Bunlardan** birini varsayılan bırakın. |
    | Kaynak bağlantı noktası aralıkları | Varsayılan **(*)** dışında bırakın |
    | Hedef | **Uygulama güvenlik grubu**' nu seçin. |
    | Hedef uygulama güvenlik grubu | **Myasgmgmtservers**' ı seçin. |
    | Hizmet | Varsayılan değer olan **özel**' i bırakın. |
    | Hedef bağlantı noktası aralıkları | **3389** girin. |
    | Protokol | **TCP**’yi seçin. |
    | Eylem | Varsayılan **Izin ver**' i bırakın. |
    | Öncelik | Varsayılan değer olan **110**' i bırakın. |
    | Name | **Allow-RDP-All** girin. |

    > [!CAUTION]
    > Bu makalede, RDP (bağlantı noktası 3389), **Myasgmgmtservers** uygulama güvenlik grubuna atanan sanal makine için internet 'e açıktır. 
    >
    > Üretim ortamları için 3389 numaralı bağlantı noktasını Internet 'e sunmak yerine bir VPN, özel ağ bağlantısı veya Azure savunma kullanarak yönetmek istediğiniz Azure kaynaklarına bağlanmanız önerilir.
    >
    > Azure savunma hakkında daha fazla bilgi için bkz. [Azure](../bastion/bastion-overview.md)savunma nedir?.

1 ile 3 arası adımları tamamladıktan sonra, oluşturduğunuz kuralları gözden geçirin. Listenizde aşağıdaki örnekteki liste gibi görünmelidir:

:::image type="content" source="./media/tutorial-filter-network-traffic/security-rules.png" alt-text="Güvenlik kuralları." border="true":::

## <a name="create-virtual-machines"></a>Sanal makineler oluşturma

Sanal ağ üzerinde iki sanal makine oluşturun.

### <a name="create-the-first-vm"></a>Birinci sanal makineyi oluşturma

1. Portalın sol üst köşesinde bulunan **kaynak oluştur** ' u seçin.

2. **İşlem**' ı seçin ve ardından **sanal makine**' yi seçin.

3. **Sanal makine oluştur**' da, **temel** bilgiler sekmesinde bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Proje ayrıntıları** |  |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **myResourceGroup** öğesini seçin. |
    | **Örnek ayrıntıları** |   |
    | Sanal makine adı | **Myvmweb** girin. |
    | Region | **Doğu ABD (ABD)** seçin. |
    | Kullanılabilirlik seçenekleri | Varsayılan bir artıklık olmaması gerekmez. |
    | Görüntü | **Windows Server 2019 Datacenter-Gen1** öğesini seçin. |
    | Azure Spot örneği | Varsayılan işaretini işaretsiz bırakın. |
    | Boyut | **Standard_D2s_V3** seçin. |
    | **Yönetici hesabı** |   |
    | Kullanıcı adı | Bir Kullanıcı adı girin. |
    | Parola | Bir parola girin. |
    | Parolayı onayla | Parolayı yeniden girin. |
    | **Gelen bağlantı noktası kuralları** |   |
    | Genel gelen bağlantı noktaları | **Hiçbiri** seçeneğini belirtin. |

4. **Ağ** sekmesini seçin.

5. **Ağ** sekmesinde, aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Ağ arabirimi** |   |
    | Sanal ağ | **Myvnet**' i seçin. |
    | Alt ağ | **Varsayılan (10.0.0.0/24)** seçeneğini belirleyin. |
    | Genel IP | Varsayılan olarak yeni bir genel IP bırakın. |
    | NIC ağ güvenlik grubu | **Hiçbiri** seçeneğini belirtin. | 

6. **Gözden geçir + oluştur** sekmesini seçin veya sayfanın altındaki mavi **gözden geçir + oluştur** düğmesini seçin.

7. **Oluştur**’u seçin.

### <a name="create-the-second-vm"></a>İkinci sanal makineyi oluşturma

1-7 adımını yeniden deneyin, ancak adım 3 ' te VM 'yi **Myvmmgmt** olarak adlandırın. Sanal makinenin dağıtılması birkaç dakika sürer. 

VM dağıtılana kadar sonraki adıma devam etmeyin.

## <a name="associate-network-interfaces-to-an-asg"></a>Ağ arabirimlerini ASG ile ilişkilendirme

Portal VM'leri oluştururken, her VM için bir ağ arabirimi oluşturur ve ağ arabirimini VM'ye bağlar. 

Her VM'nin ağ arabirimini daha önce oluşturduğunuz uygulama güvenlik gruplarından birine ekleyin:

1. Portalın üst kısmındaki **kaynakları, hizmetleri ve belgeleri arayın** kutusuna **myvmweb** yazmaya başlayın. Arama sonuçlarında **Myvmweb** sanal makinesi göründüğünde seçin.

2. **Ayarlar**' da **ağ**' ı seçin.  

3. **Uygulama güvenlik grupları** sekmesini seçin ve ardından **uygulama güvenlik gruplarını Yapılandır**' ı seçin.

    :::image type="content" source="./media/tutorial-filter-network-traffic/configure-app-sec-groups.png" alt-text="Uygulama güvenlik gruplarını yapılandırın." border="true":::

4. **Uygulama güvenlik gruplarını yapılandırma** bölümünde **Myasgwebservers**' ı seçin. **Kaydet**’i seçin.

    :::image type="content" source="./media/tutorial-filter-network-traffic/select-asgs.png" alt-text="Uygulama güvenlik grupları ' nı seçin." border="true":::

5. 1 ve 2. adımları tekrar tamamlayarak, **Myvmmgmt** sanal makinesini arayarak ve  **Myasgmgmtservers** ASG ' sini seçin.

## <a name="test-traffic-filters"></a>Trafik filtrelerini test etme

1. **Myvmmgmt** sanal makinesine bağlanın. Portalın üst kısmındaki arama kutusuna **Myvmmgmt** yazın. Arama sonuçlarında **Myvmmgmt** göründüğünde seçin. **Bağlan** düğmesini seçin.

2. **RDP dosyasını indir**’i seçin.

3. İndirilen rdp dosyasını açın ve **Bağlan**'ı seçin. Sanal makine oluştururken belirttiğiniz kullanıcı adını ve parolayı girin.

4. **Tamam**’ı seçin.

5. Bağlantı işlemi sırasında bir sertifika uyarısı alabilirsiniz. Uyarıyı alırsanız, bağlantıya devam etmek için **Evet** veya **devam et**' i seçin.

    Bağlantı noktası 3389 ' e internet 'ten **Myasgmgmtservers** uygulama güvenlik grubuna izin verildiğinden bağlantı başarılı olur. 
    
    **Myvmmgmt** için ağ arabirimi **Myasgmgmtservers** uygulama güvenlik grubuyla ilişkilendirilir ve bağlantıya izin verir.

6. **Myvmmgmt** üzerinde bir PowerShell oturumu açın. Aşağıdaki örneği kullanarak **Myvmweb** 'e bağlanın: 

    ```powershell
    mstsc /v:myVmWeb
    ```

    Aynı ağdaki sanal makineler varsayılan olarak herhangi bir bağlantı noktası üzerinden iletişim kurabildiğinden, **Myvmmgmt** Ile **myvmweb** arasındaki RDP bağlantısı başarılı olur.
    
    İnternet 'ten **Myvmweb** sanal makinesine RDP bağlantısı oluşturamazsınız. **Myasgwebservers** güvenlik kuralı, internet 'ten gelen bağlantı noktası 3389 ' e giden bağlantıları engeller. Internet 'ten gelen trafik varsayılan olarak tüm kaynaklara reddedilir.

7. Microsoft IIS 'yi **Myvmweb** sanal makinesine yüklemek Için, **myvmweb** sanal makinesindeki bir PowerShell oturumundan aşağıdaki komutu girin:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. IIS yüklemesi tamamlandıktan sonra, **Myvmweb** sanal makinesi ile bağlantıyı kesin, bu, sizi **myvmmgmt** sanal makine Uzak Masaüstü bağlantısında bırakır.

9. **Myvmmgmt** sanal makinesi ile bağlantısını kesin.

10. Azure portal en üstündeki **kaynakları, hizmetleri ve belgeleri arayın** kutusuna **myvmweb** 'i bilgisayarınızdan yazmaya başlayın. Arama sonuçlarında **Myvmweb** göründüğünde seçin. VM'nizin **Genel IP adresini** not alın. Aşağıdaki örnekte gösterilen adres 23.96.39.113 'dir, ancak adresiniz farklıdır:

    :::image type="content" source="./media/tutorial-filter-network-traffic/public-ip-address.png" alt-text="Genel IP adresi." border="true":::
    
11. Internet 'ten **Myvmweb** Web sunucusuna erişebildiğinizden emin olmak için, bilgisayarınızda bir internet tarayıcısı açın ve konumuna gidin `http://<public-ip-address-from-previous-step>` . 

80 numaralı bağlantı noktasına Internet 'ten **Myasgwebservers** uygulama güvenlik grubuna ızın verildiğinden IIS karşılama ekranını görürsünüz. 

**Myvmweb** için bağlı ağ arabirimi **Myasgwebservers** uygulama güvenlik grubuyla ilişkilendirilir ve bağlantıya izin verir. 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu ve içerdiği tüm kaynakları silin:

1. Portalın üst kısmındaki **Ara** kutusuna **myResourceGroup** değerini girin. Arama sonuçlarında **myResourceGroup** seçeneğini gördüğünüzde bunu seçin.
2. **Kaynak grubunu sil**'i seçin.
3. **KAYNAK GRUBU ADINI YAZIN:** için **myResourceGroup** girin ve **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

* Bir ağ güvenlik grubu oluşturdunuz ve bir sanal ağ alt ağıyla ilişkilendirildi. 
* Web ve yönetim için uygulama güvenlik grupları oluşturuldu.
* İki sanal makine oluşturuldu.
* Uygulama güvenlik grubu ağ filtrelemesi test edildi.

Ağ güvenlik grupları hakkında daha fazla bilgi edinmek bkz. [Ağ güvenlik grubuna genel bakış](./network-security-groups-overview.md) ve [Ağ güvenlik grubunu yönetme](manage-network-security-group.md).

Azure, varsayılan olarak trafiği alt ağlar arasında yönlendirir. Bunun yerine, alt ağlar arasındaki trafiği, örneğin, güvenlik duvarı olarak görev yapan bir VM aracılığıyla yönlendirmeyi seçebilirsiniz. 

Yönlendirme tablosu oluşturma hakkında bilgi edinmek için sonraki öğreticiye geçin.
> [!div class="nextstepaction"]
> [Yönlendirme tablosu oluşturma](./tutorial-create-route-table-portal.md)