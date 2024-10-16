# Create an SSH Key

**How to generate SSH keys**

**Windows:**

On Windows 10 and later, you can generate an SSH key pair using PowerShell and a client called “OpenSSH”:

1. Open PowerShell by searching for PowerShell in the Start menu and selecting it. Then use the following command to generate an SSH key pair:
    
    ```
    ssh-keygen -t ed25519
    ```
    
2. You will be prompted to choose the location to save the key pair. 
    - I recommend using something similar to the following:
        - `C:\Users\<yourusername>\.ssh\ravleen_digitalocean`
    - Alternatively, press Enter to accept the default location.
        - The default location is `C:\Users\YourUsername\.ssh\ed25519`
    - Note that if a key named `ed25519` already exists, you will overwrite it.
3. Once the key pair is generated, you’ll see output indicating that the keys were successfully created.
    - Your public key will be stored in a file with a `.pub` extension. This is the key you’ll share with servers or services where you want to authenticate using SSH public-key authentication.
    - Your private key will be stored in a file without an extension (e.g., `id_rsa`). This is the key you’ll use when you’re authenticating to the server. Keep this file secure and do not share it with anyone.