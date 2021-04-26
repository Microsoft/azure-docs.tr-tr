---
title: Azure Stream Analytics işleri kopyalama veya yedekleme
description: Bu makalede bir Azure Stream Analytics işinin nasıl kopyalanacağı veya yedekleneceği açıklanır.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/11/2019
ms.openlocfilehash: 864c5ffc9ed88f438a5be5a1fcb55d0b78df5e07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016620"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Azure Stream Analytics işleri kopyalama veya yedekleme

Visual Studio Code veya Visual Studio kullanarak dağıtılan Azure Stream Analytics işlerinizi kopyalayabilir veya yedekleyebilirsiniz. Bir işi başka bir bölgeye kopyalamak, son çıkış süresini kopyalamaz. Bu nedenle, kopyalanmış işi başlatırken [**son durdurulduğu zaman**](./start-job.md#start-options) seçeneğini kullanamazsınız.

## <a name="before-you-begin"></a>Başlamadan önce
* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

* [Azure portalında](https://portal.azure.com/) oturum açın.

* [Visual Studio için Visual Studio Code veya Azure Stream Analytics araçları](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension) [için Azure Stream Analytics uzantısını](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension) yükler.  

## <a name="visual-studio-code"></a>Visual Studio Code

1. Visual Studio Code etkinlik çubuğundaki **Azure** simgesine tıklayın ve ardından **Stream Analytics** düğümünü genişletin. İşleriniz aboneliklerinizin altında görünmelidir.

   ![Stream Analytics Gezginini aç](./media/vscode-explore-jobs/open-explorer.png)

2. Bir işi yerel bir projeye aktarmak için, Visual Studio Code **Stream Analytics Gezgininde** dışarı aktarmak istediğiniz işi bulun. Ardından projeniz için bir klasör seçin.

    ![Visual Studio Code içinde ASA işini bulun](./media/vscode-explore-jobs/export-job.png)

    Proje seçtiğiniz klasöre aktarılmışsa ve geçerli çalışma alanınıza eklemiş olursunuz.

3. Farklı bir ad kullanarak işi başka bir bölgeye veya yedeklemeye yayımlamak için, sorgu Düzenleyicisi 'nde (. asaql) **yayımlamak üzere aboneliklerinizden Seç** ' i seçin \* ve yönergeleri izleyin.

    ![Visual Studio Code Azure 'da yayımlayın](./media/quick-create-visual-studio-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. [Dağıtılan Azure Stream Analytics işini bir proje yönergelerine dışarı aktarma](./stream-analytics-vs-tools.md#export-jobs-to-a-project)işlemini izleyin.

2. \*Sorgu Düzenleyicisi 'nde. asaql dosyasını açın, betik düzenleyicisinde **Azure 'a gönder** ' i seçin ve yeni bir ad kullanarak işi başka bir bölgeye veya yedeklemeye yayımlamak için yönergeleri izleyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Hızlı başlangıç: Visual Studio Code kullanarak Stream Analytics işi oluşturma](quick-create-visual-studio-code.md)
* [Hızlı başlangıç: Visual Studio 'Yu kullanarak Stream Analytics işi oluşturma](stream-analytics-quick-create-vs.md)