---
title: Sık sorulan sorular-Azure ayrılmış HSM | Microsoft Docs
description: Azure ayrılmış HSM hakkındaki farklı konuları kapsayan sık sorulan sorular
services: dedicated-hsm
author: johncdawson
manager: rkarlin
tags: azure-resource-manager
ms.custom: mvc
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 5/8/2019
ms.author: mbaldwin
ms.openlocfilehash: 338977c236ebb8f1b800eb6c28747a010e708300
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244234"
---
# <a name="frequently-asked-questions-faq"></a>Sık sorulan sorular (SSS)

Microsoft Azure adanmış HSM hakkında sık sorulan soruların yanıtlarını bulun.

## <a name="the-basics"></a>Temel bilgiler

### <a name="q-what-is-a-hardware-security-module-hsm"></a>S: donanım güvenlik modülü (HSM) nedir?

Donanım güvenlik modülü (HSM), şifreleme anahtarlarını korumak ve yönetmek için kullanılan bir fiziksel bilgi işlem aygıtıdır. HSM 'lerde depolanan anahtarlar, şifreleme işlemleri için kullanılabilir. Önemli malzeme, yetkisiz ve yetkisiz ve onaysız donanım modüllerine karşı güvenli bir şekilde kalır. HSM yalnızca kimliği doğrulanmış ve yetkilendirilmiş uygulamaların anahtarları kullanmasına izin verir. Anahtar malzemeleri asla HSM koruma sınırlarından ayrılmaz.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>S: Azure adanmış HSM teklifi nedir?

