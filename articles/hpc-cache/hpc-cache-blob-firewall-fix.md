---
title: Depolama güvenlik duvarı ayarlarını çözmek
description: Azure HPC önbelleğinde bir Azure Blob depolama hedefi oluştururken bir depolama hesabı ağ güvenlik duvarı ayarı hata oluşmasına neden olabilir. Bu makale, bir yazılım düzeltmesinin gerçekleşene kadar sınırlama için geçici bir çözüm sunar.
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 03/18/2021
ms.author: v-erkel
ms.openlocfilehash: 45a7169330b11e98a8618b08205217212414ca5d
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258937"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>Blob depolama hesabı güvenlik duvarı ayarları geçici çözümü

Depolama hesabı güvenlik duvarları içinde kullanılan belirli bir ayar, BLOB depolama hedefi oluşturmanın başarısız olmasına neden olabilir. Azure HPC önbellek ekibi, bu soruna yönelik bir yazılım düzeltmesinin üzerinde çalışıyor, ancak bu makaledeki yönergeleri izleyerek bu sorunu geçici olarak çözebilirsiniz.

Yalnızca "seçili ağlardan" erişime izin veren güvenlik duvarı ayarı, önbelleğin bir BLOB depolama hedefi oluşturmasını veya değiştirilmesini engelleyebilir. Bu yapılandırma, depolama hesabının **güvenlik duvarları ve sanal ağlar** ayarları sayfalarnda bulunur. (Bu sorun ADLS-NFS depolama hedefleri için de geçerlidir.)

Bu sorun, önbellek hizmetinin müşteri ortamlarından ayrı bir gizli hizmet sanal ağı kullanmadır. Bu ağa, depolama hesabınıza erişmek için açıkça yetki vermek mümkün değildir.

BLOB depolama hedefi oluşturduğunuzda, önbellek hizmeti kapsayıcının boş olup olmadığını denetlemek için bu ağı kullanır. Güvenlik Duvarı gizli ağdan erişime izin vermediği takdirde denetim başarısız olur ve depolama hedefi oluşturma işlemi başarısız olur.

Güvenlik Duvarı, BLOB depolama hedefi ad alanı yollarındaki değişiklikleri de engelleyebilir.

Sorunu geçici olarak çözmek için depolama hedefini oluştururken güvenlik duvarı ayarlarınızı geçici olarak değiştirin:

1. Depolama hesabı **güvenlik duvarları ve sanal ağlar** sayfasına gidin ve "erişime izin ver" ayarını **tüm ağlara** değiştirin.
1. Azure HPC önbelleğinizin BLOB depolama hedefini oluşturun.
1. Depolama hedefinin ad alanı yolunu oluşturun.
1. Depolama hedefi ve yolu başarıyla oluşturulduktan sonra, hesabın güvenlik duvarı ayarını **Seçili ağlara** geri çevirin.

Azure HPC Cache, tamamlanmış depolama hedefine erişmek için hizmet sanal ağını kullanmaz.

Bu geçici çözümle ilgili yardım için [Microsoft hizmet ve destek 'e başvurun](hpc-cache-support-ticket.md).
