---
title: Rest 'te uygulama kaynağınızı şifreleyin
description: Azure Storage 'da uygulama verilerinizi şifreleyin ve paket dosyası olarak dağıtın.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 71668bf27628bb2af2dfc7112d28ba10ecfdf9f3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768822"
---
# <a name="encrypt-your-application-data-at-rest-using-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarları kullanarak uygulama verilerinizi Rest 'te şifreleyin

İşlev uygulamanızın uygulama verilerini bekleyen şekilde şifrelemek için bir Azure depolama hesabı ve bir Azure Key Vault gerekir. Bu hizmetler, uygulamanızı bir dağıtım paketinden çalıştırdığınızda kullanılır.

  - [Azure depolama, bekleyen şifreleme sağlar](../storage/common/storage-service-encryption.md). Sistem tarafından sağlanmış anahtarları veya kendi müşteri tarafından yönetilen anahtarlarını kullanabilirsiniz. Bu, uygulama verilerinizin Azure 'daki bir işlev uygulamasında çalışmadığı durumlarda depolandığı yerdir.
  - [Dağıtım paketinden çalıştırmak](run-functions-from-deployment-package.md) , App Service dağıtım özelliğidir. Bir Azure depolama hesabından, paylaşılan erişim Imzası (SAS) URL 'SI kullanarak site içeriğinizi dağıtmanıza olanak tanır.
  - [Key Vault başvurular](../app-service/app-service-key-vault-references.md) App Service bir güvenlik özelliğidir. Çalışma zamanında gizli dizileri uygulama ayarları olarak içeri aktarmanıza olanak tanır. Azure depolama hesabınızın SAS URL 'sini şifrelemek için bunu kullanın.

## <a name="set-up-encryption-at-rest"></a>Bekleyen şifrelemeyi ayarla

### <a name="create-an-azure-storage-account"></a>Azure Depolama hesabı oluşturma

İlk olarak, [bir Azure depolama hesabı oluşturun](../storage/common/storage-account-create.md) ve [müşterinin yönettiği anahtarlarla şifreleyin](../storage/common/customer-managed-keys-overview.md). Depolama hesabı oluşturulduktan sonra, paket dosyalarını karşıya yüklemek için [Azure Depolama Gezgini](../vs-azure-tools-storage-manage-with-storage-explorer.md) kullanın.

Sonra, [SAS oluşturmak](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer)için Depolama Gezgini kullanın. 

> [!NOTE]
> Bu SAS URL 'sini kaydedin, daha sonra çalışma zamanında dağıtım paketine güvenli erişim sağlamak için kullanılır.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>Depolama hesabınızdan bir paketten çalıştırmayı yapılandırma
  
Dosyanızı blob depolamaya yükledikten ve dosya için bir SAS URL 'sine sahip olduktan sonra, `WEBSITE_RUN_FROM_PACKAGE` uygulama AYARıNı SAS URL 'si olarak ayarlayın. Aşağıdaki örnek, Azure CLı kullanarak bunu yapar:

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

