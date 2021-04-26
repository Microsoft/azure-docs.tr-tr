---
title: include dosyası
description: " dosyası"
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 04/16/2021
ms.openlocfilehash: e119d40cd0b8f482d33c3c86c644cf6a0846390a
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727134"
---
## <a name="generalize-the-image"></a>Görüntüyü genelleştirin

Azure Marketi 'ndeki tüm görüntülerin genel bir biçimde yeniden kullanılabilir olması gerekir. Bunu başarmak için, işletim sistemi VHD 'SI, bir VM 'den örneğe özgü tüm tanımlayıcıları ve yazılım sürücülerini kaldıran bir işlem olan genelleştirilmelidir.

### <a name="for-windows"></a>Windows için

Windows işletim sistemi diskleri, [Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) aracıyla genelleştirilmiştir. Daha sonra işletim sistemini güncelleştirirseniz veya yeniden yapılandırırsanız, Sysprep 'i yeniden çalıştırmanız gerekir.

> [!WARNING]
> Sysprep 'i çalıştırdıktan sonra, güncelleştirmeler otomatik olarak çalıştırılabileceğinden, sanal makineyi dağıtıldıktan sonra devre dışı bırakın. Bu kapanıyor, sonraki güncelleştirmelerin işletim sisteminde veya yüklü hizmetlerde örneğe özgü değişiklikler yapmasını önler. Sysprep 'i çalıştırma hakkında daha fazla bilgi için bkz. [BIR VHD Genelleştirme adımları](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>Linux için

Aşağıdaki işlem bir Linux sanal makinesini genelleştirir ve ayrı bir VM olarak yeniden dağıtır. Ayrıntılar için bkz. [sanal makine veya VHD 'nin görüntüsünü oluşturma](../../virtual-machines/linux/capture-image.md). "Yakalanan görüntüden sanal makine oluşturma" adlı bölüme ulaştığınızda durulabiliyor olabilirsiniz.

1. Azure Linux aracısını kaldırın.
    1. SSH istemcisi kullanarak Linux sanal makinenize bağlanın.
    2. SSH penceresinde şu komutu girin: `sudo waagent –deprovision+user` .
    3. Devam etmek için Y yazın (onay adımını önlemek için önceki komuta-zorlama parametresini ekleyebilirsiniz).
    4. Komut tamamlandıktan sonra SSH istemcisini kapatmak için **Çıkış** ' ı girin.
2. Sanal makineyi durdurun.
    1. Azure portal, kaynak grubunuzu (RG) seçin ve VM 'yi serbest olarak ayırın.
    2. VM 'niz artık genelleştirilmiştir ve bu VM diskini kullanarak yeni bir VM oluşturabilirsiniz.

### <a name="capture-image"></a>Görüntü yakala

> [!NOTE]
> Imza içeren Azure aboneliğinin, yayımlamak için yayımcı hesabıyla aynı kiracı altında olması gerekir. Ayrıca, yayımcı hesabının SıG içeren aboneliğe en az katkıda bulunan erişimi olmalıdır.

VM 'niz çalışmaya başladıktan sonra Azure Paylaşılan görüntü galerisinde bu dosyayı yakalayabilirsiniz. Yakalamak için aşağıdaki adımları izleyin:

1. [Azure Portal](https://ms.portal.azure.com/), sanal makinenizin sayfasına gidin.
2. **Yakala**' yı seçin.
3. **Görüntüyü paylaşılan görüntüye paylaşma** bölümünde **Evet ' i seçin, görüntü sürümü olarak Galeri ile paylaşabilirsiniz**.
4. **İşletim sistemi durumu** altında Genelleştirilmiş öğesini seçin.
5. Bir hedef görüntü Galerisi seçin veya **Yeni oluştur**.
6. Bir hedef görüntü tanımı seçin veya **Yeni oluştur**.
7. Görüntü için bir **sürüm numarası** belirtin.
8. Seçimlerinizi gözden geçirmek için **gözden geçir + oluştur** ' u seçin.
9. Doğrulama başarılı olduktan sonra **Oluştur**' u seçin.

Erişim vermek için:

1. Paylaşılan görüntü galerisine gidin.
2. Sol panelde **erişim denetimi** (IAM) seçeneğini belirleyin.
3. **Ekle** ' yi ve ardından **rol ataması Ekle**' yi seçin.
4. Bir **rol** veya **sahip** seçin.
5. **Kullanıcı, Grup veya hizmet sorumlusu** seçmek **için erişim ata** altında.
6. Görüntüyü yayımlayan kişinin Azure e-postasını seçin.
7. **Kaydet**’i seçin.

:::image type="content" source="../media/create-vm/add-role-assignment.png" alt-text="Rol ataması Ekle penceresini görüntüler.":::

> [!NOTE]
> Artık Iş Ortağı Merkezi 'nde bir SıG görüntüsü yayımlayabilmeniz için SAS URI 'Leri oluşturmanız gerekmez. Ancak yine de SAS URI 'SI oluşturma adımlarına başvurmanız gerekirse, bkz. [sanal makine görüntüsü IÇIN SAS URI 'si oluşturma](../azure-vm-get-sas-uri.md).
