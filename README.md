# 🛡️ AdGuardian-Term Docker

[![GitHub Stars](https://img.shields.io/github/stars/Lissy93/AdGuardian-Term?style=for-the-badge&logo=github)](https://github.com/Lissy93/AdGuardian-Term)
[![Docker Pulls](https://img.shields.io/docker/pulls/lissy93/adguardian?style=for-the-badge&logo=docker)](https://hub.docker.com/r/lissy93/adguardian)
[![License](https://img.shields.io/github/license/Lissy93/AdGuardian-Term?style=for-the-badge)](https://github.com/Lissy93/AdGuardian-Term/blob/main/LICENSE)

## 📋 Descripción general

**AdGuardian-Term** es un dashboard de monitoreo terminal (TUI) construido en **Rust** para visualizar en tiempo real el tráfico DNS, estadísticas de bloqueo y consultas de una instancia **AdGuard Home** autohospedada. Permite monitoreo eficiente sin interfaz web, e incluso es embebible en navegadores web vía **ttyd**.

Es la herramienta perfecta para homelabs sin IU web accesible, ofreciendo un dashboard TUI terminal ultra-rápido, multi-threaded async (zero lag), con actualizaciones automáticas cada 1-2 segundos.

## ✨ Características principales

- 📊 **Monitor real-time estadísticas AdGuard Home**: queries totales, bloqueadas, % tasa bloqueo, tiempo promedio
- 📋 **Query log interactivo**: listado scrolleable y filtrable (cliente, dominio, estado)
- 🔍 **Upstream DNS tracking**: muestra qué upstream DNS manejó cada query (v1.5+)
- 📈 **Charts ASCII históricos**: gráficos queries over time con escala automática
- 🌐 **Integración ttyd**: embeber TUI en navegador web para acceso remoto
- ⚡ **Rust ultra-rápido**: multi-threaded async, zero lag, performance excelente
- ⌨️ **Keyboard-friendly navigation**: arrow keys, Page Up/Down, Ctrl+C para salir
- 🌓 **Dark/Light terminal modes**: respeta terminal theme, eye-friendly, auto-adapt
- 🐳 **Docker image multi-stage**: imagen optimizada `lissy93/adguardian`, multiarch
- 🔐 **Auth via env vars**: AdGuard user, pass, API key via `-e` flags
- 📦 **MIT open source**: código abierto, comunidad 1.5K+ stars, activamente mantenido
- 🏥 **Health checks** incluidos
- 🔄 **CI/CD pipeline** automatizado

## 📋 Requisitos del sistema

- Docker (o Rust + cargo si compilas desde source)
- 50 MB - 200 MB RAM mínimo (muy ligero, Rust)
- Instancia AdGuard Home corriendo (local red o remota)
- Credenciales AdGuard (usuario, contraseña o API key)
- Terminal emulator moderno (para TUI)
- Opcional: **ttyd** (para exponer en navegador)
- Opcional: **docker-compose** si quieres usar Compose file

## 🐳 Instalación

### Opción 1: Docker run simple (recomendado)

```bash
# Con usuario y contraseña
docker run -it \
  -e ADGUARD_HOST=http://192.168.1.10:3000 \
  -e ADGUARD_USER=admin \
  -e ADGUARD_PASS=tu_contraseña \
  lissy93/adguardian

# Con API key (más seguro)
docker run -it \
  -e ADGUARD_HOST=http://192.168.1.10:3000 \
  -e ADGUARD_KEY=tu_api_key \
  lissy93/adguardian
```

### Opción 2: Docker Compose

```bash
cat > docker-compose.yml << 'EOF'
version: '3.8'

services:
  adguardian:
    image: lissy93/adguardian:latest
    container_name: adguardian
    stdin_open: true
    tty: true
    environment:
      # URL AdGuard Home (reemplaza IP/puerto)
      - ADGUARD_HOST=http://192.168.1.10:3000
      # Credenciales (usa USER+PASS o KEY, no ambas)
      - ADGUARD_USER=admin
      - ADGUARD_PASS=tu_contraseña_fuerte
      # O si usas API key:
      # - ADGUARD_KEY=tu_api_key_aqui
EOF

docker compose up -it
```

### Opción 3: Con ttyd (embeber en navegador web)

```bash
docker run -d \
  -p 7681:7681 \
  -e ADGUARD_HOST=http://192.168.1.10:3000 \
  -e ADGUARD_USER=admin \
  -e ADGUARD_PASS=tu_contraseña \
  lissy93/adguardian:ttyd

# Acceso: http://localhost:7681 (navegador)
```

## ⚙️ Configuración

1. **ADGUARD_HOST** (obligatorio): URL completa de tu instancia AdGuard Home (ej: `http://192.168.1.10:3000`)
2. **Autenticación** (elige una):
   - `ADGUARD_USER` + `ADGUARD_PASS`: usuario y contraseña de AdGuard Home
   - `ADGUARD_KEY`: API key generada en AdGuard Home Settings → API
3. **stdin_open: true** y **tty: true**: requeridos para modo interactivo TUI
4. **Puerto 7681**: solo necesario si usas la imagen `:ttyd` para acceso web

## 🚀 Primeros pasos

1. **Encontrar URL y credenciales AdGuard Home**
   - Localiza IP AdGuard: típicamente `192.168.1.10:3000`
   - Abre web UI: `http://192.168.1.10:3000`
   - Si tiene contraseña, anótala (o crea API key en Settings → API)

2. **Ejecutar AdGuardian-Term**
   ```bash
   docker run -it \
     -e ADGUARD_HOST=http://192.168.1.10:3000 \
     -e ADGUARD_USER=admin \
     -e ADGUARD_PASS=tu_contraseña \
     lissy93/adguardian
   ```

3. **Ver dashboard TUI en vivo**
   - Terminal abre con interfaz colorida
   - Arriba: estadísticas principales (queries, bloqueados, %)
   - Abajo: query log tabla viva (updates 1-2 seg)
   - Derecha: charts ASCII históricos queries

4. **Navegar con teclado**
   - `Arrow Keys` (↑↓←→): navegación tablas, scroll arriba/abajo, izq/der
   - `Page Up/Page Down`: scroll rápido tabla
   - `Ctrl+C`: Salir AdGuardian-Term
   - `Tab`: Cambiar panels (si multiselect)

5. **Ver query log detallado**
   - Tabla central muestra: Time, Domain, Client IP, Status
   - Status: ✅ (permitido), ❌ (bloqueado), ⚠️ (otros)
   - Scroll con arrow keys para ver más queries

6. **Upstream DNS column (v1.5+)**
   - Query log tabla incluye upstream DNS resolver usado
   - Útil para ver qué resolver maneja cada query
   - En pantallas pequeñas, upstream puede esconderse (responsive)

7. **Charts históricos (parte derecha)**
   - Gráfico ASCII muestra queries over time (últimas horas/mins)
   - Escala automática
   - Visualiza tendencias uso DNS

8. **Integrar ttyd (acceso navegador)**
   - Usa imagen tag: `lissy93/adguardian:ttyd`
   - Expone puerto 7681
   - Accede: `http://localhost:7681` en navegador
   - TUI terminal renderizado en web

9. **Usar API key en lugar de password**
   - AdGuard Home: Settings → API
   - Genera API key (si disponible)
   - Usa: `-e ADGUARD_KEY=tu_api_key` en lugar de PASS
   - Más seguro que hardcoding password

10. **Logs en segundo plano (sin terminal interactiva)**
    ```bash
    docker logs -f adguardian
    # Ve conexión logs, errors, debug info
    ```

## 💡 Casos de uso

- 🖥️ **Monitoreo homelab CLI**: Dashboard terminal sin web UI, real-time stats DNS
- 📊 **Dashboards embebidos**: ttyd embeber en otras apps/dashboards (Dashy, etc)
- 🔧 **Troubleshooting DNS**: Ver query log live, identificar bloqueos específicos
- 🖧 **Servidores headless**: SSH access, terminal-only monitoring, zero web overhead
- 🔒 **Monitoreo remoto seguro**: ttyd + HTTPS Caddy, TUI acceso navegador seguro
- 🍓 **Raspberry Pi / bajo recursos**: Rust super-ligero, minimal CPU/RAM

## 🔒 Acceso remoto seguro

### HTTPS con Caddy (si usas ttyd remoto)

```caddyfile
adguardian.tudominio.com {
    reverse_proxy localhost:7681
    # Opcional: autenticación básica
    basicauth / {
        usuario contraseña_hash
    }
}
```

Acceso remoto seguro: `https://adguardian.tudominio.com` con HTTPS automático + login

## 🛠️ Gestión y mantenimiento

```bash
# Ver logs
docker logs -f adguardian
# Conexión, errores, debug info

# Reiniciar container
docker restart adguardian

# Actualizar a versión más reciente
docker pull lissy93/adguardian:latest
docker run -it \
  -e ADGUARD_HOST=... \
  lissy93/adguardian:latest

# Compilar desde source (Rust)
git clone https://github.com/Lissy93/AdGuardian-Term.git
cd AdGuardian-Term
cargo build --release
./target/release/adguardian

# Monitorear consumo (ultra-ligero)
docker stats adguardian
# Típicamente: ~50-200MB RAM, <1% CPU

# Cambiar env vars dinámicamente
docker stop adguardian
docker run -it \
  -e ADGUARD_HOST=http://nueva_ip:3000 \
  -e ADGUARD_USER=admin \
  -e ADGUARD_PASS=nueva_pass \
  lissy93/adguardian
```

## 📝 Licencia

MIT License - Código abierto, comunidad 1.5K+ stars, activamente mantenido.

Ver [LICENSE](https://github.com/Lissy93/AdGuardian-Term/blob/main/LICENSE) en el repositorio oficial.

---

> 📖 **Basado en el post:** [Cómo instalar AdGuardian-Term en Docker - Dashboard TUI monitoreo real-time AdGuard Home autohospedado](https://genbyte.blogspot.com/2026/08/como-instalar-adguardian-term-en-docker.html)
>
> 🐳 **Imagen Docker:** `lissy93/adguardian` | 📦 **GitHub:** [Lissy93/AdGuardian-Term](https://github.com/Lissy93/AdGuardian-Term)