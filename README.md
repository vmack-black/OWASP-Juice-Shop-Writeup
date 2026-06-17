# OWASP-Juice-Shop
Penetration testing write-up for OWASP Juice Shop.

# OWASP Juice Shop Setup & Installation via Aptitude (Kali Linux Native)

If you are using **Kali Linux**, you do not need to manually clone the GitHub repository or configure complex runtimes. OWASP Juice Shop is packaged directly inside the official Kali Linux rolling repositories.

This guide covers how to install and manage the application directly using native package commands.

---

## 🚀 Installation

Since Juice Shop is already maintained in Kali’s tool repositories, you can install it using the advanced package tool (`apt`).

1. **Update your system package index:**
Ensure your local repository lists are up to date to pull the latest verified version.
```bash
sudo apt update

```


2. **Install OWASP Juice Shop:**
Run the following installation command. This will automatically download the application and bundle all required Node.js backend dependencies natively.
```bash
sudo apt install juice-shop

```



---

## ⚙️ Service Management

Once installed, the application runs as a system background service (`systemd`). You can control the environment using standard service execution commands.

### Start the Application

To launch the Juice Shop server, execute:

```bash
sudo juice-shop-start

```

### Stop the Application

To shut down the background server and free up local resources:

```bash
sudo juice-shop-stop

```

### Check Status

To verify if the server is active, checking up-time or evaluating startup logs:

```bash
sudo systemctl status owasp-juice-shop

```

---

## 🌐 Verifying the Setup

Once you have started the service, the application will initialize its database locally.

1. Open any web browser available in your Kali environment (e.g., Firefox ESM).
2. Navigate to the local loopback address on the default service port:
```text
http://127.0.0.1:42000

```


3. The application UI will load, and you can begin mapping vulnerabilities.

---

## 🧹 Complete Uninstallation

If you wish to remove the package along with its local configurations and dependent database files from your system storage later on, use the purge command:

```bash
sudo apt autoremove juice-shop

```

---

> ⚠️ **SECURITY WARNING:** While running natively via `apt` simplifies setup, remember that the application opens network sockets to host vulnerable modules. Ensure your Kali Linux network settings or firewalls block external access if you are operating on a public or shared untrusted network. Keep your assessments bound to `127.0.0.1`.
