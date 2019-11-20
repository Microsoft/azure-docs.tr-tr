---
title: Azure blok zinciri çalışma ekranı iletileri tümleştirmesine genel bakış
description: Azure blok zinciri çalışma ekranı önizlemesinde ileti kullanımına genel bakış.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 99159b15ea663d43d125748d6db1f334b72931ae
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161803"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Azure blok zinciri çalışma ekranı mesajlaşma tümleştirmesi

Azure blok zinciri de REST API sağlamaya ek olarak, mesajlaşma tabanlı tümleştirme de sağlar. Çalışma ekranı, Azure Event Grid aracılığıyla defter merkezli olayları yayımlar ve bu olaylara dayalı olarak aşağı akış tüketicilerini verileri alacak veya işlem yapması için etkinleştirir. Güvenilir Mesajlaşma gerektiren istemciler için, Azure blok zinciri çalışma ekranı, iletileri bir Azure Service Bus uç noktasına da sunar.

## <a name="input-apis"></a>Giriş API 'Leri

Kullanıcıları oluşturmak, sözleşmeleri oluşturmak ve sözleşmeleri güncelleştirmek için dış sistemlerden işlem başlatmak istiyorsanız, bir muhasebe üzerinde işlem gerçekleştirmek için mesajlaşma girişi API 'Lerini kullanabilirsiniz. Giriş API 'Lerini gösteren bir örnek için [mesajlaşma tümleştirme örneklerine](https://aka.ms/blockchain-workbench-integration-sample) bakın.

Şu anda kullanılabilir giriş API 'Leri aşağıda verilmiştir.

### <a name="create-user"></a>Kullanıcı Oluştur

Yeni bir kullanıcı oluşturur.

İstek aşağıdaki alanları gerektiriyor:

| **Adı**             | **Açıklama**                                      |
|----------------------|------------------------------------------------------|
| No            | İstemci tarafından sağlanan GUID                                |
| firstName            | Kullanıcının adı                              |
| Soyadı             | Kullanıcının soyadı                               |
| emailAddress         | Kullanıcının e-posta adresi                           |
| externalID           | Kullanıcının Azure AD nesne KIMLIĞI                      |
| ConnectionID         | Blok zinciri bağlantısı için benzersiz tanımlayıcı |
| Iletichemaversion | Mesajlaşma şeması sürümü                            |
| Ileti Gename          | **CreateUserRequest**                               |

Örnek:

``` json
{
    "requestId": "e2264523-6147-41fc-bbbb-edba8e44562d",
    "firstName": "Ali",
    "lastName": "Alio",
    "emailAddress": "aa@contoso.com",
    "externalId": "6a9b7f65-ffff-442f-b3b8-58a35abd1bcd",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateUserRequest"
}
```

Blok zinciri çalışma ekranı aşağıdaki alanlarla bir yanıt döndürür:

| **Adı**              | **Açıklama**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| No             | İstemci tarafından sağlanan GUID |
| userId                | Oluşturulan kullanıcının KIMLIĞI |
| Userchainıdentifier   | Blok zinciri ağında oluşturulan kullanıcının adresi. Ethereum 'da, adres kullanıcının **zincir** adresidir. |
| ConnectionID          | Blok zinciri bağlantısı için benzersiz tanımlayıcı|
| Iletichemaversion  | Mesajlaşma şeması sürümü |
| Ileti Gename           | **CreateUserUpdate** |
| durum                | Kullanıcı oluşturma isteğinin durumu.  Başarılı olursa, değer **başarılı**olur. Hata durumunda, değer **hata**.     |
| AdditionalInformation | Durum temelinde sunulan ek bilgiler |

Blok zinciri çalışma ekranı 'ndan başarılı bir şekilde **Kullanıcı yanıtı oluştur** :

``` json
{ 
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Success", 
    "additionalInformation": { } 
} 
```

İstek başarısız olduysa, hata hakkındaki ayrıntılar ek bilgilere dahil edilir.

``` json
{
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": null, 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Failure", 
    "additionalInformation": { 
        "errorCode": 4000, 
        "errorMessage": "User cannot be provisioned on connection." 
    }
}
```

### <a name="create-contract"></a>Sözleşme Oluştur

Yeni bir sözleşme oluşturur.

İstek aşağıdaki alanları gerektiriyor:

| **Adı**             | **Açıklama**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| No            | İstemci tarafından sağlanan GUID |
| Userchainıdentifier  | Blok zinciri ağında oluşturulan kullanıcının adresi. Ethereum 'da, bu adres kullanıcının **zincir** adresidir. |
| ApplicationName      | Uygulamanın adı |
| version              | Uygulamanın sürümü. Uygulamanın birden çok sürümünün etkinleştirilmiş olması halinde gereklidir. Aksi takdirde, sürüm isteğe bağlıdır. Uygulama sürümü oluşturma hakkında daha fazla bilgi için bkz. [Azure blok zinciri uygulama sürümü oluşturma](version-app.md). |
| workflowName         | İş akışının adı |
| parametreler           | Sözleşme oluşturma için parametreler girişi |
| ConnectionID         | Blok zinciri bağlantısı için benzersiz tanımlayıcı |
| Iletichemaversion | Mesajlaşma şeması sürümü |
| Ileti Gename          | **CreateContractRequest** |

Örnek:

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer",
    "version": "1.0",
    "workflowName": "AssetTransfer", 
    "parameters": [ 
        { 
            "name": "description", 
            "value": "a 1969 dodge charger" 
        }, 
        { 
            "name": "price", 
            "value": "12345" 
        } 
    ], 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateContractRequest" 
}
```

Blok zinciri çalışma ekranı aşağıdaki alanlarla bir yanıt döndürür:

| **Adı**                 | **Açıklama**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| No                | İstemci tarafından sağlanan GUID                                                             |
| sözleşme kimliği               | Azure blok zinciri çalışma ekranı içindeki sözleşme için benzersiz tanımlayıcı |
| Contractledgerıdentifier | Muhasebedeki sözleşmenin adresi                                            |
| ConnectionID             | Blok zinciri bağlantısı için benzersiz tanımlayıcı                               |
| Iletichemaversion     | Mesajlaşma şeması sürümü                                                         |
| Ileti Gename              | **CreateContractUpdate**                                                      |
| durum                   | Sözleşme oluşturma isteğinin durumu.  Olası değerler: **gönderildi**, **kaydedildi**, **başarısız**.  |
| AdditionalInformation    | Durum temelinde sunulan ek bilgiler                              |

Blok zinciri için gönderilen bir **sözleşme yanıtı oluşturma** çalışma ekranı örneği:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Blok zinciri çalışma ekranı 'ndan kaydedilmiş bir **sözleşme yanıtı oluşturma** örneği:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

İstek başarısız olduysa, hata hakkındaki ayrıntılar ek bilgilere dahil edilir.

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>Sözleşme oluşturma eylemi

Yeni bir sözleşme eylemi oluşturur.

İstek aşağıdaki alanları gerektiriyor:

| **Adı**                 | **Açıklama**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| No                | İstemci tarafından sağlanan GUID |
| Userchainıdentifier      | Blok zinciri ağında oluşturulan kullanıcının adresi. Ethereum 'da, bu adres kullanıcının **zincir** adresidir. |
| Contractledgerıdentifier | Muhasebedeki sözleşmenin adresi |
| version                  | Uygulamanın sürümü. Uygulamanın birden çok sürümünün etkinleştirilmiş olması halinde gereklidir. Aksi takdirde, sürüm isteğe bağlıdır. Uygulama sürümü oluşturma hakkında daha fazla bilgi için bkz. [Azure blok zinciri uygulama sürümü oluşturma](version-app.md). |
| Workflowfonksiyonadı     | İş akışı işlevinin adı |
| parametreler               | Sözleşme oluşturma için parametreler girişi |
| ConnectionID             | Blok zinciri bağlantısı için benzersiz tanımlayıcı |
| Iletichemaversion     | Mesajlaşma şeması sürümü |
| Ileti Gename              | **CreateContractActionRequest** |

Örnek:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "version": "1.0",
    "workflowFunctionName": "modify",
    "parameters": [
        {
            "name": "description",
            "value": "a 1969 dodge charger"
        },
        {
            "name": "price",
            "value": "12345"
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionRequest"
}
```

