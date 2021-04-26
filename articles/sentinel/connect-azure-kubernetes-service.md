---
title: Azure Kubernetes hizmeti (AKS) tanılama günlüklerini Azure Sentinel 'e bağlama
description: Azure Kubernetes hizmet tanılama günlüklerini Azure Sentinel 'e bağlamak için Azure Ilkesi 'ni nasıl kullanacağınızı öğrenin.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 03/07/2021
ms.author: yelevin
ms.openlocfilehash: c3a4593aa92acededf9784974b2a1e2dd3cfb319
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102507186"
---
# <a name="connect-azure-kubernetes-service-diagnostics-logs"></a>Azure Kubernetes hizmet tanılama günlüklerini bağlama

Azure Kubernetes hizmeti (AKS), bir küme ortamında Docker kapsayıcıları ve kapsayıcı tabanlı uygulamalar dağıtmanıza, ölçeklendirmenize ve yönetmenize olanak sağlayan açık kaynaklı, tam olarak yönetilen bir kapsayıcı düzenleme hizmetidir.

Bu bağlayıcı Azure Kubernetes hizmeti (AKS) tanılama günlüklerinizi Azure Sentinel 'e aktarmanıza olanak tanıyarak tüm örneklerinizin etkinliğini sürekli izleyebilirsiniz. 

[Azure Kubernetes hizmetini izleme](../azure-monitor/containers/container-insights-overview.md) ve [aks tanılama telemetrisi](../aks/view-control-plane-logs.md)hakkında daha fazla bilgi edinin.

## <a name="prerequisites"></a>Önkoşullar

AKS günlüklerini Azure Sentinel 'e almak için:

- Azure Sentinel çalışma alanında okuma ve yazma izinlerine sahip olmanız gerekir.

- Azure Ilkesini, AKS kaynaklarına bir günlük akış ilkesi uygulamak üzere kullanmak için, ilke atama kapsamı için sahip rolü atanmalıdır.

## <a name="connect-to-azure-kubernetes-service"></a>Azure Kubernetes hizmetine bağlanma

Bu bağlayıcı, bir kapsam olarak tanımlanan bir örnek koleksiyonuna tek bir Azure Kubernetes hizmeti günlük akış yapılandırması uygulamak için Azure Ilkesi 'ni kullanır. Bağlayıcı sayfasının sol tarafında, **veri türleri** altında Azure Kubernetes hizmetinden alınan günlük türlerini görebilirsiniz.

1. Azure Sentinel gezinti menüsünde **veri bağlayıcıları**' nı seçin.

1. Veri bağlayıcıları galerisinden **Azure Kubernetes hizmeti (AKS)** öğesini seçin ve ardından Önizleme bölmesinde **bağlayıcı sayfasını aç** ' ı seçin.

1. Bağlayıcı sayfasının **yapılandırma** bölümünde **Azure Kubernetes SERVICE (aks) üzerinde tanılama günlüklerini etkinleştir**' i genişletin.

1. **Azure Ilke atamasını Başlat Sihirbazı** düğmesini seçin.

    İlke atama Sihirbazı açılarak, dağıtım adlı yeni bir ilke oluşturmaya hazır, **Log Analytics çalışma alanına Azure Kubernetes hizmeti için tanılama ayarlarını yapılandırın**.

    1. **Temel bilgiler** sekmesinde, aboneliğinizi (ve isteğe bağlı olarak bir kaynak grubu) seçmek için **kapsam** altında üç nokta olan düğmeye tıklayın. Ayrıca, bir açıklama ekleyebilirsiniz.

    1. **Parametreler** sekmesinde, **Log Analytics çalışma alanı** açılır listesinden Azure Sentinel çalışma alanınızı seçin. Kalan aşağı açılan alanlar kullanılabilir tanılama günlüğü türlerini temsil eder. Almak istediğiniz tüm günlük türlerini "true" olarak işaretlenmiş olarak bırakın.

    1. İlkeyi mevcut kaynaklarınıza uygulamak için **Düzeltme** sekmesini seçin ve **Düzeltme görevi oluştur** onay kutusunu işaretleyin.

    1. **Gözden geçir + oluştur** sekmesinde **Oluştur**' a tıklayın. İlkeniz artık seçtiğiniz kapsama atanır.

> [!NOTE]
>
> Bu veri bağlayıcısıyla bağlantı durumu göstergeleri (veri bağlayıcıları galerisindeki bir renk şeridi ve veri türü adlarının yanında bulunan bağlantı simgeleri), yalnızca geçen iki haftada bir noktada verilerin alınmış olması halinde *bağlı* (yeşil) olarak görünür. Veri alımı olmadan iki hafta geçtikten sonra bağlayıcının bağlantısı kesilmekte olarak gösterilir. Daha fazla veri geldiğinde *bağlantılı* durum döndürülür.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede Azure Kubernetes hizmetini Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Verilerinize ve olası tehditlere nasıl görünürlük alabileceğinizi](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
