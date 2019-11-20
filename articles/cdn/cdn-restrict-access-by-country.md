---
title: Ülke/bölgeye göre Azure CDN içeriğini kısıtla | Microsoft Docs
description: Azure CDN içeriğinizi ülke/bölge tarafından coğrafi filtreleme özelliğini kullanarak erişimi kısıtlama hakkında bilgi edinin.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: magattus
ms.openlocfilehash: 75c422d456f2509ce478e2609a6509f78a6eb31e
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593421"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>Ülke/bölgeye göre Azure CDN içeriğini kısıtla

## <a name="overview"></a>Genel Bakış
Kullanıcı varsayılan olarak, içeriği istediğinde, içerik isteği yapan kullanıcının konumunu bağımsız olarak sunulur. Ancak, bazı durumlarda, ülke/bölge tarafından içeriğinize erişimi kısıtlamak isteyebilirsiniz. İle *coğrafi filtreleme* özelliği oluşturabileceğiniz kuralları belirli yollarda izin vermeyi veya engellemeyi içeriği seçilen ülkelerde/bölgelerde CDN uç noktanız.

> [!IMPORTANT]
> **Azure CDN standart Microsoft gelen** profilleri yol tabanlı coğrafi filtreleme desteklemez.
> 

## <a name="standard-profiles"></a>Standart profilleri
Bu bölümdeki yordamlar içindir **akamai'den Azure CDN standart** ve **verizon'dan Azure CDN standart** yalnızca profiller. 

İçin **verizon'dan Azure CDN Premium** profilleri kullanmalıdır **Yönet** coğrafi filtrelemeyi etkinleştirmek için portalı. Daha fazla bilgi için [profillerinden Verizon'dan Azure CDN Premium](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Dizin yolu tanımlayın
Coğrafi filtreleme özelliğe erişmek için CDN uç noktanıza Portal'ı seçin ve ardından **coğrafi filtreleme** sol menüdeki ayarlar altında. 

![Standart coğrafi filtreleme](./media/cdn-filtering/cdn-geo-filtering-standard.png)

Gelen **yolu** kutusunda, istediğiniz kullanıcılar izin verilen ya da erişim engellendi konumuna göreli yolunu belirtin. 

Tüm dosyaları ile bir ileri eğik çizgi (/) veya dizin yolları belirterek belirli klasörleri seçmeniz için coğrafi filtreleme uygulayabilirsiniz (örneğin, */resimler*). Ayrıca coğrafi filtreleme tek bir dosyaya uygulayabilirsiniz (örneğin */pictures/city.png*). Birden çok kural izin verilir; bir kural girdikten sonra sonraki kural girmek boş bir satır görüntülenir.

Örneğin, aşağıdaki dizin yolu filtrelerinin tümüne geçerli şunlardır:   
*/*                                 
*/Photos/*      
*/Photos Strasbourg /*      
*/Photos/Strasbourg/City.PNG*

### <a name="define-the-type-of-action"></a>Eylemin türünü tanımlayın

Gelen **eylem** listesinden **izin** veya **blok**: 

- **İzin**: Yalnızca belirtilen ülkeler/bölgeler kullanıcılardan özyinelemeli yolundan istenen varlıklara erişmesine izin verilir.

- **Blok**: Belirtilen ülkeler/bölgeler kullanıcılardan özyinelemeli yolundan istenen varlıklara erişimi reddedilir. Ardından bu konumda için diğer ülke/bölge filtreleme seçeneği yapılandırıldıysa, diğer tüm kullanıcıların erişimine izin verilir.

Yolun engelleme gibi bir coğrafi filtreleme kuralı */fotoğraflar/Strasbourg/* aşağıdaki dosyalar filtrelenir:     
*http:\//\<uç noktası >.azureedge.net/Photos/Strasbourg/1000.jpg*
*http:\//\<uç noktası >.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>Ülkeler/bölgeler tanımlayın
Gelen **ülke kodları** listesinde, engellemek veya yol için izin vermek istediğiniz ülke/bölge seçin. 

Ülkeler/bölgeler seçerek tamamladıktan sonra seçin **Kaydet** yeni coğrafi filtreleme kuralını etkinleştirmek için. 

![Coğrafi filtreleme kuralları](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Kaynakları temizleme
Kural silmek için listeden seçin **coğrafi filtreleme** sayfasında ve ardından **Sil**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Profillerinden Verizon'dan Azure CDN Premium
İçin **verizon'dan Azure CDN Premium için** profilleri, kullanıcı arabirimini farklı bir coğrafi filtreleme kuralı oluşturmak için:

1. Azure CDN profilinizde üstteki menüden seçin **Yönet**.

2. Verizon Portalı'ndan seçin **HTTP büyük**, ardından **ülke filtrelemesi**.

    ![Standart coğrafi filtreleme](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. Seçin **ülke Filtre Ekle**.

    **Adım bir:** sayfası görüntülenir.

4. Dizin yolu girin, seçin **blok** veya **Ekle**, ardından **sonraki**.

    **Adım iki:** sayfası görüntülenir. 

5. Listeden bir veya daha fazla ülke/bölge seçin ve ardından **son** Kuralı etkinleştirmek için. 
    
    Yeni Kural tabloda görüntülenir **ülke filtrelemesi** sayfası.

    ![Coğrafi filtreleme kuralları](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Kaynakları temizleme
Ülke/bölge filtreleme kuralları tablosunda yanındaki bir kuralı silmek için Sil simgesine veya değiştirmek için Düzenle simgesini seçin.

## <a name="considerations"></a>Dikkat edilmesi gerekenler
* Coğrafi filtreleme yapılandırma değişiklikleri hemen etkili olmaz:
   * **Microsoft’tan Azure CDN Standart** profilleri için yayma işlemi genellikle 10 dakikada tamamlanır. 
   * **Akamai’den Azure CDN Standart** profilleri için yayma işlemi genellikle bir dakika içinde tamamlanır. 
   * **Verizon’dan Azure CDN Standart** ve **Verizon’dan Azure CDN Premium** profilleri için yayma işlemi genellikle 10 dakika içinde tamamlanır. 
 
* Bu özellik, joker karakterleri desteklemiyor (örneğin, *).

* Göreli yol ile ilişkili coğrafi filtreleme yol yinelemeli olarak uygulandığından bir yapılandırmadır.

* Yalnızca bir kural aynı göreli yol uygulanabilir. Diğer bir deyişle, aynı göreli yolunu işaret eden birden fazla ülke/bölge filtre oluşturulamıyor. Ancak, ülke/bölge filtreleri özyinelemeli olduğundan, birden fazla ülke/bölge filtresi bir klasör olabilir. Diğer bir deyişle, önceden yapılandırılmış bir klasörün bir alt farklı ülke/bölge filtresi atanabilir.

* Coğrafi filtreleme özelliği, bir isteği izin verilen veya engellenen için güvenli bir dizin ülkeler/bölgeler tanımlamak için ülke kodları kullanır. Aynı ülke kodları çoğunu Akamai ve Verizon'dan profillerini desteklese de, bazı farklar vardır. Daha fazla bilgi için [Azure CDN ülke kodları](/previous-versions/azure/mt761717(v=azure.100)). 

