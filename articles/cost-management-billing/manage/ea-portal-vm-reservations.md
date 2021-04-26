---
title: Azure EA VM ayrılmış örnekleri
description: Bu makalede, sanal makine ayrılmış örnekleri için Azure rezervasyonlarının, kurumsal kaydınızla para tasarrufu yapmanıza nasıl yardımcı olabildiği özetlenmektedir.
author: bandersmsft
ms.author: banders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 6303a94cec9efc01815b6dc6c697abdfe0f84227
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220909"
---
# <a name="azure-ea-vm-reserved-instances"></a>Azure EA VM ayrılmış örnekleri

Bu makalede, sanal makine ayrılmış örnekleri için Azure rezervasyonlarının, kurumsal kaydınızla para tasarrufu yapmanıza nasıl yardımcı olabildiği özetlenmektedir. Rezervasyonlar hakkında daha fazla bilgi için bkz. [Azure Ayrılmış Sanal Makine Örnekleri nedir?](../reservations/save-compute-costs-reservations.md)

## <a name="reservation-exchanges-and-refunds"></a>Rezervasyon değişiklikleri ve para iadeleri

Bir rezervasyonu aynı türdeki başka bir rezervasyonla değiştirebilirsiniz. İhtiyaç duymadığınız rezervasyonları da iade ederek yılda 50.000 ABD dolarına kadar para iadesi alabilirsiniz. Rezervasyon değiştirmek veya para iadesi almak için Azure portalını kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure Ayrılmış Sanal Makine Örnekleri için self servis değişimler ve para iadeleri](../reservations/exchange-and-refund-azure-reservations.md).

### <a name="partial-refunds"></a>Kısmi para iadeleri

EA müşterileri, fazla kullanım ve parasal olmayan Azure Ön Ödemesi (eski adıyla parasal taahhüt) kullanılarak satın alınan rezervasyonları iade ettiğinde kısmi para iadesi yaparız.

Para iadesi önceki aya ait negatif ve geçerli aya ait pozitif bir ayarlama olarak EA portalında gösterilir. Bu işlemler, rezervasyon değişimine benzer şekilde gösterilir. Alacak faturasında özgün fatura numarası belirtildiğinden, alacak faturasıyla ilk satın almanın mutabakatını yapmak için lütfen özgün fatura numarasını belirtin.

## <a name="reservation-costs-and-usage"></a>Rezervasyon maliyetleri ve kullanımı

Kurumsal anlaşma müşterileri, Azure portalında ve REST API’lerde maliyet ve kullanım verilerini görüntüleyebilir. Rezervasyon maliyetleri ve kullanımı için şunları yapabilirsiniz:

- Rezervasyon satın alma verilerini alma.
- Hangi aboneliğin, kaynak grubunun veya kaynağın rezervasyonu kullandığını öğrenme.
- Rezervasyon kullanımı için geri ödeme.
- Rezervasyon tasarruflarını hesaplama.
- Rezervasyonun kapasitesi altındaki kullanım verilerini alma.
- Rezervasyon maliyetlerini amorti etme.

Rezervasyon maliyetleri ve kullanımı hakkında daha fazla bilgi için bkz. [Kurumsal Anlaşma rezervasyon maliyetleri ve kullanım bilgilerini alma](../reservations/understand-reserved-instance-usage-ea.md).

