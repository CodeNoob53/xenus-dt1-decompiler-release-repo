![banner](assets/img/banner.avif)

# Xenus DT1/DT2 Decompiler

[![Platform](https://img.shields.io/badge/platform-Windows-blue.svg)](https://www.microsoft.com/windows)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](https://github.com/CodeNoob53/xenus-dt1-decompiler/blob/main/LICENSE)
[![.NET](https://img.shields.io/badge/.NET-8.0-blueviolet.svg)](https://dotnet.microsoft.com/download/dotnet/8.0)
[![Release](https://img.shields.io/github/v/release/CodeNoob53/xenus-dt1-decompiler)](../../releases/latest)
[![Source Code](https://img.shields.io/badge/source_code-GitHub-181717.svg?logo=github)](https://github.com/CodeNoob53/xenus-dt1-decompiler)

**[English version (README.md)](README.md)** | **[Версія українською (README_UA.md)](README_UA.md)**

Herramienta para extraer texturas de los archivos de caché de los juegos **Xenus 2: White Gold**, **Boiling Point: Road to Hell**, y **The Precursors** (`*.DT1`).
Guarda las texturas decodificadas en formato `.dds`, `.tga`, `.png`, `.bmp` o `.jpg` preservando la estructura de carpetas original.

---

## Requisitos

- **Windows** (x86 o x64)
- **[.NET 8 Runtime](https://dotnet.microsoft.com/download/dotnet/8.0)** — necesario para ejecutar la aplicación
- **VELoader.dll** de la **versión Steam** de Xenus 2: White Gold — colócalo junto a `xenus-dt1-decompiler.exe`

> **Nota:** `VELoader.dll` de copias piratas del juego no funcionará (error 1114). Debes usar la DLL de la versión legítima de Steam.
>
> **Compatibilidad con Boiling Point y The Precursors:** Esta herramienta también puede decompilar texturas de **Boiling Point: Road to Hell** (Xenus 1) y **The Precursors**. Para procesar las texturas de Boiling Point, **debes** utilizar la `VELoader.dll` de **Xenus 2**, **The Precursors**, o del **Vital Engine 3 SDK**. La DLL original de Boiling Point no funcionará.

> ⚠️ **Nota sobre alertas de antivirus:** Esta herramienta es de código abierto y segura. Algunos escáneres (como Malwarebytes) pueden marcarla como "Anómala" porque es un ejecutable sin firmar que usa Native Interop para comunicarse con `VELoader.dll` del juego. Puedes verificar el código fuente en [GitHub](https://github.com/CodeNoob53/xenus-dt1-decompiler).

---

## Obtener los archivos de caché

El juego almacena sus recursos dentro de archivos `.grp`. La carpeta `CACHE/` con los archivos `.DT1` puede no existir en el directorio del juego hasta que los archivos sean desempaquetados.

Para extraerlos, usa **GrpUnpacker** — una herramienta que normalmente se incluye con el **Vital Engine SDK**. Ejecútala contra los archivos `.grp` del juego para obtener la estructura de carpetas `CACHE/` que este decompilador necesita.

> **Nota:** GrpUnpacker a veces se distribuye como parte del Vital Engine SDK y a veces como herramienta independiente. Algunas versiones pueden ser marcadas por el antivirus — esto es común en utilidades antiguas de modding. Usa tu propio criterio al buscarlo.

---

## Instalación

1. Descarga el último archivo de la versión desde la página [Releases](https://github.com/CodeNoob53/xenus-dt1-decompiler/releases).
2. Extrae el archivo — creará una carpeta `xenus-dt1-decompiler-vX.X.X`.
3. Copia `VELoader.dll` desde la carpeta del juego a esa carpeta, o especifica la ruta en la GUI.
4. Ejecuta `xenus-dt1-decompiler.exe`.

> **Consejo:** Si `VELoader.dll` se encuentra en la misma carpeta que el exe, no necesitas especificar su ruta en la GUI — se detectará automáticamente.

---

## Uso

### GUI (Interfaz Gráfica)

Haz doble clic en `xenus-dt1-decompiler.exe` (sin argumentos).

1. Selecciona la **carpeta de entrada** con los archivos `.DT1` (por ejemplo, `Xenus 2\CACHE\TEXTURES`).
2. Selecciona la **carpeta de salida** donde se guardarán los archivos decodificados (por defecto — `out_tex` en la carpeta del programa).
3. `VELoader.dll` se detecta automáticamente si está junto al exe.
4. *(Opcional)* **Materials** — ruta a la carpeta `MATERIALS/` desempaquetada. Se usa para la detección precisa de normal maps. Desempaquétala de los archivos `.grp` del juego con **GrpUnpacker** y luego indica aquí la ruta. Sin ella, los normal maps se detectan solo por el sufijo `_N`/`_N_` en el nombre del archivo, lo que puede omitir algunos.
5. Elige el **formato de salida**:
   - **Auto** — detecta el formato real desde los magic bytes del archivo *(recomendado)*
   - **dds / tga / png / bmp / jpg** — convierte la textura al formato elegido
6. Haz clic en **START DECOMPILE** y observa el progreso en la ventana de registro.

![Interfaz de la aplicación](assets/img/GUI_interface.avif)

### Línea de Comandos

```
xenus-dt1-decompiler.exe <entrada> [carpeta_salida] [ruta_veloader] [formato] [carpeta_materials]
```

| Argumento | Por defecto | Descripción |
|---|---|---|
| `input` | — | Ruta a un archivo `.DT1` o una carpeta para escanear recursivamente |
| `output_dir` | misma carpeta que la entrada | Carpeta para guardar los archivos decodificados |
| `veloader_path` | auto-detectado | Ruta a `VELoader.dll` |
| `format` | auto | Formato de salida: `dds`, `tga`, `bmp`, `png`, `jpg` |
| `materials_dir` | ninguno | Ruta a la carpeta `MATERIALS/` desempaquetada para la detección de normal maps |

**Ejemplos:**

```powershell
# Decodificar todas las texturas, formato automático
xenus-dt1-decompiler.exe "C:\Games\Xenus 2\CACHE\TEXTURES" ".\out"

# Decodificar y convertir a TGA
xenus-dt1-decompiler.exe "C:\Games\Xenus 2\CACHE\TEXTURES" ".\out" "C:\Games\Xenus 2\VELoader.dll" tga

# Con ruta explícita a MATERIALS (útil si VELoader no está en la carpeta del juego)
xenus-dt1-decompiler.exe "C:\Games\Xenus 2\CACHE\TEXTURES" ".\out" "C:\Tools\VELoader.dll" tga "C:\Games\Xenus 2\MATERIALS"
```

---

## Reempaquetar texturas en el juego

> Créditos: este proceso fue documentado originalmente por **badmofo** en [RPGWatch Forums](https://rpgwatch.com/forum/threads/editing-the-in-game-textures.12577/).

El Vital Engine 3 re-comprime automáticamente los archivos TGA en formato de caché `.DT1`/`.DT2` al iniciar el juego. No se necesita ninguna herramienta de empaquetado externa.

> **Requisitos:** El directorio del juego debe estar completamente desempaquetado (archivos `.grp` extraídos con GrpUnpacker, de modo que los archivos sueltos existan en `CACHE\TEXTURES\`).

### Reemplazar una textura existente

Usando `KURSOR_1_TGA.DT1` como ejemplo:

1. **Extrae la textura original** con esta herramienta en formato TGA o BMP.
   > **Nota:** el formato original se puede deducir del sufijo del nombre de archivo — por ejemplo `_TGA` en `KURSOR_1_TGA.DT1` indica TGA.
2. **Edita o reemplaza el TGA con tu propia textura.**
   > **Nota sobre el tamaño de la textura:** Los elementos HUD, algunos sprites 2D LOD y los efectos de partículas se referencian mediante coordenadas de píxeles (`Position`, `Size`) codificadas en los archivos XML de diseño de la interfaz. Cambiar las dimensiones de la imagen desplazará esas coordenadas dentro de la textura. Si redimensionas intencionalmente una de estas texturas, también debes actualizar los valores `Position` y `Size` correspondientes en el archivo XML. **De lo contrario, mantén las dimensiones idénticas al original.**
   >
   > Ejemplo — fondo del compás en `UI\PC\Planet\Scheme.xml`, que referencia una región de 167×150 px en el offset (857, 290) dentro de `_hud.tga`:
   > ```xml
   > <Material MaterialID="_hud">
   >     <Position x="857" y="290"/>
   >     <Size x="167" y="150"/>
   > </Material>
   > ```
   > Si escalas `_hud.tga` al doble, multiplica todos los valores de `Position` y `Size` por 2.
   >
   > ![Ejemplo de HUD roto al escalar la textura x2 sin actualizar las coordenadas XML](assets/img/hud_example.avif)
   > *HUD con `_hud.tga` escalado al doble pero las coordenadas XML sin cambiar — los elementos están desalineados.*
   >
   > Para el resto de texturas, el motor admite hasta **2048×2048** píxeles (mayor tamaño no compilará); **se recomienda 1024×1024** para mayor estabilidad.
3. **Guarda el archivo editado como `.TGA`.** El decompilador ya elimina automáticamente el sufijo del motor — `KURSOR_1_TGA.DT1` se guarda como `KURSOR_1.tga`, así que usa ese nombre.
4. **Elimina** el `.DT1` original y cualquier archivo `.DT2`/`.DT3` con el mismo nombre base de `CACHE\TEXTURES\HUD\`.
5. **Coloca** tu nuevo `KURSOR_1.TGA` en `TEXTURES\HUD\` dentro del directorio del juego (crea esta carpeta — no existe por defecto):
   ```
   <directorio del juego>\TEXTURES\HUD\KURSOR_1.TGA
   ```
6. **Inicia el juego.** El motor detecta el TGA sin convertir y lo re-comprime, creando nuevos archivos de caché en `CACHE\TEXTURES\HUD\`:
   ```
   KURSOR_1_TGA.DT1
   KURSOR_1_TGA.DT2
   ```

### Añadir una nueva textura

El proceso es el mismo que reemplazar — coloca un nuevo archivo `.TGA` con un nombre único en `TEXTURES\<subcarpeta>\` e inicia el juego para almacenarlo en caché. Después de almacenarlo, referéncialo por nombre en el archivo de material `.MAT` correspondiente.

> **Alternativa:** Las herramientas SDK **RF2View** y **levedit2** pueden activar la re-compresión sin iniciar el juego completo, pero son difíciles de ejecutar en sistemas modernos.

### Distribuir un mod de texturas

Para publicar texturas modificadas como mod, distribuye los archivos `.DT1` / `.DT2` generados desde `CACHE\TEXTURES\`. Cuando los jugadores los coloquen en la subcarpeta correspondiente de su directorio `CACHE\TEXTURES\`, esos archivos tendrán prioridad sobre los archivos `.grp` empaquetados — no se necesita reempaquetar.

---

## Notas

- Todas las texturas del juego se almacenan internamente como **DDS** independientemente del sufijo en el nombre del archivo (`_TGA`, `_BMP`, etc.). La herramienta detecta el formato real automáticamente.
- La conversión de formato (tga/png/etc.) es realizada por **[texconv](https://github.com/microsoft/DirectXTex)** (Microsoft DirectXTex, licencia MIT) — incluido en el archivo de la versión.
- Las texturas de normal map tienen sus canales de píxeles reordenados a RGBA estándar al exportar. La herramienta identifica los normal maps leyendo los archivos `.MAT` de la carpeta `MATERIALS/` del juego (entradas `Texture1_BUMP:`). Esto es importante porque no todas las texturas de normal map siguen una convención de nombres predecible — algunas tienen nombres arbitrarios sin el sufijo `_N`, por lo que la detección solo por nombre de archivo no es fiable. Para mejores resultados, desempaqueta `MATERIALS/` de los archivos `.grp` del juego con **GrpUnpacker** antes de ejecutar el decompilador. Si no se encuentra la carpeta, se usa el sufijo `_N`/`_N_` como alternativa, que puede omitir algunos normal maps.

---

## Changelog

### v2.1.2
- **Corrección de regresión en normal maps:** v2.1.1 aplicaba incorrectamente el reordenamiento de canales a TODAS las texturas DDS sin comprimir, rompiendo texturas difusas, HUD, SKY y otras. La corrección ahora se aplica solo a los normal maps. Reportado por [@evgeniy-mapper](https://github.com/evgeniy-mapper).
- **Detección de normal maps mediante archivos MAT:** Los normal maps se identifican leyendo entradas `Texture1_BUMP:` en archivos `.MAT` de la carpeta `MATERIALS/` del juego. Si no se encuentra, se usa el sufijo `_N`/`_N_` como alternativa.
- **Campo Materials en GUI y CLI:** Nuevo campo opcional para especificar manualmente la ruta a `MATERIALS/` (útil cuando VELoader no está en la carpeta del juego).

### v2.1.1
- **Intercambio de bytes en normal maps:** Los bytes de píxeles ahora se reordenan para que los normal maps se rendericen correctamente en todos los visores.

### v2.1.0
- **Corrección del orden de canales:** Las texturas DDS de normal map ahora se exportan con el orden de canales correcto. Reportado por [@evgeniy-mapper](https://github.com/evgeniy-mapper).
- **Versión en la barra de título:** La ventana de la aplicación ahora muestra el número de versión.
- **Juegos compatibles:** Confirmado soporte para Boiling Point: Road to Hell y The Precursors.

---

## Licencia

MIT — ver [LICENSE](https://github.com/CodeNoob53/xenus-dt1-decompiler/blob/main/LICENSE)

## Código Fuente

[github.com/CodeNoob53/xenus-dt1-decompiler](https://github.com/CodeNoob53/xenus-dt1-decompiler)
