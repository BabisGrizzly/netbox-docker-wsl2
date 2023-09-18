# NetBox Docker on WSL 2

This is a How-To, to use NetBox with Docker on your local machine with WSL 2.

## Requirements 

You need to install WSL 2 on your Windows machine with your PowerShell with `wsl --install` or you can follow the instructions on how to  [set up a WSL development environment](https://learn.microsoft.com/en-us/windows/wsl/setup/environment?source=recommendations) for your use specifications.

Make sure to follow the instructions, on how to use the WSL 2 based engine with Docker.

## Setting up your NetBox Docker

Follow the instructions step by step with [Getting Started with NetBox Docker](https://github.com/netbox-community/netbox-docker/wiki/Getting-Started) on your WSL terminal, as if it was a normal Linux environment.

After making sure that you can connect to your NetBox instance with your localhost IP, then you could redirect an external IP (in this case, IP of your machine) with the internal IP of your WSL. (In case you´re setting the NetBox instance with public access).

## Port Forwarding

Create a PowerShell script with the following commands:

    netsh interface portproxy delete v4tov4 listenport=[your_port] listenaddress=0.0.0.0
    wsl -u [WSL_user] hostname -I | Set-Variable -Name "IP"
    $IP=$IP.replace(" ","")
    netsh interface portproxy add v4tov4 listenport=[your_port] listenaddress=0.0.0.0 connectport=[your_port] connectaddress=$IP
    
Make sure you run the script with admin privileges:

    powershell.exe -File "[path_to_script]\[name_of_script].ps1"

In case you get the following error:

    File cannot be loaded because the execution of scripts is disabled on this system. Please see "get-help about_signing" for more details.

  Run the following command:

    Set-ExecutionPolicy unrestricted
    
Check your portproxy status with:

    netsh interface portproxy show all
   
You should be able to see at least the following:

    PS C:\> netsh interface portproxy show all
    
    Listen on ipv4:             Connect to ipv4:
    
    Address         Port        Address         Port
    --------------- ----------  --------------- ----------
    0.0.0.0         8000        XXX.XXX.XXX.XXX 8000

After that, make sure first that your NetBox Docker is running. You´ll be able to connect to your NetBox instance from a browser either with: **localhost:[your_port], [internal_IP]:[your_port] or [external_IP]:[your_port]**.

## Public Access
In case you´re the only one who can access the NetBox instance from your machine only and no other host can, then probably you need to create a new Firewall Inbound Rule to give access to other hosts to connect to your NetBox by running the following command:

    New-NetFirewallRule -DisplayName "[choose_name]" -Direction Inbound -Action Allow -Protocol TCP -LocalPort [your_port]
    
or manually: 

`Windows Menu -> Settings -> Update & Security -> Windows Security -> Open Windows Security -> Firewall & network protection -> Advanced settings -> Inbound Rules -> New Rule -> Port -> Give only wished port -> Allow the connection -> Apply on all network locations and domains -> Give a name -> Finish.`

Congratulations, now others (on the same Network) will be able to connect to your NetBox instance.
