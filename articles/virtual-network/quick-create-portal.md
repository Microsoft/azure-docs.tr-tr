---
title: 'Hızlı başlangıç: sanal ağ oluşturma-Azure portal'
titleSuffix: Azure Virtual Network
description: Bu hızlı başlangıçta, Azure portal kullanarak bir sanal ağ oluşturmayı öğrenin.
author: KumudD
ms.author: kumud
ms.date: 03/17/2021
ms.topic: quickstart
ms.service: virtual-network
ms.workload: infrastructure
ms.tgt_pltfrm: virtual-network
ms.devlang: na
tags:
- azure-resource-manager
ms.custom:
- mode-portal
ms.openlocfilehash: 43c45b43084656a45d2509ee2c7a4376cdc7c052
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531187"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Hızlı başlangıç: Azure portalını kullanarak bir sanal ağ oluşturma

Bu hızlı başlangıçta, Azure portal kullanarak bir sanal ağ oluşturmayı öğreneceksiniz. İki sanal makine (VM) dağıtırsınız. Daha sonra, VM 'Ler arasında güvenli bir şekilde iletişim kurabilir ve internet 'ten VM 'lere bağlanırsınız. Bir sanal ağ, Azure 'daki özel ağınız için temel yapı taşdır. VM 'Ler gibi Azure kaynaklarının birbirleriyle ve internet ile güvenli bir şekilde iletişim kurmasına olanak sağlar.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure portalında](https://portal.azure.com) oturum açın.

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

    :::image type="content" source="./media/quick-create-portal/create-virtual-network.png" alt-text="Sanal ağ Azure portal oluştur" border="true":::

5. **IP adresleri** sekmesini seçin veya sayfanın altındaki **Sonraki: IP adresleri** düğmesini seçin.

6. **IPv4 adres alanı**' nda, mevcut adres alanını seçip **10.1.0.0/16** olarak değiştirin.

7. + Alt ağ **Ekle**' yi seçin ve alt ağ **adres aralığı** için **alt ağ adı** için **mysubnet** ve **10.1.0.0/24** girin.

8. **Add (Ekle)** seçeneğini belirleyin.

9. **Güvenlik** sekmesini seçin veya sayfanın altındaki **İleri: güvenlik** düğmesini seçin.

10. **Bastionhost** altında **Etkinleştir**' i seçin. Bu bilgileri girin:

    | Ayar            | Değer                      |
    |--------------------|----------------------------|
    | Savunma adı | **Mybastionhost** girin |
    | AzureBastionSubnet adres alanı | **10.1.1.0/24** girin |
    | Genel IP Adresi | **Yeni oluştur**’u seçin. </br> **Ad** Için **Mybastionıp** girin. </br> **Tamam**’ı seçin. |

11. **Gözden geçir + oluştur** sekmesini seçin ya da **gözden geçir + oluştur** düğmesini seçin.

12. **Oluştur**’u seçin.

## <a name="create-virtual-machines"></a>Sanal makineler oluşturma

Sanal ağ üzerinde iki sanal makine oluşturun:

### <a name="create-the-first-vm"></a>Birinci sanal makineyi oluşturma

1. Portalın sol üst kısmında **kaynak oluştur**  >  **işlem**  >  **sanal makinesi**' ni seçin. 
   
2. **Sanal makine oluştur**' da **temel bilgiler** sekmesinde değerleri yazın veya seçin:

    | Ayar | Değer                                          |
    |-----------------------|----------------------------------|
    | **Proje ayrıntıları** |  |
    | Abonelik | Azure aboneliğinizi seçin |
    | Kaynak Grubu | **Myresourcegroup** seçin |
    | **Örnek ayrıntıları** |  |
    | Sanal makine adı | **MyVM1** girin |
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
    | Sanal ağ | **Myvnet**' i seçin. |
    | Alt ağ | **mySubnet** öğesini seçin |
    | Genel IP | **Hiçbirini** seçme |
    | NIC ağ güvenlik grubu | **Temel** seçin|
    | Ortak gelen bağlantı noktaları ağı | **Hiçbiri** seçeneğini belirtin. |
   
5. **Gözden geçir + oluştur** sekmesini seçin veya sayfanın altındaki mavi **gözden geçir + oluştur** düğmesini seçin.
  
