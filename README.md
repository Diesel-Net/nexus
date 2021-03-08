# nexus
Sonatype Nexus Repository Manager 3 on Docker Swarm

## Requirements
- Ansible 2.10+

## Installing Dependencies
```bash
ansible-galaxy install -r roles/requirements.yaml -p ./roles --force
```

## Deploy to Docker Swarm
```bash
ansible-playbook deploy.yaml -i inventories/production/hosts --vault-id ~/.tokens/master_id
```