Fiyatlandırma hakkında bilgi için bkz. [Linux Sanal Makineleri Fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) veya [Windows Sanal Makineleri Fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

## <a name="reserved-instances-api-support"></a>Ayrılan örnekler API desteği

Kuruluş için Azure hizmeti veya yazılım rezervasyonları ile ilgili bilgileri program aracılığıyla almak için Azure API’lerini kullanın. Örneğin, API'leri kullanarak şunları yapabilirsiniz:

- Satın alınacak rezervasyonları bulma
- Ayırma satın alma
- Satın alınan rezervasyonları görüntüleme
- Rezervasyon erişimini görüntüleme ve yönetme
- Rezervasyonları bölme veya birleştirme
- Rezervasyonların kapsamını değiştirme

Daha fazla bilgi için bkz. [Azure rezervasyon otomasyonu API’leri](../reservations/reservation-apis.md).

## <a name="azure-reserved-virtual-machine-instances"></a>Azure ayrılmış sanal makine örnekleri

Ayrılmış örnekler, sanal makine maliyetlerinizi tüm VM'ler için geçerli olan Kullandıkça Öde fiyatlarına kıyasla %72'ye kadar azaltabilir. Öte yandan Azure hibrit avantajı ile birlikte kullanıldığında da %82'ye kadar azaltabilir. Ayrılmış örnekler bir yıllık veya üç yıllık peşin ödeme yaparak iş yüklerinizi, bütçenizi ve tahminlerinizi daha iyi yönetmenize yardımcı olur. İş gereksinimleriniz değiştikçe rezervasyonları değiştirebilir veya iptal edebilirsiniz.

### <a name="how-to-buy-reserved-virtual-machine-instances"></a>Ayrılmış sanal makine örneklerini satın alma

Azure ayrılmış sanal makine örneği satın almak için bir Kurumsal Azure kayıt yöneticisinin _Ayrılmış Örnek_ satın alma seçeneğini etkinleştirmesi gerekir. Bu seçenek _Azure EA Portal_'ın _Kayıt_ sekmesindeki [Kayıt Ayrıntıları](https://ea.azure.com/) bölümünde yer alır.

EA kaydına ayrılmış örnek ekleme seçeneği etkinleştirildikten sonra EA kaydı ile ilişkilendirilmiş etkin bir aboneliğe sahip olan hesap kullanıcıları, [Azure portalından](https://aka.ms/reservations) ayrılmış sanal makine örneği satın alabilir. Daha fazla bilgi için bkz. [Ayrılmış Sanal Makine Örnekleriyle sanal makineler için önceden ödeme yapma ve tasarruf etme](../../virtual-machines/prepay-reserved-vm-instances.md).

### <a name="how-to-view-reserved-instance-purchase-details"></a>Ayrılmış örnek satın alma ayrıntılarını görüntüleme

Ayrılmış örnek satın alma ayrıntılarınızı [Azure portalının](https://aka.ms/reservations) sol tarafındaki _Rezervasyonlar_ menüsünden veya [Azure EA Portal](https://ea.azure.com/)'dan görüntüleyebilirsiniz. Sol taraftaki menüden **Raporlar**'ı seçin ve _Kullanım Özeti_ sekmesinde _Hizmetlere Göre Ücretler_ bölümüne inin. Bölümün en altına indiğinizde satın alınan ayrılmış örneklerinizin hizmetin yanında `1 year` veya `3 years` ifadesi ile birlikte listelendiğini göreceksiniz, örneğin: `Standard_DS1_v2 eastus 1 year` veya `Standard_D2s_v3 eastus2 3 years`.

### <a name="how-can-i-change-the-subscription-associated-with-reserved-instance-or-transfer-my-reserved-instance-benefits-to-a-subscription-under-the-same-account"></a>Ayrılmış örnek ile ilişkilendirilmiş olan aboneliği nasıl değiştirebilirim veya ayrılmış örnek avantajlarımı aynı hesap altındaki başka bir aboneliğe nasıl aktarabilirim?

Ayrılmış örnek avantajlarından faydalanan aboneliği değiştirmek için:

- [Azure portalında](https://aka.ms/reservations) oturum açın.
- Uygulanan abonelik kapsamını aynı hesabın altındaki farklı bir abonelikle ilişkilendirerek güncelleştirin.

Rezervasyon kapsamını değiştirme hakkında daha fazla bilgi edinmek için bkz. [Rezervasyon kapsamını değiştirme](../reservations/manage-reserved-vm-instance.md#change-the-reservation-scope).

### <a name="how-to-view-reserved-instance-usage-details"></a>Ayrılmış örnek kullanım ayrıntılarını görüntüleme

Ayrılmış örneklerinizin kullanım ayrıntılarını [Azure portalından](https://aka.ms/reservations) veya [Azure EA Portal](https://ea.azure.com/)'da (faturalama bilgilerini görüntüleme erişimine sahip olan EA müşterileri için) _Raporlar_ > _Kullanım Özeti_ > _Hizmetlere Göre Ücretler_ sayfasında görüntüleyebilirsiniz. Adlarındaki "Reservation" ifadesinden ayrılmış örneklerinizi kolayca ayırt edebilirsiniz, örneğin: `Reservation-Base VM or Virtual Machines Reservation-Windows Svr (1 Core)`.

Kullanım ayrıntılarınızda ve CSV biçiminde indirebileceğiniz gelişmiş raporda ayrılmış örnek kullanımıyla ilgili ek bilgiler vardır. _Ek Bilgi_ alanı, ayrılmış örnek kullanımını belirlemenize yardımcı olur.

Azure ayrılmış sanal makine örneklerini satın almak için Azure hibrit avantajından yararlanmadıysanız ayrılmış örnekleriniz iki ölçüm (donanım ve yazılım) sunar. Ayrılmış örnek satın almak için Azure hibrit avantajını kullandığınızda ayrılmış örnek kullanım ayrıntılarınızda yazılım ölçümü bulunmaz.

### <a name="reserved-instance-billing"></a>Ayrılmış örnek faturalaması

Kurumsal müşteriler için Azure ayrılmış sanal makine örnekleri Azure Ön Ödemesi ile satın alınır. Kaydınızda ayrılmış örnek satın alma işlemi için yeterli Azure Ön Ödeme bakiyesi varsa ilgili tutar Ön Ödeme bakiyenizden düşer ve satın alma işlemine ait bir fatura almazsınız.

Azure EA müşterilerinin Azure Ön Ödemesini tükettiği durumlarda da ayrılmış örnek satın alınabilir ve bu satın alma işlemleri bir sonraki fazla kullanım faturanıza dahil edilir. Varsa ayrılmış örnek fazla kullanımı, normal fazla kullanım faturanıza eklenir.

### <a name="reserved-instance-expiration"></a>Ayrılmış örnek süre sonu

Rezervasyon süre sonu ve diğeri süre sonu öncesinde ilk 1 30 gün önce bir e-posta bildirimi alacaksınız. Ayırma süresi sona erdiğinde, dağıtılan VM’ler çalışmaya devam eder ve kullandıkça öde ücreti üzerinden faturalandırılır. Daha fazla bilgi için bkz. [Ayrılmış Sanal Makine Örnekleri teklifi](https://azure.microsoft.com/pricing/reserved-vm-instances/).

## <a name="next-steps"></a>Sonraki adımlar

- Azure rezervasyonları hakkında daha fazla bilgi için bkz. [Azure Rezervasyonlar nedir?](../reservations/save-compute-costs-reservations.md)
- Kurumsal rezervasyon maliyetleri ve kullanımı hakkında daha fazla bilgi edinmek için bkz. [Kurumsal Anlaşma rezervasyon maliyetleri ve kullanım bilgilerini alma](../reservations/understand-reserved-instance-usage-ea.md).
- Fiyatlandırma hakkında bilgi için bkz. [Linux Sanal Makineleri Fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) veya [Windows Sanal Makineleri Fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).
