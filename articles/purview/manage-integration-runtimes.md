---
title: Tümleştirme çalışma zamanları oluşturma ve yönetme
description: Bu makalede, Azure purview 'da tümleştirme çalışma zamanları oluşturma ve yönetme adımları açıklanır.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: 9276f845c95b5e736180159b282ddedc33523c17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99980754"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma ve yönetme

Bu makalede, Azure purview 'da veri kaynaklarını taramanızı sağlayan şirket içinde barındırılan bir tümleştirme çalışma zamanının (SHıR) nasıl oluşturulacağı ve yönetileceği açıklanmaktadır.

> [!NOTE]
> Purview Integration Runtime aynı makinede bir Azure SYNAPSE Analytics veya Azure Data Factory Integration Runtime paylaştırılamaz. Ayrılmış bir makineye yüklenmesi gerekir.

## <a name="create-a-self-hosted-integration-runtime"></a>Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma

1. Purview Studio 'nun giriş sayfasında sol gezinti bölmesinden **Yönetim Merkezi** ' ni seçin.

2. Sol bölmedeki **kaynaklar ve tarama** ' nın altında tümleştirme çalışma **zamanları**' nı seçin ve **+ Yeni**' yi seçin.

   :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="IR 'ye tıklayın.":::

3. **Tümleştirme çalışma zamanı kurulumu** sayfasında, BIR Self-Hosted IR oluşturmak için **Şirket içinde barındırılan** ' i seçin ve ardından **devam**' ı seçin.

   :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="Yeni bir dolgu oluşturun.":::

4. IR 'niz için bir ad girin ve Oluştur ' u seçin.

5. **Integration Runtime ayarları** sayfasında, **el ile kurulum** bölümünde yer alan adımları izleyin. Tümleştirme çalışma zamanını, yüklemeyi planladığınız bir VM 'ye veya makineye indirme sitesinden indirmeniz gerekir.

   :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="Anahtar al":::

   - Kimlik doğrulama anahtarını kopyalayıp yapıştırın.

   - Şirket içinde barındırılan tümleştirme çalışma zamanını yerel bir Windows makinesinde [Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) indirin. Yükleyiciyi çalıştırın.

   - **Kayıt Integration Runtime (Şirket içinde barındırılan)** sayfasında, daha önce kaydettiğiniz iki anahtardan birini yapıştırın ve **Kaydet**' i seçin.

     :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="giriş anahtarı.":::

   - **Yeni Integration Runtime (Şirket içinde barındırılan) düğümü** sayfasında **son**' u seçin.

6. Şirket içinde barındırılan tümleştirme çalışma zamanı başarıyla kaydedildikten sonra aşağıdaki pencereyi görürsünüz:

   :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="başarıyla kaydedildi.":::

## <a name="manage-a-self-hosted-integration-runtime"></a>Şirket içinde barındırılan tümleştirme çalışma zamanını yönetme

Şirket içinde barındırılan tümleştirme çalışma zamanını, **yönetim merkezindeki** **tümleştirme çalışma zamanları** ' na giderek, IR ' yi seçip Düzenle ' ye tıklayarak düzenleyebilirsiniz. Artık açıklamayı güncelleştirebilir, anahtarı kopyalayabilir veya yeni anahtarları yeniden oluşturabilirsiniz.

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="IR 'yi düzenleyin.":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="IR ayrıntılarını düzenleyin.":::

Şirket içinde barındırılan tümleştirme çalışma zamanını, yönetim merkezindeki **tümleştirme çalışma zamanları** ' na giderek IR ' yi seçip **Sil**' e tıklayarak silebilirsiniz. IR silindikten sonra, ona bağlı olan devam eden taramaların hiçbiri başarısız olur.

## <a name="next-steps"></a>Sonraki adımlar

[Taramalar silinen varlıkları nasıl algılar?](concept-detect-deleted-assets.md)
