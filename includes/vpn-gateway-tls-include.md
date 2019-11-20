---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/27/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0d5c3b55d20be19d4aeb92b82d6e44d417259a7b
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "67188197"
---
1. **Komut istemi** ' ne sağ tıklayıp **yönetici olarak çalıştır**' ı seçerek yükseltilmiş ayrıcalıklarla bir komut istemi açın.
2. Komut isteminde aşağıdaki komutları çalıştırın:

   ```
   reg add HKLM\SYSTEM\CurrentControlSet\Services\RasMan\PPP\EAP\13 /v TlsVersion /t REG_DWORD /d 0xfc0
   reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp" /v DefaultSecureProtocols /t REG_DWORD /d 0xaa0
   if %PROCESSOR_ARCHITECTURE% EQU AMD64 reg add "HKLM\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp" /v DefaultSecureProtocols /t REG_DWORD /d 0xaa0
   ```

3. Aşağıdaki güncelleştirmeleri yükleyin:
  
   * [KB3140245](https://www.catalog.update.microsoft.com/search.aspx?q=kb3140245)
   * [KB2977292](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2977292)

4. Bilgisayarı yeniden başlatın.
5. VPN 'ye bağlanın.

> [!NOTE]
> Windows 10 ' un eski bir sürümünü çalıştırıyorsanız Yukarıdaki kayıt defteri anahtarını ayarlamanız gerekir (10240).
>
