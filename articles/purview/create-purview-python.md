---
title: 'Hızlı başlangıç: Python kullanarak bir purview hesabı oluşturma'
description: Python kullanarak bir Azure purview hesabı oluşturun.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: python
ms.topic: quickstart
ms.date: 04/02/2021
ms.openlocfilehash: f8ac611d25507913d6d5f2e2dd289ea52ce4ae9f
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387574"
---
# <a name="quickstart-create-a-purview-account-using-python"></a>Hızlı başlangıç: Python kullanarak bir purview hesabı oluşturma

Bu hızlı başlangıçta Python kullanarak bir purview hesabı oluşturursunuz. 

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Kendi [Azure Active Directory kiracınız](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Hesabınız abonelikte kaynak oluşturma iznine sahip olmalıdır

* Tüm uygulamaların **depolama hesabı** ve **EventHub ad alanı** oluşturmasını engelleyen **Azure Ilkeniz** varsa, bir purview hesabı oluşturma işlemi sırasında girilebilecek şekilde etiketi kullanarak ilke özel durumu yapmanız gerekir. Ana neden her bir purview hesabının oluşturulması, bir yönetilen kaynak grubu ve bu kaynak grubu, bir depolama hesabı ve bir EventHub ad alanı içinde oluşturulması gerekir. Daha fazla bilgi için [bkz. Katalog portalı oluşturma](create-catalog-portal.md)


## <a name="install-the-python-package"></a>Python paketi yükleme

1. Yönetici ayrıcalıklarıyla bir terminal veya komut istemi açın. 
2. İlk olarak, Azure yönetim kaynakları için Python paketini yükleyin:

    ```python
    pip install azure-mgmt-resource
    ```
3. Purview için Python paketini yüklemek için aşağıdaki komutu çalıştırın:

    ```python
    pip install azure-mgmt-purview
    ```

    [Purview Için Python SDK 'Sı](https://github.com/Azure/azure-sdk-for-python) Python 2,7, 3,3, 3,4, 3,5, 3,6 ve 3,7 'yi destekler.

4. Azure kimlik doğrulaması için Python paketini yüklemek için aşağıdaki komutu çalıştırın:

    ```python
    pip install azure-identity
    ```
    > [!NOTE] 
    > "Azure-Identity" paketi bazı yaygın bağımlılıklarda "Azure-CLI" ile çakışıyor olabilir. Herhangi bir kimlik doğrulama sorununu karşılayabilir, "Azure-CLI" ve bağımlılıklarını kaldırın veya "Azure-CLI" paketini yüklemeden temiz bir makine kullanarak çalışır hale getirin.
    
## <a name="create-a-purview-client"></a>Bir takip görünümü istemcisi oluşturma

1. **Purview.py** adlı bir dosya oluşturun. Ad alanlarına başvurular eklemek için aşağıdaki deyimleri ekleyin.

    ```python
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    ```

2. Aşağıdaki kodu, PurviewManagementClient sınıfının bir örneğini oluşturan **Main** yöntemine ekleyin. Bu nesneyi, bir takip etme hesabı oluşturmak, takip etme hesabını silmek, ad kullanılabilirliğini ve diğer kaynak sağlayıcısı işlemlerini görüntülemek için kullanırsınız.
 
 ```python
    def main():
    
    # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Location name, where Purview account must be created.
    location = '<location name>'    

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    ```

## <a name="create-a-purview-account"></a>Bir sağlamayla hesabı oluşturun

Aşağıdaki kodu bir **sağlamayla hesabı** oluşturan **Main** yöntemine ekleyin. Kaynak grubunuz zaten varsa, birinci `create_or_update` deyimine açıklama ekleyin.

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location =location )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", pa.name, " Id: " , pa.id ," tags: " , pa.tags)  
    except:
        print("Error")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)      
        
```

Şimdi, program çalıştırıldığında **main** yöntemini çağırmak için aşağıdaki deyimi ekleyin:

```python
# Start the main method
main()
```

## <a name="full-script"></a>Tam betik

Tam Python kodu aşağıdaki gibidir:

```python
    
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    
     # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location ="southcentralus" )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", purview_name, " Id: " , pa.id ," tags: " , pa.tags) 
    except:
        print("Error in submitting job to create account")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)    

# Start the main method
main()
```

## <a name="run-the-code"></a>Kodu çalıştırma

Uygulamayı derleyip başlatın, ardından işlem hattı yürütmesini doğrulayın.

Konsol; veri fabrikası, bağlı hizmet, veri kümeleri, işlem hattı ve işlem hattı çalıştırmasının ilerleme durumunu yazdırır. Okunan/yazılan veri boyutunu içeren kopyalama etkinliği ayrıntılarını görene kadar bekleyin. Ardından, [Azure Depolama gezgini](https://azure.microsoft.com/features/storage-explorer/) gibi araçlar kullanarak, blobların değişkenlerde belirttiğiniz şekilde "inputBlobPath" yolundan "outputBlobPath" yoluna kopyalandığını doğrulayın.

Örnek çıktı aşağıdaki gibidir:

```console
location: southcentralus  Purview Account Name:  purviewpython7  Id:  /subscriptions/8c2c7b23-848d-40fe-b817-690d79ad9dfd/resourceGroups/Demo_Catalog/providers/Microsoft.Purview/accounts/purviewpython7  tags:  None
Creating
Creating
Succeeded
```

## <a name="verify-the-output"></a>Çıktıyı doğrulama

Azure portal takip edilecek **hesaplar** sayfasına gidin ve yukarıdaki kodu kullanarak oluşturulan hesabı doğrulayın. 

## <a name="delete-purview-account"></a>Purview hesabını Sil

Sağlamayla hesabını silmek için aşağıdaki kodu programa ekleyin:

```python
pa = purview_client.accounts.begin_delete(rg_name, purview_name).result()
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticideki kod bir takip hesabı oluşturur ve bir sağlamayla hesabını siler. Artık Python SDK 'sını indirebilir ve bir purview hesabı için gerçekleştirebileceğiniz diğer kaynak sağlayıcısı eylemleri hakkında bilgi edinebilirsiniz.

Kullanıcıların Azure purview hesabınıza erişmesine nasıl izin vereceğinizi öğrenmek için sonraki makaleye ilerleyin. 

> [!div class="nextstepaction"]
> [Azure purview hesabınıza kullanıcı ekleme](catalog-permissions.md)
