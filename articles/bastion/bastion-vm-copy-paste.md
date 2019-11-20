---
title: 'Sanal bir makineye Kopyala ve buradan Yapıştır: Azure savunma | Microsoft Docs'
description: Bu makalede, savunma kullanarak bir Azure VM 'ye kopyalamayı ve buradan yapıştırmayı öğrenin.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: ed8d52a4932271020dfb2a010392b312fa38703b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73519364"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>Sanal makineye Kopyala ve Yapıştır: Azure savunma

Bu makale, Azure savunma kullanırken sanal makinelere ve sanal makinelere metin kopyalayıp yapıştırmanıza yardımcı olur. Bir VM ile çalışmadan önce, [bir savunma ana bilgisayarı oluşturma](bastion-create-host-portal.md)adımlarını izlediğinizden emin olun. Ardından, [RDP](bastion-connect-vm-rdp.md) veya [SSH](bastion-connect-vm-ssh.md)kullanarak birlikte çalışmak istediğiniz sanal makineye bağlanın.

Gelişmiş Pano API erişimini destekleyen tarayıcılarda, yerel cihazınızdaki uygulamalar arasında kopyalama ve yapıştırma yaptığınız şekilde yerel cihazınız ile uzak oturum arasında metin kopyalayabilir ve yapıştırabilirsiniz. Diğer tarayıcılarda, savunma panosu erişim aracı paleti ' ni kullanabilirsiniz.

   ![Panoya izin ver](./media/bastion-vm-manage/allow.png)

Yalnızca metin kopyalama/yapıştırma desteklenir. Doğrudan kopyalama ve yapıştırma için, tarayıcınız, savunma oturumu başlatıldığında Pano erişimi isteyebilir. Web sayfasının panoya erişmesine **Izin verin** .

## <a name="to"></a>Uzak oturuma Kopyala

[Azure Portal ](https://portal.azure.com)kullanarak sanal makineye bağlandıktan sonra aşağıdaki adımları izleyin:

1. Yerel cihazdan metin/içerik ' i yerel panoya kopyalayın.
1. Uzak oturum sırasında, iki oku seçerek savunma panosu erişim aracı paletini başlatın. Oklar oturumun sol ortasında bulunur.

   ![Araç paleti](./media/bastion-vm-manage/left.png)

   ![Yapıştırır](./media/bastion-vm-manage/clipboard.png)
1. Genellikle, kopyalanmış metin, bir savunma Kopyala Yapıştır paletinde otomatik olarak gösterilir. Metniniz yoksa, metni paletteki metin alanına yapıştırın.
1. Metin metin alanından olduktan sonra Uzak oturuma yapıştırabilirsiniz.

   ![Masına](./media/bastion-vm-manage/local.png)

## <a name="from"></a>Uzak oturumdan Kopyala

[Azure Portal ](https://portal.azure.com)kullanarak sanal makineye bağlandıktan sonra aşağıdaki adımları izleyin:

1. Uzak oturumdaki metin/içeriği uzak Pano 'ya (CTRL-C kullanarak) kopyalayın.

   ![Araç paleti](./media/bastion-vm-manage/remote.png)
1. Uzak oturum sırasında, iki oku seçerek savunma panosu erişim aracı paletini başlatın. Oklar oturumun sol ortasında bulunur.

   ![Yapıştırır](./media/bastion-vm-manage/clipboard2.png)
1. Genellikle, kopyalanmış metin, bir savunma Kopyala Yapıştır paletinde otomatik olarak gösterilir. Metniniz yoksa, metni paletteki metin alanına yapıştırın.
1. Metin metin alanından olduktan sonra yerel cihaza yapıştırabilirsiniz.

   ![Masına](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Sonraki adımlar

Savunma [hakkında SSS](bastion-faq.md)makalesini okuyun.