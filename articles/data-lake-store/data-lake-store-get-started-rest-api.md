---
title: "REST API&quot;yi kullanarak Data Lake Store ile çalışmaya başlama| Microsoft Docs"
description: "Data Lake Store üzerinde işlem yapmak için WebHDFS REST API&quot;lerini kullanma"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/03/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: 33574b0c1f023a8a5f83b1bf06f0523623891757
ms.lasthandoff: 01/24/2017


---
# <a name="get-started-with-azure-data-lake-store-using-rest-apis"></a>REST API'lerini kullanarak Azure Data Lake Store ile çalışmaya başlama
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

Bu makalede, Azure Data Lake Store üzerinde hesap yönetimi ve dosya sistemi işlemleri gerçekleştirmek üzere WebHDFS REST API'lerini ve Data Lake Store REST API'lerini nasıl kullanacağınızı öğreneceksiniz. Azure Data Lake Store, hesap yönetimi işlemleri için kendi REST API'lerini kullanıma sunar. Ancak Data Lake Store, HDFS ve Hadoop ekosistemi ile uyumlu olması nedeniyle, dosya sistemi işlemleri için WebHDFS REST API'lerini kullanarak destek sağlar.

> [!NOTE]
> Data Lake Store için REST API desteği hakkında ayrıntılı bilgi için bkz. [Azure Data Lake Store REST API Başvurusu](https://msdn.microsoft.com/library/mt693424.aspx).
> 
> 

## <a name="prerequisites"></a>Ön koşullar
* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Active Directory Uygulaması oluşturma**. Data Lake Store uygulamasında Azure AD ile kimlik doğrulaması yapmak için Azure AD uygulamasını kullanın. Azure AD kimlik doğrulaması için **son kullanıcı kimlik doğrulaması** veya **hizmetten hizmete kimlik doğrulama** gibi farklı yaklaşımlar bulunmaktadır. Kimlik doğrulaması hakkında yönergeler ve daha fazla bilgi için bkz. [Azure Active Directory kullanarak Data Lake Store kimlik doğrulaması yapma](data-lake-store-authenticate-using-active-directory.md).
* [cURL](http://curl.haxx.se/). Bu makalede, bir Data Lake Store hesabına yönelik olarak REST API çağrılarının nasıl yapılacağını göstermek üzere cURL kullanılmıştır.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Azure Active Directory'yi kullanarak nasıl kimlik doğrulaması gerçekleştiririm?
Azure Active Directory'yi kullanarak kimlik doğrulaması gerçekleştirmek üzere iki yaklaşımdan faydalanabilirsiniz:

### <a name="end-user-authentication-interactive"></a>Son kullanıcı kimlik doğrulaması (etkileşimli)
Bu senaryoda, uygulama kullanıcıdan oturum açmasını ister ve tüm işlemler, kullanıcı bağlamında gerçekleştirilir. Etkileşimli kimlik doğrulaması için aşağıdaki adımları gerçekleştirin.

1. Uygulamanızı kullanarak kullanıcıyı şu URL'ye yönlendirin:
   
        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>
   
   > [!NOTE]
   > \<REDIRECT-URI>, bir URL içinde kullanılmak üzere kodlanmalıdır. Bu nedenle, https://localhost için `https%3A%2F%2Flocalhost` kullanılır)
   > 
   > 
   
    Bu öğreticinin amaçları doğrultusunda, yukarıdaki URL'deki yer tutucu değerlerini değiştirebilir ve bir web tarayıcısının adres çubuğuna yapıştırabilirsiniz. Azure oturum açma bilgilerinizi kullanarak kimlik doğrulaması gerçekleştirmeye yönlendirileceksiniz. Başarıyla oturum açtığınızda yanıt, tarayıcının adres çubuğunda görüntülenir. Yanıt şu biçimde olacaktır:
   
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>
2. Yanıttaki yetki kodunu alın. Bu öğretici için, web tarayıcısının adres çubuğundan yetki kodunu kopyalayabilir ve belirteç uç noktasını istemek için aşağıda gösterildiği gibi POST isteğinde geçirebilirsiniz:
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>
   
   > [!NOTE]
   > Bu durumda, \<REDIRECT-URI> öğesinin kodlanması gerekmez.
   > 
   > 
3. Yanıt, bir erişim belirteci içeren bir JSON nesnesi (ör. `"access_token": "<ACCESS_TOKEN>"`) ve bir yenileme belirtecidir (ör. `"refresh_token": "<REFRESH_TOKEN>"`). Uygulamanız Azure Data Lake Store'a erişmek için erişim belirtecini ve bir erişim belirtecinin süresi olduğunda başka bir erişim belirteci almak için yenileme belirtecini kullanır.
   
        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}
4. Erişim belirtecinin süresi dolduğunda, aşağıda gösterildiği gibi yenileme belirtecini kullanarak yeni bir erişim belirteci isteyebilirsiniz:
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<CLIENT-ID> \
             -F refresh_token=<REFRESH-TOKEN>

Etkileşimli kullanıcı kimlik doğrulaması hakkında daha fazla bilgi için bkz. [Yetki kodu izin akışı](https://msdn.microsoft.com/library/azure/dn645542.aspx).

### <a name="service-to-service-authentication-non-interactive"></a>Hizmetten hizmete kimlik doğrulaması (etkileşimli olmayan)
Bu senaryoda uygulama, işlemleri gerçekleştirmek için kendi kimlik bilgilerini sağlar. Bunun için aşağıda gösterilene benzer bir POST isteği yayımlamanız gerekir. 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

Bu isteğin çıktısı, sonrasında REST API çağrılarınızla geçireceğiniz bir yetki belirteci (aşağıdaki çıktıda `access-token` ile belirtilmiştir) içerir. Bu kimlik doğrulama belirtecini bir metin dosyasına kaydedin; belirteç, bu makalenin ilerleyen bölümlerinde gerekli olacaktır.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Bu makalede, **etkileşimli olmayan** yaklaşım kullanılmıştır. Etkileşimli olmayan seçeneği (hizmet-hizmet çağrıları) hakkında daha fazla bilgi için bkz. [Kimlik bilgilerini kullanarak gerçekleştirilen hizmet-hizmet çağrıları](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## <a name="create-a-data-lake-store-account"></a>Data Lake Store hesabı oluşturma
Bu işlem, [burada](https://msdn.microsoft.com/library/mt694078.aspx) tanımlanan REST API çağrısını temel alır.

Aşağıdaki cURL komutunu kullanın. **\<yourstorename>** ifadesini, Data Lake Store adınızla değiştirin.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

Yukarıdaki komutta; \<`REDACTED`\> öğesini daha önce aldığınız yetkilendirme belirteciyle değiştirin. Bu komuta yönelik istek yükü, yukarıdaki `-d` parametresi için sağlanan **input.json** dosyasına dahildir. Söz konusu input.json dosyasının içeriği şuna benzer:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="create-folders-in-a-data-lake-store-account"></a>Data Lake Store hesabında klasör oluşturma
Bu işlem, [burada](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory) tanımlanan WebHDFS REST API çağrısını temel alır.

Aşağıdaki cURL komutunu kullanın. **\<yourstorename>** ifadesini, Data Lake Store adınızla değiştirin.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS

Yukarıdaki komutta; \<`REDACTED`\> öğesini daha önce aldığınız yetkilendirme belirteciyle değiştirin. Bu komut, Data Lake Store hesabınızın kök klasörü altında **mytempdir** adlı bir dizin oluşturur.

İşlem başarıyla tamamlanırsa şuna benzer bir yanıt görmeniz gerekir:

    {"boolean":true}

## <a name="list-folders-in-a-data-lake-store-account"></a>Data Lake Store hesabındaki klasörleri listeleme
Bu işlem, [burada](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory) tanımlanan WebHDFS REST API çağrısını temel alır.

Aşağıdaki cURL komutunu kullanın. **\<yourstorename>** ifadesini, Data Lake Store adınızla değiştirin.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS

Yukarıdaki komutta; \<`REDACTED`\> öğesini daha önce aldığınız yetkilendirme belirteciyle değiştirin.

İşlem başarıyla tamamlanırsa şuna benzer bir yanıt görmeniz gerekir:

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "NotSupportYet",
            "group": "NotSupportYet"
        }]
    }
    }

