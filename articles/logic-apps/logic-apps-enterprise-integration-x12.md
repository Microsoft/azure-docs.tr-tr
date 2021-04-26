---
title: B2B için x12 iletileri gönderme ve alma
description: Enterprise Integration Pack ile Azure Logic Apps kullanarak B2B kurumsal tümleştirme senaryoları için Exchange x12 iletileri
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/29/2020
ms.openlocfilehash: 87a2bcc386ec5688fadb68aabdd2e5239e205516
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077483"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Enterprise Integration Pack ile Azure Logic Apps B2B kurumsal tümleştirme için Exchange x12 iletileri

Azure Logic Apps içinde x12 iletilerle çalışmak için, x12 iletişimini yönetmeye yönelik Tetikleyiciler ve eylemler sağlayan x12 bağlayıcısını kullanabilirsiniz. Bunun yerine EDIOLGU iletileri hakkında daha fazla bilgi için bkz. [Exchange ediolgu iletileri](logic-apps-enterprise-integration-edifact.md).

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Henüz bir Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* X12 bağlayıcısını kullanmak istediğiniz mantıksal uygulama ve mantıksal uygulamanızın iş akışını başlatan bir tetikleyici. X12 Bağlayıcısı, Tetikleyiciler değil yalnızca eylemler sağlar. Logic Apps 'e yeni başladıysanız, [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ve [hızlı başlangıç: Ilk mantıksal uygulamanızı oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md)' yı gözden geçirin.

* Azure aboneliğinizle ilişkili ve x12 bağlayıcısını kullanmayı planladığınız mantıksal uygulamaya bağlı olan bir [tümleştirme hesabı](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) . Hem mantıksal uygulamanız hem de tümleştirme hesabınız aynı konumda veya Azure bölgesinde bulunmalıdır.

* X12 Identity niteleyicisi kullanarak, tümleştirme hesabınızda zaten tanımlamış olduğunuz en az iki [ticari iş ortağı](../logic-apps/logic-apps-enterprise-integration-partners.md) .

