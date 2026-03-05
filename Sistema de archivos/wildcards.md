# 🐧 Wildcards en Linux

> Los **wildcards** (comodines) son caracteres especiales que representan uno o más caracteres en nombres de archivos y rutas. Son fundamentales para trabajar eficientemente en la terminal.

---

## 📋 Tabla de Wildcards

| Wildcard | Significado | Ejemplo |
|----------|-------------|---------|
| `*` | Cero o más caracteres | `*.txt` |
| `?` | Exactamente un carácter | `archivo?.log` |
| `[...]` | Cualquier carácter dentro de los corchetes | `[abc]*.sh` |
| `[!...]` o `[^...]` | Cualquier carácter que NO esté en los corchetes | `[!0-9]*` |
| `{...}` | Lista de opciones separadas por coma | `{jpg,png,gif}` |
| `**` | Cero o más directorios (con `globstar` activo) | `**/*.py` |

---

## ⭐ Asterisco `*`

Representa **cero o más caracteres** de cualquier tipo.

```bash
# Listar todos los archivos .txt
ls *.txt

# Borrar todos los archivos que empiezan con "temp"
rm temp*

# Copiar todos los archivos .log a otro directorio
cp /var/log/*.log ~/backups/

# Listar archivos que contienen "data" en el nombre
ls *data*

# Mover todos los archivos de imagen
mv *.jpg *.png *.gif ~/imagenes/
```

---

## ❓ Signo de interrogación `?`

Representa **exactamente un carácter** (cualquiera).

```bash
# Encontrar archivos con exactamente un carácter antes de .txt
ls ?.txt
# Coincide: a.txt, 1.txt — NO coincide: ab.txt

# Archivos con exactamente 6 caracteres en el nombre
ls ??????

# Encontrar logs con formato fecha: log_2024-01-0?.txt
ls log_2024-01-0?.txt
# Coincide: log_2024-01-01.txt ... log_2024-01-09.txt

# Buscar versiones de un archivo
ls config.?.bak
# Coincide: config.1.bak, config.2.bak
```

---

## 🔲 Corchetes `[...]`

Representan **exactamente un carácter** de los que están dentro del rango o lista.

```bash
# Archivos que empiezan con a, b o c
ls [abc]*

# Archivos que empiezan con mayúscula
ls [A-Z]*

# Archivos que empiezan con número
ls [0-9]*

# Combinar rangos: letras minúsculas o números
ls [a-z0-9]*

# Script: procesar solo archivos .sh o .bash
for f in [a-z]*.sh; do
    echo "Procesando: $f"
done
```

### Negación `[!...]` o `[^...]`

```bash
# Archivos que NO empiezan con número
ls [!0-9]*

# Archivos que NO empiezan con punto (ocultos)
ls [!.]*

# Archivos que no empiezan con mayúscula
ls [^A-Z]*
```

---

## 🧩 Llaves `{...}`

Expanden una **lista de opciones** (no es técnicamente un wildcard del shell, sino expansión de llaves).

```bash
# Crear múltiples directorios a la vez
mkdir -p proyecto/{src,tests,docs,assets}

# Copiar múltiples extensiones
cp *.{jpg,png,gif} ~/imagenes/

# Hacer backup con sufijo
cp config.txt config.txt.{bak,old}

# Crear archivos de prueba
touch test_{1..5}.txt
# Crea: test_1.txt test_2.txt test_3.txt test_4.txt test_5.txt

# Renombrar extensión (con brace expansion)
mv archivo.{txt,md}
# Equivale a: mv archivo.txt archivo.md
```

---

## 🔍 Globstar `**` (recursivo)

Requiere activar la opción `globstar` en bash. Representa **cero o más subdirectorios**.

```bash
# Activar globstar
shopt -s globstar

# Encontrar todos los archivos .py en cualquier subdirectorio
ls **/*.py

# Buscar todos los archivos de configuración
ls **/*.conf

# Contar líneas en todos los archivos JS del proyecto
wc -l **/*.js

# Copiar todos los .log de forma recursiva
cp **/*.log ~/logs_backup/
```

---

## 🔧 Uso con comandos comunes

### `find` — búsqueda avanzada
```bash
# Buscar archivos por patrón (usar comillas para evitar expansión prematura)
find . -name "*.txt"
find . -name "archivo?.log"
find /var -name "[0-9]*.log" -mtime -7
```

### `grep` — buscar dentro de archivos
```bash
# Buscar patrón en todos los .py
grep -r "def main" *.py

# Buscar en múltiples tipos de archivo
grep "TODO" **/*.{js,ts,jsx}
```

### `chmod` / `chown`
```bash
# Dar permisos de ejecución a todos los scripts
chmod +x *.sh

# Cambiar dueño de todos los logs
chown ubuntu:ubuntu /var/log/*.log
```

---

## ⚠️ Buenas prácticas

```bash
# ✅ Siempre usa comillas con find para evitar expansión prematura
find . -name "*.txt"       # correcto
find . -name *.txt         # puede fallar si hay .txt en el directorio actual

# ✅ Verifica antes de borrar con rm
ls *.tmp                   # primero lista
rm *.tmp                   # luego borra

# ✅ Usa set -f para desactivar wildcards temporalmente
set -f
echo *.txt    # imprime literalmente "*.txt"
set +f

# ✅ Escapa el wildcard si quieres el carácter literal
echo \*.txt   # imprime "*.txt" sin expandir
```

---

## 📝 Resumen rápido

```
*         →  cualquier cosa (cero o más caracteres)
?         →  un solo carácter
[abc]     →  uno de: a, b o c
[a-z]     →  un carácter en el rango a-z
[!abc]    →  cualquier carácter excepto a, b, c
{a,b,c}   →  expansión de lista: a, b o c
**        →  recursivo (necesita shopt -s globstar)
```

---

> 💡 **Tip:** Para practicar de forma segura, usa `echo` en lugar de `rm` o `mv` para ver qué archivos coincidirían con tu patrón antes de ejecutar el comando real.

```bash
# Ver qué archivos afectaría antes de borrar
echo *.log
# Cuando estés seguro:
rm *.log
```
