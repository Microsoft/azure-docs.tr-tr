---
title: Azure EA VM ayrılmış örnekleri
description: Bu makalede, sanal makine ayrılmış örnekleri için Azure rezervasyonlarının, kurumsal kaydınızla para tasarrufu yapmanıza nasıl yardımcı olabildiği özetlenmektedir.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 1ab6a3c0a4497ce49534f2708e13d7479130ba15
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73887175"
---
# <a name="azure-ea-vm-reserved-instances"></a>Azure EA VM ayrılmış örnekleri

Bu makalede, sanal makine ayrılmış örnekleri için Azure rezervasyonlarının, kurumsal kaydınızla para tasarrufu yapmanıza nasıl yardımcı olabildiği özetlenmektedir. Rezervasyonlar hakkında daha fazla bilgi için bkz. [Azure Ayrılmış Sanal Makine Örnekleri nedir?](billing-save-compute-costs-reservations.md)

## <a name="reservation-exchanges-and-refunds"></a>Rezervasyon değişiklikleri ve para iadeleri

Bir rezervasyonu aynı türdeki başka bir rezervasyonla değiştirebilirsiniz. İhtiyaç duymadığınız rezervasyonları da iade ederek yılda 50.000 ABD dolarına kadar para iadesi alabilirsiniz. Rezervasyon değiştirmek veya para iadesi almak için Azure portalını kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure Ayrılmış Sanal Makine Örnekleri için self servis değişimler ve para iadeleri](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="reservation-costs-and-usage"></a>Rezervasyon maliyetleri ve kullanımı

Kurumsal anlaşma müşterileri, Azure portalında ve REST API’lerde maliyet ve kullanım verilerini görüntüleyebilir. Rezervasyon maliyetleri ve kullanımı için şunları yapabilirsiniz:

- Rezervasyon satın alma verilerini alma.
- Hangi aboneliğin, kaynak grubunun veya kaynağın rezervasyonu kullandığını öğrenme.
- Rezervasyon kullanımı için ücreti yansıtma.
- Rezervasyon tasarruflarını hesaplama.
- Rezervasyonun kapasitesi altındaki kullanım verilerini alma.
- Rezervasyon maliyetlerini amorti etme.

Rezervasyon maliyetleri ve kullanımı hakkında daha fazla bilgi için bkz. [Kurumsal Anlaşma rezervasyon maliyetleri ve kullanım bilgilerini alma](billing-understand-reserved-instance-usage-ea.md).

Fiyatlandırma hakkında bilgi için bkz. [Linux Sanal Makineleri Fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) veya [Windows Sanal Makineleri Fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

## <a name="reserved-instances-api-support"></a>Ayrılan örnekler API desteği

Kuruluşunuz için Azure hizmeti veya yazılım rezervasyonları ile ilgili bilgileri program aracılığıyla almak için Azure API’lerini kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure rezervasyon otomasyonu API’leri](billing-reservation-apis.md).

## <a name="azure-reserved-virtual-machine-instances"></a>Azure ayrılmış sanal makine örnekleri

Ayrılmış örnekler, sanal makine maliyetlerinizi tüm VM'ler için geçerli olan Kullandıkça Öde fiyatlarına kıyasla %72'ye kadar, Azure hibrit avantajı ile birlikte kullanıldığında da %82'ye kadar azaltabilir. Bir yıllık veya üç yıllık peşin ödeme yaparak iş yüklerinizi önceliklendirebilir, bütçenizi belirleyebilir ve daha iyi tahminlerde bulunabilirsiniz. İş gereksinimleriniz değiştikçe rezervasyonları değiştirebilir veya iptal edebilirsiniz.

### <a name="how-to-buy-reserved-virtual-machine-instances"></a>Ayrılmış sanal makine örneklerini satın alma

Azure ayrılmış sanal makine örneği satın almak için bir Kurumsal Azure kayıt yöneticisinin [Azure EA Portal](https://ea.azure.com/)'ın _Kayıt_ sekmesindeki _Kayıt Ayrıntıları_ bölümünde yer alan _Ayrılmış Örnek_ satın alma seçeneğini etkinleştirmesi gerekir.

EA kaydına ayrılmış örnek ekleme seçeneği etkinleştirildikten sonra EA kaydı ile ilişkilendirilmiş etkin bir aboneliğe sahip olan hesap kullanıcıları, [Azure portalından](https://aka.ms/reservations) ayrılmış sanal makine örneği satın alabilir. Daha fazla bilgi için bkz. [Ayrılmış Sanal Makine Örnekleriyle sanal makineler için önceden ödeme yapma ve tasarruf etme](https://go.microsoft.com/fwlink/?linkid=861721).

### <a name="how-to-view-reserved-instance-purchase-details"></a>Ayrılmış örnek satın alma ayrıntılarını görüntüleme

Ayrılmış örnek satın alma ayrıntılarınızı [Azure portalının](https://aka.ms/reservations) sol tarafındaki _Rezervasyonlar_ menüsünden veya [Azure EA Portal](https://ea.azure.com/)'dan görüntüleyebilirsiniz. Sol taraftaki menüden **Raporlar**'ı seçin ve _Kullanım Özeti_ sekmesinde _Hizmetlere Göre Ücretler_ bölümüne inin. Bölümün en altına indiğinizde satın alınan ayrılmış örneklerinizin hizmetin yanında "1 yıl" veya "3 yıl" ifadesi ile birlikte listelendiğini göreceksiniz, örneğin: Standard_DS1_v2 eastus 1 yıl veya Standard_D2s_v3 eastus2 3 yıl.

### <a name="how-can-i-change-the-subscription-associated-with-reserved-instance-or-transfer-my-reserved-instance-benefits-to-a-subscription-under-the-same-account"></a>Ayrılmış örnek ile ilişkilendirilmiş olan aboneliği nasıl değiştirebilirim veya ayrılmış örnek avantajlarımı aynı hesap altındaki başka bir aboneliğe nasıl aktarabilirim?

Ayrılmış örnek avantajlarından aynı anda yalnızca bir hesap faydalanabilir. Ayrılmış örnek avantajlarından faydalanan aboneliği değiştirmek için:

- [Azure portalında](https://aka.ms/reservations) oturum açın.
- Uygulanan abonelik kapsamını aynı hesabın altındaki farklı bir abonelikle ilişkilendirerek güncelleştirin.

### <a name="how-to-view-reserved-instance-usage-details"></a>Ayrılmış örnek kullanım ayrıntılarını görüntüleme

Ayrılmış örneklerinizin kullanım ayrıntılarını [Azure portalından](https://aka.ms/reservations) veya [Azure EA Portal](https://ea.azure.com/)'da (faturalama bilgilerini görüntüleme erişimine sahip olan EA müşterileri için) _Raporlar_ > _Kullanım Özeti_ > _Hizmetlere Göre Ücretler_ sayfasında görüntüleyebilirsiniz. Adlarındaki "Reservation" ifadesinden ayrılmış örneklerinizi kolayca ayırt edebilirsiniz, örneğin: Ayırma-Temel VM veya Sanal Makineler Ayırma-Windows Svr (1 Çekirdek).

Kullanım ayrıntılarınızda ve CSV biçiminde indirebileceğiniz gelişmiş raporda ayrılmış örnek kullanımıyla ilgili ek bilgiler yer alır. _Ek Bilgi_ alanı, ayrılmış örnek kullanımını belirlemenize yardımcı olur.

Azure ayrılmış sanal makine örneklerini satın almak için Azure hibrit avantajından faydalanmadıysanız ayrılmış örnekleriniz iki ölçüm (donanım ve yazılım) sunar. Ayrılmış örnek satın almak için Azure hibrit avantajını kullandıysanız ayrılmış örnek kullanım ayrıntılarınızda yazılım ölçümü bulunmaz.

### <a name="reserved-instance-billing"></a>Ayrılmış örnek faturalaması

Kurumsal müşteriler için Azure ayrılmış sanal makine örnekleri parasal taahhüt ile satın alınır. Kaydınızda ayrılmış örnek satın alma işlemi için yeterli parasal taahhüt bakiyesi varsa ilgili tutar parasal taahhüt bakiyenizden düşer ve satın alma işlemine ait bir fatura almazsınız.

Azure EA müşterilerinin parasal taahhüdünü tükettiği durumlarda da ayrılmış örnek satın alınabilir ve bu satın alma işlemleri bir sonraki fazla kullanım faturanıza dahil edilir. Varsa ayrılmış örnek fazla kullanımı, normal fazla kullanım faturanıza eklenir.

### <a name="reserved-instance-expiration"></a>Ayrılmış örnek süre sonu

Ayırma süresinin bitimine 30 gün kala ve son gün e-posta bildirimi gönderilir. Ayırma süresi sona erdiğinde, dağıtılan VM’ler çalışmaya devam eder ve kullandıkça öde ücreti üzerinden faturalandırılır. Daha fazla bilgi için bkz. [Ayrılmış Sanal Makine Örnekleri teklifi](https://azure.microsoft.com/pricing/reserved-vm-instances/).

## <a name="next-steps"></a>Sonraki adımlar
- Azure rezervasyonları hakkında daha fazla bilgi için bkz. [Azure Rezervasyonlar nedir?](billing-save-compute-costs-reservations.md)
- Kurumsal rezervasyon maliyetleri ve kullanımı hakkında daha fazla bilgi edinmek için bkz. [Kurumsal Anlaşma rezervasyon maliyetleri ve kullanım bilgilerini alma](billing-understand-reserved-instance-usage-ea.md).
- Fiyatlandırma hakkında bilgi için bkz. [Linux Sanal Makineleri Fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) veya [Windows Sanal Makineleri Fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).
