---
title: DNS Bölgeleri ve kayıtlara genel bakış-Azure DNS
description: Microsoft Azure DNS 'de DNS bölgelerini ve kayıtlarını barındırma desteğine genel bakış.
author: rohinkoul
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 04/20/2021
ms.author: rohink
ms.openlocfilehash: 26eefe5cbcef417dee1a400b492ee847394ca6a9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816972"
---
# <a name="overview-of-dns-zones-and-records"></a>DNS bölgelerine ve kayıtlarına genel bakış

Bu makalede etki alanları, DNS bölgeleri, DNS kayıtları ve kayıt kümelerinin temel kavramları açıklanmaktadır. Azure DNS nasıl desteklendiğini öğrenirsiniz.

## <a name="domain-names"></a>Etki alanı adları

Etki Alanı Adı Sistemi, bir etki alanları hiyerarşisidir. Hiyerarşi, adı yalnızca '**.**' olan ' root ' etki alanından başlar.  Bunun altında "com", "net", "org", "uk" veya "jp" gibi en üst düzey etki alanları bulunur.  Üst düzey etki alanlarının altında, ' org.uk ' veya ' co.jp ' gibi ikinci düzey etki alanları bulunur. DNS hiyerarşisindeki etki alanları, dünyanın dört bir yanındaki DNS ad sunucuları tarafından barındırılan küresel olarak dağıtılır.

Etki alanı adı kaydedici, gibi bir etki alanı adı satın almanızı sağlayan bir kuruluştur `contoso.com` .  Bir etki alanı adı satın alma, bu ad altında DNS hiyerarşisini denetleme hakkını sağlar, örneğin adı şirket web sitenize yönlendirmenize olanak tanır `www.contoso.com` . Kaydedici, etki alanını sizin adınıza kendi ad sunucularında barındırabilir veya alternatif ad sunucuları belirtmenize izin verebilir.

Azure DNS, etki alanınızı barındırmak için kullanabileceğiniz, küresel olarak dağıtılmış ve yüksek kullanılabilirliğe sahip bir ad sunucusu altyapısı sağlar. Etki alanlarınızı Azure DNS barındırarak DNS kayıtlarınızı, diğer Azure hizmetlerinizle aynı kimlik bilgileri, API, araç, faturalandırma ve destek ile yönetebilirsiniz.

