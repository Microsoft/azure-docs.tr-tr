---
title: Azure hızlı başlangıç-Azure portal kullanarak Azure depolama 'da kuyruk oluşturma | Microsoft Docs
description: Bu hızlı başlangıçta, bir kuyruk oluşturmak için Azure portal kullanırsınız. Ardından bir ileti eklemek, iletinin özelliklerini görüntülemek ve iletiyi sıradan almak için Azure portal kullanın.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/06/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.reviewer: cbrooks
ms.openlocfilehash: d2f7b01c03b877a3dede496fc02f0d0cc9525cef
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721328"
---
# <a name="quickstart-create-a-queue-and-add-a-message-with-the-azure-portal"></a>Hızlı Başlangıç: Bir kuyruk oluşturun ve Azure portal bir ileti ekleyin

Bu hızlı başlangıçta, Azure depolama 'da bir sıra oluşturmak ve ileti eklemek ve sıradan çıkarma için [Azure Portal](https://portal.azure.com/) kullanmayı öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-queue"></a>Kuyruk oluştur

Azure portal bir sıra oluşturmak için aşağıdaki adımları izleyin:

1. Azure portalında yeni depolama hesabınıza gidin.
2. Depolama hesabının sol menüsünde **kuyruk hizmeti** bölümüne gidin ve **Kuyruklar**' ı seçin.
3. **+ Queue** düğmesini seçin.
4. Yeni kuyruğunuz için bir ad yazın. Kuyruk adı küçük harfle yazılmalıdır, bir harf veya sayıyla başlamalıdır ve yalnızca harf, rakam ve tire (-) karakterini içerebilir.
6. Kuyruğu oluşturmak için **Tamam ' ı** seçin.

    ![Azure portal sıranın nasıl oluşturulacağını gösteren ekran görüntüsü](media/storage-quickstart-queues-portal/create-queue.png)

## <a name="add-a-message"></a>İleti ekleme

Sonra, yeni kuyruğa bir ileti ekleyin. İleti boyutu 64 KB 'ye kadar olabilir.

1. Depolama hesabındaki kuyruk listesinden yeni kuyruğu seçin.
1. Kuyruğa bir ileti eklemek için **+ Ileti Ekle** düğmesini seçin. **İleti metni** alanına bir ileti girin. 
1. İletinin ne zaman sona ereceğini belirtin. Bir iletinin sıra kalabileceği en uzun süre 7 gündür.
1. İletiyi Base64 olarak kodlamak isteyip istemediğinizi belirtin. İkili verileri kodlama önerilir.
1. İletiyi eklemek için **Tamam** düğmesini seçin.

    ![Bir sıraya nasıl ileti ekleneceğini gösteren ekran görüntüsü](media/storage-quickstart-queues-portal/add-message.png)

## <a name="view-message-properties"></a>İleti özelliklerini görüntüle

Bir ileti ekledikten sonra Azure portal kuyruktaki tüm iletilerin bir listesini görüntüler. İleti KIMLIĞINI, iletinin içeriğini, ileti ekleme süresini ve ileti sona erme süresini görüntüleyebilirsiniz. Ayrıca, bu iletinin kaç kez kuyruğa alınmış olduğunu da görebilirsiniz.

![İleti özelliklerini gösteren ekran görüntüsü](media/storage-quickstart-queues-portal/view-message-properties.png)

## <a name="dequeue-a-message"></a>Bir iletiyi sıradan çıkarma

Azure portal kuyruğun önünden bir iletiyi sıradan silebilirsiniz. İletiyi sıradan kaldırdığınızda ileti silinir. 

Sıradan çıkarıcı her zaman kuyruktaki en eski iletiyi kaldırır. 

![Portaldan bir iletinin nasıl sıradan alınacağını gösteren ekran görüntüsü](media/storage-quickstart-queues-portal/dequeue-message.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir kuyruk oluşturma, ileti ekleme, ileti özelliklerini görüntüleme ve Azure portal bir iletiyi sıradan çıkarma hakkında öğrenirsiniz.

> [!div class="nextstepaction"]
> [Azure Kuyrukları nelerdir?](storage-queues-introduction.md)
