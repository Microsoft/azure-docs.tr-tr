---
title: Azure savunma oturumu izleme ve yönetimi | Microsoft Docs
description: Bu makalede, devam eden bir oturum seçip bağlantıyı kesmeyi veya silmeyi öğrenin.
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 05/21/2020
ms.author: charwen
ms.openlocfilehash: beb4f8fc5b9e22bc3e2bdd4732c8321dfcd70780
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90980725"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Azure savunma için oturum izleme ve yönetimi

Savunma hizmeti, sanal ağınıza sağlandıktan ve dağıtıldıktan sonra, bu sanal ağdaki herhangi bir VM 'ye sorunsuz bir şekilde bağlanmak için kullanabilirsiniz. Kullanıcılar iş yüklerine bağlandıklarında, uzak oturumları izlemek ve hızlı yönetim eylemleri almak için Azure savunma kullanılabilir. Azure Bastion oturum izleme özelliği, hangi kullanıcıların hangi VM’lere bağlandığını görüntülemenize olanak sağlar. Kullanıcının bağlı olduğu IP 'yi, ne kadar bağlı olduğunu ve ne zaman bağlandığını gösterir. Oturum yönetimi deneyimi, devam eden bir oturum seçmenizi sağlar ve kullanıcının devam eden oturumla bağlantısını kesmek için bir oturum kesmeyi zorunlu tutar veya siler.

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>Uzak oturumları izleme

1. [Azure Portal](https://portal.azure.com)Azure savunma kaynağınız ' ne gidin ve Azure savunma sayfasından **Oturumlar** ' ı seçin.

   ![Ekran görüntüsünde, oturum seçili Azure portal menü ayarları gösterilir.](./media/session-monitoring/sessions.png)
2. **Oturumlar** sayfasında, devam eden uzak oturumları sağ tarafta görebilirsiniz.

   ![oturumu görüntüle](./media/session-monitoring/view-session.png)
3. Güncelleştirilmiş uzak oturum listesini görmek için **Yenile** ' yi seçin. Yenile ' yi seçtiğinizde, Azure savunma en son izleme bilgilerini alıp portalda yenilemeyecektir.

   ![Ekran görüntüsünde yenileme seçeneği vurgulanmış şekilde Azure savunma kullanan bir oturum gösterilir.](./media/session-monitoring/refresh.png)


## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>Devam eden bir uzak oturumu silme veya zorla kesme

Bir dizi oturum (ler) seçebilir ve bunları zorla kesebilirsiniz. Aşağıdaki adımlarda, uzak oturumları silme işlemleri gösterilmektedir:

1. Azure savunma kaynağınız ' ne gidin ve Azure savunma sayfasından **Oturumlar** ' ı seçin.

   ![Ekran görüntüsü, ayarlar altında seçili oturumlarla Azure portal gösterir.](./media/session-monitoring/navigate.png)
2. Oturumlar ' ı seçtikten sonra, uzak oturumların bir listesini görürsünüz.

   ![oturumları listeleme](./media/session-monitoring/list.png)
3. Belirli bir uzak oturumu seçin, sonra oturum satırının sağ tarafındaki üç üç noktayı seçin ve **Sil**' i seçin.

   ![Ekran görüntüsünde, silme simgesi seçili bir oturumu gösteren Azure portal gösterilmektedir.](./media/session-monitoring/delete.png)
4. Sil ' i seçtiğinizde, uzak oturumun bağlantısı kesilir ve Kullanıcı uzak oturumdaki "bağlantınız kesildi" iletisini gösterilir.

   ![Ekran görüntüsünde, bağlantınız kesildiğinin doğrulanırken bir ileti gösterilir.](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>Sonraki adımlar

Savunma [hakkında SSS](bastion-faq.md)makalesini okuyun.
