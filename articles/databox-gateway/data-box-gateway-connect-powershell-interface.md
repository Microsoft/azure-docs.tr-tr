---
title: Azure Data Box Gateway cihaza bağlanmak ve yönetmek için Windows PowerShell 'i kullanma
description: Windows PowerShell arabirimi aracılığıyla Data Box Gateway nasıl bağlanabileceğinizi ve daha sonra yönetileceğini açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: f3b93bfc9af9bce50c301c10bd372567360d7223
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582738"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Windows PowerShell aracılığıyla Azure Data Box Gateway cihazını yönetme

Azure Data Box Gateway çözümü, verileri ağ üzerinden Azure 'a göndermenizi sağlar. Bu makalede, Data Box Gateway cihazınız için yapılandırma ve yönetim görevlerinin bazıları açıklanmaktadır. Cihazınızı yönetmek için Azure portal, yerel Web Kullanıcı arabirimini veya Windows PowerShell arabirimini kullanabilirsiniz.

Bu makale, PowerShell arabirimini kullanarak yaptığınız görevlere odaklanır.

Bu makale aşağıdaki yordamları içerir:

- PowerShell arabirimine bağlanın
- Destek paketi oluşturma
- Sertifikayı karşıya yükleme
- DHCP dışı ortamda önyükleme
- Cihaz bilgilerini görüntüle

## <a name="connect-to-the-powershell-interface"></a>PowerShell arabirimine bağlanın

[!INCLUDE [Connect to admin runspace](../../includes/data-box-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Destek paketi oluşturma

[!INCLUDE [Create a support package](../../includes/data-box-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Sertifikayı karşıya yükleme

[!INCLUDE [Upload certificate](../../includes/data-box-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>DHCP dışı ortamda önyükleme

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Cihaz bilgilerini görüntüle

[!INCLUDE [View device information](../../includes/data-box-gateway-view-device-info.md)]

## <a name="next-steps"></a>Sonraki adımlar

- Azure portalında [Azure Data Box Gateway](data-box-gateway-deploy-prep.md)’i dağıtın.
