---
title: DNS Bölgeleri ve kayıtlara genel bakış-Azure DNS | Microsoft Docs
description: Microsoft Azure DNS 'de DNS bölgelerini ve kayıtlarını barındırma desteğine genel bakış.
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: victorh
ms.openlocfilehash: fdf9b60e38ad37334fe6183bb1a9c60cce9f85e1
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832068"
---
# <a name="overview-of-dns-zones-and-records"></a>DNS bölgelerine ve kayıtlarına genel bakış

Bu sayfada etki alanları, DNS bölgeleri ve DNS kayıtları ve kayıt kümelerinin temel kavramları ve bunların Azure DNS nasıl desteklendiği açıklanmaktadır.

## <a name="domain-names"></a>Etki alanı adları

Etki Alanı Adı Sistemi, bir etki alanları hiyerarşisidir. Hiyerarşi, adı yalnızca " **.** " olan "kök" etki alanından başlar.  Bunun altında "com", "net", "org", "uk" veya "jp" gibi en üst düzey etki alanları bulunur.  Bunların altında "org.uk" veya "co.jp" gibi ikinci düzey etki alanları bulunur. DNS hiyerarşisindeki etki alanları, dünyanın dört bir yanındaki DNS ad sunucuları tarafından barındırılan küresel olarak dağıtılır.

Etki alanı adı kaydedici, `contoso.com`gibi bir etki alanı adı satın almanızı sağlayan bir kuruluştur.  Bir etki alanı adı satın alma, bu ad altında DNS hiyerarşisini denetleme hakkını sağlar, örneğin adı `www.contoso.com` şirketinizin Web sitenize yönlendirmenize olanak tanır. Kaydedici, etki alanını sizin adınıza kendi ad sunucularında barındırabilir veya alternatif ad sunucuları belirtmenize izin verebilir.

Azure DNS, etki alanınızı barındırmak için kullanabileceğiniz, küresel olarak dağıtılmış, yüksek kullanılabilirliğe sahip bir ad sunucu altyapısı sağlar. Etki alanlarınızı Azure DNS barındırarak DNS kayıtlarınızı, diğer Azure hizmetlerinizle aynı kimlik bilgileri, API, araç, faturalandırma ve destek ile yönetebilirsiniz.

