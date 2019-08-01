---
title: API de Azure .NET
description: Introducción a las API de Azure para .NET
ms.date: 10/19/2017
ms.topic: conceptual
ms.openlocfilehash: 7b7a3dacec2a6ee706baa545621b4d6609a43ebf
ms.sourcegitcommit: f799dd4590dc5a5e646d7d50c9604a9975dadeb1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/31/2019
ms.locfileid: "68691396"
---
# <a name="azure-net-apis"></a>API de Azure .NET

Las API de Azure .NET le permiten usar servicios de Azure y administrar recursos de Azure desde el código de la aplicación. Las API están disponibles como [paquetes NuGet](/dotnet/api/overview/azure/) para su uso en proyectos .NET. 

## <a name="manage-azure-resources"></a>Administración de recursos de Azure

Las bibliotecas de Azure para .NET le permiten crear y administrar recursos de Azure de las aplicaciones .NET.

Muchos de los paquetes para la administración de recursos de Azure tienen una interfaz [fluida](dotnet-sdk-azure-concepts.md) para configurar los recursos exactamente según sus especificaciones. Por ejemplo, para crear una máquina virtual Windows escribiría el código siguiente:

```csharp
var windowsVM = azure.VirtualMachines.Define(windowsVmName)
    .WithRegion(Region.USEast)
    .WithNewResourceGroup(rgName)
    .WithNewPrimaryNetwork("10.0.0.0/28")
    .WithPrimaryPrivateIPAddressDynamic()
    .WithNewPrimaryPublicIPAddress(publicIpDnsLabel)
    .WithPopularWindowsImage(KnownWindowsVirtualMachineImage.WindowsServer2012R2Datacenter)
    .WithAdminUsername(username)
    .WithAdminPassword(password)
    .WithSize(VirtualMachineSizeTypes.StandardD3V2)
    .Create();
 ```

Revise la [lista de servicios de .NET](/dotnet/api/overview/azure/) para empezar a usar las bibliotecas inmediatamente con los proyectos. A continuación, lea el [artículo de introducción](dotnet-sdk-azure-get-started.md) para configurar la autenticación y ejecutar el código de ejemplo en su propia suscripción de Azure.  El [artículo de conceptos](dotnet-sdk-azure-concepts.md) se adentra en las convenciones que usa el SDK y cómo aprovecharlas para simplificar el código de la aplicación. Las nuevas características, cambios importantes e instrucciones de migración están disponibles en las [notas de la versión](https://github.com/Azure/azure-libraries-for-net).

## <a name="consume-azure-services"></a>Uso de servicios de Azure

Además de usar las API de .NET para crear y administrar mediante programación los recursos dentro de Azure, también puede usar las API de .NET para conectar sus aplicaciones a estos recursos y utilizarlos en tiempo de ejecución.  Por ejemplo, puede conectarse a SQL Database o almacenar datos en Azure Storage.  Puede identificar qué paquete NuGet debe usar para un determinado servicio de Azure explorando nuestra [lista completa de API de servicio](/dotnet/api/overview/azure/).  

## <a name="samples"></a>Ejemplos

Los ejemplos siguientes tratan tareas comunes de automatización con bibliotecas de Azure para .NET:

- [Máquinas virtuales](dotnet-samples.md)
- [Aplicaciones web](dotnet-samples.md)
- [SQL Database](dotnet-samples.md)

Hay disponible una [referencia](/dotnet/api/overview/azure/?view=azure-dotnet) unificada para todos los paquetes tanto del servicio como de las bibliotecas de administración. Las nuevas características, cambios importantes e instrucciones de migración están disponibles en las [notas de la versión](https://github.com/Azure/azure-libraries-for-net).

[!include[Contribute and community](includes/contribute.md)]