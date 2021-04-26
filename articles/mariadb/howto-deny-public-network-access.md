---
title: Ortak ağ erişimini reddetme-Azure portal-MariaDB için Azure veritabanı
description: MariaDB için Azure veritabanınız için Azure portal kullanarak genel ağ erişimini reddetme işlemini nasıl yapılandıracağınızı öğrenin
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 3117ebfd258c72bb97432871c2ea74d30c52f669
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98665081"
---
# <a name="deny-public-network-access-in-azure-database-for-mariadb-using-azure-portal"></a>Azure portal kullanarak MariaDB için Azure veritabanı 'nda genel ağ erişimini reddetme

Bu makalede, bir MariaDB sunucusu için Azure veritabanı 'nı tüm genel yapılandırmaların erişimini reddedecek ve yalnızca özel uç noktalar aracılığıyla ağ güvenliğini artırmak üzere yalnızca bağlantılara izin verecek şekilde nasıl yapılandırabileceğiniz açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzunu tamamlayabilmeniz için şunlar gerekir:

* [MariaDB Için Azure veritabanı](quickstart-create-MariaDB-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Ortak ağ erişimini reddet ayarla

MariaDB sunucusunu genel ağ erişimini engelle ' yi ayarlamak için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/), var olan MariaDB sunucusu Için Azure veritabanınızı seçin.

1. MariaDB sunucusu sayfasında, **Ayarlar** altında **bağlantı güvenliği** ' ne tıklayarak bağlantı güvenliği yapılandırması sayfasını açın.

1. Ortak ağ erişimini engelle ' de, MariaDB sunucunuz için genel erişimi Reddet ' i etkinleştirmek için **Evet** ' i seçin.

    ![MariaDB için Azure veritabanı ağ erişimini reddet](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Değişiklikleri kaydetmek için **Kaydet**’e tıklayın.

1. Bildirim, bağlantı güvenliği ayarının başarıyla etkinleştirildiğini onaylanır.

    ![MariaDB için Azure veritabanı ağ erişimini reddetme başarılı](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Sonraki adımlar

[Ölçümler üzerinde uyarılar oluşturma](howto-alert-metric.md)hakkında bilgi edinin.