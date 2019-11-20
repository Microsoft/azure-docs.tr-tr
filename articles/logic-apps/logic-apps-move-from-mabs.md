---
title: BizTalk Services uygulamalarını Azure Logic Apps taşıyın | Microsoft Docs
description: Azure BizTalk Services 'den (MABS) Azure Logic Apps 'ye geçiş
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 05/30/2017
ms.openlocfilehash: dfc0aa4fa7c70ae91f25f97671b15dacfe991594
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68273194"
---
# <a name="migrate-from-biztalk-services-to-azure-logic-apps"></a>BizTalk Services 'den Azure Logic Apps geçir

Microsoft Azure BizTalk Services (MABS) devre dışı bırakılıyor. MABS tümleştirme çözümlerinizi [Azure Logic Apps](../logic-apps/logic-apps-overview.md)taşımak için bu makaledeki yönergeleri izleyin. 

## <a name="introduction"></a>Giriş

BizTalk Services iki alt hizmetten oluşur:

* Microsoft BizTalk Services Karma Bağlantılar
* EAı ve EDI köprü tabanlı tümleştirme

[Azure App Service Karma Bağlantılar](../app-service/app-service-hybrid-connections.md) BizTalk Services karma bağlantılar yerini alır. Azure Karma Bağlantılar, Azure portal aracılığıyla Azure App Service kullanılabilir. Bu hizmet, mevcut BizTalk Services hibrit bağlantılarını ve ayrıca portalda oluşturduğunuz yeni karma bağlantıları yönetebilmeniz için bir Karma Bağlantı Yöneticisi sağlar. 

[Logic Apps](../logic-apps/logic-apps-overview.md) , EAı ve EDI köprü tabanlı tümleştirmeyi BizTalk Services ve daha birçok aynı yetenekler ile değiştirir. Bu hizmet, bir tarayıcı veya Visual Studio ile hızlı ve kolay bir şekilde karmaşık tümleştirme çözümleri oluşturmak için bulut ölçekli tüketim tabanlı iş akışı ve düzenleme özellikleri sağlar.

Bu tablo, Logic Apps BizTalk Services yeteneklerini eşler.

| BizTalk Services   | Logic Apps            | Amaç                      |
| ------------------ | --------------------- | ---------------------------- |
| Bağlayıcı          | Bağlayıcı             | Veri gönderme ve alma   |
| Bridge             | Logic App             | Ardışık düzen işlemcisi           |
| Aşamayı doğrula     | XML doğrulama eylemi | Bir XML belgesini bir şemaya göre doğrulama | 
| Zenginleştirme aşaması       | Veri belirteçleri           | Özellikleri iletilere veya yönlendirme kararlarına yükseltme |
| Dönüşüm aşaması    | Dönüştürme eylemi      | XML iletilerini bir biçimden diğerine dönüştürme |
| Kod çözme aşaması       | Düz dosya kod çözme eylemi | Düz dosyadan XML 'e Dönüştür |
| Kodlama aşaması       | Düz dosya kodlama eylemi | XML 'den düz dosyaya Dönüştür |
| İleti denetçisi  | Azure Işlevleri veya API Apps | Tümleştirmelerinizin özel kodunu çalıştırın |
| Rota eylemi       | Koşul veya anahtar | İletileri belirtilen bağlayıcılardan birine yönlendir |
|||| 

## <a name="biztalk-services-artifacts"></a>BizTalk Services yapıtlar

BizTalk Services çeşitli yapıt türlerine sahiptir. 

## <a name="connectors"></a>Bağlayıcılar

BizTalk Services bağlayıcılar, HTTP tabanlı istek/yanıt etkileşimlerini etkinleştiren iki yönlü köprüler de dahil olmak üzere köprüleri veri gönderme ve alma. Logic Apps aynı terminolojiyi kullanır ve çok çeşitli teknoloji ve hizmetlere bağlanarak aynı amaca hizmet eden yüzlerce bağlayıcı içerir. Örneğin, şirket Içi veri ağ geçidi aracılığıyla OneDrive, Office365, Dynamics CRM ve daha fazlası gibi bulut SaaS ve PaaS hizmetleri ve şirket içi sistemler, BizTalk Services için BizTalk bağdaştırıcı hizmeti 'nin yerini alan şirket içi sistemler için kullanılabilir. BizTalk Services kaynaklar FTP, SFTP ve Service Bus kuyruğu veya konu aboneliği ile sınırlıdır.

