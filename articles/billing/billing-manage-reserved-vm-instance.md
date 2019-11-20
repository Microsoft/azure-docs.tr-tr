---
title: Azure Ayrılmış Sanal Makine Örnekleri’ni yönetme
description: Azure Ayrılmış Sanal Makine Örnekleri’ni nasıl yönetebileceğinizi öğrenin.
ms.service: billing
author: bandersmsft
manager: yashesvi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2019
ms.author: banders
ms.openlocfilehash: b161fc7cd4faa75dd87613c297c12f1edd862510
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "68840009"
---
# <a name="manage-reservations-for-azure-resources"></a>Azure kaynakları için Rezervasyonları yönetme

Azure rezervasyonu satın aldıktan sonra rezervasyonu farklı bir aboneliğe uygulamanız, rezervasyonu yönetebilecek kullanıcıları değiştirmeniz veya rezervasyon kapsamını düzenlemeniz gerekebilir. Ayrıca bir rezervasyonu iki rezervasyona ayırarak satın aldığınız örneklerin bazılarını başka bir aboneliğe uygulayabilirsiniz.

Azure Ayrılmış Sanal Makine Örnekleri satın aldıysanız rezervasyon için iyileştirme ayarını değiştirebilirsiniz. Rezervasyon indirimi aynı serideki VM'lere uygulanabilir veya veri merkezi kapasitesini belirli bir VM boyutu için ayırabilirsiniz. Ayrıca tamamen kullanılmalarını sağlamak için rezervasyonları iyileştirmeyi denemelisiniz.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>Rezervasyon Siparişi ve Rezervasyon

Rezervasyon satın aldığınızda iki nesne oluşturulur: **Rezervasyon Siparişi** ve **Rezervasyon**.

Satın alma sırasında Rezervasyon Siparişinin altında tek bir Rezervasyon vardır. Bölme, birleştirme, kısmi para iadesi veya değiştirme gibi eylemler **Rezervasyon Siparişi**'nin altında yeni rezervasyonlar oluşturur.

Rezervasyon Siparişini görüntülemek için **Rezervasyonlar**'a gidin > rezervasyonu seçin ve ardından **Rezervasyon sipariş kimliği**'ne tıklayın.

![Rezervasyon sipariş kimliğinin gösterildiği rezervasyon sipariş ayrıntıları örneği ](./media/billing-manage-reserved-vm-instance/reservation-order-details.png)

Rezervasyonlar kendi rezervasyon siparişlerinin izinlerini devralır.

## <a name="change-the-reservation-scope"></a>Rezervasyon kapsamını değiştirme

 Rezervasyon indiriminiz sanal makinelere, SQL veritabanlarına, Azure Cosmos DB'ye veya rezervasyonunuzla eşleşen ve rezervasyon kapsamında çalıştırılan diğer kaynaklara uygulanır. Faturalama bağlamı, rezervasyonu satın almak için kullanılan aboneliğe bağlıdır.

Rezervasyon kapsamını güncelleştirmek için:

