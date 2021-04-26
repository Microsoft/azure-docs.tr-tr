---
title: Güvenli bağlantı için özel uç nokta oluşturma
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama hizmetine güvenli bağlantı için bir sanal ağda özel uç nokta ayarlayın.
manager: nitinme
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 7445ac5d750ac29d3e6ce466a48e82efd1bcde40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545539"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search"></a>Azure Bilişsel Arama güvenli bağlantısı için özel uç nokta oluşturma

Bu makalede, Internet üzerinden erişilemeyen yeni bir Azure Bilişsel Arama hizmet örneği oluşturmak için Azure portal kullanacaksınız. Daha sonra, aynı sanal ağ içinde bir Azure sanal makinesini yapılandıracaksınız ve özel bir uç nokta aracılığıyla arama hizmetine erişmek için kullanacaksınız.

Özel uç noktalar [Azure özel bağlantısı](../private-link/private-link-overview.md)tarafından ayrı bir hizmet olarak sağlanır. Maliyetler hakkında daha fazla bilgi için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/private-link/).

Azure portal, bu makalede açıklandığı gibi, özel bir uç nokta oluşturabilirsiniz. Alternatif olarak, 2020-03-13, [Azure PowerShell](/powershell/module/az.search)veya [Azure CLI](/cli/azure/search) [REST API yönetim sürümünü](/rest/api/searchmanagement/)de kullanabilirsiniz.

