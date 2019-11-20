---
title: Azure Lab Services Hakkında | Microsoft Docs
description: Lab Services’in geliştiriciler, test uzmanları, eğitimciler, öğrenciler ve diğerleri tarafından kullanılabilen sanal makinelerle laboratuvar oluşturma, yönetme ve güvenli hale getirmeyi nasıl kolaylaştırabildiğini öğrenin.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/13/2018
ms.author: spelluru
ms.openlocfilehash: 115320a8b4ee7afc6e420dcfa96612b91ea6d1a0
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790763"
---
# <a name="an-introduction-to-azure-lab-services"></a>Azure Lab Services’a giriş
Azure 'da laboratuvar ortamlarını bulutta ayarlamanıza olanak tanıyan iki hizmet vardır. 

- **Azure DevTest Labs** -bu hizmet ekibiniz için hızlı bir ortam ayarlamanıza olanak sağlar (örneğin, geliştirme ortamı veya bulutta test ortamı). Laboratuvar sahibi laboratuvarı oluşturur, Windows veya Linux sanal makineleri sağlar, gerekli yazılım ve araçları yükler ve laboratuvar kullanıcıları için kullanılabilir hale getirir. Laboratuvar kullanıcıları laboratuvara sanal makinelere (VM 'Ler) bağlanır ve bunları gündelik iş ve kısa vadeli projeler için kullanır. Kullanıcılar laboratuvardaki kaynakları kullanmaya başladıktan sonra, laboratuvar yöneticisi birden fazla laboratuvardaki maliyet ve kullanımı analiz edebilir ve kuruluşunuzun veya takımınızın maliyetlerini en iyi duruma getirmeye yönelik kapsayıcı ilkeler ayarlayabilir.
- **Azure Lab Services** -bu hizmet, yönetilen laboratuvar türleri oluşturmanıza olanak sağlar. Şu anda, sınıf laboratuvarları Azure Lab Services tarafından desteklenen tek bir yönetilen laboratuvar türüdür. Hizmet, yönetilen bir laboratuvar türü için tüm altyapı yönetimini, VM 'Leri hataları işlemeye ve altyapıyı ölçeklendirmeye kadar işler. BT Yöneticisi Azure Lab Services bir laboratuvar hesabı oluşturduktan sonra, bir eğitmen kendi sınıfı için hızlıca laboratuvar ayarlayabilir, sınıfta uygulanabilir olan VM 'lerin sayısını ve türünü belirtebilir ve sınıfa Kullanıcı eklemektir. Kullanıcı sınıfa kaydolduktan sonra, Kullanıcı, sınıf için alıştırmaları yapmak üzere VM 'ye erişebilir.  

## <a name="key-capabilities"></a>Temel işlevler

Bu hizmetler (Azure DevTest Labs ve Azure Lab Services) aşağıdaki temel özellikleri/özellikleri destekler:

- **Hızlı ve esnek bir laboratuvar kurulumu**. Laboratuvar sahipleri Azure Lab Services’i kullanarak gereksinimlerine uygun bir laboratuvarı hızlıca ayarlayabilir. Hizmet, yönetilen laboratuvar türleri için tüm Azure altyapı çalışmalarını alma veya laboratuvar sahiplerinin laboratuvar sahibi aboneliğindeki altyapıyı kendi kendine yönetmesine ve özelleştirmesine olanak tanımak için seçeneği sunar. Hizmet, sizin yerinize yönettiği laboratuvarlar için yerleşik ölçeklendirme ve esneklik özelliği sağlar.
- **Laboratuvar kullanıcıları için basitleştirilmiş deneyim**. Sınıf Laboratuvarı gibi bir yönetilen laboratuvar türünde, laboratuar kullanıcıları kayıt kodu içeren bir laboratuvara kaydoya kaydedebilir ve laboratuvar kaynaklarını kullanmak için her zaman laboratuvara erişebilir. DevTest Labs hizmetinde oluşturulan bir laboratuvarda laboratuvar sahibi, laboratuvar kullanıcılarına sanal makine oluşturma ve sanal makinelere erişme, veri disklerini yönetme ve yeniden kullanma ve yeniden kullanılabilir gizli diziler ayarlama izinleri verebilir.  
- **Maliyet iyileştirme ve analizi**. Laboratuvar sahibi, sanal makineleri otomatik olarak kapatmak ve başlatmak için laboratuvar zamanlamaları ayarlayabilir. Laboratuvar sahibi, laboratuvarın sanal makinelerine kullanıcılar tarafından erişilebildiğinde zaman dilimlerini belirlemek üzere bir zamanlama ayarlayabilir, maliyeti iyileştirmek için kullanıcı ya da laboratuvar başına kullanım ilkeleri belirleyebilir ve bir laboratuvardaki kullanım ve etkinlik eğilimlerini analiz edebilir. Sınıf laboratuvarları gibi yönetilen laboratuvar türleri için şu anda daha küçük bir maliyet iyileştirmesi ve analiz seçenekleri alt kümesi bulunabilir.
- **Yerleşik güvenlik**. Laboratuvar sahibi, özel bir sanal ağ ve bir laboratuvar alt ağı oluşturabilir ve paylaşılan bir genel IP adresini etkinleştirebilir. Laboratuvar kullanıcıları, ExpressRoute veya siteden siteye VPN ile yapılandırılan sanal ağı kullanarak kaynaklara güvenle erişebilir. (şu anda yalnızca DevTest Labs ile kullanılabilir)
- **İş akışlarınız ve araçlarınızla tümleştirme**. Azure Lab Services, laboratuvarları kuruluşunuzun web sitesi ve yönetim sistemleri ile tümleştirme olanağı sağlar. Ortamları sürekli tümleştirme/sürekli dağıtım (CI/CD) araçlarınızın içinden otomatik olarak sağlayabilirsiniz. (şu anda yalnızca DevTest Labs ile kullanılabilir)

> [!NOTE]
> Şu anda Azure Lab Services yalnızca Azure Market görüntülerinden oluşturulan sanal makineleri destekler. Özel görüntüler kullanmak veya laboratuvar ortamında başka PaaS kaynakları oluşturmak istiyorsanız, DevTest Labs kullanın. Daha fazla bilgi için bkz. [DevTest Labs 'de özel görüntü oluşturma](devtest-lab-create-custom-image-from-vm-using-portal.md) ve [Kaynak Yöneticisi şablonları kullanarak laboratuvar ortamları oluşturma](devtest-lab-create-environment-from-arm.md).

## <a name="scenarios"></a>Senaryolar

Azure DevTest Labs ve Azure Lab Services desteği olan senaryolardan bazıları şunlardır:

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>Bulutta sınıfınız için yeniden boyutlandırılabilen bir bilgisayar laboratuvarı oluşturma  

- Yönetilen bir sınıf laboratuvarı oluşturun. Hizmete tam olarak ihtiyacınız olanları söylediğinizde laboratuvarın altyapısını sizin için oluşturup yönetir, böylece siz de laboratuvarın ayrıntılarına değil, ders vermeye odaklanabilirsiniz.
- Öğrencilere tam olarak bir sınıfın gereksinimleriyle yapılandırılmış sanal makinelerden oluşan bir laboratuvar sağlayın. Her öğrenciye, sınıf çalışmaları için VM’leri kullanabilecekleri sınırlı sayıda saat verin.  
- Okulunuzun fiziksel bilgisayar laboratuvarını buluta taşıyın. VM sayısını yalnızca laboratuvarda ayarladığınız üst kullanım sınırı ve maliyet eşiğine göre otomatik olarak ölçeklendirin.
- İşiniz bittiğinde tek bir tıklama ile laboratuvarı silin.

### <a name="use-devtest-labs-for-development-environments"></a>Geliştirme ortamları için DevTest Labs kullanma

Azure DevTest Labs, çok sayıda önemli senaryoyu uygulamak için kullanılabilir ancak başlıca senaryolardan biri, geliştiriciler için geliştirme makinelerini barındırmak üzere DevTest Labs kullanmayı içerir. Bu senaryoda DevTest Labs şu avantajları sağlar:

- Geliştiricilerin isteğe bağlı olarak geliştirme makinelerini hızlıca sağlaması.
- Yeniden kullanılabilir şablonları ve yapıtları kullanarak Windows ve Linux ortamları sağlama
- Geliştiriciler, geliştirme makinelerini gerekli olan her durumda özelleştirebilir.
- Yöneticiler, geliştiricilerin geliştirme için gerektiğinde daha fazla VM alamamasını ve kullanımda olmadığında VM’lerin kapanmasını sağlayarak maliyetleri kontrol edebilir.

Daha fazla bilgi için bkz. [Geliştirme için DevTest Labs kullanma](devtest-lab-developer-lab.md).

### <a name="use-devtest-labs-for-test-environments"></a>Test ortamları için DevTest Labs kullanma

Azure DevTest Labs’i çok sayıda önemli senaryoyu uygulamak için kullanabilirsiniz ancak başlıca senaryolardan biri, test uzmanları için makineleri barındırmak üzere DevTest Labs kullanmayı içerir. Bu senaryoda DevTest Labs şu avantajları sağlar:

- Test uzmanları, yeniden kullanılabilir şablonları ve yapıtları kullanarak Windows ve Linux ortamlarını hızla sağlayabilir ve bu sayede uygulamalarının en son sürümünü test edebilir.
- Test uzmanları birden fazla test aracısı sağlayarak yük testlerinin ölçeğini artırabilirler.
- Yöneticiler, test uzmanlarının test için gerektiğinde daha fazla VM alamamasını ve kullanımda olmadığında VM’lerin kapanmasını sağlayarak maliyetleri kontrol edebilir.

Daha fazla bilgi için bkz. [Test için DevTest Labs kullanma](devtest-lab-test-env.md).

## <a name="types-of-labs"></a>Laboratuvarların türleri
İki tür laboratuvar oluşturabilirsiniz: Azure Lab Services ile Azure Lab Services ve **laboratuvarlarla** **yönetilen laboratuvar türleri** . Yalnızca bir laboratuvarda ihtiyacınız olanları girmek ve hizmetin laboratuvar için gereken altyapıyı ayarlayıp yönetmesine izin vermek istiyorsanız, **yönetilen laboratuvar türlerinden**birini seçin. Şu anda, **sınıf laboratuvarı** Azure Lab Services oluşturabileceğiniz tek yönetilen laboratuvar türüdür. Kendi altyapınızı yönetmek istiyorsanız **Azure DevTest Labs**kullanarak bir laboratuvar oluşturun.

Aşağıdaki bölümlerde bu laboratuvarlar hakkında daha ayrıntılı bilgi verilmektedir. 

## <a name="managed-lab-types"></a>Yönetilen laboratuvar türleri
Azure Lab Services, altyapısı Azure tarafından yönetilen laboratuvarlar oluşturmanızı sağlar. Bu makale, bunlara yönetilen laboratuvar türleri olarak başvurur. Yönetilen laboratuvar türleri, özel gereksinimlerinize uyan farklı türde laboratuvarlar sunar. Şu anda yalnızca desteklenen laboratuvar türü **sınıf laboratuvarıdır**. 

Yönetilen laboratuvar türleri, en az kurulum ile hemen çalışmaya başlamanızı sağlar. Hizmet, VM’leri tasarlamaktan hataları işlemeye ve altyapıyı ölçeklendirmeye varan tüm laboratuvar altyapısı yönetimi konularını ele alır. Sınıf Laboratuvarı gibi bir yönetilen laboratuvar türü oluşturmak için önce kuruluşunuz için bir laboratuar hesabı oluşturmanız gerekir. Laboratuvar hesabı, kuruluştaki tüm laboratuvarların yönetildiği merkezi hesap olarak görev yapar. 

Azure kaynaklarını oluşturduğunuz ve bu yönetilen laboratuvar türlerinde kullandığınızda hizmet, iç Microsoft aboneliklerinde kaynakları oluşturur ve yönetir. Bunlar sizin Azure aboneliğinizde oluşturulmaz. Hizmet bu kaynakların dahili Microsoft aboneliklerindeki kullanımını takip eder. Bu kullanım, laboratuvar hesabını içeren Azure aboneliğinize faturalanır.   

**Yönetilen laboratuvar türleri için kullanım örneklerinin**bazıları şunlardır: 

- Öğrencilere tam olarak bir sınıfın gereksinimleriyle yapılandırılmış sanal makinelerden oluşan bir laboratuvar sağlayın. Her öğrenciye, ev ödevi veya kişisel projeleri için VM’leri kullanabilecekleri sınırlı sayıda saat verin.
- İşlem yoğunluklu veya grafik yoğunluklu araştırmalar gerçekleştirmek üzere yüksek performanslı işlem VM’leri içeren bir havuz oluşturun. Gerektiğinde VM’leri çalıştırın ve işiniz bittikten sonra makineleri temizleyin. 
- Okulunuzun fiziksel bilgisayar laboratuvarını buluta taşıyın. VM sayısını yalnızca laboratuvarda ayarladığınız üst kullanım sınırı ve maliyet eşiğine göre otomatik olarak ölçeklendirin.  
- Bir hackathon barındırmak için hızlıca bir sanal makine laboratuvarı sağlayın. İşiniz bittiğinde tek bir tıklama ile laboratuvarı silin. 


## <a name="devtest-labs"></a>DevTest Labs
Tüm altyapıyı ve yapılandırmayı kendi başınıza, kendi aboneliğiniz içinde yönetmek istediğiniz senaryolar olabilir. Bunu yapmak için, Azure portalda Azure DevTest Labs ile bir laboratuvar oluşturabilirsiniz. Bu laboratuvarlar için bir laboratuar hesabı oluşturmanız gerekmez. Bu laboratuvarlar laboratuvar hesabında gösterilmez (yönetilen laboratuvar türleri için mevcuttur).  

**DevTest Labs kullanmaya ilişkin kullanım örneklerinden** bazıları aşağıda verilmiştir: 

- Bir hackathon veya bir konferansta uygulamalı oturum barındırmak için hızlıca bir sanal makine laboratuvarı sağlayın. İşiniz bittiğinde tek bir tıklama ile laboratuvarı silin. 
- Uygulamanızla yapılandırılan bir VM havuzu oluşturun ve takımınızın yoğun hata ayıklama için bir sanal makineyi kolayca kullanmasını sağlayın.  
- Geliştiricilere, ihtiyaç duydukları tüm araçlarla yapılandırılmış sanal makineler sağlayın. Maliyeti en aza indirmek için otomatik başlatma ve kapatma zamanlayın. 
- Dağıtımınızın bir parçası olarak tekrarlanan şekilde bir test makineleri laboratuvarı oluşturun. En son bitleri test edin ve işiniz bittiğinde test makinelerini temizleyin. 
- Farklı şekilde yapılandırılmış çeşitli sanal makineler ve ölçeklendirme ile performans testi için birden fazla test aracısı ayarlayın. 
- Ürününüzün en son sürümü ile yapılandırılmış bir laboratuvar kullanarak müşterilerinize eğitim oturumları sunun. Her müşteriye laboratuvarda kullanmak üzere sınırlı sayıda saat verin. 


## <a name="managed-lab-types-vs-devtest-labs"></a>Yönetilen laboratuvar türleri ile DevTest Labs karşılaştırması
Aşağıdaki tabloda Azure Lab Services tarafından desteklenen iki laboratuvar türü karşılaştırılmaktadır: 

| Özellikler | Yönetilen laboratuvar türleri | DevTest Labs |
| -------- | ----------------- | ---------- |
| Laboratuvarda Azure altyapısı yönetimi. |  Hizmet tarafından otomatik olarak yönetilir | Kendi başınıza yönetirsiniz  |
| Altyapı sorunlarında yerleşik esneklik | Hizmet tarafından otomatik olarak gerçekleştirilir | Kendi başınıza yönetirsiniz  |
| Abonelik yönetimi | Hizmet, hizmeti destekleyen Microsoft abonelikleri içinde kaynak ayırmayı gerçekleştirir. Ölçeklendirme hizmet tarafından otomatik olarak gerçekleştirilir. | Kendi Azure aboneliğinizde kendi başınıza yönetirsiniz. Aboneliklerin otomatik ölçeklendirilmesi yok. |
| Laboratuvar içinde Azure Resource Manager dağıtımı | Kullanılamıyor | Kullanılabilir |

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelere bakın: 

- [Sınıf laboratuvarları hakkında](./classroom-labs/classroom-labs-overview.md)
- [DevTest Labs hakkında](devtest-lab-overview.md)