## <a name="upload-data-into-a-data-lake-store-account"></a>Data Lake Store hesabına veri yükleme
Bu işlem, [burada](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File) tanımlanan WebHDFS REST API çağrısını temel alır.

Aşağıda açıklandığı gibi, WebHDFS REST API'sini kullanarak veri yüklemek iki adımlı bir işlemdir.

1. Yüklenecek dosya verilerini göndermeden bir HTTP PUT isteği gönderin. Aşağıdaki komutta, **\<yourstorename>** ifadesini, Data Lake Store adınızla değiştirin.
   
        curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=CREATE
   
    Bu komutun çıktısı, aşağıda gösterilene benzer bir geçici yeniden yönlendirme URL'si içerecektir.
   
        HTTP/1.1 100 Continue
   
        HTTP/1.1 307 Temporary Redirect
        ...
        ...
        Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=CREATE&write=true
        ...
        ...
2. Yanıttaki **Konum** özelliği için listelenen URL için başka bir HTTP PUT isteği göndermeniz gerekir. **\<yourstorename>** ifadesini, Data Lake Store adınızla değiştirin.
   
        curl -i -X PUT -T myinputfile.txt -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=CREATE&write=true
   
    Çıktı şununla benzerlik gösterecektir:
   
        HTTP/1.1 100 Continue
   
        HTTP/1.1 201 Created
        ...
        ...

