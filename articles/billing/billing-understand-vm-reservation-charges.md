---
title: Azure Ayrılmış VM Örnekleri indirimini anlama | Microsoft Docs
description: Azure Ayrılış VM Örneği indiriminin çalıştırılan sanal makinelere nasıl uygulandığını öğrenin.
author: yashesvi
manager: yashar
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 543cf81cdb9dad2b0f16a29c85accab820c82ec1
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719580"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Azure rezervasyon indirimini sanal makinelere uygulama

Azure Ayrılmış Sanal Makine Örneği satın almanızın ardından, rezervasyonun öznitelikleriyle ve miktarıyla eşleşen sanal makinelere otomatik olarak rezervasyon indirimi uygulanır. Rezervasyon, sanal makinelerinizin işlem maliyetlerini kapsar.

Rezervasyon indirimi Azure Market'ten satın aldığınız temel VM'lere uygulanır.

SQL Veritabanı ayrılmış kapasitesi için bkz. [Azure Ayrılmış Örnekleri indirimini anlama](billing-understand-reservation-charges.md).

Aşağıdaki tabloda, Ayrılmış VM Örneği satın aldıktan sonra sanal makinenizin maliyetleri gösterilir. Her durumda depolama ve ağ iletişimi için normal fiyatlarla ücretlendirilirsiniz.

| Sanal Makine Türü  | Ayrılmış VM Örneği ile ücretler |
|-----------------------|--------------------------------------------|
|Ek yazılım içermeyen Linux VM'leri | Rezervasyon VM altyapı maliyetlerinizi kapsar.|
|Yazılım değişiklikleri içeren Linux VM'leri (örneğin Red Hat) | Rezervasyon altyapı maliyetlerini kapsar. Ek yazılım için ücretlendirilirsiniz.|
|Ek yazılım içermeyen Windows VM'leri |Rezervasyon altyapı maliyetlerini kapsar. Windows yazılımı için ücretlendirilirsiniz.|
|Ek yazılım içeren Windows VM'leri (örneğin SQL server) | Rezervasyon altyapı maliyetlerini kapsar. Windows yazılımı ve ek yazılım için ücretlendirilirsiniz.|
|[Azure Hibrit Avantajı](../virtual-machines/windows/hybrid-use-benefit-licensing.md) ile Windows VM'leri | Rezervasyon altyapı maliyetlerini kapsar. Windows yazılım maliyetleri Azure Hibrit Avantajının kapsamına girer. Ek yazılımlar ayrı ücretlendirilir.|

## <a name="how-reservation-discount-is-applied"></a>Rezervasyon indiriminin uygulanması

Rezervasyon indirimi “*kullanılmadığı takdirde hakkı kaybedilen*” bir özelliktir. Bu nedenle, herhangi bir saat için eşleşen kaynaklarınız yoksa, o saate ait rezervasyon miktarını kaybedersiniz. Kullanılmayan ayrılmış saatleri devredemezsiniz.

Bir kaynağı kapattığınızda rezervasyon indirimi, belirtilen kapsamdaki başka bir eşleşen kaynağa otomatik olarak uygulanır. Belirtilen kapsamda eşleşen kaynak bulunamazsa, ayrılan saatler *kaybedilir*.

## <a name="reservation-discount-for-non-windows-vms"></a>Windows dışı VM'ler için rezervasyon indirimi

 Azure rezervasyon indirimi çalıştırılan VM örneklerine saat bazında uygulanır. Rezervasyon indiriminin uygulanması için, satın aldığınız rezervasyonlar çalıştırılan VM'lerin gösterdiği kullanımla eşleştirilir. Tam bir saat çalıştırılmayabilen VM'ler için rezervasyon eş zamanlı çalıştırılan VM'ler de dahil olmak üzere rezervasyonu kullanmayan diğer VM'lerden doldurulur. Saatin sonunda, VM'ler için o saatin rezervasyon uygulaması kilitlenir. VM'nin bir saat çalışmaması veya eş zamanlı VM'lerin saati doldurmaması durumunda, o saat boyunca rezervasyon kapasitesinin altında kullanılmış olur. Aşağıdaki grafikte rezervasyonun faturalanabilir VM kullanımına uygulanması gösterilir. Çizimde bir rezervasyon satın alımı ve iki eşleşen VM örneği temel alınmıştır.

