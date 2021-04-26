---
title: Azure Stack Edge Pro FPGA kullanıcıları yönetme | Microsoft Docs
description: Azure Stack Edge Pro 'unuzdaki kullanıcıları yönetmek için Azure portal nasıl kullanılacağını açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/05/2021
ms.author: alkohli
ms.openlocfilehash: 27ca190f3bad7f75175e5206d48e13dae1f5687e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97913351"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-stack-edge-pro-fpga"></a>Azure Stack Edge Pro FPGA 'daki kullanıcıları yönetmek için Azure portal kullanın

Bu makalede Azure Stack Edge Pro FPGA cihazındaki kullanıcıların nasıl yönetileceği açıklanır. Azure Stack Edge Pro 'Yu Azure portal veya yerel Web Kullanıcı arabirimi aracılığıyla yönetebilirsiniz. Kullanıcı ekleme, değiştirme ve silme işlemleri için Azure portalı kullanın.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Kullanıcı ekleme
> * Kullanıcıyı değiştirme
> * Kullanıcı silme

## <a name="about-users"></a>Kullanıcılar hakkında

Kullanıcılara salt okunur erişim veya tam ayrıcalık verilebilir. Adından anlaşılacağı üzere salt okunur kullanıcılar paylaşım verilerini yalnızca görüntüleyebilir. Tam ayrıcalık sahibi kullanıcılar paylaşım verilerini okuyabilir, bu paylaşımlara veri yazabilir, paylaşım verilerini değiştirebilir veya silebilir.

 - **Tam ayrıcalıklı kullanıcı**: Tam erişime sahip yerel kullanıcıdır.
 - **Salt okunur kullanıcı**: Salt okunur erişime sahip yerel kullanıcıdır. Bu kullanıcılar yalnızca salt okunur işlemlere izin veren paylaşımlarla ilişkilendirilir.

Kullanıcı izinleri, paylaşım oluşturma sırasında kullanıcı oluşturulurken tanımlanır. Paylaşma düzeyi izinlerinin değiştirilmesi Şu anda desteklenmiyor.

## <a name="add-a-user"></a>Kullanıcı ekleme

Kullanıcı eklemek için Azure portalda aşağıdaki adımları gerçekleştirin.

1. Azure portal, Azure Stack Edge kaynağınız ' ne gidin ve ardından **Kullanıcılar**' a gidin. Komut çubuğunda **+ Kullanıcı Ekle** ' yi seçin.

    ![Kullanıcı Ekle ' yi seçin](media/azure-stack-edge-manage-users/add-user-1.png)

2. Eklemek istediğiniz kullanıcının kullanıcı adını ve parolasını belirtin. Parolayı onaylayın ve **Ekle**' yi seçin.

    ![Kullanıcı adı ve parola belirtin](media/azure-stack-edge-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Bu kullanıcılar sistem tarafından ayrılmıştır ve kullanılmamalıdır: Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. Kullanıcı oluşturma başladığında ve tamamlandığında bir bildirim gösterilir. Kullanıcı oluşturulduktan sonra, komut çubuğundan, güncelleştirilmiş Kullanıcı listesini görüntülemek için **Yenile** ' yi seçin.


## <a name="modify-user"></a>Kullanıcıyı değiştirme

Kullanıcı oluşturulduktan sonra parolasını değiştirebilirsiniz. Kullanıcı listesinden seçin. Yeni parolayı girin ve onaylayın. Değişiklikleri kaydedin.
 
![Kullanıcıyı değiştirme](media/azure-stack-edge-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Kullanıcı silme

Kullanıcı silmek için Azure portalda aşağıdaki adımları gerçekleştirin.


1. Azure portal, Azure Stack Edge kaynağınız ' ne gidin ve ardından **Kullanıcılar**' a gidin.

    ![Silinecek kullanıcıyı seçin](media/azure-stack-edge-manage-users/delete-user-1.png)

2. Kullanıcılar listesinden bir kullanıcı seçin ve **Sil**' i seçin.  

   ![Sil ' i seçin](media/azure-stack-edge-manage-users/delete-user-2.png)

3. Sorulduğunda silme işlemini onaylayın. 

   ![Silmeyi onayla](media/azure-stack-edge-manage-users/delete-user-3.png)

Kullanıcı listesi silinen kullanıcıya göre güncelleştirilir.

![Güncelleştirilmiş kullanıcı listesi](media/azure-stack-edge-manage-users/delete-user-4.png)


## <a name="next-steps"></a>Sonraki adımlar

- [Bant genişliğini yönetmeyi](azure-stack-edge-manage-bandwidth-schedules.md) öğrenin.
