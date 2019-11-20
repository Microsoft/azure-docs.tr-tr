---
title: include dosyası
description: include dosyası
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 10/04/2019
ms.openlocfilehash: b44d624db419919823ec85bcb599007fb75c92b9
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929658"
---
1. Azure aboneliğinizin kimlik bilgilerini kullanarak [Azure Portal](https://portal.azure.com/) oturum açın. 

1. Azure portal sol üst köşesinde **+ kaynak oluştur**' u seçin.

      ![Yeni kaynak oluşturma](media/aml-create-in-portal/create-workspace.gif)

1. **Machine Learning**bulmak için arama çubuğunu kullanın.

1. **Machine Learning**seçin.

1. Başlamak için **Machine Learning** bölmesinde **Oluştur** ' u seçin.

1. Yeni çalışma alanınızı yapılandırmak için aşağıdaki bilgileri sağlayın:

   Alan|Açıklama 
   ---|---
   Çalışma alanı adı |Çalışma alanınızı tanımlayan benzersiz bir ad girin. Bu örnekte **docs-WS**kullanıyoruz. Adlar, kaynak grubu genelinde benzersiz olmalıdır. Başkaları tarafından oluşturulan çalışma alanlarını birbirinden ayırmak ve geri çekmek için kolay bir ad kullanın. 
   Abonelik |Kullanmak istediğiniz Azure aboneliğini seçin.
   Kaynak grubu | Aboneliğinizde var olan bir kaynak grubunu kullanın veya yeni bir kaynak grubu oluşturmak için bir ad girin. Kaynak grubu, bir Azure çözümü için ilgili kaynakları barındırır. Bu örnekte **docs-AML**kullanılır. 
   Konum | Çalışma alanınızı oluşturmak için kullanıcılarınıza en yakın konumu ve veri kaynaklarını seçin.
   Çalışma alanı sürümü | **Kurumsal**' i seçin. Bu öğreticide Enterprise sürümünün kullanılması gerekir. Enterprise Edition önizlemededir ve şu anda ek maliyet eklemez. 

1. Çalışma alanını yapılandırmayı tamamladıktan sonra **Oluştur**' u seçin. 

   > [!WARNING] 
   > Çalışma alanınızı bulutta oluşturmak birkaç dakika sürebilir.

   İşlem tamamlandığında, bir dağıtım başarı iletisi görüntülenir. 
 
 1. Yeni çalışma alanını görüntülemek için **Kaynağa Git**' i seçin.

