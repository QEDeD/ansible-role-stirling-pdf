# ansible-role-stirling_pdf
An ansible role developed for the MASH-Playbook to install Stirling PDF.
It is inspired by the following projects:

- [Mother of All Self Hosting - GotoSocial Role](https://github.com/mother-of-all-self-hosting/ansible-role-gotosocial)
- [Mother of All Self Hosting - Gitea Role](https://github.com/mother-of-all-self-hosting/ansible-role-gitea)

## Features

- ✅ **PostgreSQL Support**: Full database backend integration matching the [official docker-compose example](https://github.com/Stirling-Tools/Stirling-PDF/blob/main/exampleYmlFiles/docker-compose-latest-fat-security-postgres.yml)
- ✅ **Backward Compatibility**: Existing installations continue to work without changes  
- ✅ **Automatic Image Selection**: Uses appropriate image variants based on configuration
- ✅ **Resource Management**: Memory and CPU limits aligned with official recommendations
- ✅ **Health Checks**: Comprehensive health monitoring for all services
- ✅ **Service Dependencies**: Proper startup order and dependency management

## Requirements

- Docker
- Ansible
- [MASH playbook](https://github.com/mother-of-all-self-hosting/mash-playbook)

## Usage
See the guide in  [MASH playbook](https://github.com/mother-of-all-self-hosting/mash-playbook/blob/main/docs/services/stirling-pdf.md) repo.

For PostgreSQL support, see [POSTGRESQL_SUPPORT.md](POSTGRESQL_SUPPORT.md).

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## Support

If you encounter any problems or have any questions, please open an issue on GitHub.