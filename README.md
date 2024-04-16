# DevOps-On-AWS

Here's a step-by-step guide on how to add SSH keys to your GitHub account:

1. **Generate SSH Key**: If you haven't already, you need to generate an SSH key pair. Open a terminal or command prompt and enter the following command, replacing `<your_email@example.com>` with your GitHub-associated email:
   ```
   ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
   ```
   Press Enter to accept the default file location or specify a different one if you prefer.

2. **Add SSH Key to SSH Agent**: Start the SSH agent in the background (if not already running) and add your SSH private key. Enter:
   ```
   eval "$(ssh-agent -s)"
   ssh-add ~/.ssh/id_rsa
   ```
   If you saved your key with a different name, replace `id_rsa` with your key filename.

3. **Copy the SSH Key**: Use the following command to copy the SSH key to your clipboard:
   ```
   pbcopy < ~/.ssh/id_rsa.pub
   ```
   If you're not on macOS, use the appropriate command to copy text from a file to the clipboard for your operating system.

4. **Add SSH Key to GitHub**: 
   - Log in to your GitHub account.
   - Click on your profile icon in the top-right corner, then select "Settings".
   - In the left sidebar, click on "SSH and GPG keys".
   - Click on the "New SSH Key" button.
   - Paste your SSH key into the "Key" field.
   - Give your key a descriptive title in the "Title" field to identify it (e.g., "My Laptop SSH Key").
   - Finally, click on the "Add SSH Key" button.

5. **Verify SSH Connection**: To make sure everything is set up correctly, you can test your SSH connection to GitHub by entering the following command:
   ```
   ssh -T git@github.com
   ```
   If successful, you'll see a message confirming that you've authenticated.

Now your SSH key is added to your GitHub account, and you can use it to securely interact with GitHub repositories.
