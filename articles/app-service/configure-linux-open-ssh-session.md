---
title: Linux kapsayıcıları için SSH erişimi
description: Azure App Service bir Linux kapsayıcısına SSH oturumu açabilirsiniz. Özel Linux kapsayıcıları, özel görüntinizdeki bazı değişikliklerle desteklenir.
keywords: Azure App Service, Web uygulaması, Linux, OSS
author: msangapu-msft
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.topic: article
ms.date: 02/23/2021
ms.author: msangapu
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 3610c4a571d73631ed39d416c72d0d6004dd170d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871768"
---
# <a name="open-an-ssh-session-to-a-linux-container-in-azure-app-service"></a>Azure App Service bir Linux kapsayıcısına SSH oturumu açın

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) genellikle bir komut satırı terminalden uzaktan yönetim komutları yürütmek için kullanılır. Linux üzerinde App Service, uygulama kapsayıcısına SSH desteği sağlar. 

![Linux App Service SSH](./media/configure-linux-open-ssh-session/app-service-linux-ssh.png)

Ayrıca, SSH ve SFTP kullanarak doğrudan yerel geliştirme makinenizden kapsayıcıya da bağlanabilirsiniz.

## <a name="open-ssh-session-in-browser"></a>Tarayıcıda SSH oturumu açma

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>Özel Docker görüntüleriyle SSH desteğini kullanma

Bkz. [özel KAPSAYıCıDA SSH yapılandırma](configure-custom-container.md#enable-ssh).

## <a name="open-ssh-session-from-remote-shell"></a>Uzak kabuktan SSH oturumu aç

> [!NOTE]
> Bu özellik şu anda önizleme aşamasındadır.
>

TCP tünelini kullanarak, geliştirme makineniz ile kimliği doğrulanmış bir WebSocket bağlantısı üzerinden Kapsayıcılar için Web App bir ağ bağlantısı oluşturabilirsiniz. Seçtiğiniz istemciden App Service çalıştıran kapsayıcınıza bir SSH oturumu açmanıza olanak sağlar.

Başlamak için [Azure CLI](/cli/azure/install-azure-cli)'yi yüklemeniz gerekir. Azure CLı yüklemeden nasıl çalıştığını görmek için [Azure Cloud Shell](../cloud-shell/overview.md)açın. 

[Az WebApp Remote-Connection Create](/cli/azure/ext/webapp/webapp/remote-connection#ext-webapp-az-webapp-remote-connection-create) komutunu kullanarak uygulamanıza uzak bir bağlantı açın. _\<subscription-id>_ _\<group-name>_ \_ \<app-name> Uygulamanız için ve _ öğesini belirtin.

```azurecli-interactive
az webapp create-remote-connection --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
```

> [!TIP]
> `&` komutun sonunda Cloud Shell kullanıyorsanız kolaylık sağlamak yeterlidir. Bir sonraki komutu aynı kabukta çalıştırabilmeniz için işlemi arka planda çalıştırır.

> [!NOTE]
> Bu komut başarısız olursa, aşağıdaki komutla [Uzaktan hata ayıklamanın](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0) *devre dışı bırakıldığından* emin olun:
>
> ```azurecli-interactive
> az webapp config set --resource-group <resource-group-name> -n <app-name> --remote-debugging-enabled=false
> ```

Komut çıktısı size bir SSH oturumu açmak için gereken bilgileri sağlar.

```output
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Yerel bağlantı noktasını kullanarak, istediğiniz istemcisiyle kapsayıcınıza sahip bir SSH oturumu açın. Aşağıdaki örnek varsayılan [SSH](https://ss64.com/bash/ssh.html) komutunu kullanır:

```bash
ssh root@127.0.0.1 -p <port>
```

İstendiğinde, `yes` bağlanmaya devam etmek için yazın. Bundan sonra parola istenir. `Docker!`Daha önce gösterilen ' i kullanın.

<pre>
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
</pre>

Kimliğiniz doğrulandıktan sonra oturum hoş geldiniz ekranını görmeniz gerekir.

<pre>
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  &gt;
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
</pre>

Artık bağlayıcınıza bağlısınız.  

[En üstteki](https://ss64.com/bash/top.html) komutu çalıştırmayı deneyin. Uygulamanızın işlemini işlem listesinde görebilmeniz gerekir. Aşağıdaki örnek çıktıda, ile olan `PID 263` .

<pre>
Mem: 1578756K used, 127032K free, 8744K shrd, 201592K buff, 341348K cached
CPU:   3% usr   3% sys   0% nic  92% idle   0% io   0% irq   0% sirq
Load average: 0.07 0.04 0.08 4/765 45738
  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
    1     0 root     S     1528   0%   0   0% /sbin/init
  235     1 root     S     632m  38%   0   0% PM2 v2.10.3: God Daemon (/root/.pm2)
  263   235 root     S     630m  38%   0   0% node /home/site/wwwroot/app.js
  482   291 root     S     7368   0%   0   0% sshd: root@pts/0
45513   291 root     S     7356   0%   0   0% sshd: root@pts/1
  291     1 root     S     7324   0%   0   0% /usr/sbin/sshd
  490   482 root     S     1540   0%   0   0% -ash
45539 45513 root     S     1540   0%   0   0% -ash
45678 45539 root     R     1536   0%   0   0% top
45733     1 root     Z        0   0%   0   0% [init]
45734     1 root     Z        0   0%   0   0% [init]
45735     1 root     Z        0   0%   0   0% [init]
45736     1 root     Z        0   0%   0   0% [init]
45737     1 root     Z        0   0%   0   0% [init]
45738     1 root     Z        0   0%   0   0% [init]
</pre>

## <a name="next-steps"></a>Sonraki adımlar

[Azure forumuna](/answers/topics/azure-webapps.html)sorular ve sorunlar gönderebilirsiniz.

Kapsayıcılar için Web App hakkında daha fazla bilgi için bkz.

* [VS Code Azure App Service Node.js uygulamalarda uzaktan hata ayıklamayı tanıtma](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Hızlı başlangıç: App Service özel kapsayıcı çalıştırma](quickstart-custom-container.md?pivots=container-linux)
* [Linux üzerinde Azure App Service’te Ruby Kullanma](quickstart-ruby.md)
* [Kapsayıcılar için Azure App Service Web App SSS](faq-app-service-linux.md)
