---
title: Form Tanıma nedir?
titleSuffix: Azure Cognitive Services
description: Azure bilişsel hizmetler formu tanıyıcı, form belgelerinden anahtar/değer çiftlerini ve tablo verilerini tanımlamanızı ve ayıklamanızı sağlar.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 07/25/2019
ms.author: pafarley
ms.openlocfilehash: 86e0494ded84e9711b2283ae5fad77ff626cf792
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904494"
---
# <a name="what-is-form-recognizer"></a>Form Tanıma nedir?

Azure form tanıyıcı, form belgelerinden anahtar/değer çiftlerini ve tablo verilerini tanımlamak ve ayıklamak için makine öğrenimi teknolojisini kullanan bilişsel bir hizmettir. Ardından, asıl dosyadaki ilişkileri de içeren yapılandırılmış verinin çıktısını verir. Karmaşıklığı azaltmak ve kolayca iş akışınız veya uygulamanızla bütünleştirmek için basit bir REST API kullanarak özel form tanıyıcı modelinize çağrı yapabilirsiniz. Başlamak için, yalnızca beş doldurulmuş form belgelerine veya iki doldurulmuş forma ve giriş malzemeniz ile aynı türde boş bir forma ihtiyacınız vardır. El ile müdahale veya kapsamlı veri bilimi uzmanlığı olmadan, belirli içeriğinize kolayca doğru sonuçlar elde edersiniz.

## <a name="custom-models"></a>Özel modeller

Form tanıyıcı özel modeli kendi verilerinize yönelik olarak, yalnızca başlamak için beş örnek giriş formunuz olması gerekir. Giriş verilerinizi gönderdiğinizde, algoritma formları türe göre kümeler, hangi anahtarların ve tabloların mevcut olduğunu bulur ve değerleri anahtarlar ve girdilerle tablolarla ilişkilendirir. Ardından, asıl dosyadaki ilişkileri de içeren yapılandırılmış verinin çıktısını verir. Modeli eğdikten sonra, bunu test edebilir ve yeniden eğitebilir ve sonunda, gereksinimlerinize göre daha fazla formdan verileri güvenilir bir şekilde çıkarmak için kullanabilirsiniz.

Denetimsiz öğrenme, el ile veri etiketleme veya yoğun kodlama ve bakım olmadan, modelin alanlar ve girdiler arasındaki düzen ve ilişkileri anlamasına izin verir. Buna karşılık, önceden eğitilen makine öğrenimi modelleri standartlaştırılmış veri gerektirir. Sektöre özgü formlar gibi geleneksel biçimlerden farklı olan giriş malzemesiyle daha az doğru olurlar.

## <a name="prebuilt-receipt-model"></a>Önceden oluşturulmuş makbuz modeli

Form tanıyıcı Ayrıca satış alındıları okumak için bir model içerir. Bu model, işlemin saati ve tarihi, ticari bilgiler, vergiler ve toplam miktar gibi önemli bilgileri ayıklar. Ayrıca, önceden oluşturulmuş makbuz modeli, bir Makbuzdaki tüm metni tanımak ve döndürmek için eğitilmiş olur.

## <a name="what-it-includes"></a>Neleri içerir

Form tanıyıcı REST API olarak kullanılabilir. Özel bir model oluşturabilir, eğitme ve puan verebilir ya da bu API 'Leri çağırarak önceden oluşturulmuş modele erişebilirsiniz. İsterseniz, yerel bir Docker kapsayıcısında özel modeller eğitebilir ve çalıştırabilirsiniz.

## <a name="input-requirements"></a>Giriş gereksinimleri
### <a name="custom-model"></a>Özel model

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt-receipt-model"></a>Önceden oluşturulmuş makbuz modeli

Makbuz modeli için giriş gereksinimleri biraz farklıdır.

