---
title: Windows sanal masaüstü FSLogix profili kapsayıcı paylaşma-Azure
description: Sanal makine tabanlı dosya paylaşımının kullanıldığı bir Windows sanal masaüstü konak havuzu için FSLogix profil kapsayıcısını ayarlama.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: helohr
ms.openlocfilehash: 65d800cc6c1b6818369807ffeae9cd350a34066f
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606999"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>Dosya paylaşımı kullanarak ana bilgisayar havuzu için profil kapsayıcısı oluşturma

Windows sanal masaüstü hizmeti, önerilen Kullanıcı profili çözümü olarak FSLogix profil kapsayıcıları sunar. Windows sanal masaüstü 'nün gelecek sürümlerinde kullanım dışı bırakılacak Kullanıcı profili diski (UPD) çözümünü kullanmanızı önermiyoruz.

Bu makalede, sanal makine tabanlı dosya paylaşımından bir konak havuzu için bir FSLogix profil kapsayıcısı paylaşımının nasıl ayarlanacağı açıklanır. Daha fazla FSLogix belgeleri için bkz. [fslogix sitesi](https://docs.fslogix.com/).

>[!NOTE]
>Azure 'daki farklı FSLogix profili kapsayıcı depolama seçenekleri hakkında daha fazla bilgi arıyorsanız, bkz. [FSLogix profil kapsayıcıları Için depolama seçenekleri](store-fslogix-profile.md).

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Dosya paylaşma işlevi görecek yeni bir sanal makine oluşturun

Sanal makineyi oluştururken, konak havuzu sanal makineleriyle aynı sanal ağa veya konak havuzu sanal makinelerine bağlantısı olan bir sanal ağa yerleştirdiğinizden emin olun. Bir sanal makineyi birden çok şekilde oluşturabilirsiniz:

- [Azure Galeri görüntüsünden sanal makine oluşturma](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Yönetilen görüntüden sanal makine oluşturma](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Yönetilmeyen görüntüden sanal makine oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Sanal makineyi oluşturduktan sonra, aşağıdaki işlemleri gerçekleştirerek etki alanına ekleyin:

1. Sanal makineyi oluştururken girdiğiniz kimlik bilgileriyle [sanal makineye bağlanın](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) .
2. Sanal makinede, **Denetim Masası** ' nı başlatın ve **sistem**' i seçin.
3. **Bilgisayar adı**' nı seçin, **Ayarları Değiştir**' i seçin ve ardından Değiştir ' i seçin **.**
4. **Etki alanı** ' nı seçin ve ardından sanal ağda Active Directory etki alanını girin.
5. Etki alanına katma makinelere ayrıcalıkları olan bir etki alanı hesabıyla kimlik doğrulaması yapın.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>Sanal makineyi Kullanıcı profilleri için dosya paylaşma görevi görecek şekilde hazırlama

Aşağıda, bir sanal makinenin Kullanıcı profilleri için dosya paylaşma işlevi görecek şekilde hazırlanmasına ilişkin genel yönergeler verilmiştir:

1. Windows sanal masaüstü Active Directory kullanıcılarını [Active Directory bir güvenlik grubuna](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-security-groups)ekleyin. Bu güvenlik grubu, yeni oluşturduğunuz dosya paylaşımının sanal makinesine Windows sanal masaüstü kullanıcılarının kimliğini doğrulamak için kullanılacaktır.
2. [Dosya paylaşımının sanal makinesine bağlanın](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine).
3. Dosya paylaşma sanal makinesinde, **C sürücüsünde** profil paylaşma olarak kullanılacak bir klasör oluşturun.
4. Yeni klasöre sağ tıklayın, **Özellikler**' i seçin, **Paylaşım**' ı seçin ve **Gelişmiş paylaşım...** öğesini seçin.
5. **Bu klasörü paylaşma**, **izinler...** öğesini seçin ve ardından **Ekle...** seçeneğini belirleyin.
6. Windows sanal masaüstü kullanıcılarını eklediğiniz güvenlik grubunu arayın, sonra grubun **tam denetime**sahip olduğundan emin olun.
7. Güvenlik grubu eklendikten sonra, klasöre sağ tıklayın, **Özellikler**' i seçin, **Paylaşım**' ı seçin ve ardından **ağ yolunu** daha sonra kullanmak üzere kopyalayın.

İzinler hakkında daha fazla bilgi için bkz. [Fslogix belgeleri](https://docs.microsoft.com/fslogix/fslogix-storage-config-ht).

## <a name="configure-the-fslogix-profile-container"></a>FSLogix profil kapsayıcısını yapılandırma

Sanal makineleri FSLogix yazılımıyla yapılandırmak için, konak havuzuna kayıtlı her makinede aşağıdakileri yapın:

1. Sanal makineyi oluştururken girdiğiniz kimlik bilgileriyle [sanal makineye bağlanın](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) .
2. Bir internet tarayıcısı başlatın ve FSLogix aracısını indirmek için [Bu bağlantıya](https://go.microsoft.com/fwlink/?linkid=2084562) gidin.
3. . Zip dosyasında \\\\Win32\\Release veya \\\\x64\\sürümü ' ne gidin ve **Fslogixappssetup** ' ı çalıştırarak FSLogix aracısını kurun.  FSLogix 'i yükleme hakkında daha fazla bilgi edinmek için bkz. [Fslogix indirme ve yükleme](https://docs.microsoft.com/fslogix/install-ht).
4. Aracının yüklü olduğunu onaylamak için > **Fslogix** > **Apps** **program dosyalarına** gidin.
5. Başlat menüsünde, **Regedit** komutunu yönetici olarak çalıştırın. **\\hkey_local_machıne\\software\\FSLogix bilgisayarına**gidin.
6. **Profiller**adlı bir anahtar oluşturun.
7. Profiller anahtarı için aşağıdaki değerleri oluşturun:

| Ad                | Tür               | Veri/değer                        |
|---------------------|--------------------|-----------------------------------|
| Etkin             | EKLEYEBILECEĞI              | 1                                 |
| Vhdkonumları        | Çok dizeli değer | "Dosya paylaşımının ağ yolu"     |

>[!IMPORTANT]
>Azure 'da Windows sanal masaüstü ortamınızı güvenli hale getirmeye yardımcı olmak için, VM 'leriniz üzerinde gelen bağlantı noktası 3389 ' i açmanız önerilir. Windows sanal masaüstü, kullanıcıların konak havuzunun VM 'lerine erişmesi için açık bir gelen bağlantı noktası 3389 gerektirmez. Sorun giderme amacıyla bağlantı noktası 3389 ' i açmanız gerekiyorsa, [tam ZAMANıNDA VM erişimi](../security-center/security-center-just-in-time.md)kullanmanızı öneririz.
