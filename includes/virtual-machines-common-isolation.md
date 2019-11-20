---
title: include dosyası
description: include dosyası
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: d85ea932a62718d04e0c0aff1eeae80472a410f1
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173923"
---
Azure Compute, belirli bir donanım türüyle sınırlanmış ve tek bir müşteriye ayrılmış olan sanal makine boyutları sunar.  Bu sanal makine boyutları, uyumluluk ve düzenleme gereksinimleri gibi öğeler içeren iş yükleri nedeniyle diğer müşterilerden yüksek ölçüde yalıtıma ihtiyaç duyan iş yükleri için idealdir.  Müşteriler Ayrıca, [iç içe geçmiş sanal makineler Için Azure desteği](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)'ni kullanarak bu yalıtılmış sanal makinelerin kaynaklarını daha fazla alt bölümlere ayırmak da tercih edebilir.

Yalıtılmış bir boyut kullanılması, sanal makinenizin o belirli sunucu örneğinde çalışan tek bir tane olmasını garanti eder.  Geçerli yalıtılmış sanal makine teklifleri şunları içerir:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

Her kullanılabilir yalıtılmış boyut hakkında daha fazla bilgiyi [burada](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)bulabilirsiniz.

## <a name="retiring-d15_v2ds15_v2-isolation-on-february-15-2020"></a>15 Şubat 2020 ' de D15_v2/DS15_v2 yalıtımını devre dışı bırakma
Kısa süre önce Azure ayrılmış ana bilgisayarının önizlemesini duyurduk. Bu, kuruluşunuzun Linux ve Windows sanal makinelerini tek kiracılı fiziksel sunucularda çalıştırmanıza olanak sağlar. Yalıtılmış Azure VM 'lerini Azure ayrılmış ana bilgisayarıyla tamamen değiştirmeyi planlıyoruz. **15 şubat 2020 '** den sonra, D15_v2/DS15_v2 Azure VM 'leri artık donanım yalıtılamayabilir.

## <a name="how-does-this-affect-me"></a>Bu değişiklik beni nasıl etkileyecek?
15 Şubat 2020 ' den sonra, artık D15_v2/DS15_v2 Azure sanal makineleriniz için bir yalıtım garantisi sağlamayız. 

## <a name="what-actions-should-i-take"></a>Hangi eylemleri gerçekleştirmeniz gerekir?
Sizin için donanım yalıtımı gerekmiyorsa uygulamanız gereken bir eylem yoktur. 

15 Şubat 2020 tarihinden önce, yalıtım yapmanız gerekiyorsa, şunlardan birini yapmanız gerekir:

