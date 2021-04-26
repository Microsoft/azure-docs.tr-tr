---
title: Azure Logic Apps için Bağlayıcılar
description: Azure Logic Apps ile otomatik iş akışları oluşturmak için bağlayıcılara genel bakış. Farklı bağlayıcı türlerinin, Tetikleyicilerin ve eylemlerin nasıl çalıştığını öğrenin.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: 9e138cb7fb591728e7bc6a02ff61b3167d13da30
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771362"
---
# <a name="connectors-for-azure-logic-apps"></a>Azure Logic Apps için Bağlayıcılar

Azure Logic Apps, *Bağlayıcılar* diğer uygulamalar, hizmetler, sistemler, protokoller ve platformlardaki olaylara, verilere ve eylemlere hızlı bir şekilde erişmenize yardımcı olur. Bu görevleri genellikle ek kod olmadan yapabilirsiniz. Ayrıca, bu bilgileri bulut tabanlı, şirket içi veya hibrit ortamlarda kullanan Logic Apps iş akışları oluşturmak için bağlayıcıları kullanabilirsiniz.

Logic Apps için yüzlerce bağlayıcı bulunur. Sonuç olarak, bu belge Logic Apps için en popüler ve yaygın olarak kullanılan bağlayıcılara odaklanır. Logic Apps, Microsoft Power otomatikleştirmek ve Microsoft Power Apps 'teki bağlayıcılar hakkında tüm bilgiler için bkz. [Bağlayıcılar belgeleri](/connectors). 