Azure DNS, şu anda etki alanı adlarının satın alınmasını desteklememektedir. Bir etki alanı adı satın almak istiyorsanız, üçüncü taraf etki alanı adı kaydedicisi kullanmanız gerekir. Kaydedici genellikle küçük bir yıllık ücreti ücretlendirir. Etki alanları daha sonra DNS kayıtlarının yönetimi için Azure DNS içinde barındırılabilir. Ayrıntılar için bkz. [Azure DNS'ye bir etki alanı devretme](dns-domain-delegation.md).

## <a name="dns-zones"></a>DNS bölgeleri

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>DNS kayıtları

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Yaşam süresi

Yaşam süresi veya TTL, her kaydın yeniden sorgulama yapılmadan önce istemciler tarafından ne kadar süre önbelleğe alınacağını belirtir. Yukarıdaki örnekte, TTL 3600 saniye veya 1 saattir.

Azure DNS, TTL, her kayıt için değil kayıt kümesi için belirtilir, bu nedenle bu kayıt kümesi içindeki tüm kayıtlar için aynı değer kullanılır.  1 ila 2.147.483.647 saniye arasında herhangi bir TTL değeri belirtebilirsiniz.

### <a name="wildcard-records"></a>Joker karakter kayıtları

Azure DNS [joker kayıtlarını](https://en.wikipedia.org/wiki/Wildcard_DNS_record) destekler. Joker karakter kayıtları, eşleşen ada sahip herhangi bir sorguya yanıt olarak döndürülür (joker karakter olmayan bir kayıt kümesinden daha yakından eşleşme yoksa). Azure DNS, NS ve SOA hariç tüm kayıt türleri için joker karakter kayıt kümelerini destekler.

Joker karakter kayıt kümesi oluşturmak için, '\*' kayıt kümesi adını kullanın. Alternatif olarak, en sol etiketi olarak '\*' ile bir ad da kullanabilirsiniz; Örneğin, '\*. foo '.

### <a name="caa-records"></a>CAA kayıtları

CAA kayıtları, etki alanı sahiplerinin, etki alanı için sertifika verme yetkisine sahip olan sertifika yetkililerini (CA 'Lar) belirtmesini sağlar. Bu, CA 'Ların bazı koşullarda yanlış bir şekilde sertifika verilmesine engel olmasını sağlar. CAA kayıtlarının üç özelliği vardır:
* **Bayraklar**: Bu, [RFC](https://tools.ietf.org/html/rfc6844#section-3) başına özel anlamı olan kritik bayrağını temsil etmek için kullanılan 0 ile 255 arasında bir tamsayıdır
* **Etiket**: aşağıdakilerden biri olabılecek bir ASCII dizesi:
    * **sorun**: sertifika verme Izni verilen CA 'ları belirtmek istiyorsanız bunu kullanın (tüm türler)
    * **ıssuewildcard**: sertifika verme Izni verilen CA 'ları belirtmek istiyorsanız bunu kullanın (yalnızca Joker sertifikaları)
    * **iodef**: CA 'ların yetkisiz sertifika verme isteklerini bildirebileceği bir e-posta adresi veya ana bilgisayar adı belirtin
* **Değer**: seçilen belirli bir etiketin değeri

### <a name="cname-records"></a>CNAME kayıtları

CNAME kaydı kümeleri aynı ada sahip diğer kayıt kümeleriyle birlikte olamaz. Örneğin, göreli adı ' www ' olan bir CNAME kayıt kümesi ve aynı anda göreli adı ' www ' olan bir kayıt oluşturamazsınız.

Bölge tepesinde (Name = '\@') her zaman bölgenin oluşturulduğu sırada oluşturulan NS ve SOA kayıt kümelerini içerdiğinden, tepesinde bölgesinde bir CNAME kayıt kümesi oluşturamazsınız.

Bu kısıtlamalar DNS standartlarından oluşur ve Azure DNS kısıtlamalarıdır.

### <a name="ns-records"></a>NS kayıtları

Bölge tepesinde (adı '\@') konumundaki NS kaydı, her DNS bölgesiyle otomatik olarak oluşturulur ve bölge silindiğinde otomatik olarak silinir (Ayrıca silinemez).

Bu kayıt kümesi, bölgeye atanan Azure DNS ad sunucularının adlarını içerir. Birden fazla DNS sağlayıcısıyla ortak barındırma etki alanlarını desteklemek için bu NS kayıt kümesine ek ad sunucuları ekleyebilirsiniz. Bu kayıt kümesi için TTL ve meta verileri de değiştirebilirsiniz. Ancak, önceden doldurulmuş Azure DNS adı sunucularını kaldıramaz veya değiştiremezsiniz. 

Bu, yalnızca tepesinde bölgesindeki NS kayıt kümesi için geçerlidir. Bölgesindeki diğer NS kayıt kümeleri (alt bölgeleri temsilci atamak için kullanılan), kısıtlama olmadan oluşturulabilir, değiştirilebilir ve silinebilir.

### <a name="soa-records"></a>SOA kayıtları

SOA kayıt kümesi, her bölgenin tepesinde (ad = '\@') üzerinde otomatik olarak oluşturulur ve bölge silindiğinde otomatik olarak silinir.  SOA kayıtları ayrı olarak oluşturulamaz veya silinemez.

Azure DNS tarafından belirtilen birincil ad sunucu adına başvurmak üzere önceden yapılandırılmış olan ' host ' özelliği dışında SOA kaydının tüm özelliklerini değiştirebilirsiniz.

Bölgede kayıtlar üzerinde değişiklik yapıldığında SOA kaydındaki bölge seri numarası otomatik olarak güncellenmez. Gerekirse, SOA kaydı düzenlenerek el ile güncelleştirilebilen bir şekilde yapılandırılabilir.

### <a name="spf-records"></a>SPF kayıtları

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>SRV kayıtları

[SRV kayıtları](https://en.wikipedia.org/wiki/SRV_record) , çeşitli hizmetler tarafından sunucu konumlarını belirtmek için kullanılır. Azure DNS içinde bir SRV kaydı belirtirken:

* *Hizmet* ve *protokol* , kayıt kümesi adının bir parçası olarak belirtilmelidir, alt çizgi öneki.  Örneğin, '\_SIP.\_tcp.name '.  Tepesinde bölgesindeki bir kayıt için, kayıt adında '\@' belirtmeniz gerekmez, örneğin '\_SIP gibi hizmet ve protokolü kullanmanız yeterlidir.\_TCP '.
* *Öncelik*, *Ağırlık*, *bağlantı noktası*ve *hedef* , kayıt kümesindeki her bir kaydın parametresi olarak belirtilir.

### <a name="txt-records"></a>TXT kayıtları

TXT kayıtları, etki alanı adlarını rastgele metin dizeleriyle eşlemek için kullanılır. Bunlar, [Gönderen Ilke çerçevesi (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) ve [DomainKeys tarafından tanımlanan posta (dkim)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail)gibi, e-posta yapılandırmasıyla ilgili birden çok uygulamada kullanılır.

DNS standartları tek bir TXT kaydına, her birinin en fazla 254 karakter uzunluğunda olabilen birden çok dize içermesine izin verir. Birden çok dize kullanıldığında, istemciler tarafından birleştirilir ve tek bir dize olarak kabul edilir.

Azure DNS REST API çağrılırken her bir TXT dizesini ayrı ayrı belirtmeniz gerekir.  Azure portal, PowerShell veya CLı arabirimlerini kullanırken, her kayıt için tek bir dize belirtmelisiniz, bu, gerekirse otomatik olarak 254 karakter segmentlerine bölünmüştür.

Bir DNS kaydındaki birden çok dize, bir TXT kayıt kümesindeki birden çok TXT kaydıyla karıştırılmamalıdır.  Bir TXT kayıt kümesi, *her biri* birden çok dize içerebilen birden çok kayıt içerebilir.  Azure DNS, her bir TXT kayıt kümesinde en fazla 1024 karakterden oluşan toplam dize uzunluğunu destekler (Birleşik tüm kayıtlar arasında).

## <a name="tags-and-metadata"></a>Etiketler ve meta veriler

### <a name="tags"></a>Etiketler

Etiketler, ad-değer çiftleri listesidir ve kaynakları etiketlemek için Azure Resource Manager tarafından kullanılır.  Azure Resource Manager, Azure faturanızda Filtrelenmiş görünümleri etkinleştirmek için Etiketler kullanır ve ayrıca etiketlerin gerekli olduğu bir ilke ayarlamanıza olanak sağlar. Etiketler hakkında daha fazla bilgi için bkz. [Etiketleri kullanarak Azure kaynaklarınızı düzenleme](../azure-resource-manager/resource-group-using-tags.md).

Azure DNS, DNS bölge kaynaklarında Azure Resource Manager etiketlerin kullanılmasını destekler.  DNS kayıt kümelerinde etiketleri desteklemez, ancak aşağıda açıklandığı gibi DNS kayıt kümelerinde alternatif bir ' Metadata ' desteklenir.

### <a name="metadata"></a>Meta Veriler

Kayıt kümesi etiketlerinin bir alternatifi olarak Azure DNS, ' Metadata ' kullanarak kayıt kümelerine açıklama eklemeyi destekler.  Etiketlere benzer şekilde meta veriler, ad-değer çiftlerini her bir kayıt kümesiyle ilişkilendirmenizi sağlar.  Bu, örneğin, her bir kayıt kümesinin amacını kaydetmek yararlı olabilir.  Etiketlerin aksine, meta veriler Azure faturanızda filtrelenmiş bir görünüm sağlamak için kullanılamaz ve bir Azure Resource Manager ilkesinde belirtilemez.

## <a name="etags"></a>ETag 'ler

İki kişinin veya iki işlemin aynı anda bir DNS kaydını değiştirmeyi deneydiğini varsayalım. Tek bir WINS mi? Ayrıca, başka biri tarafından oluşturulan değişikliklerin üzerine yazıldıklarından emin misiniz?

Azure DNS, aynı kaynaktaki eşzamanlı değişiklikleri güvenli bir şekilde işlemek için ETags kullanır. ETags [Azure Resource Manager ' Tags '](#tags)öğesinden ayrıdır. Her DNS kaynağına (bölge veya kayıt kümesi) ilişkili bir ETag öğesi vardır. Her kaynak alındığında ETag de alınır. Bir kaynağı güncelleştirirken, Azure DNS sunucu üzerindeki ETag 'in eşleştiğini doğrulayabilmesi için ETag 'i geri geçirmeye tercih edebilirsiniz. Bir kaynaktaki her güncelleştirme ETag 'in yeniden üretildiğinden dolayı bir ETag uyumsuzluğu, eşzamanlı bir değişikliğin gerçekleştiğini gösterir. ETags, kaynağın zaten mevcut olmamasını sağlamak için yeni bir kaynak oluştururken de kullanılabilir.

Varsayılan olarak, Azure DNS PowerShell, bölgelerde ve kayıt kümelerinde eşzamanlı değişiklikleri engellemek için ETags kullanır. İsteğe bağlı *-üzerine yazma* anahtarı ETag denetimlerini bastırmak için kullanılabilir, bu durumda gerçekleşen eşzamanlı değişiklikler üzerine yazılır.

Azure DNS REST API düzeyinde ETags, HTTP üstbilgileri kullanılarak belirtilir.  Davranışları aşağıdaki tabloda verilmiştir:

| Üst bilgi | Davranış |
| --- | --- |
| None |PUT her zaman başarılı olur (ETag denetimi yok) |
| IF-Match \<ETag > |PUT yalnızca kaynak varsa ve ETag eşleştirmelerinde başarılı olur |
| IF-Match * |PUT yalnızca kaynak mevcutsa başarılı olur |
| If-None-Match * |PUT yalnızca kaynak yoksa başarılı olur |


## <a name="limits"></a>Sınırlar

Azure DNS kullanırken aşağıdaki varsayılan sınırlar geçerlidir:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Sonraki adımlar

* Azure DNS kullanmaya başlamak için, [DNS bölgesi oluşturmayı](dns-getstarted-create-dnszone-portal.md) ve [DNS kayıtları oluşturmayı](dns-getstarted-create-recordset-portal.md)öğrenin.
* Var olan bir DNS bölgesini geçirmek için [BIR DNS bölge dosyasını içeri ve dışarı aktarmayı](dns-import-export.md)öğrenin.
