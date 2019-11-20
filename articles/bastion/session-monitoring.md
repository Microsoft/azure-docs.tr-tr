---
title: Azure savunma oturumu izleme ve yönetimi | Microsoft Docs
description: Bu makalede, devam eden bir oturum seçip bağlantıyı kesmeyi veya silmeyi öğrenin.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: a4a97ebd0e44bfd3b0ee167a2f3a7da435ac5087
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513009"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Azure savunma için oturum izleme ve yönetimi

Savunma hizmeti, sanal ağınıza sağlandıktan ve dağıtıldıktan sonra, bu sanal ağdaki herhangi bir VM 'ye sorunsuz bir şekilde bağlanmak için kullanabilirsiniz. Kullanıcılar iş yüklerine bağlandıklarında, uzak oturumları izlemek ve hızlı yönetim eylemleri almak için Azure savunma kullanılabilir. Azure savunma oturumu izleme, hangi kullanıcıların hangi VM 'lere bağlandığını görüntülemenize olanak sağlar. Kullanıcının bağlı olduğu IP 'yi, ne kadar bağlı olduğunu ve ne zaman bağlandığını gösterir. Oturum yönetimi deneyimi, devam eden bir oturum seçmenizi sağlar ve kullanıcının devam eden oturumla bağlantısını kesmek için bir oturum kesmeyi zorunlu tutar veya siler.

## <a name="monitor"></a>Uzak oturumları izleme

1. [Azure Portal](https://portal.azure.com)Azure savunma kaynağınız ' ne gidin ve Azure savunma sayfasından **Oturumlar** ' ı seçin.

   ![bağlanabilecek](./media/session-monitoring/sessions.png)
2. **Oturumlar** sayfasında, devam eden uzak oturumları sağ tarafta görebilirsiniz.

   ![oturumu görüntüle](./media/session-monitoring/view-session.png)
3. Güncelleştirilmiş uzak oturum listesini görmek için **Yenile** ' yi seçin. Yenile ' yi seçtiğinizde, Azure savunma en son izleme bilgilerini alıp portalda yenilemeyecektir.

   ![yenileyebilir](./media/session-monitoring/refresh.png)

>[!IMPORTANT]
> Oturum izlemenin çalışması için Ağ Geçidi Yöneticisi 'nden gelen trafik için 4443 bağlantı noktasını etkinleştirin.
>

## <a name="view"></a>Devam eden bir uzak oturumu silme veya zorla kesme

Bir dizi oturum (ler) seçebilir ve bunları zorla kesebilirsiniz. Aşağıdaki adımlarda, uzak oturumları silme işlemleri gösterilmektedir:

1. Azure savunma kaynağınız ' ne gidin ve Azure savunma sayfasından **Oturumlar** ' ı seçin.

   ![Geçmek](./media/session-monitoring/navigate.png)
2. Oturumlar ' ı seçtikten sonra, uzak oturumların bir listesini görürsünüz.

   ![oturumları listeleme](./media/session-monitoring/list.png)
3. Belirli bir uzak oturumu seçin, sonra oturum satırının sağ tarafındaki üç üç noktayı seçin ve **Sil**' i seçin.

   ![delete](./media/session-monitoring/delete.png)
4. Sil ' i seçtiğinizde, uzak oturumun bağlantısı kesilir ve Kullanıcı uzak oturumdaki "bağlantınız kesildi" iletisini gösterilir.

   ![Ayır](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>Sonraki adımlar

Savunma [hakkında SSS](bastion-faq.md)makalesini okuyun.