> [!NOTE]
> Hizmet uç noktası özel olduğunda, bazı Portal özellikleri devre dışı bırakılır. Hizmet düzeyi bilgilerini görüntüleyebilir ve yönetebilirsiniz, ancak dizin, Dizin Oluşturucu ve beceri bilgileri güvenlik nedenleriyle gizlidir. Portala alternatif olarak, hizmet içindeki çeşitli bileşenlerle etkileşim kurmak için [vs Code uzantısını](https://aka.ms/vscode-search) kullanabilirsiniz.

## <a name="why-use-a-private-endpoint-for-secure-access"></a>Güvenli erişim için neden özel bir uç nokta kullanılmalıdır?

Azure Bilişsel Arama [Özel uç noktaları](../private-link/private-endpoint-overview.md) , bir sanal ağdaki bir Istemcinin bir [özel bağlantı](../private-link/private-link-overview.md)üzerinden arama dizinindeki verilere güvenli bir şekilde erişmesini sağlar. Özel uç nokta, arama hizmetiniz için [sanal ağ adres alanından](../virtual-network/private-ip-addresses.md) bir IP adresi kullanır. İstemci ile arama hizmeti arasındaki ağ trafiği, sanal ağın ve Microsoft omurga ağındaki özel bir bağlantının üzerinde geçiş yaparken, genel İnternet 'ten etkilenme olasılığını ortadan kaldırır. Özel bağlantıyı destekleyen diğer PaaS hizmetlerinin bir listesi için ürün belgelerindeki [kullanılabilirlik bölümüne](../private-link/private-link-overview.md#availability) bakın.

Arama hizmetiniz için özel uç noktalar şunları yapmanızı sağlar:

- Arama hizmetiniz için genel uç noktasındaki tüm bağlantıları engelleyin.
- Sanal ağ üzerinden veri alımını engellemeyi etkinleştirerek sanal ağ güvenliğini artırın.
- Sanal ağa [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) veya [ExpressRoute](../expressroute/expressroute-locations.md) kullanarak özel eşleme ile bağlanan şirket içi ağlardan arama hizmetinize güvenli bir şekilde bağlanın.

## <a name="create-the-virtual-network"></a>Sanal ağı oluşturma

Bu bölümde, arama hizmetinizin özel uç noktasına erişmek için kullanılacak VM 'yi barındırmak için bir sanal ağ ve alt ağ oluşturacaksınız.

1. Azure Portal giriş sekmesinden **kaynak oluştur**  >  **ağ**  >  **sanal ağ**' ı seçin.

1. **Sanal ağ oluştur**' da bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Abonelik | Aboneliğinizi seçin|
    | Kaynak grubu | **Yeni oluştur**' u seçin, *Myresourcegroup* yazın ve **Tamam** ' ı seçin. |
    | Name | *MyVirtualNetwork* girin |
    | Region | İstediğiniz bölgeyi seçin |
    |||

1. Geri kalan ayarlar için varsayılan değerleri bırakın. **Gözden geçir + oluştur** ve sonra **Oluştur** ' a tıklayın.

## <a name="create-a-search-service-with-a-private-endpoint"></a>Özel bir uç nokta ile arama hizmeti oluşturma

Bu bölümde, özel bir uç nokta ile yeni bir Azure Bilişsel Arama hizmeti oluşturacaksınız. 

1. Azure Portal ekranın sol üst tarafında, **kaynak oluştur**  >  **Web**  >  **Azure bilişsel arama**' nı seçin.

1. **Yeni arama hizmeti temel kavramları** için bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **PROJE AYRıNTıLARı** | |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **myResourceGroup** öğesini seçin. Bu, önceki bölümde oluşturdunuz.|
    | **ÖRNEK AYRıNTıLARı** |  |
    | URL | Benzersiz bir ad girin. |
    | Konum | İstediğiniz bölgeyi seçin. |
    | Fiyatlandırma katmanı | **Fiyatlandırma katmanını Değiştir** ' i seçin ve istediğiniz hizmet katmanını seçin. ( **Ücretsiz** katmanda desteklenmez. **Temel** veya daha yüksek olmalıdır.) |
    |||
  
1. **İleri ' yi seçin: ölçek**.

1. Değerleri varsayılan olarak bırakın ve Ileri ' **yi seçin: ağ**.

1. **Yeni arama hizmeti-ağ**' da, **uç nokta bağlantısı (veriler)** için **özel** ' i seçin.

1. **Yeni arama hizmeti-ağ**' da, **Özel uç nokta** altında **+ Ekle** ' yi seçin. 

1. **Özel uç nokta oluştur**' da bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **myResourceGroup** öğesini seçin. Bu, önceki bölümde oluşturdunuz.|
    | Konum | **Batı ABD** seçin.|
    | Name | *myPrivateEndpoint* değerini girin.  |
    | Hedef alt kaynak | Varsayılan **SearchService**' i bırakın. |
    | **AĞ** |  |
    | Sanal ağ  | *Myresourcegroup* kaynak grubundan *MyVirtualNetwork* öğesini seçin. |
    | Alt ağ | *Mysubnet* öğesini seçin. |
    | **ÖZEL DNS TÜMLEŞTİRMESİ** |  |
    | Özel DNS bölgesi ile tümleştirme  | Varsayılan **Evet**' i bırakın. |
    | Özel DNS bölgesi  | Varsayılan * * (yeni) privatelink.search.windows.net * * ' i bırakın. |
    |||

1. **Tamam**’ı seçin. 

1. **Gözden geçir ve oluştur**’u seçin. Azure’ın yapılandırmanızı doğrulayacağı **Gözden geçir ve oluştur** sayfasına yönlendirilirsiniz. 

1. **Doğrulama başarılı** iletisini gördüğünüzde **Oluştur**’u seçin. 

1. Yeni hizmetinizin sağlanması tamamlandıktan sonra, az önce oluşturduğunuz kaynağa gidin.

1. Sol içerik menüsünde **anahtarlar** ' ı seçin.

1. Hizmete bağlanırken, daha sonra **birincil yönetici anahtarını** kopyalayın.

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

1. Azure Portal ekranın sol üst tarafında **kaynak oluştur**  >  **işlem**  >  **sanal makinesi**' ni seçin.

1. **Sanal makine oluşturma-temel bilgiler** bölümünde, bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **PROJE AYRıNTıLARı** | |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **myResourceGroup** öğesini seçin. Bu, önceki bölümde oluşturdunuz.  |
    | **ÖRNEK AYRıNTıLARı** |  |
    | Sanal makine adı | *Myvm*' i girin. |
    | Region | **Batı ABD** veya kullandığınız bölgeyi seçin. |
    | Kullanılabilirlik seçenekleri | Varsayılan **altyapı yedekliliği gerekli değildir**. |
    | Görüntü | **Windows Server 2019 Datacenter** öğesini seçin. |
    | Boyut | Varsayılan **Standart DS1 v2**' i bırakın. |
    | **YÖNETİCİ HESABI** |  |
    | Kullanıcı adı | Seçmekten bir Kullanıcı adı girin. |
    | Parola | Seçtiğiniz bir parolayı girin. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)karşılamalıdır.|
    | Parolayı Onayla | Parolayı yeniden girin. |
    | **GELEN BAĞLANTI NOKTASI KURALLARI** |  |
    | Genel gelen bağlantı noktaları | Varsayılan olarak **Seçili bağlantı noktalarına Izin ver**' i bırakın. |
    | Gelen bağlantı noktalarını seçin | Varsayılan RDP 'yi **(3389)** bırakın. |
    | **TASARRUF EDİN** |  |
    | Zaten bir Windows lisansınız var mı? | Varsayılan **Hayır** olarak bırakın. |
    |||

1. **İleri ' yi seçin: diskler**.

1. **Sanal makine oluştur - Diskler** bölümünde varsayılan değerleri değiştirmeyin ve **Sonraki: Ağ** seçeneğini belirleyin.

