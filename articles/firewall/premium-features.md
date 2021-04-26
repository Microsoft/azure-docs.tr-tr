---
title: Azure Güvenlik Duvarı Premium Önizleme özellikleri
description: Azure Güvenlik Duvarı Premium, Azure sanal ağ kaynaklarınızı koruyan, yönetilen, bulut tabanlı bir ağ güvenlik hizmetidir.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: victorh
ms.custom: references_regions
ms.openlocfilehash: fdf0113d73b0b2e82ec889caf2a82c77bb7a040c
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226710"
---
# <a name="azure-firewall-premium-preview-features"></a>Azure Güvenlik Duvarı Premium Önizleme özellikleri

:::image type="content" source="media/premium-features/icsa-cert-firewall-small.png" alt-text="ICSA sertifika logosu" border="false"::::::image type="content" source="media/premium-features/pci-logo.png" alt-text="PCI sertifika logosu" border="false":::


> [!IMPORTANT]
> Azure Güvenlik Duvarı Premium Şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Azure Güvenlik Duvarı Premium önizleme, yüksek düzeyde hassas ve düzenlenmiş ortamlar için gereken özellikleri içeren bir sonraki nesil güvenlik duvarıdır.

:::image type="content" source="media/premium-features/premium-overview.png" alt-text="Azure Güvenlik Duvarı Premium genel bakış Diyagramı":::

Azure Güvenlik Duvarı Premium önizlemesi, Azure Güvenlik Duvarı Yöneticisi 'Ni kullanarak güvenlik duvarlarını merkezi olarak yönetmek için kullanılabilen genel bir kaynak olan güvenlik duvarı Ilkesi kullanır. Bu yayın başlatıldığında, tüm yeni özellikler yalnızca güvenlik duvarı Ilkesi aracılığıyla yapılandırılabilir. Güvenlik duvarı kuralları (klasik) desteklenmeye devam eder ve mevcut standart güvenlik duvarı özelliklerini yapılandırmak için kullanılabilir.  Güvenlik Duvarı Ilkesi bağımsız olarak veya Azure Güvenlik Duvarı Yöneticisi ile yönetilebilir. Tek bir güvenlik duvarıyla ilişkili bir güvenlik duvarı ilkesinin ücreti yoktur.

> [!IMPORTANT]
> Şu anda güvenlik duvarı Premium SKU 'SU güvenli hub dağıtımları ve Zorlamalı tünel yapılandırmalarında desteklenmez. 

Azure Güvenlik Duvarı Premium önizleme aşağıdaki özellikleri içerir:

- **TLS incelemesi** -giden trafiğin şifresini çözer, verileri işler, ardından verileri şifreler ve hedefe gönderir.
- **IDPs** -ağ izinsiz giriş algılama ve önleme SISTEMI (IDPs), kötü amaçlı etkinlik için ağ etkinliklerini izlemenize, bu etkinlikle ilgili bilgileri günlüğe almanıza, raporlamayla ve isteğe bağlı olarak engellemeyi denemenize olanak tanır.
- **URL Filtresi** -Azure GÜVENLIK duvarının FQDN filtreleme özelliğini genişleterek tüm URL 'yi düşünün. Örneğin, `www.contoso.com/a/c` yerine `www.contoso.com` .
- **Web kategorileri** -Yöneticiler, kumar web siteleri, sosyal medya web siteleri ve diğerleri gibi web sitesi kategorilerine Kullanıcı erişimine izin verebilir veya erişimi reddedebilir.


## <a name="tls-inspection"></a>TLS incelemesi

Azure Güvenlik Duvarı Premium, giden ve Doğu Batı TLS bağlantılarını sonlandırır. Gelen TLS incelemesi, [Azure Application Gateway](../web-application-firewall/ag/ag-overview.md) uçtan uca şifrelemeye izin vererek desteklenir. Azure Güvenlik Duvarı, gerekli değer katan güvenlik işlevlerini yapar ve özgün hedefe gönderilen trafiği yeniden şifreler.

