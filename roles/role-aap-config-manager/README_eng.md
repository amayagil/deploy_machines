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

Basic principles:
- The role supports two naming sets for backward compatibility: `controller_` (legacy, AAP 2.4) and `aap_` (AAP 2.6+). Internally the role maps them; you normally only need to provide `aap_` values (via Vault or environment variables).
- `aap_hostname` should include the URL scheme (`https://` or `http://`) when provided via environment or Vault (or set `vault_controller_hostname`).

Main variables and behavior:

| Variable | Description | Notes |
| :--- | :--- | :--- |
| `aap_hostname` | Base host of the Controller (include scheme) | Sourced from `vault_controller_hostname` or `CONTROLLER_HOST`. Example: `https://aap.example.com` |
| `aap_username` / `aap_password` | Credentials | Sourced from Vault (`vault_controller_username`) or env `CONTROLLER_USERNAME` / `CONTROLLER_PASSWORD` |
| `aap_validate_certs` | Validate TLS certs | boolean |
| `controller_api_path` | Full or relative API path | Can be a relative path (`/api/v2` or `/api/controller/v2`) or a full URL. If relative, the role concatenates it with `aap_hostname` automatically. |
| `controller_hostname` | Host used by legacy `controller_` modules | Calculated from `aap_hostname` for compatibility with AAP 2.4 modules |
| `controller_oauthtoken_url` | Token management URL | Built from `controller_api_path` |
| `aap_action` | Action (`extract` or `inject`) | `extract` |
| `base_dir` / `backup_output_file` | Paths used for extraction/injection | `base_dir` (preferred) or `playbook_dir/platform_configuration` by default |
| `controller_source_version` / `controller_target_version` | Source / target controller versions (eg. `2.4`, `2.6`) | Used to select roles/paths internally |

Variable format recommendations:
- Prefer providing `CONTROLLER_HOST` or `vault_controller_hostname` with a full URL (`https://host`) to avoid ambiguity.
- `controller_api_path` may point directly to the API (eg. `https://host/api/v2`) or be left relative; the role will assemble the full endpoint.

Compatibility and roles used internally:
- Extraction from AAP 2.4 (legacy): uses `infra.controller_configuration.filetree_create` (collection `infra.controller_configuration`).
- Injection/reading for AAP 2.6+: uses `infra.aap_configuration_extended.filetree_read` and `infra.aap_configuration.dispatch` (related collections). Ensure required collections are installed.

Recommended collections install:
```bash
ansible-galaxy collection install infra.controller_configuration infra.aap_configuration_extended
```

Quick examples:
- Extract (provide `CONTROLLER_HOST` or Vault):
```bash
export CONTROLLER_HOST='https://aap.example.com'
ansible-navigator run cac_aap.yml -e "aap_action=extract base_dir=$(pwd)"
```
- Inject (use `base_dir` with extracted files):
```bash
ansible-navigator run cac_aap.yml -e "aap_action=inject base_dir=./platform_configuration"
```

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

