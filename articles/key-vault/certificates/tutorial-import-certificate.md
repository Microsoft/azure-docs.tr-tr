---
title: Öğretici-Azure portal kullanarak Key Vault sertifikayı Içeri aktarma | Microsoft Docs
description: Azure Key Vault bir sertifikanın nasıl içeri aktarılacağını gösteren öğretici
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc, devx-track-azurecli
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: aebb0fa5da4cef655acc424c5ac90b628a4ba3af
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814451"
---
# <a name="tutorial-import-a-certificate-in-azure-key-vault"></a>Öğretici: Azure Key Vault sertifikayı Içeri aktarma

Azure Key Vault, gizli diziler için güvenli bir depolama sağlayan bulut hizmetidir. Anahtarları, parolaları, sertifikaları ve diğer gizli dizileri güvenli bir şekilde depolayabilirsiniz. Azure anahtar kasaları Azure portalı aracılığıyla oluşturulup yönetilebilir. Bu öğreticide bir Anahtar Kasası oluşturup bir sertifikayı içeri aktarmak için kullanacaksınız. Key Vault hakkında daha fazla bilgi için [Genel Bakış](../general/overview.md) bölümünü inceleyin.

Öğretici şunların nasıl yapıldığını göstermektedir:

> [!div class="checklist"]
> * Anahtar kasası oluşturma.
> * Portalı kullanarak Key Vault bir sertifikayı içeri aktarın.
> * CLı kullanarak Key Vault bir sertifikayı içeri aktarın.
> * PowerShell kullanarak Key Vault bir sertifikayı içeri aktarın.


Başlamadan önce [temel kavramları Key Vault](../general/basic-concepts.md)okuyun. 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-a-vault"></a>Kasa oluşturma

1. Azure portal menüsünde veya **giriş** sayfasından **kaynak oluştur**' u seçin.
2. Arama kutusuna **Key Vault** girin.
3. Sonuç listesinden **Key Vault**’u seçin.
4. Key Vault bölümünde **Oluştur**’u seçin.
5. **Anahtar kasası oluşturma** bölümünde aşağıdaki bilgileri sağlayın:
    - **Ad**: Benzersiz bir ad gereklidir. Bu hızlı başlangıçta **örnek kasasını** kullanırız. 
    - **Abonelik**: Bir abonelik seçin.
    - **Kaynak grubu** altında **Yeni oluştur** ' u seçin ve bir kaynak grubu adı girin.
    - **Konum** aşağı açılan menüsünde bir konum seçin.
    - Diğer seçenekleri varsayılan değerlerinde bırakın.
6. Yukarıdaki bilgileri girdikten sonra **Oluştur**’u seçin.

Aşağıda listelenen iki özelliği not edin:

* **Kasa adı**: örnekte bu **örnek kasadır**. Bu adı diğer adımlar için kullanacaksınız.
* **Kasa URI’si**: Örnekte bu: https://example-vault.vault.azure.net/. REST API'si aracılığıyla kasanızı kullanan uygulamaların bu URI'yi kullanması gerekir.

Bu noktada Azure hesabınız, bu yeni anahtar kasasında işlemler gerçekleştirmeye yetkili olan tek hesaptır.

