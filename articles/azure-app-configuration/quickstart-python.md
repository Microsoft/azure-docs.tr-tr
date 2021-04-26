---
title: Python uygulamalarıyla Azure Uygulama yapılandırması 'nı kullanmaya yönelik hızlı başlangıç | Microsoft Docs
description: Bu hızlı başlangıçta, kodınızdan ayrı uygulama ayarlarının depolanmasını ve yönetimini merkezileştirmek için Azure Uygulama yapılandırmasıyla bir Python uygulaması oluşturun.
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: devx-track-python
ms.date: 9/17/2020
ms.author: drewbat
ms.openlocfilehash: 1be74ceeb1f4808a8df8f56b71b66aa870cdd91d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800882"
---
# <a name="quickstart-create-a-python-app-with-azure-app-configuration"></a>Hızlı başlangıç: Azure Uygulama yapılandırmasıyla bir Python uygulaması oluşturma

Bu hızlı başlangıçta, [Python Için Azure uygulama yapılandırma istemci kitaplığı](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration)'nı kullanarak uygulama ayarlarının depolanmasını ve yönetimini merkezileştirmek üzere Azure Uygulama yapılandırması 'nı kullanacaksınız.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
- Python 2,7, veya 3,6 veya üzeri-Windows üzerinde Python ayarlama hakkında daha fazla bilgi Için bkz. [Windows belgelerindeki Python]( https://docs.microsoft.com/windows/python/)

## <a name="create-an-app-configuration-store"></a>Uygulama yapılandırma deposu oluşturma

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7.   >    >  Aşağıdaki anahtar-değer çiftlerini eklemek için yapılandırma Gezgini **anahtar değeri** oluştur ' u seçin:

    | Anahtar | Değer |
    |---|---|
    | TestApp: ayarlar: Ileti | Azure Uygulama yapılandırmasından veriler |

    **Etiket** ve **içerik türü** şimdilik boş bırakın.

8. **Uygula**’yı seçin.

## <a name="setting-up-the-python-app"></a>Python uygulamasını ayarlama

1. Bu öğreticide *uygulama-yapılandırma-hızlı başlangıç* adlı proje için yeni bir dizin oluşturacaksınız.

    ```console
    mkdir app-configuration-quickstart
    ```

1. Yeni oluşturulan *App-Configuration-QuickStart* dizinine geçin.

    ```console
    cd app-configuration-quickstart
    ```

1. Komutunu kullanarak Azure uygulama yapılandırma istemci kitaplığını yükler `pip install` .

    ```console
    pip install azure-appconfiguration
    ```

1. *App-Configuration-QuickStart* dizininde *App-Configuration-QuickStart.py* adlı yeni bir dosya oluşturun ve aşağıdaki kodu ekleyin:

    ```python
    import os
    from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting
    
    try:
        print("Azure App Configuration - Python Quickstart")
        # Quickstart code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

> [!NOTE]
> Bu hızlı başlangıçtaki kod parçacıkları, Python için uygulama yapılandırma istemci kitaplığı ile çalışmaya başlamanıza yardımcı olur. Uygulamanız için özel durumları gereksinimlerinize göre işlemeyi da göz önünde bulundurmanız gerekir. Özel durum işleme hakkında daha fazla bilgi edinmek için lütfen [Python SDK belgelerinize](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration)bakın.

## <a name="configure-your-app-configuration-connection-string"></a>Uygulama yapılandırma Bağlantı dizenizi yapılandırma

1. **AZURE_APP_CONFIG_CONNECTION_STRING** adlı bir ortam değişkeni ayarlayın ve bunu uygulama yapılandırma deponuzu için erişim anahtarı olarak ayarlayın. Komut satırında aşağıdaki komutu çalıştırın:

    ```cmd
    setx AZURE_APP_CONFIG_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell kullanıyorsanız şu komutu çalıştırın:

    ```azurepowershell
    $Env:AZURE_APP_CONFIG_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    MacOS veya Linux kullanıyorsanız şu komutu çalıştırın:

    ```console
    export AZURE_APP_CONFIG_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

2. Değişikliğin etkili olması için komut istemi ' ni yeniden başlatın. Düzgün ayarlandığını doğrulamak için ortam değişkeninin değerini yazdırın.

## <a name="code-samples"></a>Kod örnekleri

Bu bölümdeki örnek kod parçacıkları, Python için uygulama yapılandırması istemci kitaplığı ile genel işlemleri nasıl gerçekleştirekullanacağınızı göstermektedir. Bu kod parçacıklarını `try` daha önce oluşturduğunuz *App-Configuration-QuickStart.py* dosyasındaki bloğa ekleyin.

> [!NOTE]
> Uygulama Yapılandırması istemci kitaplığı, anahtar-değer nesnesini olarak ifade eder `ConfigurationSetting` . Bu nedenle, bu makalede, uygulama yapılandırma deposundaki **Anahtar değerlere** **yapılandırma ayarları** denir.

* [Uygulama yapılandırma deposuna bağlanma](#connect-to-an-app-configuration-store)
* [Yapılandırma ayarı al](#get-a-configuration-setting)
* [Yapılandırma ayarı Ekle](#add-a-configuration-setting)
* [Yapılandırma ayarlarının bir listesini alın](#get-a-list-of-configuration-settings)
* [Yapılandırma ayarını kilitle](#lock-a-configuration-setting)
* [Yapılandırma ayarının kilidini aç](#unlock-a-configuration-setting)
* [Yapılandırma ayarını güncelleştirme](#update-a-configuration-setting)
* [Yapılandırma ayarını Sil](#delete-a-configuration-setting)

### <a name="connect-to-an-app-configuration-store"></a>Uygulama yapılandırma deposuna bağlanma

Aşağıdaki kod parçacığı, ortam değişkeninizde depolanan bağlantı dizesini kullanarak bir **AzureAppConfigurationClient** örneği oluşturur.

```python
    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)
```

### <a name="get-a-configuration-setting"></a>Yapılandırma ayarı al

Aşağıdaki kod parçacığı, bir yapılandırma ayarını ada göre alır `key` .

```python
    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)
```

### <a name="add-a-configuration-setting"></a>Yapılandırma ayarı Ekle

Aşağıdaki kod parçacığı, `ConfigurationSetting` ve alanları içeren bir nesne oluşturur `key` `value` ve yöntemini çağırır `add_configuration_setting` . Bu yöntem, deponuzda zaten var olan bir yapılandırma ayarı eklemeye çalışırsanız bir özel durum oluşturur. Bu özel durumdan kaçınmak istiyorsanız bunun yerine [set_configuration_setting](#update-a-configuration-setting) yöntemi kullanılabilir.

```python
    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)
```

### <a name="get-a-list-of-configuration-settings"></a>Yapılandırma ayarlarının bir listesini alın

Aşağıdaki kod parçacığı, yapılandırma ayarlarının bir listesini alır. `key_filter`Ve `label_filter` bağımsız değişkenleri, anahtar değerlerini ve sırasıyla filtrelemek için belirtilebilir `key` `label` . Filtreleme hakkında daha fazla bilgi için bkz. [yapılandırma ayarlarını sorgulama](./concept-key-value.md#query-key-values).

```python
    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)
```

### <a name="lock-a-configuration-setting"></a>Yapılandırma ayarını kilitle

Uygulama yapılandırmasındaki bir anahtar-değer kilit durumu `read_only` nesnenin özniteliği tarafından gösterilir `ConfigurationSetting` . `read_only`İse `True` , ayar kilitlenir. `set_read_only`Yöntemi, `read_only=True` yapılandırma ayarını kilitlemek için bağımsız değişkenle çağrılabilir.

```python
    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))
```

### <a name="unlock-a-configuration-setting"></a>Yapılandırma ayarının kilidini aç

`read_only`Öğesinin özniteliği bir `ConfigurationSetting` ise `False` , ayarın kilidi açılır. `set_read_only`Yöntemi, `read_only=False` yapılandırma ayarının kilidini açmak için bağımsız değişkenle çağrılabilir.

```python
    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))
```

### <a name="update-a-configuration-setting"></a>Yapılandırma ayarını güncelleştirme

`set_configuration_setting`Yöntemi, var olan bir ayarı güncelleştirmek veya yeni bir ayar oluşturmak için kullanılabilir. Aşağıdaki kod parçacığı, varolan bir yapılandırma ayarının değerini değiştirir.

```python
    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)
```

### <a name="delete-a-configuration-setting"></a>Yapılandırma ayarını Sil

Aşağıdaki kod parçacığı bir yapılandırma ayarını ada göre siler `key` .

```python
    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)
```

## <a name="run-the-app"></a>Uygulamayı çalıştırma

Bu hızlı başlangıçta, Azure portal aracılığıyla oluşturulan bir yapılandırma ayarı almak, yeni bir ayar eklemek, mevcut ayarların bir listesini almak, bir ayarı kilitlemek ve açmak, bir ayarı güncelleştirmek ve son olarak bir ayarı silmek için Azure uygulama yapılandırma istemci kitaplığını kullanan bir Python uygulaması oluşturdunuz.

Bu noktada, *App-Configuration-QuickStart.py* dosyanız aşağıdaki koda sahip olmalıdır:

```python
import os
from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting

try:
    print("Azure App Configuration - Python Quickstart")
    # Quickstart code goes here

    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)

    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)

    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)

    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)

    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))

    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))

    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)

    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)

except Exception as ex:
    print('Exception:')
    print(ex)
```

Konsol pencerenizde, *App-Configuration-QuickStart.py* dosyasını içeren dizine gidin ve uygulamayı çalıştırmak için aşağıdaki Python komutunu yürütün:

```console
python app-configuration-quickstart.py
```

Aşağıdaki çıkışı görmeniz gerekir:

```output
Azure App Configuration - Python Quickstart

Retrieved configuration setting:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration

Added configuration setting:
Key: TestApp:Settings:NewSetting, Value: New setting value

Retrieved list of configuration settings:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration
Key: TestApp:Settings:NewSetting, Value: New setting value

Read-only status for TestApp:Settings:NewSetting: True

Read-only status for TestApp:Settings:NewSetting: False

Updated configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!

Deleted configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!
```

## <a name="clean-up-resources"></a>Kaynakları temizleme


[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta yeni bir uygulama yapılandırma deposu oluşturdunuz ve bir Python uygulamasından anahtar değerlerine nasıl erişekullanacağınızı öğrenirsiniz.

Ek kod örnekleri için şu adresi ziyaret edin:

> [!div class="nextstepaction"]
> [Azure Uygulama Yapılandırması istemci kitaplığı örnekleri](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration/samples)
