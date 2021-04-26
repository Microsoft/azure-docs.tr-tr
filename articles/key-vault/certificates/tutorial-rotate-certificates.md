---
title: Öğretici-Key Vault 'de sertifika otomatik döndürme sıklığı güncelleştiriliyor | Microsoft Docs
description: Azure Key Vault Azure portal kullanarak bir sertifikanın otomatik döndürme sıklığını güncelleştirmeyi gösteren öğretici
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: ec88dfc7ded7ecb1d640eb4d73ef4c8045e549cf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791994"
---
# <a name="tutorial-configure-certificate-auto-rotation-in-key-vault"></a>Öğretici: Key Vault içinde sertifika otomatik döndürmeyi yapılandırma

Azure Key Vault kullanarak dijital sertifikalar kolayca temin edebilir, yönetebilir ve dağıtabilirsiniz. Sertifikalar, bir sertifika yetkilisi (CA) tarafından imzalanmış ortak ve özel Güvenli Yuva Katmanı (SSL)/Aktarım Katmanı Güvenliği (TLS) sertifikaları veya otomatik olarak imzalanan bir sertifika olabilir. Key Vault, sertifika yaşam döngüsü yönetimine yönelik sağlam bir çözüm sunarak, CA 'larla ortaklık aracılığıyla sertifika talep edebilir ve yenileyebilir. Bu öğreticide, bir sertifikanın geçerlilik süresini, otomatik döndürme sıklığını ve CA özniteliklerini güncelleşitecaksınız.

Öğretici şunların nasıl yapıldığını göstermektedir:

> [!div class="checklist"]
> * Azure portal kullanarak bir sertifikayı yönetin.
> * CA sağlayıcısı hesabı ekleyin.
> * Sertifikanın geçerlilik süresini güncelleştirin.
> * Sertifikanın otomatik döndürme sıklığını güncelleştirin.
> * Azure PowerShell kullanarak sertifikanın özniteliklerini güncelleştirin.

Başlamadan önce [temel kavramları Key Vault](../general/basic-concepts.md)okuyun.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

https://portal.azure.com adresinden Azure portalında oturum açın.

## <a name="create-a-vault"></a>Kasa oluşturma

[Azure Portal](../general/quick-create-portal.md), [Azure CLI](../general/quick-create-cli.md)veya [Azure PowerShell](../general/quick-create-powershell.md)kullanarak Azure Key Vault oluşturun. Örnekte, Anahtar Kasası adı **örnek kasadır**.

