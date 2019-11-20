---
title: B2B tümleştirmesi için x12 iletileri-Azure Logic Apps
description: Enterprise Integration Pack ile Azure Logic Apps B2B kurumsal tümleştirme için EDI biçiminde Exchange x12 iletileri
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 01/31/2017
ms.openlocfilehash: 8bc5c458240925af1fdd74ebc9b2d4c3db71fb05
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679992"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Enterprise Integration Pack ile Azure Logic Apps B2B kurumsal tümleştirme için Exchange x12 iletileri

Azure Logic Apps için x12 iletileri alışverişi yapabilmeniz için önce bir x12 sözleşmesi oluşturmanız ve bu sözleşmeyi tümleştirme hesabınızda depolamanız gerekir. X12 sözleşmesinin nasıl oluşturulacağı hakkında adımlar aşağıda verilmiştir.

> [!NOTE]
> Bu sayfa Azure Logic Apps için x12 özelliklerini içerir. Daha fazla bilgi için bkz. [Ediolgu](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Başlamadan önce

İhtiyacınız olan öğeler aşağıda verilmiştir:

* Zaten tanımlanmış ve Azure aboneliğinizle ilişkilendirilen bir [tümleştirme hesabı](logic-apps-enterprise-integration-create-integration-account.md)
* Tümleştirme hesabınızda tanımlanan ve **Iş kimlikleri** altında x12 tanımlayıcısı ile yapılandırılmış en az iki [iş ortağı](../logic-apps/logic-apps-enterprise-integration-partners.md)    
* Tümleştirme hesabınıza yükleyebileceğiniz gerekli bir [şema](../logic-apps/logic-apps-enterprise-integration-schemas.md)

[Tümleştirme hesabı oluşturup](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) [iş ortakları ekleyin](logic-apps-enterprise-integration-partners.md)ve kullanmak istediğiniz bir [şemaya](../logic-apps/logic-apps-enterprise-integration-schemas.md) sahip olduktan sonra, bu adımları izleyerek bir x12 sözleşmesi oluşturabilirsiniz.

## <a name="create-an-x12-agreement"></a>X12 sözleşmesi oluşturma

1. [Azure Portal](https://portal.azure.com "Azure portalı")’ında oturum açın. 

2. Ana Azure menüsünde **tüm hizmetler**' i seçin. 
   Arama kutusuna "tümleştirme" yazın ve ardından **tümleştirme hesapları**' nı seçin.  

   ![Tümleştirme hesabınızı bulun](./media/logic-apps-enterprise-integration-x12/account-1.png)

   > [!TIP]
   > **Tüm hizmetler** görünmezse, önce menüyü genişletmeniz gerekebilir. Daraltılan menünün en üstünde **menüyü göster**' i seçin.

3. **Tümleştirme hesapları**altında, sözleşmeyi eklemek istediğiniz tümleştirme hesabını seçin.

   ![Anlaşmanın oluşturulacağı tümleştirme hesabını seçin](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. **Genel bakış**' ı seçin, sonra **anlaşmalar** kutucuğunu seçin. 
   Anlaşma kutucuğuna sahipseniz, önce kutucuğu ekleyin. 

   !["Anlaşmalar" kutucuğunu seçin](./media/logic-apps-enterprise-integration-x12/agreement-1.png)

5. **Sözleşmeler**altında **Ekle**' yi seçin.

   !["Ekle" yi seçin](./media/logic-apps-enterprise-integration-x12/agreement-2.png)     

6. **Ekle**' nin altına sözleşmeniz Için bir **ad** girin. 
   Anlaşma türü için **x12**öğesini seçin. 
   Sözleşmeniz için **konak ortağını**, **ana bilgisayar kimliğini**, **Konuk iş ortağını**ve **Konuk kimliğini** seçin. 
   Daha fazla özellik ayrıntısı için bu adımdaki tabloya bakın.

    ![Anlaşma ayrıntılarını sağlama](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Özellik | Açıklama |
    | --- | --- |
    | Adı |Sözleşmenin adı |
    | Anlaşma türü | X12 olmalıdır |
    | Ana bilgisayar ortağı |Bir anlaşmanın hem ana bilgisayar hem de Konuk iş ortağı olması gerekir. Ana bilgisayar ortağı, sözleşmeyi yapılandıran kuruluşu temsil eder. |
    | Ana bilgisayar kimliği |Ana bilgisayar ortağı için bir tanımlayıcı |
    | Konuk Iş ortağı |Bir anlaşmanın hem ana bilgisayar hem de Konuk iş ortağı olması gerekir. Konuk iş ortağı, ana bilgisayar ortağıyla iş yapan kuruluşu temsil eder. |
    | Konuk kimliği |Konuk iş ortağı için bir tanımlayıcı |
    | Ayarları al |Bu özellikler, bir sözleşme tarafından alınan tüm iletiler için geçerlidir. |
    | Ayarları gönder |Bu özellikler, bir anlaşma tarafından gönderilen tüm iletiler için geçerlidir. |  

   > [!NOTE]
   > X12 sözleşmesinin çözümlenmesi, gönderici niteleyicisi ve tanımlayıcı ile, iş ortağında ve gelen iletide tanımlanan alıcı niteleyicisi ve tanımlayıcı ile eşleşmesine bağlıdır. Bu değerler iş ortağınız için değişiklik içeriyorsa sözleşmeyi de güncelleştirin.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Anlaşmanızın alınan iletileri nasıl işleyeceğini yapılandırın

Anlaşma özelliklerini ayarlamış olduğunuza göre, bu sözleşmenin bu sözleşme aracılığıyla iş ortağınızdan alınan gelen iletileri nasıl tanımladığını ve işleyeceğini yapılandırabilirsiniz.

1.  **Ekle**' nin altında, **alma ayarları**' nı seçin.
Sizinle ileti alışverişi yapan iş ortağıyla anlaşmanıza bağlı olarak bu özellikleri yapılandırın. Özellik açıklamaları için bu bölümdeki tablolara bakın.

    **Alma ayarları** şu bölümler halinde düzenlenmiştir: tanımlayıcılar, bildirim, şemalar, zarflar, denetim numaraları, doğrulamalar ve iç ayarlar.

2. İşiniz bittiğinde, **Tamam**' ı seçerek ayarlarınızı kaydettiğinizden emin olun.

Artık sözleşmeniz, seçili ayarlarınıza uygun gelen iletileri işlemeye hazır.

### <a name="identifiers"></a>Lara

![Tanımlayıcı özelliklerini ayarlama](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Özellik | Açıklama |
| --- | --- |
| ISA1 (yetkilendirme niteleyicisi) |Açılan listeden yetkilendirme niteleyicisi değerini seçin. |
| ISA2 |İsteğe bağlı. Yetkilendirme bilgileri değerini girin. ISA1 için girdiğiniz değer 00 ' dan büyükse, en az bir alfasayısal karakter ve en fazla 10 değeri girin. |
| ISA3 (güvenlik niteleyicisi) |Açılan listeden güvenlik niteleyicisi değerini seçin. |
| ISA4 |İsteğe bağlı. Güvenlik bilgileri değerini girin. ISA3 için girdiğiniz değer 00 ' dan büyükse, en az bir alfasayısal karakter ve en fazla 10 değeri girin. |

### <a name="acknowledgment"></a>Olumlu

![Onaylama özelliklerini ayarla](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Özellik | Açıklama |
| --- | --- |
| TA1 bekleniyor |Değiş tokuş göndericisine teknik bildirim döndürür |
| SK bekleniyor |Değişim göndericisine işlevsel bir bildirim döndürür. Ardından, şema sürümüne göre 997 veya 999 ile ilgili bildirimleri isteyip istemediğinizi seçin |
| Include AK2/IK2 döngüsü |Kabul edilen işlem kümeleri için işlevsel AK2 döngülerinin oluşturulmasına izin vermez |

### <a name="schemas"></a>Şemalar

Her işlem türü (ST1) ve gönderici uygulaması (GS2) için bir şema seçin. Alma işlem hattı, gelen iletideki ST1 ve GS2 değerlerini, burada ayarladığınız değerlerle ve gelen iletinin şemasını burada ayarladığınız şemayla eşleştirerek gelen iletiyi ayrıştırır.

![Şema seçin](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Özellik | Açıklama |
| --- | --- |
| Sürüm |X12 sürümünü seçin |
| İşlem türü (ST01) |İşlem türünü seçin |
| Gönderen uygulaması (GS02) |Gönderen uygulamasını seçin |
| Şema |Kullanmak istediğiniz şema dosyasını seçin. Şemalar tümleştirme hesabınıza eklenir. |

> [!NOTE]
> [Tümleştirme hesabınıza](../logic-apps/logic-apps-enterprise-integration-accounts.md)yüklenen gerekli [Şemayı](../logic-apps/logic-apps-enterprise-integration-schemas.md) yapılandırın.

### <a name="envelopes"></a>Larla

![Bir işlem kümesinde ayırıcıyı belirtin: standart tanımlayıcı veya yineleme ayırıcısı seçin](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Özellik | Açıklama |
| --- | --- |
| ISA11 kullanımı |Bir işlem kümesinde kullanılacak ayırıcıyı belirtir: <p>**Standart tanımlayıcıyı** , EDI alma işlem hattındaki gelen belgenin ondalık gösterimi yerine ondalık Gösterim için bir nokta (.) kullanmak üzere seçin. <p>Basit bir veri öğesinin yinelenen tekrarlarının veya yinelenen bir veri yapısının ayırıcısını belirtmek için **yineleme ayırıcısı** ' nı seçin. Örneğin, genellikle simgeyi seçtiğinizde (^), yineleme ayırıcısı olarak kullanılır. HIPAA şemaları için, yalnızca Carat 'yı kullanabilirsiniz. |

### <a name="control-numbers"></a>Denetim numaraları

![Denetim numarası yinelemelerini nasıl işleyeceğinizi seçin](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Özellik | Açıklama |
| --- | --- |
| Değişim denetim numarası çoğaltmasına izin verme |Yinelenen değişiklikleri engelleyin. Alınan değişim denetim numarası için değişim denetim numarasını (ISA13) denetler. Bir eşleşme algılanırsa, alma işlem hattı değişimi işlemez. *Her (gün) YINELENEN ISA13 için bir denetim*değeri vererek denetim gerçekleştirmek için gereken gün sayısını belirtebilirsiniz. |
| Grup denetim numarası yinelenmesine izin verme |Yinelenen grup denetim numaralarıyla karşılıklı değişiklikleri engelleyin. |
| Işlem kümesi denetim numarası yinelenmesine izin verme |Yinelenen işlem kümesi denetim numaralarıyla karşılıklı değişiklikleri engelleyin. |

### <a name="validations"></a>Ların

![Alınan iletiler için doğrulama özelliklerini ayarlama](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

Her doğrulama satırını tamamladığınızda, başka bir otomatik olarak eklenir. Herhangi bir kural belirtmezseniz, doğrulama "varsayılan" satırını kullanır.

| Özellik | Açıklama |
| --- | --- |
| İleti türü |EDI ileti türünü seçin. |
| EDI doğrulaması |Şema EDI özellikleri, uzunluk kısıtlamaları, boş veri öğeleri ve sondaki ayırıcılar tarafından tanımlanan veri türlerinde EDI doğrulaması gerçekleştirin. |
| Genişletilmiş Doğrulama |Veri türü EDI değilse, doğrulama veri öğesi gereksinimidir ve izin verilen yineleme, numaralandırmalar ve veri öğesi uzunluğu doğrulaması (min/max). |
| Baştaki/sondaki sıfırları izin ver |Tüm ek öndeki veya sondaki sıfır ve boşluk karakterlerini koruyun. Bu karakterleri kaldırmayın. |
| Baştaki/sondaki sıfırları Kırp |Baştaki veya sondaki sıfır ve boşluk karakterlerini kaldırın. |
| Sondaki ayırıcı Ilkesi |Sondaki ayırıcılar oluştur. <p>Alınan değişim içindeki sonda sınırlayıcılar ve ayırıcılara **Izin verilmeyeceğine Izin verilmiyor** ' ı seçin. Değişim, sonda sınırlayıcılar ve ayırıcılar içeriyorsa, değişim geçerli değil olarak bildirilmiştir. <p>Sondaki sınırlayıcılarla veya Ayırıcısız değişiklikler kabul etmek için **Isteğe bağlı** ' i seçin. <p>Değişim, sonda sınırlayıcılar ve ayırıcılara sahip olması gerektiğinde **zorunlu** ' ı seçin. |

### <a name="internal-settings"></a>İç ayarlar

![İç ayarları seçin](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Özellik | Açıklama |
| --- | --- |
| Kapsanan "nn" ondalık biçimini 10 tabanında sayısal değere Dönüştür |"Nn" biçimiyle belirtilen EDI numarasını Base-10 sayısal değerine dönüştürür |
| Sondaki ayırıcılara izin veriliyorsa boş XML etiketleri oluştur |Bu onay kutusunu, değişim göndericisinin sondaki ayırıcılar için boş XML etiketleri içermesini sağlamak için seçin. |
| Değişimi işlem kümeleri olarak Böl-hata durumunda işlem kümelerini askıya al|Bir değişim içindeki her işlem kümesini, işlem kümesine uygun zarfı uygulayarak ayrı bir XML belgesi olarak ayrıştırır. Yalnızca doğrulamanın başarısız olduğu işlemleri askıya alır. |
| Değişimi işlem kümeleri olarak Böl-hata durumunda değişimi askıya al|Bir değiş tokuş içindeki her işlem kümesini, uygun zarfı uygulayarak ayrı bir XML belgesi olarak ayrıştırır. Değişim başarısız doğrulamasında bir veya daha fazla işlem kümesi olduğunda tüm değişimi askıya alır. | 
| Değişimi koru-hata durumunda işlem kümelerini askıya al |Değişimi dokunulmadan bırakır, tüm toplu değişim için bir XML belgesi oluşturur. Yalnızca doğrulamanın başarısız olduğu işlem kümelerini askıya alırken, diğer tüm işlem kümelerini işlemeye devam eder. |
| Değişimi koru-hata durumunda değişimi askıya al |Değişimi dokunulmadan bırakır, tüm toplu değişim için bir XML belgesi oluşturur. Değişim başarısız doğrulamasında bir veya daha fazla işlem kümesi olduğunda tüm değişimi askıya alır. |

## <a name="configure-how-your-agreement-sends-messages"></a>Sözleşmenizin iletileri nasıl göndereceğini yapılandırın

Bu sözleşmenin, bu anlaşma aracılığıyla iş ortağınıza göndereceğiniz giden iletileri nasıl tanımladığını ve işleyeceğini yapılandırabilirsiniz.

1.  **Ekle**' nin altında, **ayarları gönder**' i seçin.
Sizinle ileti alışverişi yapan iş ortağınız ile anlaşmanıza bağlı olarak bu özellikleri yapılandırın. Özellik açıklamaları için bu bölümdeki tablolara bakın.

    **Gönderme ayarları** şu bölümler halinde düzenlenmiştir: tanımlayıcılar, bildirim, şemalar, zarflar, karakter kümeleri ve ayırıcılar, denetim numaraları ve doğrulama.

2. İşiniz bittiğinde, **Tamam**' ı seçerek ayarlarınızı kaydettiğinizden emin olun.

Artık sözleşmeniz, seçili ayarlarınıza uygun olan giden iletileri işlemeye hazır.

### <a name="identifiers"></a>Lara

![Tanımlayıcı özelliklerini ayarlama](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Özellik | Açıklama |
| --- | --- |
| Yetkilendirme niteleyicisi (ISA1) |Açılan listeden yetkilendirme niteleyicisi değerini seçin. |
| ISA2 |Yetkilendirme bilgileri değerini girin. Bu değer 00 ' dan büyükse, en az bir alfasayısal karakter ve en fazla 10 girin. |
| Güvenlik niteleyicisi (ISA3) |Açılan listeden güvenlik niteleyicisi değerini seçin. |
| ISA4 |Güvenlik bilgileri değerini girin. Bu değer 00 ' dan başka bir değer (ISA4) metin kutusu için en az bir alfasayısal değer ve en fazla 10 girin. |

### <a name="acknowledgment"></a>Olumlu

![Onaylama özelliklerini ayarla](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Özellik | Açıklama |
| --- | --- |
| TA1 bekleniyor |Değiş tokuş göndericisine teknik onay (TA1) döndürün. Bu ayar, iletiyi gönderen ana bilgisayar ortağının anlaşmada Konuk ortağından bir bildirim istemesi gerektiğini belirtir. Bu bildirimler, sözleşmenin alma ayarlarına bağlı olarak ana bilgisayar ortağı tarafından beklenir. |
| SK bekleniyor |Değişim göndericisine bir işlevsel bildirim (SK) döndürün. Üzerinde çalıştığınız şema sürümlerine göre 997 veya 999 ' nin ne olduğunu belirlemek istediğinizi seçin. Bu bildirimler, sözleşmenin alma ayarlarına bağlı olarak ana bilgisayar ortağı tarafından beklenir. |
| SK sürümü |SK sürümünü seçin |

### <a name="schemas"></a>Şemalar

![Kullanılacak şemayı seçin](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Özellik | Açıklama |
| --- | --- |
| Sürüm |X12 sürümünü seçin |
| İşlem türü (ST01) |İşlem türünü seçin |
| MANıZı |Kullanılacak şemayı seçin. Şemalar tümleştirme hesabınızda bulunur. Önce şemayı seçerseniz, sürümü ve işlem türünü otomatik olarak yapılandırır  |

> [!NOTE]
> [Tümleştirme hesabınıza](../logic-apps/logic-apps-enterprise-integration-accounts.md)yüklenen gerekli [Şemayı](../logic-apps/logic-apps-enterprise-integration-schemas.md) yapılandırın.

### <a name="envelopes"></a>Larla

![Bir işlem kümesinde ayırıcıyı belirtin: standart tanımlayıcı veya yineleme ayırıcısı seçin](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Özellik | Açıklama |
| --- | --- |
| ISA11 kullanımı |Bir işlem kümesinde kullanılacak ayırıcıyı belirtir: <p>**Standart tanımlayıcıyı** , EDI alma işlem hattındaki gelen belgenin ondalık gösterimi yerine ondalık Gösterim için bir nokta (.) kullanmak üzere seçin. <p>Basit bir veri öğesinin yinelenen tekrarlarının veya yinelenen bir veri yapısının ayırıcısını belirtmek için **yineleme ayırıcısı** ' nı seçin. Örneğin, genellikle simgeyi seçtiğinizde (^), yineleme ayırıcısı olarak kullanılır. HIPAA şemaları için, yalnızca Carat 'yı kullanabilirsiniz. |

### <a name="control-numbers"></a>Denetim numaraları

![Denetim numarası özelliklerini belirtin](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Özellik | Açıklama |
| --- | --- |
| Denetim sürüm numarası (ıSA12) |X12 Standard sürümünü seçin |
| Kullanım göstergesi (ISA15) |Bir değişim bağlamını seçin.  Değerler bilgi, üretim verileri veya test verileri |
| Şema |Send işlem hattına gönderdiği x12 kodlu bir değişim için GS ve ST segmentlerini üretir |
| GS1 |İsteğe bağlı, açılan listeden işlevsel kod için bir değer seçin |
| GS2 |İsteğe bağlı, uygulama gönderici |
| GS3 |İsteğe bağlı, uygulama alıcısı |
| GS4 |İsteğe bağlı, CCYYMMDD veya YYAAGG seçin |
| GS5 |İsteğe bağlı, SSMM, HHMMSS veya HHMMSSdd seçin |
| GS7 |İsteğe bağlı, açılan listeden sorumlu ajanı için bir değer seçin |
| GS8 |İsteğe bağlı, belge sürümü |
| Değişim denetim numarası (ISA13) |Gerekli, değişim denetim numarası için bir değer aralığı girin. En az 1 ve en fazla 999999999 olan sayısal bir değer girin |
| Grup denetim numarası (GS06) |Gerekli, Grup denetim numarası için bir sayı aralığı girin. En az 1 ve en fazla 999999999 olan sayısal bir değer girin |
| İşlem kümesi denetim numarası (ST02) |Gerekli, Işlem kümesi denetim numarası için bir sayı aralığı girin. En az 1 ve en fazla 999999999 olan bir sayısal değer aralığı girin |
| Alan kodu |Bildirim içinde kullanılan işlem kümesi denetim numaraları aralığı için belirlenmiş isteğe bağlı. İkinci iki alan için sayısal bir değer ve önek ve sonek alanları için alfasayısal bir değer (isteniyorsa) girin. Orta alanlar gereklidir ve denetim numarası için en düşük ve en yüksek değerleri içermelidir |
| Önekini |Bir bildirim içinde kullanılan işlem kümesi denetim numaraları aralığı için belirlenmiş isteğe bağlı. İkinci iki alan için sayısal bir değer ve önek ve sonek alanları için alfasayısal bir değer (isteniyorsa) girin. Orta alanlar gereklidir ve denetim numarası için en düşük ve en yüksek değerleri içermelidir |

### <a name="character-sets-and-separators"></a>Karakter kümeleri ve ayırıcılar

Karakter kümesi dışında, her ileti türü için farklı bir sınırlayıcı kümesi girebilirsiniz. Belirli bir ileti şeması için bir karakter kümesi belirtilmemişse, varsayılan karakter kümesi kullanılır.

![İleti türleri için sınırlandırıcıları belirt](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Özellik | Açıklama |
| --- | --- |
| Kullanılacak karakter kümesi |Özellikleri doğrulamak için x12 karakter kümesini seçin. Temel, genişletilmiş ve UTF8 seçenekleri vardır. |
| Şema |Açılan listeden bir şema seçin. Her satırı tamamladıktan sonra, yeni bir satır otomatik olarak eklenir. Seçili şema için, aşağıdaki ayırıcı açıklamalara göre kullanmak istediğiniz ayırıcılar kümesini seçin. |
| Giriş türü |Açılan listeden bir giriş türü seçin. |
| Bileşen ayırıcısı |Bileşik veri öğelerini ayırmak için tek bir karakter girin. |
| Veri öğesi ayırıcısı |Bileşik veri öğeleri içindeki basit veri öğelerini ayırmak için tek bir karakter girin. |
| Değiştirme karakteri |Giden x12 iletisini oluştururken yük verilerinde tüm ayırıcı karakterleri değiştirmek için kullanılan bir değiştirme karakteri girin. |
| Segment Sonlandırıcı |EDI segmentinin sonunu belirtmek için tek bir karakter girin. |
| Önekini |Kesim tanımlayıcısıyla birlikte kullanılan karakteri seçin. Bir sonek belirlerseniz, segment Sonlandırıcı veri öğesi boş olabilir. Segment Sonlandırıcı boş bırakılırsa, bir sonek atamanız gerekir. |

> [!TIP]
> Özel karakter değerleri sağlamak için, sözleşmeyi JSON olarak düzenleyin ve özel karakter için ASCII değeri sağlayın.

### <a name="validation"></a>Doğrulama

![İleti göndermek için doğrulama özelliklerini ayarlama](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

Her doğrulama satırını tamamladığınızda, başka bir otomatik olarak eklenir. Herhangi bir kural belirtmezseniz, doğrulama "varsayılan" satırını kullanır.

| Özellik | Açıklama |
| --- | --- |
| İleti türü |EDI ileti türünü seçin. |
| EDI doğrulaması |Şema EDI özellikleri, uzunluk kısıtlamaları, boş veri öğeleri ve sondaki ayırıcılar tarafından tanımlanan veri türlerinde EDI doğrulaması gerçekleştirin. |
| Genişletilmiş Doğrulama |Veri türü EDI değilse, doğrulama veri öğesi gereksinimidir ve izin verilen yineleme, numaralandırmalar ve veri öğesi uzunluğu doğrulaması (min/max). |
| Baştaki/sondaki sıfırları izin ver |Tüm ek öndeki veya sondaki sıfır ve boşluk karakterlerini koruyun. Bu karakterleri kaldırmayın. |
| Baştaki/sondaki sıfırları Kırp |Baştaki veya sondaki sıfır karakterleri kaldırın. |
| Sondaki ayırıcı Ilkesi |Sondaki ayırıcılar oluştur. <p>Gönderilen değişim içindeki sonda ayırıcıları ve ayırıcıları yasakla ' ya **Izin verilmiyor** ' ı seçin. Değişim, sonda sınırlayıcılar ve ayırıcılar içeriyorsa, değişim geçerli değil olarak bildirilmiştir. <p>Sondaki sınırlayıcılar ve ayırıcılar olmadan veya bunlarla karşılıklı değişiklikler göndermek için **Isteğe bağlı** ' yı seçin. <p>Gönderilen değişim için sonda sınırlayıcılar ve ayırıcılar olması gerekiyorsa **zorunlu** ' ı seçin. |

## <a name="find-your-created-agreement"></a>Oluşturulan sözleşmeyi bulun

1.  Tüm anlaşma özelliklerinizi ayarlamayı bitirdikten sonra, **Ekle** sayfasında, sözleşmenizi oluşturmayı tamamladıktan sonra tümleştirme hesabınıza geri dönmek için **Tamam** ' ı seçin.

    Yeni eklenen sözleşmeniz artık **anlaşmalar** listenizde görünür.

2.  Ayrıca, tümleştirme hesabınıza genel bakış konusunda anlaşmalar da görüntüleyebilirsiniz. Tümleştirme hesabı menüsünde **genel bakış**' ı seçin, sonra **anlaşmalar** kutucuğunu seçin.

    !["Anlaşmalar" kutucuğunu seçin](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="view-the-swagger"></a>Swagger 'yi görüntüleme
[Swagger ayrıntılarına](/connectors/x12/)bakın. 

## <a name="learn-more"></a>Daha fazla bilgi
* [Enterprise Integration Pack hakkında daha fazla bilgi edinin](../logic-apps/logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack hakkında bilgi edinin")  

