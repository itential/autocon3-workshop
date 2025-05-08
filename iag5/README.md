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

## 🧪 Let's run a test!
To validate that _IAG5_ is installed and ready to roll, try:
```bash
iagctl version
```

> If successful, it'll return the version, executable location, and mode!

Congratulations - you've successfully set up IAG5. Time to update your technical resume with that next **BIG** acronym.