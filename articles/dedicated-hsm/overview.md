---
title: Ayrılmış HSM nedir? -Azure ayrılmış HSM | Microsoft Docs
description: Azure adanmış HSM 'nin Azure 'da şifreleme anahtar depolaması sağlayan bir Azure hizmeti olduğunu öğrenin.
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 418c8f0844bf2336ce0d4a681071f237d81877ca
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505726"
---
# <a name="what-is-azure-dedicated-hsm"></a>Azure Ayrılmış HSM nedir?

Azure adanmış HSM, Azure 'da şifreleme anahtar depolaması sağlayan bir Azure hizmetidir. Adanmış HSM en katı güvenlik gereksinimlerini karşılar. FIPS 140-2 düzey 3 tarafından doğrulanan cihazlar ve HSM gerecinin tam ve dışlamalı denetimini gerektiren müşteriler için ideal bir çözümdür. 

 HSM cihazları, çeşitli Azure bölgelerinde küresel olarak dağıtılır. Bunlar, cihaz çifti olarak kolayca sağlanabilir ve yüksek kullanılabilirlik için yapılandırılabilir. Ayrıca, bölgesel düzeyde yük devretmeye karşı güvence altına almak için bölgeler arasında HSM cihazları sağlanabilir. Microsoft, [Thales Luna 7 HSM model A790 gereçlerini](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) kullanarak adanmış HSM hizmetini sunar. Bu cihaz en yüksek performans ve şifreleme tümleştirme seçeneklerini sunar. 

