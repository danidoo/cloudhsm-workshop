### 1. Crie Security Groups para os VPC Endpoints e para a quarentena
<details closed>
<summary><strong>(abrir detalhes)</strong></summary>
<br />

Nesta seção você criará políticas de firewall Security Groups para comunicação das instancias com os VPC Endpoints dos serviços AWS Systems Manager e Session Manager e para isolamento de instâncias Amazon EC2 comprometidas.

- __1.1.__ Abra el servicio [EC2](https://console.aws.amazon.com/ec2/home) en una nueva pestaña o ventana

- __1.2.__ Haga clic en **Instances** en el menu lateral

- __1.3.__ Establezca una sessión RDP a la instancia **LabADFS Dev Instance** usando la siguiente información:
**Connection Name**: Windows Dev Instance
**PC Name**: use la dirección pública de IP de LabADFS
**User name**: mydomain\Stackadmin
**Password**: 12#soupBBUBBLEblue

- __1.4.__ Haga clic en **Start**

- __1.5.__ Haga clic en el icono de busqueda (top right corner)

- __1.6.__ Empieze a digitar **inet**

- __1.7.__ Haga clic en el icono **IIS Manager** que aparece en la lista

- __1.8.__ En **Connections** en el lado izquierdo, haga clic en el **hosthame**, algo parecido a **WIN-xxxxx**



