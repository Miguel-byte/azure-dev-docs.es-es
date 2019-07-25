---
title: SDK de Azure para la configuración de operaciones de Python
description: C iniciado por el SDK de Azure para Python
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 03/07/2018
ms.topic: conceptual
ms.devlang: python
ms.openlocfilehash: 9638aa4602f96e2da0155a7b3840e5be4857eb98
ms.sourcegitcommit: 2efdb9d8a8f8a2c1914bd545a8c22ae6fe0f463b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2019
ms.locfileid: "68285516"
---
# <a name="operation-config"></a>Configuración de operaciones 

Los métodos en operaciones tienen parámetros adicionales que se pueden proporcionar en elementos `kwargs`. Esto se denomina operation_config.

Las opciones de configuración de operaciones son:

|Nombre de parámetro|type|Role|
|----------------------|------|---------------|
| Comprobación |`bool`|Si se debe comprobar el certificado SSL. El valor predeterminado es True.|
|  cert |`str`| Ruta de acceso al certificado local para la comprobación del lado cliente.|
|  timeout |`int`| Tiempo de espera para establecer una conexión de servidor, en segundos.|
|  allow_redirects |`bool` | Si se permiten redirecciones.|
|  max_redirects  |`int`| Número máximo de redirecciones permitido.|
|  proxies  |`dict` |Configuración del servidor proxy.|
|  use_env_proxies |`bool` |Si se debe leer la configuración del servidor proxy de las variables de entorno locales.|
|  retries  |`int` | Número total de reintentos.|
|  enable_http_logger | `bool`| Habilitar los registros de HTTP en modo de depuración (False de forma predeterminada).|
