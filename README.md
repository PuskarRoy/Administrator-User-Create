# linux-user-ssh-key-create
Bash script to create Linux users and provision SSH key-based access with private key generation.

    #!/bin/bash
    set -e

    if [ $# -ne 1 ]; then
    echo "Usage: bash $0 <username>"
    exit 1
    fi
    
    USERNAME="$1"
    
    sudo mkdir pvt_keys
    sudo useradd -m -s /bin/bash "$USERNAME"
    sudo ssh-keygen -f "./pvt_keys/$USERNAME" -N ""
    
    sudo mkdir -p "/home/$USERNAME/.ssh"
    sudo mv "pvt_keys/$USERNAME.pub" "/home/$USERNAME/.ssh/authorized_keys"
    sudo mv "pvt_keys/$USERNAME" "$USERNAME.pem"
    sudo chmod 700 "/home/$USERNAME/.ssh"
    sudo chmod 600 "/home/$USERNAME/.ssh/authorized_keys"
    sudo chmod 777 "$USERNAME.pem"
    sudo chown -R "$USERNAME:$USERNAME" "/home/$USERNAME/.ssh"

    echo '$USERNAME ALL=(ALL) NOPASSWD: ALL' | sudo EDITOR='tee -a' visudo
    
    sudo rm -rf pvt_keys