6. Ayarları gözden geçirin ve ardından **Oluştur**' u seçin.

### <a name="create-the-second-vm"></a>İkinci sanal makineyi oluşturma

1. Portalın sol üst kısmında **kaynak oluştur**  >  **işlem**  >  **sanal makinesi**' ni seçin. 
   
2. **Sanal makine oluştur**' da **temel bilgiler** sekmesinde değerleri yazın veya seçin:

    | Ayar | Değer                                          |
    |-----------------------|----------------------------------|
    | **Proje ayrıntıları** |  |
    | Abonelik | Azure aboneliğinizi seçin |
    | Kaynak Grubu | **Myresourcegroup** seçin |
    | **Örnek ayrıntıları** |  |
    | Sanal makine adı | **MyVM2** girin |
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
    | Sanal ağ | **Myvnet**' i seçin. |
    | Alt ağ | **mySubnet** öğesini seçin |
    | Genel IP | **Hiçbirini** seçme |
    | NIC ağ güvenlik grubu | **Temel** seçin|
    | Ortak gelen bağlantı noktaları ağı | **Hiçbiri** seçeneğini belirtin. |
   
5. **Gözden geçir + oluştur** sekmesini seçin veya sayfanın altındaki mavi **gözden geçir + oluştur** düğmesini seçin.
  
6. Ayarları gözden geçirin ve ardından **Oluştur**' u seçin.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="connect-to-myvm1"></a>MyVM1 'e bağlanma

1. Özel VM 'nizi yönetmek için [Azure Portal](https://portal.azure.com) gidin. **Sanal makineleri** arayın ve seçin.

2. **MyVM1** özel sanal makinenizin adını seçin.

3. VM menü çubuğunda **Bağlan**' ı ve sonra da ' yi **seçin.**

    :::image type="content" source="./media/quick-create-portal/connect-to-virtual-machine.png" alt-text="Azure ile myVM1 'e bağlanma" border="true":::

4. **Bağlan** sayfasında mavi **kullanımı** savunma düğmesini seçin.

5. Savunma sayfasında **,** daha önce sanal makine için oluşturduğunuz Kullanıcı adını ve parolayı girin.

6. **Bağlan**’ı seçin.

## <a name="communicate-between-vms"></a>Sanal makineler arasında iletişim

1. **MyVM1**'in savunma bağlantısında PowerShell ' i açın.

2. `ping myvm2` yazın.

    Bu çıktıya benzer bir ileti alacaksınız:

    ```powershell
    Pinging myvm2.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.5] with 32 bytes of data:
    Reply from 10.1.0.5: bytes=32 time=3ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.5:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 3ms, Average = 1ms
    ```

3. **MyVM1**'e yönelik savunma bağlantısını kapatın.

4. [MyVM1 'e bağlanma](#connect-to-myvm1)bölümündeki adımları doldurun, ancak **myVM2**'e bağlanın.

5. PowerShell 'i **myVM2** üzerinde açın, girin `ping myvm1` .

    Şöyle bir ileti alacaksınız:

    ```powershell
    Pinging myvm1.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 1ms, Average = 1ms
    ```

7. **MyVM2**'e yönelik savunma bağlantısını kapatın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıçta, varsayılan bir sanal ağ ve iki sanal makine oluşturdunuz. 

İnternet 'ten bir sanal makineye bağlanırsınız ve iki VM arasında güvenli bir şekilde iletişim kurdınız.

Sanal ağ ve VM 'Leri kullanarak işiniz bittiğinde, kaynak grubunu ve içerdiği tüm kaynakları silin:

1. **Myresourcegroup** öğesini arayın ve seçin.

1. **Kaynak grubunu sil**'i seçin.

1. **Kaynak grubu adını yazın** ve **Sil**' i seçmek için **myresourcegroup** girin.

## <a name="next-steps"></a>Sonraki adımlar

Sanal ağ ayarları hakkında daha fazla bilgi edinmek için bkz. [sanal ağ oluşturma, değiştirme veya silme](manage-virtual-network.md).

VM ağı iletişimleri türleri hakkında daha fazla bilgi edinmek için bkz. [ağ trafiğini filtreleme](tutorial-filter-network-traffic.md).