Blok zinciri çalışma ekranı aşağıdaki alanlarla bir yanıt döndürür:

| **Adı**              | **Açıklama**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| No             | İstemci tarafından sağlanan GUID|
| sözleşme kimliği            | Azure blok zinciri çalışma ekranı içindeki sözleşme için benzersiz tanımlayıcı |
| ConnectionID          | Blok zinciri bağlantısı için benzersiz tanımlayıcı |
| Iletichemaversion  | Mesajlaşma şeması sürümü |
| Ileti Gename           | **CreateContractActionUpdate** |
| durum                | Sözleşme eylemi isteğinin durumu. Olası değerler: **gönderildi**, **kaydedildi**, **başarısız**.                         |
| AdditionalInformation | Durum temelinde sunulan ek bilgiler |

Blok zinciri çalışma ekranı 'ndan gönderilen **sözleşme oluşturma eylem** yanıtı örneği:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Blok zinciri çalışma ekranı 'ndan kaydedilmiş bir **sözleşme oluşturma eylem** yanıtı örneği:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

İstek başarısız olduysa, hata hakkındaki ayrıntılar ek bilgilere dahil edilir.

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>Giriş API 'SI hata kodları ve iletileri

**Hata kodu 4000: Hatalı istek hatası**
- Geçersiz ConnectionID
- CreateUserRequest seri durumdan çıkarılamadı
- CreateContractRequest seri durumdan çıkarılamadı
- CreateContractActionRequest seri durumundan çıkarılamadı
- {Uygulama adı tarafından tanımlanan} uygulaması yok
- {Application Name tarafından tanımlanan} uygulamasının iş akışı yok
- Userchainıdentifier yok
- {Muhasebe tanımlayıcısı ile tanımlanan} sözleşmesi yok
- {Muhasebe tanımlayıcısına göre tanımlanan} sözleşmede {Workflow Function Name} işlevi yok
- Userchainıdentifier yok

