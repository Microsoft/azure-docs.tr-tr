---
title: Azure Data Lake Storage 2. ACL 'Leri ayarlamak için Azure CLı 'yi kullanma
description: Hiyerarşik bir ad alanı olan depolama hesaplarında erişim denetim listelerini (ACL) yönetmek için Azure CLı 'yi kullanın.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5ec7d2b243a5eadab2d22dea14ebeac8eabb1722
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563173"
---
# <a name="use-azure-cli-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. ACL 'Leri yönetmek için Azure CLı kullanma

Bu makalede, dizin ve dosyaların erişim denetim listelerini almak, ayarlamak ve güncelleştirmek için [Azure Command-Line arabirimi 'nin (CLI)](/cli/azure/) nasıl kullanılacağı gösterilmektedir.

ACL devralma, bir üst dizin altında oluşturulan yeni alt öğeler için zaten kullanılabilir. Ancak, bu değişiklikleri her alt öğe için ayrı ayrı yapmak zorunda kalmadan ACL 'Leri bir üst dizinin varolan alt öğelerine yinelemeli olarak ekleyebilir, güncelleştirebilir ve kaldırabilirsiniz.

[Başvuru](/cli/azure/storage/fs/access)  |  [Örnekler](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)  |  [Geri bildirimde](https://github.com/Azure/azure-cli-extensions/issues) bulunun

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).

- Hiyerarşik ad alanı etkin olan bir depolama hesabı. Bir tane oluşturmak için [Bu](create-data-lake-storage-account.md) yönergeleri izleyin.

- Azure CLı sürümü `2.14.0` veya üzeri.

- Aşağıdaki güvenlik izinlerinden biri:

  - Hedef kapsayıcının, üst kaynak grubunun veya aboneliğin kapsamındaki [Depolama Blobu veri sahibi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) rolüne atanan sağlanmış bir Azure Active Directory (Azure AD) [güvenlik sorumlusu](../../role-based-access-control/overview.md#security-principal) .  

  - ACL ayarlarını uygulamayı planladığınız Hedef kapsayıcının veya dizinin sahibi olan kullanıcı. ACL 'Leri yinelemeli olarak ayarlamak için, hedef kapsayıcı veya dizindeki tüm alt öğeleri içerir.
  
  - Depolama hesabı anahtarı.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Azure CLı 'nin doğru sürümünün yüklü olduğundan emin olun

1. [Azure Cloud Shell](../../cloud-shell/overview.md)açın veya Azure CLI 'yı yerel olarak [yüklediyseniz](/cli/azure/install-azure-cli) , Windows PowerShell gibi bir komut konsol uygulaması açın.

2. Yüklü olan Azure CLı sürümünün `2.14.0` aşağıdaki komutu kullanarak veya daha yüksek olduğunu doğrulayın.

   ```azurecli
    az --version
   ```

   Azure CLı sürümünüz daha düşükse `2.14.0` , daha sonra yeni bir sürüm yüklersiniz. Bkz. [Azure CLI 'Yi yüklemeyi](/cli/azure/install-azure-cli).

## <a name="connect-to-the-account"></a>Hesaba Bağlan

1. Azure CLı 'yi yerel olarak kullanıyorsanız Login komutunu çalıştırın.

   ```azurecli
   az login
   ```

   CLı varsayılan tarayıcınızı açabildiğinden, bunu yapılır ve bir Azure oturum açma sayfası yükler.

   Aksi takdirde, konumunda bir tarayıcı sayfası açın [https://aka.ms/devicelogin](https://aka.ms/devicelogin) ve terminalinizde görünen yetkilendirme kodunu girin. Ardından, tarayıcıda hesap kimlik bilgilerinizle oturum açın.

   Farklı kimlik doğrulama yöntemleri hakkında daha fazla bilgi edinmek için bkz. [Azure CLI ile blob veya kuyruk verilerine erişim yetkisi verme](./authorize-data-operations-cli.md).

2. Kimliğiniz birden fazla abonelikle ilişkiliyse, etkin aboneliğinizi statik Web sitenizi barındıracak depolama hesabının aboneliğine ayarlayın.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>`Yer tutucu değerini ABONELIĞINIZIN kimliğiyle değiştirin.

> [!NOTE]
> Bu makalede sunulan örnekte Azure Active Directory (Azure AD) yetkilendirmesi gösterilmektedir. Yetkilendirme yöntemleri hakkında daha fazla bilgi edinmek için bkz. [Azure CLI ile blob veya kuyruk verilerine erişim yetkisi verme](./authorize-data-operations-cli.md).

## <a name="get-acls"></a>ACL 'Leri al

Komutunu kullanarak bir **DIZININ** ACL 'sini alın `az storage fs access show` .

Bu örnek, bir dizinin ACL 'sini alır ve ardından ACL 'yi konsola yazdırır.

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Komutunu kullanarak bir **dosyanın** erişim izinlerini alın `az storage fs access show` . 

Bu örnek, bir dosyanın ACL 'sini alır ve ardından ACL 'yi konsola yazdırır.

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Aşağıdaki görüntüde bir dizinin ACL 'SI alındıktan sonra çıkış gösterilmektedir.

![ACL çıkışı al](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

Bu örnekte, sahip olan kullanıcının okuma, yazma ve yürütme izinleri vardır. Sahip olan grubun yalnızca okuma ve yürütme izinleri vardır. Erişim denetim listeleri hakkında daha fazla bilgi için bkz. [Azure Data Lake Storage 2. Access Control](data-lake-storage-access-control.md).

## <a name="set-acls"></a>ACL 'Leri ayarla

Bir ACL *ayarladığınızda* , tüm ACL 'leri dahil olmak üzere tüm ACL 'yi **değiştirirsiniz** . Bir güvenlik sorumlusunun izin düzeyini değiştirmek veya var olan girişleri etkilemeden ACL 'ye yeni bir güvenlik sorumlusu eklemek istiyorsanız, ACL 'yi *güncelleştirmeniz* gerekir. Bunu değiştirmek yerine bir ACL 'yi güncelleştirmek için, bu makalenin [ACL 'Leri Güncelleştir](#update-acls) bölümüne bakın.  

ACL 'yi *ayarlamayı* seçerseniz, sahip olan kullanıcı için bir giriş, sahip olan grup için bir giriş ve diğer tüm kullanıcılar için bir girdi eklemeniz gerekir. Sahip olan Kullanıcı, sahip olan grup ve diğer tüm kullanıcılar hakkında daha fazla bilgi edinmek için bkz. [Kullanıcılar ve kimlikler](data-lake-storage-access-control.md#users-and-identities).

Bu bölümde nasıl yapılacağı gösterilmektedir:

- ACL ayarla
- ACL’leri özyinelemeli olarak belirleme

### <a name="set-an-acl"></a>ACL ayarla

`az storage fs access set`Bir **dizinin** ACL 'sini ayarlamak için komutunu kullanın. 

Bu örnek, ACL 'yi sahip olan Kullanıcı, sahip olan grup veya diğer kullanıcılar için bir dizinde ayarlar ve ardından ACL 'yi konsola yazdırır.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Bu örnek, sahip olan Kullanıcı, sahip olan grup veya diğer kullanıcılar için bir dizinde *varsayılan* ACL 'yi ayarlar ve ardından ACL 'yi konsola yazdırır.

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

`az storage fs access set`Bir **dosyanın** ACL 'sini ayarlamak için komutunu kullanın. 

Bu örnek, sahip olan Kullanıcı, sahip olan grup veya diğer kullanıcılar için bir dosyadaki ACL 'yi ayarlar ve ardından ACL 'yi konsola yazdırır.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Belirli bir grubun veya kullanıcının ACL 'sini ayarlamak için ilgili nesne kimliklerini kullanın. Örneğin `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` veya `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` olabilir.

Aşağıdaki görüntüde, bir dosyanın ACL 'sini ayarlamadıktan sonra çıkış gösterilmektedir.

![ACL çıkışı al 2](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

Bu örnekte, sahip olan Kullanıcı ve sahip olan Grup yalnızca okuma ve yazma izinlerine sahiptir. Diğer tüm kullanıcılar yazma ve yürütme izinlerine sahiptir. Erişim denetim listeleri hakkında daha fazla bilgi için bkz. [Azure Data Lake Storage 2. Access Control](data-lake-storage-access-control.md).

### <a name="set-acls-recursively"></a>ACL’leri özyinelemeli olarak belirleme

[Az Storage FS Access set-özyinelemeli](/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive) komutunu kullanarak ACL 'leri yinelemeli olarak ayarlayın.

Bu örnek, adlı bir dizinin ACL 'sini ayarlar `my-parent-directory` . Bu girişler, sahip olan kullanıcıya okuma, yazma ve yürütme izinleri verir, sahip olan gruba yalnızca okuma ve yürütme izinleri verir ve tüm diğerlerine erişim vermez. Bu örnekteki son ACL girişi, "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" okuma ve yürütme izinlerine sahip belirli bir kullanıcıya izin verir.

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> **Varsayılan** bir ACL girişi ayarlamak istiyorsanız, `default:` Her girişe öneki ekleyin. Örneğin `default:user::rwx` veya `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x` olabilir.

## <a name="update-acls"></a>ACL 'Leri Güncelleştir

Bir ACL 'yi *güncelleştirdiğinizde* ACL 'yi DEğIşTIRMEk yerine ACL 'yi değiştirirsiniz. Örneğin, ACL 'de listelenen diğer güvenlik sorumlularını etkilemeden ACL 'ye yeni bir güvenlik sorumlusu ekleyebilirsiniz.  ACL 'yi güncelleştirmek yerine değiştirmek için, bu makalenin [ACL 'Leri ayarlama](#set-acls) bölümüne bakın.

Bir ACL 'yi güncelleştirmek için, güncelleştirmek istediğiniz ACL girdisiyle yeni bir ACL nesnesi oluşturun ve ACL 'yi Güncelleştir işleminde bu nesneyi kullanın. Mevcut ACL 'yi kullanmayın, yalnızca görüntülenecek ACL girdilerini sağlayın.

Bu bölümde nasıl yapılacağı gösterilmektedir:

- ACL güncelleştirme
- ACL 'Leri yinelemeli olarak güncelleştir

### <a name="update-an-acl"></a>ACL güncelleştirme

Bu izni ayarlamaya yönelik başka bir yol ise komutunu kullanmaktır `az storage fs access set` . 

Bir `-permissions` ACL 'nin kısa biçimine parametresini ayarlayarak bir dizin veya DOSYANıN ACL 'sini güncelleştirin.

Bu örnek, bir **DIZININ** ACL 'sini günceller.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Bu örnek, bir **DOSYANıN** ACL 'sini günceller.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Belirli bir grubun veya kullanıcının ACL 'sini güncelleştirmek için ilgili nesne kimliklerini kullanın. Örneğin `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` veya `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` olabilir.

Ayrıca, `--owner` `group` bir kullanıcının varlık kimliğine veya Kullanıcı asıl ADıNA (UPN) veya parametrelerini ayarlayarak bir dizin ya da dosya için sahip olan Kullanıcı ve grubu güncelleştirebilirsiniz.

Bu örnek, bir dizinin sahibini değiştirir.

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Bu örnek, bir dosyanın sahibini değiştirir. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login

```

### <a name="update-acls-recursively"></a>ACL 'Leri yinelemeli olarak güncelleştir

[Az Storage FS Access Update-özyinelemeli](/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive) komutunu kullanarak ACL 'leri yinelemeli olarak güncelleştirin.

Bu örnekte, yazma izniyle bir ACL girişi güncelleştirilir.

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> **Varsayılan** bir ACL girişini güncelleştirmek istiyorsanız, `default:` Her girişe öneki ekleyin. Örneğin, `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

## <a name="remove-acl-entries-recursively"></a>ACL girdilerini yinelemeli olarak kaldır

Bir veya daha fazla ACL girişini yinelemeli olarak kaldırabilirsiniz. Bir ACL girişini kaldırmak için, ACL girdisi kaldırılacak yeni bir ACL nesnesi oluşturun ve sonra ACL 'yi kaldır işleminde bu nesneyi kullanın. Mevcut ACL 'yi almaz, kaldırılacak ACL girdilerini sağlamanız yeterlidir.

[Az Storage FS Access Remove-özyinelemeli](/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive) komutunu kullanarak acl girişlerini kaldırın.

Bu örnek, kapsayıcının kök dizininden bir ACL girişini kaldırır.  

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> **Varsayılan** bir ACL girişini kaldırmak istiyorsanız, `default:` Her girişe öneki ekleyin. Örneğin, `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

## <a name="recover-from-failures"></a>Hatalardan kurtarma

ACL 'Leri yinelemeli olarak değiştirirken çalışma zamanı veya izin hatalarıyla karşılaşabilirsiniz. Çalışma zamanı hataları için işlemi baştan başlatın. Güvenlik sorumlusu, değiştirilen Dizin hiyerarşisindeki bir dizinin veya dosyanın ACL 'sini değiştirmek için yeterli izne sahip değilse, izin hataları oluşabilir. İzin sorununu çözün ve sonra bir devamlılık belirteci kullanarak işlemi hata noktasından sürdürmeyi seçin ya da işlemi baştan başlatın. Baştan başlatmayı tercih ediyorsanız devamlılık belirtecini kullanmanız gerekmez. ACL girdilerini negatif bir etki olmadan yeniden uygulayabilirsiniz.

Bir hata durumunda, parametresini olarak ayarlayarak bir devamlılık belirteci döndürebilirsiniz `--continue-on-failure` `false` . Hataları adresledikten sonra, komutu yeniden çalıştırarak ve sonra parametreyi devamlılık belirtecine ayarlayarak işlemi hata noktasından devam ettirebilirsiniz `--continuation` .

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure false --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

İşlemin, izin hatalarıyla kesintiye uğramadan tamamlanmasını istiyorsanız, bunu belirtebilirsiniz.

İşlemin kesintisiz tamamlanmasını sağlamak için `--continue-on-failure` parametresini olarak ayarlayın `true` .

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Ayrıca bkz.

- [Örnekler](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [Görüş bildirin](https://github.com/Azure/azure-cli-extensions/issues)
- [Bilinen sorunlar](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Azure Data Lake Storage 2. 'de erişim denetimi modeli](data-lake-storage-access-control.md)
- [Azure Data Lake Storage 2. 'de erişim denetim listeleri (ACL 'Ler)](data-lake-storage-access-control.md)