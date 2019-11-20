---
title: Yönlendirme ağ trafiği-öğretici-Azure portal
titlesuffix: Azure Virtual Network
description: Bu öğreticide, Azure portalını kullanarak bir yönlendirme tablosu ile ağ trafiğini yönlendirme hakkında bilgi edineceksiniz.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/12/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 7e7a01b7fdc1a508fa19397900f8fd4f52d49c53
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164009"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Öğretici: Azure portalını kullanarak bir yönlendirme tablosu ile ağ trafiğini yönlendirme

Azure, varsayılan olarak bir sanal ağ içindeki tüm alt ağlar arasındaki trafiği yönlendirir. Azure’ın varsayılan yönlendirmesini geçersiz kılmak için kendi yönlendirmelerinizi oluşturabilirsiniz. Örneğin, bir ağ sanal gereci üzerinden alt ağlar arasındaki trafiği yönlendirmek isteyebilirsiniz. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Yönlendirme tablosu oluşturma
> * Yönlendirme oluşturma
> * Birden fazla alt ağa sahip bir sanal ağ oluşturma
> * Yönlendirme tablosunu bir alt ağ ile ilişkilendirme
> * Trafiği yönlendiren bir NVA oluşturma
> * Sanal makineleri (VM) farklı alt ağlara dağıtma
> * NVA aracılığıyla trafiği bir alt ağdan başka birine yönlendirme

Tercih ederseniz, [Azure CLI](tutorial-create-route-table-cli.md) veya [Azure PowerShell](tutorial-create-route-table-powershell.md)kullanarak bu öğreticiyi izleyebilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="sign-in-to-azure"></a>Azure'da oturum açın

