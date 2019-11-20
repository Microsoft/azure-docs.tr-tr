---
title: Müşteri müşteri adaylarını yapılandırma | Azure Marketi
description: Bulut İş Ortağı Portalı müşteri adaylarını yapılandırın.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 312e172321c25248f1b0801bdbccf71762319989
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818820"
---
<a name="get-customer-leads"></a>Müşteri adayları alma
==================

Bu makalede, Bulut İş Ortağı Portalı kullanarak müşteri adaylarının nasıl oluşturulacağı açıklanmaktadır. Bu müşteri adaylarını CRM sisteminize bağlayabilirsiniz ve bunları satış işlem hattınızla tümleştirebilirsiniz.

## <a name="leads"></a>Yönetimi

Müşteri adayları, ilgilendiğiniz veya ürünlerinizi [Azure Marketi](https://azuremarketplace.microsoft.com/) 'Nden veya [appsource](https://appsource.microsoft.com)'tan dağıtmakta olan müşterilerdir.

### <a name="azure-marketplace"></a>Azure Market

1.  Müşteri teklifinizin bir "test sürücüsü" alır. Sınama sürücüleri, girişte herhangi bir engelleri olmadan işletmenizin potansiyel müşterilerle anında paylaşılması için hızlandırılmış bir fırsattır. Tüm test sürücüleri, daha fazla bilgi edinmek için ürününüzü deneme konusunda ilgilendiğiniz müşteri için bir müşteri adayı oluşturur. [Azure Marketi test sürücüsünde](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf)test sürücüleri hakkında daha fazla bilgi edinin.

    ![Market test sürücüsü örnekleri](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

1. Müşteri, bilgilerini "Şimdi al" seçeneğini belirledikten sonra paylaşıyoruz. Bu müşteri adayı, ürününüzü alma konusunda sizi ifade eden müşteri hakkındaki bilgileri paylaşdığımız bir **ilk ilgi çekici** liderdir. Müşteri adayı, Alım huninin en üstü.

   ![Şimdi al seçeneği](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

1. Müşteri, ürününüzü almak için [Azure portalında](https://portal.azure.com/) "satın al" öğesini seçer. Bu müşteri adayı, ürününüzü dağıtmaya başlamış bir müşteri hakkındaki bilgileri paylaşdığımız **etkin** bir lider.

   ![Satın alma seçeneği](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  Müşteri teklifiniz için bir "test sürücüsü" sürdü. Sınama sürücüleri, girişte herhangi bir engelleri olmadan işletmenizin potansiyel müşterilerle anında paylaşılması için hızlandırılmış bir fırsattır. Tüm test sürücüleri, daha fazla bilgi edinmek için ürününüzü denemeye ilgilenen bir müşteri lideri oluşturacaktır. [Appsource test sürücüsünde](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive)test sürücüleri hakkında daha fazla bilgi edinin.

    ![Test sürücüsü örneği](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  Müşteri, bilgilerini "Şimdi al" seçeneğini belirledikten sonra paylaşıyoruz. Bu müşteri adayı, ürününüzü alma hakkında bilgi veren müşteriyle ilgili bilgileri paylaşdığımız bir **ilk ilgi çekici** liderdir. Müşteri adayı, Alım huninin en üstü.

      ![Şimdi al seçeneği](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  Müşteri teklifiniz üzerinde "benimle Iletişim kurun" öğesini seçer. Bu müşteri adayı, ürününüzle ilgili olarak takip etmek isteyen bir müşteri hakkındaki bilgileri paylaşdığımız **etkin** bir lider.

    ![Benimle iletişim kurma seçeneği](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>Müşteri adayı verileri
---------

Müşteri alma işlemi sırasında aldığınız her bir müşteri, belirli alanlardaki verilere sahiptir. Birden çok adımdan müşteri adaylarını alacağınız için, müşteri adaylarını işlemenin en iyi yolu, takip eden pencereleri yinelemek ve kişiselleştirmenizi sağlar. Bu şekilde, her müşteri uygun bir ileti almaya ve benzersiz bir ilişki oluşturuyoruz.

### <a name="lead-source"></a>Müşteri adayı kaynağı

Bir müşteri adayı kaynağının biçimi **kaynak**-**eylem** |  **tekliftir**

**Kaynaklar**: "AzureMarketplace", "AzurePortal", "testdrive" ve "appsource (spza)"

**Eylemler**:
- "INS"--yükleme. Bu eylem, bir müşteri ürününüzü satın aldığında Azure Market veya AppSource 'ta bulunur.
- "PLT"--Iş ortağı LED deneme sürümünü temsil eder. Bu eylem, bir müşteri benimle Iletişim kurma seçeneğini kullandığında AppSource 'ta bulunur.
- "DNC"--Iletişim kurulamadı. Bu eylem, uygulama sayfanızda çapraz olarak listelenen bir Iş ortağı ile iletişim kurulması istendiğinde AppSource 'ta bulunur. Bu müşterinin uygulamanızda çapraz olarak listelenme kafalarını paylaşıyoruz, ancak bunlara başvurulmaları gerekmez.
- "Oluştur"--Bu eylem yalnızca Azure portalının içindedir ve bir müşteri, teklifinizi kendi hesaplarına satın aldığında üretilir.
- "StartTestDrive"--Bu eylem yalnızca test sürücüleri için geçerlidir ve bir müşteri test sürücüsünü başlattığında oluşturulur.

**Teklifler**

Aşağıdaki örneklerde, bir yayımcıya atanan benzersiz tanımlayıcılar ve belirli bir teklif gösterilmektedir: kontrol noktası. Check-Point-r77-10sg-KLG, bitnamı. openedxcypress ve Docusign. 3701c77e-1cfa-4c56-91e6-3ed0b622145a.


### <a name="customer-info"></a>Müşteri bilgileri

Aşağıdaki örnekteki alanlar, bir müşteri adayıyla bulunan müşteri bilgilerini gösterir.
- FirstName: John
- Soyadı: Smith
- E-posta: jsmith\@microsoft.com
- Telefon: 1234567890
- Ülke: ABD
- Şirket: Microsoft
- Başlık: CTO

>[!Note]
>Önceki örnekteki verilerin hepsi her bir müşteri adayı için her zaman kullanılabilir değildir.

Müşteri adaylarını geliştirmek için etkin bir şekilde çalışıyoruz, bu nedenle burada görmediğiniz ancak sahip olmak istediğiniz bir veri alanı varsa lütfen [görüşlerinizi bize gönderin](mailto:AzureMarketOnboard@microsoft.com).

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>CRM sisteminizi Bulut İş Ortağı Portalı bağlama
------------------------------------------------------------

Müşteri adaylarını almaya başlamak için, Bulut İş Ortağı Portalı için lider yönetimi bağlayıcımızı geliştirdik. böylece, CRM bilgilerinizi kolayca takabilirsiniz ve bağlantıyı sizin için yapacağız. Artık, bir dış sistemle tümleştirme için önemli bir mühendislik çabası olmadan Market tarafından oluşturulan müşteri adaylarını kolayca kullanabilirsiniz.

![Lider yönetimi Bağlayıcısı](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

Müşteri adaylarını çeşitli CRM sistemlerine veya doğrudan, istediğiniz müşteri adaylarını yönetebileceğiniz bir Azure depolama tablosuna yazalım. Aşağıdaki bağlantıların her biri olası müşteri hedeflerine bağlanmak için yönergeler sağlar:

-   Dynamics [CRM Online](./cloud-partner-portal-lead-management-instructions-dynamics.md) 'ı, müşteri adaylarını almak IÇIN Dynamics CRM Online 'ın nasıl yapılandırılacağı hakkında yönergeler almak için.
-   Müşteri adaylarını almak üzere Marketo lider yapılandırmasını ayarlamaya yönelik yönergeleri almak için [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md) .
-    Salesforce, müşteri adaylarını almak için Salesforce örneğinizi ayarlamaya yönelik yönergeler almak için [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md) .
-    Azure tablosu 'nda müşteri adaylarını almak için Azure depolama hesabınızı ayarlamaya yönelik yönergeleri almak için [Azure tablosu](./cloud-partner-portal-lead-management-instructions-azure-table.md) .
-   HTTPS uç [noktasını](./cloud-partner-portal-lead-management-instructions-https.md) , müşteri adaylarını almak için ayarlama yönergelerini almak için

Lider hedefini yapılandırdıktan ve teklifinizi yayımladıktan sonra, bağlantıyı doğrulayacağız ve size bir test lideri göndereceğiz. Teklifi, canlı çalışmadan önce görüntülerken, teklifi önizleme ortamında edinmeyi deneyerek lider bağlantınızı da test edebilirsiniz. Müşteri adaylarını kaybetmemeniz için müşteri adayı ayarlarınızın güncel kalmasını sağlamak önemlidir. bu nedenle, bu bağlantıları sonunda her değişiklik yapıldığında güncelleştirdiğinizden emin olun.

<a name="what-next"></a>Ne kadar ileri?
----------

Teknik kurulum hazır olduktan sonra, bu müşteri adaylarını geçerli satış & Pazarlama stratejinize ve işlemsel süreçlerinize eklemeniz gerekir. Genel Satış sürecinizi daha iyi anlamak ve yüksek kaliteli müşteri adayları sağlamak ve size başarılı olmak için yeterli veri sağlamak için sizinle yakından çalışmak istiyoruz. Size bu müşterileri başarılı hale getirmeye yardımcı olmak için size ek veriler gönderdiğimiz müşteri adaylarını iyileştirebilecek ve geliştirebilmemiz için geri bildirimde bulunun. Satış ekibinizin Market müşteri adaylarıyla daha başarılı olmasını sağlamak için [geri bildirim](mailto:AzureMarketOnboard@microsoft.com) ve öneriler sağlamayı öğrenmek istiyorsanız lütfen bize bildirin.
