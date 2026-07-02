# API CTUCL Parada

## Descripción

Este proyecto corresponde a una API para el control del mecanismo de acceso a una parada de bus mediante un sistema de torniquete.

El software está diseñado para ejecutarse en una **Raspberry Pi** con:

- Sistema operativo **Raspberry Pi OS Lite (64 bits)**
- Versión **Bookworm**

---

# Copiar el proyecto a la Raspberry Pi

Desde el equipo de desarrollo, copie el proyecto utilizando el siguiente comando:

```bash
scp -r ./api-ctucl-parada admin@raspberry.local:/home/admin
```

---

# Instalación

## Paso 1. Actualizar el sistema

```bash
sudo apt update && sudo apt full-upgrade
```

---

## Paso 2. Instalar Python y el entorno virtual

```bash
sudo apt install python3-virtualenv python3-venv
```

---

## Paso 3. Crear un entorno virtual

Ubíquese en la carpeta del proyecto y ejecute:

```bash
python3 -m venv env
```

---

## Paso 4. Instalar las dependencias

```bash
pip install gpiozero
pip install rpi-lgpio
pip install flask
pip install pyserial
pip install python-dotenv
pip install paho-mqtt
```

También puede instalar todas las dependencias mediante un archivo `requirements.txt` si el proyecto lo incluye.

---

## Paso 5. Activar el entorno virtual

```bash
source env/bin/activate
```

---

## Paso 6. Ejecutar la aplicación

```bash
python main.py
```

---

# Configuración del servicio con systemd

Para que la aplicación se inicie automáticamente al encender la Raspberry Pi, configure un servicio de **systemd**.

## Paso 1. Crear el archivo del servicio

```bash
sudo nano /etc/systemd/system/api_ctucl_parada.service
```

---

## Paso 2. Agregar la siguiente configuración

```ini
[Unit]
Description=Aplicación para controlar la parada
After=network.target

[Service]
User=admin
WorkingDirectory=/home/admin/api-ctucl-parada/
ExecStart=/home/admin/env/bin/python3 /home/admin/api-ctucl-parada/main.py
Restart=always

[Install]
WantedBy=multi-user.target
```

---

## Paso 3. Recargar y habilitar el servicio

```bash
sudo systemctl daemon-reload

sudo systemctl enable api_ctucl_parada

sudo systemctl start api_ctucl_parada
```

---

# Comandos útiles para el servicio

Reiniciar el servicio:

```bash
sudo systemctl restart api_ctucl_parada
```

Ver el estado del servicio:

```bash
sudo systemctl status api_ctucl_parada
```

Detener el servicio:

```bash
sudo systemctl stop api_ctucl_parada
```

Ver los registros en tiempo real:

```bash
sudo journalctl -u api_ctucl_parada.service -f
```

---

# Hardware utilizado

## Pines GPIO

| GPIO | Función |
|------|---------|
| GPIO 6 | Cerradura |
| GPIO 27 | Flecha LED |
| GPIO 21 | Extender actuador |
| GPIO 20 | Retraer actuador |
| GPIO 5  | Electroimán especial |

---

## Sensores

| GPIO | Descripción |
|------|-------------|
| GPIO 16 | Sensor ubicado a 45° |
| GPIO 26 | Sensor principal (posición normal) |

---

# Notas

- Se recomienda ejecutar la aplicación siempre dentro del entorno virtual.
- Verifique que el usuario `admin` tenga permisos para acceder a los GPIO.
- Si se agregan nuevas dependencias, actualice el archivo `requirements.txt` para facilitar futuras instalaciones.