> [!TIP]
> TLS 1,0 ve 1,1 kullanım dışıdır ve desteklenmez. TLS 1,0 ve 1,1 TLS/Güvenli Yuva Katmanı (SSL) sürümlerinin güvenlik açığı olduğu belirlenmiştir ve yine de geriye dönük uyumlulukla çalışmaya devam ederken, bunlar önerilmez. TLS 1,2 'e en kısa sürede geçiş yapın.

Azure Güvenlik Duvarı Premium önizleme ara CA sertifika gereksinimleri hakkında daha fazla bilgi edinmek için bkz. [Azure Güvenlik Duvarı Premium önizleme sertifikaları](premium-certificates.md).

## <a name="idps"></a>IDP 'YI DESTEKLEMESI

Ağ üzerinden izinsiz giriş algılama ve önleme sistemi (ıDPS), ağınızı kötü amaçlı etkinlikler için izlemenize, bu etkinlikle ilgili bilgileri günlüğe almanıza, rapor etmeye ve isteğe bağlı olarak engellemeyi denemenize olanak tanır. 

Azure Güvenlik Duvarı Premium önizleme, ağ trafiğinden bayt dizileri veya kötü amaçlı yazılım tarafından kullanılan bilinen kötü amaçlı yönerge dizileri gibi belirli desenleri arayarak saldırıları hızlı bir şekilde algılamaya izin veren imza tabanlı IDPs 'leri sağlar. IDPS imzaları tamamen yönetilir ve sürekli olarak güncelleştirilir.

Azure Güvenlik Duvarı imzaları/RuleSets şunları içerir:
- Bir parmak izi, gerçek kötü amaçlı yazılım, komut ve denetim, yararlanma setlerine ve geleneksel önleme yöntemleri tarafından kaçırılmış kötü amaçlı etkinlik.
- 50 ' den fazla kategoride 35.000 kural üzerinde.
    - Kategoriler arasında kötü amaçlı yazılım komutu ve denetimi, DoS saldırıları, botlar, bilgilendirici olaylar, güvenlik açıkları, güvenlik açıkları, SCADA ağ protokolleri, Exploit Kit etkinliği ve daha fazlası bulunur.
- Her gün 20 ila 40 + yeni kural serbest bırakılır.
- Son derece kötü amaçlı yazılım korumalı alanı ve küresel algılayıcı ağ geri bildirim döngüsü kullanılarak düşük yanlış pozitif derecelendirme.

IDPS, şifrelenmemiş trafik için tüm bağlantı noktalarında ve protokollerde saldırıları algılamanıza olanak tanır. Ancak, HTTPS trafiğinin İncelenme gerektiğinde, Azure Güvenlik Duvarı trafiğin şifresini çözmek ve kötü amaçlı etkinlikleri daha iyi algılamak için TLS İnceleme özelliğini kullanabilir.  

IDPS atlama listesi atlama listesinde belirtilen IP adreslerinden, aralıklardan ve alt ağlardan herhangi birine gelen trafiği filtrelemenize izin verir. 

## <a name="url-filtering"></a>URL filtreleme

URL filtrelemesi, tüm URL 'YI kabul etmek için Azure Güvenlik duvarının FQDN filtreleme özelliğini genişletir. Örneğin, `www.contoso.com/a/c` yerine `www.contoso.com` .  

URL filtrelemesi, hem HTTP hem de HTTPS trafiği üzerinde uygulanabilir. HTTPS trafiği incelemesinde Azure Güvenlik Duvarı Premium önizlemesi, trafiğin şifresini çözmek için TLS İnceleme özelliğini kullanabilir ve erişim izni verilip verilmediğini doğrulamak için hedef URL 'yi ayıklayabilir. TLS incelemesi, uygulama kuralı düzeyinde katılım gerektirir. Etkinleştirildikten sonra, HTTPS ile filtrelemek için URL 'Leri kullanabilirsiniz. 

## <a name="web-categories"></a>Web kategorileri

Web kategorileri, yöneticilerin kumar web siteleri, sosyal medya web siteleri ve diğerleri gibi web sitesi kategorilerine Kullanıcı erişimine izin verebilir veya erişimi reddetmesini sağlar. Web kategorileri de Azure Güvenlik Duvarı standardına dahil edilecek, ancak Azure Güvenlik Duvarı Premium önizlemesinde daha ince ayarlanmış olacak. Bir FQDN tabanlı kategori ile eşleşen standart SKU 'daki Web kategorileri yeteneğinin aksine, Premium SKU, hem HTTP hem de HTTPS trafiği için tüm URL 'ye göre kategori ile eşleşir. 

