# SD-Practica06

Despliegue en nube de la práctica de venta concurrente con tres piezas separadas:

- `webapp/`: dashboard visual de monitoreo.
- `servidor.py`: API principal de la venta.
- `ticketing_service.py`: servicio externo que persiste tickets.

## Arquitectura

Usuario -> Frontend web -> Servidor API -> Ticketing Service

El frontend consulta `GET /api/stats` cada segundo y dispara la simulación con `POST /api/generate-load`.

## Ejecución local

1. Inicia el ticketing service:

```bash
python ticketing_service.py --host 127.0.0.1 --port 7000 --store-file tickets/tickets.txt
```

2. Inicia el servidor API:

```bash
python servidor.py --host 127.0.0.1 --port 8080 --ticket-service-host 127.0.0.1 --ticket-service-port 7000
```

3. Sirve la carpeta `webapp/` con cualquier servidor estático y proxy a `/api`, o usa Docker Compose.

## Docker

La VM no necesita instalar Python ni Java para esta práctica. Todo corre dentro de Docker.

Prerrequisitos en cualquier VM Linux de Azure, GCP o AWS:

- `git`
- `docker engine`
- `docker compose plugin`

Instalación rápida en Ubuntu:

```bash
sudo apt update
sudo apt install -y git docker.io docker-compose-v2
sudo usermod -aG docker $USER
```

Después cierra sesión y vuelve a entrar para usar Docker sin `sudo`.

Despliegue paso a paso:

1. Clona el repositorio en la VM.
2. Entra a la carpeta `SD-Practica06`.
3. Verifica la configuración con `docker compose config`.
4. Construye y levanta los contenedores con `docker compose up -d --build`.
5. Abre el puerto `80` en el firewall / security group de la VM.
6. Entra al dashboard desde `http://IP_PUBLICA/`.

Si quieres probar los servicios internos para depuración, también puedes exponer `8080` y `7000`, pero para uso normal basta con el `80` del frontend.

Servicios:

- Frontend: `http://localhost/`
- API interna: `http://server:8080/` dentro de la red Docker
- Ticketing Service interno: `http://ticketing_service:7000/` dentro de la red Docker

## Operación

- El botón `Generar carga` lanza internamente una simulación concurrente de compradores.
- La vista muestra asientos vendidos, reservados, libres, tickets emitidos, métricas y eventos recientes.
- No usa WebSockets, IndexedDB, service workers ni carrito de compras.
