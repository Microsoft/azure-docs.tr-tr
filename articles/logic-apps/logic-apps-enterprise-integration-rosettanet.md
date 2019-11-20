---
title: B2B tümleştirmesi için RosettaNet iletileri-Azure Logic Apps
description: Enterprise Integration Pack ile Azure Logic Apps 'de Exchange RosettaNet iletileri
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 570c7907f320b881e2db0bd45cdce311490f4f45
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680327"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Azure Logic Apps 'de B2B kurumsal tümleştirme için Exchange RosettaNet iletileri

[RosettaNet](https://resources.gs1us.org) , iş bilgilerini paylaşmak için standart süreçler oluşturmuş olan bir kar dışı konsorsiyum. Bu standartlar, genellikle tedarik zinciri işlemlerinde kullanılır ve yarı iletken, elektronik ve lojistik sektörlerinde yaygın olarak bulunur. RosettaNet Consortium, tüm RosettaNet iletileri alışverişi için ortak iş süreci tanımları sağlayan Iş ortağı arabirimi Işlemlerini (PII 'ler) oluşturur ve korur. RosettaNet, XML 'i temel alır ve ileti kılavuzlarını, iş süreçlerinin arabirimlerini ve şirketler arasında iletişim için uygulama çerçevelerini tanımlar.

[Azure Logic Apps](../logic-apps/logic-apps-overview.md), RosettaNet Bağlayıcısı RosettaNet standartlarını destekleyen tümleştirme çözümleri oluşturmanıza yardımcı olur. Bağlayıcı, RosettaNet uygulama çerçevesi (RNBU) sürüm 2.0.01 tabanlıdır. RN, iş ortaklarının RosettaNet 'Leri işbirliği yapmasına olanak sağlayan açık bir ağ uygulama çerçevesidir. Bu çerçeve ileti yapısını, bildirimler gereksinimini, çok amaçlı Internet posta uzantıları (MIME) kodlamasını ve dijital imzayı tanımlar.

Özellikle, bağlayıcı şu özellikleri sağlar:

* RosettaNet iletilerini kodlayın veya alın.
* RosettaNet iletileri kodunu çözün veya gönderin.
* Hata bildiriminin yanıt ve oluşturulmasını bekleyin.

Bu yetenekler için bağlayıcı, RNıF 2.0.01 tarafından tanımlanan tüm IFS 'Leri destekler. İş ortağıyla iletişim zaman uyumlu veya zaman uyumsuz olabilir.

## <a name="rosettanet-concepts"></a>RosettaNet kavramları

Aşağıda, RosettaNet belirtiminin benzersiz olduğu ve RosettaNet tabanlı tümleştirmeler oluşturulurken önemli olan bazı kavramlar ve şartlar verilmiştir:

* **PIP**

  RosettaNet organizasyonu, tüm RosettaNet iletileri alışverişi için ortak iş süreci tanımları sağlayan Iş ortağı arabirim Işlemlerini (PII 'ler) oluşturur ve korur. Her PıP belirtimi bir belge türü tanımı (DTD) dosyası ve bir ileti kılavuz belgesi sağlar. DTD dosyası, hizmet içeriği ileti yapısını tanımlar. İnsan tarafından okunabilen bir HTML dosyası olan ileti Kılavuzu belgesi, öğe düzeyi kısıtlamaları belirtir. Birlikte, bu dosyalar iş sürecinin tamamen tanımını sağlar.

   Ifler, üst düzey bir iş işlevine, kümeye, bir alt işleve veya kesime göre kategorize edilir. Örneğin, "3A4", satın alma siparişi için nokta, "3" ise sipariş yönetimi işlevi, "3A" ise quote & Order entry alt işlevidir. Daha fazla bilgi için bkz. [RosettaNet sitesi](https://resources.gs1us.org).

* **Eylem**

  Bir PıP 'nin bir parçası olan eylem iletileri, iş ortakları arasında değiş tokuş edilen iş iletilerdir.

* **Sinyalinin**

   Bir PıP 'nin parçası olarak, sinyal iletilerinin işlem iletilerine yanıt olarak gönderilen bildirimler vardır.

* **Tek eylem ve çift eylem**

  Tek bir eylem PıP 'si için tek yanıt bir bildirim sinyali iletisidir. Bir çift eylem PıP 'si için, başlatıcı bir yanıt iletisi alır ve tek işlem ileti akışına ek olarak bir bildirim ile yanıt verir.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Henüz bir Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* Sözleşmenizi ve diğer B2B yapıtlarını depolamak için bir [tümleştirme hesabı](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) . Bu tümleştirme hesabının Azure aboneliğinizle ilişkilendirilmesi gerekir.

* Tümleştirme hesabınızda tanımlanmış ve **Iş kimlikleri** altında "dlanlar" niteleyicisi ile yapılandırılmış en az iki [iş ortağı](../logic-apps/logic-apps-enterprise-integration-partners.md)

* Tümleştirme hesabınızda RosettaNet iletileri göndermek veya almak için gerekli olan bir PıP işlem yapılandırması. İşlem yapılandırması tüm PıP yapılandırma özelliklerini depolar. Daha sonra iş ortağıyla bir sözleşme oluşturduğunuzda bu yapılandırmaya başvurabilirsiniz. Tümleştirme hesabınızda bir PıP işlem yapılandırması oluşturmak için, bkz. [PIP işlem yapılandırması ekleme](#add-pip).

* Tümleştirme hesabına yüklediğiniz iletileri şifrelemek, şifrelerini çözmek veya imzalamak için isteğe bağlı [Sertifikalar](../logic-apps/logic-apps-enterprise-integration-certificates.md) . Sertifikalar yalnızca imzalama veya şifreleme kullandığınızda gereklidir.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>PıP işlem Yapılandırması Ekle

Tümleştirme hesabınıza bir PıP işlem yapılandırması eklemek için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com)tümleştirme hesabınızı bulun ve açın.

1. **Genel bakış** bölmesinde, **RosettaNet PI** kutucuğunu seçin.

   ![RosettaNet kutucuğunu seçin](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. **RosettaNet PIP**altında **Ekle**' yi seçin. PıP ayrıntılarınızı sağlayın.

   ![RosettaNet PıP ayrıntıları ekleme](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | Özellik | Gereklidir | Açıklama |
   |----------|----------|-------------|
   | **Adı** | Yes | PıP adınız |
   | **PıP kodu** | Yes | PıP üç basamaklı kod. Daha fazla bilgi için bkz. [RosettaNet PII](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips). |
   | **PıP sürümü** | Yes | Seçtiğiniz PıP kodunuz temelinde kullanılabilir olan PıP sürüm numarası |
   ||||

   Bu PıP özellikleri hakkında daha fazla bilgi için, [RosettaNet Web sitesini](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg)ziyaret edin.

1. İşiniz bittiğinde, PıP yapılandırmasını oluşturan **Tamam**' ı seçin.

1. İşlem yapılandırmasını görüntülemek veya düzenlemek için, PıP 'yi seçin ve **JSON olarak Düzenle**' yi seçin.

   Tüm işlem yapılandırma ayarları, PıP belirtimlerinden gelir. Logic Apps ayarların çoğunu, bu özellikler için en yaygın olarak kullanılan değerler olan varsayılan değerlerle doldurur.

   ![RosettaNet PıP yapılandırmasını düzenle](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. Ayarların uygun PıP belirtiminde bulunan değerlere karşılık geldiğinden ve iş gereksinimlerinizi karşıladığından emin olun. Gerekirse, JSON 'daki değerleri güncelleştirin ve bu değişiklikleri kaydedin.

## <a name="create-rosettanet-agreement"></a>RosettaNet sözleşmesi oluşturma

1. [Azure Portal](https://portal.azure.com), henüz açık değilse, tümleştirme hesabınızı bulun ve açın.

1. **Genel bakış** bölmesinde **anlaşmalar** kutucuğunu seçin.

   ![Anlaşmalar kutucuğunu seçin](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. **Sözleşmeler**altında **Ekle**' yi seçin. Anlaşma ayrıntılarınızı belirtin.

   ![Anlaşma ayrıntıları Ekle](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | Özellik | Gereklidir | Açıklama |
   |----------|----------|-------------|
   | **Adı** | Yes | Sözleşmenin adı |
   | **Anlaşma türü** | Yes | **RosettaNet**' i seçin. |
   | **Ana bilgisayar ortağı** | Yes | Bir anlaşma hem konak hem de Konuk iş ortağı gerektirir. Ana bilgisayar ortağı, sözleşmeyi yapılandıran kuruluşu temsil eder. |
   | **Ana bilgisayar kimliği** | Yes | Ana bilgisayar ortağı için bir tanımlayıcı |
   | **Konuk Iş ortağı** | Yes | Bir anlaşma hem konak hem de Konuk iş ortağı gerektirir. Konuk iş ortağı, ana bilgisayar ortağıyla iş yapan kuruluşu temsil eder. |
   | **Konuk kimliği** | Yes | Konuk iş ortağı için bir tanımlayıcı |
   | **Ayarları al** | Değişir | Bu özellikler, ana bilgisayar ortağı tarafından alınan tüm iletiler için geçerlidir |
   | **Ayarları gönder** | Değişir | Bu özellikler, ana bilgisayar ortağı tarafından gönderilen tüm iletiler için geçerlidir |  
   | **RosettaNet PıP başvuruları** | Yes | Anlaşmanın PıP başvuruları. Tüm RosettaNet iletileri, PıP yapılandırması gerektirir. |
   ||||

1. Konuk iş ortağından gelen iletileri alma sözleşmenizi ayarlamak için, **alma ayarları**' nı seçin.

   ![Ayarları al](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. Gelen iletilerde imzalamayı veya şifrelemeyi etkinleştirmek için **iletiler**altında **ileti Imzalı** olmalıdır veya **ileti sırasıyla şifrelenmelidir** .

      | Özellik | Gereklidir | Açıklama |
      |----------|----------|-------------|
      | **İleti imzalanmalıdır** | Hayır | Seçili sertifikayla gelen iletileri imzala. |
      | **Sertifika** | Evet, imzalama etkinse | İmzalama için kullanılacak sertifika |
      | **İleti şifrelemesini etkinleştir** | Hayır | Seçilen sertifikayla gelen iletileri şifreleyin. |
      | **Sertifika** | Evet, şifreleme etkinse | Şifreleme için kullanılacak sertifika |
      ||||

   1. Her seçim altında, imzalama veya şifreleme için kullanmak üzere daha önce tümleştirme hesabınıza eklediğiniz ilgili [sertifikayı](./logic-apps-enterprise-integration-certificates.md)seçin.

1. Konuk iş ortağına ileti gönderme sözleşmenizi ayarlamak için **ayarları gönder**' i seçin.

   ![Ayarları gönder](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. Giden iletiler için imzalamayı veya şifrelemeyi etkinleştirmek üzere **iletiler**altında **ileti imzalamayı etkinleştir** veya **ileti şifrelemeyi etkinleştir** ' i seçin. Her seçim altında, imzalama veya şifreleme için kullanmak üzere daha önce tümleştirme hesabınıza eklediğiniz ilgili algoritmayı ve [sertifikayı](./logic-apps-enterprise-integration-certificates.md)seçin.

      | Özellik | Gereklidir | Açıklama |
      |----------|----------|-------------|
      | **İleti imzalamayı etkinleştir** | Hayır | Giden iletileri seçilen imzalama algoritması ve sertifikasıyla imzalayın. |
      | **İmzalama algoritması** | Evet, imzalama etkinse | Seçilen sertifikaya göre kullanılacak imzalama algoritması |
      | **Sertifika** | Evet, imzalama etkinse | İmzalama için kullanılacak sertifika |
      | **İleti şifrelemesini etkinleştir** | Hayır | Seçili şifreleme algoritması ve sertifikasıyla giden bir şekilde şifreleyin. |
      | **Şifreleme algoritması** | Evet, şifreleme etkinse | Seçilen sertifikaya göre kullanılacak şifreleme algoritması |
      | **Sertifika** | Evet, şifreleme etkinse | Şifreleme için kullanılacak sertifika |
      ||||

   1. **Uç noktalar**altında, eylem iletileri ve bildirimleri göndermek için kullanılacak gerekli URL 'leri belirtin.

      | Özellik | Gereklidir | Açıklama |
      |----------|----------|-------------|
      | **Eylem URL 'SI** |  Yes | Eylem iletilerini göndermek için kullanılacak URL. URL, hem zaman uyumlu hem de zaman uyumsuz iletiler için gerekli bir alandır. |
      | **Bildirim URL 'SI** | Yes | Bildirim iletilerini göndermek için kullanılacak URL. URL, zaman uyumsuz iletiler için gerekli bir alandır. |
      ||||

1. İş ortakları için RosettaNet PıP başvuruları ile sözleşmenizi ayarlamak için **ROSETTANET PIP başvuruları**' nı seçin. **PIP adı**altında, önceden oluşturduğunuz PIP adı ' nı seçin.

   ![PıP başvuruları](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   Seçiminiz, tümleştirme hesabınızda ayarladığınız PıP 'yi temel alan kalan özellikleri doldurur. Gerekirse, **PIP rolünü**değiştirebilirsiniz.

   ![Seçili PıP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

Bu adımları tamamladıktan sonra, RosettaNet iletileri göndermeye veya almaya hazırsınız demektir.

## <a name="rosettanet-templates"></a>RosettaNet şablonları

Geliştirme sürecini hızlandırmak ve tümleştirme desenleri önermek için, RosettaNet iletilerinin kodunu çözmek ve kodlamak amacıyla mantıksal uygulama şablonları kullanabilirsiniz. Mantıksal uygulama oluşturduğunuzda, mantıksal uygulama Tasarımcısı ' nda şablon Galerisi ' nden seçim yapabilirsiniz. Ayrıca, bu şablonları [Azure Logic Apps Için GitHub deposunda](https://github.com/Azure/logicapps)bulabilirsiniz.

![RosettaNet şablonları](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>RosettaNet iletileri alma veya kodunu çözme

1. [Boş bir mantıksal uygulama oluşturun](quickstart-create-first-logic-app-workflow.md).

1. [Tümleştirme hesabınızı](logic-apps-enterprise-integration-create-integration-account.md#link-account) mantıksal uygulamanıza bağlayın.

1. RosettaNet iletisinin kodunu çözmeye yönelik bir eylem ekleyebilmek için önce, bir Istek tetikleyicisi gibi mantıksal uygulamanızı başlatmak için bir tetikleyici eklemeniz gerekir.

1. Tetikleyiciyi ekledikten sonra **yeni adım**' ı seçin.

   ![Istek tetikleyicisi Ekle](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Arama kutusuna "RosettaNet" yazın ve şu eylemi seçin: **RosettaNet kodunu çöz**

   !["RosettaNet şifre çözme" eylemi bulun ve seçin](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. Eylemin özellikleriyle ilgili bilgileri girin:

   ![Eylem ayrıntılarını belirtin](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | Özellik | Gereklidir | Açıklama |
   |----------|----------|-------------|
   | **İleti** | Yes | Kodu çözülecek RosettaNet iletisi  |
   | **Üst Bilgiler** | Yes | RNZE sürümü olan sürüm için değerler sağlayan HTTP üstbilgileri ve iş ortakları arasındaki iletişim türünü gösteren ve zaman uyumlu veya zaman uyumsuz olabilecek yanıt türü. |
   | **Rol** | Yes | PıP 'de ana bilgisayar ortağının rolü |
   ||||

   RosettaNet kod çözme eyleminde, çıkış, diğer özelliklerle birlikte, kodlamayı kodlamak ve geri dönmek ya da bu çıktıda başka bir işlem yapmak için seçebileceğiniz **giden sinyali**içerir.

## <a name="send-or-encode-rosettanet-messages"></a>RosettaNet iletileri gönderin veya kodlayın

1. [Boş bir mantıksal uygulama oluşturun](quickstart-create-first-logic-app-workflow.md).

1. [Tümleştirme hesabınızı](logic-apps-enterprise-integration-create-integration-account.md#link-account) mantıksal uygulamanıza bağlayın.

1. RosettaNet iletisini kodlamak için bir eylem ekleyebilmek için önce, bir Istek tetikleyicisi gibi mantıksal uygulamanızı başlatmak için bir tetikleyici eklemeniz gerekir.

1. Tetikleyiciyi ekledikten sonra **yeni adım**' ı seçin.

   ![Istek tetikleyicisi Ekle](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. Arama kutusuna "RosettaNet" yazın ve şu eylemi seçin: **RosettaNet encode**

   !["RosettaNet encode" eylemini bulun ve seçin](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. Eylemin özellikleriyle ilgili bilgileri girin:

   ![Eylem ayrıntılarını belirtin](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | Özellik | Gereklidir | Açıklama |
   |----------|----------|-------------|
   | **İleti** | Yes | Kodlanacak RosettaNet iletisi  |
   | **Ana bilgisayar ortağı** | Yes | Ana bilgisayar iş ortağı adı |
   | **Konuk iş ortağı** | Yes | Konuk iş ortağı adı |
   | **PıP kodu** | Yes | PıP kodu |
   | **PıP sürümü** | Yes | PıP sürümü |  
   | **PıP örnek kimliği** | Yes | Bu PıP iletisi için benzersiz tanımlayıcı |  
   | **İleti türü** | Yes | Kodlanacak iletinin türü |  
   | **Rol** | Yes | Ana bilgisayar ortağının rolü |
   ||||

   Kodlanmış ileti artık iş ortağına gönderilmeye hazırdır.

1. Kodlanan iletiyi göndermek için bu örnek, "HTTP-kodlanmış ileti gönder" olarak yeniden adlandırılan **http** eylemini kullanır.

   ![RosettaNet iletisi göndermek için HTTP eylemi](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   RosettaNet standartları başına, iş işlemleri yalnızca PıP tarafından tanımlanan tüm adımlar tamamlandığında tamamlanmış olarak kabul edilir.

1. Ana bilgisayar kodlanmış iletiyi ortağa gönderdikten sonra, ana bilgisayar sinyal ve bildirim bekler. Bu görevi gerçekleştirmek için, **Yanıt Için RosettaNet wait** eylemini ekleyin.

   !["Yanıt karşılığında RosettaNet wait" eylemini Ekle](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   Bekleme için kullanılacak süre ve yeniden deneme sayısı, tümleştirme hesabınızdaki PıP yapılandırmasına dayanır. Yanıt alınmıyorsa, bu eylem bir hata bildirimi oluşturur. Yeniden denemeleri işlemek için, her zaman **kodlamayı** koyun ve bir **until** döngüsünde **Yanıt eylemlerini bekleyin** .

   ![RosettaNet eylemleriyle döngü Until](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) ile doğrulama, dönüştürme ve diğer ileti işlemlerini nasıl doğrulayacağınızı öğrenin
* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
