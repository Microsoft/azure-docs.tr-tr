---
title: Uygulamanızı kaydetme-Özel Karar Alma Hizmeti
titlesuffix: Azure Cognitive Services
description: Azure Özel Karar Alma Hizmeti ile yeni bir uygulamanın nasıl kaydedileceği adım adım kılavuz.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ms.reviewer: marcozo
ROBOTS: NOINDEX
ms.openlocfilehash: 4a8e4e91e768e3ad28ea9ff88c18515db4a40323
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704561"
---
# <a name="register-your-application"></a>Uygulamanızı kaydedin

Uygulamanız için Özel Karar Alma Hizmeti kullanmak için portala kaydolun. Bu makalede nasıl yapılacağı açıklanır.

1. Özel Karar Alma Hizmeti [ön sayfasına](https://portal.ds.microsoft.com/) gidin. Şeritte, görüntüde vurgulanan şekilde **Portalm**' e tıklayın:

    ![Portalm](./media/portal.png)

    Henüz oturum açmadıysanız, portal [Microsoft hesabı](https://account.microsoft.com/account)oturum açmanızı ister. Oturum açtıktan sonra Portal, Microsoft hesabı sayfanın sağ üst köşesinde görüntüler.

2. Uygulamanızı kaydetmek için **Yeni uygulama** düğmesine tıklayın.

3. İletişim kutusunda, uygulamanız için bir uygulama KIMLIĞI seçin. Özel Karar Alma Hizmeti her uygulama için benzersiz bir KIMLIK gerektirir. Başka birisi bu KIMLIĞI zaten gerçekleştirmişse, sistem farklı bir tane seçmenizi ister.

4. Bir eylem kümesi API 'SI belirtin. Bu ayar, uygulamanızın Özel Karar Alma Hizmeti için kullanılabilir içeriği iletişim kuran bir RSS veya Atom akışdır. Akış için bir ad girin ve hizmet verilen URL 'YI girin. Bu adımı daha sonra yapmak için **akışlar** düğmesine tıklayın ve ardından **Yeni akış** düğmesine tıklayın. RSS akışı oluşturan bir örnek daha sonra açıklanmaktadır.

5. Uygulamanızı kaydetmek için sol alt köşedeki **özel uygulama** onay kutusunu seçin. Uygulama verilerinizin günlüğe kaydedildiği Azure depolama hesabı için bir [bağlantı dizesi](../../storage/common/storage-configure-connection-string.md) girin. Depolama hesabı oluşturma hakkında daha fazla bilgi için bkz. [depolama hesabı oluşturma, yönetme veya silme](../../storage/common/storage-create-storage-account.md).

### <a name="next-steps"></a>Sonraki adımlar

* [Bir web sayfasını](custom-decision-service-get-started-browser.md) veya [akıllı telefon uygulamasını](custom-decision-service-get-started-app.md) iyileştirmeye başlayın.
* Daha ayrıntılı bir örnek için [öğreticide](custom-decision-service-tutorial-news.md) çalışın.
* Sağlanan işlevler hakkında daha fazla bilgi edinmek için [API başvurusu](custom-decision-service-api-reference.md)’na bakın.
