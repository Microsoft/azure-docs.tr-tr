---
title: Blok zinciri uygulaması oluşturma-Azure blok zinciri çalışma ekranı
description: Azure blok zinciri çalışma ekranı önizlemesinde bir blok zinciri uygulaması oluşturma öğreticisi.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: a7d7c68840999772461655090d0213ada0997a7a
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329102"
---
# <a name="tutorial-create-a-blockchain-application-in-azure-blockchain-workbench"></a>Öğretici: Azure blok zinciri çalışma ekranı 'nda bir blok zinciri uygulaması oluşturma

Yapılandırma ve akıllı anlaşma kodu tarafından tanımlanan çok taraflı iş akışlarını temsil eden blok zinciri uygulamaları oluşturmak için Azure blok zinciri çalışma ekranı ' nı kullanabilirsiniz.

Şunları öğrenirsiniz:

> [!div class="checklist"]
> * Blok zinciri uygulaması yapılandırma
> * Akıllı sözleşme kod dosyası oluşturma
> * Blok zinciri uygulaması çalışma ekranına blok zinciri uygulaması ekleme
> * Blok zinciri uygulamasına üye ekleme

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* Blok zinciri çalışma ekranı dağıtımı. Daha fazla bilgi için bkz. dağıtım ayrıntıları için [Azure blok zinciri çalışma ekranı dağıtımı](deploy.md) .
* Kiracıdaki blok zinciri çalışma ekranı ile ilişkili kullanıcıları Azure Active Directory. Daha fazla bilgi için bkz. Azure [blok zinciri çalışma ekranı 'Nda Azure AD kullanıcıları ekleme](manage-users.md#add-azure-ad-users).
* Blok zinciri çalışma ekranı yönetici hesabı. Daha fazla bilgi için bkz. [Azure blok zinciri çalışma ekranında blok zinciri ekleme ekranı yöneticileri](manage-users.md#manage-blockchain-workbench-administrators).

## <a name="hello-blockchain"></a>Merhaba, blok zinciri!

Bir istek sahibinin isteği gönderdiği temel bir uygulama ve bir yanıtlayanın yanıt göndermesini sağlar.
Örneğin bir istek, "Merhaba, nasıl yapılır?" olabilir ve yanıt, "Ben harika!" olabilir. Hem istek hem de yanıt, temel alınan blok zincirine kaydedilir.

Uygulama dosyalarını oluşturmak için adımları izleyin veya [örneği GitHub 'dan indirebilirsiniz](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain).

## <a name="configuration-file"></a>Yapılandırma dosyası

Yapılandırma meta verileri, blok zinciri uygulamasının üst düzey iş akışlarını ve etkileşim modelini tanımlar. Yapılandırma meta verileri, blok zinciri uygulamasının iş akışı aşamalarını ve etkileşim modelini temsil eder.

1. En sevdiğiniz düzenleyicide `HelloBlockchain.json` adlı bir dosya oluşturun.
2. Blok zinciri uygulamasının yapılandırmasını tanımlamak için aşağıdaki JSON öğesini ekleyin.

    ``` json
    {
      "ApplicationName": "HelloBlockchain",
      "DisplayName": "Hello, Blockchain!",
      "Description": "A simple application to send request and get response",
      "ApplicationRoles": [
        {
          "Name": "Requestor",
          "Description": "A person sending a request."
        },
        {
          "Name": "Responder",
          "Description": "A person responding to a request"
        }
      ],
      "Workflows": [
        {
          "Name": "HelloBlockchain",
          "DisplayName": "Request Response",
          "Description": "A simple workflow to send a request and receive a response.",
          "Initiators": [ "Requestor" ],
          "StartState": "Request",
          "Properties": [
            {
              "Name": "State",
              "DisplayName": "State",
              "Description": "Holds the state of the contract.",
              "Type": {
                "Name": "state"
              }
            },
            {
              "Name": "Requestor",
              "DisplayName": "Requestor",
              "Description": "A person sending a request.",
              "Type": {
                "Name": "Requestor"
              }
            },
            {
              "Name": "Responder",
              "DisplayName": "Responder",
              "Description": "A person sending a response.",
              "Type": {
                "Name": "Responder"
              }
            },
            {
              "Name": "RequestMessage",
              "DisplayName": "Request Message",
              "Description": "A request message.",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "ResponseMessage",
              "DisplayName": "Response Message",
              "Description": "A response message.",
              "Type": {
                "Name": "string"
              }
            }
          ],
          "Constructor": {
            "Parameters": [
              {
                "Name": "message",
                "Description": "...",
                "DisplayName": "Request Message",
                "Type": {
                  "Name": "string"
                }
              }
            ]
          },
          "Functions": [
            {
              "Name": "SendRequest",
              "DisplayName": "Request",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "requestMessage",
                  "Description": "...",
                  "DisplayName": "Request Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            },
            {
              "Name": "SendResponse",
              "DisplayName": "Response",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "responseMessage",
                  "Description": "...",
                  "DisplayName": "Response Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            }
          ],
          "States": [
            {
              "Name": "Request",
              "DisplayName": "Request",
              "Description": "...",
              "PercentComplete": 50,
              "Value": 0,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": ["Responder"],
                  "AllowedInstanceRoles": [],
                  "Description": "...",
                  "Function": "SendResponse",
                  "NextStates": [ "Respond" ],
                  "DisplayName": "Send Response"
                }
              ]
            },
            {
              "Name": "Respond",
              "DisplayName": "Respond",
              "Description": "...",
              "PercentComplete": 90,
              "Value": 1,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": [],
                  "AllowedInstanceRoles": ["Requestor"],
                  "Description": "...",
                  "Function": "SendRequest",
                  "NextStates": [ "Request" ],
                  "DisplayName": "Send Request"
                }
              ]
            }
          ]
        }
      ]
    }
    ```

3. `HelloBlockchain.json` dosyasını kaydedin.

Yapılandırma dosyasında birkaç bölüm vardır. Her bölüm hakkındaki ayrıntılar aşağıdaki gibidir:

### <a name="application-metadata"></a>Uygulama meta verileri

Yapılandırma dosyasının başlangıcında uygulama adı ve açıklaması dahil olmak üzere uygulamayla ilgili bilgiler yer alır.

### <a name="application-roles"></a>Uygulama rolleri

Uygulama rolleri bölümü, blok zinciri uygulamasına davranabilir veya katılabilen kullanıcı rollerini tanımlar. İşlevlere göre ayrı roller kümesi tanımlarsınız. İstek-yanıt senaryosunda, bir istek sahibinin işlevselliği, yanıtları üreten bir varlık olarak istek ve Yanıtlayıcı veren bir varlık olarak bir farklılık vardır.

### <a name="workflows"></a>İş akışları

İş akışları, sözleşmenin bir veya daha fazla aşamasını ve eylemini tanımlar. İstek-yanıt senaryosunda, iş akışının ilk aşaması (durum) bir istek sahibi (rol) istek (işlev) göndermek için bir eylem (geçiş) alır. Sonraki aşama (durum) bir yanıtlayanın (rol) yanıt göndermek için bir eylem (geçiş) alır (işlev). Bir uygulamanın iş akışı, bir sözleşmenin akışını betimleyen özellikleri, işlevleri ve durumları içerebilir.

Yapılandırma dosyalarının içerikleri hakkında daha fazla bilgi için bkz. [Azure blok zinciri Iş akışı yapılandırma başvurusu](configuration.md).

## <a name="smart-contract-code-file"></a>Akıllı sözleşme kod dosyası

Akıllı sözleşmeler, blok zinciri uygulamasının iş mantığını temsil eder. Şu anda blok zinciri çalışma ekranı, blok zinciri defteri için Ethereum 'u destekliyor. Ethereum akıllı sözleşmeleri kendi kendine zorlama iş mantığını yazmak için programlama dili olarak [Solidity](https://solidity.readthedocs.io) kullanır.

Solidity ' deki akıllı sözleşmeler, nesne odaklı dillerdeki sınıflara benzerdir. Her sözleşme, akıllı sözleşmenin aşamalarını ve eylemlerini uygulamak için durum ve işlevler içerir.

En sevdiğiniz düzenleyicide `HelloBlockchain.sol` adlı bir dosya oluşturun.

### <a name="version-pragma"></a>Sürüm pragması

En iyi uygulama olarak, hedeflediğiniz Solidity sürümünü belirtin. Sürümü belirtmek gelecekteki Solidity sürümlerle uyumsuzluktan kaçınmanıza yardımcı olur.

@No__t-0 akıllı sözleşme kodu dosyasının en üstüne aşağıdaki sürüm pragmasını ekleyin.

``` solidity
pragma solidity >=0.4.25 <0.6.0;
```

### <a name="configuration-and-smart-contract-code-relationship"></a>Yapılandırma ve akıllı sözleşme kodu ilişkisi

Blok zinciri çalışma ekranı, blok zinciri uygulaması oluşturmak için yapılandırma dosyasını ve akıllı anlaşma kodu dosyasını kullanır. Yapılandırma ve akıllı sözleşmede kod arasında tanımlananla ilişkili bir ilişki vardır. Uygulamayı oluşturmak için, sözleşme ayrıntıları, işlevler, parametreler ve türlerin eşleşmesi gerekir. Blok zinciri çalışma ekranı, uygulama oluşturmadan önce dosyaları doğrular.

### <a name="contract"></a>Sözleşmesi

**Sözleşme** üst bilgisini `HelloBlockchain.sol` akıllı sözleşme kod dosyanıza ekleyin.

``` solidity
contract HelloBlockchain {
```

### <a name="state-variables"></a>Durum değişkenleri

Durum değişkenleri, her bir sözleşme örneği için durumun değerlerini depolar. Sözleşinizdeki durum değişkenlerinin yapılandırma dosyasında tanımlanan iş akışı özellikleriyle eşleşmesi gerekir.

Durum değişkenlerini `HelloBlockchain.sol` akıllı sözleşme kod dosyanızdaki sözleşmeye ekleyin.

``` solidity
    //Set of States
    enum StateType { Request, Respond}

    //List of properties
    StateType public  State;
    address public  Requestor;
    address public  Responder;

    string public RequestMessage;
    string public ResponseMessage;
```

### <a name="constructor"></a>Constructor

Oluşturucu, bir iş akışının yeni bir akıllı sözleşme örneği için giriş parametrelerini tanımlar. Oluşturucu için gerekli parametreler yapılandırma dosyasında Oluşturucu parametreleri olarak tanımlanmıştır. Parametrelerin sayısı, sırası ve türü her iki dosyada da eşleşmelidir.

Oluşturucu işlevinde, sözleşmeyi oluşturmadan önce gerçekleştirmek istediğiniz iş mantığını yazın. Örneğin, başlangıç değerleriyle durum değişkenlerini başlatın.

Oluşturucu işlevini `HelloBlockchain.sol` akıllı sözleşme kod dosyanızdaki sözleşmeye ekleyin.

``` solidity
    // constructor function
    constructor(string memory message) public
    {
        Requestor = msg.sender;
        RequestMessage = message;
        State = StateType.Request;
    }
```

### <a name="functions"></a>İşlevler

İşlevler, bir sözleşmede iş mantığının çalıştırılabilir birimleridir. İşlevi için gerekli parametreler, yapılandırma dosyasında işlev parametreleri olarak tanımlanmıştır. Parametrelerin sayısı, sırası ve türü her iki dosyada da eşleşmelidir. İşlevler, yapılandırma dosyasında bir blok zinciri çalışma iş akışındaki geçişlere ilişkilendirilir. Geçiş, bir uygulamanın iş akışının sözleşme tarafından belirlendiği bir sonraki aşamasına geçmek için gerçekleştirilen bir eylemdir.

İşlevinde gerçekleştirmek istediğiniz herhangi bir iş mantığını yazın. Örneğin, bir durum değişkeninin değerini değiştirme.

1. Aşağıdaki işlevleri `HelloBlockchain.sol` akıllı sözleşme kod dosyanızdaki sözleşmeye ekleyin.

    ``` solidity
        // call this function to send a request
        function SendRequest(string memory requestMessage) public
        {
            if (Requestor != msg.sender)
            {
                revert();
            }
    
            RequestMessage = requestMessage;
            State = StateType.Request;
        }
    
        // call this function to send a response
        function SendResponse(string memory responseMessage) public
        {
            Responder = msg.sender;
    
            ResponseMessage = responseMessage;
            State = StateType.Respond;
        }
    }
    ```

2. @No__t-0 akıllı sözleşme kodu dosyanızı kaydedin.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Blockzincirle blok zinciri uygulaması ekleme çalışma ekranı

Blok zinciri çalışma ekranına blok zinciri uygulaması eklemek için, uygulamayı tanımlamak üzere yapılandırmayı ve akıllı sözleşme dosyalarını karşıya yüklersiniz.

1. Bir Web tarayıcısında, blok zinciri çalışma ekranı web adresine gidin. Örneğin, `https://{workbench URL}.azurewebsites.net/`, blok zinciri çalışma ekranı dağıtılırken Web uygulaması oluşturulur. Blok zinciri Web adresinizi bulma hakkında daha fazla bilgi için bkz. [blok zinciri çalışma ekranı web URL 'si](deploy.md#blockchain-workbench-web-url)
2. [Blok zinciri çalışma ekranı Yöneticisi](manage-users.md#manage-blockchain-workbench-administrators)olarak oturum açın.
3. @No__t-1**Yeni**bir **uygulama**seçin. **Yeni uygulama** bölmesi görüntülenir.
4. **Sözleşme yapılandırmasını karşıya yükle** >  ' e**giderek** oluşturduğunuz **helloblockzincirine. JSON** yapılandırma dosyasını bulun. Yapılandırma dosyası otomatik olarak onaylanır. Doğrulama hatalarını görüntülemek için bağlantıyı **göster** ' i seçin. Uygulamayı dağıtmadan önce doğrulama hatalarını düzeltir.
5. @No__t **sözleşme kodunu karşıya yükle**' yi seçin, **Helloblockzincirine. Nuevo** akıllı sözleşme kodu dosyasını bulmak için-1 ' e**gidin** . Kod dosyası otomatik olarak onaylanır. Doğrulama hatalarını görüntülemek için bağlantıyı **göster** ' i seçin. Uygulamayı dağıtmadan önce doğrulama hatalarını düzeltir.
6. Yapılandırma ve akıllı sözleşme dosyalarına göre blok zinciri uygulamasını oluşturmak için **Dağıt** ' ı seçin.

Blok zinciri uygulamasının dağıtılması birkaç dakika sürer. Dağıtım tamamlandığında, yeni uygulama **uygulamalarda**görüntülenir. 

> [!NOTE]
> Ayrıca, [Azure blok zinciri çalışma ekranı REST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)kullanarak blok zinciri uygulamaları da oluşturabilirsiniz.

## <a name="add-blockchain-application-members"></a>Blok zinciri uygulama üyeleri Ekle

Uygulama üyelerini uygulamanıza ekleyerek, sözleşmeleri başlatın ve sözleşme işlemleri gerçekleştirin. Uygulama üyelerini eklemek için, [blok zinciri çalışma ekranı Yöneticisi](manage-users.md#manage-blockchain-workbench-administrators)olmanız gerekir.

1. @No__t-1**Merhaba, blok zinciri!** **uygulama**seçin.
2. Uygulamayla ilişkili üyelerin sayısı sayfanın sağ üst köşesinde görüntülenir. Yeni bir uygulama için üye sayısı sıfır olacaktır.
3. Sayfanın sağ üst köşesindeki **Üyeler** bağlantısını seçin. Uygulamanın geçerli bir üye listesi görüntülenir.
4. Üyelik listesinde **üye Ekle**' yi seçin.
5. Eklemek istediğiniz üyenin adını seçin veya girin. Yalnızca blok zinciri çalışma ekranı kiracısında mevcut olan Azure AD kullanıcıları listelenir. Kullanıcı bulunamazsa, [Azure AD kullanıcıları eklemeniz](manage-users.md#add-azure-ad-users)gerekir.
6. Üyenin **rolünü** seçin. İlk üye için rol olarak **Istek sahibi** ' i seçin.
7. İlişkili rolün bulunduğu üyeyi uygulamaya eklemek için **Ekle** ' yi seçin.
8. **Yanıtlayıcı** rolüyle uygulamaya başka bir üye ekleyin.

Blok zinciri çalışma ekranı 'nda kullanıcıları yönetme hakkında daha fazla bilgi için bkz. [Azure blok zinciri üzerinde kullanıcıları yönetme çalışma ekranı](manage-users.md)

## <a name="next-steps"></a>Sonraki adımlar

Bu nasıl yapılır makalesinde, temel bir istek ve yanıt uygulaması oluşturdunuz. Uygulamayı nasıl kullanacağınızı öğrenmek için, sonraki nasıl yapılır makalesine ilerleyin.

> [!div class="nextstepaction"]
> [Blok zinciri uygulaması kullanma](use.md)
