# Role: aap_config_manager
Este rol gestiona la **Configuración como Código (CaC)** de Ansible Automation Platform (AAP) de forma simétrica. Permite extraer la configuración de un Controller a archivos YAML o inyectar dicha configuración en un Controller de destino.

## Características Principales
* **Simetría Total**: Mismos submódulos para extraer e inyectar.
* **Pre-vuelo Robusto**: Valida conectividad, credenciales y permisos de *System Administrator* antes de empezar.
* **Modo Dry Run**: Simulación completa de cambios tanto en extracción como en inyección.
* **Gestión de Sesiones**: Limpieza automática de tokens de autenticación tras la ejecución.

## Requisitos de Colecciones
Es necesario tener instalada la colección de Red Hat para gestión de infraestructura:
```bash
ansible-galaxy collection install infra.controller_configuration
```

## Variables del Rol (`vars/main.yml`)

Principios básicos:
- El rol admite dos conjuntos de nombres por compatibilidad histórica: `controller_` (legacy, AAP 2.4) y `aap_` (AAP 2.6+). Ambos se mapean internamente; no es necesario duplicar valores si se usa `aap_hostname` + `vault_*` / variables de entorno.
- `aap_hostname` debe contener el esquema (`https://` o `http://`) cuando se pase por variables de entorno o Vault. Alternativamente, defina `vault_controller_hostname`.

Variables principales y comportamiento:

| Variable | Descripción | Comentarios |
| :--- | :--- | :--- |
| `aap_hostname` | Host base del Controller (incluye esquema) | Se obtiene de `vault_controller_hostname` o `CONTROLLER_HOST`. Ej: `https://aap.example.com` |
| `aap_username` / `aap_password` | Credenciales | Se obtienen de Vault (`vault_controller_username`) o `CONTROLLER_USERNAME` / `CONTROLLER_PASSWORD` de entorno |
| `aap_validate_certs` | Validar SSL | boolean |
| `controller_api_path` | Ruta completa o relativa de la API | Puede ser una ruta relativa (`/api/v2` o `/api/controller/v2`) o una URL completa. Si es relativa, el rol la prefiere y la concatena con `aap_hostname` automáticamente. |
| `controller_hostname` | Host usado por módulos legacy `controller_` | Calculado a partir de `aap_hostname` para compatibilidad con AAP 2.4 modules |
| `controller_oauthtoken_url` | URL para gestionar tokens | Construida a partir de `controller_api_path` |
| `aap_action` | Acción a realizar (`extract` o `inject`) | `extract` |
| `base_dir` / `backup_output_file` | Rutas para extracción/inyección | `base_dir` (preferred) o `playbook_dir/platform_configuration` por defecto |
| `controller_source_version` / `controller_target_version` | Versiones de origen/objetivo (ej. `2.4`, `2.6`) | Usadas para seleccionar rutas/roles apropiados |

Recomendación de formato de variables:
- Preferir pasar `CONTROLLER_HOST` o `vault_controller_hostname` con la URL completa (`https://host`) para evitar ambigüedades.
- `controller_api_path` puede apuntar directamente a la API (ej. `https://host/api/v2`) o dejarse como ruta relativa; el rol se encargará de unirla.

Compatibilidad y roles usados internamente:
- Extracción desde AAP 2.4 (legacy): usa `infra.controller_configuration.filetree_create` (colección `infra.controller_configuration`).
- Inyección/lectura en AAP 2.6+: usa `infra.aap_configuration_extended.filetree_read` y `infra.aap_configuration.dispatch` (colecciones relacionadas). Instale las colecciones necesarias.

Instalación recomendada de colecciones:
```bash
ansible-galaxy collection install infra.controller_configuration infra.aap_configuration_extended
```

Ejemplos rápidos:
- Extraer (usar `CONTROLLER_HOST` o Vault):
```bash
export CONTROLLER_HOST='https://aap.example.com'
ansible-navigator run cac_aap.yml -e "aap_action=extract base_dir=$(pwd)"
```
- Inyectar (usar `base_dir` con los archivos extraídos):
```bash
ansible-navigator run cac_aap.yml -e "aap_action=inject base_dir=./platform_configuration"
```

## Uso del Playbook `cac_aap.yml`

### 1. Extraer Configuración (Backup)
Genera una estructura de directorios y un archivo comprimido con la configuración actual.
```bash
ansible-playbook cac_aap.yml -e "aap_action=extract"
```

### 2. Inyectar Configuración (Despliegue)
Sube los archivos YAML de una carpeta (definida en `base_dir`) al Controller de destino.
```bash
ansible-playbook cac_aap.yml -e "aap_action=inject base_dir=./data"
```

### 3. Simulación (Dry Run)
Valida los cambios sin aplicarlos en el Controller.
```bash
ansible-playbook cac_aap.yml -e "aap_action=inject aap_dry_run=true"
```