Bunlar sağlandıktan sonra, HSM cihazları doğrudan bir müşterinin sanal ağına bağlanır. Ayrıca, Noktadan siteye veya siteden siteye VPN bağlantısı yapılandırdığınızda şirket içi uygulama ve yönetim araçları tarafından da erişilebilir. Müşteriler, [Thales müşteri destek PORTALıNDAN](https://supportportal.thalesgroup.com/csm)HSM cihazlarını yapılandırmak ve yönetmek için yazılım ve belgeleri alırlar.

## <a name="why-use-azure-dedicated-hsm"></a>Azure ayrılmış HSM neden kullanılmalıdır?

### <a name="fips-140-2-level-3-compliance"></a>FIPS 140-2 düzey-3 uyumluluğu

Birçok kuruluşta, şifreleme anahtarlarının [fıps 140-2 düzey 3 tarafından](https://csrc.nist.gov/publications/detail/fips/140/2/final) doğrulanan HSM 'lerde depolanması gerektiğini belirten katı sektör düzenlemeleri vardır. Azure ayrılmış HSM ve yeni bir tek kiracılı teklif, [Azure Key Vault YÖNETILEN HSM](https://docs.microsoft.com/azure/key-vault/managed-hsm), finansal hizmetler sektör, kamu kurumları ve diğerleri FIPS 140-2 düzey 3 gereksinimlerini karşılar. Microsoft 'un çok kiracılı [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) hizmeti şu anda FIPS 140-2 düzey 2 tarafından doğrulanan HSM 'leri kullanmaktadır. 

### <a name="single-tenant-devices"></a>Tek kiracılı cihazlar

Birçok müşterinizin, şifreleme depolama cihazının tek kiracınıza yönelik bir gereksinimi vardır. Azure ayrılmış HSM hizmeti, Microsoft 'un genel olarak dağıtılmış veri merkezlerinden birinden fiziksel bir cihaz sağlamasını sağlar. Müşteriye sağlandıktan sonra, yalnızca söz konusu müşteri cihaza erişebilir.

### <a name="full-administrative-control"></a>Tam yönetim denetimi

Birçok müşteri, yönetim amacıyla cihazlarına tam yönetim denetimi ve tek tek erişim gerektirir. Bir cihaz sağlandıktan sonra, yalnızca müşterinin cihaza yönetici veya uygulama düzeyinde erişimi vardır.

 Müşterinin cihaza ilk kez eriştiği bir yönetim denetimi yoktur, bu noktada müşterinin parolayı değiştirdiği anlamına gelir. Bu noktadan itibaren müşteri, tam yönetim denetimi ve uygulama yönetimi özelliği olan gerçek bir tek kiracıya sahiptir. Microsoft, seri bağlantı noktası bağlantısı aracılığıyla telemetri için izleyici düzeyinde erişimi (yönetici rolü değil) korur. Bu erişim sıcaklık, güç kaynağı ve fan sistem durumu gibi donanım izleyicilerini içerir. 
 
 Müşteri, gerekli olan bu izlemeyi devre dışı bırakmak için ücretsizdir. Ancak devre dışı bırakırsanız, Microsoft 'tan öngörülü sistem durumu uyarıları almaz.

### <a name="high-performance"></a>Yüksek performans

Bu hizmet için Thales cihazı çeşitli nedenlerle seçildi. Geniş kapsamlı bir şifreleme algoritması desteği, desteklenen çeşitli işletim sistemleri ve geniş API desteği sunar. Dağıtılan özel model, RSA-2048 için saniyede 10.000 işlem ile mükemmel performans sunar. Benzersiz uygulama örnekleri için kullanılabilen 10 bölümü destekler. Bu cihaz düşük gecikme süreli, yüksek kapasiteli ve yüksek performanslı bir cihazdır.

### <a name="unique-cloud-based-offering"></a>Bulut tabanlı benzersiz teklif

Microsoft, benzersiz bir müşteri kümesine yönelik belirli bir ihtiyacı fark. Bu, FIPS 140-2 düzey 3 tarafından doğrulanan özel bir HSM hizmeti sunan ve bulut tabanlı ve şirket içi uygulama tümleştirmesinin bir kapsamını sunan, yeni müşteriler sunan tek bulut sağlayıcıdır.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>Azure ayrılmış HSM sizin için uygun mu?

Azure ayrılmış HSM, belirli bir büyük ölçekli kuruluş türü için benzersiz gereksinimlere yönelik özel bir hizmettir. Sonuç olarak, Azure müşterilerinin toplu 'un bu hizmet için kullanım profiline uymayacak olması beklenmektedir. Birçok, daha uygun ve uygun maliyetli bir Azure Key Vault hizmeti bulur. Gereksinimlerinize uygun olup olmadığına karar vermenize yardımcı olması için aşağıdaki ölçütleri belirledik.

### <a name="best-fit"></a>En iyi sığdırma

Azure adanmış HSM, en çok HSM cihazlarına doğrudan ve tek erişimli erişimi gerektiren "yükseltme ve-kaydırma" senaryoları için uygundur. Örnekler arasında şunlar yer almaktadır:

- Şirket içinden Azure sanal makinelerine uygulama geçirme
- AWS bulut HSM klasik hizmetini kullanan sanal makinelere (Amazon, bu hizmeti yeni müşterilere sunmadığından), Amazon AWS EC2 'tan uygulama geçirme
- Apache/Ngnx SSL boşaltması, Oracle TDE ve Azure sanal makinelerinde ADC gibi, Sarmalanan bir sarmalanmış yazılım çalıştırma 

### <a name="not-a-fit"></a>Uygun değil

Azure ayrılmış HSM aşağıdaki tür senaryoya uygun değildir: Azure adanmış HSM ile tümleştirilen, müşteri tarafından yönetilen anahtarlarla (Azure Information Protection, Azure disk şifrelemesi, Azure Data Lake Store, Azure depolama, Azure SQL veritabanı ve Office 365 için müşteri anahtarı) şifrelemeyi destekleyen Microsoft bulut hizmetleri.

### <a name="it-depends"></a>Duruma göre değişir

Azure ayrılmış HSM 'nin sizin için çalışıp çalışmadığına göre, gereksinimlerinize ve yapamayacaksınız. FIPS 140-2 düzey 3 gereksinimi bir örnektir. Bu gereksinim, yaygın ve Azure ayrılmış HSM ve yeni bir tek kiracılı tekliftir ve [Azure Key Vault YÖNETILEN HSM](https://docs.microsoft.com/azure/key-vault/managed-hsm) , şu anda bu dosyayı toplantıya yönelik seçeneklerdir. Bu uygulanan gereksinimleri ilgili değilse, genellikle Azure Key Vault ve Azure adanmış HSM arasında bir seçimdir. Karar vermeden önce gereksinimlerinizi değerlendirin.

Seçenekleriniz için gereken durumlar şunlardır: 

- Müşterinin Azure sanal makinesinde çalışan yeni kod
- Azure sanal makinesinde TDE SQL Server
- Azure depolama istemci tarafı şifrelemesi
- SQL Server ve Azure SQL DB Always Encrypted

## <a name="next-steps"></a>Sonraki adımlar

Bu, son derece özelleştirilmiş bir hizmettir. Bu nedenle, fiyatlandırma, destek ve hizmet düzeyi sözleşmeleri dahil olmak üzere bu belge kümesindeki temel kavramları tam olarak anlamanız önerilir. 

[Thales tümleştirme kılavuzlarında](https://cpl.thalesgroup.com/partners/overview) , HSM 'lerin mevcut bir sanal ağ ortamına sağlanması kolaylaştırılmasına yardımcı olur. Dağıtım mimarinizi ayarlamayı saptamanıza yardımcı olacak nasıl yapılır kılavuzlarından de yararlanın.

* [Yüksek kullanılabilirlik](high-availability.md)
* [Fiziksel güvenlik](physical-security.md)
* [Ağ](networking.md)
* [Desteklenebilirlik](supportability.md)
* [İzleme](monitoring.md)
