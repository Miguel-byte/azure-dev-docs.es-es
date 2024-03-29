---
ms.openlocfilehash: ab31ee32ea940db2d7bcfa2fe36475d8a648bfc9
ms.sourcegitcommit: 2efdb9d8a8f8a2c1914bd545a8c22ae6fe0f463b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2019
ms.locfileid: "68284356"
---
| **Creación de máquinas virtuales** || 
|---|---|
| [Administración de máquinas virtuales][1] | Cree, modifique, inicie, detenga y elimine máquinas virtuales. |
| [Creación de una máquina virtual desde una imagen personalizada][2] | Cree una imagen de máquina virtual personalizada y úsela para crear nuevas máquinas virtuales. | 
| [Create a virtual machine using specialized VHD from a snapshot][3] (Creación de una máquina virtual con un disco duro virtual especializado a partir de una instantánea) | Cree una instantánea del sistema operativo de la máquina virtual y los discos de datos, cree discos administrados a partir de las instantáneas y, a continuación, asocie los discos administrados para crear una máquina virtual. |  
| [Create virtual machines in parallel in the same network][4] (Creación de máquinas virtuales en paralelo en la misma red) | Cree máquinas virtuales en la misma región de la misma red virtual con dos subredes en paralelo. |
| [Creación de máquinas virtuales en varias regiones en paralelo][5] | Cree y equilibre la carga de un conjunto de máquinas virtuales a través de varias regiones de Azure. |
| **Máquinas virtuales de red** || 
| [Administración de redes virtuales][6] | Configure una red virtual con dos subredes y restrinja el acceso a Internet a ellas. |
| **Creación de conjuntos de escalado** ||
| [Create a virtual machine scale set with a load balancer][7] (Creación de un conjunto de escalado de máquinas virtuales con un equilibrador de carga) | Cree un conjunto de escalado de máquinas virtuales, configure un equilibrador de carga y obtenga las cadenas de conexión SSH a las máquinas virtuales del conjunto de escalado. |

[1]: ../java-sdk-manage-virtual-machines.md
[2]: https://azure.microsoft.com/resources/samples/managed-disk-java-create-virtual-machine-using-custom-image/
[3]: https://azure.microsoft.com/resources/samples/managed-disk-java-create-virtual-machine-using-specialized-disk-from-vhd/
[4]: https://azure.microsoft.com/resources/samples/compute-java-manage-virtual-machines-in-parallel/
[5]: ../java-sdk-virtual-machines-in-parallel.md
[6]: ../java-sdk-manage-virtual-networks.md
[7]: ../java-sdk-manage-vm-scalesets.md