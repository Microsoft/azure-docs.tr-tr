---
title: Bing Özel Arama deneyiminizi yapılandırma | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Portal, Web 'in dilimlerini belirten bir arama örneği oluşturmanızı sağlar; etki alanları, alt sayfalar ve Web sayfaları.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: c14376cc80373371ec5fcb8f22a00584a6b2f714
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072827"
---
# <a name="configure-your-bing-custom-search-experience"></a>Bing Özel Arama deneyiminizi yapılandırın

Özel bir arama örneği, arama deneyimini yalnızca kullanıcılarınızın ilgilenme Web sitelerinden içerik içerecek şekilde uyarlamanızı sağlar. Bing, web genelinde bir arama gerçekleştirmek yerine yalnızca sizi ilgilendiren Web Slice 'ları arar. Size özel web görünümünü oluşturmak için Bing Özel Arama [portalını](https://customsearch.ai) kullanın.

Portal, Web 'in (etki alanları, alt sayfalar ve Web sayfaları), Bing 'in aramasını ve aramasını istemediğiniz olanları belirten bir arama örneği oluşturmanızı sağlar. Portal, dahil etmek isteyebileceğiniz içerikleri de önerebilir.

Web 'in dilimlerinizi tanımlarken aşağıdakileri kullanın:

| Dilim adı | Açıklama                                                                                                                                                                                                                                                                                                |
|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Domain     | Bir etki alanı dilimi, bir İnternet etki alanı içinde bulunan tüm içeriği içerir. Örneğin, `www.microsoft.com`. `www.` atlanması, Bing 'in etki alanının alt etki alanlarını da aramasına neden olur. Örneğin, `microsoft.com`belirtirseniz, Bing Ayrıca `support.microsoft.com` veya `technet.microsoft.com`sonuçları da döndürür. |
| Sayfaya    | Alt sayfa dilimi, alt sayfa ve altındaki yollarda bulunan tüm içeriği içerir. Yolda en fazla iki alt sayfaların sayısını belirtebilirsiniz. Örneğin, `www.microsoft.com/en-us/windows/`                                                                                                                       |
| Web    | Bir Web sayfası dilimi yalnızca özel bir aramada bu Web sayfasını içerebilir. İsteğe bağlı olarak, alt sayfaların eklenip eklenmeyeceğini belirtebilirsiniz.                                                                                                                                                                                  |

> [!IMPORTANT]
> Belirttiğiniz tüm etki alanları, alt sayfalar ve Web sayfaları, kamu ve Bing tarafından dizinlenmelidir. Aramaya dahil etmek istediğiniz bir ortak siteniz varsa ve Bing onu dizinlenmediyse, dizin oluşturma hakkında ayrıntılı bilgi edinmek için Bing [Web uzmanı belgelerine](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) bakın. Ayrıca, Dizin güncel değilse, Gezilen sitenizi güncelleştirme hakkında daha fazla bilgi edinmek için Web uzmanı belgelerine bakın.

## <a name="add-slices-of-the-web-to-your-custom-search-instance"></a>Web 'in dilimlerini özel arama örneğinize ekleyin

Özel arama örneğinizi oluştururken, arama sonuçlarınızdan dahil edilmesini veya engellenmesini istediğiniz Web, etki alanları, alt sayfalar ve Web sayfaları için Web Slice 'ları belirtebilirsiniz. 

Özel arama örneğinize dahil etmek istediğiniz dilimleri biliyorsanız, bunları örneğinizin **etkin** listesine ekleyin. 

Hangi dilimlerin dahil edileceğini bilmiyorsanız, **Önizleme** bölmesinde Bing 'e arama sorguları gönderebilir ve istediğiniz dilimleri seçebilirsiniz. Bunu yapmak için: 

1. Önizleme bölmesindeki açılan listeden "Bing" i seçin ve bir arama sorgusu girin

2. Dahil etmek istediğiniz sonucun yanına **Site Ekle** ' ye tıklayın. Daha sonra Tamam'a tıklayın.

>[!NOTE]
> [!INCLUDE[publish or revert](./includes/publish-revert.md)]

