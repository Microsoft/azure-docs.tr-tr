---
title: Azure’da desteklenen Linux dağıtımları
description: Ubuntu, CentOS, Oracle ve SUSE için yönergeler de dahil olmak üzere, Azure tarafından onaylanan dağıtımlara yönelik Linux hakkında bilgi edinin.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/04/2019
ms.author: szark
ms.openlocfilehash: 66717222b505b7dd91f7f270cab27ec4ca97038e
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036226"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Azure 'da desteklenen Linux dağıtımları
İş ortakları, Azure Marketi 'nde Linux görüntüleri sağlar. Çeşitli Linux topluluklarıyla birlikte çalışarak, onaylı dağıtım listesine daha da fazla bilgi ekleyebilirsiniz. Bu sırada, Market 'ten kullanılamayan dağıtımlar için, [Linux işletim sistemini içeren bir sanal sabit disk oluşturma ve karşıya yükleme](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic)konusundaki yönergeleri Izleyerek kendi Linux 'u her zaman getirebilirsiniz.

## <a name="supported-distributions-and-versions"></a>Desteklenen dağıtımlar ve sürümler
Aşağıdaki tablo, Azure 'da desteklenen Linux dağıtımlarını ve sürümlerini listelemektedir. Azure 'da Linux ve açık kaynaklı teknoloji desteği hakkında daha ayrıntılı bilgi için [Microsoft Azure Içindeki Linux görüntüleri Için destek](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) bölümüne bakın.

