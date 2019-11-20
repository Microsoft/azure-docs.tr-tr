---
title: Windows PowerShell arabirimi aracılığıyla Microsoft Azure Data Box Gateway cihaza bağlanın ve yönetin | Microsoft Docs
description: Windows PowerShell arabirimi aracılığıyla Data Box Gateway nasıl bağlanabileceğinizi ve daha sonra yönetileceğini açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: 650777d849e172686e8b46502a84db8c519174e7
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775189"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Windows PowerShell aracılığıyla Azure Data Box Gateway cihazını yönetme

Azure Data Box Gateway çözümü, verileri ağ üzerinden Azure 'a göndermenizi sağlar. Bu makalede, Data Box Gateway cihazınız için yapılandırma ve yönetim görevlerinin bazıları açıklanmaktadır. Cihazınızı yönetmek için Azure portal, yerel Web Kullanıcı arabirimini veya Windows PowerShell arabirimini kullanabilirsiniz.

Bu makale, PowerShell arabirimini kullanarak yaptığınız görevlere odaklanır.

Bu makale aşağıdaki yordamları içerir:

- PowerShell arabirimine bağlanma
- Destek paketi oluşturma
- Karşıya sertifika yükleme
- DHCP dışı ortamda önyükleme
- Cihaz bilgilerini görüntüle

## <a name="connect-to-the-powershell-interface"></a>PowerShell arabirimine bağlanma

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Destek paketi oluşturma

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Karşıya sertifika yükleme

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>DHCP dışı ortamda önyükleme

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Cihaz bilgilerini görüntüle

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Sonraki adımlar

- Azure portalında [Azure Data Box Gateway](data-box-gateway-deploy-prep.md)’i dağıtın.
