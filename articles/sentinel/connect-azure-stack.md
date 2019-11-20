---
title: Azure Stack sanal makinelerinizi Azure Sentinel 'e ekleme | Microsoft Docs
description: Bu makalede, Azure Izleyici, güncelleştirme ve yapılandırma yönetimi sanal makine uzantısının Azure Stack sanal makinelerde nasıl sağlanacağı ve Sentinel ile nasıl izleneceği gösterilmektedir.
services: sentinel
documentationcenter: na
author: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: fb306ec3f8abe2eedb97f83d01836745779db914
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240767"
---
# <a name="connect-azure-stack-virtual-machines-to-azure-sentinel"></a>Azure Stack sanal makinelerini Azure Sentinel 'e bağlama




Azure Sentinel ile Azure 'da çalışan ve tek bir yerde Azure Stack sanal makinelerinizi izleyebilirsiniz. Azure Stack makinelerinizi Azure Sentinel 'e bağlamak için, önce sanal makine uzantısını mevcut Azure Stack sanal makinelerinize eklemeniz gerekir. 

Azure Stack makinelere bağlandıktan sonra verilerinizi temel alan bir pano galerisinden seçin. Bu panolar, gereksinimlerinize kolayca kolayca özelleştirilebilir.



## <a name="add-the-virtual-machine-extension"></a>Sanal makine uzantısını ekle 

Azure Stack çalışan sanal makinelere **Azure izleyici, güncelleştirme ve yapılandırma yönetimi** sanal makine uzantısını ekleyin. 

1. Yeni bir tarayıcı sekmesinde [Azure Stack portalda](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal)oturum açın.
2. **Sanal makineler** sayfasına gidin, Azure Sentinel ile korumak istediğiniz sanal makineyi seçin. Azure Stack üzerinde sanal makine oluşturma hakkında daha fazla bilgi için, bkz. [Azure Stack portalı Ile Windows Server VM oluşturma](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) veya [Azure Stack portalını kullanarak BIR Linux Server VM oluşturma](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. **Uzantılar**'ı seçin. Bu sanal makinede yüklü olan sanal makine uzantılarının listesi gösteriliyor.
4. **Ekle** sekmesine tıklayın. **Yeni kaynak** menüsü dikey penceresi açılır ve kullanılabilir sanal makine uzantılarının listesini gösterir. 
5. **Azure izleyici, güncelleştirme ve yapılandırma yönetimi** uzantısını seçin ve **Oluştur**' a tıklayın. **Uzantı yapılandırmasını yükler** penceresi açılır.

   ![Azure Izleyici, güncelleştirme ve yapılandırma yönetimi ayarları](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Market 'te listelenen **Azure izleyici, güncelleştirme ve yapılandırma yönetimi** uzantısını görmüyorsanız, kullanılabilir hale getirmek için Azure Stack işletmenine ulaşın.

6. Azure Sentinel menüsünde, **çalışma alanı ayarları** ' nı ve ardından **Gelişmiş**' i SEÇIN ve **çalışma alanı kimliğini** ve **çalışma alanı anahtarını (birincil anahtar)** kopyalayın. 
1. **Uzantıyı Azure Stack yüklemesi** penceresinde, belirtilen alanlara yapıştırın ve **Tamam**' a tıklayın.
1. Uzantı yüklemesi tamamlandıktan sonra, durumu **sağlama başarılı**olarak gösterilir. Sanal makinenin Azure Sentinel portalında görünmesi bir saate kadar sürebilir.

Windows için aracı yükleme ve yapılandırma hakkında daha fazla bilgi için bkz. [Windows bilgisayarlarını bağlama](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Aracı sorunlarını gidermek için bkz. [Azure Log Analytics Linux Aracısı sorunlarını giderme](../azure-monitor/platform/agent-linux-troubleshoot.md).

Azure üzerinde Azure Sentinel portalında, **sanal makineler**altında, tüm VM 'lere ve bilgisayarlara ilişkin bir genel bakışa sahip olursunuz. 

## <a name="clean-up-resources"></a>Kaynakları temizleme
Artık gerekli değilse, Azure Stack portalı aracılığıyla sanal makineden uzantıyı kaldırabilirsiniz.

Uzantıyı kaldırmak için:

1. **Azure Stack portalını**açın.
2. **Sanal makineler** sayfasına gidin, uzantıyı kaldırmak istediğiniz sanal makineyi seçin.
3. **Uzantılar**' ı seçin, **Microsoft. Enterprisecloud. Monitoring**uzantısını seçin.
4. **Kaldır**' a tıklayın ve seçiminizi onaylayın.

## <a name="next-steps"></a>Sonraki adımlar

Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](tutorial-detect-threats-built-in.md)başlayın.
- [Yaygın hata biçimi gereçlerden](connect-common-event-format.md) Azure Sentinel 'e veri akışı.
