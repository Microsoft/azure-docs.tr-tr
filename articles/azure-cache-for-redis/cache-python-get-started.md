---
title: "Hızlı başlangıç: Python 'da Redsıs için Azure önbelleğini kullanma"
description: Bu hızlı başlangıçta, Redu için Azure önbelleği kullanan bir Python uygulaması oluşturmayı öğreneceksiniz.
author: yegu-ms
ms.author: yegu
ms.date: 11/05/2019
ms.topic: quickstart
ms.service: cache
ms.devlang: python
ms.custom:
- mvc
- seo-python-october2019
- devx-track-python
- mode-api
ms.openlocfilehash: 40990dfb651817cf52cd5b5a039566e3209d6ac7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532046"
---
# <a name="quickstart-use-azure-cache-for-redis-in-python"></a>Hızlı başlangıç: Python 'da Redsıs için Azure önbelleğini kullanma

Bu makalede, Azure 'daki herhangi bir uygulamadan erişilebilen güvenli, ayrılmış bir önbelleğe erişim sağlamak için Redsıs için Azure önbelleğini bir Python uygulamasına katabilirsiniz.

## <a name="skip-to-the-code-on-github"></a>GitHub 'daki koda atlayın

Koda doğrudan atlamak istiyorsanız GitHub 'da [Python hızlı başlangıç](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/python) bölümüne bakın.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
- [Python 2 veya 3](https://www.python.org/downloads/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Redsıs örneği için Azure önbelleği oluşturma
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>redis-py yükleyin

[Redin-](https://github.com/andymccurdy/redis-py) , Redsıs Için Azure önbelleğine yönelik bir Python arabirimidir. Komut isteminden *Redu-Kopyala* paketini yüklemek için, *PIP* Python paketleri aracını kullanın. 

Aşağıdaki örnek, bir yönetici komut isteminden Windows 10 ' da *Reda-Kopyala* yüklemek için Python 3 için *pip3* kullandı.

![Redsıs için Azure önbelleğine Reda-Kopyala Python arabirimini yükler](./media/cache-python-get-started/cache-python-install-redis-py.png)

## <a name="read-and-write-to-the-cache"></a>Önbellek üzerinde okuma ve yazma

Komut satırından Python komutunu çalıştırın ve aşağıdaki kodu kullanarak önbelleğinizi test edin. `<Your Host Name>`Ve `<Your Access Key>` değerlerini, redsıs örneği Için Azure önbelleğinizin değerleriyle değiştirin. Ana bilgisayar adınız *\<DNS name> . Redis.cache.Windows.net* biçimindedir.

```python
>>> import redis
>>> r = redis.StrictRedis(host='<Your Host Name>',
        port=6380, db=0, password='<Your Access Key>', ssl=True)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
b'bar'
```

> [!IMPORTANT]
> Redsıs sürüm 3,0 veya üzeri için Azure önbelleği için TLS/SSL sertifika denetimi zorlanır. Redsıs için Azure Cache 'e bağlanılırken ssl_ca_certs açıkça ayarlanması gerekir. RedHat Linux için ssl_ca_certs, */etc/pki/TLS/certs/CA-Paketleyici ka.exe CRT* sertifika modülüdür.

## <a name="create-a-python-sample-app"></a>Python örnek uygulaması oluşturma

Yeni bir metin dosyası oluşturun, aşağıdaki betiği ekleyin ve dosyayı *PythonApplication1.py* olarak kaydedin. `<Your Host Name>`Ve `<Your Access Key>` değerlerini, redsıs örneği Için Azure önbelleğinizin değerleriyle değiştirin. Ana bilgisayar adınız *\<DNS name> . Redis.cache.Windows.net* biçimindedir.

```python
import redis

myHostname = "<Your Host Name>"
myPassword = "<Your Access Key>"

r = redis.StrictRedis(host=myHostname, port=6380,
                      password=myPassword, ssl=True)

result = r.ping()
print("Ping returned : " + str(result))

result = r.set("Message", "Hello!, The cache is working with Python!")
print("SET Message returned : " + str(result))

result = r.get("Message")
print("GET Message returned : " + result.decode("utf-8"))

result = r.client_list()
print("CLIENT LIST returned : ")
for c in result:
    print("id : " + c['id'] + ", addr : " + c['addr'])
```

Python ile *PythonApplication1.py* çalıştırın. Aşağıdaki örnekte olduğu gibi sonuçlar görmeniz gerekir:

![Önbellek erişimini sınamak için Python betiği çalıştırma](./media/cache-python-get-started/cache-python-completed.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıçta oluşturduğunuz Azure Kaynak grubu ve kaynakları ile işiniz bittiğinde, ücretleri önlemek için bunları silebilirsiniz.

> [!IMPORTANT]
> Bir kaynak grubunun silinmesi geri alınamaz ve kaynak grubu ve içindeki tüm kaynaklar kalıcı olarak silinir. Korumak istediğiniz var olan bir kaynak grubunda Redsıs örneği için Azure önbelleğinizi oluşturduysanız, yalnızca önbelleğin **genel bakış** sayfasında **Sil** ' i seçerek önbelleği silebilirsiniz. 

Kaynak grubunu ve Azure örneği için Redis Cache silmek için:

1. [Azure Portal](https://portal.azure.com), **kaynak gruplarını** arayıp seçin.
1. **Ada göre filtrele** metin kutusunda, önbellek örneğinizi içeren kaynak grubunun adını girin ve arama sonuçlarından bunu seçin. 
1. Kaynak grubu sayfanızda **kaynak grubunu sil**' i seçin.
1. Kaynak grubu adını yazın ve ardından **Sil**' i seçin.
   
   ![Redsıs için Azure önbelleği için kaynak grubunuzu silme](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Redsıs için Azure önbelleği kullanan basit bir ASP.NET Web uygulaması oluşturun.](./cache-web-app-howto.md)