![Uygulanan bir rezervasyonun ve eşleşen iki VM örneğinin ekran görüntüsü](./media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. Rezervasyon çizgisinin üstündeki kullanımlar normal kullandıkça öde fiyatlarından ücretlendirilir. Rezervasyon çizgisinin altındaki kullanımlardan ücretlendirilmezsiniz çünkü rezervasyon alışverişi kapsamında zaten ödenmiştir.
2. 1\. saatte 1. örnek 0,75 saat ve 2. örnek 0,5 saat çalışıyor. 1\. saat için toplam kullanım 1,25 saattir. Artan 0,25 saat için kullandıkça öde fiyatlarından ücretlendirilirsiniz.
3. 2\. saat ve 3. saat için her iki örnek de 1 saat çalışıyor. Örneklerden biri rezervasyonun kapsamına girer ve diğeri kullandıkça öde fiyatlarından ücretlendirilir.
4. 4\. saatte 1. örnek 0,5 saat ve 2. örnek 1 saat çalışıyor. 1\. örnek tümüyle rezervasyon kapsamındadır ve 2. örneğin 0,5 saatlik kısmı kapsama gider. Artan 0,5 saat için kullandıkça öde fiyatından ücretlendirilirsiniz.

Faturalama kullanım raporlarında Azure rezervasyonlarınızın uygulanmasını anlamak ve görüntülemek için bkz. [Rezervasyon kullanımınızı anlama](billing-understand-reserved-instance-usage-ea.md).

## <a name="reservation-discount-for-windows-vms"></a>Windows VM'leri için rezervasyon indirimi

Windows VM örneklerini çalıştırırken altyapı maliyetlerini karşılamak için rezervasyon uygulanır. Windows VM'leri için rezervasyonun VM altyapı maliyetlerine uygulanması, Windows dışı VM'lerle aynı şekildedir. Windows yazılımı için vCPU başına ayrı ücretlendirilirsiniz. Bkz. [Rezervasyonlarla Windows yazılım maliyetleri](billing-reserved-Instance-windows-software-costs.md). Windows lisans maliyetlerinizi [Windows Server için Azure Hibrit Avantajı](../virtual-machines/windows/hybrid-use-benefit-licensing.md) ile karşılayabilirsiniz.

## <a name="discount-can-apply-to-different-sizes"></a>Farklı boyutlara indirim uygulanabilir

Ayrılmış VM Örneği satın alırken **En iyi duruma getir**: **örnek boyutu esnekliği**'ni seçerseniz, indirim kapsamı seçtiğiniz VM boyutuna bağlı olur. Rezervasyon aynı boyut serisi grubundaki sanal makine (VM) boyutlarına uygulanır. Daha fazla bilgi için bkz. [Ayrılmış VM Örnekleriyle sanal makine boyutu esnekliği](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

## <a name="discount-applies-to-matching-servicetype-only"></a>Yalnızca eşleşen ServiceType için indirim uygulanır

Rezervasyon indirimi yalnızca `AdditionalInfo` alanındaki `ServiceType` değerinin satın alınan rezervasyonla eşleştiği VM kullanımına uygulanır. Rezervasyon indirimi uygulamasında VM'ler için kullanılan ölçüm yoksayılır ve yalnızca `ServiceType` değerlendirilir. VM'yi hangi hizmet türü için satın aldığınızı bilmelisiniz. Premium olmayan depolama VM rezervasyonunu premium depolama rezervasyonuyla (veya tersine) değiştirebilirsiniz.

## <a name="services-that-get-vm-reservation-discounts"></a>VM rezervasyon indirimlerini alan hizmetler

VM rezervasyonlarınız yalnızca VM dağıtımlarınıza değil çeşitli hizmetlerden çıkan VM kullanımına da uygulanabilir. Rezervasyon indirimi alan kaynaklar örnek boyutu esnekliği ayarına bağlı olarak değişir.

### <a name="instance-size-flexibility-setting"></a>Örnek boyut esnekliği ayarı

Örnek boyutu esnekliği ayarı, ayrılmış örnek indirimlerini hangi hizmetlerin alacağını belirler.

*ConsumedService* `Microsoft.Compute` olduğunda, ayarın açık veya kapalı olmasına bakılmaksızın eşleşen her VM kullanımına rezervasyon indirimleri uygulanır. Dolayısıyla kullanım verilerinizde *ConsumedService* değerini gözden geçirin. Bazı örnekler:

- Sanal makineler
- Sanal makine ölçek kümeleri
- Kapsayıcı hizmeti
- Azure Batch dağıtımları (kullanıcı abonelikleri modunda)
- Azure Kubernetes Hizmeti (AKS)
- Service Fabric

Ayar açık olduğunda, eşleşen her VM kullanımına rezervasyon indirimlerinin uygulanması için *ConsumedService* aşağıdaki öğelerden biri olmalıdır:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Kullanımın rezervasyon indirimlerine uygun olup olmadığını saptamak için kullanım verilerinizde *ConsumedService* değerini gözden geçirin.

Örnek boyutu esnekliği hakkında daha fazla bilgi için bkz. [Ayrılmış VM Örnekleriyle sanal makine boyutu esnekliği](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).


## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bizimle iletişim kurun

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar

Azure Ayrılmış Sanal Makine Örnekleri hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Azure rezervasyonları nedir?](billing-save-compute-costs-reservations.md)
- [Azure Ayrılmış VM Örnekleri ile Sanal Makinelere ön ödeme yapma](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure SQL Veritabanı ayrılmış kapasitesi ile SQL Veritabanı işlem kaynakları için ön ödeme yapma](../sql-database/sql-database-reserved-capacity.md)
- [Azure rezervasyonlarını yönetme](billing-manage-reserved-vm-instance.md)
- [Kullandıkça Öde aboneliğiniz için rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage.md)
- [Kurumsal kaydınız için rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage-ea.md)
- [CSP abonelikleri için rezervasyon kullanımını anlama](/partner-center/azure-reservations)
- [Rezervasyonlara dahil olmayan Windows yazılımı maliyetleri](billing-reserved-instance-windows-software-costs.md)
