---
title: Çevrimiçi yedekleme ve isteğe bağlı veri yükleme Azure Cosmos DB.
description: Bu makalede otomatik yedekleme, isteğe bağlı veri geri yükleme nasıl çalıştığı açıklanır. Ayrıca sürekli ve düzenli yedekleme modları arasındaki farkı açıklar.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 2629e9c6e048620d9490a1e091a16c138fd1e615
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99525441"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Azure Cosmos DB'de çevrimiçi yedekleme ve isteğe bağlı veri geri yükleme
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB düzenli aralıklarla otomatik olarak verilerinizin yedeğini alır. Otomatik yedeklemeler yapılırken veritabanı işlemlerinin performansı veya kullanılabilirliği etkilenmez. Yedeklerin tümü bir depolama hizmetinde ayrı olarak depolanır. Otomatik yedeklemeler Azure Cosmos hesabınızı, veritabanınızı veya kapsayıcınızı yanlışlıkla sildiğiniz veya güncelleştirdiğiniz ve daha sonra verileri kurtarmaya gerek duyduğunuz senaryolarda yararlı olur. İki yedekleme modu vardır:

* **Düzenli yedekleme modu** -Bu mod, mevcut tüm hesaplar için varsayılan yedekleme modudur. Bu modda, yedekleme düzenli aralıklarla alınır ve destek ekibiyle bir istek oluşturularak veriler geri yüklenir. Bu modda, hesabınız için bir yedekleme aralığı ve bekletme yapılandırırsınız. Maksimum bekletme süresi bir aya genişletilir. En düşük yedekleme aralığı bir saat olabilir.  Daha fazla bilgi için bkz. [düzenli yedekleme modu](configure-periodic-backup-restore.md) makalesi.

* **Sürekli yedekleme modu** (Şu anda genel önizlemede) – Azure Cosmos DB hesabını oluştururken bu modu seçersiniz. Bu mod, son 30 gün içinde herhangi bir zaman noktasına geri yükleme yapmanıza olanak sağlar. Daha fazla bilgi edinmek için bkz. [sürekli yedekleme moduna giriş](continuous-backup-restore-introduction.md), [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)ve [Kaynak Yöneticisi](continuous-backup-restore-template.md) makaleleriyle sürekli yedeklemeyi yapılandırma.

  > [!NOTE]
  > Sürekli yedekleme ile yeni bir hesap yapılandırırsanız, Azure portal, PowerShell veya CLı aracılığıyla Self Servis geri yükleme gerçekleştirebilirsiniz. Hesabınız sürekli modda yapılandırılmışsa, onu düzenli moda geri geçirebilirsiniz. Şu anda, düzenli yedekleme modundaki mevcut hesaplar sürekli moda değiştirilemez.  

## <a name="next-steps"></a>Sonraki adımlar

Daha sonra, hesabınız için düzenli ve sürekli yedekleme modlarını yapılandırma ve yönetme hakkında bilgi edinebilirsiniz:

* [Düzenli yedekleme Ilkesini yapılandırın ve yönetin](configure-periodic-backup-restore.md) .
* [Sürekli yedekleme](continuous-backup-restore-introduction.md) modu nedir?
* [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)veya [Azure Resource Manager](continuous-backup-restore-template.md)kullanarak sürekli yedeklemeyi yapılandırın ve yönetin.
* Sürekli yedekleme moduyla verileri geri yüklemek için gereken [Izinleri yönetin](continuous-backup-restore-permissions.md) .
