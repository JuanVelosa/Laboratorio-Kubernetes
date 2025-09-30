# 🚀 Guía de instalación y uso de Minikube en macOS (Apple Silicon)

Este documento explica cómo instalar y configurar **Minikube** en un Mac
con chip ARM (M1/M2/M3), utilizando **Docker** como driver, y cómo
habilitar complementos como el **Ingress**.

------------------------------------------------------------------------

## 1. Requisitos previos

-   macOS 11+ (Big Sur o superior).
-   [Homebrew](https://brew.sh/) instalado.
-   [Docker Desktop para Mac (Apple
    Silicon)](https://docs.docker.com/desktop/install/mac/).

------------------------------------------------------------------------

## 2. Instalación

### 2.1 Instalar Minikube

``` bash
brew install minikube
```

### 2.2 Instalar kubectl

``` bash
brew install kubectl
```

------------------------------------------------------------------------

## 3. Verificar Docker Desktop

1.  Abre **Docker Desktop** desde Launchpad.

2.  Espera a que el ícono de la ballena 🐳 quede fijo en la barra
    superior.

3.  Confirma que corre correctamente:

    ``` bash
    docker ps
    ```

------------------------------------------------------------------------

## 4. Iniciar Minikube con Docker

``` bash
minikube start --driver=docker
```

Si quieres dejar Docker como driver por defecto:

``` bash
minikube config set driver docker
```

------------------------------------------------------------------------

## 5. Validar el clúster

``` bash
kubectl get nodes
```

Deberías ver tu nodo en **STATUS = Ready**.

------------------------------------------------------------------------

## 6. Usar el dashboard de Kubernetes

``` bash
minikube dashboard
```

Esto abre un dashboard web para visualizar tus recursos.

------------------------------------------------------------------------

## 7. Habilitar el complemento de Ingress

El **Ingress** permite exponer servicios dentro del clúster de forma
amigable.

### 7.1 Activar Ingress en Minikube

``` bash
minikube addons enable ingress
```

Verifica que el controlador se creó:

``` bash
kubectl get pods -n kube-system
```

Debes ver pods con el nombre `ingress-nginx-controller`.

------------------------------------------------------------------------

## 8. Crear un recurso Ingress de ejemplo

### 8.1 Desplegar un servicio de prueba

``` bash
kubectl create deployment hello-minikube --image=k8s.gcr.io/echoserver:1.4
kubectl expose deployment hello-minikube --type=NodePort --port=8080
```

### 8.2 Crear el Ingress

Crea un archivo `hello-ingress.yaml`:

``` yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: hello-ingress
spec:
  rules:
  - host: hello.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: hello-minikube
            port:
              number: 8080
```

Aplica el manifiesto:

``` bash
kubectl apply -f hello-ingress.yaml
```

------------------------------------------------------------------------

## 9. Configurar el /etc/hosts en macOS

Agrega la entrada para que `hello.local` resuelva a la IP de Minikube:

1.  Obtén la IP:

    ``` bash
    minikube ip
    ```

2.  Edita el archivo hosts:

    ``` bash
    sudo nano /etc/hosts
    ```

    Y agrega una línea como:

        192.168.49.2 hello.local

------------------------------------------------------------------------

## 10. Probar el Ingress

Abre en tu navegador:

    http://hello.local

Deberías ver la respuesta del servidor `echoserver`.

------------------------------------------------------------------------

✅ Con esto tienes **Minikube corriendo en Mac ARM**, con **Docker como
driver**, **Ingress habilitado** y un **ejemplo funcionando**.
