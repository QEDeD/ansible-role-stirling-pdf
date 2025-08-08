# PostgreSQL Support

This ansible role now supports PostgreSQL database backend for Stirling PDF, aligning with the official [docker-compose-latest-fat-security-postgres.yml](https://github.com/Stirling-Tools/Stirling-PDF/blob/main/exampleYmlFiles/docker-compose-latest-fat-security-postgres.yml) example.

## Configuration

To enable PostgreSQL support, set the following variables in your playbook:

```yaml
stirling_pdf_postgres_enabled: true
```

When PostgreSQL is enabled, the role will:

1. **Automatically use the PostgreSQL-compatible image**: Changes from `stirlingtools/stirling-pdf:1.1.0-fat` to `stirlingtools/stirling-pdf:1.1.0-fat-postgres`
2. **Deploy a PostgreSQL container**: Uses `postgres:17.2-alpine` with proper configuration
3. **Configure database connection**: Sets up `SYSTEM_DATASOURCE_*` environment variables
4. **Apply resource limits**: 4GB memory for Stirling PDF, 512MB for PostgreSQL (matching official example)
5. **Enable health checks**: Both services get proper health check configuration
6. **Set up service dependencies**: Stirling PDF depends on PostgreSQL service

## PostgreSQL Configuration Variables

### Basic Configuration
```yaml
stirling_pdf_postgres_enabled: false                    # Enable PostgreSQL support
stirling_pdf_postgres_db_name: stirling_pdf            # Database name
stirling_pdf_postgres_username: admin                   # Database username  
stirling_pdf_postgres_password: stirling               # Database password
```

### Container Configuration
```yaml
stirling_pdf_postgres_container_image: "postgres:17.2-alpine"
stirling_pdf_postgres_container_port: 5432
stirling_pdf_postgres_container_host_bind_port: ""     # External port binding (optional)
```

### Resource Limits (matching official example)
```yaml
stirling_pdf_postgres_memory_limit: 512m               # PostgreSQL memory limit
stirling_pdf_postgres_cpu_limit: "0.5"                 # PostgreSQL CPU limit  
stirling_pdf_postgres_shm_size: 512m                   # PostgreSQL shared memory
stirling_pdf_container_memory_limit: "4G"              # Stirling PDF memory (when PostgreSQL enabled)
```

### Data Storage
```yaml
stirling_pdf_postgres_data_path: "{{ stirling_pdf_data_path }}/postgres"
```

## Example Usage

### Basic PostgreSQL Setup
```yaml
- hosts: stirling-pdf-server
  vars:
    stirling_pdf_enabled: true
    stirling_pdf_postgres_enabled: true
    stirling_pdf_hostname: pdf.example.com
    stirling_pdf_postgres_password: "your-secure-password-here"
  roles:
    - stirling_pdf
```

### Advanced PostgreSQL Setup  
```yaml
- hosts: stirling-pdf-server
  vars:
    stirling_pdf_enabled: true
    stirling_pdf_postgres_enabled: true
    stirling_pdf_hostname: pdf.example.com
    
    # Custom database configuration
    stirling_pdf_postgres_db_name: my_stirling_db
    stirling_pdf_postgres_username: stirling_user
    stirling_pdf_postgres_password: "{{ vault_postgres_password }}"
    
    # External PostgreSQL access (optional)
    stirling_pdf_postgres_container_host_bind_port: "127.0.0.1:5432"
    
    # Custom resource limits
    stirling_pdf_postgres_memory_limit: 1G
    stirling_pdf_postgres_cpu_limit: "1.0"
    
  roles:
    - stirling_pdf
```

## Migration from Non-PostgreSQL Setup

When migrating from a non-PostgreSQL setup:

1. **Backup your data**: Ensure you have backups of your Stirling PDF configuration and data
2. **Enable PostgreSQL**: Set `stirling_pdf_postgres_enabled: true`
3. **Update configuration**: The role will automatically deploy PostgreSQL and update Stirling PDF configuration
4. **Data migration**: User accounts and settings will need to be recreated as this changes the storage backend

## Backward Compatibility

The role maintains full backward compatibility:

- **Default behavior unchanged**: `stirling_pdf_postgres_enabled: false` by default
- **Existing installations**: Continue to work without PostgreSQL
- **Image selection**: Automatically uses appropriate image based on PostgreSQL setting
- **Resource limits**: Only applied when PostgreSQL is enabled

## Alignment with Official Example

This implementation matches the official Stirling PDF docker-compose example:

| Configuration | Official Example | This Role |
|---------------|------------------|-----------|
| Stirling PDF Image | `latest-fat-postgres` | `{version}-fat-postgres` when enabled |
| PostgreSQL Image | `postgres:17.2-alpine` | `postgres:17.2-alpine` ✓ |
| Memory Limits | 4G/512MB | 4G/512MB ✓ |
| Database Config | admin/stirling | admin/stirling ✓ |
| Health Checks | Full config | Full config ✓ |
| Resource Limits | CPU/SHM limits | CPU/SHM limits ✓ |

## Troubleshooting

### PostgreSQL Connection Issues
- Check that both services are running: `systemctl status stirling_pdf stirling_pdf-postgres`
- Verify network connectivity between containers
- Check PostgreSQL logs: `journalctl -u stirling_pdf-postgres`

### Resource Issues  
- Monitor memory usage, especially with the 4GB limit for Stirling PDF
- Adjust `stirling_pdf_postgres_memory_limit` and `stirling_pdf_postgres_cpu_limit` as needed

### Data Persistence
- PostgreSQL data is stored in `{{ stirling_pdf_postgres_data_path }}`
- Ensure proper backup strategies for the database directory