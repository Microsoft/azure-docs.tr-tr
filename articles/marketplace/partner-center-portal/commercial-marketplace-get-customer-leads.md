---
title: Müşteri müşteri adaylarını yapılandırma | Azure Marketi
description: Ticari Market 'te müşteri adaylarını yapılandırın.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: 7ead8dee12d4376e6e1058b84a25b91c021a937c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812638"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>Market teklifinizin müşteri adayları

Müşteri adayları, [Azure Marketi](https://azuremarketplace.microsoft.com) 'Nden veya [appsource](https://appsource.microsoft.com)'tan tekliflerinizi ilgilenen veya dağıtmakta olan müşteriler. Teklifiniz Market 'te yayımlandığında müşteri adaylarını alacaksınız. Bu makalede şunlar açıklanmaktadır:

* Market teklifinizin müşteri adaylarını nasıl üretdirebileceği, iş fırsatlarını kaçırmadığınızdan emin olmanızı sağlar. 
* Müşteri adaylarınızı tek bir merkezi konumda yönetebilmeniz için CRM 'nizi teklifize bağlayın.
* Size gönderilen müşteri adayı verilerini anlayın, bu sayede size ulaşan müşterileri takip edebilirsiniz.

## <a name="generate-customer-leads"></a>Müşteri adayları oluşturma

Bir müşteri adayının oluşturulduğu yer şunlardır:

1. Bir müşteri, marketten "benimle Iletişim kurun" seçeneğini belirledikten sonra bilgilerini paylaştığında. Bu müşteri adayı, ürününüzü alma konusunda ilgilendiğiniz müşteri hakkındaki bilgileri paylaşdığımız bir **ilk vade farkının** bulunduğu liderdir. Müşteri adayı, Alım huninin en üstü.

      ![Dynamics 365 benimle Iletişim kurun](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

2. Bir müşteri teklifinizi almak için "Şimdi al" veya "Oluştur" seçeneğini seçtiğinde ( [Azure Portal](https://portal.azure.com/)), bu müşteri adayı, ürününüzü dağıtmaya başlamış bir müşteri hakkındaki bilgileri paylaşdığımız **etkin bir lider**olur.

    ![SQL şimdi al](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Windows Server oluşturma](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

3. Müşteri "test sürücüsü" alır veya teklifinizin "ücretsiz deneme sürümü" başlatır. Sınama sürücüleri veya ücretsiz denemeler, herhangi bir giriş konusunda herhangi bir engelleri olmadan işletmenizin potansiyel müşterilerle anında paylaşılması için hızlandırılmış fırsatlardır.

    ![Dynamics 365 test sürücüsü](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dynamics 365 test sürücüsü](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>CRM sisteminize bağlanma

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Müşteri adayı verilerini anlama

Müşteri alma işlemi sırasında aldığınız her bir müşteri, belirli alanlardaki verilere sahiptir. Aranacak ilk alan, şu biçimi izleyen `LeadSource` alanıdır: **Kaynak-eylem** | **teklif**.

**Kaynaklar**: Bu alanın değeri, müşteri adayını oluşturan Market 'e göre doldurulur. Olası değerler şunlardır `"AzureMarketplace"`, `"AzurePortal"`ve `"AppSource (SPZA)"`.

**Eylemler**: Bu alanın değeri, müşterinin müşteri adayını oluşturan, Market 'te aldığı eyleme göre doldurulur. 

Olası değerler şunlardır:

- "INS"--yükleme. Bu eylem, bir müşteri ürününüzü satın aldığında Azure Market veya AppSource 'ta bulunur.
- "PLT"--Iş ortağı LED deneme sürümünü temsil eder. Bu eylem, bir müşteri benimle Iletişim kurma seçeneğini kullandığında AppSource 'ta bulunur.
- "DNC"--Iletişim kurulamadı. Bu eylem, uygulama sayfanızda çapraz olarak listelenen bir Iş ortağı ile iletişim kurulması istendiğinde AppSource 'ta bulunur. Bu müşterinin uygulamanızda çapraz olarak listelenme kafalarını paylaşıyoruz, ancak bunlara başvurulmaları gerekmez.
- "Oluştur"--Bu eylem yalnızca Azure portal içindedir ve müşteri teklifinizi kendi hesaplarına satın aldığında üretilir.
- "StartTestDrive"--Bu eylem yalnızca test sürücüleri için geçerlidir ve bir müşteri test sürücüsünü başlattığında oluşturulur.

**Teklifler**: market 'te birden çok teklif olabilir. Bu alanın değeri, müşteri adayını oluşturan teklifine göre doldurulur. Yayımcı KIMLIĞI ve teklif KIMLIĞI bu alana gönderilir ve teklifi Market 'e yayımladığınızda verdiğiniz değerlerdir.

Aşağıdaki örneklerde, beklenen biçimdeki örnek değerler gösterilmektedir `publisherid.offerid`: 

1. `checkpoint.check-point-r77-10sg-byol`
1. `bitnami.openedxcypress`
1. `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-info"></a>Müşteri bilgileri

Müşterinin bilgileri birden çok alan aracılığıyla gönderilir. Aşağıdaki örnek, bir müşteri adayıyla bulunan müşteri bilgilerini gösterir.

- FirstName: John
- Soyadı: Smith
- E-posta: jsmith\@microsoft.com
- Telefon: 1234567890
- Ülke: ABD
- Şirket: Microsoft
- Başlık: CTO

>[!Note]
>Önceki örnekteki verilerin hepsi her bir müşteri adayı için her zaman kullanılabilir değildir. Müşteri adayları bölümünde belirtildiği gibi birden çok adımdan müşteri adaylarını alacağınız için, müşteri adaylarını işlemenin en iyi yolu, kayıtları yeniden çoğaltmanız ve takip eden pencereleri kişiselleştirmenize neden olur. Bu şekilde, her müşteri uygun bir ileti almaya ve benzersiz bir ilişki oluşturuyoruz.

## <a name="best-practices-for-lead-management"></a>Müşteri adayı yönetimi için en iyi yöntemler

1. *İşlem* -kilometre taşları, KPI 'ler ve takım sahipliğini temizle ile açık bir satış işlemi tanımlayın.
2. *Nitelik belirleme* -bir müşteri adayının tam nitelikli olup olmadığını gösteren önkoşulları tanımlayın. Satış veya pazarlama temsilcilerinin, müşteri adaylarını tam satış süreci boyunca almadan önce dikkatle uygun hale getirin.
3. *Takip edin* -izlemeyi unutmayın, tipik işlemin beş ile 12 arasında izleme çağrısı gerektirdiğini bekliyor
4. *Nurra* , daha yüksek bir kâr marjına ulaşmak için müşteri adaylarınızı ister.

## <a name="leads-frequently-asked-questions"></a>Müşteri adayları hakkında sık sorulan sorular

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Lider hedefini ayarlama konusunda nereden yardım alabilirim?

Belgeleri [buradan](#connect-to-your-crm-system) bulabilir veya aka.MS/marketplacepublishersupport aracılığıyla bir destek bileti gönderebilir ve **"teklif oluşturma"** , **teklif** → **' sağlama Yönetim yapılandırması** ' nı seçebilirsiniz.

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-on-the-marketplace"></a>Market 'te teklif yayımlamak için bir lider hedefi yapılandırmak istiyorum mu?

Yanıt, yayımladığınız teklifin türüne bağlıdır. Müşteri katılım için SaaS ve Dynamics 365 ' i, ' benimle Iletişim kurun ', Işlem için tüm Dynamics 365 teklifleri, tüm Dynamics 365 Iş Merkezi teklifleri ve tüm danışmanlık hizmeti teklifleri bir müşteri adayı hedefine bağlantı gerektirir. Teklif türü listelenmediyse, gerekli değildir. Ancak, iş fırsatlarını kaçırmamak için bir lider hedefi yapılandırmanız önerilir.

### <a name="how-can-i-find-the-test-lead"></a>Test lideri nasıl bulabilirim?

Müşteri adayı Hedefinizdeki `"MSFT_TEST"` arayın, Microsoft 'tan örnek bir test lideri aşağıda verilmiştir:

```
company = MSFT_TEST_636573304831318844
country = US
description = MSFT_TEST_636573304831318844
email = MSFT_TEST_636573304831318844@test.com
encoding = UTF-8
encoding = UTF-8
first_name = MSFT_TEST_636573304831318844
last_name = MSFT_TEST_636573304831318844
lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|<Offer Name>
oid = 00Do0000000ZHog
phone = 1234567890
title = MSFT_TEST_636573304831318844
```

### <a name="i-have-a-live-offer-but-im-not-seeing-any-leads"></a>Canlı bir teklifim var, ancak hiçbir müşteri adayı görmüyorum?

Müşteri adayı hedefine olan bağlantınızın geçerli olduğundan emin olun. Iş Ortağı Merkezi 'nde teklifiniz üzerinde Yayımla ' yı vurduktan sonra size bir test lideri göndereceğiz. Test lideri görürseniz, bağlantı geçerli olur. Ayrıca, Market 'teki listede "Şimdi al", "benimle iletişim" veya "ücretsiz deneme" seçeneğine tıklayarak, önizleme adımı sırasında teklif önizlemeyi edinmeye çalışırken müşteri adayı bağlantınızı test edebilirsiniz.

Ayrıca, doğru verileri baktığınızdan emin olun. Bu belgenin [müşteri adayı verilerini anlama](#understand-lead-data) bölümünde bulunan içerik, müşteri adayının hedefine gönderdiğimiz müşteri adayı verilerini açıklar.

### <a name="i-have-configured-azure-blob-as-my-lead-destination-why-dont-i-see-the-lead"></a>Azure BLOB 'U lider hedefi olarak yapılandırdım, neden müşteri adayını görmüyorum?

Azure Blob lider hedefi artık desteklenmiyor, bu nedenle teklifiniz tarafından oluşturulan müşteri adaylarını kaçıruyoruz. Diğer [lider hedefi seçeneklerinden](./commercial-marketplace-get-customer-leads.md)herhangi birine geçin. 

### <a name="i-received-an-email-from-marketplace-why-cant-i-find-the-lead-in-my-crm"></a>Market 'ten bir e-posta aldım, neden CRM 'de müşteri adayını bulamıyorum?

Son kullanıcının e-posta etki alanı. edu 'dan olabilir. Gizlilik nedenleriyle,. edu etki alanından PII verileri geçirmedik. Aka.ms/marketplacepublishersupport aracılığıyla destek bileti gönderme.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Azure tablosunu lider hedefim olarak yapılandırdım, müşteri adaylarını nasıl görüntüleyebilirim?

Azure portalında depolanan müşteri adayı verilerine Azure portalından erişebilirsiniz veya Azure depolama hesabınızın tablo verilerini görüntülemek için [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) ücretsiz olarak indirip yükleyebilirsiniz.

### <a name="i-have-configured-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-lead-is-sent-by-marketplace"></a>Azure tablosunu lider hedefim olarak yapılandırdım, Market tarafından yeni bir müşteri adayı gönderildiğinde bildirim alabilir miyim?

Evet, [buradaki](./commercial-marketplace-lead-management-instructions-azure-table.md)belgelerde Azure tablosuna bir müşteri adayı eklenirse bir e-posta gönderen Microsoft Flow 'u ayarlamak için yönergeleri izleyin.

### <a name="i-have-configured-salesforce-as-my-lead-destination-why-cant-i-find-the-leads"></a>Salesforce 'um hedefi olarak Salesforce yapılandırdım, neden müşteri adaylarını bulamıyorum?

"Müşteri adayına Web" formunun bir seçim listesine göre zorunlu bir alan olup olmadığını denetleyin. Yanıt Evet ise, alanı üzerinde zorunlu olmayan bir metin alanına geçin.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Lider hedefimde bir sorun oluştu ve bazı müşteri adaylarını kaçırdım. E-postada bana gönderilebilirim?

PII (özel olarak tanımlanabilen bilgiler) ilkeleri nedeniyle, güvenli olmayan e-posta ile müşteri adayı bilgilerini paylaşamazsınız.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Azure tablosunu, lider hedefi olarak yapılandırdım, ne kadar ücret alınacaktır?

Öncü gen verileri düşüktür (neredeyse tüm yayımcılar için 1 GB <). Maliyet, bir 50 ayda 1.000 müşteri adayı alınmışsa, ücretlendirilecektir. Depolama fiyatlandırması hakkında daha fazla bilgi için bkz. [Depolama fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/).

Sorunuz hala yanıtlanmazsa, aka.ms/marketplacepublishersupport aracılığıyla desteğe başvurun, sonra **' teklif oluşturma '** ' yı seçin ve **teklif** → **' sağlama Yönetim yapılandırması** ' nı →. 

## <a name="next-steps"></a>Sonraki adımlar

Teknik kurulum hazır olduktan sonra, bu müşteri adaylarını geçerli satış & Pazarlama stratejinize ve işlemsel süreçlerinize eklemeniz gerekir. Genel Satış sürecinizi daha iyi anlamak ve yüksek kaliteli müşteri adayları sağlamak ve size yeterli veri sağlamak için size yakın bir şekilde çalışmak istemeniz gerekir. Size bu müşterileri başarılı hale getirmeye yardımcı olmak için size ek veriler gönderdiğimiz müşteri adaylarını iyileştirebilecek ve geliştirebilmemiz için geri bildirimde bulunun. Satış ekibinizin Market müşteri adaylarıyla daha başarılı olmasını sağlamak için [geri bildirim](mailto:AzureMarketOnboard@microsoft.com) ve öneriler sağlamayı düşünüyorsanız bize bilgi verin.