![Anahtar Kasası oluşturma işlemi tamamlandıktan sonra çıkış](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Key Vault bir sertifika oluşturun

Bir sertifika oluşturun veya bir sertifikayı anahtar kasasına aktarın (bkz. [Key Vault sertifika oluşturma adımları](../secrets/quick-create-portal.md)). Bu durumda, **examplecertificate** adlı bir sertifika üzerinde çalışacaksınız.

## <a name="update-certificate-lifecycle-attributes"></a>Sertifika yaşam döngüsü özniteliklerini güncelleştirme

Azure Key Vault, sertifikaların yaşam döngüsü özniteliklerini her ikisi de sertifika oluşturma sırasında veya sonrasında güncelleştirebilirsiniz.

Key Vault oluşturulan bir sertifika şu olabilir:

- Otomatik olarak imzalanan bir sertifika.
- Key Vault ile iş ortağı olan bir CA ile oluşturulan bir sertifika.
- Key Vault ile iş ortağı olmayan bir CA 'ya sahip bir sertifika.

Aşağıdaki CA 'Lar Şu anda Key Vault ile iş ortağı sağlayıcılardır:

- DigiCert: Key Vault OV TLS/SSL sertifikaları sunmaktadır.
- GlobalSign: Key Vault OV TLS/SSL sertifikaları sunmaktadır.

Key Vault CAs ile belirlenen ortaklıklar aracılığıyla sertifikaları otomatik olarak döndürür. Key Vault, sertifikaları iş ortaklığı üzerinden otomatik olarak istediğinde ve yenilediği için otomatik döndürme özelliği, Key Vault iş ortağı olmayan CA 'larla oluşturulan sertifikalar için geçerli değildir.

> [!NOTE]
> CA sağlayıcısı için bir hesap yöneticisi, Key Vault, TLS/SSL sertifikaları oluşturmak, yenilemek ve kullanmak için kullandığı kimlik bilgilerini oluşturur.
![Sertifika yetkilisi](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
>

### <a name="update-certificate-lifecycle-attributes-at-the-time-of-creation"></a>Sertifika yaşam döngüsü özniteliklerini oluşturma sırasında Güncelleştir

1. Key Vault Özellikler sayfalarında, **Sertifikalar**' ı seçin.
1. **Oluştur/Içeri aktar**' ı seçin.
1. **Sertifika oluştur** ekranında, aşağıdaki değerleri güncelleştirin:

   - **Geçerlilik süresi**: değeri girin (ay). Kısa süreli sertifikaların oluşturulması önerilen bir güvenlik uygulamasıdır. Varsayılan olarak, yeni oluşturulan sertifikanın geçerlilik değeri 12 aydan oluşur.
   - **Ömür eylemi türü**: sertifikanın otomatik yenileme ve uyarı eylemini seçin ve ardından **yüzde ömrü** veya **süre sonu öncesindeki gün sayısını** güncelleştirin. Varsayılan olarak, bir sertifikanın otomatik yenilenmesi, yaşam süresinin yüzde 80 ' sinden belirlenir. Aşağı açılan menüden, aşağıdaki seçeneklerden birini seçin.

      |  Belirli bir zamanda otomatik olarak Yenile| Belirli bir zamanda tüm kişileri e-postayla gönder |
      |-----------|------|
      |Bu seçeneğin belirlenmesi, oto dönüşü *etkinleştirebilir* . | Bu seçeneğin *belirlenmesi otomatik olarak döndürülmez, ancak* yalnızca ilgili kişileri uyarır.|
      
      [Buraya e-posta kişisi ayarlama](https://docs.microsoft.com/azure/key-vault/certificates/overview-renew-certificate#get-notified-about-certificate-expiration) hakkında bilgi edinebilirsiniz

1. **Oluştur**’u seçin.

![Sertifika yaşam döngüsü](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="update-lifecycle-attributes-of-a-stored-certificate"></a>Depolanan bir sertifikanın yaşam döngüsü özniteliklerini güncelleştirme

1. Anahtar kasasını seçin.
1. Key Vault Özellikler sayfalarında, **Sertifikalar**' ı seçin.
1. Güncelleştirmek istediğiniz sertifikayı seçin. Bu durumda, **examplecertificate** adlı bir sertifika üzerinde çalışacaksınız.
1. Üst menü çubuğundan **verme ilkesi** ' ni seçin.

   ![Verme Ilkesi düğmesini vurgulayan ekran görüntüsü.](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)

1. **Verme ilkesi** ekranında, aşağıdaki değerleri güncelleştirin:

   - **Geçerlilik süresi**: değeri güncelleştirin (ay).
   - **Ömür eylemi türü**: sertifikanın otomatik yenileme ve uyarı eylemini seçin ve ardından sürenin dolması için gereken **ömrü** veya **gün sayısını** güncelleştirin.

   ![Sertifika Özellikleri](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)

1. **Kaydet**’i seçin.

> [!IMPORTANT]
> Bir sertifikanın ömür eylemi türünü değiştirmek, mevcut sertifikalara yönelik değişiklikleri hemen kaydeder.


### <a name="update-certificate-attributes-by-using-powershell"></a>PowerShell kullanarak sertifika özniteliklerini güncelleştirme

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> Sertifika listesinin yenileme ilkesini değiştirmek için, `File.csv` Aşağıdaki örnekteki gibi içeren öğesini girin `VaultName,CertName` :
> <br/>
 `vault1,Cert1` <br/>
>  `vault2,Cert2`
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
Parametreler hakkında daha fazla bilgi edinmek için bkz. [az keykasa Certificate](/cli/azure/keyvault/certificate#az_keyvault_certificate_set_attributes).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Diğer Key Vault öğreticileri Bu öğreticiye göre derleyin. Bu öğreticilerle çalışmayı planlıyorsanız, mevcut kaynakları yerinde bırakmak isteyebilirsiniz.
Artık ihtiyacınız kalmadığında, anahtar kasasını ve ilgili kaynakları silen kaynak grubunu silin.

Portalı kullanarak kaynak grubunu silmek için:

1. Portalın üst kısmındaki **arama** kutusuna kaynak grubunuzun adını girin. Bu hızlı başlangıçta kullanılan kaynak grubu arama sonuçlarında göründüğünde, bunu seçin.
1. **Kaynak grubunu sil**'i seçin.
1. **Kaynak grubu adını yazın:** kutusuna kaynak grubunun adını yazın ve **Sil**' i seçin.


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir sertifikanın yaşam döngüsü özniteliklerini güncelleştirmiş olursunuz. Key Vault ve uygulamalarınızla tümleştirme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere geçin:

- [Azure Key Vault 'de sertifika oluşturmayı yönetme](./create-certificate-scenarios.md)hakkında daha fazla bilgi edinin.
- [Key Vault genel bakışı](../general/overview.md)gözden geçirin.
