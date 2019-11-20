---
title: Gizli dizi değişikliklerinin Key Vault durumu olduğunda e-posta
description: Key Vault gizli dizi değişikliklerine yanıt vermek için Logic Apps kullanma kılavuzu
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 7ccc4aaed8e5827fbc06b252c8c88b814d9a31fb
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74135022"
---
# <a name="use-logic-apps-to-receive-email-about-status-changes-of-key-vault-secrets"></a>Anahtar Kasası gizliliklerin durum değişiklikleriyle ilgili e-posta almak için Logic Apps kullanın

Bu kılavuzda, [Azure Logic Apps](../logic-apps/index.yml)kullanarak [Azure Event Grid](../event-grid/index.yml) aracılığıyla alınan Azure Key Vault olaylarına nasıl yanıt verileceğini öğreneceksiniz. Son olarak, Azure Key Vault her gizli dizi oluşturulduğunda bildirim e-postası gönderecek şekilde ayarlanmış bir Azure Logic App 'e sahip olursunuz.

Azure Key Vault/Azure Event Grid tümleştirmesine genel bakış için bkz. [Azure Event Grid Ile izleme Key Vault (Önizleme)](event-grid-overview.md).

## <a name="prerequisites"></a>Önkoşullar

- Azure Logic Apps tarafından desteklenen herhangi bir e-posta sağlayıcısından (Office 365 Outlook gibi) bir e-posta hesabı. Bu e-posta hesabı olay bildirimlerini göndermek için kullanılır. Desteklenen Logic App bağlayıcılarının tam listesi için bkz. [Bağlayıcılara genel bakış](/connectors)
- Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.
- Azure aboneliğinizdeki bir Anahtar Kasası. [Azure CLI kullanarak Azure Key Vault bir gizli anahtarı ayarlama ve alma](quick-create-cli.md)bölümündeki adımları izleyerek hızlı bir şekilde yeni bir Anahtar Kasası oluşturabilirsiniz.

## <a name="create-a-logic-app-via-event-grid"></a>Event Grid aracılığıyla mantıksal uygulama oluşturma

İlk olarak, Event Grid işleyicisiyle mantıksal uygulama oluşturun ve "SecretNewVersionCreated" olaylarını Azure Key Vault abone olun.

Azure Event Grid aboneliği oluşturmak için aşağıdaki adımları izleyin:

1. Aşağıdaki bağlantıyı kullanarak Azure portal açın: https://portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true 
1. Azure portal, anahtar kasanıza gidin, **olay > Başlarken** ' i seçin ve **Logic Apps** ' ye tıklayın.

    
    ![Key Vault Olaylar sayfası](./media/eventgrid-logicapps-kvsubs.png)

1. **Logic Apps tasarımcı** bağlantıyı doğrulayıp **devam** ' a tıklayın. 
 
    ![Mantıksal uygulama Tasarımcısı-bağlantı](./media/eventgrid-logicappdesigner1.png)

1. **Bir kaynak olayı gerçekleştiğinde** , şunları yapın:
    - **Aboneliği** ve **kaynak adını** varsayılan olarak bırakın.
    - **Kaynak türü**için **Microsoft. keykasası. kaults** ' ı seçin.
    - **Olay türü öğesi-1**için **Microsoft. Keykasası. SecretNewVersionCreated** öğesini seçin.

    ![Logic App Designer-olay işleyicisi](./media/eventgrid-logicappdesigner2.png)

1. **+ Yeni adım** ' ı seçin Bu işlem bir eylem seçmek üzere bir pencere açar.
1. **E-posta**'yı arayın. E-posta sağlayıcınıza uygun bağlayıcıyı bulun ve seçin. Bu öğreticide **Office 365 Outlook** kullanılır. Diğer e-posta sağlayıcılarının adımları da bunlara benzer.
1. **E-posta gönder (v2)** eylemini seçin.

   ![Mantıksal uygulama Tasarımcısı-e-posta Ekle](./media/eventgrid-logicappdesigner3.png)

1. E-posta şablonunuzu oluşturun:
    - **Şunları yapmak için:** Bildirim e-postalarını alacak e-posta adresini girin. Bu öğreticide, test etmek için erişebileceğiniz bir e-posta hesabı kullanın.
    - **Konu** ve **Gövde**: E-postanızın metnini yazın. Olay verileri temelinde dinamik içerik eklemek için seçici aracından JSON özelliklerini seçin. `@{triggerBody()?['Data']}`kullanarak olayın verilerini alabilirsiniz.

    E-posta şablonunuz, bu örneğe benzeyebilir.

    ![Mantıksal uygulama Tasarımcısı-e-posta Ekle](./media/eventgrid-logicappdesigner4.png)

8. **Farklı kaydet**' e tıklayın.
9. Yeni mantıksal uygulama için bir **ad** girin ve **Oluştur**' a tıklayın.
    
    ![Mantıksal uygulama Tasarımcısı-e-posta Ekle](./media/eventgrid-logicappdesigner5.png)

## <a name="test-and-verify"></a>Test ve doğrulama

1.  Azure portal anahtar kasanıza gidin ve **olay abonelikleri > olaylar**' ı seçin.  Yeni bir aboneliğin oluşturulduğunu doğrulama
    
    ![Mantıksal uygulama Tasarımcısı-e-posta Ekle](./media/eventgrid-logicapps-kvnewsubs.png)

1.  Anahtar kasanıza gidin, **gizlilikler**' ı seçin ve **+ Oluştur/al**' ı seçin. Sınama amacıyla yeni bir gizli dizi oluşturun anahtarı adlandırın ve varsayılan ayarlarında kalan parametreleri saklayın.

    ![Key Vault-gizli dizi oluştur](./media/eventgrid-logicapps-kv-create-secret.png)

1. Gizli dizi **Oluştur** ekranında herhangi bir ad ve herhangi bir değer girip **Oluştur**' u seçin.

Gizli dizi oluşturulduğunda, yapılandırılan adreslerde bir e-posta alınır.

## <a name="next-steps"></a>Sonraki adımlar

- Genel Bakış: [Azure Event Grid Key Vault izleme (Önizleme)](event-grid-overview.md)
- Nasıl yapılır: [Anahtar Kasası bildirimlerini Azure Otomasyonu 'Na yönlendirme](event-grid-tutorial.md).
- [Azure Key Vault için Azure Event Grid olay şeması (Önizleme)](../event-grid/event-schema-key-vault.md)
- [Azure Event Grid](../event-grid/index.yml) hakkında daha fazla bilgi edinin.
- [Azure App Service’in Logic Apps özelliği](../logic-apps/index.yml) hakkında daha fazla bilgi edinin.
