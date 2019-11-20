---
title: Azure 'da Linux sanal makineleri için DNS ad çözümleme seçenekleri
description: Azure IaaS içindeki Linux sanal makineleri için, sunulan DNS hizmetleri, karma dış DNS ve kendi DNS sunucunuzu getir gibi ad çözümleme senaryoları.
services: virtual-machines
documentationcenter: na
author: RicksterCDN
manager: gwallace
editor: tysonn
ms.assetid: 787a1e04-cebf-4122-a1b4-1fcf0a2bbf5f
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: rclaus
ms.openlocfilehash: 16dc7d16b3e8f2a4c95e93f9b85c74027291ce19
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084035"
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>Azure 'da Linux sanal makineleri için DNS ad çözümleme seçenekleri
Azure, tek bir sanal ağdaki tüm sanal makineler için varsayılan olarak DNS ad çözümlemesi sağlar. Azure 'un barındırdığı sanal makinelerinizde kendi DNS hizmetlerinizi yapılandırarak kendi DNS ad çözümlemesi çözümünüzü uygulayabilirsiniz. Aşağıdaki senaryolar, durumunuza uygun olanı seçmenize yardımcı olmalıdır.

* [Azure 'un sağladığı ad çözümlemesi](#name-resolution-that-azure-provides)
* [Kendi DNS sunucunuzu kullanarak ad çözümlemesi](#name-resolution-using-your-own-dns-server)

Kullandığınız ad çözümlemesi türü, sanal makinelerinizin ve rol örneklerinizin birbirleriyle nasıl iletişim kurması gerektiği konusunda farklılık gösterir.

Aşağıdaki tabloda senaryolar ve ilgili ad çözümleme çözümleri gösterilmektedir:

| **Senaryo** | **Çözüm** | **Önekini** |
| --- | --- | --- |
| Aynı sanal ağdaki rol örnekleri veya sanal makineler arasında ad çözümlemesi |Azure 'un sağladığı ad çözümlemesi |Ana bilgisayar adı veya tam etki alanı adı (FQDN) |
| Farklı sanal ağlardaki rol örnekleri veya sanal makineler arasında ad çözümlemesi |Azure tarafından çözümlenmek üzere sanal ağlar arasında sorguları ileten, müşteri tarafından yönetilen DNS sunucuları (DNS proxy). Bkz. [kendı DNS sunucunuzu kullanarak ad çözümlemesi](#name-resolution-using-your-own-dns-server). |Yalnızca FQDN |
| Şirket içi bilgisayarların ve hizmet adlarının Azure 'daki rol örneklerinden veya sanal makinelerden çözümlenmesi |Müşteri tarafından yönetilen DNS sunucuları (örneğin, şirket içi etki alanı denetleyicisi, yerel salt okuma etki alanı denetleyicisi veya bölge aktarımları kullanılarak eşitlenen bir DNS ikincil). Bkz. [kendı DNS sunucunuzu kullanarak ad çözümlemesi](#name-resolution-using-your-own-dns-server). |Yalnızca FQDN |
| Şirket içi bilgisayarlardan Azure ana bilgisayar adlarının çözümlenmesi |Sorguları karşılık gelen sanal ağdaki müşteri tarafından yönetilen bir DNS proxy sunucusuna iletin. Proxy sunucusu, çözümleme için sorguları Azure 'a iletir. Bkz. [kendı DNS sunucunuzu kullanarak ad çözümlemesi](#name-resolution-using-your-own-dns-server). |Yalnızca FQDN |
| İç IP 'Ler için ters DNS |[Kendi DNS sunucunuzu kullanarak ad çözümlemesi](#name-resolution-using-your-own-dns-server) |yok |

## <a name="name-resolution-that-azure-provides"></a>Azure 'un sağladığı ad çözümlemesi
Azure, genel DNS adlarını çözümlemesiyle birlikte, sanal makineler ve aynı sanal ağdaki rol örnekleri için iç ad çözümlemesi sağlar. Azure Resource Manager temel alan sanal ağlarda, DNS son eki sanal ağ genelinde tutarlıdır; FQDN gerekli değil. DNS adları, hem ağ arabirim kartlarına (NIC 'ler) hem de sanal makinelere atanabilir. Azure 'un sağladığı ad çözümlemesi herhangi bir yapılandırma gerektirse de, yukarıdaki tabloda görüldüğü gibi tüm dağıtım senaryolarında uygun seçim değildir.

### <a name="features-and-considerations"></a>Özellikler ve önemli noktalar
**Özelliklerinde**

* Azure 'un sağladığı ad çözümlemesini kullanmak için yapılandırma gerekmez.
* Azure 'un sağladığı ad çözümleme hizmeti yüksek oranda kullanılabilir. Kendi DNS sunucularınızın kümelerini oluşturmanız ve yönetmeniz gerekmez.
* Azure 'un sağladığı ad çözümleme hizmeti, hem şirket içi hem de Azure ana bilgisayar adlarını çözümlemek için kendi DNS sunucularınızla birlikte kullanılabilir.
* Ad çözümlemesi, sanal ağlardaki sanal makineler arasında FQDN olması gerekmeden sağlanır.
* Otomatik olarak oluşturulan adlarla çalışmak yerine, dağıtımlarınızı en iyi şekilde tanımlayan ana bilgisayar adlarını kullanabilirsiniz.

**Konuları**

* Azure tarafından oluşturulan DNS son eki değiştirilemez.
* Kendi kayıtlarınızı el ile kaydedemezsiniz.
* WINS ve NetBIOS desteklenmez.
* Ana bilgisayar adları DNS ile uyumlu olmalıdır.
    Adlar yalnızca 0-9, a-z ve '-' karakterlerini kullanmalıdır ve '-' ile başlayamaz veya bitemez. Bkz. RFC 3696 Bölüm 2.
* DNS sorgu trafiği her bir sanal makine için kısıtlanıyor. Azaltma birçok uygulamanın etkilenmemelidir.  İstek azaltma gözlemlenip, istemci tarafı önbelleğinin etkinleştirildiğinden emin olun.  Daha fazla bilgi için bkz. [Azure 'un sağladığı ad çözümünden en iyi şekilde yararlanmak](#getting-the-most-from-name-resolution-that-azure-provides).

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Azure 'un sağladığı ad çözümünden en iyi şekilde yararlanmak
**İstemci tarafı önbelleğe alma:**

Bazı DNS sorguları ağ üzerinden gönderilmez. İstemci tarafı önbelleğe alma, yinelenen DNS sorgularını yerel bir önbellekten çözümleyerek gecikme süresini azaltmaya ve ağ tutarsızlıklarından esnekliği iyileştirilmesine yardımcı olur. DNS kayıtları, kayıt yeniliği etkilemeden önbelleğin mümkün olduğunca uzun süre depolanmasını sağlayan yaşam süresi (TTL) içerir. Sonuç olarak, çoğu durum için istemci tarafı önbelleğe alma uygundur.

Bazı Linux dağıtımları önbelleğe alma işlemini varsayılan olarak içermez. Zaten yerel bir önbellek olup olmadığını kontrol ettikten sonra her Linux sanal makinesine bir önbellek eklemenizi öneririz.

Dnsmasq gibi çeşitli farklı DNS önbelleğe alma paketleri kullanılabilir. Aşağıda, en yaygın dağıtımlardan dnsmasq 'yi yüklemek için gereken adımlar verilmiştir:

**Ubuntu (resolvconf kullanır)**
  * Dnsmasq paketini ("sudo apt-get Install dnsmasq") yükler.

**SUSE (netconf kullanır)** :
1. Dnsmasq paketini ("sudo zypper Install dnsmasq") yükler.
2. Dnsmasq hizmetini etkinleştirin ("systemctl Enable dnsmasq. Service").
3. Dnsmasq hizmetini ("systemctl start dnsmasq. Service") başlatın.
4. "/Etc/sysconfig/Network/config" öğesini düzenleyin ve NETCONFIG_DNS_FORWARDER = "" değerini "dnsmasq" olarak değiştirin.
5. Yerel DNS Çözümleyicisi olarak önbelleği ayarlamak için resolv. conf ("netconfig Update") güncelleştirmesini güncelleştirin.

**Standart dışı bir dalga yazılımıyla CentOS (eski adıyla OpenLogic; NetworkManager kullanır)**
1. Dnsmasq paketini ("sudo yıum Install dnsmasq") yükler.
2. Dnsmasq hizmetini etkinleştirin ("systemctl Enable dnsmasq. Service").
3. Dnsmasq hizmetini ("systemctl start dnsmasq. Service") başlatın.
4. "Prepfrom etki alanı-adı-Servers 127.0.0.1;" öğesini "/etc/dhclient-ET0.conf" olarak ekleyin.
5. Önbelleği yerel DNS Çözümleyicisi olarak ayarlamak için ağ hizmetini ("hizmet ağı yeniden başlatma") yeniden başlatın

> [!NOTE]
> : ' Dnsmasq ' paketi yalnızca Linux için kullanılabilen çok sayıda DNS Önbelleklerinden biridir. Kullanmadan önce, gereksinimlerinize uygunluğunu ve başka bir önbelleğin yüklenmediğini denetleyin.
>
>

**İstemci tarafı yeniden denemeleri**

DNS birincil olarak bir UDP protokolüdür. UDP Protokolü ileti teslimini garanti edemediğinden, DNS protokolünün kendisi yeniden deneme mantığını işler. Her DNS istemcisi (işletim sistemi), oluşturucunun tercihine bağlı olarak farklı bir yeniden deneme mantığı sergiler:

* Windows işletim sistemleri bir saniye sonra yeniden dener ve sonra iki, dört ve dört saniyelik bir kez daha sonra yeniden dener.
* Varsayılan Linux Kurulumu beş saniye sonra yeniden dener.  Tek saniyelik aralıklarla beş kez yeniden denemek için bunu değiştirmelisiniz.  

Bir Linux sanal makinesindeki (' cat/etc/resolv.exe ') geçerli ayarları denetlemek ve "Seçenekler" satırına bakmak için:

    options timeout:1 attempts:5

Resolv. conf dosyası otomatik olarak oluşturulur ve düzenlenmemelidir. ' Seçenekler ' satırını ekleyen belirli adımlar dağıtıma göre farklılık gösterir:

**Ubuntu** (resolvconf kullanır)
1. Seçenekler satırını '/etc/resolveconf/resolv.exe ' öğesine ekleyin.
2. Güncelleştirmek için ' resolvconf-u ' öğesini çalıştırın.

**SUSE** (netconf kullanır)
1. '/Etc/sysconfig/Network/config ' içindeki NETCONFIG_DNS_RESOLVER_OPTIONS = "" parametresine ' timeout: 1 deneme: 5 ' ekleyin.
2. Güncelleştirmek için ' netconfig Update ' komutunu çalıştırın.

**Standart dışı bir dalga yazılımıyla CentOS (eski adıyla OpenLogic)** (NetworkManager 'ı kullanır)
1. ' RES_OPTIONS = "timeout: 1 deneme: 5" ' öğesini '/Etc/sysconfig/Network ' öğesine ekleyin.
2. Güncelleştirmek için ' hizmet ağı yeniden başlatma ' Çalıştır.

## <a name="name-resolution-using-your-own-dns-server"></a>Kendi DNS sunucunuzu kullanarak ad çözümlemesi
Ad çözümleme gereksinimleriniz, Azure 'un sağladığı özelliklerin ötesine geçebilir. Örneğin, sanal ağlar arasında DNS çözümlemesi yapmanız gerekebilir. Bu senaryoyu ele almak için kendi DNS sunucularınızı kullanabilirsiniz.  

Bir sanal ağ içindeki DNS sunucuları, aynı sanal ağdaki ana bilgisayar adlarını çözümlemek için DNS sorgularını Azure 'un özyinelemeli çözümleyicilerine iletebilir. Örneğin, Azure 'da çalışan bir DNS sunucusu kendi DNS bölge dosyaları için DNS sorgularına yanıt verebilir ve diğer tüm sorguları Azure 'a iletebilir. Bu işlevsellik, sanal makinelerin hem bölge dosyalarınızda hem de Azure tarafından sağlanan ana bilgisayar adları (iletici aracılığıyla) görmesini sağlar. Azure 'un özyinelemeli çözümleyicilerine erişim, sanal IP 168.63.129.16 aracılığıyla sağlanır.

DNS iletimi, sanal ağlar arasında DNS çözümlemesini de sağlar ve şirket içi makinelerinizin Azure 'un sağladığı ana bilgisayarları çözümlemesini sağlar. Bir sanal makinenin ana bilgisayar adını çözümlemek için, DNS sunucusu sanal makinesi aynı sanal ağda bulunmalı ve ana bilgisayar adı sorgularını Azure 'a iletecek şekilde yapılandırılmalıdır. DNS son eki her bir sanal ağda farklı olduğundan, çözümleme için doğru sanal ağa DNS sorguları göndermek üzere koşullu iletme kurallarını kullanabilirsiniz. Aşağıdaki görüntüde, bu yöntemi kullanarak sanal ağlar arasında DNS çözümlemesi yapan iki sanal ağ ve şirket içi ağ gösterilmektedir:

![Sanal ağlar arasındaki DNS çözümlemesi](./media/azure-dns/inter-vnet-dns.png)

Azure 'un sağladığı ad çözümlemesini kullandığınızda, iç DNS son eki, her bir sanal makineye DHCP kullanılarak sağlanır. Kendi ad çözümleme çözümünüzü kullandığınızda, bu sonek sanal makinelere sağlanmaz çünkü son ek diğer DNS mimarilerini kesintiye uğratır. Makinelere FQDN ile başvurmak veya sanal makinelerinizde son eki yapılandırmak için, soneki belirtmek üzere PowerShell 'i veya API 'yi kullanabilirsiniz:

* Azure Resource Manager tarafından yönetilen sanal ağlar için, sonek [ağ arabirim kartı](https://msdn.microsoft.com/library/azure/mt163668.aspx) kaynağı aracılığıyla kullanılabilir. Ayrıca, NIC 'in FQDN `azure network public-ip show <resource group> <pip name>` 'sini içeren genel IP 'nizin ayrıntılarını göstermek için komutunu da çalıştırabilirsiniz.

Sorguların Azure 'a iletilmesi gereksinimlerinize uygun değilse, kendi DNS çözümünüzü sağlamanız gerekir.  DNS çözümünüzün şunları yapması gerekir:

* Örneğin, [DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md)aracılığıyla uygun konak adı çözümlemesini sağlayın. DDNS kullanırsanız, DNS kaydı atmayı devre dışı bırakmanız gerekebilir. Azure 'un DHCP kiraları çok uzun ve atma, DNS kayıtlarını erken kaldırabilir.
* Dış etki alanı adlarının çözümlenmesine izin vermek için uygun özyinelemeli çözüm sağlayın.
* Hizmet verdiği ve Internet 'e erişebilen istemcilerden erişilebilir (53 numaralı bağlantı noktası üzerinden TCP ve UDP).
* Dış aracılardan kaynaklanan tehditleri azaltmak için Internet 'ten erişime karşı güvenli hale getirilmesi.

> [!NOTE]
> En iyi performans için, Azure DNS sunucularında sanal makineler kullandığınızda IPv6 'Yı devre dışı bırakın ve her DNS sunucusu sanal makinesine [örnek düzeyi genel IP](../../virtual-network/virtual-networks-instance-level-public-ip.md) atayın.  
>
>