## <a name="read-data-from-a-data-lake-store-account"></a>Data Lake Store hesabından veri okuma
Bu işlem, [burada](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File) tanımlanan WebHDFS REST API çağrısını temel alır.

Data Lake Store hesabından veri okuma, iki adımlı bir işlemdir.

* İlk olarak `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN` uç noktası için bir GET isteği gönderirsiniz. Bu, sonraki GET isteğini göndermek için bir konum döndürür.
* Ardından `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true` uç noktası için GET isteğini gönderirsiniz. Bu, dosyanın içeriğini görüntüler.

Ancak giriş parametrelerinde birinci ve ikinci adım arasında fark olmadığından, ilk isteği göndermek için `-L` parametresini kullanabilirsiniz. `-L` seçeneği, temelde iki isteği tek istekte birleştirir ve cURL'nin isteği yeni konumda yeniden gerçekleştirmesini sağlar. Son olarak, aşağıda gösterildiği gibi, tüm istek çağrılarının çıktısı görüntülenir. **\<yourstorename>** ifadesini, Data Lake Store adınızla değiştirin.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN

Aşağıdakine benzer bir çıktı görmeniz gerekir:

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...

    HTTP/1.1 200 OK
    ...

    Hello, Data Lake Store user!

## <a name="rename-a-file-in-a-data-lake-store-account"></a>Data Lake Store hesabındaki bir dosyayı yeniden adlandırma
Bu işlem, [burada](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory) tanımlanan WebHDFS REST API çağrısını temel alır.

Bir dosyayı yeniden adlandırmak için aşağıdaki cURL komutunu kullanın. **\<yourstorename>** ifadesini, Data Lake Store adınızla değiştirin.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt

Aşağıdakine benzer bir çıktı görmeniz gerekir:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-file-from-a-data-lake-store-account"></a>Data Lake Store hesabından dosya silme
Bu işlem, [burada](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory) tanımlanan WebHDFS REST API çağrısını temel alır.

Bir dosyayı silmek için aşağıdaki cURL komutunu kullanın. **\<yourstorename>** ifadesini, Data Lake Store adınızla değiştirin.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

Aşağıdaki gibi bir çıktı görmeniz gerekir:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-data-lake-store-account"></a>Data Lake Store hesabını silme
Bu işlem, [burada](https://msdn.microsoft.com/library/mt694075.aspx) tanımlanan REST API çağrısını temel alır.

Bir Data Lake Store hesabını silmek için aşağıdaki cURL komutunu kullanın. **\<yourstorename>** ifadesini, Data Lake Store adınızla değiştirin.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Aşağıdaki gibi bir çıktı görmeniz gerekir:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="see-also"></a>Ayrıca bkz.
* [Azure Data Lake Store ile uyumlu Açık Kaynak Büyük Veri uygulamaları](data-lake-store-compatible-oss-other-applications.md)


