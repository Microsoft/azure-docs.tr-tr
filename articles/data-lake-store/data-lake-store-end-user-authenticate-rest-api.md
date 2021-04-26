---
title: Son Kullanıcı kimlik doğrulaması-Data Lake Storage 1. ile REST-Azure
description: REST API kullanarak Azure Active Directory kullanarak Azure Data Lake Storage 1. Son Kullanıcı kimlik doğrulaması elde etme hakkında bilgi edinin
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 462cd06c9da3b1f0a57c293d52c59181372b709b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103756"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>REST API kullanarak Azure Data Lake Storage 1. ile son kullanıcı kimlik doğrulaması
> [!div class="op_single_selector"]
> * [Java'yı kullanma](data-lake-store-end-user-authenticate-java-sdk.md)
> * [.NET SDK’sını kullanma](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Python’u kullanma](data-lake-store-end-user-authenticate-python.md)
> * [REST API’sini kullanma](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

Bu makalede, Azure Data Lake Storage 1. ile son kullanıcı kimlik doğrulaması yapmak için REST API nasıl kullanacağınızı öğreneceksiniz. REST API kullanarak Data Lake Storage 1. ile hizmetten hizmete kimlik doğrulaması için, bkz. [Data Lake Storage 1. ile hizmetten hizmete kimlik doğrulaması REST API kullanarak](data-lake-store-service-to-service-authenticate-rest-api.md).

## <a name="prerequisites"></a>Önkoşullar

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).

* **Bir Azure Active Directory "yerel" uygulaması oluşturun**. [Azure Active Directory kullanarak Data Lake Storage 1. Ile Son Kullanıcı kimlik doğrulaması](data-lake-store-end-user-authenticate-using-active-directory.md)adımlarını tamamlamış olmanız gerekir.

* **[kıvır](https://curl.haxx.se/)**. Bu makalede, bir Data Lake Storage 1. hesabına yönelik REST API çağrılarının nasıl yapılacağını göstermek için kıvrımlı kullanılır.

## <a name="end-user-authentication"></a>Son kullanıcı kimlik doğrulaması
Son Kullanıcı kimlik doğrulaması, bir kullanıcının Azure AD 'yi kullanarak uygulamanızda oturum açmasını istiyorsanız önerilen yaklaşımdır. Uygulamanız, oturum açmış kullanıcıyla aynı erişim düzeyine sahip Azure kaynaklarına erişebiliyor. Uygulamanızın erişimi sürdürmek için, kullanıcının kimlik bilgilerini düzenli aralıklarla sağlaması gerekir.

Son Kullanıcı oturumuna sahip olmanın sonucu, uygulamanıza bir erişim belirteci ve yenileme belirteci verilme sonucudur. Erişim belirteci Data Lake Storage 1. veya Data Lake Analytics yapılan her isteğe iliştirilir ve varsayılan olarak bir saat için geçerlidir. Yenileme belirteci yeni bir erişim belirteci almak için kullanılabilir ve düzenli olarak kullanılıyorsa, varsayılan olarak en fazla iki hafta için geçerlidir. Son Kullanıcı oturumu için iki farklı yaklaşım kullanabilirsiniz.

Bu senaryoda, uygulama kullanıcıdan oturum açmasını ister ve tüm işlemler, kullanıcı bağlamında gerçekleştirilir. Aşağıdaki adımları gerçekleştirin:

1. Uygulamanızı kullanarak kullanıcıyı şu URL'ye yönlendirin:

    `https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>`

   > [!NOTE]
   > \<REDIRECT-URI> URL 'de kullanılmak üzere kodlanmalıdır. Bu nedenle, için https://localhost kullanın `https%3A%2F%2Flocalhost` )

    Bu öğreticinin amaçları doğrultusunda, yukarıdaki URL'deki yer tutucu değerlerini değiştirebilir ve bir web tarayıcısının adres çubuğuna yapıştırabilirsiniz. Azure oturum açma bilgilerinizi kullanarak kimlik doğrulaması gerçekleştirmeye yönlendirileceksiniz. Başarıyla oturum açtığınızda yanıt, tarayıcının adres çubuğunda görüntülenir. Yanıt şu biçimde olacaktır:

    `http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>`

2. Yanıttaki yetki kodunu alın. Bu öğreticide, aşağıdaki kod parçacığında gösterildiği gibi, Web tarayıcısının adres çubuğundan yetkilendirme kodunu kopyalayabilir ve onu, belirteç uç noktasına POST isteğine geçirebilirsiniz:

    ```console
    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
    -F redirect_uri=<REDIRECT-URI> \
    -F grant_type=authorization_code \
    -F resource=https://management.core.windows.net/ \
    -F client_id=<APPLICATION-ID> \
    -F code=<AUTHORIZATION-CODE>
    ```

   > [!NOTE]
   > Bu durumda, \<REDIRECT-URI> kodlanmamalıdır.
   > 
   > 

3. Yanıt, bir erişim belirteci (örneğin, `"access_token": "<ACCESS_TOKEN>"` ) ve yenileme belirteci (örneğin,) içeren BIR JSON nesnesidir `"refresh_token": "<REFRESH_TOKEN>"` . Uygulamanız Azure Data Lake Storage 1. erişirken erişim belirtecini ve bir erişim belirtecinin süresi dolmuşsa başka bir erişim belirteci almak için yenileme belirtecini kullanır.

    ```json
    {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}
    ```

4. Erişim belirtecinin süresi dolarsa, aşağıdaki kod parçacığında gösterildiği gibi yenileme belirtecini kullanarak yeni bir erişim belirteci isteyebilirsiniz:

    ```console
    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
         -F grant_type=refresh_token \
         -F resource=https://management.core.windows.net/ \
         -F client_id=<APPLICATION-ID> \
         -F refresh_token=<REFRESH-TOKEN>
    ```

Etkileşimli kullanıcı kimlik doğrulaması hakkında daha fazla bilgi için bkz. [Yetki kodu izin akışı](/previous-versions/azure/dn645542(v=azure.100)).

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, REST API kullanarak Azure Data Lake Storage 1. kimlik doğrulaması yapmak için hizmetten hizmete kimlik doğrulamasını nasıl kullanacağınızı öğrendiniz. Artık, Azure Data Lake Storage 1. çalışmak için REST API kullanma hakkında konuşabilecek aşağıdaki makalelere bakabilirsiniz.

* [REST API kullanarak Data Lake Storage 1. hesap yönetimi işlemleri](data-lake-store-get-started-rest-api.md)
* [REST API kullanarak Data Lake Storage 1. veri işlemleri](data-lake-store-data-operations-rest-api.md)