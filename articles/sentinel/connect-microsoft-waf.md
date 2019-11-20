---
title: Microsoft Web uygulaması güvenlik duvarı verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Microsoft Web uygulaması güvenlik duvarı verilerini Azure Sentinel 'e bağlamayı öğrenin.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: e7dc1e6c1bb1ca81ada59cb3dae8fecbc6452b7f
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029777"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Microsoft Web uygulaması güvenlik duvarındaki verileri bağlama



Azure Application Gateway Microsoft Web uygulaması güvenlik duvarı 'ndan (WAF) günlükleri akışla aktarabilirsiniz. Bu WAF, uygulamalarınızı SQL ekleme ve siteler arası betik oluşturma gibi yaygın web açıklarına karşı korur ve hatalı pozitif sonuçları azaltmak için kuralları özelleştirmenizi sağlar. Microsoft Web uygulaması güvenlik duvarı günlüklerinizi Azure Sentinel 'e aktarmak için bu yönergeleri izleyin.


## <a name="prerequisites"></a>Önkoşullar

- Mevcut bir Application Gateway kaynağı

## <a name="connect-to-microsoft-web-application-firewall"></a>Microsoft Web uygulaması güvenlik duvarı 'na Bağlan

Zaten Microsoft Web uygulaması güvenlik duvarınız varsa, mevcut bir ağ geçidi kaynağınız olduğundan emin olun.
Microsoft Web uygulaması güvenlik duvarınız dağıtıldıktan ve veriler alındıktan sonra, uyarı verileri kolayca Azure Sentinel 'e akışla eklenebilir.
    
1. Azure Sentinel portalında **veri bağlayıcıları**' nı seçin.
1. Veri bağlayıcıları sayfasında **WAF** kutucuğunu seçin.
1. [Application Gateway kaynak](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) adresine gıdıp WAF 'nizi seçin.
    1. **Tanılama ayarları**' nı seçin.
    1. Tablo altında **+ Tanılama ayarı Ekle** ' yi seçin.
    1. **Tanılama ayarları** sayfasında, bir **ad** yazın ve **Log Analytics gönder**' i seçin.
    1. **Log Analytics çalışma alanı** altında Azure Sentinel çalışma alanını seçin.
    1. Çözümlemek istediğiniz günlük türlerini seçin. Şunları öneririz: ApplicationGatewayAccessLog ve ApplicationGatewayFirewallLog.
1. Microsoft Web uygulaması güvenlik duvarı uyarıları için Log Analytics ilgili şemayı kullanmak için, **AzureDiagnostics**aratın.

## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, Microsoft Web uygulaması güvenlik duvarını Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
