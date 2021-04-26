---
title: Visual Studio için Azure Stream Analytics araçları ayarlama
description: Bu makalede, Yükleme gereksinimleri ve Visual Studio için Azure Stream Analytics araçlarının nasıl ayarlanacağı açıklanır.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/22/2018
ms.openlocfilehash: 0077ac8465e8f785e772b384f26e0edc6874a1a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018709"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Visual Studio için Azure Stream Analytics araçları 'nı yükler

Visual Studio 2019 ve Visual Studio 2017 Azure Data Lake ve Stream Analytics araçları destekler. Bu makalede araçların nasıl yükleneceği ve kaldırılacağı açıklanır.

Araçları kullanma hakkında daha fazla bilgi için bkz. [hızlı başlangıç: Visual Studio 'yu kullanarak Azure Stream Analytics Işi oluşturma](stream-analytics-quick-create-vs.md).

## <a name="install"></a>Yükleme

Visual Studio Enterprise (Ultimate/Premium), Professional ve Community sürümleri, araçları destekler. Express Edition ve Mac için Visual Studio bunları desteklemez.

Visual Studio 2019 önerilir.

### <a name="install-for-visual-studio-2019-and-2017"></a>Visual Studio 2019 ve 2017 için Install<a name="recommended-visual-studio-2019-and-2017"></a>

Azure Data Lake ve Stream Analytics araçları, **Azure geliştirme** ve **veri depolama ve işleme** iş yüklerinin bir parçasıdır. Yükleme sırasında bu iki iş yükünün birini etkinleştirin. Visual Studio zaten yüklüyse, **araçları**  >  **ve özellikleri al** ' ı seçerek iş yükleri ekleyin.

[Visual studio 2019 (Preview 2 veya üzeri) ya da Visual studio 2017 (15,3 veya üzeri)](https://www.visualstudio.com/) sürümünü indirin ve yükleme yönergelerini izleyin.

**Veri depolama ve işleme** iş yükünü gösterildiği gibi seçin:

![Veri depolama ve işleme iş yükü seçildi](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-01.png)

Gösterildiği gibi **Azure geliştirme** iş yükünü seçin:

![Azure geliştirme iş yükü seçildi](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-02.png)

İş yükünü ekledikten sonra, araçları güncelleştirin. Bu yordam, Visual Studio 2019 'e başvurur:

1. Uzantıları   >  **Yönet uzantıları**' nı seçin.

1. **Uzantıları Yönet**' de **güncelleştirmeler** ' i seçin ve **Azure Data Lake ve Stream Analytics araçları**' nı seçin.

1. En son uzantıyı yüklemek için **Güncelleştir** ' i seçin.

![Visual Studio uzantıları ve güncelleştirmeleri](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-vs2019-extensions-updates.png)

### <a name="install-for-visual-studio-2015-and-2013"></a>Visual Studio 2015 ve 2013 için Install<a name="visual-studio-2015-2013"></a>

Visual Studio Enterprise (Ultimate/Premium), Professional ve Community sürümleri, araçları destekler. Express Edition bunları desteklemez.

* Visual Studio 2015 veya Visual Studio 2013 Güncelleştirme 4 'i yükler.
* [Web platformu yükleyicisini](https://www.microsoft.com/web/downloads/platform.aspx)kullanarak .NET için Microsoft Azure SDK sürüm 2.7.1 veya üstünü yükleme.
* [Visual Studio için Microsoft Azure Data Lake ve Stream Analytics araçları](https://www.microsoft.com/en-us/download/details.aspx?id=49504)'nı yükler.

## <a name="update"></a>Update<a name="visual-studio-2019-and-2017"></a><a name="visual-studio-2015-and-2013"></a>

Visual Studio 2019 ve Visual Studio 2017 için yeni bir sürüm anımsatıcısı, Visual Studio bildirimi olarak gösterilir.

Visual Studio 2015 ve Visual Studio 2013 için araçlar, yeni sürümleri otomatik olarak denetler. En son sürümü yüklemek için yönergeleri izleyin.

## <a name="uninstall"></a>Kaldır

Azure Data Lake ve Stream Analytics araçlarını kaldırabilirsiniz. Visual Studio 2019 veya Visual Studio 2017 için **Araçlar**  >  **ve Özellikler al**' ı seçin. **Değiştirme** bölümünde **Azure Data Lake ve Stream Analytics araçlarının** seçimini kaldırın. **Veri depolama ve işleme** iş yükü ya da **Azure geliştirme** iş yükü altında görünür.

Visual Studio 2015 veya Visual Studio 2013 kaldırmak için **Denetim Masası**  >  **Programlar ve Özellikler**' e gidin. **Visual Studio için Microsoft Azure Data Lake ve Stream Analytics araçları**'nı kaldırın.