<a name="active-and-blocked-lists"></a>

### <a name="customize-your-search-experience-with-active-and-blocked-lists"></a>Etkin ve engellenen listelerle arama deneyiminizi özelleştirin 

Özel arama örneğinizin **etkin** ve **Engellenen** sekmelerine tıklayarak etkin ve engellenen dilimlerin listesine erişebilirsiniz. Etkin listeye eklenen dilimler özel aramanıza dahil edilir. Engellenen dilimler aranmaz ve arama sonuçlarınızda görünmez.

Bing 'in aramasını istediğiniz Web Slice 'ları belirtmek için **etkin** sekmesine tıklayın ve bir veya daha fazla URL ekleyin. URL 'Leri düzenlemek veya silmek için, **denetimler** sütununun altındaki seçenekleri kullanın. 

**Etkin** listeye URL eklerken, karşıya yükle simgesini kullanarak bir metin dosyasını karşıya yükleyerek tek URL 'ler veya birden çok URL ekleyebilirsiniz.

![Bing Özel Arama etkin sekmesi](media/file-upload-icon.png)

Bir dosyayı karşıya yüklemek için bir metin dosyası oluşturun ve her satırda tek bir etki alanı, alt sayfa veya Web sayfası belirtin. Dosyanız, doğru biçimlendirilmediyse reddedilir.

> [!NOTE]
> * **Etkin** listeye yalnızca bir dosya yükleyebilirsiniz. Bunu, **Engellenen** listeye dilimler eklemek için kullanamazsınız.  
> * **Engellenen** listede karşıya yükleme dosyasında belirttiğiniz bir etki alanı, alt sayfa veya Web sayfası varsa, bu, **Engellenen** listeden kaldırılır ve **etkin** listesine eklenir.
> * Karşıya yükleme dosyanızdaki yinelenen girişler, Bing Özel Arama tarafından yok sayılacak. 

### <a name="get-website-suggestions-for-your-search-experience"></a>Arama deneyiminiz için Web sitesi önerileri alın

**Etkin** listeye Web Slices eklendikten sonra, Bing özel arama portalı sekmenin en altında Web sitesi ve alt sayfa önerilerini oluşturacaktır. Bunlar, dahil etmek isteyebileceğiniz Bing Özel Arama dilimlerdir. Özel arama örneğinizin ayarlarını güncelleştirdikten sonra güncelleştirilmiş öneriler almak için **Yenile** ' ye tıklayın. Bu bölüm yalnızca öneriler varsa görünür.

## <a name="search-for-images-and-videos"></a>Görüntü ve video arayın

[Bing özel resım arama API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) 'Sini veya [BING özel video arama API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)'sini kullanarak Web içeriğiyle benzer görüntü ve video arayabilirsiniz. Bu sonuçları [barındırılan Kullanıcı arabirimi](hosted-ui.md)veya API 'ler ile görüntüleyebilirsiniz. 

Bu API 'Ler, özel olmayan [Bing resim arama](../Bing-Image-Search/overview.md) ve [Bing video arama](../Bing-Video-Search/search-the-web.md) API 'lerine benzerdir, ancak tüm Web 'de arama yapın ve `customConfig` sorgu parametresi gerektirmez. Görüntüler ve videolar ile çalışma hakkında daha fazla bilgi için bu belge kümelerine bakın. 

## <a name="test-your-search-instance-with-the-preview-pane"></a>Önizleme bölmesiyle arama örneğinizi test etme

Arama sorguları göndermek ve sonuçları görüntülemek için portalın sağ tarafındaki önizleme bölmesini kullanarak arama örneğinizi test edebilirsiniz. 

1. Arama kutusunun altında **örneğim**' ı seçin. **Bing**' i seçerek arama deneyiminizdeki sonuçları Bing ile karşılaştırabilirsiniz. 
2. Güvenli bir arama filtresi seçin ve hangi pazarın aranmasına (bkz. [Sorgu parametrelerine](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)bakın).
3. Geçerli yapılandırmanın sonuçlarını görüntülemek için bir sorgu girin ve ENTER tuşuna basın veya arama simgesine tıklayın. İlgili sonuçları almak için **Web**, **resim**veya **video** ' ya tıklayarak gerçekleştirdiğiniz arama türünü değiştirebilirsiniz. 

