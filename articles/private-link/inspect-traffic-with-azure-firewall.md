---
title: Azure Güvenlik Duvarı'nı kullanarak özel uç noktaya giden trafiği inceleme
titleSuffix: Azure Private Link
description: Azure Güvenlik Duvarı 'nı kullanarak özel bir uç noktaya giden trafiği nasıl inceleyebilirsiniz öğrenin.
services: private-link
author: jocortems
ms.service: private-link
ms.topic: how-to
ms.date: 09/02/2020
ms.author: allensu
ms.openlocfilehash: 3059d88c9894ba50a608afaf1cb23367dfaf089c
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259464"
---
# <a name="use-azure-firewall-to-inspect-traffic-destined-to-a-private-endpoint"></a>Azure Güvenlik Duvarı'nı kullanarak özel uç noktaya giden trafiği inceleme

> [!NOTE]
> Güvenli sanal hub 'ı kullanarak Azure sanal WAN 'daki özel uç noktalara trafiği güvenli hale getirmek istiyorsanız, bkz. [Azure sanal WAN 'da özel uç noktalara giden güvenli trafik](../firewall-manager/private-link-inspection-secure-virtual-hub.md).

Azure özel uç noktası, Azure özel bağlantısı için temel yapı taşıdır. Özel uç noktalar, bir sanal ağda dağıtılan Azure kaynaklarının özel bağlantı kaynaklarıyla özel olarak iletişim kurmasına olanak tanır.

Özel uç noktalar, kaynakların bir sanal ağda dağıtılan özel bağlantı hizmetine erişimine izin verir. Sanal ağ eşlemesi ve şirket içi ağ bağlantıları aracılığıyla özel uç noktaya erişim bağlantıyı genişletir.

Özel uç noktalar aracılığıyla kullanıma sunulan hizmetlere gelen trafiği incelemeniz veya engellemeniz gerekebilir. [Azure Güvenlik Duvarı](../firewall/overview.md) veya üçüncü taraf ağ sanal gereci kullanarak bu incelemeyi doldurun.

Aşağıdaki sınırlamalar geçerlidir:

* Ağ güvenlik grupları (NSG), Özel uç noktalardan gelen trafik tarafından atlanır
* Kullanıcı tanımlı yollar (UDR), Özel uç noktalardan gelen trafik tarafından atlanır
* Tek bir yol tablosu bir alt ağa bağlanabilir
* Yol tablosu en fazla 400 yolu destekler

Azure Güvenlik Duvarı şu iki kullanarak trafiği filtreler:

* TCP ve UDP protokolleri için [ağ kurallarında FQDN](../firewall/fqdn-filtering-network-rules.md)
* HTTP, HTTPS ve MSSQL için [uygulama kurallarında FQDN](../firewall/features.md#application-fqdn-filtering-rules) . 

> [!IMPORTANT] 
> Akış simetriyi sürdürmek için özel uç noktalara giden trafiği incelerken, ağ kuralları üzerinde uygulama kurallarının kullanılması önerilir. Ağ kuralları kullanılıyorsa veya Azure Güvenlik Duvarı yerine bir NVA kullanılıyorsa, Özel uç noktalara giden trafik için SNAT yapılandırılmalıdır.

> [!NOTE]
> SQL FQDN filtrelemesi yalnızca [proxy modunda](../azure-sql/database/connectivity-architecture.md#connection-policy) desteklenir (bağlantı noktası 1433). **Ara sunucu** modu, *yeniden yönlendirmeye* kıyasla daha fazla gecikme süresine neden olabilir. Azure 'da bağlanan istemciler için varsayılan olan yeniden yönlendirme modunu kullanmaya devam etmek istiyorsanız, güvenlik duvarı ağ kurallarında FQDN 'yi kullanarak erişimi filtreleyebilirsiniz.

## <a name="scenario-1-hub-and-spoke-architecture---dedicated-virtual-network-for-private-endpoints"></a>Senaryo 1: hub ve bağlı bileşen mimarisi-özel uç noktalar için ayrılmış sanal ağ

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/hub-and-spoke.png" alt-text="Özel uç noktalar için ayrılmış sanal ağ" border="true":::

Bu senaryo, Özel uç noktalar kullanılarak birden çok Azure hizmetine özel olarak bağlanmak için en genişletilebilir mimaridir. Özel uç noktaların dağıtıldığı ağ adresi alanına işaret eden bir yol oluşturulur. Bu yapılandırma, yönetim yükünü azaltır ve 400 rotamın sınırının üzerinde çalışmasını önler.

Sanal ağlar eşlenirse, bir istemci sanal ağından Azure Güvenlik duvarına bir hub sanal ağında yapılan bağlantılar ücretlendirilir. Bir hub sanal ağındaki Azure Güvenlik duvarının bağlantısı, eşlenmiş bir sanal ağdaki özel uç noktalara ücretlendirilmez.

Eşlenen sanal ağlarla bağlantılarla ilgili ücretler hakkında daha fazla bilgi için, [fiyatlandırma](https://azure.microsoft.com/pricing/details/private-link/) sayfasının SSS bölümüne bakın.

## <a name="scenario-2-hub-and-spoke-architecture---shared-virtual-network-for-private-endpoints-and-virtual-machines"></a>Senaryo 2: hub ve bağlı bileşen mimarisi-özel uç noktalar ve sanal makineler için paylaşılan sanal ağ

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/shared-spoke.png" alt-text="Aynı sanal ağdaki özel uç noktalar ve sanal makineler" border="true":::

Bu senaryo şu durumlarda uygulanır:

* Özel uç noktalar için ayrılmış bir sanal ağ olması mümkün değildir

* Sanal ağda özel uç noktalar kullanılarak yalnızca birkaç hizmet kullanıma sunulduğunda

Sanal makineler her özel uç noktayı işaret eden/32 Sistem yollarına sahip olacaktır. Özel uç nokta başına bir rota, trafiği Azure Güvenlik Duvarı üzerinden yönlendirmek üzere yapılandırılmıştır. 

Yönlendirme tablosunun bakımının yönetim yükü, sanal ağda hizmet sunulurken artar. Yol sınırına vurmaya olasılığı da artar.

Genel mimarinize bağlı olarak, 400 rota sınırının içinde çalışmak mümkündür. Mümkün olan her durumda Senaryo 1 ' i kullanmanız önerilir.

Sanal ağlar eşlenirse, bir istemci sanal ağından Azure Güvenlik duvarına bir hub sanal ağında yapılan bağlantılar ücretlendirilir. Bir hub sanal ağındaki Azure Güvenlik duvarının bağlantısı, eşlenmiş bir sanal ağdaki özel uç noktalara ücretlendirilmez.

Eşlenen sanal ağlarla bağlantılarla ilgili ücretler hakkında daha fazla bilgi için, [fiyatlandırma](https://azure.microsoft.com/pricing/details/private-link/) sayfasının SSS bölümüne bakın.

## <a name="scenario-3-single-virtual-network"></a>Senaryo 3: tek sanal ağ

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/single-vnet.png" alt-text="Tek sanal ağ" border="true":::

Hub ve bağlı bileşen mimarisine geçiş mümkün olmadığında bu stili kullanın. Senaryo 2 ' de ile aynı noktalar geçerlidir. Bu senaryoda, sanal ağ eşleme ücretleri uygulanmaz.

## <a name="scenario-4-on-premises-traffic-to-private-endpoints"></a>Senaryo 4: özel uç noktalara şirket içi trafik

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/on-premises.png" alt-text="Özel uç noktalara şirket içi trafik" border="true":::

Bu mimari, şirket içi ağınızla bağlantı yapılandırdıysanız aşağıdakilerden birini kullanarak yapılandırabilirsiniz: 

* [ExpressRoute](..\expressroute\expressroute-introduction.md)
* [Siteden siteye VPN](../vpn-gateway/tutorial-site-to-site-portal.md) 

Güvenlik gereksinimleriniz, bir güvenlik gereci aracılığıyla yönlendirilmek üzere özel uç noktalar aracılığıyla sunulan hizmetlere istemci trafiği gerektiriyorsa, bu senaryoyu dağıtın.

Yukarıdaki senaryo 2 ile aynı noktalar geçerlidir. Bu senaryoda sanal ağ eşleme ücretleri yoktur. DNS sunucularınızı şirket içi iş yüklerinin özel uç noktalara erişmesine izin verecek şekilde yapılandırma hakkında daha fazla bilgi için, bkz. Şirket [içi iş yükleri BIR DNS ileticisi kullanarak](./private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder).

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği.
* Log Analytics çalışma alanı.  

Aboneliğinizde bir tane yoksa, bir çalışma alanı oluşturmak için [Azure portal Log Analytics çalışma alanı oluşturma](../azure-monitor/logs/quick-create-workspace.md) bölümüne bakın.


## <a name="sign-in-to-azure"></a>Azure'da oturum açma

https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-a-vm"></a>VM oluşturma

Bu bölümde, özel bağlantı kaynağına erişmek için kullanılan VM 'yi barındırmak için bir sanal ağ ve alt ağ oluşturacaksınız. Örnek hizmet olarak bir Azure SQL veritabanı kullanılır.

## <a name="virtual-networks-and-parameters"></a>Sanal ağlar ve parametreler

Üç sanal ağı ve bunlara karşılık gelen alt ağlarını şu şekilde oluşturun:

* VM ile özel uç nokta arasındaki iletişimi kısıtlamak için kullanılan Azure Güvenlik duvarını içerir.
* Özel bağlantı kaynağına erişmek için kullanılan VM 'yi barındırın.
* Özel uç noktayı barındırın.

Adımlarda aşağıdaki parametreleri aşağıdaki bilgilerle değiştirin:

### <a name="azure-firewall-network"></a>Azure Güvenlik Duvarı ağı

| Parametre                   | Değer                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myAzFwVNet          |
| **\<region-name>**          | Orta Güney ABD      |
| **\<IPv4-address-space>**   | 10.0.0.0/16          |
| **\<subnet-name>**          | AzureFirewallSubnet        |
| **\<subnet-address-range>** | 10.0.0.0/24          |

### <a name="virtual-machine-network"></a>Sanal makine ağı

| Parametre                   | Değer                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVMVNet          |
| **\<region-name>**          | Orta Güney ABD      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | Başlama      |
| **\<subnet-address-range>** | 10.1.0.0/24          |

### <a name="private-endpoint-network"></a>Özel uç nokta ağı

| Parametre                   | Değer                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myPEVNet         |
| **\<region-name>**          | Orta Güney ABD      |
| **\<IPv4-address-space>**   | 10.2.0.0/16          |
| **\<subnet-name>**          | PrivateEndpointSubnet |
| **\<subnet-address-range>** | 10.2.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

10. Sanal makineyi ve özel uç nokta kaynaklarını barındırmak için sanal ağlar oluşturmak üzere 1 ile 9 arasındaki adımları yineleyin.

### <a name="create-virtual-machine"></a>Sanal makine oluşturma

1. Azure Portal ekranın sol üst tarafında **kaynak oluştur**  >  **işlem**  >  **sanal makinesi**' ni seçin.

2. **Sanal makine oluşturma-temel bilgiler** bölümünde, bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Proje ayrıntıları** | |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **myResourceGroup** öğesini seçin. Bu kaynak grubunu önceki bölümde oluşturdunuz.  |
    | **Örnek ayrıntıları** |  |
    | Sanal makine adı | **Myvm**' i girin. |
    | Bölge | **Orta Güney ABD (ABD)** seçin. |
    | Kullanılabilirlik seçenekleri | Varsayılan **altyapı yedekliliği gerekli değildir**. |
    | Görüntü | **Ubuntu Server 18,04 LTS-Gen1** öğesini seçin. |
    | Boyut | **Standard_B2s** seçin. |
    | **Yönetici hesabı** |  |
    | Kimlik doğrulaması türü | **Parola** seçin. |
    | Kullanıcı adı | Seçmekten bir Kullanıcı adı girin. |
    | Parola | Seçtiğiniz bir parolayı girin. Parola en az 12 karakter uzunluğunda olmalı ve [tanımlanmış karmaşıklık gereksinimlerini](../virtual-machines/linux/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)karşılamalıdır.|
    | Parolayı Onayla | Parolayı yeniden girin. |
    | **Gelen bağlantı noktası kuralları** |  |
    | Genel gelen bağlantı noktaları | **Hiçbiri** seçeneğini belirtin. |
    |||

3. **İleri ' yi seçin: diskler**.

4. **Sanal makine oluştur - Diskler** bölümünde varsayılan değerleri değiştirmeyin ve **Sonraki: Ağ** seçeneğini belirleyin.

5. **Sanal makine oluşturma-ağ oluşturma** bölümünde şu bilgileri seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Sanal ağ | **Myvmvnet**' i seçin.  |
    | Alt ağ | **VMSubnet (10.1.0.0/24)** öğesini seçin.|
    | Genel IP | Varsayılan **(yeni) myVm-ip**' i bırakın. |
    | Genel gelen bağlantı noktaları | **Seçili bağlantı noktalarına Izin ver**' i seçin. |
    | Gelen bağlantı noktalarını seçin | **SSH**' ı seçin.|
    ||

6. **Gözden geçir ve oluştur**’u seçin. Azure’ın yapılandırmanızı doğrulayacağı **Gözden geçir ve oluştur** sayfasına yönlendirilirsiniz.

7. **Doğrulama başarılı** iletisini gördüğünüzde **Oluştur**’u seçin.

## <a name="deploy-the-firewall"></a>Güvenlik duvarını dağıtma

1. Azure portal menüsünde veya **Giriş** sayfasında, **Kaynak oluştur**’u seçin.

2. Arama kutusuna **güvenlik duvarı** yazın ve **ENTER** tuşuna basın.

3. **Güvenlik duvarı** ' nı seçip **Oluştur**' u seçin.

4. **Güvenlik duvarı oluştur** sayfasında aşağıdaki ayarları kullanarak güvenlik duvarını yapılandırın:

    | Ayar | Değer |
    | ------- | ----- |
    | **Proje ayrıntıları** | |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **myResourceGroup** öğesini seçin.  |
    | **Örnek ayrıntıları** |  |
    | Name | **Myazurefirewall** yazın. |
    | Bölge | **Orta Güney ABD** seçin. |
    | Kullanılabilirlik alanı | Varsayılanı **yok** olarak bırakın. |
    | Bir sanal ağ seçin    |    **Mevcut olanı kullan**' ı seçin.    |
    | Sanal ağ    |    **Myazfwvnet**' i seçin.    |
    | Genel IP adresi    |    **Yeni Ekle** ' yi seçin ve ad alanına **MyFirewall-ip** yazın.    |
    | Zorlamalı tünel oluşturma    | Varsayılanı **devre dışı** bırakın.    |
    |||
5. **Gözden geçir ve oluştur**’u seçin. Azure’ın yapılandırmanızı doğrulayacağı **Gözden geçir ve oluştur** sayfasına yönlendirilirsiniz.

6. **Doğrulama başarılı** iletisini gördüğünüzde **Oluştur**’u seçin.

## <a name="enable-firewall-logs"></a>Güvenlik Duvarı günlüklerini etkinleştir

Bu bölümde, güvenlik duvarında günlükleri etkinleştirirsiniz.

1. Azure portal, sol taraftaki menüden **tüm kaynaklar** ' ı seçin.

2. Kaynak listesinde güvenlik duvarı **Myazurefirewall** ' ı seçin.

3. Güvenlik Duvarı ayarlarında **izleme** altında **Tanılama ayarları** ' nı seçin.

4. Tanılama Ayarları ' nda **+ Tanılama ayarı Ekle** ' yi seçin.

5. **Tanılama ayarı**' nda bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Tanılama ayarı adı | **Mydiagsetting** yazın. |
    | Kategori ayrıntıları | |
    | açmasını | **AzureFirewallApplicationRule** ve **AzureFirewallNetworkRule** öğesini seçin. |
    | Hedef Ayrıntıları | **Log Analytics gönder**' i seçin. |
    | Abonelik | Aboneliğinizi seçin. |
    | Log Analytics çalışma alanı | Log Analytics çalışma alanınızı seçin. |

6. **Kaydet**’i seçin.

## <a name="create-azure-sql-database"></a>Azure SQL veritabanı oluşturma

Bu bölümde, özel bir SQL veritabanı oluşturursunuz.

1. Ekranın sol üst tarafında Azure Portal **kaynak**  >  **veritabanları** oluştur  >  **SQL veritabanı**' nı seçin.

2. **SQL veritabanı oluşturma-temel** bilgiler bölümünde, bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Proje ayrıntıları** | |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **myResourceGroup** öğesini seçin. Bu kaynak grubunu önceki bölümde oluşturdunuz.|
    | **Veritabanı ayrıntıları** |  |
    | Veritabanı adı  | **MyDatabase** yazın.  |
    | Sunucu | **Yeni oluştur** ' u seçin ve aşağıdaki bilgileri girin.    |
    | Sunucu adı | **Mydbserver** girin. Bu ad alındıysanız, benzersiz bir ad girin.   |
    | Sunucu yöneticisi oturumu açma | Seçtiğiniz bir adı girin. |
    | Parola    |    Seçtiğiniz bir parolayı girin.    |
    | Parolayı Onayla | Parolayı yeniden girin    |
    | Konum    | **Orta Güney ABD (ABD)** seçin.    |
    | SQL elastik havuzunu kullanmak istiyor    | Varsayılan **Hayır** olarak bırakın. |
    | İşlem + depolama | Varsayılan **genel amaçlı 5. nesil, 2 sanal çekirdek, 32 GB depolama alanını** bırakın. |
    |||

3. **Gözden geçir ve oluştur**’u seçin. Azure’ın yapılandırmanızı doğrulayacağı **Gözden geçir ve oluştur** sayfasına yönlendirilirsiniz.

4. **Doğrulama başarılı** iletisini gördüğünüzde **Oluştur**’u seçin.

## <a name="create-private-endpoint"></a>Özel uç nokta oluştur

Bu bölümde, önceki bölümde Azure SQL veritabanı için özel bir uç nokta oluşturacaksınız.

1. Azure portal, sol taraftaki menüden **tüm kaynaklar** ' ı seçin.

2. Hizmetler listesinde Azure SQL Server **mydbserver** ' ı seçin.  Farklı bir sunucu adı kullandıysanız, bu adı seçin.

3. Sunucu ayarları ' nda, **güvenlik** altında **Özel uç nokta bağlantıları** ' nı seçin.

4. **+ Özel uç nokta**' ı seçin.

5. **Özel uç nokta oluştur**' da, **temel** bilgiler sekmesinde bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Proje ayrıntıları** | |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **myResourceGroup** öğesini seçin. |
    | **Örnek ayrıntıları** | |
    | Name | **Sqlprivateendpoint** girin. |
    | Bölge | **Orta Güney ABD (ABD) seçin.** |

6. **Kaynak** sekmesini seçin veya sayfanın altındaki **kaynak** ' ı seçin.

7. **Kaynak** sekmesinde, bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Bağlantı yöntemi | **Dizinimde bir Azure kaynağına bağlan '** ı seçin. |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak türü | **Microsoft. SQL/Servers**' ı seçin. |
    | Kaynak | **Mydbserver** ' ı veya önceki adımda oluşturduğunuz sunucunun adını seçin.
    | Hedef alt kaynak | **SqlServer**' ı seçin. |

8. **Yapılandırma** sekmesini seçin veya sayfanın altındaki **yapılandırma** ' yı seçin.

9. **Yapılandırma** sekmesinde, bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | **Ağ** | |
    | Sanal ağ | **Mypevnet**' i seçin. |
    | Alt ağ | **Privateendpointsubnet** öğesini seçin. |
    | **Özel DNS tümleştirme** | |
    | Özel DNS bölgesi ile tümleştirme | **Evet**’i seçin. |
    | Abonelik | Aboneliğinizi seçin. |
    | Özel DNS bölgeleri | Varsayılan **Privatelink.Database.Windows.net** bırakın. |

10. **Gözden geçir + oluştur** sekmesini seçin veya sayfanın altındaki **gözden geçir + oluştur** ' u seçin.

11. **Oluştur**’u seçin.

12. Uç nokta oluşturulduktan sonra **güvenlik** altında Güvenlik **duvarları ve sanal ağlar** ' ı seçin.

13. **Güvenlik duvarları ve sanal ağlarda**, **Azure hizmetlerinin ve kaynaklarının bu sunucuya erişmesine izin** vermek **için ileri ' yi seçin** .

14. **Kaydet**’i seçin.

## <a name="connect-the-virtual-networks-using-virtual-network-peering"></a>Sanal ağ eşlemesi kullanarak sanal ağları bağlama

Bu bölümde, eşleme kullanarak **Myvmvnet** ve **Mypevnet** 'e ait sanal ağları **myazfwvnet** 'e bağlayacağız. **Myvmvnet** ve **Mypevnet** arasında doğrudan bağlantı olmayacaktır.

1. Portalın arama çubuğunda, **Myazfwvnet** girin.

2. **Ayarlar** menüsünde **peerler** ' i seçin ve **+ Ekle**' yi seçin.

3. **Eşleme Ekle** ' de aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | MyAzFwVNet 'ten uzak sanal ağa eşleme adı | **Myazfwvnet 'ten-myVMVNet** girin. |
    | **Eş ayrıntıları** |  |
    | Sanal ağ dağıtım modeli  | Varsayılan **Kaynak Yöneticisi** bırakın.  |
    | Kaynak kimliğimi biliyorum | İşaretlemeden bırakın.    |
    | Abonelik | Aboneliğinizi seçin.    |
    | Sanal ağ | **Myvmvnet**' i seçin. |
    | Uzak sanal ağdan myAzFwVNet 'e eşleme adı    |    **Myvmvnet 'Ten myAzFwVNet**' i girin.    |
    | **Yapılandırma** | |
    | **Sanal ağ erişim ayarlarını yapılandırma** | |
    | MyAzFwVNet 'ten uzak sanal ağa sanal ağ erişimine izin ver | Varsayılanı **etkin** bırakın.    |
    | Uzak sanal ağdan myAzFwVNet 'e sanal ağ erişimine izin ver    | Varsayılanı **etkin** bırakın.    |
    | **İletilen trafik ayarlarını yapılandırma** | |
    | Uzak sanal ağdan myAzFwVNet 'e iletilen trafiğe izin ver    | **Etkin**'i seçin. |
    | MyAzFwVNet 'ten uzak sanal ağa iletilen trafiğe izin ver | **Etkin**'i seçin. |
    | **Ağ Geçidi geçiş ayarlarını yapılandırma** | |
    | Ağ Geçidi aktarımına izin ver | İşaretlemeden bırakın |
    |||

4. **Tamam**’ı seçin.

5. **+ Ekle**'yi seçin.

6. **Eşleme Ekle** ' de aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | MyAzFwVNet 'ten uzak sanal ağa eşleme adı | **Myazfwvnet 'Ten myPEVNet**' i girin. |
    | **Eş ayrıntıları** |  |
    | Sanal ağ dağıtım modeli  | Varsayılan **Kaynak Yöneticisi** bırakın.  |
    | Kaynak kimliğimi biliyorum | İşaretlemeden bırakın.    |
    | Abonelik | Aboneliğinizi seçin.    |
    | Sanal ağ | **Mypevnet**' i seçin. |
    | Uzak sanal ağdan myAzFwVNet 'e eşleme adı    |    **Mypevnet-to-myAzFwVNet** girin.    |
    | **Yapılandırma** | |
    | **Sanal ağ erişim ayarlarını yapılandırma** | |
    | MyAzFwVNet 'ten uzak sanal ağa sanal ağ erişimine izin ver | Varsayılanı **etkin** bırakın.    |
    | Uzak sanal ağdan myAzFwVNet 'e sanal ağ erişimine izin ver    | Varsayılanı **etkin** bırakın.    |
    | **İletilen trafik ayarlarını yapılandırma** | |
    | Uzak sanal ağdan myAzFwVNet 'e iletilen trafiğe izin ver    | **Etkin**'i seçin. |
    | MyAzFwVNet 'ten uzak sanal ağa iletilen trafiğe izin ver | **Etkin**'i seçin. |
    | **Ağ Geçidi geçiş ayarlarını yapılandırma** | |
    | Ağ Geçidi aktarımına izin ver | İşaretlemeden bırakın |

7. **Tamam**’ı seçin.

## <a name="link-the-virtual-networks-to-the-private-dns-zone"></a>Sanal ağları özel DNS bölgesine bağlama

Bu bölümde, **Myvmvnet** ve **Myazfwvnet** sanal ağlarını **Privatelink.Database.Windows.net** özel DNS bölgesine bağlayacağız. Özel uç nokta oluşturduğumuz zaman bu bölge oluşturulmuştur. 

VM ve güvenlik duvarının veritabanının FQDN 'sini özel uç noktası adresine çözümlemesi için bağlantı gereklidir. Özel uç nokta oluşturulduğunda, **Mypevnet** sanal ağı otomatik olarak bağlandı.

>[!NOTE]
>VM ve güvenlik duvarı sanal ağlarını özel DNS bölgesine bağlayamazsınız, hem VM hem de güvenlik duvarı SQL Server FQDN 'yi çözümleyebilecektir. Bunlar, genel IP adresi olarak çözümlenir.

1. Portalın arama çubuğunda **Privatelink. Database** yazın.

2. Arama sonuçlarında **Privatelink.Database.Windows.net** öğesini seçin.

3. **Ayarlar** altında **sanal ağ bağlantıları** ' nı seçin.

4. **+ Ekle** ' yi seçin

5. **Sanal ağ ekle bağlantısı** ' nda aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Bağlantı adı | **Bağlantı-myVMVNet** girin. |
    | **Sanal ağ ayrıntıları** |  |
    | Sanal ağın kaynak KIMLIĞINI biliyorum  | İşaretlemeden bırakın.  |
    | Abonelik | Aboneliğinizi seçin.    |
    | Sanal ağ | **Myvmvnet**' i seçin. |
    | **YAPıLANDıRMADA** | |
    | Otomatik kaydı etkinleştir | İşaretlemeden bırakın.    |


6. **Tamam**’ı seçin.

## <a name="configure-an-application-rule-with-sql-fqdn-in-azure-firewall"></a>Azure Güvenlik duvarında SQL FQDN ile uygulama kuralı yapılandırma

Bu bölümde, SQL Server **mydbserver.Database.Windows.net** Için **myvm** ile özel uç nokta arasında iletişime izin vermek üzere bir uygulama kuralı yapılandırın. 

Bu kural, önceki adımlarda oluşturduğumuz güvenlik duvarı üzerinden iletişim sağlar.

1. Portalın arama çubuğunda, **Myazurefirewall** yazın.

2. Arama sonuçlarında **Myazurefirewall** ' u seçin.

3. **Myazurefirewall** genel görünümünde **Ayarlar** ' ın altında **kurallar** ' ı seçin.

4. **Uygulama kuralı koleksiyonu** sekmesini seçin.

5. **+ Uygulama kuralı koleksiyonu Ekle**' yi seçin.

6. **Uygulama kuralı koleksiyonu Ekle** ' de aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Ad | **Sqlprivateendpoint** girin. |
    | Öncelik | **100** değerini girin. |
    | Eylem | **Izin ver**' i girin. |
    | **Kurallar** |  |
    | **FQDN etiketleri** | |
    | Name  | Boş bırakın.  |
    | Kaynak türü | Varsayılan **IP adresini** bırakın.    |
    | Kaynak | Boş bırakın. |
    | FQDN etiketleri | Varsayılan **0 seçili** bırakın. |
    | **Hedef FQDN 'Ler** | |
    | Name | **Sqlprivateendpoint** girin.    |
    | Kaynak türü | Varsayılan **IP adresini** bırakın. |
    | Kaynak | **10.1.0.0/16** girin. |
    | Protokol: bağlantı noktası | **MSSQL: 1433** girin. |
    | Hedef FQDN 'Ler | **Mydbserver.Database.Windows.net** girin. |
    |||

7. **Add (Ekle)** seçeneğini belirleyin.

## <a name="route-traffic-between-the-virtual-machine-and-private-endpoint-through-azure-firewall"></a>Azure Güvenlik Duvarı ile sanal makine ve özel uç nokta arasında trafiği yönlendirme

**Myvmvnet** ve **Mypevnet** sanal ağları arasında doğrudan sanal ağ eşlemesi oluşturmadınız. **Myvm** sanal makinesi oluşturduğumuz özel uç noktaya yönelik bir yola sahip değil. 

Bu bölümde, özel bir yol içeren bir yol tablosu oluşturacağız. 

Yol, Azure Güvenlik Duvarı aracılığıyla **Myvm** alt ağından gelen trafiği **Mypevnet** adlı sanal ağın adres alanına gönderir.

1. Azure portal menüsünde veya **Giriş** sayfasında, **Kaynak oluştur**’u seçin.

2. Arama kutusuna **yol tablosunu** yazın ve **ENTER** tuşuna basın.

3. **Yol tablosu** ' nu seçin ve ardından **Oluştur**' u seçin.

4. Yol **tablosu oluştur** sayfasında, yol tablosunu yapılandırmak için aşağıdaki tabloyu kullanın:

    | Ayar | Değer |
    | ------- | ----- |
    | **Proje ayrıntıları** | |
    | Abonelik | Aboneliğinizi seçin. |
    | Kaynak grubu | **myResourceGroup** öğesini seçin.  |
    | **Örnek ayrıntıları** |  |
    | Bölge | **Orta Güney ABD** seçin. |
    | Name | **VMsubnet-to-AzureFirewall** girin. |
    | Ağ Geçidi yollarını yayma | **Hayır**'ı seçin. |

5. **Gözden geçir ve oluştur**’u seçin. Azure’ın yapılandırmanızı doğrulayacağı **Gözden geçir ve oluştur** sayfasına yönlendirilirsiniz.

6. **Doğrulama başarılı** iletisini gördüğünüzde **Oluştur**’u seçin.

7. Dağıtım tamamlandıktan sonra **Kaynağa Git**' i seçin.

8. **Ayarlar** altında **rotalar** ' ı seçin.

9. **+ Ekle**'yi seçin.

10. **Yol Ekle** sayfasında, girin veya bu bilgileri seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Yönlendirme adı | **Myvmsubnet-privateendpoint** yazın. |
    | Adres ön eki | **10.2.0.0/16** girin.  |
    | Sonraki atlama türü | **Sanal gereç**’i seçin. |
    | Sonraki atlama adresi | **10.0.0.4** girin. |

11. **Tamam**’ı seçin.

12. **Ayarlar** altında **alt ağlar** ' ı seçin.

13. **+ İlişkilendir**' i seçin.

14. **Alt ağı ilişkilendir** sayfasında, bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Sanal ağ | **Myvmvnet**' i seçin. |
    | Alt ağ | **VMSubnet**' i seçin.  |

15. **Tamam**’ı seçin.

## <a name="connect-to-the-virtual-machine-from-your-client-computer"></a>İstemci bilgisayarınızdan sanal makineye bağlanma

Aşağıdaki gibi, internet **'ten gelen VM VM** 'sine bağlanın:

1. Portalın arama çubuğunda **Myvm-ip** yazın.

2. Arama sonuçlarında **Myvm-IP** ' yi seçin.

3. **IP adresi** altındaki değeri kopyalayın veya aşağı yazın.

4. Windows 10 kullanıyorsanız, PowerShell 'i kullanarak aşağıdaki komutu çalıştırın. Diğer Windows istemci sürümleri için, [Putty](https://www.putty.org/)gıbı bir SSH istemcisi kullanın:

* **Kullanıcı adını** , VM oluşturma sırasında girdiğiniz yönetici kullanıcı adıyla değiştirin.

* **IPAddress** değerini ÖNCEKI adımdaki IP adresiyle değiştirin.

    ```bash
    ssh username@IPaddress
    ```

5. **Myvm** oluştururken tanımladığınız parolayı girin

## <a name="access-sql-server-privately-from-the-virtual-machine"></a>Sanal makineden özel olarak SQL Server erişim

Bu bölümde, Özel uç noktasını kullanarak SQL veritabanına özel olarak bağlanırsınız.

1. `nslookup mydbserver.database.windows.net` değerini girin
    
    Aşağıdakine benzer bir ileti alacaksınız:

    ```bash
    Server:         127.0.0.53
    Address:        127.0.0.53#53

    Non-authoritative answer:
    mydbserver.database.windows.net       canonical name = mydbserver.privatelink.database.windows.net.
    Name:   mydbserver.privatelink.database.windows.net
    Address: 10.2.0.4
    ```

2. [SQL Server komut satırı araçlarını](/sql/linux/quickstart-install-connect-ubuntu#tools)yükler.

3. SQL Server bağlanmak için aşağıdaki komutu çalıştırın. Önceki adımlarda SQL Server oluştururken tanımladığınız sunucu yöneticisini ve parolayı kullanın.

* **\<ServerAdmin>** SQL Server oluşturma sırasında girdiğiniz yönetici kullanıcı adıyla değiştirin.

* **\<YourPassword>** SQL Server oluşturma sırasında girdiğiniz yönetici parolasıyla değiştirin.

    ```bash
    sqlcmd -S mydbserver.database.windows.net -U '<ServerAdmin>' -P '<YourPassword>'
    ```
4. Başarılı oturum açma sırasında bir SQL komut istemi görüntülenir. **Sqlcmd** aracından çıkmak için **Çıkış** girin.

5. **Çıkış**' a girerek **myvm** bağlantısını kapatın.

## <a name="validate-the-traffic-in-azure-firewall-logs"></a>Azure Güvenlik Duvarı günlüklerinde trafiği doğrulama

1. Azure portal, **tüm kaynaklar** ' ı seçin ve Log Analytics çalışma alanınızı seçin.

2. Log Analytics çalışma alanı sayfasında **genel** altında **Günlükler** ' i seçin.

3. Mavi **Başlangıç** düğmesini seçin.

4. **Örnek sorgular** penceresinde **tüm sorgular** altında **güvenlik duvarları** ' ni seçin.

5. **Uygulama kuralı günlük verileri** altındaki **Çalıştır** düğmesini seçin.

6. Günlük sorgusu çıkışında, **mydbserver.Database.Windows.net** 'in **FQDN** altında listelendiğini ve **Sqlprivateendpoint** ' in **RuleCollection** altında listelendiğini doğrulayın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynakları kullanarak işiniz bittiğinde, kaynak grubunu ve içerdiği tüm kaynakları silin:

1. Portalın üst kısmındaki **arama** kutusuna **myresourcegroup** yazın ve arama sonuçlarından **myresourcegroup** öğesini seçin.

1. **Kaynak grubunu sil**'i seçin.

1. **Kaynak grubu adını yazın** ve **Sil**' i seçmek için **myresourcegroup** girin.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Güvenlik Duvarı 'nı kullanarak bir sanal makine ile özel uç nokta arasındaki trafiği kısıtlamak için kullanabileceğiniz farklı senaryolar incelediniz. 

VM 'ye bağlanırsınız ve özel bağlantı kullanarak Azure Güvenlik Duvarı üzerinden veritabanına güvenli bir şekilde iletilecaksınız.

Özel uç nokta hakkında daha fazla bilgi edinmek için bkz. [Azure özel uç noktası nedir?](private-endpoint-overview.md).
