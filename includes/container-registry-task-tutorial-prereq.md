---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/02/2019
ms.author: danlep
ms.openlocfilehash: 79e2ca71d6b8178be63c3429edba89cf3bb523e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96030006"
---
## <a name="prerequisites"></a>Önkoşullar

### <a name="get-sample-code"></a>Örnek kodu alma

Bu öğreticide, [önceki öğreticide](../articles/container-registry/container-registry-tutorial-quick-task.md) yer alan adımları zaten tamamladığınız ve örnek deponun çatalını ve kopyasını oluşturduğunuz varsayılır. Henüz yapmadıysanız, devam etmeden önce önceki öğreticinin [Önkoşullar](../articles/container-registry/container-registry-tutorial-quick-task.md#prerequisites) bölümündeki adımları tamamlayın.

### <a name="container-registry"></a>Kapsayıcı kayıt defteri

Bu öğreticiyi tamamlamak için Azure aboneliğinizde bir Azure kapsayıcı kayıt defteri olması gerekir. Bir kayıt defterine ihtiyacınız varsa, [önceki öğreticiye](../articles/container-registry/container-registry-tutorial-quick-task.md) veya [Hızlı Başlangıç: Azure CLI kullanarak kapsayıcı kayıt defteri oluşturma](../articles/container-registry/container-registry-get-started-azure-cli.md) bölümüne bakın.

### <a name="create-a-github-personal-access-token"></a>GitHub kişisel erişim belirteci oluşturma

Bir git deposuna yapılan işlemede bir görevi tetiklemek için ACR görevlerinin depoya erişmek için bir kişisel erişim belirteci (PAT) gerekir. Zaten bir PAT yoksa GitHub 'da bir tane oluşturmak için aşağıdaki adımları izleyin:

1. GitHub üzerinde https://github.com/settings/tokens/new adresindeki PAT oluşturma sayfasında gidin
1. Belirteç için kısa bir **açıklama** girin; örneğin, "ACR Görevleri Tanıtımı"
1. ACR 'nin depoya erişmesi için kapsamlar ' ı seçin. Bu öğreticide olduğu gibi **genel depoya erişmek için depo altında** depoyu etkinleştirin **: durum** ve **public_repo**

   ![GitHub'da Kişisel Erişim Belirteci oluşturma sayfasının ekran görüntüsü][build-task-01-new-token]

   > [!NOTE]
   > *Özel* depoya erişmek üzere bir pat oluşturmak için tam **Depo** denetimi için kapsamı seçin.

1. **Belirteç Oluştur** düğmesini seçin (parolanızı onaylamanız istenebilir)
1. Oluşturulan belirteci kopyalayın ve **güvenli bir konuma** kaydedin (bu belirteci sonraki bölümde bir görev tanımlarken kullanacaksınız)

   ![GitHub'da oluşturulan Kişisel Erişim Belirtecinin ekran görüntüsü][build-task-02-generated-token]

<!-- Images -->
[build-task-01-new-token]: ./media/container-registry-task-tutorial-prereq/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-task-tutorial-prereq/build-task-02-generated-token.png
