---
title: Yönetilen HSM 'yi etkileyen bir Azure hizmet kesintisi olursa yapmanız gerekenler-Azure Key Vault | Microsoft Docs
description: Ne yapılacağını öğrenmek için, yönetilen HSM 'yi etkileyen bir Azure hizmet kesintisi vardır.
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 8c284e9993002f6e05e41ca00d00b388feef8f82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376012"
---
# <a name="managed-hsm-disaster-recovery"></a>Yönetilen HSM olağanüstü durum kurtarma

Orijinalin kaybolması veya aşağıdaki nedenlerden herhangi biri nedeniyle kullanılamaması durumunda, HSM 'nizin tam bir çoğaltmasını oluşturmak isteyebilirsiniz:

- Silindi ve sonra temizlendi.
- Bölgedeki çok zararlı bir hata, tüm üye bölümlerinin yok edildiğini sonuçlandı.

Aşağıdaki durumlarda HSM örneğini aynı veya farklı bir bölgede yeniden oluşturabilirsiniz:
- Kaynak HSM 'nin [güvenlik etki alanı](security-domain.md) .
- Güvenlik etki alanını şifreleyen özel anahtarlar (en az çekirdek numarası).
- Kaynak HSM 'den en son tam HSM [yedeklemesi](backup-restore.md) .

Olağanüstü durum kurtarma yordamının adımları şunlardır:

1. Yeni bir HSM örneği oluşturun.
1. "Güvenlik etki alanı kurtarma" yı etkinleştirin. Güvenlik etki alanı aktarımı için yeni bir RSA anahtar çifti (güvenlik etki alanı değişim anahtarı) oluşturulacak ve yanıt olarak gönderilmek üzere bir SecurityDomainExchangeKey (ortak anahtar) olarak indirilecek.
1. "Güvenlik etki alanı aktarım dosyası" nı oluşturup karşıya yükleyin. Güvenlik etki alanını şifreleyen özel anahtarlara ihtiyacınız olacak. Özel anahtarlar yerel olarak kullanılır ve bu işlemde hiçbir yerde hiç aktarılmaz.
1. Yeni HSM 'nin bir yedeğini alın. HSM boş olduğunda bile, herhangi bir geri yüklemeden önce bir yedekleme gereklidir. Yedeklemeler, kolayca geri alma için izin verir.
1. Kaynak HSM 'den son HSM yedeklemesini geri yükleme

Bu adımlar, HSM 'nin içeriğini başka bir bölgeye el ile çoğaltmanıza olanak sağlar. HSM adı (ve hizmet uç noktası URI 'SI) farklı olacak, bu nedenle bu anahtarların farklı bir konumdan kullanımını sağlamak için uygulama yapılandırmanızı değiştirmeniz gerekebilir.

## <a name="create-a-new-managed-hsm"></a>Yeni bir yönetilen HSM oluştur

`az keyvault create`Yönetilen BIR HSM oluşturmak için komutunu kullanın. Bu betik üç zorunlu parametreye sahiptir: bir kaynak grubu adı, HSM adı ve coğrafi konum.

Yönetilen bir HSM kaynağı oluşturmak için aşağıdaki girişleri sağlamanız gerekir:

- HSM 'nin adı.
- Aboneliğinize yerleştirilecek kaynak grubu.
- Azure konumu.
- İlk yöneticilerin listesi.