[Azure Portal](https://portal.azure.com)’ında oturum açın.

## <a name="create-a-route-table"></a>Yönlendirme tablosu oluşturma

1. Ekranın sol üst tarafında, **kaynak oluştur** > **ağ** > **yol tablosu**' nu seçin.

1. **Yol tablosu oluştur**' da bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Adı | *Myroutetablepublic*girin. |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **Yeni oluştur**' u seçin, *Myresourcegroup*girin ve *Tamam*' ı seçin. |
    | Konum | Varsayılan **Doğu ABD**bırakın.
    | BGP yol yayma | Varsayılanı **etkin**bırakın. |

1. **Oluştur**'u seçin.

## <a name="create-a-route"></a>Yönlendirme oluşturma

1. Portalın arama çubuğunda *Myroutetablepublic*yazın.

1. Arama sonuçlarında **myRouteTablePublic** göründüğünde seçin.

1. **Ayarlar**altında **Myroutetablepublic** içinde, **rotalar** >  **+ Ekle**' yi seçin.

    ![Yönlendirme ekleme](./media/tutorial-create-route-table-portal/add-route.png)

1. **Yol Ekle**' de bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Yönlendirme adı | *Toprivatesubnet*girin. |
    | Adres ön eki | *10.0.1.0/24*girin. |
    | Sonraki atlama türü | **Sanal gereç**’i seçin. |
    | Sonraki atlama adresi | *10.0.2.4*girin. |

1. **Tamam**’ı seçin.

## <a name="associate-a-route-table-to-a-subnet"></a>Yönlendirme tablosunu bir alt ağ ile ilişkilendirme

Bir rota tablosunu bir alt ağ ile ilişkilendirebilmeniz için önce bir sanal ağ ve alt ağ oluşturmanız gerekir.

### <a name="create-a-virtual-network"></a>Sanal ağ oluşturun

1. Ekranın sol üst kısmında, **kaynak oluştur** > **ağ** > **sanal ağ**' ı seçin.

1. **Sanal ağ oluştur**' da bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Adı | *MyVirtualNetwork*girin. |
    | Adres alanı | *10.0.0.0/16*girin. |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | ***Mevcut*** > **myresourcegroup**Seç öğesini seçin. |
    | Konum | Varsayılan **Doğu ABD**bırakın. |
    | Alt ağ adı | *Genel*girin. |
    | Alt Ağ - Adres aralığı | *10.0.0.0/24*girin. |

1. Varsayılan değerleri bırakın ve **Oluştur**' u seçin.

### <a name="add-subnets-to-the-virtual-network"></a>Sanal ağa alt ağlar ekleyin

1. Portalın arama çubuğunda *myVirtualNetwork*girin.

1. Arama sonuçlarında **myVirtualNetwork** göründüğünde seçin.

1. **MyVirtualNetwork**' de, **Ayarlar**altında **alt ağlar** >  **+ alt ağ**' ı seçin.

    ![Alt ağ ekleme](./media/tutorial-create-route-table-portal/add-subnet.png)

1. **Alt ağ ekle**' de şu bilgileri girin:

    | Ayar | Değer |
    | ------- | ----- |
    | Adı | *Özel*girin. |
    | Adres alanı | *10.0.1.0/24*girin. |

1. Diğer varsayılan ayarları olduğu gibi bırakın ve **Tamam**’ı seçin.

1. **+ Alt ağ** ' ı yeniden seçin. Bu kez, bu bilgileri girin:

    | Ayar | Değer |
    | ------- | ----- |
    | Adı | *DMZ*girin. |
    | Adres alanı | *10.0.2.0/24*girin. |

1. Son zamanı beğendiniz, geri kalan varsayılan değerleri bırakın ve **Tamam**' ı seçin.

    Azure üç alt ağı gösterir: **genel**, **özel**ve **DMZ**.

### <a name="associate-myroutetablepublic-to-your-public-subnet"></a>MyRouteTablePublic 'i genel alt ağınızla ilişkilendirin

1. **Ortak**seçeneğini belirleyin.

1. **Ortak**bölümünde, **yol tablosu** > **Myroutetablepublic** > **Kaydet**' i seçin.

    ![Yönlendirme tablosunu ilişkilendirme](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="create-an-nva"></a>NVA oluşturma

NVA 'lar, Yönlendirme ve güvenlik duvarı iyileştirmesi gibi ağ işlevleriyle yardım eden sanal makinelerdir. İsterseniz farklı bir işletim sistemi seçebilirsiniz. Bu öğretici, **Windows Server 2016 Datacenter**kullandığınızı varsayar.

1. Ekranın sol üst kısmında **Windows Server 2016 Datacenter** > **Işlem** > **kaynak oluştur** ' u seçin.

1. **Sanal makine oluşturma-temel bilgiler**bölümünde, bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **PROJE AYRıNTıLARı** | |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **Myresourcegroup**öğesini seçin. |
    | **ÖRNEK AYRıNTıLARı** |  |
    | Sanal makine adı | *Myvmnva*girin. |
    | Bölge | **Doğu ABD**’yi seçin. |
    | Kullanılabilirlik seçenekleri | Varsayılan **altyapı yedekliliği gerekli değildir**. |
    | Görüntü | Varsayılan **Windows Server 2016 veri merkezini**bırakın. |
    | Boyut | Varsayılan **Standart DS1 v2**' i bırakın. |
    | **YÖNETICI HESABı** |  |
    | Kullanıcı adı | Seçtiğiniz bir kullanıcı adını girin. |
    | Parola | Seçtiğiniz bir parolayı girin. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm) karşılamalıdır.|
    | Parolayı Onayla | Parolayı yeniden girin. |
    | **GELEN BAĞLANTı NOKTASı KURALLARı** |  |
    | Ortak gelen bağlantı noktaları | Varsayılanı **yok**olarak bırakın.
    | **TASARRUF EDIN** |  |
    | Zaten bir Windows lisansınız var mı? | Varsayılan **Hayır**olarak bırakın. |

1. **İleri ' yi seçin: diskler**.

1. **Sanal makine oluşturma-diskler**' de, gereksinimlerinize uygun olan ayarları seçin.

1. **İleri ' yi seçin: ağ**.

1. **Sanal makine oluşturma-ağ oluşturma**bölümünde şu bilgileri seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Sanal ağ | Varsayılan **myVirtualNetwork**bırakın. |
    | Alt ağ | **DMZ (10.0.2.0/24)** öğesini seçin. |
    | Genel IP | **Hiçbiri**' ni seçin. Genel bir IP adresine ihtiyacınız yoktur. VM, internet üzerinden bağlanmaz.|

1. Varsayılan değerleri bırakın ve **İleri: yönetim**' i seçin.

1. **Sanal makine yönetimi oluştur**bölümünde, **Tanılama depolama hesabı**için **Yeni oluştur**' u seçin.

1. **Depolama hesabı oluştur**' da bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Adı | *Mynvastorageaccount*girin. |
    | Hesap türü | Varsayılan **depolama alanını (genel amaçlı v1)** bırakın. |
    | Performans | Varsayılan **Standart**bırakın. |
    | Çoğaltma | Varsayılan **yerel olarak yedekli depolamayı (LRS)** bırakın.

1. **Tamam**’ı seçin

1. **İncele ve oluştur**’u seçin. **Gözden geçir + oluştur** sayfasına götürülürsünüz ve Azure yapılandırmanızı doğrular.

1. **Doğrulamanın geçtiğini**gördüğünüzde **Oluştur**' u seçin.

    Sanal makinenin oluşturulması birkaç dakika sürer. Azure VM 'yi oluşturmaya bitene kadar devam etmeyin. **Dağıtım** çalışma sayfası, dağıtım ayrıntılarını gösterecektir.

1. VM 'niz hazırsanız **Kaynağa Git**' i seçin.

## <a name="turn-on-ip-forwarding"></a>IP iletmeyi aç

*Myvmnva*için IP iletmeyi açın. Azure, *Myvmnva*'ya ağ trafiği gönderdiğinde, trafik farklı bir IP adresine gidiyor Ise, IP iletimi trafiği doğru konuma gönderir.

1. **Myvmnva**'Da, **Ayarlar**altında **ağ**' ı seçin.

1. **Myvmnva123**öğesini seçin. Bu, VM 'niz için oluşturulan Azure ağ arabirimidir. Sizin için benzersiz hale getirmek için bir sayı dizesi olacaktır.

    ![VM ağı](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. **Ayarlar**altında **IP yapılandırması**' nı seçin.

1. **Myvmnva123-IP yapılandırmalarında**, **IP iletimi**için **etkin** ' i seçin ve ardından **Kaydet**' i seçin.

    ![IP iletmeyi etkinleştirme](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Ortak ve özel sanal makineler oluşturma

Sanal ağda ortak bir VM ve özel bir VM oluşturun. Daha sonra, Azure 'un *genel* alt ağ trafiğini NVA aracılığıyla *özel* alt ağa yönlendirdiğini görmek için bu uygulamaları kullanacaksınız.

[BIR NVA oluşturma](#create-an-nva)1-12 için tüm adımları izleyin. Aynı ayarların çoğunu kullanın. Bu değerler, farklı olması gereken olanlardır:

| Ayar | Değer |
| ------- | ----- |
| **ORTAK VM** | |
| TEMEL BILGILERI |  |
| Sanal makine adı | *MyVmPublic*girin. |
| IŞLEMLERI | |
| Alt ağ | **Public (10.0.0.0/24)** seçeneğini belirleyin. |
| Genel IP adresi | Varsayılanı kabul edin. |
| Ortak gelen bağlantı noktaları | **Seçili bağlantı noktalarına Izin ver**' i seçin. |
| Gelen bağlantı noktalarını seçin | **Http** ve **RDP**' yi seçin. |
| YÖNETME | |
| Tanılama depolama hesabı | Varsayılan **mynvastorageaccount**değerini bırakın. |
| **ÖZEL VM** | |
| TEMEL BILGILERI |  |
| Sanal makine adı | *MyVmPrivate*girin. |
| IŞLEMLERI | |
| Alt ağ | **Özel (10.0.1.0/24)** seçeneğini belirleyin. |
| Genel IP adresi | Varsayılanı kabul edin. |
| Ortak gelen bağlantı noktaları | **Seçili bağlantı noktalarına Izin ver**' i seçin. |
| Gelen bağlantı noktalarını seçin | **Http** ve **RDP**' yi seçin. |
| YÖNETME | |
| Tanılama depolama hesabı | Varsayılan **mynvastorageaccount**değerini bırakın. |

Azure *myVmPublic* VM’yi oluştururken *myVmPrivate* VM’yi oluşturabilirsiniz. Azure her iki VM oluşturmayı tamamlayana kadar adımların geri kalanına devam etmeyin.

## <a name="route-traffic-through-an-nva"></a>Trafiği NVA üzerinden yönlendirme

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Uzak Masaüstü üzerinden myVmPrivate 'de oturum açın

1. Portalın arama çubuğunda *myVmPrivate*girin.

1. Arama sonuçlarında **myVmPrivate** VM göründüğünde seçin.

1. *MyVmPrivate* VM 'ye Uzak Masaüstü bağlantısı oluşturmak için **Bağlan** ' ı seçin.

1. **Sanal makineye bağlan**' da, **RDP dosyasını indir**' i seçin. Azure bir Uzak Masaüstü Protokolü ( *. rdp*) dosyası oluşturur ve bilgisayarınıza indirir.

1. İndirilen *. rdp* dosyasını açın.

    1. İstendiğinde **Bağlan**’ı seçin.

    1. Özel VM oluştururken belirttiğiniz kullanıcı adını ve parolayı girin.

    1. Özel VM kimlik bilgilerini kullanmak için **farklı bir hesap kullanmak** > **daha fazla seçenek** belirlemeniz gerekebilir.

1. **Tamam**’ı seçin.

    Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz.

1. SANAL makineye bağlanmak için **Evet** ' i seçin.

### <a name="enable-icmp-through-the-windows-firewall"></a>Windows Güvenlik Duvarı üzerinden ıCMP 'yi etkinleştirme

Sonraki bir adımda, yönlendirmeyi test etmek için izleme yönlendirme aracını kullanacaksınız. İzleme yolu, Windows güvenlik duvarının varsayılan olarak engellediği Internet Denetim Iletisi Protokolü 'Nü (ıCMP) kullanır. Windows Güvenlik Duvarı üzerinden ıCMP 'yi etkinleştirin.

1. *MyVmPrivate*uzak masaüstünde PowerShell ' i açın.

1. Şu komutu girin:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Bu öğreticide yönlendirmeyi test etmek için izleme rotası kullanıyorsunuz. Üretim ortamları için Windows Güvenlik Duvarı üzerinden ıCMP 'ye izin vermeyi önermiyoruz.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>MyVmNva içinde IP iletmeyi aç

Azure kullanarak VM 'nin ağ arabirimi için [IP iletmeyi açtınız](#turn-on-ip-forwarding) . VM 'nin işletim sistemi de ağ trafiğini iletmektir. Bu komutlarla *Myvmnva* VM 'nin işletim SISTEMI için IP iletmeyi açın.

1. *MyVmPrivate* VM 'deki bir komut Isteminden, *Myvmnva* VM 'ye bir Uzak Masaüstü açın:

    ```cmd
    mstsc /v:myvmnva
    ```

1. *Myvmnva*'daki POWERSHELL 'den IP iletmeyi açmak için şu komutu girin:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. *Myvmnva* VM 'sini yeniden başlatın. Görev çubuğundan **Başlat düğmesini** > **güç düğmesine**, **diğer (planlı)**  > **devam**' ı seçin.

    Bu ayrıca uzak masaüstü oturumunun bağlantısını keser.

1. *Myvmnva* VM yeniden başlatıldıktan sonra, *myVmPublic* VM 'ye bir Uzak Masaüstü oturumu oluşturun. *MyVmPrivate* VM 'sine hala bağlıyken, bir komut istemi açın ve şu komutu çalıştırın:

    ```cmd
    mstsc /v:myVmPublic
    ```
1. *MyVmPublic*uzak masaüstünde PowerShell ' i açın.

1. Şu komutu girerek Windows Güvenlik Duvarı üzerinden ıCMP 'yi etkinleştirin:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>Ağ trafiğinin yönlendirilmesini test etme

İlk olarak, ağ trafiğinin *myVmPublic* VM 'den *myVmPrivate* VM 'ye yönlendirilmesini test edelim.

1. *MyVmPublic* VM 'deki PowerShell 'den şu komutu girin:

    ```powershell
    tracert myVmPrivate
    ```

    Yanıt bu örneğe benzer:

    ```powershell
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4

    Trace complete.
    ```

    İlk atlamanın 10.0.2.4 olduğunu görebilirsiniz. Bu, Nvaın özel IP adresidir. İkinci atlama *myVmPrivate* VM 'nın özel IP adresidir: 10.0.1.4. Daha önce, yolu *Myroutetablepublic* yol tablosuna eklediniz ve bunu *ortak* alt ağla ilişkilendirdiniz. Sonuç olarak, Azure trafiği NVA üzerinden gönderdi ve doğrudan *özel* alt ağa değil.

1. *myVmPublic* VM ile uzak masaüstü oturumunu kapatın. *myVmPrivate* VM bağlantınız hala açıktır.

1. *MyVmPrivate* VM 'deki bir komut isteminden şu komutu girin:

    ```cmd
    tracert myVmPublic
    ```

    Ağ trafiğinin *myVmPrivate* VM 'den *myVmPublic* VM 'ye yönlendirilmesini sınar. Yanıt bu örneğe benzer:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Azure Route trafiğini doğrudan *myVmPrivate* VM 'den *myVmPublic* VM 'sine bakabilirsiniz. Varsayılan olarak Azure, trafiği doğrudan alt ağlar arasında yönlendirir.

1. *myVmPrivate* VM ile uzak masaüstü oturumunu kapatın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, kaynak grubunu ve içerdiği tüm kaynakları silin:

1. Portalın arama çubuğunda *Myresourcegroup*yazın.

1. Arama sonuçlarında **myResourceGroup** seçeneğini gördüğünüzde bunu seçin.

1. **Kaynak grubunu sil**'i seçin.

1. **KAYNAK GRUBU ADINI YAZIN:** için *myResourceGroup* girin ve **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide bir yönlendirme tablosu oluşturup bir alt ağ ile ilişkilendirdiniz. Bir genel alt ağdan özel alt ağa trafiği yönlendiren basit bir NVA oluşturdunuz. Bunu nasıl yapacağınızı bildiğinize göre, [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking)'nden önceden yapılandırılmış farklı NVA 'lar dağıtabilirsiniz. Yararlı bulabileceğiniz birçok ağ işlevini çalıştırır. Yönlendirme hakkında daha fazla bilgi için bkz. [Yönlendirmeye genel bakış](virtual-networks-udr-overview.md) ve [Yönlendirme tablosunu yönetme](manage-route-table.md).

Bir sanal ağ içinde birçok Azure kaynağı dağıtabilmeniz sırasında Azure, bazı PaaS hizmetleri için bir sanal ağa kaynak dağıtamaz. Bazı Azure PaaS hizmetlerinin kaynaklarına erişimi kısıtlamak mümkündür. Kısıtlama, ancak yalnızca bir sanal ağ alt ağından gelen trafik olmalıdır. Azure PaaS kaynaklarına erişimi kısıtlama hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [PaaS kaynaklarına ağ erişimini kısıtlama](tutorial-restrict-network-access-to-resources.md)
