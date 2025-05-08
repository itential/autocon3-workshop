## 🔧 IAG5 Setup Guide

In preparation for the workshop, let's install IAG5. This guide covers everything from extracting the binary to running your first service.

## 🚨 Extract Binary / Place in Executable Path
Begin by cloning the _workshop repository_:
```bash
git clone https://github.com/itential/autocon3-workshop \
  && cd autocon3-workshop
```

### Mac installation

1. Navigate to the downloaded tar file and extract the contents:
```bash
tar -zxvf iagctl-v5.0.1-darwin-<arch>.tar.gz
```

> [!NOTE]
> Replace arch with the right architecture for your setup

2. Run the newly extracted executable:
```bash
./iagctl
```

3. macOS will show a security warning. Click _'Done'_ when the popup appears.

4. Navigate to **System Settings > Privacy and Security**. Apple's walled garden needs a bit of convincing.

5. Under Security, find _'iagctl was blocked from use'_ and click _'Allow Anyway'_.

6. Provide your password or fingerprint when prompted.

7. Copy the file to your bin directory to add it to your path:
```bash
mv ./iagctl /usr/local/bin/iagctl
```

8. Open your terminal and run:
```bash
iagctl
```

9. Click _'Open Anyway'_ in the popup. Just one more hurdle from Apple's security theater!

10. You can now run Gateway anywhere by executing _'iagctl'_.

### Linux installation

1. Navigate to the downloaded tar file and extract the contents:
```bash
tar -zxvf iagctl-v5.0.1-linux-<arch>.tar.gz
```

> [!NOTE]
> Replace arch with the right architecture for your setup

2. Run the newly extracted executable:
```bash
./iagctl
```

3. Copy the file to your bin directory to add it to your path:
```bash
mv ./iagctl /usr/local/bin/iagctl
```

4. You can now run Gateway anywhere by executing _'iagctl'_. Linux keeps it refreshingly simple 💡

## Create configuration

Create your configuration file at `~/.gateway.d/gateway.conf`. If you need to use a different location, see the [configuration documentation](https://docs.itential.com/itential-cloud/docs/using-config-variables-iag5).

Here's a minimal configuration to get you started:

```bash
mkdir -p ~/.gateway.d && cat << 'EOF' > ~/.gateway.d/gateway.conf
[application]
cluster_id          = 'cluster_1'
mode                = 'client'
working_dir         = '~/.gateway.d'

[commander]
certificate_file     = '/etc/gateway/certificates/gw-manager.pem'
enabled              = true
host                 = 'PLACE-GATEWAY-MANAGER-HOST-HERE'
port                 = 443
private_key_file     = '/etc/gateway/certificates/gw-manager-key.pem'
insecure_tls         = true

[client]
host    = "127.0.0.1"
use_tls = false

[server]
listen_address    = '0.0.0.0'
port              = 50051
use_tls           = false
runtime_data_dir  = `~/.gateway.d/venv`

[log]
file_enabled = false
level        = 'DEBUG'
EOF
```

> [!TIP]
> Remember to replace _`PLACE-GATEWAY-MANAGER-HOST-HERE`_ with your actual Itential Platform endpoint. Despite the all-caps hint, this placeholder is surprisingly easy to overlook.

## Create certificates for Gateway Manager
Now for everyone's favorite part: certificate creation.

```bash
sudo mkdir -p /etc/gateway/certificates/ && chown -R $USER /etc/gateway
```

Generate the certificate:

```bash
openssl req -x509 -newkey rsa:4096 \
           -keyout /etc/gateway/certificates/gw-manager-key.pem \
           -out /etc/gateway/certificates/gw-manager.pem \
           -days 1825 -nodes \
           -subj "/CN=$(hostname)" \
           -addext "basicConstraints=CA:FALSE" \
           -addext "keyUsage=digitalSignature,keyEncipherment" \
           -addext "extendedKeyUsage=serverAuth"
```

If you have an older version of OpenSSL causing the command to fail, try this alternative approach:

```bash
cat > my-ssl-conf <<-EOF
[req]
distinguished_name=req_distinguished_name
x509_extensions=custom_ext
[req_distinguished_name]
[custom_ext]
basicConstraints=CA:FALSE
keyUsage=digitalSignature,keyEncipherment
extendedKeyUsage=serverAuth
EOF

openssl req -x509 -newkey rsa:2048 \
      -keyout /etc/gateway/certificates/gw-manager-key.pem \
      -out /etc/gateway/certificates/gw-manager.pem \
      -days 1825 -nodes \
      -subj "/CN=$(hostname)" \
      -config my-ssl-conf
```

You've just created a certificate that will last five years. Let's hope you remember where you put this documentation by then.

## Upload certificate to Gateway Manager
1. Open Itential Platform in your browser.
2. Open the Gateway Manager app.
3. Navigate to **Certificates > Upload Certificate**.
4. Give the certificate any name you prefer.
5. Upload the Gateway Manager public certificate you created in the previous step.

## Create Gateway in Gateway Manager
1. Within Gateway Manager, navigate to **Gateways > Add Gateway Cluster**.
2. Set the cluster ID as _`cluster_1`_ to match your configuration.
3. Enter a description of your choice.
4. Add the _`pronghorn_admin`_ group to your gateway.
5. Click "Add".

## Start your Gateway server
1. Create a dedicated terminal instance and run:
   ```bash
   GATEWAY_APPLICATION_MODE=server iagctl server
   ```

2. Verify successful connection with logs similar to:
   ```text
   INF connected to commander at some-server:443
   INF negotiated protocol: "json-rpc"
   INF received initial message from server: {init_message:99a31204-bbc8-43af-a660-ac785eda2552 2.0 GetClusterInfo [ping ack]}
   ```
   The dense protocol logs - a surefire way to know something is working correctly.

## Log into the server from the client

1. From another terminal, run:
   ```bash
   iagctl login admin
   ```

2. When prompted for the initial password, enter _`admin`_.

3. Create a new secure password (longer than 8 characters) when prompted.

4. Follow the remaining prompts to complete login.

## Create a repository resource and some services
Set up a repository and service:

```bash
iagctl create repository hello-gateway --url https://github.com/torerodev/hello-torero.git
iagctl create service python-script hello-python --repository hello-gateway --working-dir hello-python --filename hello-python.py
```

## Run the service from your client
Finally, run your service:

```bash
iagctl run service python-script hello-python
```

Congratulations - you've successfully set up IAG5. Time to update your technical resume with that next **BIG** acronym.