Aşağıdaki örnekte, **geçerli oturum açmış kullanıcıyla** tek yönetici olarak **Doğu ABD 2** konumunda bulunan **Contosoresourcegroup** kaynak grubunda **contosomhsm** adlı bir HSM oluşturulur.

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "East US 2" --administrators $oid
```

> [!NOTE]
> Create komutu birkaç dakika sürebilir. Başarılı bir şekilde döndüğünde, HSM 'nizi etkinleştirmeye hazırsanız.

Bu komutun çıktısı, oluşturduğunuz yönetilen HSM 'nin özelliklerini gösterir. En önemli iki özellik şunlardır:

* **ad**: örnekte, ad ContosoMHSM ' dir. Bu adı diğer Key Vault komutları için kullanacaksınız.
* **Hsmuri**: örnekte, URI ' https://contosohsm.managedhsm.azure.net . ' HSM 'nizi REST API aracılığıyla kullanan uygulamalar bu URI 'yi kullanmalıdır.

Azure hesabınız artık bu yönetilen HSM üzerinde herhangi bir işlem gerçekleştirmeye yetkilendirildi. Henüz hiç olmadığı için, başka hiç kimse yetkili değil.

## <a name="activate-the-security-domain-recovery-mode"></a>Güvenlik etki alanı kurtarma modunu etkinleştirme

Normal oluşturma sürecinde, bu noktada yeni bir güvenlik etki alanı başlatıp indiririz. Bununla birlikte, bir olağanüstü durum kurtarma yordamı yürüttüğünden, güvenlik etki alanı kurtarma moduna girmek ve bir güvenlik etki alanı değişim anahtarı indirmek için HSM 'yi isteyoruz. Güvenlik etki alanı değişim anahtarı, HSM 'ye yüklemeden önce güvenlik etki alanını şifrelemek için kullanılacak bir RSA ortak anahtarıdır. Karşılık gelen özel anahtar, aktarım sırasında güvenlik etki alanı içeriklerinizin güvende tutulması için HSM içinde korunur.

```azurecli-interactive
az keyvault security-domain init-recovery --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM2-SDE.cer
```

## <a name="upload-security-domain-to-destination-hsm"></a>Güvenlik etki alanını hedef HSM 'ye yükle

Bu adım için şunlar gerekir:
- Önceki adımda indirdiğiniz güvenlik etki alanı değişim anahtarı.
- Kaynak HSM 'nin güvenlik etki alanı.
- Güvenlik etki alanını şifrelemek için kullanılan özel anahtarların en az çekirdek sayısı.

`az keyvault security-domain upload`Komut aşağıdaki işlemleri gerçekleştirir:

- Kaynak HSM 'nin güvenlik etki alanının şifresini sağladığınız özel anahtarlarla çözün. 
- Önceki adımda indirdiğimiz güvenlik etki alanı değişim anahtarıyla şifrelenmiş bir güvenlik etki alanı karşıya yükleme blobu oluşturun ve ardından
- Güvenlik etki alanı kurtarmayı gerçekleştirmek için güvenlik etki alanı yükleme blobunu HSM 'ye yükleyin

Aşağıdaki örnekte, **Contosomhsm**'Deki güvenlik etki alanını, karşılık gelen özel anahtarların 2 ' yi kullanıyoruz ve bir güvenlik etki alanı almayı bekleyen **ContosoMHSM2**'e yüklersiniz. 

```azurecli-interactive
az keyvault security-domain upload --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM-SDE.cer --sd-file ContosoMHSM-SD.json --sd-wrapping-keys cert_0.key cert_1.key
```

Artık hem kaynak HSM (ContosoMHSM) hem de hedef HSM (ContosoMHSM2) aynı güvenlik etki alanına sahip. Artık kaynak HSM 'den hedef HSM 'ye tam yedeklemeyi geri yükleyebilirsiniz.

## <a name="create-a-backup-as-a-restore-point-of-your-new-hsm"></a>Yeni HSM 'nizin yedeğini (geri yükleme noktası olarak) oluşturun

Tam bir HSM geri yükleme yürütmeden önce tam bir yedekleme yapmanız her zaman iyi bir fikirdir. böylece geri yükleme sırasında bir sorun oluşması durumunda geri yükleme noktanız olur.

Bir HSM yedeklemesi oluşturmak için aşağıdakilere ihtiyacınız olacaktır
- Yedeklemenin depolanacağı depolama hesabı
- Bu depolama hesabındaki, yedekleme işleminin şifrelenmiş yedeklemeyi depolamak için yeni bir klasör oluşturacağınız bir BLOB depolama kapsayıcısı

`az keyvault backup`Aşağıdaki örnekte yer alan **contosobackup** depolama alanındaki **MHSMBACKUPCONTAINER** depolama kapsayıcısında HSM yedeklemesi için komut kullanıyoruz. 30 dakika içinde süresi dolan bir SAS belirteci oluşturuyoruz ve yedeklemeyi yazmak için bu yönetilen HSM 'ye izin verir.

```azurecli-interactive
end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
az storage container create --account-name  mhsmdemobackup --name mhsmbackupcontainer  --account-key $skey
sas=$(az storage container generate-sas -n mhsmbackupcontainer --account-name ContosoBackup --permissions crdw --expiry $end --account-key $skey -o tsv)
az keyvault backup start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas

```

## <a name="restore-backup-from-source-hsm"></a>Kaynak HSM 'den yedeklemeyi geri yükleme

Bu adım için şunlar gerekir:

- Depolama hesabı ve kaynak HSM 'nin yedeklerinin depolandığı blob kapsayıcısı.
- Yedeği geri yüklemek istediğiniz klasör adı. Düzenli yedeklemeler oluşturursanız, bu kapsayıcının içinde çok sayıda klasör olacaktır.


```azurecli-interactive
end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name ContosoBackup --permissions rl --expiry $end --account-key $skey -o tsv)
az keyvault restore start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-ContosoMHSM-2020083120161860
```

Artık tam bir olağanüstü durum kurtarma işlemini tamamladınız. Yedekleme çekilirken kaynak HSM 'nin içeriği, tüm anahtarlar, sürümler, öznitelikler, Etiketler ve rol atamaları dahil olmak üzere hedef HSM 'ye kopyalanır.

## <a name="next-steps"></a>Sonraki adımlar

- Güvenlik etki alanı hakkında daha fazla bilgi için bkz. [YÖNETILEN HSM güvenlik alanı hakkında](security-domain.md)
- [YÖNETILEN HSM en iyi yöntemlerini](best-practices.md) izleyin
