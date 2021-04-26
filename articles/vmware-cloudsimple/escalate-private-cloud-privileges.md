---
title: Özel bulut ayrıcalıklarını ilerletin
titleSuffix: Azure VMware Solution by CloudSimple
description: VCenter 'daki yönetim işlevleri için özel bulutunuzda ayrıcalıkların nasıl ilerletiloluşturulacağını açıklar
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c2215bd9a60fdcf76077c3a1f2a91631dc0dbe88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895831"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>CloudSimple portalından özel bulut vCenter ayrıcalıklarını ilerletin

Özel bulut vCenter 'nize yönetici erişimi için, CloudSimple ayrıcalıklarınızı geçici olarak ilerletebilirsiniz.  Yükseltilmiş ayrıcalıkları kullanarak, VMware çözümlerini yükleyebilir, kimlik kaynakları ekleyebilir ve kullanıcıları yönetebilirsiniz.

Yeni kullanıcılar vCenter SSO etki alanında oluşturulabilir ve vCenter erişimi verilebilir.  Yeni kullanıcılar oluştururken, vCenter 'a erişim için onları CloudSimple yerleşik gruplarına ekleyin.  Daha fazla bilgi için bkz. [Cloudsimple özel bulut izin modeli VMware vCenter](./learn-private-cloud-permissions.md).

> [!CAUTION]
> Yönetim bileşenleri için herhangi bir yapılandırma değişikliği yapmayın. İlerletilen ayrıcalıklı durum sırasında gerçekleştirilen eylemler sisteminizi olumsuz etkileyebilir veya sisteminizin kullanılamaz hale gelmesine neden olabilir.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="escalate-privileges"></a>Ayrıcalıkları yükseltme

1. [Cloudsimple portalına](access-cloudsimple-portal.md)erişin.

2. **Kaynaklar** sayfasını açın, ayrıcalıklarına Iletmek Istediğiniz özel bulutu seçin.

3. Özet sayfasının alt kısmındaki **vSphere ayrıcalıklarını Değiştir** altında, **Yükselt**' e tıklayın.

    ![VSphere ayrıcalığını değiştirme](media/escalate-private-cloud-privilege.png)

4. VSphere Kullanıcı türünü seçin.  Yalnızca `CloudOwner@cloudsimple.local` Yerel Kullanıcı ilerletilenebilir.

5. Açılan listeden ilerleme zaman aralığını seçin. Görevi tamamlamanıza olanak sağlayacak en kısa süreyi seçin.

6. Riskleri anladığınızdan emin olmak için onay kutusunu seçin.

    ![Ayrıcalığı ilerlet iletişim kutusu](media/escalate-private-cloud-privilege-dialog.png)

7. **Tamam**'a tıklayın.

8. Yükseltme işlemi birkaç dakika sürebilir. İşlem tamamlandığında **Tamam**’a tıklayın.

Ayrıcalık yükseltme başlar ve seçilen aralığın sonuna kadar sürer.  Yönetim görevlerini yapmak için özel bulut vCenter 'unuzda oturum açabilirsiniz.

> [!IMPORTANT]
> Yalnızca bir Kullanıcı ilerletilen ayrıcalıklara sahip olabilir.  Başka bir kullanıcının ayrıcalıklarından daha fazla kullanıcı için kullanıcı ayrıcalıklarını geçersiz bir şekilde ilerletebilirsiniz.

> [!CAUTION]
> Yeni kullanıcılar yalnızca *bulut sahibi grubu*, *bulut-genel-küme-yönetici-grubu*, *bulut-genel-depolama-yönetici-grubu*, bulut-genel- *Ağ-Yönetici-Grup* veya *bulut-genel-VM-yönetici grubu* için eklenmelidir.  *Yöneticiler* grubuna eklenen kullanıcılar otomatik olarak kaldırılacaktır.  Yalnızca hizmet hesaplarının *Yöneticiler* grubuna eklenmesi gerekir ve hizmet hesapları vSphere Web Kullanıcı arabiriminde oturum açmak için kullanılmamalıdır.

## <a name="extend-privilege-escalation"></a>Ayrıcalık yükseltmeyi uzat

Görevlerinizi tamamlaması için ek süre gerekliyse, ayrıcalık yükseltme dönemini genişletebilirsiniz.  Yönetim görevlerini tamamlamanıza izin veren ek ilerleme zaman aralığını seçin.

1.   >  Cloudsimple portalındaki kaynaklar **özel bulutlarında** , ayrıcalık yükseltme 'yi genişletmek istediğiniz özel bulutu seçin.

2. Özet sekmesinin altındaki **ayrıcalık yükseltmeyi Genişlet**' e tıklayın.

    ![Ayrıcalık yükseltmeyi uzat](media/de-escalate-private-cloud-privilege.png)

3. Açılan listeden bir ilerleme zaman aralığı seçin. Yeni ilerletme bitiş saatini gözden geçirin.

4. Aralığı genişletmek için **Kaydet** ' e tıklayın.

## <a name="de-escalate-privileges"></a>Ön yükseltme ayrıcalıkları

Yönetim görevleriniz tamamlandıktan sonra, ayrıcalıklarınızı geçersiz bir şekilde ilerletin.  

1.   >  Cloudsimple portalındaki kaynaklar **özel bulutlarında** , ayrıcalıklarını yükseltmek istediğiniz özel bulutu seçin.

2. **Ön yükseltme**' ye tıklayın.

3. **Tamam**'a tıklayın.

> [!IMPORTANT]
> Herhangi bir hatayı önlemek için vCenter oturumunu kapatın ve yükseltme ayrıcalıklarından sonra tekrar oturum açın.

## <a name="next-steps"></a>Sonraki adımlar

* [Kullanılacak vCenter Identity kaynaklarını ayarlayın Active Directory](./set-vcenter-identity.md)
* Yedekleme [iş yükü sanal makinelerini](./backup-workloads-veeam.md) yedekleme çözümünü yükler
