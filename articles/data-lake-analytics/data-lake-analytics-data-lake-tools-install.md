---
title: Visual Studio için Azure Data Lake Araçları’nı yükleme
description: Bu makalede Visual Studio için Azure Data Lake Araçları nasıl yükleneceği açıklanır.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 08f0456b6a8b663f0af68fbb179de14b9e3acf79
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914065"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Visual Studio için Data Lake Araçları’nı yükleme.

Azure Data Lake Analytics hesapları oluşturmak için Visual Studio 'Yu nasıl kullanacağınızı öğrenin. İşleri [U-SQL](data-lake-analytics-u-sql-get-started.md) içinde tanımlayabilir ve işleri Data Lake Analytics hizmetine gönderebilirsiniz. Data Lake Analytics hakkında daha fazla bilgi için bkz. [Azure Data Lake Analytics'e genel bakış](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Önkoşullar

* **Visual Studio**: Express dışındaki tüm sürümler desteklenir.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **.NET için Microsoft Azure SDK** 2.7.1 sürümü veya sonraki sürümleri. [Web platformu yükleyicisini](https://www.microsoft.com/web/downloads/platform.aspx) kullanarak yükleyin.
* **Data Lake Analytics** hesabı. Hesap oluşturmak için bkz. [Azure portalı kullanarak Azure Data Lake Analytics ile çalışmaya başlama](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>Visual Studio için Azure Data Lake Araçları 2017 veya Visual Studio 2019 ' ü yükler

Visual Studio için Azure Data Lake Araçları, Visual Studio 2017 15,3 veya üzeri sürümlerde desteklenir. Araç, **veri depolama ve işleme** ve **Azure geliştirme** iş yüklerinin bir parçasıdır. Visual Studio yüklemenizin parçası olarak bu iki iş yükünden birini etkinleştirin.

**Veri depolama ve işleme** iş yükünü aşağıda gösterildiği gibi etkinleştirin:

![Veri depolamayı etkinleştirme ve iş yükünü işleme](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

**Azure geliştirme** iş yükünü gösterildiği gibi etkinleştirin:

![Azure geliştirme iş yükünü seçin](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Visual Studio 2013 ve 2015 için Azure Data Lake Araçları’nı yükleme

[Visual Studio için Microsoft Azure Data Lake ve Stream Analytics araçları ](https://aka.ms/adltoolsvs)indirin ve yükleyin. Yükleme sonrasında, Visual Studio aşağıdaki değişiklikleri içerir:

* **Sunucu Gezgini** > **Azure** düğümü, **Data Lake Analytics** düğümü içerir.
* **Araçlar** menüsünde **Data Lake** öğesi vardır.

## <a name="next-steps"></a>Sonraki adımlar

* Tanılama bilgilerini günlüğe kaydetmek için bkz. [Azure Data Lake Analytics için tanılama günlüklerine erişme](data-lake-analytics-diagnostic-logs.md).
* Daha karmaşık bir sorgu görmek için [Azure Data Lake Analytics'i kullanarak Web sitesi günlüklerini çözümleme](data-lake-analytics-analyze-weblogs.md) makalesine bakın.
* Köşe yürütme görünümünü kullanmak için bkz. [Visual Studio için Data Lake araçları 'Nda köşe yürütme görünümünü kullanma](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
