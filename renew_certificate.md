Easy-RSA 3 Certificate Renewal and Revocation Documentation
===========================================================

This document explains how the **differing versions** of Easy-RSA 3 work
with regard to Renewal and Revocation of Certificates.


# Step 1: Navigate to your Easy-RSA directory

Log into your OpenVPN server via terminal and go to your active Easy-RSA directory (commonly located in /etc/openvpn/server/easy-rsa/ or /etc/openvpn/easy-rsa/)
'''
cd /etc/openvpn/easy-rsa
'''

# Step 2: Renew the Certificate
Run the specific commands below according to the exact version of Easy-RSA you are currently running

### Option A: For Easy-RSA 3.1.x and newer

Modern versions include a built-in renew command that retains the original configuration attributes.

## For the server certificate (replace 'server' with your actual server Common Name if different)
    sudo ./easyrsa renew <server certificate file>

## For a specific client certificate (replace 'client1' with the target client's Common Name)

    sudo ./easyrsa renew < vpn client name>

### Option B: For Easy-RSA 3.2.x and newer
If you are running Easy-RSA 3.2.x+, the sequence requires expiring the old token before signing the request:

    # Mark the old certificate as expired
    sudo ./easyrsa expire <server certificate name>
    
    # Sign the request again using the existing key
    sudo ./easyrsa --san=DNS:<server certificate name> sign-req <server certificate name> <server certificate name>

# Step 3: Copy the Renewed Files
Move the freshly generated files from you pki/ directory into your active OpenVPN configuration directory:

    # copy server certificate to /etc/openvpn/server sirectory:
    sudo cp pki/issued/<server_name.crt> /etc/openvpn/server/
    
    # If you also generated or updated the key:
    sudo cp pki/private/server_name.key /etc/openvpn/server/

# Step 4: Restart the OpenVPN Service

For the server to read and apply the renewed certificate, you must restart the daemon:

    
    sudo systemctl restart openvpn-server@server
    # OR for general setups:
    sudo systemctl restart openvpn