**Hata kodu 4090: çakışma hatası**
- Kullanıcı zaten var
- Sözleşme zaten var
- Sözleşme eylemi zaten var

**Hata kodu 5000: Iç sunucu hatası**
- Özel durum iletileri

## <a name="event-notifications"></a>Olay bildirimleri

Olay bildirimleri, blok zinciri çalışma ekranı ve bağlı olduğu blok zinciri ağı 'nda oluşan olayların kullanıcılarına ve aşağı akış sistemlerine bildirimde bulunabilir. Olay bildirimleri doğrudan kodda tüketilebilir veya Logic Apps ve Flow gibi araçlarla, aşağı akış sistemlerine veri akışını tetiklemek için kullanılabilir.

Alınabilecek çeşitli mesajların ayrıntıları için bkz. [bildirim iletisi başvurusu](#notification-message-reference) .

### <a name="consuming-event-grid-events-with-azure-functions"></a>Azure Işlevleri ile Event Grid olayları kullanma

Bir Kullanıcı blok zinciri çalışma ekranı 'nda oluşan olaylar hakkında bildirim almak için Event Grid kullanmak isterse, Azure Işlevleri 'ni kullanarak Event Grid olayları kullanabilirsiniz.

1. Azure portal **Azure işlev uygulaması** oluşturun.
2. Yeni bir işlev oluşturun.
3. Event Grid şablonunu bulun. İletiyi okumak için temel şablon kodu gösterilir. Kodu gereken şekilde değiştirin.
4. Işlevi kaydedin. 
5. Blok zinciri çalışma ekranının kaynak grubundaki Event Grid seçin.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Logic Apps Event Grid olaylarını kullanma

1. Azure portal yeni bir **Azure mantıksal uygulaması** oluşturun.
2. Portalda Azure Logic App 'i açarken bir tetikleyici seçmeniz istenir. **Bir kaynak olay gerçekleştiğinde Azure Event Grid**seçin.
3. İş akışı Tasarımcısı görüntülendiğinde oturum açmanız istenir.
4. Aboneliği seçin. **Microsoft. EventGrid. konular**olarak kaynak. Azure blok zinciri çalışma ekranı kaynak grubundaki kaynak adı listesinden **kaynak adı** ' nı seçin.
5. Blok zinciri çalışma ekranının kaynak grubundaki Event Grid seçin.

## <a name="using-service-bus-topics-for-notifications"></a>Bildirimler için Service Bus konuları kullanma

Service Bus konular, kullanıcılara blok zinciri çalışma ekranı 'nda meydana gelen olayları bildirmek için kullanılabilir. 

1. Çalışma ekranının kaynak grubu içindeki Service Bus gidin.
2. **Konular**' ı seçin.
3. **Çıkış konusunu**seçin.
4. Bu konuda yeni bir abonelik oluşturun. Bunun için bir anahtar edinin.
5. Bu abonelikteki olaylara abone olan bir program oluşturun.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Logic Apps Service Bus Iletileri kullanma

1. Azure portal yeni bir **Azure mantıksal uygulaması** oluşturun.
2. Portalda Azure Logic App 'i açarken bir tetikleyici seçmeniz istenir. Arama kutusuna **Service Bus** yazın ve Service Bus sahip olmasını istediğiniz etkileşim türü için uygun tetikleyiciyi seçin. Örneğin, **Service Bus--bir konu aboneliğinde bir ileti alındığında (otomatik olarak tamamlanır)** .
3. İş akışı Tasarımcısı görüntülendiğinde Service Bus için bağlantı bilgilerini belirtin.
4. Aboneliğinizi seçin ve çalışma **ekranı-dış**konusunu belirtin.
5. Uygulamanız için bu tetikleyiciden gelen iletiden yararlanan mantığı geliştirin.

## <a name="notification-message-reference"></a>Bildirim iletisi başvurusu

**MessageName**öğesine bağlı olarak, bildirim iletileri aşağıdaki ileti türlerinden birine sahiptir.

### <a name="block-message"></a>İletiyi engelle

Ayrı bloklarla ilgili bilgiler içerir. *Blockmessage* , blok düzeyi bilgileri olan bir bölüm ve işlem bilgileri içeren bir bölüm içerir.

| Adı | Açıklama |
|------|-------------|
| engelleyin | [Engelleme bilgilerini](#block-information) içerir |
| işlem | Blok için bir koleksiyon [işlem bilgileri](#transaction-information) içerir |
| ConnectionID | Bağlantı için benzersiz tanımlayıcı |
| Iletichemaversion | Mesajlaşma şeması sürümü |
| Ileti Gename | **Blok Ileti** |
| AdditionalInformation | Ek bilgiler belirtildi |

#### <a name="block-information"></a>Engelleme bilgileri

| Adı              | Açıklama |
|-------------------|-------------|
| blockID 'si           | Azure blok zinciri çalışma ekranı içindeki bloğun benzersiz tanımlayıcısı |
| Blok numarası       | Genel muhasebedeki bir bloğun benzersiz tanımlayıcısı |
| Blok karması         | Bloğun karması |
| Previousblockkarması | Önceki bloğun karması |
| blockTimestamp    | Bloğun zaman damgası |

#### <a name="transaction-information"></a>İşlem bilgileri

| Adı               | Açıklama |
|--------------------|-------------|
| TransactionId      | Azure blok zinciri çalışma ekranı içindeki işlem için benzersiz tanımlayıcı |
| Işlem karması    | Genel muhasebedeki işlemin karması |
| Kaynak               | İşlem kaynağı için genel muhasebedeki benzersiz tanımlayıcı |
| -                 | İşlem hedefi için genel muhasebedeki benzersiz tanımlayıcı |
| provisioningStatus | İşlem için sağlama işleminin geçerli durumunu tanımlar. Olası değerler şunlardır: </br>0 – işlem, veritabanında API tarafından oluşturulmuştur</br>1 – işlem, genel muhasebeye gönderildi</br>2 – işlem, genel muhasebeye başarıyla yürütüldü</br>3 veya 4-işlem, genel muhasebeye işlenemedi</br>5-işlem, genel muhasebeye başarıyla yürütüldü |

Blok zinciri çalışma ekranı 'ndan bir *blok iletisi* örneği:

``` json
{
    "block": {
        "blockId": 123,
        "blockNumber": 1738312,
        "blockHash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
        "previousBlockHash": null,
        "blockTimestamp": "2018-10-09T23:35:58Z",
    },
    "transactions": [
        {
            "transactionId": 234,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": null,
            "provisioningStatus": 1
        },
        {
            "transactionId": 235,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xadd97e1e595916e29ea94fda894941574000ffff",
            "to": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff",
            "provisioningStatus": 2
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "BlockMessage",
    "additionalInformation": {}
}
```

### <a name="contract-message"></a>Sözleşme iletisi

Bir sözleşme hakkındaki bilgileri içerir. İleti, sözleşme özelliklerine ve işlem bilgilerine sahip bir bölüme sahip bir bölüm içerir. Belirli bir bloğun sözleşmesini değiştiren tüm işlemler, işlem bölümüne dahil edilir.

| Adı | Açıklama |
|------|-------------|
| blockID 'si | Azure blok zinciri çalışma ekranı içindeki bloğun benzersiz tanımlayıcısı |
| Blok karması | Bloğun karması |
| modifyingTransactions | Sözleşmeyi [değiştiren işlemler](#modifying-transaction-information) |
| sözleşme kimliği | Azure blok zinciri çalışma ekranı içindeki sözleşme için benzersiz tanımlayıcı |
| Contractledgerıdentifier | Muhasebedeki sözleşme için benzersiz tanımlayıcı |
| contractProperties | [Sözleşmenin özellikleri](#contract-properties) |
| ınewcontract | Bu sözleşmenin yeni oluşturulup oluşturulmayacağını gösterir. Olası değerler: true: Bu sözleşme oluşturulan yeni bir sözleşiydi. yanlış: Bu sözleşme bir sözleşme güncelleştirmesidir. |
| ConnectionID | Bağlantı için benzersiz tanımlayıcı |
| Iletichemaversion | Mesajlaşma şeması sürümü |
| Ileti Gename | **ContractMessage** |
| AdditionalInformation | Ek bilgiler belirtildi |

#### <a name="modifying-transaction-information"></a>İşlem bilgilerini değiştirme

| Adı               | Açıklama |
|--------------------|-------------|
| TransactionId | Azure blok zinciri çalışma ekranı içindeki işlem için benzersiz tanımlayıcı |
| Işlem karması | Genel muhasebedeki işlemin karması |
| Kaynak | İşlem kaynağı için genel muhasebedeki benzersiz tanımlayıcı |
| - | İşlem hedefi için genel muhasebedeki benzersiz tanımlayıcı |

#### <a name="contract-properties"></a>Sözleşme Özellikleri

| Adı               | Açıklama |
|--------------------|-------------|
| Workflowpropertyıd | Azure blok zinciri çalışma ekranı içindeki iş akışı özelliği için benzersiz tanımlayıcı |
| ad | Workflow özelliğinin adı |
| değer | Workflow özelliğinin değeri |

Blok zinciri çalışma ekranı 'ndan bir *ContractMessage* örneği:

``` json
{
    "blockId": 123,
    "blockhash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
    "modifyingTransactions": [
        {
            "transactionId": 234,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07ffff"
        },
        {
            "transactionId": 235,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
        }
    ],
    "contractId": 111,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "contractProperties": [
        {
            "workflowPropertyId": 1,
            "name": "State",
            "value": "0"
        },
        {
            "workflowPropertyId": 2,
            "name": "Description",
            "value": "1969 Dodge Charger"
        },
        {
            "workflowPropertyId": 3,
            "name": "AskingPrice",
            "value": "30000"
        },
        {
            "workflowPropertyId": 4,
            "name": "OfferPrice",
            "value": "0"
        },
        {
            "workflowPropertyId": 5,
            "name": "InstanceAppraiser",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 6,
            "name": "InstanceBuyer",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 7,
            "name": "InstanceInspector",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 8,
            "name": "InstanceOwner",
            "value": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
        },
        {
            "workflowPropertyId": 9,
            "name": "ClosingDayOptions",
            "value": "[21,48,69]"
        }
    ],
    "isNewContract": false,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "ContractMessage",
    "additionalInformation": {}
}
```

### <a name="event-message-contract-function-invocation"></a>Olay iletisi: sözleşme işlevi çağırma

İşlev adı, parametreler girişi ve işlevin çağıranı gibi bir anlaşma işlevi çağrıldığında bilgiler içerir.

| Adı | Açıklama |
|------|-------------|
| eventName                   | **Contractfunctionınvocation** |
| yapana                      | [Arayan bilgileri](#caller-information) |
| sözleşme kimliği                  | Azure blok zinciri çalışma ekranı içindeki sözleşme için benzersiz tanımlayıcı |
| Contractledgerıdentifier    | Muhasebedeki sözleşme için benzersiz tanımlayıcı |
| Ifadelerini                | İşlevin adı |
| parametreler                  | [Parametre bilgileri](#parameter-information) |
| işlem                 | İşlem bilgileri |
| inTransactionSequenceNumber | Bloktaki işlemin sıra numarası |
| ConnectionID                | Bağlantı için benzersiz tanımlayıcı |
| Iletichemaversion        | Mesajlaşma şeması sürümü |
| Ileti Gename                 | **EventMessage** |
| AdditionalInformation       | Ek bilgiler belirtildi |

#### <a name="caller-information"></a>Arayan bilgileri

| Adı | Açıklama |
|------|-------------|
| type | Arayan türü, Kullanıcı veya sözleşme gibi |
| id | Azure blok zinciri çalışma ekranı içindeki arayan için benzersiz tanımlayıcı |
| Ledgerıdentifier | Genel muhasebedeki arayan için benzersiz tanımlayıcı |

#### <a name="parameter-information"></a>Parametre bilgileri

| Adı | Açıklama |
|------|-------------|
| ad | Parametre adı |
| değer | Parametre değeri |

#### <a name="event-message-transaction-information"></a>Olay iletisi işlem bilgileri

| Adı               | Açıklama |
|--------------------|-------------|
| TransactionId      | Azure blok zinciri çalışma ekranı içindeki işlem için benzersiz tanımlayıcı |
| Işlem karması    | Genel muhasebedeki işlemin karması |
| Kaynak               | İşlem kaynağı için genel muhasebedeki benzersiz tanımlayıcı |
| -                 | İşlem hedefi için genel muhasebedeki benzersiz tanımlayıcı |

Blok zinciri çalışma ekranı 'ndan *Eventmessage Contractfunctionınvocation* örneği:

``` json
{
    "eventName": "ContractFunctionInvocation",
    "caller": {
        "type": "User",
        "id": 21,
        "ledgerIdentifier": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60ffff"
    },
    "contractId": 34,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "functionName": "Modify",
    "parameters": [
        {
            "name": "description",
            "value": "a new description"
        },
        {
            "name": "price",
            "value": "4567"
        }
    ],
    "transaction": {
        "transactionId": 234,
        "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
        "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
        "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
    },
    "inTransactionSequenceNumber": 1,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

### <a name="event-message-application-ingestion"></a>Olay iletisi: uygulama alımı

Karşıya yüklenen uygulamanın adı ve sürümü gibi bir uygulama çalışma ekranına yüklendiğinde bilgiler içerir.

| Adı | Açıklama |
|------|-------------|
| eventName | **Uygulama alımı** |
| Uygulama | Azure blok zinciri çalışma ekranı içindeki uygulama için benzersiz tanımlayıcı |
| ApplicationName | Uygulama adı |
| applicationDisplayName | Uygulama görünen adı |
| applicationVersion | Uygulama sürümü |
| applicationDefinitionLocation | Uygulama yapılandırma dosyasının bulunduğu URL |
| contractCodes | Uygulama için [sözleşme kodları](#contract-code-information) koleksiyonu |
| applicationRoles | Uygulama için [uygulama rollerinin](#application-role-information) toplanması |
| Applicationiş akışları | Uygulama için [uygulama iş akışlarının](#application-workflow-information) toplanması |
| ConnectionID | Bağlantı için benzersiz tanımlayıcı |
| Iletichemaversion | Mesajlaşma şeması sürümü |
| Ileti Gename | **EventMessage** |
| AdditionalInformation | Burada sunulan ek bilgiler, uygulama iş akışı durumlarını ve geçiş bilgilerini içerir. |

#### <a name="contract-code-information"></a>Sözleşme kodu bilgileri

| Adı | Açıklama |
|------|-------------|
| id | Azure blok zinciri çalışma ekranı içindeki sözleşme kodu dosyası için benzersiz tanımlayıcı |
| LedgerID | Azure blok zinciri çalışma ekranı içindeki genel muhasebe için benzersiz tanımlayıcı |
| location | Anlaşma kodu dosyasının bulunduğu URL |

#### <a name="application-role-information"></a>Uygulama rolü bilgileri

| Adı | Açıklama |
|------|-------------|
| id | Azure blok zinciri çalışma ekranı içindeki uygulama rolü için benzersiz tanımlayıcı |
| ad | Uygulama rolünün adı |

#### <a name="application-workflow-information"></a>Uygulama iş akışı bilgileri

| Adı | Açıklama |
|------|-------------|
| id | Azure blok zinciri çalışma ekranı içindeki uygulama iş akışı için benzersiz tanımlayıcı |
| ad | Uygulama iş akışı adı |
| DisplayName | Uygulama iş akışı görünen adı |
| işlevler | [Uygulama iş akışı için işlevlerin](#workflow-function-information) toplanması|
| aşama | [Uygulama iş akışı için durum](#workflow-state-information) koleksiyonu |
| properties | Uygulama [iş akışı özellikleri bilgileri](#workflow-property-information) |

##### <a name="workflow-function-information"></a>İş akışı işlev bilgileri

| Adı | Açıklama |
|------|-------------|
| id | Azure blok zinciri çalışma ekranı içindeki uygulama iş akışı işlevinin benzersiz tanımlayıcısı |
| ad | İşlev adı |
| parametreler | İşlevin parametreleri |

##### <a name="workflow-state-information"></a>İş akışı durum bilgileri

| Adı | Açıklama |
|------|-------------|
| ad | Durum adı |
| DisplayName | Durum görünen adı |
| Biçim | Durum stili (başarı veya hata) |

##### <a name="workflow-property-information"></a>İş akışı özelliği bilgileri

| Adı | Açıklama |
|------|-------------|
| id | Azure blok zinciri çalışma ekranı içindeki uygulama iş akışı özelliği için benzersiz tanımlayıcı |
| ad | Özellik adı |
| type | Özellik türü |

Blok zinciri çalışma ekranı 'ndan bir *Eventmessage applicationın* örneği:

``` json
{
    "eventName": "ApplicationIngestion",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
    "applicationDefinitionLocation": "http://url",
    "contractCodes": [
        {
            "id": 23,
            "ledgerId": 1,
            "location": "http://url"
        }
    ],
    "applicationRoles": [
            {
                "id": 134,
                "name": "Buyer"
            },
            {
                "id": 135,
                "name": "Seller"
            }
       ],
    "applicationWorkflows": [
        {
            "id": 89,
            "name": "AssetTransfer",
            "displayName": "Asset Transfer",
            "functions": [
                {
                    "id": 912,
                    "name": "",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                },
                {
                    "id": 913,
                    "name": "modify",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                }
            ],
            "states": [ 
                 {
                      "name": "Created",
                      "displayName": "Created",
                      "style" : "Success"
                 },
                 {
                      "name": "Terminated",
                      "displayName": "Terminated",
                      "style" : "Failure"
                 }
            ],
            "properties": [
                {
                    "id": 879,
                    "name": "Description",
                    "type": {
                        "name": "string"
                     }
                },
                {
                    "id": 880,
                    "name": "Price",
                    "type": {
                        "name": "int"
                     }
                }
            ]
        }
    ],
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation":
        {
            "states" :
            [
                {
                    "Name": "BuyerAccepted",
                    "Transitions": [
                        {
                            "DisplayName": "Accept",
                            "AllowedRoles": [ ],
                            "AllowedInstanceRoles": [ "InstanceOwner" ],
                            "Function": "Accept",
                            "NextStates": [ "SellerAccepted" ]
                        }
                    ]
                }
            ]
        }
}
```

### <a name="event-message-role-assignment"></a>Olay iletisi: rol atama

Bir kullanıcıya, rol atamasını gerçekleştirme ve rolün adı ve ilgili uygulama gibi çalışma ekranına bir rol atandığında bilgiler içerir.

| Adı | Açıklama |
|------|-------------|
| eventName | **RoleAssignment** |
| Uygulama | Azure blok zinciri çalışma ekranı içindeki uygulama için benzersiz tanımlayıcı |
| ApplicationName | Uygulama adı |
| applicationDisplayName | Uygulama görünen adı |
| applicationVersion | Uygulama sürümü |
| applicationRole        | [Uygulama rolü](#roleassignment-application-role) hakkında bilgi |
| atayan               | [Asimzalayan](#roleassignment-assigner) hakkında bilgi |
| atanan               | [Atanan](#roleassignment-assignee) hakkında bilgi |
| ConnectionID           | Bağlantı için benzersiz tanımlayıcı |
| Iletichemaversion   | Mesajlaşma şeması sürümü |
| Ileti Gename            | **EventMessage** |
| AdditionalInformation  | Ek bilgiler belirtildi |

#### <a name="roleassignment-application-role"></a>Roleatama uygulama rolü

| Adı | Açıklama |
|------|-------------|
| id | Azure blok zinciri çalışma ekranı içindeki uygulama rolü için benzersiz tanımlayıcı |
| ad | Uygulama rolünün adı |

#### <a name="roleassignment-assigner"></a>Roleatama asimzalayan

| Adı | Açıklama |
|------|-------------|
| id | Azure blok zinciri çalışma ekranı içindeki kullanıcının benzersiz tanımlayıcısı |
| type | Asimzalayan türü |
| Chainıdentifier | Kullanıcının muhasebedeki benzersiz tanımlayıcısı |

#### <a name="roleassignment-assignee"></a>Roleatama atane

| Adı | Açıklama |
|------|-------------|
| id | Azure blok zinciri çalışma ekranı içindeki kullanıcının benzersiz tanımlayıcısı |
| type | Atanan kişiye ait tür |
| Chainıdentifier | Kullanıcının muhasebedeki benzersiz tanımlayıcısı |

Blok zinciri çalışma ekranının bir *Eventmessage Roleatama* örneği:

``` json
{
    "eventName": "RoleAssignment",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
    "applicationRole": {
        "id": 134,
        "name": "Buyer"
    },
    "assigner": {
        "id": 1,
        "type": null,
        "chainIdentifier": "0xeFFC7766d38aC862d79706c3C5CEEf089564ffff"
    },
    "assignee": {
        "id": 3,
        "type": null,
        "chainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
    },
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [Akıllı sözleşme tümleştirme desenleri](integration-patterns.md)
