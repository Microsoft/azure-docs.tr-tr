---
title: 'Hızlı başlangıç: Azure Machine Learning çalışma alanını bağlama'
description: SYNAPSE çalışma alanınızı Azure Machine Learning çalışma alanına bağlama
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 800cbf9b7a7fa415452f29b253347188c8917c52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98219462"
---
# <a name="quickstart-create-a-new-azure-machine-learning-linked-service-in-synapse"></a>Hızlı başlangıç: SYNAPSE 'de yeni Azure Machine Learning bağlantılı hizmet oluşturma

Bu hızlı başlangıçta, bir Azure SYNAPSE Analytics çalışma alanını Azure Machine Learning çalışma alanına bağlayacaksınız. Bu çalışma alanlarını bağlamak, SYNAPSE içindeki çeşitli deneyimlerden Azure Machine Learning yararlanmanızı sağlar.

Örneğin, bu Azure Machine Learning çalışma alanına bağlama Bu deneyimlere olanak sağlar:

- Azure Machine Learning işlem hatlarınızı SYNAPSE işlem hatlarınız içinde bir adım olarak çalıştırın. Daha fazla bilgi için bkz. [Azure Machine Learning işlem hatlarını yürütme](../../data-factory/transform-data-machine-learning-service.md).

- Azure Machine Learning modeli kayıt defterinden bir makine öğrenimi modeli gerçekleştirerek ve SYNAPSE SQL havuzlarında modeli puanlayarak tahminlerle verilerinizi zenginleştirin. Daha ayrıntılı bilgi için bkz. [öğretici: SYNAPSE SQL havuzları Için makine öğrenimi modeli Puanlama Sihirbazı](tutorial-sql-pool-model-scoring-wizard.md).

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/).
- Varsayılan depolama alanı olarak yapılandırılmış bir ADLS 2. depolama hesabıyla [SYNAPSE Analytics çalışma alanı](../get-started-create-workspace.md) . Birlikte çalıştığınız ADLS 2. FileSystem 'ın **Depolama Blobu veri katılımcısı** olması gerekir.
- [Azure Machine Learning çalışma alanı](../../machine-learning/how-to-manage-workspace.md).
- Bağlı hizmeti oluşturmak için kullanabileceğiniz, bir hizmet sorumlusu ve gizli dizi oluşturmak için izinlere (veya izinleri olan birisinden gelen istek) ihtiyacınız vardır. Bu hizmet sorumlusunun Azure Machine Learning Çalışma Alanı katkıda bulunan rolüne atanması gerektiğini unutmayın.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalda](https://portal.azure.com/) oturum açma

## <a name="create-a-service-principal"></a>Hizmet sorumlusu oluşturma

Bu adım, yeni bir hizmet sorumlusu oluşturacak. Mevcut bir hizmet sorumlusunu kullanmak istiyorsanız, bu adımı atlayabilirsiniz.
1. Azure portalını açın. 

1. **Azure Active Directory**  ->  **uygulama kayıtları** gidin.

1. **Yeni kayıt**’a tıklayın. Ardından, yeni bir uygulama kaydetmek için Kullanıcı arabirimindeki yönergeleri izleyin.

1. Uygulama kaydedildikten sonra. Uygulama için bir gizli dizi oluşturun. Gizli dizi   ->  **& uygulama sertifikanıza** gidin. Gizli dizi oluşturmak için **istemci parolası Ekle** ' ye tıklayın. Gizli anahtarı güvende tutun ve daha sonra kullanılacaktır.

   ![Gizli dizi oluştur](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00a.png)

1. Uygulama için bir hizmet sorumlusu oluşturun. **Uygulamanıza**  ->  **genel bakış** ' a gidip **hizmet sorumlusu oluştur**' a tıklayın. Bazı durumlarda, bu hizmet sorumlusu otomatik olarak oluşturulur.

   ![Hizmet sorumlusu oluşturma](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00b.png)

1. Hizmet sorumlusunu, Azure Machine Learning çalışma alanının "katkıda bulunan" olarak ekleyin. Bunun, Azure Machine Learning çalışma alanının ait olduğu kaynak grubunun sahibi olmasını gerektirdiğini unutmayın.

   ![Katkıda bulunan rolü atama](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-createsp-00c.png)

## <a name="create-a-linked-service"></a>Bağlı hizmet oluşturma

1. Yeni Azure Machine Learning bağlı hizmetini oluşturmak istediğiniz SYNAPSE çalışma alanında **Yönetim**  ->  **bağlantılı hizmeti**' ne gidin, "Azure Machine Learning" türünde yeni bir bağlı hizmet oluşturun.

   ![Bağlı hizmet oluştur](media/quickstart-integrate-azure-machine-learning/quickstart-integrate-azure-machine-learning-create-linked-service-00a.png)

2. Formu doldurun:

   - Hizmet sorumlusu KIMLIĞI: uygulamanın **uygulama (istemci) kimliğidir** .
  
     > [!NOTE]
     > Bu, uygulamanın adı DEĞILDIR. Bu KIMLIĞI uygulamanın genel bakış sayfasında bulabilirsiniz. Bu "81707eac-ab38-406u-8F6C-10ce76a568d5" şuna benzer bir dize olmalıdır.

   - Hizmet sorumlusu anahtarı: önceki bölümde oluşturulan gizli dizi.

3. Yapılandırmanın doğru olup olmadığını doğrulamak için **Bağlantıyı Sına** ' ya tıklayın. Bağlantı testi geçerse **Kaydet**' e tıklayın.

   Bağlantı testi başarısız olduysa, hizmet sorumlusu KIMLIĞI ve parolasının doğru olduğundan emin olun ve yeniden deneyin.

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: Machine Learning model Puanlama Sihirbazı-adanmış SQL havuzu](tutorial-sql-pool-model-scoring-wizard.md)
- [Azure Synapse Analytics'te Makine Öğrenmesi özellikleri](what-is-machine-learning.md)