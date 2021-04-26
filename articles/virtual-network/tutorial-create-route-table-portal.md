---
title: Yönlendirme ağ trafiği-öğretici-Azure portal
titlesuffix: Azure Virtual Network
description: Bu öğreticide, Azure portalını kullanarak bir yönlendirme tablosu ile ağ trafiğini yönlendirme hakkında bilgi edineceksiniz.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/16/2021
ms.author: kumud
ms.openlocfilehash: 7da59e996ec37d3653dbde68c5f56caa9e8261ee
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061919"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Öğretici: Azure portalını kullanarak bir yönlendirme tablosu ile ağ trafiğini yönlendirme

Azure, varsayılan olarak bir sanal ağ içindeki tüm alt ağlar arasındaki trafiği yönlendirir. Azure’ın varsayılan yönlendirmesini geçersiz kılmak için kendi yönlendirmelerinizi oluşturabilirsiniz. Özel yollar, örneğin bir ağ sanal gereci (NVA) ile alt ağlar arasında trafiği yönlendirmek istediğinizde yararlıdır. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Trafiği yönlendiren bir NVA oluşturma
> * Yönlendirme tablosu oluşturma
> * Yönlendirme oluşturma
> * Yönlendirme tablosunu bir alt ağ ile ilişkilendirme
> * Sanal makineleri (VM) farklı alt ağlara dağıtma
> * NVA aracılığıyla trafiği bir alt ağdan başka birine yönlendirme

