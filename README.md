# 🚀 LogiTrack · Despliegue y Orquestación

![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Docker Compose](https://img.shields.io/badge/Docker%20Compose-v2-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-8.4.0-0EA5E9?style=for-the-badge&logo=mysql&logoColor=white)
![License](https://img.shields.io/badge/Licencia-MIT-F59E0B?style=for-the-badge)

Repositorio centralizado para el despliegue, la orquestación y la administración local del ecosistema de microservicios de **LogiTrack** utilizando **Docker** y **Docker Compose**. Realizado en el marco de la cursada de **Desarrollo y Arquitecturas Avanzadas de Software** (Universidad Nacional de Jujuy).

---

## 👥 Equipo de Desarrollo

* **Proyecto:** LogiTrack - Arquitectura de Software
* **Comisión / Grupo:** Proyecto G2 ARQ
* **Integrantes:**
  * Jorge Agustín Armella
  * Lucas Nahuel Armella
  * Mauricio Fernando Benitez

---

## 🏗️ Arquitectura del Despliegue

Todos los microservicios se ejecutan en contenedores aislados y se comunican a través de una red virtual tipo bridge (`logitrack-net`), compartiendo una instancia central de base de datos MySQL 8.4 y un servidor de configuración centralizado.

```text
                             [ Red: logitrack-net ]
                                       │
        ┌───────────────┬──────────────┼──────────────┬──────────────┐
        │               │              │              │              │
        ▼               ▼              ▼              ▼              ▼
 ┌─────────────┐ ┌─────────────┐ ┌───────────┐ ┌─────────────┐ ┌───────────┐
 │   MySQL     │ │   Config    │ │ Security  │ │   Courier   │ │ Shipment  │
 │  (8.4.0)    │ │   Server    │ │  Service  │ │   Service   │ │  Service  │
 │ Port: 3307  │ │ Port: 8085  │ │Port: 8083 │ │ Port: 8081  │ │Port: 8080 │
 └─────────────┘ └─────────────┘ └───────────┘ └─────────────┘ └───────────┘
        │
        ▼
 [ Volumen: mysql_data ]
```

---

## 📋 Mapeo de Puertos y Servicios

| Servicio | Contenedor | Puerto Host | Puerto Contenedor | Descripción |
| --- | --- | --- | --- | --- |
| **MySQL DB** | `logitrack-mysql` | `3307` | `3306` | Base de datos relacional compartida |
| **Config Server** | `logitrack-configserver` | `8085` | `8085` | Servidor central de configuraciones |
| **Security Service** | `security-service` | `8083` | `8083` | Autenticación, JWT y roles de usuario |
| **Courier Service** | `courier-service` | `8081` | `8081` | Gestión y asignación de repartidores |
| **Shipment Service** | `shipment-service` | `8080` | `8080` | Gestión de envíos, tracking y tarifas |

---

## ⚙️ Requisitos Previos

Antes de ejecutar la orquestación, asegurate de contar con las siguientes herramientas instaladas:

* [Docker Desktop](https://www.docker.com/products/docker-desktop/) (v24.0+ con Docker Compose v2)
* [Git](https://git-scm.com/)
* Conexión a Internet para descargar las imágenes base desde Docker Hub

---

## 🚀 Puesta en Marcha

### 1. Clonar el repositorio

```bash
git clone https://github.com/ProjectG2ARQ/logitrack-deployment.git
cd logitrack-deployment
```

### 2. Iniciar el clúster de servicios

Para levantar todos los contenedores en segundo plano (*detached mode*):

```bash
docker compose up -d
```

### 3. Verificar el estado de los contenedores

Para confirmar que los 5 contenedores se encuentran en estado `Up` y `healthy`:

```bash
docker compose ps
```

---

## 🔍 Documentación Interactiva (Swagger / OpenAPI) y Health Check

Una vez iniciado el clúster, podés acceder en el navegador a la documentación y prueba de los microservicios, y al estado del Config Server:

* **Courier Service:** [http://localhost:8081/swagger-ui/index.html](http://localhost:8081/swagger-ui/index.html)
* **Shipment Service:** [http://localhost:8080/swagger-ui/index.html](http://localhost:8080/swagger-ui/index.html)
* **Config Server (Health):** [http://localhost:8085/actuator/health](http://localhost:8085/actuator/health)

---

## 🛑 Detención y Mantenimiento

### Detener los servicios conservando datos

Detiene y elimina los contenedores y redes virtuales, pero **mantiene intactos los datos** de la base de datos en el volumen `mysql_data`:

```bash
docker compose down
```

### Detener y reiniciar desde cero (Hard Reset)

Elimina contenedores, redes y **destruye los volúmenes de datos**, permitiendo que los scripts iniciales (`data.sql`) vuelvan a ejecutarse en el siguiente arranque:

```bash
docker compose down -v
```

### Ver logs en tiempo real

Para inspeccionar la salida de un servicio específico:

```bash
# Ejemplo: Ver logs de Courier Service
docker compose logs -f courier-service

# Ejemplo: Ver logs de la base de datos
docker compose logs -f mysqldb
```

---

## 📄 Licencia

Licencia MIT. Ver el archivo [LICENSE](LICENSE).