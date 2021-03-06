---
title: include dosyası
titleSuffix: Azure
description: include dosyası
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: beffb2babefd86c2807e21e9337cba66f42fcfc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "81678475"
---
Yapılandırmaya başlamadan önce gerekli modülleri yükleyip içeri aktarın. PowerShell 'e modül yüklemek için yönetici ayrıcalıklarına sahip olmanız gerekir.

1. Az Module 'ü yükleyip içeri aktarın.
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Az. eşleme modülünü yükleyip içeri aktarın.
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Bu komutu kullanarak modüllerin doğru bir şekilde içeri aktarıldığını doğrulayın:
    ```powershell
    Get-Module
    ```
1. Şu komutu kullanarak Azure hesabınızda oturum açın:
    ```powershell
    Connect-AzAccount
    ```
1. Hesap için abonelikleri denetleyin ve eşleme oluşturmak istediğiniz aboneliği seçin.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. Henüz bir kaynak grubunuz yoksa, eşleme oluşturmadan önce bir tane oluşturmanız gerekir. Şu komutu çalıştırarak bunu yapabilirsiniz:

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> ASN ve aboneliğiniz henüz ilişkilendirilmediyse, [EŞDÜZEY ASN 'Yi ilişkilendir](../howto-subscription-association-powershell.md)bölümündeki adımları izleyin. Bu eylem bir eşleme istemek için gereklidir.

> [!NOTE]
> Bir kaynak grubunun konumu, bir eşleme ayarlamayı seçtiğiniz konumdan bağımsızdır.
&nbsp;
