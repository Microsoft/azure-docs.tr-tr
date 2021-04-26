---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/07/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e40f421c0fa45d772cd333dac51fe2bdf2779f48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91828970"
---
Windows 10 VPN istemcisinin yeni bir özelliği olan her zaman açık, bir VPN bağlantısını sürdürme olanağıdır. Her zaman açık olduğunda, etkin VPN profili otomatik olarak bağlanabilir ve Kullanıcı oturum açma, ağ durumu değişikliği veya cihaz ekranı etkin gibi tetikleyicilere göre bağlı kalır.

Azure 'a kalıcı Kullanıcı tüneli ve cihaz tünelleri sağlamak için Windows 10 her zaman açık olan ağ geçitleri kullanabilirsiniz.

Her zaman VPN bağlantılarında iki tür tünelden biri vardır:

* **Cihaz tüneli**: kullanıcılar cihazda oturum açmadan önce belirtilen VPN sunucularına bağlanır. Oturum açma öncesi bağlantı senaryoları ve cihaz yönetimi bir cihaz tüneli kullanır.

* **Kullanıcı tüneli**: yalnızca kullanıcılar cihazda oturum açtıktan sonra bağlanır. Kullanıcı tünellerini kullanarak, VPN sunucuları üzerinden kuruluş kaynaklarına erişebilirsiniz.

Cihaz tünelleri ve Kullanıcı tünelleri, VPN profillerinin bağımsız olarak çalışır. Bunlar aynı anda birbirine bağlanabilir ve uygun şekilde farklı kimlik doğrulama yöntemleri ve diğer VPN yapılandırma ayarlarını kullanabilirler.
