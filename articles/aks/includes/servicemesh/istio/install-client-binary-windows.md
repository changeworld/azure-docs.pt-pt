---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 562382cc1cfb6adb7e65d76e717df4c4e2962ba7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594006"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Descarregue e instale o binário do cliente istioctl istioctl

Numa concha baseada no PowerShell `Invoke-WebRequest` no Windows, utilize para descarregar `Expand-Archive` o lançamento istio e, em seguida, extrair com o seguinte:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.4.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

O `istioctl` binário do cliente funciona na sua máquina cliente e permite-lhe interagir com a malha de serviço Istio. Utilize os seguintes comandos `istioctl` para instalar o binário do cliente Istio numa concha baseada em PowerShell no Windows. Estes comandos `istioctl` copiam o binário do cliente para uma pasta Istio e, em seguida, disponibilizam-no `PATH`imediatamente (na concha atual) e permanentemente (através de reinícios de concha) através da sua . Não precisa de privilégios elevados (administradores) para executar estes comandos e não precisa reiniciar a sua concha.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```