* Biçim JPEG, PNG, BMP, PDF (metin veya taranan) veya TIFF olmalıdır.
* Dosya boyutu 20 MB 'tan az olmalıdır.
* Resim boyutları 50 x 50 piksel ve 10000 x 10000 piksel arasında olmalıdır. 
* PDF boyutları, yasal veya A3 kağıt boyutlarına ve daha küçük boyutlara karşılık gelen en fazla 17 x 17 inç olmalıdır.
* PDF ve TIFF için yalnızca ilk 200 sayfa işlenir (ücretsiz bir katman aboneliğiyle yalnızca ilk iki sayfa işlenir).

## <a name="request-access"></a>Erişim izni isteme

Form tanıyıcı, sınırlı erişim önizlemesinde kullanılabilir. Önizlemeye erişim sağlamak için [form tanıyıcı erişim isteği](https://aka.ms/FormRecognizerRequestAccess) formunu doldurun ve gönderebilirsiniz. Form, sizin, şirketiniz ve form tanıyıcı kullanacağınız kullanıcı senaryosuyla ilgili bilgiler ister. İsteğiniz Azure bilişsel hizmetler ekibi tarafından onaylanırsa, hizmete erişmek için yönergeler içeren bir e-posta alacaksınız.

## <a name="where-do-i-start"></a>Nereden başlamalıyım?

**1. Adım:** Azure portal bir form tanıyıcı kaynağı oluşturun.

**2. Adım:** REST API kullanmak için hızlı başlangıcı izleyin:
* [Hızlı başlangıç: kıvrımlı ile REST API kullanarak form tanıyıcı modeli eğitme ve form verilerini ayıklama](quickstarts/curl-train-extract.md)
* [Hızlı başlangıç: Python ile REST API kullanarak form tanıyıcı modeli eğitme ve form verilerini ayıklama](quickstarts/python-train-extract.md)
* [Hızlı başlangıç: kıvrımlı kullanarak alış verilerini ayıklama](quickstarts/curl-receipts.md)
* [Hızlı başlangıç: Python kullanarak alış verilerini ayıklama](quickstarts/python-receipts.md)

Teknolojiyi öğrenirken ücretsiz hizmeti kullanmanızı öneririz. Ücretsiz sayfa sayısının ayda 500 ile sınırlı olduğunu aklınızda bulundurun.

**Adım 3:** REST API 'Leri gözden geçirme

Formlardan yapılandırılmış verileri eğitmek ve ayıklamak için aşağıdaki API 'Leri kullanın.

|||
|---|---|
| Model Eğitme| Formlarınızı aynı türden beş form kullanarak analiz etmek için yeni bir model eğitme. Ya da boş bir form ve iki doldurulmuş form ile eğitme.  |
| Formu çözümle |Anahtar/değer çiftlerini ve tabloları özel modelinize göre formdan çıkarmak için akış olarak geçirilmiş tek bir belgeyi analiz edin.  |
| Okundu bilgisi |Anahtar bilgilerini ve diğer makbuz metnini ayıklamak için tek bir alış belgesi çözümleyin.|

Daha fazla bilgi edinmek için [REST API başvuru belgelerini](https://aka.ms/form-recognizer/api) inceleyin. 

## <a name="data-privacy-and-security"></a>Veri gizliliği ve güvenliği

Bu hizmet, [çevrimiçi hizmet koşulları](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)kapsamında bir Azure hizmetinin [önizlemesi](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) olarak sunulur. Tüm bilişsel hizmetlerde olduğu gibi, form tanıyıcı hizmetini kullanan geliştiriciler müşteri verilerinde Microsoft ilkeleriyle uyumlu olmalıdır. Daha fazla bilgi edinmek için Microsoft Güven Merkezi ' nde bilişsel [Hizmetler sayfasına](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) bakın.

## <a name="next-steps"></a>Sonraki adımlar

[Form tanıyıcı API 'lerini](https://aka.ms/form-recognizer/api)kullanmaya başlamak için [hızlı](quickstarts/curl-train-extract.md) başlangıcı doldurun.