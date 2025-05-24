# 🔁 K3s VIP Switch - master1 Bootstrap

Este proyecto de Ansible automatiza el proceso de reconfiguración del primer nodo `master1` de un clúster K3s para que deje de actuar como nodo bootstrap local (`--cluster-init`) y pase a integrarse como nodo estándar apuntando al VIP del clúster (`--server https://VIP:6443`).

---

## 📁 Estructura del proyecto

```plaintext
k3s-vip-switch-master1-bootstrap/
├── inventory/
│   └── hosts.ini # Definición del nodo master1 y la IP VIP
├── playbooks/
│   └── switch-to-vip.yml # Playbook principal que aplica el cambio
```

---

## 🛠️ Requisitos

- Acceso SSH a `master1` con usuario `core`.
- Archivo de clave privada en: `/root/.ssh/cluster_k3s/shared/id_rsa_shared_cluster`.
- Python y Ansible instalados localmente.
- K3s ya instalado y funcionando en el clúster.

---

## ⚙️ ¿Qué hace este playbook?

1. Detiene el servicio `k3s` en `master1`.
2. Reemplaza su archivo de unidad `k3s.service` de systemd.
3. Cambia `--cluster-init` por `--server https://<VIP>:6443`.
4. Usa el mismo `token` del clúster.
5. Reinicia el servicio `k3s`.

---

## 🚀 Ejecución

```bash
cd k3s-vip-switch-master1-bootstrap
ansible-playbook -i inventory/hosts.ini playbooks/switch-to-vip.yml
```

📝 **Nota:** El host `master1` debe tener resolución DNS funcional para `master1.cefaslocalserver.com`.

📌 **Advertencia:** Este cambio es irreversible por Ansible. Si deseas volver a usar `--cluster-init`, debes editar manualmente el archivo de unidad y reiniciar.

El VIP (`10.17.5.10`) debe estar gestionado correctamente por HAProxy + Keepalived.

---

## 🧠 ¿Por qué cambiar a la VIP?

K3s permite iniciar el clúster con un nodo inicial (`--cluster-init`) sin necesidad de endpoint externo. Sin embargo, para operaciones de alta disponibilidad (HA), todos los nodos deben apuntar a la misma dirección API (la VIP). Este cambio asegura coherencia y resistencia ante fallos del nodo bootstrap.

---

## 📜 Licencia

MIT