1. [Azure Portal](https://portal.azure.com) oturum açın.
2. **Tüm hizmetler** > **Rezervasyonlar**’ı seçin.
3. Rezervasyonu seçin.
4. **Ayarlar** > **Yapılandırma**'yı seçin.
5. Kapsamı değiştirin.

Paylaşılan kapsamdan tek kapsama geçerseniz yalnızca sahibi olduğunuz rezervasyon abonelikleri seçebilirsiniz. Yalnızca rezervasyonla aynı fatura bağlamında bulunan abonelikleri seçebilirsiniz.

Kapsam yalnızca kullandıkça öde fiyatının geçerliği olduğu tek tek abonelikler (MS-AZR-0003P veya MS-AZR-0023P teklifi), MS-AZR-0017P veya MS-AZR-0148P kodlu Kurumsal teklif ya da CSP abonelikleri için geçerlidir.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Rezervasyonu yönetebilecek kullanıcıları ekleme veya değiştirme

Rezervasyon siparişindeki veya rezervasyondaki rollere kullanıcı ekleyerek bir rezervasyonun yönetimi için temsilciler belirleyebilirsiniz. Varsayılan olarak rezervasyonu sipariş eden kişi ve hesap yöneticisi, rezervasyon siparişinde ve rezervasyonda Sahip rolüne atanır.

Rezervasyon siparişlerine ve rezervasyonlara erişimi, rezervasyon indirimini alan aboneliklerden bağımsız olarak yönetebilirsiniz. Birine rezervasyon siparişini veya rezervasyonu yönetme izinleri verdiğinizde, ona aboneliği yönetme izni vermiş olmazsınız. Benzer biçimde birine rezervasyon kapsamında aboneliği yönetme izinleri verirseniz, o kişi rezervasyon siparişini veya rezervasyonu yönetme hakları almış olmaz.

Değiştirme veya para iadesi yapmak için kullanıcının rezervasyon siparişine erişimi olmalıdır. Birine izinler verirken, rezervasyona değer rezervasyon siparişine izinler vermek daha iyi olabilir.


Rezervasyona yönetim erişimi temsilcisi atamak için:

1. [Azure Portal](https://portal.azure.com) oturum açın.
2. **Tüm Hizmetler** > **Rezervasyon**'u seçerek erişim sahibi olduğunuz rezervasyonları listeleyin.
3. Diğer kullanıcılara erişim vermek istediğiniz rezervasyonu seçin.
4. **Erişim denetimi (IAM)** öğesini seçin.
5. **Rol ataması ekle** > **Rol** > **Sahip**'i seçin. Sınırlı erişim vermek istiyorsanız farklı bir rol seçebilirsiniz.
6. Sahip olarak eklemek istediğiniz kullanıcının e-posta adresini yazın.
7. Kullanıcıyı ve ardından **Kaydet**'i seçin.

## <a name="split-a-single-reservation-into-two-reservations"></a>Tek rezervasyonu iki rezervasyona bölme

 Bir rezervasyon içinde birden fazla kaynak örneği satın aldıktan sonra o rezervasyon içindeki örnekleri farklı aboneliklere atamak isteyebilirsiniz. Varsayılan olarak tüm örneklerin kapsamı (tek abonelik veya paylaşılan) vardır. On rezervasyon örneği satın aldığınızı ve kapsam olarak A aboneliğini belirttiğinizi varsayalım. Şimdi yedi rezervasyonun kapsamı A aboneliği ve kalan üçünün B aboneliği olacak şekilde değişiklik yapmak istiyor olabilirsiniz. Rezervasyonun bölünmesi ayrıntılı kapsam yönetimi için örnekleri dağıtabilmenizi sağlar. Paylaşılan kapsamı seçerek abonelikleri ayırma işlemini basitleştirebilirsiniz. Ama maliyet yönetimi ve bütçeleme amacıyla, miktarları belirli aboneliklere ayırabilirsiniz.

 Rezervasyonu PowerShell, CLI veya API aracılığıyla iki rezervasyona bölebilirsiniz.

### <a name="split-a-reservation-by-using-powershell"></a>PowerShell kullanarak rezervasyonu bölme

1. Aşağıdaki komutu çalıştırarak rezervasyon sipariş kimliğini alın:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. Rezervasyonun ayrıntılarını alın:

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Rezervasyonu ikiye bölün ve örnekleri dağıtın:

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. Aşağıdaki komutu çalıştırarak kapsamı güncelleştirebilirsiniz:

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancel-exchange-or-refund-reservations"></a>Rezervasyonları iptal etme, değiştirme veya para iadesi alma

Belirli sınırlamalarla rezervasyonları iptal edebilir, değiştirebilir veya para iadesi alabilirsiniz. Daha fazla bilgi için bkz. [Azure Ayrılmış Sanal Makine Örnekleri için self servis değişimler ve para iadeleri](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Ayrılmış VM Örnekleri için iyileştirme ayarını değiştirme

 Ayrılmış VM Örneği satın aldığınızda örnek boyutu esnekliğini veya kapasite önceliğini seçersiniz. VM örneği esnekliği aynı [VM boyutu grubundaki](https://aka.ms/RIVMGroups) diğer VM'lere rezervasyon indirimini uygular. Kapasite önceliği dağıtımlarınızda veri merkezi kapasitesine öncelik verir. Bu seçenek ihtiyacınız olduğunda VM örneklerini başlatabileceğinize daha fazla güvenmenizi sağlar.

Varsayılan olarak rezervasyonun kapsamı paylaşılan olduğunda örnek boyutu esnekliği açıktır. VM dağıtımlarında veri merkezi kapasitesine öncelik verilmez.

Kapsamın tek olduğu rezervasyonlarda, VM örnek boyutu esnekliği yerine rezervasyonu kapasite önceliği için iyileştirebilirsiniz.

Rezervasyonun iyileştirme ayarını güncelleştirmek için:

1. [Azure Portal](https://portal.azure.com) oturum açın.
2. **Tüm Hizmetler** > **Rezervasyonlar**'ı seçin.
3. Rezervasyonu seçin.
4. **Ayarlar** > **Yapılandırma**'yı seçin.
5. **En iyi duruma getir** ayarını değiştirin.

## <a name="optimize-reservation-use"></a>Rezervasyon kullanımını iyileştirme

Azure rezervasyon tasarrufları ancak sürekli kaynak kullanımında elde edilir. Rezervasyon satın aldığınızda bir veya üç yıllık bir dönemde temelde %100 olası kaynak kullanımı için önceden ödeme yaparsınız. Mümkün olan en iyi kullanımı ve tasarrufu elde etmek için rezervasyonunuzu maksimum düzeye çıkarmaya çalışın. Aşağıdaki bölümlerde rezervasyonu izleme ve kullanımını iyileştirme işlemleri açıklanır.

### <a name="view-reservation-use-in-the-azure-portal"></a>Azure portalında rezervasyon kullanımını görüntüleme

Rezervasyon kullanımını görüntülemenin bir yolu bunu Azure portalında yapmaktır.

1. [Azure Portal](https://portal.azure.com/) oturum açın.
2. **Tüm hizmetler** > [**Rezervasyonlar**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)'ı seçin ve rezervasyonu **Kullanım (%)** değerini not alın.  
  ![Rezervasyon listesini gösteren resim](./media/billing-manage-reserved-vm-instance/reservation-list.png)
3. Bir rezervasyon seçin.
4. Zaman içinde rezervasyonun kullanım eğilimini gözden geçirin.  
  ![Rezervasyon kullanımını gösteren resim ](./media/billing-manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>API'yle rezervasyon kullanımını görüntüleme

Kurumsal Anlaşma (EA) müşterisiyseniz, kuruluşunuzda rezervasyonların nasıl kullanıldığını program aracılığıyla görüntüleyebilirsiniz. Kullanım verileri aracılığıyla kullanılmayan rezervasyon bilgisini elde edersiniz. Rezervasyon ücretlerini gözden geçirirken verilerin gerçek maliyet ve amorti edilmiş maliyet olarak bölündüğüne dikkat edin. Gerçek maliyet aylık faturanızda mutabakat sağlamak için gereken verileri sağlar. Ayrıca rezervasyon satın alma maliyetleri ve rezervasyon uygulama ayrıntıları vardır. Amorti edilmiş maliyet gerçek maliyet gibidir; tek farkı rezervasyon kullanımı için etkin fiyatın eşit dağıtılmış olmasıdır. Kullanılmayan rezervasyon saatleri amorti edilmiş maliyet verilerinde gösterilir. EA müşterilerinin kullanım verileri hakkında daha fazla bilgi için bkz. [Kurumsal Anlaşma rezervasyon maliyetleri ve kullanım bilgilerini alma](billing-understand-reserved-instance-usage-ea.md).

Diğer abonelik türleri için [Rezervasyon Özetleri - Rezervasyon Siparişine ve Rezervasyona Göre Listeleme](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation) API’sini kullanın.

### <a name="optimize-your-reservation"></a>Rezervasyonunuzu iyileştirme

Kuruluşunuzun rezervasyonlarının gerektiği şekilde kullanılmadığını belirlerseniz:

- Kuruluşunuzun oluşturduğu sanal makinelerin, rezervasyonun sanal makine boyutuyla aynı olduğundan emin olun.
- Örnek boyutu esnekliğinin açık olduğundan emin olun. Daha fazla bilgi için bkz [Rezervasyonları yönetme - Ayrılmış Sanal Makine Örnekleri için iyileştirme ayarını değiştirme](#change-optimize-setting-for-reserved-vm-instances).
- Rezervasyon kapsamını, daha geniş çapta geçerli olması için _paylaşılan_ olarak değiştirin. Daha fazla bilgi için bkz. [Rezervasyonun kapsamını değiştirme](#change-the-reservation-scope).
- Kullanılmayan miktarı değiştokuş etmeyi göz önünde bulundurun. Daha fazla bilgi için bkz. [İptaller ve değişimler](#cancel-exchange-or-refund-reservations).


## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bize ulaşın.

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar

Azure Ayrılmış Sanal Makine Örnekleri hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Azure rezervasyonları nedir?](billing-save-compute-costs-reservations.md)

Hizmet planı satın alma:
- [Azure Ayrılmış VM Örnekleri ile Sanal Makinelere ön ödeme yapma](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure SQL Veritabanı ayrılmış kapasitesi ile SQL Veritabanı işlem kaynakları için ön ödeme yapma](../sql-database/sql-database-reserved-capacity.md)
- [Azure Cosmos DB ayrılmış kapasitesiyle Azure Cosmos DB kaynakları için önceden ödeme yapma](../cosmos-db/cosmos-db-reserved-capacity.md)

Yazılım planı satın alma:
- [Azure Ayırmaları ile Red Hat yazılım planları için ön ödeme yapma](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Azure Ayırmaları ile SUSE yazılım planları için ön ödeme yapma](../virtual-machines/linux/prepay-suse-software-charges.md)

İndirimi ve kullanımı anlama:
- [Sanal makine rezervasyon indiriminin nasıl uygulandığını anlama](billing-understand-vm-reservation-charges.md)
- [Red Hat Enterprise Linux yazılım planı indiriminin nasıl uygulandığını anlama](../billing/billing-understand-rhel-reservation-charges.md)
- [SUSE Linux Enterprise yazılım planı indiriminin nasıl uygulandığını anlama](../billing/billing-understand-suse-reservation-charges.md)
- [Diğer rezervasyon indirimlerinin nasıl uygulandığını anlama](billing-understand-reservation-charges.md)
- [Kullandıkça Öde aboneliğiniz için rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage.md)
- [Kurumsal kaydınız için rezervasyon kullanımını anlama](billing-understand-reserved-instance-usage-ea.md)
- [Rezervasyonlara dahil olmayan Windows yazılımı maliyetleri](billing-reserved-instance-windows-software-costs.md)
