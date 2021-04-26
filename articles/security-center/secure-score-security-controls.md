---
title: Azure Güvenlik Merkezi'nde güvenlik puanı
description: Azure Güvenlik Merkezi 'nin güvenli puanı ve güvenlik denetimlerinin açıklaması
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: memildin
ms.openlocfilehash: 2f5d98dd9bf893065f2bf9c37cbec4384d0f7c94
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727149"
---
# <a name="secure-score-in-azure-security-center"></a>Azure Güvenlik Merkezi'nde güvenlik puanı

## <a name="introduction-to-secure-score"></a>Güvenli puana giriş

Azure Güvenlik Merkezi 'nin iki ana hedefi vardır: 

- Geçerli güvenlik durumunuzu anlamanıza yardımcı olması için
- güvenliği verimli ve etkili bir şekilde iyileştirmenize yardımcı olmak için

Güvenlik Merkezi 'nde, bu hedeflere ulaşmanızı sağlayan merkezi özellik **güvenli puanı**.

Güvenlik Merkezi, güvenlik sorunları için kaynaklarınızı, aboneliklerinizi ve kuruluşunuzu sürekli olarak değerlendirir. Daha sonra her türlü bulguları tek bir puanın içinde toplar, böylece bir bakışta geçerli güvenlik durumunuza göre daha yüksek puan, belirtilen risk düzeyini azaltır.

Güvenli puan, Azure portal sayfalarında yüzde değeri olarak gösterilir, ancak temel alınan değerler de açıkça sunulur:

:::image type="content" source="./media/secure-score-security-controls/single-secure-score-via-ui.png" alt-text="Portalda gösterildiği gibi genel güvenli puan":::

Güvenliğinizi artırmak için, puanınızı yükseltmek için gereken bekleyen eylemler için Güvenlik Merkezi 'nin öneriler sayfasını gözden geçirin. Her öneri, belirli bir sorunu düzeltmenizi sağlayacak yönergeler içerir.

Öneriler, **güvenlik denetimleri** halinde gruplandırılır. Her denetim, ilgili güvenlik önerilerinin mantıksal grubudur ve güvenlik açığı bulunan saldırı yüzeylerinizi yansıtır. Puanınız yalnızca bir denetim içindeki tek bir kaynağın *Tüm* önerilerini düzeltdiğinde geliştirilir. Kuruluşunuzun her bir saldırı yüzeyi için ne kadar iyi güvenlik altına alınacağını öğrenmek için her bir güvenlik denetiminin puanlarını gözden geçirin.

