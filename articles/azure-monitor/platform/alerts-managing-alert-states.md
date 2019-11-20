---
title: Uyarı ve akıllı grup durumlarını yönetme
description: Uyarı ve akıllı Grup örneklerinin durumlarını yönetme
ms.service: azure-monitor
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.author: robb
ms.date: 09/24/2018
ms.openlocfilehash: fd2a2a4b7d5033ecd35d02baa240bc7586e7a25d
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555676"
---
# <a name="manage-alert-and-smart-group-states"></a>Uyarı ve akıllı grup durumlarını yönetme
Azure Izleyici 'deki uyarılar artık bir [uyarı durumuna ve bir izleyici koşuluna](https://aka.ms/azure-alerts-overview) sahiptir ve benzer şekilde akıllı gruplar [akıllı grup durumuna](https://aka.ms/smart-groups)sahiptir. Durum üzerinde yapılan değişiklikler artık ilgili uyarıyla veya akıllı grupla ilişkili geçmiş olarak yakalanır. Bu makalede, hem uyarı hem de akıllı grup için durumu değiştirme sürecinde size yol gösterilir.

## <a name="change-the-state-of-an-alert"></a>Bir uyarının durumunu değiştirme
1. Bir uyarının durumunu aşağıdaki farklı yollarla değiştirebilirsiniz: 
    * Tüm Uyarılar sayfasında, durumunu değiştirmek istediğiniz uyarıların yanındaki onay kutusuna tıklayın ve durumu Değiştir ' e tıklayın.   
    ![İzleme](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * Belirli bir uyarı örneği için Uyarı ayrıntıları sayfasında, durumu Değiştir ' e tıklayabilirsiniz.   
    ![İzleme](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * Belirli bir uyarı örneğinin Uyarı ayrıntıları sayfasında, akıllı grup bölmesinde istediğiniz uyarıların yanındaki onay kutusuna tıklayabilirsiniz    
    ![İzleme](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * Akıllı Grup ayrıntıları sayfasında, üye uyarıları listesinde, durumunu değiştirmek istediğiniz uyarıların yanındaki onay kutusuna tıklayabilir ve durumu Değiştir ' e tıklayarak durumu Değiştir ' e tıklayabilirsiniz.   
    ![İzleme](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. Değişiklik durumunu tıklarken, bir açılan pencere açılır ve durumu seçmenizi sağlar (yeni/onaylanmış/kapalı) ve gerekirse bir yorum girersiniz.   
![İzleme](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. Bu işlem tamamlandıktan sonra durum değişikliği ilgili uyarının geçmişine kaydedilir. Bu, ilgili ayrıntılar sayfası açılarak ve geçmiş bölümü denetlenirken görüntülenebilir.    
![İzleme](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Bir akıllı grubun durumunu değiştirme
1. Akıllı bir grubun durumunu aşağıdaki farklı yollarla değiştirebilirsiniz:
    1. Akıllı Grup listesi sayfasında, durumunu değiştirmek istediğiniz akıllı grupların yanındaki onay kutusuna tıklayabilir ve durumu Değiştir ' e tıklayabilirsiniz.  
    ![İzleme](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. Akıllı Grup ayrıntıları sayfasında, durumu Değiştir ' e tıklayabilirsiniz.        
    ![İzleme](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. Değişiklik durumunu tıklarken, bir açılan pencere açılır ve durumu seçmenizi sağlar (yeni/onaylanmış/kapalı) ve gerekirse bir yorum girersiniz. 
![İzleme](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  Bir akıllı grubun durumunun değiştirilmesi, bağımsız üye uyarılarının durumunu değiştirmez.

1. Bu işlem tamamlandıktan sonra, durum değişikliği ilgili akıllı grubun geçmişine kaydedilir. Bu, ilgili ayrıntılar sayfası açılarak ve geçmiş bölümü denetlenirken görüntülenebilir.     
![İzleme](./media/alerts-managing-alert-states/state-sg-history.jpg)

