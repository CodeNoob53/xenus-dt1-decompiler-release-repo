![banner](assets/img/banner.avif)

# Xenus DT1/DT2 Decompiler

[![Release](https://img.shields.io/github/v/release/CodeNoob53/xenus-dt1-decompiler-release-repo)](../../releases/latest)
[![Platform](https://img.shields.io/badge/platform-Windows-blue.svg)](https://www.microsoft.com/windows)
[![.NET](https://img.shields.io/badge/.NET-8.0-blueviolet.svg)](https://dotnet.microsoft.com/download/dotnet/8.0)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](https://github.com/CodeNoob53/xenus-dt1-decompiler/blob/main/LICENSE)
[![Source Code](https://img.shields.io/badge/source_code-GitHub-181717.svg?logo=github)](https://github.com/CodeNoob53/xenus-dt1-decompiler)

[English](README.md) | [Українська](README_UA.md) | **Español**

Extrae las texturas de la caché de **Xenus 2: White Gold**, **Boiling Point: Road to Hell** y **The Precursors** (`*.DT1`) a `.tga`, `.dds`, `.png`, `.bmp` o `.jpg`, conservando la estructura de carpetas original.

No hay que instalar nada: .NET va dentro del ejecutable y no se necesitan archivos ni DLL del juego. Requiere Windows 10 u 11 de 64 bits.

---

## Inicio rápido

1. Descarga el último archivo desde **[Releases](../../releases/latest)** y descomprímelo.
2. Ejecuta `xenus-dt1-decompiler.exe`, elige la carpeta `CACHE\TEXTURES` del juego y pulsa **Start**.

---

## Juegos compatibles

| Juego | Estado |
|---|---|
| Xenus 2: White Gold | ✅ Probado con toda la caché (3829 texturas) |
| Boiling Point: Road to Hell | ✅ Probado con toda la caché (2603 texturas), desde la v3.0.2 |
| The Precursors | ⚠️ Sin probar desde la v3.0 |

Hasta la v2.x, las texturas las descomprimía el propio `VELoader.dll` del juego, así que la herramienta funcionaba con cualquier juego cuya DLL se le diera, incluido The Precursors. Desde la v3.0 usa su propio descompresor, reconstruido a partir de `VELoader.dll`; en Xenus 2 y Boiling Point su resultado es idéntico byte a byte al de la DLL. The Precursors usa el mismo motor, así que debería funcionar, pero todavía nadie lo ha comprobado. Si lo pruebas, comparte el resultado en [#4](https://github.com/CodeNoob53/xenus-dt1-decompiler/issues/4), funcione o no. Si falla, queda la [v2.2.1](https://github.com/CodeNoob53/xenus-dt1-decompiler/releases/tag/v2.2.1) con el `VELoader.dll` del juego.

---

## Cómo obtener los archivos de caché

El juego guarda sus recursos en archivos `.grp`. Si en la carpeta del juego no hay `CACHE\TEXTURES` con archivos `.DT1`, descomprime los archivos con **GrpUnpacker**. Viene con el Vital Engine SDK y también se distribuye por separado; algunos antivirus lo marcan, algo habitual en herramientas de modding antiguas.

El juego descomprimido también es necesario para volver a meter texturas editadas (ver [Modding](#modding-volver-a-meter-texturas-en-el-juego)).

---

## Uso

### Interfaz gráfica

Ejecuta `xenus-dt1-decompiler.exe` sin argumentos.

1. **Input**: la carpeta con archivos `.DT1`, p. ej. `Xenus 2\CACHE\TEXTURES` (se incluyen las subcarpetas), o un solo archivo `.DT1`. También puedes arrastrar una carpeta o un archivo a la ventana.
2. **Output**: dónde guardar las texturas. Por defecto, `out_tex` junto al ejecutable.
3. **Format**: elígelo en el selector; pasa el cursor sobre una opción para ver detalles. Ver [Formatos](#formatos).
4. Pulsa **Start**. La barra de progreso y el registro muestran cada archivo; **Cancel** detiene el proceso y **Open output folder** abre el resultado.

Las rutas y el formato se recuerdan entre usos. Hay dos temas, **Mono** y **White Gold**: elige uno con los círculos a la derecha de la fila Format. Ambos siguen el modo claro u oscuro de Windows.

| Mono, modo oscuro | White Gold, modo claro |
|---|---|
| ![Tema Mono, modo oscuro](assets/img/GUI_mono_dark.png) | ![Tema White Gold, modo claro](assets/img/GUI_whitegold_light.png) |

### Línea de comandos

```
xenus-dt1-decompiler.exe <entrada> [carpeta_salida] [formato]
```

| Argumento | Por defecto | Descripción |
|---|---|---|
| `entrada` | — | Un archivo `.DT1` o una carpeta para recorrer de forma recursiva |
| `carpeta_salida` | carpeta de la entrada | Dónde guardar las texturas |
| `formato` | auto | `tga`, `dds`, `png`, `bmp` o `jpg` |

```powershell
xenus-dt1-decompiler.exe "C:\Games\Xenus 2\CACHE\TEXTURES" ".\out"
xenus-dt1-decompiler.exe "C:\Games\Xenus 2\CACHE\TEXTURES" ".\out" png
```

`run_decompiler.bat`, incluido en el archivo, hace lo mismo con rutas que solo hay que escribir una vez. Las líneas de comandos de la versión 2.x que pasan `VELoader.dll` o una carpeta `MATERIALS` siguen funcionando; esos argumentos se ignoran.

---

## Resultado

### Formatos

| Formato | Resultado |
|---|---|
| **Auto** *(recomendado)* | El formato que tenía la textura antes de que el motor la guardara en caché, según el nombre del archivo: `KURSOR_1_TGA.DT1` → `KURSOR_1.tga`. Es el formato que el juego espera cuando devuelves una textura. |
| `dds` | La textura tal como está en la caché (por dentro todas son DDS). |
| `tga` / `png` / `bmp` / `jpg` | Convertida con el [texconv](https://github.com/microsoft/DirectXTex) incluido (Microsoft, MIT). |

Si falta `texconv.exe` en la carpeta del programa, las texturas comprimidas (DXT) se quedan en `.dds`.

### Qué se corrige automáticamente

- **Normal maps.** En la caché tienen los canales intercambiados y se ven verdes. La herramienta restaura el RGB estándar y las reconoce por sus píxeles, así que los nombres y los archivos de materiales no importan.
- **`SPECIAL\DXT1CTRL` y `SPECIAL\R5G6R5TABLE`** son tablas internas del motor, no imágenes. En modo Auto se guardan como el `.dds` original, que es el archivo que hay que devolver al juego. En `tga`/`png`/`bmp` se convierten en un atlas de vista previa sin pérdidas: no lo edites ni lo guardes en `jpg`.

### Qué archivos se procesan

Solo `.DT1`. El motor también crea `.DT2` (una copia de 64×64) y, para las normal maps, `.DT3` (una copia comprimida); ambos son duplicados de menor calidad del `.DT1`.

---

## Modding: volver a meter texturas en el juego

Vital Engine 3 compila los archivos `.TGA` sueltos en la caché al arrancar el juego, así que no hace falta ningún empaquetador. El juego debe estar descomprimido con GrpUnpacker (ver [Cómo obtener los archivos de caché](#cómo-obtener-los-archivos-de-caché)).

**Reemplazar una textura**, p. ej. `CACHE\TEXTURES\HUD\KURSOR_1_TGA.DT1`:

1. Extráela en modo **Auto** → `KURSOR_1.tga`.
2. Edítala. En las texturas del HUD y otros atlas, conserva el tamaño original (ver *Tamaño de la textura* más abajo).
3. Borra `KURSOR_1_TGA.DT1` y cualquier `.DT2` / `.DT3` con el mismo nombre de `CACHE\TEXTURES\HUD\`.
4. Pon `KURSOR_1.tga` en `<juego>\TEXTURES\HUD\`. Crea la carpeta; no existe por defecto.
5. Inicia el juego. El motor crea nuevos `KURSOR_1_TGA.DT1` / `.DT2` en `CACHE\TEXTURES\HUD\`.

**Añadir una textura**: igual, con un nombre nuevo; después referénciala en el archivo `.MAT` correspondiente.

**Compartir un mod**: distribuye los archivos `.DT1` / `.DT2` generados. Si un jugador los coloca en la misma subcarpeta de su `CACHE\TEXTURES\`, tienen prioridad sobre los archivos `.grp`.

> **Tamaño de la textura.** Las texturas del HUD, algunos sprites 2D y partículas son atlas: los XML de la interfaz se refieren a zonas dentro de ellas por coordenadas en píxeles. Por ejemplo, el fondo de la brújula en `UI\PC\Planet\Scheme.xml` es una zona de 167×150 de `_hud.tga`:
> ```xml
> <Material MaterialID="_hud">
>     <Position x="857" y="290"/>
>     <Size x="167" y="150"/>
> </Material>
> ```
> Si escalas una textura así, multiplica por el mismo factor todos los `Position` y `Size` que se refieren a ella; si no, el HUD se descoloca:
>
> ![HUD con _hud.tga ampliada 2× sin actualizar el XML](assets/img/hud_example.avif)
>
> El resto de texturas pueden medir hasta **2048×2048** (más grandes no compilan); **1024×1024** es la opción segura.

Las herramientas del SDK **RF2View** y **levedit2** también compilan texturas sin arrancar el juego, pero cuesta hacerlas funcionar en sistemas modernos.

---

## Solución de problemas

| Problema | Solución |
|---|---|
| El programa no arranca | Se necesita Windows 10 u 11 de 64 bits. Las versiones anteriores a la 3.0.1 también necesitaban .NET 8 Desktop Runtime x86; descarga la última versión. |
| Aviso del antivirus (p. ej. Malwarebytes «Anomalous») | El ejecutable es de código abierto pero no está firmado, y algunos escáneres lo marcan. Puedes revisar y compilar el [código fuente](https://github.com/CodeNoob53/xenus-dt1-decompiler) tú mismo. |
| `texconv.exe not found`, texturas guardadas como `.dds` | Deja el `texconv.exe` del archivo junto al ejecutable. |
| `[FAIL] resource.DT1 : method 7 (encrypted) is not supported` | `resource.DT1` son datos del juego, no una textura; puedes ignorarlo. |

¿Has encontrado un error? [Abre un issue](https://github.com/CodeNoob53/xenus-dt1-decompiler/issues).

---

## Novedades

**v3.1**
- **Nueva interfaz:** un selector de formato en lugar de la lista desplegable, barra de progreso, botones **Cancel** y **Open output folder**, ajustes recordados y dos temas que siguen el modo claro u oscuro de Windows.
- **Unas 3 veces más rápido:** los archivos se procesan en todos los núcleos (toda la caché de Xenus 2 en modo Auto: ~60 s en lugar de ~200 s).
- **Registro más corto:** una línea por archivo y un resumen con la lista de archivos fallidos.
- **No se pierden texturas:** `SKY00_BMP.DT1` y `SKY00_TGA.DT1` se sobrescribían entre sí en `dds` o en un formato forzado; ahora conservan el sufijo.

**v3.0**
- **Ya no hace falta `VELoader.dll`.** Funciona con cualquier copia del juego.
- **Se corrigen todas las normal maps.** Antes algunas se pasaban por alto y quedaban verdes.
- **Las tablas `SPECIAL`** se exportan completas y sin pérdidas.
- No hay que instalar nada: el ejecutable incluye .NET (v3.0.1). Boiling Point vuelve a funcionar (v3.0.2).

Todas las versiones y sus notas: **[Releases](../../releases)**.

---

## Licencia y créditos

MIT, ver [LICENSE](https://github.com/CodeNoob53/xenus-dt1-decompiler/blob/main/LICENSE). El `texconv.exe` incluido forma parte de [DirectXTex](https://github.com/microsoft/DirectXTex) (Microsoft, MIT).

- **badmofo**: el procedimiento para volver a meter texturas ([RPGWatch Forums](https://rpgwatch.com/forum/threads/editing-the-in-game-textures.12577/)).
- [**@evgeniy-mapper**](https://github.com/evgeniy-mapper): pruebas e informes de errores sobre normal maps y tablas internas.
