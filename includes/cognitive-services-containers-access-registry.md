---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b9d0d2b97472eb3264f5e4600fddbfc7d3250918
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704272"
---
## <a name="use-the-docker-cli-to-authenticate-the-private-container-registry"></a>Özel kapsayıcı kayıt defteri kimlik doğrulaması için Docker CLI'yı kullanın

Bilişsel hizmetler kapsayıcılarla birkaç şekilde özel kapsayıcı kayıt defteri ile doğrulayabilir, ancak önerilen yöntem komut satırından [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Kullanım [ `docker login` komut](https://docs.docker.com/engine/reference/commandline/login/)oturum açmak için aşağıdaki örnekte gösterildiği gibi `containerpreview.azurecr.io`, Bilişsel hizmetler kapsayıcılar için özel kapsayıcı kayıt defteri. Değiştirin *\<kullanıcıadı\>* kullanıcı adıyla ve *\<parola\>* kimlik bilgilerinde aldığınız sağlanan parola Azure Bilişsel hizmetler ekibi.

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Bir metin dosyasındaki kimlik bilgilerinizi güvenli hale getirdiniz ise, metin dosyasının içeriğini kullanarak bitiştirebilirsiniz `cat` komutu için `docker login` , aşağıdaki örnekte gösterildiği gibi komutu. Değiştirin *\<passwordFile\>* ile parolasını içeren bir metin dosyasının adını ve yolunu ve *\<kullanıcıadı\>* kullanıcı adı kimlik bilgilerinizi sağlanır.

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```