![](media/logic-apps-move-from-mabs/sources.png)

Varsayılan olarak, her köprü için, çalışma zamanı adresiyle ve köprünün göreli adres özellikleriyle yapılandırılmış bir HTTP uç noktası bulunur. Logic Apps ile aynı sonuçlara ulaşmak için, [istek ve yanıt](../connectors/connectors-native-reqres.md) eylemlerini kullanın.

## <a name="xml-processing-and-bridges"></a>XML işleme ve köprüler

BizTalk Services, bir köprü işleme ardışık düzenine benzer. Köprü, bir bağlayıcıdan alınan verileri alabilir, verilerle çalışmayı gerçekleştirebilir ve sonuçları başka bir sisteme gönderebilir. Logic Apps, aynı işlem hattı tabanlı etkileşim düzenlerini BizTalk Services ile destekleyerek aynı zamanda diğer tümleştirme desenleri de sağlar. BizTalk Services içindeki [XML istek-yanıt Köprüsü](https://msdn.microsoft.com/library/azure/hh689781.aspx) , bu görevleri gerçekleştiren aşamalardan oluşan BIR veter işlem hattı olarak bilinir:

* (V) doğrula
* (E) zenginleştirme
* (T) dönüştürme
* (E) zenginleştirme
* (R) yolu

Bu görüntüde, işleme ve yanıt yolları için ayrı olarak denetim sağlayan istek ve yanıt arasında işlemenin nasıl bölündüğü gösterilmektedir; Örneğin, her yol için farklı haritalar kullanarak:

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

Ayrıca, XML tek yönlü köprü, işleme ve işleme aşamasına kod çözme ve kodlama aşamalarını ekliyor. Doğrudan geçiş Köprüsü, tek bir zenginleştirme aşaması içerir.

### <a name="message-processing-decoding-and-encoding"></a>İleti işleme, kod çözme ve kodlama

BizTalk Services, farklı türlerde XML iletileri alabilir ve alınan ileti için eşleşen şemayı belirleyebilirsiniz. Bu iş, alma işlemi ardışık düzeninin *Ileti türleri* aşamasında gerçekleştirilir. Kod çözme aşaması daha sonra, belirtilen şemayı kullanarak iletinin kodunu çözmek için algılanan ileti türünü kullanır. Şema düz bir dosya şemadır, bu aşama gelen düz dosyayı XML 'e dönüştürür. 

Logic Apps benzer yetenekler sağlar. Farklı bağlayıcı tetikleyicilerini (dosya sistemi, FTP, HTTP vb.) kullanarak farklı protokoller üzerinde düz bir dosya alırsınız ve gelen verileri XML 'e dönüştürmek için [düz dosya kod çözme](../logic-apps/logic-apps-enterprise-integration-flatfile.md) eylemini kullanın. Mevcut düz dosya şemalarınızı herhangi bir değişiklik yapmadan doğrudan Logic Apps taşıyabilir ve ardından şemaları tümleştirme hesabınıza yükleyebilirsiniz.

### <a name="validation"></a>Doğrulama

Gelen veriler XML 'e dönüştürüldükten sonra (veya XML, ileti biçimi alınmışsa), iletinin XSD şemanıza uygun olup olmadığını anlamak için doğrulama çalışır. Bu görevi Logic Apps gerçekleştirmek için, [XML doğrulama](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) eylemini kullanın. Herhangi bir değişiklik yapmadan BizTalk Services aynı şemaları kullanabilirsiniz.

### <a name="transform-messages"></a>İletileri Dönüştür

BizTalk Services, dönüşüm aşaması bir XML tabanlı ileti biçimini diğerine dönüştürür. Bu iş, TRFM tabanlı Eşleyici kullanılarak bir eşleme uygulanarak yapılır. Logic Apps, işlem benzerdir. Dönüştürme eylemi, tümleştirme hesabınızdan bir eşleme yürütür. Ana fark, Logic Apps ' deki haritaların XSLT biçiminde olduğu bir biçimdir. XSLT, functoıds içeren BizTalk Server için oluşturulan haritalar da dahil olmak üzere zaten sahip olduğunuz mevcut XSLT 'yi yeniden kullanma özelliğini içerir. 

### <a name="routing-rules"></a>Yönlendirme kuralları

BizTalk Services, gelen iletileri veya verileri hangi uç noktaya veya bağlayıcıya gönderebileceğine ilişkin bir yönlendirme kararı sağlar. Önceden yapılandırılmış uç noktalardan seçim yapma özelliği, yönlendirme filtresi seçeneği kullanılarak yapılabilir:

![](media/logic-apps-move-from-mabs/route-filter.png)

BizTalk Services ' de, yalnızca iki seçenek varsa, bir *koşul* kullanmak BizTalk Services yönlendirme filtrelerini dönüştürmenin en iyi yoludur. İkiden fazla varsa, **anahtar**kullanın.

Logic Apps, [koşullu deyimler](../logic-apps/logic-apps-control-flow-conditional-statement.md) ve [Switch deyimleriyle](../logic-apps/logic-apps-control-flow-switch-statement.md)gelişmiş mantık özellikleri ve Gelişmiş denetim akışı ve yönlendirme sağlar.

### <a name="enrich"></a>Zenginleştirin

BizTalk Services işleme içinde, zenginleştirme aşamasında, alınan verilerle ilişkili ileti bağlamına özellikler eklenir. Örneğin, bir veritabanı aramadan yönlendirme için kullanılacak bir özelliği yükseltme veya bir XPath ifadesi kullanarak bir değeri ayıklama. Logic Apps, önceki eylemlerden tüm bağlamsal veri çıktılarına erişim sağlar ve bu sayede aynı davranışın çoğaltılmasını basit hale getirir. Örneğin, `Get Row` SQL bağlantı eylemini kullanarak bir SQL Server veritabanından veri döndürür ve verileri yönlendirme için bir karar eylemi içinde kullanırsınız. Benzer şekilde, bir tetikleyiciden gelen Service Bus sıraya alınan iletilerin özellikleri, XPath iş akışı tanımlama dili ifadesi kullanılarak XPath olarak da adreslenebilir.

### <a name="run-custom-code"></a>Özel kod Çalıştır

BizTalk Services, kendi derlemelerinize yüklenen [özel kodu çalıştırmanızı](https://msdn.microsoft.com/library/azure/dn232389.aspx) sağlar. Bu işlev [ımessageınspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector) arabirimi tarafından uygulanır. Köprü içindeki her aşama, bu arabirimi uygulayan .NET türünü sağlayan iki Özellik (Enter denetçisinde ve çıkış denetçisinde) içerir. Özel kod, veriler üzerinde daha karmaşık işleme gerçekleştirmenizi sağlar ve ortak iş mantığını gerçekleştiren derlemelerdeki mevcut kodu yeniden kullanmanıza olanak tanır. 

Logic Apps özel kod yürütmeye yönelik iki temel yol sağlar: Azure Işlevleri ve API Apps. Azure Işlevleri, Logic Apps 'ten oluşturulabilir ve çağrılabilir. Bkz. [Azure işlevleri aracılığıyla mantıksal uygulamalar için özel kod ekleme ve çalıştırma](../logic-apps/logic-apps-azure-functions.md). Kendi tetikleyicilerinin ve eylemlerinizi oluşturmak için API Apps, Azure App Service bir kısmını kullanın. [Logic Apps ile kullanmak üzere özel bır API oluşturma](../logic-apps/logic-apps-create-api-app.md)hakkında daha fazla bilgi edinin. 

BizTalk Services çağırdığınız derlemelerde özel kodunuz varsa, bu kodu Azure Işlevlerine taşıyabilir ya da uygulamadığınıza bağlı olarak API Apps ile özel API 'Ler oluşturabilirsiniz. Örneğin, Logic Apps Bağlayıcısı olmayan başka bir hizmeti sarmalayan kodunuz varsa, bir API uygulaması oluşturun ve API uygulamanızın mantıksal uygulamanızda sağladığı eylemleri kullanın. Yardımcı işlevleriniz veya kitaplıklarınız varsa, Azure Işlevleri büyük olasılıkla en uygun seçenektir.

### <a name="edi-processing-and-trading-partner-management"></a>EDI işleme ve ticaret iş ortağı yönetimi

BizTalk Services ve Logic Apps, AS2 (Applicability Statement 2), x12 ve EDIOLGU desteğiyle EDI ve B2B işlemlerini içerir. BizTalk Services, EDI 'yı oluşturun ve özel Izleme ve yönetim portalında ticari iş ortakları ve anlaşmalar oluşturun veya yönetin.
Logic Apps, bu işlevselliği [Enterprise Integration Pack (EıP)](../logic-apps/logic-apps-enterprise-integration-overview.md)üzerinden alırsınız. EıP, EDI ve B2B işleme için [tümleştirme hesabı](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) ve B2B eylemleri sağlar. Ayrıca, [ticaret ortakları](../logic-apps/logic-apps-enterprise-integration-partners.md) ve [anlaşmalar](../logic-apps/logic-apps-enterprise-integration-agreements.md)oluşturmak ve yönetmek için bir tümleştirme hesabı da kullanabilirsiniz. Bir tümleştirme hesabı oluşturduktan sonra, bir veya daha fazla mantıksal uygulamayı hesaba bağlayabilirsiniz. Daha sonra, mantıksal uygulamanızdan ticari iş ortağı bilgilerine erişmek için B2B eylemlerini kullanabilirsiniz. Aşağıdaki eylemler verilmiştir:

* AS2 kodlama
* AS2 kodunu çöz
* X12 kodlama
* X12 kodunu çöz
* EDIOLGU kodlama
* EDIOLGU kod çözme

BizTalk Services aksine, bu eylemler aktarım protokollerinden ayrılır. Bu nedenle, mantıksal uygulamalarınızı oluştururken veri göndermek ve almak için kullandığınız bağlayıcılardan daha fazla esneklik elde edersiniz. Örneğin, x12 dosyalarını e-postadaki ek olarak alabilir ve ardından bu dosyaları bir mantıksal uygulamada işleyebilirsiniz. 

## <a name="manage-and-monitor"></a>Yönetme ve izleme

BizTalk Services, özel bir portal, sorunları izlemek ve sorunlarını gidermek için izleme özellikleri sağlamıştır. Logic Apps, [Azure Portal](../logic-apps/logic-apps-monitor-your-logic-apps.md)üzerinden daha zengin izleme ve izleme özellikleri sağlar ve hareket durumunda olduğunuzda göz önünde bulundurmanız için bir mobil uygulama içerir.

## <a name="high-availability"></a>Yüksek kullanılabilirlik

BizTalk Services yüksek kullanılabilirlik (HA) için, belirli bir bölgede birden fazla örnek kullanarak işleme yükünü paylaşabilirsiniz. Logic Apps, ek ücret ödemeden bölge içi HA sağlar. 

BizTalk Services, B2B işleme için bölge dışı olağanüstü durum kurtarma, yedekleme ve geri yükleme işlemi gerektirir. İş sürekliliği için Logic Apps, farklı bölgelerdeki tümleştirme hesapları genelinde B2B verilerini eşitlemenize olanak sağlayan bölgeler arası etkin/Pasif [Dr yeteneği](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)sağlar.

## <a name="next-steps"></a>Sonraki adımlar

* [Logic Apps nedir?](../logic-apps/logic-apps-overview.md)
* [İlk mantıksal uygulamanızı oluşturun](../logic-apps/quickstart-create-first-logic-app-workflow.md) veya [önceden oluşturulmuş bir şablon](../logic-apps/logic-apps-create-logic-apps-from-templates.md) kullanarak hızlı şekilde çalışmaya başlayın  
* Logic Apps 'te kullanabileceğiniz [tüm kullanılabilir bağlayıcıları görüntüleme](../connectors/apis-list.md)