Fiyatlandırma hakkında daha fazla bilgi için [Logic Apps fiyatlandırma modeline](../logic-apps/logic-apps-pricing.md)ve [Logic Apps fiyatlandırma ayrıntılarına](https://azure.microsoft.com/pricing/details/logic-apps/)bakın.

Mantıksal uygulamanızı Bağlayıcısı olmayan bir hizmet ya da API ile bütünleştirmek için, hizmeti HTTP gibi bir protokol üzerinden veya [özel bir bağlayıcı oluşturabilirsiniz](#custom-apis-and-connectors).

## <a name="what-are-connectors"></a>Bağlayıcılar nelerdir?

Azure Logic Apps, bağlayıcılar mantıksal uygulamanızın iş akışında görevler gerçekleştirmek için kullandığınız *Tetikleyiciler* ve *Eylemler* sağlar. Her tetikleyici ve eylem, yapılandırabileceğiniz özelliklere sahiptir. Bazı Tetikleyiciler ve Eylemler, iş akışınızın belirli bir hizmete veya sisteme erişebilmeleri için [bağlantı oluşturmanızı ve yapılandırmanızı](#connection-configuration) gerektirir.

### <a name="triggers"></a>Tetikleyiciler

*Tetikleyici* her zaman iş akışındaki ilk adımdır ve iş akışını başlatan olayı belirtir. Birden çok tetikleyici türü vardır:

- *Yoklama Tetikleyicileri* , yeni verileri veya belirli bir olayı denetlemek için belirli bir zamanlamaya göre belirli bir hizmeti veya sistemi düzenli olarak denetler. Yeni veriler varsa veya belirli bir olay gerçekleştiğinde, bu Tetikleyiciler iş akışınızın yeni bir örneğini oluşturur ve çalıştırır. Bu yeni örnek daha sonra girdi olarak geçirilen verileri kullanabilir.
- *Gönderim Tetikleyicileri* yeni verileri dinler veya bir olayın gerçekleşmesi için yoklama olmadan dinleme yapılır. Yeni veriler kullanılabilir olduğunda veya olay gerçekleştiğinde, bu Tetikleyiciler mantıksal uygulamanızın yeni bir örneğini oluşturur ve çalıştırır. Bu yeni örnek daha sonra girdi olarak geçirilen verileri kullanabilir.

Örneğin, FTP sunucunuza bir dosya yüklendiğinde bir işlem iş akışı oluşturmak isteyebilirsiniz. **Bir dosya eklendiğinde veya değiştirildiğinde** adlı FTP Bağlayıcısı tetikleyicisini iş akışınızın ilk adımı olarak ekleyebilirsiniz. Daha sonra, karşıya yükleme olaylarını düzenli olarak denetlemek için bir zamanlama belirtebilirsiniz.

Tetikleyici Ayrıca, sonraki eylemlerin iş akışı boyunca başvurabilebileceği ve bu verileri kullanabileceği iş akışınıza herhangi bir giriş ve diğer gerekli verileri de geçirir. Örneğin, **Yeni bir e-posta geldiğinde** adlı Office 365 Outlook tetikleyicisini kullanmak isteyebilirsiniz. Bu tetikleyiciyi Gönderen, konu satırı, gövde, ekler gibi her yeni e-postanın içeriğini geçecek şekilde yapılandırabilirsiniz. Ardından, Eylemler kullanarak mantıksal uygulamanızda bu bilgileri işleyebilirsiniz.

### <a name="actions"></a>Eylemler

*Eylem* , tetikleyiciyi izleyen ve iş akışınızda bir tür görev gerçekleştiren bir işlemdir. Mantıksal uygulamanızda birden çok işlem kullanabilirsiniz. Örneğin, bir SQL veritabanında yeni müşteri verilerini algılayan bir SQL tetikleyicisine sahip olabilirsiniz. İş akışınız, verileri işleyen bir SQL eylemi olmayan başka bir eylem tarafından izlenen, müşteri verilerini alan ilk bir SQL eylemi içerebilir.

## <a name="connector-categories"></a>Bağlayıcı kategorileri

Logic Apps, genellikle Tetikleyiciler ve eylemlerin yerleşik veya yönetilen bağlayıcı sürümleri vardır. Her iki sürümde de az sayıda tetikleyici ve eylem mevcuttur. Belirli sürümler, şu anda yalnızca [Logic Apps önizlemede](../logic-apps/logic-apps-overview-preview.md)bulunan çok kiracılı bir mantıksal uygulama veya tek kiracılı bir mantıksal uygulama oluşturup oluşturmadığınıza bağlıdır.

[Yerleşik Tetikleyiciler ve eylemler](built-in.md) Logic Apps çalışma zamanında yerel olarak çalışır, bağlantı oluşturma gerektirmez ve bu tür görevleri gerçekleştirir:

- [İş akışlarınızda kodu çalıştırın](built-in.md#run-code-from-workflows).
- [Verilerinizi düzenleyin ve denetleyin](built-in.md#control-workflow).
- [Verileri yönetin veya değiştirin](built-in.md#manage-or-manipulate-data).

[Yönetilen bağlayıcılar](managed.md) Microsoft tarafından dağıtılır, barındırılır ve yönetilir. Bu bağlayıcılar, bulut Hizmetleri, şirket içi sistemler veya her ikisine yönelik Tetikleyiciler ve eylemler sağlar. Bu modüller şunlardır:

- Şirket içi sistemlerdeki verilere ve kaynaklara erişmenize yardımcı olan [Şirket içi bağlayıcılar](managed.md#on-premises-connectors) .
- Enterprise sistemlerine erişim sağlayan bazı Logic Apps bağlayıcılarının sürümleri olan [Kurumsal bağlayıcılar](managed.md#enterprise-connectors).
- İşletmeden işletmeye (B2B) iletişim senaryolarını destekleyen [tümleştirme hesabı bağlayıcıları](managed.md#integration-account-connectors).
- Küçük bir yönetilen bağlayıcı grubu olan [tümleştirme hizmeti ortamı (ıSE) bağlayıcılar](managed.md#ise-connectors) [yalnızca sesleri için kullanılabilir](#ise-and-connectors).

## <a name="connection-configuration"></a>Bağlantı yapılandırması

Çoğu bağlayıcı, mantıksal uygulamanızda tetikleyicilerini veya eylemlerini kullanabilmeniz için öncelikle hedef hizmet veya sisteme bir *bağlantı* oluşturmanızı gerektirir. Bir bağlantı oluşturmak için kimlik bilgilerinizi hesap kimlik bilgileriyle ve bazen diğer bağlantı bilgilerini kullanarak kimlik doğrulaması yapmanız gerekir. Örneğin, iş akışınız, bir mantıksal uygulamada Office 365 Outlook e-posta hesabınızla çalışmaya ve bunlarla çalışabilmeniz için, bu hesaba bir bağlantı yetkilendirmelisiniz.

Office 365, Salesforce veya GitHub gibi Azure Active Directory (Azure AD) OAuth kullanan bağlayıcılar için, erişim belirtecinizin [şifrelendiğini](../security/fundamentals/encryption-overview.md) ve Azure gizli anahtarı 'nda güvenli bir şekilde depolanacağı hizmette oturum açmanız gerekir. FTP ve SQL gibi diğer bağlayıcılar, sunucu adresi, Kullanıcı adı ve parola gibi yapılandırma ayrıntılarına sahip bir bağlantı gerektirir. Bu bağlantı yapılandırma ayrıntıları da [şifrelenir ve Azure 'da güvenli bir şekilde depolanır](../security/fundamentals/encryption-overview.md).

Kurulan bağlantılar, hizmet veya sistem izin verdiği sürece hedef hizmete veya sisteme erişebilir. Office 365 ve Dynamics gibi Azure AD OAuth bağlantıları kullanan hizmetler için Logic Apps erişim belirteçlerini süresiz olarak yeniler. Diğer hizmetlerde, Logic Apps hizmetinin yenileme olmadan bir belirteci ne kadar süre kullanabileceği sınırlandırmaları olabilir. Genellikle parolanızı değiştirme gibi bazı eylemler tüm erişim belirteçlerini geçersiz kılar.

Bir iş akışı içinden bağlantı oluştursanız da, bağlantılar kendi kaynak tanımlarıyla ayrı Azure kaynaklarıdır. Bu bağlantı kaynağı tanımlarını gözden geçirmek için, [mantıksal uygulamanızı Azure 'Dan Visual Studio 'ya indirin](../logic-apps/manage-logic-apps-with-visual-studio.md). Bu yöntem, dağıtım için en kolay geçerli bir parametreli mantıksal uygulama şablonu oluşturmanın en kolay yoludur.

> [!TIP]
> Kuruluşunuz Logic Apps bağlayıcılar aracılığıyla belirli kaynaklara erişmenize izin vermezse [Azure ilkesi](../governance/policy/overview.md)'ni kullanarak [Bu tür bağlantıları oluşturma özelliğini engelleyebilirsiniz](../logic-apps/block-connections-connectors.md) .

## <a name="recurrence-behavior"></a>Yinelenme davranışı

[Yineleme tetikleyicisi](../connectors/connectors-native-recurrence.md)gibi yinelenen yerleşik tetikleyiciler, Azure Logic Apps yerel olarak çalışır ve ilk olarak bir bağlantı oluşturmanız gereken Office 365 Outlook Bağlayıcısı tetikleyicisi gibi yinelenen bağlantı tabanlı tetikleyicilerden farklıdır.

Her iki tür tetikleyici için de bir yinelenme belirli bir başlangıç tarihi ve saati belirtmezse, bu, tetikleyicinizin yinelenme kurulumuna rağmen mantıksal uygulamayı kaydettiğinizde veya dağıtırken ilk yinelenme hemen çalışır. Bu davranışı önlemek için, ilk tekrarın çalıştırmak istediğiniz zaman için bir başlangıç tarihi ve saati belirtin.

### <a name="recurrence-for-built-in-triggers"></a>Yerleşik Tetikleyiciler için yinelenme

Yinelenen yerleşik Tetikleyiciler, belirtilen saat dilimi dahil olmak üzere ayarladığınız zamanlamayı izler. Ancak, bir yinelenme gelecekteki tekrarları çalıştırmak için belirli saatler gibi diğer Gelişmiş zamanlama seçeneklerini belirtmezse, bu Yinelenmeler son tetikleyici yürütmeye göre yapılır. Sonuç olarak, bu Yinelenmeler için başlangıç zamanları, depolama aramaları sırasında gecikme süresi gibi faktörlerin düşmesine neden olmuş olabilirler. Sorun giderme yardımı için [yineleme sorunları](#recurrence-issues) bölümüne bakın.

### <a name="recurrence-for-connection-based-triggers"></a>Bağlantı tabanlı tetikleyiciler için yinelenme

Office 365 Outlook gibi yinelenen bağlantı tabanlı tetikleyicilere göre zamanlama, yürütmeyi denetleyen tek sürücü değildir. Saat dilimi yalnızca başlangıçtaki başlangıç saatini belirler. Sonraki çalıştırmalar yineleme zamanlaması, son tetikleme yürütmesi ve çalışma sürelerinin veya beklenmeyen davranışlara neden olabilecek diğer faktörlere bağlıdır. Bu modüller şunlardır:

- Tetikleyicinin daha fazla veri içeren bir sunucuya erişip erişmeyeceğini, tetikleyicinin doğrudan getirmeye çalıştığı.
- Tetikleyicinin yer aldığı tüm arızalar veya denemeler.
- Depolama çağrıları sırasında gecikme süresi.
- Gün ışığından yararlanma saati (DST) başladığında ve sona erdiğinde belirtilen zamanlamanın korunmasından sakın.
- Sonraki çalışma zamanı gerçekleştiğinde etkileyebilecek diğer faktörler.

Sorun giderme yardımı için [yineleme sorunları](#recurrence-issues) bölümüne bakın. 

### <a name="recurrence-issues"></a>Yinelenme sorunları

İş akışınızın belirtilen başlangıç saatinde çalıştığından ve bir yinelemeyi kaçırmadığınızdan emin olmak için, özellikle sıklık gün veya daha uzun bir süre içinde, aşağıdaki çözümleri deneyin.

DST etkin olduğunda, iş akışınızın beklenen sürede çalışmaya devam edebilmesi için yinelemeyi el ile ayarlayın. Aksi takdirde, DST başlatıldığında başlangıç saati bir saat ileri ve DST sona erdiğinde bir saat geriye geçer. Daha fazla bilgi ve örnek için bkz. [gün ışığından yararlanma saati ve standart saat Için yinelenme](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time).

**Yineleme** tetikleyicisi kullanıyorsanız, bir saat dilimi, bir başlangıç tarihi ve saati belirtin. Ayrıca, **bu saatlere** ve yalnızca **gün** ve **hafta** **sıklıklarında** kullanılabilen, özelliklerde sonraki yinelenmeleri çalıştırmak için belirli zamanları yapılandırın. Ancak, bazı durumlarda Windows zaman vardiyada sorun oluşmasına neden olabilir. 

Atlanan **tekrarlamaları** önlemek için yineleme tetikleyicisi yerine bir [ **kayan pencere** tetikleyicisi](../connectors/connectors-native-sliding-window.md) kullanmayı düşünün.

## <a name="custom-apis-and-connectors"></a>Özel API 'Ler ve bağlayıcılar

Özel kod çalıştıran veya bağlayıcı olarak kullanılamayan API 'Leri çağırmak için, [özel API Apps oluşturarak](../logic-apps/logic-apps-create-api-app.md)Logic Apps platformunu genişletebilirsiniz. 

Ayrıca, bu API 'Leri Azure aboneliğinizdeki herhangi bir mantıksal uygulama için kullanılabilir hale getirmek üzere bekleyen veya SOAP tabanlı API 'Ler için [özel bağlayıcılar oluşturabilirsiniz](../logic-apps/custom-connector-overview.md) . 

Özel API Apps veya bağlayıcıları herkesin Azure 'da kullanması için genel hale getirmek amacıyla, [Microsoft sertifikası için bağlayıcılar gönderebilirsiniz](/connectors/custom-connectors/submit-certification).

## <a name="ise-and-connectors"></a>ISE ve bağlayıcılar

Bir Azure sanal ağındaki kaynaklara doğrudan erişmesi gereken iş akışları için, özel kaynaklarda iş akışlarınızı oluşturabileceğiniz, dağıtabileceğiniz ve çalıştırabileceğiniz bir adanmış [tümleştirme hizmeti ortamı (ıSE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) oluşturabilirsiniz. Sesleri oluşturma hakkında daha fazla bilgi için bkz. [Azure Logic Apps Azure sanal ağlarına bağlanma](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

Bir ıSE içinde oluşturulan özel bağlayıcılar şirket içi veri ağ geçidi ile çalışmaz. Ancak bu bağlayıcılar, ıSE 'yi barındıran bir Azure sanal ağına bağlı şirket içi veri kaynaklarına doğrudan erişebilir. Bu nedenle, bir ıSE içindeki Logic Apps, bu kaynaklarla iletişim kurarken veri ağ geçidine ihtiyaç duymamasından kaynaklanıyor olabilir. Şirket içi veri ağ geçidi gerektiren bir ıSE dışında oluşturduğunuz özel bağlayıcılarınız varsa, bir ıSE içindeki Logic Apps bu bağlayıcıları kullanabilir.

Logic Apps tasarımcısında, bir ıSE 'de Logic Apps için kullanmak istediğiniz bağlayıcılara gözatarken, yerleşik Tetikleyiciler ve eylemlerde bir **çekirdek** etiketi görünür, ancak **Ise** etiketi bazı bağlayıcılarda görünür.

:::row:::
    :::column:::
        ![Örnek çekırdek Bağlayıcısı](./media/apis-list/example-core-connector.png)
        \
        \
        **ÇEKIRDEĞIN**
        \
        \
        Bu etiketle birlikte yerleşik Tetikleyiciler ve Eylemler, Logic Apps ile aynı ıSE 'de çalışır.
    :::column-end:::
    :::column:::
        ![Örnek ıSE Bağlayıcısı](./media/apis-list/example-ise-connector.png)
        \
        \
        **KESIN**
        \
        \
        Bu etikete sahip yönetilen bağlayıcılar, Logic Apps ile aynı ıSE 'de çalışır. Azure sanal ağına bağlı bir şirket içi sisteminiz varsa, bir ıSE, Logic Apps 'in [Şirket içi veri ağ geçidi](../logic-apps/logic-apps-gateway-connection.md)olmadan bu sisteme doğrudan erişmesini sağlar. Bunun yerine, varsa, bu sistemin **Ise** bağlayıcısını kullanabilirsiniz, bir http eylemi veya [özel bağlayıcı](connectors-overview.md#custom-apis-and-connectors). **Ise** bağlayıcıları olmayan şirket içi sistemler için şirket içi veri ağ geçidi kullanın. Kullanılabilir ıSE bağlayıcılarını gözden geçirmek için bkz. [Ise bağlayıcıları](#ise-and-connectors).
    :::column-end:::
    :::column:::
        ![Örnek çok kiracılı bağlayıcı](./media/apis-list/example-multi-tenant-connector.png)
        \
        \
        Etiket yok     \
        \
        **Temel** veya **Ise** etiketi olmayan diğer tüm bağlayıcılar, kullanmaya devam edebilmeniz için genel, çok kiracılı Logic Apps hizmetinde çalıştırın.
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="known-issues"></a>Bilinen sorunlar

Logic Apps bağlayıcılarına yönelik bilinen sorunlar aşağıda verilmiştir.

#### <a name="error-badgateway-client-request-id-guid"></a>Hata: BadGateway. İstemci istek kimliği: ' {GUID} '

Bu hata, bir veya daha fazla bağlantının, SFTP ad SQL gibi Azure Active Directory (Azure AD) OAuth kimlik doğrulamasını desteklemediği bir mantıksal uygulamadaki etiketlerin güncelleştirilmesini, bu bağlantıları bozmasına neden olur. Bu davranışı önlemek için bu etiketlerin güncelleştirilmesini önleyin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Logic Apps çağırabilmeniz için özel API 'Ler oluşturun](/logic-apps/logic-apps-create-api-app)
