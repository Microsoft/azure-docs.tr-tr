---
title: Azure Ayrılmış Sanal Makine Örnekleri’ni yönetme
description: Azure Rezervasyonlarını yönetmeyi öğrenin. Rezervasyon kapsamını değiştirme, rezervasyonu bölme ve rezervasyon kullanımını iyileştirme adımlarına bakın.
ms.service: cost-management-billing
ms.subservice: reservations
author: bandersmsft
ms.reviewer: yashesvi
ms.topic: how-to
ms.date: 04/21/2021
ms.author: banders
ms.openlocfilehash: e8a5e6d40b745efb7fcd50919cd27664b3406b7d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862858"
---
# <a name="manage-reservations-for-azure-resources"></a>Azure kaynakları için Rezervasyonları yönetme

Azure rezervasyonu satın aldıktan sonra rezervasyonu farklı bir aboneliğe uygulamanız, rezervasyonu yönetebilecek kullanıcıları değiştirmeniz veya rezervasyon kapsamını düzenlemeniz gerekebilir. Ayrıca bir rezervasyonu iki rezervasyona ayırarak satın aldığınız örneklerin bazılarını başka bir aboneliğe uygulayabilirsiniz.

Azure Ayrılmış Sanal Makine Örnekleri satın aldıysanız rezervasyon için iyileştirme ayarını değiştirebilirsiniz. Rezervasyon indirimi aynı serideki VM'lere uygulanabilir veya veri merkezi kapasitesini belirli bir VM boyutu için ayırabilirsiniz. Rezervasyonları tam olarak kullanılacak şekilde iyileştirmeyi denemelisiniz.

*Bir rezervasyonu yönetmek için gereken izin, abonelik izninden farklıdır.*

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>Rezervasyon Siparişi ve Rezervasyon

Rezervasyon satın aldığınızda iki nesne oluşturulur: **Rezervasyon Siparişi** ve **Rezervasyon**.

Satın alma sırasında Rezervasyon Siparişinin altında tek bir Rezervasyon vardır. Bölme, birleştirme, kısmi para iadesi veya değiştirme gibi eylemler **Rezervasyon Siparişi**'nin altında yeni rezervasyonlar oluşturur.

Bir Rezervasyon Siparişini görüntülemek için, **Rezervasyonlar**’a gidip rezervasyonu belirleyin ve ardından **Rezervasyon siparişi kimliğini** seçin.

![Rezervasyon sipariş kimliğinin gösterildiği rezervasyon sipariş ayrıntıları örneği ](./media/manage-reserved-vm-instance/reservation-order-details.png)

Rezervasyonlar kendi rezervasyon siparişlerinin izinlerini devralır. Bir ayırmayı Exchange veya para iadesi için Kullanıcı rezervasyon emrine eklenmelidir.

## <a name="change-the-reservation-scope"></a>Rezervasyon kapsamını değiştirme

 Rezervasyon indiriminiz sanal makinelere, SQL veritabanlarına, Azure Cosmos DB'ye veya rezervasyonunuzla eşleşen ve rezervasyon kapsamında çalıştırılan diğer kaynaklara uygulanır. Faturalama bağlamı, rezervasyonu satın almak için kullanılan aboneliğe bağlıdır.

Rezervasyon kapsamını güncelleştirmek için:

