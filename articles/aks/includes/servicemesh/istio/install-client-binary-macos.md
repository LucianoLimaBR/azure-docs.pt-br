---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 06479b4396ab26c280cc6246d774bc30b5ea1c76
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530388"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Baixar e instalar o binário do cliente İSTİO istioctl

Em um shell baseado em bash no MacOS, use `curl` para baixar a versão İSTİO e, em seguida, extrair com `tar` da seguinte maneira:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.3.2

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

O binário de cliente do `istioctl` é executado no computador cliente e permite que você interaja com a malha do serviço İSTİO. Use os comandos a seguir para instalar o binário do İSTİO `istioctl` Client em um shell baseado em bash no MacOS. Esses comandos copiam o binário do `istioctl` cliente para o local do programa de usuário padrão em seu `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Se você quiser a conclusão da linha de comando para o binário do cliente do İSTİO `istioctl`, configure-o da seguinte maneira:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```