Bu öğretici [Azure Portal](https://portal.azure.com)kullanır. [Azure CLI](tutorial-create-route-table-cli.md) veya [Azure PowerShell](tutorial-create-route-table-powershell.md)de kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, etkin aboneliği olan bir Azure hesabı gerekir. Bir hesabınız yoksa, [ücretsiz olarak bir hesap oluşturabilirsiniz](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

1. Azure portalı menüsünden **Kaynak oluştur**'u seçin. Azure Marketi 'nden **ağ**  >  **sanal ağı**' nı seçin veya arama kutusunda **sanal ağ** ara ' yı seçin.

2. **Oluştur**’u seçin.

2. **Sanal ağ oluştur**' da bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Abonelik | Aboneliğinizi seçin.|
    | Kaynak grubu | **Yeni oluştur**' u seçin, **myresourcegroup** girin. </br> **Tamam**’ı seçin. |
    | Name | **MyVirtualNetwork** girin. |
    | Konum | **Doğu ABD (ABD)** seçin.|

3. **IP adresleri** sekmesini seçin veya sayfanın altındaki **Sonraki: IP adresleri** düğmesini seçin.

4. **IPv4 adres alanı**' nda, var olan adres alanını seçin ve **10.0.0.0/16** olarak değiştirin.

4. + Alt ağ **Ekle**' yi seçin, ardından alt ağ **adı** için **genel** ve **10.0.0.0/24** **alt ağ adres aralığını** girin.

5. **Add (Ekle)** seçeneğini belirleyin.

6. **+** Alt ağ Ekle ' yi seçin ve alt ağ **adres aralığı** için **10.0.1.0/24** alt ağ **adı** için **özel** girin.

7. **Add (Ekle)** seçeneğini belirleyin.

8. + Alt ağ **Ekle**' yi seçin, ardından alt ağ **adı** Için **DMZ** girin ve **alt ağ adres aralığı** için **10.0.2.0/24**

9. **Add (Ekle)** seçeneğini belirleyin.

10. **Güvenlik** sekmesini seçin veya sayfanın altındaki **İleri: güvenlik** düğmesini seçin.

11. **Bastionhost** altında **Etkinleştir**' i seçin. Bu bilgileri girin:

    | Ayar            | Değer                      |
    |--------------------|----------------------------|
    | Savunma adı | **Mybastionhost** girin |
    | AzureBastionSubnet adres alanı | **10.0.3.0/24** girin |
    | Genel IP Adresi | **Yeni oluştur**’u seçin. </br> **Ad** Için **Mybastionıp** girin. </br> **Tamam**’ı seçin. |

12. **Gözden geçir + oluştur** sekmesini seçin ya da **gözden geçir + oluştur** düğmesini seçin.

13. **Oluştur**’u seçin.

## <a name="create-an-nva"></a>NVA oluşturma

Ağ sanal gereçleri (NVA 'lar), Yönlendirme ve güvenlik duvarı iyileştirmesi gibi ağ işlevleriyle yardımcı olan sanal makinelerdir. Bu öğretici, **Windows Server 2019 Datacenter** kullandığınızı varsayar. İsterseniz farklı bir işletim sistemi seçebilirsiniz.

1. Portalın sol üst kısmında **kaynak oluştur**  >  **işlem**  >  **sanal makinesi**' ni seçin. 
   
2. **Sanal makine oluştur**' da **temel bilgiler** sekmesinde değerleri yazın veya seçin:

    | Ayar | Değer                                          |
    |-----------------------|----------------------------------|
    | **Proje ayrıntıları** |  |
    | Abonelik | Azure aboneliğinizi seçin |
    | Kaynak Grubu | **Myresourcegroup** seçin |
    | **Örnek ayrıntıları** |  |
    | Sanal makine adı | **Myvmnva** girin |
    | Region | **(US) Doğu ABD** seçin |
    | Kullanılabilirlik seçenekleri | **Altyapı yedekliliği gerekli değil** ' i seçin |
    | Görüntü | **Windows Server 2019 Datacenter** seçin |
    | Azure Spot örneği | **Hayır** seçin |
    | Boyut | VM boyutunu seçin veya varsayılan ayarı yapın |
    | **Yönetici hesabı** |  |
    | Kullanıcı adı | Kullanıcı adı girin |
    | Parola | Parola girin |
    | Parolayı onayla | Parolayı yeniden girin |
    | **Gelen bağlantı noktası kuralları** |    |
    | Genel gelen bağlantı noktaları | **Hiçbiri** seçeneğini belirtin. |
    |

3. **Ağ** sekmesini seçin veya **Sonraki: diskler**' i ve sonra **İleri: ağ**' ı seçin.
  
4. Ağ sekmesinde, şunu seçin veya girin:

    | Ayar | Değer |
    |-|-|
    | **Ağ arabirimi** |  |
    | Sanal ağ | **MyVirtualNetwork** öğesini seçin. |
    | Alt ağ | **DMZ** seçin |
    | Genel IP | **Hiçbirini** seçme |
    | NIC ağ güvenlik grubu | **Temel** seçin|
    | Ortak gelen bağlantı noktaları ağı | **Hiçbiri** seçeneğini belirtin. |
   
5. **Gözden geçir + oluştur** sekmesini seçin veya sayfanın altındaki mavi **gözden geçir + oluştur** düğmesini seçin.
  
6. Ayarları gözden geçirin ve ardından **Oluştur**' u seçin.

## <a name="create-a-route-table"></a>Yönlendirme tablosu oluşturma

1. [Azure portalı](https://portal.azure.com) menüsünde veya **Giriş** sayfasında **Kaynak oluştur**’u seçin.

2. Arama kutusuna **yol tablosu** girin. Arama sonuçlarında **yol tablosu** göründüğünde, bunu seçin.

3. **Yol tablosu** sayfasında **Oluştur**' u seçin.

4. **Temel bilgiler** sekmesinde **yol tablosu oluştur** ' da aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Proje ayrıntıları** |   |
    | Abonelik | Aboneliğinizi seçin.|
    | Kaynak grubu | **myResourceGroup** öğesini seçin. |
    | **Örnek ayrıntıları** |    |
    | Region | **Doğu ABD**’yi seçin. |
    | Name | **Myroutetablepublic** girin. |
    | Ağ Geçidi yollarını yayma | **Evet**’i seçin. |

    :::image type="content" source="./media/tutorial-create-route-table-portal/create-route-table.png" alt-text="Yol tablosu oluşturun, Azure portal." border="true":::

5. **Gözden geçir + oluştur** sekmesini seçin veya sayfanın altındaki mavi **gözden geçir + oluştur** düğmesini seçin.

## <a name="create-a-route"></a>Yönlendirme oluşturma

1. Yol tablonuzu yönetmek için [Azure Portal](https://portal.azure.com) gidin. **Rota tablolarını** arayın ve seçin.

2. **Myroutetablepublic** yol tablonuzun adını seçin.

3. **Myroutetablepublic** sayfasında, **Ayarlar** bölümünde **rotalar**' ı seçin.

4. Rotalar sayfasında **+ Ekle** düğmesini seçin.

5. **Yol Ekle**' de bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Yönlendirme adı | **Toprivatesubnet** girin |
    | Adres ön eki | **10.0.1.0/24** girin (daha önce oluşturulan **özel** alt ağın adres aralığı) |
    | Sonraki atlama türü | **Sanal gereç**’i seçin. |
    | Sonraki atlama adresi | **10.0.2.4** girin ( **DMZ** alt ağının adres aralığı içinde bir adres) |

6. **Tamam**’ı seçin.

## <a name="associate-a-route-table-to-a-subnet"></a>Yönlendirme tablosunu bir alt ağ ile ilişkilendirme

1. Sanal ağınızı yönetmek için [Azure Portal](https://portal.azure.com) gidin. **Sanal ağları** arayın ve seçin.

2. Sanal ağ **myVirtualNetwork** adını seçin.

3. **MyVirtualNetwork** sayfasında, **Ayarlar** bölümünde, **alt ağlar**' ı seçin.

4. Sanal ağın alt ağ listesinde **genel**' i seçin.

5. **Yol tablosu**' nda, **Myroutetablepublic** oluşturduğunuz yol tablosunu seçin. 

6. Rota tablonuzu **ortak** alt ağla Ilişkilendirmek için **Kaydet** ' i seçin.

    :::image type="content" source="./media/tutorial-create-route-table-portal/associate-route-table.png" alt-text="Rota tablosunu, alt ağ listesini, sanal ağı, Azure portal ilişkilendirin." border="true":::

## <a name="turn-on-ip-forwarding"></a>IP iletmeyi aç

Ardından, **Myvmnva adlı** yeni NVA sanal MAKINENIZ için IP iletmeyi açın. Azure, **Myvmnva**'ya ağ trafiği gönderdiğinde, trafik farklı bir IP adresine gidiyor Ise, IP iletimi trafiği doğru konuma gönderir.

1. VM 'nizi yönetmek için [Azure Portal](https://portal.azure.com) gidin. **Sanal makineleri** arayın ve seçin.

2. **Myvmnva** sanal makinenizin adını seçin.

3. **Myvmnva** Genel Bakış sayfasında, **Ayarlar**' da **ağ**' ı seçin.

4. **Myvmnva**'nın ağ sayfasında, **ağ arabirimi**' nin **yanındaki ağ arabirimini** seçin.  Arabirimin adı **myvmnva** ile başlar.

    :::image type="content" source="./media/tutorial-create-route-table-portal/virtual-machine-networking.png" alt-text="Ağ iletişimi, ağ sanal gereci (NVA) sanal makinesi (VM), Azure portal" border="true":::

5. Ağ arabirimine Genel Bakış sayfasında, **Ayarlar**' da **IP yapılandırması**' nı seçin.

6. **IP yapılandırması** sayfasında **IP iletmeyi** **etkin** olarak ayarlayın ve ardından **Kaydet**' i seçin.

    :::image type="content" source="./media/tutorial-create-route-table-portal/enable-ip-forwarding.png" alt-text="IP iletmeyi etkinleştirme" border="true":::

## <a name="create-public-and-private-virtual-machines"></a>Genel ve özel sanal makineleri oluşturma

Sanal ağda ortak bir VM ve özel bir VM oluşturun. Daha sonra, Azure 'un **genel** alt ağ trafiğini NVA aracılığıyla **özel** alt ağa yönlendirdiğini görmek için bu uygulamaları kullanacaksınız.


### <a name="public-vm"></a>Ortak VM

1. Portalın sol üst kısmında **kaynak oluştur**  >  **işlem**  >  **sanal makinesi**' ni seçin. 
   
2. **Sanal makine oluştur**' da **temel bilgiler** sekmesinde değerleri yazın veya seçin:

    | Ayar | Değer                                          |
    |-----------------------|----------------------------------|
    | **Proje ayrıntıları** |  |
    | Abonelik | Azure aboneliğinizi seçin |
    | Kaynak Grubu | **Myresourcegroup** seçin |
    | **Örnek ayrıntıları** |  |
    | Sanal makine adı | **MyVMPublic** girin |
    | Region | **(US) Doğu ABD** seçin |
    | Kullanılabilirlik seçenekleri | **Altyapı yedekliliği gerekli değil** ' i seçin |
    | Görüntü | **Windows Server 2019 Datacenter** seçin |
    | Azure Spot örneği | **Hayır** seçin |
    | Boyut | VM boyutunu seçin veya varsayılan ayarı yapın |
    | **Yönetici hesabı** |  |
    | Kullanıcı adı | Kullanıcı adı girin |
    | Parola | Parola girin |
    | Parolayı onayla | Parolayı yeniden girin |
    | **Gelen bağlantı noktası kuralları** |    |
    | Genel gelen bağlantı noktaları | **Hiçbiri** seçeneğini belirtin. |
    |

3. **Ağ** sekmesini seçin veya **Sonraki: diskler**' i ve sonra **İleri: ağ**' ı seçin.
  
4. Ağ sekmesinde, şunu seçin veya girin:

    | Ayar | Değer |
    |-|-|
    | **Ağ arabirimi** |  |
    | Sanal ağ | **MyVirtualNetwork** öğesini seçin. |
    | Alt ağ | **Ortak** seçin |
    | Genel IP | **Hiçbirini** seçme |
    | NIC ağ güvenlik grubu | **Temel** seçin|
    | Ortak gelen bağlantı noktaları ağı | **Hiçbiri** seçeneğini belirtin. |
   
5. **Gözden geçir + oluştur** sekmesini seçin veya sayfanın altındaki mavi **gözden geçir + oluştur** düğmesini seçin.
  
6. Ayarları gözden geçirin ve ardından **Oluştur**' u seçin.

### <a name="private-vm"></a>Özel VM

1. Portalın sol üst kısmında **kaynak oluştur**  >  **işlem**  >  **sanal makinesi**' ni seçin. 
   
2. **Sanal makine oluştur**' da **temel bilgiler** sekmesinde değerleri yazın veya seçin:

    | Ayar | Değer                                          |
    |-----------------------|----------------------------------|
    | **Proje ayrıntıları** |  |
    | Abonelik | Azure aboneliğinizi seçin |
    | Kaynak Grubu | **Myresourcegroup** seçin |
    | **Örnek ayrıntıları** |  |
    | Sanal makine adı | **MyVMPrivate** girin |
    | Region | **(US) Doğu ABD** seçin |
    | Kullanılabilirlik seçenekleri | **Altyapı yedekliliği gerekli değil** ' i seçin |
    | Görüntü | **Windows Server 2019 Datacenter** seçin |
    | Azure Spot örneği | **Hayır** seçin |
    | Boyut | VM boyutunu seçin veya varsayılan ayarı yapın |
    | **Yönetici hesabı** |  |
    | Kullanıcı adı | Kullanıcı adı girin |
    | Parola | Parola girin |
    | Parolayı onayla | Parolayı yeniden girin |
    | **Gelen bağlantı noktası kuralları** |    |
    | Genel gelen bağlantı noktaları | **Hiçbiri** seçeneğini belirtin. |
    |

3. **Ağ** sekmesini seçin veya **Sonraki: diskler**' i ve sonra **İleri: ağ**' ı seçin.
  
4. Ağ sekmesinde, şunu seçin veya girin:

    | Ayar | Değer |
    |-|-|
    | **Ağ arabirimi** |  |
    | Sanal ağ | **MyVirtualNetwork** öğesini seçin. |
    | Alt ağ | **Özel** ' i seçin |
    | Genel IP | **Hiçbirini** seçme |
    | NIC ağ güvenlik grubu | **Temel** seçin|
    | Ortak gelen bağlantı noktaları ağı | **Hiçbiri** seçeneğini belirtin. |
   
5. **Gözden geçir + oluştur** sekmesini seçin veya sayfanın altındaki mavi **gözden geçir + oluştur** düğmesini seçin.
  
6. Ayarları gözden geçirin ve ardından **Oluştur**' u seçin.

## <a name="route-traffic-through-an-nva"></a>Trafiği NVA üzerinden yönlendirme

### <a name="sign-in-to-private-vm"></a>Özel VM 'de oturum açma

1. Özel VM 'nizi yönetmek için [Azure Portal](https://portal.azure.com) gidin. **Sanal makineleri** arayın ve seçin.

2. **MyVmPrivate** özel sanal makinenizin adını seçin.

3. VM menü çubuğunda **Bağlan**' ı ve sonra da ' yi **seçin.**

4. **Bağlan** sayfasında mavi **kullanımı** savunma düğmesini seçin.

5. Savunma sayfasında **,** daha önce sanal makine için oluşturduğunuz Kullanıcı adını ve parolayı girin.

6. **Bağlan**’ı seçin.

### <a name="configure-firewall"></a>Güvenlik duvarını yapılandırma

Sonraki bir adımda, yönlendirmeyi test etmek için izleme yönlendirme aracını kullanacaksınız. İzleme yolu, Windows güvenlik duvarının varsayılan olarak engellediği Internet Denetim Iletisi Protokolü 'Nü (ıCMP) kullanır. 

Windows Güvenlik Duvarı üzerinden ıCMP 'yi etkinleştirin.

1. **MyVMPrivate**'in savunma bağlantısında, PowerShell 'i yönetici ayrıcalıklarıyla açın.

2. Şu komutu girin:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Bu öğreticide yönlendirmeyi test etmek için izleme rotası kullanıyorsunuz. Üretim ortamları için Windows Güvenlik Duvarı üzerinden ıCMP 'ye izin vermeyi önermiyoruz.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>MyVMNVA içinde IP iletmeyi aç

Azure kullanarak VM 'nin ağ arabirimi için [IP iletmeyi açtınız](#turn-on-ip-forwarding) . Sanal makinenin işletim sistemi de ağ trafiğini iletmektir. 

**Myvmnva** için IP iletmeyi Bu komutlarla açın.

1. **MyVMPrivate** VM 'deki PowerShell 'Den, **Myvmnva** VM 'ye bir Uzak Masaüstü açın:

    ```powershell
    mstsc /v:myvmnva
    ```

2. **Myvmnva** VM üzerindeki POWERSHELL 'den IP iletmeyi açmak için şu komutu girin:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

3. **Myvmnva**'yı yeniden başlatın.

    ```powershell
    Restart-Computer
    ```

4. **Myvmnva** yeniden başlatıldıktan sonra, **myVMPublic** için bir Uzak Masaüstü oturumu oluşturun. 
    
    Hala **myVMPrivate**'e bağlıyken PowerShell 'i açın ve şu komutu çalıştırın:

    ```powershell
    mstsc /v:myvmpublic
    ```
5. **MyVMPublic** uzak masaüstünde PowerShell ' i açın.

6. Şu komutu girerek Windows Güvenlik Duvarı üzerinden ıCMP 'yi etkinleştirin:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>Ağ trafiğinin yönlendirilmesini test etme

İlk olarak, ağ trafiğinin **myVMPublic** 'ten **myVMPrivate**'ye yönlendirilmesini test edelim.

1. **MyVMPublic** üzerinde PowerShell 'den şu komutu girin:

    ```powershell
    tracert myvmprivate
    ```

    Yanıt bu örneğe benzer:

    ```powershell
    Tracing route to myvmprivate.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

      1     1 ms     *        2 ms  myvmnva.internal.cloudapp.net [10.0.2.4]
      2     2 ms     1 ms     1 ms  myvmprivate.internal.cloudapp.net [10.0.1.4]

    Trace complete.
    ```

    * İlk atlamanın, **Myvmnva 'nın** özel IP adresi olan 10.0.2.4 'e olduğunu görebilirsiniz. 

    * İkinci atlama **myVMPrivate**: 10.0.1.4 özel IP adresidir. 

    Daha önce, yolu **Myroutetablepublic** yol tablosuna eklediniz ve bunu *ortak* alt ağla ilişkilendirdiniz. Azure, trafiği NVA üzerinden gönderdi ve doğrudan *özel* alt ağa değil.

2. **MyVMPublic** için Uzak Masaüstü oturumunu kapatın. Bu, hala **myVMPrivate**'e bağlı kalır.

3. PowerShell 'i **myVMPrivate** üzerinde açın, şu komutu girin:

    ```powershell
    tracert myvmpublic
    ```

    Bu komut, ağ trafiğinin *myVmPrivate* VM 'den *myVmPublic* VM 'ye yönlendirilmesini sınar. Yanıt bu örneğe benzer:

    ```cmd
    Tracing route to myvmpublic.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

      1     1 ms     1 ms     1 ms  myvmpublic.internal.cloudapp.net [10.0.0.4]

    Trace complete.
    ```

    Azure 'un trafiği doğrudan *myVMPrivate* 'ten *myVMPublic*'e yönlendirdiğini görebilirsiniz. Varsayılan olarak Azure, trafiği doğrudan alt ağlar arasında yönlendirir.

4. **MyVMPrivate** için savunma oturumunu kapatın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak grubuna artık ihtiyaç duyulmadığında **Myresourcegroup** ve içerdiği tüm kaynakları silin:

1. Kaynak grubunuzu yönetmek için [Azure Portal](https://portal.azure.com) gidin. **Kaynak gruplarını** arayın ve seçin.

2. Kaynak grubunuzun adını seçin **Myresourcegroup**.

3. **Kaynak grubunu sil**'i seçin.

4. Onay iletişim kutusunda, **kaynak grubu adını yazmak** Için **myresourcegroup** yazın ve ardından **Sil**' i seçin. 


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

* Bir rota tablosu oluşturup bir alt ağla ilişkilendirdi.
* Trafiği ortak bir alt ağdan özel bir alt ağa yönlendirmeyen basit bir NVA oluşturuldu. 

[Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking)'nden, çok sayıda kullanışlı ağ işlevi sağlayan, önceden yapılandırılmış farklı NVA 'lar dağıtabilirsiniz. 

Yönlendirme hakkında daha fazla bilgi için bkz. [Yönlendirmeye genel bakış](virtual-networks-udr-overview.md) ve [Yönlendirme tablosunu yönetme](manage-route-table.md).

Bir sanal ağdaki ağ trafiğini filtrelemek için, bkz:
> [!div class="nextstepaction"]
> [Öğretici: Azure portal kullanarak ağ trafiğini ağ güvenlik grubuyla filtreleme](tutorial-filter-network-traffic.md)
