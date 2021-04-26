---
title: Çevrimdışı dağıtım
description: Çevrimdışı dağıtım, Microsoft Container Registry çekmek yerine özel bir kapsayıcı kayıt defterinden kapsayıcı görüntülerini çekmenizi sağlar.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 5fa0b6ca41349d20614a64006536e78d8ee71844
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97955374"
---
# <a name="offline-deployment-overview"></a>Çevrimdışı dağıtıma genel bakış

Genellikle, Azure Arc veri denetleyicisinin oluşturulmasında kullanılan kapsayıcı görüntüleri, SQL yönetilen örnekler ve PostgreSQL hiper ölçek sunucu grupları doğrudan Microsoft Container Registry (MCR) üzerinden alınır. Bazı durumlarda, dağıttığınız ortamın Microsoft Container Registry bağlantısı olmayacaktır.  Bu gibi durumlarda kapsayıcı görüntülerini, Microsoft Container Registry _erişimi olan bir_ bilgisayar kullanarak çekebilir ve sonra bunları etiketleyerek Azure Arc etkin veri hizmetlerini dağıtmak istediğiniz _ortamdan bağlanılabilir bir_ özel kapsayıcı kayıt defterine gönderebilir.

Azure Arc etkin veri Hizmetleri için aylık güncelleştirmeler sağlandığı ve çok sayıda kapsayıcı görüntüsü olduğu için, bir betik kullanarak kapsayıcı görüntülerini bir özel kapsayıcı kayıt defterine çekme, etiketleme ve itme işlemlerini gerçekleştirmek en iyisidir.  Betik otomatikleştirilebilir veya el ile çalıştırılabilir.

Azure Arc GitHub deposunda [örnek bir betik](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/scripts/pull-and-push-arc-data-services-images-to-private-registry.py) bulunabilir.

> [!NOTE]
> Bu betik Python ve [Docker CLI](https://docs.docker.com/install/)yüklemesini gerektirir.

Komut dosyası etkileşimli olarak aşağıdaki bilgileri ister.  Alternatif olarak, komut dosyasını etkileşimli istemler olmadan çalıştırmak istiyorsanız, komut dosyasını çalıştırmadan önce karşılık gelen ortam değişkenlerini ayarlayabilirsiniz.

|İstem|Ortam değişkeni|Notlar|
|---|---|---|
|Kaynak kapsayıcısı kayıt defteri sağlama-kullanmak için ENTER tuşuna basın `mcr.microsoft.com`|SOURCE_DOCKER_REGISTRY|Genellikle, görüntüleri Microsoft Container Registry çektiğinizde, ancak farklı bir kayıt defteriyle özel önizlemeye katılırsınız, önizleme programının bir parçası olarak size sunulan bilgileri kullanabilirsiniz.|
|Kaynak kapsayıcısı kayıt defteri deposu sağla-kullanmak için ENTER tuşuna basın `arcdata` :|SOURCE_DOCKER_REPOSITORY|Microsoft Container Registry çekirsiniz, depo olur `arcdata` .|
|Kaynak kapsayıcısı kayıt defteri için Kullanıcı adı belirtin-kullanmadan kullanmak için ENTER tuşuna basın:|SOURCE_DOCKER_USERNAME|Yalnızca oturum açma gerektiren bir kaynaktan kapsayıcı görüntüleri çekoluşturuyorsanız bir değer sağlayın.  Microsoft Container Registry bir oturum açma gerektirmez.|
|Kaynak kapsayıcısı kayıt defteri için parola belirtin-kullanmadan kullanmak için ENTER tuşuna basın:|SOURCE_DOCKER_PASSWORD|Yalnızca oturum açma gerektiren bir kaynaktan kapsayıcı görüntüleri çekoluşturuyorsanız bir değer sağlayın.  Microsoft Container Registry bir oturum açma gerektirmez. Bu, maskelenmiş bir parola istemi.  Yazarak parolayı göremezsiniz veya yapıştırın.|
|Kaynak-' ' kullanımı için ENTER tuşuna basın `<current monthly release tag>` :|SOURCE_DOCKER_TAG|Varsayılan etiket adı, Microsoft Container Registry geçerli sürümünün ayı ve yılını yansıtacak şekilde aylık olarak güncelleştirilir.|
|Hedef kapsayıcı kayıt defteri DNS adını veya IP adresini belirtin:|TARGET_DOCKER_REGISTRY|Hedef kayıt defteri DNS adı veya IP adresi.  Bu, görüntülerin gönderildiği kayıt defteridir _._|
|Hedef kapsayıcı kayıt defteri deposu sağla:|TARGET_DOCKER_REPOSITORY|Görüntüleri göndermek için hedef kayıt defterindeki depo.|
|Hedef kapsayıcı kayıt defteri için Kullanıcı adı belirtin-kullanmadan kullanmak için ENTER tuşuna basın:|TARGET_DOCKER_USERNAME|Varsa, hedef kapsayıcı kayıt defterinde oturum açmak için kullanılan Kullanıcı adı.|
|Hedef kapsayıcı kayıt defteri için parola belirtin-kullanmadan kullanmak için ENTER tuşuna basın:|TARGET_DOCKER_PASSWORD|Varsa, hedef kapsayıcı kayıt defterinde oturum açmak için kullanılan parola. Bu, maskelenmiş bir parola istemi.  Yazarak parolayı göremezsiniz veya yapıştırın.|
|Hedefteki görüntüler için kapsayıcı görüntüsü etiketi belirtin:|TARGET_DOCKER_TAG|Genellikle, karışıklığın önüne geçmek için kaynakla aynı etiketi kullanırsınız.|