Bu uygulama ayarı eklendiğinde, işlev uygulamanızın yeniden başlatılmasına neden olur. Uygulama yeniden başlatıldıktan sonra, bu dosyaya gidin ve uygulamanın dağıtım paketini kullanarak doğru şekilde başlatıldığından emin olun. Uygulama doğru şekilde başlamazsa, [paketten Çalıştır sorun giderme kılavuzu](run-functions-from-deployment-package.md#troubleshooting)' na bakın.

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>Key Vault başvurularını kullanarak uygulama ayarını şifreleyin

Artık `WEBSITE_RUN_FROM_PACKAGE` uygulama ayarının DEĞERINI SAS kodlu URL 'ye bir Key Vault başvurusuyla değiştirebilirsiniz. Bu, ek bir güvenlik katmanı sağlayan Key Vault SAS URL 'sini şifreli tutar.

1. [`az keyvault create`](/cli/azure/keyvault#az_keyvault_create)Bir Key Vault örneği oluşturmak için aşağıdaki komutu kullanın.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. Uygulamanızı anahtar kasanıza [erişim izni vermek için aşağıdaki yönergeleri](../app-service/app-service-key-vault-references.md#granting-your-app-access-to-key-vault) izleyin:

1. [`az keyvault secret set`](/cli/azure/keyvault/secret#az_keyvault_secret_set)Dış URL 'nizi anahtar kasanıza gizli dizi olarak eklemek için aşağıdaki komutu kullanın:   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  Aşağıdaki komutu kullanarak, [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) `WEBSITE_RUN_FROM_PACKAGE` DEĞERI dış URL 'ye Key Vault başvuru olarak değere sahip bir uygulama ayarı oluşturun:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    , `<secret-version>` Önceki komutun çıktısında olacaktır `az keyvault secret set` .

Bu uygulama ayarının güncelleştirilmesi, işlev uygulamanızın yeniden başlatılmasına neden olur. Uygulama yeniden başlatıldıktan sonra, Key Vault başvurusunu kullanarak doğru şekilde başlatıldığından emin olun.

## <a name="how-to-rotate-the-access-token"></a>Erişim belirtecini döndürme

Depolama hesabınızın SAS anahtarını düzenli aralıklarla döndürmek en iyi uygulamadır. İşlev uygulamasının yanlışlıkla erişimi istemediğinden emin olmak için Key Vault SAS URL 'sini de güncelleştirmeniz gerekir.

1. Azure portal depolama hesabınıza giderek SAS anahtarını döndürün. **Ayarlar**  >  **erişim tuşları** altında, SAS anahtarını döndürmek için simgeye tıklayın.

1. Yeni SAS URL 'sini kopyalayın ve Anahtar Kasanızda güncelleştirilmiş SAS URL 'sini ayarlamak için aşağıdaki komutu kullanın:

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. Uygulama ayarınızla olan Anahtar Kasası başvurusunu yeni gizli sürüme güncelleştirin:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    , `<secret-version>` Önceki komutun çıktısında olacaktır `az keyvault secret set` .

## <a name="how-to-revoke-the-function-apps-data-access"></a>İşlev uygulamasının veri erişimini iptal etme

İşlev uygulamasının depolama hesabına erişimini iptal etmek için iki yöntem vardır. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Azure depolama hesabı için SAS anahtarını döndürün

Depolama hesabı için SAS anahtarı döndürülürse, işlev uygulamasının artık depolama hesabına erişimi olmaz, ancak paket dosyasının son indirilen sürümüyle çalışmaya devam edecektir. Son indirilen sürümü temizlemek için işlev uygulamasını yeniden başlatın.

### <a name="remove-the-function-apps-access-to-key-vault"></a>Uygulamanın Key Vault erişimini kaldırma

Uygulamanın Key Vault erişimini devre dışı bırakarak, işlev uygulamasının site verilerine erişimini iptal edebilirsiniz. Bunu yapmak için, işlev uygulaması kimliğinin erişim ilkesini kaldırın. Bu, Anahtar Kasası başvurularını yapılandırırken daha önce oluşturduğunuz kimlik ile aynıdır.

## <a name="summary"></a>Özet

Uygulama dosyalarınız artık depolama hesabınızda Rest olarak şifrelenir. İşlev uygulamanız başlatıldığında, anahtar kasaınızdan SAS URL 'sini alır. Son olarak, işlev uygulaması uygulama dosyalarını depolama hesabından yükler. 

Uygulamanın depolama hesabınıza erişimini iptal etmeniz gerekirse, anahtar kasasına erişimi iptal edebilir veya SAS URL 'sini geçersiz kılan depolama hesabı anahtarlarını döndürebilirsiniz.

## <a name="frequently-asked-questions"></a>Sık Sorulan Sorular

### <a name="is-there-any-additional-charge-for-running-my-function-app-from-the-deployment-package"></a>Dağıtım paketinden işlev uygulamamı çalıştırmak için herhangi bir ek ücret var mı?

Yalnızca Azure depolama hesabı ve geçerli çıkış ücretleri ile ilişkili maliyet.

### <a name="how-does-running-from-the-deployment-package-affect-my-function-app"></a>Dağıtım paketinden çalışan işlev uygulamamı nasıl etkiler?

- Uygulamanızı dağıtım paketinden çalıştırmak, `wwwroot/` salt okunurdur. Uygulamanız bu dizine yazmaya çalıştığında bir hata alıyor.
- TAR ve GZIP biçimleri desteklenmez.
- Bu özellik yerel önbellek ile uyumlu değil.

## <a name="next-steps"></a>Sonraki adımlar

- [App Service için Key Vault başvurular](../app-service/app-service-key-vault-references.md)
- [Bekleyen veri için Azure Depolama şifrelemesi](../storage/common/storage-service-encryption.md)