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

Prerequisitos en VM Ubuntu (Azure/GCP/AWS):

- `git`
- `docker engine`
- `docker compose plugin`

Instalación rápida (Ubuntu):

```bash
sudo apt update
sudo apt install -y git docker.io docker-compose-v2
sudo usermod -aG docker $USER
```

Luego cerrar sesión y volver a entrar para usar Docker sin `sudo`.

Levanta todo con:

```bash
docker compose up -d --build
```

Servicios expuestos:

- Frontend: `http://localhost/`
- API: `http://localhost:8080/`
- Ticketing Service: `http://localhost:7000/`

## Operación

- El botón `Generar carga` lanza internamente una simulación concurrente de compradores.
- La vista muestra asientos vendidos, reservados, libres, tickets emitidos, métricas y eventos recientes.
- No usa WebSockets, IndexedDB, service workers ni carrito de compras.