Hyper-V ve Azure için Linux Integration Services (LIS) sürücüleri, Microsoft 'un doğrudan yukarı akış Linux çekirdeğine katkıda bulunduğu çekirdek modüllerdir.  Bazı LIS sürücüleri, varsayılan olarak dağıtımın çekirdeğine yerleştirilmiştir. Red Hat Enterprise (RHEL)/CentOS tabanlı eski dağıtımlar, [Hyper-V ve Azure Için Linux Integration Services sürüm 4,2](https://www.microsoft.com/download/details.aspx?id=55106)' de ayrı bir indirme olarak sunulmaktadır. LIS sürücüleri hakkında daha fazla bilgi için bkz. [Linux çekirdek gereksinimleri](create-upload-generic.md#linux-kernel-requirements) .

Azure Linux Aracısı, Azure Marketi görüntülerinde önceden yüklenmiş olur ve genellikle dağıtımın paket deposundan kullanılabilir. Kaynak kodu [GitHub](https://github.com/azure/walinuxagent)' da bulunabilir.


| Dağıtım | Sürüm | Sürücüler | Aracı |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3 +, 7.0 +, 8.0 + |CentOS 6,3: [LIS Download](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4 +: çekirdekte |Paket: "Walınuxagent" altında [Depo](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) içinde <br/>Kaynak kodu: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |Çekirdekte |Kaynak kodu: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |DE7.9 +, 8.2 +, 9, 10 |Çekirdekte |Paket: "waagent" altında depo Içinde <br/>Kaynak kodu: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+, 7.0+ |Çekirdekte |Paket: "Walınuxagent" altında depo Içinde <br/>Kaynak kodu: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6.7 +, 7.1 +, 8.0 + |Çekirdekte |Paket: "Walınuxagent" altında depo Içinde <br/>Kaynak kodu: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES for SAP<br>11 SP4<br>12 SP1 +<br>15|Çekirdekte |Paket:<p> Bulutta 11 için [: Araçlar](https://build.opensuse.org/project/show/Cloud:Tools) deposu<br>"genel bulut" modülünde "Python-Azure-Agent" altında bulunan 12 için<br/>Kaynak kodu: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE artık 42.2 + |Çekirdekte |Paket: [bulutta:](https://build.opensuse.org/project/show/Cloud:Tools) "Python-Azure-Agent" ın altındaki araçlar deposu <br/>Kaynak kodu: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04 +  **<sup>1</sup>** |Çekirdekte |Paket: "walınuxagent" altında depo Içinde <br/>Kaynak kodu: [GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>** ubuntu 12,04 ve 14,04 için genişletilmiş destek hakkında bilgi burada bulunabilir: [Ubuntu genişletilmiş güvenlik Bakımı](https://www.ubuntu.com/esm).


## <a name="image-update-cadence"></a>Görüntü güncelleştirme temposunda
Azure, onaylı Linux dağıtımlarının yayımcılarının, üç aylık veya daha hızlı bir temposunda en son düzeltme ekleri ve güvenlik düzeltmeleri ile Azure Marketi 'ndeki görüntülerini düzenli olarak güncelleştirilmesini gerektirir. Azure Marketi 'ndeki güncelleştirilmiş görüntüler, bir görüntü SKU 'sunun yeni sürümleri olarak müşteriler tarafından otomatik olarak kullanılabilir. Linux görüntülerini bulma hakkında daha fazla bilgi: [Azure Marketi 'Nde LINUX VM görüntülerini bulun](https://docs.microsoft.com/azure/virtual-machines/linux/cli-ps-findimage).

### <a name="additional-links"></a>Ek bağlantılar
 - [SUSE genel bulut görüntüsü yaşam döngüsü](https://www.suse.com/c/suse-public-cloud-image-life-cycle/)

## <a name="azure-tuned-kernels"></a>Azure tarafından ayarlanan çekirdekler

Azure, Azure Market 'Te yayımladıkları görüntüleri iyileştirmek için, çeşitli desteklenen linux dağıtımlarıyla yakından birlikte çalışmaktadır. Bu işbirliğinin bir yönü, Azure platformu için optimize edilmiş ve Linux dağıtımının tamamen desteklenen bileşenleri olarak sunulan "ayarlanmış" Linux çekirdekleri geliştirmektedir. Azure tarafından ayarlanmış çekirdekler yeni özellikler ve performans iyileştirmeleri ve dağıtım tarafından kullanılabilen varsayılan ya da genel çekirdekler ile karşılaştırıldığında daha hızlı (genellikle üç ayda) temposunda.

Çoğu durumda, bu çekirdekler 'leri Azure Marketi 'ndeki varsayılan görüntülerde önceden yüklenmiş olarak bulacak ve Azure müşterileri bu iyileştirilmiş çekirdekler için hemen avantajına sahip olur. Azure tarafından ayarlanan bu çekirdekler hakkında daha fazla bilgi aşağıdaki bağlantılarda bulunabilir:

 - CentOS Azure-ayarlanmış çekirdek-CentOS sanallaştırma SıG ile kullanılabilir- [daha fazla bilgi](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
 - Bulut çekirdeğini devre dışı bırak- [daha fazla bilgi](https://wiki.debian.org/Cloud/MicrosoftAzure) için 10.
 - SLES Azure-ayarlanmış çekirdek- [daha fazla bilgi](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
 - Ubuntu Azure-ayarlanmış çekirdek- [daha fazla bilgi](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)


## <a name="partners"></a>İş Ortakları

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

CoreOS Web sitesinden:

*CoreOS güvenlik, tutarlılık ve güvenilirlik için tasarlanmıştır. CoreOS, VUM veya apt aracılığıyla paket yüklemek yerine, hizmetlerinizi daha yüksek bir soyutlama düzeyinde yönetmek için Linux kapsayıcıları kullanır. Tek bir hizmetin kodu ve tüm bağımlılıkları, bir veya daha fazla CoreOS makinesi üzerinde çalıştırılabilen bir kapsayıcı içinde paketlenmiştir.*

### <a name="credativ"></a>credativ
[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credadtiv, ücretsiz yazılım kullanarak profesyonel çözümlerin geliştirilmesi ve uygulanması konusunda uzmanlaşmış bağımsız bir danışmanlık ve hizmetler şirketidir. Önde gelen açık kaynaklı uzmanlar olarak, Crefetiv, desteğini kullanan pek çok BT departmanının kullanıldığı Uluslararası tanıma sahiptir. Credadtiv, Microsoft ile birlikte, şu anda Demallik 8 (Jesan) ve 7 ' den önce (Wheezy) ilgili Dekamu görüntülerini hazırlıyor. Her iki görüntü de Azure 'da çalışmak üzere özel olarak tasarlanmıştır ve platform aracılığıyla kolayca yönetilebilir. Credavtiv Ayrıca, Azure için açık kaynak destek merkezleri aracılığıyla uzun süreli bakım ve güncelleştirme görüntülerinin güncelleştirilmesini de destekleyecektir.

### <a name="oracle"></a>Oracle
[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle stratejisi, genel ve özel bulutlara yönelik kapsamlı bir çözüm portföyü sunmamaktadır. Strateji, müşterilere Oracle bulutlarında ve diğer bulutlarda Oracle yazılımı dağıtma konusunda seçim ve esneklik sağlar. Oracle ile Microsoft arasındaki iş ortaklığı sayesinde müşteriler, Oracle tarafından sağlanan sertifika ve desteğin verdiği güvenle Microsoft’un genel ve özel bulutlarında Oracle yazılımlarını dağıtabilir.  Oracle 'ın, Oracle ortak ve özel bulut çözümlerinde taahhüt ve yatırımı değiştirilmez.

### <a name="red-hat"></a>Red Hat
[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

Dünyanın önde gelen açık kaynak çözüm sağlayıcısı olan Red hat, Fortune 500 şirketinin %90 ' ünden daha fazlasına yardımcı olur. Red Hat Bu, açık iş modeli ve uygun maliyetli, öngörülebilir bir abonelik modeli aracılığıyla güvenli çözümler sunarak bunu yapar.

### <a name="suse"></a>SUSE
[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

Azure 'da SUSE Linux Enterprise Server, bulut bilgi işlem için üstün güvenilirlik ve güvenlik sağlayan, kendini kanıtlamış bir platformdur. SUSE 'in çok yönlü Linux platformu, kolayca yönetilebilir bir bulut ortamı sunmak için Azure bulut hizmetleriyle sorunsuz bir şekilde tümleşir. SUSE Linux Enterprise Server için 9.200 ' 1.800 den fazla bağımsız yazılım satıcısından daha fazla sertifikalı uygulama sayesinde, SUSE, veri merkezinde desteklenen çalışan iş yüklerinin Azure 'da güvenle dağıtılmasını sağlar.

### <a name="canonical"></a>Canonical
[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

Kurallı mühendislik ve açık topluluk idare sürücüsü Ubuntu, müşteriler için kişisel bulut hizmetleri içeren istemci, sunucu ve bulut bilgi işlem başarısı. Ubuntu 'da bulunan Birleşik, ücretsiz bir platformun, telefonlardan buluta kadar, telefon, tablet, TV ve Masaüstü için bir dizi tutarlı arabirim sağlar. Bu vizyon, genel bulut sağlayıcılarından farklı kurumlar için birinci seçimi, tüketici elektronileri ve bireysel teknolojik vericiler arasında bir sık kullanılan olarak sunar.

Dünyanın dört bir yanındaki geliştiriciler ve mühendislik merkezleri sayesinde, yaygın olarak donanım üreticileri, içerik sağlayıcıları ve yazılım geliştiricileri olan iş ortaklarına, bilgisayarlar, sunucular ve avuçiçi cihazlar için de Ubuntu çözümlerini pazara sunma olanağı sağlar.
