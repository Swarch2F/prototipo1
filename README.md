# GRADEX Prototipo 1

## Repositorio del proyecto

Para utilizar el proyecto simplemente clona el repositorio principal, el cual ya incluye todos los submódulos necesarios:

```bash
git clone --recursive https://github.com/Swarch2F/prototipo1.git
cd prototipo1
```

## Información sobre submódulos (Solo informativo)

Los submódulos fueron agregados inicialmente con estos comandos, pero no necesitas ejecutarlos nuevamente:

```bash
git submodule add https://github.com/Swarch2F/component-1.git components/component-1
git submodule add https://github.com/Swarch2F/component-2.git components/component-2
git submodule add https://github.com/Swarch2F/component-3.git components/component-3
```

## Actualización de submódulos recursivamente (por primer vez una vez clonado el proyecto):

```bash
git submodule update --init --recursive
git submodule update --remote --merge --recursive
```


## Levantar el prototipo con Docker Compose

El proyecto utiliza Docker Compose para gestionar la ejecución de todos los servicios.

### Archivo `docker-compose.yml`


### Ejecución rápida

Una vez clonado el proyecto, ejecuta:

```bash
docker compose up --build
```

## Acceso a servicios

Puedes acceder a cada servicio desde tu navegador en las siguientes rutas:

* **API REST (gx\_be\_estcur):** `http://localhost:8000/`
* **API GraphQL (gx\_be\_proasigcal):** `http://localhost:8080/graphiql`
* **Aplicación Frontend (gx\_fe\_gradex):** `http://localhost:3000/`

Para verificar el estado de los contenedores utiliza:

```bash
docker ps
```

Para pausar un contenedor utiliza el siguiente comando:
```bash
docker compose stop <nombre del contenedor>
```
Para volver a ejecutar un contenedor pausado utiliza el siguiente comando:
```bash
docker compose start <nombre del contenedor>
```
---
**© 2025 Swarch2F. GRADEX Prototipo 1**
