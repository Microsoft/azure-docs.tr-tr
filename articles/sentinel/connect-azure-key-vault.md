---
title: Azure Key Vault tanılama günlüklerini Azure Sentinel 'e bağlama
description: Azure Ilkesi 'ni kullanarak Azure Key Vault tanılama günlüklerini Azure Sentinel 'e nasıl bağlayacağınızı öğrenin.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 03/07/2021
ms.author: yelevin
ms.openlocfilehash: 00e634ec62d1fda4b1f2763a8c69b8e950d54beb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102505197"
---
# <a name="connect-azure-key-vault-diagnostics-logs"></a>Tanılama günlüklerini Azure Key Vault bağlama

Azure Key Vault, gizli dizileri güvenli bir şekilde depolamak ve bunlara erişmek için bir bulut hizmetidir. Gizli dizi, API anahtarları, parolalar, sertifikalar veya şifreleme anahtarları gibi erişimi sıkı şekilde denetlemek istediğiniz her şeydir.

Bu bağlayıcı Azure Key Vault tanılama günlüklerinizi Azure Sentinel 'e aktarmanıza olanak tanıyarak tüm örneklerinizin etkinliğini sürekli izleyebilirsiniz.

[İzleme Azure Key Vault](../azure-monitor/insights/key-vault-insights-overview.md) ve [Azure Key Vault tanılama telemetrisi](../key-vault/general/logging.md)hakkında daha fazla bilgi edinin.

## <a name="prerequisites"></a>Önkoşullar

Azure Sentinel 'e Azure Key Vault günlüklerini almak için:

- Azure Sentinel çalışma alanında okuma ve yazma izinlerine sahip olmanız gerekir.

- Azure Ilkesini, Azure Key Vault kaynaklara bir günlük akış ilkesi uygulamak üzere kullanmak için, ilke atama kapsamı için sahip rolü atanmalıdır.

## <a name="connect-to-azure-key-vault"></a>Azure Key Vault Bağlan

Bu bağlayıcı, bir kapsam olarak tanımlanan bir örnek koleksiyonuna tek bir Azure Key Vault günlük akışı yapılandırması uygulamak için Azure Ilkesi 'ni kullanır. Bağlayıcı sayfasının sol tarafındaki Azure Key Vault alınan günlük türlerini, **veri türleri** altında görebilirsiniz.

1. Azure Sentinel gezinti menüsünde **veri bağlayıcıları**' nı seçin.

1. Veri bağlayıcıları galerisinden **Azure Key Vault** ' yi seçin ve ardından Önizleme bölmesinde **bağlayıcı sayfasını aç** ' ı seçin.

1. Bağlayıcı sayfasının **yapılandırma** bölümünde **Azure Key Vault tanılama günlüklerini etkinleştir**' i genişletin.

1. **Azure Ilke atamasını Başlat Sihirbazı** düğmesini seçin.

    İlke atama Sihirbazı açılarak, dağıt adlı yeni bir ilke oluşturmaya, **Log Analytics çalışma alanına Azure Key Vault için tanılama ayarlarını yapılandırma**' ya gidin.

    1. **Temel bilgiler** sekmesinde, aboneliğinizi (ve isteğe bağlı olarak bir kaynak grubu) seçmek için **kapsam** altında üç nokta olan düğmeye tıklayın. Ayrıca, bir açıklama ekleyebilirsiniz.

    1. **Parametreler** sekmesinde, **Log Analytics çalışma alanı** açılır listesinden Azure Sentinel çalışma alanınızı seçin. Kalan aşağı açılan alanlar kullanılabilir tanılama günlüğü türlerini temsil eder. Almak istediğiniz tüm günlük türlerini "true" olarak işaretlenmiş olarak bırakın.

    1. İlkeyi mevcut kaynaklarınıza uygulamak için **Düzeltme** sekmesini seçin ve **Düzeltme görevi oluştur** onay kutusunu işaretleyin.

    1. **Gözden geçir + oluştur** sekmesinde **Oluştur**' a tıklayın. İlkeniz artık seçtiğiniz kapsama atanır.

> [!NOTE]
>
> Bu veri bağlayıcısıyla bağlantı durumu göstergeleri (veri bağlayıcıları galerisindeki bir renk şeridi ve veri türü adlarının yanında bulunan bağlantı simgeleri), yalnızca geçen iki haftada bir noktada verilerin alınmış olması halinde *bağlı* (yeşil) olarak görünür. Veri alımı olmadan iki hafta geçtikten sonra bağlayıcının bağlantısı kesilmekte olarak gösterilir. Daha fazla veri geldiğinde *bağlantılı* durum döndürülür.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede Azure Key Vault Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Verilerinize ve olası tehditlere nasıl görünürlük alabileceğinizi](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