![Key Vault oluşturma işlemi tamamlandıktan sonra alınan çıktı](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="import-a-certificate-to-key-vault"></a>Key Vault bir sertifikayı içeri aktar

Bir sertifikayı kasaya aktarmak için diskte olması gereken bir ped veya PFX Sertifika dosyası olması gerekir. Bu durumda, **örnek sertifika** adlı dosya adına sahip bir sertifikayı içeri aktaracağız.

> [!IMPORTANT]
> Azure Key Vault'ta PFX ve PEM biçimindeki sertifikalar desteklenir. 
> - . pek dosya biçimi bir veya daha fazla x509 sertifika dosyası içeriyor.
> - . pfx dosya biçimi, birden çok şifreleme nesnesini tek bir dosyada (etki alanınız için verilir), eşleşen bir özel anahtarla depolamak için bir arşiv dosyası biçimidir ve isteğe bağlı olarak bir ara CA 'yı içerebilir.  

1. Key Vault Özellikler sayfalarında, **Sertifikalar**' ı seçin.
2. **Oluştur/İçeri Aktar**’a tıklayın.
3. **Sertifika oluştur** ekranında aşağıdaki değerleri seçin:
    - **Sertifika oluşturma yöntemi**: içeri aktarma.
    - **Sertifika adı**: örnek sertifikası.
    - **Sertifika dosyasını karşıya yükle**: diskten sertifika dosyasını seçin
    - **Parola** : parola korumalı bir sertifika dosyası yüklüyorsanız, bu parolayı burada belirtin. Aksi takdirde, boş bırakın. Sertifika dosyası başarıyla alındıktan sonra Anahtar Kasası bu parolayı kaldırır.
4. **Oluştur**’a tıklayın.

![Sertifika Özellikleri](../media/certificates/tutorial-import-cert/cert-import.png)

**Içeri aktarma** yöntemi kullanılarak bir sertifika eklendiğinde, Azure Anahtar Kasası otomatik olarak sertifika parametrelerini (geçerlilik süresi, verenin adı, etkinleştirme tarihi vb.) dolduracaktır.

Sertifikanın başarıyla içeri aktarıldığını belirten iletiyi aldıktan sonra, özelliklerini görüntülemek için listede öğesine tıklayabilirsiniz. 

![Sertifika özelliklerinin nerede görüntüleneceği gösteren ekran görüntüsü.](../media/certificates/tutorial-import-cert/current-version-hidden.png)

## <a name="import-a-certificate-using-azure-cli"></a>Azure CLı kullanarak bir sertifikayı içeri aktarma

Bir sertifikayı belirtilen bir anahtar kasasına aktarın. Özel bir anahtar içeren mevcut geçerli bir sertifikayı Azure Key Vault içine aktarmak için, içeri aktarılacak dosya PFX ya da ped biçiminde olabilir. Sertifika pek biçimindeyse, ped dosyası, x509 sertifikalarını ve anahtar içermelidir. Bu işlem için sertifikalar/içeri aktarma izni gerekir.

```azurecli
az keyvault certificate import --file
                               --name
                               --vault-name
                               [--disabled {false, true}]
                               [--only-show-errors]
                               [--password]
                               [--policy]
                               [--subscription]
                               [--tags]
```

[Parametreler](/cli/azure/keyvault/certificate#az_keyvault_certificate_import)hakkında daha fazla bilgi edinin.

Sertifikayı içeri aktardıktan sonra sertifika [göster](/cli/azure/keyvault/certificate#az_keyvault_certificate_show) 'i kullanarak sertifikayı görüntüleyebilirsiniz


```azurecli
az keyvault certificate show [--id]
                             [--name]
                             [--only-show-errors]
                             [--subscription]
                             [--vault-name]
                             [--version]
```

Şimdi bir Anahtar Kasası oluşturdunuz, bir sertifika içeri aktardı ve sertifikanın özellikleri görüntülendi.

## <a name="import-a-certificate-using-azure-powershell"></a>Azure PowerShell kullanarak bir sertifikayı içeri aktarma

```
Import-AzureKeyVaultCertificate
      [-VaultName] <String>
      [-Name] <String>
      -FilePath <String>
      [-Password <SecureString>]
      [-Tag <Hashtable>]
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
```

[Parametreler](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?)hakkında daha fazla bilgi edinin.


## <a name="clean-up-resources"></a>Kaynakları temizleme

Diğer Key Vault hızlı başlangıçları ve öğreticileri bu hızlı başlangıcı temel alır. Sonraki hızlı başlangıç ve öğreticilerle çalışmaya devam etmeyi planlıyorsanız, bu kaynakları yerinde bırakmanız yararlı olabilir.
Artık gerek kalmadığında kaynak grubunu silin; bunu yaptığınızda Key Vault ve ilgili kaynaklar silinir. Kaynak grubunu portal aracılığıyla silmek için:

1. Portalın üst kısmındaki Arama kutusuna kaynak grubunuzun adını girin. Bu hızlı başlangıçta kullanılan kaynak grubunu arama sonuçlarında gördüğünüzde seçin.
2. **Kaynak grubunu sil**'i seçin.
3. **KAYNAK GRUBU ADINI YAZIN:** kutusuna kaynak grubunun adını yazın ve **Sil**’i seçin.


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide bir Key Vault oluşturdunuz ve bir sertifikayı içeri aktardınız. Key Vault ve uygulamalarınızla tümleştirme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere ilerleyin.

- [Azure Key Vault 'da sertifika oluşturmayı yönetme](./create-certificate-scenarios.md) hakkında daha fazla bilgi edinin
- [REST API 'Leri kullanarak sertifika alma](/rest/api/keyvault/importcertificate/importcertificate) örneklerine bakın
- [Key Vault güvenliğine genel bakış](../general/security-features.md) konusunu gözden geçirin