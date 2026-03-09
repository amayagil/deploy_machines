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
| Variable | Descripción | Por Defecto |
| :--- | :--- | :--- |
| `controller_hostname` | URL del API del Controller | `https://tu-aap-instancia.com` |
| `controller_username` | Usuario (debe ser Superuser) | `admin` |
| `controller_password` | Contraseña (usar Vault) | `password_seguro_vault` |
| `aap_action` | Acción a realizar (`extract` o `inject`) | `extract` |
| `aap_dry_run` | Activa el modo simulación (check mode) | `false` |
| `backup_local_dir` | Carpeta temporal para los YAML | `/tmp/aap_backup` |
| `backup_output_file` | Ruta del `.tar.gz` final | `/tmp/aap_config_...` |
| `base_dir` | Directorio de origen para la inyección | `/tmp/aap_backup` |

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

