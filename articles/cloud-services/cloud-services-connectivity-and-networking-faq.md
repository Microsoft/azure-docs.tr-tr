---
title: Microsoft Azure Cloud Services SSS için bağlantı ve ağ sorunları | Microsoft Docs
description: Bu makalede Microsoft Azure Cloud Services için bağlantı ve ağ hakkında sık sorulan sorular listelenmektedir.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/23/2018
ms.author: genli
ms.openlocfilehash: c6d470b9c14f53558d09e6876701cb25ddc15183
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154869"
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Azure Cloud Services için bağlantı ve ağ sorunları: Sık sorulan sorular (SSS)

Bu makalede, [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services)bağlantı ve ağ sorunları hakkında sık sorulan sorular yer almaktadır. Boyut bilgileri için [Cloud SERVICES VM boyutu sayfasına](cloud-services-sizes-specs.md)bakın.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Bir IP 'yi çoklu VIP bulut hizmetinde ayıramıyorum.
İlk olarak, için IP 'yi ayırmak istediğiniz sanal makine örneğinin açık olduğundan emin olun. İkinci olarak, hem hazırlama hem de üretim dağıtımları için ayrılmış IP 'Leri kullandığınızdan emin olun. Dağıtım yükseltilirken *ayarları değiştirmeyin.*

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>NSG 'ye sahip olduğumu Uzak Masaüstü Nasıl yaparım? mi kullanıyorsunuz?
NSG 'ye, **3389** ve **20000**bağlantı noktalarında trafiğe izin veren kurallar ekleyin. Uzak Masaüstü **3389**numaralı bağlantı noktasını kullanır. Bulut hizmeti örnekleri yük dengedir, bu nedenle hangi örneğin bağlanılacağını doğrudan kontrol edebilirsiniz. *Remoteforwarder* ve *RemoteAccess* ARACıLARı, Uzak Masaüstü Protokolü (RDP) trafiğini YÖNETIR ve istemcinin bir RDP tanımlama bilgisi göndermesini ve bağlanılacak tek bir örnek belirtmesini sağlar. *Remoteforwarder* ve *RemoteAccess* aracılarının bağlantı noktası **20000** ' ün açık olması gerekir. Bu, bir NSG varsa engellenebilir.

## <a name="can-i-ping-a-cloud-service"></a>Bir bulut hizmetine ping yapabilir miyim?

Hayır, normal "ping"/ıCMP protokolü kullanılarak değil. ICMP protokolüne Azure yük dengeleyici üzerinden izin verilmiyor.

Bağlantıyı test etmek için bir bağlantı noktası ping işlemi yapmanızı öneririz. Ping. exe ıCMP kullandığından, belirli bir TCP bağlantı noktasıyla bağlantıyı sınamak için PSPing, nmap ve Telnet gibi diğer araçları kullanabilirsiniz.