• İş yükünüzü Azure adanmış ana bilgisayar önizlemesine [geçirin](https://azure.microsoft.com/blog/introducing-azure-dedicated-host)

• Aynı fiyat performansını almak için bir D15i_v2 ve Azure VM DS15i_v2 [erişim isteyin](https://aka.ms/D15iRequestAccess) . Bu seçenek yalnızca Kullandıkça öde ve bir yıllık ayrılmış örnek senaryolarında kullanılabilir.    

• İş yükünüzü başka bir Azure yalıtılmış sanal makinesine [geçirin](https://azure.microsoft.com/blog/resize-virtual-machines/) . 

Ayrıntılar için aşağıya bakın:

## <a name="timeline"></a>Zaman çizelgesi
| Tarih | Eylem | 
| --- | --- |
| 18 Kasım 2019  | D/DS15i_v2 (PAYG, 1 yıllık RI) kullanılabilirliği |
| 14 Şubat 2020  | DS15i_v2 1-yıl RI satın almak için geçen gün | 
| 15 Şubat 2020   | D/DS15_v2 yalıtım garantisi kaldırıldı | 
| 15 Mayıs 2021  | Devre dışı bırakma D/DS15i_v2 (18 Kasım 2019 tarihinden önce 3 yıllık D/DS15_v2 dışında tüm müşteriler)| 
| 17 Kasım 2022   | 3 yıllık RIS tamamlandığında D/DS15i_v2 devre dışı bırak (18 Kasım 2019 tarihinden önce 3 yıllık bir D/DS15_v2 satın alan müşteriler için) | 

## <a name="faq"></a>SSS
### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>S: portalda yeni D/DS15i_v2 boyutlarını görmüyorum?
Y **: geçerli bir d**/DS15_v2 müşterisiyseniz ve yeni d/DS15i_v2 boyutlarını kullanmak istiyorsanız lütfen bu [formu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u) doldurmanız gerekir

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>S: yeni D/DS15i_v2 boyutları için neden bir kota görmedim?
Y **: geçerli bir d**/DS15_v2 müşterisiyseniz ve yeni d/DS15i_v2 boyutlarını kullanmak istiyorsanız lütfen bu [formu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u) doldurmanız gerekir

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>S: diğer yalıtılmış boyutlar ne zaman devre dışı bırakılacak?
Y **: boyutların**resmi yetkisini alma öncesinde 12 ay boyunca anımsatıcılar sağlıyoruz.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>S: sanal makine, yalıtılmış olmayan bir donanımda yer aldığı zaman kapalı kalma süresi var mı?
Y **: yalıtım**gerekmiyorsa herhangi bir işlem yapmanız gerekmez ve kapalı kalma süresi görmezsiniz.

### <a name="q-are-there-any-cost-changes-for-moving-to-a-non-isolated-virtual-machine"></a>S: yalıtılmış olmayan bir sanal makineye geçmek için herhangi bir maliyet değişikliği var mı?
Y **: Hayır** 

### <a name="q-i-already-purchased-1--or-3-year-reserved-instance-for-d15_v2-or-ds15_v2-how-will-the-discount-be-applied-to-my-vm-usage"></a>S: D15_v2 veya Ds15_v2 için zaten 1 veya 3 yıllık ayrılmış örnek satın aldım. İndirim, VM kullanımma nasıl uygulanır?
**A**: 18 Kasım 2019 tarihinden önce satın alınan RIS, kapsamı otomatik olarak yenı yalıtılmış VM serisine genişletecektir. 

| Uzaktan |  Örnek boyutu esnekliği | Avantaj uygunluk |   
| --- | --- | --- |
|   D15_v2  |   Kapalı     |   D15_v2 ve D15i_v2 |    
|   D15_v2  |   Açık  |   D15_v2 serisi ve D15i_v2 tüm RI avantajını alacaktır. |    
|   D14_v2  |   Açık  |   D15_v2 serisi ve D15i_v2 tüm RI avantajını alacaktır. |    
 
Benzer şekilde Dsv2 serisi için.
 
### <a name="q-i-want-to-purchase-additional-reserved-instances-for-dv2-which-one-should-i-choose"></a>S: dv2 için ek ayrılmış örnekler satın almak istiyorum. Hangisini seçmem gerekir?
Y **: 18**Kasım 2019 ' den sonra satın alınan tüm RIS aşağıdaki davranışa sahiptir. 

| Uzaktan |  Örnek boyutu esnekliği | Avantaj uygunluk |   
| --- | --- | --- |
| D15_v2 |  Kapalı |   Yalnızca D15_v2  
| D15_v2 |  Açık |    D15_v2 serisi, RI avantajını alacaktır. Yeni D15i_v2 bu RI türünden RI avantajına uygun olmayacaktır. | 
| D15i_v2 |     Kapalı | Yalnızca D15i_v2 |  
| D15i_v2 |     Açık  | Yalnızca D15i_v2 | 
 
Örnek boyutu esnekliği, D2_v2, D4_v2 veya D15_v2 gibi diğer boyutlara uygulamak için kullanılamaz. Benzer şekilde, Dsv2 serisi için.  
 
### <a name="q-can-i-buy-a-new-3-year-ri-for-d15i_v2-and-ds15i_v2"></a>S: D15i_v2 ve DS15i_v2 için yeni 3 yıllık RI satın alabilir miyim?
Y **: Hayır**, yeni satın alma için yalnızca 1 yıllık RI kullanılabilir.
 
### <a name="q-can-i-move-my-existing-d15_v2ds15_v2-reserve-instance-to-an-isolated-size-reserved-instance"></a>S: mevcut D15_v2/DS15_v2 Reserve örneğinden yalıtılmış bir ayrılmış örneğe taşıyabilir miyim?
Y **: Bu**, avantajı hem yalıtılmış hem de yalıtılamayan boyutlara uygulanacak olduğundan gerekli değildir. Ancak Azure, mevcut D15_v2/DS15_v2 ayrılmış örneklerin D15i_v2/DS15i_v2 değiştirilmesini destekler. Diğer tüm dv2/Dsv2 ayrılmış örnekleri için, mevcut ayrılmış örneği kullanın veya yalıtılmış boyutlar için yeni ayrılmış örnekler satın alın.