* Tümleştirme hesabınıza önceden eklediğiniz XML doğrulaması için kullanılacak [şemalar](../logic-apps/logic-apps-enterprise-integration-schemas.md) . Sağlık sigortası taşınabilirlik ve Sorumluluk Yasası (HIPAA) şemaları ile çalışıyorsanız bkz. [HIPAA şemaları](#hipaa-schemas).

* X12 bağlayıcısını kullanabilmeniz için, ticari iş ortaklarınız arasında bir x12 [sözleşmesi](../logic-apps/logic-apps-enterprise-integration-agreements.md) oluşturmanız ve bu sözleşmeyi tümleştirme hesabınızda depolamanız gerekir. Sağlık sigortası taşınabilirlik ve Sorumluluk Yasası (HIPAA) şemaları ile çalışıyorsanız, sözleşmenize bir bölüm eklemeniz gerekir `schemaReferences` . Daha fazla bilgi için bkz. [HIPAA şemaları](#hipaa-schemas).

<a name="receive-settings"></a>

## <a name="receive-settings"></a>Ayarları al

Anlaşma özelliklerini ayarladıktan sonra, bu sözleşmenin bu anlaşma aracılığıyla iş ortağınızdan aldığınız gelen iletileri nasıl tanımladığını ve işleyeceğini yapılandırabilirsiniz.

1. **Ekle**' nin altında, **alma ayarları**' nı seçin.

1. Sizinle ileti alışverişi yapan iş ortağıyla anlaşmanıza bağlı olarak bu özellikleri yapılandırın. **Alma ayarları** şu bölümler halinde düzenlenmiştir:

   * [Tanımlayıcılar](#inbound-identifiers)
   * [Bildirimi](#inbound-acknowledgement)
   * [Şemalar](#inbound-schemas)
   * [Larla](#inbound-envelopes)
   * [Denetim numaraları](#inbound-control-numbers)
   * [Doğrulamalar](#inbound-validations)
   * [İç ayarlar](#inbound-internal-settings)

   Özellik açıklamaları için bu bölümdeki tablolara bakın.

1. İşiniz bittiğinde, **Tamam**' ı seçerek ayarlarınızı kaydettiğinizden emin olun.

<a name="inbound-identifiers"></a>

### <a name="receive-settings---identifiers"></a>Alma ayarları-tanımlayıcılar

![Gelen iletiler için tanımlayıcı özellikleri](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-identifiers.png)

| Özellik | Açıklama |
|----------|-------------|
| **ISA1 (yetkilendirme niteleyicisi)** | Kullanmak istediğiniz yetkilendirme niteleyicisi değeri. Varsayılan değer 00 ' dır ve **Yetkilendirme bilgisi yok**. <p>**Note**: diğer değerleri seçerseniz, **ISA2** özelliği için bir değer belirtin. |
| **ISA2** | **ISA1** özelliği 00 olmadığında kullanılacak yetkilendirme bilgileri değeri ( **Yetkilendirme bilgisi** yok). Bu özellik değeri en az bir alfasayısal karakter ve en fazla 10 olmalıdır. |
| **ISA3 (güvenlik niteleyicisi)** | Kullanmak istediğiniz güvenlik niteleyicisi değeri. Varsayılan değer 00 ' dır. **güvenlik bilgisi yok**. <p>**Note**: diğer değerleri seçerseniz, **ISA4** özelliği için bir değer belirtin. |
| **ISA4** | **ISA3** özelliği 00 olmadığında kullanılacak güvenlik bilgileri değeri, bir **güvenlik bilgisi** yok. Bu özellik değeri en az bir alfasayısal karakter ve en fazla 10 olmalıdır. |
|||

<a name="inbound-acknowledgement"></a>

### <a name="receive-settings---acknowledgement"></a>Alma ayarları-onay

![Gelen iletiler için onay](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-acknowledgement.png)

| Özellik | Açıklama |
|----------|-------------|
| **TA1 bekleniyor** | Değiş tokuş göndericisine teknik onay (TA1) döndürün. |
| **SK bekleniyor** | Değişim göndericisine bir işlevsel bildirim (SK) döndürün. <p>Şema sürümüne göre **SK sürümü** özelliği için 997 veya 999 bildirimleri ' ni seçin. <p>Kabul edilen işlem kümelerine yönelik işlevsel AK2 döngüleri oluşturmayı etkinleştirmek için **AK2/IK2 döngüsünü dahil et**' i seçin. |

<a name="inbound-schemas"></a>

### <a name="receive-settings---schemas"></a>Alma ayarları-şemalar

![Gelen iletiler için şemalar](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-schemas.png)

Bu bölüm için, [tümleştirme hesabınızdan](./logic-apps-enterprise-integration-create-integration-account.md) her işlem türü (ST01) ve gönderen UYGULAMASı (GS02) için bir [şema](../logic-apps/logic-apps-enterprise-integration-schemas.md) seçin. EDI alma işlem hattı, gelen iletideki ST01 ve GS02 değerleri ve gelen ileti şeması ile bu bölümde ayarladığınız değerleri ve şemayı eşleştirerek gelen iletiyi ayrıştırır. Her satırı tamamladıktan sonra yeni boş bir satır otomatik olarak görüntülenir.

| Özellik | Açıklama |
|----------|-------------|
| **Sürüm** | Şema için x12 sürümü |
| **İşlem türü (ST01)** | İşlem türü |
| **Gönderen uygulaması (GS02)** | Gönderen uygulaması |
| **Şema** | Kullanmak istediğiniz şema dosyası |
|||

<a name="inbound-envelopes"></a>

### <a name="receive-settings---envelopes"></a>Alma ayarları-zarflar

![Gelen iletiler için işlem kümelerinde kullanılacak ayırıcılar](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-envelopes.png)

| Özellik | Açıklama |
|----------|-------------|
| **ISA11 kullanımı** | Bir işlem kümesinde kullanılacak ayırıcı: <p>- **Standart tanımlayıcı**: EDI alma işlem hattındaki gelen belgenin ondalık gösterimi yerine ondalık Gösterim için bir nokta (.) kullanın. <p>- **Yineleme ayırıcısı**: basit bir veri öğesinin yinelenen tekrarlarının veya yinelenen bir veri yapısının ayırıcısını belirtin. Örneğin, genellikle simgeyi seçtiğinizde (^), yineleme ayırıcısı olarak kullanılır. HIPAA şemaları için, yalnızca Carat 'yı kullanabilirsiniz. |
|||

<a name="inbound-control-numbers"></a>

### <a name="receive-settings---control-numbers"></a>Ayarları Al-denetim numaraları

![Gelen iletiler için denetim numarası yinelemelerini işleme](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-control-numbers.png) 

| Özellik | Açıklama |
|----------|-------------|
| **Değişim denetim numarası çoğaltmasına izin verme** | Yinelenen değişiklikleri engelleyin. Alınan değişim denetim numarası için değişim denetim numarasını (ISA13) denetleyin. Bir eşleşme algılanırsa, EDI alma işlem hattı değişimi işlemez. <p><p>Çekin gerçekleştirileceği gün sayısını belirtmek için, **YINELENEN ISA13 for (Days) özelliğine yönelik denetim** için bir değer girin. |
| **Grup denetim numarası yinelenmesine izin verme** | Yinelenen grup denetim numaralarına sahip olan karşılıklı değişiklikleri engelleyin. |
| **Işlem kümesi denetim numarası yinelenmesine izin verme** | Yinelenen işlem kümesi denetim numaralarına sahip karşılıklı değişiklikleri engelleyin. |
|||

<a name="inbound-validations"></a>

### <a name="receive-settings---validations"></a>Ayarları Al-doğrulamalar

![Gelen iletiler için doğrulamalar](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-validations.png)

**Varsayılan** satır, EDI ileti türü için kullanılan doğrulama kurallarını gösterir. Farklı kurallar tanımlamak istiyorsanız, kuralın **doğru** olarak ayarlandığını istediğiniz her bir kutuyu seçin. Her satırı tamamladıktan sonra yeni boş bir satır otomatik olarak görüntülenir.

| Özellik | Açıklama |
|----------|-------------|
| **İleti türü** | EDI ileti türü |
| **EDI doğrulaması** | Şema EDI özellikleri, uzunluk kısıtlamaları, boş veri öğeleri ve sondaki ayırıcılar tarafından tanımlanan veri türlerinde EDI doğrulaması gerçekleştirin. |
| **Genişletilmiş Doğrulama** | Veri türü EDI değilse, doğrulama işlemi veri öğesi gereksinimidir ve izin verilen yineleme, numaralandırmalar ve veri öğesi uzunluğu doğrulaması (Min veya Max). |
| **Baştaki/sondaki sıfırları izin ver** | Ek önde gelen veya sondaki sıfır ve boşluk karakterlerini saklayın. Bu karakterleri kaldırmayın. |
| **Baştaki/sondaki sıfırları Kırp** | Baştaki veya sondaki sıfır ve boşluk karakterlerini kaldırın. |
| **Sondaki ayırıcı Ilkesi** | Sondaki ayırıcılar oluştur. <p>- **Izin verilmiyor**: gelen değişim içinde sondaki sınırlayıcıları ve ayırıcıları yasakla. Değişim, sonda sınırlayıcılar ve ayırıcılar içeriyorsa, değişim geçerli değil olarak bildirilmiştir. <p>- **Isteğe bağlı**: sondaki sınırlayıcılarla veya Ayırıcısız değişiklikler kabul edin. <p>- **Zorunlu**: gelen değişim, sonda sınırlayıcılar ve ayırıcılara sahip olmalıdır. |
|||

<a name="inbound-internal-settings"></a>

### <a name="receive-settings---internal-settings"></a>Alma ayarları-Iç ayarlar

![Gelen iletiler için iç ayarlar](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-internal-settings.png)

| Özellik | Açıklama |
|----------|-------------|
| **Kapsanan ondalık biçimi nn değerini 10 tabanında sayısal değere Dönüştür** | "Nn" biçimiyle belirtilen EDI numarasını 10 tabanında bir sayısal değere dönüştürür. |
| **Sondaki ayırıcılara izin veriliyorsa boş XML etiketleri oluştur** | Değiş tokuş gönderenin, sondaki ayırıcılar için boş XML etiketleri içermesini sağlayabilirsiniz. |
| **Değişimi işlem kümeleri olarak Böl-hata durumunda işlem kümelerini askıya al** | Bir değiş tokuş içindeki her işlem kümesini, işlem kümesine uygun zarfı uygulayarak ayrı bir XML belgesi olarak Ayrıştır. Yalnızca doğrulamanın başarısız olduğu işlemleri askıya alın. |
| **Değişimi işlem kümeleri olarak Böl-hata durumunda değişimi askıya al** | Uygun zarfı uygulayarak bir değiş tokuş içindeki her işlem kümesini ayrı bir XML belgesi olarak Ayrıştır. Değişim başarısız doğrulamasında bir veya daha fazla işlem kümesi olduğunda tüm değişimi askıya alın. |
| **Değişimi koru-hata durumunda işlem kümelerini askıya al** | Değişimi dokunulmadan bırakın ve tüm toplu değişim için bir XML belgesi oluşturun. Yalnızca doğrulamanın başarısız olduğu işlem kümelerini askıya alın, ancak diğer tüm işlem kümelerini işlemeye devam edin. |
| **Değişimi koru-hata durumunda değişimi askıya al** |Değişimi dokunulmadan bırakır, tüm toplu değişim için bir XML belgesi oluşturur. Değişim başarısız doğrulamasında bir veya daha fazla işlem kümesi olduğunda tüm değişimi askıya alır. |
|||

<a name="send-settings"></a>

## <a name="send-settings"></a>Ayarları gönder

Anlaşma özelliklerini ayarladıktan sonra, bu sözleşmenin bu sözleşme aracılığıyla iş ortağınıza göndereceğiniz giden iletileri nasıl tanımladığını ve işleyeceğini yapılandırabilirsiniz.

1. **Ekle**' nin altında, **ayarları gönder**' i seçin.

1. Sizinle ileti alışverişi yapan iş ortağıyla anlaşmanıza bağlı olarak bu özellikleri yapılandırın. Özellik açıklamaları için bu bölümdeki tablolara bakın.

   **Gönderme ayarları** şu bölümler halinde düzenlenmiştir:

   * [Tanımlayıcılar](#outbound-identifiers)
   * [Bildirimi](#outbound-acknowledgement)
   * [Şemalar](#outbound-schemas)
   * [Larla](#outbound-envelopes)
   * [Denetim sürüm numarası](#outbound-control-version-number)
   * [Denetim numaraları](#outbound-control-numbers)
   * [Karakter kümeleri ve ayırıcılar](#outbound-character-sets-separators)
   * [Doğrulama](#outbound-validation)

1. İşiniz bittiğinde, **Tamam**' ı seçerek ayarlarınızı kaydettiğinizden emin olun.

<a name="outbound-identifiers"></a>

### <a name="send-settings---identifiers"></a>Gönderme ayarları-tanımlayıcılar

![Giden iletiler için tanımlayıcı özellikleri](./media/logic-apps-enterprise-integration-x12/x12-send-settings-identifiers.png)

| Özellik | Açıklama |
|----------|-------------|
| **ISA1 (yetkilendirme niteleyicisi)** | Kullanmak istediğiniz yetkilendirme niteleyicisi değeri. Varsayılan değer 00 ' dır ve **Yetkilendirme bilgisi yok**. <p>**Note**: diğer değerleri seçerseniz, **ISA2** özelliği için bir değer belirtin. |
| **ISA2** | **ISA1** özelliği 00 olmadığında kullanılacak yetkilendirme bilgileri değeri ( **Yetkilendirme bilgisi** yok). Bu özellik değeri en az bir alfasayısal karakter ve en fazla 10 olmalıdır. |
| **ISA3 (güvenlik niteleyicisi)** | Kullanmak istediğiniz güvenlik niteleyicisi değeri. Varsayılan değer 00 ' dır. **güvenlik bilgisi yok**. <p>**Note**: diğer değerleri seçerseniz, **ISA4** özelliği için bir değer belirtin. |
| **ISA4** | **ISA3** özelliği 00 olmadığında kullanılacak güvenlik bilgileri değeri, bir **güvenlik bilgisi** yok. Bu özellik değeri en az bir alfasayısal karakter ve en fazla 10 olmalıdır. |
|||

<a name="outbound-acknowledgement"></a>

### <a name="send-settings---acknowledgement"></a>Gönderme ayarları-onay

![Giden iletiler için bildirim özellikleri](./media/logic-apps-enterprise-integration-x12/x12-send-settings-acknowledgement.png)

| Özellik | Açıklama |
|----------|-------------|
| **TA1 bekleniyor** | Değiş tokuş göndericisine teknik onay (TA1) döndürün. <p>Bu ayar, iletiyi gönderen ana bilgisayar ortağının anlaşmada Konuk ortağından bir bildirim istemesi gerektiğini belirtir. Bu bildirimler, sözleşmenin alma ayarlarına bağlı olarak ana bilgisayar ortağı tarafından beklenir. |
| **SK bekleniyor** | Değişim göndericisine bir işlevsel bildirim (SK) döndürün. Şema sürümüne göre **SK sürümü** özelliği için 997 veya 999 bildirimleri ' ni seçin. <p>Bu ayarlar, iletiyi gönderen ana bilgisayar ortağının anlaşmada Konuk ortağından bir onay istemesi gerektiğini belirtir. Bu bildirimler, sözleşmenin alma ayarlarına bağlı olarak ana bilgisayar ortağı tarafından beklenir. |
|||

<a name="outbound-schemas"></a>

### <a name="send-settings---schemas"></a>Gönderme ayarları-şemalar

![Giden ileti şemaları](./media/logic-apps-enterprise-integration-x12/x12-send-settings-schemas.png)

Bu bölüm için, [tümleştirme hesabınızdan](./logic-apps-enterprise-integration-create-integration-account.md) her işlem türü için bir [şema](../logic-apps/logic-apps-enterprise-integration-schemas.md) seçin (ST01). Her satırı tamamladıktan sonra yeni boş bir satır otomatik olarak görüntülenir.

| Özellik | Açıklama |
|----------|-------------|
| **Sürüm** | Şema için x12 sürümü |
| **İşlem türü (ST01)** | Şema için işlem türü |
| **Şema** | Kullanmak istediğiniz şema dosyası. Önce şemayı seçerseniz, sürüm ve işlem türü otomatik olarak ayarlanır. |
|||

<a name="outbound-envelopes"></a>

### <a name="send-settings---envelopes"></a>Gönderme ayarları-zarflar

![Giden iletiler için kullanılacak bir işlem kümesindeki ayırıcılar](./media/logic-apps-enterprise-integration-x12/x12-send-settings-envelopes.png)

| Özellik | Açıklama |
|----------|-------------|
| **ISA11 kullanımı** | Bir işlem kümesinde kullanılacak ayırıcı: <p>- **Standart tanımlayıcı**: EDI Send işlem hattında giden belgenin ondalık gösterimi yerine, ondalık Gösterim için bir nokta (.) kullanın. <p>- **Yineleme ayırıcısı**: basit bir veri öğesinin yinelenen tekrarlarının veya yinelenen bir veri yapısının ayırıcısını belirtin. Örneğin, genellikle simgeyi seçtiğinizde (^), yineleme ayırıcısı olarak kullanılır. HIPAA şemaları için, yalnızca Carat 'yı kullanabilirsiniz. |
|||

<a name="outbound-control-version-number"></a>

### <a name="send-settings---control-version-number"></a>Gönderme ayarları-denetim sürüm numarası

![Giden iletiler için denetim sürüm numarası](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-version-number.png)

Bu bölüm için, her değişim için [tümleştirme hesabınızdan](./logic-apps-enterprise-integration-create-integration-account.md) bir [şema](../logic-apps/logic-apps-enterprise-integration-schemas.md) seçin. Her satırı tamamladıktan sonra yeni boş bir satır otomatik olarak görüntülenir.

| Özellik | Açıklama |
|----------|-------------|
| **Denetim sürüm numarası (ıSA12)** | X12 Standard sürümü |
| **Kullanım göstergesi (ISA15)** | **Test** verileri, **bilgi** verileri veya **Üretim** verileri olan bir değişim bağlamı |
| **Şema** | EDI Send işlem hattına gönderilen x12 ile kodlanmış bir değişim için GS ve ST segmentlerini oluşturmak için kullanılacak şema. |
| **GS1** | İsteğe bağlı, işlevsel kodu seçin. |
| **GS2** | İsteğe bağlı, uygulamayı göndereni belirtin. |
| **GS3** | İsteğe bağlı, uygulama alıcıyı belirtin. |
| **GS4** | İsteğe bağlı, **Ccyymmdd** veya **YGG**' yi seçin. |
| **GS5** | İsteğe bağlı, **ssmm**, **HHMMSS** veya **HHMMSSdd**' yi seçin. |
| **GS7** | İsteğe bağlı, sorumlu ajanı için bir değer seçin. |
| **GS8** | İsteğe bağlı, şema belgesi sürümünü belirtin. |
|||

<a name="outbound-control-numbers"></a>

### <a name="send-settings---control-numbers"></a>Gönderme ayarları-denetim numaraları

![Giden iletiler için denetim numaraları](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-numbers.png)

| Özellik | Açıklama |
|----------|-------------|
| **Değişim denetim numarası (ISA13)** | Değişim denetim numarası için en az 1, en yüksek değer olan 999999999 değerleri içerebilen değer aralığı |
| **Grup denetim numarası (GS06)** | Minimum değeri 1 ve maksimum 999999999 değeri olabilen Grup denetim numarası için değer aralığı |
| **İşlem kümesi denetim numarası (ST02)** | En az 1, en yüksek değer olan 999999999 olabilen işlem kümesi denetim numarası için değer aralığı <p>- **Ön ek**: isteğe bağlı, alfasayısal bir değer <br>- **Sonek**: isteğe bağlı, alfasayısal bir değer |
|||

<a name="outbound-character-sets-separators"></a>

### <a name="send-settings---character-sets-and-separators"></a>Gönderme ayarları-karakter kümeleri ve ayırıcılar

![Giden iletilerde ileti türleri için sınırlayıcılar](./media/logic-apps-enterprise-integration-x12/x12-send-settings-character-sets-separators.png)

**Varsayılan** satır, bir ileti şeması için sınırlayıcılar olarak kullanılan karakter kümesini gösterir. **Varsayılan** karakter kümesini kullanmak istemiyorsanız, her ileti türü için farklı bir sınırlayıcı kümesi girebilirsiniz. Her satırı tamamladıktan sonra yeni boş bir satır otomatik olarak görüntülenir.

> [!TIP]
> Özel karakter değerleri sağlamak için, sözleşmeyi JSON olarak düzenleyin ve özel karakter için ASCII değeri sağlayın.

| Özellik | Açıklama |
|----------|-------------|
| **Kullanılacak karakter kümesi** | **Temel**, **genişletilmiş** ya da **UTF8** olan x12 karakter kümesi. |
| **Şema** | Kullanmak istediğiniz şema. Şemayı seçtikten sonra, aşağıdaki ayırıcı açıklamalara göre kullanmak istediğiniz karakter kümesini seçin. |
| **Giriş türü** | Karakter kümesi için giriş türü |
| **Bileşen ayırıcısı** | Bileşik veri öğelerini ayıran tek bir karakter |
| **Veri öğesi ayırıcısı** | Bileşik veriler içindeki basit veri öğelerini ayıran tek bir karakter |
| **değiştirme karakter ayırıcısı** | Giden x12 iletisini oluştururken yük verilerinde tüm ayırıcı karakterlerinin yerini alan bir değiştirme karakteri |
| **Segment Sonlandırıcı** | EDI segmentinin sonunu gösteren tek bir karakter |
| **Önekini** | Segment tanımlayıcısıyla birlikte kullanılacak karakter. Bir sonek belirtirseniz, segment Sonlandırıcı veri öğesi boş olabilir. Segment Sonlandırıcı boş bırakılırsa, bir sonek atamanız gerekir. |
|||

<a name="outbound-validation"></a>

### <a name="send-settings---validation"></a>Gönderme ayarları-doğrulama

![Giden iletiler için doğrulama özellikleri](./media/logic-apps-enterprise-integration-x12/x12-send-settings-validation.png) 

**Varsayılan** satır, EDI ileti türü için kullanılan doğrulama kurallarını gösterir. Farklı kurallar tanımlamak istiyorsanız, kuralın **doğru** olarak ayarlandığını istediğiniz her bir kutuyu seçin. Her satırı tamamladıktan sonra yeni boş bir satır otomatik olarak görüntülenir.

| Özellik | Açıklama |
|----------|-------------|
| **İleti türü** | EDI ileti türü |
| **EDI doğrulaması** | Şema EDI özellikleri, uzunluk kısıtlamaları, boş veri öğeleri ve sondaki ayırıcılar tarafından tanımlanan veri türlerinde EDI doğrulaması gerçekleştirin. |
| **Genişletilmiş Doğrulama** | Veri türü EDI değilse, doğrulama işlemi veri öğesi gereksinimidir ve izin verilen yineleme, numaralandırmalar ve veri öğesi uzunluğu doğrulaması (Min veya Max). |
| **Baştaki/sondaki sıfırları izin ver** | Ek önde gelen veya sondaki sıfır ve boşluk karakterlerini saklayın. Bu karakterleri kaldırmayın. |
| **Baştaki/sondaki sıfırları Kırp** | Baştaki veya sondaki sıfır ve boşluk karakterlerini kaldırın. |
| **Sondaki ayırıcı Ilkesi** | Sondaki ayırıcılar oluştur. <p>- **Izin verilmiyor**: giden değişim içinde sondaki sınırlayıcıları ve ayırıcıları yasakla. Değişim, sonda sınırlayıcılar ve ayırıcılar içeriyorsa, değişim geçerli değil olarak bildirilmiştir. <p>- **Isteğe bağlı**: sondaki sınırlayıcılar ve Ayırıcısız ya da bunlarla birlikte değişiklikler gönderin. <p>- **Zorunlu**: giden değişim, sonda sınırlayıcılar ve ayırıcılara sahip olmalıdır. |
|||

<a name="hipaa-schemas"></a>

## <a name="hipaa-schemas-and-message-types"></a>HIPAA şemaları ve ileti türleri

HIPAA şemaları ve 277 ya da 837 ileti türleriyle çalışırken birkaç ek adım gerçekleştirmeniz gerekir. Bu ileti türleri için [belge sürüm numaraları (GS8)](#outbound-control-version-number) 9 ' dan fazla karaktere sahiptir, ÖRNEĞIN "005010X222A1". Ayrıca, bazı belge sürüm numaraları, VARIANT ileti türleriyle eşlenir. Şemanızda ve sözleşmenizde doğru ileti türüne başvurmazsanız şu hata iletisini alırsınız:

`"The message has an unknown document type and did not resolve to any of the existing schemas configured in the agreement."`

Bu tablo, etkilenen ileti türlerini, tüm türevlerini ve bu ileti türleriyle eşlenen belge sürüm numaralarını listeler:

| İleti türü veya varyantı |  Description | Belge sürüm numarası (GS8) |
|-------------------------|--------------|-------------------------------|
| 277 | Sağlık bilgileri bilgi durumu bildirimi | 005010X212 |
| 837_I | Sağlık hizmetleri talep kurum | 004010X096A1 <br>005010X223A1 <br>005010X223A2 |
| 837_D | Sağlık Hizmetleri talebi eğilimi | 004010X097A1 <br>005010X224A1 <br>005010X224A2 |
| 837_P | Sağlık hizmetleri talep uzmanı | 004010X098A1 <br>005010X222 <br>005010X222A1 |
|||

Ayrıca, bu belge sürümü numaralarını kullandığınızda EDI doğrulamasını devre dışı bırakmanız gerekir çünkü bu, karakter uzunluğunun geçersiz olduğunu belirten bir hatayla sonuçlanır.

Bu belge sürümü numaralarını ve ileti türlerini belirtmek için şu adımları izleyin:

1. HIPAA şemanızda, geçerli ileti türünü, kullanmak istediğiniz belge sürümü numarası için varyant ileti türü ile değiştirin.

   Örneğin, ileti türüyle belge sürümü numarasını kullanmak istediğinizi varsayalım `005010X222A1` `837` . Şemanızda her `"X12_00501_837"` değeri, yerine değeri ile değiştirin `"X12_00501_837_P"` .

   Şemanızı güncelleştirmek için şu adımları izleyin:

   1. Azure portal tümleştirme hesabınıza gidin. Şemanızı bulun ve indirin. İleti türünü değiştirin ve şema dosyasını yeniden adlandırın ve yeniden düzenlenen şemanızı tümleştirme hesabınıza yükleyin. Daha fazla bilgi için bkz. [şemaları düzenleme](../logic-apps/logic-apps-enterprise-integration-schemas.md#edit-schemas).

   1. Sözleşmenizin ileti ayarları ' nda, düzeltilen şemayı seçin.

1. Sözleşmenizin `schemaReferences` nesnesinde, belge sürüm numaranız ile eşleşen varyant iletisi türünü belirten başka bir giriş ekleyin.

   Örneğin, `005010X222A1` ileti türü için belge sürüm numarasını kullanmak istediğinizi varsayalım `837` . Sözleşmenizin `schemaReferences` Bu özellik ve değerleri içeren bir bölümü vardır:

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      }
   ]
   ```

   Bu `schemaReferences` bölümde, şu değerlere sahip başka bir giriş ekleyin:

   * `"messageId": "837_P"`
   * `"schemaVersion": "00501"`
   * `"schemaName": "X12_00501_837_P"`

   İşiniz bittiğinde, `schemaReferences` bölüm şöyle görünür:

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      },
      {
         "messageId": "837_P",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837_P"
      }
   ]
   ```

1. Sözleşmenizin ileti ayarlarında **, EDI doğrulama onay kutusunu** her ileti türü veya **varsayılan** değerleri kullanıyorsanız tüm ILETI türleri için kaldırarak EDI doğrulamasını devre dışı bırakın.

   ![Tüm ileti türleri veya her ileti türü için doğrulamayı devre dışı bırak](./media/logic-apps-enterprise-integration-x12/x12-disable-validation.png) 

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bu bağlayıcı hakkında, bağlayıcının Swagger dosyasında açıklanan eylemler ve sınırlamalar gibi ek teknik ayrıntılar için [bağlayıcının başvuru sayfasına](/connectors/x12/)bakın.

> [!NOTE]
> Bir [tümleştirme hizmeti ortamındaki (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)Logic Apps için, bu bağlayıcının Ise etiketli sürümü [Ise için B2B ileti sınırlarını](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits)kullanır.

## <a name="next-steps"></a>Sonraki adımlar

* [Logic Apps için diğer bağlayıcılar](../connectors/apis-list.md) hakkında bilgi edinin
