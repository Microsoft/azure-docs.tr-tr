---
title: Exchange eşleme kılavuzu
titleSuffix: Azure
description: Exchange eşleme kılavuzu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 97430d86cba1107ef67264a181925d2a4d7c12c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586831"
---
# <a name="exchange-peering-walkthrough"></a>Exchange eşleme kılavuzu

Bu bölümde, bir Exchange eşlemesini ayarlamak ve yönetmek için izlemeniz gereken adımlar açıklanmaktadır.

## <a name="create-an-exchange-peering"></a>Exchange eşlemesi oluşturma
> [!div class="mx-imgBorder"]
> ![Exchange eşleme iş akışı ve bağlantı durumları](./media/exchange-peering.png)

Bir Exchange eşlemesi sağlamak için aşağıdaki adımlar izlenmelidir:
1. Exchange eşleme gereksinimlerini anlamak için Microsoft [eşleme ilkesini](https://peering.azurewebsites.net/peering) gözden geçirin.
1. [Peeringdb](https://www.peeringdb.com/net/694) 'de Microsoft eşleme konumu ve eşleme tesis kimliğini bulun
1. Daha fazla ayrıntı için [PowerShell kullanarak bir Exchange eşlemesi oluşturma ve değiştirme](howto-exchange-powershell.md) konusundaki yönergeleri kullanarak bir eşleme konumu için Exchange eşlemesi isteyin.
1. Bir eşleme isteği gönderdikten sonra, Microsoft isteği gözden geçirir ve gerekirse sizinle iletişim kuracaktır.
1. Onaylandığında, bağlantı durumu Onaylandı olarak değişir
1. BGP oturumunu en sonda yapılandırın ve Microsoft 'a bildirin
1. Tüm ilkeleri Reddet ve uçtan uca doğrulama ile BGP oturumu sağlayacağız.
1. İşlem başarılı olursa, eşleme bağlantı durumunun etkin olduğunu belirten bir bildirim alırsınız.
1. Daha sonra trafiğe yeni eşleme üzerinden izin verilir.

Bağlantı durumlarının standart [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) oturum durumlarıyla karıştırılmadığını unutmayın.

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Eski bir Exchange eşlemeyi Azure kaynağına dönüştürme
Eski bir Exchange eşlemesini Azure kaynağına dönüştürmek için aşağıdaki adımlar izlenmelidir:
1. [Eski Exchange eşlemesini Azure kaynağına dönüştürme](howto-legacy-exchange-powershell.md) bölümündeki yönergeleri izleyin
1. Dönüştürme isteğini gönderdikten sonra, Microsoft isteği gözden geçirir ve gerekirse sizinle iletişim kuracaktır.
1. Onaylandığında, Exchange eşinizi bağlantı durumuyla etkin olarak görürsünüz.

## <a name="deprovision-exchange-peering"></a>Exchange eşlemesini sağlamayı kaldırma
Exchange eşlemesini sağlamak için [Microsoft eşlemesi](mailto:peering@microsoft.com) 'ne başvurun.

Bir Exchange eşlemesi, sağlamayı kaldırma için ayarlandığında, bağlantı durumunu **Pendingremove** olarak görürsünüz.

> [!NOTE]
> Bağlantı durumu ProvisioningStarted veya ProvisioningCompleted olduğunda Exchange eşlemesini silmek için PowerShell cmdlet 'ini çalıştırırsanız, işlem başarısız olur.

## <a name="next-steps"></a>Sonraki adımlar

* [Microsoft ile eşleme ayarlama önkoşulları](prerequisites.md)hakkında bilgi edinin.
