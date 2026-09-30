# Diario de depuración

## 1. Comprensión del informe
- Comportamiento esperado: La validación debe terminar con `El catálogo es válido.`
- Comportamiento observado: La aplicación busca `weather.uvl` dentro de `models/` e informa de que el fichero no existe.
- Información del entorno relevante:
- Información que falta o que pediríamos:

## 2. Reproducción
- Comandos ejecutados:
```powershell
$env:CATALOG_FILE="external/catalog.csv"
$env:UVL_MODELS_DIR="external/models"
python validate.py
```
- Evidencia obtenida:
El catálogo no es válido:
- Línea 2: no existe models\weather.uvl
- ¿Se ha reproducido de forma consistente?:
Sí

## 3. Hipótesis y diagnóstico
- Primera hipótesis: Lee "CATALOG_FILE" pero ignora "UVL_MODELS_DIR"
- Comprobación realizada:
```python
def get_models_dir() -> Path:
    return Path("models")
```
- Causa raíz:
Solo lee la carpeta `models/`

## 4. Reparación y validación
- Prueba de regresión añadida:
```python
def test_models_directory_can_be_configured(monkeypatch, tmp_path: Path):
    monkeypatch.setenv("UVL_MODELS_DIR", str(tmp_path))
    assert get_models_dir() == tmp_path

```
- Cambio realizado:
```python
Path(os.environ.get("UVL_MODELS_DIR", "models"))
```
- Comandos de validación:
```powershell
python3 -m pytest
```
- Resultado:
Tests pasado

## 5. Trazabilidad
- Número o URL de la incidencia:
- Commit que la corrige:
