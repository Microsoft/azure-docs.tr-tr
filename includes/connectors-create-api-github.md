---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 03/02/2018
ms.openlocfilehash: 1aabc1b33722c571c6519b6620fb62211a44c2e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95999560"
---
1. [Azure Portal](https://portal.azure.com), boş bir mantıksal uygulama oluşturun. 

2. Logic Apps tasarımcısında filtreniz olarak "GitHub" yazın. 

3. GitHub bağlayıcısını ve kullanmak istediğiniz tetikleyiciyi seçin.

   ![GitHub bağlayıcısını ve bir tetikleyiciyi seçin](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Tüm mantıksal uygulama iş akışlarının bir tetikleyiciyle başlaması gerekir. Yalnızca mantık iş akışınız zaten bir tetikleyiciyle başlatıldığında Eylemler ' i seçebilirsiniz. 

4. Daha önce bir bağlantı oluşturmadıysanız, istendiğinde GitHub kimlik bilgilerinizi sağlayabilmeniz için **oturum aç** ' ı seçin.  

   ![GitHub kimlik bilgilerinizle oturum açın](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   Mantıksal uygulamanız, GitHub hesabınıza yönelik verileri bağlamayı ve bunlara erişmeyi yetkilendirmek için bu kimlik bilgilerini kullanır. 

5. GitHub Kullanıcı adınızı ve parolanızı girip yetkilendirmeyi onaylayın.

   ![Kimlik bilgilerini sağlayın ve yetkilendirmeyi onaylayın](./media/connectors-create-api-github/github-connector-authorize.png)   

   Bağlantınız artık Azure portal oluşturulmuştur ve kullanıma hazırdır.

6. Mantıksal uygulama iş akışınızı tanımlamaya devam edin.

   ![Mantıksal uygulama iş akışınıza daha fazla eylem ekleyin](./media/connectors-create-api-github/github-connector-logic-app.png)

