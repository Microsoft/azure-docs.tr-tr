---
title: Azure sanal ağlarındaki kaynaklar için ad çözümlemesi
titlesuffix: Azure Virtual Network
description: Farklı bulut Hizmetleri, Active Directory ve kendi DNS sunucunuzu kullanarak Azure IaaS, Karma çözümler için ad çözümleme senaryoları.
services: virtual-network
documentationcenter: na
author: rohinkoul
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/2/2020
ms.author: rohink
ms.custom: fasttrack-edit
ms.openlocfilehash: 80a0b4634a2e84181271b515d2f6f63271cce7f2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784974"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Azure sanal ağlarındaki kaynaklar için ad çözümlemesi

IaaS, PaaS ve hibrit çözümlerini barındırmak için Azure 'u nasıl kullandığınıza bağlı olarak, sanal makinelerin (VM 'Ler) ve bir sanal ağda dağıtılan diğer kaynakların birbirleriyle iletişim kurmasına izin vermeniz gerekebilir. IP adreslerini kullanarak iletişimi etkinleştirebilseniz de, kolayca hatırlanabilme ve değiştirmediğiniz adların kullanılması çok daha basittir. 

Sanal ağlarda dağıtılan kaynakların, etki alanı adlarını iç IP adreslerine çözümlemesi gerektiğinde, bu üç yöntemden birini kullanabilir:

* [Özel bölgeler Azure DNS](../dns/private-dns-overview.md)
* [Azure tarafından sağlanmış ad çözümlemesi](#azure-provided-name-resolution)
* [Kendı DNS sunucunuzu kullanan ad çözümlemesi](#name-resolution-that-uses-your-own-dns-server) (sorguları Azure tarafından sunulan DNS sunucularına iletebilecek)

Kullandığınız ad çözümlemesi türü, kaynaklarınızın birbirleriyle nasıl iletişim kurmaları gerektiğine bağlıdır. Aşağıdaki tabloda senaryolar ve ilgili ad çözümleme çözümleri gösterilmektedir:

> [!NOTE]
> Azure DNS özel bölgeler tercih edilen çözümdür ve DNS bölgelerinizi ve kayıtlarınızı yönetirken size esneklik sağlar. Daha fazla bilgi için bkz. [Azure DNS'yi özel etki alanları için kullanma](../dns/private-dns-overview.md).

> [!NOTE]
> Azure tarafından sağlanmış DNS kullanıyorsanız, sanal makinelerinize uygun DNS son eki otomatik olarak uygulanır. Diğer tüm seçenekler için tam etki alanı adlarını (FQDN) kullanmanız veya uygun DNS sonekini sanal makinelerinize el ile uygulamanız gerekir.

| **Senaryo** | **Çözüm** | **DNS Son Ek** |
| --- | --- | --- |
| Aynı sanal ağda bulunan VM 'Ler veya aynı bulut hizmetindeki Azure Cloud Services rol örnekleri arasındaki ad çözümlemesi. | [Azure DNS özel bölgeler](../dns/private-dns-overview.md) veya [Azure tarafından sağlanmış ad çözümlemesi](#azure-provided-name-resolution) |Ana bilgisayar adı veya FQDN |
| Farklı sanal ağlardaki VM 'Ler veya farklı bulut hizmetlerindeki rol örneklerinde ad çözümleme. |[Özel bölgeler](../dns/private-dns-overview.md) veya, müşteri tarafından yönetilen dns sunucuları Azure DNS sorguları Azure tarafından çözümlenmek üzere sanal ağlar arasında (DNS proxy) iletme. Bkz. [kendı DNS sunucunuzu kullanarak ad çözümlemesi](#name-resolution-that-uses-your-own-dns-server). |Yalnızca FQDN |
| Aynı sanal ağdaki rol örneklerine veya VM 'lere sanal ağ tümleştirmesi kullanarak bir Azure App Service (Web uygulaması, Işlev veya bot) ad çözümlemesi. |Müşteri tarafından yönetilen DNS sunucuları, Azure tarafından çözümlenmek üzere sanal ağlar arasında sorguları ileten (DNS proxy). Bkz. [kendı DNS sunucunuzu kullanarak ad çözümlemesi](#name-resolution-that-uses-your-own-dns-server). |Yalnızca FQDN |
| App Service Web Apps aynı sanal ağdaki VM 'lere ad çözümleme. |Müşteri tarafından yönetilen DNS sunucuları, Azure tarafından çözümlenmek üzere sanal ağlar arasında sorguları ileten (DNS proxy). Bkz. [kendı DNS sunucunuzu kullanarak ad çözümlemesi](#name-resolution-that-uses-your-own-dns-server). |Yalnızca FQDN |
| Bir sanal ağdaki App Service Web Apps farklı bir sanal ağdaki VM 'lere ad çözümleme. |Müşteri tarafından yönetilen DNS sunucuları, Azure tarafından çözümlenmek üzere sanal ağlar arasında sorguları ileten (DNS proxy). Bkz. [kendı DNS sunucunuzu kullanarak ad çözümlemesi](#name-resolution-that-uses-your-own-dns-server). |Yalnızca FQDN |
| Şirket içi bilgisayar ve hizmet adları, VM 'lerden veya Azure 'daki rol örneklerinden çözümlenmektedir. |Müşteri tarafından yönetilen DNS sunucuları (Şirket içi etki alanı denetleyicisi, yerel salt okuma etki alanı denetleyicisi veya bölge aktarımları kullanılarak eşitlenmiş bir DNS ikincil sunucu). Bkz. [kendı DNS sunucunuzu kullanarak ad çözümlemesi](#name-resolution-that-uses-your-own-dns-server). |Yalnızca FQDN |
| Şirket içi bilgisayarlardan Azure ana bilgisayar adları çözümlemesi. |Sorguları karşılık gelen sanal ağdaki müşteri tarafından yönetilen bir DNS proxy sunucusuna ilet, proxy sunucusu sorguları çözümlenmek üzere Azure 'a iletir. Bkz. [kendı DNS sunucunuzu kullanarak ad çözümlemesi](#name-resolution-that-uses-your-own-dns-server). |Yalnızca FQDN |
| İç IP 'Ler için ters DNS. |[Kendı DNS sunucunuzu kullanarak](#name-resolution-that-uses-your-own-dns-server) [özel bölgeler](../dns/private-dns-overview.md) veya [Azure tarafından sağlanmış ad çözümlemesi](#azure-provided-name-resolution) veya ad çözümlemesi Azure DNS. |Uygulanamaz |
| Sanal bir ağda değil, farklı bulut hizmetlerinde bulunan VM 'Ler veya rol örnekleri arasında ad çözümlemesi. |Geçerli değildir. Farklı bulut hizmetlerindeki VM 'Ler ve rol örnekleri arasında bağlantı, sanal ağ dışında desteklenmez. |Uygulanamaz|

## <a name="azure-provided-name-resolution"></a>Azure tarafından sağlanmış ad çözümlemesi

Azure tarafından sağlanan ad çözümlemesi yalnızca temel yetkili DNS özellikleri sağlar. Bu seçeneği kullanırsanız, DNS bölge adları ve kayıtları Azure tarafından otomatik olarak yönetilir ve DNS kayıtlarının kullanım yaşam döngüsü için DNS bölge adlarını veya yaşam döngüsünü denetleyemeyeceksiniz. Sanal ağlarınız için tam özellikli bir DNS çözümüne ihtiyacınız varsa, [Azure DNS özel bölgeler](../dns/private-dns-overview.md) veya [MÜŞTERI tarafından yönetilen DNS sunucuları](#name-resolution-that-uses-your-own-dns-server)kullanmanız gerekir.

Azure, genel DNS adlarını çözümlemesiyle birlikte, aynı sanal ağ veya bulut hizmeti içinde bulunan VM 'Ler ve rol örnekleri için iç ad çözümlemesi sağlar. Bir bulut hizmetindeki sanal makineler ve örnekler aynı DNS sonekini paylaşır, bu nedenle ana bilgisayar adı tek başına yeterlidir. Ancak, klasik dağıtım modeli kullanılarak dağıtılan sanal ağlarda farklı bulut Hizmetleri farklı DNS soneklerine sahiptir. Bu durumda, farklı bulut hizmetleri arasındaki adları çözümlemek için FQDN 'ye ihtiyacınız vardır. Azure Resource Manager dağıtım modeli kullanılarak dağıtılan sanal ağlarda, DNS son eki bir sanal ağ içindeki tüm sanal makineler arasında tutarlıdır, bu nedenle FQDN gerekli değildir. DNS adları, hem VM 'lere hem de ağ arabirimlerine atanabilir. Azure tarafından sağlanmış ad çözümlemesi herhangi bir yapılandırma gerektirmese de, önceki tabloda açıklandığı gibi tüm dağıtım senaryolarında uygun seçim değildir.

> [!NOTE]
> Cloud Services Web ve çalışan rollerini kullanırken, Azure hizmet yönetimi REST API kullanarak rol örneklerinin iç IP adreslerine de erişebilirsiniz. Daha fazla bilgi için bkz. [hizmet yönetimi REST API başvurusu](/previous-versions/azure/ee460799(v=azure.100)). Adres, rol adı ve örnek numarasını temel alır. 
>

### <a name="features"></a>Özellikler

Azure tarafından sunulan ad çözümlemesi aşağıdaki özellikleri içerir:
* Kullanım kolaylığı. Yapılandırma gerekmez.
* Yüksek kullanılabilirlik. Kendi DNS sunucularınızın kümelerini oluşturmanız ve yönetmeniz gerekmez.
* Hizmeti, hem şirket içi hem de Azure ana bilgisayar adlarını çözümlemek için kendi DNS sunucularınızla birlikte kullanabilirsiniz.
* Aynı bulut hizmetindeki sanal makineler ve rol örnekleri arasında ad çözümlemesini bir FQDN gereksinimi olmadan kullanabilirsiniz.
* Bir FQDN 'ye gerek duymadan Azure Resource Manager dağıtım modelini kullanan sanal ağlardaki VM 'Ler arasında ad çözümlemesini kullanabilirsiniz. Klasik dağıtım modelindeki sanal ağlar, farklı bulut hizmetlerindeki adları çözerken bir FQDN gerektirir. 
* Otomatik olarak oluşturulan adlarla çalışmak yerine dağıtımlarınızı en iyi şekilde tanımlayan ana bilgisayar adlarını kullanabilirsiniz.

### <a name="considerations"></a>Dikkat edilmesi gerekenler

Azure tarafından sağlanmış ad çözümlemesi kullanırken göz önünde bulundurmanız gereken noktaları:
* Azure tarafından oluşturulan DNS son eki değiştirilemez.
* DNS arama bir sanal ağın kapsamına alınır. Bir sanal ağ için oluşturulan DNS adları, diğer sanal ağlardan çözümlenemez.
* Kendi kayıtlarınızı el ile kaydedemezsiniz.
* WINS ve NetBIOS desteklenmez. Windows Gezgini 'nde sanal makinelerinizi göremezsiniz.
* Ana bilgisayar adları, DNS ile uyumlu olmalıdır. Adlar yalnızca 0-9, a-z ve '-' karakterlerini kullanmalıdır ve '-' ile başlayamaz veya bitemez.
* DNS sorgu trafiği her VM için kısıtlanıyor. Azaltma birçok uygulamanın etkilenmemelidir. İstek azaltma gözlemlenip, istemci tarafı önbelleğinin etkinleştirildiğinden emin olun. Daha fazla bilgi için bkz. [DNS istemci yapılandırması](#dns-client-configuration).
* Klasik bir dağıtım modelindeki her bir sanal ağ için yalnızca ilk 180 bulut hizmetlerindeki VM 'Ler kaydedilir. Bu sınır Azure Resource Manager sanal ağlara uygulanmaz.
* Azure DNS IP adresi 168.63.129.16. Bu statik bir IP adresidir ve değişmeyecektir.

### <a name="reverse-dns-considerations"></a>Ters DNS konuları
Ters DNS, tüm ARM tabanlı sanal ağlarda desteklenir. Sanal makinelerin IP adreslerini FQDN 'leri sanal makinelerin FQDN 'leri ile eşlemek için ters DNS sorguları (PTR sorguları) verebilirsiniz.
* Sanal makinelerin IP adresleri için tüm PTR sorguları, \[ VMName. internal.cloudapp.net biçiminde FQDN 'ler \] döndürür
* VMName. internal.cloudapp.net biçimindeki FQDN 'lerde ileriye doğru arama, \[ \] sanal MAKINEYE atanan IP adresine çözümlenir.
* Sanal ağ, kayıt sanal ağı olarak bir [Azure DNS özel bölgesine](../dns/private-dns-overview.md) bağlanmışsa, ters DNS sorguları iki kayıt döndürür. Bir kayıt, VMName biçiminde olacaktır \[ \] . [ privatednszonename] ve diğeri \[ VMName \] . internal.cloudapp.net biçiminde olacaktır
* Ters DNS arama, diğer sanal ağlara eşlense bile, belirli bir sanal ağın kapsamına alınır. Eşlenen sanal ağlarda bulunan sanal makinelerin IP adresleri için ters DNS sorguları (PTR sorguları) NXDOMAIN döndürür.
* Bir sanal ağda ters DNS işlevini devre dışı bırakmak istiyorsanız, [Azure DNS özel bölgeler](../dns/private-dns-overview.md) ' i kullanarak bir geriye doğru arama bölgesi oluşturup bu bölgeyi sanal ağınızla bağlantılandırarak bunu yapabilirsiniz. Örneğin, sanal Ağınızın IP adresi alanı 10.20.0.0/16 ise boş bir özel DNS bölgesi 20.10.in-addr. arpa oluşturabilir ve bunu sanal ağa bağlayabilirsiniz. Bölgeyi sanal ağınıza bağlarken bağlantıda otomatik kaydı devre dışı bırakmanız gerekir. Bu bölge, sanal ağ için varsayılan geriye doğru arama bölgelerini geçersiz kılacak ve bu bölge boş olduğu için ters DNS sorgularınız için NXDOMAIN alacaksınız. Özel bir DNS bölgesi oluşturma ve bunu bir sanal ağa bağlama hakkındaki ayrıntılar için [hızlı başlangıç kılavuzumuza](../dns/private-dns-getstarted-portal.md) bakın.

> [!NOTE]
> Ters DNS aramasının sanal ağ genelinde yayılmasını istiyorsanız, [özel bölgeler Azure DNS](../dns/private-dns-overview.md) bir geriye doğru arama bölgesi (In-addr. arpa) oluşturabilir ve bunu birden çok sanal ağa bağlayabilirsiniz. Ancak, sanal makineler için ters DNS kayıtlarını el ile yönetmeniz gerekir.
>


## <a name="dns-client-configuration"></a>DNS istemci yapılandırması

Bu bölümde istemci tarafı önbelleğe alma ve istemci tarafı yeniden denemeleri ele alınmaktadır.

### <a name="client-side-caching"></a>İstemci tarafı önbelleğe alma

Her DNS sorgusunun ağ üzerinden gönderilmesi gerekmez. İstemci tarafı önbelleğe alma, yinelenen DNS sorgularını yerel bir önbellekten çözümleyerek gecikmeyi azaltmaya ve ağ bliitlerine esnekliği iyileştirilmesine yardımcı olur. DNS kayıtları, önbelleğin kaydı yeniliği etkilemeden mümkün olduğunca uzun süre depolamasına izin veren yaşam süresi (TTL) mekanizması içerir. Bu nedenle, çoğu durum için istemci tarafı önbelleğe alma uygundur.

Varsayılan Windows DNS istemcisinde yerleşik bir DNS önbelleği vardır. Bazı Linux dağıtımları önbelleğe alma işlemini varsayılan olarak içermez. Zaten yerel bir önbellek olmadığını fark ederseniz, her bir Linux sanal makinesine bir DNS önbelleği ekleyin.

Kullanılabilir farklı DNS önbelleğe alma paketleri vardır (dnsmasq gibi). Aşağıda, en yaygın dağıtımlardan dnsmasq 'nin nasıl yükleneceği açıklanmaktadır:

* **Ubuntu (resolvconf kullanır)**:
  * Dnsmasq paketini ile birlikte yükler `sudo apt-get install dnsmasq` .
* **SUSE (netconf kullanır)**:
  * Dnsmasq paketini ile birlikte yükler `sudo zypper install dnsmasq` .
  * Dnsmasq hizmetini ile etkinleştirin `systemctl enable dnsmasq.service` . 
  * Dnsmasq hizmetini ile başlatın `systemctl start dnsmasq.service` . 
  * **/Etc/sysconfig/Network/config**' i düzenleyin ve *NETCONFIG_DNS_FORWARDER = ""* değerini *dnsmasq* olarak değiştirin.
  * `netconfig update`Önbelleği yerel DNS Çözümleyicisi olarak ayarlamak için resolv. conf ile güncelleştirin.
* **CentOS (NetworkManager kullanır)**:
  * Dnsmasq paketini ile birlikte yükler `sudo yum install dnsmasq` .
  * Dnsmasq hizmetini ile etkinleştirin `systemctl enable dnsmasq.service` .
  * Dnsmasq hizmetini ile başlatın `systemctl start dnsmasq.service` .
  * **/Etc/dhclient-ah0.conf**' e *etki alanı-adı-sunucu 127.0.0.1;* ekleyin.
  * `service network restart`Önbelleği yerel DNS Çözümleyicisi olarak ayarlamak için ile ağ hizmetini yeniden başlatın.

> [!NOTE]
> Dnsmasq paketi yalnızca Linux için kullanılabilen birçok DNS Önbelleklerinden biridir. Kullanmadan önce, belirli gereksinimleriniz için uygunluğu denetleyin ve başka bir önbelleğin yüklenmediğini denetleyin.

    
### <a name="client-side-retries"></a>İstemci tarafı yeniden denemeleri

DNS birincil olarak bir UDP protokolüdür. UDP Protokolü ileti teslimini garanti etmez, yeniden deneme mantığı DNS protokolünün kendisinde işlenir. Her DNS istemcisi (işletim sistemi), oluşturucunun tercihine bağlı olarak farklı bir yeniden deneme mantığı sergiler:

* Windows işletim sistemleri bir saniye sonra yeniden dener ve sonra bir sonraki iki saniye, dört saniye ve dört saniye sonra yeniden dener. 
* Varsayılan Linux Kurulumu beş saniye sonra yeniden dener. Yeniden deneme belirtimlerinin beş kez, tek saniyelik aralıklarla değiştirilmesini öneririz.

Bir Linux VM 'deki geçerli ayarları kontrol edin `cat /etc/resolv.conf` . *Seçenekler* satırına bakın, örneğin:

```bash
options timeout:1 attempts:5
```

Resolv. conf dosyası genellikle otomatik olarak oluşturulur ve düzenlenmemelidir. *Seçenekler* satırını eklemek için özel adımlar dağıtıma göre farklılık gösterir:

* **Ubuntu** (resolvconf kullanır):
  1. *Options* satırını **/etc/resolvconf/resolv.exe**' ye ekleyin.
  2. `resolvconf -u`Güncelleştirmek için çalıştırın.
* **SUSE** (netconf kullanır):
  1. *Zaman aşımı: 1 deneme: 5* ' e, **/Etc/sysconfig/Network/config** içindeki **NETCONFIG_DNS_RESOLVER_OPTIONS = ""** parametresine ekleyin.
  2. `netconfig update`Güncelleştirmek için çalıştırın.
* **CentOS** (NetworkManager kullanır):
  1. **/Etc/NetworkManager/Dispatcher,d/11-dhclient** için *echo "Options timeout: 1 deneme: 5"* ekleyin.
  2. İle güncelleştirin `service network restart` .

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Kendi DNS sunucunuzu kullanan ad çözümlemesi

Bu bölümde VM 'Ler, rol örnekleri ve Web uygulamaları ele alınmaktadır.

### <a name="vms-and-role-instances"></a>VM 'Ler ve rol örnekleri

Ad çözümleme gereksinimleriniz, Azure tarafından sunulan özelliklerin ötesine geçebilir. Örneğin, Microsoft Windows Server Active Directory etki alanlarını kullanmanız, sanal ağlar arasındaki DNS adlarını çözmeniz gerekebilir. Azure, bu senaryoları karşılamak için kendi DNS sunucularınızı kullanmanıza olanak sağlar.

Bir sanal ağ içindeki DNS sunucuları DNS sorgularını Azure 'daki özyinelemeli çözümleyiciler ile iletebilir. Bu, sanal ağ içindeki ana bilgisayar adlarını çözmenize olanak sağlar. Örneğin, Azure 'da çalışan bir etki alanı denetleyicisi (DC), etki alanları için DNS sorgularına yanıt verebilir ve diğer tüm sorguları Azure 'a iletebilir. Sorguları iletme, VM 'Lerin hem şirket içi kaynaklarınızı (DC aracılığıyla) hem de Azure tarafından belirtilen ana bilgisayar adlarını (iletici aracılığıyla) görmesini sağlar. Azure 'daki özyinelemeli çözümleyiciler erişimi, sanal IP 168.63.129.16 aracılığıyla sağlanır.

DNS iletimi, sanal ağlar arasında DNS çözümlemesi de sağlar ve şirket içi makinelerinizin Azure tarafından belirtilen ana bilgisayar adlarını çözümlemesine olanak tanır. Bir VM 'nin ana bilgisayar adını çözümlemek için, DNS sunucusu sanal makinesi aynı sanal ağda bulunmalı ve ana bilgisayar adı sorgularını Azure 'a iletecek şekilde yapılandırılmalıdır. DNS son eki her bir sanal ağda farklı olduğundan, çözümleme için doğru sanal ağa DNS sorguları göndermek üzere koşullu iletme kurallarını kullanabilirsiniz. Aşağıdaki görüntüde, bu yöntemi kullanarak, sanal ağlar arasında DNS çözümlemesi yapan iki sanal ağ ve şirket içi ağ gösterilmektedir. [Azure hızlı başlangıç şablonları galerisinde](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) ve [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder)'da örnek bir DNS ileticisi vardır.

> [!NOTE]
> Rol örneği aynı sanal ağ içinde VM 'lerin ad çözümlemesini gerçekleştirebilir. Bunu, VM 'nin ana bilgisayar adı ve **Internal.cloudapp.net** DNS SONEKINI içeren FQDN 'yi kullanarak yapar. Ancak, bu durumda, ad çözümlemesi yalnızca rol örneği [rol şemasında (. cscfg dosyası)](/previous-versions/azure/reference/jj156212(v=azure.100))tanımlanmış VM adına sahipse başarılı olur.
> `<Role name="<role-name>" vmName="<vm-name>">`
>
> Başka bir sanal ağdaki VM 'Lerde ad çözümlemesi gerçekleştirmesi gereken rol örnekleri ( **internal.cloudapp.net** SONEKI kullanılarak FQDN) Bu bölümde açıklanan yöntemi (iki sanal ağ arasında iletme) kullanarak bunu gerçekleştirmelidir.
>

![Sanal ağlar arasındaki DNS diyagramı](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Azure tarafından sağlanan ad çözümlemesi kullanırken, Azure dinamik ana bilgisayar Yapılandırma Protokolü (DHCP) her VM için bir iç DNS son eki (**. internal.cloudapp.net**) sağlar. Konak adı kayıtları **internal.cloudapp.net** bölgesinde olduğundan, bu sonek konak adı çözümlemesini mümkün bir şekilde sunar. Kendi ad çözümleme çözümünüzü kullanırken, diğer DNS mimarilerini kesintiye uğratan (etki alanına katılmış senaryolar gibi) bu sonek VM 'lere sağlanmaz. Bunun yerine Azure, işlevsel olmayan bir yer tutucu (*reddog.Microsoft.com*) sağlar.

Gerekirse, PowerShell veya API kullanarak iç DNS sonekini belirleyebilirsiniz:

* Azure Resource Manager dağıtım modellerindeki sanal ağlar için, sonek [ağ arabirimi REST API](/rest/api/virtualnetwork/networkinterfaces), [Get-aznetworkınterface](/powershell/module/az.network/get-aznetworkinterface) PowerShell cmdlet 'i ve [az Network Nic](/cli/azure/network/nic#az_network_nic_show) Azure CLI 'yi göster komutunu kullanarak kullanılabilir.
* Klasik dağıtım modellerinde, son ek [dağıtım API 'si](/previous-versions/azure/reference/ee460804(v=azure.100)) çağrısı veya [Get-AzureVM-Debug](/powershell/module/servicemanagement/azure.service/get-azurevm) cmdlet 'i aracılığıyla kullanılabilir.

Sorguların Azure 'a iletilmesi gereksinimlerinize uygun değilse, kendi DNS çözümünüzü sağlamanız gerekir. DNS çözümünüzün şunları yapması gerekir:

* Örneğin, [DDNS](virtual-networks-name-resolution-ddns.md)aracılığıyla uygun konak adı çözümlemesi sağlayın. DDNS kullanıyorsanız, DNS kaydı atmayı devre dışı bırakmanız gerekebilir. Azure DHCP kiraları uzun ve atma, DNS kayıtlarını erken kaldırabilir. 
* Dış etki alanı adlarının çözümlenmesine izin vermek için uygun özyinelemeli çözüm sağlayın.
* Hizmet verdiği istemcilerden erişilebilir (bağlantı noktası 53 üzerinde TCP ve UDP) ve internet 'e erişebiliyor.
* Dış aracılardan oluşan tehditleri azaltmak için internet 'ten erişime karşı güvenli hale getirilmesi gerekir.

> [!NOTE]
> En iyi performansı elde etmek için, Azure VM 'lerini DNS sunucuları olarak kullanırken, IPv6 devre dışı bırakılmalıdır.

### <a name="web-apps"></a>Web uygulamaları
Sanal bir ağa bağlı App Service, aynı sanal ağdaki VM 'lere kullanarak oluşturulan Web uygulamanızdan ad çözümlemesi gerçekleştirmeniz gerektiğini varsayalım. Sorguları Azure 'a (sanal IP 168.63.129.16) ileten bir DNS ileticisi olan özel bir DNS sunucusu ayarlamaya ek olarak, aşağıdaki adımları uygulayın:
1. [Uygulamanızı bir sanal ağla tümleştirin](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)bölümünde açıklandığı gibi, henüz yapılmazsa, Web uygulamanız için sanal ağ tümleştirmesini etkinleştirin.
2. Azure portal, Web uygulamasını barındıran App Service planı **için ağ,** **sanal ağ tümleştirmesi** altında **ağı Eşitle** ' yi seçin.

    ![Sanal ağ adı çözümlemenin ekran görüntüsü](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Bir sanal ağa bağlı App Service, farklı bir sanal ağdaki VM 'lere, farklı bir sanal ağdaki sanal makinelere bir ad çözümlemesi gerçekleştirmeniz gerekirse, aşağıdaki gibi her iki sanal ağ üzerinde de özel DNS sunucuları kullanmanız gerekir:

* Sorguları Azure 'daki özyinelemeli çözümleyici 'ye (sanal IP 168.63.129.16) iletebilen bir VM 'de hedef sanal ağınızda bir DNS sunucusu ayarlayın. [Azure hızlı başlangıç şablonları galerisinde](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) ve [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder)'da örnek bir DNS ileticisi vardır. 
* Bir VM 'deki kaynak sanal ağda bir DNS ileticisi ayarlayın. Sorguları hedef sanal ağınızdaki DNS sunucusuna iletmek için bu DNS ileticisini yapılandırın.
* Kaynak DNS sunucunuzu kaynak sanal ağınızın ayarlarında yapılandırın.
* [Uygulamanızı sanal ağ Ile tümleştirme](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)bölümündeki yönergeleri izleyerek, Web uygulamanız için sanal ağ tümleştirmesini kaynak sanal ağa bağlamak için etkinleştirin.
* Azure portal, Web uygulamasını barındıran App Service planı **için ağ,** **sanal ağ tümleştirmesi** altında **ağı Eşitle** ' yi seçin.

## <a name="specify-dns-servers"></a>DNS sunucularını belirtme
Kendi DNS sunucularınızı kullanırken Azure, sanal ağ başına birden çok DNS sunucusu belirtme olanağı sağlar. Ayrıca ağ arabirimi başına (Azure Resource Manager için) veya bulut hizmeti başına (klasik dağıtım modeli için) birden çok DNS sunucusu da belirtebilirsiniz. Bir ağ arabirimi veya bulut hizmeti için belirtilen DNS sunucuları, sanal ağ için belirtilen DNS sunucularının önceliği alır.

> [!NOTE]
> DNS sunucusu IP 'Leri gibi ağ bağlantısı özellikleri doğrudan VM 'Ler içinde düzenlenmemelidir. Bunun nedeni, sanal ağ bağdaştırıcısı değiştirildiğinde Service düzelten sırasında silinebilirler. Bu, hem Windows hem de Linux VM 'Leri için geçerlidir.

Azure Resource Manager dağıtım modelini kullanırken, bir sanal ağ ve bir ağ arabirimi için DNS sunucuları belirtebilirsiniz. Ayrıntılar için bkz. [sanal ağı yönetme](manage-virtual-network.md) ve [ağ arabirimini yönetme](virtual-network-network-interface.md).

> [!NOTE]
> Sanal ağınız için özel DNS sunucusu tercih ediyorsanız, en az bir DNS sunucusu IP adresi belirtmeniz gerekir; Aksi halde, sanal ağ yapılandırmayı yoksayar ve bunun yerine Azure tarafından belirtilen DNS 'yi kullanır.

Klasik dağıtım modelini kullanırken, Azure portal veya [ağ yapılandırma dosyasında](/previous-versions/azure/reference/jj157100(v=azure.100))sanal ağ için DNS sunucuları belirtebilirsiniz. Bulut hizmetleri için, [hizmet yapılandırma dosyası](/previous-versions/azure/reference/ee758710(v=azure.100)) aracılığıyla veya PowerShell kullanarak [New-AzureVM](/powershell/module/servicemanagement/azure.service/new-azurevm)ile DNS sunucuları belirtebilirsiniz.

> [!NOTE]
> Zaten dağıtılmış bir sanal ağ veya sanal makine için DNS ayarlarını değiştirirseniz, yeni DNS ayarlarının etkili olabilmesi için, sanal ağdaki tüm etkilenen VM 'lerde bir DHCP kira yenilemesi gerçekleştirmeniz gerekir. Windows işletim sistemini çalıştıran VM 'Ler için, bunu doğrudan VM 'ye yazarak yapabilirsiniz `ipconfig /renew` . Adımlar, işletim sistemine bağlı olarak değişir. İşletim sistemi türü için ilgili belgelere bakın.

## <a name="next-steps"></a>Sonraki adımlar

Azure Resource Manager dağıtım modeli:

* [Sanal ağı yönetme](manage-virtual-network.md)
* [Ağ arabirimini yönetme](virtual-network-network-interface.md)

Klasik dağıtım modeli:

* [Azure hizmeti yapılandırma şeması](/previous-versions/azure/reference/ee758710(v=azure.100))
* [Sanal ağ yapılandırma şeması](/previous-versions/azure/reference/jj157100(v=azure.100))
* [Ağ yapılandırma dosyası kullanarak bir sanal ağ yapılandırma](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file)