Azure adanmış HSM, bir müşterinin sanal ağına doğrudan bağlı olan Azure veri merkezlerinde barındırılan HSM 'leri sağlayan bulut tabanlı bir hizmettir. Bu HSM 'ler, özel ağ gereçlerine (Gemalto 'ın SafeNet Network HSM 7 model A790) sahiptir. Bunlar doğrudan müşterilerin özel IP adresi alanına dağıtılır ve Microsoft, HSM 'lerin şifreleme işlevlerine erişemez. Bu cihazlar üzerinde yalnızca müşterinin tam yönetim ve şifreleme denetimi vardır. Müşteriler, cihazın yönetiminden sorumludur ve doğrudan cihazlarından tam etkinlik günlükleri alabilirler. Adanmış HSMs müşterileri, FIPS 140-2 düzey 3, HIPAA, PCI-DSS, ve eIDAS ve diğer birçok farklı uyumluluk/yasal gereksinimleri karşılar.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>S: adanmış HSM için hangi donanım kullanılıyor?

Microsoft, Azure ayrılmış HSM hizmetini sunmak için Gemalto ile işbirliği yaptı. Kullanılan belirli cihaz [SafeNet Luna ağ HSM 7 model A790 '](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/)dir. Bu cihaz yalnızca FIPS 140-2 düzey 3 tarafından doğrulanan bellenim sağlamaz, ancak 10 bölüm aracılığıyla düşük gecikme süresi, yüksek performans ve yüksek kapasite sağlar. 

### <a name="q-what-is-an-hsm-used-for"></a>S: için kullanılan HSM nedir?

HSM’ler SSL (güvenli yuva katmanı) gibi şifreleme işlevleri, verileri şifreleme, PKI (ortak anahtar altyapısı), DRM (dijital hak yönetimi) ve belgeleri imzalama amacıyla kullanılır.

### <a name="q-how-does-dedicated-hsm-work"></a>S: adanmış HSM nasıl çalışır?

Müşteriler, PowerShell veya komut satırı arabirimini kullanarak belirli bölgelerde HSM 'ler sağlayabilir. Müşteri, HSMs 'nin hangi sanal ağa bağlanacağı ve sağlanan IP adresleri için müşterinin özel IP adresi alanındaki atanmış alt ağda kullanılabilir olacağını belirtir. Ardından müşteriler, Gereç yönetimi ve yönetimi için SSH kullanarak HSM 'lere bağlanabilir, HSM istemci bağlantıları ayarlayabilir, HSM 'ler başlatabilir, bölümler oluşturabilir, Bölüm Müdürü, şifre müdürü ve şifre Kullanıcı gibi roller atayabilir. Daha sonra müşteri, uygulamalarından şifreleme işlemleri gerçekleştirmek için HSM istemci araçları/SDK/yazılımı sağlamak üzere Gemalto 'ı kullanır.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>S: adanmış HSM hizmeti ile hangi yazılım sağlanır?

Microsoft tarafından sağlandıktan sonra HSM cihazının tüm yazılımlarını temin etmek için Gemalto. Yazılım, [Gemalto müşteri destek portalında](https://supportportal.gemalto.com/csm/)kullanılabilir. Adanmış HSM hizmetini kullanan müşterilerin, Gemalto desteği için kaydedilmesi gerekir ve ilgili yazılımın erişimine ve indirilmesini sağlayan bir müşteri KIMLIĞI vardır. Desteklenen istemci yazılımı, FIPS 140-2 düzey 3 ile doğrulanan bellenim sürümü 7.0.3 ile uyumlu olan sürüm 7,2 ' dir. 

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>S: Azure adanmış HSM, parola tabanlı ve uygulanmış tabanlı kimlik doğrulaması sunuyor mu?

Azure ayrılmış HSM, şu anda yalnızca parola tabanlı kimlik doğrulama ile HSM 'ler sağlar.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>S: Azure adanmış HSM ana bilgisayar, HSMs benim için mi?

Microsoft, özel HSM hizmeti aracılığıyla yalnızca Gemalto SafeNet Luna ağ HSM 'sini sunar ve müşterinin sağladığı cihazları barındıramaz.

### <a name="q-does-azure-dedicated-hsm-support-payment-pineft-features"></a>S: Azure adanmış HSM destek ödemesi (PIN/EFT) özellikleri mi?

Azure ayrılmış HSM hizmeti, SafeNet Luna ağ HSM 7 (model A790) cihazlarını kullanır. Bu cihazlar, ödeme HSM 'ye özgü işlevselliği (PIN veya EFT gibi) veya sertifikaları desteklemez. Azure ayrılmış HSM hizmeti 'nin gelecekte ödeme HSM 'leri desteklemesini istiyorsanız lütfen Microsoft hesap temsilcinizle ilgili geri bildirimleri geçirin.

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>S: hangi Azure bölgeleri için adanmış HSM kullanılabilir?

En geç 2019 itibarıyla, adanmış HSM aşağıda listelenen 14 bölgede kullanılabilir. Daha fazla bölge planlanmaktadır ve Microsoft hesap temsilciniz aracılığıyla tartışılmış olabilir.

* Doğu ABD
* Doğu ABD 2
* Batı ABD
* Güney Orta ABD
* Güneydoğu Asya
* Doğu Asya
* Kuzey Avrupa
* Batı Avrupa
* Birleşik Krallık, Güney
* Birleşik Krallık, Batı
* Kanada Orta
* Kanada Doğu
* Doğu Avustralya
* Güneydoğu Avustralya

## <a name="interoperability"></a>Birlikte çalışabilirlik

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>S: Uygulamam adanmış bir HSM 'ye nasıl bağlanır?

Uygulamalarınızdan şifreleme işlemleri gerçekleştirmek için HSM istemci araçları/SDK/yazılım sağlayan Gemalto 'ı kullanırsınız. Yazılım, [Gemalto müşteri destek portalında](https://supportportal.gemalto.com/csm/)kullanılabilir. Adanmış HSM hizmetini kullanan müşterilerin, Gemalto desteği için kaydedilmesi gerekir ve ilgili yazılımın erişimine ve indirilmesini sağlayan bir müşteri KIMLIĞI vardır.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>S: bir uygulama, bölgelere veya bölgeler arasında farklı bir VNET 'ten adanmış HSM 'ye bağlanabilir mi?

Evet, sanal ağlar arasında bağlantı kurmak için bir bölge içinde [VNET eşlemesi](../virtual-network/virtual-network-peering-overview.md) kullanmanız gerekir. Bölgeler arası bağlantı için [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)kullanmanız gerekir.

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>S: adanmış HSM 'yi şirket içi HSM 'ler ile eşitlenebilir miyim?

Evet, şirket içi HSM 'leri adanmış HSM ile eşitleyebilirsiniz. [Noktadan noktaya VPN veya Noktadan siteye](../vpn-gateway/vpn-gateway-about-vpngateways.md) bağlantı, şirket içi ağınızla bağlantı kurmak için kullanılabilir.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>S: özel HSM 'de depolanan anahtarları kullanarak diğer Azure hizmetleri tarafından kullanılan verileri şifreleyebilirim?

Hayır. Azure adanmış HSM 'ler yalnızca sanal ağınızın içinden erişilebilir.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>S: mevcut bir şirket içi HSM 'den adanmış HSM 'ye anahtar aktarabilir miyim?

Evet, şirket içi Gemalsiz SafeNet HSMs varsa. Birden çok yöntem vardır. Gemalto HSM belgelerine bakın.

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>S: adanmış HSM istemci yazılımı tarafından hangi işletim sistemleri destekleniyor?

* Windows, Linux, Solaris, AıX, HP-UX, FreeBSD
* Sanal: VMware, hyperv, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>S: birden çok HSM 'den birden çok bölümden oluşan yüksek kullanılabilirliğe sahip bir yapılandırma oluşturmak için istemci uygulamamı yapılandırmak Nasıl yaparım??

Yüksek kullanılabilirlik sağlamak için, HSM istemci uygulaması yapılandırmanızı her bir HSM 'den bölümler kullanacak şekilde ayarlamanız gerekir. Gemalto HSM istemci yazılımı belgelerine bakın.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>S: adanmış HSM tarafından hangi kimlik doğrulama mekanizmaları desteklenir?

Azure adanmış HSM, SafeNet Network HSM 7 gereçlerini (model A790) kullanır ve parola tabanlı kimlik doğrulamasını destekler.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>S: adanmış HSM ile kullanılabilecek SDK 'Lar, API 'Ler, istemci yazılımı nedir?

PKCS # 11, Java (JCA/JCE), Microsoft CAPı ve CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>S: Luna 5/6 HSM 'lerden Azure adanmış HSM 'lere anahtar aktarabilir/geçiş yapabilir miyim?

Evet. Lütfen Gemalto geçiş kılavuzuna bakın. 

## <a name="using-your-hsm"></a>HSM 'nizi kullanma

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>S: Azure Key Vault mi yoksa Azure ayrılmış HSM mi kullanacağınızı Nasıl yaparım? karar veriyor musunuz?

Azure adanmış HSM, HSM 'leri kullanan Azure şirket içi uygulamalarına geçiş yapmak için uygun bir seçimdir. Adanmış HSM 'ler, bir uygulamayı en az değişiklikle geçirmeye yönelik bir seçenek sunar. Şifreleme işlemleri, uygulamanın bir Azure VM veya Web uygulamasında çalışan kodunda gerçekleştirilirse, adanmış HSM kullanabilirler. Genel olarak, IaaS (hizmet olarak altyapı) modelleriyle çalışan daraltma sarmalanmış yazılımlar, anahtar deposu olarak HSM 'leri destekleyen, anahtarsız SSL, ADCS (Active Directory Sertifika Hizmetleri) için Application Gateway veya Traffic Manager gibi bir HSM 'yi kullanabilir veya aynı PKI araçları, Belge imzalama, kod imzalama veya SQL Server (IaaS) için kullanılan araçlar/uygulamalar, EKM (Genişletilebilir anahtar yönetimi) sağlayıcısı kullanan bir HSM 'de ana anahtarla birlikte TDE (saydam veritabanı şifrelemesi) ile yapılandırılır. Azure Key Vault, "bulut kaynaklı" uygulamalar için veya müşteri verilerinin Office 365 müşteri anahtarı gibi PaaS (hizmet olarak platform) veya SaaS (hizmet olarak yazılım) senaryolarında işlendiği Rest senaryolarında şifreleme için uygundur Azure Information Protection Azure disk şifrelemesi, müşteri tarafından yönetilen anahtarla Azure Data Lake Store şifreleme, müşteri tarafından yönetilen anahtarla Azure depolama şifrelemesi ve müşteri tarafından yönetilen anahtarla Azure SQL.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>S: hangi kullanım senaryolarını en iyi Azure ayrılmış HSM 'ye göre.

Azure ayrılmış HSM, en çok geçiş senaryoları için uygundur. Bu, şirket içi uygulamaları zaten HSMs kullanan Azure 'a geçiriyorsanız olduğu anlamına gelir. Bu, uygulama üzerinde en az değişiklikle Azure 'a geçiş yapmak için düşük duyarlı bir seçenek sağlar. Şifreleme işlemleri, uygulamanın Azure VM veya Web App 'te çalışan kodunda gerçekleştirilirse, adanmış HSM kullanılabilir. Genel olarak, IaaS (hizmet olarak altyapı) modelleriyle çalışan daraltma sarmalanmış yazılımlar, bir anahtar deposu olarak HSM 'leri destekleyen HSM 'yi kullanabilir, örneğin:

* Anahtarsız SSL için Application Gateway veya Traffic Manager
* ADCS (Active Directory Sertifika Hizmetleri)
* Benzer PKI araçları
* Belge imzalama için kullanılan araçlar/uygulamalar
* Kod imzalama
* EKM (Genişletilebilir anahtar yönetimi) sağlayıcısı kullanılarak bir HSM 'de ana anahtarla TDE (saydam veritabanı şifrelemesi) ile yapılandırılan SQL Server (IaaS)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>S: Office 365 müşteri anahtarı, Azure Information Protection, Azure Data Lake Store, disk şifrelemesi, Azure depolama şifrelemesi, Azure SQL TDE ile adanmış HSM kullanılabilir mi?

Hayır. Ayrılmış HSM, diğer Azure veya Microsoft Hizmetleri tarafından erişilememesi için doğrudan müşterinin özel IP adresi alanına sağlanır.

## <a name="administration-access-and-control"></a>Yönetim, erişim ve denetim

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>S: müşteri, adanmış HSM 'ler ile HSM 'ler üzerinde tam dışlamalı denetim mi edindi?

Evet. Her HSM gereci tek bir müşteriye tam olarak ayrılmıştır ve sağlandıktan ve yönetici parolası değiştirildikten sonra hiç kimse yönetim denetimine sahip olmaz.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>S: Microsoft 'un HSM 'me hangi erişim düzeyi vardır?

Microsoft, HSM üzerinde herhangi bir yönetim veya şifreleme denetimine sahip değildir. Microsoft 'un, sıcaklık ve bileşen durumu gibi temel telemetri almak için seri bağlantı noktası bağlantısı aracılığıyla izleyici düzeyinde erişimi vardır. Bu, Microsoft 'un sistem durumu sorunlarıyla ilgili öngörülü bildirim sağlamasına olanak tanır. Gerekirse, müşteri bu hesabı devre dışı bırakabilir.

### <a name="q-what-is-the-tenantadmin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-safenet-hsms"></a>S: Microsoft 'un kullandığı "TenantAdmin" hesabı nedir, bu yönetici kullanıcının SafeNet HSMs üzerinde "Yönetici" olması için kullandım mı?

HSM cihazı, normal varsayılan parolasıyla birlikte varsayılan bir yönetici kullanıcıyla birlikte gönderilir. Microsoft, tüm cihazlar müşteriler tarafından sağlanmayı bekleyen bir havuzda olduğu sürece varsayılan parolaların kullanımda olmasını istemedi. Bu, katı güvenlik gereksinimlerimizi karşılamıyor. Bu nedenle, sağlama zamanında atılan güçlü bir parola ayarlayacağız. Ayrıca, sağlama sırasında "TenantAdmin" adlı yönetici rolünde yeni bir Kullanıcı oluşturacağız. Bu Kullanıcı varsayılan parolaya sahiptir ve müşteriler, ilk kez yeni sağlanan cihaza oturum açarken bunu ilk eylem olarak değiştirir. Bu işlem, yüksek derecede güvenlik sağlar ve müşterilerimiz için tek yönetici denetimi taahhüdünü korur. Bir müşteri bu hesabı kullanmayı tercih ediyorsa Yönetici Kullanıcı parolasını sıfırlamak için "TenantAdmin" kullanıcısının kullanılabileceğini not edilmelidir. 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>S: Microsoft 'a veya Microsoft Access anahtarınızdaki herkes adanmış HSM 'mi olabilir mi?

Hayır. Microsoft 'un müşteri tarafından ayrılan adanmış HSM 'de depolanan anahtarlara erişimi yoktur.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>S: bana ayrılan HSM 'lerde yazılım/bellenim yükseltebilir miyim?

En iyi desteği elde etmek için Microsoft, HSM 'de yazılım/üretici yazılımı yükseltmemenizi önemle tavsiye eder. Ancak, farklı bellenim sürümlerinden belirli özellikler gerekliyse, müşterinin yazılım/bellenim yükseltmesi de dahil olmak üzere tam yönetim denetimi vardır. Değişiklikler yapılmadan önce, bu, örneğin, FIPS tarafından doğrulanan durum gibi anlaşılamalıdır. 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>S: adanmış HSM 'yi yönetmek Nasıl yaparım??

Adanmış HSM 'leri, SSH kullanarak bunlara erişerek yönetebilirsiniz.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>S: adanmış HSM 'de bölümleri yönetmek Nasıl yaparım??

Gemalto HSM istemci yazılımı, HSMs ve bölümleri yönetmek için kullanılır.

### <a name="q-how-do-i-monitor-my-hsm"></a>S: HSM 'mi Nasıl yaparım??

Müşterinin Syslog ve SNMP aracılığıyla HSM etkinlik günlüklerine tam erişimi vardır. Müşterinin, HSMs 'den günlükleri veya olayları alması için bir Syslog sunucusu veya SNMP sunucusu ayarlaması gerekir.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>S: adanmış HSM 'den tüm HSM işlemlerinde tam erişim günlüğü alabilir miyim?

Evet. HSM gerecinden bir Syslog sunucusuna Günlükler gönderebilirsiniz

## <a name="high-availability"></a>Yüksek kullanılabilirlik

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>S: aynı bölgede veya birden çok bölgede yüksek kullanılabilirlik yapılandırmak mümkün mü?

Evet. Yüksek kullanılabilirlik yapılandırması ve kurulumu, Gemalto tarafından sunulan HSM istemci yazılımında gerçekleştirilir. Aynı VNET 'ten veya aynı bölgedeki veya bölgedeki diğer VNET 'lerden ya da siteler arası veya noktadan noktaya VPN kullanarak bir VNET 'e bağlı olan şirket içi HSM 'ler aynı yüksek kullanılabilirliğe sahip yapılandırmaya eklenebilir. Bunun, yalnızca anahtar malzemesini eşitlediği ve roller gibi belirli yapılandırma öğeleri olmadığı unutulmamalıdır.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>S: şirket içi ağınızdan Azure ayrılmış HSM ile yüksek kullanılabilirlik grubuna HSM 'ler ekleyebilir miyim?

Evet. SafeNet Luna ağ HSM 7 için yüksek kullanılabilirlik gereksinimlerini karşılamalıdır.

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>S: Azure ayrılmış HSM ile şirket içi ağlardan bir yüksek kullanılabilirlik grubuna Luna 5/6 HSMs ekleyebilir miyim?

Hayır.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>S: tek bir uygulamadan aynı yüksek kullanılabilirliğe sahip yapılandırmaya kaç tane HSMs ekleyebilirim?

bir HA grubunun 16 üyesi, çok iyi sonuçlarla, tam kısıtlama içeren bir testle sahiptir.

## <a name="support"></a>Destek

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>S: adanmış HSM hizmeti için SLA nedir?

Adanmış HSM hizmeti için belirli bir çalışma süresi garantisi sağlanmamıştır. Microsoft, cihaza ağ düzeyinde erişim sağlar ve bu nedenle standart Azure ağ SLA 'Ları geçerlidir.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>S: Azure adanmış HSM 'de nasıl korunan HSMs 'ler?

Azure veri merkezlerinde kapsamlı fiziksel ve yordamsal güvenlik denetimleri vardır. Bu adanmış HSM 'lerin yanı sıra, veri merkezinin daha kısıtlı erişim alanında barındırılır. Bu alanlarda ek güvenlik için ek fiziksel erişim denetimleri ve video kamera gözetimi bulunur.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>S: güvenlik ihlali veya donanım üzerinde değişiklik olayı varsa ne olur?

Adanmış HSM hizmeti SafeNet Network HSM 7 gereçlerini kullanır. Bu gereçler fiziksel ve mantıksal yeniden algılama algılamasını destekler. Bir yetkisiz hale getirilmiş bir olay varsa, HSM 'ler otomatik olarak sıfırlaması yapılır.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>S: adanmış HSM 'mdeki anahtarların hata veya kötü amaçlı bir Insider saldırısı nedeniyle kaybolmamasını sağlamak Nasıl yaparım? emin misiniz?

Olağanüstü durum kurtarma için HSM 'lerin düzenli aralıklarla düzenli yedeklemesini gerçekleştirmek üzere şirket içi bir HSM yedekleme cihazının kullanılması önemle önerilir. Bir HSM yedekleme cihazına bağlı şirket içi bir iş istasyonuna eşler arası veya siteden siteye VPN bağlantısı kullanmanız gerekir.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>S: adanmış HSM için destek almak Nasıl yaparım??

Destek, hem Microsoft hem de Gemalto tarafından sağlanır.  Donanımla veya ağ erişimiyle ilgili bir sorununuz varsa, Microsoft ile bir destek talebi yükseltin ve HSM yapılandırması, yazılım ve uygulama geliştirme ile ilgili bir sorununuz varsa, lütfen Gemalto ile bir destek isteği yükseltin. Belirlenmeyen bir sorununuz varsa, Microsoft ile bir destek isteği oluşturup daha sonra Gemalto Için gereken bir sorun olabilir. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-safenet-luna-7-hsm"></a>S: Nasıl yaparım? istemci yazılımı, belgeleri ve SafeNet Luna 7 HSM için tümleştirme yönergelerine erişin mi?

Hizmete kaydolduktan sonra, Gemalto müşteri destek portalı 'nda kayda izin veren bir müşteri KIMLIĞI sağlanacaktır. Bu, tüm yazılım ve belgelere erişimi etkinleştirir ve doğrudan Gemalto ile destek isteklerini etkinleştirir.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>S: bir güvenlik açığı varsa ve Gemalto tarafından bir düzeltme eki yayımlanmışsa, işletim sistemi/üretici yazılımı yükseltmekten/düzeltme ekiyle kimler sorumludur?

Microsoft, müşterilere ayrılan HSMs 'ye bağlanabilir. Müşterilerin HSM 'leri yükseltmeleri ve düzeltme ekini düzeltmesi gerekir.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>S: HSM 'umu yeniden başlatmalıyım ne yapmalıyım?

HSM 'nin bir komut satırı yeniden başlatma seçeneği vardır, ancak yeniden başlatma askıda kalma sorunları zaman zaman yaşanıyor ve bu nedenle, cihazın fiziksel olarak yeniden başlatılmasını sağlamak için Microsoft ile bir destek isteği yükseltmenize en güvenli hale getirtiğimiz için önerilir. 

## <a name="cryptography-and-standards"></a>Şifreleme ve standartlar

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>S: en önemli veriler için şifreleme anahtarlarını ayrılmış HSM 'de depolamak güvenli mi?

Evet, adanmış HSM, FIPS 140-2 düzey 3 tarafından doğrulanan HSM 'leri kullanan SafeNet ağ HSM 7 gereçlerini sağlar. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>S: adanmış HSM tarafından desteklenen şifreleme anahtarları ve algoritmaları nelerdir?

Adanmış HSM hizmeti SafeNet Network HSM 7 gereçlerini sağlar. Bunlar aşağıdakiler dahil olmak üzere çok çeşitli şifreleme anahtar türlerini ve algoritmaları destekler: tam Suite B desteği

* Sertifikasında
  * RSA
  * 'DA
  * Diffie-Hellman
  * Eliptik Eğri
  * Adlandırılmış, Kullanıcı tanımlı ve Brainpool eğrileri, KCDSA ile şifreleme (ECDSA, ECDH, Ed25519, ECIES)
* Simetrik
  * AES-GCM
  * Üçlü DES
  * DES
  * ARIA, ÇEKIRDEK
  * RC2
  * RC4
  * RC5
  * CAST
  * Karma/Ileti Özeti/HMAC: SHA-1, SHA-2, SM3
  * Anahtar türetme: SP800-108 sayaç modu
  * Anahtar sarmalama: SP800-38F
  * Rastgele sayı üretimi: FIPS 140-2 onaylı DRBG (SP 800-90 Mrk), BSı DRG ile uyumlu. 4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>S: adanmış HSM FIPS 140-2 düzey 3 doğrulanmadı mi?

Evet. Adanmış HSM hizmeti, FIPS 140-2 düzey 3 tarafından doğrulanan HSM 'leri kullanan SafeNet ağ HSM 7 gereçlerini sağlar.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>S: FIPS 140-2 düzey 3 tarafından doğrulanan modda adanmış HSM 'yi işlediğimde emin olmak için ne yapmam gerekir?

Adanmış HSM hizmeti SafeNet Luna ağ HSM 7 gereçlerini sağlar. Bu gereçler FIPS 140-2 düzey 3 doğrulanan HSM 'leri kullanır. Varsayılan olarak dağıtılan yapılandırma, işletim sistemi ve üretici yazılımı da FIPS olarak onaylanır. FIPS 140-2 düzey 3 uyumluluğu için herhangi bir işlem gerçekleştirmeniz gerekmez.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>S: bir HSM, bir HSM 'nin sağlanması ne zaman sağlandıysa, tüm anahtar malzemeler silinir mi?

Sağlamayı kaldırma istenmeden önce, müşterinin HSM istemci araçları 'nı kullanan HSM 'yi sıfırlaması gerekir.

## <a name="performance-and-scale"></a>Performans ve ölçeklendirme

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>S: adanmış HSM ile saniye başına kaç tane şifreleme işlemi destekleniyor?

Adanmış HSM, SafeNet ağ HSM 7 gereçlerini (model A790) sağlar. Bazı işlemler için en yüksek performans özeti aşağıda verilmiştir: 

* RSA-2048:10.000 işlem/saniye
* ECC P256:20.000 işlem/saniye
* AES-GCM: 17.000 işlem/saniye

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>S: adanmış HSM 'de kaç bölüm oluşturulabilirler?

Kullanılan SafeNet Luna HSM 7 modeli A790, hizmet maliyetinde 10 bölüm için bir lisans içerir. Cihazda 100 bölümden oluşan bir sınır bulunur ve bu sınıra kadar bölüm eklemek ek lisanslama maliyetlerine neden olur ve cihazda yeni bir lisans dosyası yüklemesi gerektirir.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>S: adanmış HSM 'de kaç anahtar desteklenebilir?

Maksimum anahtar sayısı, kullanılabilir belleğin bir işlevidir. A790 'in kullanımdaki SafeNet Luna 7 modeli, 32 MB belleğe sahiptir. Asimetrik anahtarlar kullanılıyorsa, aşağıdaki sayılar da anahtar çiftlerine uygulanabilir.

* RSA-2048-19.000
* ECC-P256-91.000

Kapasite, anahtar oluşturma şablonunda ve bölüm sayısında ayarlanan belirli anahtar özniteliklerine bağlı olarak değişir.
