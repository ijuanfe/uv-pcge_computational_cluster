# Taller de Cluster Computacional

## Autor:
> Juan Felipe Orozco Escobar

## Asignatura
>Plataformas computacionales a gran escala - 2018B


En este repositorio se muestra un despliegue automatizado de servicios de red para un cluster computacional

    NFS: sistema de archivo distribuido
    OpenMPI: herramienta para la programación de aplicaciones distribuidas
    HAProxy: herramienta para el balanceo de carga (pendiente)

Para llevar a cabo este despliegue se usó la herramienta Vagrant, por lo tanto es necesario tener instalado Vagrant en el equipo y ejecutar el siguiente comando para realizar el despliegue:

  - vagrant up
  
Nota. antes de realizar pruebas del OpenMPI, es mandatorio conectar por SSH desde servidor hacía los nodos para que se realice la primera sincronización con los mismos, luego si ejecutar los comandos del OpenMPI