1. **Sanal makine oluşturma-ağ oluşturma** bölümünde şu bilgileri seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Sanal ağ | Varsayılan **MyVirtualNetwork** bırakın.  |
    | Adres alanı | Varsayılan **10.1.0.0/24**' i bırakın.|
    | Alt ağ | Varsayılan **Mysubnet (10.1.0.0/24)** olarak bırakın.|
    | Genel IP | Varsayılan **(yeni) myVm-ip**' i bırakın. |
    | Genel gelen bağlantı noktaları | **Seçili bağlantı noktalarına Izin ver**' i seçin. |
    | Gelen bağlantı noktalarını seçin | **Http** ve **RDP**' yi seçin.|
    ||

   > [!NOTE]
   > IPv4 adresleri [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) biçiminde ifade edilebilir. [RFC 1918](https://tools.ietf.org/html/rfc1918)' de açıklandığı gibi, özel ağ IÇIN ayrılan IP aralığını kullanmaktan kaçınmaya özen gösterin:
   >
   > - `10.0.0.0 - 10.255.255.255  (10/8 prefix)`
   > - `172.16.0.0 - 172.31.255.255  (172.16/12 prefix)`
   > - `192.168.0.0 - 192.168.255.255 (192.168/16 prefix)`

1. **Gözden geçir ve oluştur**’u seçin. Azure’ın yapılandırmanızı doğrulayacağı **Gözden geçir ve oluştur** sayfasına yönlendirilirsiniz.

1. **Doğrulama başarılı** iletisini gördüğünüzde **Oluştur**’u seçin. 

## <a name="connect-to-the-vm"></a>VM’ye bağlanma

VM *myvm* ' yi indirip şu şekilde bağlayın:

1. Portalın arama çubuğuna *myVm* değerini girin.

1. **Bağlan** düğmesini seçin. **Bağlan** düğmesini seçtikten sonra **sanal makineye bağlan** açılır.

1. **RDP Dosyasını İndir**’i seçin. Azure bir Uzak Masaüstü Protokolü (*. rdp*) dosyası oluşturur ve bilgisayarınıza indirir.

1. İndirilen. rdp * dosyasını açın.

    1. İstendiğinde **Bağlan**’ı seçin.

    1. VM oluştururken belirttiğiniz kullanıcı adını ve parolayı girin.

        > [!NOTE]
        >   >  VM oluştururken girdiğiniz kimlik bilgilerini belirtmek için **farklı bir hesap kullan**' ı seçmeniz gerekebilir.

1. **Tamam**’ı seçin.

1. Oturum açma işlemi sırasında bir sertifika uyarısı alabilirsiniz. Bir sertifika uyarısı alırsanız **Evet**’i veya **Devam**’ı seçin.

1. VM masaüstü seçildikten sonra, bunu yerel masaüstünüze geri dönmek için simge durumuna küçültün.  

## <a name="test-connections"></a>Sınama bağlantıları

Bu bölümde, arama hizmetine özel ağ erişimini doğrulayacaksınız ve özel uç noktayı kullanarak özel olarak bağlanılacak şekilde bağlanıyorsunuz.

Arama hizmeti uç noktası özel olduğunda, bazı Portal özellikleri devre dışı bırakılır. Hizmet düzeyi ayarlarını görüntüleyebilir ve yönetebileceksiniz, ancak dizin, Dizin Oluşturucu ve beceri tanımları gibi hizmet içindeki çeşitli diğer bileşenlere güvenlik nedenleriyle, Portal erişimi, güvenlik nedenleriyle kısıtlıdır.

1. *Myvm* uzak masaüstünde PowerShell ' i açın.

1. ' Nslookup [arama hizmeti adı]. Search. Windows. net ' yazın

    Şuna benzer bir ileti alırsınız:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. VM 'den arama hizmetine bağlanın ve bir dizin oluşturun. REST API kullanarak hizmetinize yeni bir arama dizini oluşturmak için bu [hızlı](search-get-started-rest.md) başlangıcı izleyebilirsiniz. Bir Web API 'SI test aracından istekleri ayarlamak için arama hizmeti uç noktası (https://[arama hizmeti adı]. Search. Windows. net) ve önceki adımda kopyaladığınız yönetici API anahtarı gerekir.

1. VM 'den hızlı başlangıç işlemini tamamlamak, hizmetin tam olarak çalışır durumda olduğunu onaylamadır.

1. *Myvm* ile uzak masaüstü bağlantısını kapatın. 

1. Hizmetinizin genel bir uç noktada erişilebilir olmadığından emin olmak için, yerel iş istasyonunuzda Postman ' ı açın ve hızlı başlangıçta ilk birkaç görevi deneyin. Uzak sunucunun mevcut olmadığı bir hata alırsanız, arama hizmetiniz için bir özel uç nokta başarıyla yapılandırdınız.

## <a name="clean-up-resources"></a>Kaynakları temizleme 
Özel uç nokta, arama hizmeti ve VM 'yi kullanarak işiniz bittiğinde, kaynak grubunu ve içerdiği tüm kaynakları silin:
1.  **   Portalın üst kısmındaki **arama** kutusuna myresourcegroup yazın ve arama sonuçlarından  *myresourcegroup* öğesini seçin   . 
1. **Kaynak grubunu sil**'i seçin. 
1.  **   **Kaynak grubu adını yazın** ve **Sil**' i seçmek için myresourcegroup girin.

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, bir sanal ağ üzerinde bir VM ve özel bir uç nokta içeren bir arama hizmeti oluşturdunuz. VM 'ye internet 'ten bağlanır ve özel bağlantı kullanarak arama hizmetine güvenli bir şekilde iletilecaksınız. Özel uç nokta hakkında daha fazla bilgi edinmek için bkz. [Azure özel uç noktası nedir?](../private-link/private-endpoint-overview.md).