Daha fazla bilgi için bkz. [güvenli puanınızın aşağıda nasıl hesaplandığı](secure-score-security-controls.md#how-your-secure-score-is-calculated) . 

## <a name="how-your-secure-score-is-calculated"></a>Güvenli puanınızın hesaplanması 

Her güvenlik denetiminin genel güvenli puana doğru katkısı, öneriler sayfasında net bir şekilde gösterilir.

[![Gelişmiş güvenli skor güvenlik denetimlerini tanıtır](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Bir güvenlik denetimine yönelik tüm olası noktaları almak için, tüm kaynaklarınızın güvenlik denetimindeki tüm güvenlik önerilerine uyması gerekir. Örneğin, güvenlik merkezi 'nin Yönetim bağlantı noktalarınızı güvenli hale getirmeye yönelik birden çok önerisi vardır. Güvenli puanınız için fark yapmak üzere tümünü düzeltmeniz gerekir.

Örneğin, "sistem güncelleştirmelerini Uygula" adlı güvenlik denetiminde en fazla altı puan bulunur ve bu, denetimin olası artış değerindeki araç ipucunda görebileceğiniz şekilde:

[!["Sistem güncelleştirmelerini Uygula" güvenlik denetimi](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

Bu denetim için en yüksek puan, sistem güncelleştirmelerini Uygula, her zaman 6 ' dır. Bu örnekte, 50 kaynak vardır. Bu nedenle, maksimum puanı 50 ile bölyoruz ve sonuç her kaynağın 0,12 puntodur. 

* **Olası artış** (0,12 x 8 sağlıksız kaynak = 0,96)-denetim içinde sizin için kullanılabilir olan diğer noktaları. Bu denetimdeki tüm önerileri düzeltmeniz durumunda puanınız %2 oranında artar (Bu durumda 0,96 nokta, 1 noktaya yuvarlanır). 
* **Geçerli skor** (0,12 x 42 sağlıklı kaynaklar = 5,04)-Bu denetimin geçerli puanı. Her denetim toplam puanta katkıda bulunur. Bu örnekte, denetim 5,04, geçerli güvenli toplamın işaret eder.
* **Maksimum puan** -denetim içindeki tüm önerileri tamamlayarak elde edebilmeniz gereken en yüksek puan sayısıdır. Bir denetim için maksimum puan, bu denetimin göreli anlam olduğunu gösterir. İlk olarak çalışma sorunlarını önceliklendirme için maksimum puan değerlerini kullanın. 


### <a name="calculations---understanding-your-score"></a>Hesaplamalar-puanınızı anlama

|Metric|Formül ve örnek|
|-|-|
|**Güvenlik denetiminin geçerli puanı**|<br>![Güvenlik denetiminin Puanını hesaplama denklemi](media/secure-score-security-controls/secure-score-equation-single-control.png)<br><br>Her bireysel güvenlik denetimi güvenlik puanına katkıda bulunur. Denetim içindeki bir öneriden etkilenen her kaynak, denetimin geçerli puanına doğru katkıda bulunur. Her denetim için geçerli *puan, denetimdeki kaynakların durumunun* ölçüsüdür.<br>![Güvenlik denetiminin geçerli puanı hesaplanırken kullanılan değerleri gösteren araç ipuçları](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>Bu örnekte, sağlıklı ve sağlıksız kaynakların toplamı olduğundan, 6 ' nın en fazla puanı 78 olarak bölünür.<br>6/78 = 0,0769<br>Sağlıklı kaynak sayısına (4) göre çarpılması geçerli puanın sonucunu elde ediyor:<br>0,0769 * 4 = **0,31**<br><br>|
|**Güvenlik puanı**<br>Tek abonelik|<br>![Aboneliğin güvenli Puanını hesaplama denklemi](media/secure-score-security-controls/secure-score-equation-single-sub.png)<br><br>![Tüm denetimler etkin olan tek abonelik güvenli puanı](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>Bu örnekte, kullanılabilen tüm güvenlik denetimlerine sahip tek bir abonelik vardır (en fazla 60 punto puanı). Puan, olası bir 60 28 noktayı gösterir ve kalan 32 noktaları, güvenlik denetimlerinin "potansiyel puan artışı" biçiminde yansıtılır.<br>![Denetimlerin listesi ve olası puan artışı](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Güvenlik puanı**<br>Birden çok abonelik|<br>![Birden çok abonelik için güvenli puanı hesaplama denklemi](media/secure-score-security-controls/secure-score-equation-multiple-subs.png)<br><br>Birden çok abonelik için Birleşik puanı hesaplarken, güvenlik merkezi her abonelik için bir *Ağırlık* içerir. Abonelikleriniz için göreli ağırlıklar, Güvenlik Merkezi tarafından, kaynak sayısı gibi faktörlere göre belirlenir.<br>Her aboneliğin geçerli puanı, tek bir abonelikle aynı şekilde hesaplanır, ancak daha sonra ağırlığa, denklemde gösterildiği gibi uygulanır.<br>Birden çok abonelik görüntülenirken, güvenli puan tüm etkin ilkelerin içindeki tüm kaynakları değerlendirir ve her bir güvenlik denetiminin en fazla puanı üzerinde Birleşik etkileri gruplandırır.<br>![Tüm denetimler etkin olan birden çok abonelik için güvenli puan](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>Birleşik puan bir ortalama **değil** ; Bunun yerine, tüm aboneliklerdeki tüm kaynakların durumunun değerlendirilmiş bir şekilde olduğunu gösterir.<br>Burada, öneriler sayfasına giderseniz ve kullanılabilir olası noktaları eklerseniz, geçerli puan (24) ile kullanılabilir maksimum puan (60) arasındaki fark olduğunu göreceksiniz.|


### <a name="which-recommendations-are-included-in-the-secure-score-calculations"></a>Hangi öneriler, güvenli puan hesaplamalarına dahildir?

Yalnızca yerleşik önerilerin güvenli puanı üzerinde etkisi vardır.

**Önizleme** olarak işaretlenen öneriler, güvenli puanınızın hesaplamalarına dahil değildir. Önizleme dönemi sona erdiğinde puanınızın altına katkıda bulunduklarında, bunlar mümkün olduğunda yine de düzeltilmelidir.

Önizleme önerisi örneği:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Önizleme bayrağıyla ilgili öneri":::

## <a name="improve-your-secure-score"></a>Secure score’unuzu iyileştirme

Güvenli puanınızı iyileştirmek için, öneriler listenizden güvenlik önerilerini düzeltin. Her bir kaynak için veya **hızlı düzeltme** kullanarak her öneriyi el ile düzeltebilirsiniz! bir kaynak grubuna hızlıca bir öneri için düzeltme uygulamak üzere (kullanılabilir olduğunda) seçeneği. Daha fazla bilgi için bkz. [önerileri](security-center-remediate-recommendations.md)düzeltme.

Puanınızı artırmanın ve kullanıcılarınızın puanınızı olumsuz yönde etkileyen kaynaklar oluşturmadığından ve ilgili önerilerden zorla ve reddetme seçeneklerini yapılandıracağından emin olmanın bir diğer yolu. [Zorla/reddetme önerilerini kullanarak yanlış yapılandırma önleme konusunda](prevent-misconfigurations.md)daha fazla bilgi edinin.

## <a name="security-controls-and-their-recommendations"></a>Güvenlik denetimleri ve önerileri

Aşağıdaki tabloda, Azure Güvenlik Merkezi 'ndeki güvenlik denetimleri listelenmiştir. Her denetim için, denetimde listelenen *Tüm* önerileri, *Tüm* kaynaklarınız için düzeltmeniz durumunda, güvenli puanlarınıza ekleyebileceğiniz en fazla puan sayısını görebilirsiniz. 

Güvenlik Merkezi ile sağlanan güvenlik önerileri kümesi, her bir kuruluşun ortamındaki kullanılabilir kaynaklara göre tasarlanmıştır. Öneriler, [ilkeleri devre dışı bırakarak](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations) ve [belirli kaynakları bir öneriden muaf](exempt-resource.md)bırakarak daha da özelleştirilebilir. 
 
Her kuruluşun, atanan Azure Ilke girişimlerini dikkatle gözden geçirmesini öneririz. 

> [!TIP]
> Girişimlerinizi İnceleme ve düzenlemeyle ilgili ayrıntılar için bkz. [güvenlik Ilkeleriyle çalışma](tutorial-security-policy.md). 

Güvenlik Merkezi 'nin varsayılan güvenlik girişimi sektörde en iyi uygulamaları ve standartları temel alsa da, aşağıda listelenen yerleşik önerilerin kuruluşunuza tamamen uymamasının bir senaryosu vardır. Sonuç olarak, kuruluşunuzun kendi ilkeleriyle uyumlu olduğundan emin olmak için, güvenliği tehlikeye atmadan, bazen varsayılan girişimi ayarlamanız gerekir. endüstri standartları, mevzuat standartları ve buluşmak üzere olduğunuz kıyaslamalar.<br><br>
<div class="foo">

<style type="text/css"> . TG {Border-daraltma: daraltma; kenarlık-boşluk: 0;}. TG TD {Border-Color: Siyah; kenarlık stili: Solid; Border-width: 1px; yazıtipi-Family: Arial, sans-serif; font-size: 14px; overflow: Hidden; Padding: 10px 5px; sözcük-Break: normal;}. TG TH {Border-Color: Siyah; kenarlık stili: Solid; kenarlık genişliği: 1px; yazı tipi-ailesi: Arial, sans-serif; font-size: 18px; yazı tipi-ağırlığı: normal; taşma: gizli; doldurma: 10px 5px; sözcük-Break: normal;}. TG. TG-cly1 {text-align: Left; dikey-align: Middle}. TG. TG-lboı {Border-Color: Inherit; metin-Hizala: Left; Dikey-Hizala: Orta} </style>

[!INCLUDE [security-center-controls-and-recommendations](../../includes/asc/security-control-recommendations.md)]

</div>




## <a name="secure-score-faq"></a>Güvenli puan SSS

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Bir güvenlik denetimindeki dört önerimin yalnızca üç tanesi ele alıyorsa, güvenli puanımı değişmem gerekir mi?
Hayır. Tek bir kaynağın tüm önerilerini düzeltene kadar değişmez. Bir denetim için en fazla puanı almak üzere tüm kaynaklar için tüm önerileri düzeltmeniz gerekir.

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Öneri bana uygulanabilir değilse ve ilkede devre dışı bıraktıktan sonra güvenlik denetimin yerine getirilmesi ve güvenli puanım güncellenmem gerekir mi?
Evet. Ortamınızda uygulandıklarında önerilerin devre dışı bırakılmasını öneririz. Belirli bir öneriyi devre dışı bırakma hakkında yönergeler için bkz. [güvenlik Ilkelerini devre dışı bırakma](./tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Güvenlik denetimi, güvenli puanma doğru bir şekilde sıfır puan sunduğunda, bunu yoksaymalıdır mi?
Bazı durumlarda, en yüksek puanı sıfırdan büyük bir denetim görürsünüz, ancak etki sıfırdır. Kaynakları düzeltmeye yönelik artımlı puan göz ardı edildiğinde sıfıra yuvarlanır. Bu önerileri, hala güvenlik geliştirmeleri getirdiklerinde yok saymayın. Tek özel durum, "ek En Iyi Yöntem" denetimidir. Bu önerilerin yapılması puanınızı artırmaz, ancak genel güvenliğinizi geliştirir.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, güvenli puanı ve tarafından sunulan güvenlik denetimleri açıklanmaktadır. İlgili malzemeler için aşağıdaki makalelere bakın:

- [Önerinin farklı öğeleri hakkında bilgi edinin](security-center-recommendations.md)
- [Önerileri nasıl düzelteceğinizi öğrenin](security-center-remediate-recommendations.md)
- [Güvenli puanla programlama yoluyla çalışmaya yönelik GitHub tabanlı araçları görüntüleyin](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)


> [!div class="nextstepaction"]
> [Güvenli puanınızı erişin ve izleyin](secure-score-access-and-track.md)