Azure DNS şu anda etki alanı adlarının satın alınmasını desteklememektedir. Bir etki alanı adı satın almak istiyorsanız, üçüncü taraf etki alanı adı kaydedicisi kullanmanız gerekir. Kaydedici genellikle küçük bir yıllık ücreti ücretlendirir. Etki alanları daha sonra DNS kayıtlarının yönetimi için Azure DNS içinde barındırılabilir. Ayrıntılar için bkz. [Azure DNS'ye bir etki alanı devretme](dns-domain-delegation.md).

## <a name="dns-zones"></a>DNS bölgeleri

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>DNS kayıtları

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Yaşam süresi

Yaşam süresi veya TTL, her kaydın yeniden sorgulama yapılmadan önce istemciler tarafından ne kadar süre önbelleğe alınacağını belirtir. Yukarıdaki örnekte, TTL 3600 saniye veya 1 saattir.

Azure DNS, TTL her kayıt için değil, kayıt kümesi için belirtilir, bu nedenle bu kayıt kümesi içindeki tüm kayıtlar için aynı değer kullanılır.  1 ila 2.147.483.647 saniye arasında herhangi bir TTL değeri belirtebilirsiniz.

### <a name="wildcard-records"></a>Joker karakter kayıtları

Azure DNS [joker kayıtlarını](https://en.wikipedia.org/wiki/Wildcard_DNS_record) destekler. Joker karakter kayıtları, joker karakter olmayan bir kayıt kümesinden daha yakın bir eşleşme olmadığı takdirde eşleşen ada sahip herhangi bir sorguya yanıt olarak döndürülür. Azure DNS, NS ve SOA hariç tüm kayıt türleri için joker karakter kayıt kümelerini destekler.

Joker karakter kayıt kümesi oluşturmak için, ' ' kayıt kümesi adını kullanın \* . ' \* ', ' \* . Foo ' gibi en solundaki etiket olarak ' ' ile bir ad da kullanabilirsiniz.

### <a name="caa-records"></a>CAA kayıtları

CAA kayıtları, etki alanı sahiplerinin, etki alanı için sertifika verme yetkisine sahip olan sertifika yetkililerini (CA 'Lar) belirtmesini sağlar. Bu kayıt, CA 'Ların bazı koşullarda yanlış sertifika veren sertifikalara engel olmasını sağlar. CAA kayıtlarının üç özelliği vardır:
* **Bayraklar**: Bu alan, [RFC](https://tools.ietf.org/html/rfc6844#section-3) başına özel anlamı olan kritik bayrağını temsil etmek için kullanılan 0 ile 255 arasında bir tamsayıdır
* **Etiket**: aşağıdakilerden biri olabılecek bir ASCII dizesi:
    * **sorun**: sertifika verme Izni verilen CA 'ları belirtmek isterseniz (tüm türler)
    * **ıssuewildcard**: sertifika verme Izni verilen CA 'ları belirtmek isterseniz (yalnızca Joker sertifikaları)
    * **iodef**: CA 'ların yetkisiz sertifika verme isteklerini bildirebileceği bir e-posta adresi veya ana bilgisayar adı belirtin
* **Değer**: seçilen belirli bir etiketin değeri

### <a name="cname-records"></a>CNAME kayıtları

CNAME kayıt kümeleri aynı ada sahip diğer kayıt kümeleriyle birlikte bulunamaz. Örneğin, göreli adı ' www ' olan bir CNAME kayıt kümesi ve aynı anda göreli adı ' www ' olan bir kayıt oluşturamazsınız.

Bölge tepesinde (ad = ' \@ '), bölgenin oluşturulması sırasında her zaman NS ve SOA kayıt kümelerini içerecek olduğundan, tepesinde bölgesinde BIR CNAME kayıt kümesi oluşturamazsınız.

Bu kısıtlamalar DNS standartları kaynaklıdır, Azure DNS sınırlamaları değildir.

### <a name="ns-records"></a>NS kayıtları

Tepesinde (Name ' ') bölgesindeki NS kaydı, \@ her DNS bölgesi ile otomatik olarak oluşturulur ve bölge silindiğinde otomatik olarak silinir. Ayrı olarak silinemez.

Bu kayıt kümesi, bölgeye atanan Azure DNS ad sunucularının adlarını içerir. Birden fazla DNS sağlayıcısıyla birlikte barındırma etki alanlarını desteklemek için bu NS kayıt kümesine daha fazla ad sunucusu ekleyebilirsiniz. Bu kayıt kümesi için TTL ve meta verileri de değiştirebilirsiniz. Ancak, önceden doldurulmuş Azure DNS ad sunucularının kaldırılmasına veya değiştirilmesine izin verilmez. 

Bu kısıtlama yalnızca tepesinde bölgesindeki NS kayıt kümesi için geçerlidir. Bölgesindeki diğer NS kayıt kümeleri (alt bölgeleri temsilci atamak için kullanılan), kısıtlama olmadan oluşturulabilir, değiştirilebilir ve silinebilir.

### <a name="soa-records"></a>SOA kayıtları

SOA kayıt kümesi, her bölgenin tepesinde (ad = ' ') üzerinde otomatik olarak oluşturulur \@ ve bölge silindiğinde otomatik olarak silinir.  SOA kayıtları ayrı olarak oluşturulamaz veya silinemez.

' Host ' özelliği hariç SOA kaydının tüm özelliklerini değiştirebilirsiniz. Bu özellik, Azure DNS tarafından belirtilen birincil ad sunucu adına başvuracak şekilde önceden yapılandırılmış olarak alır.

Bölgede kayıtlar üzerinde değişiklik yapıldığında, SOA kaydındaki bölge seri numarası otomatik olarak güncellenmez. Gerekirse, SOA kaydı düzenlenerek el ile güncelleştirilebilen bir şekilde yapılandırılabilir.

### <a name="spf-records"></a>SPF kayıtları

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>SRV kayıtları

[SRV kayıtları](https://en.wikipedia.org/wiki/SRV_record) , çeşitli hizmetler tarafından sunucu konumlarını belirtmek için kullanılır. Azure DNS içinde bir SRV kaydı belirtirken:

* *Hizmet* ve *protokol* , kayıt kümesi adının bir parçası olarak belirtilmelidir, alt çizgi öneki.  Örneğin, ' \_ SIP. \_ tcp.name '.  Tepesinde bölgesindeki bir kayıt için, \@ kayıt adında ' ' belirtmeniz gerekmez, hizmet ve protokolü kullanmanız yeterlidir, örneğin ' \_ SIP. \_ TCP '.
* *Öncelik*, *Ağırlık*, *bağlantı noktası* ve *hedef* , kayıt kümesindeki her bir kaydın parametresi olarak belirtilir.

### <a name="txt-records"></a>TXT kayıtları

TXT kayıtları, etki alanı adlarını rastgele metin dizeleriyle eşlemek için kullanılır. Bunlar, [Gönderen Ilke çerçevesi (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) ve [DomainKeys tarafından tanımlanan posta (dkim)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail)gibi e-posta yapılandırmasıyla ilgili olarak birden çok uygulamada kullanılır.

DNS standartları tek bir TXT kaydına, her birinin en fazla 254 karakter uzunluğunda olabilen birden çok dize içermesine izin verir. Birden çok dize kullanıldığında, istemciler tarafından birleştirilir ve tek bir dize olarak kabul edilir.

Azure DNS REST API çağrılırken her bir TXT dizesini ayrı ayrı belirtmeniz gerekir.  Azure portal, PowerShell veya CLı arabirimlerini kullanırken, her kayıt için tek bir dize belirtmelisiniz, bu, gerekirse otomatik olarak 254 karakter segmentlerine bölünmüştür.

Bir DNS kaydındaki birden çok dize, bir TXT kayıt kümesindeki birden çok TXT kaydıyla karıştırılmamalıdır.  Bir TXT kayıt kümesi, *her biri* birden çok dize içerebilen birden çok kayıt içerebilir.  Azure DNS, her bir TXT kayıt kümesinde en fazla 1024 karakterden oluşan toplam dize uzunluğunu destekler (Birleşik tüm kayıtlar arasında).

## <a name="tags-and-metadata"></a>Etiketler ve meta veriler

### <a name="tags"></a>Etiketler

Etiketler, ad-değer çiftleri listesidir ve kaynakları etiketlemek için Azure Resource Manager tarafından kullanılır. Azure Resource Manager, Azure faturanızda Filtrelenmiş görünümleri etkinleştirmek için Etiketler kullanır ve ayrıca belirli Etiketler için bir ilke ayarlamanıza olanak sağlar. Etiketler hakkında daha fazla bilgi için bkz. [Etiketleri kullanarak Azure kaynaklarınızı düzenleme](../azure-resource-manager/management/tag-resources.md).

Azure DNS, DNS bölge kaynaklarında Azure Resource Manager etiketlerin kullanılmasını destekler.  DNS kayıt kümelerinde etiketleri desteklemez, ancak aşağıda açıklandığı gibi DNS kayıt kümelerinde alternatif bir ' Metadata ' desteklenir.

### <a name="metadata"></a>Meta veri

Kayıt kümesi etiketlerinin bir alternatifi olarak Azure DNS, ' Metadata ' kullanarak kayıt kümelerine açıklama eklemeyi destekler.  Etiketlere benzer şekilde meta veriler, ad-değer çiftlerini her bir kayıt kümesiyle ilişkilendirmenizi sağlar.  Bu özellik, örneğin her bir kayıt kümesinin amacını kaydetmek için yararlı olabilir.  Etiketlerin aksine, meta veriler Azure faturanızda filtrelenmiş bir görünüm sağlamak için kullanılamaz ve bir Azure Resource Manager ilkesinde belirtilemez.

## <a name="etags"></a>ETag 'ler

İki kişinin veya iki işlemin aynı anda bir DNS kaydını değiştirmeyi deneydiğini varsayalım. Tek bir WINS mi? Ayrıca, başka biri tarafından oluşturulan değişikliklerin üzerine yazıldıklarından emin misiniz?

Azure DNS, aynı kaynaktaki eşzamanlı değişiklikleri güvenli bir şekilde işlemek için ETags kullanır. ETags [Azure Resource Manager ' Tags '](#tags)öğesinden ayrıdır. Her DNS kaynağına (bölge veya kayıt kümesi) ilişkili bir ETag öğesi vardır. Her kaynak alındığında ETag de alınır. Bir kaynağı güncelleştirirken, Azure DNS sunucu eşleştirmelerinin eşleşmesini doğrulayabilmesi için ETag 'i geri geçirmeye tercih edebilirsiniz. Bir kaynaktaki her güncelleştirme ETag 'in yeniden üretildiğinden dolayı bir ETag uyumsuzluğu, eşzamanlı bir değişikliğin gerçekleştiğini gösterir. Kaynak zaten mevcut olmadığından emin olmak için yeni bir kaynak oluşturulurken ETags de kullanılabilir.

Varsayılan olarak, Azure DNS PowerShell, bölgelerde ve kayıt kümelerinde eşzamanlı değişiklikleri engellemek için ETags kullanır. İsteğe bağlı *-üzerine yazma* anahtarı ETag denetimlerini bastırmak için kullanılabilir, bu durumda gerçekleşen eşzamanlı değişiklikler üzerine yazılır.

Azure DNS REST API düzeyinde ETags, HTTP üstbilgileri kullanılarak belirtilir.  Davranışları aşağıdaki tabloda verilmiştir:

| Üst bilgi | Davranış |
| --- | --- |
| Yok |PUT her zaman başarılı olur (ETag denetimi yok) |
| IF-Match \<etag> |PUT yalnızca kaynak varsa ve ETag eşleştirmelerinde başarılı olur |
| IF-Match * |PUT yalnızca kaynak mevcutsa başarılı olur |
| If-None-Match * |PUT yalnızca kaynak yoksa başarılı olur |


## <a name="limits"></a>Sınırlar

Azure DNS kullanırken aşağıdaki varsayılan sınırlar geçerlidir:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Sonraki adımlar

* Azure DNS kullanmaya başlamak için, [DNS bölgesi oluşturmayı](./dns-getstarted-portal.md) ve [DNS kayıtları oluşturmayı](./dns-getstarted-portal.md)öğrenin.
* Var olan bir DNS bölgesini geçirmek için [BIR DNS bölge dosyasını içeri ve dışarı aktarmayı](dns-import-export.md)öğrenin.