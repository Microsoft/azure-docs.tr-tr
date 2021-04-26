---
title: Microsoft ticari Market 'ten lider yönetimi
description: Microsoft AppSource ve Azure Market teklifinizden müşteri adaylarını oluşturma ve alma hakkında bilgi edinin
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 10/01/2020
ms.openlocfilehash: 1d5f2248d94796d5e3ee76301642a95abddebfe4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94489344"
---
# <a name="customer-leads-from-your-commercial-marketplace-offer"></a>Ticari market teklifinizden elde edilen potansiyel müşteriler

Müşteri adayları, [Microsoft AppSource](https://appsource.microsoft.com) ve [Azure Marketi](https://azuremarketplace.microsoft.com)'nden tekliflerinizi ilgilenen veya dağıtmaktan müşteriler. Teklifiniz ticari Market 'e yayımlandıktan sonra müşteri adaylarını alabilirsiniz. Bu makalede aşağıdaki müşteri adayı yönetim kavramları açıklanmaktadır:

* Ticari Market teklifinizin, iş fırsatlarını kaçırmadığınızdan emin olmak için müşteri adaylarını nasıl üretdirebileceği. 
* Müşteri ilişkileri yönetimi (CRM) sisteminizi teklifinize bağlamak, böylelikle müşteri adaylarını tek bir merkezi konumda yönetebilmenizi sağlar.
* Size ulaşan müşterileri takip edebilmeniz için size gönderdiğimiz müşteri adayı verileri.

## <a name="generate-customer-leads"></a>Müşteri adayları oluşturma

Bir müşteri adayının oluşturulduğu yer şunlardır:

- Müşteri, ticari Market 'ten **benimle Iletişim kurun** ' i seçtikten sonra bilgilerini paylaşma. Bu müşteri adayı, bir *ilk faiz* lideri. Ürününüzü alma konusunda ilgilendiğiniz müşteri hakkındaki bilgileri sizinle paylaşıyoruz. Müşteri adayı, Alım huninin en üstü.

    ![Dynamics 365 benimle Iletişim kurun](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- Müşteri, teklifinizi almak için **Şimdi al** (veya [Azure Portal](https://portal.azure.com/) **Oluştur** ' A seçer) öğesini seçer. Bu müşteri adayı *etkin* bir müşteri adaydır. Ürününüzü dağıtmayı Başlatan müşteriyle ilgili bilgileri sizinle paylaşıyoruz.

    ![SQL Şimdi Al düğmesi](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Windows Server oluştur düğmesi](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- Müşteri, teklifinizi denemek için **test sürücüsü** veya **ücretsiz deneme sürümünü** seçer. Sınama sürücüleri veya ücretsiz denemeler, herhangi bir giriş konusunda herhangi bir engelleri olmadan işletmenizin potansiyel müşterilerle anında paylaşılması için hızlandırılmış fırsatlardır.

    ![Dynamics 365 test sürücüsü düğmesi](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dynamics 365 ücretsiz deneme düğmesi](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>CRM sisteminize bağlanma

[!INCLUDE [Links to lead configuration for different CRM systems](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Müşteri adayı verilerini anlama

Müşteri alma işlemi sırasında aldığınız her bir müşteri, belirli alanlardaki verilere sahiptir. İçin ilk aranacak alan, şu `LeadSource` biçimi izleyen alandır: **Kaynak-eylem**  |  **teklifi**.

**Kaynaklar**: Bu alanın değeri, müşteri adayını oluşturan Market 'e göre doldurulur. Olası değerler şunlardır `"AzureMarketplace"` , `"AzurePortal"` ve `"AppSource (SPZA)"` .

**Eylemler**: Bu alanın değeri, müşterinin müşteri adayını oluşturan Market 'te aldığı eyleme göre doldurulur.

Olası değerler şunlardır:

- **"INS"**: *yükleme* anlamına gelir. Bu eylem, bir müşteri ürününüzü edindiğinde Azure Market veya AppSource 'ta yer alır.
- **"Plt"**: *iş ortağının LED deneme sürümünü* temsil eder. Bu eylem, bir müşteri **benimle Iletişim kurma** seçeneğini seçtiğinde appsource 'ta bulunur.
- **"DNC"**: *başvurmayın* için temsil eder. Bu eylem, uygulama sayfanızda çapraz listeye alınan bir iş ortağı ile iletişim kurulması istendiğinde AppSource 'ta yer alır. Bu müşterinin uygulamanızda çapraz listelenme bildirimini paylaşıyoruz, ancak bunlara başvurulmaları gerekmez.
- **"Oluştur"**: Bu eylem yalnızca Azure Portal içindedir ve bir müşteri, teklifinizi kendi hesaplarına satın aldığında oluşturulur.
- **"Starttestdrive"**: Bu eylem yalnızca **Test sürücü** seçeneği içindir ve bir müşteri test sürücüsünü başlattığında oluşturulur.

**Teklifler**: ticari Market 'te birden fazla teklifiniz olabilir. Bu alanın değeri, müşteri adayını oluşturan teklifine göre doldurulur. Yayımcı KIMLIĞI ve teklif KIMLIĞI bu alana gönderilir ve teklifi Market 'e yayımladığınızda verdiğiniz değerlerdir.

Aşağıdaki örnekler, değerleri beklenen biçimde göstermektedir `publisherid.offerid` : 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>Müşteri bilgileri

Müşterinin bilgileri birden çok alan aracılığıyla gönderilir. Aşağıdaki örnek, bir müşteri adayıyla bulunan müşteri bilgilerini gösterir:

- FirstName: John
- Soyadı: Smith
- E-posta: jsmith \@ Microsoft.com
- Telefon: 1234567890
- Ülke: ABD
- Şirket: Microsoft
- Başlık: CTO

>[!NOTE]
>Önceki örnekteki verilerin hepsi her bir müşteri adayı için her zaman kullanılabilir değildir. "Müşteri adayları oluşturma" bölümünde belirtildiği gibi birden çok adımdan müşteri adaylarını alacağınız için, müşteri adaylarını işlemenin en iyi yolu kayıtları yeniden çoğaltmanız ve takip eden pencereleri kişiselleştirmesidir. Bu şekilde, her müşteri uygun bir ileti alır ve benzersiz bir ilişki oluşturursunuz.

## <a name="best-practices-for-lead-management"></a>Müşteri adayı yönetimi için en iyi yöntemler

Müşteri adaylarını satış döngüsüyle yönlendirmeyi sağlayacak bazı öneriler aşağıda verilmiştir:

- **İşlem**: kilometre taşları, analiz ve açık ekip sahipliğiyle açık bir satış işlemi tanımlayın.
- **Nitelik**: bir müşteri adayının tam nitelikli olup olmadığını gösteren önkoşulları tanımlayın. Satış veya pazarlama temsilcilerinin, müşteri adaylarını tam satış süreci boyunca almadan önce dikkatli bir şekilde nitelediğinizden emin olun.
- **İzleme**: 24 saat içinde izlemeyi unutmayın. Müşteri, bir test sürücüsü dağıtduktan sonra müşteri adayını tercih ettiğiniz şekilde hemen alacaksınız. hala normal olduklarından içinde e-posta gönderin. Ürününüzün sorunu için iyi bir çözüm olup olmadığını daha iyi anlamak için bir telefon araması zamanlaması isteyin. Tipik işlemin çok sayıda izleme çağrısı gerektirmesini bekler.
- **Nurra**: daha yüksek bir kâr marjına yol açmak için müşteri adaylarına ulaşın. İade et, ancak onları Not etmeyin. Kapatmadan önce e-posta fırsatlarından en az birkaç kez bakmasını öneririz; ilk denemeden sonra vermeyin. Bu müşterilerin ürününüze doğrudan sahip olduğunu ve ücretsiz denemede zaman harcadığını unutmayın; Harika adaylardır.

Teknik kurulum hazır olduktan sonra, bu müşteri adaylarını geçerli satış ve pazarlama stratejinize ve işletimsel süreçlerinize ekleyin. Genel Satış sürecinizi daha iyi anlamak ve yüksek kaliteli müşteri adayları sağlamak için sizinle birlikte çalışmak ve başarılı olmak için yeterli veri sunmak istiyoruz. Size bu müşterileri başarılı hale getirmeye yardımcı olmak için size ek veriler gönderdiğimiz müşteri adaylarını iyileştirebilecek ve geliştirebilmemiz için geri bildirimde bulunun. Satış ekibinizin ticari Market liderlerine göre daha başarılı olmasını sağlamak için [geri bildirim](mailto:AzureMarketOnboard@microsoft.com) ve öneriler sağlamayı düşünüyorsanız bize bilgi verin.

## <a name="next-steps"></a>Sonraki adımlar

- [Müşteri adayı yönetimi hakkında SSS ve sorun giderme](../lead-management-faq.md)