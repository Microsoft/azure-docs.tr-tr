---
title: Azure Özel DNS nedir?
description: Bu makalede, Microsoft Azure üzerinde özel DNS barındırma hizmetine genel bir bakış ile çalışmaya başlayın.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 04/09/2021
ms.author: rohink
ms.openlocfilehash: 560a88c973d71b3e2c6533e05e4f374f9a5bcd8f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311490"
---
# <a name="what-is-azure-private-dns"></a>Azure Özel DNS nedir?

Etki alanı adı sistemi veya DNS, bir hizmet adını bir IP adresine çevirmekten (veya çözümlemeden) sorumludur.  Azure DNS, etki alanları için bir barındırma hizmetidir ve Microsoft Azure altyapısını kullanarak adlandırma çözümlemesi sağlar. Azure DNS yalnızca İnternet 'e yönelik DNS etki alanlarını destekler, ancak özel DNS bölgelerini de destekler.

Azure Özel DNS, sanal ağınız için güvenilir ve güvenli bir DNS hizmeti sağlar. Azure Özel DNS, özel bir DNS çözümü yapılandırmaya gerek olmadan sanal ağdaki etki alanı adlarını yönetir ve çözümler. Özel DNS bölgelerini kullanarak, dağıtım sırasında Azure tarafından sağlanmış adlar yerine kendi özel etki alanı adınızı kullanabilirsiniz. Özel bir etki alanı adı kullanmak, sanal ağ mimarinizi kuruluşunuzun ihtiyaçlarına en iyi şekilde uyarlamanıza yardımcı olur. Sanal makineler (VM 'Ler) için bir sanal ağ ve bağlı sanal ağlar için bir adlandırma çözümlemesi sağlar. Ayrıca, bölge adlarını bir bölünmüş ufuk görünümüyle yapılandırarak özel ve ortak bir DNS bölgesinin adı paylaşmasına izin verir.

Özel bir DNS bölgesinin kayıtlarını sanal ağınızdan çözümlemek için, sanal ağı bölgeye bağlamanız gerekir. Bağlı sanal ağlar tam erişime sahiptir ve özel bölgede yayınlanan tüm DNS kayıtlarını çözümleyebilir. Ayrıca, bir sanal ağ bağlantısında, oto kaydını da etkinleştirebilirsiniz. Bir sanal ağ bağlantısında oto kaydı etkinleştirdiğinizde, bu sanal ağdaki sanal makinelerin DNS kayıtları özel bölgeye kaydedilir. Azure DNS, bir sanal makine oluşturulduğunda bölge kaydını güncelleştirir, ' IP adresini değiştirir veya silinir.

![DNS genel bakış](./media/private-dns-overview/scenario.png)

> [!NOTE]
> En iyi uygulama olarak, özel DNS bölgeniz için bir *. Local* etki alanı kullanmayın. Tüm işletim sistemleri bunu desteklemez.

## <a name="benefits"></a>Avantajlar

Azure Özel DNS aşağıdaki avantajları sağlar:

* **Özel DNS çözümleri gereksinimini ortadan kaldırır**. Daha önce, birçok müşteri, sanal ağındaki DNS bölgelerini yönetmek için özel DNS çözümleri oluşturmuştur. Artık özel DNS çözümlerini oluşturma ve yönetme yükünü ortadan kaldıran yerel Azure altyapısını kullanarak DNS bölgelerini yönetebilirsiniz.

* **Tüm ortak DNS kayıtları türlerini kullanın**. Azure DNS, bir, AAAA, CNAME, MX, PTR, SOA, SRV ve TXT kayıtlarını destekler.

* **Otomatik ana bilgisayar adı kayıt yönetimi**. Özel DNS kayıtlarınızı barındırmakla birlikte, Azure belirtilen sanal ağlardaki VM 'Ler için ana bilgisayar adı kayıtlarını otomatik olarak korur. Bu senaryoda, özel DNS çözümleri oluşturmaya veya uygulamaları değiştirmeye gerek kalmadan kullandığınız etki alanı adlarını iyileştirebilirsiniz.

* **Sanal ağlar arasında konak adı çözümlemesi**. Azure tarafından sunulan ana bilgisayar adlarından farklı olarak, özel DNS bölgeleri sanal ağlar arasında paylaşılabilir. Bu özellik, sanal ağ eşlemesi gibi ağlar arası ve hizmet bulma senaryolarını basitleştirir.

* **Tanıdık araçlar ve Kullanıcı deneyimi**. Bu hizmet, öğrenme eğrisini azaltmak için iyi kurulu Azure DNS araçları (Azure portal, Azure PowerShell, Azure CLı, Azure Resource Manager şablonları ve REST API) kullanır.

* **Bölünmüş ufuk DNS desteği**. Azure DNS ile, bir sanal ağ içinden ve genel İnternet 'ten farklı yanıtlara çözüm veren aynı ada sahip bölgeler oluşturabilirsiniz. Bölünmüş ufku DNS için tipik bir senaryo, sanal ağınızın içinde kullanılmak üzere bir hizmetin adanmış bir sürümünü sağlamaktır.

* **Tüm Azure bölgelerinde kullanılabilir**. Azure DNS özel bölgeler özelliği, Azure genel bulutundaki tüm Azure bölgelerinde kullanılabilir.

## <a name="capabilities"></a>Özellikler

Azure DNS aşağıdaki özellikleri sağlar:

* **Otomatik kayıt etkin olan bir özel bölgeye bağlı bir sanal ağ sanal makinelerin otomatik olarak kaydı**. Sanal makineler, özel IP adreslerine işaret eden bir kayıt olarak özel bölgeye kaydedilir. Bir sanal ağ bağlantısı içindeki otomatik kayıt etkinken bir sanal makine silindiğinde, Azure DNS aynı zamanda ilgili DNS kaydını bağlantılı özel bölgeden otomatik olarak kaldırır.

* **ILERI DNS çözümlemesi, özel bölgeye bağlı sanal ağlar arasında desteklenir**. Çapraz sanal ağ DNS çözümlemesi için, sanal ağların birbirleriyle eşlenme gibi açık bir bağımlılık yoktur. Bununla birlikte, sanal ağların diğer senaryolar için (örneğin, HTTP trafiği) eşdüzey olmasını isteyebilirsiniz.

* **Ters DNS araması, sanal ağ kapsamı içinde desteklenir**. Özel bir bölgeyle ilişkili özel bir IP için ters DNS araması, son ek olarak ana bilgisayar/kayıt adı ve bölge adı içeren bir FQDN döndürür.

## <a name="other-considerations"></a>Diğer önemli noktalar

Azure DNS aşağıdaki sınırlamalara sahiptir:

* Belirli bir sanal ağ, VM DNS kayıtlarının otomatik kaydı etkinse yalnızca bir özel bölgeye bağlanabilir. Ancak, birden çok sanal ağı tek bir DNS bölgesine bağlayabilirsiniz.
* Ters DNS yalnızca bağlı sanal ağdaki özel IP alanı için geçerlidir
* Bağlı sanal ağdaki özel bir IP adresi için ters DNS, `internal.cloudapp.net` sanal makine için varsayılan sonek olarak döndürülür. Gizli bir IP adresi için bir özel bölgeye bağlı sanal ağlar için, bir özel IP adresi için ters DNS iki FQDN döndürür: biri varsayılan sonekine `internal.cloudapp.net` ve diğeri ise özel bölge sonekine sahip.
* Koşullu iletme Şu anda yerel olarak desteklenmiyor. Azure ile şirket içi ağlar arasında çözünürlüğü etkinleştirmek için bkz. [VM 'ler ve rol örnekleri Için ad çözümlemesi](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
 
## <a name="pricing"></a>Fiyatlandırma

Fiyatlandırma bilgileri için bkz. [Azure DNS fiyatlandırması](https://azure.microsoft.com/pricing/details/dns/).

## <a name="next-steps"></a>Sonraki adımlar

* [Azure PowerShell](./private-dns-getstarted-powershell.md) veya [Azure CLI](./private-dns-getstarted-cli.md)kullanarak Azure DNS bir özel bölge oluşturmayı öğrenin.

* Azure DNS özel bölgelerle gerçekleştirilen bazı yaygın [özel bölge senaryoları](./private-dns-scenarios.md) hakkında bilgi edinin.

* Azure DNS özel bölgeler hakkında sık sorulan sorular ve yanıtlar için, bkz. [özel DNS SSS](./dns-faq-private.md).

* DNS bölgelerini ve [kayıtları](dns-zones-records.md)ziyaret ederek DNS bölgeleri ve kayıtları hakkında bilgi edinin.

* Azure'un diğer önemli [ağ özelliklerinden](../networking/networking-overview.md) bazıları hakkında bilgi edinin.