1. [Azure Portal](https://portal.azure.com) oturum açın.
2. **Tüm hizmetler** > **Rezervasyonlar**’ı seçin.
3. Rezervasyonu seçin.
4. **Ayarlar** > **Yapılandırma**'yı seçin.
5. Kapsamı değiştirin.

Paylaşılan kapsamdan tek kapsama geçerseniz yalnızca sahibi olduğunuz rezervasyon abonelikleri seçebilirsiniz. Yalnızca rezervasyonla aynı faturalandırma bağlamındaki abonelikler seçilebilir.

Kapsam yalnızca kullandıkça öde fiyatının geçerliği olduğu tek tek abonelikler (MS-AZR-0003P veya MS-AZR-0023P teklifi), MS-AZR-0017P veya MS-AZR-0148P kodlu Kurumsal teklif ya da CSP abonelikleri için geçerlidir.

## <a name="who-can-manage-a-reservation-by-default"></a>Bir rezervasyonu varsayılan olarak kim yönetebilir?

Varsayılan olarak, aşağıdaki kullanıcılar rezervasyonları görüntüleyebilir ve yönetebilir:

- Faturalandırma aboneliğine ait ayırmayı ve hesap sahibini satın alan kişi, rezervasyon siparişine Azure RBAC erişimi alır.
-  Kurumsal Anlaşma ve Microsoft müşteri anlaşması faturalandırma katılımcıları, maliyet yönetimi + faturalandırma > rezervasyon işlemlerinden tüm rezervasyonları yönetebilir > mavi başlığı seçin.

Diğer kişilerin rezervasyonları yönetmesine izin vermek için iki seçeneğiniz vardır:

- Bireysel bir rezervasyon siparişi için erişim yönetimi temsilcisi atama:
    1. [Azure Portal](https://portal.azure.com) oturum açın.
    1. **Tüm Hizmetler** > **Rezervasyon**'u seçerek erişim sahibi olduğunuz rezervasyonları listeleyin.
    1. Diğer kullanıcılara erişim vermek istediğiniz rezervasyonu seçin.
    1. Rezervasyon ayrıntılarından rezervasyon siparişini seçin.
    1. **Erişim denetimi (IAM)** öğesini seçin.
    1. **Rol ataması ekle** > **Rol** > **Sahip**'i seçin. Sınırlı erişim vermek istiyorsanız farklı bir rol seçin.
    1. Sahip olarak eklemek istediğiniz kullanıcının e-posta adresini yazın.
    1. Kullanıcıyı ve ardından **Kaydet**'i seçin.

- Bir Kullanıcıyı bir Kurumsal Anlaşma veya bir Microsoft Müşteri Anlaşmasına faturalandırma yöneticisi olarak ekleme:
    - Kurumsal Anlaşma için, Kurumsal Anlaşma uygulanan tüm rezervasyon siparişlerini görüntülemek ve yönetmek üzere _Kuruluş Yöneticisi_  rolüne sahip kullanıcıları ekleyin. _Kuruluş Yöneticisi (salt okuma) rolü olan kullanıcılar_  yalnızca rezervasyonu görüntüleyebilir. Bölüm yöneticileri ve hesap sahipleri, erişim denetimi (IAM) kullanılarak bunlara açıkça eklenmedikleri _takdirde ayırmaları_ görüntüleyemez. Daha fazla bilgi için bkz. [Azure Enterprise rollerini yönetme](../manage/understand-ea-roles.md).

        _Kurumsal Yöneticiler, bir rezervasyon siparişinin sahipliğini alabilir ve erişim denetimi (IAM) kullanarak bir rezervasyona başka kullanıcılar ekleyebilirler._
    - Bir Microsoft Müşteri Anlaşması için, faturalandırma profili sahibi veya faturalandırma profili katılımcısı rolüne sahip kullanıcılar, faturalandırma profilini kullanarak yapılan tüm rezervasyon satın almalarını yönetebilir. Faturalandırma profili okuyucuları ve fatura yöneticileri, faturalandırma profiliyle ilgili ödenen tüm rezervasyonları görüntüleyebilir. Ancak, rezervasyonlar üzerinde değişiklik yapamaz.
    Daha fazla bilgi için bkz. [Faturalandırma profili rolleri ve görevleri](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="how-billing-administrators-view-or-manage-reservations"></a>Faturalandırma yöneticileri rezervasyonları nasıl görüntüler veya yönetir

1. **Maliyet Yönetimi + Faturalandırma** sayfasına gidin ve ardından sayfanın sol tarafında **Rezervasyon İşlemleri**’ni seçin.
2. Gerekli faturalandırma izinlerine sahipseniz, rezervasyonları görüntüleyebilir ve yönetebilirsiniz. Herhangi bir rezervasyon görmüyorsanız, rezervasyonların oluşturulduğu Azure AD kiracısı ile oturum açtığınızdan emin olun.

## <a name="split-a-single-reservation-into-two-reservations"></a>Tek rezervasyonu iki rezervasyona bölme

 Bir rezervasyon içinde birden fazla kaynak örneği satın aldıktan sonra o rezervasyon içindeki örnekleri farklı aboneliklere atamak isteyebilirsiniz. Varsayılan olarak tüm örneklerin kapsamı (tek abonelik, kaynak grubu veya paylaşılan) vardır. 10 VM örneği için rezervasyon satın aldığınızı ve kapsam olarak A aboneliğini belirttiğinizi varsayalım. Yedi VM örneğinin kapsamını A aboneliği ve kalan üçünün B aboneliği olacak şekilde değiştirmek istediğinizi düşünelim. Rezervasyonu bölerek bunu yapabilirsiniz. Rezervasyonu böldükten sonra, özgün ReservationID iptal edilir ve iki yeni rezervasyon oluşturulur. Bölme işlemi rezervasyon siparişini etkilemez. Bölünmüş yeni bir ticari işlem yoktur ve yeni rezervasyonlar bölünmüş olan rezervasyonla aynı bitiş tarihine sahiptir.

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

Belirli sınırlamalarla rezervasyonları iptal edebilir, değiştirebilir veya para iadesi alabilirsiniz. Daha fazla bilgi için bkz. [Azure Ayrılmış Sanal Makine Örnekleri için self servis değişimler ve para iadeleri](exchange-and-refund-azure-reservations.md).

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Ayrılmış VM Örnekleri için iyileştirme ayarını değiştirme

 Ayrılmış VM Örneği satın aldığınızda örnek boyutu esnekliğini veya kapasite önceliğini seçersiniz. VM örneği esnekliği aynı [VM boyutu grubundaki](../../virtual-machines/reserved-vm-instance-size-flexibility.md) diğer VM'lere rezervasyon indirimini uygular. Kapasite önceliği, dağıtımlarınız için en önemli veri merkezinin kapasitesini belirler. Bu seçenek ihtiyacınız olduğunda VM örneklerini başlatabileceğinize daha fazla güvenmenizi sağlar.

Varsayılan olarak rezervasyonun kapsamı paylaşılan olduğunda örnek boyutu esnekliği açıktır. VM dağıtımlarında veri merkezi kapasitesine öncelik verilmez.

Kapsamın tek olduğu rezervasyonlarda, VM örnek boyutu esnekliği yerine rezervasyonu kapasite önceliği için iyileştirebilirsiniz.

Rezervasyonun iyileştirme ayarını güncelleştirmek için:

1. [Azure Portal](https://portal.azure.com) oturum açın.
2. **Tüm Hizmetler** > **Rezervasyonlar**'ı seçin.
3. Rezervasyonu seçin.
4. **Ayarlar** > **Yapılandırma**'yı seçin.
  ![Yapılandırma öğesini gösteren örnek](./media/manage-reserved-vm-instance/add-product03.png)
5. **En iyi duruma getir** ayarını değiştirin.
  ![En iyi duruma getir ayarını gösteren örnek](./media/manage-reserved-vm-instance/instance-size-flexibility-option.png)

## <a name="optimize-reservation-use"></a>Rezervasyon kullanımını iyileştirme

Azure rezervasyon tasarrufları ancak sürekli kaynak kullanımında elde edilir. Rezervasyon satın aldığınızda bir veya üç yıllık bir dönemde temelde %100 olası kaynak kullanımı için ödeme yaparsınız. Mümkün olan en iyi kullanımı ve tasarrufu elde etmek için rezervasyonunuzu maksimum düzeye çıkarmaya çalışın. Aşağıdaki bölümlerde rezervasyonu izleme ve kullanımını iyileştirme işlemleri açıklanır.

### <a name="view-reservation-use-in-the-azure-portal"></a>Azure portalında rezervasyon kullanımını görüntüleme

Rezervasyon kullanımını görüntülemenin bir yolu bunu Azure portalında yapmaktır.

1. [Azure Portal](https://portal.azure.com/) oturum açın.
2. **Tüm hizmetler** > [**Rezervasyonlar**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)'ı seçin ve rezervasyonu **Kullanım (%)** değerini not alın.  
  ![Rezervasyon listesini gösteren resim](./media/manage-reserved-vm-instance/reservation-list.png)
3. Bir rezervasyon seçin.
4. Zaman içinde rezervasyonun kullanım eğilimini gözden geçirin.
  ![Rezervasyon kullanımını gösteren resim ](./media/manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>API'yle rezervasyon kullanımını görüntüleme

Kurumsal Anlaşma (EA) müşterisiyseniz, kuruluşunuzda rezervasyonların nasıl kullanıldığını program aracılığıyla görüntüleyebilirsiniz. Kullanım verileri aracılığıyla kullanılmayan rezervasyon bilgisini elde edersiniz. Rezervasyon ücretlerini gözden geçirirken verilerin gerçek maliyet ve amorti edilmiş maliyet olarak bölündüğüne dikkat edin. Gerçek maliyet aylık faturanızda mutabakat sağlamak için gereken verileri sağlar. Ayrıca rezervasyon satın alma maliyetleri ve rezervasyon uygulama ayrıntıları vardır. Amorti edilmiş maliyet gerçek maliyet gibidir; tek farkı rezervasyon kullanımı için etkin fiyatın eşit dağıtılmış olmasıdır. Kullanılmayan rezervasyon saatleri amorti edilmiş maliyet verilerinde gösterilir. EA müşterilerinin kullanım verileri hakkında daha fazla bilgi için bkz. [Kurumsal Anlaşma rezervasyon maliyetleri ve kullanım bilgilerini alma](understand-reserved-instance-usage-ea.md).

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
 - [Ayırma kullanımını görüntüle](reservation-utilization.md)
 - [Exchange ve iadesi](exchange-and-refund-azure-reservations.md)
 - [Rezervasyonları yenileme](reservation-renew.md)
 - [Kiracılar arasında aktarımlar](troubleshoot-reservation-transfers-between-tenants.md)
 - [Azure günlüklerinden bir rezervasyon Satınalmacı bulma](find-reservation-purchaser-from-logs.md)
 - [Rezervasyonu yenileme](reservation-renew.md)