Örneğin, Azure Güvenlik Duvarı için bir HTTPS isteği alıyorsa `www.google.com/news` , aşağıdaki kategorilere ayırma beklenmektedir: 

- Güvenlik Duvarı standardı – yalnızca FQDN bölümü incelenir, `www.google.com` Bu nedenle *arama altyapısı* olarak kategorilere ayrılır. 

- Güvenlik Duvarı Premium – URL 'nin tamamı incelenir, bu nedenle `www.google.com/news` *haber* olarak kategorilere ayrılır.

Kategoriler, **yükümlülük**, **yüksek bant genişliği**, **iş kullanımı**, **verimlilik kaybı**, **Genel gezinme** ve **kategorilere ayrılmamış** önem derecesine göre düzenlenmiştir.

### <a name="category-exceptions"></a>Kategori özel durumları

Web kategorisi kurallarınız için özel durumlar oluşturabilirsiniz. Kural koleksiyonu grubu dahilinde daha yüksek önceliğe sahip bir izin verme veya reddetme kuralı koleksiyonu oluşturun. Örneğin, öncelik `www.linkedin.com` 200 Ile **sosyal ağı** engelleyen bir kural koleksiyonu ile öncelik 100 ile izin veren bir kural koleksiyonu yapılandırabilirsiniz. Bu, önceden tanımlanmış **sosyal ağ** Web kategorisi için özel durum oluşturur.

### <a name="categorization-change"></a>Kategori değişikliği

Şunları yaparsanız bir kategori değişikliği isteyebilirsiniz:

 - bir FQDN veya URL 'nin farklı bir kategori altında olması gerektiğini düşünün 
 
veya 

- kategorilere ayrılmamış bir FQDN veya URL için önerilen kategorili

' De bir istek göndermek için hoş geldiniz [https://aka.ms/azfw-webcategories-request](https://aka.ms/azfw-webcategories-request) .
 
## <a name="supported-regions"></a>Desteklenen bölgeler

Azure Güvenlik Duvarı Premium önizlemesi aşağıdaki bölgelerde desteklenir:

- Avustralya Orta (kamu/Avustralya)
- Avustralya Orta 2 (kamu/Avustralya)
- Avustralya Doğu (kamu/Avustralya)
- Avustralya Güneydoğu (kamu/Avustralya)
- Brezilya Güney (Kamu/Brezilya)
- Kanada Orta (kamu/Kanada)
- Kanada Doğu (kamu/Kanada)
- Orta ABD (genel/Birleşik Devletler)
- Orta ABD EUAP (kamu/Canary (US))
- Doğu Asya (genel/Asya Pasifik)
- Doğu ABD (genel/Birleşik Devletler)
- Doğu ABD 2 (genel/Birleşik Devletler)
- Fransa Orta (kamu/Fransa)
- Fransa Güney (kamu/Fransa)
- Japonya Doğu (kamu/Japonya)
- Japonya Batı (kamu/Japonya)
- Kore Orta (Genel/Kore)
- Kore Güney (Genel/Kore)
- Orta Kuzey ABD (genel/Birleşik Devletler)
- Kuzey Avrupa (kamu/Avrupa)
- Güney Afrika Kuzey (kamu/Güney Afrika)
- Orta Güney ABD (genel/Birleşik Devletler)
- Güneydoğu Asya (genel/Asya Pasifik)
- BAE Orta (genel/UAE)
- UK Güney (kamu/Birleşik Krallık)
- UK Batı (kamu/Birleşik Krallık)
- Orta Batı ABD (genel/Birleşik Devletler)
- Batı Avrupa (kamu/Avrupa)
- Batı Hindistan (genel/Hindistan)
- Batı ABD (genel/Birleşik Devletler)
- Batı ABD 2 (genel/Birleşik Devletler)


## <a name="known-issues"></a>Bilinen sorunlar

Azure Güvenlik Duvarı Premium önizleme aşağıdaki bilinen sorunlara sahiptir:

