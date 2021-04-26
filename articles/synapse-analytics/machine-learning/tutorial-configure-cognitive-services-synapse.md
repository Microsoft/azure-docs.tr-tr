---
title: "Öğretici: Azure SYNAPSE Analytics 'te bilişsel hizmetler için Önkoşullar"
description: Azure SYNAPSE 'da bilişsel hizmetler 'i kullanmaya yönelik önkoşulları yapılandırmayı öğrenin.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 3ab861caca0ef6f58c2c1bc722412774deb725ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936675"
---
# <a name="tutorial-prerequisites-for-using-cognitive-services-in-azure-synapse-analytics"></a>Öğretici: Azure SYNAPSE Analytics 'te bilişsel hizmetler kullanma önkoşulları

Bu öğreticide, Azure SYNAPSE Analytics 'te Azure bilişsel hizmetler 'i kullanarak güvenli bir şekilde önkoşulları nasıl ayarlayacağınızı öğreneceksiniz.

Bu öğreticinin içindekiler:
> [!div class="checklist"]
> - Metin Analizi veya anomali algılayıcısı gibi bilişsel hizmetler kaynağı oluşturun.
> - Hizmet kaynaklarını Azure Key Vault gizli dizi olarak bilişsel hizmetler için bir kimlik doğrulama anahtarı depolayın ve bir Azure SYNAPSE Analytics çalışma alanı için erişimi yapılandırın.
> - Azure SYNAPSE Analytics çalışma alanınızda Azure Key Vault bağlı bir hizmet oluşturun.

Azure aboneliğiniz yoksa [başlamadan önce ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

- Varsayılan depolama alanı olarak yapılandırılmış bir Azure Data Lake Storage 2. depolama hesabıyla [Azure SYNAPSE Analytics çalışma alanı](../get-started-create-workspace.md) . Birlikte çalıştığınız Azure Data Lake Storage 2. dosya sisteminin *Depolama Blobu veri katılımcısı* olması gerekir.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="create-a-cognitive-services-resource"></a>Bilişsel Hizmetler kaynağı oluşturma

[Azure](../../cognitive-services/index.yml) bilişsel hizmetler pek çok hizmet türü içerir. Metin Analizi ve anomali algılayıcısı, Azure SYNAPSE öğreticilerinde iki örnektir.

Azure portal bir [metin analizi](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) kaynağı oluşturabilirsiniz:

![Portalda, oluştur düğmesiyle Metin Analizi gösteren ekran görüntüsü.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00b.png)

Azure portal bir [anomali algılayıcısı](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) kaynağı oluşturabilirsiniz:

![Portalda, Oluştur düğmesine sahip anomali algılayıcısının gösterildiği ekran görüntüsü.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00a.png)

## <a name="create-a-key-vault-and-configure-secrets-and-access"></a>Anahtar Kasası oluşturma ve gizli dizileri ve erişimi yapılandırma

1. Azure portal bir [Anahtar Kasası](https://ms.portal.azure.com/#create/Microsoft.KeyVault) oluşturun.
2. **Key Vault**  >  **erişim ilkelerine** gidin ve Azure Key Vault gizli dizileri okumak için [Azure SYNAPSE çalışma alanı MSI](../security/synapse-workspace-managed-identity.md) izinleri verin.

   > [!NOTE]
   > İlke değişikliklerinin kaydedildiğinden emin olun. Bu adımın kaçırılması kolaydır.

   ![Erişim ilkesi ekleme seçimlerini gösteren ekran görüntüsü.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00c.png)

3. Bilişsel hizmetler kaynağına gidin. Örneğin, **anomali algılayıcı**  >  **anahtarlarına ve uç noktaya** gidin. Ardından iki anahtardan birini Pano 'ya kopyalayın.

4.   >  Yeni bir gizli dizi oluşturmak için Key Vault **gizli** sayfasına gidin. Gizli anahtar adını belirtin ve ardından önceki adımdan **değeri değer** alanına yapıştırın. Son olarak **Oluştur**' u seçin.

   ![Gizli dizi oluşturma seçimlerini gösteren ekran görüntüsü.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00d.png)

   > [!IMPORTANT]
   > Bu gizli dizi adını anımsadığınızdan veya aklınızda olduğunuzdan emin olun. Daha sonra Azure SYNAPSE Studio 'dan bilişsel hizmetlere bağlandığınızda kullanacaksınız.

## <a name="create-an-azure-key-vault-linked-service-in-azure-synapse"></a>Azure 'da Azure Key Vault bağlantılı hizmet oluşturma SYNAPSE

1. Azure SYNAPSE Studio 'da çalışma alanınızı açın. 
2. Bağlı Hizmetleri **Yönet**' e gidin  >  . Yeni oluşturduğunuz anahtar kasasının üzerine gelerek **Azure Key Vault** bağlantılı bir hizmet oluşturun. 
3. Bağlantıyı **Sına** düğmesini seçerek bağlantıyı doğrulayın. Bağlantı yeşil ise **Oluştur** ' u seçin ve ardından **Tümünü Yayımla** ' yı seçerek değişiklerinizi kaydedin.

![Yeni bağlı hizmet olarak Azure Key Vault gösteren ekran görüntüsü.](media/tutorial-configure-cognitive-services/tutorial-configure-cognitive-services-00e.png)

Artık Azure SYNAPSE Studio 'da Azure bilişsel hizmetler deneyimini kullanmaya yönelik öğreticilerden biriyle devam etmeye hazırsınız.

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: Azure bilişsel hizmetler ile yaklaşım Analizi](tutorial-cognitive-services-sentiment.md)
- [Öğretici: Azure bilişsel hizmetler ile anomali algılama](tutorial-cognitive-services-sentiment.md)
- [Öğretici: Azure SYNAPSE ADANMıŞ SQL havuzlarında makine öğrenimi modeli Puanlama](tutorial-sql-pool-model-scoring-wizard.md).
- [Azure Synapse Analytics'te Makine Öğrenmesi özellikleri](what-is-machine-learning.md)