Daha fazla bilgi için bkz: [bağlantı noktasına ping ICMP yerine Azure VM bağlantıyı sınamak için kullanın](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Nasıl yaparım?, bilinmeyen IP adreslerinden gelen ve bulut hizmeti için kötü amaçlı saldırı olabilecek binlerce isabetlerin alınmasını engelliyor mu?
Azure, platform hizmetlerini dağıtılmış hizmet reddi (DDoS) saldırılarına karşı korumak için çok katmanlı bir ağ güvenliği uygular. Azure DDoS savunma sistemi, Azure 'un sürekli izleme sürecinin bir parçasıdır ve bu da sızma testi aracılığıyla sürekli olarak geliştirilmiştir. Bu DDoS savunma sistemi, diğer Azure kiracılarından de yalnızca dışarıdaki saldırıları değil, yalnızca dışarıdan gerçekleştirilen saldırıları değil. Daha fazla bilgi için bkz. [Azure ağ güvenliği](https://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Belirli IP adreslerini seçmeli olarak engellemek için de bir başlangıç görevi oluşturabilirsiniz. Daha fazla bilgi için bkz. [belirli BIR IP adresini engelleme](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Bulut hizmeti örneğimde RDP 'yi deneydiğimde "Kullanıcı hesabının süresi doldu" iletisini alıyorum.
RDP ayarlarınızda yapılandırılan sona erme tarihini atladığınızda "Bu Kullanıcı hesabının süresi doldu" hata iletisini alabilirsiniz. Aşağıdaki adımları izleyerek, son kullanma tarihini portaldan değiştirebilirsiniz:

1. [Azure Portal](https://portal.azure.com)oturum açın, bulut hizmetinize gidin ve **Uzak Masaüstü** sekmesini seçin.

2. **Üretim** veya **hazırlama** dağıtım yuvasını seçin.

3. **Tarihi bitiş tarihini değiştirin** ve ardından yapılandırmayı kaydedin.

Artık makinenize RDP ile kullanabileceksiniz.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Trafiği neden eşit olarak dengelemeden Azure Load Balancer?
İç yük dengeleyicinin nasıl çalıştığı hakkında bilgi için, [Azure Load Balancer yeni dağıtım modu](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/)' na bakın.

Kullanılan dağıtım algoritması, trafiği kullanılabilir sunucularla eşlemek için 5 tanımlama grubu (kaynak IP, kaynak bağlantı noktası, hedef IP, hedef bağlantı noktası ve protokol türü) karmasıdır. Bu, yalnızca bir aktarım oturumunda sürekliliği sağlar. Aynı TCP veya UDP oturumunda bulunan paketler, yük dengeli uç noktanın arkasındaki aynı veri merkezi IP (DIP) örneğine yönlendirilir. İstemci bağlantıyı kapatır ve yeniden açar veya aynı kaynak IP 'sinden yeni bir oturum başlattığında, kaynak bağlantı noktası değişir ve trafiğin farklı bir DIP uç noktasına geçmesine neden olur.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>Gelen trafiği bulut hizmetimin varsayılan URL 'sine özel bir URL 'ye nasıl yönlendirebilirim?

IIS 'nin URL yeniden yazma modülü, bulut hizmeti için varsayılan URL 'ye gelen trafiği (örneğin, \*. cloudapp.net) bir özel ad/URL 'ye yönlendirmek için kullanılabilir. URL yeniden yazma modülü Web rolleri üzerinde varsayılan olarak etkinleştirildiğinden ve kuralları uygulamanın Web. config dosyasında yapılandırılmışsa, yeniden başlatmalar/regörüntülerden bağımsız olarak her zaman VM 'de kullanılabilir. Daha fazla bilgi için bkz.

- [URL yeniden yazma modülü için yeniden yazma kuralları oluşturma](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Varsayılan bağlantıyı kaldır](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Bulut hizmetimin varsayılan URL 'SI için gelen trafiği nasıl engelleyebilir/devre dışı bırakabilirim?

Bulut hizmetinizin varsayılan URL 'sine/adına gelen trafiği engelleyebilirsiniz (örneğin, \*. cloudapp.net). Belirtilen şekilde, bulut hizmeti tanımı (*. csdef) dosyasındaki site bağlama\.yapılandırması altında, ana bilgisayar üst bilgisini özel bir DNS adına (örneğin, www MyCloudService.com) ayarlayın:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicesDemo" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
    <WebRole name="MyWebRole" vmsize="Small">
        <Sites>
            <Site name="Web">
            <Bindings>
                <Binding name="Endpoint1" endpointName="Endpoint1" hostHeader="www.MyCloudService.com" />
            </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
</ServiceDefinition>
```

Bu konak üst bilgi bağlaması, csdef dosyası aracılığıyla zorlandığından, hizmete yalnızca "www.MyCloudService.com" özel adı üzerinden erişilebilir. "*. Cloudapp.net" etki alanına yapılan tüm gelen istekler her zaman başarısız olur. Hizmette özel bir SLB araştırması veya bir iç yük dengeleyici kullanırsanız, hizmetin varsayılan URL 'sini/adını engellemek, araştırma davranışına engel olabilirler.

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Bir bulut hizmetinin genel kullanıma açık IP adresinin hiçbir şekilde değişmeme biçimini nasıl yapabilirim?

Bulut hizmetinizin genel kullanıma açık IP adresinin (VIP olarak da bilinir) hiçbir şekilde değişmeyeceğinden emin olmak için, bazı belirli istemciler tarafından geleneksel beyaz listeye alınabilmesi için, onunla ilişkili ayrılmış bir IP 'niz olması önerilir. Aksi takdirde, dağıtımı sildiğinizde Azure tarafından sunulan sanal IP aboneliğinizden serbest bırakılır. Başarılı VIP değiştirme işlemi için, hem üretim hem de hazırlama yuvaları için ayrı ayrı ayrılmış IP 'Ler gerekir. Bu işlemler olmadan değiştirme işlemi başarısız olur. Bir IP adresi ayırmak ve bulut hizmetinize ilişkilendirmek için şu makalelere bakın:

- [Mevcut bir bulut hizmetinin IP adresini ayır](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
- [Hizmet yapılandırma dosyası kullanarak ayrılmış IP 'yi bulut hizmetiyle ilişkilendirme](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

Rolleriniz için birden fazla örneğiniz varsa, RIP 'yi bulut hizmetinize ilişkilendirerek kapalı kalma süresi olmaz. Alternatif olarak, Azure veri merkezinizin IP aralığını izin verilenler listesine ekleyebilirsiniz. Tüm Azure IP aralıklarını [Microsoft Indirme merkezi](https://www.microsoft.com/en-us/download/details.aspx?id=41653)' nde bulabilirsiniz.

Bu dosya, Azure veri merkezlerinde kullanılan IP adresi aralıklarını (işlem, SQL ve depolama aralıkları dahil) içerir. Güncelleştirilmiş bir dosya, şu anda dağıtılmış aralıkları ve IP aralıklarında yapılan tüm değişiklikleri yansıtan haftalık olarak gönderilir. Dosyada görüntülenen yeni aralıklar, en az bir hafta için veri merkezlerinde kullanılmıyor. Yeni. xml dosyasını her hafta indirin ve Azure 'da çalışan hizmetleri doğru şekilde tanımlamak için sitenizde gerekli değişiklikleri gerçekleştirin. Azure ExpressRoute kullanıcıları bu dosyanın, her ayın ilk haftasında Azure alanının BGP tanıtımını güncelleştirmek için kullanıldığını unutmayın.

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Bulut hizmetleriyle Azure Resource Manager sanal ağları nasıl kullanabilirim?

Bulut hizmetleri Azure Resource Manager sanal ağlara yerleştirilemez. Kaynak Yöneticisi sanal ağlar ve klasik dağıtım sanal ağları, eşleme ile bağlanabilir. Daha fazla bilgi için bkz. [sanal ağ eşlemesi](../virtual-network/virtual-network-peering-overview.md).


## <a name="how-can-i-get-the-list-of-public-ips-used-by-my-cloud-services"></a>Cloud Services tarafından kullanılan genel IP 'lerin listesini nasıl alabilirim?

Aboneliğiniz altındaki Cloud Services genel IP 'lerin listesini almak için aşağıdaki PS betiğini kullanabilirsiniz

```powershell
$services = Get-AzureService  | Group-Object -Property ServiceName

foreach ($service in $services)
{
    "Cloud Service '$($service.Name)'"

    $deployment = Get-AzureDeployment -ServiceName $service.Name
    "VIP - " +  $deployment.VirtualIPs[0].Address
    "================================="
}
```