<a name="adjustrank"></a>

## <a name="adjust-the-rank-of-specific-search-results"></a>Belirli arama sonuçlarının derecesini ayarla

Portal, belirli etki alanları, alt sayfalar ve Web sayfalarından içerik arama sıralamasını ayarlamanıza olanak sağlar. Önizleme bölmesinde bir arama sorgusu gönderdikten sonra, her arama sonucu, sizin için yapabileceğiniz ayarlamaların bir listesini içerir:  

|            |                                                                                                                                                                      |
|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Engelle      | Etki alanı, alt sayfa veya Web sayfasını engellenen listeye gider. Bing, seçili sitenin içeriğini arama sonuçlarında görünecek şekilde dışarıda bırakır.                    |
| Artırmak      | Etki alanı veya alt sayfa içeriğini arama sonuçlarında daha yüksek olacak şekilde.                                                                                        |
| İndirgeme     | Arama sonuçlarındaki etki alanı veya alt sayfa içeriğini indirger. İçeriğin, Web sayfasının ait olduğu etki alanı veya alt sayfa arasında mi indirgeneceğini seçersiniz. |
| En üste sabitle | Etki alanı, alt sayfa veya Web sayfasını **sabitlenmiş** listeye taşıın. Bu, Web sayfasını belirli bir arama sorgusunun en üst arama sonucu olarak görünmesini zorlar.                   |

Görüntü veya video aramalarında derece ayarlama kullanılamaz.

### <a name="boosting-and-demoting-search-results"></a>Arama sonuçlarını artırma ve indirgeme

**Etkin** listedeki herhangi bir etki alanını veya alt sayfayı süper artırabilir, artırabilir veya alçaltabilirsiniz. Varsayılan olarak, tüm dilimler sıralama ayarlamaları olmadan eklenir. Web 'in süper veya daha fazla maliyetli olan dilimleri, arama sonuçlarında daha üst dereceye sahiptir (yükseği daha yüksek olan süper arttırma derecelendirmesiyle). İndirgenen öğeler, arama sonuçlarında daha düşük sıralanır.

**Etkin** listedeki denetimleri **ayarlama** denetimlerini kullanarak veya Önizleme bölmesindeki artırma ve indirgeme denetimlerini kullanarak öğeleri süper artırma, artırma veya alçaltabilirsiniz. Hizmet, dilimi etkin listenize ekler ve derecelendirmeyi buna göre ayarlar.

> [!NOTE] 
> Etki alanlarını ve alt sayfalarını artırma ve indirgeme, Bing Özel Arama arama sonuçlarının sırasını belirlemede kullanılan birçok yöntemden biridir. Farklı Web içeriğinin sıralamasını etkileyen diğer faktörler nedeniyle, ayarlama sırasının etkileri farklılık gösterebilir. Arama sonuçlarınızın sırasını ayarlamaya yönelik etkileri test etmek için Önizleme bölmesini kullanın. 

Görüntü ve video aramalarında süper artırma, artırma ve indirgeme kullanılamaz.

## <a name="pin-slices-to-the-top-of-search-results"></a>Dilimleri arama sonuçlarının en üstüne sabitle

Portal Ayrıca, **sabitlenmiş** sekmeyi kullanarak belirli arama koşullarına ait URL 'leri arama sonuçlarının üst kısmına sabitlemenizi sağlar. en üstteki sonuç olarak görünecek Web sayfasını belirtmek IÇIN bir URL ve sorgu girin. Arama sorgusu başına en fazla bir Web sayfası sabitleyebilir ve aramalarda yalnızca dizinli Web sayfaları görüntülenecektir. Görüntü veya video aramaları için, sabitleme sonuçları kullanılamaz.

Bir Web sayfasını en üste iki şekilde sabitleyebilir:

* Sabitlenmiş sekmesine, en üste **sabitlediğiniz** Web sayfasının URL 'sini ve ilgili sorguyu girin.

* **Önizleme** bölmesinde bir arama sorgusu girin ve ara ' ya tıklayın. Sorgunuz için sabitlemek istediğiniz Web sayfasını bulun ve **en üste sabitle**' ye tıklayın. Web sayfası ve sorgu **sabitlenmiş** listeye eklenecektir.

### <a name="specify-the-pins-match-condition"></a>PIN 'in eşleşme koşulunu belirtin

Varsayılan olarak, Web sayfaları yalnızca bir kullanıcının sorgu dizesi **sabitlenmiş** listede listelenen biriyle tam olarak eşleştiğinde arama sonuçlarının üstüne sabitlenir. Aşağıdaki eşleşme koşullarından birini belirterek, bu davranışı değiştirebilirsiniz:

> [!NOTE]
> Kullanıcının arama sorgusu ve PIN 'in arama sorgusu arasındaki tüm karşılaştırmalar büyük/küçük harfe duyarlıdır.

| Değer | Açıklama                                                                          |
|---------------|----------------------------------------------------------------------------------|
| İle başlar | Kullanıcının sorgu dizesi, PIN 'in sorgu dizesiyle başlıyorsa, PIN bir eşleşmedir |
| Şununla biter   | Kullanıcının sorgu dizesi, PIN 'in sorgu dizesiyle biterse PIN bir eşleşmedir.  |
| Contains    | Kullanıcının sorgu dizesi, PIN 'in sorgu dizesini içeriyorsa, PIN bir eşleşmedir.   |


PIN 'in eşleşme koşulunu değiştirmek için, PIN 'in düzenleme simgesine tıklayın. **Sorgu eşleştirme koşulu** sütununda, açılan listeye tıklayın ve kullanılacak yeni koşulu seçin. Sonra, değişikliği kaydetmek için kaydet simgesine tıklayın.

### <a name="change-the-order-of-your-pinned-sites"></a>Sabitlenmiş sitelerinizin sırasını değiştirme

PIN 'larınızın sırasını değiştirmek için, **sabitlenmiş** listenin **denetimler** sütunundaki "Düzenle" simgesine tıklayarak bunları sürükleyip bırakabilir veya sıra numaralarını düzenleyebilirsiniz.

Birden fazla PIN eşleşme koşulunu karşılamadığı takdirde Bing Özel Arama listedeki en üst olanı kullanır.

## <a name="view-statistics"></a>İstatistikleri görüntüle

Özel aramaya uygun düzeyde abone oldıysanız ( [fiyatlandırma sayfalarına](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)bakın), üretim örneklerinizde bir **istatistik** sekmesi eklenir. İstatistikler sekmesinde, çağrı hacmi, popüler sorgular, coğrafi dağıtım, yanıt kodları ve güvenli arama dahil olmak üzere özel arama uç noktalarınızın nasıl kullanıldığı hakkında ayrıntılar gösterilir. Ayrıntıları, belirtilen denetimleri kullanarak filtreleyebilirsiniz.

## <a name="usage-guidelines"></a>Kullanım yönergeleri

- Her bir özel arama örneği için, **etkin** ve **Engellenen** dilimlerde yapabileceğiniz maksimum derecelendirme ayarlaması sayısı 400 ile sınırlıdır.
- Etkin veya engellenen sekmelere bir dilim eklemek, tek bir derecelendirme ayarlaması olarak sayılır.
- Sayıyı iki derecelendirme ayarlaması olarak arttırma ve indirgeme.
- Her bir özel arama örneği için, yapabileceğiniz en fazla PIN sayısı 200 ile sınırlıdır.

## <a name="next-steps"></a>Sonraki adımlar

- [Özel arama örneğinizi çağırma](./search-your-custom-view.md)
- [Barındırılan kullanıcı arabirimi deneyiminizi yapılandırma](./hosted-ui.md)
- [Metni vurgulamak için süsleme işaretçilerini kullanma](../bing-web-search/hit-highlighting.md)
- [Sayfa web sayfaları](./page-webpages.md)
