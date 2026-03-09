# Role: aap_config_manager
This role manages **Configuration as Code (CaC)** for Ansible Automation Platform (AAP) symmetrically. It allows extracting Controller configurations into YAML files or injecting existing configurations into a target Controller.

## Key Features
* **Total Symmetry**: Uses the same submodules for both extraction and injection.
* **Robust Preflight**: Validates connectivity, credentials, and *System Administrator* privileges before starting.
* **Dry Run Mode**: Full simulation of changes for both extract and inject actions.
* **Session Management**: Automatic cleanup of authentication tokens after execution.

## Collection Requirements
The official Red Hat infrastructure collection is required:
```bash
ansible-galaxy collection install infra.controller_configuration
```

## Role Variables (`vars/main.yml`)
| Variable | Description | Default |
| :--- | :--- | :--- |
| `controller_hostname` | Controller API URL | `https://your-aap-instance.com` |
| `controller_username` | Username (must be Superuser) | `admin` |
| `controller_password` | Password (use Vault) | `password_secure_vault` |
| `aap_action` | Action to perform (`extract` or `inject`) | `extract` |
| `aap_dry_run` | Enables check mode simulation | `false` |
| `backup_local_dir` | Temporary folder for YAMLs | `/tmp/aap_backup` |
| `backup_output_file` | Final `.tar.gz` file path | `/tmp/aap_config_...` |
| `base_dir` | Source directory for injection | `/tmp/aap_backup` |

## Usage: `cac_aap.yml` Playbook

### 1. Extract Configuration (Backup)
Generates a directory structure and a compressed file with the current setup.
```bash
ansible-playbook cac_aap.yml -e "aap_action=extract"
```

### 2. Inject Configuration (Deployment)
Uploads YAML files from a source folder (defined in `base_dir`) to the target Controller.
```bash
ansible-playbook cac_aap.yml -e "aap_action=inject base_dir=./data"
```

### 3. Simulation (Dry Run)
Validates changes without applying them to the Controller.
```bash
ansible-playbook cac_aap.yml -e "aap_action=inject aap_dry_run=true"
```

