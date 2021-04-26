---
title: Kmebook 'tan bir Azure Lab Services VM 'sine bağlanma | Microsoft Docs
description: Azure Lab Services ' de bir Kmebook 'tan sanal makineye nasıl bağlanacağınızı öğrenin.
services: devtest-lab, lab-services, virtual-machines
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 74135c0b36f533ebfbba6422bc79af47825a1a3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97813233"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-chromebook"></a>Bir Kmebook üzerinde Uzak Masaüstü Protokolü kullanarak bir VM 'ye bağlanma

Bu bölümde, bir öğrencinin RDP kullanarak bir Kmebook 'tan bir derslik laboratuvar VM 'sine nasıl bağlanabildiği gösterilmektedir.

## <a name="install-microsoft-remote-desktop-on-a-chromebook"></a>Bir Kmebook 'a Microsoft Uzak Masaüstü yüklemesi

1. Kmebook 'inizdeki uygulama mağazasını açın ve **Microsoft Uzak Masaüstü** arayın.

    ![Microsoft Uzak Masaüstü](./media/how-to-use-classroom-lab/install-ms-remote-desktop-chromebook.png)
    
1. En son Microsoft Uzak Masaüstü sürümünü yükler. 

## <a name="access-the-vm-from-your-chromebook-using-rdp"></a>RDP kullanarak Kmebook 'ınızdan VM 'ye erişme

1. **Microsoft Uzak Masaüstü** yüklü olan bilgisayarınızda indirilen **RDP** dosyasını açın. VM 'ye bağlanmaya başlamalıdır. 

    ![VM’ye bağlanma](./media/how-to-use-classroom-lab/connect-vm-chromebook.png)

1. İstendiğinde parolanızı girin.

    ![Kullanıcı adınızı ve parolanızı girdiğiniz oturum açma ekranını gösteren ekran görüntüsü.](./media/how-to-use-classroom-lab/password-chromebook.png)

1. Aşağıdaki uyarıyı alırsanız **devam** ' ı seçin. 

    ![Sertifika uyarısı](./media/how-to-use-classroom-lab/certificate-error-chromebook.png)

1. Bağlanmakta olduğunuz VM 'nin masaüstünü görmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Linux VM 'lerine bağlanma hakkında daha fazla bilgi edinmek için bkz. [Linux sanal makinelerine bağlanma](how-to-use-remote-desktop-linux-student.md)

