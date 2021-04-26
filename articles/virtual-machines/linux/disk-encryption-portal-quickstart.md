---
title: Azure portalı ile Linux VM oluşturma ve şifreleme
description: Bu hızlı başlangıçta, bir Linux sanal makinesi oluşturmak ve şifrelemek için Azure portal kullanmayı öğrenirsiniz.
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 188ba72f4da4e5a24554f895473f1c74e48d50fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558364"
---
# <a name="quickstart-create-and-encrypt-a-virtual-machine-with-the-azure-portal"></a>Hızlı başlangıç: Azure portal bir sanal makine oluşturma ve şifreleme

Azure sanal makineleri (VM’ler), Azure portalı üzerinden oluşturulabilir. Azure portal, sanal makineleri ve tüm ilgili kaynaklarını oluşturmaya yönelik tarayıcı tabanlı bir kullanıcı arabirimi sağlar. Bu hızlı başlangıçta, Ubuntu 18,04 LTS çalıştıran bir Linux sanal makinesini (VM) dağıtmak, şifreleme anahtarlarının depolanması için bir Anahtar Kasası oluşturmak ve VM 'yi şifrelemek için Azure portal kullanacaksınız.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

1. Azure portalının sol üst köşesinde bulunan **Kaynak oluştur** öğesini seçin.
1. Yeni sayfada, popüler altında **Ubuntu Server 18,04 LTS**' yi seçin.
1. Temel bilgiler sekmesinde, proje ayrıntıları ' nın altında, doğru aboneliğin seçili olduğundan emin olun.
1. "Kaynak grubu" için **Yeni oluştur**' u seçin. Ad olarak *Myresourcegroup* girin ve **Tamam**' ı seçin.
1. **Sanal makine adı** Için *myvm* yazın.
1. **Bölge** Için *(US) Doğu ABD* seçin.
1. **Boyutun** *Standart D2s v3* olduğundan emin olun.
1. **Yönetici hesabı** altında, **kimlik doğrulama türü** olarak *parola* ' yı seçin. Bir Kullanıcı adı ve parola girin.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-linux-vm-creation.png" alt-text="Linux VM oluşturma ekranı":::

    > [!WARNING]
    > "Diskler" sekmesi **disk seçeneklerinde** bir "şifreleme türü" alanı sunar. Bu alan, Azure disk şifrelemesi için **değil** , [yönetilen diskler](../managed-disks-overview.md) + CMK için şifreleme seçeneklerini belirtmek için kullanılır.
    >
    > Karışıklığı önlemek için, bu öğreticiyi tamamlarken *diskler* sekmesini tamamen atlamanızı öneririz.

1. "Yönetim" sekmesini seçin ve bir tanılama depolama hesabınız olduğunu doğrulayın. Depolama hesabınız yoksa, *Yeni oluştur*' u seçin, depolama hesabınızı *mystorageaccount* olarak adlandırın ve "Tamam" ı seçin

    :::image type="content" source="../media/disk-encryption/portal-quickstart-vm-creation-storage.png" alt-text="ResourceGroup oluşturma ekranı":::

1. "Gözden geçir + oluştur" a tıklayın.
1. **Sanal makine oluştur** sayfasında oluşturmak üzere olduğunuz VM'nin ayrıntılarını görüntüleyebilirsiniz. Hazır olduğunuzda **Oluştur**'u seçin.

VM'nizin dağıtılması birkaç dakika sürer. Dağıtım tamamlandıktan sonra bir sonraki bölüme geçin.

## <a name="encrypt-the-virtual-machine"></a>Sanal makineyi şifreleyin

1. VM dağıtımı tamamlandığında **Kaynağa Git**' i seçin.
1. Sol taraftaki kenar çubuğundan **diskler**' i seçin.
1. Üstteki çubukta **ek ayarlar** ' ı seçin.
1. Şifrelemek için **şifreleme ayarları**  >  **diskleri** altında, **işletim sistemi ve veri diskleri**' ni seçin.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-disks-to-encryption.png" alt-text="İşletim sistemi ve veri disklerini vurgulayan ekran görüntüsü.":::

1. **Şifreleme ayarları** altında, **şifreleme için bir anahtar kasası ve anahtar seçin**' i seçin.
1. **Azure Key Vault anahtar seçin** ekranında **Yeni oluştur**' u seçin.

    :::image type="content" source="../media/disk-encryption/portal-qs-keyvault-create.png" alt-text="Vurguların yeni oluştur ekran görüntüsü.":::

1. **Anahtar kasasının ve anahtarın** sol tarafında **bir anahtar seçmek Için tıklayın ' ı** seçin.
1. **Azure Key Vault anahtarı seç** bölümünde, **Key Vault** alanı altında **Yeni oluştur**' u seçin.
1. **Anahtar Kasası oluştur** ekranında, kaynak grubunun *myresourcegroup* olduğundan emin olun ve anahtar kasanıza bir ad verin.  Azure genelindeki her Anahtar Kasası benzersiz bir ada sahip olmalıdır.
1. **Erişim ilkeleri** sekmesinde, **birim şifrelemesi Için Azure disk şifrelemesi** kutusunu işaretleyin.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-keyvault-enable.png" alt-text="diskler ve şifreleme seçimi":::

1. **Gözden geçir ve oluştur**’u seçin.  
1. Anahtar Kasası doğrulamadan geçtikten sonra **Oluştur**' u seçin. Bu, sizi **Azure Key Vault ekranından seçim tuşuna** geri döndürür.
1. **Anahtar** alanını boş bırakın ve **Seç**' i seçin.
1. Şifreleme ekranının üst kısmında **Kaydet**' e tıklayın. Bir açılan pencere, sanal makinenin yeniden başlatılacağını uyarır. **Evet**'e tıklayın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, sanal makineyi ve tüm ilişkili kaynakları silebilirsiniz. Bunu yapmak için, sanal makinenin kaynak grubunu ve Sil’i seçin, ardından silinecek kaynak grubunun adını onaylayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, şifreleme anahtarları için etkinleştirilen bir Key Vault oluşturdunuz, bir sanal makine oluşturdunuz ve sanal makineyi şifreleme için etkinleştirdiniz.  

> [!div class="nextstepaction"]
> [Azure disk şifrelemeye genel bakış](disk-encryption-overview.md)