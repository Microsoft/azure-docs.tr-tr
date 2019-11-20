---
title: Windows sanal masaüstü konak havuzu Azure Resource Manager-Azure
description: Windows sanal masaüstü 'nde bir Azure Resource Manager şablonuyla konak havuzu oluşturma.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 23d032a2496e975c7e6ceafb61691c2cb1216218
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605759"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Azure Resource Manager şablonuyla ana bilgisayar havuzu oluşturma

Konak havuzları, Windows sanal masaüstü kiracı ortamlarında bir veya daha fazla özdeş sanal makine koleksiyonudur. Her konak havuzu, kullanıcıların fiziksel bir masaüstünde yaptıkları gibi etkileşime girebilecekleri bir uygulama grubu içerebilir.

Microsoft tarafından sunulan bir Azure Resource Manager şablonuyla Windows sanal masaüstü kiracısı için bir konak havuzu oluşturmak üzere bu bölümün yönergelerini izleyin. Bu makalede, Windows sanal masaüstü 'nde bir konak havuzu oluşturma, bir Azure aboneliğinde VM 'Ler içeren bir kaynak grubu oluşturma, bu VM 'leri AD etki alanına ekleme ve VM 'Leri Windows sanal masaüstü ile kaydetme hakkında bilgi alınacaktır.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Azure Resource Manager şablonunu çalıştırmak için gerekenler

Azure Resource Manager şablonunu çalıştırmadan önce aşağıdaki şeyleri öğrendiğinizden emin olun:

- Kullanmak istediğiniz görüntünün kaynağı. Azure galerisinden mi yoksa özel mi?
- Etki alanınıza katılarak kimlik bilgileri.
- Windows sanal masaüstü kimlik bilgileriniz.

Azure Resource Manager şablonuyla bir Windows sanal masaüstü konak havuzu oluşturduğunuzda, Azure Galerisi 'nden, yönetilen bir görüntüden veya yönetilmeyen görüntüden bir sanal makine oluşturabilirsiniz. VM görüntülerini oluşturma hakkında daha fazla bilgi edinmek için bkz. Azure ['a yüklemek için bir WINDOWS VHD veya vhdx hazırlama](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) ve [Azure 'DA genelleştirilmiş bir VM 'Nin yönetilen görüntüsünü oluşturma](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Yeni bir konak havuzu sağlamak için Azure Resource Manager şablonunu çalıştırma

Başlamak için [Bu GITHUB URL](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool)'sine gidin.

### <a name="deploy-the-template-to-azure"></a>Şablonu Azure 'a dağıtma

Kurumsal abonelikte dağıtım yapıyorsanız, aşağı kaydırın ve **Azure 'A dağıt**' ı seçin, sonra da görüntü kaynağınıza göre parametreleri doldurun.

' Yi bir bulut çözümü sağlayıcısı aboneliğine dağıtıyorsanız, Azure 'a dağıtmak için aşağıdaki adımları izleyin:

1. Aşağı kaydırın ve **Azure 'A dağıt**' a sağ tıkladıktan sonra **bağlantı konumunu Kopyala**' yı seçin.
2. Not Defteri gibi bir metin Düzenleyicisi açın ve bağlantıyı buraya yapıştırın.
3. "https://portal.azure.com/" işaretinden önce ve diyez etiketinden önce (#) bir at işareti (@) ve ardından kiracı etki alanı adını girin. Aşağıda, kullanmanız gereken biçime bir örnek verilmiştir: https://portal.azure.com/@Contoso.onmicrosoft.com#create/.
4. Bulut çözümü sağlayıcısı aboneliğine yönetici/katkıda bulunan izinleri olan bir kullanıcı olarak Azure portal oturum açın.
5. Metin düzenleyicisine kopyaladığınız bağlantıyı adres çubuğuna yapıştırın.

Senaryonuza yönelik girmeniz gereken parametreler hakkında rehberlik için bkz. Windows sanal masaüstü [Benioku dosyası](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md). Benioku dosyası her zaman en son değişikliklerle güncellenir.

## <a name="assign-users-to-the-desktop-application-group"></a>Kullanıcıları Masaüstü uygulama grubuna atama

GitHub Azure Resource Manager şablonu tamamlandıktan sonra, sanal makinelerinizdeki tam oturum masaüstlerini teste başlamadan önce Kullanıcı erişimi atayın.

İlk olarak, henüz yapmadıysanız PowerShell oturumunuzda kullanmak üzere [Windows sanal masaüstü PowerShell modülünü indirip içeri aktarın](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) .

Kullanıcıları Masaüstü uygulama grubuna atamak için bir PowerShell penceresi açın ve Windows sanal masaüstü ortamında oturum açmak üzere bu cmdlet 'i çalıştırın:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Bundan sonra, bu cmdlet 'i kullanarak masaüstü uygulama grubuna kullanıcı ekleyin:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Kullanıcının UPN 'si Azure Active Directory kullanıcının kimliğiyle eşleşmelidir (örneğin, user1@contoso.com). Birden çok kullanıcı eklemek istiyorsanız, her kullanıcı için bu cmdlet 'i çalıştırmanız gerekir.

Bu adımları tamamladıktan sonra, Masaüstü uygulama grubuna eklenen kullanıcılar Windows sanal masaüstünde desteklenen uzak masaüstü istemcileriyle oturum açabilir ve oturum Masaüstü için bir kaynak görebilirler.

>[!IMPORTANT]
>Azure 'da Windows sanal masaüstü ortamınızı güvenli hale getirmeye yardımcı olmak için, VM 'leriniz üzerinde gelen bağlantı noktası 3389 ' i açmanız önerilir. Windows sanal masaüstü, kullanıcıların konak havuzunun VM 'lerine erişmesi için açık bir gelen bağlantı noktası 3389 gerektirmez. Sorun giderme amacıyla bağlantı noktası 3389 ' i açmanız gerekiyorsa, [tam ZAMANıNDA VM erişimi](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)kullanmanızı öneririz.