|Sorun  |Description  |Risk azaltma  |
|---------|---------|---------|
|TLS Incelemesi yalnızca HTTPS standart bağlantı noktasında destekleniyor|TLS Incelemesi yalnızca HTTPS/443 ' ü destekler. |Yok. Diğer bağlantı noktaları GA 'de desteklenecektir.|
|HTTPS 'de FQDN çözümlemesi için ESNı desteği|Şifrelenmiş SNı, HTTPS el sıkışması içinde desteklenmez.|Yalnızca bugün Firefox, özel yapılandırma ile ESNı destekler. Önerilen geçici çözüm bu özelliği devre dışı bırakdır.|
|İstemci sertifikaları (TLS)|İstemci sertifikaları, istemci ve sunucu arasında karşılıklı kimlik güveni oluşturmak için kullanılır. İstemci sertifikaları, bir TLS anlaşması sırasında kullanılır. Azure Güvenlik Duvarı, sunucuyla bir bağlantıyı yeniden sağlar ve istemci sertifikalarının özel anahtarına erişemez.|Yok|
|QUIC/HTTP3|QUIC, HTTP 'nin yeni ana sürümüdür. Bu, 80 (PLAN) ve 443 (SSL) üzerinden UDP tabanlı bir protokoldür. FQDN/URL/TLS incelemesi desteklenmez.|UDP 80/443 ' i ağ kuralları olarak geçirmeyi yapılandırın.|
Güvenilmeyen müşteri tarafından imzalanan sertifikalar|Bir intranet tabanlı Web sunucusundan alındıktan sonra, müşteri tarafından imzalanan sertifikalara güvenlik duvarı tarafından güvenilmiyor.|GA için zamanlanan düzelme.
|IDPS 'de TLS incelemesi ile ilgili uyarılarda yanlış kaynak ve hedef IP adresleri.|TLS incelemesini ve ıDPS 'yi etkinleştirdiğinizde yeni bir uyarı olduğunda, görünen kaynak/hedef IP adresi yanlış olur (özgün IP adresi yerine iç IP adresi görüntülenir).|GA için zamanlanan düzelme.|
|HTTP için ıDPS ile ilgili uyarılarda yanlış kaynak IP adresi (TLS denetimi olmadan).|Düz metin HTTP trafiği kullanımda olduğunda ve ıDPS yeni bir uyarı yayınlar ve hedef bir IP adresi ise, görünen kaynak IP adresi yanlış olur (özgün IP adresi yerine iç IP adresi görüntülenir).|GA için zamanlanan düzelme.|
|Sertifika yayma|Güvenlik duvarında bir CA sertifikası uygulandıktan sonra, sertifikanın etkili olması 5-10 dakika sürebilir.|GA için zamanlanan düzelme.|
|IDPS atlama|IDPS atlama, TLS ile sonlandırılan trafik için çalışmaz ve kaynak IP adresi ve kaynak IP grupları desteklenmez.|GA için zamanlanan düzelme.|
|TLS 1,3 desteği|TLS 1,3 kısmen desteklenir. İstemciden güvenlik duvarına olan TLS tüneli, TLS 1,2 ' i temel alır ve güvenlik duvarındaki dış Web sunucusuna TLS 1,3 ' i temel alır.|Güncelleştirmeler araştırılmaktadır.|
|Anahtar Kasası özel uç noktası|Anahtar Kasası, ağ pozlamasını sınırlamak için özel uç nokta erişimini destekler. Güvenilen Azure Hizmetleri, bir özel durum [Keykasası belgelerinde](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)açıklandığı şekilde yapılandırıldıysa bu sınırlamayı atlayabilir. Azure Güvenlik Duvarı şu anda güvenilir bir hizmet olarak listelenmemiştir ve Key Vault erişemez.|GA için zamanlanan düzelme.|


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Güvenlik Duvarı Premium sertifikaları hakkında bilgi edinin](premium-certificates.md)
- [Azure Güvenlik Duvarı Premium önizlemesini dağıtma ve yapılandırma](premium-deploy.md)
- [Azure Güvenlik Duvarı Premium önizlemesine geçiş](premium-migrate.md)
