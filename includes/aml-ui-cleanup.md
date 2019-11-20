---
title: include dosyası
description: include dosyası
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 11/06/2019
ms.openlocfilehash: 96ede63b097999247675364217cf458a268e54d9
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929662"
---
>[!IMPORTANT]
>Diğer Azure Machine Learning öğreticileri ve nasıl yapılır makaleleri için önkoşul olarak oluşturduğunuz kaynakları kullanabilirsiniz.

### <a name="delete-everything"></a>Her şeyi sil

Oluşturduğunuz herhangi bir şeyi kullanmayı planlamıyorsanız, herhangi bir ücret ödemeniz için tüm kaynak grubunu silin.

1. Azure portal, pencerenin sol tarafındaki **kaynak grupları** ' nı seçin.
 
   ![Azure portalında kaynak grubunu silme](./media/aml-ui-cleanup/delete-resources.png)

1. Listede, oluşturduğunuz kaynak grubunu seçin.

1. **Kaynak grubunu sil**'i seçin.

Kaynak grubunun silinmesi, tasarımcıda oluşturduğunuz tüm kaynakları da siler. 

### <a name="delete-individual-assets"></a>Tek tek varlıkları Sil

Denemenizi oluşturduğunuz tasarımcıda, her bir varlığı seçerek ve ardından **Sil** düğmesini seçerek tek tek varlıkları silin.

Burada oluşturduğunuz işlem hedefi, kullanılmadıysa *otomatik olarak sıfır düğümlere otomatik olarak ölçeklendirilir* . Bu eylem, ücretleri en aza indirmek için yapılır. İşlem hedefini silmek istiyorsanız şu adımları uygulayın:

![Varlıkları Sil](./media/aml-ui-cleanup/delete-asset.png)

Her bir veri kümesini seçip **kayıt Sil**' i seçerek, veri kümelerinin kaydını çalışma alanınızdan silebilirsiniz.

![Veri kümesinin kaydını sil](./media/aml-ui-cleanup/unregister-dataset.png)

Bir veri kümesini silmek için Azure portal veya Azure Depolama Gezgini kullanarak depolama hesabına gidin ve bu varlıkları el ile silin.


