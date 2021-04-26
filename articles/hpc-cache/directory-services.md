---
title: Azure HPC önbelleğinde genişletilmiş grupları kullanma
description: Azure HPC önbelleğinde depolama hedeflerine istemci erişimi için dizin hizmetlerini yapılandırma
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: 9db7576cb2278cc2ef0d8b93ef04bb633962cd0e
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107255758"
---
# <a name="configure-directory-services"></a>Dizin hizmetlerini yapılandırma

**Dizin Hizmetleri** ayarları, Azure HPC önbelleğinizin, arka uç depolamaya erişmek için kullanıcıların kimliğini doğrulamak üzere bir dış kaynak kullanmasına izin verir.

İş akışınız NFS depolama hedefleri ve 16 ' dan fazla grubun üyesi olan istemciler içeriyorsa, **genişletilmiş grupları** etkinleştirmeniz gerekebilir.

Genişletilmiş grupları etkinleştirmek için düğmeye tıkladıktan sonra, Azure HPC önbelleğinin Kullanıcı ve grup kimlik bilgilerini almak için kullanacağı kaynağı seçmeniz gerekir.

* [Active Directory](#configure-active-directory) -dış Active Directory sunucusundan kimlik bilgileri alın. Bu görev için Azure Active Directory kullanamazsınız.
* [Düz dosya](#configure-file-download) - `/etc/group` `/etc/passwd` bir ağ konumundan indir ve dosyalar.
* [LDAP](#configure-ldap) -bir hafif Dizin Erişim Protokolü (LDAP) ile uyumlu kaynaktan kimlik bilgilerini alın.

> [!NOTE]
> Önbelleğinizin, güvenli alt ağı içinden Grup bilgileri kaynağına erişebildiğinizden emin olun.<!-- + details/examples -->

**Indirilen Kullanıcı adı** alanı, en son grup bilgileri indirmesinin durumunu gösterir.

![Kuruluşunuzdaki Dizin Hizmetleri sayfa ayarları sayfasının ekran görüntüsü, genişletilmiş gruplar için Evet seçeneği işaretli ve kaynak Indirme açık olarak etiketlenmiş açılan menü](media/directory-services-select-group-source.png)

## <a name="configure-active-directory"></a>Active Directory'yi yapılandırma

Bu bölümde, bir dış Active Directory (AD) sunucusundan Kullanıcı ve grup kimlik bilgilerini almak için önbelleğin nasıl ayarlanacağı açıklanmaktadır.

**Active Directory ayrıntıları** altında şu değerleri sağlayın:

* **BIRINCIL DNS** -önbelleğin ad etki alanı adını çözümlemek için kullanabileceği bir etki alanı ad sunucusunun IP adresini belirtin.

* **IKINCIL DNS** (isteğe bağlı)-birincil sunucu kullanılamadığında kullanılacak bir ad sunucusunun adresini girin.

* **Ad DNS etki alanı adı** -kimlik bilgilerini almak için önbelleğin birleşyolacağı ad sunucusunun tam etki alanı adını belirtin.

* **Önbellek sunucusu adı (bilgisayar hesabı)** -ad etki alanına katıldığında, bu HPC önbelleğine atanacak adı ayarlayın. Bu önbellek olarak tanıması kolay bir ad belirtin. Ad en fazla 15 karakter uzunluğunda olabilir ve büyük veya küçük harfler, rakamlar ve kısa çizgiler (-) içerebilir.

**Kimlik bilgileri** bölümünde, Azure HPC önbelleğinin ad sunucusuna erişmek için KULLANABILECEĞI bir ad Yöneticisi Kullanıcı adı ve parola sağlayın. Bu bilgiler depolandığında şifrelenir ve sorgulanamaz.

Sayfanın üst kısmındaki düğmeye tıklayarak ayarları kaydedin.

![Active Directory değerlerin doldurulduğu Yükleme ayrıntıları bölümünün ekran görüntüsü](media/group-download-details-ad.png)

## <a name="configure-file-download"></a>Dosya indirmeyi yapılandırma

Bu değerler, Kullanıcı ve grup bilgilerinizi içeren dosyaları indirmek istiyorsanız gereklidir. Dosyalar standart Linux/UNIX `/etc/group` ve `/etc/passwrd` biçimde olmalıdır.

* **Kullanıcı dosyası URI 'si** -DOSYANıN tam URI 'sini girin `/etc/passwrd` .
* **Grup dosyası URI 'si** -DOSYANıN tam URI 'sini girin `/etc/group` .

![düz dosya indirme için Indirme ayrıntıları bölümünün ekran görüntüsü](media/group-download-details-file.png)

## <a name="configure-ldap"></a>LDAP yapılandırma

Kullanıcı ve grup kimlik bilgilerini almak için AD olmayan bir LDAP kaynağı kullanmak istiyorsanız bu değerleri girin. Bu değerlerle ilgili yardıma ihtiyacınız varsa LDAP yöneticinizle görüşün.

* **LDAP sunucusu** -kullanılacak LDAP sunucusunun tam etki alanı adını veya IP adresini girin. <!-- only one, not up to 3 -->

* **LDAP Temel DN** -LDAP etki alanı için temel ayırt EDICI adı DN biçiminde belirtin. Temel DN 'nizi bilmiyorsanız LDAP yöneticinize danışın.

Sunucu ve Temel DN, LDAP işi yapmak için gereken tek ayardır, ancak ek seçenekler bağlantınızın daha güvenli olmasını sağlar.

![Dizin Hizmetleri sayfa ayarları sayfasının LDAP yapılandırma alanının ekran görüntüsü](media/group-download-details-ldap.png)

**Güvenli erişim** bölümünde LDAP bağlantısı için şifrelemeyi ve sertifika doğrulamayı etkinleştirebilirsiniz. Şifrelemeyi etkinleştirmek için **Evet** ' e tıkladıktan sonra şu seçeneklere sahipsiniz:

* **Sertifikayı doğrula** -bu AYARLANDıĞıNDA, LDAP sunucusunun SERTIFIKASı aşağıdaki URI alanındaki sertifika yetkilisine göre doğrulanır.

* **CA sertifikası URI 'si** -yetkili sertifikanın yolunu belirtin. Bu, CA tarafından doğrulanan bir sertifikanın veya otomatik olarak imzalanan bir sertifikanın bağlantısı olabilir. Bu alan, dışarıdan doğrulanan sertifikalar ayarını kullanmak için gereklidir.

* **Sertifikayı otomatik indir** -bu ayarları gönderdikten hemen sonra bir sertifika indirmeyi denemek istiyorsanız **Evet** ' i seçin.

LDAP güvenliği için statik kimlik bilgileri kullanmak istiyorsanız **kimlik bilgileri** bölümünü girin. Bu bilgiler depolandığında şifrelenir ve sorgulanamaz.

* **Bağlama DN** -LDAP sunucusunda kimlik doğrulamak için kullanılacak bağlama ayırt edici adını girin. (DN biçimini kullanın.)
* **Bağlama parolası** -BIND DN için parola sağlayın.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure HPC önbelleğini bağlama](hpc-cache-mount.md) bölümünde istemci erişimi hakkında daha fazla bilgi edinin
* Kimlik bilgileriniz doğru şekilde indirilmezseniz, kimlik bilgileriniz için yöneticiye başvurun. Gerekirse bir [destek bileti](hpc-cache-support-ticket.md) açın.
