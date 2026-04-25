# 💡 Control de Lámpara por Código Morse vía Bluetooth

Proyecto de Arduino que permite encender y apagar una lámpara usando Bluetooth desde una app como **DSD** (Bluetooth Serial). Soporta comandos directos (`1`/`0`) y transmisión de texto en **código Morse** usando un relé.

---

## 🧰 Componentes necesarios

| Componente | Cantidad |
|---|---|
| Arduino Uno (o compatible) | 1 |
| Módulo Bluetooth HM-10 | 1 |
| Módulo relé (1 canal) | 1 |
| Lámpara / foco | 1 |
| Cables Dupont | Los necesarios |

---

## 🔌 Conexiones

| HM-10 | Arduino |
|---|---|
| RX | Pin 11 (TX de SoftwareSerial) |
| TX | Pin 10 (RX de SoftwareSerial) |
| VCC | 5V |
| GND | GND |

| Relé | Arduino |
|---|---|
| IN | Pin 7 |
| VCC | 5V |
| GND | GND |

---

## 🚀 ¿Cómo funciona?

El Arduino recibe texto vía Bluetooth usando el módulo **HM-10**. Dependiendo del mensaje:

- **`1`** → Enciende la lámpara directamente
- **`0`** → Apaga la lámpara directamente
- **Cualquier texto** (ej: `HOLA`) → Lo convierte a código Morse y parpadea la lámpara

### Código Morse
- `.` (punto) = pulso corto de **200 ms**
- `-` (guión) = pulso largo de **600 ms**
- Pausa entre letras: **600 ms**
- Pausa entre palabras (espacio): **1000 ms**

---

## 📲 Cómo usarlo

1. Cargar el código en el Arduino desde el **IDE de Arduino**
2. Conectar el módulo HM-10 y el relé según el diagrama
3. Abrir la app **DSD** (o cualquier app de Bluetooth serial) en tu teléfono
4. Vincular el HM-10
5. Enviar:
   - `1` para encender
   - `0` para apagar
   - Texto en mayúsculas o minúsculas para ver el Morse (ej: `SOS`)

---

## 📚 Bibliotecas utilizadas

- `SoftwareSerial.h` — incluida por defecto en el IDE de Arduino

---

## 📁 Estructura del proyecto

```
📦 morse-bluetooth-lamp
 ┣ 📄 morse_lamp.ino   ← Código principal
 ┗ 📄 README.md        ← Este archivo
```

---

## 🛠️ Posibles mejoras futuras

- Agregar soporte para números (0-9) en Morse
- Indicador LED de estado de conexión Bluetooth
- Control por voz usando una app secundaria
- Ajuste de velocidad Morse desde la app
