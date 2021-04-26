---
title: VMware ESXi verilerini Azure Sentinel 'e bağlama | Microsoft Docs
description: VMware ESXi Data bağlayıcısını kullanarak ESXi günlüklerini Azure Sentinel 'e çekme hakkında bilgi edinin. Çalışma kitaplarında ESXi verilerini görüntüleyin, uyarılar oluşturun ve araştırmayı geliştirebilirsiniz.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2021
ms.author: yelevin
ms.openlocfilehash: 3d478a9ac3cf91f3f6815859b8534efff88f07b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101700927"
---
# <a name="connect-your-vmware-esxi-to-azure-sentinel"></a>VMware ESXi Azure Sentinel 'e bağlama

> [!IMPORTANT]
> VMware ESXi Bağlayıcısı Şu anda **önizlemededir**. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan ek koşullar için [Microsoft Azure önizlemeleri için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

Bu makalede VMware ESXi gerecinizi Azure Sentinel 'e nasıl bağlayabileceğiniz açıklanır. VMware ESXi Data Connector, kuruluşunuzun ESXi etkinliklerine daha fazla bilgi vererek ve güvenlik Işlemleri olanaklarınızı artırmaya yardımcı olmak için VMware ESXi günlüklerinizi Azure Sentinel 'e kolayca almanıza olanak sağlar. VMware ESXi ile Azure Sentinel arasında tümleştirme, Log Analytics aracısının yüklü olduğu Syslog sunucusu kullanımını sağlar. Ayrıca, kusto işlevine dayalı özel olarak oluşturulmuş bir günlük ayrıştırıcısı kullanır.

> [!NOTE]
> Veriler, Azure Sentinel çalıştırdığınız çalışma alanının coğrafi konumunda depolanır.

## <a name="prerequisites"></a>Önkoşullar

- Azure Sentinel çalışma alanında okuma ve yazma izninizin olması gerekir.

- VMware ESXi çözümünüz Syslog aracılığıyla günlükleri dışarı aktarmak için yapılandırılmış olmalıdır.

## <a name="send-vmware-esxi-logs-to-the-syslog-agent"></a>Syslog aracısına VMware ESXi günlükleri gönderme  

Syslog iletilerini Syslog Aracısı aracılığıyla Azure Sentinel çalışma alanınıza iletmek için VMware ESXi yapılandırın.
3. **VMware ESXi** sayfasındaki yönergeleri izleyin.


1. Azure Sentinel gezinti menüsünde **veri bağlayıcıları**' nı seçin.

1. **Veri bağlayıcıları** galerisinden **VMware ESXi (Önizleme)** bağlayıcısını seçin ve ardından **bağlayıcı sayfasını açın**.

1. **VMware ESXi** Bağlayıcısı sayfasındaki yönergeleri izleyin:

    1. Linux için aracıyı yükleyip ekleme

        - Azure Linux VM veya Azure olmayan bir Linux makinesi (fiziksel veya sanal) seçin.

    1. Toplanacak günlükleri yapılandırın

        - **Çalışma alanı aracıları yapılandırmasındaki** tesisleri ve önem derecelerine seçin.

    1. VMware ESXi yapılandırma ve bağlama

        - VMware ESXi Syslog 'ı iletmek üzere yapılandırmak için bu yönergeleri izleyin:
            - [VMware ESXi 3,5 ve 4. x](https://kb.vmware.com/s/article/1016621)
            - [VMware ESXi 5,0 ve üzeri](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html)

            Uzak sunucu için, Linux aracısını yüklediğiniz Linux makinenin IP adresini kullanın.

## <a name="validate-connectivity-and-find-your-data"></a>Bağlantıyı doğrulayın ve verilerinizi bulun

Günlüklerinizin Azure Sentinel 'de görünmeye başlaması 20 dakikaya kadar sürebilir. 

Başarılı bir bağlantı kurulduktan sonra, veriler **günlüklerde**, *günlük yönetimi* bölümünde, *Syslog* tablosunda görüntülenir.

Bu veri Bağlayıcısı, beklenen şekilde çalışması için bir kusto Işlevini temel alan bir ayrıştırıcıya bağımlıdır. **Vmwareesxi** kusto işlev diğer adını oluşturmak için [Bu adımları izleyin](https://aka.ms/sentinel-vmwareesxi-parser) . Ardından `VMwareESXi` , verileri sorgulamak Için Azure Sentinel 'de herhangi bir sorgu penceresini yazabilirsiniz.

Bazı yararlı sorgu örnekleri için bağlayıcı sayfasındaki **sonraki adımlar** sekmesine bakın.

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede VMware ESXi Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Verilerinize ve olası tehditlere nasıl görünürlük alabileceğinizi](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .
