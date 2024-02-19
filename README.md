# setup-odoo
Setup and run customized Odoo applications as Docker images

## Running Odoo
### Odoo 16.0
```
cd odoo16
docker compose up odoo16
```

### Odoo 13.0
```
cd odoo16
docker compose up odoo13
```

## Customizing Odoo
* link, create or copy your addons in odoo16/addons or odoo13/addons and restart odoo
* edit odoo16/odoo.config or odoo13/odoo.config and restart odoo
