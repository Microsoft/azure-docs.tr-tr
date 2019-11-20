---
title: Windows Güvenlik Duvarı verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: Windows Güvenlik Duvarı verilerini Azure Sentinel 'e bağlamayı öğrenin.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 840e8b3bc86281a8c42689b1cb68917741ef2bd9
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240635"
---
# <a name="connect-windows-firewall"></a>Windows güvenlik duvarını bağlama



Windows Güvenlik Duvarı Bağlayıcısı, Azure Sentinel çalışma alanınıza bağlı olmaları durumunda Windows Güvenlik duvarları günlüklerinizi kolayca bağlamanıza olanak tanır. Bu bağlantı, panoları görüntülemenizi, özel uyarılar oluşturmanızı ve araştırmanızı sağlar. Bu, kuruluşunuzun ağı hakkında daha ayrıntılı bilgi verir ve güvenlik işlemi yeteneklerini geliştirir. Çözüm, Log Analytics aracısının yüklendiği Windows makinelerden Windows Güvenlik Duvarı olaylarını toplar. 


> [!NOTE]
> Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.

## <a name="enable-the-connector"></a>Bağlayıcıyı etkinleştirme 

1. Azure Sentinel portalında, **veri bağlayıcıları** ' nı seçin ve ardından **Windows Güvenlik Duvarı** kutucuğuna tıklayın. 
1.  Windows makineleriniz Azure 'da ise:
    1. **Azure Windows sanal makinesinde aracıyı yükler**' e tıklayın.
    1. **Sanal makineler** listesinde, Azure Sentinel 'de akışı yapmak istediğiniz Windows makinesini seçin. Bunun bir Windows sanal makine olduğundan emin olun.
    1. Bu VM için açılan pencerede, **Bağlan**' a tıklayın.  
    1. **Windows Güvenlik Duvarı Bağlayıcısı** penceresinde **Etkinleştir** ' e tıklayın. 

2. Windows makineniz bir Azure VM değilse:
    1. **Azure dışı makinelerde aracıyı yükler**' e tıklayın.
    1. **Doğrudan aracı** penceresinde, **Windows agent 'ı (64 bit) İndir** veya **Windows Agent 'ı (32 bit) İndir**seçeneğini belirleyin.
    1. Aracıyı Windows makinenize yükler. **Çalışma alanı kimliği**, **birincil anahtar**ve **İkincil anahtarı** kopyalayın ve yükleme sırasında istendiğinde bunları kullanın.

4. Hangi veri türlerini akışa almak istediğinizi seçin.
5. **Çözümü yüklensin**' e tıklayın.
6. Windows Güvenlik Duvarı için Log Analytics ilgili şemayı kullanmak için, **Securityevent**araması yapın.

## <a name="validate-connectivity"></a>Bağlantıyı doğrula

Günlüklerinizin Log Analytics görünene kadar 20 dakikadan bu kadar bir zaman çıkabilir. 



## <a name="next-steps"></a>Sonraki adımlar
Bu belgede, Windows Güvenlik Duvarı 'nı Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.

