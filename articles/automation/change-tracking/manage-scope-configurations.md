---
title: Azure Otomasyonu Değişiklik İzleme ve envanter dağıtım kapsamını sınırlayın
description: Bu makalede, bir Değişiklik İzleme ve envanter dağıtımının kapsamını sınırlamak için kapsam yapılandırmalarına nasıl çalışılacağı açıklanmaktadır.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 6eb9a20920f0a340491459f0875fc85b90dfa193
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92210410"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>Değişiklik İzleme ve envanter dağıtım kapsamını sınırlandırma

Bu makalede, sanal makinelerinizdeki değişiklikleri dağıtmak üzere [değişiklik izleme ve envanter](overview.md) özelliği kullanılırken kapsam yapılandırmalarının nasıl çalıştığı açıklanır. Daha fazla bilgi için bkz. [Azure izleyici 'de izleme çözümlerini hedefleme (Önizleme)](../../azure-monitor/insights/solution-targeting.md).

## <a name="about-scope-configurations"></a>Kapsam konfigürasyonları hakkında

Kapsam yapılandırması, Değişiklik İzleme ve envanterinin kapsamını belirli bilgisayarlarla sınırlandırmak için kullanılan bir veya daha fazla kayıtlı arama (sorgu) grubudur. Kapsam yapılandırması, etkinleştirmek üzere bilgisayarları hedeflemek için Log Analytics çalışma alanında kullanılır. Özellikten değişikliklere bir bilgisayar eklediğinizde, bilgisayar çalışma alanındaki kayıtlı bir aramaya de eklenir.

## <a name="set-the-scope-limit"></a>Kapsam sınırını ayarla

Değişiklik İzleme ve envanter dağıtımınızın kapsamını sınırlandırmak için:

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. Azure portal, **tüm hizmetler**' i seçin. Kaynak listesinde **Otomasyon** yazın. Yazmaya başladığınızda liste, giriş listenize göre öneriler uygular. **Automation Hesapları**’nı seçin.

3. Otomasyon hesapları listenizde, Değişiklik İzleme ve envanteri etkinleştirildiğinde seçtiğiniz hesabı seçin.

4. Otomasyon hesabınızda **ilgili kaynaklar** altında **bağlantılı çalışma alanı** ' nı seçin.

5. **Çalışma alanına git ' e** tıklayın.

6. **Çalışma alanı veri kaynakları** altında **kapsam yapılandırma (Önizleme)** öğesini seçin.

7. Kapsam yapılandırmasının sağ tarafındaki üç noktayı seçin  `MicrosoftDefaultScopeConfig-ChangeTracking` ve **Düzenle**' ye tıklayın.

8. Düzen bölmesinde **bilgisayar gruplarını Seç**' i seçin. Bilgisayar grupları bölmesi, kapsam yapılandırmasını oluşturmak için kullanılan kaydedilmiş aramaları gösterir. Değişiklik İzleme ve envanter tarafından kullanılan kayıtlı arama:

    |Name     |Kategori  |Diğer ad  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  Değişiklik izleme dosyanız       | ChangeTracking__MicrosoftDefaultComputerGroup        |

9. Grubu doldurmak için kullanılan sorguyu görüntülemek ve düzenlemek için kayıtlı aramayı seçin. Aşağıdaki görüntüde sorgu ve sonuçları gösterilmektedir:

    ![Kayıtlı aramalar](media/manage-scope-configurations/logsearch.png)

## <a name="next-steps"></a>Sonraki adımlar

* Değişiklik İzleme ve envanterle çalışmak için bkz. [Manage değişiklik izleme and Inventory](manage-change-tracking.md).
* Genel özellik sorunlarını gidermek için bkz. [değişiklik izleme ve envanter sorunlarını giderme](../troubleshoot/change-tracking.md).
