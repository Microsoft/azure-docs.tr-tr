---
title: SaaS teklifi oluşturma denetim listesi-Azure için ticari Market
description: SaaS teklifi oluşturma sürecinde sağlayabilmeniz için ayrıntılar. -Azure için ticari Market
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 1643a1e309ecc2bcfc440e3553fcffef54cfb038
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824634"
---
# <a name="saas-offer-creation-checklist"></a>SaaS teklifi oluşturma denetim listesi

SaaS teklifi oluşturma işlemi sizi birden çok sayfaya götürür. Her bir sayfada sağladığınız ayrıntılar, her bir öğe hakkında daha fazla bilgi edinmek için bağlantılarla birlikte verilmiştir.

Sağlamanız veya belirtmeniz gereken öğeler aşağıda belirtilmiştir. Bazı alanların isteğe bağlı olması veya varsayılan değerlere sahip olması, istediğiniz gibi değiştirebilmeniz gerekir. Burada listelenen sırayla bu bölümler üzerinde çalışmanız gerekmez.

| **Öğe**    | **Amaç**  |
| :---------- | :-------------------|
| [**Yeni teklif kalıcı**](#new-offer-modal) | Teklif kimliği bilgilerini toplar.  |
| [Teklif kurulum sayfası](#offer-setup-page) | Temel özellikleri kullanmayı ve teklifinizi Microsoft aracılığıyla nasıl satmayı seçmenizi sağlar.  |
| [Özellikler sayfası](#properties-page) | Teklifinizi Pazar konumlarına, teklifinizi destekleyen yasal sözleşmeleri ve uygulama sürümünüzü gruplamak için kullanılan kategorileri ve endüstrileri tanımlayın. |
| [Teklif listesi sayfası](#offer-listing-page) | Teklif ve pazarlama varlıklarınızın açıklamaları dahil olmak üzere Market 'te görüntülenecek teklif ayrıntılarını tanımlayın. |
| [Önizleme sayfası](#preview-page) | Teklifinizi daha geniş Market hedef kitlelerine yayımlamadan önce teklifinizi serbest bırakmak için sınırlı bir önizleme hedef kitlesi tanımlayın. |
| [Teklif teknik yapılandırma sayfası](#technical-configuration-page)  | Yalnızca teklifi Microsoft üzerinden satmayı seçerseniz kullanılabilir. Teklifinizi bağlamak için kullanılan teknik ayrıntıları (URL yolu, Web kancası, kiracı KIMLIĞI ve uygulama KIMLIĞI) tanımlayın. |
| [**Yeni plan kalıcı**](#plan-identity-modal) | Plan kimlik bilgilerini toplar.  |
| [Plan listeleme sayfası](#plan-listing-page)  | Yalnızca teklifi Microsoft üzerinden satmayı seçerseniz kullanılabilir. Planı Market 'te listelemek için kullanılan ayrıntıları tanımlayın.  |
| [Fiyatlandırma & kullanılabilirliği sayfasını planlayın](#plan-pricing--availability-page)  | Yalnızca teklifi Microsoft üzerinden satmayı seçerseniz kullanılabilir.  Teklifinizin her bir planı (sürümü) için iş özelliklerini (fiyatlandırma modeli), hedef kitleyi ve Pazar kullanılabilirliğini toplar.  |
| [Test sürücü listeleme sayfası](#test-drive-listing-page)  | Yalnızca teklifiniz için bir test sürücüsü sunmayı seçerseniz kullanılabilir. Market 'teki test sürücüsünü listelemek için kullanılan ayrıntıları tanımlayın.  |
| Sınama sürücüsü teknik yapılandırma sayfası  | Yalnızca teklifiniz için bir test sürücüsü sunmayı seçerseniz kullanılabilir. Müşterilerin satın alma işlemine geçmeden önce teklifinizi denemesini sağlayacak tanıtım (veya "test sürücüsü") için teknik ayrıntıları tanımlayın.  |
| [İnceleme ve yayımlama sayfası](#review-and-publish-page)  | Yayımlamak istediğiniz değişiklikleri seçin, her sayfanın durumuna bakın ve sertifika ekibine not sağlayın.  |


## <a name="new-offer-modal"></a>Yeni teklif kalıcı 

Sunabileceğiniz bilgilerin ilk parçaları teklifiniz için bir KIMLIK ve diğer addır. 

| **Alan adı**    | **Notlar**   |  
| :---------------- | :-----------| 
| Teklif KIMLIĞI  | Gerekli, oluşturulduktan sonra değiştirilemez. Maksimum 50 karakter ve yalnızca küçük harf, alfasayısal karakter, kısa çizgi veya alt çizgi içermelidir. |
| Teklif diğer adı  | Gereklidir. |

## <a name="offer-setup-page"></a>Teklif kurulum sayfası

Teklif kurulumu sayfası, farklı kanalları kabul edebilir ve ücretlerinizi satıyor ve test sürücüsü ve müşteri adayları gibi önemli özelliklerin kullanımını bildirebileceğiniz yerdir. 

| **Alan adı**    | **Notlar**   | 
| :---------------- | :-----------|  
| Microsoft üzerinden satımek istiyor musunuz?  | Gereklidir. Varsayılan: Evet |
| Potansiyel müşterilerin teklif listesi ile nasıl etkileşime geçmesini istiyorsunuz? (Eyleme çağrı)  | Microsoft üzerinden satılırken gereklidir. Varsayılan: ücretsiz deneme, Seçenekler: "Şimdi al", "ücretsiz deneme", "benimle Iletişim kurma". |
| Deneme URL 'SI  | Müşterilerin teklif listesi ile etkileşim kurması için "ücretsiz deneme" seçilirse gereklidir. |
| Teklif URL 'SI  | Müşterilerin teklif listesi ile etkileşim kurması için "Şimdi al" seçildiyse gereklidir |
| Kanallar  | İsteğe bağlı. Varsayılan: CSP (satıcı) kanalına kabul edilmedi.  |
| Test Sürüşü | İsteğe bağlı. Varsayılan: etkin bir test sürücüsü yok.  |
| Test sürücüsünün türü | Bir sınama sürücüsü etkinleştirildiyse gereklidir. Varsayılan: hiçbiri seçili değil. Seçenekler: Azure Resource Manager, Iş Merkezi için Dynamics 365, müşteri katılımı için Dynamics 365, Işlemler için Dynamics 365, mantıksal uygulama, Power BI.  |
| Müşteri adayı yönetimi-bir CRM sistemine bağlanma | Microsoft üzerinden satılırken veya liste "bana başvur" olarak sunulduğunda gereklidir. Varsayılan: hiçbir CRM sistemi bağlanmadı. CRM seçenekleri: Azure tablosu, Azure blobu, Dynamics CRM Online, HTTPs ' uç noktası, Marketo, Salesforce  |

## <a name="properties-page"></a>Özellikler sayfası

Özellikler sayfası, teklifinizi markette gruplandırmak için kullanılan kategorileri ve endüstrileri, teklifinizi destekleyen yasal sözleşmeleri ve uygulama sürümünüzü tanımladığınız yerdir. Bu sayfada teklif hakkında tam ve doğru ayrıntıları sağladığınızdan emin olun, böylece uygun şekilde görüntülenir ve doğru müşteri kümesine sunulur. 

| **Alan adı**    | **Notlar**   | 
| :---------------- | :-----------|  
| Kategori ve alt kategori | Gerekli 1 ve en fazla 3. Varsayılan: hiçbiri seçili değil. |
| Sektörler ve alt sektör | İsteğe bağlı. Her L1 sektöründe maksimum 2 L1 sektörleri ve maksimum 2 alt sektör, varsayılan: hiçbiri seçilmedi |
| Uygulama sürümü  | İsteğe bağlı. Varsayılan: yok. |
| Standart Sözleşme kullan  | İsteğe bağlı. Varsayılan: seçili değil.  | |
| Kullanım koşulları  | Standart Sözleşme seçilmemişse gereklidir.  |

## <a name="offer-listing-page"></a>Teklif listesi sayfası

Liste sayfası, müşterilerin Market 'te teklifinizin listesini görüntülerken göreceği metin ve görüntüleri sağladığınız yerdir. 

| **Alan adı**    | **Notlar**   |
| :---------------- | :-----------| 
| Ad  | Gerekli, en fazla 50 karakter. |
| Özet  | Gerekli, en fazla 100 karakter. | 
| Açıklama  | Gerekli, en fazla 3000 karakter. |
| Başlarken yönergeleri  | Gerekli, en fazla 3000 karakter. |
| Başlarken yönergeleri  | Gerekli, en fazla 3000 karakter. |
| Anahtar sözcüklerde ara  | İsteğe bağlı, önerilen, en fazla 3 anahtar sözcük. |
| Gizlilik ilkesi URL 'SI  | Gereklidir. |
| CSP program pazarlama malzemeleri URL 'SI  | İsteğe bağlı. |
| Faydalı bağlantılar başlığı + URL  | İsteğe bağlı. |
| Destekleyici belgeler başlık + dosya  | Gerekli, en az 1 ve en fazla 3. PDF dosyası biçimi olmalıdır. |
| Ekran görüntüleri  | Gerekli, en az 1 ekran görüntüsü ve en fazla 5; dört veya daha fazla önerilir. PNG biçiminde 1280 X 720 olmalıdır. |
| Mağaza logoları (küçük, orta, büyük, geniş, Hero)  | Küçük (48 X 48) ve büyük (216 X 216) gereklidir; diğer boyutlar isteğe bağlıdır ancak önerilir: Orta (90 x 90), geniş (255 x 115), Hero (815 x 290). PNG biçiminde olmalıdır. |
| Videolar adı + URL + küçük resim  | İsteğe bağlı, önerilen, en fazla 4 video. Küçük resim, PNG biçiminde 1280 x 720 olmalıdır. Videonun YouTube veya Vimeo 'da barındırılması gerekir. |
| Kişiler (CSP programı, mühendislik, destek)  | Mühendislik ve destek ilgili kişisi gerekli (ad, e-posta ve telefon numarası); CSP programı isteğe bağlı, ancak önerilir. |
| Destek URL 'SI  | Gereklidir. |

## <a name="preview-page"></a>Önizleme sayfası

Önizleme sayfası, teklifin, canlı olmadan önce tüm gereksinimlerinizi karşıladığından emin olmak için, teklif önizlemeye erişim sağlamak için hedef kitleyi belirlediğiniz yerdir. 

| **Alan adı**    | **Notlar**   | 
| :---------------- | :-----------| 
| AAD/MSA e-postası + açıklama | El ile girilmişse, en az 1 ve en fazla 10, bir CSV dosyası karşıya yüklüyorsanız 20 ' ye kadar, gereklidir. |

## <a name="technical-configuration-page"></a>Teknik yapılandırma sayfası 

Teknik yapılandırma sayfası, Microsoft tarafından teklifinizi bağlamak için kullanılan teknik ayrıntıları belirlediğiniz yerdir. Microsoft ile satımamaya karar verdiyseniz Bu sayfa sizin için görünür değildir.

| **Alan adı**    | **Notlar**   |  
| :---------------- | :-----------| 
| Giriş sayfası URL 'SI | Microsoft üzerinden satılırken gereklidir. |
| Bağlantı Web kancası | Microsoft üzerinden satılırken gereklidir. |
| Azure AD kiracı KIMLIĞI | Microsoft üzerinden satılırken gereklidir. |
| Azure AD uygulama KIMLIĞI | Microsoft üzerinden satılırken gereklidir. |

## <a name="plan-identity-modal"></a>Kimlik planlaması kalıcı

Sağladığınız ilk bilgi parçaları, planınız için bir ad ve bir KIMLIK. Microsoft ile satımamaya karar verdiyseniz Bu sayfa sizin için görünür değildir.

| **Alan adı**    | **Notlar**   |  
| :---------------- | :-----------| 
| Plan KIMLIĞI  | Microsoft üzerinden satılırken gereklidir. Oluşturulduktan sonra değiştirilemez. Maksimum 50 karakter ve yalnızca küçük harf, alfasayısal karakter, kısa çizgi veya alt çizgi içermelidir. |
| Plan Adı  | Microsoft üzerinden satılırken gereklidir. Teklifteki tüm planlar genelinde benzersiz olmalıdır. En fazla 50 karakter. |

## <a name="plan-listing-page"></a>Plan listeleme sayfası

Plan listeleme sayfası, müşterilerin planı Market 'te görüntülerken göreceği metni sağladığınız yerdir. Microsoft ile satımamaya karar verdiyseniz Bu sayfa sizin için görünür değildir.

| **Alan adı**    | **Notlar**   |  
| :---------------- | :-----------| 
| Plan açıklaması   | Microsoft üzerinden satılırken gereklidir. En fazla 500 karakter. | |

## <a name="plan-pricing--availability-page"></a>Fiyatlandırma & kullanılabilirliği sayfasını planlayın

Fiyatlandırma ve kullanılabilirlik planı sayfası, teklifinizin her bir planı (sürümü) için iş özelliklerini, kitleyi ve Pazar kullanılabilirliğini tanımladığınız yerdir. Microsoft ile satımamaya karar verdiyseniz Bu sayfa sizin için görünür değildir.

| **Alan adı**    | **Notlar**   | 
| :---------------- | :-----------| 
| Pazar kullanılabilirliği  | Gerekli, en az 1 ve en fazla 141. |
| Fiyatlandırma Modeli  | Gereklidir. Varsayılan: düz fiyat. Seçenekler: Kullanıcı başına düz fiyat. |
| En düşük ve en yüksek koltuk  | İsteğe bağlı, yalnızca bilgisayar tabanlı fiyatlandırma modeli seçilirse kullanılabilir. |
| Fatura dönemi  | Gereklidir. Varsayılan: aylık. Seçenekler: aylık, yıllık. |
| Fiyat  | Aylık faturalandırma dönemi seçilmişse, ayda bir aylık USD gereklidir; yıllık faturalandırma şartı seçilirse yıl başına ABD Doları. |
| Hedef kitle planlayın  | İsteğe bağlı. Varsayılan: genel plan. Seçenekler: ortak, kiracı KIMLIĞINE göre özel |
| Kısıtlanmış plan hedef kitlesi (kiracı KIMLIĞI + açıklama)  | Özel plan seçilmişse gereklidir. El ile girilmişse, en az 1 ve en fazla 10 Kiracı kimliği. CSV dosyası içeri aktarırsanız Max 20000. |

## <a name="test-drive-listing-page"></a>Test sürücü listeleme sayfası

Yalnızca teklifiniz için bir test sürücüsü sunmayı seçerseniz kullanılabilir. Market 'teki test sürücüsünü listelemek için kullanılan ayrıntıları tanımlayın.

| **Alan adı**    | **Notlar**   | 
| :---------------- | :-----------| 
| Açıklama  | Gereklidir. |
| Kullanıcı El Ile adı + dosyası  | Gerekli, en fazla 1 belge. PDF biçiminde olmalıdır. |
| Video adı, URL + küçük resim  | İsteğe bağlı, önerilir. Küçük resim, JPGP veya PNG biçiminde 533 x 324 olmalıdır. Videonun YouTube veya Vimeo 'da barındırılması gerekir. |

## <a name="review-and-publish-page"></a>İnceleme ve yayımlama sayfası

| **Alan adı**    | **Notlar**   | 
| :---------------- | :-----------| 
| Sertifika notları  | İsteğe bağlı. |

## <a name="next-steps"></a>Sonraki adımlar

- [Yeni bir SaaS teklifi oluşturun](./create-new-saas-offer.md)
