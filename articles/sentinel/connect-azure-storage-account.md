---
title: Azure depolama hesabı tanılama günlüklerini Azure Sentinel 'e bağlama
description: Azure Storage hesabı tanılama günlüklerini Azure Sentinel 'e bağlamak için Azure Ilkesi 'ni nasıl kullanacağınızı öğrenin.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: b4260d31b587f2a20d7bc9d4c4e3e6a0d225a416
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879138"
---
# <a name="connect-azure-storage-account-diagnostics-logs"></a>Azure depolama hesabı tanılama günlüklerini bağlama

Azure depolama hesabı, modern veri depolama senaryolarına yönelik bir bulut çözümüdür. Tüm veri nesnelerinizi içerir: Bloblar, dosyalar, kuyruklar, tablolar ve diskler.

Bu bağlayıcı, Azure depolama hesabınızın tanılama günlüklerini Azure Sentinel 'e aktarmanıza olanak tanıyarak, kuruluşunuzdaki tüm Azure depolama kaynaklarınızdaki etkinlikleri sürekli olarak izlemenize ve güvenlik tehditleri algılamanıza olanak tanır.

[Azure Depolama hesabını izleme](../storage/common/storage-analytics-logging.md)hakkında daha fazla bilgi edinin.

## <a name="prerequisites"></a>Önkoşullar

Azure depolama hesabı tanılama günlüklerini Azure Sentinel 'e almak için:

- Azure Sentinel çalışma alanında okuma ve yazma izinlerine sahip olmanız gerekir.

- Azure Ilkesi 'ni kullanarak Azure depolama kaynaklarına bir günlük akış ilkesi uygulamak için, ilke atama kapsamı için sahip rolüne sahip olmanız gerekir.

## <a name="connect-to-azure-storage-account"></a>Azure depolama hesabına Bağlan

Bu bağlayıcı, bir kapsam olarak tanımlanan bir Azure depolama hesabı kaynakları koleksiyonuna tek bir günlük akış yapılandırması uygulamak için Azure Ilkesi 'ni kullanır. Kullanılabilir Azure depolama günlüğü ve ölçüm türlerini bağlayıcı sayfasının sol tarafında, **veri türleri** altında görebilirsiniz.

1. Azure Sentinel gezinti menüsünde **veri bağlayıcıları**' nı seçin.

1. Veri bağlayıcıları galerisinden **Azure Storage hesabı** ' nı seçin ve ardından Önizleme bölmesinde **bağlayıcı sayfasını aç** ' ı seçin.

1. Bağlayıcı sayfasının **yapılandırma** bölümünde, **Azure Depolama hesabınızdaki akış tanılama günlüklerini ölçeklendirerek** genişletin.

1. **Azure Ilke atamasını Başlat Sihirbazı** düğmesini seçin.

    İlke atama Sihirbazı açılarak, **Log Analytics çalışma alanına depolama hesapları için tanılama ayarlarını yapılandırma** adlı yeni bir ilke oluşturmaya hazırlanın.

    1. **Temel bilgiler** sekmesinde, aboneliğinizi (ve isteğe bağlı olarak bir kaynak grubu) seçmek için **kapsamın** yanındaki üç nokta ile düğmeye tıklayın. Ayrıca, bir açıklama ekleyebilirsiniz.

    1. **Parametreler** sekmesinde:
        - **Log Analytics çalışma alanı** açılır listesinden Azure Sentinel çalışma alanınızı seçin.
        - Dağıtım ayarlarını dağıtmak istediğiniz depolama **Hizmetleri** açılan listesini, depolama kaynak türlerini (dosya, tablo, kuyruk, vb.) seçin.
        - **Ayar adı** ve **efekt** alanlarını olduğu gibi bırakın.
        - Kalan aşağı açılan alanlar, kullanılabilir tanılama günlüğü ve ölçüm türlerini temsil eder. Almak istediğiniz tüm türler "true" olarak işaretlenirler.

    1. Yukarıdaki adımlar, ilkenin gelecekteki tüm depolama kaynaklarına uygulanmasını sağlar. İlkeyi mevcut kaynaklarınıza uygulamak için **Düzeltme** sekmesini seçin ve **Düzeltme görevi oluştur** onay kutusunu işaretleyin.

    1. **Gözden geçir + oluştur** sekmesinde **Oluştur**' a tıklayın. İlkeniz artık seçtiğiniz kapsama atanır.

> [!NOTE]
>
> Bu veri bağlayıcısıyla bağlantı durumu göstergeleri (veri bağlayıcıları galerisindeki bir renk şeridi ve veri türü adlarının yanında bulunan bağlantı simgeleri), yalnızca son 14 gündeki bir noktada verilerin alınmış olması halinde *bağlı* (yeşil) olarak görünür. Veri alımı olmadan 14 gün geçtikten sonra bağlayıcı, kesilmekte olduğu şekilde görünür. Daha fazla veri geldiğinde *bağlantılı* durum döndürülür.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede Azure Storage hesabını Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Verilerinize ve olası tehditlere nasıl görünürlük alabileceğinizi](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
