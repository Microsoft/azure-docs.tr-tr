---
title: Cloud Foundry Azure ile nasıl tümleştirilir | Microsoft Docs
description: Cloud Foundry kurumsal deneyimi geliştirmek için Azure hizmetlerini nasıl kullanabileceğinizi açıklar.
services: virtual-machines-linux
documentationcenter: ''
author: ningk
manager: jeconnoc
editor: ''
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: e341cc5beeb8e8362a848bb1e208ddf1dc773978
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976794"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Cloud Foundry ile Azure’ı tümleştirme

[Cloud Foundry](https://docs.cloudfoundry.org/) , bulut sağlayıcılarının IaaS platformunun en üstünde çalışan bir PaaS platformudur. Bulut sağlayıcıları genelinde tutarlı uygulama dağıtım deneyimi sağlar. Ayrıca kurumsal sınıf HA, ölçeklenebilirlik ve maliyet tasarrufları ile çeşitli Azure hizmetleriyle de tümleştirilebilir.
[Cloud Foundry 6 alt sistemi](https://docs.cloudfoundry.org/concepts/architecture/)vardır; yönlendirme, kimlik doğrulama, uygulama yaşam döngüsü yönetimi, hizmet yönetimi, mesajlaşma ve izleme dahil olmak üzere çevrimiçi olarak ölçeklenebilir. Alt sistemlerin her biri için Cloud Foundry, Azure hizmeti 'ni kullanacak şekilde yapılandırabilirsiniz. 

![Azure tümleştirme mimarisinde Cloud Foundry](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. yüksek kullanılabilirlik ve ölçeklenebilirlik
### <a name="managed-disk"></a>Yönetilen disk
Bosh, disk oluşturma ve silme yordamları için Azure MPE 'yi (bulut sağlayıcısı arabirimi) kullanır. Varsayılan olarak, yönetilmeyen diskler kullanılır. Müşterinin el ile depolama hesapları oluşturmasını ve ardından CF bildirim dosyalarındaki hesapları yapılandırmasını gerektirir. Bunun nedeni, depolama hesabı başına disk sayısı kısıtlamasıdır.
Artık [yönetilen disk](https://azure.microsoft.com/services/managed-disks/) kullanılabilir, sanal makineler için yönetilen güvenli ve güvenilir disk depolama alanı sunar. Müşterinin artık ölçek ve HA için depolama hesabıyla uğraşmak zorunda değildir. Azure diskleri otomatik olarak düzenler. Yeni veya mevcut bir dağıtım olup olmadığı için Azure MPE, bir CF dağıtımı sırasında yönetilen diskin oluşturulmasını veya geçirilmesini işleymeyecektir. PCF 1,11 ile desteklenir. Ayrıca, başvuru için açık kaynaklı Cloud Foundry [yönetilen disk kılavuzunu](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) inceleyebilirsiniz. 
### <a name="availability-zone-"></a>Kullanılabilirlik alanı *
Bulut Yerel uygulama platformu olarak, Cloud Foundry [dört yüksek düzeyde kullanılabilirlik](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html)ile tasarlanmıştır. Yazılım hatalarının ilk üç düzeyi CF sistemi tarafından işlenebilirse de, bulut sağlayıcıları tarafından platform hata toleransı sağlanır. Anahtar CF bileşenleri, bir bulut sağlayıcısının platform HA çözümüyle korunmuş olmalıdır. Bu, GoRouters, Diego Brains, CF veritabanı ve hizmet kutucukları içerir. Varsayılan olarak, [Azure kullanılabilirlik kümesi](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) , bir veri merkezindeki kümeler arasındaki hata toleransı için kullanılır.
Yeni bir deyişle, [Azure kullanılabilirlik bölgesi](https://docs.microsoft.com/azure/availability-zones/az-overview ) şimdi serbest bırakılır ve bu da hata toleransını bir sonraki düzeye getirerek, veri merkezlerinde düşük gecikmeli artıklık sağlar.
Azure kullanılabilirlik bölgesi, 2 + veri merkezlerine bir VM kümesi yerleştirerek, her bir sanal makine kümesi diğer kümeler için gereksizdir. Bir bölge kapalıysa, diğer kümeler hala etkin, olağanüstü durumdan yalıtılmıştır.
> [!NOTE] 
> Azure kullanılabilirlik bölgesi henüz tüm bölgelere sunulmaz, [desteklenen bölgelerin listesi için](https://docs.microsoft.com/azure/availability-zones/az-overview)en son duyuruyu denetleyin. Açık kaynak Cloud Foundry için, [açık kaynak Cloud Foundry Kılavuzu Için Azure kullanılabilirlik bölgesi](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone)' ne bakın.

## <a name="2-network-routing"></a>2. ağ yönlendirme
Varsayılan olarak, Azure temel yük dengeleyici, gelen CF API/uygulama istekleri için kullanılır ve bunları Gorulara ileterek. Diego bey, MySQL, ERT gibi CF bileşenleri, HA trafiğini dengelemek için yük dengeleyiciyi de kullanabilir. Azure, tam olarak yönetilen bir Yük Dengeleme çözümleri kümesi de sağlar. TLS sonlandırmasını ("SSL boşaltması") veya HTTP/HTTPS isteği uygulama katmanı işleme için arıyorsanız, Application Gateway göz önünde bulundurun. Katman 4 ' te yüksek kullanılabilirlik ve ölçeklenebilirlik yük dengelemesi için standart yük dengeleyiciyi göz önünde bulundurun.
### <a name="azure-application-gateway-"></a>Azure Application Gateway *
[Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) , SSL boşaltma, uçtan uca SSL, Web uygulaması güvenlik duvarı, tanımlama bilgisi tabanlı oturum benzeşimi ve daha fazlasını içeren çeşitli katman 7 yük dengeleme özellikleri sunar. [Açık kaynak Cloud Foundry Application Gateway yapılandırabilirsiniz](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). PCF için, POC testi için [pcf 2,1 sürüm notlarına](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) bakın.

### <a name="azure-standard-load-balancer-"></a>Azure Standart Load Balancer *
Azure Load Balancer katman 4 yük dengeleyicidir. Trafiği, yük dengeli bir küme içindeki hizmet örnekleri arasında dağıtmak için kullanılır. Standart sürüm, temel sürümün üstünde [Gelişmiş Özellikler](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) sağlar. Örneğin 1. Arka uç havuzu en yüksek sınırı 100 ' den 1000 VM 'ye yükseltilir.  2. Uç noktalar artık tek kullanılabilirlik kümesi yerine birden çok kullanılabilirlik kümesini destekler.  3. HA bağlantı noktaları, daha zengin izleme verileri vb. gibi ek özellikler. Azure kullanılabilirlik bölgesine taşındıysanız standart yük dengeleyici gereklidir. Yeni bir dağıtım için Azure Standart Load Balancer ile başlamanız önerilir. 

## <a name="3-authentication"></a>3. kimlik doğrulaması 
[Cloud Foundry Kullanıcı hesabı ve kimlik doğrulaması](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) , CF ve çeşitli bileşenleri için merkezi kimlik yönetimi hizmetidir. [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) , Microsoft 'un çok kiracılı, bulut tabanlı dizin ve kimlik yönetimi hizmetidir. Varsayılan olarak, Cloud Foundry kimlik doğrulaması için UAA kullanılır. Ayrıca, UAA, gelişmiş bir seçenek olarak Azure AD 'yi dış kullanıcı deposu olarak da destekler. Azure AD kullanıcıları, Cloud Foundry hesabı olmadan LDAP kimliklerini kullanarak Cloud Foundry erişebilir. [PCF 'de UAA Için Azure AD 'yi yapılandırmak](https://docs.pivotal.io/p-identity/1-6/azure/index.html)için aşağıdaki adımları izleyin.

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. Cloud Foundry çalışma zamanı sistemi için veri depolama
Cloud Foundry, uygulama çalışma zamanı sistem depolaması için Azure blobdeposu veya Azure MySQL/PostgreSQL hizmetlerini kullanmak için harika genişletilebilirlik sağlar.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Cloud Foundry Cloud Controller blobdeposu için Azure blobdeposu
Bulut denetleyicisi blobdeposu, buildpacks, damlacıklar, paketler ve kaynak havuzları için kritik bir veri deposudur. Varsayılan olarak, bulut denetleyicisi BlobStore için NFS sunucusu kullanılır. Tek hata noktasını önlemek için, Azure Blob depolama 'yı dış mağaza olarak kullanın. Arka plan için [Cloud Foundry belgelerine](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) ve Özet [Cloud Foundry seçeneklere](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html)göz atın.

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>Cloud Foundry elastik çalışma zamanı veritabanı olarak MySQL/PostgreSQL *
CF elastik çalışma zamanı iki ana sistem veritabanı gerektirir:
#### <a name="ccdb"></a>CCDB 
Bulut denetleyicisi veritabanı.  Bulut denetleyicisi, istemcilerin sisteme erişmesi için REST API uç noktaları sağlar. CCDB, daha fazla bulut denetleyicisi için, org 'ler, boşluklar, hizmetler, Kullanıcı rolleri ve daha fazlası için tabloları depolar.
#### <a name="uaadb"></a>UAADB 
Kullanıcı hesabı ve kimlik doğrulaması için veritabanı. Kullanıcı kimlik doğrulaması ile ilgili verileri, örneğin şifrelenmiş Kullanıcı adlarını ve parolalarını depolar.

Varsayılan olarak, bir yerel sistem veritabanı (MySQL) kullanılabilir. HA ve ölçeklendirmek için Azure Managed MySQL veya PostgreSQL hizmetlerinden yararlanın. [CCDB, UAADB ve açık kaynaklı Cloud Foundry diğer sistem veritabanları Için Azure MySQL/PostgreSQL etkinleştirme](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service)yönergesi aşağıda verilmiştir.

## <a name="5-open-service-broker"></a>5. Hizmet Aracısı açın
Azure Hizmet Aracısı, uygulamanın Azure Hizmetlerine erişimini yönetmek için tutarlı bir arabirim sunar. [Azure projesi için yeni açık hizmet Aracısı](https://github.com/Azure/open-service-broker-azure) , Hizmetleri Cloud Foundry, OpenShift ve Kubernetes 'te uygulamalara sunmaya yönelik tek ve basit bir yol sağlar. PCF üzerindeki dağıtım yönergeleri için bkz. [PCF Için Azure açık hizmet Aracısı](https://pivotal.io/platform/services-marketplace/data-management/microsoft-azure) .

## <a name="6-metrics-and-logging"></a>6. ölçümler ve günlüğe kaydetme
Azure Log Analytics başlık, [Cloud Foundry loggregator fire'tan](https://docs.cloudfoundry.org/loggregator/architecture.html) [Azure izleyici günlüklerine](https://azure.microsoft.com/services/log-analytics/)olan ölçümleri ileten bir Cloud Foundry bileşenidir. Başlık sayesinde, CF sistem durumu ve performans ölçümlerinizi birden çok dağıtımda toplayabilir, görüntüleyebilir ve çözümleyebilirsiniz.
Azure Log Analytics başlığı 'nı hem açık kaynak hem de Özet Cloud Foundry ortamına dağıtmayı öğrenmek için [buraya](https://docs.microsoft.com/azure/cloudfoundry/cloudfoundry-oms-nozzle) tıklayın ve sonra Azure izleyici günlükleri konsolundan verilere erişin. 
> [!NOTE]
> PCF 2,0 ' den VM 'Ler için BOSH sağlık ölçümleri, varsayılan olarak hortum ve Azure Izleyici günlükleri konsolu ile tümleştirilir.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="7-cost-saving"></a>7. maliyet kaydetme
### <a name="cost-saving-for-devtest-environments"></a>Geliştirme ve test ortamları için maliyet tasarrufu
#### <a name="b-series-"></a>B Serisi: *
F ve D VM Serisi, Özet Cloud Foundry üretim ortamı için sıklıkla önerilse de yeni "Burstable" [B serisi](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) yeni seçenekleri getirir. B serisi Burstable VM 'Ler, Web sunucuları, küçük veritabanları ve geliştirme ve test ortamları gibi CPU 'nun tam performansına gerek olmayan iş yükleri için idealdir. Bu iş yükleri genellikle ani performans gereksinimlerine sahiptir. $0.012/Hour (B1) $0,05/Saat (F1) ile karşılaştırıldığında, Ayrıntılar için [VM boyutlarının](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general) tam listesine ve [fiyatlarına](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) bakın. 
#### <a name="managed-standard-disk"></a>Yönetilen standart disk: 
Üretim ortamında güvenilir performans için Premium diskler önerilmıştı.  [Yönetilen disk](https://azure.microsoft.com/services/managed-disks/)ile standart depolama, farklı performansla benzer bir güvenilirlik de sunabilir. Geliştirme/test veya kritik olmayan ortam gibi performans açısından hassas olmayan iş yükü için, yönetilen standart diskler daha düşük maliyetli alternatif bir seçenek sunar.  
### <a name="cost-saving-in-general"></a>Genel olarak maliyet kaydetme 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>Azure ayırmaları ile çok önemli VM maliyeti kaydetme: 
Günümüzde tüm CF VM 'Leri, ortamlar genellikle sonsuza kadar sürekli kalsa da "talep üzerine" fiyatlandırması kullanılarak faturalandırılır. Artık 1 veya 3 yıllık bir dönemde VM kapasitesini ayırabilir ve% 45-65 indirim elde edebilirsiniz. İndirimler, ortamınızda hiçbir değişiklik yapmadan faturalandırma sistemine uygulanır. Ayrıntılar için bkz. [Azure ayırmaları nasıl kullanılır](https://azure.microsoft.com/pricing/reserved-vm-instances/). 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Daha küçük boyutlarda yönetilen Premium disk: 
Yönetilen diskler, hem Premium hem de Standart diskler için P4 (32 GB) ve P6 (64 GB) gibi daha küçük disk boyutlarını destekler. Küçük iş yükleriniz varsa Standart Premium disklerden yönetilen Premium disklere geçiş yaparken maliyeti tasarrufu sağlayabilirsiniz.
#### <a name="use-azure-first-party-services"></a>Azure birinci taraf hizmetlerini kullanın: 
Azure 'un birinci taraf hizmetinden yararlanmak, yukarıdaki bölümlerde bahsedilen HA ve güvenilirliğe ek olarak uzun vadeli yönetim maliyetini düşürmenize olanak sağlar. 

Özet, PCF müşterileri için [küçük bir ayak kaplama ert](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) başlattı, bileşenlerin en fazla 2500 uygulama örneği çalıştırılarak yalnızca 4 VM 'de birlikte bulunur. Deneme sürümü artık [Azure pazar yeri](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)üzerinden kullanılabilir.

## <a name="next-steps"></a>Sonraki Adımlar
Azure tümleştirme özellikleri, ilk olarak [açık kaynak Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/)ile kullanılabilir ve Özet Cloud Foundry kullanılabilir. \* İle işaretlenmiş Özellikler, PCF aracılığıyla yine de kullanılamaz. Azure Stack ile Cloud Foundry tümleştirme bu belgede kapsanmaz.
\* İle işaretlenmiş Özellikler üzerinde PCF desteği veya Azure Stack tümleştirmesi Cloud Foundry, en son durum için özetleme ve Microsoft hesabı yöneticinize başvurun. 

