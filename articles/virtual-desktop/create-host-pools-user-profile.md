---
title: Windows sanal masaüstü FSLogix profili kapsayıcı paylaşma-Azure
description: Sanal makine tabanlı dosya paylaşımının kullanıldığı bir Windows sanal masaüstü konak havuzu için FSLogix profil kapsayıcısını ayarlama.
author: Heidilohr
ms.topic: how-to
ms.date: 08/20/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: 69582f26544d954088fd5a57bfe5730750b7b5a8
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447922"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>Dosya paylaşımı kullanarak ana bilgisayar havuzu için profil kapsayıcısı oluşturma

Windows sanal masaüstü hizmeti, önerilen Kullanıcı profili çözümü olarak FSLogix profil kapsayıcıları sunar. Windows sanal masaüstü 'nün gelecek sürümlerinde kullanım dışı bırakılacak Kullanıcı profili diski (UPD) çözümünü kullanmanızı önermiyoruz.

Bu makalede, sanal makine tabanlı dosya paylaşımından bir konak havuzu için bir FSLogix profil kapsayıcısı paylaşımının nasıl ayarlanacağı açıklanır. Dosya paylaşımları yerine Azure dosyaları kullanmanızı kesinlikle öneririz. Daha fazla FSLogix belgeleri için bkz. [fslogix sitesi](https://docs.fslogix.com/).

>[!NOTE]
>Azure 'daki farklı FSLogix profili kapsayıcı depolama seçenekleri hakkında daha fazla bilgi arıyorsanız, bkz. [FSLogix profil kapsayıcıları Için depolama seçenekleri](store-fslogix-profile.md).

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Dosya paylaşma işlevi görecek yeni bir sanal makine oluşturun

Sanal makineyi oluştururken, konak havuzu sanal makineleriyle aynı sanal ağa veya konak havuzu sanal makinelerine bağlantısı olan bir sanal ağa yerleştirdiğinizden emin olun. Bir sanal makineyi birden çok şekilde oluşturabilirsiniz:

- [Azure Galeri görüntüsünden sanal makine oluşturma](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Yönetilen görüntüden sanal makine oluşturma](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Yönetilmeyen görüntüden sanal makine oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Sanal makineyi oluşturduktan sonra, aşağıdaki işlemleri gerçekleştirerek etki alanına ekleyin:

1. Sanal makineyi oluştururken girdiğiniz kimlik bilgileriyle [sanal makineye bağlanın](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) .
2. Sanal makinede, **Denetim Masası** ' nı başlatın ve **sistem**' i seçin.
3. **Bilgisayar adı**' nı seçin, **Ayarları Değiştir**' i seçin ve ardından Değiştir ' i seçin **.**
4. **Etki alanı** ' nı seçin ve ardından sanal ağda Active Directory etki alanını girin.
5. Etki alanına katma makinelere ayrıcalıkları olan bir etki alanı hesabıyla kimlik doğrulaması yapın.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>Sanal makineyi Kullanıcı profilleri için dosya paylaşma görevi görecek şekilde hazırlama

Aşağıda, bir sanal makinenin Kullanıcı profilleri için dosya paylaşma işlevi görecek şekilde hazırlanmasına ilişkin genel yönergeler verilmiştir:

1. Windows sanal masaüstü Active Directory kullanıcılarını [Active Directory bir güvenlik grubuna](/windows/security/identity-protection/access-control/active-directory-security-groups/)ekleyin. Bu güvenlik grubu, yeni oluşturduğunuz dosya paylaşımının sanal makinesine Windows sanal masaüstü kullanıcılarının kimliğini doğrulamak için kullanılacaktır.
2. [Dosya paylaşımının sanal makinesine bağlanın](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine).
3. Dosya paylaşma sanal makinesinde, **C sürücüsünde** profil paylaşma olarak kullanılacak bir klasör oluşturun.
4. Yeni klasöre sağ tıklayın, **Özellikler**' i seçin, **Paylaşım**' ı seçin ve **Gelişmiş paylaşım...** öğesini seçin.
5. **Bu klasörü paylaşma**, **izinler...** öğesini seçin ve ardından **Ekle...** seçeneğini belirleyin.
6. Windows sanal masaüstü kullanıcılarını eklediğiniz güvenlik grubunu arayın, sonra grubun **tam denetime** sahip olduğundan emin olun.
7. Güvenlik grubu eklendikten sonra, klasöre sağ tıklayın, **Özellikler**' i seçin, **Paylaşım**' ı seçin ve ardından **ağ yolunu** daha sonra kullanmak üzere kopyalayın.

İzinler hakkında daha fazla bilgi için bkz. [Fslogix belgeleri](/fslogix/fslogix-storage-config-ht/).

## <a name="configure-the-fslogix-profile-container"></a>FSLogix profil kapsayıcısını yapılandırma

Sanal makineleri FSLogix yazılımıyla yapılandırmak için, konak havuzuna kayıtlı her makinede aşağıdakileri yapın:

1. Sanal makineyi oluştururken girdiğiniz kimlik bilgileriyle [sanal makineye bağlanın](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) .
2. Bir internet tarayıcısı başlatın ve FSLogix aracısını indirmek için [Bu bağlantıya](https://go.microsoft.com/fwlink/?linkid=2084562) gidin.
3. \\ \\ \\ . Zip dosyasında Win32 yayını veya \\ \\ x64 \\ sürümüne gidin ve Fslogixappssetup ' ı çalıştırarak fslogix aracısını kurun.   FSLogix 'i yükleme hakkında daha fazla bilgi edinmek için bkz. [Fslogix indirme ve yükleme](/fslogix/install-ht/).
4. Aracının yüklendiğini doğrulamak için **Program Files**  >  **fslogix**  >  **uygulamalarına** gidin.
5. Başlat menüsünde, **Regedit** komutunu yönetici olarak çalıştırın. **Bilgisayar \\ HKEY_LOCAL_MACHINE \\ yazılım \\ fslogix** sayfasına gidin.
6. **Profiller** adlı bir anahtar oluşturun.
7. Profiller anahtarı için aşağıdaki değerleri oluşturun:

| Ad                | Tür               | Veri/değer                        |
|---------------------|--------------------|-----------------------------------|
| Etkin             | DWORD              | 1                                 |
| Vhdkonumları        | Çok dizeli değer | "Dosya paylaşımının ağ yolu"     |

>[!IMPORTANT]
>Azure 'da Windows sanal masaüstü ortamınızı güvenli hale getirmeye yardımcı olmak için, VM 'leriniz üzerinde gelen bağlantı noktası 3389 ' i açmanız önerilir. Windows sanal masaüstü, kullanıcıların konak havuzunun VM 'lerine erişmesi için açık bir gelen bağlantı noktası 3389 gerektirmez. Sorun giderme amacıyla bağlantı noktası 3389 ' i açmanız gerekiyorsa, [tam ZAMANıNDA VM erişimi](../security-center/security-center-just-in-time.md)kullanmanızı öneririz.
