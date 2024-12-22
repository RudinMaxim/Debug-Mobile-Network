Here is the English translation of the uploaded article: 

---

# How to Debug a Web Application on a Mobile Device in a Local Network

For mobile testing, it's essential to launch the application in a way that makes it accessible on a phone via the local network. Here's a step-by-step guide on how to do it.

---

## Step 1. Find Your Computer's IP Address

To access the server from your phone, you need to know your local IP address. Here's how to find it:

- **Windows**:

  1. Open Command Prompt and execute the command:

     ```bash
     ipconfig
     ```

  2. Look for the `IPv4 Address` line, e.g., `192.168.1.100`.

- **macOS/Linux**:

  1. Open the terminal and execute the command:

     ```bash
     ifconfig | grep inet
     ```

  2. Find an address that looks like `192.168.x.x`.

---

## Step 2. Run the Server on the Correct Address

By default, local servers listen only to `localhost`, which is not accessible from other devices. To enable network access, do the following:

- **React/Vite/Next.js**:  
  Start the server with the `--host` parameter:

  ```bash
  npm start --host=0.0.0.0
  ```

- **Node.js/Express**:  
  Ensure that the server listens on `0.0.0.0`:

  ```javascript
  app.listen(3000, "0.0.0.0");
  ```

---

## Step 3. Connect to the Server from Your Phone

1. Ensure your phone and computer are connected to the same Wi-Fi network.
2. In your phone's browser, enter:

   ```plaintext
   http://192.168.X.X:3000
   ```

   Replace `192.168.X.X` with your computer's IP address.

If the application is inaccessible, ensure port 3000 is open for incoming connections.

---

## Step 4. Open the Port in the Firewall

### Windows

1. Open the `Windows Defender Firewall` → `Advanced Settings` via the Start menu.
2. Create a new inbound rule:
   - **Rule Type**: Port.
   - **Protocol and Ports**: TCP, port `3000`.
   - **Action**: Allow the connection.
   - **Profiles**: Domain, Private, Public.
   - **Name**: For example, "Development Access Rule".

### macOS/Linux

On macOS and Linux, configure the firewall as follows:

- **UFW (Ubuntu)**:

  ```bash
  sudo ufw allow 3000/tcp
  ```

- **iptables**:

  ```bash
  sudo iptables -A INPUT -p tcp --dport 3000 -j ACCEPT
  ```

---

## Step 5. Set Up a DNS Alias for Convenience

To avoid manually entering the IP address each time, set up a DNS alias:

### Modify the Hosts File

1. Open the `hosts` file on your computer:
   - **Windows**: `C:\Windows\System32\drivers\etc\hosts`
   - **macOS/Linux**: `/etc/hosts`
2. Add the following line:

   ```plaintext
   192.168.X.X dev.local
   ```

   Replace `192.168.X.X` with your local IP.
3. Clear the DNS cache:
   - **Windows**:

     ```bash
     ipconfig /flushdns
     ```

   - **macOS**:

     ```bash
     sudo dscacheutil -flushcache
     sudo killall -HUP mDNSResponder
     ```

   - **Linux**:

     ```bash
     sudo systemctl restart nscd
     ```

Now you can access the application at `http://dev.local:3000`. You can change the DNS name to any name you prefer.

---

## Troubleshooting Common Issues

### Connection Problems

- Ensure the server is configured to listen on `0.0.0.0`.
- Check for active VPNs or firewalls blocking access.

### Clearing the Browser Cache on Your Phone

If changes in the application don't appear, clear the cache or use incognito mode.

### Disabling Blockage of Non-Standard Domains

Some browsers may block access to non-standard domains. For Chrome:

1. In the address bar, enter:

   ```plaintext
   chrome://flags/#block-insecure-private-network-requests
   ```

2. Disable the flag and restart the browser.
