---
title: Google bağlayıcıları için veri güvenliği ve gizlilik ilkeleri
description: Google güvenlik ve gizlilik ilkelerinin Gmail gibi Google bağlayıcılarına sahip olduğu etkileri hakkında bilgi edinin Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: 2a5204be638f108b40e431b148c9cb97788c4a52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91400766"
---
# <a name="data-security-and-privacy-policies-for-google-connectors-in-azure-logic-apps"></a>Azure Logic Apps 'de Google bağlayıcıları için veri güvenliği ve gizlilik ilkeleri

**1 mayıs 2020**' den Itibaren, Google 'ın [veri güvenliği ve gizlilik ilkeleri](https://www.blog.google/technology/safety-security/project-strobe/) nedeniyle yapılan değişiklikler [Gmail bağlayıcısını](/connectors/gmail/)kullanan mantıksal uygulama iş akışlarınızı etkileyebilir. Mantıksal uygulamalarınız Gmail bağlayıcısını bir Gmail tüketicisi hesabı (veya ile biten e-posta adresi) ile kullanıyorsa @gmail.com @googlemail.com , Logic Apps yalnızca belirli [Google-onaylanan Tetikleyiciler, Eylemler ve bağlayıcılar](#approved-connectors)kullanabilir.

> [!NOTE]
> Mantıksal uygulamalarınız Gmail bağlayıcısını bir G-Suite iş hesabıyla (özel bir etki alanı olan e-posta adresi) kullanıyorsa, mantıksal uygulamalarınız etkilenmez ve Gmail bağlayıcısını kullanma konusunda hiçbir kısıtlama yoktur.

## <a name="affected-logic-apps"></a>Etkilenen Logic Apps

Gmail bağlayıcısını kullanan Logic Apps varsa, potansiyel olarak etkilenen Logic Apps hakkında bir e-posta alırsınız. Ancak, **15 haziran 2020**' den itibaren uyumlu olmayan tüm iş akışları devre dışı bırakılacaktır. Şu eylemlerden birini gerçekleştirebilirsiniz:

* Etkilenen mantık uygulamalarını, [Bu konudaki adımları izleyerek](#update-affected-workflows)güncelleştirin. Gmail tetikleyicinizin veya eyleminizdeki kimlik doğrulaması için kullandığınız istemci KIMLIĞI ve gizli anahtar sağlayan bir Google istemci uygulaması oluşturmanız gerekir.

* Etkilenen mantık uygulamalarını, devre dışı bırakılan Logic Apps 'i yeniden etkinleştirmeden önce yalnızca [Google onaylı bağlayıcıları](#approved-connectors) kullanacak şekilde güncelleştirin.

<a name="approved-connectors"></a>

## <a name="google-approved-connectors"></a>Google onaylı bağlayıcılar

Bu ilke altında, bir Gmail tüketici hesabı kullandığınızda Gmail bağlayıcısını yalnızca belirli Google onaylı hizmetlerle (değiştirilebilir) kullanabilirsiniz. Mühendislik takımlarımız, bu listeye daha fazla hizmet eklemek için Google ile çalışmaya devam eder. Şimdilik, Gmail tüketici hesabı kullandığınızda Gmail Bağlayıcısı ile aynı Logic App iş akışında kullanabileceğiniz Google onaylı Tetikleyiciler, Eylemler ve bağlayıcılar aşağıda verilmiştir:

* Logic Apps yerleşik Tetikleyiciler ve eylemler: Batch, Control, veri Işlemleri, tarih saat, düz dosya, sıvı, Istek, zamanlama, değişkenler ve XML

  HTTP, Azure Işlevleri, Azure Logic Apps ve diğerleri gibi Google tarafından onaylanmamış yerleşik Tetikleyiciler ve Eylemler, uygulama her yerden veri gönderebildiğinden ve alabileceği için Gmail bağlayıcısıyla uyumlu olmayan bir mantıksal uygulama oluşturun.

* Google hizmetleri: Gmail, Google Takvim, Google Contacts, Google Drive, Google sayfaları ve Google görevleri

* Onaylanan Microsoft Hizmetleri: Dynamics 365, Excel Online, Microsoft ekipleri, Microsoft 365, OneDrive ve SharePoint Online

* Müşteri tarafından yönetilen veri kaynakları için bağlayıcılar: FTP, RSS, SFTP, SMTP ve SQL Server

## <a name="non-compliant-examples"></a>Uyumlu olmayan örnekler

Aşağıda, Google tarafından onaylanmamış yerleşik Tetikleyiciler ve eylemler ya da yönetilen bağlayıcılar ile Gmail bağlayıcısını kullanan bazı örnekler verilmiştir:

* Bu mantıksal uygulama, HTTP yerleşik tetikleyicisiyle Gmail bağlayıcısını kullanır:

  ![Uyumlu olmayan mantıksal uygulama-örnek 1](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-1.png)
  
  Mantıksal uygulama, onaylanan Google Takvim bağlayıcısını de kullanır.

* Bu mantıksal uygulama, Azure Blob depolama Bağlayıcısı ile Gmail bağlayıcısını kullanır:

  ![Uyumlu olmayan mantıksal uygulama-örnek 2](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-2.png)

* Bu mantıksal uygulama, Twitter Bağlayıcısı ile Gmail bağlayıcısını kullanır:

  ![Uyumlu olmayan mantıksal uygulama-örnek 3](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-3.png)

En son bilgiler için [Gmail bağlayıcısının teknik başvuru belgelerine](/connectors/gmail/)bakın.

<a name="update-affected-workflows"></a>

## <a name="steps-for-affected-logic-apps"></a>Etkilenen Logic Apps için adımlar

Bir mantıksal uygulamada Gmail adlı bir tüketici hesabı ve Google onaylanmamış bağlayıcılar ile Gmail bağlayıcısını kullanmanız gerekiyorsa, kuruluşunuzda kişisel veya dahili kullanım için kendi Google uygulamanızı oluşturabilirsiniz. Bu senaryo için yapmanız gereken üst düzey adımlar şunlardır:

1. [Google API konsolunu](https://console.developers.google.com)kullanarak bir Google istemci uygulaması oluşturun.

1. Gmail bağlayıcısında, Google istemci uygulamanızdan istemci KIMLIĞI ve istemci gizli değerlerini kullanın.

Daha fazla bilgi için [Gmail bağlayıcısının teknik başvuru belgelerine](/connectors/gmail/#authentication-and-bring-your-own-application)bakın.

### <a name="create-google-client-app"></a>Google istemci uygulaması oluşturma

İstemci uygulamanız için bir proje kurmak üzere [Google API konsol Sihirbazı 'nı](https://console.developers.google.com/start/api?id=gmail&credential=client_key) kullanın ve yönergeleri izleyin. Ya da, ayrıntılı adımlar için [Gmail bağlayıcısının teknik başvuru belgelerindeki](/connectors/gmail/#authentication-and-bring-your-own-application)yönergeleri gözden geçirin.

İşiniz bittiğinde, ekranınızda daha sonra mantıksal uygulamanızda kullanacağınız kendi **ISTEMCI kimliğiniz** ve **istemci gizli** değerleriniz olacak şekilde bu örneğe benzer şekilde görünür.

![Google istemci uygulamanız için istemci KIMLIĞI ve istemci gizli anahtarı](./media/connectors-google-data-security-privacy-policy/google-api-console.png)

### <a name="use-client-app-settings-in-logic-app"></a>Mantıksal uygulamada istemci uygulama ayarlarını kullan

Google istemci uygulamanızdan Gmail Tetikleyiciniz veya eyleminizdeki istemci KIMLIĞINI ve istemci gizli anahtarını kullanmak için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com)mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

1. Yeni bir Gmail tetikleyicisi veya eylemi ekliyorsanız ve tamamen yeni bir bağlantı oluşturuyorsanız, sonraki adımla devam edin. Aksi takdirde, Gmail tetikleyicisinde veya eyleminde **Bağlantıyı Değiştir**  >  **Yeni Ekle**' yi seçin, örneğin:

   !["Bağlantıyı değiştir" > "Yeni Ekle" seçeneğini belirleyin](./media/connectors-google-data-security-privacy-policy/change-gmail-connection.png)

1. Bağlantı bilgilerinizi sağlayın, örneğin:

   ![Bağlantı bilgilerini girin](./media/connectors-google-data-security-privacy-policy/authentication-type-bring-your-own.png)

   | Özellik | Değer | Açıklama |
   |----------|-------|-------------|
   | **Kimlik doğrulama türü** | **Kendi uygulamanızı getir** | Kimlik doğrulaması için kendi istemci uygulamanızı kullanacağınızı belirtir. |
   | **İstemci Kimliği** | <*istemci KIMLIĞI*> | Google istemci uygulamanızdan istemci KIMLIĞI |
   | **İstemci parolası** | <*istemci parolası*> | Google istemci uygulamanızdan istemci parolası |
   ||||

1. İşiniz bittiğinde **oturum aç**' ı seçin.

   Oluşturduğunuz istemci uygulamasını gösteren bir sayfa görüntülenir. Gmail tüketici hesabı kullanıyorsanız, istemci uygulamanızın Google tarafından doğrulanmayacağını gösteren bir sayfa alabilir ve önce Google hesabınıza erişime izin vermenizi ister.

   ![Google hesabınıza erişim istemi](./media/connectors-google-data-security-privacy-policy/allow-access-authorized-domain.png)

1. Gerekirse, **Izin ver**' i seçin.

   Artık, mantıksal uygulamanızda kısıtlama olmadan Gmail bağlayıcısını kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Gmail Bağlayıcısı](/connectors/gmail/) hakkında daha fazla bilgi edinin

